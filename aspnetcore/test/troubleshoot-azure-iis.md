---
title: 对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除
author: rick-anderson
description: 了解如何诊断Azure 应用服务和 Internet Information Services (IIS) 的 ASP.NET Core 应用部署问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 3f6b74ee9621529943d9f685d803837e7df10cfc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589564"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="795f5-103">对 Azure 应用服务和 IIS 上的 ASP.NET Core 进行故障排除</span><span class="sxs-lookup"><span data-stu-id="795f5-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="795f5-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="795f5-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="795f5-105">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="795f5-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="795f5-106">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="795f5-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="795f5-107">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="795f5-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="795f5-108">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="795f5-109">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="795f5-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="795f5-110">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="795f5-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="795f5-111">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="795f5-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="795f5-112">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="795f5-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="795f5-113">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="795f5-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="795f5-114">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="795f5-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="795f5-115">其他资源</span><span class="sxs-lookup"><span data-stu-id="795f5-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="795f5-116">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="795f5-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="795f5-117">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="795f5-117">App startup errors</span></span>

<span data-ttu-id="795f5-118">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="795f5-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="795f5-119">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行诊断 。</span><span class="sxs-lookup"><span data-stu-id="795f5-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="795f5-120">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="795f5-120">403.14 Forbidden</span></span>

<span data-ttu-id="795f5-121">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-121">The app fails to start.</span></span> <span data-ttu-id="795f5-122">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="795f5-123">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="795f5-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="795f5-124">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="795f5-125">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="795f5-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="795f5-126">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确 。</span><span class="sxs-lookup"><span data-stu-id="795f5-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="795f5-127">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="795f5-127">Perform the following steps:</span></span>

1. <span data-ttu-id="795f5-128">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="795f5-129">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统：</span><span class="sxs-lookup"><span data-stu-id="795f5-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="795f5-130">确认部署中存在 web.config 文件，并且其内容正确。</span><span class="sxs-lookup"><span data-stu-id="795f5-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="795f5-131">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="795f5-132">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径  。</span><span class="sxs-lookup"><span data-stu-id="795f5-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="795f5-133">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="795f5-134">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="795f5-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="795f5-135">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="795f5-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="795f5-136">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="795f5-136">500 Internal Server Error</span></span>

<span data-ttu-id="795f5-137">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="795f5-138">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="795f5-139">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="795f5-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="795f5-140">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="795f5-141">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="795f5-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="795f5-142">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="795f5-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="795f5-143">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="795f5-144">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="795f5-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="795f5-145">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-145">The worker process fails.</span></span> <span data-ttu-id="795f5-146">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-146">The app doesn't start.</span></span>

<span data-ttu-id="795f5-147">加载 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)组件时出现未知错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="795f5-148">请执行以下一项操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-148">Take one of the following actions:</span></span>

* <span data-ttu-id="795f5-149">联系 [Microsoft 支持部门](https://support.microsoft.com/oas/default.aspx?prid=15832)（依次选择“开发人员工具”和“ASP.NET Core”） 。</span><span class="sxs-lookup"><span data-stu-id="795f5-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="795f5-150">在 Stack Overflow 上提出问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="795f5-151">在 [GitHub 存储库](https://github.com/dotnet/AspNetCore)中提出问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="795f5-152">500.30 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="795f5-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="795f5-153">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-153">The worker process fails.</span></span> <span data-ttu-id="795f5-154">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-154">The app doesn't start.</span></span>

<span data-ttu-id="795f5-155">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试进程内启动 .NET Core CLR，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="795f5-156">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="795f5-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="795f5-157">常见失败情况：</span><span class="sxs-lookup"><span data-stu-id="795f5-157">Common failure conditions:</span></span>

* <span data-ttu-id="795f5-158">由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="795f5-159">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="795f5-160">使用 Azure Key Vault 时，缺少对 Key Vault 的权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="795f5-161">检查目标 Key Vault 中的访问策略，确保授予了正确的权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="795f5-162">500.31 ANCM 找不到本机依赖项</span><span class="sxs-lookup"><span data-stu-id="795f5-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="795f5-163">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-163">The worker process fails.</span></span> <span data-ttu-id="795f5-164">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-164">The app doesn't start.</span></span>

<span data-ttu-id="795f5-165">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试进程内启动 .NET Core 运行时，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="795f5-166">此类启动失败的最常见原因是未安装 `Microsoft.NETCore.App` 或 `Microsoft.AspNetCore.App`运行时。</span><span class="sxs-lookup"><span data-stu-id="795f5-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="795f5-167">如果将应用部署为面向 ASP.NET Core 3.0，并且计算机上不存在该版本，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="795f5-168">示例错误消息如下所示：</span><span class="sxs-lookup"><span data-stu-id="795f5-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="795f5-169">错误消息列出了所有已安装的 .NET Core 版本以及应用请求的版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="795f5-170">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-170">To fix this error, either:</span></span>

* <span data-ttu-id="795f5-171">在计算机上安装适当版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="795f5-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="795f5-172">更改应用，使其面向计算机上已存在的 .NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="795f5-173">将应用作为[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)进行发布。</span><span class="sxs-lookup"><span data-stu-id="795f5-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="795f5-174">在开发过程（`ASPNETCORE_ENVIRONMENT` 环境变量设置为 `Development`）中运行时，HTTP 响应中会写入特定的错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="795f5-175">进程启动失败的原因也可在“应用程序事件日志”中找到。</span><span class="sxs-lookup"><span data-stu-id="795f5-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="795f5-176">500.32 ANCM 无法加载 dll</span><span class="sxs-lookup"><span data-stu-id="795f5-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="795f5-177">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-177">The worker process fails.</span></span> <span data-ttu-id="795f5-178">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-178">The app doesn't start.</span></span>

<span data-ttu-id="795f5-179">此错误的最常见原因是针对不兼容的处理器体系结构发布了应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="795f5-180">如果工作进程作为 32 位应用运行，而将应用发布为面向 64 位，则会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="795f5-181">请通过以下一种方法修复此错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-181">To fix this error, either:</span></span>

* <span data-ttu-id="795f5-182">针对同一处理器体系结构将应用作为工作进程进行重新发布。</span><span class="sxs-lookup"><span data-stu-id="795f5-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="795f5-183">将应用作为[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-executables-fde)进行发布。</span><span class="sxs-lookup"><span data-stu-id="795f5-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="795f5-184">500.33 ANCM 请求处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="795f5-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="795f5-185">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-185">The worker process fails.</span></span> <span data-ttu-id="795f5-186">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-186">The app doesn't start.</span></span>

<span data-ttu-id="795f5-187">应用未引用 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="795f5-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="795f5-188">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)只能托管面向 `Microsoft.AspNetCore.App` 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="795f5-189">要修复此错误，请确保应用面向 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="795f5-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="795f5-190">检查 `.runtimeconfig.json` 以验证该应用所面向的框架。</span><span class="sxs-lookup"><span data-stu-id="795f5-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="795f5-191">500.34 ANCM 混合托管模型不受支持</span><span class="sxs-lookup"><span data-stu-id="795f5-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="795f5-192">工作进程不能在同一进程中同时运行进程内应用和进程外应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="795f5-193">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="795f5-194">500.35 ANCM 同一进程内有多个进程内应用程序</span><span class="sxs-lookup"><span data-stu-id="795f5-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="795f5-195">工作进程不能在同一进程中运行多个进程内应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="795f5-196">要修复此错误，请在单独的 IIS 应用程序池中运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="795f5-197">500.36 ANCM 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="795f5-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="795f5-198">进程外请求处理程序 aspnetcorev2_outofprocess.dll 未与 aspnetcorev2.dll 文件相邻 。</span><span class="sxs-lookup"><span data-stu-id="795f5-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="795f5-199">这表示 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的安装已损坏。</span><span class="sxs-lookup"><span data-stu-id="795f5-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="795f5-200">要修复此错误，请修复 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)（对于 IIS）或 Visual Studio（对于 IIS Express）的安装。</span><span class="sxs-lookup"><span data-stu-id="795f5-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="795f5-201">500.37 ANCM 无法在启动时间限制内启动</span><span class="sxs-lookup"><span data-stu-id="795f5-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="795f5-202">ANCM 无法在提供的启动时间限制内启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-202">ANCM failed to start within the provided startup time limit.</span></span> <span data-ttu-id="795f5-203">默认情况下，超时时间为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="795f5-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="795f5-204">在同一台计算机上启动大量应用时，则可能发生此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="795f5-205">在启动期间检查服务器上的 CPU/内存使用峰值。</span><span class="sxs-lookup"><span data-stu-id="795f5-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="795f5-206">可能需要交错执行多个应用程序的启动进程。</span><span class="sxs-lookup"><span data-stu-id="795f5-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="795f5-207">500.38 ANCM 找不到应用程序 DLL</span><span class="sxs-lookup"><span data-stu-id="795f5-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="795f5-208">ANCM 找不到应用程序 ANCM，该内容应显示在可执行文件的旁边。</span><span class="sxs-lookup"><span data-stu-id="795f5-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="795f5-209">在使用进程内托管模型托管打包为[单文件可执行程序](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables)的应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="795f5-210">该进程内模型要求 ANCM 将 .NET Core 应用加载到现有 IIS 进程中。</span><span class="sxs-lookup"><span data-stu-id="795f5-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="795f5-211">单文件部署模型不支持此方案。</span><span class="sxs-lookup"><span data-stu-id="795f5-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="795f5-212">请在应用的项目文件中使用下述方法之一来修复此错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="795f5-213">通过将 `PublishSingleFile` MSBuild 属性设置为 `false` 来禁用单文件发布。</span><span class="sxs-lookup"><span data-stu-id="795f5-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="795f5-214">通过将 `AspNetCoreHostingModel` MSBuild 属性设置为 `OutOfProcess` 来切换到进程外托管模型。</span><span class="sxs-lookup"><span data-stu-id="795f5-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="795f5-215">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="795f5-215">502.5 Process Failure</span></span>

