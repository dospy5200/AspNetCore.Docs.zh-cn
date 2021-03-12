---
title: ASP.NET Core 和 Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 及更高版本适用于 ASP.NET Core 3.1 及更高版本。
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 44211ac7fa2acc7a7a9471ef362cff02f94fa2b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588264"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="e7372-103">ASP.NET Core 和 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="e7372-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e7372-104">作者：[Patrick Goode](https://github.com/attrib75)</span><span class="sxs-lookup"><span data-stu-id="e7372-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="e7372-105">将 Entity Framework 6 与 ASP.NET Core 结合使用</span><span class="sxs-lookup"><span data-stu-id="e7372-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="e7372-106">应在新开发中使用 [Entity Framework Core](/ef/)。</span><span class="sxs-lookup"><span data-stu-id="e7372-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="e7372-107">[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample)使用 [Entity Framework 6 (EF6)](/ef/ef6)，其可用于将现有应用迁移到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="e7372-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7372-108">其他资源</span><span class="sxs-lookup"><span data-stu-id="e7372-108">Additional resources</span></span>

* [<span data-ttu-id="e7372-109">Entity Framework - 基于代码的配置</span><span class="sxs-lookup"><span data-stu-id="e7372-109">Entity Framework - Code-Based Configuration</span></span>](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e7372-110">作者：[Paweł Grudzień](https://github.com/pgrudzien12)、[Damien Pontifex](https://github.com/DamienPontifex) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="e7372-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="e7372-111">本文演示如何在 ASP.NET Core 应用程序中使用 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="e7372-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="e7372-112">概述</span><span class="sxs-lookup"><span data-stu-id="e7372-112">Overview</span></span> 

<span data-ttu-id="e7372-113">若要使用 Entity Framework 6，则项目必须面向 .NET Framework 进行编译，因为 Entity Framework 6 不支持 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="e7372-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="e7372-114">如果需要跨平台功能，需升级到 [Entity Framework Core](/ef/)。</span><span class="sxs-lookup"><span data-stu-id="e7372-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="e7372-115">在 ASP.NET Core 应用程序中使用 Entity Framework 6 的推荐方法是：将 EF6 上下文和模型类放入面向 .NET Framework 的类库项目中。</span><span class="sxs-lookup"><span data-stu-id="e7372-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="e7372-116">添加对 ASP.NET Core 项目中的类库的引用。</span><span class="sxs-lookup"><span data-stu-id="e7372-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="e7372-117">请参阅示例[针对 EF6 和 ASP.NET Core 项目的 Visual Studio 解决方案](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/)。</span><span class="sxs-lookup"><span data-stu-id="e7372-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/).</span></span>    

<span data-ttu-id="e7372-118">不能将 EF6 上下文放入 ASP.NET Core 项目，因为 .NET Core 项目不支持 EF6 命令（如 Enable-Migrations）所需的的各项功能。</span><span class="sxs-lookup"><span data-stu-id="e7372-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="e7372-119">无论 EF6 上下文属于哪种项目类型，只有 EF6 命令行工具才能使用 EF6 上下文。</span><span class="sxs-lookup"><span data-stu-id="e7372-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="e7372-120">例如，`Scaffold-DbContext` 仅在 Entity Framework Core 中可用。</span><span class="sxs-lookup"><span data-stu-id="e7372-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="e7372-121">如果需要对数据库执行反向工程以使其成为 EF6 模型，请参阅<https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>。</span><span class="sxs-lookup"><span data-stu-id="e7372-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="e7372-122">在 ASP.NET Core 项目中引用完整框架和 EF6</span><span class="sxs-lookup"><span data-stu-id="e7372-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="e7372-123">ASP.NET Core 项目需要面向 .NET Framework 和引用 EF6。</span><span class="sxs-lookup"><span data-stu-id="e7372-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="e7372-124">例如，ASP.NET Core 项目的 .csproj 文件将与以下示例类似（仅显示该文件的相关部分）。</span><span class="sxs-lookup"><span data-stu-id="e7372-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="e7372-125">创建新项目时，请使用 ASP.NET Core Web 应用程序（.NET Framework）模板。</span><span class="sxs-lookup"><span data-stu-id="e7372-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="e7372-126">处理连接字符串</span><span class="sxs-lookup"><span data-stu-id="e7372-126">Handle connection strings</span></span>    

<span data-ttu-id="e7372-127">需通过默认构造函数在 EF6 类库项目中使用 EF6 命令行工具，以便它们能够实例化上下文。</span><span class="sxs-lookup"><span data-stu-id="e7372-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="e7372-128">但是，如果想指定要在 ASP.NET Core 项目中使用的连接字符串，则上下文构造函数必须具有可允许你在连接字符串中进行传递的参数。</span><span class="sxs-lookup"><span data-stu-id="e7372-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="e7372-129">示例如下。</span><span class="sxs-lookup"><span data-stu-id="e7372-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="e7372-130">由于 EF6 上下文不具有无参数构造函数，因此 EF6 项目必须提供 <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> 的实现。</span><span class="sxs-lookup"><span data-stu-id="e7372-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="e7372-131">EF6 命令行工具将查找和使用该实现，以便它们能够实例化上下文。</span><span class="sxs-lookup"><span data-stu-id="e7372-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="e7372-132">示例如下。</span><span class="sxs-lookup"><span data-stu-id="e7372-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="e7372-133">在此示例代码中，`IDbContextFactory` 实现将在硬编码的连接字符串中传递。</span><span class="sxs-lookup"><span data-stu-id="e7372-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="e7372-134">这是命令行工具要使用的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e7372-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="e7372-135">你需要实施策略以确保类库与调用应用程序使用相同的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e7372-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="e7372-136">例如，可从这两个项目的环境变量中获取值。</span><span class="sxs-lookup"><span data-stu-id="e7372-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="e7372-137">在 ASP.NET Core 项目中设置依赖项注入</span><span class="sxs-lookup"><span data-stu-id="e7372-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="e7372-138">在 Core 项目的 Startup.cs 文件中，为 `ConfigureServices` 中的依赖项注入 (DI) 设置 EF6 上下文。</span><span class="sxs-lookup"><span data-stu-id="e7372-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="e7372-139">应将 EF 上下文对象的范围设置为按请求生存期。</span><span class="sxs-lookup"><span data-stu-id="e7372-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="e7372-140">然后即可使用 DI 在控制器中获取上下文的实例。</span><span class="sxs-lookup"><span data-stu-id="e7372-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="e7372-141">此代码与针对 EF Core 上下文编写的代码相似：</span><span class="sxs-lookup"><span data-stu-id="e7372-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="e7372-142">示例应用程序</span><span class="sxs-lookup"><span data-stu-id="e7372-142">Sample application</span></span>   

<span data-ttu-id="e7372-143">若要获取有效的示例应用程序，请参阅本文随附的[示例 Visual Studio 解决方案](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/)。</span><span class="sxs-lookup"><span data-stu-id="e7372-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>    

<span data-ttu-id="e7372-144">可在 Visual Studio 中按照以下步骤从头创建此示例：</span><span class="sxs-lookup"><span data-stu-id="e7372-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="e7372-145">创建解决方案。</span><span class="sxs-lookup"><span data-stu-id="e7372-145">Create a solution.</span></span>    

* <span data-ttu-id="e7372-146">“添加”>“新建项目”>“Web”>“ASP.NET Core Web 应用程序”   </span><span class="sxs-lookup"><span data-stu-id="e7372-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="e7372-147">在“项目模板选择”对话框的下拉列表中，选择 API 和 .NET Framework</span><span class="sxs-lookup"><span data-stu-id="e7372-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="e7372-148">“添加”>“新建项目”>“Windows 桌面”>“类库(.NET Framework)”   </span><span class="sxs-lookup"><span data-stu-id="e7372-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="e7372-149">在两个项目的“包管理器控制台”(PMC) 中运行 `Install-Package Entityframework` 命令。</span><span class="sxs-lookup"><span data-stu-id="e7372-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="e7372-150">在类库项目中，创建数据模型类和上下文类，并创建 `IDbContextFactory` 的实现。</span><span class="sxs-lookup"><span data-stu-id="e7372-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="e7372-151">在类库项目的 PMC 中，运行 `Enable-Migrations` 和 `Add-Migration Initial` 命令。</span><span class="sxs-lookup"><span data-stu-id="e7372-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="e7372-152">如果已将 ASP.NET Core 项目设置为启动项目，请向这些命令添加 `-StartupProjectName EF6`。</span><span class="sxs-lookup"><span data-stu-id="e7372-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="e7372-153">在 Core 项目中，添加对类库项目的项目引用。</span><span class="sxs-lookup"><span data-stu-id="e7372-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="e7372-154">在 Core 项目的 Startup.cs 中，为 DI 注册上下文。</span><span class="sxs-lookup"><span data-stu-id="e7372-154">In the Core project, in *Startup.cs*, register the context for DI.</span></span>    

* <span data-ttu-id="e7372-155">在 Core 项目的 *appsettings.json* 中，添加连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e7372-155">In the Core project, in *appsettings.json*, add the connection string.</span></span>  

* <span data-ttu-id="e7372-156">在 Core 项目中，添加控制器和视图以验证可读取和写入数据。</span><span class="sxs-lookup"><span data-stu-id="e7372-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="e7372-157">（请注意，ASP.NET Core MVC 基架不会使用从类库引用的 EF6 上下文。）</span><span class="sxs-lookup"><span data-stu-id="e7372-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
