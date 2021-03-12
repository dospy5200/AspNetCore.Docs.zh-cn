---
title: 使用 IIS 在 Windows 上托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows Server Internet Information Services (IIS) 上托管 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: 13c4c2e2759355b10a4648b313f4dbed4b3aa482
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588953"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="f8d72-103">使用 IIS 在 Windows 上托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8d72-103">Host ASP.NET Core on Windows with IIS</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f8d72-104">Internet Information Services (IIS) 是一种灵活、安全且可管理的 Web 服务器，用于托管 Web 应用（包括 ASP.NET Core）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-104">Internet Information Services (IIS) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="f8d72-105">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="f8d72-105">Supported platforms</span></span>

<span data-ttu-id="f8d72-106">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="f8d72-106">The following operating systems are supported:</span></span>

* <span data-ttu-id="f8d72-107">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-107">Windows 7 or later</span></span>
* <span data-ttu-id="f8d72-108">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-108">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="f8d72-109">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-109">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="f8d72-110">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="f8d72-110">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="f8d72-111">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="f8d72-111">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="f8d72-112">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="f8d72-112">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="f8d72-113">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="f8d72-113">Has 64-bit native dependencies.</span></span>

## <a name="install-the-aspnet-core-modulehosting-bundle"></a><span data-ttu-id="f8d72-114">安装 ASP.NET Core 模块/托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-114">Install the ASP.NET Core Module/Hosting Bundle</span></span>

<span data-ttu-id="f8d72-115">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="f8d72-115">Download the installer using the following link:</span></span>

