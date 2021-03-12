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
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="6b177-103">教程：了解高级方案 - ASP.NET MVC 和 EF Core</span><span class="sxs-lookup"><span data-stu-id="6b177-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="6b177-104">之前的教程中，已经以每个类一张表的方式实现了继承。</span><span class="sxs-lookup"><span data-stu-id="6b177-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="6b177-105">本教程将会介绍在掌握开发基础 ASP.NET Core web 应用程序之后使用 Entity Framework Core 开发时需要注意的几个问题。</span><span class="sxs-lookup"><span data-stu-id="6b177-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="6b177-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="6b177-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6b177-107">执行原始 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="6b177-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="6b177-108">调用查询以返回实体</span><span class="sxs-lookup"><span data-stu-id="6b177-108">Call a query to return entities</span></span>
> * <span data-ttu-id="6b177-109">调用查询以返回其他类型</span><span class="sxs-lookup"><span data-stu-id="6b177-109">Call a query to return other types</span></span>
> * <span data-ttu-id="6b177-110">调用更新查询</span><span class="sxs-lookup"><span data-stu-id="6b177-110">Call an update query</span></span>
> * <span data-ttu-id="6b177-111">检查 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="6b177-111">Examine SQL queries</span></span>
> * <span data-ttu-id="6b177-112">创建抽象层</span><span class="sxs-lookup"><span data-stu-id="6b177-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="6b177-113">了解自动更改检测</span><span class="sxs-lookup"><span data-stu-id="6b177-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="6b177-114">了解 EF Core 源代码与开发计划</span><span class="sxs-lookup"><span data-stu-id="6b177-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="6b177-115">了解如何使用动态 LINQ 简化代码</span><span class="sxs-lookup"><span data-stu-id="6b177-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6b177-116">先决条件</span><span class="sxs-lookup"><span data-stu-id="6b177-116">Prerequisites</span></span>

