---
title: 高级配置
author: rick-anderson
description: ASP.NET Core 模块和 Internet Information Services (IIS) 的高级配置。
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: de3abb7b5f89e3b84da38a8eabb183f9551cc1f9
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588914"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="53225-103">ASP.NET Core 模块和 IIS 的高级配置</span><span class="sxs-lookup"><span data-stu-id="53225-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="53225-104">本文介绍 ASP.NET Core 模块和 IIS 的高级配置选项和方案。</span><span class="sxs-lookup"><span data-stu-id="53225-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="53225-105">修改堆栈大小</span><span class="sxs-lookup"><span data-stu-id="53225-105">Modify the stack size</span></span>

<span data-ttu-id="53225-106">*仅适用于使用进程内托管模型的情况。*</span><span class="sxs-lookup"><span data-stu-id="53225-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="53225-107">使用 `web.config` 文件中的 `stackSize` 设置（以字节为单位）配置托管堆栈大小。</span><span class="sxs-lookup"><span data-stu-id="53225-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="53225-108">默认大小为 1,048,576 字节 (1 MB)。</span><span class="sxs-lookup"><span data-stu-id="53225-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="53225-109">下面的示例将堆栈大小更改为 2 MB（2,097,152 字节）：</span><span class="sxs-lookup"><span data-stu-id="53225-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="53225-110">代理配置使用 HTTP 协议和配对令牌</span><span class="sxs-lookup"><span data-stu-id="53225-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="53225-111">*仅适用于进程外托管。*</span><span class="sxs-lookup"><span data-stu-id="53225-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="53225-112">在 ASP.NET Core 模块和 Kestrel 之间创建的代理使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="53225-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="53225-113">因此，不存在从脱离服务器的位置窃取模块和 Kestrel 之间的流量的风险。</span><span class="sxs-lookup"><span data-stu-id="53225-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="53225-114">配对令牌用于保证 Kestrel 收到的请求已由 IIS 代理且不来自某些其他源。</span><span class="sxs-lookup"><span data-stu-id="53225-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="53225-115">模块已创建配对令牌并将其设置到环境变量 (`ASPNETCORE_TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="53225-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="53225-116">此外，配对令牌还设置到每个代理请求的标头 (`MS-ASPNETCORE-TOKEN`)。</span><span class="sxs-lookup"><span data-stu-id="53225-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="53225-117">IIS 中间件检查它所接收的每个请求，以确认配对令牌标头值与环境变量值相匹配。</span><span class="sxs-lookup"><span data-stu-id="53225-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="53225-118">如果令牌值不匹配，则将记录请求并拒绝该请求。</span><span class="sxs-lookup"><span data-stu-id="53225-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="53225-119">无法从脱离服务器的位置访问配对令牌环境变量及模块和 Kestrel 之间的流量。</span><span class="sxs-lookup"><span data-stu-id="53225-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="53225-120">如果不知道配对令牌值，攻击者就无法提交绕过 IIS 中间件中的检查的请求。</span><span class="sxs-lookup"><span data-stu-id="53225-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="53225-121">具有 IIS 共享配置的 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="53225-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="53225-122">ASP.NET Core 模块安装程序使用 `TrustedInstaller` 帐户的权限运行。</span><span class="sxs-lookup"><span data-stu-id="53225-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="53225-123">由于本地系统帐户没有 IIS 共享配置所用的共享路径的修改权限，因此在尝试配置共享上的 `applicationHost.config` 文件中的模块设置时，安装程序将引发拒绝访问错误。</span><span class="sxs-lookup"><span data-stu-id="53225-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="53225-124">在与 IIS 安装相同的计算机上使用 IIS 共享配置时，请运行 ASP.NET Core Hosting Bundle 安装程序，并将 `OPT_NO_SHARED_CONFIG_CHECK` 参数设置为 `1`：</span><span class="sxs-lookup"><span data-stu-id="53225-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="53225-125">如果共享配置的路径与 IIS 安装的路径不在同一台计算机上，请按照下列步骤操作：</span><span class="sxs-lookup"><span data-stu-id="53225-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="53225-126">禁用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="53225-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="53225-127">运行安装程序。</span><span class="sxs-lookup"><span data-stu-id="53225-127">Run the installer.</span></span>
1. <span data-ttu-id="53225-128">将已更新的 `applicationHost.config` 文件导出到文件共享。</span><span class="sxs-lookup"><span data-stu-id="53225-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="53225-129">重新启用 IIS 共享配置。</span><span class="sxs-lookup"><span data-stu-id="53225-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="53225-130">数据保护</span><span class="sxs-lookup"><span data-stu-id="53225-130">Data protection</span></span>

