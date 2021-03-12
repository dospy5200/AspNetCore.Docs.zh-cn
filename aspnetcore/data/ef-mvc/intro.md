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
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="894c2-103">教程：在 ASP.NET MVC Web 应用中使用 EF Core 入门</span><span class="sxs-lookup"><span data-stu-id="894c2-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="894c2-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="894c2-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="894c2-105">Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 和 Visual Studio 创建 ASP.NET Core MVC Web 应用。</span><span class="sxs-lookup"><span data-stu-id="894c2-105">The Contoso University sample web app demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="894c2-106">该示例应用是一个虚构的 Contoso University 的网站。</span><span class="sxs-lookup"><span data-stu-id="894c2-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="894c2-107">其中包括学生录取、课程创建和讲师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="894c2-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="894c2-108">这是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。</span><span class="sxs-lookup"><span data-stu-id="894c2-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="894c2-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="894c2-109">Prerequisites</span></span>

* <span data-ttu-id="894c2-110">如果不熟悉 ASP.NET Core MVC，则在开始前，请浏览 [ASP.NET Core MVC 入门](xref:tutorials/first-mvc-app/start-mvc)教程。</span><span class="sxs-lookup"><span data-stu-id="894c2-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="894c2-111">数据库引擎</span><span class="sxs-lookup"><span data-stu-id="894c2-111">Database engines</span></span>

<span data-ttu-id="894c2-112">Visual Studio 指令使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是只在 Windows 上运行的一种 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="894c2-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="894c2-113">解决问题和进行故障排除</span><span class="sxs-lookup"><span data-stu-id="894c2-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="894c2-114">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="894c2-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="894c2-115">常见错误以及对应的解决方案，请参阅 [最新教程中的故障排除](advanced.md#common-errors)。</span><span class="sxs-lookup"><span data-stu-id="894c2-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="894c2-116">如果没有找到遇到的问题的解决方案，可以将问题发布到StackOverflow.com 的 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 版块。</span><span class="sxs-lookup"><span data-stu-id="894c2-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="894c2-117">这是一系列一共有十个教程，其中每个都是在前面教程已完成的基础上继续。</span><span class="sxs-lookup"><span data-stu-id="894c2-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="894c2-118">请考虑在完成每一个教程后保存项目的副本。</span><span class="sxs-lookup"><span data-stu-id="894c2-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="894c2-119">之后如果遇到问题，你可以从保存的副本中开始寻找问题，而不是从头开始。</span><span class="sxs-lookup"><span data-stu-id="894c2-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="894c2-120">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="894c2-120">Contoso University web app</span></span>

<span data-ttu-id="894c2-121">这些教程中所构建的应用是一个基本的大学网站。</span><span class="sxs-lookup"><span data-stu-id="894c2-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="894c2-122">用户可以查看和更新学生、课程和讲师信息。</span><span class="sxs-lookup"><span data-stu-id="894c2-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="894c2-123">以下是该应用中的几个屏幕：</span><span class="sxs-lookup"><span data-stu-id="894c2-123">Here are a few of the screens in the app:</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="894c2-126">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="894c2-126">Create web app</span></span>

