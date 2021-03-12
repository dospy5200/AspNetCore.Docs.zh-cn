---
title: 教程：读取相关数据 - ASP.NET MVC 和 EF Core
description: 本教程将读取并显示相关数据 - 即 Entity Framework 加载到导航属性中的数据。
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 9ea3bd7aaa075ae4601af51c7cc90f28a884a096
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587887"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="930d2-103">教程：读取相关数据 - ASP.NET MVC 和 EF Core</span><span class="sxs-lookup"><span data-stu-id="930d2-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="930d2-104">前面教程创建了学校数据模型。</span><span class="sxs-lookup"><span data-stu-id="930d2-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="930d2-105">本教程将读取并显示相关数据 - 即 Entity Framework 加载到导航属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="930d2-106">下图是将会用到的页面。</span><span class="sxs-lookup"><span data-stu-id="930d2-106">The following illustrations show the pages that you'll work with.</span></span>

![“课程索引”页](read-related-data/_static/courses-index.png)

![“讲师索引”页](read-related-data/_static/instructors-index.png)

<span data-ttu-id="930d2-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="930d2-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="930d2-110">了解如何加载相关数据</span><span class="sxs-lookup"><span data-stu-id="930d2-110">Learn how to load related data</span></span>
> * <span data-ttu-id="930d2-111">创建“课程”页</span><span class="sxs-lookup"><span data-stu-id="930d2-111">Create a Courses page</span></span>
> * <span data-ttu-id="930d2-112">创建“讲师”页</span><span class="sxs-lookup"><span data-stu-id="930d2-112">Create an Instructors page</span></span>
> * <span data-ttu-id="930d2-113">了解显式加载</span><span class="sxs-lookup"><span data-stu-id="930d2-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="930d2-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="930d2-114">Prerequisites</span></span>

