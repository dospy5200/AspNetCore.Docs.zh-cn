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
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="818f9-103">使用 ASP.NET Core 的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="818f9-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="818f9-104">本文档演示如何通过 ASP.NET Core 开始使用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="818f9-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="818f9-105">先决条件</span><span class="sxs-lookup"><span data-stu-id="818f9-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="818f9-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="818f9-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="818f9-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="818f9-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="818f9-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="818f9-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="818f9-109">开始使用 ASP.NET Core 中的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="818f9-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="818f9-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="818f9-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="818f9-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="818f9-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="818f9-112">请参阅 [gRPC 服务入门](xref:tutorials/grpc/grpc-start)，获取关于如何创建 gRPC 项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="818f9-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="818f9-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="818f9-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="818f9-114">在命令行中运行 `dotnet new grpc -o GrpcGreeter`。</span><span class="sxs-lookup"><span data-stu-id="818f9-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="818f9-115">将 gRPC 服务添加到 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="818f9-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="818f9-116">gRPC 需要 [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) 包。</span><span class="sxs-lookup"><span data-stu-id="818f9-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="818f9-117">配置 gRPC</span><span class="sxs-lookup"><span data-stu-id="818f9-117">Configure gRPC</span></span>

<span data-ttu-id="818f9-118">在 Startup.cs 中：</span><span class="sxs-lookup"><span data-stu-id="818f9-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="818f9-119">gRPC 通过 `AddGrpc` 方法启用。</span><span class="sxs-lookup"><span data-stu-id="818f9-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="818f9-120">每个 gRPC 服务均通过 `MapGrpcService` 方法添加到路由管道。</span><span class="sxs-lookup"><span data-stu-id="818f9-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="818f9-121">ASP.NET Core 中间件和功能共享路由管道，因此可以将应用配置为提供其他请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="818f9-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="818f9-122">其他请求处理程序（如 MVC 控制器）与已配置的 gRPC 服务并行工作。</span><span class="sxs-lookup"><span data-stu-id="818f9-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="818f9-123">服务器选项</span><span class="sxs-lookup"><span data-stu-id="818f9-123">Server options</span></span>

<span data-ttu-id="818f9-124">gRPC 服务可由所有内置 ASP.NET Core 服务器托管。</span><span class="sxs-lookup"><span data-stu-id="818f9-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="818f9-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="818f9-125">Kestrel</span></span>
> * <span data-ttu-id="818f9-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="818f9-126">TestServer</span></span>
> * <span data-ttu-id="818f9-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="818f9-127">IIS&dagger;</span></span>
> * <span data-ttu-id="818f9-128">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="818f9-128">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="818f9-129">&dagger;IIS 需要 .NET 5 和 Windows 10 内部版本 20241 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="818f9-129">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="818f9-130">&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 内部版本 19529 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="818f9-130">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="818f9-131">有关为 ASP.NET Core 应用选择正确的服务器的详细信息，请参阅 <xref:fundamentals/servers/index>。</span><span class="sxs-lookup"><span data-stu-id="818f9-131">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="818f9-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="818f9-132">Kestrel</span></span>

<span data-ttu-id="818f9-133">[Kestrel](xref:fundamentals/servers/kestrel) 是一个跨平台的适用于 ASP.NET Core 的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="818f9-133">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="818f9-134">Kestrel 提供了最佳性能和内存利用率，但它没有 HTTP.sys 中的某些高级功能，如端口共享。</span><span class="sxs-lookup"><span data-stu-id="818f9-134">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="818f9-135">Kestrel gRPC 终结点：</span><span class="sxs-lookup"><span data-stu-id="818f9-135">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="818f9-136">需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="818f9-136">Require HTTP/2.</span></span>
* <span data-ttu-id="818f9-137">应使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246) 进行保护。</span><span class="sxs-lookup"><span data-stu-id="818f9-137">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="818f9-138">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="818f9-138">HTTP/2</span></span>

