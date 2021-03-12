---
title: ASP.NET Core 中的身份验证和授权 SignalR
author: bradygaster
description: 了解如何在 ASP.NET Core 中使用身份验证和授权 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
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
uid: signalr/authn-and-authz
ms.openlocfilehash: 9a3102e4451bbc5cd9ff15e88bebd4e4f2c115f4
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588095"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="4c597-103">ASP.NET Core 中的身份验证和授权 SignalR</span><span class="sxs-lookup"><span data-stu-id="4c597-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="4c597-104">作者： [Andrew Stanton](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="4c597-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="4c597-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/authn-and-authz/sample/)[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4c597-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="4c597-106">对连接到集线器的用户进行身份验证 SignalR</span><span class="sxs-lookup"><span data-stu-id="4c597-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="4c597-107">SignalR 可以与 [ASP.NET Core authentication](xref:security/authentication/identity) 一起使用，以将用户与每个连接相关联。</span><span class="sxs-lookup"><span data-stu-id="4c597-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="4c597-108">在中心中，可以从 [HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) 属性访问身份验证数据。</span><span class="sxs-lookup"><span data-stu-id="4c597-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="4c597-109">身份验证允许中心对与用户关联的所有连接调用方法。</span><span class="sxs-lookup"><span data-stu-id="4c597-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="4c597-110">有关详细信息，请参阅中的 "[管理 SignalR 用户和组" ](xref:signalr/groups)。</span><span class="sxs-lookup"><span data-stu-id="4c597-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="4c597-111">单个用户可以关联多个连接。</span><span class="sxs-lookup"><span data-stu-id="4c597-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="4c597-112">下面是 `Startup.Configure` 使用 SignalR 和 ASP.NET Core 身份验证的示例：</span><span class="sxs-lookup"><span data-stu-id="4c597-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="4c597-113">注册 SignalR 和 ASP.NET Core 身份验证中间件的顺序。</span><span class="sxs-lookup"><span data-stu-id="4c597-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="4c597-114">始终调用 `UseAuthentication` ， `UseSignalR` 以便在 SignalR 上有用户 `HttpContext` 。</span><span class="sxs-lookup"><span data-stu-id="4c597-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="4c597-115">Cookie身份验证</span><span class="sxs-lookup"><span data-stu-id="4c597-115">Cookie authentication</span></span>

<span data-ttu-id="4c597-116">在基于浏览器的应用程序中， cookie 身份验证允许现有用户凭据自动流动到 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="4c597-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="4c597-117">使用浏览器客户端时，无需进行其他配置。</span><span class="sxs-lookup"><span data-stu-id="4c597-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="4c597-118">如果用户已登录到你的应用，则 SignalR 连接将自动继承此身份验证。</span><span class="sxs-lookup"><span data-stu-id="4c597-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="4c597-119">Cookies 是一种特定于浏览器的发送访问令牌的方法，但非浏览器客户端可以发送这些令牌。</span><span class="sxs-lookup"><span data-stu-id="4c597-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="4c597-120">使用 [.Net 客户端](xref:signalr/dotnet-client)时， `Cookies` 可以在调用中配置属性 `.WithUrl` 以提供 cookie 。</span><span class="sxs-lookup"><span data-stu-id="4c597-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="4c597-121">但是，在 cookie .net 客户端中使用身份验证要求应用提供一个 API 来交换的身份验证数据 cookie 。</span><span class="sxs-lookup"><span data-stu-id="4c597-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="4c597-122">持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="4c597-122">Bearer token authentication</span></span>

