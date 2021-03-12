---
title: gRPC for ASP.NET Core 中的身份验证和授权
author: jamesnk
description: 了解如何在 gRPC for ASP.NET Core 中使用身份验证和授权。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 5120964459a81ef2d668877bb08ecde512c2389d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587991"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="f44ab-103">gRPC for ASP.NET Core 中的身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="f44ab-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="f44ab-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="f44ab-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="f44ab-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/)[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f44ab-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="f44ab-106">对调用 gRPC 服务的用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="f44ab-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="f44ab-107">gRPC 可与 [ASP.NET Core 身份验证](xref:security/authentication/identity)配合使用，将用户与每个调用关联。</span><span class="sxs-lookup"><span data-stu-id="f44ab-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="f44ab-108">以下是使用 gRPC 和 ASP.NET Core 身份验证的 `Startup.Configure` 的示例：</span><span class="sxs-lookup"><span data-stu-id="f44ab-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="f44ab-109">注册 ASP.NET Core 身份验证中间件的顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="f44ab-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="f44ab-110">始终在 `UseRouting` 之后和 `UseEndpoints` 之前调用 `UseAuthentication` 和 `UseAuthorization`。</span><span class="sxs-lookup"><span data-stu-id="f44ab-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="f44ab-111">应用在调用期间使用的身份验证机制需要进行配置。</span><span class="sxs-lookup"><span data-stu-id="f44ab-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="f44ab-112">身份验证配置已添加到 `Startup.ConfigureServices` 中，并因应用使用的身份验证机制而异。</span><span class="sxs-lookup"><span data-stu-id="f44ab-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="f44ab-113">有关如何保护 ASP.NET Core 应用的示例，请参阅[身份验证示例](xref:security/authentication/samples)。</span><span class="sxs-lookup"><span data-stu-id="f44ab-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="f44ab-114">设置身份验证后，可通过 `ServerCallContext` 使用 gRPC 服务方法访问用户。</span><span class="sxs-lookup"><span data-stu-id="f44ab-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="f44ab-115">持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="f44ab-115">Bearer token authentication</span></span>

<span data-ttu-id="f44ab-116">客户端可提供用于身份验证的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f44ab-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="f44ab-117">服务器验证令牌并使用它来标识用户。</span><span class="sxs-lookup"><span data-stu-id="f44ab-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="f44ab-118">在服务器上，使用 [JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)配置持有者令牌身份验证。</span><span class="sxs-lookup"><span data-stu-id="f44ab-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="f44ab-119">在 .NET gRPC 客户端中，令牌可通过 `Metadata` 集合与调用一起发送。</span><span class="sxs-lookup"><span data-stu-id="f44ab-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="f44ab-120">`Metadata` 集合中的条目以 HTTP 标头的形式与 gRPC 调用一起发送：</span><span class="sxs-lookup"><span data-stu-id="f44ab-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

<span data-ttu-id="f44ab-121">在通道上配置 `ChannelCredentials` 是通过 gRPC 调用将令牌发送到服务的备用方法。</span><span class="sxs-lookup"><span data-stu-id="f44ab-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="f44ab-122">`ChannelCredentials` 可包含 `CallCredentials`，这使得能够自动设置 `Metadata`。</span><span class="sxs-lookup"><span data-stu-id="f44ab-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="f44ab-123">`CallCredentials` 在每次进行 gRPC 调用时运行，因而无需在多个位置编写代码用于自行传递令牌。</span><span class="sxs-lookup"><span data-stu-id="f44ab-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="f44ab-124">请注意，仅当通道通过 TLS 进行保护时，才应用 `CallCredentials`。</span><span class="sxs-lookup"><span data-stu-id="f44ab-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="f44ab-125">`CallCredentials` 不适用于不安全的非 TLS 通道。</span><span class="sxs-lookup"><span data-stu-id="f44ab-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="f44ab-126">以下示例中的凭据将通道配置为随每个 gRPC 调用发送令牌：</span><span class="sxs-lookup"><span data-stu-id="f44ab-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="f44ab-127">客户端证书身份验证</span><span class="sxs-lookup"><span data-stu-id="f44ab-127">Client certificate authentication</span></span>

