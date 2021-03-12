---
title: ASP.NET Core 中的部分标记帮助程序
author: scottaddie
description: 发现 ASP.NET Core 部分标记帮助程序以及每个属性在呈现分部视图时所扮演的角色。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 184901ad0bb6188ed908d41dabf2433c5ca7c1ce
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587159"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="2d01f-103">ASP.NET Core 中的部分标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="2d01f-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="2d01f-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2d01f-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2d01f-105">有关标记帮助程序的概述，请参阅 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="2d01f-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="2d01f-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2d01f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="2d01f-107">“概述”</span><span class="sxs-lookup"><span data-stu-id="2d01f-107">Overview</span></span>

<span data-ttu-id="2d01f-108">部分标记帮助器用于在页面和 MVC 应用中呈现 [分部视图](xref:mvc/views/partial) Razor 。</span><span class="sxs-lookup"><span data-stu-id="2d01f-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="2d01f-109">请考虑：</span><span class="sxs-lookup"><span data-stu-id="2d01f-109">Consider that it:</span></span>

* <span data-ttu-id="2d01f-110">需要 ASP.NET Core 2.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2d01f-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="2d01f-111">是 [HTML 帮助程序语法](xref:mvc/views/partial#reference-a-partial-view)的替代方法。</span><span class="sxs-lookup"><span data-stu-id="2d01f-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="2d01f-112">以异步方式呈现分部视图。</span><span class="sxs-lookup"><span data-stu-id="2d01f-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="2d01f-113">用于呈现分部视图的 HTML 帮助程序选项包括：</span><span class="sxs-lookup"><span data-stu-id="2d01f-113">The HTML Helper options for rendering a partial view include:</span></span>

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="2d01f-114">本文档中的示例均使用产品模型：</span><span class="sxs-lookup"><span data-stu-id="2d01f-114">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="2d01f-115">以下是分部标记帮助程序属性的清单。</span><span class="sxs-lookup"><span data-stu-id="2d01f-115">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="2d01f-116">name</span><span class="sxs-lookup"><span data-stu-id="2d01f-116">name</span></span>

<span data-ttu-id="2d01f-117">需要 `name` 属性。</span><span class="sxs-lookup"><span data-stu-id="2d01f-117">The `name` attribute is required.</span></span> <span data-ttu-id="2d01f-118">它指示要呈现的分部视图的名称或路径。</span><span class="sxs-lookup"><span data-stu-id="2d01f-118">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="2d01f-119">提供分部视图名称时，会启动[视图发现](xref:mvc/views/overview#view-discovery)进程。</span><span class="sxs-lookup"><span data-stu-id="2d01f-119">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="2d01f-120">提供显式路径时，将绕过该进程。</span><span class="sxs-lookup"><span data-stu-id="2d01f-120">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="2d01f-121">有关所有可接受的 `name` 值，请参阅[分部视图发现](xref:mvc/views/partial#partial-view-discovery)。</span><span class="sxs-lookup"><span data-stu-id="2d01f-121">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="2d01f-122">以下标记使用显式路径，指示要从共享文件夹加载 _ProductPartial.cshtml。</span><span class="sxs-lookup"><span data-stu-id="2d01f-122">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="2d01f-123">使用 [for](#for) 属性，将模型传递给分部视图进行绑定。</span><span class="sxs-lookup"><span data-stu-id="2d01f-123">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="2d01f-124">针对</span><span class="sxs-lookup"><span data-stu-id="2d01f-124">for</span></span>

<span data-ttu-id="2d01f-125">`for` 属性分配要根据当前模型评估的 [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression)。</span><span class="sxs-lookup"><span data-stu-id="2d01f-125">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="2d01f-126">`ModelExpression` 推断 `@Model.` 语法。</span><span class="sxs-lookup"><span data-stu-id="2d01f-126">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="2d01f-127">例如，可使用 `for="Product"` 而非 `for="@Model.Product"`。</span><span class="sxs-lookup"><span data-stu-id="2d01f-127">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="2d01f-128">通过使用 `@` 符号定义内联表达式来替代此默认推理行为。</span><span class="sxs-lookup"><span data-stu-id="2d01f-128">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="2d01f-129">以下标记加载 _ProductPartial.cshtml：</span><span class="sxs-lookup"><span data-stu-id="2d01f-129">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="2d01f-130">分部视图绑定到关联页模型的 `Product` 属性：</span><span class="sxs-lookup"><span data-stu-id="2d01f-130">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="2d01f-131">模型</span><span class="sxs-lookup"><span data-stu-id="2d01f-131">model</span></span>

<span data-ttu-id="2d01f-132">`model` 属性分配模型实例，以传递到分部视图。</span><span class="sxs-lookup"><span data-stu-id="2d01f-132">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="2d01f-133">`model` 属性不能与 [for](#for) 属性一起使用。</span><span class="sxs-lookup"><span data-stu-id="2d01f-133">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="2d01f-134">在以下标记中，实例化新的 `Product` 对象并将其传递给 `model` 属性进行绑定：</span><span class="sxs-lookup"><span data-stu-id="2d01f-134">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="2d01f-135">view-data</span><span class="sxs-lookup"><span data-stu-id="2d01f-135">view-data</span></span>

<span data-ttu-id="2d01f-136">`view-data` 属性分配 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)，以传递到分部视图。</span><span class="sxs-lookup"><span data-stu-id="2d01f-136">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="2d01f-137">以下标记使整个 ViewData 集合可访问分部视图：</span><span class="sxs-lookup"><span data-stu-id="2d01f-137">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="2d01f-138">在前面的代码中，`IsNumberReadOnly` 键值设置为 `true` 并添加到 ViewData 集合中。</span><span class="sxs-lookup"><span data-stu-id="2d01f-138">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="2d01f-139">因此，在以下分部视图中可访问 `ViewData["IsNumberReadOnly"]`：</span><span class="sxs-lookup"><span data-stu-id="2d01f-139">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="2d01f-140">在此示例中，`ViewData["IsNumberReadOnly"]` 的值确定 Number 字段是否显示为只读。</span><span class="sxs-lookup"><span data-stu-id="2d01f-140">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="2d01f-141">从 HTML 帮助程序迁移</span><span class="sxs-lookup"><span data-stu-id="2d01f-141">Migrate from an HTML Helper</span></span>

<span data-ttu-id="2d01f-142">请考虑以下异步 HTML 帮助程序示例。</span><span class="sxs-lookup"><span data-stu-id="2d01f-142">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="2d01f-143">循环访问和显示产品集合。</span><span class="sxs-lookup"><span data-stu-id="2d01f-143">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="2d01f-144">依据 `PartialAsync` 方法的第一个参数，加载 _ProductPartial.cshtml 分部视图。</span><span class="sxs-lookup"><span data-stu-id="2d01f-144">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="2d01f-145">`Product` 模型的实例传递给分部视图进行绑定。</span><span class="sxs-lookup"><span data-stu-id="2d01f-145">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="2d01f-146">以下分部标记帮助程序可实现与 `PartialAsync` HTML 帮助程序相同的异步呈现行为。</span><span class="sxs-lookup"><span data-stu-id="2d01f-146">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="2d01f-147">`model` 属性分配有 `Product` 模型实例以绑定到分部视图。</span><span class="sxs-lookup"><span data-stu-id="2d01f-147">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="2d01f-148">其他资源</span><span class="sxs-lookup"><span data-stu-id="2d01f-148">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
