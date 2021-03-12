---
title: 教程：添加排序、筛选和分页 - ASP.NET MVC 和 EF Core
description: 在本教程中，将向学生索引页添加排序、筛选和分页功能。 同时，还将创建一个执行简单分组的页面。
author: rick-anderson
ms.author: riande
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
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: ca4bce2274233f49e1c382dbd8d5c45615006b9c
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587640"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="d50b5-104">教程：添加排序、筛选和分页 - ASP.NET MVC 和 EF Core</span><span class="sxs-lookup"><span data-stu-id="d50b5-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="d50b5-105">在上一个教程中，已为 Student 实体实现了一组网页用于执行基本的 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="d50b5-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="d50b5-106">在本教程中，将向学生索引页添加排序、筛选和分页功能。</span><span class="sxs-lookup"><span data-stu-id="d50b5-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="d50b5-107">同时，还将创建一个执行简单分组的页面。</span><span class="sxs-lookup"><span data-stu-id="d50b5-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="d50b5-108">下图展示了完成操作后的页面外观。</span><span class="sxs-lookup"><span data-stu-id="d50b5-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="d50b5-109">列标题是用户可以单击以按该列排序的链接。</span><span class="sxs-lookup"><span data-stu-id="d50b5-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="d50b5-110">重复单击列标题可在升降排序顺序之间切换。</span><span class="sxs-lookup"><span data-stu-id="d50b5-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![“学生索引”页](sort-filter-page/_static/paging.png)

<span data-ttu-id="d50b5-112">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="d50b5-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d50b5-113">添加列排序链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-113">Add column sort links</span></span>
> * <span data-ttu-id="d50b5-114">添加“搜索”框</span><span class="sxs-lookup"><span data-stu-id="d50b5-114">Add a Search box</span></span>
> * <span data-ttu-id="d50b5-115">向学生索引添加分页</span><span class="sxs-lookup"><span data-stu-id="d50b5-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="d50b5-116">向 Index 方法添加分页</span><span class="sxs-lookup"><span data-stu-id="d50b5-116">Add paging to Index method</span></span>
> * <span data-ttu-id="d50b5-117">添加分页链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-117">Add paging links</span></span>
> * <span data-ttu-id="d50b5-118">创建“关于”页</span><span class="sxs-lookup"><span data-stu-id="d50b5-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d50b5-119">先决条件</span><span class="sxs-lookup"><span data-stu-id="d50b5-119">Prerequisites</span></span>

