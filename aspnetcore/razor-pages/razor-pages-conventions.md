---
title: ASP.NET Core 中的 Razor Pages 路由和应用约定
author: rick-anderson
description: 了解路由和应用模型提供程序约定如何帮助控制页面路由、发现和处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c75e2f1de522f80f4d8e13cf3f60c99cc6b7196a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589408"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a><span data-ttu-id="86c68-103">ASP.NET Core 中的 Razor Pages 路由和应用约定</span><span class="sxs-lookup"><span data-stu-id="86c68-103">Razor Pages route and app conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86c68-104">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="86c68-104">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="86c68-105">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-105">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="86c68-106">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="86c68-106">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="86c68-107">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="86c68-107">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="86c68-108">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="86c68-108">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="86c68-109">有关详细信息，请参阅[路由：保留的路由名称](xref:mvc/controllers/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="86c68-109">For more information, see [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).</span></span>

<span data-ttu-id="86c68-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="86c68-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="86c68-111">方案</span><span class="sxs-lookup"><span data-stu-id="86c68-111">Scenario</span></span> | <span data-ttu-id="86c68-112">示例演示...</span><span class="sxs-lookup"><span data-stu-id="86c68-112">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="86c68-113">模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-113">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="86c68-114">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="86c68-114">Conventions.Add</span></span><ul><li><span data-ttu-id="86c68-115">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-115">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-116">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-116">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-117">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-117">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="86c68-118">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-118">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="86c68-119">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-119">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="86c68-120">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-120">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-121">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-121">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-122">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="86c68-122">AddPageRoute</span></span></li></ul> | <span data-ttu-id="86c68-123">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-123">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="86c68-124">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-124">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="86c68-125">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-125">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-126">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-126">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-127">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="86c68-127">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="86c68-128">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-128">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="86c68-129">Razor Pages 约定是使用在 `Startup.ConfigureServices` 中配置 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> 的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 重载配置的。</span><span class="sxs-lookup"><span data-stu-id="86c68-129">Razor Pages conventions are configured using an <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="86c68-130">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="86c68-130">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a><span data-ttu-id="86c68-131">路由顺序</span><span class="sxs-lookup"><span data-stu-id="86c68-131">Route order</span></span>

<span data-ttu-id="86c68-132">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="86c68-132">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="86c68-133">顺序</span><span class="sxs-lookup"><span data-stu-id="86c68-133">Order</span></span>            | <span data-ttu-id="86c68-134">行为</span><span class="sxs-lookup"><span data-stu-id="86c68-134">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="86c68-135">-1</span><span class="sxs-lookup"><span data-stu-id="86c68-135">-1</span></span>               | <span data-ttu-id="86c68-136">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-136">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="86c68-137">0</span><span class="sxs-lookup"><span data-stu-id="86c68-137">0</span></span>                | <span data-ttu-id="86c68-138">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="86c68-138">Order isn't specified (default value).</span></span> <span data-ttu-id="86c68-139">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="86c68-139">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="86c68-140">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="86c68-140">1, 2, &hellip; n</span></span> | <span data-ttu-id="86c68-141">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="86c68-141">Specifies the route processing order.</span></span> |

<span data-ttu-id="86c68-142">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="86c68-142">Route processing is established by convention:</span></span>

* <span data-ttu-id="86c68-143">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="86c68-143">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="86c68-144">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-144">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="86c68-145">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-145">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="86c68-146">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="86c68-146">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="86c68-147">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-147">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="86c68-148">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="86c68-148">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="86c68-149">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="86c68-149">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="86c68-150">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="86c68-150">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="86c68-151">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="86c68-151">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="86c68-152">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="86c68-152">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="86c68-153">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="86c68-153">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="86c68-154">模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-154">Model conventions</span></span>

<span data-ttu-id="86c68-155">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="86c68-155">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="86c68-156">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-156">Add a route model convention to all pages</span></span>

<span data-ttu-id="86c68-157">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-157">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="86c68-158">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-158">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-159"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="86c68-159">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="86c68-160">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="86c68-160">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="86c68-161">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-161">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="86c68-162">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="86c68-162">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="86c68-163">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-163">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="86c68-164">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-164">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="86c68-165">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-165">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="86c68-166">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-166">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="86c68-167">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-167">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="86c68-168">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-168">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-169">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-169">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-170">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-170">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-171">将 Razor Pages 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 Razor Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="86c68-171">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when Razor Pages is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="86c68-172">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="86c68-172">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="86c68-173">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-173">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="86c68-176">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-176">Add an app model convention to all pages</span></span>

<span data-ttu-id="86c68-177">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-177">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="86c68-178">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="86c68-178">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="86c68-179">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="86c68-179">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="86c68-180">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="86c68-180">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="86c68-181">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="86c68-181">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="86c68-182">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-182">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-183">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-183">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="86c68-184">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-184">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="86c68-186">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-186">Add a handler model convention to all pages</span></span>

<span data-ttu-id="86c68-187">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-187">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-188">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-188">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="86c68-189">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-189">Page route action conventions</span></span>

<span data-ttu-id="86c68-190">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="86c68-190">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="86c68-191">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-191">Folder route model convention</span></span>

<span data-ttu-id="86c68-192">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-192">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="86c68-193">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-193">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="86c68-194"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-194">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="86c68-195">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="86c68-195">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="86c68-196">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-196">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-197">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-197">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-198">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-198">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-199">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-199">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="86c68-202">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-202">Page route model convention</span></span>