[<span data-ttu-id="f8d72-116">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="f8d72-116">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="f8d72-117">有关如何安装 ASP.NET Core Module 或不同版本的详细说明，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-117">For more details instructions on how to install the ASP.NET Core Module, or installing different versions, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

## <a name="get-started"></a><span data-ttu-id="f8d72-118">入门</span><span class="sxs-lookup"><span data-stu-id="f8d72-118">Get started</span></span>

<span data-ttu-id="f8d72-119">有关在 IIS 上托管网站的入门知识，请参阅[入门指南](xref:tutorials/publish-to-iis)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-119">For getting started with hosting a website on IIS, see our [getting started guide](xref:tutorials/publish-to-iis).</span></span>

<span data-ttu-id="f8d72-120">有关在 Azure 应用服务上托管网站的入门知识，请参阅[“部署到 Azure 应用服务”指南](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-120">For getting started with hosting a website on Azure App Services, see our [deploying to Azure App Service guide](xref:host-and-deploy/azure-apps/index).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="f8d72-121">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-121">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="f8d72-122">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="f8d72-122">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="f8d72-123">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="f8d72-123">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="f8d72-124">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-124">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="overlapped-recycle"></a><span data-ttu-id="f8d72-125">重叠的回收</span><span class="sxs-lookup"><span data-stu-id="f8d72-125">Overlapped recycle</span></span>

<span data-ttu-id="f8d72-126">通常，建议使用[蓝绿部署](https://www.martinfowler.com/bliki/BlueGreenDeployment.html)之类的模式来实现零停机部署。</span><span class="sxs-lookup"><span data-stu-id="f8d72-126">In general, we recommend using a pattern like [blue-green deployments](https://www.martinfowler.com/bliki/BlueGreenDeployment.html) for zero-downtime deployments.</span></span> <span data-ttu-id="f8d72-127">重叠的回收等功能会有帮助，但不保证可以实现零停机部署。</span><span class="sxs-lookup"><span data-stu-id="f8d72-127">Features like Overlapped Recycle help, but don't guarantee that you can do a zero-downtime deployment.</span></span> <span data-ttu-id="f8d72-128">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/10117)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-128">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/10117).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8d72-129">其他资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-129">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="f8d72-130">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="f8d72-130">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="f8d72-131">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="f8d72-131">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="f8d72-132">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="f8d72-132">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="f8d72-133">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-133">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="f8d72-134">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-134">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="f8d72-135">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="f8d72-135">Supported operating systems</span></span>

<span data-ttu-id="f8d72-136">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="f8d72-136">The following operating systems are supported:</span></span>

* <span data-ttu-id="f8d72-137">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-137">Windows 7 or later</span></span>
* <span data-ttu-id="f8d72-138">Windows Server 2012 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-138">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="f8d72-139">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-139">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="f8d72-140">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-140">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f8d72-141">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-141">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="f8d72-142">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-142">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="f8d72-143">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="f8d72-143">Supported platforms</span></span>

<span data-ttu-id="f8d72-144">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-144">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="f8d72-145">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="f8d72-145">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="f8d72-146">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="f8d72-146">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="f8d72-147">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="f8d72-147">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="f8d72-148">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="f8d72-148">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="f8d72-149">为 32 位 (x86) 发布的应用必须已为其 IIS 应用程序池启用 32 位。</span><span class="sxs-lookup"><span data-stu-id="f8d72-149">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="f8d72-150">有关详细信息，请参阅[创建 IIS 站点](#create-the-iis-site)部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-150">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="f8d72-151">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-151">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="f8d72-152">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-152">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f8d72-153">托管模型</span><span class="sxs-lookup"><span data-stu-id="f8d72-153">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f8d72-154">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="f8d72-154">In-process hosting model</span></span>

<span data-ttu-id="f8d72-155">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-155">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="f8d72-156">进程内承载相较进程外承载提供更优的性能，因为请求并不通过环回适配器进行代理，环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="f8d72-156">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="f8d72-157">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-157">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f8d72-158">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="f8d72-158">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="f8d72-159">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="f8d72-159">Performs app initialization.</span></span>
  * <span data-ttu-id="f8d72-160">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-160">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="f8d72-161">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-161">Calls `Program.Main`.</span></span>
* <span data-ttu-id="f8d72-162">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="f8d72-162">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="f8d72-163">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="f8d72-163">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="f8d72-165">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-165">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="f8d72-166">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-166">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="f8d72-167">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-167">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="f8d72-168">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="f8d72-168">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="f8d72-169">在 IIS HTTP 服务器处理请求后：</span><span class="sxs-lookup"><span data-stu-id="f8d72-169">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="f8d72-170">请求被发送到 ASP.NET Core 中间件管道。</span><span class="sxs-lookup"><span data-stu-id="f8d72-170">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="f8d72-171">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f8d72-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="f8d72-172">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-172">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="f8d72-173">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="f8d72-173">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="f8d72-174">对于现有应用，进程内托管是可选功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-174">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="f8d72-175">ASP.NET Core Web 模板使用进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="f8d72-175">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="f8d72-176">`CreateDefaultBuilder` 添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例的方式是：调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 方法来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 和将应用托管在 IIS 工作进程（`w3wp.exe` 或 `iisexpress.exe`）内。</span><span class="sxs-lookup"><span data-stu-id="f8d72-176">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="f8d72-177">性能测试表明，与在进程外托管应用并将请求代理传入 [Kestrel](xref:fundamentals/servers/kestrel) 相比，在进程中托管 .NET Core 应用可提供明显更高的请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="f8d72-177">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f8d72-178">作为单个文件可执行文件发布的应用无法由进程内托管模型加载。</span><span class="sxs-lookup"><span data-stu-id="f8d72-178">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f8d72-179">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="f8d72-179">Out-of-process hosting model</span></span>

<span data-ttu-id="f8d72-180">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-180">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="f8d72-181">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-181">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="f8d72-182">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="f8d72-182">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f8d72-183">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="f8d72-183">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="f8d72-185">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-185">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="f8d72-186">驱动程序将请求路由到网站的配置端口上的 IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-186">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="f8d72-187">配置的端口通常是 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-187">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="f8d72-188">此模块将该请求转发到应用的随机端口上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f8d72-188">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="f8d72-189">随机端口不是 80 或 443。</span><span class="sxs-lookup"><span data-stu-id="f8d72-189">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="f8d72-190">ASP.NET Core 模块在启动时通过环境变量指定端口。</span><span class="sxs-lookup"><span data-stu-id="f8d72-190">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="f8d72-191"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-191">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="f8d72-192">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-192">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f8d72-193">此模块不支持 HTTPS 转发。</span><span class="sxs-lookup"><span data-stu-id="f8d72-193">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="f8d72-194">即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="f8d72-194">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f8d72-195">Kestrel 从模块获取请求后，请求会被转发到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-195">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f8d72-196">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f8d72-196">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f8d72-197">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f8d72-197">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f8d72-198">应用的响应传递回 IIS，IIS 将响应转发回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="f8d72-198">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="f8d72-199">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-199">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="f8d72-200">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-200">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="f8d72-201">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="f8d72-201">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="f8d72-202">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="f8d72-202">Enable the IISIntegration components</span></span>

<span data-ttu-id="f8d72-203">在 `CreateHostBuilder` 中生成主机 (`Program.cs`)，请调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="f8d72-203">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="f8d72-204">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/generic-host#default-builder-settings>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-204">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="f8d72-205">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-205">IIS options</span></span>

<span data-ttu-id="f8d72-206">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="f8d72-206">**In-process hosting model**</span></span>

<span data-ttu-id="f8d72-207">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-207">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="f8d72-208">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="f8d72-208">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="f8d72-209">选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-209">Option</span></span>                         | <span data-ttu-id="f8d72-210">默认</span><span class="sxs-lookup"><span data-stu-id="f8d72-210">Default</span></span> | <span data-ttu-id="f8d72-211">设置</span><span class="sxs-lookup"><span data-stu-id="f8d72-211">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="f8d72-212">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-212">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="f8d72-213">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="f8d72-213">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="f8d72-214">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-214">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="f8d72-215">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-215">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="f8d72-216">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="f8d72-216">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="f8d72-217">`HttpContext.Request` 和 `HttpContext.Response` 是否允许同步 I/O。</span><span class="sxs-lookup"><span data-stu-id="f8d72-217">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="f8d72-218">获取或设置 `HttpRequest` 的最大请求正文大小。</span><span class="sxs-lookup"><span data-stu-id="f8d72-218">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="f8d72-219">请注意，IIS 本身有限制 `maxAllowedContentLength`，这一限制将在 `IISServerOptions` 中设置 `MaxRequestBodySize` 之前进行处理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-219">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="f8d72-220">更改 `MaxRequestBodySize` 不会影响 `maxAllowedContentLength`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-220">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="f8d72-221">若要增加 `maxAllowedContentLength`，请在 `web.config` 中添加一个将 `maxAllowedContentLength` 设置为更高值的项。</span><span class="sxs-lookup"><span data-stu-id="f8d72-221">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="f8d72-222">有关更多详细信息，请参阅[配置](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-222">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="f8d72-223">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="f8d72-223">**Out-of-process hosting model**</span></span>

<span data-ttu-id="f8d72-224">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-224">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="f8d72-225">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="f8d72-225">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="f8d72-226">选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-226">Option</span></span>                         | <span data-ttu-id="f8d72-227">默认</span><span class="sxs-lookup"><span data-stu-id="f8d72-227">Default</span></span> | <span data-ttu-id="f8d72-228">设置</span><span class="sxs-lookup"><span data-stu-id="f8d72-228">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="f8d72-229">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-229">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="f8d72-230">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="f8d72-230">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="f8d72-231">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-231">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="f8d72-232">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-232">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="f8d72-233">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="f8d72-233">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="f8d72-234">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-234">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f8d72-235">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="f8d72-235">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f8d72-236">将 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块配置为转发：</span><span class="sxs-lookup"><span data-stu-id="f8d72-236">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="f8d72-237">方案 (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-237">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="f8d72-238">发起请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f8d72-238">Remote IP address where the request originated.</span></span>

<span data-ttu-id="f8d72-239">[IIS 集成中间件](#enable-the-iisintegration-components)配置转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-239">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="f8d72-240">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-240">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="f8d72-241">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-241">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="f8d72-242">`web.config` 文件</span><span class="sxs-lookup"><span data-stu-id="f8d72-242">`web.config` file</span></span>

<span data-ttu-id="f8d72-243">`web.config` 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-243">The `web.config` file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="f8d72-244">发布项目时，`web.config` 文件的创建、转换和发布是由 MSBuild 目标 (`_TransformWebConfig`) 处理的。</span><span class="sxs-lookup"><span data-stu-id="f8d72-244">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="f8d72-245">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-245">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="f8d72-246">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="f8d72-246">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="f8d72-247">如果项目中没有 `web.config` 文件，则该文件是使用正确的 `processPath` 和 `arguments`（用于配置 ASP.NET Core 模块）创建的，并且已被移到[发布的输出](xref:host-and-deploy/directory-structure)中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-247">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="f8d72-248">如果项目中没有 `web.config` 文件，则该文件是通过正确的 `processPath` 和 `arguments`（用于配置 ASP.NET Core 模块）转换的，并且已被移到发布的输出中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-248">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="f8d72-249">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-249">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="f8d72-250">`web.config` 文件可能会提供控制活动 IIS 模块的额外 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-250">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="f8d72-251">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-251">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="f8d72-252">为了防止 Web SDK 转换 `web.config` 文件，请在项目文件中使用 `<IsTransformWebConfigDisabled>` 属性：</span><span class="sxs-lookup"><span data-stu-id="f8d72-252">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="f8d72-253">禁用 Web SDK 对文件的转换时，`processPath` 和 `arguments` 应由开发人员手动设置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-253">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="f8d72-254">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-254">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="f8d72-255">`web.config` 文件位置</span><span class="sxs-lookup"><span data-stu-id="f8d72-255">`web.config` file location</span></span>

<span data-ttu-id="f8d72-256">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，`web.config` 文件必须存在于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-256">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="f8d72-257">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="f8d72-257">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="f8d72-258">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 `web.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-258">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="f8d72-259">敏感文件存在于应用的物理路径中，如 `{ASSEMBLY}.runtimeconfig.json`、`{ASSEMBLY}.xml`（XML 文档注释）和 `{ASSEMBLY}.deps.json`，其中 `{ASSEMBLY}` 占位符为程序集名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-259">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="f8d72-260">如果有 `web.config` 文件且站点正常启动时，IIS 在收到敏感文件请求时不会提供这些敏感文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-260">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="f8d72-261">如果 `web.config` 文件缺失、名字错误或者无法将站点配置为正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-261">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="f8d72-262">`web.config` 文件必须始终存在于部署中、名称正确以及能够将站点配置为正常启动。切勿从生产部署中删除 `web.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-262">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="f8d72-263">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="f8d72-263">Transform web.config</span></span>

<span data-ttu-id="f8d72-264">如果在发布时需要转换 `web.config`，请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-264">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="f8d72-265">你需要在发布时转换 `web.config`，以根据配置、配置文件或环境来设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="f8d72-265">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="f8d72-266">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="f8d72-266">IIS configuration</span></span>

<span data-ttu-id="f8d72-267">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="f8d72-267">**Windows Server operating systems**</span></span>

<span data-ttu-id="f8d72-268">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-268">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="f8d72-269">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="f8d72-269">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="f8d72-270">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-270">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="f8d72-272">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="f8d72-272">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="f8d72-273">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-273">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="f8d72-275">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-275">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="f8d72-276">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-276">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="f8d72-277">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-277">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="f8d72-278">有关详细信息，请参阅 [Windows 身份验证 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-278">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="f8d72-279">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-279">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="f8d72-280">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-280">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="f8d72-281">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-281">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="f8d72-282">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-282">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="f8d72-283">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-283">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="f8d72-284">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-284">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="f8d72-285">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-285">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="f8d72-286">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="f8d72-286">**Windows desktop operating systems**</span></span>

<span data-ttu-id="f8d72-287">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-287">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="f8d72-288">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="f8d72-288">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="f8d72-289">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-289">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="f8d72-290">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-290">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="f8d72-291">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-291">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="f8d72-292">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-292">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="f8d72-293">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-293">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="f8d72-294">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-294">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="f8d72-295">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-295">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="f8d72-296">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-296">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="f8d72-297">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-297">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="f8d72-298">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-298">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="f8d72-299">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-299">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="f8d72-300">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-300">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="f8d72-301">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-301">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="f8d72-302">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-302">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="f8d72-303">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="f8d72-303">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="f8d72-305">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-305">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="f8d72-306">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="f8d72-306">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="f8d72-307">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-307">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="f8d72-308">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-308">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f8d72-309">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="f8d72-309">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="f8d72-310">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-310">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="f8d72-311">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-311">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="f8d72-312">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-312">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="f8d72-313">直接下载（当前版本）</span><span class="sxs-lookup"><span data-stu-id="f8d72-313">Direct download (current version)</span></span>

<span data-ttu-id="f8d72-314">使用以下链接下载安装程序：</span><span class="sxs-lookup"><span data-stu-id="f8d72-314">Download the installer using the following link:</span></span>

[<span data-ttu-id="f8d72-315">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="f8d72-315">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="f8d72-316">先前版本的安装程序</span><span class="sxs-lookup"><span data-stu-id="f8d72-316">Earlier versions of the installer</span></span>

<span data-ttu-id="f8d72-317">若要获取先前版本的安装程序：</span><span class="sxs-lookup"><span data-stu-id="f8d72-317">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="f8d72-318">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="f8d72-318">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="f8d72-319">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-319">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="f8d72-320">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-320">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="f8d72-321">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-321">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="f8d72-322">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-322">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="f8d72-323">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-323">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="f8d72-324">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-324">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="f8d72-325">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-325">Run the installer on the server.</span></span> <span data-ttu-id="f8d72-326">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="f8d72-326">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="f8d72-327">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="f8d72-327">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="f8d72-328">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-328">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="f8d72-329">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-329">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="f8d72-330">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-330">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="f8d72-331">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-331">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="f8d72-332">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-332">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="f8d72-333">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="f8d72-333">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="f8d72-334">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-334">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="f8d72-335">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (`applicationHost.config`) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="f8d72-335">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="f8d72-336">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-336">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="f8d72-337">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-337">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="f8d72-338">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f8d72-338">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="f8d72-339">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="f8d72-339">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="f8d72-340">ASP.NET Core 不采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="f8d72-340">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="f8d72-341">通过安装新的托管捆绑包升级共享框架后，请重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f8d72-341">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="f8d72-342">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-342">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="f8d72-343">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-343">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="f8d72-344">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="f8d72-344">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="f8d72-345">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-345">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="f8d72-346">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="f8d72-346">The preferred method is to use WebPI.</span></span> <span data-ttu-id="f8d72-347">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-347">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="f8d72-348">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="f8d72-348">Create the IIS site</span></span>

1. <span data-ttu-id="f8d72-349">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-349">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="f8d72-350">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-350">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="f8d72-351">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-351">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="f8d72-352">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-352">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="f8d72-353">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8d72-353">Right-click the **Sites** folder.</span></span> <span data-ttu-id="f8d72-354">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-354">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="f8d72-355">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-355">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="f8d72-356">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="f8d72-356">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="f8d72-358">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-358">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="f8d72-359">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="f8d72-359">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="f8d72-360">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-360">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="f8d72-361">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-361">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="f8d72-362">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="f8d72-362">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="f8d72-363">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-363">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="f8d72-364">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-364">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="f8d72-365">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-365">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="f8d72-366">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="f8d72-366">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="f8d72-368">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-368">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="f8d72-369">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载。</span><span class="sxs-lookup"><span data-stu-id="f8d72-369">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="f8d72-370">将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR)，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-370">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="f8d72-371">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-371">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="f8d72-372">*ASP.NET Core 2.2 或更高版本*：</span><span class="sxs-lookup"><span data-stu-id="f8d72-372">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="f8d72-373">对于使用 32 位 SDK 发布的 32 位 (x86) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，且该 SDK 使用[进程内托管模型](#in-process-hosting-model)，请为 32 位启用应用程序池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-373">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="f8d72-374">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-374">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="f8d72-375">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-375">Select the app's Application Pool.</span></span> <span data-ttu-id="f8d72-376">在“操作”边栏中，选择，“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-376">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="f8d72-377">将“启用 32 位应用程序”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-377">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="f8d72-378">对于使用[进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-378">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="f8d72-379">在 IIS 管理器中，导航到“连接”边栏中的“应用程序池” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-379">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="f8d72-380">选择应用的应用程序池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-380">Select the app's Application Pool.</span></span> <span data-ttu-id="f8d72-381">在“操作”边栏中，选择，“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-381">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="f8d72-382">将“启用 32 位应用程序”设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-382">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="f8d72-383">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-383">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="f8d72-384">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-384">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="f8d72-385">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-385">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="f8d72-386">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-386">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="f8d72-387">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-387">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="f8d72-388">部署应用</span><span class="sxs-lookup"><span data-stu-id="f8d72-388">Deploy the app</span></span>

<span data-ttu-id="f8d72-389">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="f8d72-389">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="f8d72-390">建议使用[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)这种部署机制，不过将应用从项目的 `publish` 文件夹移到托管系统的部署文件夹还有几种选择。</span><span class="sxs-lookup"><span data-stu-id="f8d72-390">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's `publish` folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="f8d72-391">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-391">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="f8d72-392">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-392">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="f8d72-393">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="f8d72-393">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="f8d72-395">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-395">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="f8d72-396">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-396">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="f8d72-397">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-397">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="f8d72-398">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="f8d72-398">Alternatives to Web Deploy</span></span>

<span data-ttu-id="f8d72-399">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="f8d72-399">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="f8d72-400">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-400">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="f8d72-401">浏览网站</span><span class="sxs-lookup"><span data-stu-id="f8d72-401">Browse the website</span></span>

<span data-ttu-id="f8d72-402">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-402">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="f8d72-403">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-403">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="f8d72-404">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="f8d72-404">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="f8d72-406">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="f8d72-406">Locked deployment files</span></span>

<span data-ttu-id="f8d72-407">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="f8d72-407">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="f8d72-408">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-408">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="f8d72-409">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="f8d72-409">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="f8d72-410">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-410">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="f8d72-411">`app_offline.htm` 文件位于 Web 应用目录的根路径下。</span><span class="sxs-lookup"><span data-stu-id="f8d72-411">An `app_offline.htm` file is placed at the root of the web app directory.</span></span> <span data-ttu-id="f8d72-412">如果该文件存在，ASP.NET Core 模块在部署期间正常关闭应用并提供 `app_offline.htm` 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-412">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the `app_offline.htm` file during the deployment.</span></span> <span data-ttu-id="f8d72-413">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-413">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="f8d72-414">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-414">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="f8d72-415">使用 PowerShell 删除 `app_offline.htm`（要求 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="f8d72-415">Use PowerShell to drop `app_offline.htm` (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm
  ```

## <a name="data-protection"></a><span data-ttu-id="f8d72-416">数据保护</span><span class="sxs-lookup"><span data-stu-id="f8d72-416">Data protection</span></span>

<span data-ttu-id="f8d72-417">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-417">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="f8d72-418">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-418">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="f8d72-419">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="f8d72-419">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="f8d72-420">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-420">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="f8d72-421">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="f8d72-421">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="f8d72-422">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="f8d72-422">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="f8d72-423">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="f8d72-423">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="f8d72-424">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-424">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="f8d72-425">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f8d72-425">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="f8d72-426">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="f8d72-426">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="f8d72-427">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-427">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="f8d72-428">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="f8d72-428">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="f8d72-429">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-429">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="f8d72-430">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="f8d72-430">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="f8d72-431">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="f8d72-431">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="f8d72-432">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="f8d72-432">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="f8d72-433">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="f8d72-433">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="f8d72-434">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="f8d72-434">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="f8d72-435">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="f8d72-435">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="f8d72-436">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-436">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="f8d72-437">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-437">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="f8d72-438">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="f8d72-438">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="f8d72-439">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-439">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="f8d72-440">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-440">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="f8d72-441">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="f8d72-441">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="f8d72-442">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-442">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="f8d72-443">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-443">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="f8d72-444">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-444">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="f8d72-445">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-445">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="f8d72-446">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f8d72-446">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="f8d72-447">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8d72-447">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="f8d72-448">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-448">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="f8d72-449">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="f8d72-449">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="f8d72-450">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-450">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="f8d72-451">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="f8d72-451">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="f8d72-452">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-452">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="f8d72-453">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="f8d72-453">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="f8d72-454">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-454">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="f8d72-455">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-455">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="f8d72-456">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="f8d72-456">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="f8d72-457">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="f8d72-457">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="f8d72-458">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-458">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="f8d72-459">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="f8d72-459">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="f8d72-460">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-460">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="f8d72-461">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-461">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="f8d72-462">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="f8d72-462">Virtual Directories</span></span>

<span data-ttu-id="f8d72-463">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-463">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="f8d72-464">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-464">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="f8d72-465">子应用程序</span><span class="sxs-lookup"><span data-stu-id="f8d72-465">Sub-applications</span></span>

<span data-ttu-id="f8d72-466">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-466">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="f8d72-467">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-467">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="f8d72-468">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="f8d72-468">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="f8d72-469">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="f8d72-469">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="f8d72-470">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-470">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="f8d72-471">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-471">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="f8d72-472">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-472">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="f8d72-473">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="f8d72-473">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="f8d72-474">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="f8d72-474">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="f8d72-475">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="f8d72-475">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="f8d72-476">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-476">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="f8d72-477">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-477">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="f8d72-478">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-478">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="f8d72-479">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-479">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="f8d72-480">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="f8d72-480">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="f8d72-481">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-481">Select **OK**.</span></span>

<span data-ttu-id="f8d72-482">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-482">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="f8d72-483">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-483">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="f8d72-484">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="f8d72-484">Configuration of IIS with web.config</span></span>

<span data-ttu-id="f8d72-485">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="f8d72-485">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="f8d72-486">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="f8d72-486">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="f8d72-487">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="f8d72-487">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="f8d72-488">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="f8d72-488">For more information, see the following topics:</span></span>

* [<span data-ttu-id="f8d72-489">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="f8d72-489">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="f8d72-490">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-490">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="f8d72-491">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="f8d72-491">Configuration sections of web.config</span></span>

<span data-ttu-id="f8d72-492">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="f8d72-492">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="f8d72-493">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-493">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="f8d72-494">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-494">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="f8d72-495">应用程序池</span><span class="sxs-lookup"><span data-stu-id="f8d72-495">Application Pools</span></span>

<span data-ttu-id="f8d72-496">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="f8d72-496">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="f8d72-497">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-497">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="f8d72-498">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="f8d72-498">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="f8d72-499">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-499">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="f8d72-500">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-500">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="f8d72-501">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-501">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="f8d72-502">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="f8d72-502">Application Pool Identity</span></span>

<span data-ttu-id="f8d72-503">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="f8d72-503">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="f8d72-504">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="f8d72-504">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="f8d72-505">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="f8d72-505">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="f8d72-507">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-507">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="f8d72-508">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="f8d72-508">Resources can be secured using this identity.</span></span> <span data-ttu-id="f8d72-509">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="f8d72-509">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="f8d72-510">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="f8d72-510">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="f8d72-511">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="f8d72-511">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="f8d72-512">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-512">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="f8d72-513">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-513">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="f8d72-514">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="f8d72-514">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="f8d72-515">在“输入要选择的对象名”区域中输入 `IIS AppPool\{APP POOL NAME}`，其中 `{APP POOL NAME}` 占位符为应用池名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-515">Enter `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="f8d72-516">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="f8d72-516">Select the **Check Names** button.</span></span> <span data-ttu-id="f8d72-517">对于 DefaultAppPool，请使用 `IIS AppPool\DefaultAppPool` 检查名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-517">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="f8d72-518">如果选择了“检查名称”按钮，对象名称区域中将指示 `DefaultAppPool`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-518">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="f8d72-519">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-519">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="f8d72-520">检查对象名称时使用 `IIS AppPool\{APP POOL NAME}` 格式，其中 `{APP POOL NAME}` 占位符是应用池名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-520">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="f8d72-522">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-522">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="f8d72-524">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-524">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="f8d72-525">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-525">Provide additional permissions as needed.</span></span>

<span data-ttu-id="f8d72-526">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-526">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="f8d72-527">以 `DefaultAppPool` 为例，使用了以下命令：</span><span class="sxs-lookup"><span data-stu-id="f8d72-527">Using the `DefaultAppPool` as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="f8d72-528">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-528">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="f8d72-529">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="f8d72-529">HTTP/2 support</span></span>

<span data-ttu-id="f8d72-530">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="f8d72-530">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="f8d72-531">进程内</span><span class="sxs-lookup"><span data-stu-id="f8d72-531">In-process</span></span>
  * <span data-ttu-id="f8d72-532">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-532">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="f8d72-533">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="f8d72-533">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="f8d72-534">进程外</span><span class="sxs-lookup"><span data-stu-id="f8d72-534">Out-of-process</span></span>
  * <span data-ttu-id="f8d72-535">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-535">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="f8d72-536">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f8d72-536">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="f8d72-537">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="f8d72-537">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f8d72-538">对于建立了 HTTP/2 连接时的进程内部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-538">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="f8d72-539">对于建立了 HTTP/2 连接时的进程外部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-539">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="f8d72-540">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-540">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="f8d72-541">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f8d72-541">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="f8d72-542">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f8d72-542">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="f8d72-543">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-543">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="f8d72-544">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="f8d72-544">CORS preflight requests</span></span>

<span data-ttu-id="f8d72-545">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-545">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="f8d72-546">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-546">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="f8d72-547">若要了解如何在 `web.config` 中配置应用的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-547">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="f8d72-548">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="f8d72-548">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="f8d72-549">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-549">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="f8d72-550">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="f8d72-550">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="f8d72-551">[空闲超时](#idle-timeout)：可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-551">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="f8d72-552">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="f8d72-552">Application Initialization Module</span></span>

<span data-ttu-id="f8d72-553">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-553">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="f8d72-554">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-554">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="f8d72-555">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="f8d72-555">The request triggers the app to start.</span></span> <span data-ttu-id="f8d72-556">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-556">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="f8d72-557">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="f8d72-557">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="f8d72-558">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-558">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="f8d72-559">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="f8d72-559">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="f8d72-560">打开“Internet Information Services”>“万维网服务”>“应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-560">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="f8d72-561">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-561">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="f8d72-562">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="f8d72-562">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="f8d72-563">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-563">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="f8d72-564">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-564">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="f8d72-565">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-565">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="f8d72-566">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="f8d72-566">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="f8d72-567">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="f8d72-567">Using IIS Manager:</span></span>

  1. <span data-ttu-id="f8d72-568">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-568">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="f8d72-569">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-569">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="f8d72-570">默认的“启动模式”为“按需” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-570">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="f8d72-571">将“启动模式”设置为“始终运行”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-571">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="f8d72-572">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-572">Select **OK**.</span></span>
  1. <span data-ttu-id="f8d72-573">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-573">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="f8d72-574">右键单击应用，并选择“管理网站”>“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-574">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="f8d72-575">默认的“预加载已启用”设置为“False” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-575">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="f8d72-576">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-576">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="f8d72-577">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-577">Select **OK**.</span></span>

* <span data-ttu-id="f8d72-578">使用 `web.config` 向应用的 web.config` 文件中的 `<system.webServer>` 元素添加 `<applicationInitialization>` 元素（其中 `doAppInitAfterRestart` 设置为 `true\`）：</span><span class="sxs-lookup"><span data-stu-id="f8d72-578">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's web.config\` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="f8d72-579">空闲超时</span><span class="sxs-lookup"><span data-stu-id="f8d72-579">Idle Timeout</span></span>

<span data-ttu-id="f8d72-580">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-580">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="f8d72-581">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-581">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="f8d72-582">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-582">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="f8d72-583">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-583">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="f8d72-584">默认的“空闲超时(分钟)”为“20”分钟 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-584">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="f8d72-585">将“空闲超时(分钟)”设置为“0”（零） 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-585">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="f8d72-586">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-586">Select **OK**.</span></span>
1. <span data-ttu-id="f8d72-587">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="f8d72-587">Recycle the worker process.</span></span>

<span data-ttu-id="f8d72-588">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="f8d72-588">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="f8d72-589">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="f8d72-589">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="f8d72-590">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-590">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="f8d72-591">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-591">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="f8d72-592">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="f8d72-592">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="f8d72-593">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-593">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="f8d72-594">[应用程序池 \<processModel> 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-594">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="f8d72-595">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-595">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="f8d72-596">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="f8d72-596">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="f8d72-597">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="f8d72-597">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="f8d72-598">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-598">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="f8d72-599">其他资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-599">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="f8d72-600">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="f8d72-600">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="f8d72-601">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="f8d72-601">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="f8d72-602">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="f8d72-602">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f8d72-603">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-603">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="f8d72-604">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-604">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="f8d72-605">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="f8d72-605">Supported operating systems</span></span>

<span data-ttu-id="f8d72-606">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="f8d72-606">The following operating systems are supported:</span></span>

* <span data-ttu-id="f8d72-607">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-607">Windows 7 or later</span></span>
* <span data-ttu-id="f8d72-608">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-608">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f8d72-609">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-609">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="f8d72-610">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-610">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f8d72-611">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-611">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="f8d72-612">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-612">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="f8d72-613">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="f8d72-613">Supported platforms</span></span>

<span data-ttu-id="f8d72-614">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-614">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="f8d72-615">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="f8d72-615">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="f8d72-616">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="f8d72-616">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="f8d72-617">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="f8d72-617">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="f8d72-618">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="f8d72-618">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="f8d72-619">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-619">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="f8d72-620">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-620">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f8d72-621">托管模型</span><span class="sxs-lookup"><span data-stu-id="f8d72-621">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f8d72-622">进程内托管模型</span><span class="sxs-lookup"><span data-stu-id="f8d72-622">In-process hosting model</span></span>

<span data-ttu-id="f8d72-623">使用进程内托管，ASP.NET Core 在与其 IIS 工作进程相同的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-623">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="f8d72-624">进程内托管的性能高于进程外托管的性能，因为：</span><span class="sxs-lookup"><span data-stu-id="f8d72-624">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="f8d72-625">请求并不通过环回适配器进行代理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-625">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="f8d72-626">环回适配器是一个网络接口，用于将传出的网络流量返回给同一计算机。</span><span class="sxs-lookup"><span data-stu-id="f8d72-626">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="f8d72-627">IIS 使用 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-627">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f8d72-628">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)：</span><span class="sxs-lookup"><span data-stu-id="f8d72-628">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="f8d72-629">执行应用初始化。</span><span class="sxs-lookup"><span data-stu-id="f8d72-629">Performs app initialization.</span></span>
  * <span data-ttu-id="f8d72-630">加载 [CoreCLR](/dotnet/standard/glossary#coreclr)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-630">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="f8d72-631">调用 `Program.Main`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-631">Calls `Program.Main`.</span></span>
* <span data-ttu-id="f8d72-632">处理 IIS 本机请求的生存期。</span><span class="sxs-lookup"><span data-stu-id="f8d72-632">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="f8d72-633">定目标到 .NET Framework 的 ASP.NET Core 应用不支持进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="f8d72-633">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="f8d72-634">下图说明了 IIS、ASP.NET Core 模块和进程内托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="f8d72-634">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![进程内托管方案中的 ASP.NET Core 模块](index/_static/ancm-inprocess.png)

<span data-ttu-id="f8d72-636">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-636">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f8d72-637">驱动程序将本机请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-637">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f8d72-638">ASP.NET Core 模块接收本机请求，并将其传递给 IIS HTTP 服务器 (`IISHttpServer`)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-638">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="f8d72-639">IIS HTTP 服务器是将请求从本机转换为托管的 IIS 进程内服务器实现。</span><span class="sxs-lookup"><span data-stu-id="f8d72-639">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="f8d72-640">IIS HTTP 服务器处理请求之后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-640">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f8d72-641">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f8d72-641">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f8d72-642">应用的响应通过 IIS HTTP 服务器传递回 IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-642">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="f8d72-643">IIS 将响应发送到发起请求的客户端。</span><span class="sxs-lookup"><span data-stu-id="f8d72-643">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="f8d72-644">进程内托管选择使用现有应用，但 [dotnet new](/dotnet/core/tools/dotnet-new) 模板默认使用所有 IIS 和 IIS Express 方案的进程内托管模型。</span><span class="sxs-lookup"><span data-stu-id="f8d72-644">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="f8d72-645">`CreateDefaultBuilder` 将通过调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> 方法添加 <xref:Microsoft.AspNetCore.Hosting.Server.IServer> 实例，来启动 [CoreCLR](/dotnet/standard/glossary#coreclr) 并在 IIS 工作进程（w3wp.exe 或 iisexpress.exe）内托管应用。 </span><span class="sxs-lookup"><span data-stu-id="f8d72-645">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="f8d72-646">性能测试表明，与在进程外托管应用并将请求代理到 [Kestrel](xref:fundamentals/servers/kestrel) 服务器相比，在进程中托管 .NET Core 应用可以大大提升请求吞吐量。</span><span class="sxs-lookup"><span data-stu-id="f8d72-646">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f8d72-647">进程外托管模型</span><span class="sxs-lookup"><span data-stu-id="f8d72-647">Out-of-process hosting model</span></span>

<span data-ttu-id="f8d72-648">由于运行 ASP.NET Core 的进程与 IIS 工作进程分开，因此 ASP.NET Core 模块会负责进程管理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-648">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="f8d72-649">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-649">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="f8d72-650">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="f8d72-650">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f8d72-651">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="f8d72-651">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![进程外托管方案中的 ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="f8d72-653">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-653">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f8d72-654">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-654">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f8d72-655">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f8d72-655">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="f8d72-656">该模块在启动时通过环境变量指定端口，<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 扩展将服务器配置为侦听 `http://localhost:{PORT}`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-656">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="f8d72-657">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-657">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f8d72-658">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="f8d72-658">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f8d72-659">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-659">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f8d72-660">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f8d72-660">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f8d72-661">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f8d72-661">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f8d72-662">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="f8d72-662">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="f8d72-663">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-663">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="f8d72-664">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-664">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="f8d72-665">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="f8d72-665">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="f8d72-666">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="f8d72-666">Enable the IISIntegration components</span></span>

<span data-ttu-id="f8d72-667">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="f8d72-667">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="f8d72-668">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-668">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="f8d72-669">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-669">IIS options</span></span>

<span data-ttu-id="f8d72-670">**进程内承载模型**</span><span class="sxs-lookup"><span data-stu-id="f8d72-670">**In-process hosting model**</span></span>

<span data-ttu-id="f8d72-671">要配置 IIS 服务器选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> 中包括 <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-671">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="f8d72-672">下面的示例禁用 AutomaticAuthentication：</span><span class="sxs-lookup"><span data-stu-id="f8d72-672">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="f8d72-673">选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-673">Option</span></span>                         | <span data-ttu-id="f8d72-674">默认</span><span class="sxs-lookup"><span data-stu-id="f8d72-674">Default</span></span> | <span data-ttu-id="f8d72-675">设置</span><span class="sxs-lookup"><span data-stu-id="f8d72-675">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="f8d72-676">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-676">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="f8d72-677">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="f8d72-677">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="f8d72-678">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-678">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="f8d72-679">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-679">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="f8d72-680">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="f8d72-680">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="f8d72-681">**进程外承载模型**</span><span class="sxs-lookup"><span data-stu-id="f8d72-681">**Out-of-process hosting model**</span></span>

<span data-ttu-id="f8d72-682">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-682">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="f8d72-683">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="f8d72-683">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="f8d72-684">选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-684">Option</span></span>                         | <span data-ttu-id="f8d72-685">默认</span><span class="sxs-lookup"><span data-stu-id="f8d72-685">Default</span></span> | <span data-ttu-id="f8d72-686">设置</span><span class="sxs-lookup"><span data-stu-id="f8d72-686">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="f8d72-687">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-687">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="f8d72-688">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="f8d72-688">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="f8d72-689">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-689">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="f8d72-690">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-690">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="f8d72-691">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="f8d72-691">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="f8d72-692">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-692">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f8d72-693">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="f8d72-693">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f8d72-694">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f8d72-694">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="f8d72-695">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-695">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="f8d72-696">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-696">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="f8d72-697">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="f8d72-697">web.config file</span></span>

<span data-ttu-id="f8d72-698">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-698">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="f8d72-699">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-699">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="f8d72-700">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-700">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="f8d72-701">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="f8d72-701">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="f8d72-702">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-702">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="f8d72-703">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="f8d72-703">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="f8d72-704">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-704">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="f8d72-705">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="f8d72-705">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="f8d72-706">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-706">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="f8d72-707">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="f8d72-707">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="f8d72-708">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="f8d72-708">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="f8d72-709">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-709">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="f8d72-710">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="f8d72-710">web.config file location</span></span>

<span data-ttu-id="f8d72-711">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-711">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="f8d72-712">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="f8d72-712">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="f8d72-713">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-713">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="f8d72-714">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-714">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="f8d72-715">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="f8d72-715">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="f8d72-716">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-716">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="f8d72-717">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="f8d72-717">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="f8d72-718">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="f8d72-718">Transform web.config</span></span>

<span data-ttu-id="f8d72-719">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-719">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="f8d72-720">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="f8d72-720">IIS configuration</span></span>

<span data-ttu-id="f8d72-721">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="f8d72-721">**Windows Server operating systems**</span></span>

<span data-ttu-id="f8d72-722">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-722">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="f8d72-723">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="f8d72-723">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="f8d72-724">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-724">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="f8d72-726">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="f8d72-726">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="f8d72-727">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-727">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="f8d72-729">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-729">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="f8d72-730">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-730">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="f8d72-731">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-731">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="f8d72-732">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-732">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="f8d72-733">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-733">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="f8d72-734">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-734">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="f8d72-735">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-735">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="f8d72-736">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-736">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="f8d72-737">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-737">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="f8d72-738">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-738">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="f8d72-739">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-739">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="f8d72-740">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="f8d72-740">**Windows desktop operating systems**</span></span>

<span data-ttu-id="f8d72-741">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-741">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="f8d72-742">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="f8d72-742">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="f8d72-743">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-743">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="f8d72-744">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-744">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="f8d72-745">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-745">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="f8d72-746">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-746">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="f8d72-747">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-747">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="f8d72-748">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-748">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="f8d72-749">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-749">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="f8d72-750">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-750">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="f8d72-751">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-751">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="f8d72-752">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-752">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="f8d72-753">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-753">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="f8d72-754">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-754">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="f8d72-755">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-755">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="f8d72-756">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-756">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="f8d72-757">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="f8d72-757">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="f8d72-759">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-759">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="f8d72-760">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="f8d72-760">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="f8d72-761">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-761">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="f8d72-762">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-762">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f8d72-763">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="f8d72-763">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="f8d72-764">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-764">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="f8d72-765">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-765">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="f8d72-766">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-766">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="f8d72-767">下载</span><span class="sxs-lookup"><span data-stu-id="f8d72-767">Download</span></span>

1. <span data-ttu-id="f8d72-768">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="f8d72-768">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="f8d72-769">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-769">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="f8d72-770">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-770">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="f8d72-771">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-771">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="f8d72-772">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-772">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="f8d72-773">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-773">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="f8d72-774">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-774">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="f8d72-775">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-775">Run the installer on the server.</span></span> <span data-ttu-id="f8d72-776">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="f8d72-776">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="f8d72-777">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="f8d72-777">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="f8d72-778">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-778">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="f8d72-779">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-779">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="f8d72-780">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-780">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="f8d72-781">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-781">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="f8d72-782">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-782">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="f8d72-783">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="f8d72-783">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="f8d72-784">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-784">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="f8d72-785">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="f8d72-785">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="f8d72-786">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-786">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="f8d72-787">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-787">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="f8d72-788">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f8d72-788">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="f8d72-789">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="f8d72-789">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="f8d72-790">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-790">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="f8d72-791">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="f8d72-791">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="f8d72-792">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="f8d72-792">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="f8d72-793">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="f8d72-793">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="f8d72-794">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="f8d72-794">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="f8d72-795">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="f8d72-795">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="f8d72-796">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-796">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="f8d72-797">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-797">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="f8d72-798">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="f8d72-798">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="f8d72-799">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-799">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="f8d72-800">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="f8d72-800">The preferred method is to use WebPI.</span></span> <span data-ttu-id="f8d72-801">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-801">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="f8d72-802">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="f8d72-802">Create the IIS site</span></span>

1. <span data-ttu-id="f8d72-803">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-803">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="f8d72-804">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-804">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="f8d72-805">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-805">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="f8d72-806">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-806">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="f8d72-807">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8d72-807">Right-click the **Sites** folder.</span></span> <span data-ttu-id="f8d72-808">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-808">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="f8d72-809">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-809">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="f8d72-810">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="f8d72-810">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="f8d72-812">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-812">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="f8d72-813">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="f8d72-813">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="f8d72-814">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-814">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="f8d72-815">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-815">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="f8d72-816">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="f8d72-816">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="f8d72-817">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-817">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="f8d72-818">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-818">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="f8d72-819">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-819">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="f8d72-820">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="f8d72-820">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="f8d72-822">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-822">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="f8d72-823">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-823">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="f8d72-824">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-824">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="f8d72-825">*ASP.NET Core 2.2 或更高版本*：对于使用 [进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-825">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="f8d72-826">在 IIS 管理器 >“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-826">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="f8d72-827">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-827">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="f8d72-828">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-828">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="f8d72-829">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-829">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="f8d72-830">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-830">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="f8d72-831">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-831">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="f8d72-832">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-832">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="f8d72-833">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-833">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="f8d72-834">部署应用</span><span class="sxs-lookup"><span data-stu-id="f8d72-834">Deploy the app</span></span>

<span data-ttu-id="f8d72-835">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="f8d72-835">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="f8d72-836">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8d72-836">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="f8d72-837">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-837">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="f8d72-838">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-838">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="f8d72-839">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="f8d72-839">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="f8d72-841">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-841">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="f8d72-842">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-842">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="f8d72-843">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-843">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="f8d72-844">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="f8d72-844">Alternatives to Web Deploy</span></span>

<span data-ttu-id="f8d72-845">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="f8d72-845">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="f8d72-846">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-846">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="f8d72-847">浏览网站</span><span class="sxs-lookup"><span data-stu-id="f8d72-847">Browse the website</span></span>

<span data-ttu-id="f8d72-848">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-848">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="f8d72-849">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-849">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="f8d72-850">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="f8d72-850">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="f8d72-852">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="f8d72-852">Locked deployment files</span></span>

<span data-ttu-id="f8d72-853">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="f8d72-853">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="f8d72-854">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-854">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="f8d72-855">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="f8d72-855">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="f8d72-856">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-856">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="f8d72-857">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-857">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="f8d72-858">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-858">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="f8d72-859">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-859">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="f8d72-860">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-860">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="f8d72-861">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="f8d72-861">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="f8d72-862">数据保护</span><span class="sxs-lookup"><span data-stu-id="f8d72-862">Data protection</span></span>

<span data-ttu-id="f8d72-863">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-863">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="f8d72-864">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-864">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="f8d72-865">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="f8d72-865">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="f8d72-866">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-866">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="f8d72-867">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="f8d72-867">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="f8d72-868">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="f8d72-868">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="f8d72-869">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="f8d72-869">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="f8d72-870">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-870">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="f8d72-871">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f8d72-871">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="f8d72-872">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="f8d72-872">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="f8d72-873">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-873">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="f8d72-874">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="f8d72-874">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="f8d72-875">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-875">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="f8d72-876">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="f8d72-876">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="f8d72-877">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="f8d72-877">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="f8d72-878">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="f8d72-878">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="f8d72-879">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="f8d72-879">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="f8d72-880">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="f8d72-880">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="f8d72-881">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="f8d72-881">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="f8d72-882">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-882">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="f8d72-883">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-883">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="f8d72-884">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="f8d72-884">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="f8d72-885">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-885">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="f8d72-886">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-886">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="f8d72-887">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="f8d72-887">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="f8d72-888">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-888">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="f8d72-889">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-889">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="f8d72-890">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-890">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="f8d72-891">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-891">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="f8d72-892">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f8d72-892">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="f8d72-893">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8d72-893">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="f8d72-894">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-894">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="f8d72-895">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="f8d72-895">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="f8d72-896">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-896">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="f8d72-897">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="f8d72-897">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="f8d72-898">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-898">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="f8d72-899">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="f8d72-899">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="f8d72-900">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-900">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="f8d72-901">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-901">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="f8d72-902">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="f8d72-902">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="f8d72-903">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="f8d72-903">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="f8d72-904">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-904">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="f8d72-905">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="f8d72-905">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="f8d72-906">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-906">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="f8d72-907">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-907">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="f8d72-908">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="f8d72-908">Virtual Directories</span></span>

<span data-ttu-id="f8d72-909">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-909">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="f8d72-910">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-910">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="f8d72-911">子应用程序</span><span class="sxs-lookup"><span data-stu-id="f8d72-911">Sub-applications</span></span>

<span data-ttu-id="f8d72-912">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-912">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="f8d72-913">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-913">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="f8d72-914">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="f8d72-914">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="f8d72-915">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="f8d72-915">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="f8d72-916">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-916">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="f8d72-917">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-917">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="f8d72-918">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-918">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="f8d72-919">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="f8d72-919">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="f8d72-920">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="f8d72-920">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="f8d72-921">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="f8d72-921">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="f8d72-922">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-922">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="f8d72-923">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-923">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="f8d72-924">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-924">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="f8d72-925">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-925">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="f8d72-926">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="f8d72-926">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="f8d72-927">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-927">Select **OK**.</span></span>

<span data-ttu-id="f8d72-928">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-928">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="f8d72-929">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-929">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="f8d72-930">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="f8d72-930">Configuration of IIS with web.config</span></span>

<span data-ttu-id="f8d72-931">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="f8d72-931">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="f8d72-932">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="f8d72-932">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="f8d72-933">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="f8d72-933">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="f8d72-934">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="f8d72-934">For more information, see the following topics:</span></span>

* [<span data-ttu-id="f8d72-935">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="f8d72-935">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="f8d72-936">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-936">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="f8d72-937">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="f8d72-937">Configuration sections of web.config</span></span>

<span data-ttu-id="f8d72-938">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="f8d72-938">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="f8d72-939">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-939">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="f8d72-940">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-940">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="f8d72-941">应用程序池</span><span class="sxs-lookup"><span data-stu-id="f8d72-941">Application Pools</span></span>

<span data-ttu-id="f8d72-942">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="f8d72-942">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="f8d72-943">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-943">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="f8d72-944">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="f8d72-944">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="f8d72-945">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-945">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="f8d72-946">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-946">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="f8d72-947">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-947">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="f8d72-948">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="f8d72-948">Application Pool Identity</span></span>

<span data-ttu-id="f8d72-949">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="f8d72-949">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="f8d72-950">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="f8d72-950">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="f8d72-951">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="f8d72-951">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="f8d72-953">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-953">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="f8d72-954">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="f8d72-954">Resources can be secured using this identity.</span></span> <span data-ttu-id="f8d72-955">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="f8d72-955">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="f8d72-956">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="f8d72-956">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="f8d72-957">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="f8d72-957">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="f8d72-958">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-958">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="f8d72-959">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-959">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="f8d72-960">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="f8d72-960">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="f8d72-961">在“输入要选择的对象名称”区域中输入 **IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-961">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="f8d72-962">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="f8d72-962">Select the **Check Names** button.</span></span> <span data-ttu-id="f8d72-963">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-963">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="f8d72-964">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="f8d72-964">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="f8d72-965">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-965">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="f8d72-966">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="f8d72-966">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="f8d72-968">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-968">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="f8d72-970">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-970">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="f8d72-971">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-971">Provide additional permissions as needed.</span></span>

<span data-ttu-id="f8d72-972">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-972">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="f8d72-973">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="f8d72-973">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="f8d72-974">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-974">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="f8d72-975">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="f8d72-975">HTTP/2 support</span></span>

<span data-ttu-id="f8d72-976">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="f8d72-976">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="f8d72-977">进程内</span><span class="sxs-lookup"><span data-stu-id="f8d72-977">In-process</span></span>
  * <span data-ttu-id="f8d72-978">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-978">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="f8d72-979">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="f8d72-979">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="f8d72-980">进程外</span><span class="sxs-lookup"><span data-stu-id="f8d72-980">Out-of-process</span></span>
  * <span data-ttu-id="f8d72-981">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-981">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="f8d72-982">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f8d72-982">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="f8d72-983">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="f8d72-983">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f8d72-984">对于已建立 HTTP/2 连接时的进程内部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-984">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="f8d72-985">对于已建立 HTTP/2 连接时的进程外部署，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-985">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="f8d72-986">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-986">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="f8d72-987">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f8d72-987">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="f8d72-988">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f8d72-988">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="f8d72-989">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-989">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="f8d72-990">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="f8d72-990">CORS preflight requests</span></span>

<span data-ttu-id="f8d72-991">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-991">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="f8d72-992">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-992">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="f8d72-993">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-993">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="f8d72-994">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="f8d72-994">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="f8d72-995">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-995">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="f8d72-996">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](#in-process-hosting-model)或[进程外](#out-of-process-hosting-model)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="f8d72-996">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="f8d72-997">[空闲超时](#idle-timeout)：可以将托管在[进程内](#in-process-hosting-model)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-997">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="f8d72-998">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="f8d72-998">Application Initialization Module</span></span>

<span data-ttu-id="f8d72-999">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-999">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="f8d72-1000">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1000">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="f8d72-1001">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1001">The request triggers the app to start.</span></span> <span data-ttu-id="f8d72-1002">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1002">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="f8d72-1003">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1003">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="f8d72-1004">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1004">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="f8d72-1005">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1005">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="f8d72-1006">打开“Internet Information Services”>“万维网服务”>“应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1006">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="f8d72-1007">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1007">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="f8d72-1008">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1008">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="f8d72-1009">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1009">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="f8d72-1010">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1010">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="f8d72-1011">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1011">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="f8d72-1012">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1012">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="f8d72-1013">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1013">Using IIS Manager:</span></span>

  1. <span data-ttu-id="f8d72-1014">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1014">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="f8d72-1015">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1015">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="f8d72-1016">默认的“启动模式”为“按需” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1016">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="f8d72-1017">将“启动模式”设置为“始终运行”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1017">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="f8d72-1018">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1018">Select **OK**.</span></span>
  1. <span data-ttu-id="f8d72-1019">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1019">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="f8d72-1020">右键单击应用，并选择“管理网站”>“高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1020">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="f8d72-1021">默认的“预加载已启用”设置为“False” 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1021">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="f8d72-1022">将“预加载已启用”设置为“True”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1022">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="f8d72-1023">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1023">Select **OK**.</span></span>

* <span data-ttu-id="f8d72-1024">使用 web.config 将 `doAppInitAfterRestart` 已设置为 `true` 的 `<applicationInitialization>` 元素添加到应用的 web.config 文件中的 `<system.webServer>` 元素中 ：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1024">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="f8d72-1025">空闲超时</span><span class="sxs-lookup"><span data-stu-id="f8d72-1025">Idle Timeout</span></span>

<span data-ttu-id="f8d72-1026">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1026">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="f8d72-1027">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1027">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="f8d72-1028">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1028">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="f8d72-1029">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1029">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="f8d72-1030">默认的“空闲超时(分钟)”为“20”分钟 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1030">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="f8d72-1031">将“空闲超时(分钟)”设置为“0”（零） 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1031">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="f8d72-1032">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1032">Select **OK**.</span></span>
1. <span data-ttu-id="f8d72-1033">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1033">Recycle the worker process.</span></span>

<span data-ttu-id="f8d72-1034">若要防止托管在[进程外](#out-of-process-hosting-model)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1034">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="f8d72-1035">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1035">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="f8d72-1036">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1036">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="f8d72-1037">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-1037">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="f8d72-1038">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="f8d72-1038">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="f8d72-1039">[应用程序初始化 \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1039">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="f8d72-1040">[应用程序池 \<processModel> 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1040">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="f8d72-1041">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-1041">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="f8d72-1042">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="f8d72-1042">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="f8d72-1043">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="f8d72-1043">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="f8d72-1044">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-1044">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="f8d72-1045">其他资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-1045">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="f8d72-1046">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="f8d72-1046">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="f8d72-1047">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="f8d72-1047">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="f8d72-1048">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="f8d72-1048">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f8d72-1049">若要学习将 ASP.NET Core 应用发布到 IIS 服务器的教程，请参阅<xref:tutorials/publish-to-iis>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1049">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="f8d72-1050">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-1050">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="f8d72-1051">支持的操作系统</span><span class="sxs-lookup"><span data-stu-id="f8d72-1051">Supported operating systems</span></span>

<span data-ttu-id="f8d72-1052">支持下列操作系统：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1052">The following operating systems are supported:</span></span>

* <span data-ttu-id="f8d72-1053">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-1053">Windows 7 or later</span></span>
* <span data-ttu-id="f8d72-1054">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-1054">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f8d72-1055">[HTTP.sys 服务器](xref:fundamentals/servers/httpsys)（以前称为 WebListener）无法以反向代理配置形式与 IIS 结合使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1055">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="f8d72-1056">请使用 [Kestrel 服务器](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1056">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="f8d72-1057">有关在 Azure 上托管的信息，请参阅<xref:host-and-deploy/azure-apps/index>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1057">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="f8d72-1058">有关疑难解答指南，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1058">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="f8d72-1059">受支持的平台</span><span class="sxs-lookup"><span data-stu-id="f8d72-1059">Supported platforms</span></span>

<span data-ttu-id="f8d72-1060">支持针对 32 位 (x86) 或 64 位 (x64) 部署发布的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1060">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="f8d72-1061">使用 32 位 (x86) .NET Core SDK 部署 32 位应用，除非应用符合以下情况：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1061">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="f8d72-1062">需要适用于 64 位应用的更大虚拟内存地址空间。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1062">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="f8d72-1063">需要更大 IIS 堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1063">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="f8d72-1064">具有 64 位本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1064">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="f8d72-1065">使用 64 位 (x64) .NET Core SDK 发布 64 位应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1065">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="f8d72-1066">主机系统必须具有 64 位运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1066">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="f8d72-1067">ASP.NET Core 随附有 [Kestrel 服务器](xref:fundamentals/servers/kestrel)，后者是默认的跨平台 HTTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1067">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="f8d72-1068">使用 [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 时，应用在独立于 IIS 工作进程（进程外）和 [Kestrel 服务器](xref:fundamentals/servers/index#kestrel)的进程中运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1068">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="f8d72-1069">由于 ASP.NET Core 应用在独立于 IIS 工作进程的进程中运行，因此该模块会处理进程管理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1069">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="f8d72-1070">该模块在第一个请求到达时启动 ASP.NET Core 应用的进程，并在应用关闭或崩溃时重新启动该应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1070">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="f8d72-1071">这基本上与在 [Windows 进程激活服务 (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) 托管的进程内运行的应用中出现的行为相同。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1071">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f8d72-1072">下图说明了 IIS、ASP.NET Core 模块和进程外托管的应用之间的关系：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1072">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core 模块](index/_static/ancm-outofprocess.png)

<span data-ttu-id="f8d72-1074">请求从 Web 到达内核模式 HTTP.sys 驱动程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1074">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f8d72-1075">驱动程序将请求路由到网站的配置端口上的 IIS，通常为 80 (HTTP) 或 443 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1075">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f8d72-1076">该模块将该请求转发到应用的随机端口（非端口 80/443）上的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1076">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="f8d72-1077">该模块在启动时通过环境变量指定端口，[IIS 集成中间件](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)将服务器配置为侦听 `http://localhost:{port}`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1077">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="f8d72-1078">执行其他检查，拒绝不是来自该模块的请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1078">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f8d72-1079">该模块不支持 HTTPS 转发，因此即使请求由 IIS 通过 HTTPS 接收，它们还是通过 HTTP 转发。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1079">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f8d72-1080">Kestrel 从模块获取请求后，请求会被推送到 ASP.NET Core 中间件管道中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1080">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f8d72-1081">中间件管道处理该请求并将其作为 `HttpContext` 实例传递给应用的逻辑。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1081">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f8d72-1082">IIS 集成添加的中间件会将方案、远程 IP 和 pathbase 更新到帐户以将请求转发到 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1082">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f8d72-1083">应用的响应传递回 IIS，IIS 将响应推送回发起请求的 HTTP 客户端。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1083">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="f8d72-1084">`CreateDefaultBuilder` 将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器，并通过配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的基础路径和端口来启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1084">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="f8d72-1085">ASP.NET Core 模块生成分配给后端进程的动态端口。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1085">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="f8d72-1086">`CreateDefaultBuilder` 调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 方法。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1086">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> method.</span></span> <span data-ttu-id="f8d72-1087">`UseIISIntegration` 配置在 localhost IP 地址 (`127.0.0.1`) 中的动态端口上侦听的 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1087">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="f8d72-1088">如果动态端口为 1234，则 Kestrel 在 `127.0.0.1:1234` 中侦听。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1088">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="f8d72-1089">此配置将替换以下 API 提供的其他 URL 配置：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1089">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="f8d72-1090">Kestrel 的侦听 API</span><span class="sxs-lookup"><span data-stu-id="f8d72-1090">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="f8d72-1091">[配置](xref:fundamentals/configuration/index)（或 [命令行 -- URL 选项](xref:fundamentals/host/web-host#override-configuration)）</span><span class="sxs-lookup"><span data-stu-id="f8d72-1091">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="f8d72-1092">使用模块时，不需要调用 `UseUrls` 或 Kestrel 的 `Listen` API。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1092">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="f8d72-1093">如果调用 `UseUrls` 或 `Listen`，则 Kestrel 仅会侦听在没有 IIS 的情况下运行应用时指定的端口。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1093">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="f8d72-1094">有关 ASP.NET Core 模块配置指南，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1094">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="f8d72-1095">有关托管的详细信息，请参阅[在 ASP.NET Core 中托管](xref:fundamentals/index#host)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1095">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="f8d72-1096">应用程序配置</span><span class="sxs-lookup"><span data-stu-id="f8d72-1096">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="f8d72-1097">启用 IISIntegration 组件</span><span class="sxs-lookup"><span data-stu-id="f8d72-1097">Enable the IISIntegration components</span></span>

<span data-ttu-id="f8d72-1098">在 `CreateWebHostBuilder` 中生成主机 (*Program.cs*)，请调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 以启用 IIS 集成：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1098">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="f8d72-1099">有关 `CreateDefaultBuilder` 的详细信息，请参阅 <xref:fundamentals/host/web-host#set-up-a-host>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1099">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="f8d72-1100">IIS 选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-1100">IIS options</span></span>

| <span data-ttu-id="f8d72-1101">选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-1101">Option</span></span>                         | <span data-ttu-id="f8d72-1102">默认</span><span class="sxs-lookup"><span data-stu-id="f8d72-1102">Default</span></span> | <span data-ttu-id="f8d72-1103">设置</span><span class="sxs-lookup"><span data-stu-id="f8d72-1103">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="f8d72-1104">若为 `true`，IIS 服务器将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1104">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="f8d72-1105">若为 `false`，服务器仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1105">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="f8d72-1106">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1106">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="f8d72-1107">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1107">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="f8d72-1108">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1108">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="f8d72-1109">要配置 IIS 选项，请在 <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> 中包括 <xref:Microsoft.AspNetCore.Builder.IISOptions> 的服务配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1109">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="f8d72-1110">下面的示例阻止应用填充 `HttpContext.Connection.ClientCertificate`：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1110">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="f8d72-1111">选项</span><span class="sxs-lookup"><span data-stu-id="f8d72-1111">Option</span></span>                         | <span data-ttu-id="f8d72-1112">默认</span><span class="sxs-lookup"><span data-stu-id="f8d72-1112">Default</span></span> | <span data-ttu-id="f8d72-1113">设置</span><span class="sxs-lookup"><span data-stu-id="f8d72-1113">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="f8d72-1114">若为 `true`，[IIS 集成中间件](#enable-the-iisintegration-components)将设置经过 [Windows 身份验证](xref:security/authentication/windowsauth)进行身份验证的 `HttpContext.User`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1114">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="f8d72-1115">若为 `false`，中间件仅提供 `HttpContext.User` 的标识并在 `AuthenticationScheme` 显式请求时响应质询。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1115">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="f8d72-1116">必须在 IIS 中启用 Windows 身份验证使 `AutomaticAuthentication` 得以运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1116">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="f8d72-1117">有关详细信息，请参阅 [Windows 身份验证](xref:security/authentication/windowsauth)主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1117">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="f8d72-1118">设置在登录页上向用户显示的显示名。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1118">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="f8d72-1119">若为 `true`，且存在 `MS-ASPNETCORE-CLIENTCERT` 请求头，则填充 `HttpContext.Connection.ClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1119">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f8d72-1120">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="f8d72-1120">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f8d72-1121">配置转发头中间件的 [IIS 集成中间件](#enable-the-iisintegration-components)和 ASP.NET Core 模块将配置为转发方案 (HTTP/HTTPS) 和发出请求的远程 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1121">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="f8d72-1122">对于托管在其他代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1122">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="f8d72-1123">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1123">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="f8d72-1124">web.config 文件</span><span class="sxs-lookup"><span data-stu-id="f8d72-1124">web.config file</span></span>

<span data-ttu-id="f8d72-1125">web.config 文件配置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1125">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="f8d72-1126">发布项目时，MSBuild 目标 (`_TransformWebConfig`) 负责创建、转换和发布 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1126">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="f8d72-1127">此目标位于 Web SDK 目标 (`Microsoft.NET.Sdk.Web`) 中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1127">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="f8d72-1128">SDK 设置在项目文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1128">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="f8d72-1129">如果项目中不存在 web.config 文件，则会使用正确的 processPath 和参数创建该文件，以便配置 ASP.NET Core 模块，并将该文件移动到[已发布的输出](xref:host-and-deploy/directory-structure)  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1129">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="f8d72-1130">如果项目中存在 web.config 文件，则会使用正确的 processPath 和参数转换该文件，以便配置 ASP.NET Core 模块，并将该文件移动到已发布的输出。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1130">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="f8d72-1131">转换不会修改文件中的 IIS 配置设置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1131">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="f8d72-1132">web.config 文件可能会提供其他 IIS 配置设置，以控制活动的 IIS 模块。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1132">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="f8d72-1133">有关能够处理 ASP.NET Core 应用请求的 IIS 模块的信息，请参阅 [IIS 模块](xref:host-and-deploy/iis/modules)主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1133">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="f8d72-1134">要防止 Web SDK 转换 web.config 文件，请使用项目文件中的 \<IsTransformWebConfigDisabled> 属性：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1134">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="f8d72-1135">在禁用 Web SDK 转换文件时，开发人员应手动设置 processPath 和参数。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1135">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="f8d72-1136">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1136">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="f8d72-1137">web.config 文件位置</span><span class="sxs-lookup"><span data-stu-id="f8d72-1137">web.config file location</span></span>

<span data-ttu-id="f8d72-1138">为了正确设置 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)，web.config 文件必须位于已部署应用的[内容根](xref:fundamentals/index#content-root)路径（通常为应用基路径）中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1138">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="f8d72-1139">该位置与向 IIS 提供的网站物理路径相同。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1139">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="f8d72-1140">若要使用 Web 部署发布多个应用，应用的根路径中需要包含 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1140">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="f8d72-1141">敏感文件存在于应用的物理路径中，如 \<assembly>.runtimeconfig.json、\<assembly>.xml（XML 文档注释）和 \<assembly>.deps.json  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1141">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="f8d72-1142">如果存在 web.config 文件且站点正常启动，则请求获取这些敏感文件时，IIS 不会提供。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1142">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="f8d72-1143">如果缺少 web.config 文件、命名不正确，或无法配置站点以正常启动，IIS 可能会公开提供敏感文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1143">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="f8d72-1144">部署中必须始终存在 web.config 文件且正确命名，并可以配置站点以正常启动。**请勿从生产部署中删除 web.config 文件。**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1144">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="f8d72-1145">转换 web.config</span><span class="sxs-lookup"><span data-stu-id="f8d72-1145">Transform web.config</span></span>

<span data-ttu-id="f8d72-1146">如果需要在发布时转换 web.config（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1146">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="f8d72-1147">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="f8d72-1147">IIS configuration</span></span>

<span data-ttu-id="f8d72-1148">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1148">**Windows Server operating systems**</span></span>

<span data-ttu-id="f8d72-1149">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1149">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="f8d72-1150">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1150">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="f8d72-1151">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1151">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="f8d72-1153">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1153">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="f8d72-1154">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1154">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="f8d72-1156">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1156">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="f8d72-1157">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1157">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="f8d72-1158">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1158">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="f8d72-1159">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1159">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="f8d72-1160">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1160">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="f8d72-1161">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1161">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="f8d72-1162">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1162">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="f8d72-1163">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1163">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="f8d72-1164">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1164">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="f8d72-1165">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1165">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="f8d72-1166">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1166">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="f8d72-1167">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1167">**Windows desktop operating systems**</span></span>

<span data-ttu-id="f8d72-1168">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1168">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="f8d72-1169">导航到“控制面板”>“程序”>“程序和功能”>“打开或关闭 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1169">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="f8d72-1170">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1170">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="f8d72-1171">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1171">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="f8d72-1172">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1172">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="f8d72-1173">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1173">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="f8d72-1174">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1174">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="f8d72-1175">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1175">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="f8d72-1176">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1176">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="f8d72-1177">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1177">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="f8d72-1178">有关详细信息，请参阅 [Windows 身份验证 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1178">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="f8d72-1179">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1179">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="f8d72-1180">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1180">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="f8d72-1181">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1181">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="f8d72-1182">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1182">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="f8d72-1183">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1183">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="f8d72-1184">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1184">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="f8d72-1186">安装 .NET Core 托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-1186">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="f8d72-1187">在托管系统上安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1187">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="f8d72-1188">捆绑包可安装 .NET Core 运行时、.NET Core 库和 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1188">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="f8d72-1189">该模块允许 ASP.NET Core 应用在 IIS 后面运行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1189">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f8d72-1190">如果在 IIS 之前安装了托管捆绑包，则必须修复捆绑包安装。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1190">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="f8d72-1191">在安装 IIS 后再次运行托管捆绑包安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1191">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="f8d72-1192">如果在安装 64 位 (x64) 版本的 .NET Core 之后安装了 Hosting Bundle，则可能看上去缺少 SDK（[未检测到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1192">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="f8d72-1193">要解决此问题，请参阅 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1193">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="f8d72-1194">下载</span><span class="sxs-lookup"><span data-stu-id="f8d72-1194">Download</span></span>

1. <span data-ttu-id="f8d72-1195">导航到 [ .NET Core](https://dotnet.microsoft.com/download/dotnet-core) 页面。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1195">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="f8d72-1196">选择所需的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1196">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="f8d72-1197">在“运行应用 - 运行时”列中，查找所需的 .NET Core 运行时版本的那一行。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1197">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="f8d72-1198">使用“托管捆绑包”链接下载安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1198">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="f8d72-1199">某些安装程序包含已到达其生命周期结束 (EOL) 且不再受 Microsoft 支持的发行版本。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1199">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="f8d72-1200">有关详细信息，请参阅[支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1200">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="f8d72-1201">安装托管捆绑包</span><span class="sxs-lookup"><span data-stu-id="f8d72-1201">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="f8d72-1202">在服务器上运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1202">Run the installer on the server.</span></span> <span data-ttu-id="f8d72-1203">通过管理员命令行界面运行安装程序时，以下参数可用：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1203">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="f8d72-1204">`OPT_NO_ANCM=1`：跳过安装 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1204">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="f8d72-1205">`OPT_NO_RUNTIME=1`：跳过安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1205">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="f8d72-1206">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1206">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="f8d72-1207">`OPT_NO_SHAREDFX=1`：跳过安装 ASP.NET 共享框架（ASP.NET 运行时）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1207">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="f8d72-1208">当服务器仅承载[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时使用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1208">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="f8d72-1209">`OPT_NO_X86=1`：跳过安装 x86 运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1209">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="f8d72-1210">确定不会托管 32 位应用时，请使用此参数。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1210">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="f8d72-1211">如果有可能会同时托管 32 位和 64 位应用，请勿使用此参数，并安装两个运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1211">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="f8d72-1212">`OPT_NO_SHARED_CONFIG_CHECK=1`：当共享配置 (applicationHost.config) 与 IIS 安装位于同一台计算机上时，禁止对使用的是否是 IIS 共享配置进行检查。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1212">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="f8d72-1213">仅适用于 ASP.NET Core 2.2 或更高版本托管捆绑程序安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1213">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="f8d72-1214">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1214">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="f8d72-1215">重新启动系统，或在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1215">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="f8d72-1216">重启 IIS 会选取安装程序对系统 PATH（环境变量）所作的更改。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1216">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="f8d72-1217">安装托管捆绑包时，无需在 IIS 中手动停止各个站点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1217">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="f8d72-1218">IIS 重新启动后，托管应用（IIS 站点）也将重新启动。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1218">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="f8d72-1219">应用收到第一个请求（包括来自[应用程序初始化模块](#application-initialization-module-and-idle-timeout)）后，将再次启动。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1219">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="f8d72-1220">ASP.NET Core 采用共享框架包的修补程序版本的前滚行为。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1220">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="f8d72-1221">当 IIS 托管的应用重新启动 IIS 时，应用会在收到第一个请求后使用其引用的包的最新修补程序版本进行加载。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1221">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="f8d72-1222">如果未重新启动 IIS，应用会在其工作进程被回收并收到第一个请求后重新启动并显示前滚行为。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1222">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="f8d72-1223">有关 IIS 共享配置的信息，请参阅[使用 IIS 共享配置的 ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1223">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="f8d72-1224">使用 Visual Studio 进行发布时安装 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-1224">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="f8d72-1225">使用 [Web 部署](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)将应用部署到服务器时，请在服务器上安装最新版本的 Web 部署。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1225">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="f8d72-1226">要安装 Web 部署，请使用 [Web 平台安装程序 (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) 或直接从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=43717)获取安装程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1226">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="f8d72-1227">建议使用 WebPI。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1227">The preferred method is to use WebPI.</span></span> <span data-ttu-id="f8d72-1228">WebPI 为托管提供程序提供独立的安装程序和配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1228">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="f8d72-1229">创建 IIS 站点</span><span class="sxs-lookup"><span data-stu-id="f8d72-1229">Create the IIS site</span></span>

1. <span data-ttu-id="f8d72-1230">在托管系统上，创建一个文件夹以包含应用已发布的文件夹和文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1230">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="f8d72-1231">在接下来的步骤中，文件夹路径作为应用程序的物理路径提供给 IIS。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1231">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="f8d72-1232">有关应用程序部署文件夹和文件布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1232">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="f8d72-1233">在 IIS 管理器中，打开“连接”面板中的服务器节点。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1233">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="f8d72-1234">右键单击“站点”文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1234">Right-click the **Sites** folder.</span></span> <span data-ttu-id="f8d72-1235">选择上下文菜单中的“添加网站”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1235">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="f8d72-1236">提供网站名称，并将物理路径设置为应用的部署文件夹 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1236">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="f8d72-1237">提供“绑定”配置，并通过选择“确定”创建网站：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1237">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![在“添加网站”步骤中提供网站名称、物理路径和主机名。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="f8d72-1239">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1239">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="f8d72-1240">顶级通配符绑定可能会为应用带来安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1240">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="f8d72-1241">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1241">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="f8d72-1242">使用显式主机名而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1242">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="f8d72-1243">如果可控制整个父域（区别于易受攻击的 `*.com`），则子域通配符绑定（例如，`*.mysub.com`）不具有此安全风险。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1243">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="f8d72-1244">有关详细信息，请参阅 [rfc7230 第 5.4 条](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1244">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="f8d72-1245">在服务器节点下，选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1245">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="f8d72-1246">右键单击站点的应用池，然后从上下文菜单中选择“基本设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1246">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="f8d72-1247">在“编辑应用程序池”窗口中，将“.NET CLR 版本”设置为“无托管代码”：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1247">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![将“.NET CLR 版本”设置为“无托管代码”。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="f8d72-1249">ASP.NET Core 在单独的进程中运行，并管理运行时。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1249">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="f8d72-1250">ASP.NET Core 不依赖桌面 CLR (.NET CLR) 加载：将启动 .NET Core 的 Core 公共语言运行时 (CoreCLR) ，在工作进程中托管应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1250">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="f8d72-1251">将“.NET CLR 版本”设置为“无托管代码”是可选步骤，但建议采用此设置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1251">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="f8d72-1252">*ASP.NET Core 2.2 或更高版本*：对于使用 [进程内托管模型](#in-process-hosting-model)的 64 位 (x64) [独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)，为 32 位 (x86) 进程禁用应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1252">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="f8d72-1253">在 IIS 管理器 >“应用程序池”的“操作”侧栏中，选择“设置应用程序池默认设置”或“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1253">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="f8d72-1254">找到“启用 32 位应用程序”并将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1254">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="f8d72-1255">此设置不会影响针对[进程外托管](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)部署的应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1255">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="f8d72-1256">确认进程模型标识拥有适当的权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1256">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="f8d72-1257">如果将应用池的默认标识（“进程模型” > “Identity”）从 ApplicationPoolIdentity 更改为另一标识，请确保新标识拥有对应用文件夹、数据库和其他所需资源的必需访问权限  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1257">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="f8d72-1258">例如，应用池需要对文件夹的读取和写入权限，以便应用在其中读取和写入文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1258">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="f8d72-1259">**Windows 身份验证配置（可选）**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1259">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="f8d72-1260">有关详细信息，请参阅[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1260">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="f8d72-1261">部署应用</span><span class="sxs-lookup"><span data-stu-id="f8d72-1261">Deploy the app</span></span>

<span data-ttu-id="f8d72-1262">将应用程序部署到 IIS 物理路径文件夹中，该文件夹是在[创建 IIS 站点](#create-the-iis-site)部分中创建的。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1262">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="f8d72-1263">[Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)是建议使用的部署机制，但有几个选项可以将应用程序从项目的发布文件夹移动到托管系统的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1263">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="f8d72-1264">在 Visual Studio 内使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-1264">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="f8d72-1265">要了解如何创建用于 Web 部署的发布配置文件，请参阅[用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1265">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="f8d72-1266">如果托管提供程序提供了发布配置文件或支持创建发布配置文件，请下载配置文件并使用 Visual Studio 的“发布”对话框将其导入：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1266">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![“发布”对话框页](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="f8d72-1268">在 Visual Studio 之外使用 Web 部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-1268">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="f8d72-1269">也可以在 Visual Studio 之外从命令行使用 [Web 部署](/iis/publish/using-web-deploy/introduction-to-web-deploy)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1269">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="f8d72-1270">有关详细信息，请参阅 [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool)（Web 部署工具）。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1270">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="f8d72-1271">Web 部署的替代方法</span><span class="sxs-lookup"><span data-stu-id="f8d72-1271">Alternatives to Web Deploy</span></span>

<span data-ttu-id="f8d72-1272">有多种方法可将应用移动到托管系统，例如手动复制、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy) 或 [PowerShell](/powershell/)，可使用其中任何一种方法。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1272">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="f8d72-1273">有关将 ASP.NET Core 部署到 IIS 的详细信息，请参阅[面向 IIS 管理员的部署资源](#deployment-resources-for-iis-administrators)部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1273">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="f8d72-1274">浏览网站</span><span class="sxs-lookup"><span data-stu-id="f8d72-1274">Browse the website</span></span>

<span data-ttu-id="f8d72-1275">将应用部署到托管系统后，向应用的一个公共终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1275">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="f8d72-1276">在以下示例中，站点被绑定到端口 `80` 上 `www.mysite.com` 的 IIS 主机名中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1276">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="f8d72-1277">向 `http://www.mysite.com` 发出请求：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1277">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge 浏览器已加载 IIS 启动页。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="f8d72-1279">锁定的部署文件</span><span class="sxs-lookup"><span data-stu-id="f8d72-1279">Locked deployment files</span></span>

<span data-ttu-id="f8d72-1280">如果应用正在运行，部署文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1280">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="f8d72-1281">在部署期间，无法覆盖已锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1281">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="f8d72-1282">若要在部署中解除已锁定的文件，请使用以下方法之一停止应用池：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1282">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="f8d72-1283">使用 Web 部署并在项目文件中引用 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1283">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="f8d72-1284">系统会在 Web 应用目录的根目录中放置一个 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1284">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="f8d72-1285">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1285">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="f8d72-1286">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1286">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="f8d72-1287">在服务器上的 IIS 管理器中手动停止应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1287">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="f8d72-1288">使用 PowerShell 删除 app_offline.html（需要使用 PowerShell 5 或更高版本）：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1288">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="f8d72-1289">数据保护</span><span class="sxs-lookup"><span data-stu-id="f8d72-1289">Data protection</span></span>

<span data-ttu-id="f8d72-1290">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1290">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="f8d72-1291">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1291">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="f8d72-1292">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1292">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="f8d72-1293">如果密钥环存储于内存中，则在应用重启时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1293">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="f8d72-1294">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1294">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="f8d72-1295">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1295">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="f8d72-1296">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1296">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="f8d72-1297">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1297">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="f8d72-1298">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1298">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="f8d72-1299">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1299">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="f8d72-1300">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1300">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="f8d72-1301">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1301">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="f8d72-1302">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1302">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="f8d72-1303">此脚本在 HKLM 注册表中创建注册表项，仅应用程序的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1303">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="f8d72-1304">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1304">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="f8d72-1305">在 web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1305">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="f8d72-1306">默认情况下，数据保护密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1306">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="f8d72-1307">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1307">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="f8d72-1308">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1308">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="f8d72-1309">考虑允许用户上传证书的机制：将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1309">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="f8d72-1310">有关详细信息，请参阅[配置 ASP.NET Core 数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1310">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="f8d72-1311">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1311">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="f8d72-1312">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1312">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="f8d72-1313">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1313">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="f8d72-1314">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1314">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="f8d72-1315">密钥保存在 %LOCALAPPDATA%/ASP.NET/DataProtection-Keys 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1315">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="f8d72-1316">同时还必须启用应用池的 [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1316">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="f8d72-1317">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1317">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="f8d72-1318">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1318">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="f8d72-1319">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1319">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="f8d72-1320">导航到 %windir%/system32/inetsrv/config 文件夹。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1320">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="f8d72-1321">打开 applicationHost.config 文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1321">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="f8d72-1322">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1322">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="f8d72-1323">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1323">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="f8d72-1324">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1324">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="f8d72-1325">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1325">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="f8d72-1326">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1326">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="f8d72-1327">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1327">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="f8d72-1328">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1328">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="f8d72-1329">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1329">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="f8d72-1330">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1330">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="f8d72-1331">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1331">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="f8d72-1332">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="f8d72-1332">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="f8d72-1333">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1333">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="f8d72-1334">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1334">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="f8d72-1335">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="f8d72-1335">Virtual Directories</span></span>

<span data-ttu-id="f8d72-1336">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1336">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="f8d72-1337">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1337">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="f8d72-1338">子应用程序</span><span class="sxs-lookup"><span data-stu-id="f8d72-1338">Sub-applications</span></span>

<span data-ttu-id="f8d72-1339">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1339">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="f8d72-1340">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1340">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="f8d72-1341">子应用不应将 ASP.NET Core 模块作为处理程序包含在其中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1341">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="f8d72-1342">如果在子应用的 web.config 文件中将该模块添加为处理程序，则在尝试浏览子应用时会收到“500.19 内部服务器错误”，即引用错误的配置文件。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1342">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="f8d72-1343">以下示例显示 ASP.NET Core 子应用的已发布 web.config 文件：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1343">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="f8d72-1344">在 ASP.NET Core 应用下托管非 ASP.NET Core 子应用时，需在子应用的 web.config 文件中显式删除继承的处理程序：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1344">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="f8d72-1345">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1345">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="f8d72-1346">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1346">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="f8d72-1347">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1347">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="f8d72-1348">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1348">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="f8d72-1349">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1349">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="f8d72-1350">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1350">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="f8d72-1351">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1351">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="f8d72-1352">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1352">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="f8d72-1353">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1353">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="f8d72-1354">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1354">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="f8d72-1355">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1355">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="f8d72-1356">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1356">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="f8d72-1357">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="f8d72-1357">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="f8d72-1358">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1358">Select **OK**.</span></span>

<span data-ttu-id="f8d72-1359">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1359">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="f8d72-1360">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1360">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="f8d72-1361">使用 web.config 配置 IIS</span><span class="sxs-lookup"><span data-stu-id="f8d72-1361">Configuration of IIS with web.config</span></span>

<span data-ttu-id="f8d72-1362">IIS 配置受用于 IIS 方案（适用于包含 ASP.NET Core 模块的 ASP.NET Core 应用）的 web.config 的 `<system.webServer>` 部分影响。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1362">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="f8d72-1363">例如，IIS 配置适用于动态压缩。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1363">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="f8d72-1364">如果在服务器一级将 IIS 配置为使用动态压缩，可通过应用的 web.config 文件中的 `<urlCompression>` 元素，对 ASP.NET Core 应用禁用它。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1364">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="f8d72-1365">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1365">For more information, see the following topics:</span></span>

* [<span data-ttu-id="f8d72-1366">\<system.webServer> 的配置参考</span><span class="sxs-lookup"><span data-stu-id="f8d72-1366">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="f8d72-1367">要为在独立应用池中运行的各应用设置环境变量（IIS 10.0 或更高版本中支持此操作），请参阅 IIS 参考文档中[环境变量 \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) 主题下的“AppCmd.exe 命令”部分。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1367">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="f8d72-1368">web.config 的配置节</span><span class="sxs-lookup"><span data-stu-id="f8d72-1368">Configuration sections of web.config</span></span>

<span data-ttu-id="f8d72-1369">ASP.NET Core 应用不会使用 web.config 中的 ASP.NET 4.x 应用的配置部分进行配置：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1369">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="f8d72-1370">会使用其他的配置提供程序配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1370">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="f8d72-1371">有关详细信息，请参阅[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1371">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="f8d72-1372">应用程序池</span><span class="sxs-lookup"><span data-stu-id="f8d72-1372">Application Pools</span></span>

<span data-ttu-id="f8d72-1373">在服务器上托管多个网站时，建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1373">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="f8d72-1374">IIS“添加网站”对话框默认执行此配置。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1374">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="f8d72-1375">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1375">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="f8d72-1376">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1376">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="f8d72-1377">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="f8d72-1377">Application Pool Identity</span></span>

<span data-ttu-id="f8d72-1378">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1378">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="f8d72-1379">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1379">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="f8d72-1380">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 ApplicationPoolIdentity  ：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1380">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="f8d72-1382">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1382">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="f8d72-1383">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1383">Resources can be secured using this identity.</span></span> <span data-ttu-id="f8d72-1384">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1384">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="f8d72-1385">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1385">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="f8d72-1386">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1386">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="f8d72-1387">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1387">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="f8d72-1388">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1388">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="f8d72-1389">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1389">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="f8d72-1390">在“输入要选择的对象名称”区域中输入 **IIS AppPool\\<app_pool_name>** 。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1390">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="f8d72-1391">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1391">Select the **Check Names** button.</span></span> <span data-ttu-id="f8d72-1392">有关 DefaultAppPool，请检查使用 IIS AppPool\DefaultAppPool 的名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1392">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="f8d72-1393">当选择“检查名称”按钮时，对象名称区域中会显示 DefaultAppPool 的值。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1393">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="f8d72-1394">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1394">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="f8d72-1395">检查对象名称时，请使用 **IIS AppPool\\<app_pool_name>** 格式。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1395">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="f8d72-1397">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1397">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="f8d72-1399">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1399">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="f8d72-1400">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1400">Provide additional permissions as needed.</span></span>

<span data-ttu-id="f8d72-1401">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1401">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="f8d72-1402">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1402">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="f8d72-1403">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1403">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="f8d72-1404">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="f8d72-1404">HTTP/2 support</span></span>

<span data-ttu-id="f8d72-1405">满足以下基本要求的进程外部署支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="f8d72-1405">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="f8d72-1406">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="f8d72-1406">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="f8d72-1407">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1407">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="f8d72-1408">目标框架：不适用于进程外部署，因为 HTTP/2 连接完全由 IIS 处理。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1408">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="f8d72-1409">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="f8d72-1409">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f8d72-1410">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1410">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="f8d72-1411">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1411">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="f8d72-1412">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1412">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="f8d72-1413">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1413">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="f8d72-1414">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="f8d72-1414">CORS preflight requests</span></span>

<span data-ttu-id="f8d72-1415">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1415">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="f8d72-1416">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1416">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="f8d72-1417">若要了解如何在 web.config 中配置应用程序的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="f8d72-1417">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="f8d72-1418">面向 IIS 管理员的部署资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-1418">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="f8d72-1419">IIS 文档</span><span class="sxs-lookup"><span data-stu-id="f8d72-1419">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="f8d72-1420">IIS 中 IIS 管理器入门</span><span class="sxs-lookup"><span data-stu-id="f8d72-1420">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="f8d72-1421">.NET Core 应用程序部署</span><span class="sxs-lookup"><span data-stu-id="f8d72-1421">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="f8d72-1422">其他资源</span><span class="sxs-lookup"><span data-stu-id="f8d72-1422">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="f8d72-1423">Microsoft IIS 官方网站</span><span class="sxs-lookup"><span data-stu-id="f8d72-1423">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="f8d72-1424">Windows Server 技术内容库</span><span class="sxs-lookup"><span data-stu-id="f8d72-1424">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="f8d72-1425">IIS 上的 HTTP/2</span><span class="sxs-lookup"><span data-stu-id="f8d72-1425">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
