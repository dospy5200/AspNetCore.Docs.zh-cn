---
title: ASP.NET Core 中基于资源的授权
author: scottaddie
description: 了解如何在 ASP.NET Core 应用中实现基于资源的授权。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
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
uid: security/authorization/resourcebased
ms.openlocfilehash: 61c97be03709f63f57a6383ab0c51ca9a511fbbb
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585768"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="22da0-103">ASP.NET Core 中基于资源的授权</span><span class="sxs-lookup"><span data-stu-id="22da0-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="22da0-104">授权策略取决于要访问的资源。</span><span class="sxs-lookup"><span data-stu-id="22da0-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="22da0-105">假设有一个具有 author 属性的文档。</span><span class="sxs-lookup"><span data-stu-id="22da0-105">Consider a document that has an author property.</span></span> <span data-ttu-id="22da0-106">仅允许作者更新文档。</span><span class="sxs-lookup"><span data-stu-id="22da0-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="22da0-107">因此，在进行授权评估之前，必须从数据存储中检索文档。</span><span class="sxs-lookup"><span data-stu-id="22da0-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="22da0-108">在数据绑定之前和在执行加载文档的页面处理程序或操作之前，会发生属性评估。</span><span class="sxs-lookup"><span data-stu-id="22da0-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="22da0-109">由于这些原因，具有属性的声明性授权 `[Authorize]` 无法满足要求。</span><span class="sxs-lookup"><span data-stu-id="22da0-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="22da0-110">相反，你可以调用自定义授权方法， &mdash; 这种方法称为 *命令式 authorization*。</span><span class="sxs-lookup"><span data-stu-id="22da0-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="22da0-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/3_0)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="22da0-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/3_0) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
<span data-ttu-id="22da0-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/2_2)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="22da0-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/2_2) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
<span data-ttu-id="22da0-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/1_1)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="22da0-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/resourcebased/samples/1_1) ([how to download](xref:index#how-to-download-a-sample)).</span></span>
::: moniker-end

<span data-ttu-id="22da0-114">使用[由授权保护的用户数据创建 ASP.NET Core 应用](xref:security/authorization/secure-data)包含使用基于资源的授权的示例应用。</span><span class="sxs-lookup"><span data-stu-id="22da0-114">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="22da0-115">使用命令性授权</span><span class="sxs-lookup"><span data-stu-id="22da0-115">Use imperative authorization</span></span>

<span data-ttu-id="22da0-116">授权作为 [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) 服务实现，并在类中的服务集合中进行注册 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="22da0-116">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="22da0-117">通过 [依赖关系注入](xref:fundamentals/dependency-injection) 到页面处理程序或操作使该服务可用。</span><span class="sxs-lookup"><span data-stu-id="22da0-117">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="22da0-118">`IAuthorizationService` 具有两个 `AuthorizeAsync` 方法重载：一个接受资源和策略名称，另一个接受资源并提供要评估的要求的列表。</span><span class="sxs-lookup"><span data-stu-id="22da0-118">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="22da0-119">在下面的示例中，要保护的资源将加载到自定义 `Document` 对象中。</span><span class="sxs-lookup"><span data-stu-id="22da0-119">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="22da0-120">`AuthorizeAsync`调用重载来确定是否允许当前用户编辑提供的文档。</span><span class="sxs-lookup"><span data-stu-id="22da0-120">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="22da0-121">将自定义 "EditPolicy" 授权策略分解为决定。</span><span class="sxs-lookup"><span data-stu-id="22da0-121">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="22da0-122">有关创建授权策略的详细信息，请参阅 [基于策略的自定义授权](xref:security/authorization/policies) 。</span><span class="sxs-lookup"><span data-stu-id="22da0-122">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="22da0-123">下面的代码示例假定已运行身份验证并设置了 `User` 属性。</span><span class="sxs-lookup"><span data-stu-id="22da0-123">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="22da0-124">编写基于资源的处理程序</span><span class="sxs-lookup"><span data-stu-id="22da0-124">Write a resource-based handler</span></span>

<span data-ttu-id="22da0-125">为基于资源的授权编写处理程序与 [编写简单的要求处理程序并无](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)差别。</span><span class="sxs-lookup"><span data-stu-id="22da0-125">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="22da0-126">创建自定义要求类，并实现需求处理程序类。</span><span class="sxs-lookup"><span data-stu-id="22da0-126">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="22da0-127">有关创建要求类的详细信息，请参阅 [要求](xref:security/authorization/policies#requirements)。</span><span class="sxs-lookup"><span data-stu-id="22da0-127">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="22da0-128">处理程序类同时指定要求和资源类型。</span><span class="sxs-lookup"><span data-stu-id="22da0-128">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="22da0-129">例如，使用和资源的处理程序 `SameAuthorRequirement` `Document` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="22da0-129">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="22da0-130">在前面的示例中，假设 `SameAuthorRequirement` 是更为通用的类的特殊情况 `SpecificAuthorRequirement` 。</span><span class="sxs-lookup"><span data-stu-id="22da0-130">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="22da0-131">`SpecificAuthorRequirement`类 (未显示) 包含 `Name` 表示作者姓名的属性。</span><span class="sxs-lookup"><span data-stu-id="22da0-131">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="22da0-132">`Name`属性可以设置为当前用户。</span><span class="sxs-lookup"><span data-stu-id="22da0-132">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="22da0-133">在以下项中注册要求和处理程序 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="22da0-133">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a><span data-ttu-id="22da0-134">操作要求</span><span class="sxs-lookup"><span data-stu-id="22da0-134">Operational requirements</span></span>

<span data-ttu-id="22da0-135">如果要根据 CRUD (创建、读取、更新、删除) 操作的结果做出决策，请使用 [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) 帮助器类。</span><span class="sxs-lookup"><span data-stu-id="22da0-135">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="22da0-136">此类使你能够为每个操作类型编写单一处理程序而不是单个类。</span><span class="sxs-lookup"><span data-stu-id="22da0-136">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="22da0-137">若要使用它，请提供一些操作名称：</span><span class="sxs-lookup"><span data-stu-id="22da0-137">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="22da0-138">处理程序的实现方式如下 `OperationAuthorizationRequirement` `Document` ：</span><span class="sxs-lookup"><span data-stu-id="22da0-138">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="22da0-139">前面的处理程序使用资源、用户的标识和要求的属性验证操作 `Name` 。</span><span class="sxs-lookup"><span data-stu-id="22da0-139">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="22da0-140">操作资源处理程序的挑战和禁止</span><span class="sxs-lookup"><span data-stu-id="22da0-140">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="22da0-141">本部分说明如何处理质询和禁止操作结果，以及质询和禁止的不同之处。</span><span class="sxs-lookup"><span data-stu-id="22da0-141">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="22da0-142">若要调用操作资源处理程序，请 `AuthorizeAsync` 在页面处理程序或操作中调用时指定操作。</span><span class="sxs-lookup"><span data-stu-id="22da0-142">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="22da0-143">下面的示例确定是否允许经过身份验证的用户查看所提供的文档。</span><span class="sxs-lookup"><span data-stu-id="22da0-143">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="22da0-144">下面的代码示例假定已运行身份验证并设置了 `User` 属性。</span><span class="sxs-lookup"><span data-stu-id="22da0-144">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="22da0-145">如果授权成功，则返回用于查看文档的页面。</span><span class="sxs-lookup"><span data-stu-id="22da0-145">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="22da0-146">如果授权失败，但用户已进行身份验证，则返回 `ForbidResult` 通知任何身份验证中间件验证失败。</span><span class="sxs-lookup"><span data-stu-id="22da0-146">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="22da0-147">`ChallengeResult`当必须执行身份验证时，将返回。</span><span class="sxs-lookup"><span data-stu-id="22da0-147">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="22da0-148">对于交互式浏览器客户端，可能需要将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="22da0-148">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="22da0-149">如果授权成功，则返回文档的视图。</span><span class="sxs-lookup"><span data-stu-id="22da0-149">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="22da0-150">如果授权失败，则返回 `ChallengeResult` 通知任何身份验证中间件身份验证失败，中间件可以采取相应的响应。</span><span class="sxs-lookup"><span data-stu-id="22da0-150">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="22da0-151">适当的响应可能返回401或403状态代码。</span><span class="sxs-lookup"><span data-stu-id="22da0-151">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="22da0-152">对于交互式浏览器客户端，这可能意味着将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="22da0-152">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
