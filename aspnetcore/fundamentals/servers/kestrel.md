---
title: ASP.NET Core 中的 Kestrel Web 服务器实现
author: rick-anderson
description: 了解跨平台 ASP.NET Core Web 服务器 Kestrel。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 54d7304430c2a77347b6e3279f638cf6d7add77e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589033"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="3f816-103">ASP.NET Core 中的 Kestrel Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="3f816-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="3f816-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Chris Ross](https://github.com/Tratcher) 和 [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="3f816-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3f816-105">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="3f816-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3f816-106">Kestrel 是包含在 ASP.NET Core 项目模板中的 Web 服务器，默认处于启用状态。</span><span class="sxs-lookup"><span data-stu-id="3f816-106">Kestrel is the web server that's included and enabled by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3f816-107">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="3f816-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3f816-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3f816-108">HTTPS</span></span>
* <span data-ttu-id="3f816-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2)（在 macOS&dagger; 上除外）</span><span class="sxs-lookup"><span data-stu-id="3f816-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (except on macOS&dagger;)</span></span>
* <span data-ttu-id="3f816-110">用于启用 [WebSocket](xref:fundamentals/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="3f816-110">Opaque upgrade used to enable [WebSockets](xref:fundamentals/websockets)</span></span>
* <span data-ttu-id="3f816-111">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-111">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="3f816-112">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="3f816-113">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3f816-114">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/5.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3f816-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started"></a><span data-ttu-id="3f816-115">入门</span><span class="sxs-lookup"><span data-stu-id="3f816-115">Get started</span></span>

<span data-ttu-id="3f816-116">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-116">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3f816-117">在“Program.cs”中，<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="3f816-117">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="3f816-118">有关生成主机的详细信息，请参阅 <xref:fundamentals/host/generic-host#set-up-a-host> 的“设置主机”和“默认生成器设置”部分 。</span><span class="sxs-lookup"><span data-stu-id="3f816-118">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f816-119">其他资源</span><span class="sxs-lookup"><span data-stu-id="3f816-119">Additional resources</span></span>

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="3f816-120">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="3f816-120">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* <span data-ttu-id="3f816-121">在 Linux 上使用 UNIX 套接字时，该套接字不会在应用关闭时自动删除。</span><span class="sxs-lookup"><span data-stu-id="3f816-121">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="3f816-122">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="3f816-122">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>

> [!NOTE]
> <span data-ttu-id="3f816-123">从 ASP.NET Core 5.0 开始，Kestrel 的 libuv 传输将过时。</span><span class="sxs-lookup"><span data-stu-id="3f816-123">As of ASP.NET Core 5.0, Kestrel's libuv transport is obsolete.</span></span> <span data-ttu-id="3f816-124">libuv 传输不会接收用于支持新 OS 平台（如 Windows ARM64）的更新，并会在将来的版本中被删除。</span><span class="sxs-lookup"><span data-stu-id="3f816-124">The libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="3f816-125">删除对过时 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> 方法的任何调用并改为使用 Kestrel 的默认套接字传输。</span><span class="sxs-lookup"><span data-stu-id="3f816-125">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3f816-126">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="3f816-126">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3f816-127">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="3f816-127">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3f816-128">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="3f816-128">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3f816-129">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3f816-129">HTTPS</span></span>
* <span data-ttu-id="3f816-130">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="3f816-130">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3f816-131">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-131">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="3f816-132">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="3f816-132">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="3f816-133">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-133">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="3f816-134">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-134">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3f816-135">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/3.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3f816-135">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="3f816-136">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="3f816-136">HTTP/2 support</span></span>

<span data-ttu-id="3f816-137">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="3f816-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="3f816-138">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="3f816-138">Operating system&dagger;</span></span>
  * <span data-ttu-id="3f816-139">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="3f816-139">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="3f816-140">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="3f816-140">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="3f816-141">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f816-141">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="3f816-142">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="3f816-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="3f816-143">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="3f816-143">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3f816-144">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-144">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="3f816-145">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="3f816-145">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="3f816-146">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="3f816-146">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="3f816-147">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-147">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="3f816-148">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="3f816-148">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="3f816-149">从 .NET Core 3.0 开始，HTTP/2 默认处于启用状态。</span><span class="sxs-lookup"><span data-stu-id="3f816-149">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="3f816-150">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="3f816-150">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3f816-151">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="3f816-151">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3f816-152">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="3f816-152">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3f816-153">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-153">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3f816-154">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="3f816-154">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3f816-156">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-156">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3f816-158">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-158">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3f816-159">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-159">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3f816-160">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="3f816-160">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3f816-161">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-161">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3f816-162">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="3f816-162">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3f816-163">反向代理：</span><span class="sxs-lookup"><span data-stu-id="3f816-163">A reverse proxy:</span></span>

* <span data-ttu-id="3f816-164">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="3f816-164">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3f816-165">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="3f816-165">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3f816-166">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="3f816-166">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3f816-167">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-167">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3f816-168">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="3f816-168">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3f816-169">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3f816-169">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3f816-170">ASP.NET Core 应用中的 Kestrel</span><span class="sxs-lookup"><span data-stu-id="3f816-170">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3f816-171">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-171">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3f816-172">在“Program.cs”中，<xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="3f816-172">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="3f816-173">有关生成主机的详细信息，请参阅 <xref:fundamentals/host/generic-host#set-up-a-host> 的“设置主机”和“默认生成器设置”部分 。</span><span class="sxs-lookup"><span data-stu-id="3f816-173">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="3f816-174">若要在调用 `ConfigureWebHostDefaults` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="3f816-174">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="3f816-175">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="3f816-175">Kestrel options</span></span>

<span data-ttu-id="3f816-176">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="3f816-176">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3f816-177">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="3f816-177">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3f816-178">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="3f816-178">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3f816-179">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="3f816-179">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3f816-180">在本文后面的示例中，Kestrel 选项是采用 C# 代码配置的。</span><span class="sxs-lookup"><span data-stu-id="3f816-180">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="3f816-181">还可以使用 [配置提供程序](xref:fundamentals/configuration/index)设置 Kestrel 选项。</span><span class="sxs-lookup"><span data-stu-id="3f816-181">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3f816-182">例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-182">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="3f816-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 和 [终结点配置](#endpoint-configuration) 可以通过配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="3f816-184">其余的 Kestrel 配置必须采用 C# 代码进行配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-184">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="3f816-185">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="3f816-185">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3f816-186">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3f816-186">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3f816-187">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="3f816-187">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3f816-188">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="3f816-188">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3f816-189">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3f816-189">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="3f816-190">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3f816-190">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3f816-191">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3f816-191">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="3f816-192">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="3f816-192">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3f816-193">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="3f816-193">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3f816-194">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="3f816-194">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3f816-195">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="3f816-195">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="3f816-196">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="3f816-196">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3f816-197">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="3f816-197">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="3f816-198">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-198">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3f816-199">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-199">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="3f816-200">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="3f816-200">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3f816-201">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="3f816-201">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3f816-202">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="3f816-202">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3f816-203">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="3f816-203">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3f816-204">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="3f816-204">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="3f816-205">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="3f816-205">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3f816-206">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3f816-206">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3f816-207">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-207">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3f816-208">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-208">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3f816-209">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="3f816-209">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3f816-210">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="3f816-210">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3f816-211">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="3f816-211">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3f816-212">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="3f816-212">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3f816-213">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="3f816-213">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3f816-214">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-214">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3f816-215">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="3f816-215">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3f816-216">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="3f816-216">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="3f816-217">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="3f816-217">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="3f816-218">用于 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>，因为鉴于协议支持请求多路复用，HTTP/2 通常不支持按请求修改速率限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="3f816-219">不过，用于 HTTP/2 请求的 `HttpContext.Features` 中仍存在 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature>，因为仍可以通过将 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 设置为 `null`（甚至对于 HTTP/2 请求），按请求完全禁用读取速率限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-219">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="3f816-220">对于给定 HTTP/2 请求，尝试读取 `IHttpMinRequestBodyDataRateFeature.MinDataRate` 或尝试将它设置为除 `null` 以外的值会导致 `NotSupportedException` 抛出。</span><span class="sxs-lookup"><span data-stu-id="3f816-220">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="3f816-221">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-221">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="3f816-222">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="3f816-222">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3f816-223">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="3f816-223">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3f816-224">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="3f816-224">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="3f816-225">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="3f816-225">Maximum streams per connection</span></span>

<span data-ttu-id="3f816-226">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="3f816-226">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="3f816-227">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="3f816-227">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="3f816-228">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="3f816-228">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="3f816-229">标题表大小</span><span class="sxs-lookup"><span data-stu-id="3f816-229">Header table size</span></span>

<span data-ttu-id="3f816-230">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="3f816-230">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="3f816-231">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="3f816-231">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="3f816-232">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="3f816-232">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="3f816-233">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="3f816-233">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="3f816-234">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="3f816-234">Maximum frame size</span></span>

<span data-ttu-id="3f816-235">`Http2.MaxFrameSize` 表示服务器接收或发送的 HTTP/2 连接帧有效负载的最大允许大小。</span><span class="sxs-lookup"><span data-stu-id="3f816-235">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="3f816-236">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="3f816-236">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="3f816-237">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="3f816-237">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="3f816-238">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="3f816-238">Maximum request header size</span></span>

<span data-ttu-id="3f816-239">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="3f816-239">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="3f816-240">此限制适用于名称和值的压缩和未压缩表示形式。</span><span class="sxs-lookup"><span data-stu-id="3f816-240">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="3f816-241">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="3f816-241">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="3f816-242">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="3f816-242">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="3f816-243">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="3f816-243">Initial connection window size</span></span>

<span data-ttu-id="3f816-244">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f816-244">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="3f816-245">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-245">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3f816-246">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="3f816-246">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="3f816-247">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="3f816-247">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="3f816-248">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="3f816-248">Initial stream window size</span></span>

<span data-ttu-id="3f816-249">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f816-249">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="3f816-250">请求也受 `Http2.InitialConnectionWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-250">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="3f816-251">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="3f816-251">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="3f816-252">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="3f816-252">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="3f816-253">预告片</span><span class="sxs-lookup"><span data-stu-id="3f816-253">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="3f816-254">重置</span><span class="sxs-lookup"><span data-stu-id="3f816-254">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="3f816-255">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="3f816-255">Synchronous I/O</span></span>

<span data-ttu-id="3f816-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="3f816-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="3f816-257">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="3f816-257">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="3f816-258">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-258">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3f816-259">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="3f816-259">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="3f816-260">以下示例会启用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="3f816-260">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="3f816-261">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="3f816-261">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3f816-262">终结点配置</span><span class="sxs-lookup"><span data-stu-id="3f816-262">Endpoint configuration</span></span>

<span data-ttu-id="3f816-263">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="3f816-263">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3f816-264">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="3f816-264">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3f816-265">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="3f816-265">Specify URLs using the:</span></span>

* <span data-ttu-id="3f816-266">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f816-266">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3f816-267">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3f816-267">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3f816-268">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="3f816-268">`urls` host configuration key.</span></span>
* <span data-ttu-id="3f816-269">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="3f816-269">`UseUrls` extension method.</span></span>

<span data-ttu-id="3f816-270">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="3f816-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3f816-271">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="3f816-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3f816-272">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="3f816-272">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3f816-273">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="3f816-273">A development certificate is created:</span></span>

* <span data-ttu-id="3f816-274">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="3f816-274">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3f816-275">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-275">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3f816-276">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-276">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3f816-277">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3f816-277">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3f816-278">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-278">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3f816-279">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="3f816-279">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3f816-280">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-280">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3f816-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3f816-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3f816-282">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-282">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3f816-283">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-283">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="3f816-284">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3f816-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3f816-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3f816-286">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-286">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3f816-287">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-287">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="3f816-288">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-288">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="3f816-289">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3f816-289">Configure(IConfiguration)</span></span>

<span data-ttu-id="3f816-290">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-290">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3f816-291">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="3f816-291">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3f816-292">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3f816-292">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3f816-293">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-293">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3f816-294">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="3f816-294">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3f816-295">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-295">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3f816-296">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3f816-296">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="3f816-297">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="3f816-297">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3f816-298">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="3f816-298">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3f816-299">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="3f816-299">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3f816-300">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-300">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3f816-301">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="3f816-301">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3f816-302">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="3f816-302">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3f816-303">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="3f816-303">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3f816-304">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-304">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3f816-305">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="3f816-305">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3f816-306">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-306">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3f816-307">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-307">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3f816-308">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-308">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3f816-309">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-309">Supported configurations described next:</span></span>

* <span data-ttu-id="3f816-310">无配置</span><span class="sxs-lookup"><span data-stu-id="3f816-310">No configuration</span></span>
* <span data-ttu-id="3f816-311">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="3f816-311">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3f816-312">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="3f816-312">Change the defaults in code</span></span>

<span data-ttu-id="3f816-313">*无配置*</span><span class="sxs-lookup"><span data-stu-id="3f816-313">*No configuration*</span></span>

<span data-ttu-id="3f816-314">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="3f816-314">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3f816-315">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="3f816-315">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3f816-316">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-316">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3f816-317">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="3f816-317">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3f816-318">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-318">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3f816-319">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="3f816-319">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3f816-320">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="3f816-320">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3f816-321">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="3f816-321">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="3f816-322">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="3f816-322">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3f816-323">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="3f816-323">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3f816-324">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="3f816-324">Schema notes:</span></span>

* <span data-ttu-id="3f816-325">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3f816-325">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3f816-326">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="3f816-326">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3f816-327">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="3f816-327">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3f816-328">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="3f816-328">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3f816-329">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="3f816-329">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3f816-330">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="3f816-330">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3f816-331">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="3f816-331">The `Certificate` section is optional.</span></span> <span data-ttu-id="3f816-332">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-332">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3f816-333">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="3f816-333">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3f816-334">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="3f816-334">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3f816-335">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-335">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3f816-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="3f816-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="3f816-337">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="3f816-337">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3f816-338">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-338">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3f816-339">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-339">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3f816-340">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="3f816-340">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3f816-341">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="3f816-341">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3f816-342">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-342">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3f816-343">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-343">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3f816-344">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="3f816-344">*Change the defaults in code*</span></span>

<span data-ttu-id="3f816-345">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-345">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3f816-346">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="3f816-346">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="3f816-347">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="3f816-347">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3f816-348">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="3f816-348">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3f816-349">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-349">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3f816-350">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="3f816-350">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3f816-351">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="3f816-351">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3f816-352">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-352">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3f816-353">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="3f816-353">SNI support requires:</span></span>

* <span data-ttu-id="3f816-354">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="3f816-354">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3f816-355">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3f816-355">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3f816-356">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3f816-356">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3f816-357">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="3f816-357">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3f816-358">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-358">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="3f816-359">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="3f816-359">Connection logging</span></span>

<span data-ttu-id="3f816-360">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="3f816-360">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="3f816-361">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="3f816-361">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="3f816-362">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="3f816-362">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="3f816-363">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="3f816-363">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3f816-364">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-364">Bind to a TCP socket</span></span>

<span data-ttu-id="3f816-365"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-365">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="3f816-366">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-366">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3f816-367">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-367">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3f816-368">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-368">Bind to a Unix socket</span></span>

<span data-ttu-id="3f816-369">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3f816-369">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="3f816-370">在 Nginx 配置文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="3f816-370">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="3f816-371">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="3f816-371">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="3f816-372">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="3f816-372">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="3f816-373">端口 0</span><span class="sxs-lookup"><span data-stu-id="3f816-373">Port 0</span></span>

<span data-ttu-id="3f816-374">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-374">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3f816-375">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="3f816-375">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3f816-376">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="3f816-376">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3f816-377">限制</span><span class="sxs-lookup"><span data-stu-id="3f816-377">Limitations</span></span>

<span data-ttu-id="3f816-378">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="3f816-378">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3f816-379">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="3f816-379">`--urls` command-line argument</span></span>
* <span data-ttu-id="3f816-380">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="3f816-380">`urls` host configuration key</span></span>
* <span data-ttu-id="3f816-381">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="3f816-381">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3f816-382">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="3f816-382">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3f816-383">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="3f816-383">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3f816-384">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="3f816-384">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3f816-385">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-385">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3f816-386">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="3f816-386">IIS endpoint configuration</span></span>

<span data-ttu-id="3f816-387">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="3f816-387">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3f816-388">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="3f816-388">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="3f816-389">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="3f816-389">ListenOptions.Protocols</span></span>

<span data-ttu-id="3f816-390">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="3f816-390">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="3f816-391">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="3f816-391">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="3f816-392">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="3f816-392">`HttpProtocols` enum value</span></span> | <span data-ttu-id="3f816-393">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="3f816-393">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="3f816-394">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3f816-394">HTTP/1.1 only.</span></span> <span data-ttu-id="3f816-395">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="3f816-395">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="3f816-396">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-396">HTTP/2 only.</span></span> <span data-ttu-id="3f816-397">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="3f816-397">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="3f816-398">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-398">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="3f816-399">HTTP/2 要求客户端在 TLS [应用层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 握手过程中选择 HTTP/2；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3f816-399">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="3f816-400">任何终结点的默认 `ListenOptions.Protocols` 值都为 `HttpProtocols.Http1AndHttp2`。</span><span class="sxs-lookup"><span data-stu-id="3f816-400">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="3f816-401">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="3f816-401">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="3f816-402">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f816-402">TLS version 1.2 or later</span></span>
* <span data-ttu-id="3f816-403">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="3f816-403">Renegotiation disabled</span></span>
* <span data-ttu-id="3f816-404">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="3f816-404">Compression disabled</span></span>
* <span data-ttu-id="3f816-405">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="3f816-405">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="3f816-406">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;：最小 224 位</span><span class="sxs-lookup"><span data-stu-id="3f816-406">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="3f816-407">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;：最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="3f816-407">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="3f816-408">不禁止密码套件。</span><span class="sxs-lookup"><span data-stu-id="3f816-408">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="3f816-409">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="3f816-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="3f816-410">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-410">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="3f816-411">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="3f816-411">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="3f816-412">使用连接中间件，针对特定密码的每个连接筛选 TLS 握手（如有必要）。</span><span class="sxs-lookup"><span data-stu-id="3f816-412">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="3f816-413">下面的示例针对应用不支持的任何密码算法引发 <xref:System.NotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="3f816-413">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="3f816-414">或者，定义 [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) 并将其与可接受的密码套件列表进行比较。</span><span class="sxs-lookup"><span data-stu-id="3f816-414">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="3f816-415">没有哪种加密是使用 [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) 密码算法。</span><span class="sxs-lookup"><span data-stu-id="3f816-415">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="3f816-416">连接筛选也可以通过 <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda 进行配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-416">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="3f816-417">在 Linux 上，<xref:System.Net.Security.CipherSuitesPolicy> 可用于针对每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="3f816-417">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="3f816-418">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="3f816-418">*Set the protocol from configuration*</span></span>

<span data-ttu-id="3f816-419">`CreateDefaultBuilder` 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-419">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="3f816-420">以下 appsettings.json 示例将 HTTP/1.1 建立为所有终结点的默认连接协议：</span><span class="sxs-lookup"><span data-stu-id="3f816-420">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="3f816-421">以下 appsettings.json 示例将为所有指定终结点建立 HTTP/1.1 连接协议：</span><span class="sxs-lookup"><span data-stu-id="3f816-421">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="3f816-422">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="3f816-422">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="3f816-423">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="3f816-423">URL prefixes</span></span>

<span data-ttu-id="3f816-424">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="3f816-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3f816-425">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="3f816-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3f816-426">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3f816-427">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="3f816-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3f816-428">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="3f816-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3f816-429">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="3f816-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3f816-430">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="3f816-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3f816-431">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="3f816-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3f816-432">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="3f816-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3f816-433">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="3f816-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3f816-434">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="3f816-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3f816-435">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3f816-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3f816-436">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="3f816-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3f816-437">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="3f816-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3f816-438">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3f816-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3f816-439">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="3f816-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3f816-440">主机筛选</span><span class="sxs-lookup"><span data-stu-id="3f816-440">Host filtering</span></span>

<span data-ttu-id="3f816-441">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="3f816-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3f816-442">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="3f816-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3f816-443">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="3f816-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3f816-444">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="3f816-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3f816-445">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="3f816-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3f816-446">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包为 ASP.NET Core 应用隐式提供。</span><span class="sxs-lookup"><span data-stu-id="3f816-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="3f816-447">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="3f816-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3f816-448">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="3f816-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3f816-449">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="3f816-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3f816-450">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="3f816-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3f816-451">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3f816-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3f816-452">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="3f816-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3f816-453">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="3f816-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3f816-454">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3f816-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3f816-455">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3f816-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3f816-456">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="3f816-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="3f816-457">Libuv 传输配置</span><span class="sxs-lookup"><span data-stu-id="3f816-457">Libuv transport configuration</span></span>

<span data-ttu-id="3f816-458">对于需要使用 Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>) 的项目：</span><span class="sxs-lookup"><span data-stu-id="3f816-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="3f816-459">将 [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) 包的依赖项添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="3f816-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="3f816-460">调用 `IWebHostBuilder` 上的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>：</span><span class="sxs-lookup"><span data-stu-id="3f816-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

