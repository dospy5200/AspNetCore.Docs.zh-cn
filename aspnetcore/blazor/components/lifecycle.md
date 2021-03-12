---
title: ASP.NET Core Blazor 生命周期
author: guardrex
description: 了解如何使用 ASP.NET Core Blazor 应用中的 Razor 组件生命周期方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 6e9d2c3180fb9e4c3e5ccc0b6d8e17183f78d698
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109840"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="bca08-103">ASP.NET Core Blazor 生命周期</span><span class="sxs-lookup"><span data-stu-id="bca08-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="bca08-104">Blazor 框架包括同步和异步生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="bca08-104">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="bca08-105">替代生命周期方法，以在组件初始化和呈现期间对组件执行其他操作。</span><span class="sxs-lookup"><span data-stu-id="bca08-105">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="bca08-106">下图展示的是 Blazor 生命周期。</span><span class="sxs-lookup"><span data-stu-id="bca08-106">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="bca08-107">本文以下部分中的示例定义了生命周期方法。</span><span class="sxs-lookup"><span data-stu-id="bca08-107">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="bca08-108">组件生命周期事件：</span><span class="sxs-lookup"><span data-stu-id="bca08-108">Component lifecycle events:</span></span>

1. <span data-ttu-id="bca08-109">如果组件是第一次呈现在请求上：</span><span class="sxs-lookup"><span data-stu-id="bca08-109">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="bca08-110">创建组件的实例。</span><span class="sxs-lookup"><span data-stu-id="bca08-110">Create the component's instance.</span></span>
   * <span data-ttu-id="bca08-111">执行属性注入。</span><span class="sxs-lookup"><span data-stu-id="bca08-111">Perform property injection.</span></span> <span data-ttu-id="bca08-112">运行 `SetParametersAsync`。</span><span class="sxs-lookup"><span data-stu-id="bca08-112">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="bca08-113">调用 [`OnInitialized{Async}`](#component-initialization-methods)。</span><span class="sxs-lookup"><span data-stu-id="bca08-113">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="bca08-114">如果返回 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bca08-114">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and the component is rendered.</span></span> <span data-ttu-id="bca08-115">如果未返回 <xref:System.Threading.Tasks.Task>，则呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bca08-115">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>
1. <span data-ttu-id="bca08-116">调用 [`OnParametersSet{Async}`](#after-parameters-are-set) 并呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bca08-116">Call [`OnParametersSet{Async}`](#after-parameters-are-set) and render the component.</span></span> <span data-ttu-id="bca08-117">如果 `OnParametersSetAsync` 返回 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bca08-117">If a <xref:System.Threading.Tasks.Task> is returned from `OnParametersSetAsync`, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rerendered.</span></span>

![Blazor 中 Razor 组件的组件生命周期事件](lifecycle/_static/lifecycle1.png)

<span data-ttu-id="bca08-119">文档对象模型 (DOM) 事件处理：</span><span class="sxs-lookup"><span data-stu-id="bca08-119">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="bca08-120">运行事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="bca08-120">The event handler is run.</span></span>
1. <span data-ttu-id="bca08-121">如果返回 <xref:System.Threading.Tasks.Task>，则将等待 <xref:System.Threading.Tasks.Task>，然后呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bca08-121">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="bca08-122">如果未返回 <xref:System.Threading.Tasks.Task>，则呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bca08-122">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

![文档对象模型 (DOM) 事件处理](lifecycle/_static/lifecycle2.png)

<span data-ttu-id="bca08-124">`Render` 生命周期：</span><span class="sxs-lookup"><span data-stu-id="bca08-124">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="bca08-125">避免对组件进行进一步的呈现操作：</span><span class="sxs-lookup"><span data-stu-id="bca08-125">Avoid further rendering operations on the component:</span></span>
   * <span data-ttu-id="bca08-126">在第一次呈现后。</span><span class="sxs-lookup"><span data-stu-id="bca08-126">After the first render.</span></span>
   * <span data-ttu-id="bca08-127">当 [`ShouldRender`](#suppress-ui-refreshing) 为 `false` 时。</span><span class="sxs-lookup"><span data-stu-id="bca08-127">When [`ShouldRender`](#suppress-ui-refreshing) is `false`.</span></span>
1. <span data-ttu-id="bca08-128">生成呈现树差异并呈现组件。</span><span class="sxs-lookup"><span data-stu-id="bca08-128">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="bca08-129">等待 DOM 更新。</span><span class="sxs-lookup"><span data-stu-id="bca08-129">Await the DOM to update.</span></span>
1. <span data-ttu-id="bca08-130">调用 [`OnAfterRender{Async}`](#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="bca08-130">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

![呈现生命周期](lifecycle/_static/lifecycle3.png)

<span data-ttu-id="bca08-132">开发人员调用 [`StateHasChanged`](#state-changes) 会产生呈现。</span><span class="sxs-lookup"><span data-stu-id="bca08-132">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span> <span data-ttu-id="bca08-133">有关详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="bca08-133">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="bca08-134">生命周期方法</span><span class="sxs-lookup"><span data-stu-id="bca08-134">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="bca08-135">设置参数之前</span><span class="sxs-lookup"><span data-stu-id="bca08-135">Before parameters are set</span></span>

<span data-ttu-id="bca08-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 设置由组件的父组件在呈现树或路由参数中提供的参数。</span><span class="sxs-lookup"><span data-stu-id="bca08-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree or from route parameters.</span></span> <span data-ttu-id="bca08-137">通过重写方法，开发人员代码可以直接与 <xref:Microsoft.AspNetCore.Components.ParameterView> 参数交互。</span><span class="sxs-lookup"><span data-stu-id="bca08-137">By overriding the method, developer code can interact directly with the <xref:Microsoft.AspNetCore.Components.ParameterView>'s parameters.</span></span>

<span data-ttu-id="bca08-138">在下面的示例中，如果分析 `Param` 路由参数成功，则 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> 会将 `Param` 参数值分配给 `value`。</span><span class="sxs-lookup"><span data-stu-id="bca08-138">In the following example, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A?displayProperty=nameWithType> assigns the `Param` parameter's value to `value` if parsing a route parameter for `Param` is successful.</span></span> <span data-ttu-id="bca08-139">如果 `value` 不是 `null`，则由组件显示值。</span><span class="sxs-lookup"><span data-stu-id="bca08-139">When `value` isn't `null`, the value is displayed by the component.</span></span>

<span data-ttu-id="bca08-140">尽管[路由参数匹配不区分大小写](xref:blazor/fundamentals/routing#route-parameters)，但 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 仅匹配路由模板中区分大小写的参数名称。</span><span class="sxs-lookup"><span data-stu-id="bca08-140">Although [route parameter matching is case insensitive](xref:blazor/fundamentals/routing#route-parameters), <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> only matches case sensitive parameter names in the route template.</span></span> <span data-ttu-id="bca08-141">以下示例需要使用 `/{Param?}`（而不是 `/{param?}`）来获取值。</span><span class="sxs-lookup"><span data-stu-id="bca08-141">The following example is required to use `/{Param?}`, not `/{param?}`, in order to get the value.</span></span> <span data-ttu-id="bca08-142">如果在此方案中使用 `/{param?}`，则 <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> 返回 `false`，并且 `message` 未设置为任一字符串。</span><span class="sxs-lookup"><span data-stu-id="bca08-142">If `/{param?}` is used in this scenario, <xref:Microsoft.AspNetCore.Components.ParameterView.TryGetValue%2A> returns `false` and `message` isn't set to either string.</span></span>

<span data-ttu-id="bca08-143">`Pages/SetParametersAsyncExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="bca08-143">`Pages/SetParametersAsyncExample.razor`:</span></span>

```razor
@page "/setparametersasync-example/{Param?}"

<h1>SetParametersAsync Example</h1>

<p>@message</p>

@code {
    private string message;

    [Parameter]
    public string Param { get; set; }

    public override async Task SetParametersAsync(ParameterView parameters)
    {
        if (parameters.TryGetValue<string>(nameof(Param), out var value))
        {
            if (value is null)
            {
                message = "The value of 'Param' is null.";
            }
            else
            {
                message = $"The value of 'Param' is {value}.";
            }
        }

        await base.SetParametersAsync(parameters);
    }
}
```

<span data-ttu-id="bca08-144">每次调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 时，<xref:Microsoft.AspNetCore.Components.ParameterView> 都包含该组件的参数值集。</span><span class="sxs-lookup"><span data-stu-id="bca08-144"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="bca08-145"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 的默认实现使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 或 [`[CascadingParameter]` 特性](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)（在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中具有对应的值）设置每个属性的值。</span><span class="sxs-lookup"><span data-stu-id="bca08-145">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]` attribute](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="bca08-146">在 <xref:Microsoft.AspNetCore.Components.ParameterView> 中没有对应值的参数保持不变。</span><span class="sxs-lookup"><span data-stu-id="bca08-146">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="bca08-147">如果未调用 [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A)，则自定义代码可使用任何需要的方式解释传入的参数值。</span><span class="sxs-lookup"><span data-stu-id="bca08-147">If [`base.SetParametersAsync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="bca08-148">例如，不要求将传入参数分配给类的属性。</span><span class="sxs-lookup"><span data-stu-id="bca08-148">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="bca08-149">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="bca08-149">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="bca08-150">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="bca08-150">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="bca08-151">组件初始化方法</span><span class="sxs-lookup"><span data-stu-id="bca08-151">Component initialization methods</span></span>

<span data-ttu-id="bca08-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 中的组件在从其父组件接收初始参数后初始化，此时，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>。</span><span class="sxs-lookup"><span data-stu-id="bca08-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="bca08-153">在组件执行异步操作时使用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>，并应在操作完成后刷新。</span><span class="sxs-lookup"><span data-stu-id="bca08-153">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="bca08-154">对于同步操作，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>：</span><span class="sxs-lookup"><span data-stu-id="bca08-154">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="bca08-155">若要执行异步操作，请替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 并对该操作使用 [`await`](/dotnet/csharp/language-reference/operators/await) 运算符：</span><span class="sxs-lookup"><span data-stu-id="bca08-155">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="bca08-156">[预呈现其内容](xref:blazor/fundamentals/signalr#render-mode)的 Blazor Server 应用调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 两次：</span><span class="sxs-lookup"><span data-stu-id="bca08-156">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/signalr#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> *twice*:</span></span>

* <span data-ttu-id="bca08-157">在组件最初作为页面的一部分静态呈现时调用一次。</span><span class="sxs-lookup"><span data-stu-id="bca08-157">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="bca08-158">在浏览器重新建立与服务器的连接时调用第二次。</span><span class="sxs-lookup"><span data-stu-id="bca08-158">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="bca08-159">为了防止 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中的开发人员代码运行两次，请参阅[预呈现后的有状态重新连接](#stateful-reconnection-after-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="bca08-159">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="bca08-160">在 Blazor Server 应用进行预呈现时，由于尚未建立与浏览器的连接，无法执行调用 JavaScript 等特定操作。</span><span class="sxs-lookup"><span data-stu-id="bca08-160">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="bca08-161">预呈现时，组件可能需要进行不同的呈现。</span><span class="sxs-lookup"><span data-stu-id="bca08-161">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="bca08-162">有关详细信息，请参阅[检测应用何时预呈现](#detect-when-the-app-is-prerendering)部分。</span><span class="sxs-lookup"><span data-stu-id="bca08-162">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="bca08-163">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="bca08-163">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="bca08-164">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="bca08-164">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="bca08-165">设置参数之后</span><span class="sxs-lookup"><span data-stu-id="bca08-165">After parameters are set</span></span>

<span data-ttu-id="bca08-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> 在以下情况下调用：</span><span class="sxs-lookup"><span data-stu-id="bca08-166"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="bca08-167">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> 或 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 中初始化组件后。</span><span class="sxs-lookup"><span data-stu-id="bca08-167">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="bca08-168">当父组件重新呈现并提供以下内容时：</span><span class="sxs-lookup"><span data-stu-id="bca08-168">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="bca08-169">至少一个参数已更改的唯一已知基元不可变类型。</span><span class="sxs-lookup"><span data-stu-id="bca08-169">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="bca08-170">任何复杂类型的参数。</span><span class="sxs-lookup"><span data-stu-id="bca08-170">Any complex-typed parameters.</span></span> <span data-ttu-id="bca08-171">框架无法知道复杂类型参数的值是否在内部发生了改变，因此，它将参数集视为已更改。</span><span class="sxs-lookup"><span data-stu-id="bca08-171">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="bca08-172">应用参数和属性值时，异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="bca08-172">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="bca08-173">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="bca08-173">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="bca08-174">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="bca08-174">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="bca08-175">组件呈现之后</span><span class="sxs-lookup"><span data-stu-id="bca08-175">After component render</span></span>

<span data-ttu-id="bca08-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 在组件完成呈现后调用。</span><span class="sxs-lookup"><span data-stu-id="bca08-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="bca08-177">此时会填充元素和组件引用。</span><span class="sxs-lookup"><span data-stu-id="bca08-177">Element and component references are populated at this point.</span></span> <span data-ttu-id="bca08-178">在此阶段中，可使用呈现的内容执行其他初始化步骤，例如激活对呈现的 DOM 元素进行操作的第三方 JavaScript 库。</span><span class="sxs-lookup"><span data-stu-id="bca08-178">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="bca08-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 的 `firstRender` 参数：</span><span class="sxs-lookup"><span data-stu-id="bca08-179">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="bca08-180">在第一次呈现组件实例时设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="bca08-180">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="bca08-181">可用于确保初始化操作仅执行一次。</span><span class="sxs-lookup"><span data-stu-id="bca08-181">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="bca08-182">呈现后立即进行的异步操作必须在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 生命周期事件期间发生。</span><span class="sxs-lookup"><span data-stu-id="bca08-182">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="bca08-183">即使从 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> 返回 <xref:System.Threading.Tasks.Task>，框架也不会在任务完成后为组件再安排一个呈现循环。</span><span class="sxs-lookup"><span data-stu-id="bca08-183">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="bca08-184">这是为了避免无限呈现循环。</span><span class="sxs-lookup"><span data-stu-id="bca08-184">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="bca08-185">它与其他生命周期方法不同，后者在返回的任务完成后会再安排呈现循环。</span><span class="sxs-lookup"><span data-stu-id="bca08-185">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="bca08-186">在服务器上的预呈现过程中，不会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="bca08-186"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="bca08-187">在预呈现完成后以交互方式呈现组件时，将调用这些方法。</span><span class="sxs-lookup"><span data-stu-id="bca08-187">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="bca08-188">当应用预呈现时：</span><span class="sxs-lookup"><span data-stu-id="bca08-188">When the app prerenders:</span></span>

1. <span data-ttu-id="bca08-189">组件将在服务器上执行，以在 HTTP 响应中生成一些静态 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="bca08-189">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="bca08-190">在此阶段，不会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="bca08-190">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="bca08-191">当 `blazor.server.js` 或 `blazor.webassembly.js` 在浏览器中启动时，组件将以交互呈现模式重新启动。</span><span class="sxs-lookup"><span data-stu-id="bca08-191">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="bca08-192">组件重新启动后，将调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> 和 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>，因为应用不再处于预呈现阶段。</span><span class="sxs-lookup"><span data-stu-id="bca08-192">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="bca08-193">如果设置有事件处理程序，处置时会将其解除挂接。</span><span class="sxs-lookup"><span data-stu-id="bca08-193">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="bca08-194">有关详细信息，请参阅[使用 `IDisposable` 处置组件](#component-disposal-with-idisposable)部分。</span><span class="sxs-lookup"><span data-stu-id="bca08-194">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="bca08-195">禁止 UI 刷新</span><span class="sxs-lookup"><span data-stu-id="bca08-195">Suppress UI refreshing</span></span>

<span data-ttu-id="bca08-196">替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以禁止 UI 刷新。</span><span class="sxs-lookup"><span data-stu-id="bca08-196">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="bca08-197">如果实现返回 `true`，则刷新 UI：</span><span class="sxs-lookup"><span data-stu-id="bca08-197">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="bca08-198">每次呈现组件时都会调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>。</span><span class="sxs-lookup"><span data-stu-id="bca08-198"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="bca08-199">即使 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 被替代，组件也始终在最初呈现。</span><span class="sxs-lookup"><span data-stu-id="bca08-199">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="bca08-200">有关详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>。</span><span class="sxs-lookup"><span data-stu-id="bca08-200">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-rendering-of-component-subtrees>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="bca08-201">状态更改</span><span class="sxs-lookup"><span data-stu-id="bca08-201">State changes</span></span>

<span data-ttu-id="bca08-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 通知组件其状态已更改。</span><span class="sxs-lookup"><span data-stu-id="bca08-202"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="bca08-203">如果适用，调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 会导致组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="bca08-203">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="bca08-204">将自动为 <xref:Microsoft.AspNetCore.Components.EventCallback> 方法调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="bca08-204"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="bca08-205">有关详细信息，请参阅 <xref:blazor/components/event-handling#eventcallback>。</span><span class="sxs-lookup"><span data-stu-id="bca08-205">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="bca08-206">有关详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="bca08-206">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="bca08-207">处理呈现时的不完整异步操作</span><span class="sxs-lookup"><span data-stu-id="bca08-207">Handle incomplete async actions at render</span></span>

<span data-ttu-id="bca08-208">在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。</span><span class="sxs-lookup"><span data-stu-id="bca08-208">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="bca08-209">执行生命周期方法时，对象可能为 `null` 或未完全填充数据。</span><span class="sxs-lookup"><span data-stu-id="bca08-209">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="bca08-210">提供呈现逻辑以确认对象已初始化。</span><span class="sxs-lookup"><span data-stu-id="bca08-210">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="bca08-211">对象为 `null` 时，呈现占位符 UI 元素（例如，加载消息）。</span><span class="sxs-lookup"><span data-stu-id="bca08-211">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="bca08-212">在 Blazor 模板的 `FetchData` 组件中，替代 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 以异步接收预测数据 (`forecasts`)。</span><span class="sxs-lookup"><span data-stu-id="bca08-212">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asynchronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="bca08-213">当 `forecasts` 为 `null` 时，将向用户显示加载消息。</span><span class="sxs-lookup"><span data-stu-id="bca08-213">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="bca08-214"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 返回的 `Task` 完成后，该组件以更新后的状态重新呈现。</span><span class="sxs-lookup"><span data-stu-id="bca08-214">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="bca08-215">Blazor Server 模板中的 `Pages/FetchData.razor`：</span><span class="sxs-lookup"><span data-stu-id="bca08-215">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/components-lifecycle/FetchData.razor?name=snippet&highlight=9,21,25)]

::: moniker-end

## <a name="handle-errors"></a><span data-ttu-id="bca08-216">处理错误</span><span class="sxs-lookup"><span data-stu-id="bca08-216">Handle errors</span></span>

<span data-ttu-id="bca08-217">有关在生命周期方法执行期间处理错误的信息，请参阅 <xref:blazor/fundamentals/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="bca08-217">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="bca08-218">预呈现后的有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="bca08-218">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="bca08-219">在 Blazor Server 应用中，当 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> 时，组件最初作为页面的一部分静态呈现。</span><span class="sxs-lookup"><span data-stu-id="bca08-219">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="bca08-220">浏览器重新建立与服务器的连接后，将 *再次* 呈现组件，并且该组件现在为交互式。</span><span class="sxs-lookup"><span data-stu-id="bca08-220">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="bca08-221">如果存在用于初始化组件的 [`OnInitialized{Async}`](#component-initialization-methods) 生命周期方法，则该方法执行两次：</span><span class="sxs-lookup"><span data-stu-id="bca08-221">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="bca08-222">在静态预呈现组件时执行一次。</span><span class="sxs-lookup"><span data-stu-id="bca08-222">When the component is prerendered statically.</span></span>
* <span data-ttu-id="bca08-223">在建立服务器连接后执行一次。</span><span class="sxs-lookup"><span data-stu-id="bca08-223">After the server connection has been established.</span></span>

<span data-ttu-id="bca08-224">在最终呈现组件时，这可能导致 UI 中显示的数据发生明显变化。</span><span class="sxs-lookup"><span data-stu-id="bca08-224">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="bca08-225">若要避免 Blazor Server 应用中出现双重呈现，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bca08-225">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="bca08-226">传递一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重启后检索状态。</span><span class="sxs-lookup"><span data-stu-id="bca08-226">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="bca08-227">在预呈现期间使用该标识符保存组件状态。</span><span class="sxs-lookup"><span data-stu-id="bca08-227">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="bca08-228">预呈现后使用该标识符检索缓存的状态。</span><span class="sxs-lookup"><span data-stu-id="bca08-228">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="bca08-229">以下代码演示基于模板的 Blazor Server 应用中更新后的 `WeatherForecastService`，其避免了双重呈现：</span><span class="sxs-lookup"><span data-stu-id="bca08-229">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="bca08-230">有关 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> 的详细信息，请参阅 <xref:blazor/fundamentals/signalr#render-mode>。</span><span class="sxs-lookup"><span data-stu-id="bca08-230">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/signalr#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="bca08-231">检测应用何时预呈现</span><span class="sxs-lookup"><span data-stu-id="bca08-231">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/blazor/includes/prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="bca08-232">使用 `IDisposable` 处置组件</span><span class="sxs-lookup"><span data-stu-id="bca08-232">Component disposal with `IDisposable`</span></span>

<span data-ttu-id="bca08-233">如果某个组件实现 <xref:System.IDisposable>，则在从 UI 中删除该组件时，框架将调用[处置方法](/dotnet/standard/garbage-collection/implementing-dispose)，在 UI 中可以释放非托管资源。</span><span class="sxs-lookup"><span data-stu-id="bca08-233">If a component implements <xref:System.IDisposable>, the framework calls the [disposal method](/dotnet/standard/garbage-collection/implementing-dispose) when the component is removed from the UI, where unmanaged resources can be released.</span></span> <span data-ttu-id="bca08-234">可随时进行处置，包括在[组件初始化](#component-initialization-methods)期间。</span><span class="sxs-lookup"><span data-stu-id="bca08-234">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="bca08-235">以下组件通过 [`@implements`](xref:mvc/views/razor#implements) Razor 指令来实现 <xref:System.IDisposable>：</span><span class="sxs-lookup"><span data-stu-id="bca08-235">The following component implements <xref:System.IDisposable> with the [`@implements`](xref:mvc/views/razor#implements) Razor directive:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

<span data-ttu-id="bca08-236">如果对象需要释放，则在调用 <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> 时，可使用 Lambda 来释放对象：</span><span class="sxs-lookup"><span data-stu-id="bca08-236">If an object requires disposal, a lambda can be used to dispose of the object when <xref:System.IDisposable.Dispose%2A?displayProperty=nameWithType> is called:</span></span>

<span data-ttu-id="bca08-237">`Pages/CounterWithTimerDisposal.razor`:</span><span class="sxs-lookup"><span data-stu-id="bca08-237">`Pages/CounterWithTimerDisposal.razor`:</span></span>

```razor
@page "/counter-with-timer-disposal"
@using System.Timers
@implements IDisposable

<h1>Counter with <code>Timer</code> disposal</h1>

<p>Current count: @currentCount</p>

@code {
    private int currentCount = 0;
    private Timer timer = new Timer(1000);

    protected override void OnInitialized()
    {
        timer.Elapsed += (sender, eventArgs) => OnTimerCallback();
        timer.Start();
    }

    private void OnTimerCallback()
    {
        _ = InvokeAsync(() =>
        {
            currentCount++;
            StateHasChanged();
        });
    }

    public void IDisposable.Dispose() => timer.Dispose();
}
```

<span data-ttu-id="bca08-238">前面的示例显示在 <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system> 中。</span><span class="sxs-lookup"><span data-stu-id="bca08-238">The preceding example appears in <xref:blazor/components/rendering#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system>.</span></span>

<span data-ttu-id="bca08-239">对于异步释放任务，请使用 `DisposeAsync` 而不是 <xref:System.IDisposable.Dispose>：</span><span class="sxs-lookup"><span data-stu-id="bca08-239">For asynchronous disposal tasks, use `DisposeAsync` instead of <xref:System.IDisposable.Dispose>:</span></span>

```csharp
public async ValueTask DisposeAsync()
{
    ...
}
```

> [!NOTE]
> <span data-ttu-id="bca08-240">不支持在 `Dispose` 中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="bca08-240">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="bca08-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 可能在拆除呈现器时调用，因此不支持在此时请求 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="bca08-241"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="bca08-242">取消订阅 .NET 事件中的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="bca08-242">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="bca08-243">下面的 [Blazor 窗体](xref:blazor/forms-validation)示例演示如何取消订阅 `Dispose` 方法中的事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="bca08-243">The following [Blazor form](xref:blazor/forms-validation) examples show how to unsubscribe an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="bca08-244">专用字段和 Lambda 方法</span><span class="sxs-lookup"><span data-stu-id="bca08-244">Private field and lambda approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal1.razor?name=snippet&highlight=24,29)]

  ::: moniker-end

* <span data-ttu-id="bca08-245">专用方法</span><span class="sxs-lookup"><span data-stu-id="bca08-245">Private method approach</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/components-lifecycle/EventHandlerDisposal2.razor?name=snippet&highlight=16,26)]

  ::: moniker-end

<span data-ttu-id="bca08-246">使用[匿名函数](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions)、方法或表达式时，无需实现 <xref:System.IDisposable> 和取消订阅委托。</span><span class="sxs-lookup"><span data-stu-id="bca08-246">When [anonymous functions](/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-functions), methods or expressions, are used, it isn't necessary to implement <xref:System.IDisposable> and unsubscribe delegates.</span></span> <span data-ttu-id="bca08-247">但是，当公开事件的对象的生存期长于注册委托的组件的生存期时，不能取消订阅委托是一个问题。</span><span class="sxs-lookup"><span data-stu-id="bca08-247">However, failing to unsubscribe a delegate is a problem **when the object exposing the event outlives the lifetime of the component registering the delegate**.</span></span> <span data-ttu-id="bca08-248">发生这种情况时，会导致内存泄漏，因为已注册的委托使原始对象保持活动状态。</span><span class="sxs-lookup"><span data-stu-id="bca08-248">When this occurs, a memory leak results because the registered delegate keeps the original object alive.</span></span> <span data-ttu-id="bca08-249">因此，仅当你知道事件委托可快速释放时，才使用以下方法。</span><span class="sxs-lookup"><span data-stu-id="bca08-249">Therefore, only use the following approaches when you know that the event delegate disposes quickly.</span></span> <span data-ttu-id="bca08-250">当不确定需要释放的对象的生存期时，请订阅委托方法并正确地释放委托，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="bca08-250">When in doubt about the lifetime of objects that require disposal, subscribe a delegate method and properly dispose the delegate as the preceding examples show.</span></span>

* <span data-ttu-id="bca08-251">匿名 Lambda 方法（无需显式释放）</span><span class="sxs-lookup"><span data-stu-id="bca08-251">Anonymous lambda method approach (explicit disposal not required)</span></span>

  ```csharp
  private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
  {
      formInvalid = !editContext.Validate();
      StateHasChanged();
  }

  protected override void OnInitialized()
  {
      editContext = new EditContext(starship);
      editContext.OnFieldChanged += (s, e) => HandleFieldChanged((editContext)s, e);
  }
  ```

* <span data-ttu-id="bca08-252">匿名 Lambda 表达式方法（无需显式释放）</span><span class="sxs-lookup"><span data-stu-id="bca08-252">Anonymous lambda expression approach (explicit disposal not required)</span></span>

  ```csharp
  private ValidationMessageStore messageStore;

  [CascadingParameter]
  private EditContext CurrentEditContext { get; set; }

  protected override void OnInitialized()
  {
      ...

      messageStore = new ValidationMessageStore(CurrentEditContext);

      CurrentEditContext.OnValidationRequested += (s, e) => messageStore.Clear();
      CurrentEditContext.OnFieldChanged += (s, e) => 
          messageStore.Clear(e.FieldIdentifier);
  }
  ```

  <span data-ttu-id="bca08-253">前面带有匿名 Lambda 表达式的完整代码示例显示在 <xref:blazor/forms-validation#validator-components> 中。</span><span class="sxs-lookup"><span data-stu-id="bca08-253">The full example of the preceding code with anonymous lambda expressions appears in <xref:blazor/forms-validation#validator-components>.</span></span>

<span data-ttu-id="bca08-254">有关详细信息，请参阅[清理非托管资源](/dotnet/standard/garbage-collection/unmanaged)以及后续关于实现 `Dispose` 和 `DisposeAsync` 方法的主题。</span><span class="sxs-lookup"><span data-stu-id="bca08-254">For more information, see [Cleaning up unmanaged resources](/dotnet/standard/garbage-collection/unmanaged) and the topics that follow it on implementing the `Dispose` and `DisposeAsync` methods.</span></span>

## <a name="cancelable-background-work"></a><span data-ttu-id="bca08-255">可取消的后台工作</span><span class="sxs-lookup"><span data-stu-id="bca08-255">Cancelable background work</span></span>

<span data-ttu-id="bca08-256">组件通常会执行长时间运行的后台工作，如进行网络调用 (<xref:System.Net.Http.HttpClient>) 以及与数据库交互。</span><span class="sxs-lookup"><span data-stu-id="bca08-256">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="bca08-257">在几种情况下，最好停止后台工作以节省系统资源。</span><span class="sxs-lookup"><span data-stu-id="bca08-257">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="bca08-258">例如，当用户离开组件时，后台异步操作不会自动停止。</span><span class="sxs-lookup"><span data-stu-id="bca08-258">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="bca08-259">后台工作项可能需要取消的其他原因包括：</span><span class="sxs-lookup"><span data-stu-id="bca08-259">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="bca08-260">正在执行的后台任务由错误的输入数据或处理参数启动。</span><span class="sxs-lookup"><span data-stu-id="bca08-260">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="bca08-261">正在执行的一组后台工作项必须替换为一组新的工作项。</span><span class="sxs-lookup"><span data-stu-id="bca08-261">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="bca08-262">必须更改当前正在执行的任务的优先级。</span><span class="sxs-lookup"><span data-stu-id="bca08-262">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="bca08-263">必须关闭应用才能将其重新部署到服务器。</span><span class="sxs-lookup"><span data-stu-id="bca08-263">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="bca08-264">服务器资源受到限制，需要重新计划后台工作项。</span><span class="sxs-lookup"><span data-stu-id="bca08-264">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="bca08-265">要在组件中实现可取消的后台工作模式：</span><span class="sxs-lookup"><span data-stu-id="bca08-265">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="bca08-266">使用 <xref:System.Threading.CancellationTokenSource> 和 <xref:System.Threading.CancellationToken>。</span><span class="sxs-lookup"><span data-stu-id="bca08-266">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="bca08-267">在[释放组件](#component-disposal-with-idisposable)时，以及需要随时通过手动取消标记进行取消时，请调用 [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) 以指示应取消后台工作。</span><span class="sxs-lookup"><span data-stu-id="bca08-267">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="bca08-268">异步调用返回后，对该标记调用 <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A>。</span><span class="sxs-lookup"><span data-stu-id="bca08-268">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="bca08-269">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="bca08-269">In the following example:</span></span>

* <span data-ttu-id="bca08-270">`await Task.Delay(5000, cts.Token);` 表示长时间运行的异步后台工作。</span><span class="sxs-lookup"><span data-stu-id="bca08-270">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="bca08-271">`BackgroundResourceMethod` 表示如果在调用方法之前释放 `Resource`，则不应启动的长时间运行的后台方法。</span><span class="sxs-lookup"><span data-stu-id="bca08-271">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```

## <a name="blazor-server-reconnection-events"></a><span data-ttu-id="bca08-272">Blazor Server 重新连接事件</span><span class="sxs-lookup"><span data-stu-id="bca08-272">Blazor Server reconnection events</span></span>

<span data-ttu-id="bca08-273">本文所述的组件生命周期事件与 [Blazor Server 的重新连接事件处理程序](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui)分开运行。</span><span class="sxs-lookup"><span data-stu-id="bca08-273">The component lifecycle events covered in this article operate separately from [Blazor Server's reconnection event handlers](xref:blazor/fundamentals/signalr#reflect-the-connection-state-in-the-ui).</span></span> <span data-ttu-id="bca08-274">当 Blazor Server 应用断开其与客户端的 SignalR 连接时，只有 UI 更新会被中断。</span><span class="sxs-lookup"><span data-stu-id="bca08-274">When a Blazor Server app loses its SignalR connection to the client, only UI updates are interrupted.</span></span> <span data-ttu-id="bca08-275">重新建立连接后，将恢复 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="bca08-275">UI updates are resumed when the connection is re-established.</span></span> <span data-ttu-id="bca08-276">有关线路处理程序事件和配置的详细信息，请参阅 <xref:blazor/fundamentals/signalr>。</span><span class="sxs-lookup"><span data-stu-id="bca08-276">For more information on circuit handler events and configuration, see <xref:blazor/fundamentals/signalr>.</span></span>