<span data-ttu-id="795f5-216">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-216">The worker process fails.</span></span> <span data-ttu-id="795f5-217">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-217">The app doesn't start.</span></span>

<span data-ttu-id="795f5-218">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="795f5-219">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="795f5-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="795f5-220">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="795f5-221">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="795f5-222">共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll 文件）。</span><span class="sxs-lookup"><span data-stu-id="795f5-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="795f5-223">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="795f5-224">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="795f5-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="795f5-225">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”错误页面：</span><span class="sxs-lookup"><span data-stu-id="795f5-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="795f5-226">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="795f5-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="795f5-227">应用未能启动，因为应用的程序集 (.dll) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="795f5-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="795f5-228">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="795f5-229">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="795f5-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="795f5-230">在 IIS 管理器的“应用程序池”中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="795f5-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="795f5-231">在“操作”面板中的“编辑应用程序池”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="795f5-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="795f5-232">设置“启用 32 位应用程序”：</span><span class="sxs-lookup"><span data-stu-id="795f5-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="795f5-233">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="795f5-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="795f5-234">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="795f5-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="795f5-235">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="795f5-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="795f5-236">连接重置</span><span class="sxs-lookup"><span data-stu-id="795f5-236">Connection reset</span></span>

<span data-ttu-id="795f5-237">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="795f5-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="795f5-238">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="795f5-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="795f5-239">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="795f5-240">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="795f5-241">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="795f5-241">Default startup limits</span></span>

<span data-ttu-id="795f5-242">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="795f5-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="795f5-243">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="795f5-244">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="795f5-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="795f5-245">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="795f5-246">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="795f5-247">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="795f5-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="795f5-248">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="795f5-249">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="795f5-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="795f5-250">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="795f5-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="795f5-251">在“支持工具”下，选择“应用程序事件”按钮 。</span><span class="sxs-lookup"><span data-stu-id="795f5-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="795f5-252">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误 。</span><span class="sxs-lookup"><span data-stu-id="795f5-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="795f5-253">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="795f5-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="795f5-254">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-255">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-256">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-257">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-258">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="795f5-259">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="795f5-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="795f5-260">检查日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-260">Examine the log.</span></span> <span data-ttu-id="795f5-261">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="795f5-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="795f5-262">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="795f5-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="795f5-263">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="795f5-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="795f5-264">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="795f5-265">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-266">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-267">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-268">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="795f5-269">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="795f5-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="795f5-270">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="795f5-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="795f5-271">运行应用：</span><span class="sxs-lookup"><span data-stu-id="795f5-271">Run the app:</span></span>
   * <span data-ttu-id="795f5-272">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="795f5-273">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="795f5-274">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="795f5-275">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="795f5-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="795f5-276">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="795f5-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="795f5-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="795f5-278">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="795f5-279">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="795f5-280">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="795f5-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="795f5-281">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="795f5-281">**Current release**</span></span>

* <span data-ttu-id="795f5-282">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="795f5-283">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="795f5-284">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="795f5-285">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="795f5-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="795f5-286">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="795f5-287">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="795f5-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="795f5-288">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="795f5-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="795f5-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="795f5-290">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="795f5-291">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="795f5-292">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-293">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-294">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="795f5-295">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="795f5-296">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-297">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="795f5-298">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="795f5-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="795f5-299">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="795f5-300">在 Azure 门户中，导航到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="795f5-301">在“应用服务”边栏选项卡中，在搜索框中输入 kudu 。</span><span class="sxs-lookup"><span data-stu-id="795f5-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="795f5-302">依次选择“高级工具”>“Go” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-302">Select **Advanced Tools** > **Go**.</span></span>
1. <span data-ttu-id="795f5-303">选择“调试控制台”>“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-303">Select  **Debug console > CMD**.</span></span>
1. <span data-ttu-id="795f5-304">导航到 site/wwwroot</span><span class="sxs-lookup"><span data-stu-id="795f5-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="795f5-305">选择铅笔图标以编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-306">在 `<aspNetCore />` 元素中，设置 `stdoutLogEnabled="true"` 并选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="795f5-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save**.</span></span>

<span data-ttu-id="795f5-307">故障排除完成后，通过设置 `stdoutLogEnabled="false"` 来禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="795f5-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="795f5-308">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="795f5-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

<a name="enhanced-diagnostic-logs"></a>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="795f5-309">ASP.NET Core 模块调试日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="795f5-310">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="795f5-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="795f5-311">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="795f5-312">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="795f5-313">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="795f5-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="795f5-314">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-314">Redeploy the app.</span></span>
   * <span data-ttu-id="795f5-315">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中：</span><span class="sxs-lookup"><span data-stu-id="795f5-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="795f5-316">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-317">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-318">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="795f5-319">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="795f5-320">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="795f5-321">通过选择铅笔按钮编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="795f5-322">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="795f5-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="795f5-323">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="795f5-324">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-325">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-326">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-327">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-328">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="795f5-329">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="795f5-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="795f5-330">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="795f5-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="795f5-331">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="795f5-332">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="795f5-333">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="795f5-334">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="795f5-335">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分。</span><span class="sxs-lookup"><span data-stu-id="795f5-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="795f5-336">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-336">Save the file.</span></span>

