---
title: ASP.NET Core 中的自定义模型绑定
author: ardalis
description: 了解如何通过模型绑定，使控制器操作能够直接使用 ASP.NET Core 中的模型类型。
ms.author: riande
ms.date: 01/06/2020
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: b674d3a034124a26e2e76f2a7f0220e3f0ecea56
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588765"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="dffa7-103">ASP.NET Core 中的自定义模型绑定</span><span class="sxs-lookup"><span data-stu-id="dffa7-103">Custom Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dffa7-104">作者：[Steve Smith](https://ardalis.com/) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="dffa7-104">By [Steve Smith](https://ardalis.com/) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="dffa7-105">通过模型绑定，控制器操作可直接使用模型类型（作为方法参数传入）而不是 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="dffa7-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="dffa7-106">由模型绑定器处理传入的请求数据和应用程序模型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="dffa7-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="dffa7-107">开发人员可以通过实现自定义模型绑定器来扩展内置的模型绑定功能（尽管通常不需要编写自己的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="dffa7-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="dffa7-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="dffa7-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="dffa7-109">默认模型绑定器限制</span><span class="sxs-lookup"><span data-stu-id="dffa7-109">Default model binder limitations</span></span>

<span data-ttu-id="dffa7-110">默认模型绑定器支持大多数常见的 .NET Core 数据类型，能够满足大部分开发人员的需求。</span><span class="sxs-lookup"><span data-stu-id="dffa7-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="dffa7-111">他们希望将基于文本的输入从请求直接绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="dffa7-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="dffa7-112">绑定输入之前，可能需要对其进行转换。</span><span class="sxs-lookup"><span data-stu-id="dffa7-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="dffa7-113">例如，当拥有某个可以用来查找模型数据的键时。</span><span class="sxs-lookup"><span data-stu-id="dffa7-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="dffa7-114">基于该键，用户可以使用自定义模型绑定器来获取数据。</span><span class="sxs-lookup"><span data-stu-id="dffa7-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="dffa7-115">模型绑定查看</span><span class="sxs-lookup"><span data-stu-id="dffa7-115">Model binding review</span></span>

<span data-ttu-id="dffa7-116">模型绑定为其操作对象的类型使用特定定义。</span><span class="sxs-lookup"><span data-stu-id="dffa7-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="dffa7-117">简单类型转换自输入中的单个字符串。</span><span class="sxs-lookup"><span data-stu-id="dffa7-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="dffa7-118">复杂类型转换自多个输入值。</span><span class="sxs-lookup"><span data-stu-id="dffa7-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="dffa7-119">框架基于是否存在 `TypeConverter` 来确定差异。</span><span class="sxs-lookup"><span data-stu-id="dffa7-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="dffa7-120">如果简单 `string` -> `SomeType` 映射不需要外部资源，建议创建类型转换器。</span><span class="sxs-lookup"><span data-stu-id="dffa7-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="dffa7-121">创建自己的自定义模型绑定器之前，有必要查看现有模型绑定器的实现方式。</span><span class="sxs-lookup"><span data-stu-id="dffa7-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="dffa7-122">考虑使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>，它可将 base64 编码的字符串转换为字节数组。</span><span class="sxs-lookup"><span data-stu-id="dffa7-122">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="dffa7-123">字节数组通常存储为文件或数据库 BLOB 字段。</span><span class="sxs-lookup"><span data-stu-id="dffa7-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="dffa7-124">使用 ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="dffa7-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="dffa7-125">Base64 编码的字符串可用来表示二进制数据。</span><span class="sxs-lookup"><span data-stu-id="dffa7-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="dffa7-126">例如，可将图像编码为一个字符串。</span><span class="sxs-lookup"><span data-stu-id="dffa7-126">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="dffa7-127">示例包括作为使用 [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt) 的 base64 编码字符串的图像。</span><span class="sxs-lookup"><span data-stu-id="dffa7-127">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="dffa7-128">ASP.NET Core MVC 可以采用 base64 编码的字符串，并使用 `ByteArrayModelBinder` 将其转换为字节数组。</span><span class="sxs-lookup"><span data-stu-id="dffa7-128">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="dffa7-129"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> 将 `byte[]` 参数映射到 `ByteArrayModelBinder`：</span><span class="sxs-lookup"><span data-stu-id="dffa7-129">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

<span data-ttu-id="dffa7-130">创建自己的自定义模型绑定器时，可实现自己的 `IModelBinderProvider` 类型，或使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>。</span><span class="sxs-lookup"><span data-stu-id="dffa7-130">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="dffa7-131">以下示例显示如何使用 `ByteArrayModelBinder` 将 base64 编码的字符串转换为 `byte[]`，并将结果保存到文件中：</span><span class="sxs-lookup"><span data-stu-id="dffa7-131">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="dffa7-132">可以使用 [Postman](https://www.getpostman.com/) 等工具将 base64 编码的字符串发布到此 api 方法：</span><span class="sxs-lookup"><span data-stu-id="dffa7-132">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="dffa7-133">![postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="dffa7-133">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="dffa7-134">只要绑定器可以将请求数据绑定到相应命名的属性或参数，模型绑定就会成功。</span><span class="sxs-lookup"><span data-stu-id="dffa7-134">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="dffa7-135">以下示例演示如何将 `ByteArrayModelBinder` 与 视图模型结合使用：</span><span class="sxs-lookup"><span data-stu-id="dffa7-135">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="dffa7-136">自定义模型绑定器示例</span><span class="sxs-lookup"><span data-stu-id="dffa7-136">Custom model binder sample</span></span>

<span data-ttu-id="dffa7-137">在本部分中，我们将实现具有以下功能的自定义模型绑定器：</span><span class="sxs-lookup"><span data-stu-id="dffa7-137">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="dffa7-138">将传入的请求数据转换为强类型键参数。</span><span class="sxs-lookup"><span data-stu-id="dffa7-138">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="dffa7-139">使用 Entity Framework Core 来提取关联的实体。</span><span class="sxs-lookup"><span data-stu-id="dffa7-139">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="dffa7-140">将关联的实体作为自变量传递给操作方法。</span><span class="sxs-lookup"><span data-stu-id="dffa7-140">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="dffa7-141">以下示例在 `Author` 模型上使用 `ModelBinder` 属性：</span><span class="sxs-lookup"><span data-stu-id="dffa7-141">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="dffa7-142">在前面的代码中，`ModelBinder` 属性指定应当用于绑定 `Author` 操作参数的 `IModelBinder` 的类型。</span><span class="sxs-lookup"><span data-stu-id="dffa7-142">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="dffa7-143">以下 `AuthorEntityBinder` 类通过 Entity Framework Core 和 `authorId` 提取数据源中的实体来绑定 `Author` 参数：</span><span class="sxs-lookup"><span data-stu-id="dffa7-143">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> <span data-ttu-id="dffa7-144">前面的 `AuthorEntityBinder` 类旨在说明自定义模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="dffa7-144">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="dffa7-145">该类不是意在说明查找方案的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="dffa7-145">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="dffa7-146">对于查找，请绑定 `authorId` 并在操作方法中查询数据库。</span><span class="sxs-lookup"><span data-stu-id="dffa7-146">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="dffa7-147">此方法将模型绑定故障与 `NotFound` 案例分开。</span><span class="sxs-lookup"><span data-stu-id="dffa7-147">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="dffa7-148">以下代码显示如何在操作方法中使用 `AuthorEntityBinder`：</span><span class="sxs-lookup"><span data-stu-id="dffa7-148">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

<span data-ttu-id="dffa7-149">可使用 `ModelBinder` 属性将 `AuthorEntityBinder` 应用于不使用默认约定的参数：</span><span class="sxs-lookup"><span data-stu-id="dffa7-149">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

<span data-ttu-id="dffa7-150">在此示例中，由于参数的名称不是默认的 `authorId`，因此，使用 `ModelBinder` 属性在参数上指定该名称。</span><span class="sxs-lookup"><span data-stu-id="dffa7-150">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="dffa7-151">比起在操作方法中查找实体，控制器和操作方法都得到了简化。</span><span class="sxs-lookup"><span data-stu-id="dffa7-151">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="dffa7-152">使用 Entity Framework Core 获取创建者的逻辑会移动到模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="dffa7-152">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="dffa7-153">如果有多种方法绑定到 `Author` 模型，就能得到很大程度的简化。</span><span class="sxs-lookup"><span data-stu-id="dffa7-153">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="dffa7-154">可以将 `ModelBinder` 属性应用到各个模型属性（例如视图模型上）或操作方法参数，以便为该类型或操作指定某一模型绑定器或模型名称。</span><span class="sxs-lookup"><span data-stu-id="dffa7-154">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="dffa7-155">实现 ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="dffa7-155">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="dffa7-156">可以实现 `IModelBinderProvider`，而不是应用属性。</span><span class="sxs-lookup"><span data-stu-id="dffa7-156">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="dffa7-157">这就是内置框架绑定器的实现方式。</span><span class="sxs-lookup"><span data-stu-id="dffa7-157">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="dffa7-158">指定绑定器所操作的类型时，指定它生成的参数的类型，而不是绑定器接受的输入。</span><span class="sxs-lookup"><span data-stu-id="dffa7-158">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="dffa7-159">以下绑定器提供程序适用于 `AuthorEntityBinder`。</span><span class="sxs-lookup"><span data-stu-id="dffa7-159">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="dffa7-160">将其添加到 MVC 提供程序的集合中时，无需在 `Author` 或 `Author` 类型参数上使用 `ModelBinder` 属性。</span><span class="sxs-lookup"><span data-stu-id="dffa7-160">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="dffa7-161">注意：上述代码返回 `BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="dffa7-161">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="dffa7-162">`BinderTypeModelBinder` 充当模型绑定器中心，并提供依赖关系注入 (DI)。</span><span class="sxs-lookup"><span data-stu-id="dffa7-162">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="dffa7-163">`AuthorEntityBinder` 需要 DI 来访问 EF Core。</span><span class="sxs-lookup"><span data-stu-id="dffa7-163">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="dffa7-164">如果模型绑定器需要 DI 中的服务，请使用 `BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="dffa7-164">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="dffa7-165">若要使用自定义模型绑定器提供程序，请将其添加到 `ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="dffa7-165">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

<span data-ttu-id="dffa7-166">评估模型绑定器时，按顺序检查提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="dffa7-166">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="dffa7-167">使用第一个返回与输入模型匹配的联编程序的提供程序。</span><span class="sxs-lookup"><span data-stu-id="dffa7-167">The first provider that returns a binder that matches the input model is used.</span></span> <span data-ttu-id="dffa7-168">因此，将提供程序添加到集合的末尾可能会导致在自定义联编程序有可能之前调用内置模型联编程序。</span><span class="sxs-lookup"><span data-stu-id="dffa7-168">Adding your provider to the end of the collection may thus result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="dffa7-169">在此示例中，将自定义提供程序添加到集合的开头，以确保它始终用于 `Author` 操作参数。</span><span class="sxs-lookup"><span data-stu-id="dffa7-169">In this example, the custom provider is added to the beginning of the collection to ensure it's always used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="dffa7-170">多态模型绑定</span><span class="sxs-lookup"><span data-stu-id="dffa7-170">Polymorphic model binding</span></span>

<span data-ttu-id="dffa7-171">绑定到不同的派生类型模型称为多态模型绑定。</span><span class="sxs-lookup"><span data-stu-id="dffa7-171">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="dffa7-172">如果请求值必须绑定到特定的派生模型类型，则需要多态自定义模型绑定。</span><span class="sxs-lookup"><span data-stu-id="dffa7-172">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="dffa7-173">多态模型绑定：</span><span class="sxs-lookup"><span data-stu-id="dffa7-173">Polymorphic model binding:</span></span>

* <span data-ttu-id="dffa7-174">对于旨在与所有语言进行互操作的 REST API 并不常见。</span><span class="sxs-lookup"><span data-stu-id="dffa7-174">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="dffa7-175">使绑定模型难以推理。</span><span class="sxs-lookup"><span data-stu-id="dffa7-175">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="dffa7-176">但是，如果应用需要多态模型绑定，则实现可能类似于以下代码：</span><span class="sxs-lookup"><span data-stu-id="dffa7-176">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="dffa7-177">建议和最佳做法</span><span class="sxs-lookup"><span data-stu-id="dffa7-177">Recommendations and best practices</span></span>

<span data-ttu-id="dffa7-178">自定义模型绑定：</span><span class="sxs-lookup"><span data-stu-id="dffa7-178">Custom model binders:</span></span>

- <span data-ttu-id="dffa7-179">不应尝试设置状态代码或返回结果（例如 404 Not Found）。</span><span class="sxs-lookup"><span data-stu-id="dffa7-179">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="dffa7-180">如果模型绑定失败，那么该操作方法本身的[操作筛选器](xref:mvc/controllers/filters)或逻辑会处理失败。</span><span class="sxs-lookup"><span data-stu-id="dffa7-180">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="dffa7-181">对于消除操作方法中的重复代码和跨领域问题最为有用。</span><span class="sxs-lookup"><span data-stu-id="dffa7-181">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="dffa7-182">通常不应用其将字符串转换为自定义类型，而应选择用 <xref:System.ComponentModel.TypeConverter> 来完成此操作。</span><span class="sxs-lookup"><span data-stu-id="dffa7-182">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dffa7-183">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dffa7-183">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dffa7-184">通过模型绑定，控制器操作可直接使用模型类型（作为方法参数传入）而不是 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="dffa7-184">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="dffa7-185">由模型绑定器处理传入的请求数据和应用程序模型之间的映射。</span><span class="sxs-lookup"><span data-stu-id="dffa7-185">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="dffa7-186">开发人员可以通过实现自定义模型绑定器来扩展内置的模型绑定功能（尽管通常不需要编写自己的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="dffa7-186">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

<span data-ttu-id="dffa7-187">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="dffa7-187">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/advanced/custom-model-binding/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="default-model-binder-limitations"></a><span data-ttu-id="dffa7-188">默认模型绑定器限制</span><span class="sxs-lookup"><span data-stu-id="dffa7-188">Default model binder limitations</span></span>

<span data-ttu-id="dffa7-189">默认模型绑定器支持大多数常见的 .NET Core 数据类型，能够满足大部分开发人员的需求。</span><span class="sxs-lookup"><span data-stu-id="dffa7-189">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="dffa7-190">他们希望将基于文本的输入从请求直接绑定到模型类型。</span><span class="sxs-lookup"><span data-stu-id="dffa7-190">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="dffa7-191">绑定输入之前，可能需要对其进行转换。</span><span class="sxs-lookup"><span data-stu-id="dffa7-191">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="dffa7-192">例如，当拥有某个可以用来查找模型数据的键时。</span><span class="sxs-lookup"><span data-stu-id="dffa7-192">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="dffa7-193">基于该键，用户可以使用自定义模型绑定器来获取数据。</span><span class="sxs-lookup"><span data-stu-id="dffa7-193">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="dffa7-194">模型绑定查看</span><span class="sxs-lookup"><span data-stu-id="dffa7-194">Model binding review</span></span>

<span data-ttu-id="dffa7-195">模型绑定为其操作对象的类型使用特定定义。</span><span class="sxs-lookup"><span data-stu-id="dffa7-195">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="dffa7-196">简单类型转换自输入中的单个字符串。</span><span class="sxs-lookup"><span data-stu-id="dffa7-196">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="dffa7-197">复杂类型转换自多个输入值。</span><span class="sxs-lookup"><span data-stu-id="dffa7-197">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="dffa7-198">框架基于是否存在 `TypeConverter` 来确定差异。</span><span class="sxs-lookup"><span data-stu-id="dffa7-198">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="dffa7-199">如果简单 `string` -> `SomeType` 映射不需要外部资源，建议创建类型转换器。</span><span class="sxs-lookup"><span data-stu-id="dffa7-199">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="dffa7-200">创建自己的自定义模型绑定器之前，有必要查看现有模型绑定器的实现方式。</span><span class="sxs-lookup"><span data-stu-id="dffa7-200">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="dffa7-201">考虑使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>，它可将 base64 编码的字符串转换为字节数组。</span><span class="sxs-lookup"><span data-stu-id="dffa7-201">Consider the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder> which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="dffa7-202">字节数组通常存储为文件或数据库 BLOB 字段。</span><span class="sxs-lookup"><span data-stu-id="dffa7-202">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="dffa7-203">使用 ByteArrayModelBinder</span><span class="sxs-lookup"><span data-stu-id="dffa7-203">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="dffa7-204">Base64 编码的字符串可用来表示二进制数据。</span><span class="sxs-lookup"><span data-stu-id="dffa7-204">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="dffa7-205">例如，可将图像编码为一个字符串。</span><span class="sxs-lookup"><span data-stu-id="dffa7-205">For example, an image can be encoded as a string.</span></span> <span data-ttu-id="dffa7-206">示例包括作为使用 [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt) 的 base64 编码字符串的图像。</span><span class="sxs-lookup"><span data-stu-id="dffa7-206">The sample includes an image as a base64-encoded string in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).</span></span>

<span data-ttu-id="dffa7-207">ASP.NET Core MVC 可以采用 base64 编码的字符串，并使用 `ByteArrayModelBinder` 将其转换为字节数组。</span><span class="sxs-lookup"><span data-stu-id="dffa7-207">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="dffa7-208"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> 将 `byte[]` 参数映射到 `ByteArrayModelBinder`：</span><span class="sxs-lookup"><span data-stu-id="dffa7-208">The <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="dffa7-209">创建自己的自定义模型绑定器时，可实现自己的 `IModelBinderProvider` 类型，或使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>。</span><span class="sxs-lookup"><span data-stu-id="dffa7-209">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute>.</span></span>

<span data-ttu-id="dffa7-210">以下示例显示如何使用 `ByteArrayModelBinder` 将 base64 编码的字符串转换为 `byte[]`，并将结果保存到文件中：</span><span class="sxs-lookup"><span data-stu-id="dffa7-210">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

<span data-ttu-id="dffa7-211">可以使用 [Postman](https://www.getpostman.com/) 等工具将 base64 编码的字符串发布到此 api 方法：</span><span class="sxs-lookup"><span data-stu-id="dffa7-211">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="dffa7-212">![postman](custom-model-binding/images/postman.png "Postman")</span><span class="sxs-lookup"><span data-stu-id="dffa7-212">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="dffa7-213">只要绑定器可以将请求数据绑定到相应命名的属性或参数，模型绑定就会成功。</span><span class="sxs-lookup"><span data-stu-id="dffa7-213">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="dffa7-214">以下示例演示如何将 `ByteArrayModelBinder` 与 视图模型结合使用：</span><span class="sxs-lookup"><span data-stu-id="dffa7-214">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="dffa7-215">自定义模型绑定器示例</span><span class="sxs-lookup"><span data-stu-id="dffa7-215">Custom model binder sample</span></span>

<span data-ttu-id="dffa7-216">在本部分中，我们将实现具有以下功能的自定义模型绑定器：</span><span class="sxs-lookup"><span data-stu-id="dffa7-216">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="dffa7-217">将传入的请求数据转换为强类型键参数。</span><span class="sxs-lookup"><span data-stu-id="dffa7-217">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="dffa7-218">使用 Entity Framework Core 来提取关联的实体。</span><span class="sxs-lookup"><span data-stu-id="dffa7-218">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="dffa7-219">将关联的实体作为自变量传递给操作方法。</span><span class="sxs-lookup"><span data-stu-id="dffa7-219">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="dffa7-220">以下示例在 `Author` 模型上使用 `ModelBinder` 属性：</span><span class="sxs-lookup"><span data-stu-id="dffa7-220">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

<span data-ttu-id="dffa7-221">在前面的代码中，`ModelBinder` 属性指定应当用于绑定 `Author` 操作参数的 `IModelBinder` 的类型。</span><span class="sxs-lookup"><span data-stu-id="dffa7-221">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="dffa7-222">以下 `AuthorEntityBinder` 类通过 Entity Framework Core 和 `authorId` 提取数据源中的实体来绑定 `Author` 参数：</span><span class="sxs-lookup"><span data-stu-id="dffa7-222">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="dffa7-223">前面的 `AuthorEntityBinder` 类旨在说明自定义模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="dffa7-223">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="dffa7-224">该类不是意在说明查找方案的最佳做法。</span><span class="sxs-lookup"><span data-stu-id="dffa7-224">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="dffa7-225">对于查找，请绑定 `authorId` 并在操作方法中查询数据库。</span><span class="sxs-lookup"><span data-stu-id="dffa7-225">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="dffa7-226">此方法将模型绑定故障与 `NotFound` 案例分开。</span><span class="sxs-lookup"><span data-stu-id="dffa7-226">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="dffa7-227">以下代码显示如何在操作方法中使用 `AuthorEntityBinder`：</span><span class="sxs-lookup"><span data-stu-id="dffa7-227">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="dffa7-228">可使用 `ModelBinder` 属性将 `AuthorEntityBinder` 应用于不使用默认约定的参数：</span><span class="sxs-lookup"><span data-stu-id="dffa7-228">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="dffa7-229">在此示例中，由于参数的名称不是默认的 `authorId`，因此，使用 `ModelBinder` 属性在参数上指定该名称。</span><span class="sxs-lookup"><span data-stu-id="dffa7-229">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="dffa7-230">比起在操作方法中查找实体，控制器和操作方法都得到了简化。</span><span class="sxs-lookup"><span data-stu-id="dffa7-230">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="dffa7-231">使用 Entity Framework Core 获取创建者的逻辑会移动到模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="dffa7-231">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="dffa7-232">如果有多种方法绑定到 `Author` 模型，就能得到很大程度的简化。</span><span class="sxs-lookup"><span data-stu-id="dffa7-232">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="dffa7-233">可以将 `ModelBinder` 属性应用到各个模型属性（例如视图模型上）或操作方法参数，以便为该类型或操作指定某一模型绑定器或模型名称。</span><span class="sxs-lookup"><span data-stu-id="dffa7-233">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="dffa7-234">实现 ModelBinderProvider</span><span class="sxs-lookup"><span data-stu-id="dffa7-234">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="dffa7-235">可以实现 `IModelBinderProvider`，而不是应用属性。</span><span class="sxs-lookup"><span data-stu-id="dffa7-235">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="dffa7-236">这就是内置框架绑定器的实现方式。</span><span class="sxs-lookup"><span data-stu-id="dffa7-236">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="dffa7-237">指定绑定器所操作的类型时，指定它生成的参数的类型，而不是绑定器接受的输入。</span><span class="sxs-lookup"><span data-stu-id="dffa7-237">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="dffa7-238">以下绑定器提供程序适用于 `AuthorEntityBinder`。</span><span class="sxs-lookup"><span data-stu-id="dffa7-238">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="dffa7-239">将其添加到 MVC 提供程序的集合中时，无需在 `Author` 或 `Author` 类型参数上使用 `ModelBinder` 属性。</span><span class="sxs-lookup"><span data-stu-id="dffa7-239">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="dffa7-240">注意：上述代码返回 `BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="dffa7-240">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="dffa7-241">`BinderTypeModelBinder` 充当模型绑定器中心，并提供依赖关系注入 (DI)。</span><span class="sxs-lookup"><span data-stu-id="dffa7-241">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="dffa7-242">`AuthorEntityBinder` 需要 DI 来访问 EF Core。</span><span class="sxs-lookup"><span data-stu-id="dffa7-242">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="dffa7-243">如果模型绑定器需要 DI 中的服务，请使用 `BinderTypeModelBinder`。</span><span class="sxs-lookup"><span data-stu-id="dffa7-243">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="dffa7-244">若要使用自定义模型绑定器提供程序，请将其添加到 `ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="dffa7-244">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

<span data-ttu-id="dffa7-245">评估模型绑定器时，按顺序检查提供程序的集合。</span><span class="sxs-lookup"><span data-stu-id="dffa7-245">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="dffa7-246">使用返回绑定器的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="dffa7-246">The first provider that returns a binder is used.</span></span> <span data-ttu-id="dffa7-247">向集合的末尾添加提供程序，可能会导致在调用自定义绑定器之前调用内置模型绑定器。</span><span class="sxs-lookup"><span data-stu-id="dffa7-247">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="dffa7-248">在此示例中，向集合的开头添加自定义提供程序，确保它用于 `Author` 操作参数。</span><span class="sxs-lookup"><span data-stu-id="dffa7-248">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

### <a name="polymorphic-model-binding"></a><span data-ttu-id="dffa7-249">多态模型绑定</span><span class="sxs-lookup"><span data-stu-id="dffa7-249">Polymorphic model binding</span></span>

<span data-ttu-id="dffa7-250">绑定到不同的派生类型模型称为多态模型绑定。</span><span class="sxs-lookup"><span data-stu-id="dffa7-250">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="dffa7-251">如果请求值必须绑定到特定的派生模型类型，则需要多态自定义模型绑定。</span><span class="sxs-lookup"><span data-stu-id="dffa7-251">Polymorphic custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="dffa7-252">多态模型绑定：</span><span class="sxs-lookup"><span data-stu-id="dffa7-252">Polymorphic model binding:</span></span>

* <span data-ttu-id="dffa7-253">对于旨在与所有语言进行互操作的 REST API 并不常见。</span><span class="sxs-lookup"><span data-stu-id="dffa7-253">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="dffa7-254">使绑定模型难以推理。</span><span class="sxs-lookup"><span data-stu-id="dffa7-254">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="dffa7-255">但是，如果应用需要多态模型绑定，则实现可能类似于以下代码：</span><span class="sxs-lookup"><span data-stu-id="dffa7-255">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="dffa7-256">建议和最佳做法</span><span class="sxs-lookup"><span data-stu-id="dffa7-256">Recommendations and best practices</span></span>

<span data-ttu-id="dffa7-257">自定义模型绑定：</span><span class="sxs-lookup"><span data-stu-id="dffa7-257">Custom model binders:</span></span>

- <span data-ttu-id="dffa7-258">不应尝试设置状态代码或返回结果（例如 404 Not Found）。</span><span class="sxs-lookup"><span data-stu-id="dffa7-258">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="dffa7-259">如果模型绑定失败，那么该操作方法本身的[操作筛选器](xref:mvc/controllers/filters)或逻辑会处理失败。</span><span class="sxs-lookup"><span data-stu-id="dffa7-259">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="dffa7-260">对于消除操作方法中的重复代码和跨领域问题最为有用。</span><span class="sxs-lookup"><span data-stu-id="dffa7-260">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="dffa7-261">通常不应用其将字符串转换为自定义类型，而应选择用 <xref:System.ComponentModel.TypeConverter> 来完成此操作。</span><span class="sxs-lookup"><span data-stu-id="dffa7-261">Typically shouldn't be used to convert a string into a custom type, a <xref:System.ComponentModel.TypeConverter> is usually a better option.</span></span>

::: moniker-end
