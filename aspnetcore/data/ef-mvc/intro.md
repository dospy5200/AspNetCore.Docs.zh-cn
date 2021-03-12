---
title: 教程：在 ASP.NET MVC Web 应用中使用 EF Core 入门
description: 本页是介绍如何构建“Contoso University 示例 EF/MVC 应用”系列教程中的第一部分。
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 5bb1a05f5e81730d0785c1d6a69a8cafb68f85ab
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586678"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>教程：在 ASP.NET MVC Web 应用中使用 EF Core 入门

作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 和 Visual Studio 创建 ASP.NET Core MVC Web 应用。

该示例应用是一个虚构的 Contoso University 的网站。 其中包括学生录取、课程创建和讲师分配等功能。 这是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。

## <a name="prerequisites"></a>先决条件

* 如果不熟悉 ASP.NET Core MVC，则在开始前，请浏览 [ASP.NET Core MVC 入门](xref:tutorials/first-mvc-app/start-mvc)教程。

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a>数据库引擎

Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a>解决问题和进行故障排除

如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)对比代码来查找解决方案。 常见错误以及对应的解决方案，请参阅 [最新教程中的故障排除](advanced.md#common-errors)。 如果没有找到遇到的问题的解决方案，可以将问题发布到StackOverflow.com 的 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 版块。

> [!TIP]
> 这是一系列一共有十个教程，其中每个都是在前面教程已完成的基础上继续。 请考虑在完成每一个教程后保存项目的副本。 之后如果遇到问题，你可以从保存的副本中开始寻找问题，而不是从头开始。

## <a name="contoso-university-web-app"></a>Contoso University Web 应用

这些教程中所构建的应用是一个基本的大学网站。

用户可以查看和更新学生、课程和讲师信息。 以下是该应用中的几个屏幕：

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

## <a name="create-web-app"></a>创建 Web 应用

1. 启动 Visual Studio 并选择“创建新项目”。
1. 在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”>“下一步”。
1. 在“配置新项目”对话框中，为“项目名称”输入 `ContosoUniversity`。 请务必使用此名称（含大写），确保在复制代码时与每个 `namespace` 都相匹配。
1. 选择“创建”。
1. 在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：
    1. 下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。
    1. ASP.NET Core Web 应用程序（模型-视图-控制器）。
    1. “创建”
      ![新的 ASP.NET Core 项目对话框](~/data/ef-mvc/intro/_static/new-aspnet5.png)

## <a name="set-up-the-site-style"></a>设置网站样式

设置网站菜单、布局和主页时需作少量基本更改。

打开 Views/Shared/_Layout.cshtml 并进行以下更改：

* 将每次出现的 `ContosoUniversity` 更改为 `Contoso University`。 需要更改三个地方。
* 添加“关于”、“学生”、“课程”“讲师”和“院系”的菜单项，并删除“隐私”菜单项。

前面的更改在以下代码中突出显示：

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

在“Views/Home/Index.cshtml”中，将文件内容替换为以下标记：

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。 主页将显示与本教程中创建的页面对应的选项卡。

![Contoso University 主页](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a>EF Core NuGet 包

本教程使用 SQL Server，相关驱动包[Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。

EF SQL Server 包与其依赖项 `Microsoft.EntityFrameworkCore` 和 `Microsoft.EntityFrameworkCore.Relational` 一起提供 EF 的运行时支持。

添加 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包和 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包。 在包管理器控制台 (PMC) 中，输入以下命令来添加 NuGet 包：

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 包提供 EF Core 错误页的 ASP.NET Core 中间件。 此中间件有助于检测和诊断 EF Core 迁移错误。

有关其他可用于 EF Core 的数据库提供程序，请参阅[数据库提供程序](/ef/core/providers/)。

## <a name="create-the-data-model"></a>创建数据模型

将为此应用创建以下实体类：

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

前面的实体具有以下关系：

* `Student` 和 `Enrollment` 实体之间存在一对多关系。 一名学生可以报名参加任意数量的课程。
* `Course` 和 `Enrollment` 实体之间存在一对多关系。 一门课程中可以包含任意数量的学生。

以下部分将为这几个实体中的每一个实体创建一个类。

### <a name="the-student-entity"></a>Student 实体

![Student 实体关系图](intro/_static/student-entity.png)

在 Models 文件夹中，使用以下代码创建 `Student` 类：

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID` 属性是对应于此类的数据库表的主键 (PK) 列。 默认情况下，EF 将名为 `ID` 或 `classnameID` 的属性解释为主键。 例如，可以将 PK 命名为 `StudentID` 而不是 `ID`。

`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。 导航属性中包含与此实体相关的其他实体。 `Student` 实体的 `Enrollments` 属性：

* 包含与该 `Student` 实体相关的所有 `Enrollment` 实体。
* 如果数据库中的特定 `Student` 行具有两个相关的 `Enrollment` 行：
  * `Student` 实体的 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。
  
`Enrollment` 行在 `StudentID` 外键 (FK) 列中包含学生的 PK 值。

如果导航属性可以包含多个实体：

 * 类型必须为列表，如 `ICollection<T>`、`List<T>` 或 `HashSet<T>`。
 * 可以添加、删除和更新实体。

多对多和一对多导航关系可包含多个实体。 使用 `ICollection<T>` 时，EF 会默认创建 `HashSet<T>` 集合。

### <a name="the-enrollment-entity"></a>Enrollment 实体

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

在 Models 文件夹中，使用以下代码创建 `Enrollment` 类：

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` 属性为 PK。 该实体使用 `ID` 模式，而不是本身使用的 `classnameID` 模式。 `Student` 实体使用 `ID` 模式。 某些开发人员更喜欢在整个数据模型中使用一种模式。 在本教程中，这种变化说明了两种模式都可以使用。 [后面的教程](inheritance.md)介绍，使用不包含 classname 的 `ID` 可以更轻松地在数据模型中实现继承。

`Grade` 属性为 `enum`。 `Grade` 类型声明后的 `?` 表示 `Grade` 属性可以为 [null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)。 `null` 级别与零级别不同。 `null` 表示级别未知或尚未分配。

`StudentID` 属性是外键 (FK)，其对应的导航属性为 `Student`。 `Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只能包含一个 `Student` 实体。 这与 `Student.Enrollments` 导航属性不同，后者可包含多个 `Enrollment` 实体。

`CourseID` 属性是 FK，其对应的导航属性为 `Course`。 `Enrollment` 实体与一个 `Course` 实体相关联。

如果将属性命名为`<`导航属性名称`><`主键属性名称`>`，则 Entity Framework 将该属性解释为 FK 属性。 例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的 PK 为 `ID`。 还可以将 FK 属性命名为 `<`主键属性名称`>`。 例如 `CourseID`，因为 `Course` 实体的 PK 为 `CourseID`。

### <a name="the-course-entity"></a>Course 实体

![Course 实体关系图](intro/_static/course-entity.png)

在 Models 文件夹中，使用以下代码创建 `Course` 类：

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 属性是导航属性。 `Course` 实体可与任意数量的 `Enrollment` 实体相关。

[DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) 属性将在[后面的教程部分](complex-data-model.md)中介绍。 该属性允许为课程输入 PK，而不是让数据库生成 PK。

## <a name="create-the-database-context"></a>创建数据库上下文

<xref:Microsoft.EntityFrameworkCore.DbContext> 数据库上下文类是为给定数据模型协调 EF 功能的主类。 此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。 `DbContext` 派生类指定数据模型中包含哪些实体。 某些 EF 行为可自定义。 在此项目中将数据库上下文类命名为 `SchoolContext`。

在项目文件夹中，创建名为的文件夹 `Data`。

在 Data 文件夹中，使用以下代码创建 `SchoolContext` 类：

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

前面的代码为每个实体集创建 `DbSet` 属性。 在 EF 术语中：

* 实体集通常对应数据库表。
* 实体对应表中的行。

可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>` 语句，实现的功能没有任何改变。 EF 将隐式包含它们，原因如下：

* `Student` 实体引用 `Enrollment` 实体。
* `Enrollment` 实体引用 `Course` 实体。

当数据库创建完成后， EF 创建一系列数据表，表名默认和 `DbSet` 属性名相同。 集合的属性名通常采用复数形式。 例如，使用 `Students`，而不使用 `Student`。 开发者对表名称是否应为复数意见不一。 在这些教程中，在 `DbContext` 中指定单数形式的表名称会覆盖默认行为。 此教程在最后一个 DbSet 属性之后，添加以下高亮显示的代码。

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>注册 `SchoolContext`

ASP.NET Core 包含[依赖关系注入](../../fundamentals/dependency-injection.md)。 在应用启动过程中通过依赖项注入来注册相关服务，例如 EF 数据库上下文。 需要这些服务的组件（如 MVC 控制器）可以通过构造函数参数来获得这些服务。 本教程后面部分将展示获取上下文实例的控制器构造函数代码。

若要将 `SchoolContext` 注册为一种服务，打开 *Startup.cs* ，并将高亮代码添加到 `ConfigureServices` 方法中。

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

通过调用 `DbContextOptionsBuilder` 中的一个方法将数据库连接字符串在配置文件中的名称传递给上下文对象。 进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。

打开 *appsettings.json* 文件，并按以下标记所示添加连接字符串：

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a>添加数据库异常筛选器

将 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> 添加到 `ConfigureServices`，如下面的代码所示：

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=6)]

`AddDatabaseDeveloperPageExceptionFilter` 在[开发环境](xref:fundamentals/environments)中提供有用的错误信息。

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。 LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。 LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。 默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件。

## <a name="initialize-db-with-test-data"></a>使用测试数据初始化数据库

EF 将创建空数据库。 在本部分中，你将添加一个方法，该方法将在数据库创建后调用，以向数据库填充测试数据。

`EnsureCreated` 方法用于自动创建数据库。 在[后面的教程](migrations.md)中，你将了解如何处理模型更改，方法是使用 Code First 迁移来更改数据库架构，而不是删除和重新创建数据库。

在 Data 文件夹中，使用以下代码创建一个名为 `DbInitializer` 的新类：

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

前面的代码检查数据库是否存在：

* 如果找不到数据库；
  * 则创建一个数据库并使用测试数据加载。 代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。
* 如果能找到数据库，则不执行任何操作。

使用以下代码更新 Program.cs：

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

Program.cs 在应用启动时执行以下操作：

* 从依赖注入容器中获取数据库上下文实例。
* 调用 `DbInitializer.Initialize` 方法。
* 当 `Initialize` 方法完成时释放上下文，如以下代码所示：

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

第一次运行该应用时，会创建数据库并使用测试数据加载该数据库。 每当数据模型发生更改时：

* 删除数据库。
* 更新 Seed 方法，并使用新数据库重新开始。

 在后面的教程中，在更改数据模型时会修改数据库，而不会删除和重新创建数据库。 数据模型发生更改时不会丢失任何数据。

## <a name="create-controller-and-views"></a>创建控制器和视图

使用 Visual Studio 中的基架引擎添加一个 MVC 控制器，以及使用 EF 来查询和保存数据的视图。

[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 操作方法和视图的自动创建被称为基架。

* 在“解决方案资源管理器”中，右键单击 `Controllers` 文件夹，然后选择“添加”>“新搭建基架的项目”。
* 在“添加基架”对话框中：
  * 选择 **视图使用 Entity Framework 的 MVC 控制器**。
  * 单击 **添加**。 随即将显示“使用 Entity Framework 添加包含视图的 MVC 控制器”对话框：![构架 Student](intro/_static/scaffold-student2.png)
  * 在“模型类”中选择“Student”。
  * 在“数据上下文类”中选择“SchoolContext” 。
  * 使用 **StudentsController** 作为默认名称。
  * 单击 **添加**。

Visual Studio 基架引擎创建 `StudentsController.cs` 文件和一组对应于控制器的视图（`*.cshtml` 文件）。

注意控制器将 `SchoolContext` 作为构造函数参数。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core 依赖关系注入负责将 `SchoolContext` 实例传递到控制器。 你在 `Startup` 类中对其进行配置。

控制器包含 `Index` 操作方法，用于显示数据库中的所有学生。 该方法从学生实体集中获取学生列表，学生实体集则是通过读取数据库上下文实例中的 `Students` 属性获得：

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

本教程后面部分将介绍此代码中的异步编程元素。

*Views/Students/Index.cshtml* 视图使用table标签显示此列表：

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。

单击学生选项卡以查看 `DbInitializer.Initialize` 插入的测试的数据。 你将看到 `Students` 选项卡链接在页的顶部或在单击右上角后的导航图标中，具体显示在哪里取决于浏览器窗口宽度。

![Contoso University 主页宽窄](intro/_static/home-page-narrow.png)

![“学生索引”页](intro/_static/students-index.png)

## <a name="view-the-database"></a>查看数据库

启动应用时，`DbInitializer.Initialize` 方法会调用 `EnsureCreated`。 EF 看到没有数据库：

* 因此，它创建了一个数据库。
* `Initialize` 方法代码用数据填充数据库。

在 Visual Studio 中使用“SQL Server 对象资源管理器”(SSOX) 查看数据库：

* 从 Visual Studio 中的“视图”菜单选择“SQL Server 对象资源管理器”。
* 在 SSOX 中，选择“(localdb)\MSSQLLocalDB”>“数据库”。
* 选择 *appsettings.json* 文件中的连接字符串中的数据库名称的实体 `ContosoUniversity1`。
* 展开“表”节点，查看数据库中的表。

![SSOX 中的表](intro/_static/ssox-tables.png)

右键单击“Student”表，然后单击“查看数据”以查看表中的数据。

![SSOX 中的 Student 表](intro/_static/ssox-student-table.png)

`*.mdf` 和 `*.ldf` 数据库文件位于 *C:\Users\\\<username>* 文件夹中。

由于 `EnsureCreated` 是在应用启动时运行的初始化方法中调用的，因此可以执行以下操作：

* 对 `Student` 类进行更改。
* 删除数据库。
* 停止，然后启动应用。 将自动重新创建数据库以匹配此更改。

例如，如果向 `Student` 类添加 `EmailAddress` 属性，则重新创建的表中会有新的 `EmailAddress` 列。 分类的视图不会显示新的 `EmailAddress` 属性。

## <a name="conventions"></a>约定

因为使用了 EF 使用的约定，为使 EF 创建完整数据库而编写的代码量是最小的：

* `DbSet` 类型的属性用作表名。 如果实体未被 `DbSet` 属性引用，实体类名称用作表名称。
* 使用实体属性名作为列名。
* 命名为 `ID` 或 `classnameID` 的实体属性识别为 PK 属性。
* 如果将属性命名为`<`导航属性名称`><`PK 属性名称`>`，则该属性解释为 FK 属性。 例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的 PK 为 `ID`。 还可以将 FK 属性命名为 `<`主键属性名称`>`。 例如 `EnrollmentID`，因为 `Enrollment` 实体的 PK 为 `EnrollmentID`。

约定行为可以重写。 例如，可以显式指定表名，如本教程中前面部分所示。 列名称和任何属性都可以设置为 PK 或 FK。

## <a name="asynchronous-code"></a>异步代码

异步编程是 ASP.NET Core 和 EF Core 的默认模式。

Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。 当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。 使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。 使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。 因此，异步代码使得服务器更有效地使用资源，并且该服务器可以无延迟地处理更多流量。

异步代码在运行时，会引入的少量开销，在低流量时对性能的影响可以忽略不计，但在针对高流量情况下潜在的性能提升是可观的。

在下面的代码中，`async`、`Task<T>`、`await` 和 `ToListAsync` 使代码以异步方式执行。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async` 关键字用于告知编译器该方法主体将生成回调并自动创建 `Task<IActionResult>` 返回对象。
* 返回类型 `Task<IActionResult>` 表示正在进行的工作返回的结果为 `IActionResult` 类型。
* `await` 关键字会使得编译器将方法拆分为两个部分。 第一部分是以异步方式结束已启动的操作。 第二部分是当操作完成时注入调用回调方法的地方。
* `ToListAsync` 是 `ToList` 扩展方法的异步版本。

编写使用 EF 的异步代码时需要注意的一些事项：

* 只有导致查询或发送数据库命令的语句才能以异步方式执行。 包括 `ToListAsync`， `SingleOrDefaultAsync`，和 `SaveChangesAsync`。 不包括只需更改 `IQueryable` 的语句，如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。
* EF 上下文是线程不安全的： 请勿尝试并行执行多个操作。 当调用异步 EF 方法时，始终使用 `await` 关键字。
* 若要利用异步代码的性能优势，请确保所使用的任何库包在它们调用导致发送至数据库的查询的任何 EF 方法时也使用异步。

有关在 .NET 异步编程的详细信息，请参阅 [异步概述](/dotnet/articles/standard/async)。

## <a name="limit-entities-fetched"></a>限制提取的实体数

有关限制从查询返回的实体数的信息，请参阅[性能注意事项](xref:data/ef-rp/intro)。

请继续阅读下一篇教程，了解如何执行基本的 CRUD（创建、读取、更新、删除）操作。

> [!div class="nextstepaction"]
> [实现基本的 CRUD 功能](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Contoso University 示例 Web 应用程序演示如何使用 Entity Framework (EF) Core 2.2 和 Visual Studio 2017 或 2019 创建 ASP.NET Core 2.2 MVC Web 应用程序。

本教程没有针对 ASP.NET Core 3.1 进行更新。 它已针对 [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0) 进行了更新。

示例应用程序供一个虚构的 Contoso 大学网站使用。 它包括诸如学生入学、 课程创建和导师分配等功能。 这是一系列教程中的第一个，这一系列教程主要展示了如何从零开始构建 Contoso 大学示例应用程序。

## <a name="prerequisites"></a>先决条件

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 包含以下工作负荷：
  * ASP.NET 和 Web 开发工作负荷
  * .NET Core 跨平台开发工作负荷

## <a name="troubleshooting"></a>疑难解答

如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)对比代码来查找解决方案。 常见错误以及对应的解决方案，请参阅 [最新教程中的故障排除](advanced.md#common-errors)。 如果没有找到遇到的问题的解决方案，可以将问题发布到StackOverflow.com 的 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 版块。

> [!TIP]
> 这是一系列一共有十个教程，其中每个都是在前面教程已完成的基础上继续。 请考虑在完成每一个教程后保存项目的副本。 之后如果遇到问题，你可以从保存的副本中开始寻找问题，而不是从头开始。

## <a name="contoso-university-web-app"></a>Contoso University Web 应用

你将在这些教程中学习构建一个简单的大学网站的应用程序。

用户可以查看和更新学生、 课程和教师信息。 以下是一些你即将创建的页面。

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

## <a name="create-web-app"></a>创建 Web 应用

* 打开 Visual Studio。

* 从“文件”菜单中选择“新建”>“项目” 。

* 从左窗格中依次选择“已安装”>“Visual C#”>“Web”。

* 选择“ASP.NET Core Web 应用程序”项目模板。

* 输入“ContosoUniversity”作为名称，然后单击“确定” 。

  ![“新建项目”对话框](intro/_static/new-project2.png)

* 等待“新建 ASP.NET Core Web 应用程序”对话框显示出来。

* 选择“.NET Core”、“ASP.NET Core 2.2”和“Web 应用程序(模型-视图-控制器)”模板  。

* 请确保 **身份验证** 设置为  **不进行身份验证**。

* 选择“确定”

  ![新的 ASP.NET Core 项目对话框](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>设置网站样式

通过几个简单的更改设置站点菜单、 布局和主页。

打开 Views/Shared/_Layout.cshtml 并进行以下更改：

* 将文件中的"ContosoUniversity"更改为"Contoso University"。 需要更改三个地方。

* 添加“关于”、“学生”、“课程”“讲师”和“院系”的菜单项，并删除“隐私”菜单项。

突出显示所作更改。

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

在 *Views/Home/Index.cshtml*，将文件的内容替换为以下代码以将有关 ASP.NET 和 MVC 的内容替换为有关此应用程序的内容：

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

按 CTRL + F5 来运行该项目或从菜单选择 **调试 > 开始执行(不调试)** 。 你会看到首页，以及通过这个教程创建的页对应的选项卡。

![Contoso University 主页](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>关于 EF Core NuGet 包

若要为项目添加 EF Core 支持，需要安装相应的数据库驱动包。 本教程使用 SQL Server，相关驱动包[Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。 此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，因此无需引用该包。

EF SQL Server 包和其依赖项（`Microsoft.EntityFrameworkCore` 和 `Microsoft.EntityFrameworkCore.Relational`）一起提供 EF 的运行时支持。 你将在之后的 [迁移](migrations.md) 教程中学习添加工具包。

有关其他可用于 EF Core 的数据库驱动的信息，请参阅 [数据库驱动](/ef/core/providers/)。

## <a name="create-the-data-model"></a>创建数据模型

接下来你将创建 Contoso 大学应用程序的实体类。 你将从以下三个实体类开始。

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

`Student` 和 `Enrollment`实体之间是一对多的关系，`Course` 和`Enrollment` 实体之间也是一个对多的关系。 换而言之，一名学生可以修读任意数量的课程, 并且某一课程可以被任意数量的学生修读。

接下来，你将创建与这些实体对应的类。

### <a name="the-student-entity"></a>Student 实体

![Student 实体关系图](intro/_static/student-entity.png)

在 *Models* 文件夹中，创建一个名为 *Student.cs* 的类文件并且将模板代码替换为以下代码。

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID` 属性将成为对应于此类的数据库表中的主键。 默认情况下，EF 将会将名为 `ID` 或 `classnameID` 的属性解析为主键。

`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。 导航属性中包含与此实体相关的其他实体。 在这个案例下，`Student entity` 中的 `Enrollments` 属性会保留所有与 `Student` 实体相关的 `Enrollment`。 换而言之，如果数据库中的 `Student` 行具有两个相关的 `Enrollment` 行（在其 StudentID 外键列中包含该学生的主键值的行），则该 `Student` 实体的 `Enrollments` 导航属性将包含这两个 `Enrollment` 实体。

如果导航属性可以具有多个实体 （如多对多或一对多关系），那么导航属性的类型必须是可以添加、 删除和更新条目的容器，如 `ICollection<T>`。 你可以指定 `ICollection<T>` 或实现该接口类型，如 `List<T>` 或 `HashSet<T>`。 如果指定 `ICollection<T>`，EF在默认情况下创建 `HashSet<T>` 集合。

### <a name="the-enrollment-entity"></a>Enrollment 实体

![修读实体关系图](intro/_static/enrollment-entity.png)

在 *Models* 文件夹中，创建 *Enrollment.cs* 并且用以下代码替换现有代码：

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` 属性将被设为主键; 此实体使用 `classnameID` 模式而不是如 `Student` 实体那样直接使用 `ID`。 通常情况下，你选择一个主键模式，并在你的数据模型自始至终使用这种模式。 在这里，使用了两种不同的模式只是为了说明你可以使用任一模式来指定主键。 在 [后面的教程](inheritance.md)，你将了解到使用`ID`这种模式可以更轻松地在数据模型之间实现继承。

`Grade` 属性是 `enum`。 `Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。 评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。

`StudentID` 属性是一个外键，`Student` 是与其且对应的导航属性。 `Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含单个 `Student` 实体 (与前面所看到的 `Student.Enrollments` 导航属性不同后，`Student`中可以容纳多个 `Enrollment` 实体)。

`CourseID` 属性是一个外键， `Course` 是与其对应的导航属性。 `Enrollment` 实体与一个 `Course` 实体相关联。

如果一个属性名为 `<navigation property name><primary key property name>`，Entity Framework 就会将这个属性解析为外键属性(例如， `Student` 实体的主键是`ID`， `Student` 是`Enrollment`的导航属性所以`Enrollment`实体中 `StudentID` 会被解析为外键)。 此外还可以将需要解析为外键的属性命名为 `<primary key property name>` (例如，`CourseID` 由于 `Course` 实体的主键所以 `CourseID` 也被解析为外键)。

### <a name="the-course-entity"></a>Course 实体

![Course 实体关系图](intro/_static/course-entity.png)

在 *Models* 文件夹中，创建 *Course.cs* 并且用以下代码替换现有代码：

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 属性是导航属性。 一个 `Course` 体可以与任意数量的 `Enrollment` 实体相关。

我们在本系列 [后面的教程](complex-data-model.md) 中会有更多有关 `DatabaseGenerated` 特性的例子。 简单来说，此特性让你能自行指定主键，而不是让数据库自动指定主键。

## <a name="create-the-database-context"></a>创建数据库上下文

使得给定的数据模型与 Entity Framework 功能相协调的主类是数据库上下文类。 可以通过继承 `Microsoft.EntityFrameworkCore.DbContext` 类的方式创建此类。 在该类中你可以指定数据模型中包含哪些实体。 你还可以定义某些 Entity Framework 行为。 在此项目中将数据库上下文类命名为 `SchoolContext`。

在项目文件夹中，创建名为的文件夹 *Data*。

在 *Data* 文件夹创建名为 *SchoolContext.cs* 的类文件，并将模板代码替换为以下代码：

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

此代码将为每个实体集创建 `DbSet` 属性。 在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。

在这里可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>` 语句，实现的功能没有任何改变。 Entity Framework 会隐式包含这两个实体因为 `Student` 实体引用了 `Enrollment` 实体、`Enrollment` 实体引用了 `Course` 实体。

当数据库创建完成后， EF 创建一系列数据表，表名默认和 `DbSet` 属性名相同。 集合属性的名称一般使用复数形式，但不同的开发人员的命名习惯可能不一样，开发人员根据自己的情况确定是否使用复数形式。 在定义 DbSet 属性的代码之后，添加下面高亮代码，对 DbContext 指定单数的表名来覆盖默认的表名。 此教程在最后一个 DbSet 属性之后，添加以下高亮显示的代码。

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

生成项目以检查编译器错误。

## <a name="register-the-schoolcontext"></a>注册 SchoolContext

ASP.NET Core 默认实现 [依赖注入](../../fundamentals/dependency-injection.md)。 在应用程序启动过程通过依赖注入注册相关服务 （例如 EF 数据库上下文）。 需要这些服务的组件 （如 MVC 控制器） 可以通过向构造函数添加相关参数来获得对应服务。 在本教程后面你将看到控制器构造函数的代码，就是通过上述方式获得上下文实例。

若要将 `SchoolContext` 注册为一种服务，打开 *Startup.cs* ，并将高亮代码添加到 `ConfigureServices` 方法中。

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

通过调用 `DbContextOptionsBuilder` 中的一个方法将数据库连接字符串在配置文件中的名称传递给上下文对象。 进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。

添加 `using` 语句引用 `ContosoUniversity.Data` 和 `Microsoft.EntityFrameworkCore` 命名空间，然后生成项目。

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

打开 *appsettings.json* 文件，并按以下所示添加连接字符串。

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

数据库连接字符串指定使用 SQL Server LocalDB 数据库。 LocalDB 是 SQL Server Express 数据库引擎的轻量级版本，用于应用程序开发，不在生产环境中使用。 LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。 默认情况下， LocalDB 在 `C:/Users/<user>` 目录下创建 *.mdf* 数据库文件。

## <a name="initialize-db-with-test-data"></a>使用测试数据初始化数据库

Entity Framework 已经为你创建了一个空数据库。 在本部分中，你将编写一个方法用于向数据库填充测试数据，该方法会在数据库创建完成之后执行。

此处将使用 `EnsureCreated` 方法来自动创建数据库。 在 [后面的教程](migrations.md) 你将了解如何通过使用 Code First Migration 来更改而不是删除并重新创建数据库来处理模型更改。

在 *Data* 文件夹中，创建名为的新类文件 *DbInitializer.cs* 并且将模板代码替换为以下代码，使得在需要时能创建数据库并向其填充测试数据。

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

这段代码首先检查是否有学生数据在数据库中，如果没有的话，就可以假定数据库是新建的，然后使用测试数据进行填充。 代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。

在 *Program.cs*，修改 `Main` 方法，使得在应用程序启动时能执行以下操作：

* 从依赖注入容器中获取数据库上下文实例。
* 调用 seed 方法，将上下文传递给它。
* Seed 方法完成此操作时释放上下文。

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

首次运行该应用程序时，将创建数据库并使用测试数据填充该数据库。 每次更改数据模型时：

 * 删除数据库。
 * 更新 Seed 方法，并以相同的方式用新数据库重新开始。
 
在之后的教程中，你将了解如何在数据模型更改时，只需修改数据库而无需删除重建数据库。

## <a name="create-controller-and-views"></a>创建控制器和视图

在本节，将使用 Visual Studio 中的基架引擎添加一个 MVC 控制器，以及使用 EF 来查询和保存数据的视图。

CRUD 操作方法和视图的自动创建被称为基架。 基架与代码生成不同，基架的代码是一个起点，可以修改基架以满足自己需求，而你通常无需修改生成的代码。 当你需要自定义生成代码时，可使用一部分类或需求发生变化时重新生成代码。

* 右键单击 **解决方案资源管理器** 中的 **Controllers** 文件夹选择 **添加 > 新搭建基架的项目**。
* 在“添加基架”对话框中：
  * 选择 **视图使用 Entity Framework 的 MVC 控制器**。
  * 单击 **添加**。 随即将显示“使用 Entity Framework 添加包含视图的 MVC 控制器”对话框：![构架 Student](intro/_static/scaffold-student2.png)
  * 在 **模型类** 选择 **Student**。
  * 在“数据上下文类”中选择 SchoolContext 。
  * 使用 **StudentsController** 作为默认名称。
  * 单击 **添加**。

Visual Studio 基架引擎创建 StudentsController.cs 文件和一组对应于控制器的视图（.cshtml 文件）

注意控制器将 `SchoolContext` 作为构造函数参数。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core 依赖关系注入负责将 `SchoolContext` 实例传递到控制器。 在 Startup.cs 文件中对其进行了配置。

控制器包含 `Index` 操作方法，用于显示数据库中的所有学生。 该方法从学生实体集中获取学生列表，学生实体集则是通过读取数据库上下文实例中的 `Students` 属性获得：

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

本教程后面部分将介绍此代码中的异步编程元素。

*Views/Students/Index.cshtml* 视图使用table标签显示此列表：

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。

单击学生选项卡以查看 `DbInitializer.Initialize` 插入的测试的数据。 你将看到 `Students` 选项卡链接在页的顶部或在单击右上角后的导航图标中，具体显示在哪里取决于浏览器窗口宽度。

![Contoso University 主页宽窄](intro/_static/home-page-narrow.png)

![“学生索引”页](intro/_static/students-index.png)

## <a name="view-the-database"></a>查看数据库

当你启动了应用程序，`DbInitializer.Initialize` 方法调用 `EnsureCreated`。 EF 没有检测到相关数据库，因此自己创建了一个，接着 `Initialize` 方法的其余代码向数据库中填充数据。 你可以使用 Visual Studio 中的 **SQL Server 对象资源管理器** (SSOX) 查看数据库。

关闭浏览器。

如果 SSOX 窗口尚未打开，请从Visual Studio 中的 **视图** 菜单中选择。

在 SSOX 中，单击“(localdb)\MSSQLLocalDB”>“数据库”，然后单击 *appsettings.json* 文件中的连接字符串中的数据库名称的实体。

展开“表”节点，查看数据库中的表。

![SSOX 中的表](intro/_static/ssox-tables.png)

右键单击 **Student** 表，然后单击 **查看数据**，即可查看已创建的列和已插入到表的行。

![SSOX 中的 Student 表](intro/_static/ssox-student-table.png)

*.mdf* 和 *.ldf* 数据库文件位于 *C:\Users\\\<username>* 文件夹中。

因为调用 `EnsureCreated` 的初始化方法在启动应用程序时才运行，所以在这之前你可以更改 `Student` 类、 删除数据库、 再运行一次应用程序，这时候数据库将自动重新创建，以匹配所做的更改。 例如，如果向 `Student` 类添加 `EmailAddress` 属性，重新的创建表中会有 `EmailAddress` 列。

## <a name="conventions"></a>约定

由于 Entity Framework 有一定的约束条件，你只需要按规则编写很少的代码就能够创建一个完整的数据库。

* `DbSet` 类型的属性用作表名。 如果实体未被 `DbSet` 属性引用，实体类名称用作表名称。
* 使用实体属性名作为列名。
* 以 ID 或 classnameID 命名的实体属性被视为主键属性。
* 如果属性名为 *\<navigation property name>\<primary key property name>* 将被解释为外键属性 (例如，`StudentID` 对应 `Student` 导航属性，`Student` 实体的主键是`ID`，所以`StudentID`被解释为外键属性)。 此外也可以将外键属性命名为 *\<primary key property name>* (例如，`EnrollmentID`，由于 `Enrollment` 实体的主键是 `EnrollmentID`，因此被解释为外键)。

约定行为可以重写。 例如，本教程前半部分显式指定表名称。 本系列 [后面教程](complex-data-model.md) 则设置列名称并将任何属性设置为主键或外键。

## <a name="asynchronous-code"></a>异步代码

异步编程是 ASP.NET Core 和 EF Core 的默认模式。

Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。 当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。 使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。 使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。 因此，异步代码使得服务器更有效地使用资源，并且该服务器可以无延迟地处理更多流量。

异步代码在运行时，会引入的少量开销，在低流量时对性能的影响可以忽略不计，但在针对高流量情况下潜在的性能提升是可观的。

在以下代码中，`async` 关键字、`Task<T>` 返回值、`await` 关键字和 `ToListAsync` 方法让代码异步执行。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async` 关键字用于告知编译器该方法主体将生成回调并自动创建 `Task<IActionResult>` 返回对象。
* 返回类型 `Task<IActionResult>` 表示正在进行的工作返回的结果为 `IActionResult` 类型。
* `await` 关键字会使得编译器将方法拆分为两个部分。 第一部分是以异步方式结束已启动的操作。 第二部分是当操作完成时注入调用回调方法的地方。
* `ToListAsync` 是 `ToList` 方法的的异步扩展版本。

使用 Entity Framework 编写异步代码时的一些注意事项：

* 只有导致查询或发送数据库命令的语句才能以异步方式执行。 包括 `ToListAsync`， `SingleOrDefaultAsync`，和 `SaveChangesAsync`。 不包括只需更改 `IQueryable` 的语句，如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。
* EF 上下文是线程不安全的： 请勿尝试并行执行多个操作。 当调用异步 EF 方法时，始终使用 `await` 关键字。
* 如果你想要利用异步代码的性能优势，请确保你所使用的任何库和包在它们调用导致 Entity Framework 数据库查询方法时也使用异步。

有关在 .NET 异步编程的详细信息，请参阅 [异步概述](/dotnet/articles/standard/async)。

## <a name="next-steps"></a>后续步骤

请继续阅读下一篇教程，了解如何执行基本的 CRUD（创建、读取、更新、删除）操作。

> [!div class="nextstepaction"]
> [实现基本的 CRUD 功能](crud.md)

::: moniker-end
