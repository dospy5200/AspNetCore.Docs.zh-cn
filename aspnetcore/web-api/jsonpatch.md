---
title: ASP.NET Core Web API 中的 JSON 修补程序
author: rick-anderson
description: 了解如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: 4ed44a0fca9e0834a78e433cdd48cbd153c58666
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587848"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="89ce5-103">ASP.NET Core Web API 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="89ce5-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="89ce5-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="89ce5-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89ce5-105">本文介绍如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。</span><span class="sxs-lookup"><span data-stu-id="89ce5-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="89ce5-106">包安装</span><span class="sxs-lookup"><span data-stu-id="89ce5-106">Package installation</span></span>

<span data-ttu-id="89ce5-107">若要在应用中启用 JSON 修补程序支持，请完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="89ce5-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="89ce5-108">安装 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="89ce5-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="89ce5-109">更新项目的 `Startup.ConfigureServices` 要调用的方法 <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> 。</span><span class="sxs-lookup"><span data-stu-id="89ce5-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="89ce5-110">例如：</span><span class="sxs-lookup"><span data-stu-id="89ce5-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="89ce5-111">`AddNewtonsoftJson` 与 MVC 服务注册方法兼容：</span><span class="sxs-lookup"><span data-stu-id="89ce5-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="89ce5-112">JSON Patch、AddNewtonsoftJson 和 System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="89ce5-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="89ce5-113">`AddNewtonsoftJson` 替换 `System.Text.Json` 用于格式化 **所有** JSON 内容的基于的输入和输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="89ce5-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="89ce5-114">若要使用添加对 JSON 修补程序的支持 `Newtonsoft.Json` ，而使其他格式化程序保持不变，请按如下所示更新项目的 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="89ce5-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="89ce5-115">前面的代码需要 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 包和以下 `using` 语句：</span><span class="sxs-lookup"><span data-stu-id="89ce5-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="89ce5-116">PATCH HTTP 请求方法</span><span class="sxs-lookup"><span data-stu-id="89ce5-116">PATCH HTTP request method</span></span>

<span data-ttu-id="89ce5-117">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) 方法用于更新现有资源。</span><span class="sxs-lookup"><span data-stu-id="89ce5-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="89ce5-118">它们之间的区别是，PUT 会替换整个资源，而PATCH 仅指定更改。</span><span class="sxs-lookup"><span data-stu-id="89ce5-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="89ce5-119">JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="89ce5-119">JSON Patch</span></span>