<span data-ttu-id="795f5-337">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="795f5-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-338">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-339">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-339">There's no limit on log file size.</span></span> <span data-ttu-id="795f5-340">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="795f5-341">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-342">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="795f5-343">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="795f5-344">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="795f5-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="795f5-345">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="795f5-346">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="795f5-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="795f5-347">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="795f5-347">Monitoring blades</span></span>

<span data-ttu-id="795f5-348">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="795f5-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="795f5-349">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="795f5-350">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="795f5-351">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="795f5-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="795f5-352">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="795f5-353">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="795f5-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="795f5-354">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="795f5-355">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="795f5-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="795f5-356">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="795f5-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="795f5-357">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="795f5-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="795f5-358">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="795f5-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="795f5-359">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="795f5-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="795f5-360">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="795f5-361">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-362">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-363">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-364">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件 。</span><span class="sxs-lookup"><span data-stu-id="795f5-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="795f5-365">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="795f5-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-366">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="795f5-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="795f5-367">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="795f5-368">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="795f5-369">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="795f5-370">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="795f5-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="795f5-371">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="795f5-372">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="795f5-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="795f5-373">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="795f5-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="795f5-374">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-374">Make a request to the app.</span></span>
1. <span data-ttu-id="795f5-375">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="795f5-376">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="795f5-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="795f5-377">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="795f5-378">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="795f5-379">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="795f5-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="795f5-380">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="795f5-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="795f5-381">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="795f5-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-382">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-383">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="795f5-384">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-385">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="795f5-386">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="795f5-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="795f5-387">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="795f5-388">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="795f5-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="795f5-389">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-389">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="795f5-390">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="795f5-390">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="795f5-391">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="795f5-392">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="795f5-393">错误具有“源”列中“IIS AspNetCore 模块”或“IIS Express AspNetCore 模块”的值。</span><span class="sxs-lookup"><span data-stu-id="795f5-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="795f5-394">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="795f5-394">Run the app at a command prompt</span></span>

<span data-ttu-id="795f5-395">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="795f5-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="795f5-396">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="795f5-397">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="795f5-397">Framework-dependent deployment</span></span>

<span data-ttu-id="795f5-398">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="795f5-399">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe 执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="795f5-400">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="795f5-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="795f5-401">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="795f5-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="795f5-402">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="795f5-403">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="795f5-404">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="795f5-405">独立部署</span><span class="sxs-lookup"><span data-stu-id="795f5-405">Self-contained deployment</span></span>

<span data-ttu-id="795f5-406">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="795f5-407">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="795f5-408">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="795f5-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="795f5-409">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="795f5-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="795f5-410">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="795f5-411">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="795f5-412">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="795f5-413">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="795f5-414">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="795f5-415">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="795f5-416">如果 logs 文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="795f5-417">有关如何启用 MSBuild 以在部署中自动创建 logs 文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="795f5-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="795f5-418">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-418">Edit the *web.config* file.</span></span> <span data-ttu-id="795f5-419">将“stdoutLogEnabled”设置为 `true` 并更改“stdoutLogFile”路径以指向 logs 文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="795f5-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="795f5-420">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="795f5-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="795f5-421">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="795f5-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="795f5-422">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”。</span><span class="sxs-lookup"><span data-stu-id="795f5-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="795f5-423">请确保应用程序池的标识具有对日志文件夹的写入权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="795f5-424">保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="795f5-425">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-425">Make a request to the app.</span></span>
1. <span data-ttu-id="795f5-426">导航到 logs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="795f5-427">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="795f5-428">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-428">Study the log for errors.</span></span>

<span data-ttu-id="795f5-429">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="795f5-430">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-431">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="795f5-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="795f5-432">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-432">Save the file.</span></span>

<span data-ttu-id="795f5-433">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="795f5-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-434">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-435">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="795f5-436">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-437">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="795f5-438">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="795f5-439">将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志：</span><span class="sxs-lookup"><span data-stu-id="795f5-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="795f5-440">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="795f5-441">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="795f5-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="795f5-442">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="795f5-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="795f5-443">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="795f5-444">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="795f5-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="795f5-445">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="795f5-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="795f5-446">在故障排除后从 web.config 文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="795f5-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="795f5-447">有关设置 web.config 中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="795f5-447">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="795f5-448">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="795f5-448">Obtain data from an app</span></span>

<span data-ttu-id="795f5-449">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="795f5-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="795f5-450">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="795f5-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="795f5-451">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="795f5-452">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="795f5-453">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="795f5-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="795f5-454">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="795f5-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="795f5-455">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="795f5-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="795f5-456">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="795f5-457">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="795f5-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="795f5-458">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="795f5-459">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="795f5-460">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="795f5-461">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="795f5-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="795f5-462">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="795f5-463">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="795f5-464">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="795f5-465">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="795f5-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-466">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="795f5-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="795f5-467">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="795f5-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="795f5-468">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="795f5-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="795f5-469">分析转储</span><span class="sxs-lookup"><span data-stu-id="795f5-469">Analyze the dump</span></span>

<span data-ttu-id="795f5-470">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="795f5-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="795f5-471">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="795f5-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="795f5-472">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="795f5-472">Clear package caches</span></span>

<span data-ttu-id="795f5-473">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="795f5-474">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="795f5-475">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="795f5-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="795f5-476">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="795f5-477">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="795f5-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="795f5-478">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="795f5-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="795f5-479">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="795f5-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="795f5-480">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="795f5-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="795f5-481">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="795f5-482">其他资源</span><span class="sxs-lookup"><span data-stu-id="795f5-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="795f5-483">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-483">Azure documentation</span></span>

* [<span data-ttu-id="795f5-484">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="795f5-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="795f5-485">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="795f5-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="795f5-486">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="795f5-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="795f5-487">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="795f5-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="795f5-488">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="795f5-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="795f5-489">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="795f5-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="795f5-490">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="795f5-491">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="795f5-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="795f5-492">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="795f5-492">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="795f5-493">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="795f5-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="795f5-494">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="795f5-495">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="795f5-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="795f5-496">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="795f5-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="795f5-497">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="795f5-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="795f5-498">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="795f5-499">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="795f5-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="795f5-500">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="795f5-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="795f5-501">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="795f5-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="795f5-502">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="795f5-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="795f5-503">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="795f5-504">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="795f5-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="795f5-505">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="795f5-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="795f5-506">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="795f5-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="795f5-507">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="795f5-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="795f5-508">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="795f5-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="795f5-509">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="795f5-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="795f5-510">其他资源</span><span class="sxs-lookup"><span data-stu-id="795f5-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="795f5-511">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="795f5-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="795f5-512">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="795f5-512">App startup errors</span></span>

<span data-ttu-id="795f5-513">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="795f5-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="795f5-514">本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行诊断 。</span><span class="sxs-lookup"><span data-stu-id="795f5-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="795f5-515">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="795f5-515">403.14 Forbidden</span></span>

<span data-ttu-id="795f5-516">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-516">The app fails to start.</span></span> <span data-ttu-id="795f5-517">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="795f5-518">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="795f5-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="795f5-519">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="795f5-520">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="795f5-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="795f5-521">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确 。</span><span class="sxs-lookup"><span data-stu-id="795f5-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="795f5-522">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="795f5-522">Perform the following steps:</span></span>

1. <span data-ttu-id="795f5-523">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="795f5-524">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统：</span><span class="sxs-lookup"><span data-stu-id="795f5-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="795f5-525">确认部署中存在 web.config 文件，并且其内容正确。</span><span class="sxs-lookup"><span data-stu-id="795f5-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="795f5-526">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="795f5-527">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径  。</span><span class="sxs-lookup"><span data-stu-id="795f5-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="795f5-528">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="795f5-529">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="795f5-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="795f5-530">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="795f5-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="795f5-531">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="795f5-531">500 Internal Server Error</span></span>

<span data-ttu-id="795f5-532">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="795f5-533">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="795f5-534">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="795f5-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="795f5-535">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="795f5-536">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="795f5-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="795f5-537">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="795f5-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="795f5-538">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="795f5-539">500.0 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="795f5-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="795f5-540">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-540">The worker process fails.</span></span> <span data-ttu-id="795f5-541">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-541">The app doesn't start.</span></span>

<span data-ttu-id="795f5-542">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)无法找到 .NET Core CLR 和进程内请求处理程序 (aspnetcorev2_inprocess.dll)。</span><span class="sxs-lookup"><span data-stu-id="795f5-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="795f5-543">检查：</span><span class="sxs-lookup"><span data-stu-id="795f5-543">Check that:</span></span>

