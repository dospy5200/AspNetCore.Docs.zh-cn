---
title: 教程：实现 CRUD 功能 - ASP.NET MVC 和 EF Core
description: 在本教程中，将评审和自定义 MVC 基架在控制器和视图中自动创建的 CRUD （创建、读取、更新、删除）代码。
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
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
uid: data/ef-mvc/crud
ms.openlocfilehash: c3cfee107541a20a089809193d4bf7fa08730bd2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586392"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a><span data-ttu-id="26e4c-103">教程：实现 CRUD 功能 - ASP.NET MVC 和 EF Core</span><span class="sxs-lookup"><span data-stu-id="26e4c-103">Tutorial: Implement CRUD Functionality - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="26e4c-104">在上一个教程中，创建了一个使用 Entity Framework 和 SQL Server LocalDB 来存储和显示数据的 MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="26e4c-104">In the previous tutorial, you created an MVC application that stores and displays data using the Entity Framework and SQL Server LocalDB.</span></span> <span data-ttu-id="26e4c-105">在本教程中，将评审和自定义 MVC 基架在控制器和视图中自动创建的 CRUD （创建、读取、更新、删除）代码。</span><span class="sxs-lookup"><span data-stu-id="26e4c-105">In this tutorial, you'll review and customize the CRUD (create, read, update, delete) code that the MVC scaffolding automatically creates for you in controllers and views.</span></span>

> [!NOTE]
> <span data-ttu-id="26e4c-106">为了在控制器和数据访问层之间创建一个抽象层，常见的做法是实现存储库模式。</span><span class="sxs-lookup"><span data-stu-id="26e4c-106">It's a common practice to implement the repository pattern in order to create an abstraction layer between your controller and the data access layer.</span></span> <span data-ttu-id="26e4c-107">为了保持这些教程内容简单并重点介绍如何使用 Entity Framework 本身，它们不使用存储库。</span><span class="sxs-lookup"><span data-stu-id="26e4c-107">To keep these tutorials simple and focused on teaching how to use the Entity Framework itself, they don't use repositories.</span></span> <span data-ttu-id="26e4c-108">有关存储库和 EF 的信息，请参阅[本系列中的最后一个教程](advanced.md)。</span><span class="sxs-lookup"><span data-stu-id="26e4c-108">For information about repositories with EF, see [the last tutorial in this series](advanced.md).</span></span>

<span data-ttu-id="26e4c-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="26e4c-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="26e4c-110">自定义“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-110">Customize the Details page</span></span>
> * <span data-ttu-id="26e4c-111">更新“创建”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-111">Update the Create page</span></span>
> * <span data-ttu-id="26e4c-112">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-112">Update the Edit page</span></span>
> * <span data-ttu-id="26e4c-113">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-113">Update the Delete page</span></span>
> * <span data-ttu-id="26e4c-114">关闭数据库连接</span><span class="sxs-lookup"><span data-stu-id="26e4c-114">Close database connections</span></span>

## <a name="prerequisites"></a><span data-ttu-id="26e4c-115">先决条件</span><span class="sxs-lookup"><span data-stu-id="26e4c-115">Prerequisites</span></span>

* [<span data-ttu-id="26e4c-116">EF Core 和 ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="26e4c-116">Get started with EF Core and ASP.NET Core MVC</span></span>](intro.md)

## <a name="customize-the-details-page"></a><span data-ttu-id="26e4c-117">自定义“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-117">Customize the Details page</span></span>

<span data-ttu-id="26e4c-118">学生索引页的基架代码省略了 `Enrollments` 属性，因为该属性包含一个集合。</span><span class="sxs-lookup"><span data-stu-id="26e4c-118">The scaffolded code for the Students Index page left out the `Enrollments` property, because that property holds a collection.</span></span> <span data-ttu-id="26e4c-119">在“详细信息”页上，将以 HTML 表形式显示集合的内容。</span><span class="sxs-lookup"><span data-stu-id="26e4c-119">In the **Details** page, you'll display the contents of the collection in an HTML table.</span></span>

