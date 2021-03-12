---
title: Razor与 ASP.NET Core 中的应用程序部件共享控制器、视图和页面等
author: rick-anderson
description: Razor与 ASP.NET Core 中的应用程序部件共享控制器、视图、页面及更多内容
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 23bc1db6a184e7babe87e2d311a8ac4a59e78dd0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588355"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="0041f-103">与应用程序部件共享控制器、视图、 Razor 页和更多内容</span><span class="sxs-lookup"><span data-stu-id="0041f-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0041f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0041f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0041f-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0041f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0041f-106">应用程序部件是对应用资源的抽象化。</span><span class="sxs-lookup"><span data-stu-id="0041f-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="0041f-107">应用程序部件允许 ASP.NET Core 发现控制器、查看组件、标记帮助程序、 Razor 页面、razor 编译源等。</span><span class="sxs-lookup"><span data-stu-id="0041f-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="0041f-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 是应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="0041f-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="0041f-109">`AssemblyPart` 用于封装程序集引用，并公开类型和编译引用。</span><span class="sxs-lookup"><span data-stu-id="0041f-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="0041f-110">[功能提供程序](#fp)使用应用程序部件填充 ASP.NET Core 应用的功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="0041f-111">应用程序部件的主要用例是将应用配置为从程序集中发现（或避免加载）ASP.NET Core 功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="0041f-112">例如，可能需要在多个应用之间共享通用功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="0041f-113">使用应用程序部件，你可以使用多个应用共享包含控制器、视图、 Razor 页面、razor 编译源、标记帮助程序以及更多应用程序 (DLL) 的程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="0041f-114">相对于在多个项目中复制代码，首选共享程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="0041f-115">ASP.NET Core 应用从 <xref:System.Web.WebPages.ApplicationPart> 加载功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="0041f-116"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 类表示受程序集支持的应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="0041f-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="0041f-117">加载 ASP.NET Core 功能</span><span class="sxs-lookup"><span data-stu-id="0041f-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="0041f-118">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 和 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 类发现并加载 ASP.NET Core 功能（控制器、视图组件等）。</span><span class="sxs-lookup"><span data-stu-id="0041f-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="0041f-119"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> 跟踪可用的应用程序部件和功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="0041f-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="0041f-120">在 `Startup.ConfigureServices` 中配置 `ApplicationPartManager`：</span><span class="sxs-lookup"><span data-stu-id="0041f-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="0041f-121">以下代码提供使用 `AssemblyPart` 配置 `ApplicationPartManager` 的可选方法：</span><span class="sxs-lookup"><span data-stu-id="0041f-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="0041f-122">前面的两个代码示例从程序集加载 `SharedController`。</span><span class="sxs-lookup"><span data-stu-id="0041f-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="0041f-123">`SharedController` 未在该应用的项目中。</span><span class="sxs-lookup"><span data-stu-id="0041f-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="0041f-124">请参阅 [WebAppParts 解决方案](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts)示例下载。</span><span class="sxs-lookup"><span data-stu-id="0041f-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="0041f-125">包含视图</span><span class="sxs-lookup"><span data-stu-id="0041f-125">Include views</span></span>

<span data-ttu-id="0041f-126">使用类库[ Razor 将视图](xref:razor-pages/ui-class)包含在程序集中。</span><span class="sxs-lookup"><span data-stu-id="0041f-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="0041f-127">阻止加载资源</span><span class="sxs-lookup"><span data-stu-id="0041f-127">Prevent loading resources</span></span>

<span data-ttu-id="0041f-128">可以使用应用程序部件来避免加载特定程序集或位置中的资源。</span><span class="sxs-lookup"><span data-stu-id="0041f-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="0041f-129">添加或删除 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 集合的成员，将隐藏或提供资源。</span><span class="sxs-lookup"><span data-stu-id="0041f-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="0041f-130">`ApplicationParts` 集合中条目的顺序并不重要。</span><span class="sxs-lookup"><span data-stu-id="0041f-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="0041f-131">在使用 `ApplicationPartManager` 配置容器中的服务之前，对该类进行配置。</span><span class="sxs-lookup"><span data-stu-id="0041f-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="0041f-132">例如，在调用 `AddControllersAsServices` 之前配置 `ApplicationPartManager`。</span><span class="sxs-lookup"><span data-stu-id="0041f-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="0041f-133">在 `ApplicationParts` 集合上调用 `Remove`，将删除资源。</span><span class="sxs-lookup"><span data-stu-id="0041f-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="0041f-134">`ApplicationPartManager` 包括以下内容的部件：</span><span class="sxs-lookup"><span data-stu-id="0041f-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="0041f-135">应用的程序集和依赖程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="0041f-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="0041f-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="0041f-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="0041f-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="0041f-138">功能提供程序</span><span class="sxs-lookup"><span data-stu-id="0041f-138">Feature providers</span></span>

<span data-ttu-id="0041f-139">应用程序功能提供程序用于检查应用程序部件，并为这些部件提供功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="0041f-140">以下 ASP.NET Core 功能有内置功能提供程序：</span><span class="sxs-lookup"><span data-stu-id="0041f-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="0041f-141">`internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="0041f-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="0041f-142">功能提供程序从 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> 继承，其中 `T` 是功能的类型。</span><span class="sxs-lookup"><span data-stu-id="0041f-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="0041f-143">可以为上面列出的任意功能类型实现功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="0041f-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="0041f-144">`ApplicationPartManager.FeatureProviders` 中的功能提供程序的顺序可能影响运行时行为。</span><span class="sxs-lookup"><span data-stu-id="0041f-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="0041f-145">较晚添加的提供程序可能会影响较早添加的提供程序执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0041f-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="0041f-146">显示可用功能</span><span class="sxs-lookup"><span data-stu-id="0041f-146">Display available features</span></span>

<span data-ttu-id="0041f-147">通过[依存关系注入](../../fundamentals/dependency-injection.md)请求 `ApplicationPartManager` 即可以枚举应用的可用功能：</span><span class="sxs-lookup"><span data-stu-id="0041f-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="0041f-148">[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2)使用前面的代码显示应用功能：</span><span class="sxs-lookup"><span data-stu-id="0041f-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="0041f-149">应用程序部件中的发现</span><span class="sxs-lookup"><span data-stu-id="0041f-149">Discovery in application parts</span></span>

<span data-ttu-id="0041f-150">使用应用程序部件进行开发时，会遇到 HTTP 404 错误且并不鲜见。</span><span class="sxs-lookup"><span data-stu-id="0041f-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="0041f-151">发生这些错误的原因通常是由于未满足某项针对应用程序部件发现方式的基本要求。</span><span class="sxs-lookup"><span data-stu-id="0041f-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="0041f-152">如果应用返回 HTTP 404 错误，请验证是否满足以下要求：</span><span class="sxs-lookup"><span data-stu-id="0041f-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="0041f-153">需要将 `applicationName` 设置设置为用于发现的根程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="0041f-154">用于发现的根程序集通常是入口点程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="0041f-155">根程序集需要引用用于发现的部件。</span><span class="sxs-lookup"><span data-stu-id="0041f-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="0041f-156">引用可以是直接的，也可以是可传递的。</span><span class="sxs-lookup"><span data-stu-id="0041f-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="0041f-157">根程序集需要引用 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="0041f-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="0041f-158">该框架的逻辑会将属性标记到用于发现的根程序集中。</span><span class="sxs-lookup"><span data-stu-id="0041f-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0041f-159">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0041f-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0041f-160">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0041f-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0041f-161">应用程序部件是对应用资源的抽象化。</span><span class="sxs-lookup"><span data-stu-id="0041f-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="0041f-162">应用程序部件允许 ASP.NET Core 发现控制器、查看组件、标记帮助程序、 Razor 页面、razor 编译源等。</span><span class="sxs-lookup"><span data-stu-id="0041f-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="0041f-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) 是一种应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="0041f-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="0041f-164">`AssemblyPart` 用于封装程序集引用，并公开类型和编译引用。</span><span class="sxs-lookup"><span data-stu-id="0041f-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="0041f-165">*功能提供程序* 使用应用程序部件填充 ASP.NET Core 应用的功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="0041f-166">应用程序部件的主要用例是将应用配置为从程序集中发现（或避免加载）ASP.NET Core 功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="0041f-167">例如，可能需要在多个应用之间共享通用功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="0041f-168">使用应用程序部件，你可以使用多个应用共享包含控制器、视图、 Razor 页面、razor 编译源、标记帮助程序以及更多应用程序 (DLL) 的程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="0041f-169">相对于在多个项目中复制代码，首选共享程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="0041f-170">ASP.NET Core 应用从 <xref:System.Web.WebPages.ApplicationPart> 加载功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="0041f-171"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 类表示受程序集支持的应用程序部件。</span><span class="sxs-lookup"><span data-stu-id="0041f-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="0041f-172">加载 ASP.NET Core 功能</span><span class="sxs-lookup"><span data-stu-id="0041f-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="0041f-173">使用 `ApplicationPart` 和 `AssemblyPart` 类发现并加载 ASP.NET Core 功能（控制器、视图组件等）。</span><span class="sxs-lookup"><span data-stu-id="0041f-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="0041f-174"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> 跟踪可用的应用程序部件和功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="0041f-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="0041f-175">在 `Startup.ConfigureServices` 中配置 `ApplicationPartManager`：</span><span class="sxs-lookup"><span data-stu-id="0041f-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="0041f-176">以下代码提供使用 `AssemblyPart` 配置 `ApplicationPartManager` 的可选方法：</span><span class="sxs-lookup"><span data-stu-id="0041f-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="0041f-177">前面的两个代码示例从程序集加载 `SharedController`。</span><span class="sxs-lookup"><span data-stu-id="0041f-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="0041f-178">`SharedController` 未在应用程序的项目中。</span><span class="sxs-lookup"><span data-stu-id="0041f-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="0041f-179">请参阅 [WebAppParts 解决方案](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts)示例下载。</span><span class="sxs-lookup"><span data-stu-id="0041f-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="0041f-180">包含视图</span><span class="sxs-lookup"><span data-stu-id="0041f-180">Include views</span></span>

<span data-ttu-id="0041f-181">使用类库[ Razor 将视图](xref:razor-pages/ui-class)包含在程序集中。</span><span class="sxs-lookup"><span data-stu-id="0041f-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="0041f-182">阻止加载资源</span><span class="sxs-lookup"><span data-stu-id="0041f-182">Prevent loading resources</span></span>

<span data-ttu-id="0041f-183">可以使用应用程序部件来避免加载特定程序集或位置中的资源。</span><span class="sxs-lookup"><span data-stu-id="0041f-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="0041f-184">添加或删除 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 集合的成员，将隐藏或提供资源。</span><span class="sxs-lookup"><span data-stu-id="0041f-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="0041f-185">`ApplicationParts` 集合中条目的顺序并不重要。</span><span class="sxs-lookup"><span data-stu-id="0041f-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="0041f-186">在使用 `ApplicationPartManager` 配置容器中的服务之前，对该类进行配置。</span><span class="sxs-lookup"><span data-stu-id="0041f-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="0041f-187">例如，在调用 `AddControllersAsServices` 之前配置 `ApplicationPartManager`。</span><span class="sxs-lookup"><span data-stu-id="0041f-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="0041f-188">在 `ApplicationParts` 集合上调用 `Remove`，将删除资源。</span><span class="sxs-lookup"><span data-stu-id="0041f-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="0041f-189">以下代码使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 删除应用中的 `MyDependentLibrary`：[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="0041f-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="0041f-190">`ApplicationPartManager` 包括以下内容的部件：</span><span class="sxs-lookup"><span data-stu-id="0041f-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="0041f-191">应用的程序集和依赖程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="0041f-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="0041f-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="0041f-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="0041f-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="0041f-194">应用程序功能提供程序</span><span class="sxs-lookup"><span data-stu-id="0041f-194">Application feature providers</span></span>

<span data-ttu-id="0041f-195">应用程序功能提供程序用于检查应用程序部件，并为这些部件提供功能。</span><span class="sxs-lookup"><span data-stu-id="0041f-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="0041f-196">以下 ASP.NET Core 功能有内置功能提供程序：</span><span class="sxs-lookup"><span data-stu-id="0041f-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="0041f-197">Controllers</span><span class="sxs-lookup"><span data-stu-id="0041f-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="0041f-198">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="0041f-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="0041f-199">查看组件</span><span class="sxs-lookup"><span data-stu-id="0041f-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="0041f-200">功能提供程序从 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> 继承，其中 `T` 是功能的类型。</span><span class="sxs-lookup"><span data-stu-id="0041f-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="0041f-201">可以为上面列出的任意功能类型实现功能提供程序。</span><span class="sxs-lookup"><span data-stu-id="0041f-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="0041f-202">`ApplicationPartManager.FeatureProviders` 中的功能提供程序的顺序可能影响运行时行为。</span><span class="sxs-lookup"><span data-stu-id="0041f-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="0041f-203">较晚添加的提供程序可能会影响较早添加的提供程序执行的操作。</span><span class="sxs-lookup"><span data-stu-id="0041f-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="0041f-204">显示可用功能</span><span class="sxs-lookup"><span data-stu-id="0041f-204">Display available features</span></span>

<span data-ttu-id="0041f-205">通过[依存关系注入](../../fundamentals/dependency-injection.md)请求 `ApplicationPartManager` 即可以枚举应用的可用功能：</span><span class="sxs-lookup"><span data-stu-id="0041f-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="0041f-206">[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2)使用前面的代码显示应用功能：</span><span class="sxs-lookup"><span data-stu-id="0041f-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="0041f-207">应用程序部件中的发现</span><span class="sxs-lookup"><span data-stu-id="0041f-207">Discovery in application parts</span></span>

<span data-ttu-id="0041f-208">使用应用程序部件进行开发时，会遇到 HTTP 404 错误且并不鲜见。</span><span class="sxs-lookup"><span data-stu-id="0041f-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="0041f-209">发生这些错误的原因通常是由于未满足某项针对应用程序部件发现方式的基本要求。</span><span class="sxs-lookup"><span data-stu-id="0041f-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="0041f-210">如果应用返回 HTTP 404 错误，请验证是否满足以下要求：</span><span class="sxs-lookup"><span data-stu-id="0041f-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="0041f-211">需要将 `applicationName` 设置设置为用于发现的根程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="0041f-212">用于发现的根程序集通常是入口点程序集。</span><span class="sxs-lookup"><span data-stu-id="0041f-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="0041f-213">根程序集需要引用用于发现的部件。</span><span class="sxs-lookup"><span data-stu-id="0041f-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="0041f-214">引用可以是直接的，也可以是可传递的。</span><span class="sxs-lookup"><span data-stu-id="0041f-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="0041f-215">根程序集需要引用 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="0041f-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="0041f-216">ASP.NET Core 框架具有自定义生成逻辑，会将属性标记到用于发现的根程序集中。</span><span class="sxs-lookup"><span data-stu-id="0041f-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
