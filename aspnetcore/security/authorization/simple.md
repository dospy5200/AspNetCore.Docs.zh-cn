---
title: ASP.NET Core 中的简单授权
author: rick-anderson
description: 了解如何使用授权属性限制对 ASP.NET Core 控制器和操作的访问。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/simple
ms.openlocfilehash: eaf36822edc47f7238c356e167b05ac44f93175d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587562"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="443d1-103">ASP.NET Core 中的简单授权</span><span class="sxs-lookup"><span data-stu-id="443d1-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="443d1-104">ASP.NET Core 中的授权通过 <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> 和其各种参数来控制。</span><span class="sxs-lookup"><span data-stu-id="443d1-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="443d1-105">在最简单的形式中，将 `[Authorize]` 属性应用于控制器、操作或 Razor 页面，将对该组件的访问限制为任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="443d1-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="443d1-106">例如，以下代码将访问权限限制为 `AccountController` 任何经过身份验证的用户。</span><span class="sxs-lookup"><span data-stu-id="443d1-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="443d1-107">如果要对操作（而不是控制器）应用授权，请将属性应用于 `AuthorizeAttribute` 操作本身：</span><span class="sxs-lookup"><span data-stu-id="443d1-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="443d1-108">现在只有经过身份验证的用户可以访问该 `Logout` 函数。</span><span class="sxs-lookup"><span data-stu-id="443d1-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="443d1-109">你还可以使用属性，以 `AllowAnonymous` 允许未通过身份验证的用户访问各个操作。</span><span class="sxs-lookup"><span data-stu-id="443d1-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="443d1-110">例如：</span><span class="sxs-lookup"><span data-stu-id="443d1-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="443d1-111">这将仅允许经过身份验证的用户 `AccountController` 访问，但 `Login` 操作除外，无论用户是否经过身份验证或未经身份验证/匿名状态，都可以访问该操作。</span><span class="sxs-lookup"><span data-stu-id="443d1-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="443d1-112">`[AllowAnonymous]` 跳过所有授权语句。</span><span class="sxs-lookup"><span data-stu-id="443d1-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="443d1-113">如果组合 `[AllowAnonymous]` 和任何 `[Authorize]` 属性，则 `[Authorize]` 忽略属性。</span><span class="sxs-lookup"><span data-stu-id="443d1-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="443d1-114">例如，如果在 `[AllowAnonymous]` 控制器级别应用，则 `[Authorize]` 会忽略同一控制器 (上的任何属性或) 中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="443d1-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a><span data-ttu-id="443d1-115">授权属性和 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="443d1-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="443d1-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>***不*** 能应用于 Razor 页面处理程序。</span><span class="sxs-lookup"><span data-stu-id="443d1-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="443d1-117">例如， `[Authorize]` 不能应用于 `OnGet` 、 `OnPost` 或任何其他页处理程序。</span><span class="sxs-lookup"><span data-stu-id="443d1-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="443d1-118">请考虑对具有不同处理程序的不同授权要求的页面使用 ASP.NET Core MVC 控制器。</span><span class="sxs-lookup"><span data-stu-id="443d1-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="443d1-119">以下两种方法可用于将授权应用于 Razor 页面处理程序方法：</span><span class="sxs-lookup"><span data-stu-id="443d1-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="443d1-120">对于需要不同授权的页面处理程序，请使用单独的页面。</span><span class="sxs-lookup"><span data-stu-id="443d1-120">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="443d1-121">将共享内容移动到一个或多个 [分部视图](xref:mvc/views/partial)中。</span><span class="sxs-lookup"><span data-stu-id="443d1-121">Move shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="443d1-122">如果可能，建议使用这种方法。</span><span class="sxs-lookup"><span data-stu-id="443d1-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="443d1-123">对于必须共享公共页面的内容，请编写一个作为 [IAsyncPageFilter](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)的一部分执行授权的筛选器。</span><span class="sxs-lookup"><span data-stu-id="443d1-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="443d1-124">[PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub 项目演示了这种方法：</span><span class="sxs-lookup"><span data-stu-id="443d1-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="443d1-125">[AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs)实现授权筛选器：[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="443d1-125">The [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="443d1-126">[[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16)属性应用于 `OnGet` 页面处理程序：[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="443d1-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="443d1-127">[PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth)示例 ***方法不执行以下操作***：</span><span class="sxs-lookup"><span data-stu-id="443d1-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="443d1-128">结合应用于页面、页面模型或全局的授权属性进行撰写。</span><span class="sxs-lookup"><span data-stu-id="443d1-128">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="443d1-129">如果将一个或多个实例应用于页面，则组合授权属性会导致身份验证和授权多次执行 `AuthorizeAttribute` `AuthorizeFilter` 。</span><span class="sxs-lookup"><span data-stu-id="443d1-129">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="443d1-130">与其他 ASP.NET Core 身份验证和授权系统一起工作。</span><span class="sxs-lookup"><span data-stu-id="443d1-130">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="443d1-131">你必须验证此方法是否适用于你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="443d1-131">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="443d1-132">没有计划支持 `AuthorizeAttribute` Razor 页处理程序。</span><span class="sxs-lookup"><span data-stu-id="443d1-132">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