* [<span data-ttu-id="6b177-117">实现继承</span><span class="sxs-lookup"><span data-stu-id="6b177-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="6b177-118">执行原始 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="6b177-118">Perform raw SQL queries</span></span>

<span data-ttu-id="6b177-119">使用 Entity Framework 的优点之一是它可避免你编写跟数据库过于耦合的代码</span><span class="sxs-lookup"><span data-stu-id="6b177-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="6b177-120">它会自动生成 SQL 查询和命令，使得你无需自行编写。</span><span class="sxs-lookup"><span data-stu-id="6b177-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="6b177-121">但有一些特殊情况，你需要执行手动创建的特定 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="6b177-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="6b177-122">对于这些情况下， Entity Framework Code First API 包括直接传递 SQL 命令将到数据库的方法。</span><span class="sxs-lookup"><span data-stu-id="6b177-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="6b177-123">在 EF Core 1.0 中具有以下选项：</span><span class="sxs-lookup"><span data-stu-id="6b177-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="6b177-124">使用`DbSet.FromSql`返回实体类型的查询方法。</span><span class="sxs-lookup"><span data-stu-id="6b177-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="6b177-125">返回的对象必须是`DbSet`对象期望的类型，并且它们会自动跟踪数据库上下文中除非你[手动关闭跟踪](crud.md#no-tracking-queries)。</span><span class="sxs-lookup"><span data-stu-id="6b177-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="6b177-126">对于非查询命令使用`Database.ExecuteSqlCommand`。</span><span class="sxs-lookup"><span data-stu-id="6b177-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="6b177-127">如果需要运行该返回类型不是实体的查询，你可以使用由 EF 提供的 ADO.NET 中使用数据库连接。</span><span class="sxs-lookup"><span data-stu-id="6b177-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="6b177-128">数据库上下文不会跟踪返回的数据，即使你使用该方法来检索实体类型也是如此。</span><span class="sxs-lookup"><span data-stu-id="6b177-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="6b177-129">在 Web 应用程序中执行 SQL 命令时，请务必采取预防措施来保护站点免受 SQL 注入攻击。</span><span class="sxs-lookup"><span data-stu-id="6b177-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="6b177-130">一种方法是使用参数化查询，确保不会将网页提交的字符串视为 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="6b177-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="6b177-131">在本教程中，将用户输入集成到查询中时会使用参数化查询。</span><span class="sxs-lookup"><span data-stu-id="6b177-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="6b177-132">调用查询以返回实体</span><span class="sxs-lookup"><span data-stu-id="6b177-132">Call a query to return entities</span></span>

<span data-ttu-id="6b177-133">`DbSet<TEntity>` 类提供了一种方法，可用于执行返回 `TEntity` 类型实体的查询。</span><span class="sxs-lookup"><span data-stu-id="6b177-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="6b177-134">若要查看实现细节，你需要更改院系控制器中 `Details` 方法的代码。</span><span class="sxs-lookup"><span data-stu-id="6b177-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="6b177-135">在 DepartmentsController.cs 的 `Details` 方法中，使用 `FromSql` 方法调用替换检索院系的代码，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="6b177-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="6b177-136">为了验证新代码是否正常工作，请选择“院系”选项卡，然后选择其中某一院系的“详细信息” 。</span><span class="sxs-lookup"><span data-stu-id="6b177-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![院系详细信息](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="6b177-138">调用查询以返回其他类型</span><span class="sxs-lookup"><span data-stu-id="6b177-138">Call a query to return other types</span></span>

<span data-ttu-id="6b177-139">之前你在“关于”页面创建了一个学生统计信息网格，显示每个注册日期的学生数量。</span><span class="sxs-lookup"><span data-stu-id="6b177-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="6b177-140">可以从学生实体集中获取数据 (`_context.Students`) ，使用 LINQ 将结果投影到`EnrollmentDateGroup`视图模型对象的列表。</span><span class="sxs-lookup"><span data-stu-id="6b177-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="6b177-141">假设你想要 SQL 本身编写，而不使用 LINQ。</span><span class="sxs-lookup"><span data-stu-id="6b177-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="6b177-142">需要运行 SQL 查询中返回实体对象之外的内容。</span><span class="sxs-lookup"><span data-stu-id="6b177-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="6b177-143">在 EF Core 1.0 中，执行该操作的另一种方法是编写 ADO.NET 代码，并从 EF 获取数据库连接。</span><span class="sxs-lookup"><span data-stu-id="6b177-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="6b177-144">在 *HomeController.cs* 中，将`About`方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6b177-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="6b177-145">添加 using 语句：</span><span class="sxs-lookup"><span data-stu-id="6b177-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="6b177-146">运行应用并转到“关于”页面。</span><span class="sxs-lookup"><span data-stu-id="6b177-146">Run the app and go to the About page.</span></span> <span data-ttu-id="6b177-147">显示的数据和之前一样。</span><span class="sxs-lookup"><span data-stu-id="6b177-147">It displays the same data it did before.</span></span>

![“关于”页面](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="6b177-149">调用更新查询</span><span class="sxs-lookup"><span data-stu-id="6b177-149">Call an update query</span></span>

<span data-ttu-id="6b177-150">假设 Contoso University 管理员希望在数据库中执行全局更改，例如更改每门课程的学分。</span><span class="sxs-lookup"><span data-stu-id="6b177-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="6b177-151">如果该大学提供了大量课程，那么将所有课程作为实体来检索并单独更改就非常低效。</span><span class="sxs-lookup"><span data-stu-id="6b177-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="6b177-152">在本节中，你将实现一个网页，使用户能够指定一个系数，通过该系数即可更改所有课程的学分，而你将通过执行 SQL UPDATE 语句来完成更改。</span><span class="sxs-lookup"><span data-stu-id="6b177-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="6b177-153">网页的外观类似于下图：</span><span class="sxs-lookup"><span data-stu-id="6b177-153">The web page will look like the following illustration:</span></span>

![“更新课程学分”页面](advanced/_static/update-credits.png)

<span data-ttu-id="6b177-155">在 CoursesController.cs 中，为 HttpGet 和 HttpPost 添加 UpdateCourseCredits 方法：</span><span class="sxs-lookup"><span data-stu-id="6b177-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="6b177-156">当控制器处理 HttpGet 请求时，`ViewData["RowsAffected"]`中不会返回任何东西，并且在视图中显示一个空文本框和提交按钮，如上图所示。</span><span class="sxs-lookup"><span data-stu-id="6b177-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="6b177-157">当单击 **Update** 按钮时，将调用 HttpPost 方法，且从文本框中输入的值获取乘数。</span><span class="sxs-lookup"><span data-stu-id="6b177-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="6b177-158">代码接着执行 SQL 语句更新课程，并向视图的`ViewData`返回受影响的行数。</span><span class="sxs-lookup"><span data-stu-id="6b177-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="6b177-159">当视图获取`RowsAffected`值，它将显示更新的行数。</span><span class="sxs-lookup"><span data-stu-id="6b177-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="6b177-160">在“解决方案资源管理器”中，右键单击“Views/Courses”文件夹，然后依次单击“添加”和“新建项”。</span><span class="sxs-lookup"><span data-stu-id="6b177-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="6b177-161">在“添加新项”对话框中，在左侧窗格的“已安装”下单击“ASP.NET Core”，单击“Razor 视图”，并将新视图命名为“UpdateCourseCredits.cshtml”   。</span><span class="sxs-lookup"><span data-stu-id="6b177-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="6b177-162">在 *Views/Courses/UpdateCourseCredits.cshtml* 中，将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="6b177-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="6b177-163">通过选择 **Courses** 选项卡运行`UpdateCourseCredits`方法，然后在浏览器地址栏中 URL 的末尾添加"/ UpdateCourseCredits"到 (例如： `http://localhost:5813/Courses/UpdateCourseCredits`)。</span><span class="sxs-lookup"><span data-stu-id="6b177-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="6b177-164">在文本框中输入数字：</span><span class="sxs-lookup"><span data-stu-id="6b177-164">Enter a number in the text box:</span></span>

![“更新课程学分”页面](advanced/_static/update-credits.png)

<span data-ttu-id="6b177-166">单击 **Update**。</span><span class="sxs-lookup"><span data-stu-id="6b177-166">Click **Update**.</span></span> <span data-ttu-id="6b177-167">你会看到受影响的行数：</span><span class="sxs-lookup"><span data-stu-id="6b177-167">You see the number of rows affected:</span></span>

![“更新课程学分”页面中受影响的行](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="6b177-169">单击“返回列表”可以查看课程列表，其中学分已替换为修改后的数字。</span><span class="sxs-lookup"><span data-stu-id="6b177-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="6b177-170">请注意，生产代码将确保更新最终生成有效的数据。</span><span class="sxs-lookup"><span data-stu-id="6b177-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="6b177-171">此处所示的简化代码可将学分的数字进行相乘，以便足以生成大于 5 的数字。</span><span class="sxs-lookup"><span data-stu-id="6b177-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="6b177-172">(`Credits` 属性具有 `[Range(0, 5)]` 特性。)更新查询会起作用，但无效数据可能在系统的其他部分中产生意外结果，这些部分假定学分为 5 或更少。</span><span class="sxs-lookup"><span data-stu-id="6b177-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="6b177-173">有关原生 SQL 查询的详细信息，请参阅[原生 SQL 查询](/ef/core/querying/raw-sql)。</span><span class="sxs-lookup"><span data-stu-id="6b177-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="6b177-174">检查 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="6b177-174">Examine SQL queries</span></span>

<span data-ttu-id="6b177-175">有时能够以查看发送到数据库的实际 SQL 查询对于开发者来说是很有用的。</span><span class="sxs-lookup"><span data-stu-id="6b177-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="6b177-176">EF Core 自动使用 ASP.NET Core 的内置日志记录功能来编写包含 SQL 查询和更新的日志。</span><span class="sxs-lookup"><span data-stu-id="6b177-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="6b177-177">在本部分中，你将看到记录 SQL 日志的一些示例。</span><span class="sxs-lookup"><span data-stu-id="6b177-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="6b177-178">打开 *StudentsController.cs* 并在`Details`方法的`if (student == null)`语句上设置断点。</span><span class="sxs-lookup"><span data-stu-id="6b177-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="6b177-179">在调试模式下运行应用，并转到某位学生的“详细信息”页面。</span><span class="sxs-lookup"><span data-stu-id="6b177-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="6b177-180">转到 **输出** 窗口显示调试输出，就可以看到查询语句：</span><span class="sxs-lookup"><span data-stu-id="6b177-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

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

<span data-ttu-id="6b177-181">你会注意到一些可能会让你觉得惊讶的操作： SQL 从 Person 表最多选择 2 行 (`TOP(2)`) 。</span><span class="sxs-lookup"><span data-stu-id="6b177-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="6b177-182">`SingleOrDefaultAsync`方法服务器上不会解析为 1 行。</span><span class="sxs-lookup"><span data-stu-id="6b177-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="6b177-183">原因是：</span><span class="sxs-lookup"><span data-stu-id="6b177-183">Here's why:</span></span>

* <span data-ttu-id="6b177-184">如果查询返回多个行，该方法会返回 null。</span><span class="sxs-lookup"><span data-stu-id="6b177-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="6b177-185">如果想知道查询是否返回多个行，EF 必须检查是否至少返回 2。</span><span class="sxs-lookup"><span data-stu-id="6b177-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="6b177-186">请注意，你不必使用调试模式，并在断点处停止，然后在 **输出** 窗口获取日志记录。</span><span class="sxs-lookup"><span data-stu-id="6b177-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="6b177-187">这种方法非常便捷，只需在想查看输出时停止日志记录即可。</span><span class="sxs-lookup"><span data-stu-id="6b177-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="6b177-188">如果不进行此操作，程序将继续进行日志记录，要查看感兴趣的部分则必须向后滚动。</span><span class="sxs-lookup"><span data-stu-id="6b177-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="6b177-189">创建抽象层</span><span class="sxs-lookup"><span data-stu-id="6b177-189">Create an abstraction layer</span></span>

<span data-ttu-id="6b177-190">许多开发人员编写代码实现存储库和工作模式单元以作为使用 Entity Framework 代码的包装器。</span><span class="sxs-lookup"><span data-stu-id="6b177-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="6b177-191">这些模式用于在应用程序的数据访问层和业务逻辑层之间创建抽象层。</span><span class="sxs-lookup"><span data-stu-id="6b177-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="6b177-192">实现这些模式可让你的应用程序对数据存储介质的更改不敏感，而且很容易进行自动化单元测试和进行测试驱动开发 (TDD)。</span><span class="sxs-lookup"><span data-stu-id="6b177-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="6b177-193">但是，编写附加代码以实现这些模式对于使用 EF 的应用程序并不总是最好的选择，原因有以下几个：</span><span class="sxs-lookup"><span data-stu-id="6b177-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="6b177-194">EF 上下文类可以为使用 EF 的数据库更新充当工作单位类。</span><span class="sxs-lookup"><span data-stu-id="6b177-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="6b177-195">对于使用 EF 进行的数据库更新，EF 上下文类可充当工作单元类。</span><span class="sxs-lookup"><span data-stu-id="6b177-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="6b177-196">EF 包括用于无需编写存储库代码就实现 TDD 的功能。</span><span class="sxs-lookup"><span data-stu-id="6b177-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="6b177-197">有关如何实现存储库和工作单元模式的详细信息，请参阅[本系列教程的 Entity Framework 5 版本](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)。</span><span class="sxs-lookup"><span data-stu-id="6b177-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="6b177-198">Entity Framework Core 的实现可用于测试的内存数据库驱动程序。</span><span class="sxs-lookup"><span data-stu-id="6b177-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="6b177-199">有关详细信息，请参阅[测试以及 InMemory](/ef/core/miscellaneous/testing/in-memory)。</span><span class="sxs-lookup"><span data-stu-id="6b177-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="6b177-200">自动脏值检测</span><span class="sxs-lookup"><span data-stu-id="6b177-200">Automatic change detection</span></span>

<span data-ttu-id="6b177-201">Entity Framework 通过比较的实体的当前值与原始值来判断更改实体的方式 （因此需要发送更新到数据库）。</span><span class="sxs-lookup"><span data-stu-id="6b177-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="6b177-202">查询或附加该实体时会存储的原始值。</span><span class="sxs-lookup"><span data-stu-id="6b177-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="6b177-203">如下方法会导致自动脏值：</span><span class="sxs-lookup"><span data-stu-id="6b177-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="6b177-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="6b177-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="6b177-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="6b177-205">DbContext.Entry</span></span>

* <span data-ttu-id="6b177-206">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="6b177-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="6b177-207">如果正在跟踪大量实体，并且这些方法之一在循环中多次调用，通过使用`ChangeTracker.AutoDetectChangesEnabled`属性暂时关闭自动脏值检测，能够显著改进性能。</span><span class="sxs-lookup"><span data-stu-id="6b177-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="6b177-208">例如：</span><span class="sxs-lookup"><span data-stu-id="6b177-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="6b177-209">EF Core 源代码与开发计划</span><span class="sxs-lookup"><span data-stu-id="6b177-209">EF Core source code and development plans</span></span>

<span data-ttu-id="6b177-210">Entity Framework Core 源位于 [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)。</span><span class="sxs-lookup"><span data-stu-id="6b177-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="6b177-211">仓库中除了有源代码，还可包括每夜生成、 问题跟踪、 功能规范、 设计会议备忘录和[将来的开发路线图](https://github.com/dotnet/efcore/wiki/Roadmap)。</span><span class="sxs-lookup"><span data-stu-id="6b177-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="6b177-212">你可以归档或查找 bug 并进行更改。</span><span class="sxs-lookup"><span data-stu-id="6b177-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="6b177-213">尽管源代码处于开源状态， Entity Framework Core 是由 Microsoft 完全支持的产品。</span><span class="sxs-lookup"><span data-stu-id="6b177-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="6b177-214">Microsoft Entity Framework 团队将控制接受哪些贡献和测试所有的代码更改，以确保每个版本的质量。</span><span class="sxs-lookup"><span data-stu-id="6b177-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="6b177-215">现有数据库逆向工程</span><span class="sxs-lookup"><span data-stu-id="6b177-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="6b177-216">若想要通过对现有数据库中的实体类反向工程得出数据模型，可以使用[scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="6b177-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="6b177-217">可以参阅[入门教程](/ef/core/get-started/aspnetcore/existing-db)。</span><span class="sxs-lookup"><span data-stu-id="6b177-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="6b177-218">使用动态 LINQ 简化代码</span><span class="sxs-lookup"><span data-stu-id="6b177-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="6b177-219">[本系列的第三个教程](sort-filter-page.md)演示如何通过在`switch`语句中对列名称进行硬编码来编写 LINQ 。</span><span class="sxs-lookup"><span data-stu-id="6b177-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="6b177-220">如果只有两列可供选择，这种方法可行，但是如果拥有许多列，代码可能会变得冗长。</span><span class="sxs-lookup"><span data-stu-id="6b177-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="6b177-221">要解决该问题，可使用 `EF.Property` 方法将属性名称指定为一个字符串。</span><span class="sxs-lookup"><span data-stu-id="6b177-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="6b177-222">要尝试此方法，请将 `StudentsController` 中的 `Index` 方法替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="6b177-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="6b177-223">鸣谢</span><span class="sxs-lookup"><span data-stu-id="6b177-223">Acknowledgments</span></span>

<span data-ttu-id="6b177-224">Tom Dykstra 和 Rick Anderson (twitter @RickAndMSFT) 共同编写了本教程。</span><span class="sxs-lookup"><span data-stu-id="6b177-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="6b177-225">Rowan Miller、 Diego Vega 和 Entity Framework 团队的其他成员协助代码评审和帮助解决编写教程代码时出现的调试问题。</span><span class="sxs-lookup"><span data-stu-id="6b177-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="6b177-226">John Parente 和 Paul Goldman 致力于更新 ASP.NET Core 2.2 的教程。</span><span class="sxs-lookup"><span data-stu-id="6b177-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="6b177-227">常见错误疑难解答</span><span class="sxs-lookup"><span data-stu-id="6b177-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="6b177-228">ContosoUniversity.dll 被另一个进程使用</span><span class="sxs-lookup"><span data-stu-id="6b177-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="6b177-229">错误消息：</span><span class="sxs-lookup"><span data-stu-id="6b177-229">Error message:</span></span>

> <span data-ttu-id="6b177-230">无法打开“...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- ”进程无法访问文件“...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll”，因为它正在由其他进程使用。</span><span class="sxs-lookup"><span data-stu-id="6b177-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="6b177-231">解决方案：</span><span class="sxs-lookup"><span data-stu-id="6b177-231">Solution:</span></span>

<span data-ttu-id="6b177-232">停止 IIS Express 中的站点。</span><span class="sxs-lookup"><span data-stu-id="6b177-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="6b177-233">请转到 Windows 系统任务栏中，找到 IIS Express 并右键单击其图标、 选择 Contoso 大学站点，然后单击 **停止站点**。</span><span class="sxs-lookup"><span data-stu-id="6b177-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="6b177-234">迁移基架的 Up 和 Down 方法中没有代码</span><span class="sxs-lookup"><span data-stu-id="6b177-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="6b177-235">可能的原因：</span><span class="sxs-lookup"><span data-stu-id="6b177-235">Possible cause:</span></span>

<span data-ttu-id="6b177-236">EF CLI 命令不会自动关闭并保存代码文件。</span><span class="sxs-lookup"><span data-stu-id="6b177-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="6b177-237">如果在运行`migrations add`命令时，你未保存更改，EF 将找不到所做的更改。</span><span class="sxs-lookup"><span data-stu-id="6b177-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="6b177-238">解决方案：</span><span class="sxs-lookup"><span data-stu-id="6b177-238">Solution:</span></span>

<span data-ttu-id="6b177-239">运行`migrations remove`命令，保存你更改的代码并重新运行`migrations add`命令。</span><span class="sxs-lookup"><span data-stu-id="6b177-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="6b177-240">运行数据库更新时出错</span><span class="sxs-lookup"><span data-stu-id="6b177-240">Errors while running database update</span></span>

<span data-ttu-id="6b177-241">在有数据的数据库中进行架构更改时，很有可能发生其他错误。</span><span class="sxs-lookup"><span data-stu-id="6b177-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="6b177-242">如果遇到无法解决的迁移错误，你可以更改连接字符串中的数据库名称，或删除数据库。</span><span class="sxs-lookup"><span data-stu-id="6b177-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="6b177-243">若要迁移，创建新的数据库，在数据库尚没有数据时使用更新数据库命令更有望完成且不发生错误。</span><span class="sxs-lookup"><span data-stu-id="6b177-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="6b177-244">最简单方法是在 appsettings.json 中重命名数据库。</span><span class="sxs-lookup"><span data-stu-id="6b177-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="6b177-245">下次运行`database update`时，会创建一个新数据库。</span><span class="sxs-lookup"><span data-stu-id="6b177-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="6b177-246">若要在 SSOX 中删除数据库，右键单击数据库，单击 **删除**，然后在 **删除数据库** 对话框框中，选择 **关闭现有连接**，单击 **确定**。</span><span class="sxs-lookup"><span data-stu-id="6b177-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="6b177-247">若要使用 CLI 删除数据库，可以运行`database drop`CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="6b177-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="6b177-248">定位 SQL Server 实例出错</span><span class="sxs-lookup"><span data-stu-id="6b177-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="6b177-249">错误消息：</span><span class="sxs-lookup"><span data-stu-id="6b177-249">Error Message:</span></span>

> <span data-ttu-id="6b177-250">建立到 SQL Server 的连接时出现与网络相关或特定于实例的错误。</span><span class="sxs-lookup"><span data-stu-id="6b177-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="6b177-251">找不到或无法访问服务器。</span><span class="sxs-lookup"><span data-stu-id="6b177-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="6b177-252">请验证实例名称是否正确，SQL Server 是否已配置为允许远程连接。</span><span class="sxs-lookup"><span data-stu-id="6b177-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="6b177-253">（提供程序：SQL 网络接口，错误：26 - 定位指定服务器/实例出错）</span><span class="sxs-lookup"><span data-stu-id="6b177-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="6b177-254">解决方案：</span><span class="sxs-lookup"><span data-stu-id="6b177-254">Solution:</span></span>

<span data-ttu-id="6b177-255">请检查连接字符串。</span><span class="sxs-lookup"><span data-stu-id="6b177-255">Check the connection string.</span></span> <span data-ttu-id="6b177-256">如果你已手动删除数据库文件，更改数据库的构造字符串中数据库的名称，然后从头开始使用新的数据库。</span><span class="sxs-lookup"><span data-stu-id="6b177-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="6b177-257">获取代码</span><span class="sxs-lookup"><span data-stu-id="6b177-257">Get the code</span></span>

[<span data-ttu-id="6b177-258">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="6b177-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="6b177-259">其他资源</span><span class="sxs-lookup"><span data-stu-id="6b177-259">Additional resources</span></span>

<span data-ttu-id="6b177-260">有关 EF Core 的详细信息，请参阅[ Entity Framework 的Core文档](/ef/core)。</span><span class="sxs-lookup"><span data-stu-id="6b177-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="6b177-261">还提供：[Entity Framework Core 实战](https://www.manning.com/books/entity-framework-core-in-action)一书。</span><span class="sxs-lookup"><span data-stu-id="6b177-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="6b177-262">有关如何部署 Web 应用的信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="6b177-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="6b177-263">有关与 ASP.NET Core MVC 相关的其他主题（如身份验证与授权）的信息，请参阅 <xref:index>。</span><span class="sxs-lookup"><span data-stu-id="6b177-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6b177-264">后续步骤</span><span class="sxs-lookup"><span data-stu-id="6b177-264">Next steps</span></span>

<span data-ttu-id="6b177-265">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="6b177-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6b177-266">已执行原始 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="6b177-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="6b177-267">已调用查询以返回实体</span><span class="sxs-lookup"><span data-stu-id="6b177-267">Called a query to return entities</span></span>
> * <span data-ttu-id="6b177-268">已调用查询以返回其他类型</span><span class="sxs-lookup"><span data-stu-id="6b177-268">Called a query to return other types</span></span>
> * <span data-ttu-id="6b177-269">已调用更新查询</span><span class="sxs-lookup"><span data-stu-id="6b177-269">Called an update query</span></span>
> * <span data-ttu-id="6b177-270">已检查 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="6b177-270">Examined SQL queries</span></span>
> * <span data-ttu-id="6b177-271">已创建抽象层</span><span class="sxs-lookup"><span data-stu-id="6b177-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="6b177-272">已了解自动更改检测</span><span class="sxs-lookup"><span data-stu-id="6b177-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="6b177-273">已了解 EF Core 源代码与开发计划</span><span class="sxs-lookup"><span data-stu-id="6b177-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="6b177-274">已了解如何使用动态 LINQ 简化代码</span><span class="sxs-lookup"><span data-stu-id="6b177-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="6b177-275">这将完成在 ASP.NET Core MVC 应用程序中使用 Entity Framework Core 这一系列教程。</span><span class="sxs-lookup"><span data-stu-id="6b177-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="6b177-276">本系列使用的是新建数据库；另一种方式是从现有数据库进行模型的反向工程。</span><span class="sxs-lookup"><span data-stu-id="6b177-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6b177-277">教程：EF Core 与 MVC，现有数据库</span><span class="sxs-lookup"><span data-stu-id="6b177-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