<span data-ttu-id="818f9-139">gRPC 需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="818f9-139">gRPC requires HTTP/2.</span></span> <span data-ttu-id="818f9-140">适用于 ASP.NET Core 的 gRPC 验证 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) 为 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="818f9-140">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="818f9-141">Kestrel 在大多数新式操作系统上[支持 HTTP/2](xref:fundamentals/servers/kestrel/http2)。</span><span class="sxs-lookup"><span data-stu-id="818f9-141">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="818f9-142">默认情况下，Kestrel 终结点配置为支持 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="818f9-142">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="818f9-143">TLS</span><span class="sxs-lookup"><span data-stu-id="818f9-143">TLS</span></span>

<span data-ttu-id="818f9-144">用于 gRPC 的 Kestrel 终结点应使用 TLS 进行保护。</span><span class="sxs-lookup"><span data-stu-id="818f9-144">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="818f9-145">在开发环境中，当存在 ASP.NET Core 开发证书时，会在 `https://localhost:5001` 自动创建使用 TLS 进行保护的终结点。</span><span class="sxs-lookup"><span data-stu-id="818f9-145">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="818f9-146">不需要任何配置。</span><span class="sxs-lookup"><span data-stu-id="818f9-146">No configuration is required.</span></span> <span data-ttu-id="818f9-147">`https` 前缀验证 Kestrel 终结点是否正在使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="818f9-147">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="818f9-148">在生产环境中，必须显式配置 TLS。</span><span class="sxs-lookup"><span data-stu-id="818f9-148">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="818f9-149">以下 appsettings.json 示例中提供了使用 TLS 进行保护的 HTTP/2 终结点：</span><span class="sxs-lookup"><span data-stu-id="818f9-149">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="818f9-150">或者，可以在 Program.cs 中配置 Kestrel 终结点：</span><span class="sxs-lookup"><span data-stu-id="818f9-150">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="818f9-151">协议协商</span><span class="sxs-lookup"><span data-stu-id="818f9-151">Protocol negotiation</span></span>

<span data-ttu-id="818f9-152">TLS 的用途不仅限于保护通信。</span><span class="sxs-lookup"><span data-stu-id="818f9-152">TLS is used for more than securing communication.</span></span> <span data-ttu-id="818f9-153">当终结点支持多个协议时，TLS [应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手可用于协商客户端与服务器之间的连接协议。</span><span class="sxs-lookup"><span data-stu-id="818f9-153">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="818f9-154">此协商确定连接是使用 HTTP/1.1 还是 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="818f9-154">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="818f9-155">如果在不使用 TLS 的情况下配置了 HTTP/2 终结点，则必须将终结点的 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) 设置为 `HttpProtocols.Http2`。</span><span class="sxs-lookup"><span data-stu-id="818f9-155">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="818f9-156">如果没有 TLS，则无法使用具有多个协议（例如 `HttpProtocols.Http1AndHttp2`）的终结点，因为没有协商。</span><span class="sxs-lookup"><span data-stu-id="818f9-156">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="818f9-157">到不安全终结点的所有连接均默认为 HTTP/1.1，且 gRPC 调用会失败。</span><span class="sxs-lookup"><span data-stu-id="818f9-157">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="818f9-158">有关使用 Kestrel 启用 HTTP/2 和 TLS 的详细信息，请参阅 [Kestrel 终结点配置](xref:fundamentals/servers/kestrel/endpoints)。</span><span class="sxs-lookup"><span data-stu-id="818f9-158">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="818f9-159">macOS 不支持 ASP.NET Core gRPC 及 TLS。</span><span class="sxs-lookup"><span data-stu-id="818f9-159">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="818f9-160">在 macOS 上成功运行 gRPC 服务需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="818f9-160">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="818f9-161">有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="818f9-161">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="818f9-162">IIS</span><span class="sxs-lookup"><span data-stu-id="818f9-162">IIS</span></span>