* <span data-ttu-id="795f5-544">该应用针对 [Microsoft.AspNetCore.Server.IIS NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) 包或 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="795f5-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="795f5-545">目标计算机上安装了该应用所针对的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="795f5-546">500.0 进程外处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="795f5-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="795f5-547">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-547">The worker process fails.</span></span> <span data-ttu-id="795f5-548">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-548">The app doesn't start.</span></span>

<span data-ttu-id="795f5-549">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)无法找到进程外托管请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="795f5-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="795f5-550">请确保 aspnetcorev2.dll 旁边的子文件夹中存在 aspnetcorev2_outofprocess.dll 。</span><span class="sxs-lookup"><span data-stu-id="795f5-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="795f5-551">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="795f5-551">502.5 Process Failure</span></span>

<span data-ttu-id="795f5-552">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-552">The worker process fails.</span></span> <span data-ttu-id="795f5-553">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-553">The app doesn't start.</span></span>

<span data-ttu-id="795f5-554">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="795f5-555">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="795f5-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="795f5-556">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="795f5-557">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="795f5-558">共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll 文件）。</span><span class="sxs-lookup"><span data-stu-id="795f5-558">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="795f5-559">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="795f5-560">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="795f5-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="795f5-561">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”错误页面：</span><span class="sxs-lookup"><span data-stu-id="795f5-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="795f5-562">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="795f5-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="795f5-563">应用未能启动，因为应用的程序集 (.dll) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="795f5-563">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="795f5-564">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="795f5-565">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="795f5-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="795f5-566">在 IIS 管理器的“应用程序池”中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="795f5-566">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="795f5-567">在“操作”面板中的“编辑应用程序池”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="795f5-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="795f5-568">设置“启用 32 位应用程序”：</span><span class="sxs-lookup"><span data-stu-id="795f5-568">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="795f5-569">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="795f5-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="795f5-570">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="795f5-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="795f5-571">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="795f5-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="795f5-572">连接重置</span><span class="sxs-lookup"><span data-stu-id="795f5-572">Connection reset</span></span>

<span data-ttu-id="795f5-573">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="795f5-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="795f5-574">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="795f5-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="795f5-575">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="795f5-576">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="795f5-577">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="795f5-577">Default startup limits</span></span>

<span data-ttu-id="795f5-578">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="795f5-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="795f5-579">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="795f5-580">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="795f5-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="795f5-581">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="795f5-582">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="795f5-583">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="795f5-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="795f5-584">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-584">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="795f5-585">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="795f5-585">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="795f5-586">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="795f5-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="795f5-587">在“支持工具”下，选择“应用程序事件”按钮 。</span><span class="sxs-lookup"><span data-stu-id="795f5-587">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="795f5-588">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误 。</span><span class="sxs-lookup"><span data-stu-id="795f5-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="795f5-589">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="795f5-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="795f5-590">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-591">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-592">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-593">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-594">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="795f5-595">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="795f5-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="795f5-596">检查日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-596">Examine the log.</span></span> <span data-ttu-id="795f5-597">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="795f5-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="795f5-598">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="795f5-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="795f5-599">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="795f5-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="795f5-600">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="795f5-601">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-602">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-603">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-604">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="795f5-605">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="795f5-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="795f5-606">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="795f5-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="795f5-607">运行应用：</span><span class="sxs-lookup"><span data-stu-id="795f5-607">Run the app:</span></span>
   * <span data-ttu-id="795f5-608">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="795f5-609">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="795f5-610">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="795f5-611">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="795f5-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="795f5-612">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="795f5-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="795f5-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="795f5-614">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="795f5-615">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="795f5-616">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="795f5-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="795f5-617">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="795f5-617">**Current release**</span></span>

* <span data-ttu-id="795f5-618">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="795f5-619">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="795f5-620">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="795f5-621">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="795f5-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="795f5-622">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="795f5-623">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="795f5-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="795f5-624">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="795f5-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="795f5-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="795f5-626">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="795f5-627">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="795f5-628">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="795f5-629">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="795f5-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="795f5-630">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="795f5-631">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="795f5-632">在“选择问题类别”下，选择“Web 应用关闭”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-632">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="795f5-633">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮  。</span><span class="sxs-lookup"><span data-stu-id="795f5-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="795f5-634">在 Kudu 诊断控制台中，打开路径“站点 > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-634">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="795f5-635">向下滚动以在列表底部显示“web.config”文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="795f5-636">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="795f5-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-637">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="795f5-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="795f5-638">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="795f5-639">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-639">Make a request to the app.</span></span>
1. <span data-ttu-id="795f5-640">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="795f5-640">Return to the Azure portal.</span></span> <span data-ttu-id="795f5-641">选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="795f5-642">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-643">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-644">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-645">选择“LogFiles”文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="795f5-646">检查“已修改”列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="795f5-647">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="795f5-648">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="795f5-649">在 Kudu 诊断控制台中，返回到路径“site > wwwroot”以显示 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-649">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="795f5-650">通过选择铅笔图标再次打开 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="795f5-651">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="795f5-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="795f5-652">选择“保存”以保存文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="795f5-653">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="795f5-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-654">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-655">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="795f5-656">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="795f5-657">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-658">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="795f5-659">ASP.NET Core 模块调试日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="795f5-660">ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。</span><span class="sxs-lookup"><span data-stu-id="795f5-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="795f5-661">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="795f5-662">要启用增强的诊断日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="795f5-663">按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。</span><span class="sxs-lookup"><span data-stu-id="795f5-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="795f5-664">重新部署应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-664">Redeploy the app.</span></span>
   * <span data-ttu-id="795f5-665">使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中：</span><span class="sxs-lookup"><span data-stu-id="795f5-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="795f5-666">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-667">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-668">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="795f5-669">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="795f5-670">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-670">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="795f5-671">通过选择铅笔按钮编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="795f5-672">添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。</span><span class="sxs-lookup"><span data-stu-id="795f5-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="795f5-673">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="795f5-674">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-675">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-676">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-677">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-678">打开路径“site > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-678">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="795f5-679">如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="795f5-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="795f5-680">如果提供了路径，请导航到日志文件的位置。</span><span class="sxs-lookup"><span data-stu-id="795f5-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="795f5-681">使用文件名旁边的铅笔按钮打开日志文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="795f5-682">故障排除完成后，禁用调试日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="795f5-683">要禁用增强的调试日志，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="795f5-684">从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="795f5-685">使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分。</span><span class="sxs-lookup"><span data-stu-id="795f5-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="795f5-686">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-686">Save the file.</span></span>