## <a name="http11-request-draining"></a><span data-ttu-id="3f816-461">HTTP/1.1 请求排出</span><span class="sxs-lookup"><span data-stu-id="3f816-461">HTTP/1.1 request draining</span></span>

<span data-ttu-id="3f816-462">打开 HTTP 连接非常耗时。</span><span class="sxs-lookup"><span data-stu-id="3f816-462">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="3f816-463">对于 HTTPS 而言，这也是资源密集型。</span><span class="sxs-lookup"><span data-stu-id="3f816-463">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="3f816-464">因此，Kestrel 会尝试按 HTTP/1.1 协议重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-464">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="3f816-465">请求正文必须完全使用才能允许重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-465">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="3f816-466">应用不会始终使用请求正文，例如 `POST` 请求，其中服务器返回重定向或 404 响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-466">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="3f816-467">在 `POST` 重定向的情况下：</span><span class="sxs-lookup"><span data-stu-id="3f816-467">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="3f816-468">客户端可能已发送部分 `POST` 数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-468">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="3f816-469">服务器写入 301 响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-469">The server writes the 301 response.</span></span>
* <span data-ttu-id="3f816-470">在完全读取上一个请求正文中的 `POST` 数据之前，不能将连接用于新请求。</span><span class="sxs-lookup"><span data-stu-id="3f816-470">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="3f816-471">Kestrel 尝试排出请求正文。</span><span class="sxs-lookup"><span data-stu-id="3f816-471">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="3f816-472">排出请求正文意味着读取和丢弃数据，而不处理数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-472">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="3f816-473">排出过程会在允许重新使用连接与排出任何剩余数据所用的时间之间进行权衡：</span><span class="sxs-lookup"><span data-stu-id="3f816-473">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="3f816-474">排出超时为五秒，该值不可配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-474">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="3f816-475">如果 `Content-Length` 或 `Transfer-Encoding` 标头指定的所有数据在超时之前都未被读取，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="3f816-475">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="3f816-476">有时，你可能想要在写入响应之前或之后立即终止请求。</span><span class="sxs-lookup"><span data-stu-id="3f816-476">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="3f816-477">例如，客户端可能具有限制性的数据上限，因此可以优先考虑限制上传的数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-477">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="3f816-478">在这种情况下，若要终止请求，请从控制器、Razor 页面或中间件调用 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)。</span><span class="sxs-lookup"><span data-stu-id="3f816-478">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="3f816-479">在调用 `Abort` 时，存在一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="3f816-479">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="3f816-480">创建新连接可能会很慢且成本高昂。</span><span class="sxs-lookup"><span data-stu-id="3f816-480">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="3f816-481">在连接关闭之前无法保证客户端已读取响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-481">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="3f816-482">应极少调用 `Abort`，并且应将此操作留给严重错误情况（而不是常见错误情况）。</span><span class="sxs-lookup"><span data-stu-id="3f816-482">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="3f816-483">只有在需要解决特定问题时才调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-483">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="3f816-484">例如，如果恶意客户端正在尝试 `POST` 数据或在客户端代码中存在导致大量请求的 bug，则调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-484">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="3f816-485">不要为常见错误情况（如 HTTP 404（找不到））调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-485">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="3f816-486">调用 `Abort` 之前调用 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) 可确保服务器已完成写入响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-486">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="3f816-487">但是，客户端行为是不可预测的，在连接中止前它们可能未读取响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-487">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="3f816-488">对于 HTTP/2，此过程又有所不同，因为协议支持在不关闭连接的情况下中止单个请求流。</span><span class="sxs-lookup"><span data-stu-id="3f816-488">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="3f816-489">五秒排出超时不适用。</span><span class="sxs-lookup"><span data-stu-id="3f816-489">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="3f816-490">如果在完成响应后存在任何未读的请求正文数据，则服务器会发送 HTTP/2 RST 帧。</span><span class="sxs-lookup"><span data-stu-id="3f816-490">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="3f816-491">其他请求正文数据帧将被忽略。</span><span class="sxs-lookup"><span data-stu-id="3f816-491">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="3f816-492">如果可能，客户端最好使用 [Expect:100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 请求标头，然后等到服务器响应后再开始发送请求正文。</span><span class="sxs-lookup"><span data-stu-id="3f816-492">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="3f816-493">这样，客户端便有机会在发送不需要的数据之前检查响应和中止。</span><span class="sxs-lookup"><span data-stu-id="3f816-493">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f816-494">其他资源</span><span class="sxs-lookup"><span data-stu-id="3f816-494">Additional resources</span></span>