<span data-ttu-id="53225-131">[ASP.NET Core 数据保护堆栈](xref:security/data-protection/introduction)由多个 ASP.NET Core [中间件](xref:fundamentals/middleware/index)使用，包括用于身份验证的中间件。</span><span class="sxs-lookup"><span data-stu-id="53225-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="53225-132">即使用户代码不调用数据保护 API，也应该使用部署脚本或在用户代码中配置数据保护，以创建持久的加密[密钥存储](xref:security/data-protection/implementation/key-management)。</span><span class="sxs-lookup"><span data-stu-id="53225-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="53225-133">如果不配置数据保护，则密钥存储在内存中。重启应用时，密钥会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="53225-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="53225-134">如果应用重启时数据保护密钥环存储于内存中：</span><span class="sxs-lookup"><span data-stu-id="53225-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="53225-135">所有基于 cookie 的身份验证令牌都无效。</span><span class="sxs-lookup"><span data-stu-id="53225-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="53225-136">用户需要在下一次请求时再次登录。</span><span class="sxs-lookup"><span data-stu-id="53225-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="53225-137">无法再解密使用密钥环保护的任何数据。</span><span class="sxs-lookup"><span data-stu-id="53225-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="53225-138">这可能包括 [CSRF 令牌](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)和 [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="53225-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="53225-139">若要在 IIS 下配置数据保护以持久化密钥环，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="53225-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="53225-140">**创建数据保护注册表项**</span><span class="sxs-lookup"><span data-stu-id="53225-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="53225-141">ASP.NET Core 应用使用的数据保护密钥存储在应用外部的注册表中。</span><span class="sxs-lookup"><span data-stu-id="53225-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="53225-142">要持久保存给定应用的密钥，需为应用池创建注册表项。</span><span class="sxs-lookup"><span data-stu-id="53225-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="53225-143">对于独立的非 Web 场 IIS 安装，可以对用于 ASP.NET Core 应用的每个应用池使用[数据保护 Provision-AutoGenKeys.ps1 PowerShell 脚本](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1)。</span><span class="sxs-lookup"><span data-stu-id="53225-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="53225-144">此脚本在 HKLM 注册表中创建注册表项，仅应用的应用池工作进程帐户可对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="53225-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="53225-145">通过计算机范围的密钥使用 DPAPI 对密钥静态加密。</span><span class="sxs-lookup"><span data-stu-id="53225-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="53225-146">在 Web 场方案中，可以将应用配置为使用 UNC 路径存储其数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="53225-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="53225-147">默认情况下，密钥未加密。</span><span class="sxs-lookup"><span data-stu-id="53225-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="53225-148">确保网络共享的文件权限仅限于应用在其下运行的 Windows 帐户。</span><span class="sxs-lookup"><span data-stu-id="53225-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="53225-149">可使用 X509 证书来保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="53225-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="53225-150">考虑允许用户上传证书的机制。</span><span class="sxs-lookup"><span data-stu-id="53225-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="53225-151">将证书置于用户信任的证书存储中，并确保这些证书对所有运行用户应用的计算机都可用。</span><span class="sxs-lookup"><span data-stu-id="53225-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="53225-152">有关详细信息，请参阅 <xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="53225-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="53225-153">**配置 IIS 应用程序池以加载用户配置文件**</span><span class="sxs-lookup"><span data-stu-id="53225-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="53225-154">此设置位于应用池“高级设置”下的“进程模型”部分 。</span><span class="sxs-lookup"><span data-stu-id="53225-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="53225-155">将“加载用户配置文件”设置为 `True`。</span><span class="sxs-lookup"><span data-stu-id="53225-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="53225-156">如果设置为 `True`，会将密钥存储在用户配置文件目录中，并使用 DPAPI 和特定于用户帐户的密钥进行保护。</span><span class="sxs-lookup"><span data-stu-id="53225-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="53225-157">密钥保留在 `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="53225-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="53225-158">同时还必须启用应用池的 [`setProfileEnvironment` 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。</span><span class="sxs-lookup"><span data-stu-id="53225-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="53225-159">`setProfileEnvironment` 的默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="53225-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="53225-160">在某些情况下（例如，Windows 操作系统），将 `setProfileEnvironment` 设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="53225-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="53225-161">如果密钥未按预期存储在用户配置文件目录中，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="53225-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="53225-162">导航到 `%windir%/system32/inetsrv/config` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="53225-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="53225-163">打开 `applicationHost.config` 文件。</span><span class="sxs-lookup"><span data-stu-id="53225-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="53225-164">查找 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。</span><span class="sxs-lookup"><span data-stu-id="53225-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="53225-165">确认 `setProfileEnvironment` 属性不存在，这会将值默认设置为 `true`，或者将属性的值显式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="53225-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="53225-166">**将文件系统用作密钥环存储**</span><span class="sxs-lookup"><span data-stu-id="53225-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="53225-167">调整应用代码，[将文件系统用作密钥环存储](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="53225-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="53225-168">使用 X509 证书保护密钥环，并确保该证书是受信任的证书。</span><span class="sxs-lookup"><span data-stu-id="53225-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="53225-169">如果它是自签名证书，则将该证书放置于受信任的根存储中。</span><span class="sxs-lookup"><span data-stu-id="53225-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="53225-170">当在 Web 场中使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="53225-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="53225-171">使用所有计算机都可以访问的文件共享。</span><span class="sxs-lookup"><span data-stu-id="53225-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="53225-172">将 X509 证书部署到每台计算机。</span><span class="sxs-lookup"><span data-stu-id="53225-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="53225-173">[通过代码配置数据保护](xref:security/data-protection/configuration/overview)。</span><span class="sxs-lookup"><span data-stu-id="53225-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="53225-174">**设置用于数据保护的计算机范围的策略**</span><span class="sxs-lookup"><span data-stu-id="53225-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="53225-175">数据保护系统对以下操作提供有限支持：为使用数据保护 API 的所有应用设置默认[计算机范围的策略](xref:security/data-protection/configuration/machine-wide-policy)。</span><span class="sxs-lookup"><span data-stu-id="53225-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="53225-176">有关详细信息，请参阅 <xref:security/data-protection/introduction>。</span><span class="sxs-lookup"><span data-stu-id="53225-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="53225-177">IIS 配置</span><span class="sxs-lookup"><span data-stu-id="53225-177">IIS configuration</span></span>

<span data-ttu-id="53225-178">**Windows Server 操作系统**</span><span class="sxs-lookup"><span data-stu-id="53225-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="53225-179">启用 Web 服务器 (IIS) 服务器角色并建立角色服务。</span><span class="sxs-lookup"><span data-stu-id="53225-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="53225-180">通过“管理”菜单或“服务器管理器”中的链接使用“添加角色和功能”向导。</span><span class="sxs-lookup"><span data-stu-id="53225-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="53225-181">在“服务器角色”步骤中，选中“Web 服务器(IIS)”框 。</span><span class="sxs-lookup"><span data-stu-id="53225-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![在选择服务器角色步骤中选择了“Web 服务器 IIS”角色。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="53225-183">在“功能”步骤后，为 Web 服务器 (IIS) 加载“角色服务”步骤。</span><span class="sxs-lookup"><span data-stu-id="53225-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="53225-184">选择所需 IIS 角色服务，或接受提供的默认角色服务。</span><span class="sxs-lookup"><span data-stu-id="53225-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![在选择角色服务步骤中选择了默认角色服务。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="53225-186">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="53225-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="53225-187">若要启用 Windows 身份验证，请依次展开以下节点：“Web 服务器” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="53225-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="53225-188">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="53225-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="53225-189">有关详细信息，请参阅 [Windows 身份验证 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="53225-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="53225-190">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="53225-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="53225-191">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="53225-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="53225-192">若要启用 WebSocket，请依次展开以下节点：“Web 服务器” > “应用开发”。</span><span class="sxs-lookup"><span data-stu-id="53225-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="53225-193">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="53225-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="53225-194">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="53225-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="53225-195">继续执行“确认”步骤，安装 Web 服务器角色和服务。</span><span class="sxs-lookup"><span data-stu-id="53225-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="53225-196">安装 Web 服务器 (IIS) 角色后无需重启服务器/IIS。</span><span class="sxs-lookup"><span data-stu-id="53225-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="53225-197">**Windows 桌面操作系统**</span><span class="sxs-lookup"><span data-stu-id="53225-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="53225-198">启用“IIS 管理控制台”和“万维网服务”。</span><span class="sxs-lookup"><span data-stu-id="53225-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="53225-199">导航到“控制面板” > “程序” > “程序和功能” > “启用或禁用 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="53225-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="53225-200">打开“Internet Information Services”节点。</span><span class="sxs-lookup"><span data-stu-id="53225-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="53225-201">打开“Web 管理工具”节点。</span><span class="sxs-lookup"><span data-stu-id="53225-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="53225-202">选中“IIS 管理控制台”框。</span><span class="sxs-lookup"><span data-stu-id="53225-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="53225-203">选中“万维网服务”框。</span><span class="sxs-lookup"><span data-stu-id="53225-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="53225-204">接受“万维网服务”的默认功能，或自定义 IIS 功能。</span><span class="sxs-lookup"><span data-stu-id="53225-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="53225-205">**Windows 身份验证（可选）**</span><span class="sxs-lookup"><span data-stu-id="53225-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="53225-206">若要启用 Windows 身份验证，请依次展开以下节点：“万维网服务” > “安全”。</span><span class="sxs-lookup"><span data-stu-id="53225-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="53225-207">选择“Windows 身份验证”功能。</span><span class="sxs-lookup"><span data-stu-id="53225-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="53225-208">有关详细信息，请参阅 [Windows 身份验证 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 和[配置 Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="53225-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="53225-209">**Websocket（可选）**</span><span class="sxs-lookup"><span data-stu-id="53225-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="53225-210">Websocket 支持 ASP.NET Core 1.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="53225-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="53225-211">若要启用 WebSocket，请依次展开以下节点：“万维网服务” > “应用开发功能”。</span><span class="sxs-lookup"><span data-stu-id="53225-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="53225-212">选择“WebSocket 协议”功能。</span><span class="sxs-lookup"><span data-stu-id="53225-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="53225-213">有关详细信息，请参阅 [WebSockets](xref:fundamentals/websockets)。</span><span class="sxs-lookup"><span data-stu-id="53225-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="53225-214">如果 IIS 安装需要重新启动，则重新启动系统。</span><span class="sxs-lookup"><span data-stu-id="53225-214">If the IIS installation requires a restart, restart the system.</span></span>

![在“Windows 功能”中选择了“IIS 管理控制台”和“万维网服务”。](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="53225-216">虚拟目录</span><span class="sxs-lookup"><span data-stu-id="53225-216">Virtual Directories</span></span>

<span data-ttu-id="53225-217">ASP.NET Core 应用不支持 [IIS 虚拟目录](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。</span><span class="sxs-lookup"><span data-stu-id="53225-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="53225-218">可将应用托管为[子应用程序](#sub-applications)。</span><span class="sxs-lookup"><span data-stu-id="53225-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="53225-219">子应用程序</span><span class="sxs-lookup"><span data-stu-id="53225-219">Sub-applications</span></span>

<span data-ttu-id="53225-220">可将 ASP.NET Core 应用托管为 [IIS 子应用程序（子应用）](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)。</span><span class="sxs-lookup"><span data-stu-id="53225-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="53225-221">子应用的路径成为根应用 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="53225-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="53225-222">子应用内的静态资产链接应使用波形符-斜杠 (`~/`) 符号。</span><span class="sxs-lookup"><span data-stu-id="53225-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="53225-223">波形符-斜杠符号触发[标记帮助器](xref:mvc/views/tag-helpers/intro)，来将子应用的基路径追加到呈现的相关链接前面。</span><span class="sxs-lookup"><span data-stu-id="53225-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="53225-224">对于 `/subapp_path` 处的子应用，使用 `src="~/image.png"` 链接的图像将呈现为 `src="/subapp_path/image.png"`。</span><span class="sxs-lookup"><span data-stu-id="53225-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="53225-225">根应用的静态文件中间件不处理静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="53225-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="53225-226">此请求由子应用的静态文件中间件处理。</span><span class="sxs-lookup"><span data-stu-id="53225-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="53225-227">若将静态资产的 `src` 属性设置为绝对路径（如 `src="/image.png"`），则呈现的链接不包含子应用的基路径。</span><span class="sxs-lookup"><span data-stu-id="53225-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="53225-228">根应用的静态文件中间件试图从根应用的 [web 根目录](xref:fundamentals/index#web-root)提供资产，这会导致“404 - 找不到”响应生成，除非可以从根应用获得静态资产。</span><span class="sxs-lookup"><span data-stu-id="53225-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="53225-229">若要将 ASP.NET Core 应用作为子应用托管在其他 ASP.NET Core 应用下：</span><span class="sxs-lookup"><span data-stu-id="53225-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="53225-230">为此子应用创建应用池。</span><span class="sxs-lookup"><span data-stu-id="53225-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="53225-231">将“.NET CLR 版本”设置为“无托管代码”，因为将启动 .NET Core 的核心公共语言运行时 (CoreCLR) ，将应用托管在工作进程中，而非桌面 CLR (.NET CLR) 中 。</span><span class="sxs-lookup"><span data-stu-id="53225-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="53225-232">在 IIS 管理器中添加根网站，并且此子应用在根网站的某个文件夹中。</span><span class="sxs-lookup"><span data-stu-id="53225-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="53225-233">在 IIS 管理器中右击此子应用文件夹，并选择“转换为应用程序”。</span><span class="sxs-lookup"><span data-stu-id="53225-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="53225-234">在“添加应用程序”对话框中，使用“应用程序池”的“选择”按钮来分配为子应用创建的应用池。  </span><span class="sxs-lookup"><span data-stu-id="53225-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="53225-235">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="53225-235">Select **OK**.</span></span>

<span data-ttu-id="53225-236">使用进程内托管模型时，需要向子应用分配单独的应用池。</span><span class="sxs-lookup"><span data-stu-id="53225-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="53225-237">有关进程内托管模型及 ASP.NET Core 模块配置的详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="53225-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="53225-238">应用程序池</span><span class="sxs-lookup"><span data-stu-id="53225-238">Application Pools</span></span>

<span data-ttu-id="53225-239">由托管模型决定应用池隔离：</span><span class="sxs-lookup"><span data-stu-id="53225-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="53225-240">托管在进程内：应用需要在单独的应用池中运行。</span><span class="sxs-lookup"><span data-stu-id="53225-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="53225-241">托管在进程外：建议在每个应用自己的应用池中运行各应用，以彼此隔离。</span><span class="sxs-lookup"><span data-stu-id="53225-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="53225-242">IIS“添加网站”对话框默认为每应用一个应用池。</span><span class="sxs-lookup"><span data-stu-id="53225-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="53225-243">提供了站点名称时，该文本会自动传输到“应用程序池”文本框 。</span><span class="sxs-lookup"><span data-stu-id="53225-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="53225-244">添加站点时，会使用该站点名称创建新的应用池。</span><span class="sxs-lookup"><span data-stu-id="53225-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="53225-245">应用程序池 Identity</span><span class="sxs-lookup"><span data-stu-id="53225-245">Application Pool Identity</span></span>

<span data-ttu-id="53225-246">通过应用池标识帐户，可以在唯一帐户下运行应用，而无需创建和管理域或本地帐户。</span><span class="sxs-lookup"><span data-stu-id="53225-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="53225-247">在 IIS 8.0 或更高版本上，IIS 管理员工作进程 (WAS) 将使用新应用池的名称创建一个虚拟帐户，并默认在此帐户下运行应用池的工作进程。</span><span class="sxs-lookup"><span data-stu-id="53225-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="53225-248">在 IIS 管理控制台中，确保应用池“高级设置”下的“Identity”设置为使用 `ApplicationPoolIdentity` ：</span><span class="sxs-lookup"><span data-stu-id="53225-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![应用程序池“高级设置”对话框](index/_static/apppool-identity.png)

<span data-ttu-id="53225-250">IIS 管理进程使用 Windows 安全系统中应用池的名称创建安全标识符。</span><span class="sxs-lookup"><span data-stu-id="53225-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="53225-251">可使用此标识保护资源。</span><span class="sxs-lookup"><span data-stu-id="53225-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="53225-252">但是，此标识不是真实的用户帐户，不会在 Windows 用户管理控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="53225-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="53225-253">如果 IIS 工作进程需要对应用的高级访问权限，请为包含该应用的目录修改访问控制列表 (ACL)：</span><span class="sxs-lookup"><span data-stu-id="53225-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="53225-254">打开 Windows 资源管理器并导航到目录。</span><span class="sxs-lookup"><span data-stu-id="53225-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="53225-255">右键单击该目录，然后选择“属性”。</span><span class="sxs-lookup"><span data-stu-id="53225-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="53225-256">在“安全”选项卡下，选择“编辑”按钮，然后单击“添加”按钮  。</span><span class="sxs-lookup"><span data-stu-id="53225-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="53225-257">选择“位置”按钮，并确保该系统处于选中状态。</span><span class="sxs-lookup"><span data-stu-id="53225-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="53225-258">在“输入要选择的对象名”区域中，输入 `IIS AppPool\{APP POOL NAME}` 格式，其中 `{APP POOL NAME}` 占位符为应用池名称。</span><span class="sxs-lookup"><span data-stu-id="53225-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="53225-259">选择“检查名称”按钮。</span><span class="sxs-lookup"><span data-stu-id="53225-259">Select the **Check Names** button.</span></span> <span data-ttu-id="53225-260">对于 DefaultAppPool，请使用 `IIS AppPool\DefaultAppPool` 检查名称。</span><span class="sxs-lookup"><span data-stu-id="53225-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="53225-261">如果选择了“检查名称”按钮，对象名称区域中将指示 `DefaultAppPool`。</span><span class="sxs-lookup"><span data-stu-id="53225-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="53225-262">无法直接在对象名称区域中输入应用池名称。</span><span class="sxs-lookup"><span data-stu-id="53225-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="53225-263">检查对象名称时使用 `IIS AppPool\{APP POOL NAME}` 格式，其中 `{APP POOL NAME}` 占位符是应用池名称。</span><span class="sxs-lookup"><span data-stu-id="53225-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在选择“检查名称”前，将“DefaultAppPool\"”的应用池名称追加到对象名称区域中的“IIS AppPool”。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="53225-265">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="53225-265">Select **OK**.</span></span>

   ![应用文件夹的“选择用户或组”对话框：在你选择“检查名称”后，对象名称“DefaultAppPool”显示在对象名称区域中。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="53225-267">默认情况下应授予读取 &amp; 执行权限。</span><span class="sxs-lookup"><span data-stu-id="53225-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="53225-268">根据需要请提供其他权限。</span><span class="sxs-lookup"><span data-stu-id="53225-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="53225-269">也可使用 ICACLS 工具在命令提示符处授予访问权限。</span><span class="sxs-lookup"><span data-stu-id="53225-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="53225-270">以 DefaultAppPool 为例，使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="53225-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="53225-271">有关详细信息，请参阅 [icacls](/windows-server/administration/windows-commands/icacls) 主题。</span><span class="sxs-lookup"><span data-stu-id="53225-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="53225-272">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="53225-272">HTTP/2 support</span></span>

<span data-ttu-id="53225-273">以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="53225-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="53225-274">进程内</span><span class="sxs-lookup"><span data-stu-id="53225-274">In-process</span></span>
  * <span data-ttu-id="53225-275">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="53225-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="53225-276">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="53225-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="53225-277">进程外</span><span class="sxs-lookup"><span data-stu-id="53225-277">Out-of-process</span></span>
  * <span data-ttu-id="53225-278">Windows Server 2016/Windows 10 或更高版本；IIS 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="53225-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="53225-279">面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="53225-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="53225-280">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="53225-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="53225-281">对于建立了 HTTP/2 连接时的进程内部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="53225-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="53225-282">对于建立了 HTTP/2 连接时的进程外部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="53225-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="53225-283">若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="53225-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="53225-284">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="53225-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="53225-285">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="53225-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="53225-286">有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。</span><span class="sxs-lookup"><span data-stu-id="53225-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="53225-287">CORS 预检请求</span><span class="sxs-lookup"><span data-stu-id="53225-287">CORS preflight requests</span></span>

<span data-ttu-id="53225-288">本部分仅适用于面向 .NET Framework 的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="53225-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="53225-289">对于面向 .NET Framework 的 ASP.NET Core 应用程序，默认情况下，IIS 不会将 OPTIONS 请求传递给应用程序。</span><span class="sxs-lookup"><span data-stu-id="53225-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="53225-290">若要了解如何在 `web.config` 中配置应用的 IIS 处理程序以传递 OPTIONS 请求，请参阅[在 ASP.NET Web API 2 中启用跨域请求：CORS 的工作原理](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。</span><span class="sxs-lookup"><span data-stu-id="53225-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="53225-291">应用程序初始化模块和空闲超时</span><span class="sxs-lookup"><span data-stu-id="53225-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="53225-292">通过 ASP.NET Core 模板版本 2 托管在 IIS 中时：</span><span class="sxs-lookup"><span data-stu-id="53225-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="53225-293">[应用程序初始化模块](#application-initialization-module)：可以将托管在[进程内](xref:host-and-deploy/iis/in-process-hosting)或[进程外](xref:host-and-deploy/iis/out-of-process-hosting)的应用配置为在工作进程重启或服务器重启后自动启动。</span><span class="sxs-lookup"><span data-stu-id="53225-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="53225-294">[空闲超时](#idle-timeout)：可以将托管在[进程内](xref:host-and-deploy/iis/in-process-hosting)的应用配置为在非活动时段不超时。</span><span class="sxs-lookup"><span data-stu-id="53225-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="53225-295">应用程序初始化模块</span><span class="sxs-lookup"><span data-stu-id="53225-295">Application Initialization Module</span></span>

<span data-ttu-id="53225-296">适用于托管在进程内和进程外的应用。</span><span class="sxs-lookup"><span data-stu-id="53225-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="53225-297">[IIS 应用程序初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一种 IIS 功能，可在应用池启动或回收时向应用发送 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="53225-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="53225-298">该请求会触发应用启动。</span><span class="sxs-lookup"><span data-stu-id="53225-298">The request triggers the app to start.</span></span> <span data-ttu-id="53225-299">默认情况下，IIS 向应用的根 URL (`/`) 发出请求以初始化应用（有关配置的详细信息，请参阅[更多资源](#application-initialization-module-and-idle-timeout-additional-resources)）。</span><span class="sxs-lookup"><span data-stu-id="53225-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="53225-300">确认已启用 IIS 应用程序初始化角色功能：</span><span class="sxs-lookup"><span data-stu-id="53225-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="53225-301">Windows 7 或更高版本桌面系统，在本地使用 IIS 时：</span><span class="sxs-lookup"><span data-stu-id="53225-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="53225-302">导航到“控制面板” > “程序” > “程序和功能” > “启用或禁用 Windows 功能”（位于屏幕左侧）   。</span><span class="sxs-lookup"><span data-stu-id="53225-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="53225-303">打开“Internet Information Services” > “万维网服务” > “应用程序开发功能”。</span><span class="sxs-lookup"><span data-stu-id="53225-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="53225-304">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="53225-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="53225-305">Windows Server 2008 R2 或更高版本：</span><span class="sxs-lookup"><span data-stu-id="53225-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="53225-306">打开“添加角色和功能向导”。</span><span class="sxs-lookup"><span data-stu-id="53225-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="53225-307">在“选择角色服务”面板中，打开“应用程序开发”节点 。</span><span class="sxs-lookup"><span data-stu-id="53225-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="53225-308">选中“应用程序初始化”的复选框。</span><span class="sxs-lookup"><span data-stu-id="53225-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="53225-309">使用下面的任一方法为站点启用“应用程序初始化模块”：</span><span class="sxs-lookup"><span data-stu-id="53225-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="53225-310">使用 IIS 管理器：</span><span class="sxs-lookup"><span data-stu-id="53225-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="53225-311">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="53225-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="53225-312">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="53225-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="53225-313">默认的“启动模式”为“`OnDemand`”。</span><span class="sxs-lookup"><span data-stu-id="53225-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="53225-314">将“启动模式”设置为“`AlwaysRunning`”。</span><span class="sxs-lookup"><span data-stu-id="53225-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="53225-315">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="53225-315">Select **OK**.</span></span>
  1. <span data-ttu-id="53225-316">打开“连接”面板中的“网站”节点。</span><span class="sxs-lookup"><span data-stu-id="53225-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="53225-317">右键单击应用，并选择“管理网站” > “高级设置” 。</span><span class="sxs-lookup"><span data-stu-id="53225-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="53225-318">默认的“预加载已启用”设置为“`False`”。</span><span class="sxs-lookup"><span data-stu-id="53225-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="53225-319">将“预加载已启用”设置为“`True`”。</span><span class="sxs-lookup"><span data-stu-id="53225-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="53225-320">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="53225-320">Select **OK**.</span></span>

* <span data-ttu-id="53225-321">使用 `web.config` 向应用的 `web.config` 文件中的 `<system.webServer>` 元素添加 `<applicationInitialization>` 元素（其中 `doAppInitAfterRestart` 设置为 `true`）：</span><span class="sxs-lookup"><span data-stu-id="53225-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="53225-322">空闲超时</span><span class="sxs-lookup"><span data-stu-id="53225-322">Idle Timeout</span></span>

<span data-ttu-id="53225-323">仅适用于托管在进程内的应用。</span><span class="sxs-lookup"><span data-stu-id="53225-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="53225-324">若要防止应用进入空闲状态，请使用 IIS 管理器设置应用池的空闲超时：</span><span class="sxs-lookup"><span data-stu-id="53225-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="53225-325">在“连接”面板中选择“应用程序池”。</span><span class="sxs-lookup"><span data-stu-id="53225-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="53225-326">在列表中右键单击应用的应用池，并选择“高级设置”。</span><span class="sxs-lookup"><span data-stu-id="53225-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="53225-327">默认的“空闲超时(分钟)”为“`20`”分钟。</span><span class="sxs-lookup"><span data-stu-id="53225-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="53225-328">将“空闲超时(分钟)”设置为“`0`”（零）。</span><span class="sxs-lookup"><span data-stu-id="53225-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="53225-329">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="53225-329">Select **OK**.</span></span>
1. <span data-ttu-id="53225-330">回收工作进程。</span><span class="sxs-lookup"><span data-stu-id="53225-330">Recycle the worker process.</span></span>

<span data-ttu-id="53225-331">若要防止托管在[进程外](xref:host-and-deploy/iis/out-of-process-hosting)的应用超时，请使用下列任一方法：</span><span class="sxs-lookup"><span data-stu-id="53225-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="53225-332">从外部服务 ping 应用，使其保持运行状态。</span><span class="sxs-lookup"><span data-stu-id="53225-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="53225-333">如果应用仅托管后台服务，请避免使用 IIS 托管，而是[使用 Windows 服务托管 ASP.NET Core 应用](xref:host-and-deploy/windows-service)。</span><span class="sxs-lookup"><span data-stu-id="53225-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="53225-334">关于应用程序初始化模块和空闲超时的更多资源</span><span class="sxs-lookup"><span data-stu-id="53225-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="53225-335">IIS 8.0 应用程序初始化</span><span class="sxs-lookup"><span data-stu-id="53225-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="53225-336">[应用程序初始化 `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="53225-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="53225-337">[应用程序池 `<processModel>` 的进程模型设置](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="53225-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="53225-338">模块、架构和配置文件位置</span><span class="sxs-lookup"><span data-stu-id="53225-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="53225-339">模块</span><span class="sxs-lookup"><span data-stu-id="53225-339">Module</span></span>

<span data-ttu-id="53225-340">**IIS (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="53225-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="53225-341">**IIS Express (x86/amd64)** ：</span><span class="sxs-lookup"><span data-stu-id="53225-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="53225-342">架构</span><span class="sxs-lookup"><span data-stu-id="53225-342">Schema</span></span>

<span data-ttu-id="53225-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="53225-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="53225-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="53225-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="53225-345">Configuration</span><span class="sxs-lookup"><span data-stu-id="53225-345">Configuration</span></span>

<span data-ttu-id="53225-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="53225-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="53225-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="53225-347">**IIS Express**</span></span>

* <span data-ttu-id="53225-348">Visual Studio：`{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="53225-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="53225-349">iisexpress.exe CLI：`%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="53225-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="53225-350">通过在 `applicationHost.config` 文件中搜索 `aspnetcore` 可以找到这些文件。</span><span class="sxs-lookup"><span data-stu-id="53225-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