<span data-ttu-id="795f5-687">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="795f5-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-688">无法禁用调试日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-689">日志文件大小没有任何限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-689">There's no limit on log file size.</span></span> <span data-ttu-id="795f5-690">仅使用调试日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="795f5-691">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-692">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="795f5-693">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="795f5-694">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="795f5-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="795f5-695">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="795f5-696">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="795f5-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="795f5-697">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="795f5-697">Monitoring blades</span></span>

<span data-ttu-id="795f5-698">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="795f5-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="795f5-699">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="795f5-700">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="795f5-701">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="795f5-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="795f5-702">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="795f5-703">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="795f5-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="795f5-704">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="795f5-705">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="795f5-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="795f5-706">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="795f5-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="795f5-707">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="795f5-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="795f5-708">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="795f5-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="795f5-709">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="795f5-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="795f5-710">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="795f5-711">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-712">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-713">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-714">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件 。</span><span class="sxs-lookup"><span data-stu-id="795f5-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="795f5-715">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="795f5-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-716">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="795f5-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="795f5-717">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="795f5-718">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="795f5-719">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="795f5-720">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="795f5-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="795f5-721">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="795f5-722">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="795f5-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="795f5-723">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="795f5-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="795f5-724">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-724">Make a request to the app.</span></span>
1. <span data-ttu-id="795f5-725">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="795f5-726">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="795f5-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="795f5-727">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="795f5-728">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="795f5-729">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="795f5-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="795f5-730">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="795f5-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="795f5-731">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="795f5-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-732">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-733">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="795f5-734">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-735">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="795f5-736">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="795f5-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="795f5-737">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="795f5-738">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="795f5-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="795f5-739">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-739">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="795f5-740">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="795f5-740">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="795f5-741">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="795f5-742">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="795f5-743">错误具有“源”列中“IIS AspNetCore 模块”或“IIS Express AspNetCore 模块”的值。</span><span class="sxs-lookup"><span data-stu-id="795f5-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="795f5-744">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="795f5-744">Run the app at a command prompt</span></span>

<span data-ttu-id="795f5-745">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="795f5-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="795f5-746">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="795f5-747">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="795f5-747">Framework-dependent deployment</span></span>

<span data-ttu-id="795f5-748">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="795f5-749">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe 执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="795f5-750">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="795f5-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="795f5-751">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="795f5-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="795f5-752">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="795f5-753">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="795f5-754">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="795f5-755">独立部署</span><span class="sxs-lookup"><span data-stu-id="795f5-755">Self-contained deployment</span></span>

<span data-ttu-id="795f5-756">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="795f5-757">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="795f5-758">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="795f5-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="795f5-759">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="795f5-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="795f5-760">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="795f5-761">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="795f5-762">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="795f5-763">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="795f5-764">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="795f5-765">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="795f5-766">如果 logs 文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="795f5-767">有关如何启用 MSBuild 以在部署中自动创建 logs 文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="795f5-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="795f5-768">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-768">Edit the *web.config* file.</span></span> <span data-ttu-id="795f5-769">将“stdoutLogEnabled”设置为 `true` 并更改“stdoutLogFile”路径以指向 logs 文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="795f5-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="795f5-770">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="795f5-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="795f5-771">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="795f5-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="795f5-772">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”。</span><span class="sxs-lookup"><span data-stu-id="795f5-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="795f5-773">请确保应用程序池的标识具有对日志文件夹的写入权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="795f5-774">保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="795f5-775">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-775">Make a request to the app.</span></span>
1. <span data-ttu-id="795f5-776">导航到 logs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="795f5-777">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="795f5-778">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-778">Study the log for errors.</span></span>

<span data-ttu-id="795f5-779">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="795f5-780">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-781">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="795f5-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="795f5-782">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-782">Save the file.</span></span>

<span data-ttu-id="795f5-783">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="795f5-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-784">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-785">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="795f5-786">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-787">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="795f5-788">ASP.NET Core 模块调试日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="795f5-789">将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志：</span><span class="sxs-lookup"><span data-stu-id="795f5-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="795f5-790">确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="795f5-791">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>。</span><span class="sxs-lookup"><span data-stu-id="795f5-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="795f5-792">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="795f5-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="795f5-793">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="795f5-794">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="795f5-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="795f5-795">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="795f5-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="795f5-796">在故障排除后从 web.config 文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="795f5-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="795f5-797">有关设置 web.config 中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="795f5-797">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="795f5-798">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="795f5-798">Obtain data from an app</span></span>

<span data-ttu-id="795f5-799">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="795f5-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="795f5-800">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="795f5-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="795f5-801">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="795f5-802">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="795f5-803">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="795f5-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="795f5-804">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="795f5-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="795f5-805">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="795f5-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="795f5-806">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="795f5-807">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="795f5-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="795f5-808">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="795f5-809">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="795f5-810">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="795f5-811">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="795f5-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="795f5-812">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="795f5-813">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="795f5-814">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="795f5-815">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="795f5-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-816">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="795f5-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="795f5-817">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="795f5-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="795f5-818">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="795f5-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="795f5-819">分析转储</span><span class="sxs-lookup"><span data-stu-id="795f5-819">Analyze the dump</span></span>

<span data-ttu-id="795f5-820">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="795f5-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="795f5-821">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="795f5-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="795f5-822">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="795f5-822">Clear package caches</span></span>

<span data-ttu-id="795f5-823">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="795f5-824">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="795f5-825">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="795f5-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="795f5-826">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="795f5-827">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="795f5-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="795f5-828">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="795f5-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="795f5-829">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="795f5-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="795f5-830">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="795f5-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="795f5-831">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="795f5-832">其他资源</span><span class="sxs-lookup"><span data-stu-id="795f5-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="795f5-833">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-833">Azure documentation</span></span>