* <span data-ttu-id="3f816-495">在 Linux 上使用 UNIX 套接字时，该套接字不会在应用关闭时自动删除。</span><span class="sxs-lookup"><span data-stu-id="3f816-495">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="3f816-496">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="3f816-496">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="3f816-497">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="3f816-497">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3f816-498">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="3f816-498">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3f816-499">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="3f816-499">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3f816-500">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="3f816-500">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3f816-501">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3f816-501">HTTPS</span></span>
* <span data-ttu-id="3f816-502">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="3f816-502">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3f816-503">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-503">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="3f816-504">HTTP/2（除 macOS&dagger; 以外）</span><span class="sxs-lookup"><span data-stu-id="3f816-504">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="3f816-505">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-505">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="3f816-506">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-506">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3f816-507">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3f816-507">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="3f816-508">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="3f816-508">HTTP/2 support</span></span>

<span data-ttu-id="3f816-509">如果满足以下基本要求，将为 ASP.NET Core 应用提供 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="3f816-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="3f816-510">操作系统&dagger;</span><span class="sxs-lookup"><span data-stu-id="3f816-510">Operating system&dagger;</span></span>
  * <span data-ttu-id="3f816-511">Windows Server 2016/Windows 10 或更高版本&Dagger;</span><span class="sxs-lookup"><span data-stu-id="3f816-511">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="3f816-512">具有 OpenSSL 1.0.2 或更高版本的 Linux（例如，Ubuntu 16.04 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="3f816-512">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="3f816-513">目标框架：.NET Core 2.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f816-513">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="3f816-514">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="3f816-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="3f816-515">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="3f816-515">TLS 1.2 or later connection</span></span>

<span data-ttu-id="3f816-516">macOS 的未来版本将支持 &dagger;HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-516">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="3f816-517">&Dagger;Kestrel 在 Windows Server 2012 R2 和 Windows 8.1 上对 HTTP/2 的支持有限。</span><span class="sxs-lookup"><span data-stu-id="3f816-517">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="3f816-518">支持受限是因为可在这些操作系统上使用的受支持 TLS 密码套件列表有限。</span><span class="sxs-lookup"><span data-stu-id="3f816-518">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="3f816-519">可能需要使用椭圆曲线数字签名算法 (ECDSA) 生成的证书来保护 TLS 连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-519">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="3f816-520">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="3f816-520">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="3f816-521">默认情况下，禁用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-521">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="3f816-522">有关配置的详细信息，请参阅 [Kestrel 选项](#kestrel-options)和 [ListenOptions.Protocols](#listenoptionsprotocols) 部分。</span><span class="sxs-lookup"><span data-stu-id="3f816-522">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3f816-523">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="3f816-523">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3f816-524">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="3f816-524">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3f816-525">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-525">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3f816-526">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="3f816-526">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3f816-528">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-528">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3f816-530">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-530">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3f816-531">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-531">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3f816-532">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="3f816-532">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3f816-533">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-533">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3f816-534">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="3f816-534">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3f816-535">反向代理：</span><span class="sxs-lookup"><span data-stu-id="3f816-535">A reverse proxy:</span></span>

* <span data-ttu-id="3f816-536">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="3f816-536">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3f816-537">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="3f816-537">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3f816-538">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="3f816-538">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3f816-539">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-539">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3f816-540">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="3f816-540">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3f816-541">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3f816-541">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3f816-542">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="3f816-542">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3f816-543">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="3f816-543">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3f816-544">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-544">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3f816-545">在 Program.cs 中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="3f816-545">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="3f816-546">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”部分。</span><span class="sxs-lookup"><span data-stu-id="3f816-546">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="3f816-547">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请使用 `ConfigureKestrel`：</span><span class="sxs-lookup"><span data-stu-id="3f816-547">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="3f816-548">如果应用未调用 `CreateDefaultBuilder` 来设置主机，请在调用 `ConfigureKestrel` 之前先调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="3f816-548">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="3f816-549">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="3f816-549">Kestrel options</span></span>

<span data-ttu-id="3f816-550">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="3f816-550">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3f816-551">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="3f816-551">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3f816-552">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="3f816-552">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3f816-553">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="3f816-553">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3f816-554">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-554">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3f816-555">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-555">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="3f816-556">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="3f816-556">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3f816-557">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3f816-557">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3f816-558">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="3f816-558">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3f816-559">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="3f816-559">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3f816-560">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3f816-560">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="3f816-561">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3f816-561">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3f816-562">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3f816-562">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="3f816-563">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="3f816-563">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3f816-564">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="3f816-564">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3f816-565">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="3f816-565">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3f816-566">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="3f816-566">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="3f816-567">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="3f816-567">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3f816-568">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="3f816-568">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="3f816-569">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-569">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3f816-570">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-570">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="3f816-571">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="3f816-571">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3f816-572">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="3f816-572">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3f816-573">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="3f816-573">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3f816-574">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="3f816-574">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3f816-575">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="3f816-575">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="3f816-576">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="3f816-576">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3f816-577">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3f816-577">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3f816-578">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-578">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3f816-579">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-579">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3f816-580">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="3f816-580">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3f816-581">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="3f816-581">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3f816-582">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="3f816-582">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3f816-583">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="3f816-583">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3f816-584">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="3f816-584">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3f816-585">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-585">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3f816-586">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="3f816-586">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3f816-587">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="3f816-587">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="3f816-588">在中间件中替代每个请求的最低速率限制：</span><span class="sxs-lookup"><span data-stu-id="3f816-588">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="3f816-589">由于协议支持请求多路复用，HTTP/2 不支持基于每个请求修改速率限制，因此 HTTP/2 请求的 `HttpContext.Features` 中不存在前面示例中引用的速率特性。</span><span class="sxs-lookup"><span data-stu-id="3f816-589">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="3f816-590">通过 `KestrelServerOptions.Limits` 配置的服务器范围的速率限制仍适用于 HTTP/1.x 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-590">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="3f816-591">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="3f816-591">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3f816-592">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="3f816-592">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3f816-593">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="3f816-593">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="3f816-594">每个连接的最大流</span><span class="sxs-lookup"><span data-stu-id="3f816-594">Maximum streams per connection</span></span>

<span data-ttu-id="3f816-595">`Http2.MaxStreamsPerConnection` 限制每个 HTTP/2 连接的并发请求流的数量。</span><span class="sxs-lookup"><span data-stu-id="3f816-595">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="3f816-596">拒绝过多的流。</span><span class="sxs-lookup"><span data-stu-id="3f816-596">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="3f816-597">默认值为 100。</span><span class="sxs-lookup"><span data-stu-id="3f816-597">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="3f816-598">标题表大小</span><span class="sxs-lookup"><span data-stu-id="3f816-598">Header table size</span></span>

<span data-ttu-id="3f816-599">HPACK 解码器解压缩 HTTP/2 连接的 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="3f816-599">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="3f816-600">`Http2.HeaderTableSize` 限制 HPACK 解码器使用的标头压缩表的大小。</span><span class="sxs-lookup"><span data-stu-id="3f816-600">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="3f816-601">该值以八位字节提供，且必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="3f816-601">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="3f816-602">默认值为 4096。</span><span class="sxs-lookup"><span data-stu-id="3f816-602">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="3f816-603">最大帧大小</span><span class="sxs-lookup"><span data-stu-id="3f816-603">Maximum frame size</span></span>

<span data-ttu-id="3f816-604">`Http2.MaxFrameSize` 指示要接收的 HTTP/2 连接帧有效负载的最大大小。</span><span class="sxs-lookup"><span data-stu-id="3f816-604">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="3f816-605">该值以八位字节提供，必须介于 2^14 (16,384) 和 2^24-1 (16,777,215) 之间。</span><span class="sxs-lookup"><span data-stu-id="3f816-605">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="3f816-606">默认值为 2^14 (16,384)。</span><span class="sxs-lookup"><span data-stu-id="3f816-606">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="3f816-607">最大请求标头大小</span><span class="sxs-lookup"><span data-stu-id="3f816-607">Maximum request header size</span></span>

<span data-ttu-id="3f816-608">`Http2.MaxRequestHeaderFieldSize` 表示请求标头值的允许的最大大小（用八进制表示）。</span><span class="sxs-lookup"><span data-stu-id="3f816-608">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="3f816-609">此限制同时适用于压缩和未压缩表示形式中的名称和值。</span><span class="sxs-lookup"><span data-stu-id="3f816-609">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="3f816-610">该值必须大于零 (0)。</span><span class="sxs-lookup"><span data-stu-id="3f816-610">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="3f816-611">默认值为 8,192。</span><span class="sxs-lookup"><span data-stu-id="3f816-611">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="3f816-612">初始连接窗口大小</span><span class="sxs-lookup"><span data-stu-id="3f816-612">Initial connection window size</span></span>

<span data-ttu-id="3f816-613">`Http2.InitialConnectionWindowSize` 表示服务器一次性缓存的最大请求主体数据大小（每次连接时在所有请求（流）中汇总，以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f816-613">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="3f816-614">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-614">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3f816-615">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="3f816-615">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="3f816-616">默认值为 128 KB (131,072)。</span><span class="sxs-lookup"><span data-stu-id="3f816-616">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="3f816-617">初始流窗口大小</span><span class="sxs-lookup"><span data-stu-id="3f816-617">Initial stream window size</span></span>

