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
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>gRPC for ASP.NET Core 中的身份验证和授权

作者：[James Newton-King](https://twitter.com/jamesnk)

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/grpc/authn-and-authz/sample/)[（如何下载）](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>对调用 gRPC 服务的用户进行身份验证

gRPC 可与 [ASP.NET Core 身份验证](xref:security/authentication/identity)配合使用，将用户与每个调用关联。

以下是使用 gRPC 和 ASP.NET Core 身份验证的 `Startup.Configure` 的示例：

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
> 注册 ASP.NET Core 身份验证中间件的顺序很重要。 始终在 `UseRouting` 之后和 `UseEndpoints` 之前调用 `UseAuthentication` 和 `UseAuthorization`。

应用在调用期间使用的身份验证机制需要进行配置。 身份验证配置已添加到 `Startup.ConfigureServices` 中，并因应用使用的身份验证机制而异。 有关如何保护 ASP.NET Core 应用的示例，请参阅[身份验证示例](xref:security/authentication/samples)。

设置身份验证后，可通过 `ServerCallContext` 使用 gRPC 服务方法访问用户。

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>持有者令牌身份验证

客户端可提供用于身份验证的访问令牌。 服务器验证令牌并使用它来标识用户。

在服务器上，使用 [JWT 持有者中间件](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)配置持有者令牌身份验证。

在 .NET gRPC 客户端中，令牌可通过 `Metadata` 集合与调用一起发送。 `Metadata` 集合中的条目以 HTTP 标头的形式与 gRPC 调用一起发送：

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

在通道上配置 `ChannelCredentials` 是通过 gRPC 调用将令牌发送到服务的备用方法。 `ChannelCredentials` 可包含 `CallCredentials`，这使得能够自动设置 `Metadata`。

`CallCredentials` 在每次进行 gRPC 调用时运行，因而无需在多个位置编写代码用于自行传递令牌。 请注意，仅当通道通过 TLS 进行保护时，才应用 `CallCredentials`。 `CallCredentials` 不适用于不安全的非 TLS 通道。

以下示例中的凭据将通道配置为随每个 gRPC 调用发送令牌：

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

### <a name="client-certificate-authentication"></a>客户端证书身份验证

客户端还可以提供用于身份验证的客户端证书。 [证书身份验证](https://tools.ietf.org/html/rfc5246#section-7.4.4)在 TLS 级别发生，远在到达 ASP.NET Core 之前。 当请求进入 ASP.NET Core 时，可借助[客户端证书身份验证包](xref:security/authentication/certauth)将证书解析为 `ClaimsPrincipal`。

> [!NOTE]
> 将服务器配置为接受客户端证书。 有关在 Kestrel、IIS 和 Azure 中接受客户端证书的信息，请参阅 <xref:security/authentication/certauth#configure-your-server-to-require-certificates>。

在 .NET gRPC 客户端中，客户端证书已添加到 `HttpClientHandler` 中，后者之后用于创建 gRPC 客户端：

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

### <a name="other-authentication-mechanisms"></a>其他身份验证机制

许多 ASP.NET Core 支持的身份验证机制都可以与 gRPC 配合使用：

* Azure Active Directory
* 客户端证书
* IdentityServer
* JWT 令牌
* OAuth 2.0
* OpenID Connect
* WS-Federation

有关在服务器上配置身份验证的详细信息，请参阅 [ASP.NET Core 身份验证](xref:security/authentication/identity)。

将 gRPC 客户端配置为使用身份验证取决于使用的身份验证机制。 之前的持有者令牌和客户端证书示例演示可将 gRPC 客户端配置为通过 gRPC 调用发送身份验证元数据的几种方法：

* 强类型 gRPC 客户端在内部使用 `HttpClient`。 可在 [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) 上配置身份验证，也可通过向 `HttpClient` 添加自定义 [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) 实例进行配置。
* 每个 gRPC 调用都有一个可选的 `CallOptions` 参数。 可使用该选项的标头集合发送自定义标头。

> [!NOTE]
> Windows 身份验证（NTLM/Kerberos/协商）不能与 gRPC 配合使用。 gRPC 需要 HTTP/2，而 HTTP/2 不支持 Windows 身份验证。

## <a name="authorize-users-to-access-services-and-service-methods"></a>授权用户访问服务和服务方法

默认情况下，未经身份验证的用户可以调用服务中的所有方法。 若要要求进行身份验证，请将 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于服务：

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

可使用 `[Authorize]` 特性的构造函数参数和属性将访问权限仅限于匹配特定[授权策略](xref:security/authorization/policies)的用户。 例如，如果有一个名为 `MyAuthorizationPolicy` 的自定义授权策略，请使用以下代码确保仅匹配该策略的用户才能访问服务：

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

各个服务方法也可以应用 `[Authorize]` 特性。 如果当前用户与 **同时** 应用于方法和类的策略不匹配，则会向调用方返回错误：

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

## <a name="additional-resources"></a>其他资源

* [ASP.NET Core 中的持有者令牌身份验证](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [在 ASP.NET Core 中配置客户端证书身份验证](xref:security/authentication/certauth)
