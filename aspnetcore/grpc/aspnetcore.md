---
title: 使用 ASP.NET Core 的 gRPC 服务
author: juntaoluo
description: 了解使用 ASP.NET Core 编写 gRPC 服务时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: aeeb3d23adbdebc35ea2d2671fb04dea57451b5b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588979"
---
# <a name="grpc-services-with-aspnet-core"></a>使用 ASP.NET Core 的 gRPC 服务

本文档演示如何通过 ASP.NET Core 开始使用 gRPC 服务。

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>开始使用 ASP.NET Core 中的 gRPC 服务

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

请参阅 [gRPC 服务入门](xref:tutorials/grpc/grpc-start)，获取关于如何创建 gRPC 项目的详细说明。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

在命令行中运行 `dotnet new grpc -o GrpcGreeter`。

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>将 gRPC 服务添加到 ASP.NET Core 应用

gRPC 需要 [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) 包。

### <a name="configure-grpc"></a>配置 gRPC

在 Startup.cs 中：

* gRPC 通过 `AddGrpc` 方法启用。
* 每个 gRPC 服务均通过 `MapGrpcService` 方法添加到路由管道。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core 中间件和功能共享路由管道，因此可以将应用配置为提供其他请求处理程序。 其他请求处理程序（如 MVC 控制器）与已配置的 gRPC 服务并行工作。

## <a name="server-options"></a>服务器选项

gRPC 服务可由所有内置 ASP.NET Core 服务器托管。

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS 需要 .NET 5 和 Windows 10 内部版本 20241 或更高版本。

&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 内部版本 19529 或更高版本。

有关为 ASP.NET Core 应用选择正确的服务器的详细信息，请参阅 <xref:fundamentals/servers/index>。

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) 是一个跨平台的适用于 ASP.NET Core 的 Web 服务器。 Kestrel 提供了最佳性能和内存利用率，但它没有 HTTP.sys 中的某些高级功能，如端口共享。

Kestrel gRPC 终结点：

* 需要 HTTP/2。
* 应使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246) 进行保护。

### <a name="http2"></a>HTTP/2

gRPC 需要 HTTP/2。 适用于 ASP.NET Core 的 gRPC 验证 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) 为 `HTTP/2`。

Kestrel 在大多数新式操作系统上[支持 HTTP/2](xref:fundamentals/servers/kestrel/http2)。 默认情况下，Kestrel 终结点配置为支持 HTTP/1.1 和 HTTP/2 连接。

### <a name="tls"></a>TLS

用于 gRPC 的 Kestrel 终结点应使用 TLS 进行保护。 在开发环境中，当存在 ASP.NET Core 开发证书时，会在 `https://localhost:5001` 自动创建使用 TLS 进行保护的终结点。 不需要任何配置。 `https` 前缀验证 Kestrel 终结点是否正在使用 TLS。

在生产环境中，必须显式配置 TLS。 以下 appsettings.json 示例中提供了使用 TLS 进行保护的 HTTP/2 终结点：

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

或者，可以在 Program.cs 中配置 Kestrel 终结点：

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>协议协商

TLS 的用途不仅限于保护通信。 当终结点支持多个协议时，TLS [应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手可用于协商客户端与服务器之间的连接协议。 此协商确定连接是使用 HTTP/1.1 还是 HTTP/2。

如果在不使用 TLS 的情况下配置了 HTTP/2 终结点，则必须将终结点的 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 设置为 `HttpProtocols.Http2`。 如果没有 TLS，则无法使用具有多个协议（例如 `HttpProtocols.Http1AndHttp2`）的终结点，因为没有协商。 到不安全终结点的所有连接均默认为 HTTP/1.1，且 gRPC 调用会失败。

有关使用 Kestrel 启用 HTTP/2 和 TLS 的详细信息，请参阅 [Kestrel 终结点配置](xref:fundamentals/servers/kestrel/endpoints)。

> [!NOTE]
> macOS 不支持 ASP.NET Core gRPC 及 TLS。 在 macOS 上成功运行 gRPC 服务需要其他配置。 有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。

## <a name="iis"></a>IIS

[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) 是一种灵活、安全且可管理的 Web 服务器，用于托管 Web 应用（包括 ASP.NET Core）。 需要 .NET 5 和 Windows 10 内部版本 20241 或更高版本，才能使用 IIS 托管 gRPC 服务。

必须将 IIS 配置为使用 TLS 和 HTTP/2。 有关详细信息，请参阅 <xref:host-and-deploy/iis/protocols>。

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) 是仅在 Windows 上运行的适用于 ASP.NET Core 的 Web 服务器。 需要 .NET 5 和 Windows 10 内部版本 19529 或更高版本，才能使用 HTTP.sys 托管 gRPC 服务。

