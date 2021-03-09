---
title: 保护 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何将 Blazor WebAssemlby 应用作为单页应用程序 (SPA) 进行保护。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/index
ms.openlocfilehash: c7bedd0b6990ab9991c1cce14f603bcda06c22aa
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394676"
---
# <a name="secure-aspnet-core-blazor-webassembly"></a><span data-ttu-id="68702-103">保护 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="68702-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="68702-104">Blazor WebAssembly 应用的保护方式与单页应用 (SPA) 相同。</span><span class="sxs-lookup"><span data-stu-id="68702-104">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="68702-105">可通过多种方式向 SPA 进行用户身份验证，但最常用、最全面的方式是使用基于 [OAuth 2.0 协议](https://oauth.net/)的实现，例如 [OpenID Connect (OIDC)](https://openid.net/connect/)。</span><span class="sxs-lookup"><span data-stu-id="68702-105">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="68702-106">身份验证库</span><span class="sxs-lookup"><span data-stu-id="68702-106">Authentication library</span></span>

<span data-ttu-id="68702-107">Blazor WebAssembly 支持通过 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 库使用 OIDC 对应用进行身份验证和授权。</span><span class="sxs-lookup"><span data-stu-id="68702-107">Blazor WebAssembly supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="68702-108">该库提供一组基元，可用于对 ASP.NET Core 后端进行无缝身份验证。</span><span class="sxs-lookup"><span data-stu-id="68702-108">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="68702-109">这个库将 ASP.NET Core Identity 与以 [Identity 服务器](https://identityserver.io/)为基础的 API 身份验证支持集成。</span><span class="sxs-lookup"><span data-stu-id="68702-109">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="68702-110">它可以针对支持 OIDC 的任何第三方 Identity 提供者 (IP)，即 OpenID 提供者 (OP)，进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="68702-110">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="68702-111">Blazor WebAssembly 中的身份验证支持建立在 `oidc-client.js` 库的基础之上，此库用于处理基础身份验证协议详细信息。</span><span class="sxs-lookup"><span data-stu-id="68702-111">The authentication support in Blazor WebAssembly is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="68702-112">还有对 SPA 进行身份验证的其他选项，例如使用 SameSite cookie。</span><span class="sxs-lookup"><span data-stu-id="68702-112">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="68702-113">但是，Blazor WebAssembly 的工程设计决定，OAuth 和 OIDC 是在 Blazor WebAssembly 应用中进行身份验证的最佳选择。</span><span class="sxs-lookup"><span data-stu-id="68702-113">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="68702-114">出于以下功能和安全原因，选择了以 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 为基础的[基于令牌的身份验证](xref:security/anti-request-forgery#token-based-authentication)而不是[基于 cookie 的身份验证](xref:security/anti-request-forgery#cookie-based-authentication)：</span><span class="sxs-lookup"><span data-stu-id="68702-114">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="68702-115">使用基于令牌的协议可以减小攻击面，因为并非所有请求中都会发送令牌。</span><span class="sxs-lookup"><span data-stu-id="68702-115">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="68702-116">服务器终结点不要求针对[跨站点请求伪造 (CSRF)](xref:security/anti-request-forgery) 进行保护，因为会显式发送令牌。</span><span class="sxs-lookup"><span data-stu-id="68702-116">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="68702-117">因此，可以将 Blazor WebAssembly 应用与 MVC 或 Razor Pages 应用一起托管。</span><span class="sxs-lookup"><span data-stu-id="68702-117">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="68702-118">令牌的权限比 cookie 窄。</span><span class="sxs-lookup"><span data-stu-id="68702-118">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="68702-119">例如，令牌不能用于管理用户帐户或更改用户密码，除非显式实现了此类功能。</span><span class="sxs-lookup"><span data-stu-id="68702-119">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="68702-120">令牌的生命周期更短（默认为一小时），这限制了攻击时间窗口。</span><span class="sxs-lookup"><span data-stu-id="68702-120">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="68702-121">还可随时撤销令牌。</span><span class="sxs-lookup"><span data-stu-id="68702-121">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="68702-122">自包含 JWT 向客户端和服务器提供身份验证进程保证。</span><span class="sxs-lookup"><span data-stu-id="68702-122">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="68702-123">例如，客户端可以检测和验证它收到的令牌是否合法，以及是否是在给定身份验证过程中发出的。</span><span class="sxs-lookup"><span data-stu-id="68702-123">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="68702-124">如果有第三方尝试在身份验证进程中偷换令牌，客户端可以检测被偷换的令牌并避免使用它。</span><span class="sxs-lookup"><span data-stu-id="68702-124">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="68702-125">OAuth 和 OIDC 的令牌不依赖于用户代理行为正确以确保应用安全。</span><span class="sxs-lookup"><span data-stu-id="68702-125">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="68702-126">基于令牌的协议（例如 OAuth 和 OIDC）允许用同一组安全特征对托管和独立应用进行验证和授权。</span><span class="sxs-lookup"><span data-stu-id="68702-126">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="68702-127">使用 OIDC 的身份验证进程</span><span class="sxs-lookup"><span data-stu-id="68702-127">Authentication process with OIDC</span></span>

<span data-ttu-id="68702-128">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 库提供几个基元，用于使用 OIDC 实现身份验证和授权。</span><span class="sxs-lookup"><span data-stu-id="68702-128">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="68702-129">从广义上说来，身份验证的原理如下：</span><span class="sxs-lookup"><span data-stu-id="68702-129">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="68702-130">当匿名用户选择登录按钮或请求应用了 [`[Authorize]` 特性](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)的页面时，该用户将重定向到应用的登录页 (`/authentication/login`)。</span><span class="sxs-lookup"><span data-stu-id="68702-130">When an anonymous user selects the login button or requests a page with the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="68702-131">在登录页上，身份验证库准备重定向到授权终结点。</span><span class="sxs-lookup"><span data-stu-id="68702-131">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="68702-132">授权终结点在 Blazor WebAssembly 应用之外，可以托管在单独的原点上。</span><span class="sxs-lookup"><span data-stu-id="68702-132">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="68702-133">该终结点负责确定用户是否通过身份验证，并发送一个或更多令牌作为响应。</span><span class="sxs-lookup"><span data-stu-id="68702-133">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="68702-134">身份验证库提供登录回叫以接收身份验证响应。</span><span class="sxs-lookup"><span data-stu-id="68702-134">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="68702-135">如果用户未通过身份验证，则会被重定向到底层身份验证系统，通常是 ASP.NET Core Identity。</span><span class="sxs-lookup"><span data-stu-id="68702-135">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="68702-136">如果用户已通过身份验证，则授权终结点会生成相应的令牌，并将浏览器重定向回登录回叫终结点 (`/authentication/login-callback`)。</span><span class="sxs-lookup"><span data-stu-id="68702-136">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="68702-137">当 Blazor WebAssembly 应用加载登录回叫终结点 (`/authentication/login-callback`) 时，就处理了身份验证响应。</span><span class="sxs-lookup"><span data-stu-id="68702-137">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="68702-138">如果身份验证进程成功完成，则用户通过身份验证，可以选择返回该用户请求的原受保护 URL。</span><span class="sxs-lookup"><span data-stu-id="68702-138">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="68702-139">如果身份验证进程由于任何原因而失败，会将用户导向登录失败页 (`/authentication/login-failed`)，并显示错误。</span><span class="sxs-lookup"><span data-stu-id="68702-139">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="68702-140">`Authentication` 组件</span><span class="sxs-lookup"><span data-stu-id="68702-140">`Authentication` component</span></span>

<span data-ttu-id="68702-141">`Authentication` 组件 (`Pages/Authentication.razor`) 会处理远程身份验证操作并允许应用：</span><span class="sxs-lookup"><span data-stu-id="68702-141">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="68702-142">为身份验证状态配置应用路由。</span><span class="sxs-lookup"><span data-stu-id="68702-142">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="68702-143">为身份验证状态设置 UI 内容。</span><span class="sxs-lookup"><span data-stu-id="68702-143">Set UI content for authentication states.</span></span>
* <span data-ttu-id="68702-144">管理身份验证状态。</span><span class="sxs-lookup"><span data-stu-id="68702-144">Manage authentication state.</span></span>

<span data-ttu-id="68702-145">身份验证操作（例如注册用户或使用户登录）传递到 Blazor 框架的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> 组件，该组件会保留和控制整个身份验证操作中的状态。</span><span class="sxs-lookup"><span data-stu-id="68702-145">Authentication actions, such as registering or signing in a user, are passed to the Blazor framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="68702-146">有关更多信息和示例，请参见<xref:blazor/security/webassembly/additional-scenarios>。</span><span class="sxs-lookup"><span data-stu-id="68702-146">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="68702-147">授权</span><span class="sxs-lookup"><span data-stu-id="68702-147">Authorization</span></span>

<span data-ttu-id="68702-148">在 Blazor WebAssembly 应用中，可绕过授权检查，因为用户可修改所有客户端代码。</span><span class="sxs-lookup"><span data-stu-id="68702-148">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="68702-149">所有客户端应用程序技术都是如此，其中包括 JavaScript SPA 框架或任何操作系统的本机应用程序。</span><span class="sxs-lookup"><span data-stu-id="68702-149">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="68702-150">**始终对客户端应用程序访问的任何 API 终结点内的服务器执行授权检查。**</span><span class="sxs-lookup"><span data-stu-id="68702-150">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="68702-151">需要对整个应用授权</span><span class="sxs-lookup"><span data-stu-id="68702-151">Require authorization for the entire app</span></span>

<span data-ttu-id="68702-152">使用以下方法之一将 [`[Authorize]` 属性](xref:blazor/security/index#authorize-attribute)（[API 文档](xref:System.Web.Mvc.AuthorizeAttribute)）应用到应用的每个 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="68702-152">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each Razor component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="68702-153">在 `_Imports.razor` 文件中使用 [`@attribute`](xref:mvc/views/razor#attribute) 指令：</span><span class="sxs-lookup"><span data-stu-id="68702-153">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="68702-154">向 `Pages` 文件夹中的每个 Razor 组件添加属性。</span><span class="sxs-lookup"><span data-stu-id="68702-154">Add the attribute to each Razor component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="68702-155">不支持使用 <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 将 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> 设置为策略。</span><span class="sxs-lookup"><span data-stu-id="68702-155">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="68702-156">刷新令牌</span><span class="sxs-lookup"><span data-stu-id="68702-156">Refresh tokens</span></span>

<span data-ttu-id="68702-157">在 Blazor WebAssembly 应用中，无法在客户端保护刷新令牌。</span><span class="sxs-lookup"><span data-stu-id="68702-157">Refresh tokens can't be secured client-side in Blazor WebAssembly apps.</span></span> <span data-ttu-id="68702-158">因此，不得将刷新令牌发送到应用以供直接使用。</span><span class="sxs-lookup"><span data-stu-id="68702-158">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="68702-159">在托管的 Blazor WebAssembly 解决方案中，服务器端应用可以维护和使用刷新令牌来访问第三方 API。</span><span class="sxs-lookup"><span data-stu-id="68702-159">Refresh tokens can be maintained and used by the server-side app in a hosted Blazor WebAssembly solution to access third-party APIs.</span></span> <span data-ttu-id="68702-160">有关详细信息，请参阅 <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>。</span><span class="sxs-lookup"><span data-stu-id="68702-160">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="68702-161">为用户建立声明</span><span class="sxs-lookup"><span data-stu-id="68702-161">Establish claims for users</span></span>

<span data-ttu-id="68702-162">应用通常要求用户声明基于对服务器的 web API 调用。</span><span class="sxs-lookup"><span data-stu-id="68702-162">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="68702-163">例如，声明常用于在应用中[建立授权](xref:blazor/security/index#authorization)。</span><span class="sxs-lookup"><span data-stu-id="68702-163">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="68702-164">在这些情况下，应用会请求访问令牌来访问服务，并使用该令牌获取声明的用户数据。</span><span class="sxs-lookup"><span data-stu-id="68702-164">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="68702-165">有关示例，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="68702-165">For examples, see the following resources:</span></span>

* [<span data-ttu-id="68702-166">其他场景：自定义用户</span><span class="sxs-lookup"><span data-stu-id="68702-166">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="68702-167">使用 Identity 服务器的 Linux 上的 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="68702-167">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="68702-168">使用 Identity 服务器部署到 Linux 上的 Azure 应用服务时，显式指定颁发者。</span><span class="sxs-lookup"><span data-stu-id="68702-168">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="68702-169">有关详细信息，请参阅 <xref:security/authentication/identity/spa#azure-app-service-on-linux>。</span><span class="sxs-lookup"><span data-stu-id="68702-169">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="windows-authentication"></a><span data-ttu-id="68702-170">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="68702-170">Windows Authentication</span></span>

<span data-ttu-id="68702-171">不建议对 Blazor Webassembly 或任何其他 SPA 框架使用 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="68702-171">We don't recommend using Windows Authentication with Blazor Webassembly or with any other SPA framework.</span></span> <span data-ttu-id="68702-172">建议使用基于令牌的协议而不是 Windows 身份验证，例如将 OIDC 用于 Active Directory 联合身份验证服务 (ADFS)。</span><span class="sxs-lookup"><span data-stu-id="68702-172">We recommend using token-based protocols instead of Windows Authentication, such as OIDC with Active Directory Federation Services (ADFS).</span></span>

<span data-ttu-id="68702-173">如果对 Blazor Webassembly 或任何其他 SPA 框架使用 Windows 身份验证，则需要采取额外措施来保护应用免受跨网站请求伪造 (CSRF) 令牌的影响。</span><span class="sxs-lookup"><span data-stu-id="68702-173">If Windows Authentication is used with Blazor Webassembly or with any other SPA framework, additional measures are required to protect the app from cross-site request forgery (CSRF) tokens.</span></span> <span data-ttu-id="68702-174">适用于 cookie 的考虑因素同样适用于 Windows 身份验证，但 Windows 身份验证不提供任何机制来阻止跨源共享身份验证上下文。</span><span class="sxs-lookup"><span data-stu-id="68702-174">The same concerns that apply to cookies apply to Windows Authentication with the addition that Windows Authentication doesn't offer any mechanism to prevent sharing of the authentication context across origins.</span></span> <span data-ttu-id="68702-175">如果应用使用 Windows 身份验证，但没有针对 CSRF 的额外保护措施，则该应用至少应限制在组织的 Intranet 中，而不能在 Internet 上使用。</span><span class="sxs-lookup"><span data-stu-id="68702-175">Apps using Windows Authentication without additional protection from CSRF should at least be restricted to an organization's intranet and not be used on the Internet.</span></span>

<span data-ttu-id="68702-176">有关信息，请参阅 <xref:security/anti-request-forgery>。</span><span class="sxs-lookup"><span data-stu-id="68702-176">For for information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="implementation-guidance"></a><span data-ttu-id="68702-177">实施指南</span><span class="sxs-lookup"><span data-stu-id="68702-177">Implementation guidance</span></span>

<span data-ttu-id="68702-178">此“概述”下的文章介绍了如何针对特定提供商对 Blazor WebAssembly 应用中的用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="68702-178">Articles under this *Overview* provide information on authenticating users in Blazor WebAssembly apps against specific providers.</span></span>

<span data-ttu-id="68702-179">独立 Blazor WebAssembly 应用：</span><span class="sxs-lookup"><span data-stu-id="68702-179">Standalone Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="68702-180">OIDC 提供程序和 WebAssembly 身份验证库的通用指南</span><span class="sxs-lookup"><span data-stu-id="68702-180">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="68702-181">Microsoft 帐户</span><span class="sxs-lookup"><span data-stu-id="68702-181">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="68702-182">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="68702-182">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="68702-183">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="68702-183">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="68702-184">托管 Blazor WebAssembly 应用：</span><span class="sxs-lookup"><span data-stu-id="68702-184">Hosted Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="68702-185">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="68702-185">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="68702-186">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="68702-186">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="68702-187">Identity 服务器</span><span class="sxs-lookup"><span data-stu-id="68702-187">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="68702-188">有关进一步的配置指南，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="68702-188">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="68702-189">有关进一步的配置指南，请参阅 <xref:blazor/security/webassembly/additional-scenarios>。</span><span class="sxs-lookup"><span data-stu-id="68702-189">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="68702-190">其他资源</span><span class="sxs-lookup"><span data-stu-id="68702-190">Additional resources</span></span>

* <span data-ttu-id="68702-191"><xref:host-and-deploy/proxy-load-balancer>：包含有关以下内容的指导：</span><span class="sxs-lookup"><span data-stu-id="68702-191"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="68702-192">使用转接头中间件跨代理服务器和内部网络保留 HTTPS 方案信息。</span><span class="sxs-lookup"><span data-stu-id="68702-192">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="68702-193">其他方案和用例，包括手动方案配置、请求路径更改以进行正确请求路由，以及转发适用于 Linux 和非 IIS 反向代理的请求方案。</span><span class="sxs-lookup"><span data-stu-id="68702-193">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