<span data-ttu-id="86c68-203">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-203">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="86c68-204">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-204">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="86c68-205"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-205">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="86c68-206">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="86c68-206">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="86c68-207">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-207">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-208">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-208">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-209">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-209">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-210">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-210">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="86c68-213">使用参数转换程序自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="86c68-213">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="86c68-214">可以使用参数转换程序自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-214">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="86c68-215">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="86c68-215">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="86c68-216">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="86c68-216">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="86c68-217">`PageRouteTransformerConvention` 页面路由模型约定将参数转换程序应用于应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="86c68-217">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="86c68-218">例如，/Pages/SubscriptionManagement/ViewAll.cshtml 处的 Razor Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写为 `/subscription-management/view-all`。</span><span class="sxs-lookup"><span data-stu-id="86c68-218">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="86c68-219">`PageRouteTransformerConvention` 仅转换来自 Razor Pages 文件夹和文件名的自动生成页面路由段。</span><span class="sxs-lookup"><span data-stu-id="86c68-219">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="86c68-220">它不会转换使用 `@page` 指令添加的路由段。</span><span class="sxs-lookup"><span data-stu-id="86c68-220">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="86c68-221">该约定也不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 添加的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-221">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="86c68-222">`PageRouteTransformerConvention` 在 `Startup.ConfigureServices` 中注册为选项：</span><span class="sxs-lookup"><span data-stu-id="86c68-222">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a><span data-ttu-id="86c68-223">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="86c68-223">Configure a page route</span></span>

<span data-ttu-id="86c68-224">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-224">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="86c68-225">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-225">Generated links to the page use your specified route.</span></span> <span data-ttu-id="86c68-226">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-226">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="86c68-227">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="86c68-227">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="86c68-228">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-228">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="86c68-229">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="86c68-229">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="86c68-230">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-230">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="86c68-231">请注意，在呈现的页面中，为 **联系人** 链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="86c68-231">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="86c68-234">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-234">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="86c68-235">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="86c68-235">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="86c68-238">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-238">Page model action conventions</span></span>

<span data-ttu-id="86c68-239">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="86c68-239">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="86c68-240">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="86c68-240">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="86c68-241">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="86c68-241">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="86c68-242">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-242">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="86c68-243">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="86c68-243">**Folder app model convention**</span></span>

<span data-ttu-id="86c68-244">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-244">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="86c68-245">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-245">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="86c68-246">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-246">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="86c68-248">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="86c68-248">**Page app model convention**</span></span>

<span data-ttu-id="86c68-249">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-249">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="86c68-250">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-250">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="86c68-251">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-251">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="86c68-253">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="86c68-253">**Configure a filter**</span></span>

<span data-ttu-id="86c68-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="86c68-254"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="86c68-255">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="86c68-255">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="86c68-256">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="86c68-256">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="86c68-257">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="86c68-257">If the condition passes, a header is added.</span></span> <span data-ttu-id="86c68-258">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="86c68-258">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="86c68-259">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="86c68-259">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="86c68-260">由于 Razor Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="86c68-260">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="86c68-261">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-261">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="86c68-263">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="86c68-263">**Configure a filter factory**</span></span>

<span data-ttu-id="86c68-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="86c68-264"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="86c68-265">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-265">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="86c68-266">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-266">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="86c68-267">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-267">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="86c68-269">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="86c68-269">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="86c68-270">Razor Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="86c68-270">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="86c68-271">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="86c68-271">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="86c68-272">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="86c68-272">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="86c68-273">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 Razor Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="86c68-273">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="86c68-274">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="86c68-274">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86c68-275">其他资源</span><span class="sxs-lookup"><span data-stu-id="86c68-275">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="86c68-276">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="86c68-276">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="86c68-277">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-277">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="86c68-278">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="86c68-278">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="86c68-279">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="86c68-279">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="86c68-280">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="86c68-280">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="86c68-281">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="86c68-281">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="86c68-282">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="86c68-282">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="86c68-283">方案</span><span class="sxs-lookup"><span data-stu-id="86c68-283">Scenario</span></span> | <span data-ttu-id="86c68-284">示例演示...</span><span class="sxs-lookup"><span data-stu-id="86c68-284">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="86c68-285">模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-285">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="86c68-286">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="86c68-286">Conventions.Add</span></span><ul><li><span data-ttu-id="86c68-287">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-287">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-288">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-288">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-289">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-289">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="86c68-290">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-290">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="86c68-291">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-291">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="86c68-292">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-292">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-293">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-293">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-294">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="86c68-294">AddPageRoute</span></span></li></ul> | <span data-ttu-id="86c68-295">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-295">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="86c68-296">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-296">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="86c68-297">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-297">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-298">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-298">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-299">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="86c68-299">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="86c68-300">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-300">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="86c68-301">使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法向 `Startup` 类中服务集合的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 添加和配置 Razor Pages 约定。</span><span class="sxs-lookup"><span data-stu-id="86c68-301">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="86c68-302">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="86c68-302">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="86c68-303">路由顺序</span><span class="sxs-lookup"><span data-stu-id="86c68-303">Route order</span></span>

<span data-ttu-id="86c68-304">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="86c68-304">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="86c68-305">顺序</span><span class="sxs-lookup"><span data-stu-id="86c68-305">Order</span></span>            | <span data-ttu-id="86c68-306">行为</span><span class="sxs-lookup"><span data-stu-id="86c68-306">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="86c68-307">-1</span><span class="sxs-lookup"><span data-stu-id="86c68-307">-1</span></span>               | <span data-ttu-id="86c68-308">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-308">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="86c68-309">0</span><span class="sxs-lookup"><span data-stu-id="86c68-309">0</span></span>                | <span data-ttu-id="86c68-310">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="86c68-310">Order isn't specified (default value).</span></span> <span data-ttu-id="86c68-311">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="86c68-311">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="86c68-312">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="86c68-312">1, 2, &hellip; n</span></span> | <span data-ttu-id="86c68-313">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="86c68-313">Specifies the route processing order.</span></span> |