<span data-ttu-id="3f816-618">`Http2.InitialStreamWindowSize` 表示服务器针对每个请求（流）的一次性缓存的最大请求主体数据大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="3f816-618">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="3f816-619">请求也受 `Http2.InitialStreamWindowSize` 限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-619">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="3f816-620">该值必须大于或等于 65,535，并小于 2^31 (2,147,483,648)。</span><span class="sxs-lookup"><span data-stu-id="3f816-620">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="3f816-621">默认值为 96 KB (98,304)。</span><span class="sxs-lookup"><span data-stu-id="3f816-621">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="3f816-622">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="3f816-622">Synchronous I/O</span></span>

<span data-ttu-id="3f816-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="3f816-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="3f816-624">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="3f816-624">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3f816-625">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-625">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3f816-626">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="3f816-626">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="3f816-627">以下示例会启用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="3f816-627">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="3f816-628">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="3f816-628">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3f816-629">终结点配置</span><span class="sxs-lookup"><span data-stu-id="3f816-629">Endpoint configuration</span></span>

<span data-ttu-id="3f816-630">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="3f816-630">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3f816-631">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="3f816-631">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3f816-632">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="3f816-632">Specify URLs using the:</span></span>

* <span data-ttu-id="3f816-633">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f816-633">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3f816-634">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3f816-634">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3f816-635">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="3f816-635">`urls` host configuration key.</span></span>
* <span data-ttu-id="3f816-636">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="3f816-636">`UseUrls` extension method.</span></span>

<span data-ttu-id="3f816-637">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="3f816-637">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3f816-638">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="3f816-638">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3f816-639">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="3f816-639">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3f816-640">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="3f816-640">A development certificate is created:</span></span>

* <span data-ttu-id="3f816-641">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="3f816-641">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3f816-642">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-642">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3f816-643">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-643">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3f816-644">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3f816-644">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3f816-645">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-645">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3f816-646">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="3f816-646">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3f816-647">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-647">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3f816-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3f816-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3f816-649">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-649">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3f816-650">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-650">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="3f816-651">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-651">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3f816-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3f816-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3f816-653">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-653">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3f816-654">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-654">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="3f816-655">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-655">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="3f816-656">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3f816-656">Configure(IConfiguration)</span></span>

<span data-ttu-id="3f816-657">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-657">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3f816-658">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="3f816-658">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3f816-659">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3f816-659">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3f816-660">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-660">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3f816-661">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="3f816-661">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3f816-662">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-662">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3f816-663">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3f816-663">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="3f816-664">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="3f816-664">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3f816-665">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="3f816-665">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3f816-666">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="3f816-666">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3f816-667">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-667">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3f816-668">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="3f816-668">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3f816-669">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="3f816-669">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3f816-670">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="3f816-670">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3f816-671">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-671">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3f816-672">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="3f816-672">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3f816-673">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-673">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3f816-674">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-674">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3f816-675">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-675">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3f816-676">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-676">Supported configurations described next:</span></span>

* <span data-ttu-id="3f816-677">无配置</span><span class="sxs-lookup"><span data-stu-id="3f816-677">No configuration</span></span>
* <span data-ttu-id="3f816-678">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="3f816-678">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3f816-679">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="3f816-679">Change the defaults in code</span></span>

<span data-ttu-id="3f816-680">*无配置*</span><span class="sxs-lookup"><span data-stu-id="3f816-680">*No configuration*</span></span>

<span data-ttu-id="3f816-681">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="3f816-681">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3f816-682">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="3f816-682">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3f816-683">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-683">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3f816-684">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="3f816-684">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3f816-685">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-685">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3f816-686">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="3f816-686">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3f816-687">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="3f816-687">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3f816-688">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="3f816-688">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="3f816-689">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="3f816-689">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3f816-690">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="3f816-690">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3f816-691">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="3f816-691">Schema notes:</span></span>

* <span data-ttu-id="3f816-692">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3f816-692">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3f816-693">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="3f816-693">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3f816-694">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="3f816-694">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3f816-695">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="3f816-695">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3f816-696">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="3f816-696">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3f816-697">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="3f816-697">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3f816-698">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="3f816-698">The `Certificate` section is optional.</span></span> <span data-ttu-id="3f816-699">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-699">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3f816-700">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="3f816-700">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3f816-701">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="3f816-701">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3f816-702">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-702">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3f816-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="3f816-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="3f816-704">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="3f816-704">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3f816-705">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-705">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3f816-706">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-706">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3f816-707">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="3f816-707">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3f816-708">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="3f816-708">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3f816-709">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-709">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3f816-710">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-710">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3f816-711">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="3f816-711">*Change the defaults in code*</span></span>

<span data-ttu-id="3f816-712">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-712">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3f816-713">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="3f816-713">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="3f816-714">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="3f816-714">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3f816-715">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="3f816-715">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3f816-716">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-716">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3f816-717">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="3f816-717">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3f816-718">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="3f816-718">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3f816-719">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-719">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3f816-720">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="3f816-720">SNI support requires:</span></span>

* <span data-ttu-id="3f816-721">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="3f816-721">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3f816-722">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3f816-722">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3f816-723">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3f816-723">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3f816-724">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="3f816-724">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3f816-725">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-725">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="3f816-726">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="3f816-726">Connection logging</span></span>

<span data-ttu-id="3f816-727">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="3f816-727">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="3f816-728">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="3f816-728">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="3f816-729">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="3f816-729">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="3f816-730">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="3f816-730">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3f816-731">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-731">Bind to a TCP socket</span></span>

<span data-ttu-id="3f816-732"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-732">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="3f816-733">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-733">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3f816-734">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-734">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3f816-735">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-735">Bind to a Unix socket</span></span>

<span data-ttu-id="3f816-736">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3f816-736">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="3f816-737">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="3f816-737">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="3f816-738">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="3f816-738">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="3f816-739">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="3f816-739">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="3f816-740">端口 0</span><span class="sxs-lookup"><span data-stu-id="3f816-740">Port 0</span></span>

<span data-ttu-id="3f816-741">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-741">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3f816-742">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="3f816-742">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3f816-743">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="3f816-743">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3f816-744">限制</span><span class="sxs-lookup"><span data-stu-id="3f816-744">Limitations</span></span>

<span data-ttu-id="3f816-745">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="3f816-745">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3f816-746">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="3f816-746">`--urls` command-line argument</span></span>
* <span data-ttu-id="3f816-747">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="3f816-747">`urls` host configuration key</span></span>
* <span data-ttu-id="3f816-748">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="3f816-748">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3f816-749">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="3f816-749">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3f816-750">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="3f816-750">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3f816-751">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="3f816-751">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3f816-752">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-752">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3f816-753">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="3f816-753">IIS endpoint configuration</span></span>

<span data-ttu-id="3f816-754">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="3f816-754">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3f816-755">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="3f816-755">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="3f816-756">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="3f816-756">ListenOptions.Protocols</span></span>