<span data-ttu-id="26e4c-120">在 Controllers/StudentsController.cs 中，“详细信息”视图的操作方法使用 `SingleOrDefaultAsync` 方法检索单个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="26e4c-120">In *Controllers/StudentsController.cs*, the action method for the Details view uses the `SingleOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="26e4c-121">添加调用 `Include` 的代码。</span><span class="sxs-lookup"><span data-stu-id="26e4c-121">Add code that calls `Include`.</span></span> <span data-ttu-id="26e4c-122">`ThenInclude` 和 `AsNoTracking` 方法，如以下突出显示的代码所示。</span><span class="sxs-lookup"><span data-stu-id="26e4c-122">`ThenInclude`,  and `AsNoTracking` methods, as shown in the following highlighted code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="26e4c-123">`Include` 和 `ThenInclude` 方法使上下文加载 `Student.Enrollments` 导航属性，并在每个注册中加载 `Enrollment.Course` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="26e4c-123">The `Include` and `ThenInclude` methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span>  <span data-ttu-id="26e4c-124">有关这些方法的详细信息，请参阅[读取相关数据](read-related-data.md)教程。</span><span class="sxs-lookup"><span data-stu-id="26e4c-124">You'll learn more about these methods in the [read related data](read-related-data.md) tutorial.</span></span>

<span data-ttu-id="26e4c-125">对于返回的实体未在当前上下文生存期中更新的情况，`AsNoTracking` 方法将会提升性能。</span><span class="sxs-lookup"><span data-stu-id="26e4c-125">The `AsNoTracking` method improves performance in scenarios where the entities returned won't be updated in the current context's lifetime.</span></span> <span data-ttu-id="26e4c-126">本教程末尾将介绍有关 `AsNoTracking` 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="26e4c-126">You'll learn more about `AsNoTracking` at the end of this tutorial.</span></span>

### <a name="route-data"></a><span data-ttu-id="26e4c-127">路由数据</span><span class="sxs-lookup"><span data-stu-id="26e4c-127">Route data</span></span>

<span data-ttu-id="26e4c-128">传递到 `Details` 方法的键值来自路由数据。</span><span class="sxs-lookup"><span data-stu-id="26e4c-128">The key value that's passed to the `Details` method comes from *route data*.</span></span> <span data-ttu-id="26e4c-129">路由数据是模型绑定器在 URL 的段中找到的数据。</span><span class="sxs-lookup"><span data-stu-id="26e4c-129">Route data is data that the model binder found in a segment of the URL.</span></span> <span data-ttu-id="26e4c-130">例如，默认路由指定控制器、操作和 ID 段：</span><span class="sxs-lookup"><span data-stu-id="26e4c-130">For example, the default route specifies controller, action, and id segments:</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

<span data-ttu-id="26e4c-131">在下面的 URL 中，默认路由将 Instructor 映射作为控制器、Index 作为操作，1 作为 ID；这些都是路由数据值。</span><span class="sxs-lookup"><span data-stu-id="26e4c-131">In the following URL, the default route maps Instructor as the controller, Index as the action, and 1 as the id; these are route data values.</span></span>

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

<span data-ttu-id="26e4c-132">URL 的最后部分 ("?courseID=2021") 是一个查询字符串。</span><span class="sxs-lookup"><span data-stu-id="26e4c-132">The last part of the URL ("?courseID=2021") is a query string value.</span></span> <span data-ttu-id="26e4c-133">如果将 `id` 作为查询字符串值传递，模型绑定器也会将 ID 值作为参数传递给 `Index` 方法：</span><span class="sxs-lookup"><span data-stu-id="26e4c-133">The model binder will also pass the ID value to the `Index` method `id` parameter if you pass it as a query string value:</span></span>

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

<span data-ttu-id="26e4c-134">在索引页中，超链接 URL 由 Razor 视图中的标记帮助器语句创建。</span><span class="sxs-lookup"><span data-stu-id="26e4c-134">In the Index page, hyperlink URLs are created by tag helper statements in the Razor view.</span></span> <span data-ttu-id="26e4c-135">在以下 Razor 代码中，`id` 参数与默认路由相匹配，因此 `id` 将添加到路由数据。</span><span class="sxs-lookup"><span data-stu-id="26e4c-135">In the following Razor code, the `id` parameter matches the default route, so `id` is added to the route data.</span></span>

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

<span data-ttu-id="26e4c-136">`item.ID` 为 6 时，会生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="26e4c-136">This generates the following HTML when `item.ID` is 6:</span></span>

```html
<a href="/Students/Edit/6">Edit</a>
```

<span data-ttu-id="26e4c-137">在以下 Razor 代码中，`studentID` 与默认路由中的参数不匹配，所以将它作为查询字符串添加。</span><span class="sxs-lookup"><span data-stu-id="26e4c-137">In the following Razor code, `studentID` doesn't match a parameter in the default route, so it's added as a query string.</span></span>

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

<span data-ttu-id="26e4c-138">`item.ID` 为 6 时，会生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="26e4c-138">This generates the following HTML when `item.ID` is 6:</span></span>

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

<span data-ttu-id="26e4c-139">有关标记帮助程序的详细信息，请参阅 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="26e4c-139">For more information about tag helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

### <a name="add-enrollments-to-the-details-view"></a><span data-ttu-id="26e4c-140">将注册添加到“详细信息”视图</span><span class="sxs-lookup"><span data-stu-id="26e4c-140">Add enrollments to the Details view</span></span>

<span data-ttu-id="26e4c-141">打开 *Views/Students/Details.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="26e4c-141">Open *Views/Students/Details.cshtml*.</span></span> <span data-ttu-id="26e4c-142">每个字段都使用 `DisplayNameFor` 和 `DisplayFor` 帮助器来显示，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="26e4c-142">Each field is displayed using `DisplayNameFor` and `DisplayFor` helpers, as shown in the following example:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

<span data-ttu-id="26e4c-143">在最后一个字段之后和 `</dl>` 闭合标记之前，添加以下代码以显示注册列表：</span><span class="sxs-lookup"><span data-stu-id="26e4c-143">After the last field and immediately before the closing `</dl>` tag, add the following code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

<span data-ttu-id="26e4c-144">如果代码缩进在粘贴代码后出现错误，请按 CTRL+K+D 进行更正。</span><span class="sxs-lookup"><span data-stu-id="26e4c-144">If code indentation is wrong after you paste the code, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="26e4c-145">此代码循环通过 `Enrollments` 导航属性中的实体。</span><span class="sxs-lookup"><span data-stu-id="26e4c-145">This code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="26e4c-146">它将针对每个注册显示课程标题和成绩。</span><span class="sxs-lookup"><span data-stu-id="26e4c-146">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="26e4c-147">课程标题从 Course 实体中检索，该实体存储在 Enrollments 实体的 `Course` 导航属性中。</span><span class="sxs-lookup"><span data-stu-id="26e4c-147">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="26e4c-148">运行应用，选择“学生”选项卡，然后单击学生的“详细信息”链接 。</span><span class="sxs-lookup"><span data-stu-id="26e4c-148">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="26e4c-149">将看到所选学生的课程和年级列表：</span><span class="sxs-lookup"><span data-stu-id="26e4c-149">You see the list of courses and grades for the selected student:</span></span>

![学生详细信息页](crud/_static/student-details.png)

## <a name="update-the-create-page"></a><span data-ttu-id="26e4c-151">更新“创建”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-151">Update the Create page</span></span>

<span data-ttu-id="26e4c-152">在 *StudentsController.cs* 中修改 HttpPost `Create` 方法，在 `Bind` 特性中添加 try catch 块并删除 ID 值。</span><span class="sxs-lookup"><span data-stu-id="26e4c-152">In *StudentsController.cs*, modify the HttpPost `Create` method by adding a try-catch block and removing ID from the `Bind` attribute.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

<span data-ttu-id="26e4c-153">此代码将 ASP.NET Core MVC 模型绑定器创建的 Student 实体添加到 Students 实体集，然后将更改保存到数据库。</span><span class="sxs-lookup"><span data-stu-id="26e4c-153">This code adds the Student entity created by the ASP.NET Core MVC model binder to the Students entity set and then saves the changes to the database.</span></span> <span data-ttu-id="26e4c-154">（模型绑定器指的是 ASP.NET Core MVC 功能，用户可利用它来轻松处理使用表单提交的数据；模型绑定器将已发布的表单值转换为 CLR 类型，并将其传递给操作方法的参数。</span><span class="sxs-lookup"><span data-stu-id="26e4c-154">(Model binder refers to the ASP.NET Core MVC functionality that makes it easier for you to work with data submitted by a form; a model binder converts posted form values to CLR types and passes them to the action method in parameters.</span></span> <span data-ttu-id="26e4c-155">在本例中，模型绑定器将使用 Form 集合的属性值实例化 Student 实体。）</span><span class="sxs-lookup"><span data-stu-id="26e4c-155">In this case, the model binder instantiates a Student entity for you using property values from the Form collection.)</span></span>

<span data-ttu-id="26e4c-156">已从 `Bind` 特性删除 `ID`，因为 ID 是插入行时 SQL Server 将自动设置的主键值。</span><span class="sxs-lookup"><span data-stu-id="26e4c-156">You removed `ID` from the `Bind` attribute because ID is the primary key value which SQL Server will set automatically when the row is inserted.</span></span> <span data-ttu-id="26e4c-157">来自用户的输入不会设置 ID 值。</span><span class="sxs-lookup"><span data-stu-id="26e4c-157">Input from the user doesn't set the ID value.</span></span>

<span data-ttu-id="26e4c-158">除了 `Bind` 特性，try-catch 块是对基架代码所做的唯一更改。</span><span class="sxs-lookup"><span data-stu-id="26e4c-158">Other than the `Bind` attribute, the try-catch block is the only change you've made to the scaffolded code.</span></span> <span data-ttu-id="26e4c-159">如果保存更改时捕获到来自 `DbUpdateException` 的异常，则会显示一般错误消息。</span><span class="sxs-lookup"><span data-stu-id="26e4c-159">If an exception that derives from `DbUpdateException` is caught while the changes are being saved, a generic error message is displayed.</span></span> <span data-ttu-id="26e4c-160">有时 `DbUpdateException` 异常是由应用程序外部的某些内容而非编程错误引起的，因此建议用户再次尝试。</span><span class="sxs-lookup"><span data-stu-id="26e4c-160">`DbUpdateException` exceptions are sometimes caused by something external to the application rather than a programming error, so the user is advised to try again.</span></span> <span data-ttu-id="26e4c-161">尽管在本示例中未实现，但生产质量应用程序会记录异常。</span><span class="sxs-lookup"><span data-stu-id="26e4c-161">Although not implemented in this sample, a production quality application would log the exception.</span></span> <span data-ttu-id="26e4c-162">有关详细信息，请参阅[监视和遥测（使用 Azure 构建真实世界云应用）](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)中的“见解记录”部分。</span><span class="sxs-lookup"><span data-stu-id="26e4c-162">For more information, see the **Log for insight** section in [Monitoring and Telemetry (Building Real-World Cloud Apps with Azure)](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).</span></span>

<span data-ttu-id="26e4c-163">`ValidateAntiForgeryToken` 特性帮助抵御跨网站请求伪造 (CSRF) 攻击。</span><span class="sxs-lookup"><span data-stu-id="26e4c-163">The `ValidateAntiForgeryToken` attribute helps prevent cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="26e4c-164">令牌通过 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) 自动注入到视图中，并在用户提交表单时包含该令牌。</span><span class="sxs-lookup"><span data-stu-id="26e4c-164">The token is automatically injected into the view by the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) and is included when the form is submitted by the user.</span></span> <span data-ttu-id="26e4c-165">令牌由 `ValidateAntiForgeryToken` 特性验证。</span><span class="sxs-lookup"><span data-stu-id="26e4c-165">The token is validated by the `ValidateAntiForgeryToken` attribute.</span></span> <span data-ttu-id="26e4c-166">有关详细信息，请参阅 <xref:security/anti-request-forgery>。</span><span class="sxs-lookup"><span data-stu-id="26e4c-166">For more information, see <xref:security/anti-request-forgery>.</span></span>

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a><span data-ttu-id="26e4c-167">有关过多发布的安全说明</span><span class="sxs-lookup"><span data-stu-id="26e4c-167">Security note about overposting</span></span>

<span data-ttu-id="26e4c-168">基架代码包含在 `Create` 方法中的 `Bind` 特性是防止在创建方案中过多发布的一种方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-168">The `Bind` attribute that the scaffolded code includes on the `Create` method is one way to protect against overposting in create scenarios.</span></span> <span data-ttu-id="26e4c-169">例如，假设 Student 实体包含不希望此网页设置的 `Secret` 属性。</span><span class="sxs-lookup"><span data-stu-id="26e4c-169">For example, suppose the Student entity includes a `Secret` property that you don't want this web page to set.</span></span>

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

<span data-ttu-id="26e4c-170">即使网页上没有 `Secret` 字段，黑客也可以使用 Fiddler 之类的工具，或者编写一些 JavaScript 来发布 `Secret` 表单值。</span><span class="sxs-lookup"><span data-stu-id="26e4c-170">Even if you don't have a `Secret` field on the web page, a hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="26e4c-171">创建 Student 实例时，如果不利用 `Bind` 特性来限制模型绑定器使用的字段，模型绑定器会选取该 `Secret` 表单值并使用它来创建 Student 实体实例。</span><span class="sxs-lookup"><span data-stu-id="26e4c-171">Without the `Bind` attribute limiting the fields that the model binder uses when it creates a Student instance, the model binder would pick up that `Secret` form value and use it to create the Student entity instance.</span></span> <span data-ttu-id="26e4c-172">然后将在数据库中更新黑客为 `Secret` 表单字段指定的任意值。</span><span class="sxs-lookup"><span data-stu-id="26e4c-172">Then whatever value the hacker specified for the `Secret` form field would be updated in your database.</span></span> <span data-ttu-id="26e4c-173">下图显示 Fiddler 工具正在将 `Secret` 字段（值为“OverPost”）添加到已发布的表单值。</span><span class="sxs-lookup"><span data-stu-id="26e4c-173">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler 添加 Secret 字段](crud/_static/fiddler.png)

<span data-ttu-id="26e4c-175">然后值“OverPost”将成功添加到插入行的 `Secret` 属性，尽管你从未打算网页可设置该属性。</span><span class="sxs-lookup"><span data-stu-id="26e4c-175">The value "OverPost" would then be successfully added to the `Secret` property of the inserted row, although you never intended that the web page be able to set that property.</span></span>

<span data-ttu-id="26e4c-176">可以防止在编辑方案中过多发布，方法是首先从数据库读取实体，然后调用 `TryUpdateModel` 并在显式允许的属性列表中传递。</span><span class="sxs-lookup"><span data-stu-id="26e4c-176">You can prevent overposting in edit scenarios by reading the entity from the database first and then calling `TryUpdateModel`, passing in an explicit allowed properties list.</span></span> <span data-ttu-id="26e4c-177">这些教程中使用的也是这种方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-177">That's the method used in these tutorials.</span></span>

<span data-ttu-id="26e4c-178">许多开发者首选的防止过多发布的另一种方法是使用视图模型，而不是包含模型绑定的实体类。</span><span class="sxs-lookup"><span data-stu-id="26e4c-178">An alternative way to prevent overposting that's preferred by many developers is to use view models rather than entity classes with model binding.</span></span> <span data-ttu-id="26e4c-179">仅包含想要在视图模型中更新的属性。</span><span class="sxs-lookup"><span data-stu-id="26e4c-179">Include only the properties you want to update in the view model.</span></span> <span data-ttu-id="26e4c-180">完成 MVC 模型绑定器后，根据需要使用 AutoMapper 之类的工具将视图模型属性复制到实体实例。</span><span class="sxs-lookup"><span data-stu-id="26e4c-180">Once the MVC model binder has finished, copy the view model properties to the entity instance, optionally using a tool such as AutoMapper.</span></span> <span data-ttu-id="26e4c-181">使用实体实例上的 `_context.Entry` 将其状态设置为 `Unchanged`，然后在视图模型中包含的每个实体属性上将 `Property("PropertyName").IsModified` 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="26e4c-181">Use `_context.Entry` on the entity instance to set its state to `Unchanged`, and then set `Property("PropertyName").IsModified` to true on each entity property that's included in the view model.</span></span> <span data-ttu-id="26e4c-182">此方法同时适用于编辑和创建方案。</span><span class="sxs-lookup"><span data-stu-id="26e4c-182">This method works in both edit and create scenarios.</span></span>

### <a name="test-the-create-page"></a><span data-ttu-id="26e4c-183">测试创建页</span><span class="sxs-lookup"><span data-stu-id="26e4c-183">Test the Create page</span></span>

<span data-ttu-id="26e4c-184">Views/Students/Create.cshtml 中的代码对每个字段使用 `label`、`input` 和 `span`（适用于验证消息）标记帮助器。</span><span class="sxs-lookup"><span data-stu-id="26e4c-184">The code in *Views/Students/Create.cshtml* uses `label`, `input`, and `span` (for validation messages) tag helpers for each field.</span></span>

<span data-ttu-id="26e4c-185">运行应用，选择“学生”选项卡，并单击“新建”。</span><span class="sxs-lookup"><span data-stu-id="26e4c-185">Run the app, select the **Students** tab, and click **Create New**.</span></span>

<span data-ttu-id="26e4c-186">输入姓名和日期。</span><span class="sxs-lookup"><span data-stu-id="26e4c-186">Enter names and a date.</span></span> <span data-ttu-id="26e4c-187">如果浏览器允许输入无效日期，请尝试输入。</span><span class="sxs-lookup"><span data-stu-id="26e4c-187">Try entering an invalid date if your browser lets you do that.</span></span> <span data-ttu-id="26e4c-188">（某些浏览器强制要求使用日期选取器。）然后单击“创建”，查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="26e4c-188">(Some browsers force you to use a date picker.) Then click **Create** to see the error message.</span></span>

![日期验证错误](crud/_static/date-error.png)

<span data-ttu-id="26e4c-190">这是默认获取的服务器端验证；在下一个教程中，还将介绍如何添加生成客户端验证代码的特性。</span><span class="sxs-lookup"><span data-stu-id="26e4c-190">This is server-side validation that you get by default; in a later tutorial you'll see how to add attributes that will generate code for client-side validation also.</span></span> <span data-ttu-id="26e4c-191">以下突出显示的代码显示 `Create` 方法中的模型验证检查。</span><span class="sxs-lookup"><span data-stu-id="26e4c-191">The following highlighted code shows the model validation check in the `Create` method.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

<span data-ttu-id="26e4c-192">将日期更改为有效值，并单击“创建”，查看“索引”页中显示的新学生。</span><span class="sxs-lookup"><span data-stu-id="26e4c-192">Change the date to a valid value and click **Create** to see the new student appear in the **Index** page.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="26e4c-193">更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-193">Update the Edit page</span></span>

<span data-ttu-id="26e4c-194">在 StudentController.cs 中，HttpGet `Edit` 方法（不具有 `HttpPost` 特性）使用 `SingleOrDefaultAsync` 方法检索所选的 Student 实体，如 `Details` 方法中所示。</span><span class="sxs-lookup"><span data-stu-id="26e4c-194">In *StudentController.cs*, the HttpGet `Edit` method (the one without the `HttpPost` attribute) uses the `SingleOrDefaultAsync` method to retrieve the selected Student entity, as you saw in the `Details` method.</span></span> <span data-ttu-id="26e4c-195">不需要更改此方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-195">You don't need to change this method.</span></span>

### <a name="recommended-httppost-edit-code-read-and-update"></a><span data-ttu-id="26e4c-196">建议的 HttpPost 编辑代码：读取和更新</span><span class="sxs-lookup"><span data-stu-id="26e4c-196">Recommended HttpPost Edit code: Read and update</span></span>

<span data-ttu-id="26e4c-197">使用以下代码替换 HttpPost Edit 操作方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-197">Replace the HttpPost Edit action method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

<span data-ttu-id="26e4c-198">这些更改实现安全最佳做法，防止过多发布。</span><span class="sxs-lookup"><span data-stu-id="26e4c-198">These changes implement a security best practice to prevent overposting.</span></span> <span data-ttu-id="26e4c-199">基架生成了 `Bind` 特性，并将模型绑定器创建的实体添加到具有 `Modified` 标记的实体集。</span><span class="sxs-lookup"><span data-stu-id="26e4c-199">The scaffolder generated a `Bind` attribute and added the entity created by the model binder to the entity set with a `Modified` flag.</span></span> <span data-ttu-id="26e4c-200">不建议将该代码用于多个方案，因为 `Bind` 特性将清除未在 `Include` 参数中列出的字段中的任何以前存在的数据。</span><span class="sxs-lookup"><span data-stu-id="26e4c-200">That code isn't recommended for many scenarios because the `Bind` attribute clears out any pre-existing data in fields not listed in the `Include` parameter.</span></span>

<span data-ttu-id="26e4c-201">新代码读取现有实体并调用 `TryUpdateModel`，以[基于已发布表单数据中的用户输入](xref:mvc/models/model-binding)更新已检索实体中的字段。</span><span class="sxs-lookup"><span data-stu-id="26e4c-201">The new code reads the existing entity and calls `TryUpdateModel` to update fields in the retrieved entity [based on user input in the posted form data](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="26e4c-202">Entity Framework 的自动更改跟踪在由表单输入更改的字段上设置 `Modified` 标记。</span><span class="sxs-lookup"><span data-stu-id="26e4c-202">The Entity Framework's automatic change tracking sets the `Modified` flag on the fields that are changed by form input.</span></span> <span data-ttu-id="26e4c-203">调用 `SaveChanges` 方法时，Entity Framework 会创建 SQL 语句，以更新数据库行。</span><span class="sxs-lookup"><span data-stu-id="26e4c-203">When the `SaveChanges` method is called, the Entity Framework creates SQL statements to update the database row.</span></span> <span data-ttu-id="26e4c-204">忽略并发冲突，并且仅在数据库中更新由用户更新的表列。</span><span class="sxs-lookup"><span data-stu-id="26e4c-204">Concurrency conflicts are ignored, and only the table columns that were updated by the user are updated in the database.</span></span> <span data-ttu-id="26e4c-205">（下一个教程将介绍如何处理并发冲突。）</span><span class="sxs-lookup"><span data-stu-id="26e4c-205">(A later tutorial shows how to handle concurrency conflicts.)</span></span>

<span data-ttu-id="26e4c-206">作为防止过多发布的最佳做法，请在 `TryUpdateModel` 参数中声明希望通过“编辑”页更新的字段。</span><span class="sxs-lookup"><span data-stu-id="26e4c-206">As a best practice to prevent overposting, the fields that you want to be updateable by the **Edit** page are declared in the `TryUpdateModel` parameters.</span></span> <span data-ttu-id="26e4c-207">（参数列表中字段列表之前的空字符串用于与表单字段名称一起使用的前缀。）目前没有要保护的额外字段，但是列出希望模型绑定器绑定的字段可确保以后将字段添加到数据模型时，它们将自动受到保护，直到明确将其添加到此处为止。</span><span class="sxs-lookup"><span data-stu-id="26e4c-207">(The empty string preceding the list of fields in the parameter list is for a prefix to use with the form fields names.) Currently there are no extra fields that you're protecting, but listing the fields that you want the model binder to bind ensures that if you add fields to the data model in the future, they're automatically protected until you explicitly add them here.</span></span>

<span data-ttu-id="26e4c-208">这些更改会导致 HttpPost `Edit` 方法与 HttpGet `Edit` 方法的方法签名相同，因此已重命名 `EditPost` 方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-208">As a result of these changes, the method signature of the HttpPost `Edit` method is the same as the HttpGet `Edit` method; therefore you've renamed the method `EditPost`.</span></span>

### <a name="alternative-httppost-edit-code-create-and-attach"></a><span data-ttu-id="26e4c-209">可选 HttpPost 编辑代码：创建并附加</span><span class="sxs-lookup"><span data-stu-id="26e4c-209">Alternative HttpPost Edit code: Create and attach</span></span>

<span data-ttu-id="26e4c-210">建议的 HttpPost 编辑代码确保只更新已更改的列，并保留不希望包含在模型绑定内的属性中的数据。</span><span class="sxs-lookup"><span data-stu-id="26e4c-210">The recommended HttpPost edit code ensures that only changed columns get updated and preserves data in properties that you don't want included for model binding.</span></span> <span data-ttu-id="26e4c-211">但是，读取优先的方法需要额外的数据库读取，并可能产生处理并发冲突的更复杂代码。</span><span class="sxs-lookup"><span data-stu-id="26e4c-211">However, the read-first approach requires an extra database read, and can result in more complex code for handling concurrency conflicts.</span></span> <span data-ttu-id="26e4c-212">另一种方法是将模型绑定器创建的实体附加到 EF 上下文，并将其标记为已修改。</span><span class="sxs-lookup"><span data-stu-id="26e4c-212">An alternative is to attach an entity created by the model binder to the EF context and mark it as modified.</span></span> <span data-ttu-id="26e4c-213">（请勿使用此代码更新项目，它只是显示一种可选的方法。）</span><span class="sxs-lookup"><span data-stu-id="26e4c-213">(Don't update your project with this code, it's only shown to illustrate an optional approach.)</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

<span data-ttu-id="26e4c-214">网页 UI 包含实体中的所有字段并能更新其中任意字段时，可以使用此方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-214">You can use this approach when the web page UI includes all of the fields in the entity and can update any of them.</span></span>

<span data-ttu-id="26e4c-215">基架代码使用创建和附加方法，但仅捕获 `DbUpdateConcurrencyException` 异常并返回 404 错误代码。</span><span class="sxs-lookup"><span data-stu-id="26e4c-215">The scaffolded code uses the create-and-attach approach but only catches `DbUpdateConcurrencyException` exceptions and returns 404 error codes.</span></span>  <span data-ttu-id="26e4c-216">显示的示例捕获任意数据库更新异常并显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="26e4c-216">The example shown catches any database update exception and displays an error message.</span></span>

### <a name="entity-states"></a><span data-ttu-id="26e4c-217">实体状态</span><span class="sxs-lookup"><span data-stu-id="26e4c-217">Entity States</span></span>

<span data-ttu-id="26e4c-218">数据库上下文跟踪内存中的实体是否与数据库中相应的行同步，并且此信息确定调用 `SaveChanges` 方法时会发生的情况。</span><span class="sxs-lookup"><span data-stu-id="26e4c-218">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database, and this information determines what happens when you call the `SaveChanges` method.</span></span> <span data-ttu-id="26e4c-219">例如，将新实体传递到 `Add` 方法时，该实体的状态会设置为 `Added`。</span><span class="sxs-lookup"><span data-stu-id="26e4c-219">For example, when you pass a new entity to the `Add` method, that entity's state is set to `Added`.</span></span> <span data-ttu-id="26e4c-220">然后调用 `SaveChanges` 方法时，数据库上下文发出 SQL INSERT 命令。</span><span class="sxs-lookup"><span data-stu-id="26e4c-220">Then when you call the `SaveChanges` method, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="26e4c-221">实体可能处于以下状态之一：</span><span class="sxs-lookup"><span data-stu-id="26e4c-221">An entity may be in one of the following states:</span></span>

* <span data-ttu-id="26e4c-222">`Added`.</span><span class="sxs-lookup"><span data-stu-id="26e4c-222">`Added`.</span></span> <span data-ttu-id="26e4c-223">数据库中尚不存在实体。</span><span class="sxs-lookup"><span data-stu-id="26e4c-223">The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="26e4c-224">`SaveChanges` 方法发出 INSERT 语句。</span><span class="sxs-lookup"><span data-stu-id="26e4c-224">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="26e4c-225">`Unchanged`.</span><span class="sxs-lookup"><span data-stu-id="26e4c-225">`Unchanged`.</span></span> <span data-ttu-id="26e4c-226">不需要通过 `SaveChanges` 方法对此实体执行操作。</span><span class="sxs-lookup"><span data-stu-id="26e4c-226">Nothing needs to be done with this entity by the `SaveChanges` method.</span></span> <span data-ttu-id="26e4c-227">从数据库读取实体时，实体将从此状态开始。</span><span class="sxs-lookup"><span data-stu-id="26e4c-227">When you read an entity from the database, the entity starts out with this status.</span></span>

* <span data-ttu-id="26e4c-228">`Modified`.</span><span class="sxs-lookup"><span data-stu-id="26e4c-228">`Modified`.</span></span> <span data-ttu-id="26e4c-229">已修改实体的部分或全部属性值。</span><span class="sxs-lookup"><span data-stu-id="26e4c-229">Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="26e4c-230">`SaveChanges` 方法发出 UPDATE 语句。</span><span class="sxs-lookup"><span data-stu-id="26e4c-230">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="26e4c-231">`Deleted`.</span><span class="sxs-lookup"><span data-stu-id="26e4c-231">`Deleted`.</span></span> <span data-ttu-id="26e4c-232">已标记该实体进行删除。</span><span class="sxs-lookup"><span data-stu-id="26e4c-232">The entity has been marked for deletion.</span></span> <span data-ttu-id="26e4c-233">`SaveChanges` 方法发出 DELETE 语句。</span><span class="sxs-lookup"><span data-stu-id="26e4c-233">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="26e4c-234">`Detached`.</span><span class="sxs-lookup"><span data-stu-id="26e4c-234">`Detached`.</span></span> <span data-ttu-id="26e4c-235">数据库上下文未跟踪该实体。</span><span class="sxs-lookup"><span data-stu-id="26e4c-235">The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="26e4c-236">在桌面应用程序中，通常会自动设置状态更改。</span><span class="sxs-lookup"><span data-stu-id="26e4c-236">In a desktop application, state changes are typically set automatically.</span></span> <span data-ttu-id="26e4c-237">读取一个实体并对其某些属性值做出更改。</span><span class="sxs-lookup"><span data-stu-id="26e4c-237">You read an entity and make changes to some of its property values.</span></span> <span data-ttu-id="26e4c-238">这将使其实体状态自动更改为 `Modified`。</span><span class="sxs-lookup"><span data-stu-id="26e4c-238">This causes its entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="26e4c-239">然后调用 `SaveChanges` 时，Entity Framework 生成 SQL UPDATE 语句，该语句仅更新已更改的实际属性。</span><span class="sxs-lookup"><span data-stu-id="26e4c-239">Then when you call `SaveChanges`, the Entity Framework generates a SQL UPDATE statement that updates only the actual properties that you changed.</span></span>

<span data-ttu-id="26e4c-240">在 Web 应用中，最初读取实体并显示其要编辑的数据的 `DbContext` 将在页面呈现后进行处理。</span><span class="sxs-lookup"><span data-stu-id="26e4c-240">In a web app, the `DbContext` that initially reads an entity and displays its data to be edited is disposed after a page is rendered.</span></span> <span data-ttu-id="26e4c-241">调用 HttpPost `Edit` 操作方法时，将发出新的 Web 请求并且具有 `DbContext` 的新实例。</span><span class="sxs-lookup"><span data-stu-id="26e4c-241">When the HttpPost `Edit` action method is called,  a new web request is made and you have a new instance of the `DbContext`.</span></span> <span data-ttu-id="26e4c-242">如果在新的上下文中重新读取实体，则将模拟桌面处理。</span><span class="sxs-lookup"><span data-stu-id="26e4c-242">If you re-read the entity in that new context, you simulate desktop processing.</span></span>

<span data-ttu-id="26e4c-243">但如果不希望进行额外的读取操作，则必须使用模型绑定器创建的实体对象。</span><span class="sxs-lookup"><span data-stu-id="26e4c-243">But if you don't want to do the extra read operation, you have to use the entity object created by the model binder.</span></span>  <span data-ttu-id="26e4c-244">执行此操作最简单的方法是将实体状态设置为“已修改”，就像在之前所示的替代 HttpPost 编辑代码中完成的一样。</span><span class="sxs-lookup"><span data-stu-id="26e4c-244">The simplest way to do this is to set the entity state to Modified as is done in the alternative HttpPost Edit code shown earlier.</span></span> <span data-ttu-id="26e4c-245">然后调用 `SaveChanges` 时，Entity Framework 会更新数据库行的所有列，因为上下文无法知道已更改的属性。</span><span class="sxs-lookup"><span data-stu-id="26e4c-245">Then when you call `SaveChanges`, the Entity Framework updates all columns of the database row, because the context has no way to know which properties you changed.</span></span>

<span data-ttu-id="26e4c-246">如果想避免读取优先的方法，但还希望 SQL UPDATE 语句只更新用户实际更改的字段，则代码将更复杂。</span><span class="sxs-lookup"><span data-stu-id="26e4c-246">If you want to avoid the read-first approach, but you also want the SQL UPDATE statement to update only the fields that the user actually changed, the code is more complex.</span></span> <span data-ttu-id="26e4c-247">调用 HttpPost `Edit` 方法时，必须以某种方式保存初始值（如使用隐藏字段），以便初始值可用。</span><span class="sxs-lookup"><span data-stu-id="26e4c-247">You have to save the original values in some way (such as by using hidden fields) so that they're available when the HttpPost `Edit` method is called.</span></span> <span data-ttu-id="26e4c-248">然后可以使用初始值创建 Student 实体、调用具有初始实体版本的 `Attach` 方法、将实体的值更新为新值，再调用 `SaveChanges`。</span><span class="sxs-lookup"><span data-stu-id="26e4c-248">Then you can create a Student entity using the original values, call the `Attach` method with that original version of the entity, update the entity's values to the new values, and then call `SaveChanges`.</span></span>

### <a name="test-the-edit-page"></a><span data-ttu-id="26e4c-249">测试编辑页</span><span class="sxs-lookup"><span data-stu-id="26e4c-249">Test the Edit page</span></span>

<span data-ttu-id="26e4c-250">运行应用，选择“学生”选项卡，然后单击“编辑”超链接。</span><span class="sxs-lookup"><span data-stu-id="26e4c-250">Run the app, select the **Students** tab, then click an **Edit** hyperlink.</span></span>

![学生编辑页](crud/_static/student-edit.png)

<span data-ttu-id="26e4c-252">更改某些数据并单击“保存”。</span><span class="sxs-lookup"><span data-stu-id="26e4c-252">Change some of the data and click **Save**.</span></span> <span data-ttu-id="26e4c-253">将打开“索引”页，将看到已更改的数据。</span><span class="sxs-lookup"><span data-stu-id="26e4c-253">The **Index** page opens and you see the changed data.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="26e4c-254">更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-254">Update the Delete page</span></span>

<span data-ttu-id="26e4c-255">在 StudentController.cs 中，HttpGet `Delete` 方法的模板代码使用 `SingleOrDefaultAsync` 方法来检索所选的 Student 实体，如 Details 和 Edit 方法中所示。</span><span class="sxs-lookup"><span data-stu-id="26e4c-255">In *StudentController.cs*, the template code for the HttpGet `Delete` method uses the `SingleOrDefaultAsync` method to retrieve the selected Student entity, as you saw in the Details and Edit methods.</span></span> <span data-ttu-id="26e4c-256">但是，若要在调用 `SaveChanges` 失败时实现自定义错误消息，请将部分功能添加到此方法及其相应的视图中。</span><span class="sxs-lookup"><span data-stu-id="26e4c-256">However, to implement a custom error message when the call to `SaveChanges` fails, you'll add some functionality to this method and its corresponding view.</span></span>

<span data-ttu-id="26e4c-257">正如所看到的更新和创建操作，删除操作需要两个操作方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-257">As you saw for update and create operations, delete operations require two action methods.</span></span> <span data-ttu-id="26e4c-258">为响应 GET 请求而调用的方法将显示一个视图，使用户有机会批准或取消操作。</span><span class="sxs-lookup"><span data-stu-id="26e4c-258">The method that's called in response to a GET request displays a view that gives the user a chance to approve or cancel the delete operation.</span></span> <span data-ttu-id="26e4c-259">如果用户批准，则创建 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="26e4c-259">If the user approves it, a POST request is created.</span></span> <span data-ttu-id="26e4c-260">发生此情况时，将调用 HttpPost `Delete` 方法，然后该方法实际执行删除操作。</span><span class="sxs-lookup"><span data-stu-id="26e4c-260">When that happens, the HttpPost `Delete` method is called and then that method actually performs the delete operation.</span></span>

<span data-ttu-id="26e4c-261">将 try-catch 块添加到 HttpPost `Delete` 方法，以处理更新数据库时可能出现的任何错误。</span><span class="sxs-lookup"><span data-stu-id="26e4c-261">You'll add a try-catch block to the HttpPost `Delete` method to handle any errors that might occur when the database is updated.</span></span> <span data-ttu-id="26e4c-262">如果发生错误，HttpPost Delete 方法会调用 HttpGet Delete 方法，并向其传递一个指示发生错误的参数。</span><span class="sxs-lookup"><span data-stu-id="26e4c-262">If an error occurs, the HttpPost Delete method calls the HttpGet Delete method, passing it a parameter that indicates that an error has occurred.</span></span> <span data-ttu-id="26e4c-263">然后 HttpGet Delete 方法重新显示确认页以及错误消息，向用户提供取消或重试的机会。</span><span class="sxs-lookup"><span data-stu-id="26e4c-263">The HttpGet Delete method then redisplays the confirmation page along with the error message, giving the user an opportunity to cancel or try again.</span></span>

<span data-ttu-id="26e4c-264">使用以下管理错误报告的代码替换 HttpGet `Delete` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-264">Replace the HttpGet `Delete` action method with the following code, which manages error reporting.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

<span data-ttu-id="26e4c-265">此代码接受可选参数，指示保存更改失败后是否调用此方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-265">This code accepts an optional parameter that indicates whether the method was called after a failure to save changes.</span></span> <span data-ttu-id="26e4c-266">没有失败的情况下调用 HttpGet `Delete` 方法时，此参数为 false。</span><span class="sxs-lookup"><span data-stu-id="26e4c-266">This parameter is false when the HttpGet `Delete` method is called without a previous failure.</span></span> <span data-ttu-id="26e4c-267">由 HttpPost `Delete` 方法调用以响应数据库更新错误时，此参数为 true，并且将错误消息传递到视图。</span><span class="sxs-lookup"><span data-stu-id="26e4c-267">When it's called by the HttpPost `Delete` method in response to a database update error, the parameter is true and an error message is passed to the view.</span></span>

### <a name="the-read-first-approach-to-httppost-delete"></a><span data-ttu-id="26e4c-268">HttpPost Delete 的读取优先方法</span><span class="sxs-lookup"><span data-stu-id="26e4c-268">The read-first approach to HttpPost Delete</span></span>

<span data-ttu-id="26e4c-269">使用以下执行实际删除操作并捕获任何数据库更新错误的代码替换 HttpPost `Delete` 操作方法（名为 `DeleteConfirmed`）。</span><span class="sxs-lookup"><span data-stu-id="26e4c-269">Replace the HttpPost `Delete` action method (named `DeleteConfirmed`) with the following code, which performs the actual delete operation and catches any database update errors.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

<span data-ttu-id="26e4c-270">此代码检索所选的实体，然后调用 `Remove` 方法以将实体的状态设置为 `Deleted`。</span><span class="sxs-lookup"><span data-stu-id="26e4c-270">This code retrieves the selected entity, then calls the `Remove` method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="26e4c-271">调用 `SaveChanges` 时生成 SQL DELETE 命令。</span><span class="sxs-lookup"><span data-stu-id="26e4c-271">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span>

### <a name="the-create-and-attach-approach-to-httppost-delete"></a><span data-ttu-id="26e4c-272">HttpPost Delete 的创建和附加方法</span><span class="sxs-lookup"><span data-stu-id="26e4c-272">The create-and-attach approach to HttpPost Delete</span></span>

<span data-ttu-id="26e4c-273">如果在大容量应用程序中提高性能是优先事项，则可以通过只使用主键值实例化 Student 实体，然后将实体状态设置为 `Deleted` 来避免不必要的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="26e4c-273">If improving performance in a high-volume application is a priority, you could avoid an unnecessary SQL query by instantiating a Student entity using only the primary key value and then setting the entity state to `Deleted`.</span></span> <span data-ttu-id="26e4c-274">这是 Entity Framework 删除实体需要执行的所有操作。</span><span class="sxs-lookup"><span data-stu-id="26e4c-274">That's all that the Entity Framework needs in order to delete the entity.</span></span> <span data-ttu-id="26e4c-275">（请勿将此代码放在项目中；这里只是为了说明替代方法。）</span><span class="sxs-lookup"><span data-stu-id="26e4c-275">(Don't put this code in your project; it's here just to illustrate an alternative.)</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

<span data-ttu-id="26e4c-276">如果实体包含还应删除的相关数据，请确保在数据库中配置了级联删除。</span><span class="sxs-lookup"><span data-stu-id="26e4c-276">If the entity has related data that should also be deleted, make sure that cascade delete is configured in the database.</span></span> <span data-ttu-id="26e4c-277">若使用此方法删除实体，EF 可能不知道有需要删除的相关实体。</span><span class="sxs-lookup"><span data-stu-id="26e4c-277">With this approach to entity deletion, EF might not realize there are related entities to be deleted.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="26e4c-278">更新“删除”视图</span><span class="sxs-lookup"><span data-stu-id="26e4c-278">Update the Delete view</span></span>

<span data-ttu-id="26e4c-279">在 Views/Student/Delete.cshtml 中，在 H2 标题和 H3 标题之间添加错误消息，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="26e4c-279">In *Views/Student/Delete.cshtml*, add an error message between the h2 heading and the h3 heading, as shown in the following example:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

<span data-ttu-id="26e4c-280">运行应用，选择“学生”选项卡，并单击“删除”超链接：</span><span class="sxs-lookup"><span data-stu-id="26e4c-280">Run the app, select the **Students** tab, and click a **Delete** hyperlink:</span></span>

![删除确认页](crud/_static/student-delete.png)

<span data-ttu-id="26e4c-282">单击“删除”。</span><span class="sxs-lookup"><span data-stu-id="26e4c-282">Click **Delete**.</span></span> <span data-ttu-id="26e4c-283">将显示不含已删除学生的索引页。</span><span class="sxs-lookup"><span data-stu-id="26e4c-283">The Index page is displayed without the deleted student.</span></span> <span data-ttu-id="26e4c-284">（将看到并发教程中错误处理代码的效果示例。）</span><span class="sxs-lookup"><span data-stu-id="26e4c-284">(You'll see an example of the error handling code in action in the concurrency tutorial.)</span></span>

## <a name="close-database-connections"></a><span data-ttu-id="26e4c-285">关闭数据库连接</span><span class="sxs-lookup"><span data-stu-id="26e4c-285">Close database connections</span></span>

<span data-ttu-id="26e4c-286">若要释放数据库连接包含的资源，完成此操作时必须尽快处理上下文实例。</span><span class="sxs-lookup"><span data-stu-id="26e4c-286">To free up the resources that a database connection holds, the context instance must be disposed as soon as possible when you are done with it.</span></span> <span data-ttu-id="26e4c-287">ASP.NET Core 内置[依赖关系注入](../../fundamentals/dependency-injection.md)会完成此任务。</span><span class="sxs-lookup"><span data-stu-id="26e4c-287">The ASP.NET Core built-in [dependency injection](../../fundamentals/dependency-injection.md) takes care of that task for you.</span></span>

<span data-ttu-id="26e4c-288">在 Startup.cs 中，调用 [AddDbContext 扩展方法](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs)来预配 ASP.NET Core DI 容器中的 `DbContext` 类。</span><span class="sxs-lookup"><span data-stu-id="26e4c-288">In *Startup.cs*, you call the [AddDbContext extension method](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) to provision the `DbContext` class in the ASP.NET Core DI container.</span></span> <span data-ttu-id="26e4c-289">默认情况下，该方法将服务生存期设置为 `Scoped`。</span><span class="sxs-lookup"><span data-stu-id="26e4c-289">That method sets the service lifetime to `Scoped` by default.</span></span> <span data-ttu-id="26e4c-290">`Scoped` 表示上下文对象生存期与 Web 请求生存期一致，并在 Web 请求结束时将自动调用 `Dispose` 方法。</span><span class="sxs-lookup"><span data-stu-id="26e4c-290">`Scoped` means the context object lifetime coincides with the web request life time, and the `Dispose` method will be called automatically at the end of the web request.</span></span>

## <a name="handle-transactions"></a><span data-ttu-id="26e4c-291">处理事务</span><span class="sxs-lookup"><span data-stu-id="26e4c-291">Handle transactions</span></span>

<span data-ttu-id="26e4c-292">默认情况下，Entity Framework 隐式实现事务。</span><span class="sxs-lookup"><span data-stu-id="26e4c-292">By default the Entity Framework implicitly implements transactions.</span></span> <span data-ttu-id="26e4c-293">在对多个行或表进行更改并调用 `SaveChanges` 的情况下，Entity Framework 自动确保所有更改都成功或全部失败。</span><span class="sxs-lookup"><span data-stu-id="26e4c-293">In scenarios where you make changes to multiple rows or tables and then call `SaveChanges`, the Entity Framework automatically makes sure that either all of your changes succeed or they all fail.</span></span> <span data-ttu-id="26e4c-294">如果完成某些更改后发生错误，这些更改会自动回退。</span><span class="sxs-lookup"><span data-stu-id="26e4c-294">If some changes are done first and then an error happens, those changes are automatically rolled back.</span></span> <span data-ttu-id="26e4c-295">如果需要更多控制操作（例如，如果想要在事务中包含在 Entity Framework 外部完成的操作），请参阅[事务](/ef/core/saving/transactions)。</span><span class="sxs-lookup"><span data-stu-id="26e4c-295">For scenarios where you need more control -- for example, if you want to include operations done outside of Entity Framework in a transaction -- see [Transactions](/ef/core/saving/transactions).</span></span>

## <a name="no-tracking-queries"></a><span data-ttu-id="26e4c-296">非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="26e4c-296">No-tracking queries</span></span>

<span data-ttu-id="26e4c-297">当数据库上下文检索表行并创建表示它们的实体对象时，默认情况下，它会跟踪内存中的实体是否与数据库中的内容同步。</span><span class="sxs-lookup"><span data-stu-id="26e4c-297">When a database context retrieves table rows and creates entity objects that represent them, by default it keeps track of whether the entities in memory are in sync with what's in the database.</span></span> <span data-ttu-id="26e4c-298">更新实体时，内存中的数据充当缓存并使用该数据。</span><span class="sxs-lookup"><span data-stu-id="26e4c-298">The data in memory acts as a cache and is used when you update an entity.</span></span> <span data-ttu-id="26e4c-299">在 Web 应用程序中，此缓存通常是不必要的，因为上下文实例通常生存期较短（创建新的实例并用于处理每个请求），并且通常在再次使用该实体之前处理读取实体的上下文。</span><span class="sxs-lookup"><span data-stu-id="26e4c-299">This caching is often unnecessary in a web application because context instances are typically short-lived (a new one is created and disposed for each request) and the context that reads an entity is typically disposed before that entity is used again.</span></span>

<span data-ttu-id="26e4c-300">可以通过调用 `AsNoTracking` 方法禁用对内存中的实体对象的跟踪。</span><span class="sxs-lookup"><span data-stu-id="26e4c-300">You can disable tracking of entity objects in memory by calling the `AsNoTracking` method.</span></span> <span data-ttu-id="26e4c-301">可能想要执行的典型方案包括以下操作：</span><span class="sxs-lookup"><span data-stu-id="26e4c-301">Typical scenarios in which you might want to do that include the following:</span></span>

* <span data-ttu-id="26e4c-302">在上下文生存期内，不需要更新任何实体，并且不需要 EF [自动加载具有由单独的查询检索的实体的导航属性](read-related-data.md)。</span><span class="sxs-lookup"><span data-stu-id="26e4c-302">During the context lifetime you don't need to update any entities, and you don't need EF to [automatically load navigation properties with  entities retrieved by separate queries](read-related-data.md).</span></span> <span data-ttu-id="26e4c-303">在控制器的 HttpGet 操作方法中经常遇到这些情况。</span><span class="sxs-lookup"><span data-stu-id="26e4c-303">Frequently these conditions are met in a controller's HttpGet action methods.</span></span>

* <span data-ttu-id="26e4c-304">正在运行检索大量数据的查询，将只更新一小部分返回的数据。</span><span class="sxs-lookup"><span data-stu-id="26e4c-304">You are running a query that retrieves a large volume of data, and only a small portion of the returned data will be updated.</span></span> <span data-ttu-id="26e4c-305">关闭对大型查询的跟踪可能更有效，稍后为少数需要更新的实体运行查询。</span><span class="sxs-lookup"><span data-stu-id="26e4c-305">It may be more efficient to turn off tracking for the large query, and run a query later for the few entities that need to be updated.</span></span>

* <span data-ttu-id="26e4c-306">想要附加一个实体来更新它，但之前为了其他目的，已检索了相同的实体。</span><span class="sxs-lookup"><span data-stu-id="26e4c-306">You want to attach an entity in order to update it, but earlier you retrieved the same entity for a different purpose.</span></span> <span data-ttu-id="26e4c-307">由于数据库上下文已跟踪了该实体，因此无法附加要更改的实体。</span><span class="sxs-lookup"><span data-stu-id="26e4c-307">Because the entity is already being tracked by the database context, you can't attach the entity that you want to change.</span></span> <span data-ttu-id="26e4c-308">处理这种情况的一种方法是在早前的查询上调用 `AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="26e4c-308">One way to handle this situation is to call `AsNoTracking` on the earlier query.</span></span>

