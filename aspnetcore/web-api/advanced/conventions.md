---
title: 使用 Web API 约定
author: pranavkm
description: 了解 ASP.NET Core 中的 Web API 约定。
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
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
uid: web-api/advanced/conventions
ms.openlocfilehash: 1e6526f46fbd177add3699fb5b667021b741c6a4
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587835"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="dc0b1-103">使用 Web API 约定</span><span class="sxs-lookup"><span data-stu-id="dc0b1-103">Use web API conventions</span></span>

<span data-ttu-id="dc0b1-104">作者：[Pranav Krishnamoorthy](https://github.com/pranavkm) 和 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="dc0b1-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="dc0b1-105">ASP.NET Core 2.2 及更高版本附带一种方法，可提取常见的 [API 文档](xref:tutorials/web-api-help-pages-using-swagger)并将其应用于多个操作、控制器或某程序集内的所有控制器。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="dc0b1-106">Web API 约定是使用修饰单独操作的替代方案 [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-106">Web API conventions are a substitute for decorating individual actions with [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="dc0b1-107">使用此约定，可以：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-107">A convention allows you to:</span></span>

* <span data-ttu-id="dc0b1-108">定义通过特定操作类型返回的、最常见的返回类型和状态代码。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="dc0b1-109">识别偏离所定义的标准的操作。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="dc0b1-110">ASP.NET Core MVC 2.2 及更高版本在 <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName> 中包含一组默认的约定。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="dc0b1-111">约定基于 ASP.NET Core API 项目模板中提供的控件 (ValuesController.cs)。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-111">The conventions are based on the controller (*ValuesController.cs*) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="dc0b1-112">若操作遵循模板中的模式，则应成功使用默认约定。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="dc0b1-113">如果默认约定不能满足需要，请参阅[创建 Web API 约定](#create-web-api-conventions)。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="dc0b1-114">在运行时，<xref:Microsoft.AspNetCore.Mvc.ApiExplorer> 会了解约定。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="dc0b1-115">`ApiExplorer` 是 MVC 与 [OpenAPI](https://www.openapis.org/)（也称为 Swagger）文档生成器进行通信的抽象内容。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="dc0b1-116">已应用的约定中的属性与某个操作相关联，并包含在操作的 OpenAPI 文档中。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="dc0b1-117">[API 分析器](xref:web-api/advanced/analyzers) 还了解约定。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="dc0b1-118">若操作为非常规操作（例如，它返回已应用的约定未记录的状态代码），则会生成警告，建议记录该状态代码。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="dc0b1-119">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/conventions/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="dc0b1-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="dc0b1-120">应用 Web API 约定</span><span class="sxs-lookup"><span data-stu-id="dc0b1-120">Apply web API conventions</span></span>

<span data-ttu-id="dc0b1-121">约定不是组合而成的，每个操作可能只与一个约定相关联。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="dc0b1-122">更明确的约定优先于不太明确的约定。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="dc0b1-123">当具有相同优先级的两个或更多约定应用于某个操作时，选择是不确定的。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="dc0b1-124">存在以下可将约定应用于操作的选项，明确性依次降低：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="dc0b1-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash;适用于各个操作，并指定应用的约定类型和约定方法。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="dc0b1-126">在以下示例中，默认约定类型的 `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` 约定方法将应用于 `Update` 操作：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="dc0b1-127">`Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` 约定方法可将以下属性应用于操作：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    <span data-ttu-id="dc0b1-128">若要详细了解 `[ProducesDefaultResponseType]`，请参阅[默认响应](https://swagger.io/docs/specification/describing-responses/#default)。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="dc0b1-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` 应用于控制器 &mdash; 将指定约定类型应用于控制器上的所有操作。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="dc0b1-130">约定方法都标记有提示，可确定要向其应用约定方法的操作。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-130">A convention method is marked with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="dc0b1-131">有关提示的详细信息，请参阅[创建 Web API 约定](#create-web-api-conventions)）。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="dc0b1-132">在以下示例中，默认的约定集将应用于 ContactsConventionController 中的所有操作：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController*:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="dc0b1-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` 应用于程序集 &mdash; 将指定约定类型应用于当前程序集中的所有控制器。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="dc0b1-134">建议将程序集级别的属性应用于 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="dc0b1-135">在以下示例中，默认的约定集将应用于程序集中的所有操作：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="dc0b1-136">创建 Web API 约定</span><span class="sxs-lookup"><span data-stu-id="dc0b1-136">Create web API conventions</span></span>

<span data-ttu-id="dc0b1-137">如果默认 API 约定不能满足需要，请创建自己的约定。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="dc0b1-138">约定是：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-138">A convention is:</span></span>

* <span data-ttu-id="dc0b1-139">带有方法的静态类型。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-139">A static type with methods.</span></span>
* <span data-ttu-id="dc0b1-140">能够对操作定义[响应类型](#response-types)和[命名要求](#naming-requirements)。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="dc0b1-141">响应类型</span><span class="sxs-lookup"><span data-stu-id="dc0b1-141">Response types</span></span>

<span data-ttu-id="dc0b1-142">这些方法使用 `[ProducesResponseType]` 或 `[ProducesDefaultResponseType]` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="dc0b1-143">例如：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-143">For example:</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="dc0b1-144">如果没有更具体的元数据属性，则将此约定应用于程序集可强制实现以下内容：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="dc0b1-145">该约定方法应用于所有名为 `Find` 的操作。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="dc0b1-146">`Find` 操作上存在名为 `id` 的参数。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="dc0b1-147">命名要求</span><span class="sxs-lookup"><span data-stu-id="dc0b1-147">Naming requirements</span></span>

<span data-ttu-id="dc0b1-148">`[ApiConventionNameMatch]` 和 `[ApiConventionTypeMatch]` 属性可应用于约定方法，确定它们所要应用的操作。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="dc0b1-149">例如：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-149">For example:</span></span>

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="dc0b1-150">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="dc0b1-150">In the preceding example:</span></span>

* <span data-ttu-id="dc0b1-151">应用于该方法的 `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` 选项表示该约定可匹配前缀是“Find”的任何操作。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="dc0b1-152">匹配的操作可以是 `Find`、`FindPet` 和 `FindById`。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="dc0b1-153">应用于该参数的 `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` 表示该约定可匹配带有唯一以标识符作为后缀结尾的参数的方法。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="dc0b1-154">示例包括 `id` 或 `petId` 等参数。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="dc0b1-155">与此类似，可将 `ApiConventionTypeMatch` 应用于类型，以约束参数类型。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="dc0b1-156">`params[]` 参数指示无需显式匹配的剩余参数。</span><span class="sxs-lookup"><span data-stu-id="dc0b1-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc0b1-157">其他资源</span><span class="sxs-lookup"><span data-stu-id="dc0b1-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