<span data-ttu-id="86c68-314">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="86c68-314">Route processing is established by convention:</span></span>

* <span data-ttu-id="86c68-315">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="86c68-315">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="86c68-316">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-316">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="86c68-317">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-317">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="86c68-318">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="86c68-318">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="86c68-319">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-319">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="86c68-320">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="86c68-320">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="86c68-321">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="86c68-321">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="86c68-322">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="86c68-322">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="86c68-323">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="86c68-323">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="86c68-324">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="86c68-324">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="86c68-325">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="86c68-325">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="86c68-326">模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-326">Model conventions</span></span>

<span data-ttu-id="86c68-327">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="86c68-327">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="86c68-328">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-328">Add a route model convention to all pages</span></span>

<span data-ttu-id="86c68-329">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-329">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="86c68-330">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-330">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-331"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="86c68-331">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="86c68-332">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="86c68-332">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="86c68-333">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-333">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="86c68-334">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="86c68-334">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="86c68-335">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-335">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="86c68-336">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-336">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="86c68-337">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-337">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="86c68-338">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-338">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="86c68-339">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-339">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="86c68-340">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-340">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-341">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-341">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-342">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-342">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-343">将 MVC 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 Razor Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="86c68-343">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="86c68-344">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="86c68-344">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="86c68-345">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-345">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="86c68-348">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-348">Add an app model convention to all pages</span></span>

<span data-ttu-id="86c68-349">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-349">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="86c68-350">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="86c68-350">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="86c68-351">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="86c68-351">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="86c68-352">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="86c68-352">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="86c68-353">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="86c68-353">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="86c68-354">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-354">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-355">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-355">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="86c68-356">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-356">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="86c68-358">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-358">Add a handler model convention to all pages</span></span>

<span data-ttu-id="86c68-359">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-359">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-360">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-360">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="86c68-361">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-361">Page route action conventions</span></span>

<span data-ttu-id="86c68-362">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="86c68-362">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="86c68-363">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-363">Folder route model convention</span></span>

<span data-ttu-id="86c68-364">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-364">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="86c68-365">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-365">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="86c68-366"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-366">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="86c68-367">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="86c68-367">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="86c68-368">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-368">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-369">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-369">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-370">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-370">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-371">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-371">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="86c68-374">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-374">Page route model convention</span></span>

<span data-ttu-id="86c68-375">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-375">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="86c68-376">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-376">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="86c68-377"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-377">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="86c68-378">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="86c68-378">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="86c68-379">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-379">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-380">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-380">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-381">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-381">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-382">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-382">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a><span data-ttu-id="86c68-385">使用参数转换程序自定义页面路由</span><span class="sxs-lookup"><span data-stu-id="86c68-385">Use a parameter transformer to customize page routes</span></span>

<span data-ttu-id="86c68-386">可以使用参数转换程序自定义 ASP.NET Core 生成的页面路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-386">Page routes generated by ASP.NET Core can be customized using a parameter transformer.</span></span> <span data-ttu-id="86c68-387">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="86c68-387">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="86c68-388">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="86c68-388">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="86c68-389">`PageRouteTransformerConvention` 页面路由模型约定将参数转换程序应用于应用中自动生成的页面路由的文件夹和文件名段。</span><span class="sxs-lookup"><span data-stu-id="86c68-389">The `PageRouteTransformerConvention` page route model convention applies a parameter transformer to the folder and file name segments of automatically generated page routes in an app.</span></span> <span data-ttu-id="86c68-390">例如，/Pages/SubscriptionManagement/ViewAll.cshtml 处的 Razor Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写为 `/subscription-management/view-all`。</span><span class="sxs-lookup"><span data-stu-id="86c68-390">For example, the Razor Pages file at */Pages/SubscriptionManagement/ViewAll.cshtml* would have its route rewritten from `/SubscriptionManagement/ViewAll` to `/subscription-management/view-all`.</span></span>

<span data-ttu-id="86c68-391">`PageRouteTransformerConvention` 仅转换来自 Razor Pages 文件夹和文件名的自动生成页面路由段。</span><span class="sxs-lookup"><span data-stu-id="86c68-391">`PageRouteTransformerConvention` only transforms the automatically generated segments of a page route that come from the Razor Pages folder and file name.</span></span> <span data-ttu-id="86c68-392">它不会转换使用 `@page` 指令添加的路由段。</span><span class="sxs-lookup"><span data-stu-id="86c68-392">It doesn't transform route segments added with the `@page` directive.</span></span> <span data-ttu-id="86c68-393">该约定也不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 添加的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-393">The convention also doesn't transform routes added by <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.</span></span>

<span data-ttu-id="86c68-394">`PageRouteTransformerConvention` 在 `Startup.ConfigureServices` 中注册为选项：</span><span class="sxs-lookup"><span data-stu-id="86c68-394">The `PageRouteTransformerConvention` is registered as an option in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a><span data-ttu-id="86c68-395">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="86c68-395">Configure a page route</span></span>

<span data-ttu-id="86c68-396">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-396">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="86c68-397">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-397">Generated links to the page use your specified route.</span></span> <span data-ttu-id="86c68-398">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-398">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="86c68-399">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="86c68-399">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="86c68-400">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-400">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="86c68-401">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="86c68-401">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="86c68-402">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-402">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="86c68-403">请注意，在呈现的页面中，为 **联系人** 链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="86c68-403">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="86c68-406">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-406">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="86c68-407">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="86c68-407">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="86c68-410">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-410">Page model action conventions</span></span>