* [<span data-ttu-id="930d2-115">创建复杂数据模型</span><span class="sxs-lookup"><span data-stu-id="930d2-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="930d2-116">了解如何加载相关数据</span><span class="sxs-lookup"><span data-stu-id="930d2-116">Learn how to load related data</span></span>

<span data-ttu-id="930d2-117">对象关系映射 (ORM) 软件（如 Entity Framework）可通过多种方式将相关数据加载到实体的导航属性中：</span><span class="sxs-lookup"><span data-stu-id="930d2-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="930d2-118">预先加载。</span><span class="sxs-lookup"><span data-stu-id="930d2-118">Eager loading.</span></span> <span data-ttu-id="930d2-119">读取该实体时，会同时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="930d2-120">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="930d2-121">可使用 `Include` 和 `ThenInclude` 方法指定 Entity Framework Core 中的预先加载。</span><span class="sxs-lookup"><span data-stu-id="930d2-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![预先加载示例](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="930d2-123">可在单独查询中检索一些数据，EF 会“修正”导航属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="930d2-124">也就是说，EF 会自动添加单独检索的实体，将其添加到之前检索的实体的导航属性中所属的位置。</span><span class="sxs-lookup"><span data-stu-id="930d2-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="930d2-125">对于检索相关数据的查询，可使用 `Load` 方法，而不采用返回列表或对象的方法，如 `ToList` 或 `Single`。</span><span class="sxs-lookup"><span data-stu-id="930d2-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![单独查询示例](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="930d2-127">显式加载。</span><span class="sxs-lookup"><span data-stu-id="930d2-127">Explicit loading.</span></span> <span data-ttu-id="930d2-128">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="930d2-129">如有需要，可编写检索相关数据的代码。</span><span class="sxs-lookup"><span data-stu-id="930d2-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="930d2-130">就像使用单独查询进行预先加载一样，显式加载时会向数据库发送多个查询。</span><span class="sxs-lookup"><span data-stu-id="930d2-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="930d2-131">二者的区别在于，代码通过显式加载指定要加载的导航属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="930d2-132">在 Entity Framework Core 1.1 中，可使用 `Load` 方法执行显式加载。</span><span class="sxs-lookup"><span data-stu-id="930d2-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="930d2-133">例如：</span><span class="sxs-lookup"><span data-stu-id="930d2-133">For example:</span></span>

  ![显式加载示例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="930d2-135">延迟加载。</span><span class="sxs-lookup"><span data-stu-id="930d2-135">Lazy loading.</span></span> <span data-ttu-id="930d2-136">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="930d2-137">然而，首次尝试访问导航属性时，会自动检索导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="930d2-138">每次首次尝试从导航属性获取数据时，都向数据库发送查询。</span><span class="sxs-lookup"><span data-stu-id="930d2-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="930d2-139">Entity Framework Core 1.0 不支持延迟加载。</span><span class="sxs-lookup"><span data-stu-id="930d2-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="930d2-140">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="930d2-140">Performance considerations</span></span>

<span data-ttu-id="930d2-141">如果知道自己需要每个检索的实体的相关数据，选择预先加载可获得最佳性能，因为相比每个检索的实体的单独查询，发送到数据库的单个查询更加有效。</span><span class="sxs-lookup"><span data-stu-id="930d2-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="930d2-142">例如，假设每个系有十个相关课程。</span><span class="sxs-lookup"><span data-stu-id="930d2-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="930d2-143">预先加载所有相关数据时，只会进行单一（联接）查询，往返数据库一次。</span><span class="sxs-lookup"><span data-stu-id="930d2-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="930d2-144">单独查询每个系的课程时，会往返数据库十一次。</span><span class="sxs-lookup"><span data-stu-id="930d2-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="930d2-145">延迟较高时，额外往返数据库对性能尤为不利。</span><span class="sxs-lookup"><span data-stu-id="930d2-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="930d2-146">另一方面，在某些情况下，单独查询会更加高效。</span><span class="sxs-lookup"><span data-stu-id="930d2-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="930d2-147">在一个查询中预先加载所有相关数据时，可能会生成一个非常复杂的联接，SQL Server 无法有效处理该联接。</span><span class="sxs-lookup"><span data-stu-id="930d2-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="930d2-148">或者，如果你正在处理一组实体且只需访问其子集的导航属性，那么采用单独查询可获得更佳性能，因为预先加载所有数据后，会检索不需要的数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="930d2-149">如果看重性能，那么最好测试两种方式的性能，以便做出最佳选择。</span><span class="sxs-lookup"><span data-stu-id="930d2-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="930d2-150">创建“课程”页</span><span class="sxs-lookup"><span data-stu-id="930d2-150">Create a Courses page</span></span>

<span data-ttu-id="930d2-151">Course 实体包括导航属性，其中包含分配有课程的系的 Department 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="930d2-152">若要在课程列表中显示接受分配的系的名称，需从位于 `Course.Department` 导航属性中的 Department 实体获取 Name 属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="930d2-153">使用与带视图的 MVC 控制器相同的选项，及之前用于学生控制器的 Entity Framework 基架为 Course 实体类型创建名为 CoursesController 的控制器，如下图所示：</span><span class="sxs-lookup"><span data-stu-id="930d2-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![添加课程控制器](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="930d2-155">打开 CoursesController.cs 并检查 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="930d2-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="930d2-156">自动基架使用 `Include` 方法为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="930d2-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="930d2-157">将 `Index` 方法替换为以下代码，该代码为返回 Course 实体（是 `courses` 而不是 `schoolContext`）的 `IQueryable` 赋予了更合适的名称：</span><span class="sxs-lookup"><span data-stu-id="930d2-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="930d2-158">在 Views/Courses/Index.cshtml 中，将模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="930d2-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="930d2-159">突出显示所作更改：</span><span class="sxs-lookup"><span data-stu-id="930d2-159">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="930d2-160">已对基架代码进行了如下更改：</span><span class="sxs-lookup"><span data-stu-id="930d2-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="930d2-161">将标题从“索引”更改为“课程”。</span><span class="sxs-lookup"><span data-stu-id="930d2-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="930d2-162">添加了显示 `CourseID` 属性值的“数字”列。</span><span class="sxs-lookup"><span data-stu-id="930d2-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="930d2-163">默认情况下，不针对主键进行架构，因为对最终用户而言，它们通常没有意义。</span><span class="sxs-lookup"><span data-stu-id="930d2-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="930d2-164">但在这种情况下主键是有意义的，而你需要将其呈现出来。</span><span class="sxs-lookup"><span data-stu-id="930d2-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="930d2-165">更改“院系”列，显示院系名称。</span><span class="sxs-lookup"><span data-stu-id="930d2-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="930d2-166">该代码显示已加载到 `Department` 导航属性中的 Department 实体的 `Name` 属性：</span><span class="sxs-lookup"><span data-stu-id="930d2-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="930d2-167">运行应用并选择“课程”选项卡，查看包含系名称的列表。</span><span class="sxs-lookup"><span data-stu-id="930d2-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![“课程索引”页](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="930d2-169">创建“讲师”页</span><span class="sxs-lookup"><span data-stu-id="930d2-169">Create an Instructors page</span></span>

<span data-ttu-id="930d2-170">本节将为 Instructor 实体创建一个控制器和视图，从而显示“讲师”页：</span><span class="sxs-lookup"><span data-stu-id="930d2-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![“讲师索引”页](read-related-data/_static/instructors-index.png)

<span data-ttu-id="930d2-172">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="930d2-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="930d2-173">讲师列表显示 OfficeAssignment 实体的相关数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="930d2-174">Instructor 与 OfficeAssignment 实体间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="930d2-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="930d2-175">将预先加载 OfficeAssignment 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="930d2-176">如前所述，需要主表所有检索行的相关数据时，预先加载通常更有效。</span><span class="sxs-lookup"><span data-stu-id="930d2-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="930d2-177">在这种情况下，你希望显示所有显示的讲师的办公室分配情况。</span><span class="sxs-lookup"><span data-stu-id="930d2-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="930d2-178">用户选择一名讲师时，显示相关 Course 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="930d2-179">Instructor 和 Course 实体是多对多关系。</span><span class="sxs-lookup"><span data-stu-id="930d2-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="930d2-180">预先加载 Course 实体及其相关 Department 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="930d2-181">在这种情况下，单独查询可能更有效，因为仅需显示所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="930d2-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="930d2-182">但此示例显示的是如何在本身就位于导航属性内的实体中预先加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="930d2-183">用户选择一门课程时，会显示 Enrollment 实体集的相关数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="930d2-184">Course 和 Enrollment 实体是一对多关系。</span><span class="sxs-lookup"><span data-stu-id="930d2-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="930d2-185">单独查询 Enrollment 实体及其相关 Student 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="930d2-186">创建“讲师索引”视图的视图模型</span><span class="sxs-lookup"><span data-stu-id="930d2-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="930d2-187">“讲师”页显示来自三个不同表格的数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="930d2-188">因此将创建包含三个属性的视图模型，每个属性都包含一个表的数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="930d2-189">在 SchoolViewModels 文件夹中，创建 InstructorIndexData.cs，并使用以下代码替换现有代码 ：</span><span class="sxs-lookup"><span data-stu-id="930d2-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="930d2-190">创建讲师控制器和视图</span><span class="sxs-lookup"><span data-stu-id="930d2-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="930d2-191">使用 EF 读/写操作创建讲师控制器，如下图所示：</span><span class="sxs-lookup"><span data-stu-id="930d2-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![添加讲师控制器](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="930d2-193">打开 InstructorsController.cs 并为 ViewModels 名称空间添加 using 语句：</span><span class="sxs-lookup"><span data-stu-id="930d2-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="930d2-194">使用以下代码替换 Index 方法，预先加载相关数据并将其放入视图模型。</span><span class="sxs-lookup"><span data-stu-id="930d2-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="930d2-195">该方法接受可选路由数据 (`id`) 和查询字符串参数 (`courseID`)，二者提供所选讲师和课程的 ID 值。</span><span class="sxs-lookup"><span data-stu-id="930d2-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="930d2-196">参数由页面上的“选择”超链接提供。</span><span class="sxs-lookup"><span data-stu-id="930d2-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="930d2-197">代码先创建一个视图模型实例，并在其中放入讲师列表。</span><span class="sxs-lookup"><span data-stu-id="930d2-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="930d2-198">代码指定预先加载 `Instructor.OfficeAssignment` 和 `Instructor.CourseAssignments` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="930d2-199">在 `CourseAssignments` 属性中，加载 `Course` 属性，在其中加载 `Enrollments` 和 `Department` 属性，同时在每个 `Enrollment` 实体中加载 `Student` 属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="930d2-200">由于视图始终需要 OfficeAssignment 实体，因此更有效的做法是在同一查询中获取。</span><span class="sxs-lookup"><span data-stu-id="930d2-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="930d2-201">在网页中选择讲师后，需要 Course 实体，因此只有在页面频繁显示选中课程时，单个查询才比多个查询更有效。</span><span class="sxs-lookup"><span data-stu-id="930d2-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="930d2-202">代码重复 `CourseAssignments` 和 `Course`，因为你需要 `Course` 中的两个属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="930d2-203">`ThenInclude` 调用的第一个字符串获取 `CourseAssignment.Course`、`Course.Enrollments` 和 `Enrollment.Student`。</span><span class="sxs-lookup"><span data-stu-id="930d2-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="930d2-204">此时，代码中的另一个 `ThenInclude` 将成为 `Student` 的导航属性，你不需要该属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="930d2-205">但调用 `Include` 是从 `Instructor` 属性重新开始，因此必须再次遍历该链，这次指定 `Course.Department` 而不是 `Course.Enrollments`。</span><span class="sxs-lookup"><span data-stu-id="930d2-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="930d2-206">选择讲师时，将执行以下代码。</span><span class="sxs-lookup"><span data-stu-id="930d2-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="930d2-207">从视图模型中的讲师列表检索所选讲师。</span><span class="sxs-lookup"><span data-stu-id="930d2-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="930d2-208">然后，该视图模型的 `Courses` 属性加载讲师 `CourseAssignments` 导航属性中的 Course 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="930d2-209">`Where` 方法返回一个集合，但在这种情况下，向该方法传入条件后，只返回一个 Instructor 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="930d2-210">`Single` 方法将集合转换为单个 Instructor 实体，让你可以访问该实体的 `CourseAssignments` 属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="930d2-211">`CourseAssignments` 属性包含多个 `CourseAssignment` 实体，而你只需要相关的 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="930d2-212">如果知道集合只有一个项，则可在集合上使用 `Single` 方法。</span><span class="sxs-lookup"><span data-stu-id="930d2-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="930d2-213">如果集合为空或包含多个项，Single 方法将引发异常。</span><span class="sxs-lookup"><span data-stu-id="930d2-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="930d2-214">还可使用 `SingleOrDefault`，该方式在集合为空时返回默认值（本例中为 null）。</span><span class="sxs-lookup"><span data-stu-id="930d2-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="930d2-215">但在这种情况下，仍会引发异常（尝试在 null 引用上查找 `Courses` 属性时），并且异常消息不会清楚指出异常原因。</span><span class="sxs-lookup"><span data-stu-id="930d2-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="930d2-216">调用 `Single` 方法时，还可传入 Where 条件，而不是分别调用 `Where` 方法：</span><span class="sxs-lookup"><span data-stu-id="930d2-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="930d2-217">而不是：</span><span class="sxs-lookup"><span data-stu-id="930d2-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="930d2-218">接着，如果选择了课程，则从视图模型中的课程列表中检索所选课程。</span><span class="sxs-lookup"><span data-stu-id="930d2-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="930d2-219">然后，视图模型的 `Enrollments` 属性加载该课程 `Enrollments` 导航属性中的 Enrollment 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="930d2-220">修改讲师索引视图</span><span class="sxs-lookup"><span data-stu-id="930d2-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="930d2-221">在 Views/Instructors/Index.cshtml 中，将模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="930d2-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="930d2-222">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="930d2-222">The changes are highlighted.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-cshtml[](intro/samples/5cu-snap/Views/Instructors/Index.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-62&highlight=1,3-7,15-19,24,26-31,41-52,54)]

::: moniker-end

<span data-ttu-id="930d2-223">已对现有代码进行了如下更改：</span><span class="sxs-lookup"><span data-stu-id="930d2-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="930d2-224">将模型类更改为了 `InstructorIndexData`。</span><span class="sxs-lookup"><span data-stu-id="930d2-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="930d2-225">将页标题从“索引”更改为了“讲师” 。</span><span class="sxs-lookup"><span data-stu-id="930d2-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="930d2-226">添加了仅在 `item.OfficeAssignment` 不为 null 时才显示 `item.OfficeAssignment.Location` 的“办公室”列。</span><span class="sxs-lookup"><span data-stu-id="930d2-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="930d2-227">（由于这是一对零或一的关系，因此可能没有相关的 OfficeAssignment 实体。）</span><span class="sxs-lookup"><span data-stu-id="930d2-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```cshtml
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="930d2-228">添加了显示每位讲师所授课程的“课程”列。</span><span class="sxs-lookup"><span data-stu-id="930d2-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="930d2-229">有关更多信息，请参阅 Razor 语法文章的[显式行转换](xref:mvc/views/razor#explicit-line-transition)部分。</span><span class="sxs-lookup"><span data-stu-id="930d2-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="930d2-230">添加了代码来将 Bootstrap CSS 类附有条件地添加到所选讲师的 `tr` 元素中。</span><span class="sxs-lookup"><span data-stu-id="930d2-230">Added code that conditionally adds a Bootstrap CSS class to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="930d2-231">此类会设置选定行的背景色。</span><span class="sxs-lookup"><span data-stu-id="930d2-231">This class sets a background color for the selected row.</span></span>

* <span data-ttu-id="930d2-232">紧贴每行其他链接的前端添加了标有 Select 的新超链接，从而使所选讲师 ID 发送到 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="930d2-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```cshtml
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="930d2-233">运行应用并选择“讲师”选项卡。没有相关 OfficeAssignment 实体时，该页面显示相关 OfficeAssignment 实体的 Location 属性和空表格单元格。</span><span class="sxs-lookup"><span data-stu-id="930d2-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![未选择“讲师索引”页中的任何项](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="930d2-235">在 Views/Instructors/Index.cshtml 文件中，关闭表格元素（在文件末尾）后，添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="930d2-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="930d2-236">选择讲师时，此代码显示与讲师相关的课程列表。</span><span class="sxs-lookup"><span data-stu-id="930d2-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=63-98)]

<span data-ttu-id="930d2-237">此代码读取视图模型的 `Courses` 属性以显示课程列表。</span><span class="sxs-lookup"><span data-stu-id="930d2-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="930d2-238">它还提供 Select 超链接，该链接可将所选课程的 ID 发送到 `Index` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="930d2-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="930d2-239">刷新页面并选择讲师。</span><span class="sxs-lookup"><span data-stu-id="930d2-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="930d2-240">此时会出现一个网格，其中显示有分配给所选讲师的课程，且还显示有每个课程的分配系的名称。</span><span class="sxs-lookup"><span data-stu-id="930d2-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![已选择“讲师索引”页中的讲师](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="930d2-242">在刚刚添加的代码块后，添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="930d2-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="930d2-243">选择课程后，代码将显示参与课程的学生列表。</span><span class="sxs-lookup"><span data-stu-id="930d2-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="930d2-244">此代码读取视图模型的 Enrollment 属性，从而显示参与课程的学生列表。</span><span class="sxs-lookup"><span data-stu-id="930d2-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="930d2-245">再次刷新该页并选择讲师。</span><span class="sxs-lookup"><span data-stu-id="930d2-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="930d2-246">然后选择一门课程，查看参与的学生列表及其成绩。</span><span class="sxs-lookup"><span data-stu-id="930d2-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![已选择“讲师索引”页中的讲师和课程](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="930d2-248">关于显式加载</span><span class="sxs-lookup"><span data-stu-id="930d2-248">About explicit loading</span></span>

<span data-ttu-id="930d2-249">在 InstructorsController.cs 中检索讲师列表时，指定了预先加载 `CourseAssignments` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="930d2-250">假设你希望用户在选中讲师和课程时尽量少查看注册情况。</span><span class="sxs-lookup"><span data-stu-id="930d2-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="930d2-251">此时建议只在有请求时加载注册数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="930d2-252">若要查看如何执行显式加载的示例，请使用以下代码替换 `Index` 方法，这将删除预先加载 Enrollment 并显式加载该属性。</span><span class="sxs-lookup"><span data-stu-id="930d2-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="930d2-253">代码所作更改为突出显示状态。</span><span class="sxs-lookup"><span data-stu-id="930d2-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="930d2-254">新代码将从检索 Instructor 实体的代码中删除注册数据的 ThenInclude 方法调用。</span><span class="sxs-lookup"><span data-stu-id="930d2-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="930d2-255">它还会删除 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="930d2-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="930d2-256">如果选择了讲师和课程，突出显示的代码会检索所选课程的 Enrollment 实体，及每个 Enrollment 的 Student 实体。</span><span class="sxs-lookup"><span data-stu-id="930d2-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="930d2-257">运行应用，立即转到“讲师”索引页，尽管已经更改了数据的检索方式，但该页上显示的内容没有任何不同。</span><span class="sxs-lookup"><span data-stu-id="930d2-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="930d2-258">获取代码</span><span class="sxs-lookup"><span data-stu-id="930d2-258">Get the code</span></span>

[<span data-ttu-id="930d2-259">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="930d2-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="930d2-260">后续步骤</span><span class="sxs-lookup"><span data-stu-id="930d2-260">Next steps</span></span>

<span data-ttu-id="930d2-261">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="930d2-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="930d2-262">已了解如何加载相关数据</span><span class="sxs-lookup"><span data-stu-id="930d2-262">Learned how to load related data</span></span>
> * <span data-ttu-id="930d2-263">已创建“课程”页</span><span class="sxs-lookup"><span data-stu-id="930d2-263">Created a Courses page</span></span>
> * <span data-ttu-id="930d2-264">已创建“讲师”页</span><span class="sxs-lookup"><span data-stu-id="930d2-264">Created an Instructors page</span></span>
> * <span data-ttu-id="930d2-265">已了解显式加载</span><span class="sxs-lookup"><span data-stu-id="930d2-265">Learned about explicit loading</span></span>

<span data-ttu-id="930d2-266">请继续阅读下一篇教程，了解如何更新相关数据。</span><span class="sxs-lookup"><span data-stu-id="930d2-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="930d2-267">更新相关数据</span><span class="sxs-lookup"><span data-stu-id="930d2-267">Update related data</span></span>](update-related-data.md)