1. <span data-ttu-id="894c2-127">启动 Visual Studio 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="894c2-127">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="894c2-128">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="894c2-128">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="894c2-129">在“配置新项目”对话框中，为“项目名称”输入 `ContosoUniversity`。</span><span class="sxs-lookup"><span data-stu-id="894c2-129">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="894c2-130">请务必使用此名称（含大写），确保在复制代码时与每个 `namespace` 都相匹配。</span><span class="sxs-lookup"><span data-stu-id="894c2-130">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="894c2-131">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="894c2-131">Select **Create**.</span></span>
1. <span data-ttu-id="894c2-132">在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：</span><span class="sxs-lookup"><span data-stu-id="894c2-132">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="894c2-133">下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。</span><span class="sxs-lookup"><span data-stu-id="894c2-133">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="894c2-134">ASP.NET Core Web 应用程序（模型-视图-控制器）。</span><span class="sxs-lookup"><span data-stu-id="894c2-134">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="894c2-135">“创建”
      ![新的 ASP.NET Core 项目对话框](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="894c2-135">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="894c2-136">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="894c2-136">Set up the site style</span></span>

<span data-ttu-id="894c2-137">设置网站菜单、布局和主页时需作少量基本更改。</span><span class="sxs-lookup"><span data-stu-id="894c2-137">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="894c2-138">打开 Views/Shared/_Layout.cshtml 并进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="894c2-138">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="894c2-139">将每次出现的 `ContosoUniversity` 更改为 `Contoso University`。</span><span class="sxs-lookup"><span data-stu-id="894c2-139">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="894c2-140">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="894c2-140">There are three occurrences.</span></span>
* <span data-ttu-id="894c2-141">添加“关于”、“学生”、“课程”“讲师”和“院系”的菜单项，并删除“隐私”菜单项。</span><span class="sxs-lookup"><span data-stu-id="894c2-141">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="894c2-142">前面的更改在以下代码中突出显示：</span><span class="sxs-lookup"><span data-stu-id="894c2-142">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="894c2-143">在“Views/Home/Index.cshtml”中，将文件内容替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="894c2-143">In *Views/Home/Index.cshtml*, replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="894c2-144">按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。</span><span class="sxs-lookup"><span data-stu-id="894c2-144">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="894c2-145">主页将显示与本教程中创建的页面对应的选项卡。</span><span class="sxs-lookup"><span data-stu-id="894c2-145">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Contoso University 主页](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="894c2-147">EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="894c2-147">EF Core NuGet packages</span></span>

<span data-ttu-id="894c2-148">本教程使用 SQL Server，相关驱动包[Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="894c2-148">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="894c2-149">EF SQL Server 包与其依赖项 `Microsoft.EntityFrameworkCore` 和 `Microsoft.EntityFrameworkCore.Relational` 一起提供 EF 的运行时支持。</span><span class="sxs-lookup"><span data-stu-id="894c2-149">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="894c2-150">添加 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包和 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="894c2-150">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="894c2-151">在包管理器控制台 (PMC) 中，输入以下命令来添加 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="894c2-151">In the Package Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="894c2-152">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet 包提供 EF Core 错误页的 ASP.NET Core 中间件。</span><span class="sxs-lookup"><span data-stu-id="894c2-152">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="894c2-153">此中间件有助于检测和诊断 EF Core 迁移错误。</span><span class="sxs-lookup"><span data-stu-id="894c2-153">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="894c2-154">有关其他可用于 EF Core 的数据库提供程序，请参阅[数据库提供程序](/ef/core/providers/)。</span><span class="sxs-lookup"><span data-stu-id="894c2-154">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="894c2-155">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="894c2-155">Create the data model</span></span>

<span data-ttu-id="894c2-156">将为此应用创建以下实体类：</span><span class="sxs-lookup"><span data-stu-id="894c2-156">The following entity classes are created for this app:</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="894c2-158">前面的实体具有以下关系：</span><span class="sxs-lookup"><span data-stu-id="894c2-158">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="894c2-159">`Student` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="894c2-159">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="894c2-160">一名学生可以报名参加任意数量的课程。</span><span class="sxs-lookup"><span data-stu-id="894c2-160">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="894c2-161">`Course` 和 `Enrollment` 实体之间存在一对多关系。</span><span class="sxs-lookup"><span data-stu-id="894c2-161">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="894c2-162">一门课程中可以包含任意数量的学生。</span><span class="sxs-lookup"><span data-stu-id="894c2-162">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="894c2-163">以下部分将为这几个实体中的每一个实体创建一个类。</span><span class="sxs-lookup"><span data-stu-id="894c2-163">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="894c2-164">Student 实体</span><span class="sxs-lookup"><span data-stu-id="894c2-164">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="894c2-166">在 Models 文件夹中，使用以下代码创建 `Student` 类：</span><span class="sxs-lookup"><span data-stu-id="894c2-166">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-167">`ID` 属性是对应于此类的数据库表的主键 (PK) 列。</span><span class="sxs-lookup"><span data-stu-id="894c2-167">The `ID` property is the primary key (**PK**) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="894c2-168">默认情况下，EF 将名为 `ID` 或 `classnameID` 的属性解释为主键。</span><span class="sxs-lookup"><span data-stu-id="894c2-168">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="894c2-169">例如，可以将 PK 命名为 `StudentID` 而不是 `ID`。</span><span class="sxs-lookup"><span data-stu-id="894c2-169">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="894c2-170">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="894c2-170">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="894c2-171">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-171">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="894c2-172">`Student` 实体的 `Enrollments` 属性：</span><span class="sxs-lookup"><span data-stu-id="894c2-172">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="894c2-173">包含与该 `Student` 实体相关的所有 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-173">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="894c2-174">如果数据库中的特定 `Student` 行具有两个相关的 `Enrollment` 行：</span><span class="sxs-lookup"><span data-stu-id="894c2-174">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="894c2-175">`Student` 实体的 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-175">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="894c2-176">`Enrollment` 行在 `StudentID` 外键 (FK) 列中包含学生的 PK 值。</span><span class="sxs-lookup"><span data-stu-id="894c2-176">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key (**FK**) column.</span></span>

<span data-ttu-id="894c2-177">如果导航属性可以包含多个实体：</span><span class="sxs-lookup"><span data-stu-id="894c2-177">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="894c2-178">类型必须为列表，如 `ICollection<T>`、`List<T>` 或 `HashSet<T>`。</span><span class="sxs-lookup"><span data-stu-id="894c2-178">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="894c2-179">可以添加、删除和更新实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-179">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="894c2-180">多对多和一对多导航关系可包含多个实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-180">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="894c2-181">使用 `ICollection<T>` 时，EF 会默认创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="894c2-181">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="894c2-182">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="894c2-182">The Enrollment entity</span></span>

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="894c2-184">在 Models 文件夹中，使用以下代码创建 `Enrollment` 类：</span><span class="sxs-lookup"><span data-stu-id="894c2-184">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-185">`EnrollmentID` 属性为 PK。</span><span class="sxs-lookup"><span data-stu-id="894c2-185">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="894c2-186">该实体使用 `ID` 模式，而不是本身使用的 `classnameID` 模式。</span><span class="sxs-lookup"><span data-stu-id="894c2-186">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="894c2-187">`Student` 实体使用 `ID` 模式。</span><span class="sxs-lookup"><span data-stu-id="894c2-187">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="894c2-188">某些开发人员更喜欢在整个数据模型中使用一种模式。</span><span class="sxs-lookup"><span data-stu-id="894c2-188">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="894c2-189">在本教程中，这种变化说明了两种模式都可以使用。</span><span class="sxs-lookup"><span data-stu-id="894c2-189">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="894c2-190">[后面的教程](inheritance.md)介绍，使用不包含 classname 的 `ID` 可以更轻松地在数据模型中实现继承。</span><span class="sxs-lookup"><span data-stu-id="894c2-190">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="894c2-191">`Grade` 属性为 `enum`。</span><span class="sxs-lookup"><span data-stu-id="894c2-191">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="894c2-192">`Grade` 类型声明后的 `?` 表示 `Grade` 属性可以为 [null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)。</span><span class="sxs-lookup"><span data-stu-id="894c2-192">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="894c2-193">`null` 级别与零级别不同。</span><span class="sxs-lookup"><span data-stu-id="894c2-193">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="894c2-194">`null` 表示级别未知或尚未分配。</span><span class="sxs-lookup"><span data-stu-id="894c2-194">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="894c2-195">`StudentID` 属性是外键 (FK)，其对应的导航属性为 `Student`。</span><span class="sxs-lookup"><span data-stu-id="894c2-195">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="894c2-196">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只能包含一个 `Student` 实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-196">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="894c2-197">这与 `Student.Enrollments` 导航属性不同，后者可包含多个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-197">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="894c2-198">`CourseID` 属性是 FK，其对应的导航属性为 `Course`。</span><span class="sxs-lookup"><span data-stu-id="894c2-198">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="894c2-199">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="894c2-199">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="894c2-200">如果将属性命名为`<`导航属性名称`><`主键属性名称`>`，则 Entity Framework 将该属性解释为 FK 属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-200">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="894c2-201">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的 PK 为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="894c2-201">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="894c2-202">还可以将 FK 属性命名为 `<`主键属性名称`>`。</span><span class="sxs-lookup"><span data-stu-id="894c2-202">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="894c2-203">例如 `CourseID`，因为 `Course` 实体的 PK 为 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="894c2-203">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="894c2-204">Course 实体</span><span class="sxs-lookup"><span data-stu-id="894c2-204">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="894c2-206">在 Models 文件夹中，使用以下代码创建 `Course` 类：</span><span class="sxs-lookup"><span data-stu-id="894c2-206">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-207">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-207">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="894c2-208">`Course` 实体可与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="894c2-208">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="894c2-209">[DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) 属性将在[后面的教程部分](complex-data-model.md)中介绍。</span><span class="sxs-lookup"><span data-stu-id="894c2-209">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="894c2-210">该属性允许为课程输入 PK，而不是让数据库生成 PK。</span><span class="sxs-lookup"><span data-stu-id="894c2-210">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="894c2-211">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="894c2-211">Create the database context</span></span>

<span data-ttu-id="894c2-212"><xref:Microsoft.EntityFrameworkCore.DbContext> 数据库上下文类是为给定数据模型协调 EF 功能的主类。</span><span class="sxs-lookup"><span data-stu-id="894c2-212">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="894c2-213">此类由 `Microsoft.EntityFrameworkCore.DbContext` 类派生而来。</span><span class="sxs-lookup"><span data-stu-id="894c2-213">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="894c2-214">`DbContext` 派生类指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-214">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="894c2-215">某些 EF 行为可自定义。</span><span class="sxs-lookup"><span data-stu-id="894c2-215">Some EF behaviors can be customized.</span></span> <span data-ttu-id="894c2-216">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="894c2-216">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="894c2-217">在项目文件夹中，创建名为的文件夹 `Data`。</span><span class="sxs-lookup"><span data-stu-id="894c2-217">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="894c2-218">在 Data 文件夹中，使用以下代码创建 `SchoolContext` 类：</span><span class="sxs-lookup"><span data-stu-id="894c2-218">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-219">前面的代码为每个实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-219">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="894c2-220">在 EF 术语中：</span><span class="sxs-lookup"><span data-stu-id="894c2-220">In EF terminology:</span></span>

* <span data-ttu-id="894c2-221">实体集通常对应数据库表。</span><span class="sxs-lookup"><span data-stu-id="894c2-221">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="894c2-222">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="894c2-222">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="894c2-223">可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>` 语句，实现的功能没有任何改变。</span><span class="sxs-lookup"><span data-stu-id="894c2-223">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="894c2-224">EF 将隐式包含它们，原因如下：</span><span class="sxs-lookup"><span data-stu-id="894c2-224">EF would include them implicitly because:</span></span>

* <span data-ttu-id="894c2-225">`Student` 实体引用 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-225">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="894c2-226">`Enrollment` 实体引用 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-226">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="894c2-227">当数据库创建完成后， EF 创建一系列数据表，表名默认和 `DbSet` 属性名相同。</span><span class="sxs-lookup"><span data-stu-id="894c2-227">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="894c2-228">集合的属性名通常采用复数形式。</span><span class="sxs-lookup"><span data-stu-id="894c2-228">Property names for collections are typically plural.</span></span> <span data-ttu-id="894c2-229">例如，使用 `Students`，而不使用 `Student`。</span><span class="sxs-lookup"><span data-stu-id="894c2-229">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="894c2-230">开发者对表名称是否应为复数意见不一。</span><span class="sxs-lookup"><span data-stu-id="894c2-230">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="894c2-231">在这些教程中，在 `DbContext` 中指定单数形式的表名称会覆盖默认行为。</span><span class="sxs-lookup"><span data-stu-id="894c2-231">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="894c2-232">此教程在最后一个 DbSet 属性之后，添加以下高亮显示的代码。</span><span class="sxs-lookup"><span data-stu-id="894c2-232">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="894c2-233">注册 `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="894c2-233">Register the `SchoolContext`</span></span>

<span data-ttu-id="894c2-234">ASP.NET Core 包含[依赖关系注入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="894c2-234">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="894c2-235">在应用启动过程中通过依赖项注入来注册相关服务，例如 EF 数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="894c2-235">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="894c2-236">需要这些服务的组件（如 MVC 控制器）可以通过构造函数参数来获得这些服务。</span><span class="sxs-lookup"><span data-stu-id="894c2-236">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="894c2-237">本教程后面部分将展示获取上下文实例的控制器构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="894c2-237">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="894c2-238">若要将 `SchoolContext` 注册为一种服务，打开 *Startup.cs* ，并将高亮代码添加到 `ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="894c2-238">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="894c2-239">通过调用 `DbContextOptionsBuilder` 中的一个方法将数据库连接字符串在配置文件中的名称传递给上下文对象。</span><span class="sxs-lookup"><span data-stu-id="894c2-239">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="894c2-240">进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="894c2-240">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="894c2-241">打开 *appsettings.json* 文件，并按以下标记所示添加连接字符串：</span><span class="sxs-lookup"><span data-stu-id="894c2-241">Open the *appsettings.json* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="894c2-242">添加数据库异常筛选器</span><span class="sxs-lookup"><span data-stu-id="894c2-242">Add the database exception filter</span></span>

<span data-ttu-id="894c2-243">将 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> 添加到 `ConfigureServices`，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="894c2-243">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=6)]

<span data-ttu-id="894c2-244">`AddDatabaseDeveloperPageExceptionFilter` 在[开发环境](xref:fundamentals/environments)中提供有用的错误信息。</span><span class="sxs-lookup"><span data-stu-id="894c2-244">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="894c2-245">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="894c2-245">SQL Server Express LocalDB</span></span>

<span data-ttu-id="894c2-246">连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="894c2-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="894c2-247">LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。</span><span class="sxs-lookup"><span data-stu-id="894c2-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="894c2-248">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="894c2-248">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="894c2-249">默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="894c2-249">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="894c2-250">使用测试数据初始化数据库</span><span class="sxs-lookup"><span data-stu-id="894c2-250">Initialize DB with test data</span></span>

<span data-ttu-id="894c2-251">EF 将创建空数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-251">EF creates an empty database.</span></span> <span data-ttu-id="894c2-252">在本部分中，你将添加一个方法，该方法将在数据库创建后调用，以向数据库填充测试数据。</span><span class="sxs-lookup"><span data-stu-id="894c2-252">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="894c2-253">`EnsureCreated` 方法用于自动创建数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-253">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="894c2-254">在[后面的教程](migrations.md)中，你将了解如何处理模型更改，方法是使用 Code First 迁移来更改数据库架构，而不是删除和重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-254">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="894c2-255">在 Data 文件夹中，使用以下代码创建一个名为 `DbInitializer` 的新类：</span><span class="sxs-lookup"><span data-stu-id="894c2-255">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="894c2-256">前面的代码检查数据库是否存在：</span><span class="sxs-lookup"><span data-stu-id="894c2-256">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="894c2-257">如果找不到数据库；</span><span class="sxs-lookup"><span data-stu-id="894c2-257">If the database is not found;</span></span>
  * <span data-ttu-id="894c2-258">则创建一个数据库并使用测试数据加载。</span><span class="sxs-lookup"><span data-stu-id="894c2-258">It is created and loaded with test data.</span></span> <span data-ttu-id="894c2-259">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="894c2-259">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="894c2-260">如果能找到数据库，则不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="894c2-260">If the database if found, it takes no action.</span></span>

<span data-ttu-id="894c2-261">使用以下代码更新 Program.cs：</span><span class="sxs-lookup"><span data-stu-id="894c2-261">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="894c2-262">Program.cs 在应用启动时执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="894c2-262">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="894c2-263">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="894c2-263">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="894c2-264">调用 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="894c2-264">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="894c2-265">当 `Initialize` 方法完成时释放上下文，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="894c2-265">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="894c2-266">第一次运行该应用时，会创建数据库并使用测试数据加载该数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-266">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="894c2-267">每当数据模型发生更改时：</span><span class="sxs-lookup"><span data-stu-id="894c2-267">Whenever the data model changes:</span></span>

* <span data-ttu-id="894c2-268">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-268">Delete the database.</span></span>
* <span data-ttu-id="894c2-269">更新 Seed 方法，并使用新数据库重新开始。</span><span class="sxs-lookup"><span data-stu-id="894c2-269">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="894c2-270">在后面的教程中，在更改数据模型时会修改数据库，而不会删除和重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-270">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="894c2-271">数据模型发生更改时不会丢失任何数据。</span><span class="sxs-lookup"><span data-stu-id="894c2-271">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="894c2-272">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="894c2-272">Create controller and views</span></span>

<span data-ttu-id="894c2-273">使用 Visual Studio 中的基架引擎添加一个 MVC 控制器，以及使用 EF 来查询和保存数据的视图。</span><span class="sxs-lookup"><span data-stu-id="894c2-273">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="894c2-274">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 操作方法和视图的自动创建被称为基架。</span><span class="sxs-lookup"><span data-stu-id="894c2-274">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="894c2-275">在“解决方案资源管理器”中，右键单击 `Controllers` 文件夹，然后选择“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="894c2-275">In **Solution Explorer**, right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="894c2-276">在“添加基架”对话框中：</span><span class="sxs-lookup"><span data-stu-id="894c2-276">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="894c2-277">选择 **视图使用 Entity Framework 的 MVC 控制器**。</span><span class="sxs-lookup"><span data-stu-id="894c2-277">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="894c2-278">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="894c2-278">Click **Add**.</span></span> <span data-ttu-id="894c2-279">随即将显示“使用 Entity Framework 添加包含视图的 MVC 控制器”对话框：![构架 Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="894c2-279">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="894c2-280">在“模型类”中选择“Student”。</span><span class="sxs-lookup"><span data-stu-id="894c2-280">In **Model class**, select **Student**.</span></span>
  * <span data-ttu-id="894c2-281">在“数据上下文类”中选择“SchoolContext” 。</span><span class="sxs-lookup"><span data-stu-id="894c2-281">In **Data context class**, select **SchoolContext**.</span></span>
  * <span data-ttu-id="894c2-282">使用 **StudentsController** 作为默认名称。</span><span class="sxs-lookup"><span data-stu-id="894c2-282">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="894c2-283">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="894c2-283">Click **Add**.</span></span>

<span data-ttu-id="894c2-284">Visual Studio 基架引擎创建 `StudentsController.cs` 文件和一组对应于控制器的视图（`*.cshtml` 文件）。</span><span class="sxs-lookup"><span data-stu-id="894c2-284">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="894c2-285">注意控制器将 `SchoolContext` 作为构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="894c2-285">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="894c2-286">ASP.NET Core 依赖关系注入负责将 `SchoolContext` 实例传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="894c2-286">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="894c2-287">你在 `Startup` 类中对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="894c2-287">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="894c2-288">控制器包含 `Index` 操作方法，用于显示数据库中的所有学生。</span><span class="sxs-lookup"><span data-stu-id="894c2-288">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="894c2-289">该方法从学生实体集中获取学生列表，学生实体集则是通过读取数据库上下文实例中的 `Students` 属性获得：</span><span class="sxs-lookup"><span data-stu-id="894c2-289">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="894c2-290">本教程后面部分将介绍此代码中的异步编程元素。</span><span class="sxs-lookup"><span data-stu-id="894c2-290">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="894c2-291">*Views/Students/Index.cshtml* 视图使用table标签显示此列表：</span><span class="sxs-lookup"><span data-stu-id="894c2-291">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="894c2-292">按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。</span><span class="sxs-lookup"><span data-stu-id="894c2-292">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="894c2-293">单击学生选项卡以查看 `DbInitializer.Initialize` 插入的测试的数据。</span><span class="sxs-lookup"><span data-stu-id="894c2-293">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="894c2-294">你将看到 `Students` 选项卡链接在页的顶部或在单击右上角后的导航图标中，具体显示在哪里取决于浏览器窗口宽度。</span><span class="sxs-lookup"><span data-stu-id="894c2-294">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University 主页宽窄](intro/_static/home-page-narrow.png)

![“学生索引”页](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="894c2-297">查看数据库</span><span class="sxs-lookup"><span data-stu-id="894c2-297">View the database</span></span>

<span data-ttu-id="894c2-298">启动应用时，`DbInitializer.Initialize` 方法会调用 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="894c2-298">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="894c2-299">EF 看到没有数据库：</span><span class="sxs-lookup"><span data-stu-id="894c2-299">EF saw that there was no database:</span></span>

* <span data-ttu-id="894c2-300">因此，它创建了一个数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-300">So it created a database.</span></span>
* <span data-ttu-id="894c2-301">`Initialize` 方法代码用数据填充数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-301">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="894c2-302">在 Visual Studio 中使用“SQL Server 对象资源管理器”(SSOX) 查看数据库：</span><span class="sxs-lookup"><span data-stu-id="894c2-302">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="894c2-303">从 Visual Studio 中的“视图”菜单选择“SQL Server 对象资源管理器”。</span><span class="sxs-lookup"><span data-stu-id="894c2-303">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="894c2-304">在 SSOX 中，选择“(localdb)\MSSQLLocalDB”>“数据库”。</span><span class="sxs-lookup"><span data-stu-id="894c2-304">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="894c2-305">选择 *appsettings.json* 文件中的连接字符串中的数据库名称的实体 `ContosoUniversity1`。</span><span class="sxs-lookup"><span data-stu-id="894c2-305">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="894c2-306">展开“表”节点，查看数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="894c2-306">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX 中的表](intro/_static/ssox-tables.png)

<span data-ttu-id="894c2-308">右键单击“Student”表，然后单击“查看数据”以查看表中的数据。</span><span class="sxs-lookup"><span data-stu-id="894c2-308">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![SSOX 中的 Student 表](intro/_static/ssox-student-table.png)

<span data-ttu-id="894c2-310">`*.mdf` 和 `*.ldf` 数据库文件位于 *C:\Users\\\<username>* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="894c2-310">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="894c2-311">由于 `EnsureCreated` 是在应用启动时运行的初始化方法中调用的，因此可以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="894c2-311">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="894c2-312">对 `Student` 类进行更改。</span><span class="sxs-lookup"><span data-stu-id="894c2-312">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="894c2-313">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-313">Delete the database.</span></span>
* <span data-ttu-id="894c2-314">停止，然后启动应用。</span><span class="sxs-lookup"><span data-stu-id="894c2-314">Stop, then start the app.</span></span> <span data-ttu-id="894c2-315">将自动重新创建数据库以匹配此更改。</span><span class="sxs-lookup"><span data-stu-id="894c2-315">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="894c2-316">例如，如果向 `Student` 类添加 `EmailAddress` 属性，则重新创建的表中会有新的 `EmailAddress` 列。</span><span class="sxs-lookup"><span data-stu-id="894c2-316">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="894c2-317">分类的视图不会显示新的 `EmailAddress` 属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-317">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="894c2-318">约定</span><span class="sxs-lookup"><span data-stu-id="894c2-318">Conventions</span></span>

<span data-ttu-id="894c2-319">因为使用了 EF 使用的约定，为使 EF 创建完整数据库而编写的代码量是最小的：</span><span class="sxs-lookup"><span data-stu-id="894c2-319">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="894c2-320">`DbSet` 类型的属性用作表名。</span><span class="sxs-lookup"><span data-stu-id="894c2-320">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="894c2-321">如果实体未被 `DbSet` 属性引用，实体类名称用作表名称。</span><span class="sxs-lookup"><span data-stu-id="894c2-321">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="894c2-322">使用实体属性名作为列名。</span><span class="sxs-lookup"><span data-stu-id="894c2-322">Entity property names are used for column names.</span></span>
* <span data-ttu-id="894c2-323">命名为 `ID` 或 `classnameID` 的实体属性识别为 PK 属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-323">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="894c2-324">如果将属性命名为`<`导航属性名称`><`PK 属性名称`>`，则该属性解释为 FK 属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-324">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="894c2-325">例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的 PK 为 `ID`。</span><span class="sxs-lookup"><span data-stu-id="894c2-325">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="894c2-326">还可以将 FK 属性命名为 `<`主键属性名称`>`。</span><span class="sxs-lookup"><span data-stu-id="894c2-326">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="894c2-327">例如 `EnrollmentID`，因为 `Enrollment` 实体的 PK 为 `EnrollmentID`。</span><span class="sxs-lookup"><span data-stu-id="894c2-327">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="894c2-328">约定行为可以重写。</span><span class="sxs-lookup"><span data-stu-id="894c2-328">Conventional behavior can be overridden.</span></span> <span data-ttu-id="894c2-329">例如，可以显式指定表名，如本教程中前面部分所示。</span><span class="sxs-lookup"><span data-stu-id="894c2-329">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="894c2-330">列名称和任何属性都可以设置为 PK 或 FK。</span><span class="sxs-lookup"><span data-stu-id="894c2-330">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="894c2-331">异步代码</span><span class="sxs-lookup"><span data-stu-id="894c2-331">Asynchronous code</span></span>

<span data-ttu-id="894c2-332">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="894c2-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="894c2-333">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="894c2-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="894c2-334">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="894c2-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="894c2-335">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="894c2-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="894c2-336">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="894c2-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="894c2-337">因此，异步代码使得服务器更有效地使用资源，并且该服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="894c2-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="894c2-338">异步代码在运行时，会引入的少量开销，在低流量时对性能的影响可以忽略不计，但在针对高流量情况下潜在的性能提升是可观的。</span><span class="sxs-lookup"><span data-stu-id="894c2-338">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="894c2-339">在下面的代码中，`async`、`Task<T>`、`await` 和 `ToListAsync` 使代码以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="894c2-339">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="894c2-340">`async` 关键字用于告知编译器该方法主体将生成回调并自动创建 `Task<IActionResult>` 返回对象。</span><span class="sxs-lookup"><span data-stu-id="894c2-340">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="894c2-341">返回类型 `Task<IActionResult>` 表示正在进行的工作返回的结果为 `IActionResult` 类型。</span><span class="sxs-lookup"><span data-stu-id="894c2-341">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="894c2-342">`await` 关键字会使得编译器将方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="894c2-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="894c2-343">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="894c2-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="894c2-344">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="894c2-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="894c2-345">`ToListAsync` 是 `ToList` 扩展方法的异步版本。</span><span class="sxs-lookup"><span data-stu-id="894c2-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="894c2-346">编写使用 EF 的异步代码时需要注意的一些事项：</span><span class="sxs-lookup"><span data-stu-id="894c2-346">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="894c2-347">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="894c2-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="894c2-348">包括 `ToListAsync`， `SingleOrDefaultAsync`，和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="894c2-348">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="894c2-349">不包括只需更改 `IQueryable` 的语句，如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="894c2-349">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="894c2-350">EF 上下文是线程不安全的： 请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="894c2-350">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="894c2-351">当调用异步 EF 方法时，始终使用 `await` 关键字。</span><span class="sxs-lookup"><span data-stu-id="894c2-351">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="894c2-352">若要利用异步代码的性能优势，请确保所使用的任何库包在它们调用导致发送至数据库的查询的任何 EF 方法时也使用异步。</span><span class="sxs-lookup"><span data-stu-id="894c2-352">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="894c2-353">有关在 .NET 异步编程的详细信息，请参阅 [异步概述](/dotnet/articles/standard/async)。</span><span class="sxs-lookup"><span data-stu-id="894c2-353">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="894c2-354">限制提取的实体数</span><span class="sxs-lookup"><span data-stu-id="894c2-354">Limit entities fetched</span></span>

<span data-ttu-id="894c2-355">有关限制从查询返回的实体数的信息，请参阅[性能注意事项](xref:data/ef-rp/intro)。</span><span class="sxs-lookup"><span data-stu-id="894c2-355">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="894c2-356">请继续阅读下一篇教程，了解如何执行基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="894c2-356">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="894c2-357">实现基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="894c2-357">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="894c2-358">Contoso University 示例 Web 应用程序演示如何使用 Entity Framework (EF) Core 2.2 和 Visual Studio 2017 或 2019 创建 ASP.NET Core 2.2 MVC Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="894c2-358">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="894c2-359">本教程没有针对 ASP.NET Core 3.1 进行更新。</span><span class="sxs-lookup"><span data-stu-id="894c2-359">This tutorial has not been updated for ASP.NET Core 3.1.</span></span> <span data-ttu-id="894c2-360">它已针对 [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0) 进行了更新。</span><span class="sxs-lookup"><span data-stu-id="894c2-360">It has been updated for [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).</span></span>

<span data-ttu-id="894c2-361">示例应用程序供一个虚构的 Contoso 大学网站使用。</span><span class="sxs-lookup"><span data-stu-id="894c2-361">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="894c2-362">它包括诸如学生入学、 课程创建和导师分配等功能。</span><span class="sxs-lookup"><span data-stu-id="894c2-362">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="894c2-363">这是一系列教程中的第一个，这一系列教程主要展示了如何从零开始构建 Contoso 大学示例应用程序。</span><span class="sxs-lookup"><span data-stu-id="894c2-363">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="894c2-364">先决条件</span><span class="sxs-lookup"><span data-stu-id="894c2-364">Prerequisites</span></span>

* [<span data-ttu-id="894c2-365">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="894c2-365">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="894c2-366">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 包含以下工作负荷：</span><span class="sxs-lookup"><span data-stu-id="894c2-366">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="894c2-367">ASP.NET 和 Web 开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="894c2-367">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="894c2-368">.NET Core 跨平台开发工作负荷</span><span class="sxs-lookup"><span data-stu-id="894c2-368">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="894c2-369">疑难解答</span><span class="sxs-lookup"><span data-stu-id="894c2-369">Troubleshooting</span></span>

<span data-ttu-id="894c2-370">如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)对比代码来查找解决方案。</span><span class="sxs-lookup"><span data-stu-id="894c2-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="894c2-371">常见错误以及对应的解决方案，请参阅 [最新教程中的故障排除](advanced.md#common-errors)。</span><span class="sxs-lookup"><span data-stu-id="894c2-371">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="894c2-372">如果没有找到遇到的问题的解决方案，可以将问题发布到StackOverflow.com 的 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 版块。</span><span class="sxs-lookup"><span data-stu-id="894c2-372">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="894c2-373">这是一系列一共有十个教程，其中每个都是在前面教程已完成的基础上继续。</span><span class="sxs-lookup"><span data-stu-id="894c2-373">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="894c2-374">请考虑在完成每一个教程后保存项目的副本。</span><span class="sxs-lookup"><span data-stu-id="894c2-374">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="894c2-375">之后如果遇到问题，你可以从保存的副本中开始寻找问题，而不是从头开始。</span><span class="sxs-lookup"><span data-stu-id="894c2-375">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="894c2-376">Contoso University Web 应用</span><span class="sxs-lookup"><span data-stu-id="894c2-376">Contoso University web app</span></span>

<span data-ttu-id="894c2-377">你将在这些教程中学习构建一个简单的大学网站的应用程序。</span><span class="sxs-lookup"><span data-stu-id="894c2-377">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="894c2-378">用户可以查看和更新学生、 课程和教师信息。</span><span class="sxs-lookup"><span data-stu-id="894c2-378">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="894c2-379">以下是一些你即将创建的页面。</span><span class="sxs-lookup"><span data-stu-id="894c2-379">Here are a few of the screens you'll create.</span></span>

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="894c2-382">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="894c2-382">Create web app</span></span>

* <span data-ttu-id="894c2-383">打开 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="894c2-383">Open Visual Studio.</span></span>

* <span data-ttu-id="894c2-384">从“文件”菜单中选择“新建”>“项目” 。</span><span class="sxs-lookup"><span data-stu-id="894c2-384">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="894c2-385">从左窗格中依次选择“已安装”>“Visual C#”>“Web”。</span><span class="sxs-lookup"><span data-stu-id="894c2-385">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="894c2-386">选择“ASP.NET Core Web 应用程序”项目模板。</span><span class="sxs-lookup"><span data-stu-id="894c2-386">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="894c2-387">输入“ContosoUniversity”作为名称，然后单击“确定” 。</span><span class="sxs-lookup"><span data-stu-id="894c2-387">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![“新建项目”对话框](intro/_static/new-project2.png)

* <span data-ttu-id="894c2-389">等待“新建 ASP.NET Core Web 应用程序”对话框显示出来。</span><span class="sxs-lookup"><span data-stu-id="894c2-389">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="894c2-390">选择“.NET Core”、“ASP.NET Core 2.2”和“Web 应用程序(模型-视图-控制器)”模板  。</span><span class="sxs-lookup"><span data-stu-id="894c2-390">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="894c2-391">请确保 **身份验证** 设置为  **不进行身份验证**。</span><span class="sxs-lookup"><span data-stu-id="894c2-391">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="894c2-392">选择“确定”</span><span class="sxs-lookup"><span data-stu-id="894c2-392">Select **OK**</span></span>

  ![新的 ASP.NET Core 项目对话框](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="894c2-394">设置网站样式</span><span class="sxs-lookup"><span data-stu-id="894c2-394">Set up the site style</span></span>

<span data-ttu-id="894c2-395">通过几个简单的更改设置站点菜单、 布局和主页。</span><span class="sxs-lookup"><span data-stu-id="894c2-395">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="894c2-396">打开 Views/Shared/_Layout.cshtml 并进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="894c2-396">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="894c2-397">将文件中的"ContosoUniversity"更改为"Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="894c2-397">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="894c2-398">需要更改三个地方。</span><span class="sxs-lookup"><span data-stu-id="894c2-398">There are three occurrences.</span></span>

* <span data-ttu-id="894c2-399">添加“关于”、“学生”、“课程”“讲师”和“院系”的菜单项，并删除“隐私”菜单项。</span><span class="sxs-lookup"><span data-stu-id="894c2-399">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="894c2-400">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="894c2-400">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="894c2-401">在 *Views/Home/Index.cshtml*，将文件的内容替换为以下代码以将有关 ASP.NET 和 MVC 的内容替换为有关此应用程序的内容：</span><span class="sxs-lookup"><span data-stu-id="894c2-401">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="894c2-402">按 CTRL + F5 来运行该项目或从菜单选择 **调试 > 开始执行(不调试)** 。</span><span class="sxs-lookup"><span data-stu-id="894c2-402">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="894c2-403">你会看到首页，以及通过这个教程创建的页对应的选项卡。</span><span class="sxs-lookup"><span data-stu-id="894c2-403">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University 主页](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="894c2-405">关于 EF Core NuGet 包</span><span class="sxs-lookup"><span data-stu-id="894c2-405">About EF Core NuGet packages</span></span>

<span data-ttu-id="894c2-406">若要为项目添加 EF Core 支持，需要安装相应的数据库驱动包。</span><span class="sxs-lookup"><span data-stu-id="894c2-406">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="894c2-407">本教程使用 SQL Server，相关驱动包[Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="894c2-407">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="894c2-408">此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，因此无需引用该包。</span><span class="sxs-lookup"><span data-stu-id="894c2-408">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="894c2-409">EF SQL Server 包和其依赖项（`Microsoft.EntityFrameworkCore` 和 `Microsoft.EntityFrameworkCore.Relational`）一起提供 EF 的运行时支持。</span><span class="sxs-lookup"><span data-stu-id="894c2-409">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="894c2-410">你将在之后的 [迁移](migrations.md) 教程中学习添加工具包。</span><span class="sxs-lookup"><span data-stu-id="894c2-410">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="894c2-411">有关其他可用于 EF Core 的数据库驱动的信息，请参阅 [数据库驱动](/ef/core/providers/)。</span><span class="sxs-lookup"><span data-stu-id="894c2-411">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="894c2-412">创建数据模型</span><span class="sxs-lookup"><span data-stu-id="894c2-412">Create the data model</span></span>

<span data-ttu-id="894c2-413">接下来你将创建 Contoso 大学应用程序的实体类。</span><span class="sxs-lookup"><span data-stu-id="894c2-413">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="894c2-414">你将从以下三个实体类开始。</span><span class="sxs-lookup"><span data-stu-id="894c2-414">You'll start with the following three entities.</span></span>

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

<span data-ttu-id="894c2-416">`Student` 和 `Enrollment`实体之间是一对多的关系，`Course` 和`Enrollment` 实体之间也是一个对多的关系。</span><span class="sxs-lookup"><span data-stu-id="894c2-416">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="894c2-417">换而言之，一名学生可以修读任意数量的课程, 并且某一课程可以被任意数量的学生修读。</span><span class="sxs-lookup"><span data-stu-id="894c2-417">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="894c2-418">接下来，你将创建与这些实体对应的类。</span><span class="sxs-lookup"><span data-stu-id="894c2-418">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="894c2-419">Student 实体</span><span class="sxs-lookup"><span data-stu-id="894c2-419">The Student entity</span></span>

![Student 实体关系图](intro/_static/student-entity.png)

<span data-ttu-id="894c2-421">在 *Models* 文件夹中，创建一个名为 *Student.cs* 的类文件并且将模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="894c2-421">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-422">`ID` 属性将成为对应于此类的数据库表中的主键。</span><span class="sxs-lookup"><span data-stu-id="894c2-422">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="894c2-423">默认情况下，EF 将会将名为 `ID` 或 `classnameID` 的属性解析为主键。</span><span class="sxs-lookup"><span data-stu-id="894c2-423">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="894c2-424">`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="894c2-424">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="894c2-425">导航属性中包含与此实体相关的其他实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-425">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="894c2-426">在这个案例下，`Student entity` 中的 `Enrollments` 属性会保留所有与 `Student` 实体相关的 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="894c2-426">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="894c2-427">换而言之，如果数据库中的 `Student` 行具有两个相关的 `Enrollment` 行（在其 StudentID 外键列中包含该学生的主键值的行），则该 `Student` 实体的 `Enrollments` 导航属性将包含这两个 `Enrollment` 实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-427">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="894c2-428">如果导航属性可以具有多个实体 （如多对多或一对多关系），那么导航属性的类型必须是可以添加、 删除和更新条目的容器，如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="894c2-428">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="894c2-429">你可以指定 `ICollection<T>` 或实现该接口类型，如 `List<T>` 或 `HashSet<T>`。</span><span class="sxs-lookup"><span data-stu-id="894c2-429">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="894c2-430">如果指定 `ICollection<T>`，EF在默认情况下创建 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="894c2-430">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="894c2-431">Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="894c2-431">The Enrollment entity</span></span>

![修读实体关系图](intro/_static/enrollment-entity.png)

<span data-ttu-id="894c2-433">在 *Models* 文件夹中，创建 *Enrollment.cs* 并且用以下代码替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="894c2-433">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-434">`EnrollmentID` 属性将被设为主键; 此实体使用 `classnameID` 模式而不是如 `Student` 实体那样直接使用 `ID`。</span><span class="sxs-lookup"><span data-stu-id="894c2-434">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="894c2-435">通常情况下，你选择一个主键模式，并在你的数据模型自始至终使用这种模式。</span><span class="sxs-lookup"><span data-stu-id="894c2-435">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="894c2-436">在这里，使用了两种不同的模式只是为了说明你可以使用任一模式来指定主键。</span><span class="sxs-lookup"><span data-stu-id="894c2-436">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="894c2-437">在 [后面的教程](inheritance.md)，你将了解到使用`ID`这种模式可以更轻松地在数据模型之间实现继承。</span><span class="sxs-lookup"><span data-stu-id="894c2-437">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="894c2-438">`Grade` 属性是 `enum`。</span><span class="sxs-lookup"><span data-stu-id="894c2-438">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="894c2-439">`Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。</span><span class="sxs-lookup"><span data-stu-id="894c2-439">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="894c2-440">评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。</span><span class="sxs-lookup"><span data-stu-id="894c2-440">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="894c2-441">`StudentID` 属性是一个外键，`Student` 是与其且对应的导航属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-441">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="894c2-442">`Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含单个 `Student` 实体 (与前面所看到的 `Student.Enrollments` 导航属性不同后，`Student`中可以容纳多个 `Enrollment` 实体)。</span><span class="sxs-lookup"><span data-stu-id="894c2-442">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="894c2-443">`CourseID` 属性是一个外键， `Course` 是与其对应的导航属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-443">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="894c2-444">`Enrollment` 实体与一个 `Course` 实体相关联。</span><span class="sxs-lookup"><span data-stu-id="894c2-444">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="894c2-445">如果一个属性名为 `<navigation property name><primary key property name>`，Entity Framework 就会将这个属性解析为外键属性(例如， `Student` 实体的主键是`ID`， `Student` 是`Enrollment`的导航属性所以`Enrollment`实体中 `StudentID` 会被解析为外键)。</span><span class="sxs-lookup"><span data-stu-id="894c2-445">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="894c2-446">此外还可以将需要解析为外键的属性命名为 `<primary key property name>` (例如，`CourseID` 由于 `Course` 实体的主键所以 `CourseID` 也被解析为外键)。</span><span class="sxs-lookup"><span data-stu-id="894c2-446">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="894c2-447">Course 实体</span><span class="sxs-lookup"><span data-stu-id="894c2-447">The Course entity</span></span>

![Course 实体关系图](intro/_static/course-entity.png)

<span data-ttu-id="894c2-449">在 *Models* 文件夹中，创建 *Course.cs* 并且用以下代码替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="894c2-449">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-450">`Enrollments` 属性是导航属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="894c2-451">一个 `Course` 体可以与任意数量的 `Enrollment` 实体相关。</span><span class="sxs-lookup"><span data-stu-id="894c2-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="894c2-452">我们在本系列 [后面的教程](complex-data-model.md) 中会有更多有关 `DatabaseGenerated` 特性的例子。</span><span class="sxs-lookup"><span data-stu-id="894c2-452">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="894c2-453">简单来说，此特性让你能自行指定主键，而不是让数据库自动指定主键。</span><span class="sxs-lookup"><span data-stu-id="894c2-453">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="894c2-454">创建数据库上下文</span><span class="sxs-lookup"><span data-stu-id="894c2-454">Create the database context</span></span>

<span data-ttu-id="894c2-455">使得给定的数据模型与 Entity Framework 功能相协调的主类是数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="894c2-455">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="894c2-456">可以通过继承 `Microsoft.EntityFrameworkCore.DbContext` 类的方式创建此类。</span><span class="sxs-lookup"><span data-stu-id="894c2-456">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="894c2-457">在该类中你可以指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-457">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="894c2-458">你还可以定义某些 Entity Framework 行为。</span><span class="sxs-lookup"><span data-stu-id="894c2-458">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="894c2-459">在此项目中将数据库上下文类命名为 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="894c2-459">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="894c2-460">在项目文件夹中，创建名为的文件夹 *Data*。</span><span class="sxs-lookup"><span data-stu-id="894c2-460">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="894c2-461">在 *Data* 文件夹创建名为 *SchoolContext.cs* 的类文件，并将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="894c2-461">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-462">此代码将为每个实体集创建 `DbSet` 属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-462">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="894c2-463">在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。</span><span class="sxs-lookup"><span data-stu-id="894c2-463">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="894c2-464">在这里可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>` 语句，实现的功能没有任何改变。</span><span class="sxs-lookup"><span data-stu-id="894c2-464">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="894c2-465">Entity Framework 会隐式包含这两个实体因为 `Student` 实体引用了 `Enrollment` 实体、`Enrollment` 实体引用了 `Course` 实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-465">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="894c2-466">当数据库创建完成后， EF 创建一系列数据表，表名默认和 `DbSet` 属性名相同。</span><span class="sxs-lookup"><span data-stu-id="894c2-466">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="894c2-467">集合属性的名称一般使用复数形式，但不同的开发人员的命名习惯可能不一样，开发人员根据自己的情况确定是否使用复数形式。</span><span class="sxs-lookup"><span data-stu-id="894c2-467">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="894c2-468">在定义 DbSet 属性的代码之后，添加下面高亮代码，对 DbContext 指定单数的表名来覆盖默认的表名。</span><span class="sxs-lookup"><span data-stu-id="894c2-468">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="894c2-469">此教程在最后一个 DbSet 属性之后，添加以下高亮显示的代码。</span><span class="sxs-lookup"><span data-stu-id="894c2-469">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="894c2-470">生成项目以检查编译器错误。</span><span class="sxs-lookup"><span data-stu-id="894c2-470">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="894c2-471">注册 SchoolContext</span><span class="sxs-lookup"><span data-stu-id="894c2-471">Register the SchoolContext</span></span>

<span data-ttu-id="894c2-472">ASP.NET Core 默认实现 [依赖注入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="894c2-472">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="894c2-473">在应用程序启动过程通过依赖注入注册相关服务 （例如 EF 数据库上下文）。</span><span class="sxs-lookup"><span data-stu-id="894c2-473">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="894c2-474">需要这些服务的组件 （如 MVC 控制器） 可以通过向构造函数添加相关参数来获得对应服务。</span><span class="sxs-lookup"><span data-stu-id="894c2-474">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="894c2-475">在本教程后面你将看到控制器构造函数的代码，就是通过上述方式获得上下文实例。</span><span class="sxs-lookup"><span data-stu-id="894c2-475">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="894c2-476">若要将 `SchoolContext` 注册为一种服务，打开 *Startup.cs* ，并将高亮代码添加到 `ConfigureServices` 方法中。</span><span class="sxs-lookup"><span data-stu-id="894c2-476">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="894c2-477">通过调用 `DbContextOptionsBuilder` 中的一个方法将数据库连接字符串在配置文件中的名称传递给上下文对象。</span><span class="sxs-lookup"><span data-stu-id="894c2-477">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="894c2-478">进行本地开发时，[ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *appsettings.json* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="894c2-478">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="894c2-479">添加 `using` 语句引用 `ContosoUniversity.Data` 和 `Microsoft.EntityFrameworkCore` 命名空间，然后生成项目。</span><span class="sxs-lookup"><span data-stu-id="894c2-479">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="894c2-480">打开 *appsettings.json* 文件，并按以下所示添加连接字符串。</span><span class="sxs-lookup"><span data-stu-id="894c2-480">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="894c2-481">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="894c2-481">SQL Server Express LocalDB</span></span>

<span data-ttu-id="894c2-482">数据库连接字符串指定使用 SQL Server LocalDB 数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-482">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="894c2-483">LocalDB 是 SQL Server Express 数据库引擎的轻量级版本，用于应用程序开发，不在生产环境中使用。</span><span class="sxs-lookup"><span data-stu-id="894c2-483">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="894c2-484">LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。</span><span class="sxs-lookup"><span data-stu-id="894c2-484">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="894c2-485">默认情况下， LocalDB 在 `C:/Users/<user>` 目录下创建 *.mdf* 数据库文件。</span><span class="sxs-lookup"><span data-stu-id="894c2-485">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="894c2-486">使用测试数据初始化数据库</span><span class="sxs-lookup"><span data-stu-id="894c2-486">Initialize DB with test data</span></span>

<span data-ttu-id="894c2-487">Entity Framework 已经为你创建了一个空数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-487">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="894c2-488">在本部分中，你将编写一个方法用于向数据库填充测试数据，该方法会在数据库创建完成之后执行。</span><span class="sxs-lookup"><span data-stu-id="894c2-488">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="894c2-489">此处将使用 `EnsureCreated` 方法来自动创建数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-489">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="894c2-490">在 [后面的教程](migrations.md) 你将了解如何通过使用 Code First Migration 来更改而不是删除并重新创建数据库来处理模型更改。</span><span class="sxs-lookup"><span data-stu-id="894c2-490">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="894c2-491">在 *Data* 文件夹中，创建名为的新类文件 *DbInitializer.cs* 并且将模板代码替换为以下代码，使得在需要时能创建数据库并向其填充测试数据。</span><span class="sxs-lookup"><span data-stu-id="894c2-491">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="894c2-492">这段代码首先检查是否有学生数据在数据库中，如果没有的话，就可以假定数据库是新建的，然后使用测试数据进行填充。</span><span class="sxs-lookup"><span data-stu-id="894c2-492">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="894c2-493">代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。</span><span class="sxs-lookup"><span data-stu-id="894c2-493">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="894c2-494">在 *Program.cs*，修改 `Main` 方法，使得在应用程序启动时能执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="894c2-494">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="894c2-495">从依赖注入容器中获取数据库上下文实例。</span><span class="sxs-lookup"><span data-stu-id="894c2-495">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="894c2-496">调用 seed 方法，将上下文传递给它。</span><span class="sxs-lookup"><span data-stu-id="894c2-496">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="894c2-497">Seed 方法完成此操作时释放上下文。</span><span class="sxs-lookup"><span data-stu-id="894c2-497">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="894c2-498">首次运行该应用程序时，将创建数据库并使用测试数据填充该数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-498">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="894c2-499">每次更改数据模型时：</span><span class="sxs-lookup"><span data-stu-id="894c2-499">Whenever you change the data model:</span></span>

 * <span data-ttu-id="894c2-500">删除数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-500">Delete the database.</span></span>
 * <span data-ttu-id="894c2-501">更新 Seed 方法，并以相同的方式用新数据库重新开始。</span><span class="sxs-lookup"><span data-stu-id="894c2-501">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="894c2-502">在之后的教程中，你将了解如何在数据模型更改时，只需修改数据库而无需删除重建数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-502">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="894c2-503">创建控制器和视图</span><span class="sxs-lookup"><span data-stu-id="894c2-503">Create controller and views</span></span>

<span data-ttu-id="894c2-504">在本节，将使用 Visual Studio 中的基架引擎添加一个 MVC 控制器，以及使用 EF 来查询和保存数据的视图。</span><span class="sxs-lookup"><span data-stu-id="894c2-504">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="894c2-505">CRUD 操作方法和视图的自动创建被称为基架。</span><span class="sxs-lookup"><span data-stu-id="894c2-505">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="894c2-506">基架与代码生成不同，基架的代码是一个起点，可以修改基架以满足自己需求，而你通常无需修改生成的代码。</span><span class="sxs-lookup"><span data-stu-id="894c2-506">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="894c2-507">当你需要自定义生成代码时，可使用一部分类或需求发生变化时重新生成代码。</span><span class="sxs-lookup"><span data-stu-id="894c2-507">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="894c2-508">右键单击 **解决方案资源管理器** 中的 **Controllers** 文件夹选择 **添加 > 新搭建基架的项目**。</span><span class="sxs-lookup"><span data-stu-id="894c2-508">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="894c2-509">在“添加基架”对话框中：</span><span class="sxs-lookup"><span data-stu-id="894c2-509">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="894c2-510">选择 **视图使用 Entity Framework 的 MVC 控制器**。</span><span class="sxs-lookup"><span data-stu-id="894c2-510">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="894c2-511">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="894c2-511">Click **Add**.</span></span> <span data-ttu-id="894c2-512">随即将显示“使用 Entity Framework 添加包含视图的 MVC 控制器”对话框：![构架 Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="894c2-512">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="894c2-513">在 **模型类** 选择 **Student**。</span><span class="sxs-lookup"><span data-stu-id="894c2-513">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="894c2-514">在“数据上下文类”中选择 SchoolContext 。</span><span class="sxs-lookup"><span data-stu-id="894c2-514">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="894c2-515">使用 **StudentsController** 作为默认名称。</span><span class="sxs-lookup"><span data-stu-id="894c2-515">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="894c2-516">单击 **添加**。</span><span class="sxs-lookup"><span data-stu-id="894c2-516">Click **Add**.</span></span>

<span data-ttu-id="894c2-517">Visual Studio 基架引擎创建 StudentsController.cs 文件和一组对应于控制器的视图（.cshtml 文件）</span><span class="sxs-lookup"><span data-stu-id="894c2-517">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="894c2-518">注意控制器将 `SchoolContext` 作为构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="894c2-518">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="894c2-519">ASP.NET Core 依赖关系注入负责将 `SchoolContext` 实例传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="894c2-519">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="894c2-520">在 Startup.cs 文件中对其进行了配置。</span><span class="sxs-lookup"><span data-stu-id="894c2-520">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="894c2-521">控制器包含 `Index` 操作方法，用于显示数据库中的所有学生。</span><span class="sxs-lookup"><span data-stu-id="894c2-521">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="894c2-522">该方法从学生实体集中获取学生列表，学生实体集则是通过读取数据库上下文实例中的 `Students` 属性获得：</span><span class="sxs-lookup"><span data-stu-id="894c2-522">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="894c2-523">本教程后面部分将介绍此代码中的异步编程元素。</span><span class="sxs-lookup"><span data-stu-id="894c2-523">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="894c2-524">*Views/Students/Index.cshtml* 视图使用table标签显示此列表：</span><span class="sxs-lookup"><span data-stu-id="894c2-524">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="894c2-525">按 CTRL + F5 来运行该项目，或从菜单选择 **调试 > 开始执行(不调试)** 。</span><span class="sxs-lookup"><span data-stu-id="894c2-525">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="894c2-526">单击学生选项卡以查看 `DbInitializer.Initialize` 插入的测试的数据。</span><span class="sxs-lookup"><span data-stu-id="894c2-526">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="894c2-527">你将看到 `Students` 选项卡链接在页的顶部或在单击右上角后的导航图标中，具体显示在哪里取决于浏览器窗口宽度。</span><span class="sxs-lookup"><span data-stu-id="894c2-527">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University 主页宽窄](intro/_static/home-page-narrow.png)

![“学生索引”页](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="894c2-530">查看数据库</span><span class="sxs-lookup"><span data-stu-id="894c2-530">View the database</span></span>

<span data-ttu-id="894c2-531">当你启动了应用程序，`DbInitializer.Initialize` 方法调用 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="894c2-531">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="894c2-532">EF 没有检测到相关数据库，因此自己创建了一个，接着 `Initialize` 方法的其余代码向数据库中填充数据。</span><span class="sxs-lookup"><span data-stu-id="894c2-532">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="894c2-533">你可以使用 Visual Studio 中的 **SQL Server 对象资源管理器** (SSOX) 查看数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-533">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="894c2-534">关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="894c2-534">Close the browser.</span></span>

<span data-ttu-id="894c2-535">如果 SSOX 窗口尚未打开，请从Visual Studio 中的 **视图** 菜单中选择。</span><span class="sxs-lookup"><span data-stu-id="894c2-535">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="894c2-536">在 SSOX 中，单击“(localdb)\MSSQLLocalDB”>“数据库”，然后单击 *appsettings.json* 文件中的连接字符串中的数据库名称的实体。</span><span class="sxs-lookup"><span data-stu-id="894c2-536">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>

<span data-ttu-id="894c2-537">展开“表”节点，查看数据库中的表。</span><span class="sxs-lookup"><span data-stu-id="894c2-537">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX 中的表](intro/_static/ssox-tables.png)

<span data-ttu-id="894c2-539">右键单击 **Student** 表，然后单击 **查看数据**，即可查看已创建的列和已插入到表的行。</span><span class="sxs-lookup"><span data-stu-id="894c2-539">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX 中的 Student 表](intro/_static/ssox-student-table.png)

<span data-ttu-id="894c2-541">*.mdf* 和 *.ldf* 数据库文件位于 *C:\Users\\\<username>* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="894c2-541">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="894c2-542">因为调用 `EnsureCreated` 的初始化方法在启动应用程序时才运行，所以在这之前你可以更改 `Student` 类、 删除数据库、 再运行一次应用程序，这时候数据库将自动重新创建，以匹配所做的更改。</span><span class="sxs-lookup"><span data-stu-id="894c2-542">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="894c2-543">例如，如果向 `Student` 类添加 `EmailAddress` 属性，重新的创建表中会有 `EmailAddress` 列。</span><span class="sxs-lookup"><span data-stu-id="894c2-543">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="894c2-544">约定</span><span class="sxs-lookup"><span data-stu-id="894c2-544">Conventions</span></span>

<span data-ttu-id="894c2-545">由于 Entity Framework 有一定的约束条件，你只需要按规则编写很少的代码就能够创建一个完整的数据库。</span><span class="sxs-lookup"><span data-stu-id="894c2-545">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="894c2-546">`DbSet` 类型的属性用作表名。</span><span class="sxs-lookup"><span data-stu-id="894c2-546">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="894c2-547">如果实体未被 `DbSet` 属性引用，实体类名称用作表名称。</span><span class="sxs-lookup"><span data-stu-id="894c2-547">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="894c2-548">使用实体属性名作为列名。</span><span class="sxs-lookup"><span data-stu-id="894c2-548">Entity property names are used for column names.</span></span>
* <span data-ttu-id="894c2-549">以 ID 或 classnameID 命名的实体属性被视为主键属性。</span><span class="sxs-lookup"><span data-stu-id="894c2-549">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="894c2-550">如果属性名为 *\<navigation property name>\<primary key property name>* 将被解释为外键属性 (例如，`StudentID` 对应 `Student` 导航属性，`Student` 实体的主键是`ID`，所以`StudentID`被解释为外键属性)。</span><span class="sxs-lookup"><span data-stu-id="894c2-550">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="894c2-551">此外也可以将外键属性命名为 *\<primary key property name>* (例如，`EnrollmentID`，由于 `Enrollment` 实体的主键是 `EnrollmentID`，因此被解释为外键)。</span><span class="sxs-lookup"><span data-stu-id="894c2-551">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="894c2-552">约定行为可以重写。</span><span class="sxs-lookup"><span data-stu-id="894c2-552">Conventional behavior can be overridden.</span></span> <span data-ttu-id="894c2-553">例如，本教程前半部分显式指定表名称。</span><span class="sxs-lookup"><span data-stu-id="894c2-553">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="894c2-554">本系列 [后面教程](complex-data-model.md) 则设置列名称并将任何属性设置为主键或外键。</span><span class="sxs-lookup"><span data-stu-id="894c2-554">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="894c2-555">异步代码</span><span class="sxs-lookup"><span data-stu-id="894c2-555">Asynchronous code</span></span>

<span data-ttu-id="894c2-556">异步编程是 ASP.NET Core 和 EF Core 的默认模式。</span><span class="sxs-lookup"><span data-stu-id="894c2-556">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="894c2-557">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="894c2-557">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="894c2-558">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="894c2-558">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="894c2-559">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="894c2-559">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="894c2-560">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="894c2-560">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="894c2-561">因此，异步代码使得服务器更有效地使用资源，并且该服务器可以无延迟地处理更多流量。</span><span class="sxs-lookup"><span data-stu-id="894c2-561">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="894c2-562">异步代码在运行时，会引入的少量开销，在低流量时对性能的影响可以忽略不计，但在针对高流量情况下潜在的性能提升是可观的。</span><span class="sxs-lookup"><span data-stu-id="894c2-562">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="894c2-563">在以下代码中，`async` 关键字、`Task<T>` 返回值、`await` 关键字和 `ToListAsync` 方法让代码异步执行。</span><span class="sxs-lookup"><span data-stu-id="894c2-563">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="894c2-564">`async` 关键字用于告知编译器该方法主体将生成回调并自动创建 `Task<IActionResult>` 返回对象。</span><span class="sxs-lookup"><span data-stu-id="894c2-564">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="894c2-565">返回类型 `Task<IActionResult>` 表示正在进行的工作返回的结果为 `IActionResult` 类型。</span><span class="sxs-lookup"><span data-stu-id="894c2-565">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="894c2-566">`await` 关键字会使得编译器将方法拆分为两个部分。</span><span class="sxs-lookup"><span data-stu-id="894c2-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="894c2-567">第一部分是以异步方式结束已启动的操作。</span><span class="sxs-lookup"><span data-stu-id="894c2-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="894c2-568">第二部分是当操作完成时注入调用回调方法的地方。</span><span class="sxs-lookup"><span data-stu-id="894c2-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="894c2-569">`ToListAsync` 是 `ToList` 方法的的异步扩展版本。</span><span class="sxs-lookup"><span data-stu-id="894c2-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="894c2-570">使用 Entity Framework 编写异步代码时的一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="894c2-570">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="894c2-571">只有导致查询或发送数据库命令的语句才能以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="894c2-571">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="894c2-572">包括 `ToListAsync`， `SingleOrDefaultAsync`，和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="894c2-572">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="894c2-573">不包括只需更改 `IQueryable` 的语句，如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="894c2-573">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="894c2-574">EF 上下文是线程不安全的： 请勿尝试并行执行多个操作。</span><span class="sxs-lookup"><span data-stu-id="894c2-574">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="894c2-575">当调用异步 EF 方法时，始终使用 `await` 关键字。</span><span class="sxs-lookup"><span data-stu-id="894c2-575">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="894c2-576">如果你想要利用异步代码的性能优势，请确保你所使用的任何库和包在它们调用导致 Entity Framework 数据库查询方法时也使用异步。</span><span class="sxs-lookup"><span data-stu-id="894c2-576">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="894c2-577">有关在 .NET 异步编程的详细信息，请参阅 [异步概述](/dotnet/articles/standard/async)。</span><span class="sxs-lookup"><span data-stu-id="894c2-577">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="894c2-578">后续步骤</span><span class="sxs-lookup"><span data-stu-id="894c2-578">Next steps</span></span>

<span data-ttu-id="894c2-579">请继续阅读下一篇教程，了解如何执行基本的 CRUD（创建、读取、更新、删除）操作。</span><span class="sxs-lookup"><span data-stu-id="894c2-579">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="894c2-580">实现基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="894c2-580">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