<span data-ttu-id="86c68-411">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="86c68-411">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="86c68-412">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="86c68-412">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="86c68-413">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="86c68-413">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="86c68-414">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-414">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="86c68-415">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="86c68-415">**Folder app model convention**</span></span>

<span data-ttu-id="86c68-416">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-416">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="86c68-417">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-417">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="86c68-418">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-418">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="86c68-420">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="86c68-420">**Page app model convention**</span></span>

<span data-ttu-id="86c68-421">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-421">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="86c68-422">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-422">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="86c68-423">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-423">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="86c68-425">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="86c68-425">**Configure a filter**</span></span>

<span data-ttu-id="86c68-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="86c68-426"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="86c68-427">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="86c68-427">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="86c68-428">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="86c68-428">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="86c68-429">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="86c68-429">If the condition passes, a header is added.</span></span> <span data-ttu-id="86c68-430">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="86c68-430">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="86c68-431">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="86c68-431">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="86c68-432">由于 Razor Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="86c68-432">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="86c68-433">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-433">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="86c68-435">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="86c68-435">**Configure a filter factory**</span></span>

<span data-ttu-id="86c68-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="86c68-436"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="86c68-437">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-437">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="86c68-438">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-438">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="86c68-439">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-439">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="86c68-441">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="86c68-441">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="86c68-442">Razor Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="86c68-442">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="86c68-443">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="86c68-443">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="86c68-444">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="86c68-444">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="86c68-445">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 Razor Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="86c68-445">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="86c68-446">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="86c68-446">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86c68-447">其他资源</span><span class="sxs-lookup"><span data-stu-id="86c68-447">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="86c68-448">了解如何使用页面[路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor Pages 应用中的页面路由、发现和处理。</span><span class="sxs-lookup"><span data-stu-id="86c68-448">Learn how to use page [route and app model provider conventions](xref:mvc/controllers/application-model#conventions) to control page routing, discovery, and processing in Razor Pages apps.</span></span>

<span data-ttu-id="86c68-449">需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-449">When you need to configure custom page routes for individual pages, configure routing to pages with the [AddPageRoute convention](#configure-a-page-route) described later in this topic.</span></span>

<span data-ttu-id="86c68-450">若要指定页面路由、添加路由段或向路由添加参数，请使用页面的 `@page` 指令。</span><span class="sxs-lookup"><span data-stu-id="86c68-450">To specify a page route, add route segments, or add parameters to a route, use the page's `@page` directive.</span></span> <span data-ttu-id="86c68-451">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。</span><span class="sxs-lookup"><span data-stu-id="86c68-451">For more information, see [Custom routes](xref:razor-pages/index#custom-routes).</span></span>

<span data-ttu-id="86c68-452">有些保留字不能用作路由段或参数名称。</span><span class="sxs-lookup"><span data-stu-id="86c68-452">There are reserved words that can't be used as route segments or parameter names.</span></span> <span data-ttu-id="86c68-453">有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。</span><span class="sxs-lookup"><span data-stu-id="86c68-453">For more information, see [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).</span></span>

<span data-ttu-id="86c68-454">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="86c68-454">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

