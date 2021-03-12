---
title: ASP.NET Core 表单中的标记帮助程序
author: rick-anderson
description: 介绍与表单配合使用的内置标记帮助程序。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: 51e5f2f74493e7f4c18273c8589ed0424a1f2cac
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585976"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="c62e4-103">ASP.NET Core 表单中的标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="c62e4-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[N. Taylor Mullen](https://github.com/NTaylorMullen)、[Dave Paquette](https://twitter.com/Dave_Paquette) 和 [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="c62e4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="c62e4-105">本文档演示如何使用表单和表单中常用的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="c62e4-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="c62e4-106">HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) 元素提供 Web 应用用于向服务器回发数据的主要机制。</span><span class="sxs-lookup"><span data-stu-id="c62e4-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="c62e4-107">本文档的大部分内容介绍[标记帮助程序](tag-helpers/intro.md)及其如何帮助高效创建可靠的 HTML 表单。</span><span class="sxs-lookup"><span data-stu-id="c62e4-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="c62e4-108">建议在阅读本文档前先阅读[标记帮助程序简介](tag-helpers/intro.md)。</span><span class="sxs-lookup"><span data-stu-id="c62e4-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="c62e4-109">在很多情况下，HTML 帮助程序为特定标记帮助程序提供了一种替代方法，但标记帮助程序不会替代 HTML 帮助程序，且并非每个 HTML 帮助程序都有对应的标记帮助程序，认识到这点也很重要。</span><span class="sxs-lookup"><span data-stu-id="c62e4-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="c62e4-110">如果存在 HTML 帮助程序替代项，文中会提到。</span><span class="sxs-lookup"><span data-stu-id="c62e4-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="c62e4-111">表单标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-111">The Form Tag Helper</span></span>

<span data-ttu-id="c62e4-112">[表单](https://www.w3.org/TR/html401/interact/forms.html)标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="c62e4-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="c62e4-113">[\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` 为 MVC 控制器操作或命名路由生成 HTML 特性值</span><span class="sxs-lookup"><span data-stu-id="c62e4-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="c62e4-114">生成隐藏的[请求验证令牌](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)，防止跨站点请求伪造（在 HTTP Post 操作方法中与 `[ValidateAntiForgeryToken]` 属性配合使用时）</span><span class="sxs-lookup"><span data-stu-id="c62e4-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="c62e4-115">提供 `asp-route-<Parameter Name>` 属性，其中 `<Parameter Name>` 添加到路由值。</span><span class="sxs-lookup"><span data-stu-id="c62e4-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="c62e4-116">`Html.BeginForm` 和 `Html.BeginRouteForm` 的 `routeValues` 参数提供类似的功能。</span><span class="sxs-lookup"><span data-stu-id="c62e4-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="c62e4-117">具有 HTML 帮助程序替代项 `Html.BeginForm` 和 `Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="c62e4-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="c62e4-118">示例：</span><span class="sxs-lookup"><span data-stu-id="c62e4-118">Sample:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="c62e4-119">上述表单标记帮助程序生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-119">The Form Tag Helper above generates the following HTML:</span></span>

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="c62e4-120">MVC 运行时通过表单标记帮助程序属性 `asp-controller` 和 `asp-action` 生成 `action` 属性值。</span><span class="sxs-lookup"><span data-stu-id="c62e4-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="c62e4-121">表单标记帮助程序还会生成隐藏的[请求验证令牌](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)，防止跨站点请求伪造（在 HTTP Post 操作方法中与 `[ValidateAntiForgeryToken]` 属性配合使用时）。</span><span class="sxs-lookup"><span data-stu-id="c62e4-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="c62e4-122">保护纯 HTML 表单免受跨站点请求伪造的影响很难，但表单标记帮助程序可提供此服务。</span><span class="sxs-lookup"><span data-stu-id="c62e4-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="c62e4-123">使用命名路由</span><span class="sxs-lookup"><span data-stu-id="c62e4-123">Using a named route</span></span>

<span data-ttu-id="c62e4-124">`asp-route` 标记帮助程序属性还可为 HTML `action` 属性生成标记。</span><span class="sxs-lookup"><span data-stu-id="c62e4-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="c62e4-125">具有名为 `register` 的[路由](../../fundamentals/routing.md)的应用可将以下标记用于注册页：</span><span class="sxs-lookup"><span data-stu-id="c62e4-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="c62e4-126">*Views/Account* 文件夹中的许多视图（在新建使用 *个人用户帐户* 身份验证的 Web 应用时生成）包含 [asp-route-returnurl](xref:mvc/views/working-with-forms) 属性：</span><span class="sxs-lookup"><span data-stu-id="c62e4-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="c62e4-127">使用内置模板时，`returnUrl` 仅会在用户尝试访问授权资源，但未验证身份或未获得授权的情况下自动填充。</span><span class="sxs-lookup"><span data-stu-id="c62e4-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="c62e4-128">如果尝试执行未经授权的访问，安全中间件会使用 `returnUrl` 集将用户重定向至登录页。</span><span class="sxs-lookup"><span data-stu-id="c62e4-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="c62e4-129">窗体操作标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="c62e4-130">窗体操作标记帮助程序在生成的 `<button ...>` 或 `<input type="image" ...>` 标记上生成 `formaction` 属性。</span><span class="sxs-lookup"><span data-stu-id="c62e4-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="c62e4-131">`formaction` 属性控制窗体在何处提交数据。</span><span class="sxs-lookup"><span data-stu-id="c62e4-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="c62e4-132">它绑定到 [\<input>](https://www.w3.org/wiki/HTML/Elements/input) 类型 `image` 和元素的元素 [\<button>](https://www.w3.org/wiki/HTML/Elements/button) 。</span><span class="sxs-lookup"><span data-stu-id="c62e4-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="c62e4-133">窗体操作标记帮助程序允许使用多个 [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` 属性来控制为相应元素生成的 `formaction` 链接。</span><span class="sxs-lookup"><span data-stu-id="c62e4-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="c62e4-134">用于控制 `formaction` 值的受支持的 [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 属性：</span><span class="sxs-lookup"><span data-stu-id="c62e4-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="c62e4-135">属性</span><span class="sxs-lookup"><span data-stu-id="c62e4-135">Attribute</span></span>|<span data-ttu-id="c62e4-136">描述</span><span class="sxs-lookup"><span data-stu-id="c62e4-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="c62e4-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="c62e4-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="c62e4-138">控制器的名称。</span><span class="sxs-lookup"><span data-stu-id="c62e4-138">The name of the controller.</span></span>|
|[<span data-ttu-id="c62e4-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="c62e4-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="c62e4-140">操作方法的名称。</span><span class="sxs-lookup"><span data-stu-id="c62e4-140">The name of the action method.</span></span>|
|[<span data-ttu-id="c62e4-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="c62e4-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="c62e4-142">区域名称。</span><span class="sxs-lookup"><span data-stu-id="c62e4-142">The name of the area.</span></span>|
|[<span data-ttu-id="c62e4-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="c62e4-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="c62e4-144">页的名称 Razor 。</span><span class="sxs-lookup"><span data-stu-id="c62e4-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="c62e4-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="c62e4-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="c62e4-146">Razor页面处理程序的名称。</span><span class="sxs-lookup"><span data-stu-id="c62e4-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="c62e4-147">asp-route</span><span class="sxs-lookup"><span data-stu-id="c62e4-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="c62e4-148">路由的名称。</span><span class="sxs-lookup"><span data-stu-id="c62e4-148">The name of the route.</span></span>|
|[<span data-ttu-id="c62e4-149">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="c62e4-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="c62e4-150">单个 URL 路由值。</span><span class="sxs-lookup"><span data-stu-id="c62e4-150">A single URL route value.</span></span> <span data-ttu-id="c62e4-151">例如，`asp-route-id="1234"`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="c62e4-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="c62e4-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="c62e4-153">所有路由值。</span><span class="sxs-lookup"><span data-stu-id="c62e4-153">All route values.</span></span>|
|[<span data-ttu-id="c62e4-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="c62e4-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="c62e4-155">URL 片段。</span><span class="sxs-lookup"><span data-stu-id="c62e4-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="c62e4-156">提交到控制器示例</span><span class="sxs-lookup"><span data-stu-id="c62e4-156">Submit to controller example</span></span>

<span data-ttu-id="c62e4-157">选中输入或按钮时，下面的标记将窗体提交到 `HomeController` 的 `Index` 操作：</span><span class="sxs-lookup"><span data-stu-id="c62e4-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="c62e4-158">之前的标记将生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="c62e4-159">提交到页示例</span><span class="sxs-lookup"><span data-stu-id="c62e4-159">Submit to page example</span></span>

<span data-ttu-id="c62e4-160">以下标记会将窗体提交给 `About` Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="c62e4-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="c62e4-161">之前的标记将生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="c62e4-162">提交到路由示例</span><span class="sxs-lookup"><span data-stu-id="c62e4-162">Submit to route example</span></span>

<span data-ttu-id="c62e4-163">请考虑使用 `/Home/Test` 终结点：</span><span class="sxs-lookup"><span data-stu-id="c62e4-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="c62e4-164">以下标记将窗体提交到 `/Home/Test` 终结点。</span><span class="sxs-lookup"><span data-stu-id="c62e4-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="c62e4-165">之前的标记将生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="c62e4-166">输入标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-166">The Input Tag Helper</span></span>

<span data-ttu-id="c62e4-167">输入标记帮助程序将 HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) 元素绑定到 razor 视图中的模型表达式。</span><span class="sxs-lookup"><span data-stu-id="c62e4-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="c62e4-168">语法：</span><span class="sxs-lookup"><span data-stu-id="c62e4-168">Syntax:</span></span>

```cshtml
<input asp-for="<Expression Name>">
```

<span data-ttu-id="c62e4-169">输入标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="c62e4-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="c62e4-170">为 `asp-for` 属性中指定的表达式名称生成 `id` 和 `name` HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="c62e4-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="c62e4-171">`asp-for="Property1.Property2"` 等效于 `m => m.Property1.Property2`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="c62e4-172">表达式的名称用于 `asp-for` 属性值。</span><span class="sxs-lookup"><span data-stu-id="c62e4-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="c62e4-173">有关其他信息，请参阅[表达式名称](#expression-names)部分。</span><span class="sxs-lookup"><span data-stu-id="c62e4-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="c62e4-174">根据模型类型和应用于模型属性的[数据注释](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)特性设置 HTML `type` 特性值</span><span class="sxs-lookup"><span data-stu-id="c62e4-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="c62e4-175">如果已经指定，不会覆盖 HTML `type` 属性值</span><span class="sxs-lookup"><span data-stu-id="c62e4-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="c62e4-176">通过应用于模型属性的[数据注释](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)特性生成 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) 验证特性</span><span class="sxs-lookup"><span data-stu-id="c62e4-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="c62e4-177">具有与 `Html.TextBoxFor` 和 `Html.EditorFor` 重叠的 HTML 帮助程序功能。</span><span class="sxs-lookup"><span data-stu-id="c62e4-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="c62e4-178">有关详细信息，请参阅 **输入标记帮助程序的 HTML 帮助程序替代项** 部分。</span><span class="sxs-lookup"><span data-stu-id="c62e4-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="c62e4-179">提供强类型化。</span><span class="sxs-lookup"><span data-stu-id="c62e4-179">Provides strong typing.</span></span> <span data-ttu-id="c62e4-180">如果属性的名称更改，但未更新标记帮助程序，则会收到类似如下内容的错误：</span><span class="sxs-lookup"><span data-stu-id="c62e4-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

<span data-ttu-id="c62e4-181">`Input` 标记帮助程序根据 .NET 类型设置 HTML `type` 属性。</span><span class="sxs-lookup"><span data-stu-id="c62e4-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="c62e4-182">下表列出一些常见的 .NET 类型和生成的 HTML 类型（并未列出每个 .NET 类型）。</span><span class="sxs-lookup"><span data-stu-id="c62e4-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="c62e4-183">.NET 类型</span><span class="sxs-lookup"><span data-stu-id="c62e4-183">.NET type</span></span>|<span data-ttu-id="c62e4-184">输入类型</span><span class="sxs-lookup"><span data-stu-id="c62e4-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="c62e4-185">Bool</span><span class="sxs-lookup"><span data-stu-id="c62e4-185">Bool</span></span>|<span data-ttu-id="c62e4-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="c62e4-186">type="checkbox"</span></span>|
|<span data-ttu-id="c62e4-187">字符串</span><span class="sxs-lookup"><span data-stu-id="c62e4-187">String</span></span>|<span data-ttu-id="c62e4-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="c62e4-188">type="text"</span></span>|
|<span data-ttu-id="c62e4-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="c62e4-189">DateTime</span></span>|<span data-ttu-id="c62e4-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="c62e4-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="c62e4-191">Byte</span><span class="sxs-lookup"><span data-stu-id="c62e4-191">Byte</span></span>|<span data-ttu-id="c62e4-192">type="number"</span><span class="sxs-lookup"><span data-stu-id="c62e4-192">type="number"</span></span>|
|<span data-ttu-id="c62e4-193">int</span><span class="sxs-lookup"><span data-stu-id="c62e4-193">Int</span></span>|<span data-ttu-id="c62e4-194">type="number"</span><span class="sxs-lookup"><span data-stu-id="c62e4-194">type="number"</span></span>|
|<span data-ttu-id="c62e4-195">Single、Double</span><span class="sxs-lookup"><span data-stu-id="c62e4-195">Single, Double</span></span>|<span data-ttu-id="c62e4-196">type="number"</span><span class="sxs-lookup"><span data-stu-id="c62e4-196">type="number"</span></span>|

<span data-ttu-id="c62e4-197">下表显示输入标记帮助程序会映射到特定输入类型的一些常见[数据注释](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter)属性（并未列出每个验证属性）：</span><span class="sxs-lookup"><span data-stu-id="c62e4-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="c62e4-198">属性</span><span class="sxs-lookup"><span data-stu-id="c62e4-198">Attribute</span></span>|<span data-ttu-id="c62e4-199">输入类型</span><span class="sxs-lookup"><span data-stu-id="c62e4-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="c62e4-200">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="c62e4-200">[EmailAddress]</span></span>|<span data-ttu-id="c62e4-201">type="email"</span><span class="sxs-lookup"><span data-stu-id="c62e4-201">type="email"</span></span>|
|<span data-ttu-id="c62e4-202">[Url]</span><span class="sxs-lookup"><span data-stu-id="c62e4-202">[Url]</span></span>|<span data-ttu-id="c62e4-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="c62e4-203">type="url"</span></span>|
|<span data-ttu-id="c62e4-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="c62e4-204">[HiddenInput]</span></span>|<span data-ttu-id="c62e4-205">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="c62e4-205">type="hidden"</span></span>|
|<span data-ttu-id="c62e4-206">[Phone]</span><span class="sxs-lookup"><span data-stu-id="c62e4-206">[Phone]</span></span>|<span data-ttu-id="c62e4-207">type="tel"</span><span class="sxs-lookup"><span data-stu-id="c62e4-207">type="tel"</span></span>|
|<span data-ttu-id="c62e4-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="c62e4-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="c62e4-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="c62e4-209">type="password"</span></span>|
|<span data-ttu-id="c62e4-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="c62e4-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="c62e4-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="c62e4-211">type="date"</span></span>|
|<span data-ttu-id="c62e4-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="c62e4-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="c62e4-213">type="time"</span><span class="sxs-lookup"><span data-stu-id="c62e4-213">type="time"</span></span>|

<span data-ttu-id="c62e4-214">示例：</span><span class="sxs-lookup"><span data-stu-id="c62e4-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="c62e4-215">上述代码生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-215">The code above generates the following HTML:</span></span>

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="c62e4-216">应用于 `Email` 和 `Password` 属性的数据注释在模型中生成元数据。</span><span class="sxs-lookup"><span data-stu-id="c62e4-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="c62e4-217">输入标记帮助程序使用模型元数据并生成 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` 属性（请参阅[模型验证](../models/validation.md)）。</span><span class="sxs-lookup"><span data-stu-id="c62e4-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="c62e4-218">这些属性描述要附加到输入字段的验证程序。</span><span class="sxs-lookup"><span data-stu-id="c62e4-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="c62e4-219">这样可以提供非介入式 HTML5 和 [jQuery](https://jquery.com/) 验证。</span><span class="sxs-lookup"><span data-stu-id="c62e4-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="c62e4-220">不引人注目的特性具有格式 `data-val-rule="Error Message"` ，其中 rule 是验证规则 (如 `data-val-required` 、、等）的名称 `data-val-email` `data-val-maxlength` 。 ) 如果属性中提供错误消息，则该属性将显示为属性的值 `data-val-rule` 。</span><span class="sxs-lookup"><span data-stu-id="c62e4-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="c62e4-221">还有表单 `data-val-ruleName-argumentName="argumentValue"` 的属性，这些属性提供有关规则的其他详细信息，例如，`data-val-maxlength-max="1024"`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="c62e4-222">输入标记帮助程序的 HTML 帮助程序替代项</span><span class="sxs-lookup"><span data-stu-id="c62e4-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="c62e4-223">`Html.TextBox`、`Html.TextBoxFor`、`Html.Editor` 和 `Html.EditorFor` 与输入标记帮助程序的功能存在重叠。</span><span class="sxs-lookup"><span data-stu-id="c62e4-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="c62e4-224">输入标记帮助程序会自动设置 `type` 属性；而 `Html.TextBox` 和 `Html.TextBoxFor` 不会。</span><span class="sxs-lookup"><span data-stu-id="c62e4-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="c62e4-225">`Html.Editor` 和 `Html.EditorFor` 处理集合、复杂对象和模板；而输入标记帮助程序不会。</span><span class="sxs-lookup"><span data-stu-id="c62e4-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="c62e4-226">输入标记帮助程序、`Html.EditorFor` 和 `Html.TextBoxFor` 是强类型（使用 lambda 表达式）；而 `Html.TextBox` 和 `Html.Editor` 不是（使用表达式名称）。</span><span class="sxs-lookup"><span data-stu-id="c62e4-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="c62e4-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="c62e4-227">HtmlAttributes</span></span>

<span data-ttu-id="c62e4-228">`@Html.Editor()` 和 `@Html.EditorFor()` 在执行其默认模板时使用名为 `htmlAttributes` 的特殊 `ViewDataDictionary` 条目。</span><span class="sxs-lookup"><span data-stu-id="c62e4-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="c62e4-229">此行为可选择使用 `additionalViewData` 参数增强。</span><span class="sxs-lookup"><span data-stu-id="c62e4-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="c62e4-230">键“htmlAttributes”区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c62e4-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="c62e4-231">键“htmlAttributes”的处理方式与传递到输入帮助程序的 `htmlAttributes` 对象（例如 `@Html.TextBox()`）的处理方式类似。</span><span class="sxs-lookup"><span data-stu-id="c62e4-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="c62e4-232">表达式名称</span><span class="sxs-lookup"><span data-stu-id="c62e4-232">Expression names</span></span>

<span data-ttu-id="c62e4-233">`asp-for` 属性值是 `ModelExpression`，并且是 lambda 表达式的右侧。</span><span class="sxs-lookup"><span data-stu-id="c62e4-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="c62e4-234">因此，`asp-for="Property1"` 在生成的代码中变成 `m => m.Property1`，这也是无需使用 `Model` 前缀的原因。</span><span class="sxs-lookup"><span data-stu-id="c62e4-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="c62e4-235">“\@”字符可用作内联表达式的开头，并移到 `m.` 前面：</span><span class="sxs-lookup"><span data-stu-id="c62e4-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

<span data-ttu-id="c62e4-236">生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-236">Generates the following:</span></span>

```html
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="c62e4-237">使用集合属性时，`asp-for="CollectionProperty[23].Member"` 在 `i` 具有值 `23` 时生成与 `asp-for="CollectionProperty[i].Member"` 相同的名称。</span><span class="sxs-lookup"><span data-stu-id="c62e4-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="c62e4-238">在 ASP.NET Core MVC 计算 `ModelExpression` 的值时，它会检查多个源，包括 `ModelState`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="c62e4-239">以 `<input type="text" asp-for="@Name">` 为例。</span><span class="sxs-lookup"><span data-stu-id="c62e4-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="c62e4-240">计算出的 `value` 属性是第一个非 null 值，属于：</span><span class="sxs-lookup"><span data-stu-id="c62e4-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="c62e4-241">带有“Name”键的 `ModelState` 条目。</span><span class="sxs-lookup"><span data-stu-id="c62e4-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="c62e4-242">`Model.Name` 表达式的结果。</span><span class="sxs-lookup"><span data-stu-id="c62e4-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="c62e4-243">导航子属性</span><span class="sxs-lookup"><span data-stu-id="c62e4-243">Navigating child properties</span></span>

<span data-ttu-id="c62e4-244">还可使用视图模型的属性路径导航到子属性。</span><span class="sxs-lookup"><span data-stu-id="c62e4-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="c62e4-245">设想一个包含子 `Address` 属性的更复杂的模型类。</span><span class="sxs-lookup"><span data-stu-id="c62e4-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="c62e4-246">在视图中，绑定到 `Address.AddressLine1`：</span><span class="sxs-lookup"><span data-stu-id="c62e4-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="c62e4-247">为 `Address.AddressLine1` 生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="c62e4-248">表达式名称和集合</span><span class="sxs-lookup"><span data-stu-id="c62e4-248">Expression names and Collections</span></span>

<span data-ttu-id="c62e4-249">包含 `Colors` 数组的模型示例：</span><span class="sxs-lookup"><span data-stu-id="c62e4-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="c62e4-250">操作方法：</span><span class="sxs-lookup"><span data-stu-id="c62e4-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

<span data-ttu-id="c62e4-251">下面 Razor 演示了如何访问特定 `Color` 元素：</span><span class="sxs-lookup"><span data-stu-id="c62e4-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="c62e4-252">*Views/Shared/EditorTemplates/String.cshtml* 模板：</span><span class="sxs-lookup"><span data-stu-id="c62e4-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="c62e4-253">使用 `List<T>` 的示例：</span><span class="sxs-lookup"><span data-stu-id="c62e4-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="c62e4-254">下面 Razor 演示如何循环访问集合：</span><span class="sxs-lookup"><span data-stu-id="c62e4-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="c62e4-255">*Views/Shared/EditorTemplates/ToDoItem.cshtml* 模板：</span><span class="sxs-lookup"><span data-stu-id="c62e4-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="c62e4-256">应尽量在 `asp-for` 或 `Html.DisplayFor` 等效上下文中使用值 `foreach`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="c62e4-257">一般情况下，`for` 优于 `foreach`（如果情况允许使用的话），因为它不需要分配枚举器，但在 LINQ 表达式中评估索引器的成本高昂，应最大限度地减少使用它。</span><span class="sxs-lookup"><span data-stu-id="c62e4-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="c62e4-258">上述带有注释的示例代码演示如何将 lambda 表达式替换为 `@` 运算符来访问列表中的每个 `ToDoItem`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="c62e4-259">文本区标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="c62e4-260">`Textarea Tag Helper` 标记帮助程序类似于输入标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="c62e4-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="c62e4-261">`id` `name` 从元素的模型生成和特性，以及数据验证特性 [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) 。</span><span class="sxs-lookup"><span data-stu-id="c62e4-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="c62e4-262">提供强类型化。</span><span class="sxs-lookup"><span data-stu-id="c62e4-262">Provides strong typing.</span></span>

* <span data-ttu-id="c62e4-263">HTML 帮助程序替代项：`Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="c62e4-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="c62e4-264">示例：</span><span class="sxs-lookup"><span data-stu-id="c62e4-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="c62e4-265">生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-265">The following HTML is generated:</span></span>

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="c62e4-266">标签标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-266">The Label Tag Helper</span></span>

* <span data-ttu-id="c62e4-267">`for` [\<label>](https://www.w3.org/wiki/HTML/Elements/label) 为表达式名称生成元素上的标签标题和属性</span><span class="sxs-lookup"><span data-stu-id="c62e4-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="c62e4-268">HTML 帮助程序替代项：`Html.LabelFor`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="c62e4-269">`Label Tag Helper` 通过纯 HTML 标签元素提供如下优势：</span><span class="sxs-lookup"><span data-stu-id="c62e4-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="c62e4-270">可自动从 `Display` 属性中获取描述性标签值。</span><span class="sxs-lookup"><span data-stu-id="c62e4-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="c62e4-271">预期的显示名称可能会随时间变化，`Display` 属性和标签标记帮助程序的组合会在其被使用的所有位置应用 `Display`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="c62e4-272">源代码中的标记更少</span><span class="sxs-lookup"><span data-stu-id="c62e4-272">Less markup in source code</span></span>

* <span data-ttu-id="c62e4-273">模型属性的强类型化。</span><span class="sxs-lookup"><span data-stu-id="c62e4-273">Strong typing with the model property.</span></span>

<span data-ttu-id="c62e4-274">示例：</span><span class="sxs-lookup"><span data-stu-id="c62e4-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="c62e4-275">为 `<label>` 元素生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-275">The following HTML is generated for the `<label>` element:</span></span>

```html
<label for="Email">Email Address</label>
```

<span data-ttu-id="c62e4-276">标签标记帮助程序生成“Email”的 `for` 属性值，即与 `<input>` 元素关联的 ID。</span><span class="sxs-lookup"><span data-stu-id="c62e4-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="c62e4-277">标记帮助程序生成一致的 `id` 和 `for` 元素，方便将其正确关联。</span><span class="sxs-lookup"><span data-stu-id="c62e4-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="c62e4-278">本示例中的描述来自 `Display` 属性。</span><span class="sxs-lookup"><span data-stu-id="c62e4-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="c62e4-279">如果模型不包含 `Display` 特性，描述将为表达式的属性名称。</span><span class="sxs-lookup"><span data-stu-id="c62e4-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="c62e4-280">验证标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="c62e4-281">有两个验证标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="c62e4-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="c62e4-282">`Validation Message Tag Helper`（为模型中的单个属性显示验证消息）和 `Validation Summary Tag Helper`（显示验证错误的摘要）。</span><span class="sxs-lookup"><span data-stu-id="c62e4-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="c62e4-283">`Input Tag Helper` 根据模型类的数据注释属性将 HTML5 客户端验证属性添加到输入元素中。</span><span class="sxs-lookup"><span data-stu-id="c62e4-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="c62e4-284">同时在服务器上执行验证。</span><span class="sxs-lookup"><span data-stu-id="c62e4-284">Validation is also performed on the server.</span></span> <span data-ttu-id="c62e4-285">验证标记帮助程序会在发生验证错误时显示这些错误消息。</span><span class="sxs-lookup"><span data-stu-id="c62e4-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="c62e4-286">验证消息标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="c62e4-287">将 [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` 属性添加到 [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) 元素中，该元素会附加指定模型属性的输入字段中的验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="c62e4-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="c62e4-288">[jQuery](https://jquery.com/) 会在发生客户端验证错误时在 `<span>` 元素中显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="c62e4-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="c62e4-289">还会在服务器上执行验证。</span><span class="sxs-lookup"><span data-stu-id="c62e4-289">Validation also takes place on the server.</span></span> <span data-ttu-id="c62e4-290">客户端可能已禁用 JavaScript，一些验证仅可在服务器端执行。</span><span class="sxs-lookup"><span data-stu-id="c62e4-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="c62e4-291">HTML 帮助程序替代项：`Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="c62e4-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="c62e4-292">`Validation Message Tag Helper` 与 HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) 元素中的 `asp-validation-for` 属性配合使用。</span><span class="sxs-lookup"><span data-stu-id="c62e4-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```cshtml
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="c62e4-293">验证消息标记帮助程序会生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="c62e4-294">对于同一属性，通常在 `Input` 标记帮助程序后使用 `Validation Message Tag Helper`。</span><span class="sxs-lookup"><span data-stu-id="c62e4-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="c62e4-295">这样做可在导致错误的输入附近显示所有验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="c62e4-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="c62e4-296">必须拥有包含正确的 JavaScript 和 [jQuery](https://jquery.com/) 脚本引用的视图才能执行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="c62e4-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="c62e4-297">有关详细信息，请参阅[模型验证](../models/validation.md)。</span><span class="sxs-lookup"><span data-stu-id="c62e4-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="c62e4-298">发生服务器端验证错误时（例如，禁用自定义服务器端验证或客户端验证时），MVC 会将该错误消息作为 `<span>` 元素的主体。</span><span class="sxs-lookup"><span data-stu-id="c62e4-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="c62e4-299">验证摘要标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="c62e4-300">针对具有 `asp-validation-summary` 属性的 `<div>` 元素</span><span class="sxs-lookup"><span data-stu-id="c62e4-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="c62e4-301">HTML 帮助程序替代项：`@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="c62e4-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="c62e4-302">`Validation Summary Tag Helper` 用于显示验证消息的摘要。</span><span class="sxs-lookup"><span data-stu-id="c62e4-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="c62e4-303">`asp-validation-summary` 属性值可以是以下任意值：</span><span class="sxs-lookup"><span data-stu-id="c62e4-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="c62e4-304">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="c62e4-304">asp-validation-summary</span></span>|<span data-ttu-id="c62e4-305">显示的验证消息</span><span class="sxs-lookup"><span data-stu-id="c62e4-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="c62e4-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="c62e4-306">ValidationSummary.All</span></span>|<span data-ttu-id="c62e4-307">属性和模型级别</span><span class="sxs-lookup"><span data-stu-id="c62e4-307">Property and model level</span></span>|
|<span data-ttu-id="c62e4-308">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="c62e4-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="c62e4-309">型号</span><span class="sxs-lookup"><span data-stu-id="c62e4-309">Model</span></span>|
|<span data-ttu-id="c62e4-310">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="c62e4-310">ValidationSummary.None</span></span>|<span data-ttu-id="c62e4-311">无</span><span class="sxs-lookup"><span data-stu-id="c62e4-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="c62e4-312">示例</span><span class="sxs-lookup"><span data-stu-id="c62e4-312">Sample</span></span>

<span data-ttu-id="c62e4-313">在以下示例中，数据模型具有 `DataAnnotation` 属性，在 `<input>` 元素中生成验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="c62e4-313">In the following example, the data model has `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="c62e4-314">验证标记帮助程序会在发生验证错误时显示错误消息：</span><span class="sxs-lookup"><span data-stu-id="c62e4-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="c62e4-315">生成的 HTML（如果模型有效）：</span><span class="sxs-lookup"><span data-stu-id="c62e4-315">The generated HTML (when the model is valid):</span></span>

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="c62e4-316">选择标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="c62e4-316">The Select Tag Helper</span></span>

* <span data-ttu-id="c62e4-317">为模型属性生成 [select](https://www.w3.org/wiki/HTML/Elements/select) 元素和关联的 [option](https://www.w3.org/wiki/HTML/Elements/option) 元素。</span><span class="sxs-lookup"><span data-stu-id="c62e4-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="c62e4-318">具有 HTML 帮助程序替代项 `Html.DropDownListFor` 和 `Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="c62e4-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="c62e4-319">`Select Tag Helper` `asp-for` 为 [select](https://www.w3.org/wiki/HTML/Elements/select) 元素指定模型属性名称，`asp-items` 指定 [option](https://www.w3.org/wiki/HTML/Elements/option) 元素。</span><span class="sxs-lookup"><span data-stu-id="c62e4-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="c62e4-320">例如：</span><span class="sxs-lookup"><span data-stu-id="c62e4-320">For example:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="c62e4-321">示例：</span><span class="sxs-lookup"><span data-stu-id="c62e4-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="c62e4-322">`Index` 方法初始化 `CountryViewModel`，设置选定的国家/地区并将其传递到 `Index` 视图。</span><span class="sxs-lookup"><span data-stu-id="c62e4-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="c62e4-323">HTTP POST `Index` 方法显示选定内容：</span><span class="sxs-lookup"><span data-stu-id="c62e4-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="c62e4-324">`Index` 视图：</span><span class="sxs-lookup"><span data-stu-id="c62e4-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="c62e4-325">生成以下 HTML（选择“CA”时）：</span><span class="sxs-lookup"><span data-stu-id="c62e4-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="c62e4-326">不建议将 `ViewBag` 或 `ViewData` 与选择标记帮助程序配合使用。</span><span class="sxs-lookup"><span data-stu-id="c62e4-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="c62e4-327">视图模型在提供 MVC 元数据方面更可靠且通常更不容易出现问题。</span><span class="sxs-lookup"><span data-stu-id="c62e4-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="c62e4-328">`asp-for` 属性值是特殊情况，它不要求提供 `Model` 前缀，但其他标记帮助程序属性需要该前缀（例如 `asp-items`）</span><span class="sxs-lookup"><span data-stu-id="c62e4-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="c62e4-329">枚举绑定</span><span class="sxs-lookup"><span data-stu-id="c62e4-329">Enum binding</span></span>

<span data-ttu-id="c62e4-330">通常可方便地将 `<select>` 与 `enum` 属性配合使用并通过 `enum` 值生成 `SelectListItem` 元素。</span><span class="sxs-lookup"><span data-stu-id="c62e4-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="c62e4-331">示例：</span><span class="sxs-lookup"><span data-stu-id="c62e4-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="c62e4-332">`GetEnumSelectList` 方法为枚举生成 `SelectList` 对象。</span><span class="sxs-lookup"><span data-stu-id="c62e4-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="c62e4-333">可使用 `Display` 属性标记枚举器列表，以获取更丰富的 UI：</span><span class="sxs-lookup"><span data-stu-id="c62e4-333">You can mark your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="c62e4-334">生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-334">The following HTML is generated:</span></span>

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="c62e4-335">选项组</span><span class="sxs-lookup"><span data-stu-id="c62e4-335">Option Group</span></span>

<span data-ttu-id="c62e4-336">[\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup)当视图模型包含一个或多个对象时，将生成 HTML 元素 `SelectListGroup` 。</span><span class="sxs-lookup"><span data-stu-id="c62e4-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="c62e4-337">`CountryViewModelGroup` 将 `SelectListItem` 元素分组为“North America”组和“Europe”组：</span><span class="sxs-lookup"><span data-stu-id="c62e4-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="c62e4-338">两个组如下所示：</span><span class="sxs-lookup"><span data-stu-id="c62e4-338">The two groups are shown below:</span></span>

![选项组示例](working-with-forms/_static/grp.png)

<span data-ttu-id="c62e4-340">生成的 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-340">The generated HTML:</span></span>

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="c62e4-341">多重选择</span><span class="sxs-lookup"><span data-stu-id="c62e4-341">Multiple select</span></span>

<span data-ttu-id="c62e4-342">如果 `asp-for` 属性中指定的属性为 `IEnumerable`，选择标记帮助程序会自动生成 [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) 属性。</span><span class="sxs-lookup"><span data-stu-id="c62e4-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="c62e4-343">例如，如果给定以下模型：</span><span class="sxs-lookup"><span data-stu-id="c62e4-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="c62e4-344">及以下视图：</span><span class="sxs-lookup"><span data-stu-id="c62e4-344">With the following view:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="c62e4-345">则会生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-345">Generates the following HTML:</span></span>

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="c62e4-346">无选定内容</span><span class="sxs-lookup"><span data-stu-id="c62e4-346">No selection</span></span>

<span data-ttu-id="c62e4-347">如果发现自己在多个页面中使用“未指定”选项，可创建模板用于消除重复的 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="c62e4-348">*Views/Shared/EditorTemplates/CountryViewModel.cshtml* 模板：</span><span class="sxs-lookup"><span data-stu-id="c62e4-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="c62e4-349">添加 HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) 元素的情况并不限于 " *无选择* "。</span><span class="sxs-lookup"><span data-stu-id="c62e4-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="c62e4-350">例如，以下视图和操作方法会生成与上述代码类似的 HTML：</span><span class="sxs-lookup"><span data-stu-id="c62e4-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="c62e4-351">根据当前的 `Country` 值选择正确的 `<option>` 元素（包含 `selected="selected"` 属性）。</span><span class="sxs-lookup"><span data-stu-id="c62e4-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="c62e4-352">其他资源</span><span class="sxs-lookup"><span data-stu-id="c62e4-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="c62e4-353">HTML 窗体元素</span><span class="sxs-lookup"><span data-stu-id="c62e4-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="c62e4-354">请求验证令牌</span><span class="sxs-lookup"><span data-stu-id="c62e4-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="c62e4-355">IAttributeAdapter 接口</span><span class="sxs-lookup"><span data-stu-id="c62e4-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="c62e4-356">本文档的代码片段</span><span class="sxs-lookup"><span data-stu-id="c62e4-356">Code snippets for this document</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/working-with-forms/sample/final)
