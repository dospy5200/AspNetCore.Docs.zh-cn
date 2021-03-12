---
title: ASP.NET Core 中的定位点标记帮助程序
author: pkellner
description: 了解 ASP.NET Core 定位点标记帮助程序属性以及每个属性在扩展 HTML 定位点标记的行为中所起的作用。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
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
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 2e49c545b0d343475ce44a636a6ae66324f9d3bf
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587198"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="e126b-103">ASP.NET Core 中的定位点标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="e126b-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="e126b-104">作者：[Peter Kellner](https://peterkellner.net) 和 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="e126b-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="e126b-105">[定位点标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper)可通过添加新属性来增强标准的 HTML 定位点 (`<a ... ></a>`) 标记。</span><span class="sxs-lookup"><span data-stu-id="e126b-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="e126b-106">按照约定，属性名称将使用前缀 `asp-`。</span><span class="sxs-lookup"><span data-stu-id="e126b-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="e126b-107">`asp-` 属性的值决定呈现的定位点元素的 `href` 属性值。</span><span class="sxs-lookup"><span data-stu-id="e126b-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="e126b-108">有关标记帮助程序的概述，请参阅 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="e126b-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="e126b-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="e126b-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e126b-110">本文档中的示例均使用 SpeakerController：</span><span class="sxs-lookup"><span data-stu-id="e126b-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="e126b-111">定位点标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="e126b-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="e126b-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="e126b-112">asp-controller</span></span>

<span data-ttu-id="e126b-113">[asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) 属性可分配用于生成 URL 的控制器。</span><span class="sxs-lookup"><span data-stu-id="e126b-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="e126b-114">下面的标记列出了所有发言人：</span><span class="sxs-lookup"><span data-stu-id="e126b-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="e126b-115">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="e126b-116">如果指定了 `asp-controller` 属性，而未指定 `asp-action` 属性，则默认的 `asp-action` 值为与当前正在执行的视图关联的控制器操作。</span><span class="sxs-lookup"><span data-stu-id="e126b-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="e126b-117">如果前面的标记中省略了 `asp-action`，并在 HomeController 的索引视图 (/Home) 中使用了定位点标记帮助程序，则生成的 HTML 为：</span><span class="sxs-lookup"><span data-stu-id="e126b-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="e126b-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="e126b-118">asp-action</span></span>

<span data-ttu-id="e126b-119">[asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) 属性值表示生成的 `href` 属性中包含的控制器操作名称。</span><span class="sxs-lookup"><span data-stu-id="e126b-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="e126b-120">下面的标记可将生成的 `href` 属性值设置为发言人评估页：</span><span class="sxs-lookup"><span data-stu-id="e126b-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="e126b-121">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="e126b-122">如果未指定 `asp-controller` 属性，则使用默认控制器，该控制器调用执行当前视图的视图。</span><span class="sxs-lookup"><span data-stu-id="e126b-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="e126b-123">如果 `asp-action` 属性值为 `Index`，则不向 URL 追加任何操作，从而导致调用默认的 `Index` 操作。</span><span class="sxs-lookup"><span data-stu-id="e126b-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="e126b-124">`asp-controller` 引用的控制器中必须存在指定的（或默认的）操作。</span><span class="sxs-lookup"><span data-stu-id="e126b-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="e126b-125">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="e126b-125">asp-route-{value}</span></span>

<span data-ttu-id="e126b-126">[asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 属性可实现通配符路由前缀。</span><span class="sxs-lookup"><span data-stu-id="e126b-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="e126b-127">占用 `{value}` 占位符的所有值都解释为潜在的路由参数。</span><span class="sxs-lookup"><span data-stu-id="e126b-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="e126b-128">如果找不到默认路由，则将此路由前缀作为请求参数和值追加到生成的 `href` 属性。</span><span class="sxs-lookup"><span data-stu-id="e126b-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="e126b-129">否则，将在路由模板中替换它。</span><span class="sxs-lookup"><span data-stu-id="e126b-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="e126b-130">考虑以下控制器操作：</span><span class="sxs-lookup"><span data-stu-id="e126b-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="e126b-131">在 Startup.Configure 中定义默认路由模板：</span><span class="sxs-lookup"><span data-stu-id="e126b-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="e126b-132">MVC 视图使用操作提供的模型，如下所示：</span><span class="sxs-lookup"><span data-stu-id="e126b-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="e126b-133">默认路由的 `{id?}` 占位符得以匹配。</span><span class="sxs-lookup"><span data-stu-id="e126b-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="e126b-134">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="e126b-135">假设路由前缀不属于匹配路由模板的一部分，如下面的 MVC 视图所示：</span><span class="sxs-lookup"><span data-stu-id="e126b-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="e126b-136">生成以下 HTML，因为匹配的路由中未找到 `speakerid`：</span><span class="sxs-lookup"><span data-stu-id="e126b-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="e126b-137">如果 `asp-controller` 或 `asp-action` 均未指定，则会执行与 `asp-route` 属性中相同的默认处理。</span><span class="sxs-lookup"><span data-stu-id="e126b-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="e126b-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="e126b-138">asp-route</span></span>

<span data-ttu-id="e126b-139">[asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) 属性用于创建直接链接到命名路由的 URL。</span><span class="sxs-lookup"><span data-stu-id="e126b-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="e126b-140">使用[路由属性](xref:mvc/controllers/routing#attribute-routing)，路由可以按 `SpeakerController` 中所示进行命名并用于其 `Evaluations` 操作：</span><span class="sxs-lookup"><span data-stu-id="e126b-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="e126b-141">在下列标记中，`asp-route` 属性引用命名路由：</span><span class="sxs-lookup"><span data-stu-id="e126b-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="e126b-142">定位点标记帮助程序使用 URL /Speaker/Evaluations 生成直接指向该控制器操作的路由。</span><span class="sxs-lookup"><span data-stu-id="e126b-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="e126b-143">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="e126b-144">如果除了 `asp-route`，还指定了 `asp-controller` 或 `asp-action`，则可能不会生成预期的路由。</span><span class="sxs-lookup"><span data-stu-id="e126b-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="e126b-145">为了避免发生路由冲突，不应将 `asp-route` 与 `asp-controller` 和 `asp-action` 属性结合使用。</span><span class="sxs-lookup"><span data-stu-id="e126b-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="e126b-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="e126b-146">asp-all-route-data</span></span>

<span data-ttu-id="e126b-147">[asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) 属性支持创建键值对字典。</span><span class="sxs-lookup"><span data-stu-id="e126b-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="e126b-148">键是参数名称，值是参数值。</span><span class="sxs-lookup"><span data-stu-id="e126b-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="e126b-149">在下面的示例中，将对字典进行初始化并将其传递给 Razor 视图。</span><span class="sxs-lookup"><span data-stu-id="e126b-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="e126b-150">或者，也可以使用模型传入数据。</span><span class="sxs-lookup"><span data-stu-id="e126b-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="e126b-151">前面的代码生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="e126b-152">平展 `asp-all-route-data` 字典，以生成满足重载 `Evaluations` 操作要求的查询字符串：</span><span class="sxs-lookup"><span data-stu-id="e126b-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="e126b-153">如果字典中的任何键匹配路由参数，则将根据需要在路由中替换这些值。</span><span class="sxs-lookup"><span data-stu-id="e126b-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="e126b-154">其他不匹配的值作为请求参数生成。</span><span class="sxs-lookup"><span data-stu-id="e126b-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="e126b-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="e126b-155">asp-fragment</span></span>

<span data-ttu-id="e126b-156">[asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) 属性可定义要追加到 URL 的 URL 片段。</span><span class="sxs-lookup"><span data-stu-id="e126b-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="e126b-157">定位点标记帮助程序添加哈希字符 (#)。</span><span class="sxs-lookup"><span data-stu-id="e126b-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="e126b-158">请考虑以下标记：</span><span class="sxs-lookup"><span data-stu-id="e126b-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="e126b-159">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="e126b-160">生成客户端应用时，哈希标记很有用。</span><span class="sxs-lookup"><span data-stu-id="e126b-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="e126b-161">它们可用于在 JavaScript 中轻松地执行标记和搜索等操作。</span><span class="sxs-lookup"><span data-stu-id="e126b-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="e126b-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="e126b-162">asp-area</span></span>

<span data-ttu-id="e126b-163">[asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) 属性可设置用来设置相应路由的区域名称。</span><span class="sxs-lookup"><span data-stu-id="e126b-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="e126b-164">以下示例展示了 `asp-area` 属性如何导致重新映射路由。</span><span class="sxs-lookup"><span data-stu-id="e126b-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-razor-pages"></a><span data-ttu-id="e126b-165">页面使用量 Razor</span><span class="sxs-lookup"><span data-stu-id="e126b-165">Usage in Razor Pages</span></span>

<span data-ttu-id="e126b-166">Razor ASP.NET Core 2.1 或更高版本中支持页区域。</span><span class="sxs-lookup"><span data-stu-id="e126b-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="e126b-167">考虑以下目录层次结构：</span><span class="sxs-lookup"><span data-stu-id="e126b-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="e126b-168">**{项目名称}**</span><span class="sxs-lookup"><span data-stu-id="e126b-168">**{Project name}**</span></span>
  * <span data-ttu-id="e126b-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="e126b-169">**wwwroot**</span></span>
  * <span data-ttu-id="e126b-170">**Areas**</span><span class="sxs-lookup"><span data-stu-id="e126b-170">**Areas**</span></span>
    * <span data-ttu-id="e126b-171">**会话**</span><span class="sxs-lookup"><span data-stu-id="e126b-171">**Sessions**</span></span>
      * <span data-ttu-id="e126b-172">**页**</span><span class="sxs-lookup"><span data-stu-id="e126b-172">**Pages**</span></span>
        * <span data-ttu-id="e126b-173">*\_Viewstart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e126b-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="e126b-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e126b-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="e126b-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e126b-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="e126b-176">**页**</span><span class="sxs-lookup"><span data-stu-id="e126b-176">**Pages**</span></span>

<span data-ttu-id="e126b-177">用于引用 *会话* 区域 *索引* Razor 页的标记为：</span><span class="sxs-lookup"><span data-stu-id="e126b-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="e126b-178">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="e126b-179">若要在页面应用中支持区域 Razor ，请在中执行以下 `Startup.ConfigureServices` 操作之一：</span><span class="sxs-lookup"><span data-stu-id="e126b-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="e126b-180">将[兼容性版本](xref:mvc/compatibility-version)设置为 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="e126b-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="e126b-181">将 AllowAreas 属性设置为[ Razor PagesOptions](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) `true` ：</span><span class="sxs-lookup"><span data-stu-id="e126b-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="e126b-182">MVC 中的使用情况</span><span class="sxs-lookup"><span data-stu-id="e126b-182">Usage in MVC</span></span>

<span data-ttu-id="e126b-183">考虑以下目录层次结构：</span><span class="sxs-lookup"><span data-stu-id="e126b-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="e126b-184">**{项目名称}**</span><span class="sxs-lookup"><span data-stu-id="e126b-184">**{Project name}**</span></span>
  * <span data-ttu-id="e126b-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="e126b-185">**wwwroot**</span></span>
  * <span data-ttu-id="e126b-186">**Areas**</span><span class="sxs-lookup"><span data-stu-id="e126b-186">**Areas**</span></span>
    * <span data-ttu-id="e126b-187">**Blogs**</span><span class="sxs-lookup"><span data-stu-id="e126b-187">**Blogs**</span></span>
      * <span data-ttu-id="e126b-188">**Controllers**</span><span class="sxs-lookup"><span data-stu-id="e126b-188">**Controllers**</span></span>
        * <span data-ttu-id="e126b-189">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="e126b-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="e126b-190">**视图**</span><span class="sxs-lookup"><span data-stu-id="e126b-190">**Views**</span></span>
        * <span data-ttu-id="e126b-191">**主页**</span><span class="sxs-lookup"><span data-stu-id="e126b-191">**Home**</span></span>
          * <span data-ttu-id="e126b-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e126b-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="e126b-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e126b-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="e126b-194">*\_Viewstart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="e126b-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="e126b-195">**Controllers**</span><span class="sxs-lookup"><span data-stu-id="e126b-195">**Controllers**</span></span>

<span data-ttu-id="e126b-196">如果将 `asp-area` 设置为 “Blogs”，则会为此定位点标记的关联控制器和视图的路由添加目录 Areas/Blogs 作为前缀。</span><span class="sxs-lookup"><span data-stu-id="e126b-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="e126b-197">用于引用 AboutBlog 视图的标记是：</span><span class="sxs-lookup"><span data-stu-id="e126b-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="e126b-198">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="e126b-199">若要在 MVC 应用中支持区域，路由模板必须包含对该区域（如果存在）的引用。</span><span class="sxs-lookup"><span data-stu-id="e126b-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="e126b-200">该模板由 `routes.MapRoute` *Startup.Configu)* 中的方法调用的第二个参数表示：</span><span class="sxs-lookup"><span data-stu-id="e126b-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="e126b-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="e126b-201">asp-protocol</span></span>

<span data-ttu-id="e126b-202">[asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) 属性用于在 URL 中指定协议（比如 `https`）。</span><span class="sxs-lookup"><span data-stu-id="e126b-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="e126b-203">例如：</span><span class="sxs-lookup"><span data-stu-id="e126b-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="e126b-204">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="e126b-205">示例中的主机名为 localhost。</span><span class="sxs-lookup"><span data-stu-id="e126b-205">The host name in the example is localhost.</span></span> <span data-ttu-id="e126b-206">生成 URL 时，定位点标记帮助程序会使用网站的公共域。</span><span class="sxs-lookup"><span data-stu-id="e126b-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="e126b-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="e126b-207">asp-host</span></span>

<span data-ttu-id="e126b-208">[asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) 属性用于在 URL 中指定主机名。</span><span class="sxs-lookup"><span data-stu-id="e126b-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="e126b-209">例如：</span><span class="sxs-lookup"><span data-stu-id="e126b-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="e126b-210">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="e126b-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="e126b-211">asp-page</span></span>

<span data-ttu-id="e126b-212">[Asp 页](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*)属性用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="e126b-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="e126b-213">使用它向特定页设置定位点标记的 `href` 属性值。</span><span class="sxs-lookup"><span data-stu-id="e126b-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="e126b-214">通过在页面名称前面使用正斜杠 (“/”) 作为前缀，可创建 URL。</span><span class="sxs-lookup"><span data-stu-id="e126b-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="e126b-215">以下示例指向 "与会者" Razor 页：</span><span class="sxs-lookup"><span data-stu-id="e126b-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="e126b-216">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="e126b-217">`asp-page` 属性与 `asp-route`、`asp-controller` 和 `asp-action` 属性互斥。</span><span class="sxs-lookup"><span data-stu-id="e126b-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="e126b-218">但是，`asp-page` 可与 `asp-route-{value}` 结合使用以控制路由，如以下标记所示：</span><span class="sxs-lookup"><span data-stu-id="e126b-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="e126b-219">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="e126b-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="e126b-220">asp-page-handler</span></span>

<span data-ttu-id="e126b-221">[Asp 页处理程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*)属性与页一起使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="e126b-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="e126b-222">它用于链接到特定的页处理程序。</span><span class="sxs-lookup"><span data-stu-id="e126b-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="e126b-223">请考虑以下页处理程序：</span><span class="sxs-lookup"><span data-stu-id="e126b-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="e126b-224">页模型的关联标记链接到 `OnGetProfile` 页处理程序。</span><span class="sxs-lookup"><span data-stu-id="e126b-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="e126b-225">注意，`asp-page-handler` 属性值中省略了页处理程序方法名称的 `On<Verb>` 前缀。</span><span class="sxs-lookup"><span data-stu-id="e126b-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="e126b-226">如果方法是异步的，也省略 `Async` 后缀。</span><span class="sxs-lookup"><span data-stu-id="e126b-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="e126b-227">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="e126b-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="e126b-228">其他资源</span><span class="sxs-lookup"><span data-stu-id="e126b-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