<span data-ttu-id="89ce5-120">[JSON 修补程序](https://tools.ietf.org/html/rfc6902)是一种格式，用于指定要应用于资源的更新。</span><span class="sxs-lookup"><span data-stu-id="89ce5-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="89ce5-121">JSON 修补程序文档有一个操作数组。</span><span class="sxs-lookup"><span data-stu-id="89ce5-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="89ce5-122">每个操作标识一种特定类型的更改。</span><span class="sxs-lookup"><span data-stu-id="89ce5-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="89ce5-123">此类更改的示例包括添加数组元素或替换属性值。</span><span class="sxs-lookup"><span data-stu-id="89ce5-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="89ce5-124">例如，以下 JSON 文档表示资源、资源的 JSON 修补文档和应用修补程序操作的结果。</span><span class="sxs-lookup"><span data-stu-id="89ce5-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="89ce5-125">资源示例</span><span class="sxs-lookup"><span data-stu-id="89ce5-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="89ce5-126">JSON 修补程序示例</span><span class="sxs-lookup"><span data-stu-id="89ce5-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="89ce5-127">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="89ce5-127">In the preceding JSON:</span></span>

* <span data-ttu-id="89ce5-128">`op` 属性指示操作的类型。</span><span class="sxs-lookup"><span data-stu-id="89ce5-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="89ce5-129">`path` 属性指示要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="89ce5-130">`value` 属性提供新值。</span><span class="sxs-lookup"><span data-stu-id="89ce5-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="89ce5-131">修补程序之后的资源</span><span class="sxs-lookup"><span data-stu-id="89ce5-131">Resource after patch</span></span>

<span data-ttu-id="89ce5-132">下面是应用上述 JSON 修补程序文档后的资源：</span><span class="sxs-lookup"><span data-stu-id="89ce5-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="89ce5-133">通过向资源应用 JSON 修补程序文档所做的更改是原子的。</span><span class="sxs-lookup"><span data-stu-id="89ce5-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="89ce5-134">如果列表中的任何操作失败，则不会应用列表中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="89ce5-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="89ce5-135">路径语法</span><span class="sxs-lookup"><span data-stu-id="89ce5-135">Path syntax</span></span>

<span data-ttu-id="89ce5-136">操作对象的[路径](https://tools.ietf.org/html/rfc6901)属性的级别之间有斜杠。</span><span class="sxs-lookup"><span data-stu-id="89ce5-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="89ce5-137">例如，`"/address/zipCode"`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="89ce5-138">使用从零开始的索引来指定数组元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="89ce5-139">`addresses` 数组的第一个元素将位于 `/addresses/0`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="89ce5-140">若要到 `add` 数组的末尾，请使用连字符 (`-`) 而不是索引号： `/addresses/-` 。</span><span class="sxs-lookup"><span data-stu-id="89ce5-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="89ce5-141">Operations</span><span class="sxs-lookup"><span data-stu-id="89ce5-141">Operations</span></span>

<span data-ttu-id="89ce5-142">下表显示了 [JSON 修补程序规范](https://tools.ietf.org/html/rfc6902)中定义的支持操作：</span><span class="sxs-lookup"><span data-stu-id="89ce5-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="89ce5-143">操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-143">Operation</span></span>  | <span data-ttu-id="89ce5-144">注意</span><span class="sxs-lookup"><span data-stu-id="89ce5-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="89ce5-145">添加属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-145">Add a property or array element.</span></span> <span data-ttu-id="89ce5-146">对于现有属性：设置值。</span><span class="sxs-lookup"><span data-stu-id="89ce5-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="89ce5-147">删除属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="89ce5-148">与在相同位置后跟 `add` 的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="89ce5-149">与从后跟 `add` 的源到使用源中的值的目标的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="89ce5-150">与到使用源中的值的目标的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="89ce5-151">如果 `path` 处的值 = 提供的 `value`，则返回成功状态代码。</span><span class="sxs-lookup"><span data-stu-id="89ce5-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="89ce5-152">ASP.NET Core 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="89ce5-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="89ce5-153">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 包中提供了 JSON 修补程序的 ASP.NET Core 实现。</span><span class="sxs-lookup"><span data-stu-id="89ce5-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="89ce5-154">操作方法代码</span><span class="sxs-lookup"><span data-stu-id="89ce5-154">Action method code</span></span>

<span data-ttu-id="89ce5-155">在 API 控制器中，JSON 修补程序的操作方法：</span><span class="sxs-lookup"><span data-stu-id="89ce5-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="89ce5-156">使用 `HttpPatch` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="89ce5-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="89ce5-157">接受 `JsonPatchDocument<T>`，通常带有 `[FromBody]`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="89ce5-158">在修补程序文档上调用 `ApplyTo` 以应用更改。</span><span class="sxs-lookup"><span data-stu-id="89ce5-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="89ce5-159">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="89ce5-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="89ce5-160">此示例应用程序中的代码可用于以下 `Customer` 模型：</span><span class="sxs-lookup"><span data-stu-id="89ce5-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="89ce5-161">示例操作方法：</span><span class="sxs-lookup"><span data-stu-id="89ce5-161">The sample action method:</span></span>

* <span data-ttu-id="89ce5-162">构造一个 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="89ce5-163">应用修补程序。</span><span class="sxs-lookup"><span data-stu-id="89ce5-163">Applies the patch.</span></span>
* <span data-ttu-id="89ce5-164">在响应的正文中返回结果。</span><span class="sxs-lookup"><span data-stu-id="89ce5-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="89ce5-165">在实际应用中，该代码将从存储（如数据库）中检索数据并在应用修补程序后更新数据库。</span><span class="sxs-lookup"><span data-stu-id="89ce5-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="89ce5-166">模型状态</span><span class="sxs-lookup"><span data-stu-id="89ce5-166">Model state</span></span>

<span data-ttu-id="89ce5-167">上述操作方法示例调用将模型状态用作其参数之一的 `ApplyTo` 的重载。</span><span class="sxs-lookup"><span data-stu-id="89ce5-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="89ce5-168">使用此选项，可以在响应中收到错误消息。</span><span class="sxs-lookup"><span data-stu-id="89ce5-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="89ce5-169">以下示例显示了 `test` 操作的“400 错误请求”响应的正文：</span><span class="sxs-lookup"><span data-stu-id="89ce5-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="89ce5-170">动态对象</span><span class="sxs-lookup"><span data-stu-id="89ce5-170">Dynamic objects</span></span>

<span data-ttu-id="89ce5-171">以下操作方法示例演示如何将修补程序应用于动态对象：</span><span class="sxs-lookup"><span data-stu-id="89ce5-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="89ce5-172">添加操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-172">The add operation</span></span>

* <span data-ttu-id="89ce5-173">如果 `path` 指向数组元素：将新元素插入到 `path` 指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="89ce5-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="89ce5-174">如果 `path` 指向属性：设置属性值。</span><span class="sxs-lookup"><span data-stu-id="89ce5-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="89ce5-175">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="89ce5-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="89ce5-176">如果要修补的资源是一个动态对象：添加属性。</span><span class="sxs-lookup"><span data-stu-id="89ce5-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="89ce5-177">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="89ce5-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="89ce5-178">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Order` 对象添加到 `Orders` 数组末尾。</span><span class="sxs-lookup"><span data-stu-id="89ce5-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="89ce5-179">删除操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-179">The remove operation</span></span>

* <span data-ttu-id="89ce5-180">如果 `path` 指向数组元素：删除元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="89ce5-181">如果 `path` 指向属性：</span><span class="sxs-lookup"><span data-stu-id="89ce5-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="89ce5-182">如果要修补的资源是一个动态对象：删除属性。</span><span class="sxs-lookup"><span data-stu-id="89ce5-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="89ce5-183">如果要修补的资源是一个静态对象：</span><span class="sxs-lookup"><span data-stu-id="89ce5-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="89ce5-184">如果属性可以为 Null：将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="89ce5-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="89ce5-185">如果属性不可以为 Null，将其设置为 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="89ce5-186">下面的示例将修补文档集设置 `CustomerName` 为 null 并删除 `Orders[0]` ：</span><span class="sxs-lookup"><span data-stu-id="89ce5-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="89ce5-187">替换操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-187">The replace operation</span></span>

<span data-ttu-id="89ce5-188">此操作在功能上与后跟 `add` 的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="89ce5-189">以下示例修补文档设置的值 `CustomerName` ，并将替换 `Orders[0]` 为新的 `Order` 对象：</span><span class="sxs-lookup"><span data-stu-id="89ce5-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="89ce5-190">移动操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-190">The move operation</span></span>

* <span data-ttu-id="89ce5-191">如果 `path` 指向数组元素：将 `from` 元素复制到 `path` 元素的位置，然后对 `from` 元素运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="89ce5-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="89ce5-192">如果 `path` 指向属性：将 `from` 属性的值复制到 `path` 属性，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="89ce5-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="89ce5-193">如果 `path` 指向不存在的属性：</span><span class="sxs-lookup"><span data-stu-id="89ce5-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="89ce5-194">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="89ce5-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="89ce5-195">如果要修补的资源是一个动态对象：将 `from` 属性复制到 `path` 指示的位置，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="89ce5-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="89ce5-196">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="89ce5-196">The following sample patch document:</span></span>

* <span data-ttu-id="89ce5-197">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="89ce5-198">将 `Orders[0].OrderName` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="89ce5-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="89ce5-199">将 `Orders[1]` 移动到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="89ce5-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="89ce5-200">复制操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-200">The copy operation</span></span>

<span data-ttu-id="89ce5-201">此操作在功能上与不包含最后 `remove` 步骤的 `move` 操作相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="89ce5-202">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="89ce5-202">The following sample patch document:</span></span>

* <span data-ttu-id="89ce5-203">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="89ce5-204">将 `Orders[1]` 的副本插入到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="89ce5-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="89ce5-205">测试操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-205">The test operation</span></span>

<span data-ttu-id="89ce5-206">如果 `path` 指示的位置处的值与 `value` 中提供的值不同，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="89ce5-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="89ce5-207">在这种情况下，整个 PATCH 请求会失败，即使修补程序文档中的所有其他操作均成功也是如此。</span><span class="sxs-lookup"><span data-stu-id="89ce5-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="89ce5-208">`test` 操作通常用于在发生并发冲突时阻止更新。</span><span class="sxs-lookup"><span data-stu-id="89ce5-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="89ce5-209">如果 `CustomerName` 的初始值是“John”，则以下示例修补程序文档不起任何作用，因为测试失败：</span><span class="sxs-lookup"><span data-stu-id="89ce5-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="89ce5-210">获取代码</span><span class="sxs-lookup"><span data-stu-id="89ce5-210">Get the code</span></span>

<span data-ttu-id="89ce5-211">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples)。</span><span class="sxs-lookup"><span data-stu-id="89ce5-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="89ce5-212">（[下载方法](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="89ce5-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="89ce5-213">若要测试此示例，请使用以下设置运行应用并发送 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="89ce5-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="89ce5-214">URL： `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="89ce5-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="89ce5-215">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="89ce5-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="89ce5-216">标头：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="89ce5-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="89ce5-217">Body：从 *json* 项目文件夹复制并粘贴一个 json 修补文档示例。</span><span class="sxs-lookup"><span data-stu-id="89ce5-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89ce5-218">其他资源</span><span class="sxs-lookup"><span data-stu-id="89ce5-218">Additional resources</span></span>

* [<span data-ttu-id="89ce5-219">IETF RFC 5789 PATCH 方法规范</span><span class="sxs-lookup"><span data-stu-id="89ce5-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="89ce5-220">IETF RFC 6902 JSON 修补程序规范</span><span class="sxs-lookup"><span data-stu-id="89ce5-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="89ce5-221">IETF RFC 6901 JSON 修补程序路径格式规范</span><span class="sxs-lookup"><span data-stu-id="89ce5-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="89ce5-222">[JSON 路径文档](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="89ce5-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="89ce5-223">包括指向用于创建 JSON 修补程序文档的资源的链接。</span><span class="sxs-lookup"><span data-stu-id="89ce5-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="89ce5-224">ASP.NET Core JSON 修补程序源代码</span><span class="sxs-lookup"><span data-stu-id="89ce5-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89ce5-225">本文介绍如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。</span><span class="sxs-lookup"><span data-stu-id="89ce5-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="89ce5-226">PATCH HTTP 请求方法</span><span class="sxs-lookup"><span data-stu-id="89ce5-226">PATCH HTTP request method</span></span>

<span data-ttu-id="89ce5-227">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) 方法用于更新现有资源。</span><span class="sxs-lookup"><span data-stu-id="89ce5-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="89ce5-228">它们之间的区别是，PUT 会替换整个资源，而PATCH 仅指定更改。</span><span class="sxs-lookup"><span data-stu-id="89ce5-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="89ce5-229">JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="89ce5-229">JSON Patch</span></span>

<span data-ttu-id="89ce5-230">[JSON 修补程序](https://tools.ietf.org/html/rfc6902)是一种格式，用于指定要应用于资源的更新。</span><span class="sxs-lookup"><span data-stu-id="89ce5-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="89ce5-231">JSON 修补程序文档有一个操作数组。</span><span class="sxs-lookup"><span data-stu-id="89ce5-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="89ce5-232">每个操作标识特定类型的更改，例如添加数组元素或替换属性值。</span><span class="sxs-lookup"><span data-stu-id="89ce5-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="89ce5-233">例如，以下 JSON 文档表示资源、资源的 JSON 修补程序文档和应用修补程序操作的结果。</span><span class="sxs-lookup"><span data-stu-id="89ce5-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="89ce5-234">资源示例</span><span class="sxs-lookup"><span data-stu-id="89ce5-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="89ce5-235">JSON 修补程序示例</span><span class="sxs-lookup"><span data-stu-id="89ce5-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="89ce5-236">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="89ce5-236">In the preceding JSON:</span></span>

* <span data-ttu-id="89ce5-237">`op` 属性指示操作的类型。</span><span class="sxs-lookup"><span data-stu-id="89ce5-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="89ce5-238">`path` 属性指示要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="89ce5-239">`value` 属性提供新值。</span><span class="sxs-lookup"><span data-stu-id="89ce5-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="89ce5-240">修补程序之后的资源</span><span class="sxs-lookup"><span data-stu-id="89ce5-240">Resource after patch</span></span>

<span data-ttu-id="89ce5-241">下面是应用上述 JSON 修补程序文档后的资源：</span><span class="sxs-lookup"><span data-stu-id="89ce5-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="89ce5-242">通过将 JSON 修补程序文档应用于资源所做的更改是原子操作：如果列表中的任何操作失败，则不会应用列表中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="89ce5-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="89ce5-243">路径语法</span><span class="sxs-lookup"><span data-stu-id="89ce5-243">Path syntax</span></span>

<span data-ttu-id="89ce5-244">操作对象的[路径](https://tools.ietf.org/html/rfc6901)属性的级别之间有斜杠。</span><span class="sxs-lookup"><span data-stu-id="89ce5-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="89ce5-245">例如，`"/address/zipCode"`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="89ce5-246">使用从零开始的索引来指定数组元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="89ce5-247">`addresses` 数组的第一个元素将位于 `/addresses/0`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="89ce5-248">若要将 `add` 置于数组末尾，请使用连字符 (-)，而不是索引号：`/addresses/-`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="89ce5-249">Operations</span><span class="sxs-lookup"><span data-stu-id="89ce5-249">Operations</span></span>

<span data-ttu-id="89ce5-250">下表显示了 [JSON 修补程序规范](https://tools.ietf.org/html/rfc6902)中定义的支持操作：</span><span class="sxs-lookup"><span data-stu-id="89ce5-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="89ce5-251">操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-251">Operation</span></span>  | <span data-ttu-id="89ce5-252">注意</span><span class="sxs-lookup"><span data-stu-id="89ce5-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="89ce5-253">添加属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-253">Add a property or array element.</span></span> <span data-ttu-id="89ce5-254">对于现有属性：设置值。</span><span class="sxs-lookup"><span data-stu-id="89ce5-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="89ce5-255">删除属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="89ce5-256">与在相同位置后跟 `add` 的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="89ce5-257">与从后跟 `add` 的源到使用源中的值的目标的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="89ce5-258">与到使用源中的值的目标的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="89ce5-259">如果 `path` 处的值 = 提供的 `value`，则返回成功状态代码。</span><span class="sxs-lookup"><span data-stu-id="89ce5-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="89ce5-260">ASP.NET Core 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="89ce5-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="89ce5-261">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 包中提供了 JSON 修补程序的 ASP.NET Core 实现。</span><span class="sxs-lookup"><span data-stu-id="89ce5-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="89ce5-262">此包包含在 [AspnetCore](xref:fundamentals/metapackage-app) 元包中。</span><span class="sxs-lookup"><span data-stu-id="89ce5-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="89ce5-263">操作方法代码</span><span class="sxs-lookup"><span data-stu-id="89ce5-263">Action method code</span></span>

<span data-ttu-id="89ce5-264">在 API 控制器中，JSON 修补程序的操作方法：</span><span class="sxs-lookup"><span data-stu-id="89ce5-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="89ce5-265">使用 `HttpPatch` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="89ce5-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="89ce5-266">接受 `JsonPatchDocument<T>`，通常带有 `[FromBody]`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="89ce5-267">在修补程序文档上调用 `ApplyTo` 以应用更改。</span><span class="sxs-lookup"><span data-stu-id="89ce5-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="89ce5-268">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="89ce5-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="89ce5-269">示例应用中的此代码适用于以下 `Customer` 模型。</span><span class="sxs-lookup"><span data-stu-id="89ce5-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="89ce5-270">示例操作方法：</span><span class="sxs-lookup"><span data-stu-id="89ce5-270">The sample action method:</span></span>

* <span data-ttu-id="89ce5-271">构造一个 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="89ce5-272">应用修补程序。</span><span class="sxs-lookup"><span data-stu-id="89ce5-272">Applies the patch.</span></span>
* <span data-ttu-id="89ce5-273">在响应的正文中返回结果。</span><span class="sxs-lookup"><span data-stu-id="89ce5-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="89ce5-274">在实际应用中，该代码将从存储（如数据库）中检索数据并在应用修补程序后更新数据库。</span><span class="sxs-lookup"><span data-stu-id="89ce5-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="89ce5-275">模型状态</span><span class="sxs-lookup"><span data-stu-id="89ce5-275">Model state</span></span>

<span data-ttu-id="89ce5-276">上述操作方法示例调用将模型状态用作其参数之一的 `ApplyTo` 的重载。</span><span class="sxs-lookup"><span data-stu-id="89ce5-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="89ce5-277">使用此选项，可以在响应中收到错误消息。</span><span class="sxs-lookup"><span data-stu-id="89ce5-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="89ce5-278">以下示例显示了 `test` 操作的“400 错误请求”响应的正文：</span><span class="sxs-lookup"><span data-stu-id="89ce5-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="89ce5-279">动态对象</span><span class="sxs-lookup"><span data-stu-id="89ce5-279">Dynamic objects</span></span>

<span data-ttu-id="89ce5-280">以下操作方法示例演示如何将修补程序应用于动态对象。</span><span class="sxs-lookup"><span data-stu-id="89ce5-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="89ce5-281">添加操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-281">The add operation</span></span>

* <span data-ttu-id="89ce5-282">如果 `path` 指向数组元素：将新元素插入到 `path` 指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="89ce5-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="89ce5-283">如果 `path` 指向属性：设置属性值。</span><span class="sxs-lookup"><span data-stu-id="89ce5-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="89ce5-284">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="89ce5-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="89ce5-285">如果要修补的资源是一个动态对象：添加属性。</span><span class="sxs-lookup"><span data-stu-id="89ce5-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="89ce5-286">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="89ce5-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="89ce5-287">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Order` 对象添加到 `Orders` 数组末尾。</span><span class="sxs-lookup"><span data-stu-id="89ce5-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="89ce5-288">删除操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-288">The remove operation</span></span>

* <span data-ttu-id="89ce5-289">如果 `path` 指向数组元素：删除元素。</span><span class="sxs-lookup"><span data-stu-id="89ce5-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="89ce5-290">如果 `path` 指向属性：</span><span class="sxs-lookup"><span data-stu-id="89ce5-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="89ce5-291">如果要修补的资源是一个动态对象：删除属性。</span><span class="sxs-lookup"><span data-stu-id="89ce5-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="89ce5-292">如果要修补的资源是一个静态对象：</span><span class="sxs-lookup"><span data-stu-id="89ce5-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="89ce5-293">如果属性可以为 Null：将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="89ce5-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="89ce5-294">如果属性不可以为 Null，将其设置为 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="89ce5-295">以下示例修补程序文档将 `CustomerName` 设置为 null 并删除 `Orders[0]`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="89ce5-296">替换操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-296">The replace operation</span></span>

<span data-ttu-id="89ce5-297">此操作在功能上与后跟 `add` 的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="89ce5-298">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Orders[0]` 替换为新的 `Order` 对象。</span><span class="sxs-lookup"><span data-stu-id="89ce5-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="89ce5-299">移动操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-299">The move operation</span></span>

* <span data-ttu-id="89ce5-300">如果 `path` 指向数组元素：将 `from` 元素复制到 `path` 元素的位置，然后对 `from` 元素运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="89ce5-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="89ce5-301">如果 `path` 指向属性：将 `from` 属性的值复制到 `path` 属性，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="89ce5-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="89ce5-302">如果 `path` 指向不存在的属性：</span><span class="sxs-lookup"><span data-stu-id="89ce5-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="89ce5-303">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="89ce5-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="89ce5-304">如果要修补的资源是一个动态对象：将 `from` 属性复制到 `path` 指示的位置，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="89ce5-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="89ce5-305">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="89ce5-305">The following sample patch document:</span></span>

* <span data-ttu-id="89ce5-306">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="89ce5-307">将 `Orders[0].OrderName` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="89ce5-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="89ce5-308">将 `Orders[1]` 移动到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="89ce5-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="89ce5-309">复制操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-309">The copy operation</span></span>

<span data-ttu-id="89ce5-310">此操作在功能上与不包含最后 `remove` 步骤的 `move` 操作相同。</span><span class="sxs-lookup"><span data-stu-id="89ce5-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="89ce5-311">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="89ce5-311">The following sample patch document:</span></span>

* <span data-ttu-id="89ce5-312">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="89ce5-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="89ce5-313">将 `Orders[1]` 的副本插入到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="89ce5-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="89ce5-314">测试操作</span><span class="sxs-lookup"><span data-stu-id="89ce5-314">The test operation</span></span>

<span data-ttu-id="89ce5-315">如果 `path` 指示的位置处的值与 `value` 中提供的值不同，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="89ce5-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="89ce5-316">在这种情况下，整个 PATCH 请求会失败，即使修补程序文档中的所有其他操作均成功也是如此。</span><span class="sxs-lookup"><span data-stu-id="89ce5-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="89ce5-317">`test` 操作通常用于在发生并发冲突时阻止更新。</span><span class="sxs-lookup"><span data-stu-id="89ce5-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="89ce5-318">如果 `CustomerName` 的初始值是“John”，则以下示例修补程序文档不起任何作用，因为测试失败：</span><span class="sxs-lookup"><span data-stu-id="89ce5-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="89ce5-319">获取代码</span><span class="sxs-lookup"><span data-stu-id="89ce5-319">Get the code</span></span>

<span data-ttu-id="89ce5-320">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2)。</span><span class="sxs-lookup"><span data-stu-id="89ce5-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="89ce5-321">（[下载方法](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="89ce5-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="89ce5-322">若要测试此示例，请使用以下设置运行应用并发送 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="89ce5-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="89ce5-323">URL： `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="89ce5-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="89ce5-324">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="89ce5-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="89ce5-325">标头：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="89ce5-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="89ce5-326">Body：从 *json* 项目文件夹复制并粘贴一个 json 修补文档示例。</span><span class="sxs-lookup"><span data-stu-id="89ce5-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="89ce5-327">其他资源</span><span class="sxs-lookup"><span data-stu-id="89ce5-327">Additional resources</span></span>

* [<span data-ttu-id="89ce5-328">IETF RFC 5789 PATCH 方法规范</span><span class="sxs-lookup"><span data-stu-id="89ce5-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="89ce5-329">IETF RFC 6902 JSON 修补程序规范</span><span class="sxs-lookup"><span data-stu-id="89ce5-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="89ce5-330">IETF RFC 6901 JSON 修补程序路径格式规范</span><span class="sxs-lookup"><span data-stu-id="89ce5-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="89ce5-331">[JSON 路径文档](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="89ce5-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="89ce5-332">包括指向用于创建 JSON 修补程序文档的资源的链接。</span><span class="sxs-lookup"><span data-stu-id="89ce5-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="89ce5-333">ASP.NET Core JSON 修补程序源代码</span><span class="sxs-lookup"><span data-stu-id="89ce5-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/main/src/Features/JsonPatch/src)

::: moniker-end
