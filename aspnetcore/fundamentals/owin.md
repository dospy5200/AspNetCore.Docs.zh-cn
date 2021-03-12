---
title: ASP.NET Core 中 .NET 的开放 Web 接口 (OWIN)
author: ardalis
description: 了解 ASP.NET Core 如何支持 .NET 的开放 Web 接口 (OWIN)，将 Web 应用与 Web 服务器分离。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
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
uid: fundamentals/owin
ms.openlocfilehash: e476f3f62a250d960c809e5062b3bd8ca3a1940a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587226"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>ASP.NET Core 中 .NET 的开放 Web 接口 (OWIN)

作者：[Steve Smith](https://ardalis.com/) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core：

* 支持 .NET 的开放 Web 接口 (OWIN)。
* 具有与 .NET Core 兼容的 `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) 库的替换项。

OWIN 允许 Web 应用从 Web 服务器分离。 它定义了在管道中使用中间件来处理请求和相关响应的标准方法。 ASP.NET Core 应用程序和中间件可以与基于 OWIN 的应用程序、服务器和中间件进行互操作。

OWIN 提供了一个分离层，可一起使用具有不同对象模型的两个框架。 `Microsoft.AspNetCore.Owin` 包提供了两个适配器实现：

* ASP.NET Core 到 OWIN 
* OWIN 到 ASP.NET Core

此方法可将 ASP.NET Core 托管在兼容 OWIN 的服务器/主机上，或在 ASP.NET Core 上运行其他兼容 OWIN 的组件。

> [!NOTE]
> 使用这些适配器会带来性能成本。 仅使用 ASP.NET Core 组件的应用不应使用 `Microsoft.AspNetCore.Owin` 包或适配器。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/owin/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>在 ASP.NET Core 管道中运行 OWIN 中间件

ASP.NET Core 的 OWIN 支持作为 `Microsoft.AspNetCore.Owin` 包的一部分进行部署。 可通过安装此包将 OWIN 支持导入到项目中。

OWIN 中间件符合 [OWIN 规范](https://owin.org/spec/spec/owin-1.0.0.html)，该规范要求使用 `Func<IDictionary<string, object>, Task>` 接口，并设置特定的键（如 `owin.ResponseBody`）。 以下简单的 OWIN 中间件显示“Hello World”：

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

示例签名返回 `Task`，并接受 OWIN 所要求的 `IDictionary<string, object>`。

以下代码显示了如何使用 `UseOwin` 扩展方法将 `OwinHello` 中间件（如上所示）添加到 ASP.NET Core 管道。

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

可配置在 OWIN 管道中要进行的其他操作。

> [!NOTE]
> 响应标头只能在首次写入响应流之前进行修改。

> [!NOTE]
> 由于性能原因，不建议多次调用 `UseOwin`。 组合在一起时 OWIN 组件的性能最佳。

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>在基于 OWIN 的服务器上运行 ASP.NET Core 并使用其 WebSocket 支持

ASP.NET Core 如何利用基于 OWIN 的服务器功能的另一个示例是访问 WebSocket 等功能。 前面示例中使用的 .NET OWIN Web 服务器支持内置的 Web 套接字，可由 ASP.NET Core 应用程序利用。 下面的示例显示了简单的 Web 应用，它支持 Web 套接字并回显通过 WebSocket 发送到服务器的所有内容。

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a>OWIN 环境

可使用 `HttpContext` 来构造 OWIN 环境。

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWIN 键

OWIN 依赖于 `IDictionary<string,object>` 对象，以在整个 HTTP请求/响应交换中传达信息。 ASP.NET Core 实现以下所列的键。 请参阅[主规范、扩展](https://owin.org/#spec)和 [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html)（OWIN 键指南和常用键）。

### <a name="request-data-owin-v100"></a>请求数据 (OWIN v1.0.0)

| 键               | 值（类型） | 描述 |
| ----------------- | ------------ | ----------- |
| owin.RequestScheme | `String` |  |
| owin.RequestMethod  | `String` | |    
| owin.RequestPathBase  | `String` | |    
| owin.RequestPath | `String` | |     
| owin.RequestQueryString  | `String` | |    
| owin.RequestProtocol  | `String` | |    
| owin.RequestHeaders | `IDictionary<string,string[]>`  | |
| owin.RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>请求数据 (OWIN v1.1.0)

| 键               | 值（类型） | 描述 |
| ----------------- | ------------ | ----------- |
| owin.RequestId | `String` | 可选 |

### <a name="response-data-owin-v100"></a>响应数据 (OWIN v1.0.0)

| 键               | 值（类型） | 描述 |
| ----------------- | ------------ | ----------- |
| owin.ResponseStatusCode | `int` | 可选 |
| owin.ResponseReasonPhrase | `String` | 可选 |
| owin.ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin.ResponseBody | `Stream`  | |

### <a name="other-data-owin-v100"></a>其他数据 (OWIN v1.0.0)

| 键               | 值（类型） | 描述 |
| ----------------- | ------------ | ----------- |
| owin.CallCancelled | `CancellationToken` |  |
| owin.Version  | `String` | |   

### <a name="common-keys"></a>常用键

| 键               | 值（类型） | 描述 |
| ----------------- | ------------ | ----------- |
| ssl.ClientCertificate | `X509Certificate` |  |
| ssl.LoadClientCertAsync  | `Func<Task>` | |    
| server.RemoteIpAddress  | `String` | |    
| server.RemotePort | `String` | |     
| server.LocalIpAddress  | `String` | |    
| server.LocalPort  | `String` | |    
| server.IsLocal  | `bool` | |    
| server.OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| 键               | 值（类型） | 描述 |
| ----------------- | ------------ | ----------- |
| sendfile.SendAsync | 请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | 每请求 |

### <a name="opaque-v030"></a>Opaque v0.3.0

| 键               | 值（类型） | 描述 |
| ----------------- | ------------ | ----------- |
| opaque.Version | `String` |  |
| opaque.Upgrade | `OpaqueUpgrade` | 请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| opaque.Stream | `Stream` |  |
| opaque.CallCancelled | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| 键               | 值（类型） | 描述 |
| ----------------- | ------------ | ----------- |
| websocket.Version | `String` |  |
| websocket.Accept | `WebSocketAccept` | 请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket.AcceptAlt |  | 非规范 |
| websocket.SubProtocol | `String` | 请参阅 [RFC6455 4.2.2 节](https://tools.ietf.org/html/rfc6455#section-4.2.2)步骤 5.5 |
| websocket.SendAsync | `WebSocketSendAsync` | 请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.ReceiveAsync | `WebSocketReceiveAsync` | 请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CloseAsync | `WebSocketCloseAsync` | 请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CallCancelled | `CancellationToken` |  |
| websocket.ClientCloseStatus | `int` | 可选 |
| websocket.ClientCloseDescription | `String` | 可选 |

## <a name="additional-resources"></a>其他资源

* [中间件](xref:fundamentals/middleware/index)
* [服务器](xref:fundamentals/servers/index)
