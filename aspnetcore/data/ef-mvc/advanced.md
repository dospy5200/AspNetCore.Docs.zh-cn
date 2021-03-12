---
title: 教程：了解高级方案 - ASP.NET MVC 和 EF Core
description: 本教程不止会介绍使用 Entity Framework Core 开发 ASP.NET Core Web 应用的基础知识，还会介绍其他有用主题。
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/advanced
ms.openlocfilehash: 135881bfb4137bc81bd0f7250f1ce191cab13dcd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586834"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>教程：了解高级方案 - ASP.NET MVC 和 EF Core

之前的教程中，已经以每个类一张表的方式实现了继承。 本教程将会介绍在掌握开发基础 ASP.NET Core web 应用程序之后使用 Entity Framework Core 开发时需要注意的几个问题。

在本教程中，你将了解：

> [!div class="checklist"]
> * 执行原始 SQL 查询
> * 调用查询以返回实体
> * 调用查询以返回其他类型
> * 调用更新查询
> * 检查 SQL 查询
> * 创建抽象层
> * 了解自动更改检测
> * 了解 EF Core 源代码与开发计划
> * 了解如何使用动态 LINQ 简化代码

## <a name="prerequisites"></a>先决条件

* [实现继承](inheritance.md)

## <a name="perform-raw-sql-queries"></a>执行原始 SQL 查询

使用 Entity Framework 的优点之一是它可避免你编写跟数据库过于耦合的代码 它会自动生成 SQL 查询和命令，使得你无需自行编写。 但有一些特殊情况，你需要执行手动创建的特定 SQL 查询。 对于这些情况下， Entity Framework Code First API 包括直接传递 SQL 命令将到数据库的方法。 在 EF Core 1.0 中具有以下选项：