<span data-ttu-id="f44ab-128">客户端还可以提供用于身份验证的客户端证书。</span><span class="sxs-lookup"><span data-stu-id="f44ab-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="f44ab-129">[证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)在 TLS 级别发生，远在到达 ASP.NET Core 之前。</span><span class="sxs-lookup"><span data-stu-id="f44ab-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="f44ab-130">当请求进入 ASP.NET Core 时，可借助[客户端证书身份验证包](xref:security/authentication/certauth)将证书解析为 `ClaimsPrincipal`。</span><span class="sxs-lookup"><span data-stu-id="f44ab-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="f44ab-131">将服务器配置为接受客户端证书。</span><span class="sxs-lookup"><span data-stu-id="f44ab-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="f44ab-132">有关在 Kestrel、IIS 和 Azure 中接受客户端证书的信息，请参阅 <xref:security/authentication/certauth#configure-your-server-to-require-certificates>。</span><span class="sxs-lookup"><span data-stu-id="f44ab-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="f44ab-133">在 .NET gRPC 客户端中，客户端证书已添加到 `HttpClientHandler` 中，后者之后用于创建 gRPC 客户端：</span><span class="sxs-lookup"><span data-stu-id="f44ab-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="f44ab-134">其他身份验证机制</span><span class="sxs-lookup"><span data-stu-id="f44ab-134">Other authentication mechanisms</span></span>

<span data-ttu-id="f44ab-135">许多 ASP.NET Core 支持的身份验证机制都可以与 gRPC 配合使用：</span><span class="sxs-lookup"><span data-stu-id="f44ab-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="f44ab-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="f44ab-136">Azure Active Directory</span></span>
* <span data-ttu-id="f44ab-137">客户端证书</span><span class="sxs-lookup"><span data-stu-id="f44ab-137">Client Certificate</span></span>
* <span data-ttu-id="f44ab-138">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="f44ab-138">IdentityServer</span></span>
* <span data-ttu-id="f44ab-139">JWT 令牌</span><span class="sxs-lookup"><span data-stu-id="f44ab-139">JWT Token</span></span>
* <span data-ttu-id="f44ab-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="f44ab-140">OAuth 2.0</span></span>
* <span data-ttu-id="f44ab-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="f44ab-141">OpenID Connect</span></span>
* <span data-ttu-id="f44ab-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="f44ab-142">WS-Federation</span></span>

<span data-ttu-id="f44ab-143">有关在服务器上配置身份验证的详细信息，请参阅 [ASP.NET Core 身份验证](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="f44ab-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="f44ab-144">将 gRPC 客户端配置为使用身份验证取决于使用的身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="f44ab-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="f44ab-145">之前的持有者令牌和客户端证书示例演示可将 gRPC 客户端配置为通过 gRPC 调用发送身份验证元数据的几种方法：</span><span class="sxs-lookup"><span data-stu-id="f44ab-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="f44ab-146">强类型 gRPC 客户端在内部使用 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="f44ab-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="f44ab-147">可在 [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) 上配置身份验证，也可通过向 `HttpClient` 添加自定义 [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) 实例进行配置。</span><span class="sxs-lookup"><span data-stu-id="f44ab-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="f44ab-148">每个 gRPC 调用都有一个可选的 `CallOptions` 参数。</span><span class="sxs-lookup"><span data-stu-id="f44ab-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="f44ab-149">可使用该选项的标头集合发送自定义标头。</span><span class="sxs-lookup"><span data-stu-id="f44ab-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="f44ab-150">Windows 身份验证（NTLM/Kerberos/协商）不能与 gRPC 配合使用。</span><span class="sxs-lookup"><span data-stu-id="f44ab-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="f44ab-151">gRPC 需要 HTTP/2，而 HTTP/2 不支持 Windows 身份验证。</span><span class="sxs-lookup"><span data-stu-id="f44ab-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="f44ab-152">授权用户访问服务和服务方法</span><span class="sxs-lookup"><span data-stu-id="f44ab-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="f44ab-153">默认情况下，未经身份验证的用户可以调用服务中的所有方法。</span><span class="sxs-lookup"><span data-stu-id="f44ab-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="f44ab-154">若要要求进行身份验证，请将 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于服务：</span><span class="sxs-lookup"><span data-stu-id="f44ab-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="f44ab-155">可使用 `[Authorize]` 特性的构造函数参数和属性将访问权限仅限于匹配特定[授权策略](xref:security/authorization/policies)的用户。</span><span class="sxs-lookup"><span data-stu-id="f44ab-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="f44ab-156">例如，如果有一个名为 `MyAuthorizationPolicy` 的自定义授权策略，请使用以下代码确保仅匹配该策略的用户才能访问服务：</span><span class="sxs-lookup"><span data-stu-id="f44ab-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="f44ab-157">各个服务方法也可以应用 `[Authorize]` 特性。</span><span class="sxs-lookup"><span data-stu-id="f44ab-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="f44ab-158">如果当前用户与 **同时** 应用于方法和类的策略不匹配，则会向调用方返回错误：</span><span class="sxs-lookup"><span data-stu-id="f44ab-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="f44ab-159">其他资源</span><span class="sxs-lookup"><span data-stu-id="f44ab-159">Additional resources</span></span>

* [<span data-ttu-id="f44ab-160">ASP.NET Core 中的持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="f44ab-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="f44ab-161">在 ASP.NET Core 中配置客户端证书身份验证</span><span class="sxs-lookup"><span data-stu-id="f44ab-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