<span data-ttu-id="4c597-123">客户端可以提供访问令牌，而不是使用 cookie 。</span><span class="sxs-lookup"><span data-stu-id="4c597-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="4c597-124">服务器验证令牌并使用它来标识用户。</span><span class="sxs-lookup"><span data-stu-id="4c597-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="4c597-125">仅在建立连接时才执行此验证。</span><span class="sxs-lookup"><span data-stu-id="4c597-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="4c597-126">在连接的生命周期内，服务器不会自动重新验证以检查令牌是否已吊销。</span><span class="sxs-lookup"><span data-stu-id="4c597-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="4c597-127">在 JavaScript 客户端中，可使用 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) 选项提供令牌。</span><span class="sxs-lookup"><span data-stu-id="4c597-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="4c597-128">在 .NET 客户端中，有一个类似的 [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) 属性，可用于配置令牌：</span><span class="sxs-lookup"><span data-stu-id="4c597-128">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="4c597-129">提供的访问令牌函数在发出的 **每个** HTTP 请求之前调用 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="4c597-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="4c597-130">如果你需要续订标记以便保持连接处于活动状态， (因为它可能会在连接) 期间过期，请在此函数中执行此操作，并返回已更新的令牌。</span><span class="sxs-lookup"><span data-stu-id="4c597-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="4c597-131">在标准 web Api 中，持有者令牌是在 HTTP 标头中发送的。</span><span class="sxs-lookup"><span data-stu-id="4c597-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="4c597-132">但是，在 SignalR 使用某些传输时，无法在浏览器中设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="4c597-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="4c597-133">使用 Websocket 和 Server-Sent 事件时，会将令牌作为查询字符串参数进行传输。</span><span class="sxs-lookup"><span data-stu-id="4c597-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> 

#### <a name="built-in-jwt-authentication"></a><span data-ttu-id="4c597-134">内置的 JWT 身份验证</span><span class="sxs-lookup"><span data-stu-id="4c597-134">Built-in JWT authentication</span></span>

