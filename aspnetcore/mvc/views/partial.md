---
title: ASP.NET Core 中的分部视图
author: ardalis
description: 了解如何使用分部视图来分解大型标记文件，并减少 ASP.NET Core 应用程序中跨网页的常见标记重复情况。
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 0a8e4a4fdecd657840c6c02424ffffa64d4ab473
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586873"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="35e20-103">ASP.NET Core 中的分部视图</span><span class="sxs-lookup"><span data-stu-id="35e20-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="35e20-104">作者：[Steve Smith](https://ardalis.com/)、[Maher JENDOUBI](https://twitter.com/maherjend)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="35e20-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="35e20-105">分部视图是 [Razor](xref:mvc/views/razor) () 的标记文件 ，没有在 [`@page`](xref:mvc/views/razor#page) 其他标记文件呈现的输出 *中* 呈现 HTML 输出的指令。</span><span class="sxs-lookup"><span data-stu-id="35e20-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="35e20-106">当开发 MVC 应用程序时，将使用术语 *分部视图* ，其中标记文件称为 *视图*，或 Razor 页面应用，其中的标记文件被称为 *页*。</span><span class="sxs-lookup"><span data-stu-id="35e20-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="35e20-107">本主题一般将 MVC 视图和 Razor 页面页称为 *标记文件*。</span><span class="sxs-lookup"><span data-stu-id="35e20-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="35e20-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/partial/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="35e20-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="35e20-109">何时使用分部视图</span><span class="sxs-lookup"><span data-stu-id="35e20-109">When to use partial views</span></span>

<span data-ttu-id="35e20-110">分部视图是执行下列操作的有效方式：</span><span class="sxs-lookup"><span data-stu-id="35e20-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="35e20-111">将大型标记文件分解为更小的组件。</span><span class="sxs-lookup"><span data-stu-id="35e20-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="35e20-112">在由多个逻辑部分组成的大型复杂标记文件中，在分部视图中处理隔开的每个部分是有利的。</span><span class="sxs-lookup"><span data-stu-id="35e20-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="35e20-113">标记文件中的代码是可管理的，因为标记仅包含整体页面结构和对分部视图的引用。</span><span class="sxs-lookup"><span data-stu-id="35e20-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="35e20-114">减少跨标记文件中常见标记内容的重复。</span><span class="sxs-lookup"><span data-stu-id="35e20-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="35e20-115">当在标记文件中使用相同的标记元素时，分部视图会将重复的标记内容移到一个分部视图文件中。</span><span class="sxs-lookup"><span data-stu-id="35e20-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="35e20-116">在分部视图中更改标记后，它会更新使用该分部视图的标记文件呈现的输出。</span><span class="sxs-lookup"><span data-stu-id="35e20-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="35e20-117">不应使用分部视图来维护常见布局元素。</span><span class="sxs-lookup"><span data-stu-id="35e20-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="35e20-118">常见布局元素应在 [_Layout.cshtml](xref:mvc/views/layout) 文件中指定。</span><span class="sxs-lookup"><span data-stu-id="35e20-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="35e20-119">请勿使用需要复杂呈现逻辑或代码执行来呈现标记的分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="35e20-120">使用[视图组件](xref:mvc/views/view-components)而不是分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="35e20-121">声明分部视图</span><span class="sxs-lookup"><span data-stu-id="35e20-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="35e20-122">分部视图是未在 [`@page`](xref:mvc/views/razor#page) *Views* 文件夹中维护的指令 (MVC) 或 *页面* 文件夹 (Razor 页面) 。</span><span class="sxs-lookup"><span data-stu-id="35e20-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="35e20-123">在 ASP.NET Core MVC 中，控制器的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 能够返回视图或分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="35e20-124">在 Razor 页中， <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 可以返回以对象形式表示的分部视图 <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> 。</span><span class="sxs-lookup"><span data-stu-id="35e20-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="35e20-125">[引用分部视图](#reference-a-partial-view)部分介绍了引用和呈现分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="35e20-126">与 MVC 视图或页面呈现不同，分部视图不会运行 _ViewStart.cshtml。</span><span class="sxs-lookup"><span data-stu-id="35e20-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="35e20-127">有关 _ViewStart.cshtml 的详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="35e20-127">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="35e20-128">分部视图的文件名通常以下划线 (`_`) 开头。</span><span class="sxs-lookup"><span data-stu-id="35e20-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="35e20-129">虽然未强制要求遵从此命名约定，但它有助于直观地将分部视图与视图和页面区分开来。</span><span class="sxs-lookup"><span data-stu-id="35e20-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="35e20-130">分部视图是在 Views 文件夹中维护的 .cshtml 标记文件。</span><span class="sxs-lookup"><span data-stu-id="35e20-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="35e20-131">控制器的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 能够返回视图或分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="35e20-132">[引用分部视图](#reference-a-partial-view)部分介绍了引用和呈现分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="35e20-133">与 MVC 视图呈现不同，分部视图不会运行 _ViewStart.cshtml。</span><span class="sxs-lookup"><span data-stu-id="35e20-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="35e20-134">有关 _ViewStart.cshtml 的详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="35e20-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="35e20-135">分部视图的文件名通常以下划线 (`_`) 开头。</span><span class="sxs-lookup"><span data-stu-id="35e20-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="35e20-136">虽然未强制要求遵从此命名约定，但它有助于直观地将分部视图与视图区分开来。</span><span class="sxs-lookup"><span data-stu-id="35e20-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="35e20-137">引用分部视图</span><span class="sxs-lookup"><span data-stu-id="35e20-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-razor-pages-pagemodel"></a><span data-ttu-id="35e20-138">在页面中使用分部视图 Razor PageModel</span><span class="sxs-lookup"><span data-stu-id="35e20-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="35e20-139">在 ASP.NET Core 2.0 或2.1 中，以下处理程序方法将 *\_ AuthorPartialRP* 分部视图呈现给响应：</span><span class="sxs-lookup"><span data-stu-id="35e20-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="35e20-140">在 ASP.NET Core 2.2 或更高版本中，处理程序方法也可以调用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> 方法来生成 `PartialViewResult` 对象：</span><span class="sxs-lookup"><span data-stu-id="35e20-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="35e20-141">在标记文件中使用分部视图</span><span class="sxs-lookup"><span data-stu-id="35e20-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="35e20-142">在标记文件中，有多种方法可引用分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="35e20-143">我们建议应用程序使用以下异步呈现方法之一：</span><span class="sxs-lookup"><span data-stu-id="35e20-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="35e20-144">分部标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="35e20-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="35e20-145">异步 HTML 帮助程序</span><span class="sxs-lookup"><span data-stu-id="35e20-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="35e20-146">在标记文件中，有两种方法可引用分部视图：</span><span class="sxs-lookup"><span data-stu-id="35e20-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="35e20-147">异步 HTML 帮助程序</span><span class="sxs-lookup"><span data-stu-id="35e20-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="35e20-148">同步 HTML 帮助程序</span><span class="sxs-lookup"><span data-stu-id="35e20-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="35e20-149">我们建议应用程序使用[异步 HTML 帮助程序](#asynchronous-html-helper)。</span><span class="sxs-lookup"><span data-stu-id="35e20-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="35e20-150">分部标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="35e20-150">Partial Tag Helper</span></span>

<span data-ttu-id="35e20-151">[部分标记帮助](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)程序需要 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="35e20-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="35e20-152">分部标记帮助程序会异步呈现内容并使用类似 HTML 的语法：</span><span class="sxs-lookup"><span data-stu-id="35e20-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="35e20-153">当存在文件扩展名时，标记帮助程序会引用分部视图，该视图必须与调用分部视图的标记文件位于同一文件夹中：</span><span class="sxs-lookup"><span data-stu-id="35e20-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="35e20-154">以下示例从应用程序根目录引用分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="35e20-155">以波形符斜杠 (`~/`) 或斜杠 (`/`) 开头的路径指代应用程序根目录：</span><span class="sxs-lookup"><span data-stu-id="35e20-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="35e20-156">**Razor 页**</span><span class="sxs-lookup"><span data-stu-id="35e20-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="35e20-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="35e20-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="35e20-158">以下示例引用使用相对路径的分部视图：</span><span class="sxs-lookup"><span data-stu-id="35e20-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="35e20-159">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="35e20-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="35e20-160">异步 HTML 帮助程序</span><span class="sxs-lookup"><span data-stu-id="35e20-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="35e20-161">使用 HTML 帮助程序时，最佳做法是使用 <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>。</span><span class="sxs-lookup"><span data-stu-id="35e20-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="35e20-162">`PartialAsync` 返回包含在 <xref:System.Threading.Tasks.Task%601> 中的 <xref:Microsoft.AspNetCore.Html.IHtmlContent> 类型。</span><span class="sxs-lookup"><span data-stu-id="35e20-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="35e20-163">通过在等待的调用前添加 `@` 字符前缀来引用该方法：</span><span class="sxs-lookup"><span data-stu-id="35e20-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="35e20-164">当存在文件扩展名时，HTML 帮助程序会引用分部视图，该视图必须与调用分部视图的标记文件位于同一文件夹中：</span><span class="sxs-lookup"><span data-stu-id="35e20-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="35e20-165">以下示例从应用程序根目录引用分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="35e20-166">以波形符斜杠 (`~/`) 或斜杠 (`/`) 开头的路径指代应用程序根目录：</span><span class="sxs-lookup"><span data-stu-id="35e20-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="35e20-167">**Razor 页**</span><span class="sxs-lookup"><span data-stu-id="35e20-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="35e20-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="35e20-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="35e20-169">以下示例引用使用相对路径的分部视图：</span><span class="sxs-lookup"><span data-stu-id="35e20-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="35e20-170">或者，也可以使用 <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*> 呈现分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="35e20-171">此方法不返回 <xref:Microsoft.AspNetCore.Html.IHtmlContent>。</span><span class="sxs-lookup"><span data-stu-id="35e20-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="35e20-172">它将呈现的输出直接流式传输到响应。</span><span class="sxs-lookup"><span data-stu-id="35e20-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="35e20-173">因为该方法不返回结果，所以必须在代码块中调用该方法 Razor ：</span><span class="sxs-lookup"><span data-stu-id="35e20-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="35e20-174">由于 `RenderPartialAsync` 流式传输呈现的内容，因此在某些情况下它可提供更好的性能。</span><span class="sxs-lookup"><span data-stu-id="35e20-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="35e20-175">在性能起关键作用的情况下，使用两种方法对页面进行基准测试，并使用生成更快响应的方法。</span><span class="sxs-lookup"><span data-stu-id="35e20-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="35e20-176">同步 HTML 帮助程序</span><span class="sxs-lookup"><span data-stu-id="35e20-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="35e20-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> 和 <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> 分别是 `PartialAsync` 和 `RenderPartialAsync` 的同步等效项。</span><span class="sxs-lookup"><span data-stu-id="35e20-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="35e20-178">但不建议使用同步等效项，因为可能会出现死锁的情况。</span><span class="sxs-lookup"><span data-stu-id="35e20-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="35e20-179">同步方法针对以后版本中的删除功能。</span><span class="sxs-lookup"><span data-stu-id="35e20-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="35e20-180">如果需要执行代码，请使用[视图组件](xref:mvc/views/view-components)，而不是使用分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="35e20-181">调用 `Partial` 或 `RenderPartial` 会导致 Visual Studio 分析器警告。</span><span class="sxs-lookup"><span data-stu-id="35e20-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="35e20-182">例如，使用 `Partial` 会产生以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="35e20-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="35e20-183">使用 IHtmlHelper.Partial 可能会导致应用程序死锁。</span><span class="sxs-lookup"><span data-stu-id="35e20-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="35e20-184">考虑使用 &lt;分部&gt; 标记帮助程序或 IHtmlHelper.PartialAsync。</span><span class="sxs-lookup"><span data-stu-id="35e20-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="35e20-185">将对的调用替换为 `@Html.Partial` `@await Html.PartialAsync` 或 [部分标记帮助器](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="35e20-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="35e20-186">有关分部标记帮助程序迁移的详细信息，请参阅[从 HTML 帮助程序迁移](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper)。</span><span class="sxs-lookup"><span data-stu-id="35e20-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="35e20-187">分部视图发现</span><span class="sxs-lookup"><span data-stu-id="35e20-187">Partial view discovery</span></span>

<span data-ttu-id="35e20-188">如果按名称（无文件扩展名）引用分部视图，则按所述顺序搜索以下位置：</span><span class="sxs-lookup"><span data-stu-id="35e20-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="35e20-189">**Razor 页**</span><span class="sxs-lookup"><span data-stu-id="35e20-189">**Razor Pages**</span></span>

1. <span data-ttu-id="35e20-190">当前正在执行页面的文件夹</span><span class="sxs-lookup"><span data-stu-id="35e20-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="35e20-191">该页面文件夹上方的目录图</span><span class="sxs-lookup"><span data-stu-id="35e20-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="35e20-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="35e20-192">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="35e20-193">以下约定适用于分部视图发现：</span><span class="sxs-lookup"><span data-stu-id="35e20-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="35e20-194">当分部视图位于不同的文件夹中时，允许使用具有相同文件名的不同分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="35e20-195">当按名称（无文件扩展名）引用分部视图且分部视图出现在调用方的文件夹和 文件夹中时，调用方文件夹中的分部视图会提供分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="35e20-196">如果调用方文件夹中不存在分部视图，则会从 文件夹中提供分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="35e20-197"> 文件夹中的分部视图称为“共享分部视图”或“默认分部视图”。</span><span class="sxs-lookup"><span data-stu-id="35e20-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="35e20-198">分部视图可以 *链接* &mdash; 到分部视图，如果不是由调用形成循环引用，则可以调用另一个分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="35e20-199">相对路径始终相对于当前文件，而不是相对于文件的根视图或父视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="35e20-200">[Razor](xref:mvc/views/razor) `section` 分部视图中定义的对于父标记文件是不可见的。</span><span class="sxs-lookup"><span data-stu-id="35e20-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="35e20-201">`section` 仅对定义它时所在的分部视图可见。</span><span class="sxs-lookup"><span data-stu-id="35e20-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="35e20-202">通过分部视图访问数据</span><span class="sxs-lookup"><span data-stu-id="35e20-202">Access data from partial views</span></span>

<span data-ttu-id="35e20-203">实例化分部视图时，它会获得父视图的 `ViewData` 字典的副本。</span><span class="sxs-lookup"><span data-stu-id="35e20-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="35e20-204">在分部视图内对数据所做的更新不会保存到父视图中。</span><span class="sxs-lookup"><span data-stu-id="35e20-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="35e20-205">在分部视图中的 `ViewData` 更改会在分部视图返回时丢失。</span><span class="sxs-lookup"><span data-stu-id="35e20-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="35e20-206">以下示例演示如何将 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)的实例传递给分部视图：</span><span class="sxs-lookup"><span data-stu-id="35e20-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="35e20-207">还可将模型传入分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="35e20-208">模型可以是自定义对象。</span><span class="sxs-lookup"><span data-stu-id="35e20-208">The model can be a custom object.</span></span> <span data-ttu-id="35e20-209">你可以使用 `PartialAsync`（向调用方呈现内容块）或 `RenderPartialAsync`（将内容流式传输到输出）传递模型：</span><span class="sxs-lookup"><span data-stu-id="35e20-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="35e20-210">**Razor 页**</span><span class="sxs-lookup"><span data-stu-id="35e20-210">**Razor Pages**</span></span>

<span data-ttu-id="35e20-211">示例应用程序中的以下标记来自 *Pages/ArticlesRP/ReadRP.cshtml* 页面。</span><span class="sxs-lookup"><span data-stu-id="35e20-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="35e20-212">此页包含两个分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-212">The page contains two partial views.</span></span> <span data-ttu-id="35e20-213">第二个分部视图将模型和 `ViewData` 传入分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="35e20-214">`ViewDataDictionary` 构造函数重载可用于传递新 `ViewData` 字典，同时保留现有的 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="35e20-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="35e20-215">Pages/Shared/_AuthorPartialRP.cshtml 是 ReadRP.cshtml 标记文件引用的第一个分部视图：</span><span class="sxs-lookup"><span data-stu-id="35e20-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="35e20-216">Pages/ArticlesRP/_ArticleSectionRP.cshtml 是 ReadRP.cshtml 标记文件引用的第二个分部视图：</span><span class="sxs-lookup"><span data-stu-id="35e20-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="35e20-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="35e20-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="35e20-218">示例应用中的以下标记显示 Views/Articles/Read.cshtml 视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="35e20-219">此视图包含两个分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-219">The view contains two partial views.</span></span> <span data-ttu-id="35e20-220">第二个分部视图将模型和 `ViewData` 传入分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="35e20-221">`ViewDataDictionary` 构造函数重载可用于传递新 `ViewData` 字典，同时保留现有的 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="35e20-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="35e20-222">Views/Shared/_AuthorPartial.cshtml 是 Read.cshtml 标记文件引用的第一个分部视图：</span><span class="sxs-lookup"><span data-stu-id="35e20-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="35e20-223">Views/Articles/_ArticleSection.cshtml 是 Read.cshtml 标记文件引用的第二个分部视图：</span><span class="sxs-lookup"><span data-stu-id="35e20-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="35e20-224">在运行时，分部视图在父标记文件呈现的输出中呈现，而父标记文件本身在共享的 _Layout.cshtml 内呈现。</span><span class="sxs-lookup"><span data-stu-id="35e20-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="35e20-225">第一个分部视图呈现文章作者的姓名和发布日期：</span><span class="sxs-lookup"><span data-stu-id="35e20-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="35e20-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="35e20-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="35e20-227">来自 &lt;共享分部视图文件路径&gt; 的分部视图。</span><span class="sxs-lookup"><span data-stu-id="35e20-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="35e20-228">1863 年 11 月 19 日中午 12:00:00</span><span class="sxs-lookup"><span data-stu-id="35e20-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="35e20-229">第二个分部视图呈现文章的各部分：</span><span class="sxs-lookup"><span data-stu-id="35e20-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="35e20-230">第一节索引：0</span><span class="sxs-lookup"><span data-stu-id="35e20-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="35e20-231">八十七年前...</span><span class="sxs-lookup"><span data-stu-id="35e20-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="35e20-232">第二节索引：1</span><span class="sxs-lookup"><span data-stu-id="35e20-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="35e20-233">如今，我们正在进行一场伟大的内战，考验着......</span><span class="sxs-lookup"><span data-stu-id="35e20-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="35e20-234">第三节索引：2</span><span class="sxs-lookup"><span data-stu-id="35e20-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="35e20-235">然而，从更广泛的意义上说，我们无法奉献...</span><span class="sxs-lookup"><span data-stu-id="35e20-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="35e20-236">其他资源</span><span class="sxs-lookup"><span data-stu-id="35e20-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="35e20-237">ASP.NET Core 的 Razor 语法参考</span><span class="sxs-lookup"><span data-stu-id="35e20-237">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* [<span data-ttu-id="35e20-238">ASP.NET Core 的 Razor 语法参考</span><span class="sxs-lookup"><span data-stu-id="35e20-238">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