<span data-ttu-id="3f816-757">`Protocols` 属性建立在连接终结点上或为服务器启用的 HTTP 协议（`HttpProtocols`）。</span><span class="sxs-lookup"><span data-stu-id="3f816-757">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="3f816-758">从 `HttpProtocols` 枚举向 `Protocols` 属性赋值。</span><span class="sxs-lookup"><span data-stu-id="3f816-758">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="3f816-759">`HttpProtocols` 枚举值</span><span class="sxs-lookup"><span data-stu-id="3f816-759">`HttpProtocols` enum value</span></span> | <span data-ttu-id="3f816-760">允许的连接协议</span><span class="sxs-lookup"><span data-stu-id="3f816-760">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="3f816-761">仅 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3f816-761">HTTP/1.1 only.</span></span> <span data-ttu-id="3f816-762">可以在具有 TLS 或没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="3f816-762">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="3f816-763">仅 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-763">HTTP/2 only.</span></span> <span data-ttu-id="3f816-764">仅当客户端支持[先验知识模式](https://tools.ietf.org/html/rfc7540#section-3.4)时，才可以在没有 TLS 的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="3f816-764">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="3f816-765">HTTP/1.1 和 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3f816-765">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="3f816-766">HTTP/2 需要 TLS 和[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接；否则，连接默认为 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3f816-766">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="3f816-767">默认协议是 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="3f816-767">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="3f816-768">HTTP/2 的 TLS 限制：</span><span class="sxs-lookup"><span data-stu-id="3f816-768">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="3f816-769">TLS 版本 1.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3f816-769">TLS version 1.2 or later</span></span>
* <span data-ttu-id="3f816-770">重新协商已禁用</span><span class="sxs-lookup"><span data-stu-id="3f816-770">Renegotiation disabled</span></span>
* <span data-ttu-id="3f816-771">压缩已禁用</span><span class="sxs-lookup"><span data-stu-id="3f816-771">Compression disabled</span></span>
* <span data-ttu-id="3f816-772">最小的临时密钥交换大小：</span><span class="sxs-lookup"><span data-stu-id="3f816-772">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="3f816-773">椭圆曲线 Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;：最小 224 位</span><span class="sxs-lookup"><span data-stu-id="3f816-773">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="3f816-774">有限字段 Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;：最小 2048 位</span><span class="sxs-lookup"><span data-stu-id="3f816-774">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="3f816-775">未阻止密码套件</span><span class="sxs-lookup"><span data-stu-id="3f816-775">Cipher suite not blocked</span></span>

<span data-ttu-id="3f816-776">默认情况下，支持具有 P-256 椭圆曲线 &lbrack;`FIPS186`&rbrack; 的 `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack;。</span><span class="sxs-lookup"><span data-stu-id="3f816-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="3f816-777">以下示例允许端口 8000 上的 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-777">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="3f816-778">TLS 使用提供的证书来保护连接：</span><span class="sxs-lookup"><span data-stu-id="3f816-778">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="3f816-779">（可选）创建 `IConnectionAdapter` 实现，以针对特定密码的每个连接筛选 TLS 握手：</span><span class="sxs-lookup"><span data-stu-id="3f816-779">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="3f816-780">*从配置中设置协议*</span><span class="sxs-lookup"><span data-stu-id="3f816-780">*Set the protocol from configuration*</span></span>

<span data-ttu-id="3f816-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 在默认情况下调用 `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="3f816-782">在以下 appsettings.json 示例中，为 Kestrel 的所有终结点建立默认的连接协议（HTTP/1.1 和 HTTP/2）：</span><span class="sxs-lookup"><span data-stu-id="3f816-782">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="3f816-783">以下配置文件示例为特定终结点建立了连接协议：</span><span class="sxs-lookup"><span data-stu-id="3f816-783">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="3f816-784">代码中指定的协议覆盖了由配置设置的值。</span><span class="sxs-lookup"><span data-stu-id="3f816-784">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="3f816-785">Libuv 传输配置</span><span class="sxs-lookup"><span data-stu-id="3f816-785">Libuv transport configuration</span></span>

<span data-ttu-id="3f816-786">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="3f816-786">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="3f816-787">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="3f816-787">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="3f816-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="3f816-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="3f816-789">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="3f816-789">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="3f816-790">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="3f816-790">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="3f816-791">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="3f816-791">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="3f816-792">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="3f816-792">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="3f816-793">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="3f816-793">URL prefixes</span></span>

<span data-ttu-id="3f816-794">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="3f816-794">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3f816-795">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="3f816-795">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3f816-796">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-796">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3f816-797">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="3f816-797">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3f816-798">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="3f816-798">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3f816-799">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="3f816-799">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3f816-800">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="3f816-800">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3f816-801">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="3f816-801">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3f816-802">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="3f816-802">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3f816-803">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="3f816-803">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3f816-804">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="3f816-804">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3f816-805">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3f816-805">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3f816-806">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="3f816-806">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3f816-807">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="3f816-807">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3f816-808">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3f816-808">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3f816-809">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="3f816-809">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3f816-810">主机筛选</span><span class="sxs-lookup"><span data-stu-id="3f816-810">Host filtering</span></span>

<span data-ttu-id="3f816-811">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="3f816-811">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3f816-812">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="3f816-812">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3f816-813">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="3f816-813">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3f816-814">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="3f816-814">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3f816-815">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="3f816-815">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3f816-816">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="3f816-816">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="3f816-817">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="3f816-817">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3f816-818">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="3f816-818">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3f816-819">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="3f816-819">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3f816-820">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="3f816-820">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3f816-821">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3f816-821">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3f816-822">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="3f816-822">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3f816-823">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="3f816-823">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3f816-824">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3f816-824">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3f816-825">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3f816-825">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3f816-826">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="3f816-826">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="3f816-827">HTTP/1.1 请求排出</span><span class="sxs-lookup"><span data-stu-id="3f816-827">HTTP/1.1 request draining</span></span>

<span data-ttu-id="3f816-828">打开 HTTP 连接非常耗时。</span><span class="sxs-lookup"><span data-stu-id="3f816-828">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="3f816-829">对于 HTTPS 而言，这也是资源密集型。</span><span class="sxs-lookup"><span data-stu-id="3f816-829">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="3f816-830">因此，Kestrel 会尝试按 HTTP/1.1 协议重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-830">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="3f816-831">请求正文必须完全使用才能允许重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-831">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="3f816-832">应用不会始终使用请求正文，例如 `POST` 请求，其中服务器返回重定向或 404 响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-832">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="3f816-833">在 `POST` 重定向的情况下：</span><span class="sxs-lookup"><span data-stu-id="3f816-833">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="3f816-834">客户端可能已发送部分 `POST` 数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-834">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="3f816-835">服务器写入 301 响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-835">The server writes the 301 response.</span></span>
* <span data-ttu-id="3f816-836">在完全读取上一个请求正文中的 `POST` 数据之前，不能将连接用于新请求。</span><span class="sxs-lookup"><span data-stu-id="3f816-836">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="3f816-837">Kestrel 尝试排出请求正文。</span><span class="sxs-lookup"><span data-stu-id="3f816-837">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="3f816-838">排出请求正文意味着读取和丢弃数据，而不处理数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-838">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="3f816-839">排出过程会在允许重新使用连接与排出任何剩余数据所用的时间之间进行权衡：</span><span class="sxs-lookup"><span data-stu-id="3f816-839">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="3f816-840">排出超时为五秒，该值不可配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-840">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="3f816-841">如果 `Content-Length` 或 `Transfer-Encoding` 标头指定的所有数据在超时之前都未被读取，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="3f816-841">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="3f816-842">有时，你可能想要在写入响应之前或之后立即终止请求。</span><span class="sxs-lookup"><span data-stu-id="3f816-842">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="3f816-843">例如，客户端可能具有限制性的数据上限，因此可以优先考虑限制上传的数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-843">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="3f816-844">在这种情况下，若要终止请求，请从控制器、Razor 页面或中间件调用 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)。</span><span class="sxs-lookup"><span data-stu-id="3f816-844">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="3f816-845">在调用 `Abort` 时，存在一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="3f816-845">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="3f816-846">创建新连接可能会很慢且成本高昂。</span><span class="sxs-lookup"><span data-stu-id="3f816-846">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="3f816-847">在连接关闭之前无法保证客户端已读取响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-847">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="3f816-848">应极少调用 `Abort`，并且应将此操作留给严重错误情况（而不是常见错误情况）。</span><span class="sxs-lookup"><span data-stu-id="3f816-848">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="3f816-849">只有在需要解决特定问题时才调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-849">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="3f816-850">例如，如果恶意客户端正在尝试 `POST` 数据或在客户端代码中存在导致大量请求的 bug，则调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-850">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="3f816-851">不要为常见错误情况（如 HTTP 404（找不到））调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-851">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="3f816-852">调用 `Abort` 之前调用 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) 可确保服务器已完成写入响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-852">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="3f816-853">但是，客户端行为是不可预测的，在连接中止前它们可能未读取响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-853">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="3f816-854">对于 HTTP/2，此过程又有所不同，因为协议支持在不关闭连接的情况下中止单个请求流。</span><span class="sxs-lookup"><span data-stu-id="3f816-854">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="3f816-855">五秒排出超时不适用。</span><span class="sxs-lookup"><span data-stu-id="3f816-855">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="3f816-856">如果在完成响应后存在任何未读的请求正文数据，则服务器会发送 HTTP/2 RST 帧。</span><span class="sxs-lookup"><span data-stu-id="3f816-856">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="3f816-857">其他请求正文数据帧将被忽略。</span><span class="sxs-lookup"><span data-stu-id="3f816-857">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="3f816-858">如果可能，客户端最好使用 [Expect:100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 请求标头，然后等到服务器响应后再开始发送请求正文。</span><span class="sxs-lookup"><span data-stu-id="3f816-858">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="3f816-859">这样，客户端便有机会在发送不需要的数据之前检查响应和中止。</span><span class="sxs-lookup"><span data-stu-id="3f816-859">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f816-860">其他资源</span><span class="sxs-lookup"><span data-stu-id="3f816-860">Additional resources</span></span>

* <span data-ttu-id="3f816-861">在 Linux 上使用 UNIX 套接字时，该套接字不会在应用关闭时自动删除。</span><span class="sxs-lookup"><span data-stu-id="3f816-861">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="3f816-862">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="3f816-862">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="3f816-863">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="3f816-863">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3f816-864">Kestrel 是一个跨平台的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="3f816-864">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="3f816-865">Kestrel 是 Web 服务器，默认包括在 ASP.NET Core 项目模板中。</span><span class="sxs-lookup"><span data-stu-id="3f816-865">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="3f816-866">Kestrel 支持以下方案：</span><span class="sxs-lookup"><span data-stu-id="3f816-866">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="3f816-867">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3f816-867">HTTPS</span></span>
* <span data-ttu-id="3f816-868">用于启用 [WebSocket](https://github.com/aspnet/websockets) 的不透明升级</span><span class="sxs-lookup"><span data-stu-id="3f816-868">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="3f816-869">用于获得 Nginx 高性能的 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-869">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="3f816-870">.NET Core 支持的所有平台和版本均支持 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-870">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="3f816-871">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3f816-871">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="3f816-872">何时结合使用 Kestrel 和反向代理</span><span class="sxs-lookup"><span data-stu-id="3f816-872">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="3f816-873">可以单独使用 Kestrel，也可以将其与反向代理服务器（如 [Internet Information Services (IIS)](https://www.iis.net/)、[Nginx](https://nginx.org) 或 [Apache](https://httpd.apache.org/)）结合使用。</span><span class="sxs-lookup"><span data-stu-id="3f816-873">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="3f816-874">反向代理服务器接收来自网络的 HTTP 请求，并将这些请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-874">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="3f816-875">Kestrel 用作边缘（面向 Internet）Web 服务器：</span><span class="sxs-lookup"><span data-stu-id="3f816-875">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel 直接与 Internet 通信，不使用反向代理服务器](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="3f816-877">Kestrel 用于反向代理配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-877">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel 通过反向代理服务器（如 IIS、Nginx 或 Apache）间接与 Internet 进行通信](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="3f816-879">无论配置是否使用反向代理服务器，都是受支持的托管配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-879">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="3f816-880">在没有反向代理服务器的情况下用作边缘服务器的 Kestrel 不支持在多个进程间共享相同的 IP 和端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-880">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="3f816-881">如果将 Kestrel 配置为侦听某个端口，Kestrel 会处理该端口的所有流量（无视请求的 `Host` 标头）。</span><span class="sxs-lookup"><span data-stu-id="3f816-881">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="3f816-882">可以共享端口的反向代理能在唯一的 IP 和端口上将请求转发至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-882">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="3f816-883">即使不需要反向代理服务器，使用反向代理服务器可能也是个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="3f816-883">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="3f816-884">反向代理：</span><span class="sxs-lookup"><span data-stu-id="3f816-884">A reverse proxy:</span></span>

* <span data-ttu-id="3f816-885">可以限制所承载的应用中的公开的公共外围应用。</span><span class="sxs-lookup"><span data-stu-id="3f816-885">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="3f816-886">提供额外的配置和防护层。</span><span class="sxs-lookup"><span data-stu-id="3f816-886">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="3f816-887">可以更好地与现有基础结构集成。</span><span class="sxs-lookup"><span data-stu-id="3f816-887">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="3f816-888">简化了负载均和和安全通信 (HTTPS) 配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-888">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="3f816-889">仅反向代理服务器需要 X.509 证书，并且该服务器可使用普通 HTTP 在内部网络上与应用服务器通信。</span><span class="sxs-lookup"><span data-stu-id="3f816-889">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="3f816-890">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3f816-890">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="3f816-891">如何在 ASP.NET Core 应用中使用 Kestrel</span><span class="sxs-lookup"><span data-stu-id="3f816-891">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="3f816-892">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中包括 [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) 包。</span><span class="sxs-lookup"><span data-stu-id="3f816-892">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3f816-893">默认情况下，ASP.NET Core 项目模板使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-893">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="3f816-894">在 Program.cs 中，模板代码调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>，后者在后台调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>。</span><span class="sxs-lookup"><span data-stu-id="3f816-894">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="3f816-895">若要在调用 `CreateDefaultBuilder` 后提供其他配置，请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>：</span><span class="sxs-lookup"><span data-stu-id="3f816-895">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="3f816-896">有关 `CreateDefaultBuilder` 和生成主机的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host> 的“设置主机”部分。</span><span class="sxs-lookup"><span data-stu-id="3f816-896">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="3f816-897">Kestrel 选项</span><span class="sxs-lookup"><span data-stu-id="3f816-897">Kestrel options</span></span>

<span data-ttu-id="3f816-898">Kestrel Web 服务器具有约束配置选项，这些选项在面向 Internet 的部署中尤其有用。</span><span class="sxs-lookup"><span data-stu-id="3f816-898">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="3f816-899">对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 类的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> 属性设置约束。</span><span class="sxs-lookup"><span data-stu-id="3f816-899">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="3f816-900">`Limits` 属性包含 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> 类的实例。</span><span class="sxs-lookup"><span data-stu-id="3f816-900">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="3f816-901">下面的示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core> 命名空间。</span><span class="sxs-lookup"><span data-stu-id="3f816-901">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="3f816-902">Kestrel 选项（已在以下示例的 C# 代码中配置）也可以使用[配置提供程序](xref:fundamentals/configuration/index)进行设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-902">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="3f816-903">例如，文件配置提供程序可以从 appsettings.json 或 appsettings.{Environment}.json 文件加载 Kestrel 配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-903">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="3f816-904">使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="3f816-904">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="3f816-905">在 `Startup.ConfigureServices` 中配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3f816-905">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="3f816-906">将 `IConfiguration` 的实例注入到 `Startup` 类中。</span><span class="sxs-lookup"><span data-stu-id="3f816-906">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="3f816-907">下面的示例假定注入的配置已分配给 `Configuration` 属性。</span><span class="sxs-lookup"><span data-stu-id="3f816-907">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="3f816-908">在 `Startup.ConfigureServices` 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3f816-908">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="3f816-909">构建主机时配置 Kestrel：</span><span class="sxs-lookup"><span data-stu-id="3f816-909">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="3f816-910">在 Program.cs 中，将配置的 `Kestrel` 部分加载到 Kestrel 的配置中：</span><span class="sxs-lookup"><span data-stu-id="3f816-910">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="3f816-911">上述两种方法适用于任何[配置提供程序](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="3f816-911">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="3f816-912">保持活动状态超时</span><span class="sxs-lookup"><span data-stu-id="3f816-912">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="3f816-913">获取或设置[保持活动状态超时](https://tools.ietf.org/html/rfc7230#section-6.5)。</span><span class="sxs-lookup"><span data-stu-id="3f816-913">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="3f816-914">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="3f816-914">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="3f816-915">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="3f816-915">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="3f816-916">可使用以下代码为整个应用设置并发打开的最大 TCP 连接数：</span><span class="sxs-lookup"><span data-stu-id="3f816-916">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="3f816-917">对于已从 HTTP 或 HTTPS 升级到另一个协议（例如，Websocket 请求）的连接，有一个单独的限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-917">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="3f816-918">连接升级后，不会计入 `MaxConcurrentConnections` 限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-918">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="3f816-919">默认情况下，最大连接数不受限制 (NULL)。</span><span class="sxs-lookup"><span data-stu-id="3f816-919">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="3f816-920">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="3f816-920">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="3f816-921">默认的请求正文最大大小为 30,000,000 字节，大约 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="3f816-921">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="3f816-922">在 ASP.NET Core MVC 应用中替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="3f816-922">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="3f816-923">以下示例演示如何为每个请求上的应用配置约束：</span><span class="sxs-lookup"><span data-stu-id="3f816-923">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="3f816-924">在中间件中替代特定请求的设置：</span><span class="sxs-lookup"><span data-stu-id="3f816-924">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="3f816-925">如果应用在开始读取请求后配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3f816-925">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="3f816-926">`IsReadOnly` 属性指示 `MaxRequestBodySize` 属性处于只读状态，意味已经无法再配置限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-926">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="3f816-927">当应用在 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)后于[进程外](xref:host-and-deploy/iis/index#out-of-process-hosting-model)运行时，由于 IIS 已设置限制，因此禁用了 Kestrel 的请求正文大小限制。</span><span class="sxs-lookup"><span data-stu-id="3f816-927">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="3f816-928">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="3f816-928">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="3f816-929">Kestrel 每秒检查一次数据是否以指定的速率（字节/秒）传入。</span><span class="sxs-lookup"><span data-stu-id="3f816-929">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="3f816-930">如果速率低于最小值，则连接超时。宽限期是 Kestrel 提供给客户端用于将其发送速率提升到最小值的时间量；在此期间不会检查速率。</span><span class="sxs-lookup"><span data-stu-id="3f816-930">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="3f816-931">宽限期有助于避免最初由于 TCP 慢启动而以较慢速率发送数据的连接中断。</span><span class="sxs-lookup"><span data-stu-id="3f816-931">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="3f816-932">默认的最小速率为 240 字节/秒，包含 5 秒的宽限期。</span><span class="sxs-lookup"><span data-stu-id="3f816-932">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="3f816-933">最小速率也适用于响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-933">A minimum rate also applies to the response.</span></span> <span data-ttu-id="3f816-934">除了属性和接口名称中具有 `RequestBody` 或 `Response` 以外，用于设置请求限制和响应限制的代码相同。</span><span class="sxs-lookup"><span data-stu-id="3f816-934">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="3f816-935">以下示例演示如何在 Program.cs 中配置最小数据速率：</span><span class="sxs-lookup"><span data-stu-id="3f816-935">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="3f816-936">请求标头超时</span><span class="sxs-lookup"><span data-stu-id="3f816-936">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="3f816-937">获取或设置服务器接收请求标头所花费的最大时间量。</span><span class="sxs-lookup"><span data-stu-id="3f816-937">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="3f816-938">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="3f816-938">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="3f816-939">同步 I/O</span><span class="sxs-lookup"><span data-stu-id="3f816-939">Synchronous I/O</span></span>

<span data-ttu-id="3f816-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> 控制是否允许对请求和响应使用同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="3f816-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="3f816-941">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="3f816-941">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3f816-942">大量阻止同步 I/O 的操作可能会导致线程池资源不足，进而导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-942">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="3f816-943">仅在使用不支持异步 I/O 的库时，才启用 `AllowSynchronousIO`。</span><span class="sxs-lookup"><span data-stu-id="3f816-943">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="3f816-944">以下示例会禁用同步 I/O：</span><span class="sxs-lookup"><span data-stu-id="3f816-944">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="3f816-945">有关其他 Kestrel 选项和限制的信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="3f816-945">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="3f816-946">终结点配置</span><span class="sxs-lookup"><span data-stu-id="3f816-946">Endpoint configuration</span></span>

<span data-ttu-id="3f816-947">默认情况下，ASP.NET Core 绑定到：</span><span class="sxs-lookup"><span data-stu-id="3f816-947">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="3f816-948">`https://localhost:5001`（存在本地开发证书时）</span><span class="sxs-lookup"><span data-stu-id="3f816-948">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="3f816-949">使用以下内容指定 URL：</span><span class="sxs-lookup"><span data-stu-id="3f816-949">Specify URLs using the:</span></span>

* <span data-ttu-id="3f816-950">`ASPNETCORE_URLS` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="3f816-950">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="3f816-951">`--urls` 命令行参数。</span><span class="sxs-lookup"><span data-stu-id="3f816-951">`--urls` command-line argument.</span></span>
* <span data-ttu-id="3f816-952">`urls` 主机配置键。</span><span class="sxs-lookup"><span data-stu-id="3f816-952">`urls` host configuration key.</span></span>
* <span data-ttu-id="3f816-953">`UseUrls` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="3f816-953">`UseUrls` extension method.</span></span>

<span data-ttu-id="3f816-954">采用这些方法提供的值可以是一个或多个 HTTP 和 HTTPS 终结点（如果默认证书可用，则为 HTTPS）。</span><span class="sxs-lookup"><span data-stu-id="3f816-954">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="3f816-955">将值配置为以分号分隔的列表（例如 `"Urls": "http://localhost:8000;http://localhost:8001"`）。</span><span class="sxs-lookup"><span data-stu-id="3f816-955">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="3f816-956">有关这些方法的详细信息，请参阅[服务器 URL](xref:fundamentals/host/web-host#server-urls) 和[重写配置](xref:fundamentals/host/web-host#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="3f816-956">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="3f816-957">关于开发证书的创建：</span><span class="sxs-lookup"><span data-stu-id="3f816-957">A development certificate is created:</span></span>

* <span data-ttu-id="3f816-958">安装了 [.NET Core SDK](/dotnet/core/sdk) 时。</span><span class="sxs-lookup"><span data-stu-id="3f816-958">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="3f816-959">[dev-certs tool](xref:aspnetcore-2.1#https) 用于创建证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-959">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="3f816-960">某些浏览器需要授予显式权限才能信任本地开发证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-960">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="3f816-961">项目模板将应用配置为默认情况下在 HTTPS 上运行，并包括 [HTTPS 重定向和 HSTS 支持](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="3f816-961">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="3f816-962">调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> 上的 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 或 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 方法以配置 URL 前缀和 Kestrel 的端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-962">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="3f816-963">`UseUrls`、`--urls` 命令行参数、`urls` 主机配置键以及 `ASPNETCORE_URLS` 环境变量也有用，但具有本节后面注明的限制（必须要有可用于 HTTPS 终结点配置的默认证书）。</span><span class="sxs-lookup"><span data-stu-id="3f816-963">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="3f816-964">`KestrelServerOptions` 配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-964">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="3f816-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="3f816-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="3f816-966">指定一个为每个指定的终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-966">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="3f816-967">多次调用 `ConfigureEndpointDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-967">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="3f816-968">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-968">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="3f816-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="3f816-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="3f816-970">指定一个为每个 HTTPS 终结点运行的配置 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-970">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="3f816-971">多次调用 `ConfigureHttpsDefaults`，用最新指定的 `Action` 替换之前的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-971">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="3f816-972">通过在调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> 之前调用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 创建的终结点将不会应用默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-972">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="3f816-973">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="3f816-973">Configure(IConfiguration)</span></span>

<span data-ttu-id="3f816-974">创建配置加载程序，用于设置将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 作为输入的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="3f816-974">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="3f816-975">配置必须针对 Kestrel 的配置节。</span><span class="sxs-lookup"><span data-stu-id="3f816-975">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="3f816-976">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="3f816-976">ListenOptions.UseHttps</span></span>

<span data-ttu-id="3f816-977">将 Kestrel 配置为使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-977">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="3f816-978">`ListenOptions.UseHttps` 扩展：</span><span class="sxs-lookup"><span data-stu-id="3f816-978">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="3f816-979">`UseHttps`：将 Kestrel 配置为使用 HTTPS，采用默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-979">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="3f816-980">如果没有配置默认证书，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="3f816-980">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="3f816-981">`ListenOptions.UseHttps` 参数：</span><span class="sxs-lookup"><span data-stu-id="3f816-981">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="3f816-982">`filename` 是证书文件的路径和文件名，关联包含应用内容文件的目录。</span><span class="sxs-lookup"><span data-stu-id="3f816-982">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="3f816-983">`password` 是访问 X.509 证书数据所需的密码。</span><span class="sxs-lookup"><span data-stu-id="3f816-983">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="3f816-984">`configureOptions` 是配置 `HttpsConnectionAdapterOptions` 的 `Action`。</span><span class="sxs-lookup"><span data-stu-id="3f816-984">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="3f816-985">返回 `ListenOptions`。</span><span class="sxs-lookup"><span data-stu-id="3f816-985">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="3f816-986">`storeName` 是从中加载证书的证书存储。</span><span class="sxs-lookup"><span data-stu-id="3f816-986">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="3f816-987">`subject` 是证书的主题名称。</span><span class="sxs-lookup"><span data-stu-id="3f816-987">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="3f816-988">`allowInvalid` 指示是否存在需要留意的无效证书，例如自签名证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-988">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="3f816-989">`location` 是从中加载证书的存储位置。</span><span class="sxs-lookup"><span data-stu-id="3f816-989">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="3f816-990">`serverCertificate` 是 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-990">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="3f816-991">在生产中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-991">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3f816-992">至少必须提供默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-992">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="3f816-993">下面要描述的支持的配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-993">Supported configurations described next:</span></span>

* <span data-ttu-id="3f816-994">无配置</span><span class="sxs-lookup"><span data-stu-id="3f816-994">No configuration</span></span>
* <span data-ttu-id="3f816-995">从配置中替换默认证书</span><span class="sxs-lookup"><span data-stu-id="3f816-995">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="3f816-996">更改代码中的默认值</span><span class="sxs-lookup"><span data-stu-id="3f816-996">Change the defaults in code</span></span>

<span data-ttu-id="3f816-997">*无配置*</span><span class="sxs-lookup"><span data-stu-id="3f816-997">*No configuration*</span></span>

<span data-ttu-id="3f816-998">Kestrel 在 `http://localhost:5000` 和 `https://localhost:5001` 上进行侦听（如果默认证书可用）。</span><span class="sxs-lookup"><span data-stu-id="3f816-998">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="3f816-999">*从配置中替换默认证书*</span><span class="sxs-lookup"><span data-stu-id="3f816-999">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="3f816-1000">`CreateDefaultBuilder` 在默认情况下调用 `Configure(context.Configuration.GetSection("Kestrel"))` 来加载 Kestrel 配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-1000">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="3f816-1001">Kestrel 可以使用默认 HTTPS 应用设置配置架构。</span><span class="sxs-lookup"><span data-stu-id="3f816-1001">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="3f816-1002">从磁盘上的文件或从证书存储中配置多个终结点，包括要使用的 URL 和证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-1002">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="3f816-1003">在以下 appsettings.json 示例中：</span><span class="sxs-lookup"><span data-stu-id="3f816-1003">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="3f816-1004">将 AllowInvalid 设置为 `true`，从而允许使用无效证书（例如自签名证书）。</span><span class="sxs-lookup"><span data-stu-id="3f816-1004">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="3f816-1005">任何未指定证书的 HTTPS 终结点（下例中的 HttpsDefaultCert）会回退至在 Certificates > Default 下定义的证书或开发证书  。</span><span class="sxs-lookup"><span data-stu-id="3f816-1005">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="3f816-1006">此外还可以使用任何证书节点的 Path 和 Password，采用证书存储字段指定证书 。</span><span class="sxs-lookup"><span data-stu-id="3f816-1006">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="3f816-1007">例如，可将 Certificates > Default 证书指定为 ：</span><span class="sxs-lookup"><span data-stu-id="3f816-1007">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="3f816-1008">架构的注意事项：</span><span class="sxs-lookup"><span data-stu-id="3f816-1008">Schema notes:</span></span>

* <span data-ttu-id="3f816-1009">终结点的名称不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="3f816-1009">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="3f816-1010">例如，`HTTPS` 和 `Https` 都是有效的。</span><span class="sxs-lookup"><span data-stu-id="3f816-1010">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="3f816-1011">每个终结点都要具备 `Url` 参数。</span><span class="sxs-lookup"><span data-stu-id="3f816-1011">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="3f816-1012">此参数的格式和顶层 `Urls` 配置参数一样，只不过它只能有单个值。</span><span class="sxs-lookup"><span data-stu-id="3f816-1012">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="3f816-1013">这些终结点不会添加进顶层 `Urls` 配置中定义的终结点，而是替换它们。</span><span class="sxs-lookup"><span data-stu-id="3f816-1013">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="3f816-1014">通过 `Listen` 在代码中定义的终结点与在配置节中定义的终结点相累积。</span><span class="sxs-lookup"><span data-stu-id="3f816-1014">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="3f816-1015">`Certificate` 部分是可选的。</span><span class="sxs-lookup"><span data-stu-id="3f816-1015">The `Certificate` section is optional.</span></span> <span data-ttu-id="3f816-1016">如果为指定 `Certificate` 部分，则使用在之前的方案中定义的默认值。</span><span class="sxs-lookup"><span data-stu-id="3f816-1016">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="3f816-1017">如果没有可用的默认值，服务器会引发异常且无法启动。</span><span class="sxs-lookup"><span data-stu-id="3f816-1017">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="3f816-1018">`Certificate` 支持 Path&ndash;Password 和 Subject&ndash;Store 证书   。</span><span class="sxs-lookup"><span data-stu-id="3f816-1018">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="3f816-1019">只要不会导致端口冲突，就能以这种方式定义任何数量的终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-1019">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="3f816-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` 通过 `.Endpoint(string name, listenOptions => { })` 方法返回 `KestrelConfigurationLoader`，可以用于补充已配置的终结点设置：</span><span class="sxs-lookup"><span data-stu-id="3f816-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="3f816-1021">可以直接访问 `KestrelServerOptions.ConfigurationLoader` 以继续迭代现有加载程序，例如由 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 提供的加载程序。</span><span class="sxs-lookup"><span data-stu-id="3f816-1021">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="3f816-1022">每个终结点的配置节都可用于 `Endpoint` 方法中的选项，以便读取自定义设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-1022">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="3f816-1023">通过另一节再次调用 `options.Configure(context.Configuration.GetSection("{SECTION}"))` 可能加载多个配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-1023">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="3f816-1024">只使用最新配置，除非之前的实例上显式调用了 `Load`。</span><span class="sxs-lookup"><span data-stu-id="3f816-1024">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="3f816-1025">元包不会调用 `Load`，所以可能会替换它的默认配置节。</span><span class="sxs-lookup"><span data-stu-id="3f816-1025">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="3f816-1026">`KestrelConfigurationLoader` 从 `KestrelServerOptions` 将 API 的 `Listen` 簇反射为 `Endpoint` 重载，因此可在同样的位置配置代码和配置终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-1026">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="3f816-1027">这些重载不使用名称，且只使用配置中的默认设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-1027">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="3f816-1028">*更改代码中的默认值*</span><span class="sxs-lookup"><span data-stu-id="3f816-1028">*Change the defaults in code*</span></span>

<span data-ttu-id="3f816-1029">可以使用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults` 更改 `ListenOptions` 和 `HttpsConnectionAdapterOptions` 的默认设置，包括重写之前的方案指定的默认证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-1029">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="3f816-1030">需要在配置任何终结点之前调用 `ConfigureEndpointDefaults` 和 `ConfigureHttpsDefaults`。</span><span class="sxs-lookup"><span data-stu-id="3f816-1030">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="3f816-1031">*SNI 的 Kestrel 支持*</span><span class="sxs-lookup"><span data-stu-id="3f816-1031">*Kestrel support for SNI*</span></span>

<span data-ttu-id="3f816-1032">[服务器名称指示 (SNI)](https://tools.ietf.org/html/rfc6066#section-3) 可用于承载相同 IP 地址和端口上的多个域。</span><span class="sxs-lookup"><span data-stu-id="3f816-1032">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="3f816-1033">为了运行 SNI，客户端在 TLS 握手过程中将进行安全会话的主机名发送至服务器，从而让服务器可以提供正确的证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-1033">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="3f816-1034">在 TLS 握手后的安全会话期间，客户端将服务器提供的证书用于与服务器进行加密通信。</span><span class="sxs-lookup"><span data-stu-id="3f816-1034">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="3f816-1035">Kestrel 通过 `ServerCertificateSelector` 回调支持 SNI。</span><span class="sxs-lookup"><span data-stu-id="3f816-1035">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="3f816-1036">每次连接调用一次回调，从而允许应用检查主机名并选择合适的证书。</span><span class="sxs-lookup"><span data-stu-id="3f816-1036">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="3f816-1037">SNI 支持要求：</span><span class="sxs-lookup"><span data-stu-id="3f816-1037">SNI support requires:</span></span>

* <span data-ttu-id="3f816-1038">在目标框架 `netcoreapp2.1` 或更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="3f816-1038">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="3f816-1039">在 `net461` 或最高版本上，将调用回调，但是 `name` 始终为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3f816-1039">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="3f816-1040">如果客户端未在 TLS 握手过程中提供主机名参数，则 `name` 也为 `null`。</span><span class="sxs-lookup"><span data-stu-id="3f816-1040">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="3f816-1041">所有网站在相同的 Kestrel 实例上运行。</span><span class="sxs-lookup"><span data-stu-id="3f816-1041">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="3f816-1042">Kestrel 在无反向代理时不支持跨多个实例共享一个 IP 地址和端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-1042">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="3f816-1043">连接日志记录</span><span class="sxs-lookup"><span data-stu-id="3f816-1043">Connection logging</span></span>

<span data-ttu-id="3f816-1044">调用 <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> 以发出用于进行连接上的字节级别通信的调试级别日志。</span><span class="sxs-lookup"><span data-stu-id="3f816-1044">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="3f816-1045">连接日志记录有助于排查低级通信中的问题，例如在 TLS 加密期间和代理后。</span><span class="sxs-lookup"><span data-stu-id="3f816-1045">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="3f816-1046">如果 `UseConnectionLogging` 放置在 `UseHttps` 之前，则会记录加密的流量。</span><span class="sxs-lookup"><span data-stu-id="3f816-1046">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="3f816-1047">如果 `UseConnectionLogging` 放置于 `UseHttps` 之后，则会记录解密的流量。</span><span class="sxs-lookup"><span data-stu-id="3f816-1047">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="3f816-1048">绑定到 TCP 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-1048">Bind to a TCP socket</span></span>

<span data-ttu-id="3f816-1049"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> 方法绑定至 TCP 套接字，且 options lambda 允许 X.509 证书配置：</span><span class="sxs-lookup"><span data-stu-id="3f816-1049">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="3f816-1050">示例使用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> 为终结点配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-1050">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="3f816-1051">可使用相同 API 为特定终结点配置其他 Kestrel 设置。</span><span class="sxs-lookup"><span data-stu-id="3f816-1051">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="3f816-1052">绑定到 Unix 套接字</span><span class="sxs-lookup"><span data-stu-id="3f816-1052">Bind to a Unix socket</span></span>

<span data-ttu-id="3f816-1053">可通过 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 侦听 Unix 套接字以提高 Nginx 的性能，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="3f816-1053">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="3f816-1054">在 Nginx confiuguration 文件中，将 `server` > `location` > `proxy_pass` 条目设置为 `http://unix:/tmp/{KESTREL SOCKET}:/;`。</span><span class="sxs-lookup"><span data-stu-id="3f816-1054">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="3f816-1055">`{KESTREL SOCKET}` 是提供给 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> 的套接字的名称（例如，上述示例中的 `kestrel-test.sock`）。</span><span class="sxs-lookup"><span data-stu-id="3f816-1055">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="3f816-1056">确保套接字可由 Nginx （例如 `chmod go+w /tmp/kestrel-test.sock`）进行写入。</span><span class="sxs-lookup"><span data-stu-id="3f816-1056">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="3f816-1057">端口 0</span><span class="sxs-lookup"><span data-stu-id="3f816-1057">Port 0</span></span>

<span data-ttu-id="3f816-1058">如果指定端口号 `0`，Kestrel 将动态绑定到可用端口。</span><span class="sxs-lookup"><span data-stu-id="3f816-1058">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="3f816-1059">以下示例演示如何确定 Kestrel 在运行时实际绑定到的端口：</span><span class="sxs-lookup"><span data-stu-id="3f816-1059">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="3f816-1060">在应用运行时，控制台窗口输出指示可用于访问应用的动态端口：</span><span class="sxs-lookup"><span data-stu-id="3f816-1060">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="3f816-1061">限制</span><span class="sxs-lookup"><span data-stu-id="3f816-1061">Limitations</span></span>

<span data-ttu-id="3f816-1062">使用以下方法配置终结点：</span><span class="sxs-lookup"><span data-stu-id="3f816-1062">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="3f816-1063">`--urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="3f816-1063">`--urls` command-line argument</span></span>
* <span data-ttu-id="3f816-1064">`urls` 主机配置键</span><span class="sxs-lookup"><span data-stu-id="3f816-1064">`urls` host configuration key</span></span>
* <span data-ttu-id="3f816-1065">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="3f816-1065">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="3f816-1066">若要将代码用于 Kestrel 以外的服务器，这些方法非常有用。</span><span class="sxs-lookup"><span data-stu-id="3f816-1066">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="3f816-1067">不过，请注意以下限制：</span><span class="sxs-lookup"><span data-stu-id="3f816-1067">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="3f816-1068">HTTPS 无法与这些方法结合使用，除非在 HTTPS 终结点配置中提供了默认证书（例如，使用 `KestrelServerOptions` 配置或配置文件，如本主题前面的部分所示）。</span><span class="sxs-lookup"><span data-stu-id="3f816-1068">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="3f816-1069">如果同时使用 `Listen` 和 `UseUrls` 方法，`Listen` 终结点将覆盖 `UseUrls` 终结点。</span><span class="sxs-lookup"><span data-stu-id="3f816-1069">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="3f816-1070">IIS 终结点配置</span><span class="sxs-lookup"><span data-stu-id="3f816-1070">IIS endpoint configuration</span></span>

<span data-ttu-id="3f816-1071">使用 IIS 时，由 `Listen` 或 `UseUrls` 设置用于 IIS 覆盖绑定的 URL 绑定。</span><span class="sxs-lookup"><span data-stu-id="3f816-1071">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="3f816-1072">有关详细信息，请参阅 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)主题。</span><span class="sxs-lookup"><span data-stu-id="3f816-1072">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="3f816-1073">Libuv 传输配置</span><span class="sxs-lookup"><span data-stu-id="3f816-1073">Libuv transport configuration</span></span>

<span data-ttu-id="3f816-1074">对于 ASP.NET Core 2.1 版，Kestrel 默认传输不再基于 Libuv，而是基于托管的套接字。</span><span class="sxs-lookup"><span data-stu-id="3f816-1074">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="3f816-1075">这是 ASP.NET Core 2.0 应用升级到 2.1 时的一个重大更改，它调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> 并依赖于以下包中的一个：</span><span class="sxs-lookup"><span data-stu-id="3f816-1075">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="3f816-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/)（直接包引用）</span><span class="sxs-lookup"><span data-stu-id="3f816-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="3f816-1077">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="3f816-1077">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="3f816-1078">对于需要使用 Libuv 的项目：</span><span class="sxs-lookup"><span data-stu-id="3f816-1078">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="3f816-1079">将用于 [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) 包的依赖项添加到应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="3f816-1079">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="3f816-1080">调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>：</span><span class="sxs-lookup"><span data-stu-id="3f816-1080">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="3f816-1081">URL 前缀</span><span class="sxs-lookup"><span data-stu-id="3f816-1081">URL prefixes</span></span>

<span data-ttu-id="3f816-1082">如果使用 `UseUrls`、`--urls` 命令行参数、`urls` 主机配置键或 `ASPNETCORE_URLS` 环境变量，URL 前缀可采用以下任意格式。</span><span class="sxs-lookup"><span data-stu-id="3f816-1082">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="3f816-1083">仅 HTTP URL 前缀是有效的。</span><span class="sxs-lookup"><span data-stu-id="3f816-1083">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="3f816-1084">使用 `UseUrls` 配置 URL 绑定时，Kestrel 不支持 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3f816-1084">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="3f816-1085">包含端口号的 IPv4 地址</span><span class="sxs-lookup"><span data-stu-id="3f816-1085">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="3f816-1086">`0.0.0.0` 是一种绑定到所有 IPv4 地址的特殊情况。</span><span class="sxs-lookup"><span data-stu-id="3f816-1086">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="3f816-1087">包含端口号的 IPv6 地址</span><span class="sxs-lookup"><span data-stu-id="3f816-1087">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="3f816-1088">`[::]` 是 IPv4 `0.0.0.0` 的 IPv6 等效项。</span><span class="sxs-lookup"><span data-stu-id="3f816-1088">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="3f816-1089">包含端口号的主机名</span><span class="sxs-lookup"><span data-stu-id="3f816-1089">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="3f816-1090">主机名、`*`和 `+` 并不特殊。</span><span class="sxs-lookup"><span data-stu-id="3f816-1090">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="3f816-1091">没有识别为有效 IP 地址或 `localhost` 的任何内容都将绑定到所有 IPv4 和 IPv6 IP。</span><span class="sxs-lookup"><span data-stu-id="3f816-1091">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="3f816-1092">若要将不同主机名绑定到相同端口上的不同 ASP.NET Core 应用，请使用 [HTTP.sys](xref:fundamentals/servers/httpsys) 或 IIS、Nginx 或 Apache 等反向代理服务器。</span><span class="sxs-lookup"><span data-stu-id="3f816-1092">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="3f816-1093">采用反向代理配置进行托管需要[主机筛选](#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="3f816-1093">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="3f816-1094">包含端口号的主机 `localhost` 名称或包含端口号的环回 IP</span><span class="sxs-lookup"><span data-stu-id="3f816-1094">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="3f816-1095">指定 `localhost` 后，Kestrel 将尝试绑定到 IPv4 和 IPv6 环回接口。</span><span class="sxs-lookup"><span data-stu-id="3f816-1095">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="3f816-1096">如果其他服务正在任一环回接口上使用请求的端口，则 Kestrel 将无法启动。</span><span class="sxs-lookup"><span data-stu-id="3f816-1096">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="3f816-1097">如果任一环回接口出于任何其他原因（通常是因为 IPv6 不受支持）而不可用，则 Kestrel 将记录一个警告。</span><span class="sxs-lookup"><span data-stu-id="3f816-1097">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="3f816-1098">主机筛选</span><span class="sxs-lookup"><span data-stu-id="3f816-1098">Host filtering</span></span>

<span data-ttu-id="3f816-1099">尽管 Kestrel 支持基于前缀的配置（例如 `http://example.com:5000`），但 Kestrel 在很大程度上会忽略主机名。</span><span class="sxs-lookup"><span data-stu-id="3f816-1099">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="3f816-1100">主机 `localhost` 是一个特殊情况，用于绑定至环回地址。</span><span class="sxs-lookup"><span data-stu-id="3f816-1100">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="3f816-1101">除了显式 IP 地址以外的所有主机都绑定至所有公共 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="3f816-1101">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="3f816-1102">不验证 `Host` 标头。</span><span class="sxs-lookup"><span data-stu-id="3f816-1102">`Host` headers aren't validated.</span></span>

<span data-ttu-id="3f816-1103">解决方法是，使用主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="3f816-1103">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="3f816-1104">主机筛选中间件由 [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) 包提供，此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中（ASP.NET Core 2.1 或 2.2）。</span><span class="sxs-lookup"><span data-stu-id="3f816-1104">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="3f816-1105">由调用 <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> 的 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 添加中间件：</span><span class="sxs-lookup"><span data-stu-id="3f816-1105">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="3f816-1106">默认情况下，主机筛选中间件处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="3f816-1106">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="3f816-1107">要启用该中间件，请在 appsettings.json/appsettings.\<EnvironmentName>.json 中定义一个 `AllowedHosts` 键。</span><span class="sxs-lookup"><span data-stu-id="3f816-1107">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="3f816-1108">此值是以分号分隔的不带端口号的主机名列表：</span><span class="sxs-lookup"><span data-stu-id="3f816-1108">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="3f816-1109">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="3f816-1109">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="3f816-1110">[转接头中间件](xref:host-and-deploy/proxy-load-balancer) 同样包含 <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> 选项。</span><span class="sxs-lookup"><span data-stu-id="3f816-1110">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="3f816-1111">转接头中间件和主机筛选中间件具有适合不同方案的相似功能。</span><span class="sxs-lookup"><span data-stu-id="3f816-1111">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="3f816-1112">如果未保留 `Host` 标头，并且使用反向代理服务器或负载均衡器转接请求，则使用转接头中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3f816-1112">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="3f816-1113">将 Kestrel 用作面向公众的边缘服务器或直接转接 `Host` 标头时，使用主机筛选中间件设置 `AllowedHosts` 比较合适。</span><span class="sxs-lookup"><span data-stu-id="3f816-1113">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="3f816-1114">有关转接头中间件的详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="3f816-1114">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="3f816-1115">HTTP/1.1 请求排出</span><span class="sxs-lookup"><span data-stu-id="3f816-1115">HTTP/1.1 request draining</span></span>

<span data-ttu-id="3f816-1116">打开 HTTP 连接非常耗时。</span><span class="sxs-lookup"><span data-stu-id="3f816-1116">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="3f816-1117">对于 HTTPS 而言，这也是资源密集型。</span><span class="sxs-lookup"><span data-stu-id="3f816-1117">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="3f816-1118">因此，Kestrel 会尝试按 HTTP/1.1 协议重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-1118">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="3f816-1119">请求正文必须完全使用才能允许重新使用连接。</span><span class="sxs-lookup"><span data-stu-id="3f816-1119">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="3f816-1120">应用不会始终使用请求正文，例如 `POST` 请求，其中服务器返回重定向或 404 响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-1120">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="3f816-1121">在 `POST` 重定向的情况下：</span><span class="sxs-lookup"><span data-stu-id="3f816-1121">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="3f816-1122">客户端可能已发送部分 `POST` 数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-1122">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="3f816-1123">服务器写入 301 响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-1123">The server writes the 301 response.</span></span>
* <span data-ttu-id="3f816-1124">在完全读取上一个请求正文中的 `POST` 数据之前，不能将连接用于新请求。</span><span class="sxs-lookup"><span data-stu-id="3f816-1124">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="3f816-1125">Kestrel 尝试排出请求正文。</span><span class="sxs-lookup"><span data-stu-id="3f816-1125">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="3f816-1126">排出请求正文意味着读取和丢弃数据，而不处理数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-1126">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="3f816-1127">排出过程会在允许重新使用连接与排出任何剩余数据所用的时间之间进行权衡：</span><span class="sxs-lookup"><span data-stu-id="3f816-1127">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="3f816-1128">排出超时为五秒，该值不可配置。</span><span class="sxs-lookup"><span data-stu-id="3f816-1128">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="3f816-1129">如果 `Content-Length` 或 `Transfer-Encoding` 标头指定的所有数据在超时之前都未被读取，则连接将关闭。</span><span class="sxs-lookup"><span data-stu-id="3f816-1129">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="3f816-1130">有时，你可能想要在写入响应之前或之后立即终止请求。</span><span class="sxs-lookup"><span data-stu-id="3f816-1130">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="3f816-1131">例如，客户端可能具有限制性的数据上限，因此可以优先考虑限制上传的数据。</span><span class="sxs-lookup"><span data-stu-id="3f816-1131">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="3f816-1132">在这种情况下，若要终止请求，请从控制器、Razor 页面或中间件调用 [HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A)。</span><span class="sxs-lookup"><span data-stu-id="3f816-1132">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="3f816-1133">在调用 `Abort` 时，存在一些注意事项：</span><span class="sxs-lookup"><span data-stu-id="3f816-1133">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="3f816-1134">创建新连接可能会很慢且成本高昂。</span><span class="sxs-lookup"><span data-stu-id="3f816-1134">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="3f816-1135">在连接关闭之前无法保证客户端已读取响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-1135">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="3f816-1136">应极少调用 `Abort`，并且应将此操作留给严重错误情况（而不是常见错误情况）。</span><span class="sxs-lookup"><span data-stu-id="3f816-1136">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="3f816-1137">只有在需要解决特定问题时才调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-1137">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="3f816-1138">例如，如果恶意客户端正在尝试 `POST` 数据或在客户端代码中存在导致大量请求的 bug，则调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-1138">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="3f816-1139">不要为常见错误情况（如 HTTP 404（找不到））调用 `Abort`。</span><span class="sxs-lookup"><span data-stu-id="3f816-1139">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="3f816-1140">调用 `Abort` 之前调用 [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) 可确保服务器已完成写入响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-1140">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="3f816-1141">但是，客户端行为是不可预测的，在连接中止前它们可能未读取响应。</span><span class="sxs-lookup"><span data-stu-id="3f816-1141">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="3f816-1142">对于 HTTP/2，此过程又有所不同，因为协议支持在不关闭连接的情况下中止单个请求流。</span><span class="sxs-lookup"><span data-stu-id="3f816-1142">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="3f816-1143">五秒排出超时不适用。</span><span class="sxs-lookup"><span data-stu-id="3f816-1143">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="3f816-1144">如果在完成响应后存在任何未读的请求正文数据，则服务器会发送 HTTP/2 RST 帧。</span><span class="sxs-lookup"><span data-stu-id="3f816-1144">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="3f816-1145">其他请求正文数据帧将被忽略。</span><span class="sxs-lookup"><span data-stu-id="3f816-1145">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="3f816-1146">如果可能，客户端最好使用 [Expect:100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) 请求标头，然后等到服务器响应后再开始发送请求正文。</span><span class="sxs-lookup"><span data-stu-id="3f816-1146">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="3f816-1147">这样，客户端便有机会在发送不需要的数据之前检查响应和中止。</span><span class="sxs-lookup"><span data-stu-id="3f816-1147">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f816-1148">其他资源</span><span class="sxs-lookup"><span data-stu-id="3f816-1148">Additional resources</span></span>

* <span data-ttu-id="3f816-1149">在 Linux 上使用 UNIX 套接字时，该套接字不会在应用关闭时自动删除。</span><span class="sxs-lookup"><span data-stu-id="3f816-1149">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="3f816-1150">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/14134)。</span><span class="sxs-lookup"><span data-stu-id="3f816-1150">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="3f816-1151">RFC 7230：消息语法和路由（5.4 节：主机）</span><span class="sxs-lookup"><span data-stu-id="3f816-1151">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end