<span data-ttu-id="818f9-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) 是一种灵活、安全且可管理的 Web 服务器，用于托管 Web 应用（包括 ASP.NET Core）。</span><span class="sxs-lookup"><span data-stu-id="818f9-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="818f9-164">需要 .NET 5 和 Windows 10 内部版本 20241 或更高版本，才能使用 IIS 托管 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="818f9-164">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="818f9-165">必须将 IIS 配置为使用 TLS 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="818f9-165">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="818f9-166">有关详细信息，请参阅 <xref:host-and-deploy/iis/protocols>。</span><span class="sxs-lookup"><span data-stu-id="818f9-166">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="818f9-167">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="818f9-167">HTTP.sys</span></span>

<span data-ttu-id="818f9-168">[HTTP.sys](xref:fundamentals/servers/httpsys) 是仅在 Windows 上运行的适用于 ASP.NET Core 的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="818f9-168">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="818f9-169">需要 .NET 5 和 Windows 10 内部版本 19529 或更高版本，才能使用 HTTP.sys 托管 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="818f9-169">.NET 5 and Windows 10 Build 19529 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="818f9-170">必须将 HTTP.sys 配置为使用 TLS 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="818f9-170">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="818f9-171">有关详细信息，请参阅 [HTTP.sys Web 服务器 HTTP/2 支持](xref:fundamentals/servers/httpsys#http2-support)。</span><span class="sxs-lookup"><span data-stu-id="818f9-171">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="818f9-172">Kestrel</span><span class="sxs-lookup"><span data-stu-id="818f9-172">Kestrel</span></span>

<span data-ttu-id="818f9-173">[Kestrel](xref:fundamentals/servers/kestrel) 是一个跨平台的适用于 ASP.NET Core 的 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="818f9-173">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="818f9-174">Kestrel 提供了最佳性能和内存利用率，但它没有 HTTP.sys 中的某些高级功能，如端口共享。</span><span class="sxs-lookup"><span data-stu-id="818f9-174">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="818f9-175">Kestrel gRPC 终结点：</span><span class="sxs-lookup"><span data-stu-id="818f9-175">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="818f9-176">需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="818f9-176">Require HTTP/2.</span></span>
* <span data-ttu-id="818f9-177">应使用[传输层安全性 (TLS)](https://tools.ietf.org/html/rfc5246) 进行保护。</span><span class="sxs-lookup"><span data-stu-id="818f9-177">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="818f9-178">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="818f9-178">HTTP/2</span></span>

<span data-ttu-id="818f9-179">gRPC 需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="818f9-179">gRPC requires HTTP/2.</span></span> <span data-ttu-id="818f9-180">适用于 ASP.NET Core 的 gRPC 验证 [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) 为 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="818f9-180">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="818f9-181">Kestrel 在大多数新式操作系统上[支持 HTTP/2](xref:fundamentals/servers/kestrel#http2-support)。</span><span class="sxs-lookup"><span data-stu-id="818f9-181">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="818f9-182">默认情况下，Kestrel 终结点配置为支持 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="818f9-182">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="818f9-183">TLS</span><span class="sxs-lookup"><span data-stu-id="818f9-183">TLS</span></span>

<span data-ttu-id="818f9-184">用于 gRPC 的 Kestrel 终结点应使用 TLS 进行保护。</span><span class="sxs-lookup"><span data-stu-id="818f9-184">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="818f9-185">在开发环境中，当存在 ASP.NET Core 开发证书时，会在 `https://localhost:5001` 自动创建使用 TLS 进行保护的终结点。</span><span class="sxs-lookup"><span data-stu-id="818f9-185">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="818f9-186">不需要任何配置。</span><span class="sxs-lookup"><span data-stu-id="818f9-186">No configuration is required.</span></span> <span data-ttu-id="818f9-187">`https` 前缀验证 Kestrel 终结点是否正在使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="818f9-187">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="818f9-188">在生产环境中，必须显式配置 TLS。</span><span class="sxs-lookup"><span data-stu-id="818f9-188">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="818f9-189">以下 appsettings.json 示例中提供了使用 TLS 进行保护的 HTTP/2 终结点：</span><span class="sxs-lookup"><span data-stu-id="818f9-189">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="818f9-190">或者，可以在 Program.cs 中配置 Kestrel 终结点：</span><span class="sxs-lookup"><span data-stu-id="818f9-190">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="818f9-191">协议协商</span><span class="sxs-lookup"><span data-stu-id="818f9-191">Protocol negotiation</span></span>

<span data-ttu-id="818f9-192">TLS 的用途不仅限于保护通信。</span><span class="sxs-lookup"><span data-stu-id="818f9-192">TLS is used for more than securing communication.</span></span> <span data-ttu-id="818f9-193">当终结点支持多个协议时，TLS [应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手可用于协商客户端与服务器之间的连接协议。</span><span class="sxs-lookup"><span data-stu-id="818f9-193">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="818f9-194">此协商确定连接是使用 HTTP/1.1 还是 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="818f9-194">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="818f9-195">如果在不使用 TLS 的情况下配置了 HTTP/2 终结点，则必须将终结点的 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 设置为 `HttpProtocols.Http2`。</span><span class="sxs-lookup"><span data-stu-id="818f9-195">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="818f9-196">如果没有 TLS，则无法使用具有多个协议（例如 `HttpProtocols.Http1AndHttp2`）的终结点，因为没有协商。</span><span class="sxs-lookup"><span data-stu-id="818f9-196">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="818f9-197">到不安全终结点的所有连接均默认为 HTTP/1.1，且 gRPC 调用会失败。</span><span class="sxs-lookup"><span data-stu-id="818f9-197">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="818f9-198">有关使用 Kestrel 启用 HTTP/2 和 TLS 的详细信息，请参阅 [Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="818f9-198">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="818f9-199">macOS 不支持 ASP.NET Core gRPC 及 TLS。</span><span class="sxs-lookup"><span data-stu-id="818f9-199">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="818f9-200">在 macOS 上成功运行 gRPC 服务需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="818f9-200">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="818f9-201">有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="818f9-201">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="818f9-202">与 ASP.NET Core API 集成</span><span class="sxs-lookup"><span data-stu-id="818f9-202">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="818f9-203">gRPC 服务对 ASP.NET Core 功能（如[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 和 [日志记录](xref:fundamentals/logging/index)）具有完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="818f9-203">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="818f9-204">例如，服务实现可以通过构造函数从 DI 容器解析记录器服务：</span><span class="sxs-lookup"><span data-stu-id="818f9-204">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="818f9-205">默认情况下，gRPC 服务实现可以解析具有任意生存期（单一实例、范围内或暂时）的其他 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="818f9-205">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="818f9-206">解析 gRPC 方法中的 HttpContext</span><span class="sxs-lookup"><span data-stu-id="818f9-206">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="818f9-207">gRPC API 提供对某些 HTTP/2 消息数据（如方法、主机、标头和尾部）的访问权限。</span><span class="sxs-lookup"><span data-stu-id="818f9-207">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="818f9-208">访问是通过传递到每个 gRPC 方法的 `ServerCallContext` 参数进行的：</span><span class="sxs-lookup"><span data-stu-id="818f9-208">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="818f9-209">`ServerCallContext` 不提供对所有 ASP.NET API 中 `HttpContext` 的完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="818f9-209">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="818f9-210">`GetHttpContext` 扩展方法提供对在 ASP.NET API 中表示基础 HTTP/2 消息的 `HttpContext` 的完全访问权限：</span><span class="sxs-lookup"><span data-stu-id="818f9-210">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="818f9-211">其他资源</span><span class="sxs-lookup"><span data-stu-id="818f9-211">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