<span data-ttu-id="4c597-135">在服务器上，使用 [JWT 持有者中间件](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)配置持有者令牌身份验证：</span><span class="sxs-lookup"><span data-stu-id="4c597-135">On the server, bearer token authentication is configured using the [JWT Bearer middleware](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="4c597-136">由于浏览器 API 限制，连接到 Websocket 和 Server-Sent 事件时，将在浏览器上使用查询字符串。</span><span class="sxs-lookup"><span data-stu-id="4c597-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="4c597-137">使用 HTTPS 时，查询字符串值受 TLS 连接保护。</span><span class="sxs-lookup"><span data-stu-id="4c597-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="4c597-138">但是，许多服务器都记录查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="4c597-138">However, many servers log query string values.</span></span> <span data-ttu-id="4c597-139">有关详细信息，请参阅[ASP.NET Core SignalR 中的安全注意事项](xref:signalr/security)。</span><span class="sxs-lookup"><span data-stu-id="4c597-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> <span data-ttu-id="4c597-140">SignalR 使用标头在支持 (如 .NET 和 Java 客户端) 的环境中传输标记。</span><span class="sxs-lookup"><span data-stu-id="4c597-140">SignalR uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

#### <a name="identity-server-jwt-authentication"></a><span data-ttu-id="4c597-141">Identity 服务器 JWT 身份验证</span><span class="sxs-lookup"><span data-stu-id="4c597-141">Identity Server JWT authentication</span></span>

<span data-ttu-id="4c597-142">使用 Identity 服务器时，将 <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> 服务添加到项目：</span><span class="sxs-lookup"><span data-stu-id="4c597-142">When using Identity Server, add a <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> service to the project:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
public class ConfigureJwtBearerOptions : IPostConfigureOptions<JwtBearerOptions>
{
    public void PostConfigure(string name, JwtBearerOptions options)
    {
        var originalOnMessageReceived = options.Events.OnMessageReceived;
        options.Events.OnMessageReceived = async context =>
        {
            await originalOnMessageReceived(context);
                
            if (string.IsNullOrEmpty(context.Token))
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;
                
                if (!string.IsNullOrEmpty(accessToken) && 
                    path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }
            }
        };
    }
}
```

<span data-ttu-id="4c597-143">将服务添加到 `Startup.ConfigureServices` 身份验证 (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) 和 Identity 服务器 () 的身份验证处理程序之后，请在中注册该服务 <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> ：</span><span class="sxs-lookup"><span data-stu-id="4c597-143">Register the service in `Startup.ConfigureServices` after adding services for authentication (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) and the authentication handler for Identity Server (<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>):</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="4c597-144">Cookie和持有者令牌</span><span class="sxs-lookup"><span data-stu-id="4c597-144">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="4c597-145">Cookie是特定于浏览器的。</span><span class="sxs-lookup"><span data-stu-id="4c597-145">Cookies are specific to browsers.</span></span> <span data-ttu-id="4c597-146">与发送持有者令牌相比，从其他类型的客户端发送这些客户端增加了复杂性。</span><span class="sxs-lookup"><span data-stu-id="4c597-146">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="4c597-147">因此， cookie 除非应用只需从浏览器客户端对用户进行身份验证，否则不建议进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4c597-147">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="4c597-148">当使用浏览器客户端之外的客户端时，建议使用持有者令牌身份验证。</span><span class="sxs-lookup"><span data-stu-id="4c597-148">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="4c597-149">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="4c597-149">Windows authentication</span></span>

<span data-ttu-id="4c597-150">如果在你的应用中配置了 [Windows 身份验证](xref:security/authentication/windowsauth) ，则 SignalR 可以使用该标识来保护中心。</span><span class="sxs-lookup"><span data-stu-id="4c597-150">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="4c597-151">但是，若要将消息发送给单个用户，则需要添加自定义用户 ID 提供程序。</span><span class="sxs-lookup"><span data-stu-id="4c597-151">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="4c597-152">Windows 身份验证系统不提供 "名称标识符" 声明。</span><span class="sxs-lookup"><span data-stu-id="4c597-152">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> <span data-ttu-id="4c597-153">SignalR 使用声明来确定用户名。</span><span class="sxs-lookup"><span data-stu-id="4c597-153">SignalR uses the claim to determine the user name.</span></span>

<span data-ttu-id="4c597-154">添加一个新类，该类实现 `IUserIdProvider` 并检索用户要用作标识符的声明之一。</span><span class="sxs-lookup"><span data-stu-id="4c597-154">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="4c597-155">例如，若要使用 "名称" 声明 (是) 格式的 Windows 用户名 `[Domain]\[Username]` ，请创建以下类：</span><span class="sxs-lookup"><span data-stu-id="4c597-155">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="4c597-156">`ClaimTypes.Name`您可以使用 (中的任何值 `User` ，例如 Windows SID 标识符，等等) 。</span><span class="sxs-lookup"><span data-stu-id="4c597-156">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="4c597-157">您选择的值在系统中的所有用户中必须是唯一的。</span><span class="sxs-lookup"><span data-stu-id="4c597-157">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="4c597-158">否则，用于一个用户的消息可能最终会转到其他用户。</span><span class="sxs-lookup"><span data-stu-id="4c597-158">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="4c597-159">在方法中注册此组件 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="4c597-159">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="4c597-160">在 .NET 客户端中，必须通过设置 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) 属性来启用 Windows 身份验证：</span><span class="sxs-lookup"><span data-stu-id="4c597-160">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="4c597-161">Internet Explorer 和 Microsoft Edge 支持 Windows 身份验证，但在所有浏览器中都不支持。</span><span class="sxs-lookup"><span data-stu-id="4c597-161">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="4c597-162">例如，在 Chrome 和 Safari 中，尝试使用 Windows 身份验证和 Websocket 会失败。</span><span class="sxs-lookup"><span data-stu-id="4c597-162">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="4c597-163">当 Windows 身份验证失败时，客户端将尝试回退到可能工作的其他传输。</span><span class="sxs-lookup"><span data-stu-id="4c597-163">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="4c597-164">使用声明自定义标识处理</span><span class="sxs-lookup"><span data-stu-id="4c597-164">Use claims to customize identity handling</span></span>

<span data-ttu-id="4c597-165">对用户进行身份验证的应用可以 SignalR 从用户声明派生用户 id。</span><span class="sxs-lookup"><span data-stu-id="4c597-165">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="4c597-166">若要指定 SignalR 创建用户 id 的方式，请实现 `IUserIdProvider` 并注册实现。</span><span class="sxs-lookup"><span data-stu-id="4c597-166">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="4c597-167">示例代码演示了如何使用声明选择用户的电子邮件地址作为识别属性。</span><span class="sxs-lookup"><span data-stu-id="4c597-167">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="4c597-168">您选择的值在系统中的所有用户中必须是唯一的。</span><span class="sxs-lookup"><span data-stu-id="4c597-168">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="4c597-169">否则，用于一个用户的消息可能最终会转到其他用户。</span><span class="sxs-lookup"><span data-stu-id="4c597-169">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="4c597-170">帐户注册会将类型为的声明添加 `ClaimsTypes.Email` 到 ASP.NET 标识数据库。</span><span class="sxs-lookup"><span data-stu-id="4c597-170">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="4c597-171">在中注册此组件 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="4c597-171">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="4c597-172">授权用户访问集线器和集线器方法</span><span class="sxs-lookup"><span data-stu-id="4c597-172">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="4c597-173">默认情况下，集线器中的所有方法都可由未经身份验证的用户调用。</span><span class="sxs-lookup"><span data-stu-id="4c597-173">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="4c597-174">若要要求身份验证，请将 [授权](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 属性应用于中心：</span><span class="sxs-lookup"><span data-stu-id="4c597-174">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="4c597-175">可使用 `[Authorize]` 特性的构造函数参数和属性将访问权限仅限于匹配特定[授权策略](xref:security/authorization/policies)的用户。</span><span class="sxs-lookup"><span data-stu-id="4c597-175">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="4c597-176">例如，如果你有一个名为的自定义授权策略， `MyAuthorizationPolicy` 则可以确保只有符合该策略的用户才能使用以下代码访问该中心：</span><span class="sxs-lookup"><span data-stu-id="4c597-176">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="4c597-177">单个集线器方法 `[Authorize]` 也可以应用该属性。</span><span class="sxs-lookup"><span data-stu-id="4c597-177">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="4c597-178">如果当前用户与应用于方法的策略不匹配，则会向调用方返回错误：</span><span class="sxs-lookup"><span data-stu-id="4c597-178">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="4c597-179">使用授权处理程序自定义集线器方法授权</span><span class="sxs-lookup"><span data-stu-id="4c597-179">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="4c597-180">SignalR 当集线器方法要求授权时，为授权处理程序提供自定义资源。</span><span class="sxs-lookup"><span data-stu-id="4c597-180">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="4c597-181">资源是 `HubInvocationContext` 的一个实例。</span><span class="sxs-lookup"><span data-stu-id="4c597-181">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="4c597-182">`HubInvocationContext`包括 `HubCallerContext` 、正在调用的集线器方法的名称，以及中心方法的参数。</span><span class="sxs-lookup"><span data-stu-id="4c597-182">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="4c597-183">请考虑允许通过 Azure Active Directory 多个组织登录的聊天室的示例。</span><span class="sxs-lookup"><span data-stu-id="4c597-183">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="4c597-184">拥有 Microsoft 帐户的任何人都可以登录到聊天，但只有拥有组织的成员才能阻止用户或查看用户的聊天历史记录。</span><span class="sxs-lookup"><span data-stu-id="4c597-184">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="4c597-185">而且，我们可能希望限制某些用户的某些功能。</span><span class="sxs-lookup"><span data-stu-id="4c597-185">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="4c597-186">使用 ASP.NET Core 3.0 中的更新功能，这是完全可能的。</span><span class="sxs-lookup"><span data-stu-id="4c597-186">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="4c597-187">请注意如何 `DomainRestrictedRequirement` 充当自定义 `IAuthorizationRequirement` 。</span><span class="sxs-lookup"><span data-stu-id="4c597-187">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="4c597-188">既然 `HubInvocationContext` 正在传入资源参数，内部逻辑就可以检查正在调用中心的上下文，并决定是否允许用户执行单个集线器方法。</span><span class="sxs-lookup"><span data-stu-id="4c597-188">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="4c597-189">在中 `Startup.ConfigureServices` ，添加新策略，并将自定义 `DomainRestrictedRequirement` 要求用作创建策略的参数 `DomainRestricted` 。</span><span class="sxs-lookup"><span data-stu-id="4c597-189">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="4c597-190">在前面的示例中， `DomainRestrictedRequirement` 类既是 `IAuthorizationRequirement` 又是 `AuthorizationHandler` 该要求的。</span><span class="sxs-lookup"><span data-stu-id="4c597-190">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="4c597-191">可以将这两个组件拆分为单独的类，以分隔问题。</span><span class="sxs-lookup"><span data-stu-id="4c597-191">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="4c597-192">该示例方法的优点是，无需在 `AuthorizationHandler` 启动过程中注入，因为要求和处理程序是相同的。</span><span class="sxs-lookup"><span data-stu-id="4c597-192">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4c597-193">其他资源</span><span class="sxs-lookup"><span data-stu-id="4c597-193">Additional resources</span></span>

* [<span data-ttu-id="4c597-194">ASP.NET Core 中的持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="4c597-194">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="4c597-195">基于资源的授权</span><span class="sxs-lookup"><span data-stu-id="4c597-195">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