<span data-ttu-id="26e4c-309">有关详细信息，请参阅[跟踪与非跟踪](/ef/core/querying/tracking)。</span><span class="sxs-lookup"><span data-stu-id="26e4c-309">For more information, see [Tracking vs. No-Tracking](/ef/core/querying/tracking).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="26e4c-310">获取代码</span><span class="sxs-lookup"><span data-stu-id="26e4c-310">Get the code</span></span>

[<span data-ttu-id="26e4c-311">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="26e4c-311">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="26e4c-312">后续步骤</span><span class="sxs-lookup"><span data-stu-id="26e4c-312">Next steps</span></span>

<span data-ttu-id="26e4c-313">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="26e4c-313">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="26e4c-314">已自定义“详细信息”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-314">Customized the Details page</span></span>
> * <span data-ttu-id="26e4c-315">已更新“创建”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-315">Updated the Create page</span></span>
> * <span data-ttu-id="26e4c-316">已更新“编辑”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-316">Updated the Edit page</span></span>
> * <span data-ttu-id="26e4c-317">已更新“删除”页</span><span class="sxs-lookup"><span data-stu-id="26e4c-317">Updated the Delete page</span></span>
> * <span data-ttu-id="26e4c-318">已关闭数据库连接</span><span class="sxs-lookup"><span data-stu-id="26e4c-318">Closed database connections</span></span>

<span data-ttu-id="26e4c-319">请继续阅读下一篇教程，了解如何通过添加排序、筛选和分页来扩展“索引”页的功能。</span><span class="sxs-lookup"><span data-stu-id="26e4c-319">Advance to the next tutorial to learn how to expand the functionality of the **Index** page by adding sorting, filtering, and paging.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="26e4c-320">下一篇：排序、筛选和分页</span><span class="sxs-lookup"><span data-stu-id="26e4c-320">Next: Sorting, filtering, and paging</span></span>](sort-filter-page.md)