| <span data-ttu-id="86c68-455">方案</span><span class="sxs-lookup"><span data-stu-id="86c68-455">Scenario</span></span> | <span data-ttu-id="86c68-456">示例演示...</span><span class="sxs-lookup"><span data-stu-id="86c68-456">The sample demonstrates ...</span></span> |
| -------- | --------------------------- |
| [<span data-ttu-id="86c68-457">模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-457">Model conventions</span></span>](#model-conventions)<br><br><span data-ttu-id="86c68-458">Conventions.Add</span><span class="sxs-lookup"><span data-stu-id="86c68-458">Conventions.Add</span></span><ul><li><span data-ttu-id="86c68-459">IPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-459">IPageRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-460">IPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-460">IPageApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-461">IPageHandlerModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-461">IPageHandlerModelConvention</span></span></li></ul> | <span data-ttu-id="86c68-462">将路由模板和标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-462">Add a route template and header to an app's pages.</span></span> |
| [<span data-ttu-id="86c68-463">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-463">Page route action conventions</span></span>](#page-route-action-conventions)<ul><li><span data-ttu-id="86c68-464">AddFolderRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-464">AddFolderRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-465">AddPageRouteModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-465">AddPageRouteModelConvention</span></span></li><li><span data-ttu-id="86c68-466">AddPageRoute</span><span class="sxs-lookup"><span data-stu-id="86c68-466">AddPageRoute</span></span></li></ul> | <span data-ttu-id="86c68-467">将路由模板添加到某个文件夹中的页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-467">Add a route template to pages in a folder and to a single page.</span></span> |
| [<span data-ttu-id="86c68-468">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-468">Page model action conventions</span></span>](#page-model-action-conventions)<ul><li><span data-ttu-id="86c68-469">AddFolderApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-469">AddFolderApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-470">AddPageApplicationModelConvention</span><span class="sxs-lookup"><span data-stu-id="86c68-470">AddPageApplicationModelConvention</span></span></li><li><span data-ttu-id="86c68-471">ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</span><span class="sxs-lookup"><span data-stu-id="86c68-471">ConfigureFilter (filter class, lambda expression, or filter factory)</span></span></li></ul> | <span data-ttu-id="86c68-472">将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-472">Add a header to pages in a folder, add a header to a single page, and configure a [filter factory](xref:mvc/controllers/filters#ifilterfactory) to add a header to an app's pages.</span></span> |

<span data-ttu-id="86c68-473">使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法向 `Startup` 类中服务集合的 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> 添加和配置 Razor Pages 约定。</span><span class="sxs-lookup"><span data-stu-id="86c68-473">Razor Pages conventions are added and configured using the <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> extension method to <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> on the service collection in the `Startup` class.</span></span> <span data-ttu-id="86c68-474">本主题稍后会介绍以下约定示例：</span><span class="sxs-lookup"><span data-stu-id="86c68-474">The following convention examples are explained later in this topic:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a><span data-ttu-id="86c68-475">路由顺序</span><span class="sxs-lookup"><span data-stu-id="86c68-475">Route order</span></span>

<span data-ttu-id="86c68-476">路由会为进行处理指定一个 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>（路由匹配）。</span><span class="sxs-lookup"><span data-stu-id="86c68-476">Routes specify an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> for processing (route matching).</span></span>

| <span data-ttu-id="86c68-477">顺序</span><span class="sxs-lookup"><span data-stu-id="86c68-477">Order</span></span>            | <span data-ttu-id="86c68-478">行为</span><span class="sxs-lookup"><span data-stu-id="86c68-478">Behavior</span></span> |
| :--------------: | -------- |
| <span data-ttu-id="86c68-479">-1</span><span class="sxs-lookup"><span data-stu-id="86c68-479">-1</span></span>               | <span data-ttu-id="86c68-480">在处理其他路由之前处理该路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-480">The route is processed before other routes are processed.</span></span> |
| <span data-ttu-id="86c68-481">0</span><span class="sxs-lookup"><span data-stu-id="86c68-481">0</span></span>                | <span data-ttu-id="86c68-482">未指定顺序（默认值）。</span><span class="sxs-lookup"><span data-stu-id="86c68-482">Order isn't specified (default value).</span></span> <span data-ttu-id="86c68-483">不分配 `Order` (`Order = null`) 会将路由 `Order` 默认为 0（零）以进行处理。</span><span class="sxs-lookup"><span data-stu-id="86c68-483">Not assigning `Order` (`Order = null`) defaults the route `Order` to 0 (zero) for processing.</span></span> |
| <span data-ttu-id="86c68-484">1、2 &hellip; n</span><span class="sxs-lookup"><span data-stu-id="86c68-484">1, 2, &hellip; n</span></span> | <span data-ttu-id="86c68-485">指定路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="86c68-485">Specifies the route processing order.</span></span> |

<span data-ttu-id="86c68-486">按约定建立路由处理：</span><span class="sxs-lookup"><span data-stu-id="86c68-486">Route processing is established by convention:</span></span>

* <span data-ttu-id="86c68-487">按顺序处理路由（-1、0、1、2 &hellip; n）。</span><span class="sxs-lookup"><span data-stu-id="86c68-487">Routes are processed in sequential order (-1, 0, 1, 2, &hellip; n).</span></span>
* <span data-ttu-id="86c68-488">当路由具有相同 `Order` 时，首先匹配最具体的路由，然后匹配不太具体的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-488">When routes have the same `Order`, the most specific route is matched first followed by less specific routes.</span></span>
* <span data-ttu-id="86c68-489">当具有相同 `Order` 和相同数量参数的路由与请求 URL 匹配时，会按添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> 的顺序处理路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-489">When routes with the same `Order` and the same number of parameters match a request URL, routes are processed in the order that they're added to the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.</span></span>

<span data-ttu-id="86c68-490">如果可能，请避免依赖于建立的路由处理顺序。</span><span class="sxs-lookup"><span data-stu-id="86c68-490">If possible, avoid depending on an established route processing order.</span></span> <span data-ttu-id="86c68-491">通常，路由会通过 URL 匹配选择正确路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-491">Generally, routing selects the correct route with URL matching.</span></span> <span data-ttu-id="86c68-492">如果必须设置路由 `Order` 属性以便正确路由请求，则应用的路由方案可能会使客户端感到困惑并且难以维护。</span><span class="sxs-lookup"><span data-stu-id="86c68-492">If you must set route `Order` properties to route requests correctly, the app's routing scheme is probably confusing to clients and fragile to maintain.</span></span> <span data-ttu-id="86c68-493">应设法简化应用的路由方案。</span><span class="sxs-lookup"><span data-stu-id="86c68-493">Seek to simplify the app's routing scheme.</span></span> <span data-ttu-id="86c68-494">示例应用需要显式路由处理顺序以使用单个应用来演示几个路由方案。</span><span class="sxs-lookup"><span data-stu-id="86c68-494">The sample app requires an explicit route processing order to demonstrate several routing scenarios using a single app.</span></span> <span data-ttu-id="86c68-495">但是，在生产应用中应尝试避免设置路由 `Order` 的做法。</span><span class="sxs-lookup"><span data-stu-id="86c68-495">However, you should attempt to avoid the practice of setting route `Order` in production apps.</span></span>

<span data-ttu-id="86c68-496">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="86c68-496">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="86c68-497">有关 MVC 主题中的路由顺序的信息可在以下位置获得：[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。</span><span class="sxs-lookup"><span data-stu-id="86c68-497">Information on route order in the MVC topics is available at [Routing to controller actions: Ordering attribute routes](xref:mvc/controllers/routing#ordering-attribute-routes).</span></span>

## <a name="model-conventions"></a><span data-ttu-id="86c68-498">模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-498">Model conventions</span></span>

<span data-ttu-id="86c68-499">为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加委托，以添加应用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。</span><span class="sxs-lookup"><span data-stu-id="86c68-499">Add a delegate for <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> to add [model conventions](xref:mvc/controllers/application-model#conventions) that apply to Razor Pages.</span></span>

### <a name="add-a-route-model-convention-to-all-pages"></a><span data-ttu-id="86c68-500">将路由模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-500">Add a route model convention to all pages</span></span>

<span data-ttu-id="86c68-501">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面路由模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-501">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page route model construction.</span></span>

<span data-ttu-id="86c68-502">示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-502">The sample app adds a `{globalTemplate?}` route template to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-503"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。</span><span class="sxs-lookup"><span data-stu-id="86c68-503">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `1`.</span></span> <span data-ttu-id="86c68-504">这可确保在示例应用中实现以下路由匹配行为：</span><span class="sxs-lookup"><span data-stu-id="86c68-504">This ensures the following route matching behavior in the sample app:</span></span>

* <span data-ttu-id="86c68-505">本主题后面会添加 `TheContactPage/{text?}` 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-505">A route template for `TheContactPage/{text?}` is added later in the topic.</span></span> <span data-ttu-id="86c68-506">“联系人”页面路由具有默认顺序 `null` (`Order = 0`)，因此它在 `{globalTemplate?}` 路由模板之前进行匹配。</span><span class="sxs-lookup"><span data-stu-id="86c68-506">The Contact Page route has a default order of `null` (`Order = 0`), so it matches before the `{globalTemplate?}` route template.</span></span>
* <span data-ttu-id="86c68-507">本主题后面会添加 `{aboutTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-507">An `{aboutTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="86c68-508">为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-508">The `{aboutTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="86c68-509">当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-509">When the About page is requested at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>
* <span data-ttu-id="86c68-510">本主题后面会添加 `{otherPagesTemplate?}` 路由模板。</span><span class="sxs-lookup"><span data-stu-id="86c68-510">An `{otherPagesTemplate?}` route template is added later in the topic.</span></span> <span data-ttu-id="86c68-511">为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-511">The `{otherPagesTemplate?}` template is given an `Order` of `2`.</span></span> <span data-ttu-id="86c68-512">当使用路由参数请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-512">When any page in the *Pages/OtherPages* folder is requested with a route parameter (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-513">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-513">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-514">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-514">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-515">将 MVC 添加到 `Startup.ConfigureServices` 中的服务集合时，会添加 Razor Pages 选项，例如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。</span><span class="sxs-lookup"><span data-stu-id="86c68-515">Razor Pages options, such as adding <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, are added when MVC is added to the service collection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="86c68-516">有关示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/)。</span><span class="sxs-lookup"><span data-stu-id="86c68-516">For an example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/razor-pages-conventions/samples/).</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="86c68-517">在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-517">Request the sample's About page at `localhost:5000/About/GlobalRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a><span data-ttu-id="86c68-520">将应用模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-520">Add an app model convention to all pages</span></span>

<span data-ttu-id="86c68-521">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面应用模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-521">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page app model construction.</span></span>

<span data-ttu-id="86c68-522">为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。</span><span class="sxs-lookup"><span data-stu-id="86c68-522">To demonstrate this and other conventions later in the topic, the sample app includes an `AddHeaderAttribute` class.</span></span> <span data-ttu-id="86c68-523">类构造函数采用 `name` 字符串和 `values` 字符串数组。</span><span class="sxs-lookup"><span data-stu-id="86c68-523">The class constructor accepts a `name` string and a `values` string array.</span></span> <span data-ttu-id="86c68-524">将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。</span><span class="sxs-lookup"><span data-stu-id="86c68-524">These values are used in its `OnResultExecuting` method to set a response header.</span></span> <span data-ttu-id="86c68-525">本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。</span><span class="sxs-lookup"><span data-stu-id="86c68-525">The full class is shown in the [Page model action conventions](#page-model-action-conventions) section later in the topic.</span></span>

<span data-ttu-id="86c68-526">示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-526">The sample app uses the `AddHeaderAttribute` class to add a header, `GlobalHeader`, to all of the pages in the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-527">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-527">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="86c68-528">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-528">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a><span data-ttu-id="86c68-530">将处理程序模型约定添加到所有页面</span><span class="sxs-lookup"><span data-stu-id="86c68-530">Add a handler model convention to all pages</span></span>

<span data-ttu-id="86c68-531">使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> 并将其添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例集合中，这些实例将在页面处理程序模型构造过程中应用。</span><span class="sxs-lookup"><span data-stu-id="86c68-531">Use <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> to the collection of <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instances that are applied during page handler model construction.</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

<span data-ttu-id="86c68-532">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-532">*Startup.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a><span data-ttu-id="86c68-533">页面路由操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-533">Page route action conventions</span></span>

<span data-ttu-id="86c68-534">默认路由模型提供程序派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>，可调用旨在为页面路由配置提供扩展点的约定。</span><span class="sxs-lookup"><span data-stu-id="86c68-534">The default route model provider that derives from <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> invokes conventions which are designed to provide extensibility points for configuring page routes.</span></span>

### <a name="folder-route-model-convention"></a><span data-ttu-id="86c68-535">文件夹路由模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-535">Folder route model convention</span></span>

<span data-ttu-id="86c68-536">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-536">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for all of the pages under the specified folder.</span></span>

<span data-ttu-id="86c68-537">示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-537">The sample app uses <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> to add an `{otherPagesTemplate?}` route template to the pages in the *OtherPages* folder:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="86c68-538"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-538">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="86c68-539">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="86c68-539">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="86c68-540">如果使用路由参数值请求 Pages/OtherPages 文件夹中的任何页面（例如，`/OtherPages/Page1/RouteDataValue`）时，由于设置了 `Order` 属性，因此“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 中，而不是 `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-540">If a page in the *Pages/OtherPages* folder is requested with a route parameter value (for example, `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-541">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-541">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-542">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-542">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-543">在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-543">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a><span data-ttu-id="86c68-546">页面路由模型约定</span><span class="sxs-lookup"><span data-stu-id="86c68-546">Page route model convention</span></span>

<span data-ttu-id="86c68-547">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-547">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> for the page with the specified name.</span></span>

<span data-ttu-id="86c68-548">示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-548">The sample app uses `AddPageRouteModelConvention` to add an `{aboutTemplate?}` route template to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="86c68-549"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。</span><span class="sxs-lookup"><span data-stu-id="86c68-549">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> property for the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> is set to `2`.</span></span> <span data-ttu-id="86c68-550">这样可确保，当提供单个路由值时，优先将 `{globalTemplate?}` 的模板（已在本主题的前面部分设置为 `1`）作为第一个路由数据值位置。</span><span class="sxs-lookup"><span data-stu-id="86c68-550">This ensures that the template for `{globalTemplate?}` (set earlier in the topic to `1`) is given priority for the first route data value position when a single route value is provided.</span></span> <span data-ttu-id="86c68-551">如果在 `/About/RouteDataValue` 中使用路由参数值请求“关于”页面，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。</span><span class="sxs-lookup"><span data-stu-id="86c68-551">If the About page is requested with a route parameter value at `/About/RouteDataValue`, "RouteDataValue" is loaded into `RouteData.Values["globalTemplate"]` (`Order = 1`) and not `RouteData.Values["aboutTemplate"]` (`Order = 2`) due to setting the `Order` property.</span></span>

<span data-ttu-id="86c68-552">尽可能不要将 `Order` 设置为 `Order = 0`。</span><span class="sxs-lookup"><span data-stu-id="86c68-552">Wherever possible, don't set the `Order`, which results in `Order = 0`.</span></span> <span data-ttu-id="86c68-553">依赖路由选择正确的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-553">Rely on routing to select the correct route.</span></span>

<span data-ttu-id="86c68-554">在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-554">Request the sample's About page at `localhost:5000/About/GlobalRouteValue/AboutRouteValue` and inspect the result:</span></span>

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a><span data-ttu-id="86c68-557">配置页面路由</span><span class="sxs-lookup"><span data-stu-id="86c68-557">Configure a page route</span></span>

<span data-ttu-id="86c68-558">使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置路由，该路由指向指定页面路径中的页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-558">Use <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> to configure a route to a page at the specified page path.</span></span> <span data-ttu-id="86c68-559">生成的页面链接使用指定的路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-559">Generated links to the page use your specified route.</span></span> <span data-ttu-id="86c68-560">`AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。</span><span class="sxs-lookup"><span data-stu-id="86c68-560">`AddPageRoute` uses `AddPageRouteModelConvention` to establish the route.</span></span>

<span data-ttu-id="86c68-561">示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：</span><span class="sxs-lookup"><span data-stu-id="86c68-561">The sample app creates a route to `/TheContactPage` for *Contact.cshtml*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

<span data-ttu-id="86c68-562">还可在 `/Contact` 中通过默认路由访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-562">The Contact page can also be reached at `/Contact` via its default route.</span></span>

<span data-ttu-id="86c68-563">示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。</span><span class="sxs-lookup"><span data-stu-id="86c68-563">The sample app's custom route to the Contact page allows for an optional `text` route segment (`{text?}`).</span></span> <span data-ttu-id="86c68-564">该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-564">The page also includes this optional segment in its `@page` directive in case the visitor accesses the page at its `/Contact` route:</span></span>

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

<span data-ttu-id="86c68-565">请注意，在呈现的页面中，为 **联系人** 链接生成的 URL 反映了已更新的路由：</span><span class="sxs-lookup"><span data-stu-id="86c68-565">Note that the URL generated for the **Contact** link in the rendered page reflects the updated route:</span></span>

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

<span data-ttu-id="86c68-568">在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-568">Visit the Contact page at either its ordinary route, `/Contact`, or the custom route, `/TheContactPage`.</span></span> <span data-ttu-id="86c68-569">如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：</span><span class="sxs-lookup"><span data-stu-id="86c68-569">If you supply an additional `text` route segment, the page shows the HTML-encoded segment that you provide:</span></span>

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a><span data-ttu-id="86c68-572">页面模型操作约定</span><span class="sxs-lookup"><span data-stu-id="86c68-572">Page model action conventions</span></span>

<span data-ttu-id="86c68-573">实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序可调用约定，这些约定旨在为页面模型配置提供扩展点。</span><span class="sxs-lookup"><span data-stu-id="86c68-573">The default page model provider that implements <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> invokes conventions which are designed to provide extensibility points for configuring page models.</span></span> <span data-ttu-id="86c68-574">在生成和修改页面发现及处理方案时，可使用这些约定。</span><span class="sxs-lookup"><span data-stu-id="86c68-574">These conventions are useful when building and modifying page discovery and processing scenarios.</span></span>

<span data-ttu-id="86c68-575">对于此部分中的示例，示例应用使用 `AddHeaderAttribute` 类（一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>）来应用响应标头：</span><span class="sxs-lookup"><span data-stu-id="86c68-575">For the examples in this section, the sample app uses an `AddHeaderAttribute` class, which is a <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, that applies a response header:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

<span data-ttu-id="86c68-576">示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。</span><span class="sxs-lookup"><span data-stu-id="86c68-576">Using conventions, the sample demonstrates how to apply the attribute to all of the pages in a folder and to a single page.</span></span>

<span data-ttu-id="86c68-577">**文件夹应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="86c68-577">**Folder app model convention**</span></span>

<span data-ttu-id="86c68-578">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于指定文件夹下的所有页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-578">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instances for all pages under the specified folder.</span></span>

<span data-ttu-id="86c68-579">示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-579">The sample demonstrates the use of `AddFolderApplicationModelConvention` by adding a header, `OtherPagesHeader`, to the pages inside the *OtherPages* folder of the app:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

<span data-ttu-id="86c68-580">在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-580">Request the sample's Page1 page at `localhost:5000/OtherPages/Page1` and inspect the headers to view the result:</span></span>

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

<span data-ttu-id="86c68-582">**页面应用模型约定**</span><span class="sxs-lookup"><span data-stu-id="86c68-582">**Page app model convention**</span></span>

<span data-ttu-id="86c68-583">使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 可创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它对于具有指定名称的页面，会在 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。</span><span class="sxs-lookup"><span data-stu-id="86c68-583">Use <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> to create and add an <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> that invokes an action on the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> for the page with the specified name.</span></span>

<span data-ttu-id="86c68-584">示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-584">The sample demonstrates the use of `AddPageApplicationModelConvention` by adding a header, `AboutHeader`, to the About page:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

<span data-ttu-id="86c68-585">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-585">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

<span data-ttu-id="86c68-587">**配置筛选器**</span><span class="sxs-lookup"><span data-stu-id="86c68-587">**Configure a filter**</span></span>

<span data-ttu-id="86c68-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置要应用的指定筛选器。</span><span class="sxs-lookup"><span data-stu-id="86c68-588"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified filter to apply.</span></span> <span data-ttu-id="86c68-589">用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：</span><span class="sxs-lookup"><span data-stu-id="86c68-589">You can implement a filter class, but the sample app shows how to implement a filter in a lambda expression, which is implemented behind-the-scenes as a factory that returns a filter:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

<span data-ttu-id="86c68-590">页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。</span><span class="sxs-lookup"><span data-stu-id="86c68-590">The page app model is used to check the relative path for segments that lead to the Page2 page in the *OtherPages* folder.</span></span> <span data-ttu-id="86c68-591">如果条件通过，则添加标头。</span><span class="sxs-lookup"><span data-stu-id="86c68-591">If the condition passes, a header is added.</span></span> <span data-ttu-id="86c68-592">如果不通过，则应用 `EmptyFilter`。</span><span class="sxs-lookup"><span data-stu-id="86c68-592">If not, the `EmptyFilter` is applied.</span></span>

<span data-ttu-id="86c68-593">`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。</span><span class="sxs-lookup"><span data-stu-id="86c68-593">`EmptyFilter` is an [Action filter](xref:mvc/controllers/filters#action-filters).</span></span> <span data-ttu-id="86c68-594">由于 Razor Pages 会忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，`EmptyFilter` 应该无效。</span><span class="sxs-lookup"><span data-stu-id="86c68-594">Since Action filters are ignored by Razor Pages, the `EmptyFilter` has no effect as intended if the path doesn't contain `OtherPages/Page2`.</span></span>

<span data-ttu-id="86c68-595">在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-595">Request the sample's Page2 page at `localhost:5000/OtherPages/Page2` and inspect the headers to view the result:</span></span>

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

<span data-ttu-id="86c68-597">**配置筛选器工厂**</span><span class="sxs-lookup"><span data-stu-id="86c68-597">**Configure a filter factory**</span></span>

<span data-ttu-id="86c68-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 可配置指定的工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="86c68-598"><xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> configures the specified factory to apply [filters](xref:mvc/controllers/filters) to all Razor Pages.</span></span>

<span data-ttu-id="86c68-599">示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：</span><span class="sxs-lookup"><span data-stu-id="86c68-599">The sample app provides an example of using a [filter factory](xref:mvc/controllers/filters#ifilterfactory) by adding a header, `FilterFactoryHeader`, with two values to the app's pages:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

<span data-ttu-id="86c68-600">*AddHeaderWithFactory.cs*：</span><span class="sxs-lookup"><span data-stu-id="86c68-600">*AddHeaderWithFactory.cs*:</span></span>

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

<span data-ttu-id="86c68-601">在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：</span><span class="sxs-lookup"><span data-stu-id="86c68-601">Request the sample's About page at `localhost:5000/About` and inspect the headers to view the result:</span></span>

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a><span data-ttu-id="86c68-603">MVC 筛选器和页面筛选器 (IPageFilter)</span><span class="sxs-lookup"><span data-stu-id="86c68-603">MVC Filters and the Page filter (IPageFilter)</span></span>

<span data-ttu-id="86c68-604">Razor Pages 会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor Pages 使用处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="86c68-604">MVC [Action filters](xref:mvc/controllers/filters#action-filters) are ignored by Razor Pages, since Razor Pages use handler methods.</span></span> <span data-ttu-id="86c68-605">可以使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。</span><span class="sxs-lookup"><span data-stu-id="86c68-605">Other types of MVC filters are available for you to use: [Authorization](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Resource](xref:mvc/controllers/filters#resource-filters), and [Result](xref:mvc/controllers/filters#result-filters).</span></span> <span data-ttu-id="86c68-606">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。</span><span class="sxs-lookup"><span data-stu-id="86c68-606">For more information, see the [Filters](xref:mvc/controllers/filters) topic.</span></span>

<span data-ttu-id="86c68-607">页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是应用于 Razor Pages 的一种筛选器。</span><span class="sxs-lookup"><span data-stu-id="86c68-607">The Page filter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) is a filter that applies to Razor Pages.</span></span> <span data-ttu-id="86c68-608">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="86c68-608">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="86c68-609">其他资源</span><span class="sxs-lookup"><span data-stu-id="86c68-609">Additional resources</span></span>

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