* [<span data-ttu-id="d50b5-120">实现 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="d50b5-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="d50b5-121">添加列排序链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-121">Add column sort links</span></span>

<span data-ttu-id="d50b5-122">要向学生索引页添加排序功能，需更改学生控制器的 `Index` 方法并将代码添加到学生索引视图。</span><span class="sxs-lookup"><span data-stu-id="d50b5-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="d50b5-123">向 Index 方法添加排序功能</span><span class="sxs-lookup"><span data-stu-id="d50b5-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="d50b5-124">在 StudentsController.cs 中，将 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="d50b5-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="d50b5-125">此代码接收来自 URL 中的查询字符串的 `sortOrder` 参数。</span><span class="sxs-lookup"><span data-stu-id="d50b5-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="d50b5-126">查询字符串值由 ASP.NET Core MVC 提供，作为操作方法的参数。</span><span class="sxs-lookup"><span data-stu-id="d50b5-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="d50b5-127">该参数将是一个字符串，可为“Name”或“Date”，可选择后跟下划线和字符串“desc”来指定降序。</span><span class="sxs-lookup"><span data-stu-id="d50b5-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="d50b5-128">默认排序顺序为升序。</span><span class="sxs-lookup"><span data-stu-id="d50b5-128">The default sort order is ascending.</span></span>

<span data-ttu-id="d50b5-129">首次请求索引页时，没有任何查询字符串。</span><span class="sxs-lookup"><span data-stu-id="d50b5-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="d50b5-130">学生按照姓氏升序显示，这是 `switch` 语句中的 fall-through 事例所建立的默认值。</span><span class="sxs-lookup"><span data-stu-id="d50b5-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="d50b5-131">当用户单击列标题超链接时，查询字符串中会提供相应的 `sortOrder` 值。</span><span class="sxs-lookup"><span data-stu-id="d50b5-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="d50b5-132">视图使用两个 `ViewData` 元素（NameSortParm 和 DateSortParm）来为列标题超链接配置相应的查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="d50b5-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="d50b5-133">这些是三元语句。</span><span class="sxs-lookup"><span data-stu-id="d50b5-133">These are ternary statements.</span></span> <span data-ttu-id="d50b5-134">第一个指定如果 `sortOrder` 参数为 NULL 或空，则应将 NameSortParm 设置为“name_desc”；否则，应将其设置为空字符串。</span><span class="sxs-lookup"><span data-stu-id="d50b5-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="d50b5-135">通过这两个语句，视图可如下设置列标题超链接：</span><span class="sxs-lookup"><span data-stu-id="d50b5-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="d50b5-136">当前排序顺序</span><span class="sxs-lookup"><span data-stu-id="d50b5-136">Current sort order</span></span>  | <span data-ttu-id="d50b5-137">姓氏超链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-137">Last Name Hyperlink</span></span> | <span data-ttu-id="d50b5-138">日期超链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="d50b5-139">姓氏升序</span><span class="sxs-lookup"><span data-stu-id="d50b5-139">Last Name ascending</span></span>  | <span data-ttu-id="d50b5-140">descending</span><span class="sxs-lookup"><span data-stu-id="d50b5-140">descending</span></span>          | <span data-ttu-id="d50b5-141">ascending</span><span class="sxs-lookup"><span data-stu-id="d50b5-141">ascending</span></span>      |
| <span data-ttu-id="d50b5-142">姓氏降序</span><span class="sxs-lookup"><span data-stu-id="d50b5-142">Last Name descending</span></span> | <span data-ttu-id="d50b5-143">ascending</span><span class="sxs-lookup"><span data-stu-id="d50b5-143">ascending</span></span>           | <span data-ttu-id="d50b5-144">ascending</span><span class="sxs-lookup"><span data-stu-id="d50b5-144">ascending</span></span>      |
| <span data-ttu-id="d50b5-145">日期升序</span><span class="sxs-lookup"><span data-stu-id="d50b5-145">Date ascending</span></span>       | <span data-ttu-id="d50b5-146">ascending</span><span class="sxs-lookup"><span data-stu-id="d50b5-146">ascending</span></span>           | <span data-ttu-id="d50b5-147">descending</span><span class="sxs-lookup"><span data-stu-id="d50b5-147">descending</span></span>     |
| <span data-ttu-id="d50b5-148">日期降序</span><span class="sxs-lookup"><span data-stu-id="d50b5-148">Date descending</span></span>      | <span data-ttu-id="d50b5-149">ascending</span><span class="sxs-lookup"><span data-stu-id="d50b5-149">ascending</span></span>           | <span data-ttu-id="d50b5-150">ascending</span><span class="sxs-lookup"><span data-stu-id="d50b5-150">ascending</span></span>      |

<span data-ttu-id="d50b5-151">该方法使用 LINQ to Entities 指定要作为排序依据的列。</span><span class="sxs-lookup"><span data-stu-id="d50b5-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="d50b5-152">该代码在 switch 语句之前创建一个 `IQueryable` 变量，在 switch 语句中对其进行修改，并在 `switch` 语句后调用 `ToListAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="d50b5-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="d50b5-153">当创建和修改 `IQueryable` 变量时，不会向数据库发送任何查询。</span><span class="sxs-lookup"><span data-stu-id="d50b5-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="d50b5-154">只有通过调用 `ToListAsync` 之类的方法将 `IQueryable` 对象转换为集合，查询才会执行。</span><span class="sxs-lookup"><span data-stu-id="d50b5-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="d50b5-155">因此，此代码导致直到 `return View` 语句才会执行单个查询。</span><span class="sxs-lookup"><span data-stu-id="d50b5-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="d50b5-156">此代码可能获得包含大量列的详细信息。</span><span class="sxs-lookup"><span data-stu-id="d50b5-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="d50b5-157">[本系列的最后一个教程](advanced.md#dynamic-linq)演示了如何编写在字符串变量中传递 `OrderBy` 列名的代码。</span><span class="sxs-lookup"><span data-stu-id="d50b5-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="d50b5-158">向“学生索引”视图添加列标题超链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="d50b5-159">将 Views / Students / Index.cshtml 中的代码替换为以下代码，以添加列标题超链接。</span><span class="sxs-lookup"><span data-stu-id="d50b5-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="d50b5-160">已更改的行为突出显示状态。</span><span class="sxs-lookup"><span data-stu-id="d50b5-160">The changed lines are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="d50b5-161">此代码使用 `ViewData` 属性中的信息来设置具有相应查询字符串值的超链接。</span><span class="sxs-lookup"><span data-stu-id="d50b5-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="d50b5-162">运行应用，选择“学生”选项卡，然后单击“姓氏”和“注册日期”列标题以验证排序是否正常工作 。</span><span class="sxs-lookup"><span data-stu-id="d50b5-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![按姓名顺序的学生索引页](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="d50b5-164">添加“搜索”框</span><span class="sxs-lookup"><span data-stu-id="d50b5-164">Add a Search box</span></span>

<span data-ttu-id="d50b5-165">要向学生索引页添加筛选功能，需将文本框和提交按钮添加到视图，并在 `Index` 方法中做出相应的更改。</span><span class="sxs-lookup"><span data-stu-id="d50b5-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="d50b5-166">在文本框中输入一个字符串以在名字和姓氏字段中进行搜索。</span><span class="sxs-lookup"><span data-stu-id="d50b5-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="d50b5-167">向 Index 方法添加筛选功能</span><span class="sxs-lookup"><span data-stu-id="d50b5-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="d50b5-168">在 StudentsController.cs 中，将 `Index` 方法替换为以下代码（所做的更改为突出显示状态）。</span><span class="sxs-lookup"><span data-stu-id="d50b5-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="d50b5-169">已向 `Index` 方法添加 `searchString` 参数。</span><span class="sxs-lookup"><span data-stu-id="d50b5-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="d50b5-170">从要添加到索引视图的文本框中接收搜索字符串值。</span><span class="sxs-lookup"><span data-stu-id="d50b5-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="d50b5-171">并且，还向 LINQ 语句添加了 where 子句，该子句仅选择名字或姓氏中包含搜索字符串的学生。</span><span class="sxs-lookup"><span data-stu-id="d50b5-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="d50b5-172">只有在有搜索值的情况下，才会执行添加了 where 子句的语句。</span><span class="sxs-lookup"><span data-stu-id="d50b5-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="d50b5-173">此处正在调用 `IQueryable` 对象上的 `Where` 方法，并且将在服务器上处理该筛选器。</span><span class="sxs-lookup"><span data-stu-id="d50b5-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="d50b5-174">在某些情况下，可能会将 `Where` 方法作为内存中集合上的扩展方法进行调用。</span><span class="sxs-lookup"><span data-stu-id="d50b5-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="d50b5-175">（例如，假设将引用更改为 `_context.Students`，这样它引用的不再是 EF `DbSet`，而是一个返回 `IEnumerable` 集合的存储库方法。）结果通常是相同的，但在某些情况下可能不同。</span><span class="sxs-lookup"><span data-stu-id="d50b5-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="d50b5-176">例如，`Contains` 方法的 .NET Framework 实现在默认情况下执行区分大小写比较，但在 SQL Server 中，这由 SQL Server 实例的排序规则设置决定。</span><span class="sxs-lookup"><span data-stu-id="d50b5-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="d50b5-177">该设置默认为不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="d50b5-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="d50b5-178">可调用 `ToUpper` 方法进行不区分大小写的显式测试：*Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())* 。</span><span class="sxs-lookup"><span data-stu-id="d50b5-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="d50b5-179">如果稍后更改代码以使用返回 `IEnumerable` 集合而不是 `IQueryable` 对象的存储库，则可确保结果保持不变。</span><span class="sxs-lookup"><span data-stu-id="d50b5-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="d50b5-180">（在 `IEnumerable` 集合上调用 `Contains` 方法时，将获得 .NET Framework 实现；当在 `IQueryable` 对象上调用它时，将获得数据库提供程序实现。）但是，此解决方案会降低性能。</span><span class="sxs-lookup"><span data-stu-id="d50b5-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="d50b5-181">`ToUpper` 代码将在 TSQL SELECT 语句的 WHERE 子句中放置一个函数。</span><span class="sxs-lookup"><span data-stu-id="d50b5-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="d50b5-182">这将阻止优化器使用索引。</span><span class="sxs-lookup"><span data-stu-id="d50b5-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="d50b5-183">鉴于 SQL 主要安装为不区分大小写，在迁移到区分大小写的数据存储之前，最好避免使用 `ToUpper` 代码。</span><span class="sxs-lookup"><span data-stu-id="d50b5-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="d50b5-184">向“学生索引”视图添加搜索框</span><span class="sxs-lookup"><span data-stu-id="d50b5-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="d50b5-185">在 Views/Student/Index.cshtml 中，请在打开表格标签之前立即添加突出显示的代码，以创建标题栏、文本框和搜索按钮。</span><span class="sxs-lookup"><span data-stu-id="d50b5-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="d50b5-186">此代码使用 `<form>` [标记帮助程序](xref:mvc/views/tag-helpers/intro)来添加搜索文本框和按钮。</span><span class="sxs-lookup"><span data-stu-id="d50b5-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="d50b5-187">默认情况下，`<form>` 标记帮助器使用 POST 提交表单数据，这意味着参数在 HTTP 消息正文中传递，而不是作为查询字符串在 URL 中传递。</span><span class="sxs-lookup"><span data-stu-id="d50b5-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="d50b5-188">当指定 HTTP GET 时，表单数据作为查询字符串在 URL 中传递，从而使用户能够将 URL 加入书签。</span><span class="sxs-lookup"><span data-stu-id="d50b5-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="d50b5-189">W3C 指南建议在操作不会导致更新时使用 GET。</span><span class="sxs-lookup"><span data-stu-id="d50b5-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="d50b5-190">运行应用，选择“学生”选项卡，输入搜索字符串，然后单击“搜索”以验证筛选是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="d50b5-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![带有筛选功能的学生索引页](sort-filter-page/_static/filtering.png)

<span data-ttu-id="d50b5-192">请注意，该 URL 包含搜索字符串。</span><span class="sxs-lookup"><span data-stu-id="d50b5-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="d50b5-193">如果将此页加入书签，当使用书签时，将获得已筛选的列表。</span><span class="sxs-lookup"><span data-stu-id="d50b5-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="d50b5-194">向 `form` 标记添加 `method="get"` 是导致生成查询字符串的原因。</span><span class="sxs-lookup"><span data-stu-id="d50b5-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="d50b5-195">在此阶段，如果单击列标题排序链接，则会丢失已在“搜索”框中输入的筛选器值。</span><span class="sxs-lookup"><span data-stu-id="d50b5-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="d50b5-196">此问题将在下一部分得以解决。</span><span class="sxs-lookup"><span data-stu-id="d50b5-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="d50b5-197">向学生索引添加分页</span><span class="sxs-lookup"><span data-stu-id="d50b5-197">Add paging to Students Index</span></span>

<span data-ttu-id="d50b5-198">要向学生索引页添加分页功能，需创建一个使用 `Skip` 和 `Take` 语句的 `PaginatedList` 类来筛选服务器上的数据，而不总是对表中的所有行进行检索。</span><span class="sxs-lookup"><span data-stu-id="d50b5-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="d50b5-199">然后在 `Index` 方法中进行其他更改，并将分页按钮添加到 `Index` 视图。</span><span class="sxs-lookup"><span data-stu-id="d50b5-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="d50b5-200">下图显示了分页按钮。</span><span class="sxs-lookup"><span data-stu-id="d50b5-200">The following illustration shows the paging buttons.</span></span>

![带有分页链接的“学生索引”页](sort-filter-page/_static/paging.png)

<span data-ttu-id="d50b5-202">在项目文件夹中，创建 `PaginatedList.cs`，然后用以下代码替换模板代码。</span><span class="sxs-lookup"><span data-stu-id="d50b5-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="d50b5-203">此代码中的 `CreateAsync` 方法将提取页面大小和页码，并将相应的 `Skip` 和 `Take` 语句应用于 `IQueryable`。</span><span class="sxs-lookup"><span data-stu-id="d50b5-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="d50b5-204">当在 `IQueryable` 上调用 `ToListAsync` 时，它将返回仅包含请求页的列表。</span><span class="sxs-lookup"><span data-stu-id="d50b5-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="d50b5-205">属性 `HasPreviousPage` 和 `HasNextPage` 可用于启用或禁用“上一页”和“下一页”的分页按钮 。</span><span class="sxs-lookup"><span data-stu-id="d50b5-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="d50b5-206">由于构造函数不能运行异步代码，因此使用 `CreateAsync` 方法来创建 `PaginatedList<T>` 对象，而非构造函数。</span><span class="sxs-lookup"><span data-stu-id="d50b5-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="d50b5-207">向 Index 方法添加分页</span><span class="sxs-lookup"><span data-stu-id="d50b5-207">Add paging to Index method</span></span>

<span data-ttu-id="d50b5-208">在 StudentsController.cs 中，将 `Index` 方法替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="d50b5-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="d50b5-209">该代码向方法签名中添加一个页码参数、一个当前排序顺序参数和一个当前筛选器参数。</span><span class="sxs-lookup"><span data-stu-id="d50b5-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="d50b5-210">第一次显示页面时，或者如果用户没有单击分页或排序链接，所有参数都将为 NULL。</span><span class="sxs-lookup"><span data-stu-id="d50b5-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="d50b5-211">如果单击了分页链接，页面变量将包含要显示的页码。</span><span class="sxs-lookup"><span data-stu-id="d50b5-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="d50b5-212">名为 CurrentSort 的 `ViewData` 元素为视图提供当前排序顺序，因为此值必须包含在分页链接中，以便在分页时保持排序顺序相同。</span><span class="sxs-lookup"><span data-stu-id="d50b5-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="d50b5-213">名为 CurrentFilter 的 `ViewData` 元素为视图提供当前筛选器字符串。</span><span class="sxs-lookup"><span data-stu-id="d50b5-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="d50b5-214">此值必须包含在分页链接中，以便在分页过程中保持筛选器设置，并且在页面重新显示时必须将其还原到文本框中。</span><span class="sxs-lookup"><span data-stu-id="d50b5-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="d50b5-215">如果在分页过程中搜索字符串发生变化，则页面必须重置为 1，因为新的筛选器会导致显示不同的数据。</span><span class="sxs-lookup"><span data-stu-id="d50b5-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="d50b5-216">在文本框中输入值并按下“提交”按钮时，搜索字符串将被更改。</span><span class="sxs-lookup"><span data-stu-id="d50b5-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="d50b5-217">在这种情况下，`searchString` 参数不为 NULL。</span><span class="sxs-lookup"><span data-stu-id="d50b5-217">In that case, the `searchString` parameter isn't null.</span></span>

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

<span data-ttu-id="d50b5-218">在 `Index` 方法最后，`PaginatedList.CreateAsync` 方法会将学生查询转换为支持分页的集合类型中的学生的单个页面。</span><span class="sxs-lookup"><span data-stu-id="d50b5-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="d50b5-219">然后将学生的单个页面传递给视图。</span><span class="sxs-lookup"><span data-stu-id="d50b5-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="d50b5-220">`PaginatedList.CreateAsync` 方法需要一个页码。</span><span class="sxs-lookup"><span data-stu-id="d50b5-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="d50b5-221">两个问号表示 NULL 合并运算符。</span><span class="sxs-lookup"><span data-stu-id="d50b5-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="d50b5-222">NULL 合并运算符为可为 NULL 的类型定义默认值；表达式 `(pageNumber ?? 1)` 表示如果 `pageNumber` 有值，则返回该值，如果 `pageNumber` 为 NULL，则返回 1。</span><span class="sxs-lookup"><span data-stu-id="d50b5-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="d50b5-223">添加分页链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-223">Add paging links</span></span>

<span data-ttu-id="d50b5-224">在 Views/Students/Index.cshtml 中，将现有代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="d50b5-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="d50b5-225">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="d50b5-225">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="d50b5-226">页面顶部的 `@model` 语句指定视图现在获取的是 `PaginatedList<T>` 对象，而不是 `List<T>` 对象。</span><span class="sxs-lookup"><span data-stu-id="d50b5-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="d50b5-227">列标题链接使用查询字符串向控制器传递当前搜索字符串，以便用户可以在筛选结果中进行排序：</span><span class="sxs-lookup"><span data-stu-id="d50b5-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="d50b5-228">分页按钮由标记帮助器显示：</span><span class="sxs-lookup"><span data-stu-id="d50b5-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="d50b5-229">运行应用并转到“学生”页。</span><span class="sxs-lookup"><span data-stu-id="d50b5-229">Run the app and go to the Students page.</span></span>

![带有分页链接的“学生索引”页](sort-filter-page/_static/paging.png)

<span data-ttu-id="d50b5-231">单击不同排序顺序的分页链接，以确保分页正常工作。</span><span class="sxs-lookup"><span data-stu-id="d50b5-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="d50b5-232">然后输入一个搜索字符串并再次尝试分页，以验证分页也可以正确地进行排序和筛选。</span><span class="sxs-lookup"><span data-stu-id="d50b5-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="d50b5-233">创建“关于”页</span><span class="sxs-lookup"><span data-stu-id="d50b5-233">Create an About page</span></span>

<span data-ttu-id="d50b5-234">对于 Contoso 大学网站的“关于”页，将显示每个注册日期注册了多少名学生。</span><span class="sxs-lookup"><span data-stu-id="d50b5-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="d50b5-235">这需要对组进行分组和简单计算。</span><span class="sxs-lookup"><span data-stu-id="d50b5-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="d50b5-236">若要完成此操作，需要执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d50b5-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="d50b5-237">为需要传递给视图的数据创建一个视图模型类。</span><span class="sxs-lookup"><span data-stu-id="d50b5-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="d50b5-238">创建主控制器中的“关于”方法。</span><span class="sxs-lookup"><span data-stu-id="d50b5-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="d50b5-239">创建“关于”视图。</span><span class="sxs-lookup"><span data-stu-id="d50b5-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="d50b5-240">创建视图模型</span><span class="sxs-lookup"><span data-stu-id="d50b5-240">Create the view model</span></span>

<span data-ttu-id="d50b5-241">在 Models 文件夹中创建一个 SchoolViewModels 文件夹 。</span><span class="sxs-lookup"><span data-stu-id="d50b5-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="d50b5-242">在新文件夹中，添加一个类文件 EnrollmentDateGroup.cs，并用以下代码替换模板代码：</span><span class="sxs-lookup"><span data-stu-id="d50b5-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="d50b5-243">修改主控制器</span><span class="sxs-lookup"><span data-stu-id="d50b5-243">Modify the Home Controller</span></span>

<span data-ttu-id="d50b5-244">在 HomeController.cs 中，使用文件顶部的语句添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="d50b5-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="d50b5-245">在类的左大括号之后立即为数据库上下文添加一个类变量，并从 ASP.NET Core DI 获取上下文的实例：</span><span class="sxs-lookup"><span data-stu-id="d50b5-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="d50b5-246">使用以下代码添加 `About` 方法：</span><span class="sxs-lookup"><span data-stu-id="d50b5-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="d50b5-247">LINQ 语句按注册日期对学生实体进行分组，计算每组中实体的数量，并将结果存储在 `EnrollmentDateGroup` 视图模型对象的集合中。</span><span class="sxs-lookup"><span data-stu-id="d50b5-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="d50b5-248">创建“关于”视图</span><span class="sxs-lookup"><span data-stu-id="d50b5-248">Create the About View</span></span>

<span data-ttu-id="d50b5-249">使用以下代码添加 Views/Home/About.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="d50b5-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="d50b5-250">运行应用并转到“关于”页面。</span><span class="sxs-lookup"><span data-stu-id="d50b5-250">Run the app and go to the About page.</span></span> <span data-ttu-id="d50b5-251">表格中会显示每个注册日期的学生计数。</span><span class="sxs-lookup"><span data-stu-id="d50b5-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="d50b5-252">获取代码</span><span class="sxs-lookup"><span data-stu-id="d50b5-252">Get the code</span></span>

[<span data-ttu-id="d50b5-253">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="d50b5-253">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="d50b5-254">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d50b5-254">Next steps</span></span>

<span data-ttu-id="d50b5-255">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="d50b5-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d50b5-256">已添加列排序链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-256">Added column sort links</span></span>
> * <span data-ttu-id="d50b5-257">已添加“搜索”框</span><span class="sxs-lookup"><span data-stu-id="d50b5-257">Added a Search box</span></span>
> * <span data-ttu-id="d50b5-258">已向学生索引添加分页</span><span class="sxs-lookup"><span data-stu-id="d50b5-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="d50b5-259">已向 Index 方法添加分页</span><span class="sxs-lookup"><span data-stu-id="d50b5-259">Added paging to Index method</span></span>
> * <span data-ttu-id="d50b5-260">已添加分页链接</span><span class="sxs-lookup"><span data-stu-id="d50b5-260">Added paging links</span></span>
> * <span data-ttu-id="d50b5-261">已创建“关于”页</span><span class="sxs-lookup"><span data-stu-id="d50b5-261">Created an About page</span></span>

<span data-ttu-id="d50b5-262">请继续阅读下一篇教程，了解如何使用迁移来处理数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="d50b5-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="d50b5-263">下一篇：处理数据模型更改</span><span class="sxs-lookup"><span data-stu-id="d50b5-263">Next: Handle data model changes</span></span>](migrations.md)
