---
title: ASP.NET Core 中的标记帮助程序组件
author: scottaddie
description: 了解标记帮助程序组件的定义及其在 ASP.NET Core 中的用法。
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: fb0bda0cf8d225df4c58ae43f81ed0dce10c1adc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587146"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="6a377-103">ASP.NET Core 中的标记帮助程序组件</span><span class="sxs-lookup"><span data-stu-id="6a377-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="6a377-104">作者：[Scott Addie](https://twitter.com/Scott_Addie) 和 [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span><span class="sxs-lookup"><span data-stu-id="6a377-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="6a377-105">标记帮助程序组件是可用于有条件地修改或添加服务器端代码中的 HTML 元素的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="6a377-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="6a377-106">ASP.NET Core 2.0 或更高版本中提供了此功能。</span><span class="sxs-lookup"><span data-stu-id="6a377-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="6a377-107">ASP.NET Core 包括两个内置标记帮助程序组件：`head` 和 `body`。</span><span class="sxs-lookup"><span data-stu-id="6a377-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="6a377-108">它们位于 <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> 命名空间中，并且可在 MVC 和页面中使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="6a377-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="6a377-109">标记帮助程序组件不需要在 *_ViewImports.cshtml* 中注册应用。</span><span class="sxs-lookup"><span data-stu-id="6a377-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="6a377-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/th-components/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="6a377-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="6a377-111">用例</span><span class="sxs-lookup"><span data-stu-id="6a377-111">Use cases</span></span>

<span data-ttu-id="6a377-112">标记帮助程序组件的两个常见用例包括：</span><span class="sxs-lookup"><span data-stu-id="6a377-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="6a377-113">将 `<link>` 插入 `<head>` 。</span><span class="sxs-lookup"><span data-stu-id="6a377-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="6a377-114">将 `<script>` 插入 `<body>` 。</span><span class="sxs-lookup"><span data-stu-id="6a377-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="6a377-115">以下各节介绍了这些用例。</span><span class="sxs-lookup"><span data-stu-id="6a377-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="6a377-116">注入到 HTML head 元素中</span><span class="sxs-lookup"><span data-stu-id="6a377-116">Inject into HTML head element</span></span>

<span data-ttu-id="6a377-117">在 HTML `<head>` 元素中，通常使用 HTML `<link>` 元素导入 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="6a377-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="6a377-118">以下代码使用 `head` 标记帮助程序组件将 `<link>` 元素注入到 `<head>` 元素中：</span><span class="sxs-lookup"><span data-stu-id="6a377-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="6a377-119">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="6a377-119">In the preceding code:</span></span>

* <span data-ttu-id="6a377-120">`AddressStyleTagHelperComponent` 可实现 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>。</span><span class="sxs-lookup"><span data-stu-id="6a377-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="6a377-121">抽象：</span><span class="sxs-lookup"><span data-stu-id="6a377-121">The abstraction:</span></span>
  * <span data-ttu-id="6a377-122">允许初始化带有 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext> 的类。</span><span class="sxs-lookup"><span data-stu-id="6a377-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="6a377-123">启用标记帮助程序组件以添加或修改 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="6a377-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="6a377-124"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> 属性可定义呈现组件的顺序。</span><span class="sxs-lookup"><span data-stu-id="6a377-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="6a377-125">应用中的标记帮助程序组件有多种用法时，`Order` 是必需的。</span><span class="sxs-lookup"><span data-stu-id="6a377-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="6a377-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> 会将执行上下文的 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> 属性值与 `head` 进行比较。</span><span class="sxs-lookup"><span data-stu-id="6a377-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="6a377-127">如果比较的计算结果为 true，则 `_style` 字段的内容将注入到 HTML `<head>` 元素中。</span><span class="sxs-lookup"><span data-stu-id="6a377-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="6a377-128">注入到 HTML body 元素中</span><span class="sxs-lookup"><span data-stu-id="6a377-128">Inject into HTML body element</span></span>

<span data-ttu-id="6a377-129">`body` 标记帮助程序组件可以将 `<script>` 元素注入到 `<body>` 元素中。</span><span class="sxs-lookup"><span data-stu-id="6a377-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="6a377-130">以下代码演示了此项技术：</span><span class="sxs-lookup"><span data-stu-id="6a377-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="6a377-131">单独的 HTML 文件用于存储 `<script>` 元素。</span><span class="sxs-lookup"><span data-stu-id="6a377-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="6a377-132">HTML 文件使代码更清洁且更易于维护。</span><span class="sxs-lookup"><span data-stu-id="6a377-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="6a377-133">上述代码可读取 *TagHelpers/Templates/AddressToolTipScript.html* 的内容并将其追加到标记帮助程序输出中。</span><span class="sxs-lookup"><span data-stu-id="6a377-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="6a377-134">*AddressToolTipScript.html* 文件包括以下标记：</span><span class="sxs-lookup"><span data-stu-id="6a377-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="6a377-135">上述代码可将[启动工具提示小组件](https://getbootstrap.com/docs/3.3/javascript/#tooltips)绑定到包含 `printable` 属性的任何 `<address>` 元素。</span><span class="sxs-lookup"><span data-stu-id="6a377-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="6a377-136">当鼠标指针悬停在元素上时，可以显示效果。</span><span class="sxs-lookup"><span data-stu-id="6a377-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="6a377-137">注册组件</span><span class="sxs-lookup"><span data-stu-id="6a377-137">Register a Component</span></span>

<span data-ttu-id="6a377-138">标记帮助程序组件必须添加到应用的标记帮助程序组件集合。</span><span class="sxs-lookup"><span data-stu-id="6a377-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="6a377-139">有以下三种方法可添加到集合：</span><span class="sxs-lookup"><span data-stu-id="6a377-139">There are three ways to add to the collection:</span></span>

* [<span data-ttu-id="6a377-140">通过服务容器注册</span><span class="sxs-lookup"><span data-stu-id="6a377-140">Registration via services container</span></span>](#registration-via-services-container)
* [<span data-ttu-id="6a377-141">通过 Razor 文件注册</span><span class="sxs-lookup"><span data-stu-id="6a377-141">Registration via Razor file</span></span>](#registration-via-razor-file)
* [<span data-ttu-id="6a377-142">通过页面模型或控制器注册</span><span class="sxs-lookup"><span data-stu-id="6a377-142">Registration via Page Model or controller</span></span>](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a><span data-ttu-id="6a377-143">通过服务容器注册</span><span class="sxs-lookup"><span data-stu-id="6a377-143">Registration via services container</span></span>

<span data-ttu-id="6a377-144">如果未使用 <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> 管理标记帮助程序组件类，则必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 系统注册。</span><span class="sxs-lookup"><span data-stu-id="6a377-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="6a377-145">以下 `Startup.ConfigureServices` 代码可注册带有[瞬态生存期](xref:fundamentals/dependency-injection#lifetime-and-registration-options)的 `AddressStyleTagHelperComponent` 和 `AddressScriptTagHelperComponent` 类:</span><span class="sxs-lookup"><span data-stu-id="6a377-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="6a377-146">通过 Razor 文件注册</span><span class="sxs-lookup"><span data-stu-id="6a377-146">Registration via Razor file</span></span>

<span data-ttu-id="6a377-147">如果标记帮助程序组件未使用 DI 注册，则可以从 Razor 页面页或 MVC 视图注册。</span><span class="sxs-lookup"><span data-stu-id="6a377-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="6a377-148">此方法用于控制来自文件的注入标记和组件执行顺序 Razor 。</span><span class="sxs-lookup"><span data-stu-id="6a377-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="6a377-149">`ITagHelperComponentManager` 用于添加标记帮助程序组件或从应用中删除这些组件。</span><span class="sxs-lookup"><span data-stu-id="6a377-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="6a377-150">以下代码演示了使用 `AddressTagHelperComponent` 的此项技术：</span><span class="sxs-lookup"><span data-stu-id="6a377-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="6a377-151">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="6a377-151">In the preceding code:</span></span>

* <span data-ttu-id="6a377-152">`@inject` 指令提供了 `ITagHelperComponentManager` 的实例。</span><span class="sxs-lookup"><span data-stu-id="6a377-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="6a377-153">在文件中，将实例分配给一个名 `manager` 为的用于访问的变量 Razor 。</span><span class="sxs-lookup"><span data-stu-id="6a377-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="6a377-154">`AddressTagHelperComponent` 的实例将添加到应用的标记帮助程序组件集合。</span><span class="sxs-lookup"><span data-stu-id="6a377-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="6a377-155">修改 `AddressTagHelperComponent` 以适应接受 `markup` 和 `order` 参数的构造函数：</span><span class="sxs-lookup"><span data-stu-id="6a377-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="6a377-156">提供的 `markup` 参数用于 `ProcessAsync`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6a377-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="6a377-157">通过页面模型或控制器注册</span><span class="sxs-lookup"><span data-stu-id="6a377-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="6a377-158">如果标记帮助程序组件未使用 DI 注册，则可以从 Razor 页面模型或 MVC 控制器注册它。</span><span class="sxs-lookup"><span data-stu-id="6a377-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="6a377-159">此方法可用于分隔文件中的 c # 逻辑 Razor 。</span><span class="sxs-lookup"><span data-stu-id="6a377-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="6a377-160">构造函数注入用于访问 `ITagHelperComponentManager` 的实例。</span><span class="sxs-lookup"><span data-stu-id="6a377-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="6a377-161">标记帮助程序组件将添加到该实例的标记帮助程序组件集合。</span><span class="sxs-lookup"><span data-stu-id="6a377-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="6a377-162">以下 Razor 页面模型演示了此方法 `AddressTagHelperComponent` ：</span><span class="sxs-lookup"><span data-stu-id="6a377-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="6a377-163">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="6a377-163">In the preceding code:</span></span>

* <span data-ttu-id="6a377-164">构造函数注入用于访问 `ITagHelperComponentManager` 的实例。</span><span class="sxs-lookup"><span data-stu-id="6a377-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="6a377-165">`AddressTagHelperComponent` 的实例将添加到应用的标记帮助程序组件集合。</span><span class="sxs-lookup"><span data-stu-id="6a377-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="6a377-166">创建组件</span><span class="sxs-lookup"><span data-stu-id="6a377-166">Create a Component</span></span>

<span data-ttu-id="6a377-167">创建自定义标记帮助程序组件：</span><span class="sxs-lookup"><span data-stu-id="6a377-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="6a377-168">创建派生自 <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> 的公共类。</span><span class="sxs-lookup"><span data-stu-id="6a377-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="6a377-169">将特性应用于 [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) 类。</span><span class="sxs-lookup"><span data-stu-id="6a377-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="6a377-170">指定目标 HTML 元素的名称。</span><span class="sxs-lookup"><span data-stu-id="6a377-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="6a377-171">*可选*：将特性应用于 [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) 类，以在 IntelliSense 中取消显示该类型的显示内容。</span><span class="sxs-lookup"><span data-stu-id="6a377-171">*Optional*: Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="6a377-172">以下代码可创建面向 `<address>` HTML 元素的自定义标记帮助程序组件：</span><span class="sxs-lookup"><span data-stu-id="6a377-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="6a377-173">按如下所示使用自定义 `address` 标记帮助程序组件注入 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="6a377-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="6a377-174">上述 `ProcessAsync` 方法可将向 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> 提供的 HTML 注入到匹配的 `<address>` 元素中。</span><span class="sxs-lookup"><span data-stu-id="6a377-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="6a377-175">在以下情况下进行注入：</span><span class="sxs-lookup"><span data-stu-id="6a377-175">The injection occurs when:</span></span>

* <span data-ttu-id="6a377-176">执行上下文的 `TagName` 属性值等于 `address`。</span><span class="sxs-lookup"><span data-stu-id="6a377-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="6a377-177">相应的 `<address>` 元素具有 `printable` 属性。</span><span class="sxs-lookup"><span data-stu-id="6a377-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="6a377-178">例如，在处理以下 `<address>` 元素时，`if` 语句的计算结果为 true：</span><span class="sxs-lookup"><span data-stu-id="6a377-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="6a377-179">其他资源</span><span class="sxs-lookup"><span data-stu-id="6a377-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
