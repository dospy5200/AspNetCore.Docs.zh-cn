---
title: ASP.NET Core 中的 Razor Pages 的筛选方法
author: Rick-Anderson
description: 了解如何为 ASP.NET Core 中的 Razor Pages 创建筛选方法。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: 178e6348d2d50dae34feea6a0ed261de01037136
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586132"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="07e35-103">ASP.NET Core 中的 Razor Pages 的筛选方法</span><span class="sxs-lookup"><span data-stu-id="07e35-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07e35-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="07e35-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="07e35-105">Razor 页面筛选器 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) 和 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) 允许 Razor Pages 在运行 Razor 页面处理程序的前后运行代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="07e35-106">Razor 页面筛选器与 [ASP.NET Core MVC 操作筛选器](xref:mvc/controllers/filters#action-filters)类似，但它们不能应用于单个页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="07e35-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="07e35-107">Razor 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="07e35-107">Razor Page filters:</span></span>

* <span data-ttu-id="07e35-108">在选择处理程序方法后但在模型绑定发生前运行代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="07e35-109">在模型绑定完成后，执行处理程序方法前运行代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="07e35-110">在执行处理程序方法后运行代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="07e35-111">可在页面或全局范围内实现。</span><span class="sxs-lookup"><span data-stu-id="07e35-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="07e35-112">无法应用于特定的页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="07e35-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="07e35-113">可以用[依赖项注入](xref:fundamentals/dependency-injection) (DI) 填充构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="07e35-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="07e35-114">有关详细信息，请参阅 [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) 和 [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute)。</span><span class="sxs-lookup"><span data-stu-id="07e35-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="07e35-115">虽然页构造函数和中间件允许在处理程序方法执行之前执行自定义代码，但只有 Razor 页面筛选器允许访问 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> 和页面。</span><span class="sxs-lookup"><span data-stu-id="07e35-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="07e35-116">中间件可以访问 `HttpContext`，但不能访问“页面上下文”。</span><span class="sxs-lookup"><span data-stu-id="07e35-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="07e35-117">筛选器具有 <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> 派生参数，该参数提供对 `HttpContext` 的访问权限。</span><span class="sxs-lookup"><span data-stu-id="07e35-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="07e35-118">下面是页面筛选器的示例：[实现筛选器属性](#ifa)，该属性将标头添加到响应中，而使用构造函数或中间件则无法做到这点。</span><span class="sxs-lookup"><span data-stu-id="07e35-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="07e35-119">对页面上下文的访问（包括对页面实例及其模型的访问）仅在执行筛选器、处理程序或 Razor 页面的正文时适用。</span><span class="sxs-lookup"><span data-stu-id="07e35-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="07e35-120">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/3.1sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="07e35-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="07e35-121">Razor 页面筛选器提供的以下方法可在全局或页面级应用：</span><span class="sxs-lookup"><span data-stu-id="07e35-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="07e35-122">同步方法：</span><span class="sxs-lookup"><span data-stu-id="07e35-122">Synchronous methods:</span></span>

  * <span data-ttu-id="07e35-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生之前调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="07e35-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0)：在模型绑定完成后，执行处理程序方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="07e35-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0)：在执行处理器方法后，生成操作结果前调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="07e35-126">异步方法：</span><span class="sxs-lookup"><span data-stu-id="07e35-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="07e35-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生前，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="07e35-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0)：在调用处理程序方法前，但在模型绑定结束后，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="07e35-129">筛选器接口的同步和异步版本任意实现一个，而不是同时实现 。</span><span class="sxs-lookup"><span data-stu-id="07e35-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="07e35-130">该框架会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="07e35-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="07e35-131">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="07e35-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="07e35-132">如果两个接口都已实现，则只会调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="07e35-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="07e35-133">对页面中的替代应用相同的规则，同步替代或异步替代只能任选其一实现，不可二者皆选。</span><span class="sxs-lookup"><span data-stu-id="07e35-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="07e35-134">全局实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="07e35-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="07e35-135">以下代码实现了 `IAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="07e35-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="07e35-136">在前面的代码中，`ProcessUserAgent.Write` 是用户提供的与用户代理字符串一起使用的代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="07e35-137">以下代码启用 `Startup` 类中的 `SampleAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="07e35-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="07e35-138">以下代码调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>，将 `SampleAsyncPageFilter` 仅应用于 /Movies 中的页面：</span><span class="sxs-lookup"><span data-stu-id="07e35-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="07e35-139">以下代码实现同步的 `IPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="07e35-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="07e35-140">以下代码启用 `SamplePageFilter`：</span><span class="sxs-lookup"><span data-stu-id="07e35-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="07e35-141">通过重写筛选器方法实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="07e35-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="07e35-142">以下代码替代异步 Razor 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="07e35-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="07e35-143">实现筛选器属性</span><span class="sxs-lookup"><span data-stu-id="07e35-143">Implement a filter attribute</span></span>

<span data-ttu-id="07e35-144">基于属性的内置筛选器 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> 可以进行子类化。</span><span class="sxs-lookup"><span data-stu-id="07e35-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="07e35-145">以下筛选器向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="07e35-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="07e35-146">以下代码应用 `AddHeader` 属性：</span><span class="sxs-lookup"><span data-stu-id="07e35-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="07e35-147">使用浏览器开发人员工具等工具来检查标头。</span><span class="sxs-lookup"><span data-stu-id="07e35-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="07e35-148">在响应标头下，将显示 `author: Rick`。</span><span class="sxs-lookup"><span data-stu-id="07e35-148">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="07e35-149">有关重写顺序的说明，请参阅[重写默认顺序](xref:mvc/controllers/filters#overriding-the-default-order)。</span><span class="sxs-lookup"><span data-stu-id="07e35-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="07e35-150">有关将筛选器管道与筛选器短路的说明，请参阅[取消和设置短路](xref:mvc/controllers/filters#cancellation-and-short-circuiting)。</span><span class="sxs-lookup"><span data-stu-id="07e35-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="07e35-151">授权筛选器属性</span><span class="sxs-lookup"><span data-stu-id="07e35-151">Authorize filter attribute</span></span>

<span data-ttu-id="07e35-152">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性可应用于 `PageModel`：</span><span class="sxs-lookup"><span data-stu-id="07e35-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07e35-153">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="07e35-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="07e35-154">Razor 页面筛选器 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) 和 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) 允许 Razor Pages 在运行 Razor 页面处理程序的前后运行代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="07e35-155">Razor 页面筛选器与 [ASP.NET Core MVC 操作筛选器](xref:mvc/controllers/filters#action-filters)类似，但它们不能应用于单个页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="07e35-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="07e35-156">Razor 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="07e35-156">Razor Page filters:</span></span>

* <span data-ttu-id="07e35-157">在选择处理程序方法后但在模型绑定发生前运行代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="07e35-158">在模型绑定完成后，执行处理程序方法前运行代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="07e35-159">在执行处理程序方法后运行代码。</span><span class="sxs-lookup"><span data-stu-id="07e35-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="07e35-160">可在页面或全局范围内实现。</span><span class="sxs-lookup"><span data-stu-id="07e35-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="07e35-161">无法应用于特定的页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="07e35-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="07e35-162">代码可在使用页面构造函数或中间件执行处理程序方法前运行，但仅 Razor 页面筛选器可访问 [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)。</span><span class="sxs-lookup"><span data-stu-id="07e35-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="07e35-163">筛选器具有 [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 派生参数，可用于访问 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="07e35-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="07e35-164">例如，[实现筛选器属性](#ifa)示例将标头添加到响应中，而使用构造函数或中间件则无法做到这点。</span><span class="sxs-lookup"><span data-stu-id="07e35-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="07e35-165">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/sample/PageFilter)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="07e35-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="07e35-166">Razor 页面筛选器提供的以下方法可在全局或页面级应用：</span><span class="sxs-lookup"><span data-stu-id="07e35-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="07e35-167">同步方法：</span><span class="sxs-lookup"><span data-stu-id="07e35-167">Synchronous methods:</span></span>

  * <span data-ttu-id="07e35-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生之前调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="07e35-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0)：在模型绑定完成后，执行处理程序方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="07e35-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0)：在执行处理器方法后，生成操作结果前调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="07e35-171">异步方法：</span><span class="sxs-lookup"><span data-stu-id="07e35-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="07e35-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生前，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="07e35-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0)：在调用处理程序方法前，但在模型绑定结束后，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="07e35-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="07e35-174">筛选器接口的同步和异步版本 **任意** 实现一个，而不是同时实现。</span><span class="sxs-lookup"><span data-stu-id="07e35-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="07e35-175">该框架会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="07e35-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="07e35-176">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="07e35-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="07e35-177">如果两个接口都已实现，则只会调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="07e35-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="07e35-178">对页面中的替代应用相同的规则，同步替代或异步替代只能任选其一实现，不可二者皆选。</span><span class="sxs-lookup"><span data-stu-id="07e35-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="07e35-179">全局实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="07e35-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="07e35-180">以下代码实现了 `IAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="07e35-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="07e35-181">在前面的代码中，[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) 不是必需的。</span><span class="sxs-lookup"><span data-stu-id="07e35-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="07e35-182">它在示例中用于提供应用程序的跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="07e35-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="07e35-183">以下代码启用 `Startup` 类中的 `SampleAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="07e35-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="07e35-184">以下代码显示完整的 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="07e35-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="07e35-185">以下代码调用 `AddFolderApplicationModelConvention` 将 `SampleAsyncPageFilter` 仅应用于 /subFolder 中的页面：</span><span class="sxs-lookup"><span data-stu-id="07e35-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="07e35-186">以下代码实现同步的 `IPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="07e35-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="07e35-187">以下代码启用 `SamplePageFilter`：</span><span class="sxs-lookup"><span data-stu-id="07e35-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="07e35-188">通过重写筛选器方法实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="07e35-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="07e35-189">以下代码替代同步 Razor 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="07e35-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="07e35-190">实现筛选器属性</span><span class="sxs-lookup"><span data-stu-id="07e35-190">Implement a filter attribute</span></span>

<span data-ttu-id="07e35-191">基于属性的内置筛选器 [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) 可以进行子类化。</span><span class="sxs-lookup"><span data-stu-id="07e35-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="07e35-192">以下筛选器向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="07e35-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="07e35-193">以下代码应用 `AddHeader` 属性：</span><span class="sxs-lookup"><span data-stu-id="07e35-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="07e35-194">有关重写顺序的说明，请参阅[重写默认顺序](xref:mvc/controllers/filters#overriding-the-default-order)。</span><span class="sxs-lookup"><span data-stu-id="07e35-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="07e35-195">有关将筛选器管道与筛选器短路的说明，请参阅[取消和设置短路](xref:mvc/controllers/filters#cancellation-and-short-circuiting)。</span><span class="sxs-lookup"><span data-stu-id="07e35-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="07e35-196">授权筛选器属性</span><span class="sxs-lookup"><span data-stu-id="07e35-196">Authorize filter attribute</span></span>

<span data-ttu-id="07e35-197">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性可应用于 `PageModel`：</span><span class="sxs-lookup"><span data-stu-id="07e35-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end