必须将 HTTP.sys 配置为使用 TLS 和 HTTP/2。 有关详细信息，请参阅 [HTTP.sys Web 服务器 HTTP/2 支持](xref:fundamentals/servers/httpsys#http2-support)。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) 是一个跨平台的适用于 ASP.NET Core 的 Web 服务器。 Kestrel 提供了最佳性能和内存利用率，但它没有 HTTP.sys 中的某些高级功能，如端口共享。

Kestrel gRPC 终结点：

* 需要 HTTP/2。
* 应使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246) 进行保护。

### <a name="http2"></a>HTTP/2

gRPC 需要 HTTP/2。 适用于 ASP.NET Core 的 gRPC 验证 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) 为 `HTTP/2`。

Kestrel 在大多数新式操作系统上[支持 HTTP/2](xref:fundamentals/servers/kestrel#http2-support)。 默认情况下，Kestrel 终结点配置为支持 HTTP/1.1 和 HTTP/2 连接。

### <a name="tls"></a>TLS

用于 gRPC 的 Kestrel 终结点应使用 TLS 进行保护。 在开发环境中，当存在 ASP.NET Core 开发证书时，会在 `https://localhost:5001` 自动创建使用 TLS 进行保护的终结点。 不需要任何配置。 `https` 前缀验证 Kestrel 终结点是否正在使用 TLS。

在生产环境中，必须显式配置 TLS。 以下 appsettings.json 示例中提供了使用 TLS 进行保护的 HTTP/2 终结点：

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

或者，可以在 Program.cs 中配置 Kestrel 终结点：

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>协议协商

TLS 的用途不仅限于保护通信。 当终结点支持多个协议时，TLS [应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手可用于协商客户端与服务器之间的连接协议。 此协商确定连接是使用 HTTP/1.1 还是 HTTP/2。

如果在不使用 TLS 的情况下配置了 HTTP/2 终结点，则必须将终结点的 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 设置为 `HttpProtocols.Http2`。 如果没有 TLS，则无法使用具有多个协议（例如 `HttpProtocols.Http1AndHttp2`）的终结点，因为没有协商。 到不安全终结点的所有连接均默认为 HTTP/1.1，且 gRPC 调用会失败。

有关使用 Kestrel 启用 HTTP/2 和 TLS 的详细信息，请参阅 [Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。

> [!NOTE]
> macOS 不支持 ASP.NET Core gRPC 及 TLS。 在 macOS 上成功运行 gRPC 服务需要其他配置。 有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a>与 ASP.NET Core API 集成

gRPC 服务对 ASP.NET Core 功能（如[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 和 [日志记录](xref:fundamentals/logging/index)）具有完全访问权限。 例如，服务实现可以通过构造函数从 DI 容器解析记录器服务：

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

默认情况下，gRPC 服务实现可以解析具有任意生存期（单一实例、范围内或暂时）的其他 DI 服务。

### <a name="resolve-httpcontext-in-grpc-methods"></a>解析 gRPC 方法中的 HttpContext

gRPC API 提供对某些 HTTP/2 消息数据（如方法、主机、标头和尾部）的访问权限。 访问是通过传递到每个 gRPC 方法的 `ServerCallContext` 参数进行的：

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` 不提供对所有 ASP.NET API 中 `HttpContext` 的完全访问权限。 `GetHttpContext` 扩展方法提供对在 ASP.NET API 中表示基础 HTTP/2 消息的 `HttpContext` 的完全访问权限：

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>其他资源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