* 使用`DbSet.FromSql`返回实体类型的查询方法。 返回的对象必须是`DbSet`对象期望的类型，并且它们会自动跟踪数据库上下文中除非你[手动关闭跟踪](crud.md#no-tracking-queries)。

* 对于非查询命令使用`Database.ExecuteSqlCommand`。

如果需要运行该返回类型不是实体的查询，你可以使用由 EF 提供的 ADO.NET 中使用数据库连接。 数据库上下文不会跟踪返回的数据，即使你使用该方法来检索实体类型也是如此。

在 Web 应用程序中执行 SQL 命令时，请务必采取预防措施来保护站点免受 SQL 注入攻击。 一种方法是使用参数化查询，确保不会将网页提交的字符串视为 SQL 命令。 在本教程中，将用户输入集成到查询中时会使用参数化查询。

## <a name="call-a-query-to-return-entities"></a>调用查询以返回实体

`DbSet<TEntity>` 类提供了一种方法，可用于执行返回 `TEntity` 类型实体的查询。 若要查看实现细节，你需要更改院系控制器中 `Details` 方法的代码。

在 DepartmentsController.cs 的 `Details` 方法中，使用 `FromSql` 方法调用替换检索院系的代码，如以下突出显示的代码所示：

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

为了验证新代码是否正常工作，请选择“院系”选项卡，然后选择其中某一院系的“详细信息” 。

![院系详细信息](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>调用查询以返回其他类型

之前你在“关于”页面创建了一个学生统计信息网格，显示每个注册日期的学生数量。 可以从学生实体集中获取数据 (`_context.Students`) ，使用 LINQ 将结果投影到`EnrollmentDateGroup`视图模型对象的列表。 假设你想要 SQL 本身编写，而不使用 LINQ。 需要运行 SQL 查询中返回实体对象之外的内容。 在 EF Core 1.0 中，执行该操作的另一种方法是编写 ADO.NET 代码，并从 EF 获取数据库连接。

在 *HomeController.cs* 中，将`About`方法替换为以下代码：

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

添加 using 语句：

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

运行应用并转到“关于”页面。 显示的数据和之前一样。

![“关于”页面](advanced/_static/about.png)

## <a name="call-an-update-query"></a>调用更新查询

假设 Contoso University 管理员希望在数据库中执行全局更改，例如更改每门课程的学分。 如果该大学提供了大量课程，那么将所有课程作为实体来检索并单独更改就非常低效。 在本节中，你将实现一个网页，使用户能够指定一个系数，通过该系数即可更改所有课程的学分，而你将通过执行 SQL UPDATE 语句来完成更改。 网页的外观类似于下图：

![“更新课程学分”页面](advanced/_static/update-credits.png)

在 CoursesController.cs 中，为 HttpGet 和 HttpPost 添加 UpdateCourseCredits 方法：

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

当控制器处理 HttpGet 请求时，`ViewData["RowsAffected"]`中不会返回任何东西，并且在视图中显示一个空文本框和提交按钮，如上图所示。

当单击 **Update** 按钮时，将调用 HttpPost 方法，且从文本框中输入的值获取乘数。 代码接着执行 SQL 语句更新课程，并向视图的`ViewData`返回受影响的行数。 当视图获取`RowsAffected`值，它将显示更新的行数。

在“解决方案资源管理器”中，右键单击“Views/Courses”文件夹，然后依次单击“添加”和“新建项”。

在“添加新项”对话框中，在左侧窗格的“已安装”下单击“ASP.NET Core”，单击“Razor 视图”，并将新视图命名为“UpdateCourseCredits.cshtml”   。

在 *Views/Courses/UpdateCourseCredits.cshtml* 中，将模板代码替换为以下代码：

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

通过选择 **Courses** 选项卡运行`UpdateCourseCredits`方法，然后在浏览器地址栏中 URL 的末尾添加"/ UpdateCourseCredits"到 (例如： `http://localhost:5813/Courses/UpdateCourseCredits`)。 在文本框中输入数字：

![“更新课程学分”页面](advanced/_static/update-credits.png)

单击 **Update**。 你会看到受影响的行数：

![“更新课程学分”页面中受影响的行](advanced/_static/update-credits-rows-affected.png)

单击“返回列表”可以查看课程列表，其中学分已替换为修改后的数字。

请注意，生产代码将确保更新最终生成有效的数据。 此处所示的简化代码可将学分的数字进行相乘，以便足以生成大于 5 的数字。 (`Credits` 属性具有 `[Range(0, 5)]` 特性。)更新查询会起作用，但无效数据可能在系统的其他部分中产生意外结果，这些部分假定学分为 5 或更少。

有关原生 SQL 查询的详细信息，请参阅[原生 SQL 查询](/ef/core/querying/raw-sql)。

## <a name="examine-sql-queries"></a>检查 SQL 查询

有时能够以查看发送到数据库的实际 SQL 查询对于开发者来说是很有用的。 EF Core 自动使用 ASP.NET Core 的内置日志记录功能来编写包含 SQL 查询和更新的日志。 在本部分中，你将看到记录 SQL 日志的一些示例。

打开 *StudentsController.cs* 并在`Details`方法的`if (student == null)`语句上设置断点。

在调试模式下运行应用，并转到某位学生的“详细信息”页面。

转到 **输出** 窗口显示调试输出，就可以看到查询语句：

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

你会注意到一些可能会让你觉得惊讶的操作： SQL 从 Person 表最多选择 2 行 (`TOP(2)`) 。 `SingleOrDefaultAsync`方法服务器上不会解析为 1 行。 原因是：

* 如果查询返回多个行，该方法会返回 null。
* 如果想知道查询是否返回多个行，EF 必须检查是否至少返回 2。

请注意，你不必使用调试模式，并在断点处停止，然后在 **输出** 窗口获取日志记录。 这种方法非常便捷，只需在想查看输出时停止日志记录即可。 如果不进行此操作，程序将继续进行日志记录，要查看感兴趣的部分则必须向后滚动。

## <a name="create-an-abstraction-layer"></a>创建抽象层

许多开发人员编写代码实现存储库和工作模式单元以作为使用 Entity Framework 代码的包装器。 这些模式用于在应用程序的数据访问层和业务逻辑层之间创建抽象层。 实现这些模式可让你的应用程序对数据存储介质的更改不敏感，而且很容易进行自动化单元测试和进行测试驱动开发 (TDD)。 但是，编写附加代码以实现这些模式对于使用 EF 的应用程序并不总是最好的选择，原因有以下几个：

* EF 上下文类可以为使用 EF 的数据库更新充当工作单位类。

* 对于使用 EF 进行的数据库更新，EF 上下文类可充当工作单元类。

* EF 包括用于无需编写存储库代码就实现 TDD 的功能。

有关如何实现存储库和工作单元模式的详细信息，请参阅[本系列教程的 Entity Framework 5 版本](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)。

Entity Framework Core 的实现可用于测试的内存数据库驱动程序。 有关详细信息，请参阅[测试以及 InMemory](/ef/core/miscellaneous/testing/in-memory)。

## <a name="automatic-change-detection"></a>自动脏值检测

Entity Framework 通过比较的实体的当前值与原始值来判断更改实体的方式 （因此需要发送更新到数据库）。 查询或附加该实体时会存储的原始值。 如下方法会导致自动脏值：

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

如果正在跟踪大量实体，并且这些方法之一在循环中多次调用，通过使用`ChangeTracker.AutoDetectChangesEnabled`属性暂时关闭自动脏值检测，能够显著改进性能。 例如：

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Core 源代码与开发计划

Entity Framework Core 源位于 [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)。 仓库中除了有源代码，还可包括每夜生成、 问题跟踪、 功能规范、 设计会议备忘录和[将来的开发路线图](https://github.com/dotnet/efcore/wiki/Roadmap)。 你可以归档或查找 bug 并进行更改。

尽管源代码处于开源状态， Entity Framework Core 是由 Microsoft 完全支持的产品。 Microsoft Entity Framework 团队将控制接受哪些贡献和测试所有的代码更改，以确保每个版本的质量。

## <a name="reverse-engineer-from-existing-database"></a>现有数据库逆向工程

若想要通过对现有数据库中的实体类反向工程得出数据模型，可以使用[scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext)。 可以参阅[入门教程](/ef/core/get-started/aspnetcore/existing-db)。

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>使用动态 LINQ 简化代码

[本系列的第三个教程](sort-filter-page.md)演示如何通过在`switch`语句中对列名称进行硬编码来编写 LINQ 。 如果只有两列可供选择，这种方法可行，但是如果拥有许多列，代码可能会变得冗长。 要解决该问题，可使用 `EF.Property` 方法将属性名称指定为一个字符串。 要尝试此方法，请将 `StudentsController` 中的 `Index` 方法替换为以下代码。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>鸣谢

Tom Dykstra 和 Rick Anderson (twitter @RickAndMSFT) 共同编写了本教程。 Rowan Miller、 Diego Vega 和 Entity Framework 团队的其他成员协助代码评审和帮助解决编写教程代码时出现的调试问题。 John Parente 和 Paul Goldman 致力于更新 ASP.NET Core 2.2 的教程。

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>常见错误疑难解答

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll 被另一个进程使用

错误消息：

> 无法打开“...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- ”进程无法访问文件“...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll”，因为它正在由其他进程使用。

解决方案：

停止 IIS Express 中的站点。 请转到 Windows 系统任务栏中，找到 IIS Express 并右键单击其图标、 选择 Contoso 大学站点，然后单击 **停止站点**。

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>迁移基架的 Up 和 Down 方法中没有代码

可能的原因：

EF CLI 命令不会自动关闭并保存代码文件。 如果在运行`migrations add`命令时，你未保存更改，EF 将找不到所做的更改。

解决方案：

运行`migrations remove`命令，保存你更改的代码并重新运行`migrations add`命令。

### <a name="errors-while-running-database-update"></a>运行数据库更新时出错

在有数据的数据库中进行架构更改时，很有可能发生其他错误。 如果遇到无法解决的迁移错误，你可以更改连接字符串中的数据库名称，或删除数据库。 若要迁移，创建新的数据库，在数据库尚没有数据时使用更新数据库命令更有望完成且不发生错误。

最简单方法是在 appsettings.json 中重命名数据库。 下次运行`database update`时，会创建一个新数据库。

若要在 SSOX 中删除数据库，右键单击数据库，单击 **删除**，然后在 **删除数据库** 对话框框中，选择 **关闭现有连接**，单击 **确定**。

若要使用 CLI 删除数据库，可以运行`database drop`CLI 命令：

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>定位 SQL Server 实例出错

错误消息：

> 建立到 SQL Server 的连接时出现与网络相关或特定于实例的错误。 找不到或无法访问服务器。 请验证实例名称是否正确，SQL Server 是否已配置为允许远程连接。 （提供程序：SQL 网络接口，错误：26 - 定位指定服务器/实例出错）

解决方案：

请检查连接字符串。 如果你已手动删除数据库文件，更改数据库的构造字符串中数据库的名称，然后从头开始使用新的数据库。

## <a name="get-the-code"></a>获取代码

[下载或查看已完成的应用程序。](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>其他资源

有关 EF Core 的详细信息，请参阅[ Entity Framework 的Core文档](/ef/core)。 还提供：[Entity Framework Core 实战](https://www.manning.com/books/entity-framework-core-in-action)一书。

有关如何部署 Web 应用的信息，请参阅 <xref:host-and-deploy/index>。

有关与 ASP.NET Core MVC 相关的其他主题（如身份验证与授权）的信息，请参阅 <xref:index>。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 已执行原始 SQL 查询
> * 已调用查询以返回实体
> * 已调用查询以返回其他类型
> * 已调用更新查询
> * 已检查 SQL 查询
> * 已创建抽象层
> * 已了解自动更改检测
> * 已了解 EF Core 源代码与开发计划
> * 已了解如何使用动态 LINQ 简化代码

这将完成在 ASP.NET Core MVC 应用程序中使用 Entity Framework Core 这一系列教程。 本系列使用的是新建数据库；另一种方式是从现有数据库进行模型的反向工程。

> [!div class="nextstepaction"]
> [教程：EF Core 与 MVC，现有数据库](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