* [<span data-ttu-id="795f5-834">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="795f5-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="795f5-835">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="795f5-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="795f5-836">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="795f5-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="795f5-837">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="795f5-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="795f5-838">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="795f5-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="795f5-839">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="795f5-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="795f5-840">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="795f5-841">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="795f5-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="795f5-842">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="795f5-842">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="795f5-843">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="795f5-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="795f5-844">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="795f5-845">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="795f5-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="795f5-846">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="795f5-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="795f5-847">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="795f5-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="795f5-848">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-848">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="795f5-849">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="795f5-849">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="795f5-850">本文提供了有关常见应用启动错误的信息，以及在将应用部署到 Azure 应用服务或 IIS 时如何诊断错误的说明：</span><span class="sxs-lookup"><span data-stu-id="795f5-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="795f5-851">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="795f5-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="795f5-852">介绍常见的启动 HTTP 状态代码方案。</span><span class="sxs-lookup"><span data-stu-id="795f5-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="795f5-853">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="795f5-854">为部署到 Azure 应用服务的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="795f5-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="795f5-855">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="795f5-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="795f5-856">为部署到 IIS 或在本地 IIS Express 上运行的应用提供疑难解答建议。</span><span class="sxs-lookup"><span data-stu-id="795f5-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="795f5-857">本指南适用于 Windows Server 和 Windows 桌面部署。</span><span class="sxs-lookup"><span data-stu-id="795f5-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="795f5-858">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="795f5-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="795f5-859">说明当执行重大升级或更改包版本时，不一致的包中断应用时应如何处理。</span><span class="sxs-lookup"><span data-stu-id="795f5-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="795f5-860">其他资源</span><span class="sxs-lookup"><span data-stu-id="795f5-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="795f5-861">列出其他疑难解答主题。</span><span class="sxs-lookup"><span data-stu-id="795f5-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="795f5-862">应用启动错误</span><span class="sxs-lookup"><span data-stu-id="795f5-862">App startup errors</span></span>

<span data-ttu-id="795f5-863">在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。</span><span class="sxs-lookup"><span data-stu-id="795f5-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="795f5-864">本地调试时出现的“502.5 - 进程失败”可以使用本主题中的建议进行诊断。</span><span class="sxs-lookup"><span data-stu-id="795f5-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="795f5-865">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="795f5-865">403.14 Forbidden</span></span>

<span data-ttu-id="795f5-866">应用无法启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-866">The app fails to start.</span></span> <span data-ttu-id="795f5-867">记录以下错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="795f5-868">此错误通常是由于托管系统上的部署中断引起的，包括以下任何情况：</span><span class="sxs-lookup"><span data-stu-id="795f5-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="795f5-869">该应用部署到托管系统上的错误文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="795f5-870">部署过程未能将所有应用的文件和文件夹移到托管系统上的部署文件夹中。</span><span class="sxs-lookup"><span data-stu-id="795f5-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="795f5-871">部署中缺少 web.config 文件，或者 web.config 文件内容格式不正确 。</span><span class="sxs-lookup"><span data-stu-id="795f5-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="795f5-872">执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="795f5-872">Perform the following steps:</span></span>

1. <span data-ttu-id="795f5-873">从托管系统上的部署文件夹中删除所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="795f5-874">使用常规部署方法（如 Visual Studio、PowerShell 或手动部署）将应用“发布”文件夹的内容重新部署到托管系统：</span><span class="sxs-lookup"><span data-stu-id="795f5-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="795f5-875">确认部署中存在 web.config 文件，并且其内容正确。</span><span class="sxs-lookup"><span data-stu-id="795f5-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="795f5-876">在 Azure 应用服务上托管时，请确认该应用已部署到 `D:\home\site\wwwroot` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="795f5-877">当应用由 IIS 托管时，请确认应用已部署到“IIS 管理器”的“基本设置”中显示的 IIS 物理路径  。</span><span class="sxs-lookup"><span data-stu-id="795f5-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="795f5-878">通过将托管系统上的部署与项目“发布”文件夹的内容进行比较，确认已部署应用的所有文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="795f5-879">有关已发布 ASP.NET Core 应用布局的详细信息，请参阅 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="795f5-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="795f5-880">有关 web.config 文件的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="795f5-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="795f5-881">500 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="795f5-881">500 Internal Server Error</span></span>

<span data-ttu-id="795f5-882">应用启动，但某个错误阻止了服务器完成请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="795f5-883">在启动期间或在创建响应时，应用的代码内出现此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="795f5-884">响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。</span><span class="sxs-lookup"><span data-stu-id="795f5-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="795f5-885">应用程序事件日志通常表明应用正常启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="795f5-886">从服务器的角度来看，这是正确的。</span><span class="sxs-lookup"><span data-stu-id="795f5-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="795f5-887">应用已启动，但无法生成有效的响应。</span><span class="sxs-lookup"><span data-stu-id="795f5-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="795f5-888">在服务器上的命令提示符下运行应用，或启用 ASP.NET Core 模块 stdout 日志来解决问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="795f5-889">502.5 进程失败</span><span class="sxs-lookup"><span data-stu-id="795f5-889">502.5 Process Failure</span></span>

<span data-ttu-id="795f5-890">工作进程失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-890">The worker process fails.</span></span> <span data-ttu-id="795f5-891">应用不启动。</span><span class="sxs-lookup"><span data-stu-id="795f5-891">The app doesn't start.</span></span>

<span data-ttu-id="795f5-892">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。</span><span class="sxs-lookup"><span data-stu-id="795f5-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="795f5-893">进程启动失败的原因通常可通过“应用程序事件日志”和“ASP.NET Core 模块 stdout 日志”中的条目进行确定。</span><span class="sxs-lookup"><span data-stu-id="795f5-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="795f5-894">常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="795f5-895">检查目标计算机上安装的 ASP.NET Core 共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="795f5-896">共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll 文件）。</span><span class="sxs-lookup"><span data-stu-id="795f5-896">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="795f5-897">元包引用可以指定所需的最低版本。</span><span class="sxs-lookup"><span data-stu-id="795f5-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="795f5-898">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="795f5-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="795f5-899">托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”错误页面：</span><span class="sxs-lookup"><span data-stu-id="795f5-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="795f5-900">未能启动应用程序（错误代码“0x800700c1”）</span><span class="sxs-lookup"><span data-stu-id="795f5-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="795f5-901">应用未能启动，因为应用的程序集 (.dll) 无法加载。</span><span class="sxs-lookup"><span data-stu-id="795f5-901">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="795f5-902">当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="795f5-903">确认应用池的 32 位设置正确：</span><span class="sxs-lookup"><span data-stu-id="795f5-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="795f5-904">在 IIS 管理器的“应用程序池”中选择应用池。</span><span class="sxs-lookup"><span data-stu-id="795f5-904">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="795f5-905">在“操作”面板中的“编辑应用程序池”下选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="795f5-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="795f5-906">设置“启用 32 位应用程序”：</span><span class="sxs-lookup"><span data-stu-id="795f5-906">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="795f5-907">如果部署 32 位 (x86) 应用，则将值设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="795f5-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="795f5-908">如果部署 64 位 (x64) 应用，则将值设置为 `False`。</span><span class="sxs-lookup"><span data-stu-id="795f5-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="795f5-909">确认项目文件中的 `<Platform>` MSBuild 属性与应用的已发布位数之间没有冲突。</span><span class="sxs-lookup"><span data-stu-id="795f5-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="795f5-910">连接重置</span><span class="sxs-lookup"><span data-stu-id="795f5-910">Connection reset</span></span>

<span data-ttu-id="795f5-911">如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。</span><span class="sxs-lookup"><span data-stu-id="795f5-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="795f5-912">通常在序列化响应的复杂对象期间出现错误时发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="795f5-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="795f5-913">此类型的错误在客户端上显示为“连接重置”错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="795f5-914">[应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="795f5-915">默认启动限制</span><span class="sxs-lookup"><span data-stu-id="795f5-915">Default startup limits</span></span>

<span data-ttu-id="795f5-916">[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)的默认“startupTimeLimit”配置为 120 秒。</span><span class="sxs-lookup"><span data-stu-id="795f5-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="795f5-917">保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="795f5-918">有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。</span><span class="sxs-lookup"><span data-stu-id="795f5-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="795f5-919">排查 Azure 应用服务中的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="795f5-920">应用程序事件日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="795f5-921">若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：</span><span class="sxs-lookup"><span data-stu-id="795f5-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="795f5-922">在 Azure 门户中打开“应用服务”中的应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-922">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="795f5-923">选择“诊断并解决问题”。</span><span class="sxs-lookup"><span data-stu-id="795f5-923">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="795f5-924">选择“诊断工具”标题。</span><span class="sxs-lookup"><span data-stu-id="795f5-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="795f5-925">在“支持工具”下，选择“应用程序事件”按钮 。</span><span class="sxs-lookup"><span data-stu-id="795f5-925">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="795f5-926">检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2条目提供的最新错误 。</span><span class="sxs-lookup"><span data-stu-id="795f5-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="795f5-927">使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：</span><span class="sxs-lookup"><span data-stu-id="795f5-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="795f5-928">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-929">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-930">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-931">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-932">打开 LogFiles 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="795f5-933">选择 eventlog.xml 文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="795f5-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="795f5-934">检查日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-934">Examine the log.</span></span> <span data-ttu-id="795f5-935">滚动到日志底部以查看最新事件。</span><span class="sxs-lookup"><span data-stu-id="795f5-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="795f5-936">在 Kudu 控制台中运行应用</span><span class="sxs-lookup"><span data-stu-id="795f5-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="795f5-937">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="795f5-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="795f5-938">可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：</span><span class="sxs-lookup"><span data-stu-id="795f5-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="795f5-939">打开“开发工具”区域中的“高级工具” 。</span><span class="sxs-lookup"><span data-stu-id="795f5-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="795f5-940">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-941">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-942">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="795f5-943">测试 32 位 (x86) 应用</span><span class="sxs-lookup"><span data-stu-id="795f5-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="795f5-944">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="795f5-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="795f5-945">运行应用：</span><span class="sxs-lookup"><span data-stu-id="795f5-945">Run the app:</span></span>
   * <span data-ttu-id="795f5-946">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="795f5-947">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="795f5-948">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="795f5-949">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="795f5-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="795f5-950">必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="795f5-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="795f5-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="795f5-952">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="795f5-953">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="795f5-954">测试 64 位 (x64) 应用</span><span class="sxs-lookup"><span data-stu-id="795f5-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="795f5-955">**当前版本**</span><span class="sxs-lookup"><span data-stu-id="795f5-955">**Current release**</span></span>

* <span data-ttu-id="795f5-956">如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="795f5-957">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="795f5-958">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="795f5-959">运行应用：`{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="795f5-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="795f5-960">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="795f5-961">**在预览版上运行的依赖框架的部署**</span><span class="sxs-lookup"><span data-stu-id="795f5-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="795f5-962">必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="795f5-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）</span><span class="sxs-lookup"><span data-stu-id="795f5-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="795f5-964">运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="795f5-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="795f5-965">来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="795f5-966">ASP.NET Core 模块 stdout 日志（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="795f5-967">ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。</span><span class="sxs-lookup"><span data-stu-id="795f5-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="795f5-968">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="795f5-969">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="795f5-970">在“选择问题类别”下，选择“Web 应用关闭”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-970">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="795f5-971">在“建议的解决方案”>“启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮  。</span><span class="sxs-lookup"><span data-stu-id="795f5-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="795f5-972">在 Kudu 诊断控制台中，打开路径“站点 > wwwroot”下的文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-972">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="795f5-973">向下滚动以在列表底部显示“web.config”文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="795f5-974">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="795f5-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-975">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="795f5-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="795f5-976">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="795f5-977">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-977">Make a request to the app.</span></span>
1. <span data-ttu-id="795f5-978">返回到 Azure 门户。</span><span class="sxs-lookup"><span data-stu-id="795f5-978">Return to the Azure portal.</span></span> <span data-ttu-id="795f5-979">选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="795f5-980">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-981">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-982">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-983">选择“LogFiles”文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="795f5-984">检查“已修改”列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="795f5-985">打开日志文件后，将显示错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="795f5-986">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="795f5-987">在 Kudu 诊断控制台中，返回到路径“site > wwwroot”以显示 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-987">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="795f5-988">通过选择铅笔图标再次打开 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="795f5-989">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="795f5-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="795f5-990">选择“保存”以保存文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="795f5-991">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="795f5-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-992">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-993">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="795f5-994">仅使用 stdout 日志记录来解决应用启动问题。</span><span class="sxs-lookup"><span data-stu-id="795f5-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="795f5-995">对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-996">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="795f5-997">应用缓慢或挂起（Azure 应用服务）</span><span class="sxs-lookup"><span data-stu-id="795f5-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="795f5-998">如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="795f5-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="795f5-999">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="795f5-1000">使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储</span><span class="sxs-lookup"><span data-stu-id="795f5-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="795f5-1001">监视边栏选项卡</span><span class="sxs-lookup"><span data-stu-id="795f5-1001">Monitoring blades</span></span>

<span data-ttu-id="795f5-1002">监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。</span><span class="sxs-lookup"><span data-stu-id="795f5-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="795f5-1003">这些边栏选项卡可用于诊断 500 系列错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="795f5-1004">确认是否已安装 ASP.NET Core 扩展。</span><span class="sxs-lookup"><span data-stu-id="795f5-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="795f5-1005">如果未安装扩展，请手动进行安装：</span><span class="sxs-lookup"><span data-stu-id="795f5-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="795f5-1006">在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="795f5-1007">“ASP.NET Core 扩展”应显示在列表中。</span><span class="sxs-lookup"><span data-stu-id="795f5-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="795f5-1008">如果未安装扩展，请选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="795f5-1009">从列表中选择“ASP.NET Core 扩展”。</span><span class="sxs-lookup"><span data-stu-id="795f5-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="795f5-1010">选择“确定”以接受法律条款。</span><span class="sxs-lookup"><span data-stu-id="795f5-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="795f5-1011">选择“添加扩展”边栏选项卡上的“确定”。</span><span class="sxs-lookup"><span data-stu-id="795f5-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="795f5-1012">信息性弹出消息指示成功安装扩展的时间。</span><span class="sxs-lookup"><span data-stu-id="795f5-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="795f5-1013">如果未启用 stdout 日志记录，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="795f5-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="795f5-1014">在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="795f5-1015">选择“转到&rarr;”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="795f5-1016">此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。</span><span class="sxs-lookup"><span data-stu-id="795f5-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="795f5-1017">使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。</span><span class="sxs-lookup"><span data-stu-id="795f5-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="795f5-1018">打开路径“site>wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件 。</span><span class="sxs-lookup"><span data-stu-id="795f5-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="795f5-1019">单击“web.config”文件旁边的铅笔图标。</span><span class="sxs-lookup"><span data-stu-id="795f5-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-1020">将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="795f5-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="795f5-1021">选择“保存”以保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="795f5-1022">继续激活诊断日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="795f5-1023">在 Azure 门户中，选择“诊断日志”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="795f5-1024">选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="795f5-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="795f5-1025">选择边栏选项卡顶部的“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="795f5-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="795f5-1026">若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。</span><span class="sxs-lookup"><span data-stu-id="795f5-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="795f5-1027">选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。</span><span class="sxs-lookup"><span data-stu-id="795f5-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="795f5-1028">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="795f5-1029">在日志流数据中，指示了错误的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="795f5-1030">故障排除完成后，请务必禁用 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="795f5-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="795f5-1031">若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="795f5-1032">在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="795f5-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="795f5-1033">从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。</span><span class="sxs-lookup"><span data-stu-id="795f5-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="795f5-1034">有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。</span><span class="sxs-lookup"><span data-stu-id="795f5-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="795f5-1035">有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。</span><span class="sxs-lookup"><span data-stu-id="795f5-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-1036">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-1037">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="795f5-1038">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-1039">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="795f5-1040">IIS 疑难解答</span><span class="sxs-lookup"><span data-stu-id="795f5-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="795f5-1041">应用程序事件日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="795f5-1042">访问应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="795f5-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="795f5-1043">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-1043">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="795f5-1044">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="795f5-1044">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="795f5-1045">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="795f5-1046">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="795f5-1047">错误具有“源”列中“IIS AspNetCore 模块”或“IIS Express AspNetCore 模块”的值。</span><span class="sxs-lookup"><span data-stu-id="795f5-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="795f5-1048">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="795f5-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="795f5-1049">许多启动错误未在应用程序事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="795f5-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="795f5-1050">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="795f5-1051">依赖框架的部署</span><span class="sxs-lookup"><span data-stu-id="795f5-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="795f5-1052">如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="795f5-1053">在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe 执行应用的程序集来运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="795f5-1054">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="795f5-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="795f5-1055">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="795f5-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="795f5-1056">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="795f5-1057">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="795f5-1058">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="795f5-1059">独立部署</span><span class="sxs-lookup"><span data-stu-id="795f5-1059">Self-contained deployment</span></span>

<span data-ttu-id="795f5-1060">如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：</span><span class="sxs-lookup"><span data-stu-id="795f5-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="795f5-1061">在命令提示符处，导航到部署文件夹并运行应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="795f5-1062">在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="795f5-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="795f5-1063">来自应用且显示任何错误的控制台输出将写入控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="795f5-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="795f5-1064">如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="795f5-1065">如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="795f5-1066">如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="795f5-1067">ASP.NET Core 模块 stdout 日志 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="795f5-1068">若要启用和查看 stdout 日志，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="795f5-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="795f5-1069">在托管系统上导航到站点的部署文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="795f5-1070">如果 logs 文件夹不存在，请创建该文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="795f5-1071">有关如何启用 MSBuild 以在部署中自动创建 logs 文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。</span><span class="sxs-lookup"><span data-stu-id="795f5-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="795f5-1072">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="795f5-1073">将“stdoutLogEnabled”设置为 `true` 并更改“stdoutLogFile”路径以指向 logs 文件夹（例如，`.\logs\stdout`）。</span><span class="sxs-lookup"><span data-stu-id="795f5-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="795f5-1074">路径中的 `stdout` 是日志文件名的前缀。</span><span class="sxs-lookup"><span data-stu-id="795f5-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="795f5-1075">创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="795f5-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="795f5-1076">如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”。</span><span class="sxs-lookup"><span data-stu-id="795f5-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="795f5-1077">请确保应用程序池的标识具有对日志文件夹的写入权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="795f5-1078">保存已更新的 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="795f5-1079">向应用发出请求。</span><span class="sxs-lookup"><span data-stu-id="795f5-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="795f5-1080">导航到 logs 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="795f5-1081">查找并打开最新的 stdout 日志。</span><span class="sxs-lookup"><span data-stu-id="795f5-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="795f5-1082">研究日志以查找错误。</span><span class="sxs-lookup"><span data-stu-id="795f5-1082">Study the log for errors.</span></span>

<span data-ttu-id="795f5-1083">故障排除完成后，禁用 stdout 日志记录：</span><span class="sxs-lookup"><span data-stu-id="795f5-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="795f5-1084">编辑 web.config 文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="795f5-1085">将“stdoutLogEnabled”设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="795f5-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="795f5-1086">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-1086">Save the file.</span></span>

<span data-ttu-id="795f5-1087">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。</span><span class="sxs-lookup"><span data-stu-id="795f5-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-1088">无法禁用 stdout 日志可能会导致应用或服务器出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="795f5-1089">日志文件大小或创建的日志文件数没有限制。</span><span class="sxs-lookup"><span data-stu-id="795f5-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="795f5-1090">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="795f5-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="795f5-1091">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="795f5-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="795f5-1092">启用开发人员异常页面</span><span class="sxs-lookup"><span data-stu-id="795f5-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="795f5-1093">`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="795f5-1094">只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。</span><span class="sxs-lookup"><span data-stu-id="795f5-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="795f5-1095">仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="795f5-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="795f5-1096">在故障排除后从 web.config 文件中删除环境变量。</span><span class="sxs-lookup"><span data-stu-id="795f5-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="795f5-1097">有关设置 web.config 中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="795f5-1097">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="795f5-1098">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="795f5-1098">Obtain data from an app</span></span>

<span data-ttu-id="795f5-1099">如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="795f5-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="795f5-1100">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="795f5-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="795f5-1101">应用缓慢或挂起 (IIS)</span><span class="sxs-lookup"><span data-stu-id="795f5-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="795f5-1102">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="795f5-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="795f5-1103">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="795f5-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="795f5-1104">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="795f5-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="795f5-1105">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="795f5-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="795f5-1106">应用池必须对该文件夹具有写权限。</span><span class="sxs-lookup"><span data-stu-id="795f5-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="795f5-1107">运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="795f5-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="795f5-1108">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="795f5-1109">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="795f5-1110">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="795f5-1111">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="795f5-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="795f5-1112">如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="795f5-1113">如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：</span><span class="sxs-lookup"><span data-stu-id="795f5-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="795f5-1114">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="795f5-1115">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="795f5-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="795f5-1116">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="795f5-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="795f5-1117">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="795f5-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="795f5-1118">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="795f5-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="795f5-1119">分析转储</span><span class="sxs-lookup"><span data-stu-id="795f5-1119">Analyze the dump</span></span>

<span data-ttu-id="795f5-1120">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="795f5-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="795f5-1121">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="795f5-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="795f5-1122">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="795f5-1122">Clear package caches</span></span>

<span data-ttu-id="795f5-1123">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="795f5-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="795f5-1124">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="795f5-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="795f5-1125">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="795f5-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="795f5-1126">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="795f5-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="795f5-1127">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="795f5-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="795f5-1128">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="795f5-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="795f5-1129">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="795f5-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="795f5-1130">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="795f5-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="795f5-1131">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="795f5-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="795f5-1132">其他资源</span><span class="sxs-lookup"><span data-stu-id="795f5-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="795f5-1133">Azure 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-1133">Azure documentation</span></span>

* [<span data-ttu-id="795f5-1134">用于 ASP.NET Core 的 Application Insights</span><span class="sxs-lookup"><span data-stu-id="795f5-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="795f5-1135">“使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除”的“远程调试 Web 应用”一节</span><span class="sxs-lookup"><span data-stu-id="795f5-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="795f5-1136">Azure 应用服务诊断概述</span><span class="sxs-lookup"><span data-stu-id="795f5-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="795f5-1137">如何：在 Azure 应用服务中监视应用</span><span class="sxs-lookup"><span data-stu-id="795f5-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="795f5-1138">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="795f5-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="795f5-1139">解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误</span><span class="sxs-lookup"><span data-stu-id="795f5-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="795f5-1140">解决 Azure 应用服务中 Web 应用性能缓慢的问题</span><span class="sxs-lookup"><span data-stu-id="795f5-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="795f5-1141">Azure 中的 Web 应用的应用程序性能常见问题</span><span class="sxs-lookup"><span data-stu-id="795f5-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="795f5-1142">Azure Web 应用沙盒（应用服务运行时执行限制）</span><span class="sxs-lookup"><span data-stu-id="795f5-1142">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="795f5-1143">Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）</span><span class="sxs-lookup"><span data-stu-id="795f5-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="795f5-1144">Visual Studio 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="795f5-1145">在 Azure 中的 IIS 上的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="795f5-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="795f5-1146">在远程 IIS 计算机的 Visual Studio 2017 中远程调试 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="795f5-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="795f5-1147">了解如何使用 Visual Studio 进行调试</span><span class="sxs-lookup"><span data-stu-id="795f5-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="795f5-1148">Visual Studio Code 文档</span><span class="sxs-lookup"><span data-stu-id="795f5-1148">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="795f5-1149">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="795f5-1149">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
