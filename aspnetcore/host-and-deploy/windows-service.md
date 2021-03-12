---
title: 在 Windows 服务中托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 Windows 服务中托管 ASP.NET Core 应用。
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588810"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="0d60e-103">在 Windows 服务中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d60e-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d60e-104">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="0d60e-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="0d60e-105">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="0d60e-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="0d60e-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0d60e-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d60e-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="0d60e-107">Prerequisites</span></span>

* [<span data-ttu-id="0d60e-108">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0d60e-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="0d60e-109">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0d60e-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="0d60e-110">辅助角色服务模板</span><span class="sxs-lookup"><span data-stu-id="0d60e-110">Worker Service template</span></span>

<span data-ttu-id="0d60e-111">ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。</span><span class="sxs-lookup"><span data-stu-id="0d60e-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="0d60e-112">要使用该模板作为编写 Windows 服务应用的基础：</span><span class="sxs-lookup"><span data-stu-id="0d60e-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="0d60e-113">从 .NET Core 模板创建辅助角色服务应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="0d60e-114">按照[应用配置](#app-configuration)部分中的指导来更新辅助角色服务应用，以便它可以作为 Windows 服务运行。</span><span class="sxs-lookup"><span data-stu-id="0d60e-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="0d60e-115">应用配置</span><span class="sxs-lookup"><span data-stu-id="0d60e-115">App configuration</span></span>

<span data-ttu-id="0d60e-116">应用需要 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="0d60e-117">生成主机时会调用 `IHostBuilder.UseWindowsService`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="0d60e-118">若应用作为 Windows 服务运行，方法为：</span><span class="sxs-lookup"><span data-stu-id="0d60e-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="0d60e-119">将主机生存期设置为 `WindowsServiceLifetime`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="0d60e-120">将[内容根](xref:fundamentals/index#content-root)设置为 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="0d60e-121">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="0d60e-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="0d60e-122">为事件日志启用日志记录：</span><span class="sxs-lookup"><span data-stu-id="0d60e-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="0d60e-123">应用名称用作默认源名称。</span><span class="sxs-lookup"><span data-stu-id="0d60e-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="0d60e-124">对于基于 ASP.NET Core 模板且调用 `CreateDefaultBuilder` 生成主机的应用，默认日志级别为“警告”或更高级别。</span><span class="sxs-lookup"><span data-stu-id="0d60e-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="0d60e-125">在 appsettings.json/appsettings.{Environment}.json 或其他配置提供程序中，使用 `Logging:EventLog:LogLevel:Default` 键覆盖默认日志级别。</span><span class="sxs-lookup"><span data-stu-id="0d60e-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="0d60e-126">只有管理员可以创建新的事件源。</span><span class="sxs-lookup"><span data-stu-id="0d60e-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="0d60e-127">无法使用应用程序名称创建事件源时，应用程序源将记录一条警告，并禁用事件源。</span><span class="sxs-lookup"><span data-stu-id="0d60e-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="0d60e-128">在 Program.cs 的 `CreateHostBuilder` 中：</span><span class="sxs-lookup"><span data-stu-id="0d60e-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="0d60e-129">本主题附带了以下示例应用：</span><span class="sxs-lookup"><span data-stu-id="0d60e-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="0d60e-130">后台辅助角色服务示例：一个基于[辅助角色服务模板](#worker-service-template)的非 Web 应用示例，该模板使用[托管服务](xref:fundamentals/host/hosted-services)执行后台任务。</span><span class="sxs-lookup"><span data-stu-id="0d60e-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="0d60e-131">Web 应用服务示例：一个作为 Windows 服务运行的 Razor Pages Web 应用示例，通过[托管服务](xref:fundamentals/host/hosted-services)执行后台任务。</span><span class="sxs-lookup"><span data-stu-id="0d60e-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="0d60e-132">有关 MVC 指南，请参阅 <xref:mvc/overview> 和 <xref:migration/22-to-30> 下的文章。</span><span class="sxs-lookup"><span data-stu-id="0d60e-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="0d60e-133">部署类型</span><span class="sxs-lookup"><span data-stu-id="0d60e-133">Deployment type</span></span>

<span data-ttu-id="0d60e-134">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="0d60e-135">SDK</span><span class="sxs-lookup"><span data-stu-id="0d60e-135">SDK</span></span>

<span data-ttu-id="0d60e-136">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="0d60e-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="0d60e-137">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="0d60e-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="0d60e-138">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="0d60e-139">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="0d60e-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="0d60e-140">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="0d60e-141">如果使用 [Web SDK](#sdk)，则 web.config 文件（通常在发布 ASP.NET Core 应用时生成）不是 Windows 服务应用的必要文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="0d60e-142">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="0d60e-143">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="0d60e-144">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="0d60e-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="0d60e-145">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="0d60e-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="0d60e-146">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="0d60e-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="0d60e-147">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="0d60e-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="0d60e-148">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="0d60e-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="0d60e-149">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="0d60e-150">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="0d60e-151">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="0d60e-151">Service user account</span></span>

<span data-ttu-id="0d60e-152">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="0d60e-153">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="0d60e-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-154">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="0d60e-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="0d60e-155">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="0d60e-156">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="0d60e-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="0d60e-157">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="0d60e-158">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="0d60e-159">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="0d60e-160">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="0d60e-160">Log on as a service rights</span></span>

<span data-ttu-id="0d60e-161">为服务用户帐户创建“以服务身份登录”权限：</span><span class="sxs-lookup"><span data-stu-id="0d60e-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="0d60e-162">通过运行 secpool.msc，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="0d60e-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="0d60e-163">展开“本地策略”节点，选择“用户权限分配”。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="0d60e-164">打开“以服务身份登录”策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="0d60e-165">选择“添加用户或组”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="0d60e-166">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="0d60e-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="0d60e-167">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="0d60e-168">选择“高级”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-168">Select **Advanced**.</span></span> <span data-ttu-id="0d60e-169">选择“开始查找”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-169">Select **Find Now**.</span></span> <span data-ttu-id="0d60e-170">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-170">Select the user account from the list.</span></span> <span data-ttu-id="0d60e-171">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-171">Select **OK**.</span></span> <span data-ttu-id="0d60e-172">再次选择“确定”，以将该用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="0d60e-173">选择“确定”或“应用”，以接受更改。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="0d60e-174">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="0d60e-175">创建服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-175">Create a service</span></span>

<span data-ttu-id="0d60e-176">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="0d60e-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="0d60e-177">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0d60e-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="0d60e-178">`{EXE PATH}`：应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="0d60e-179">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="0d60e-180">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="0d60e-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="0d60e-181">`{DOMAIN OR COMPUTER NAME\USER}`：服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="0d60e-182">`{SERVICE NAME}`：服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="0d60e-183">`{EXE FILE PATH}`：应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="0d60e-184">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="0d60e-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="0d60e-185">`{DESCRIPTION}`：服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="0d60e-186">`{DISPLAY NAME}`：服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="0d60e-187">启动服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-187">Start a service</span></span>

<span data-ttu-id="0d60e-188">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-189">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="0d60e-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="0d60e-190">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="0d60e-190">Determine a service's status</span></span>

<span data-ttu-id="0d60e-191">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="0d60e-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-192">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="0d60e-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="0d60e-193">停止服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-193">Stop a service</span></span>

<span data-ttu-id="0d60e-194">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="0d60e-195">删除服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-195">Remove a service</span></span>

<span data-ttu-id="0d60e-196">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0d60e-197">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="0d60e-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0d60e-198">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="0d60e-199">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="0d60e-200">配置终结点</span><span class="sxs-lookup"><span data-stu-id="0d60e-200">Configure endpoints</span></span>

<span data-ttu-id="0d60e-201">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="0d60e-202">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="0d60e-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="0d60e-203">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="0d60e-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="0d60e-204">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="0d60e-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="0d60e-205">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="0d60e-206">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="0d60e-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="0d60e-207">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="0d60e-207">Current directory and content root</span></span>

<span data-ttu-id="0d60e-208">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory%2A> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0d60e-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory%2A> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="0d60e-209">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="0d60e-210">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="0d60e-211">使用 ContentRootPath 或 ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="0d60e-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="0d60e-212">使用 [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 或 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> 查找应用的资源。</span><span class="sxs-lookup"><span data-stu-id="0d60e-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="0d60e-213">应用作为服务运行时，<xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> 将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> 设置为 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="0d60e-214">通过调用 [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host)，从应用的内容根加载应用的默认设置文件 appsettings.json 和 appsettings.{Environment}.json。</span><span class="sxs-lookup"><span data-stu-id="0d60e-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="0d60e-215">对于 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 中的开发人员代码加载的其他设置文件，无需调用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>.</span></span> <span data-ttu-id="0d60e-216">在下面的示例中，custom_settings.json 文件位于应用的内容根，加载它时未显式设置基本路径：</span><span class="sxs-lookup"><span data-stu-id="0d60e-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="0d60e-217">请勿尝试使用 <xref:System.IO.Directory.GetCurrentDirectory%2A> 来获取资源路径，因为 Windows 服务应用会返回“C:\\WINDOWS\\system32”文件夹作为其当前目录。</span><span class="sxs-lookup"><span data-stu-id="0d60e-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory%2A> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="0d60e-218">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="0d60e-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="0d60e-219">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0d60e-220">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0d60e-220">Troubleshoot</span></span>

<span data-ttu-id="0d60e-221">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="0d60e-222">常见错误</span><span class="sxs-lookup"><span data-stu-id="0d60e-222">Common errors</span></span>

* <span data-ttu-id="0d60e-223">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="0d60e-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="0d60e-224">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。</span><span class="sxs-lookup"><span data-stu-id="0d60e-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="0d60e-225">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="0d60e-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="0d60e-226">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="0d60e-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="0d60e-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="0d60e-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="0d60e-229">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="0d60e-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="0d60e-230">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="0d60e-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="0d60e-231">Windows 服务的基本路径是“c:\\Windows\\System32”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="0d60e-232">用户没有“以服务身份登录”权限。</span><span class="sxs-lookup"><span data-stu-id="0d60e-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="0d60e-233">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="0d60e-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="0d60e-234">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="0d60e-235">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="0d60e-236">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="0d60e-236">System and Application Event Logs</span></span>

<span data-ttu-id="0d60e-237">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="0d60e-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="0d60e-238">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="0d60e-239">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="0d60e-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="0d60e-240">选择“系统”，以打开系统事件日志。</span><span class="sxs-lookup"><span data-stu-id="0d60e-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="0d60e-241">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="0d60e-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="0d60e-242">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="0d60e-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="0d60e-243">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="0d60e-243">Run the app at a command prompt</span></span>

<span data-ttu-id="0d60e-244">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="0d60e-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="0d60e-245">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="0d60e-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="0d60e-246">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="0d60e-247">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="0d60e-247">Clear package caches</span></span>

<span data-ttu-id="0d60e-248">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="0d60e-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="0d60e-249">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="0d60e-250">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="0d60e-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="0d60e-251">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0d60e-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="0d60e-252">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="0d60e-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="0d60e-253">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="0d60e-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="0d60e-254">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="0d60e-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="0d60e-255">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="0d60e-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="0d60e-256">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="0d60e-257">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="0d60e-257">Slow or hanging app</span></span>

<span data-ttu-id="0d60e-258">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="0d60e-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="0d60e-259">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="0d60e-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="0d60e-260">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="0d60e-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="0d60e-261">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="0d60e-262">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="0d60e-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="0d60e-263">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="0d60e-264">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="0d60e-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="0d60e-265">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="0d60e-266">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="0d60e-267">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="0d60e-268">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="0d60e-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="0d60e-269">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="0d60e-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="0d60e-270">分析转储</span><span class="sxs-lookup"><span data-stu-id="0d60e-270">Analyze the dump</span></span>

<span data-ttu-id="0d60e-271">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="0d60e-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="0d60e-272">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d60e-273">其他资源</span><span class="sxs-lookup"><span data-stu-id="0d60e-273">Additional resources</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* <span data-ttu-id="0d60e-274">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel/endpoints)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="0d60e-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* <span data-ttu-id="0d60e-275">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="0d60e-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="0d60e-276">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="0d60e-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="0d60e-277">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="0d60e-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="0d60e-278">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0d60e-278">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d60e-279">先决条件</span><span class="sxs-lookup"><span data-stu-id="0d60e-279">Prerequisites</span></span>

* [<span data-ttu-id="0d60e-280">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0d60e-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="0d60e-281">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0d60e-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="0d60e-282">应用配置</span><span class="sxs-lookup"><span data-stu-id="0d60e-282">App configuration</span></span>

<span data-ttu-id="0d60e-283">应用需要引用 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 包。</span><span class="sxs-lookup"><span data-stu-id="0d60e-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="0d60e-284">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="0d60e-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="0d60e-285">检查是否已连接调试器或是否存在 `--console` 开关。</span><span class="sxs-lookup"><span data-stu-id="0d60e-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="0d60e-286">如果其中一个条件为 true（应用不作为服务运行），请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="0d60e-287">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="0d60e-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="0d60e-288">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="0d60e-289">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0d60e-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="0d60e-290">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="0d60e-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="0d60e-291">请先执行此步骤，然后再在 `CreateWebHostBuilder` 中配置应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="0d60e-292">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="0d60e-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="0d60e-293">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="0d60e-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="0d60e-294">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="0d60e-295">使用 appsettings.Production.json文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="0d60e-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="0d60e-296">示例应用中的以下示例调用了 `RunAsCustomService`，来代替 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>，以处理应用内的生存期事件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="0d60e-297">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="0d60e-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="0d60e-298">部署类型</span><span class="sxs-lookup"><span data-stu-id="0d60e-298">Deployment type</span></span>

<span data-ttu-id="0d60e-299">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="0d60e-300">SDK</span><span class="sxs-lookup"><span data-stu-id="0d60e-300">SDK</span></span>

<span data-ttu-id="0d60e-301">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="0d60e-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="0d60e-302">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="0d60e-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="0d60e-303">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="0d60e-304">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="0d60e-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="0d60e-305">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="0d60e-306">Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。</span><span class="sxs-lookup"><span data-stu-id="0d60e-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="0d60e-307">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="0d60e-308">`<SelfContained>` 属性设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="0d60e-309">这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe) 以及一个依赖共享 NET Core 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="0d60e-310">web.config文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="0d60e-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="0d60e-311">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="0d60e-312">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="0d60e-313">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="0d60e-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="0d60e-314">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="0d60e-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="0d60e-315">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="0d60e-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="0d60e-316">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="0d60e-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="0d60e-317">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="0d60e-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="0d60e-318">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="0d60e-319">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="0d60e-320">将 `<SelfContained>` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="0d60e-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="0d60e-321">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="0d60e-321">Service user account</span></span>

<span data-ttu-id="0d60e-322">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="0d60e-323">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="0d60e-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-324">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="0d60e-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="0d60e-325">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="0d60e-326">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="0d60e-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="0d60e-327">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="0d60e-328">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="0d60e-329">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="0d60e-330">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="0d60e-330">Log on as a service rights</span></span>

<span data-ttu-id="0d60e-331">为服务用户帐户创建“以服务身份登录”权限：</span><span class="sxs-lookup"><span data-stu-id="0d60e-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="0d60e-332">通过运行 secpool.msc，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="0d60e-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="0d60e-333">展开“本地策略”节点，选择“用户权限分配”。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="0d60e-334">打开“以服务身份登录”策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="0d60e-335">选择“添加用户或组”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="0d60e-336">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="0d60e-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="0d60e-337">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="0d60e-338">选择“高级”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-338">Select **Advanced**.</span></span> <span data-ttu-id="0d60e-339">选择“开始查找”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-339">Select **Find Now**.</span></span> <span data-ttu-id="0d60e-340">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-340">Select the user account from the list.</span></span> <span data-ttu-id="0d60e-341">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-341">Select **OK**.</span></span> <span data-ttu-id="0d60e-342">再次选择“确定”，以将该用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="0d60e-343">选择“确定”或“应用”，以接受更改。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="0d60e-344">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="0d60e-345">创建服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-345">Create a service</span></span>

<span data-ttu-id="0d60e-346">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="0d60e-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="0d60e-347">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0d60e-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="0d60e-348">`{EXE PATH}`：应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-348">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="0d60e-349">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="0d60e-350">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="0d60e-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="0d60e-351">`{DOMAIN OR COMPUTER NAME\USER}`：服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-351">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="0d60e-352">`{SERVICE NAME}`：服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-352">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="0d60e-353">`{EXE FILE PATH}`：应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-353">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="0d60e-354">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="0d60e-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="0d60e-355">`{DESCRIPTION}`：服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-355">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="0d60e-356">`{DISPLAY NAME}`：服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-356">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="0d60e-357">启动服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-357">Start a service</span></span>

<span data-ttu-id="0d60e-358">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-359">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="0d60e-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="0d60e-360">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="0d60e-360">Determine a service's status</span></span>

<span data-ttu-id="0d60e-361">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="0d60e-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-362">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="0d60e-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="0d60e-363">停止服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-363">Stop a service</span></span>

<span data-ttu-id="0d60e-364">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="0d60e-365">删除服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-365">Remove a service</span></span>

<span data-ttu-id="0d60e-366">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="0d60e-367">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="0d60e-367">Handle starting and stopping events</span></span>

<span data-ttu-id="0d60e-368">处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="0d60e-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="0d60e-369">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="0d60e-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="0d60e-370">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="0d60e-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="0d60e-371">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="0d60e-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="0d60e-372">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[部署类型](#deployment-type)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="0d60e-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0d60e-373">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="0d60e-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0d60e-374">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="0d60e-375">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="0d60e-376">配置终结点</span><span class="sxs-lookup"><span data-stu-id="0d60e-376">Configure endpoints</span></span>

<span data-ttu-id="0d60e-377">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="0d60e-378">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="0d60e-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="0d60e-379">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="0d60e-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="0d60e-380">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="0d60e-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="0d60e-381">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="0d60e-382">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="0d60e-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="0d60e-383">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="0d60e-383">Current directory and content root</span></span>

<span data-ttu-id="0d60e-384">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0d60e-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="0d60e-385">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="0d60e-386">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="0d60e-387">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="0d60e-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="0d60e-388"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="0d60e-389">请调用包含应用[内容根目录](xref:fundamentals/index#content-root)的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="0d60e-390">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="0d60e-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="0d60e-391">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="0d60e-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="0d60e-392">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0d60e-393">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0d60e-393">Troubleshoot</span></span>

<span data-ttu-id="0d60e-394">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="0d60e-395">常见错误</span><span class="sxs-lookup"><span data-stu-id="0d60e-395">Common errors</span></span>

* <span data-ttu-id="0d60e-396">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="0d60e-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="0d60e-397">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。</span><span class="sxs-lookup"><span data-stu-id="0d60e-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="0d60e-398">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="0d60e-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="0d60e-399">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="0d60e-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="0d60e-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="0d60e-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="0d60e-402">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="0d60e-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="0d60e-403">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="0d60e-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="0d60e-404">Windows 服务的基本路径是“c:\\Windows\\System32”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="0d60e-405">用户没有“以服务身份登录”权限。</span><span class="sxs-lookup"><span data-stu-id="0d60e-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="0d60e-406">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="0d60e-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="0d60e-407">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="0d60e-408">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="0d60e-409">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="0d60e-409">System and Application Event Logs</span></span>

<span data-ttu-id="0d60e-410">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="0d60e-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="0d60e-411">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="0d60e-412">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="0d60e-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="0d60e-413">选择“系统”，以打开系统事件日志。</span><span class="sxs-lookup"><span data-stu-id="0d60e-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="0d60e-414">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="0d60e-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="0d60e-415">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="0d60e-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="0d60e-416">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="0d60e-416">Run the app at a command prompt</span></span>

<span data-ttu-id="0d60e-417">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="0d60e-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="0d60e-418">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="0d60e-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="0d60e-419">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="0d60e-420">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="0d60e-420">Clear package caches</span></span>

<span data-ttu-id="0d60e-421">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="0d60e-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="0d60e-422">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="0d60e-423">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="0d60e-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="0d60e-424">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0d60e-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="0d60e-425">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="0d60e-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="0d60e-426">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="0d60e-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="0d60e-427">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="0d60e-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="0d60e-428">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="0d60e-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="0d60e-429">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="0d60e-430">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="0d60e-430">Slow or hanging app</span></span>

<span data-ttu-id="0d60e-431">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="0d60e-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="0d60e-432">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="0d60e-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="0d60e-433">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="0d60e-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="0d60e-434">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="0d60e-435">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="0d60e-435">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="0d60e-436">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="0d60e-437">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="0d60e-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="0d60e-438">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="0d60e-439">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="0d60e-440">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="0d60e-441">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="0d60e-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="0d60e-442">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="0d60e-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="0d60e-443">分析转储</span><span class="sxs-lookup"><span data-stu-id="0d60e-443">Analyze the dump</span></span>

<span data-ttu-id="0d60e-444">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="0d60e-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="0d60e-445">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d60e-446">其他资源</span><span class="sxs-lookup"><span data-stu-id="0d60e-446">Additional resources</span></span>

* <span data-ttu-id="0d60e-447">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="0d60e-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0d60e-448">不使用 IIS 时，可以在 Windows 上将 ASP.NET Core 应用作为 [Windows 服务](/dotnet/framework/windows-services/introduction-to-windows-service-applications)进行托管。</span><span class="sxs-lookup"><span data-stu-id="0d60e-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="0d60e-449">作为 Windows 服务进行托管时，应用将在服务器重新启动后自动启动。</span><span class="sxs-lookup"><span data-stu-id="0d60e-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="0d60e-450">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0d60e-450">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d60e-451">先决条件</span><span class="sxs-lookup"><span data-stu-id="0d60e-451">Prerequisites</span></span>

* [<span data-ttu-id="0d60e-452">ASP.NET .NET Core SDK 2.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0d60e-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="0d60e-453">PowerShell 6.2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="0d60e-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="0d60e-454">应用配置</span><span class="sxs-lookup"><span data-stu-id="0d60e-454">App configuration</span></span>

<span data-ttu-id="0d60e-455">应用需要引用 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 和 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 包。</span><span class="sxs-lookup"><span data-stu-id="0d60e-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="0d60e-456">在服务之外运行时，若要进行测试和调试，请添加代码以确定应用是否作为服务或控制台应用运行。</span><span class="sxs-lookup"><span data-stu-id="0d60e-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="0d60e-457">检查是否已连接调试器或是否存在 `--console` 开关。</span><span class="sxs-lookup"><span data-stu-id="0d60e-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="0d60e-458">如果其中一个条件为 true（应用不作为服务运行），请调用 <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="0d60e-459">如果条件为 false（应用作为服务运行）：</span><span class="sxs-lookup"><span data-stu-id="0d60e-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="0d60e-460">调用 <xref:System.IO.Directory.SetCurrentDirectory*> 并使用应用的发布位置路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="0d60e-461">不要调用 <xref:System.IO.Directory.GetCurrentDirectory*> 来获取路径，因为在调用 <xref:System.IO.Directory.GetCurrentDirectory*> 时，Windows 服务应用将返回 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0d60e-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="0d60e-462">有关详细信息，请参阅[当前目录和内容根](#current-directory-and-content-root)部分。</span><span class="sxs-lookup"><span data-stu-id="0d60e-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="0d60e-463">请先执行此步骤，然后再在 `CreateWebHostBuilder` 中配置应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="0d60e-464">调用 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 以将应用作为服务运行。</span><span class="sxs-lookup"><span data-stu-id="0d60e-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="0d60e-465">由于[命令行配置提供程序](xref:fundamentals/configuration/index#command-line-configuration-provider)需要命令行参数的名称/值对，因此将先从参数中删除 `--console` 开关，然后 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 会接收这些参数。</span><span class="sxs-lookup"><span data-stu-id="0d60e-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="0d60e-466">若要写入 Windows 事件日志，请将事件日志提供程序添加到 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="0d60e-467">使用 appsettings.Production.json文件中的 `Logging:LogLevel:Default` 键设置日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="0d60e-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="0d60e-468">示例应用中的以下示例调用了 `RunAsCustomService`，来代替 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>，以处理应用内的生存期事件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="0d60e-469">有关详细信息，请参阅[处理启动和停止事件](#handle-starting-and-stopping-events)部分。</span><span class="sxs-lookup"><span data-stu-id="0d60e-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="0d60e-470">部署类型</span><span class="sxs-lookup"><span data-stu-id="0d60e-470">Deployment type</span></span>

<span data-ttu-id="0d60e-471">有关部署方案的信息和建议，请参阅 [.NET Core 应用程序部署](/dotnet/core/deploying/)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="0d60e-472">SDK</span><span class="sxs-lookup"><span data-stu-id="0d60e-472">SDK</span></span>

<span data-ttu-id="0d60e-473">对于使用 Razor Pages 或 MVC 框架的基于 Web 应用的服务，请在项目文件中指定 Web SDK：</span><span class="sxs-lookup"><span data-stu-id="0d60e-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="0d60e-474">如果服务仅执行后台任务（例如 [托管服务](xref:fundamentals/host/hosted-services)），请在项目文件中指定辅助角色 SDK：</span><span class="sxs-lookup"><span data-stu-id="0d60e-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="0d60e-475">依赖框架的部署 (FDD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="0d60e-476">依赖框架的部署 (FDD) 依赖目标系统上存在共享系统级版本的 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="0d60e-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="0d60e-477">按照本文中的指南采用 FDD 方案时，SDK 会生成一个称为“依赖框架的可执行文件”的可执行文件 (.exe)。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="0d60e-478">Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) 包含目标框架。</span><span class="sxs-lookup"><span data-stu-id="0d60e-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="0d60e-479">在以下示例中，将 RID 设置为 `win7-x64`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="0d60e-480">`<SelfContained>` 属性设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="0d60e-481">这些属性指示 SDK 生成用于 Windows 的可执行文件 (.exe) 以及一个依赖共享 NET Core 框架的应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="0d60e-482">`<UseAppHost>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="0d60e-483">此属性为服务提供 FDD 的激活路径（一个可执行文件，格式为 .exe）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="0d60e-484">web.config文件（通常在发布 ASP.NET Core 应用时生成）对于 Windows 服务应用来说是不必要的。</span><span class="sxs-lookup"><span data-stu-id="0d60e-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="0d60e-485">若要禁止创建 web.config 文件，请将 `<IsTransformWebConfigDisabled>` 属性集添加到 `true`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="0d60e-486">独立部署 (SCD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="0d60e-487">独立部署 (SCD) 不依赖主机系统上存在的共享框架。</span><span class="sxs-lookup"><span data-stu-id="0d60e-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="0d60e-488">运行时和应用的依赖项将与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="0d60e-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="0d60e-489">将 Windows [运行时标识符 (RID)](/dotnet/core/rid-catalog) 添加到包含目标框架的 `<PropertyGroup>` 中：</span><span class="sxs-lookup"><span data-stu-id="0d60e-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="0d60e-490">要发布多个 RID：</span><span class="sxs-lookup"><span data-stu-id="0d60e-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="0d60e-491">通过以分号分隔的列表提供 RID。</span><span class="sxs-lookup"><span data-stu-id="0d60e-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="0d60e-492">使用属性名称 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)（复数）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="0d60e-493">有关详细信息，请参阅 [.NET Core RID 目录](/dotnet/core/rid-catalog)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="0d60e-494">将 `<SelfContained>` 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="0d60e-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="0d60e-495">服务用户帐户</span><span class="sxs-lookup"><span data-stu-id="0d60e-495">Service user account</span></span>

<span data-ttu-id="0d60e-496">在管理 PowerShell 6 命令行界面中运行 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，为服务创建用户帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="0d60e-497">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="0d60e-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-498">对于 Windows 10 2018 年 10 月更新（版本 1809/内部版本 10.0.17763）之前的 Windows 操作系统：</span><span class="sxs-lookup"><span data-stu-id="0d60e-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="0d60e-499">在系统提示时，提供[强密码](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="0d60e-500">除非将 `-AccountExpires` 参数提供给到期时间为 <xref:System.DateTime> 的 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet，否则用户帐户不会到期。</span><span class="sxs-lookup"><span data-stu-id="0d60e-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="0d60e-501">有关详细信息，请参阅 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 和[服务用户帐户](/windows/desktop/services/service-user-accounts)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="0d60e-502">使用 Active Directory 时管理用户的另一种方法是使用托管服务帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="0d60e-503">有关详细信息，请参阅[组托管服务帐户概述](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="0d60e-504">以服务身份登录权限</span><span class="sxs-lookup"><span data-stu-id="0d60e-504">Log on as a service rights</span></span>

<span data-ttu-id="0d60e-505">为服务用户帐户创建“以服务身份登录”权限：</span><span class="sxs-lookup"><span data-stu-id="0d60e-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="0d60e-506">通过运行 secpool.msc，打开本地安全策略编辑器。</span><span class="sxs-lookup"><span data-stu-id="0d60e-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="0d60e-507">展开“本地策略”节点，选择“用户权限分配”。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="0d60e-508">打开“以服务身份登录”策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="0d60e-509">选择“添加用户或组”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="0d60e-510">使用下列方法之一提供对象名称（用户帐户）：</span><span class="sxs-lookup"><span data-stu-id="0d60e-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="0d60e-511">在对象名称字段键入用户帐户 (`{DOMAIN OR COMPUTER NAME\USER}`)，然后选择“确定”，以将此用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="0d60e-512">选择“高级”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-512">Select **Advanced**.</span></span> <span data-ttu-id="0d60e-513">选择“开始查找”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-513">Select **Find Now**.</span></span> <span data-ttu-id="0d60e-514">从列表中选择该用户帐户。</span><span class="sxs-lookup"><span data-stu-id="0d60e-514">Select the user account from the list.</span></span> <span data-ttu-id="0d60e-515">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-515">Select **OK**.</span></span> <span data-ttu-id="0d60e-516">再次选择“确定”，以将该用户添加到策略。</span><span class="sxs-lookup"><span data-stu-id="0d60e-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="0d60e-517">选择“确定”或“应用”，以接受更改。 </span><span class="sxs-lookup"><span data-stu-id="0d60e-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="0d60e-518">创建和管理 Windows 服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="0d60e-519">创建服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-519">Create a service</span></span>

<span data-ttu-id="0d60e-520">使用 PowerShell 注册服务。</span><span class="sxs-lookup"><span data-stu-id="0d60e-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="0d60e-521">从 PowerShell 6 命令行界面，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0d60e-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="0d60e-522">`{EXE PATH}`：应用在主机上的文件夹的路径（如 `d:\myservice`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-522">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="0d60e-523">请勿在此路径中包含应用的可执行文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="0d60e-524">尾部反斜杠是非必需项。</span><span class="sxs-lookup"><span data-stu-id="0d60e-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="0d60e-525">`{DOMAIN OR COMPUTER NAME\USER}`：服务用户帐户（如 `Contoso\ServiceUser`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-525">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="0d60e-526">`{SERVICE NAME}`：服务名称（如 `MyService`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-526">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="0d60e-527">`{EXE FILE PATH}`：应用的可执行文件路径（如 `d:\myservice\myservice.exe`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-527">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="0d60e-528">请将可执行文件的文件名和扩展名包括在内。</span><span class="sxs-lookup"><span data-stu-id="0d60e-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="0d60e-529">`{DESCRIPTION}`：服务说明（如 `My sample service`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-529">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="0d60e-530">`{DISPLAY NAME}`：服务显示名称（如 `My Service`）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-530">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="0d60e-531">启动服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-531">Start a service</span></span>

<span data-ttu-id="0d60e-532">使用以下 PowerShell 6 命令启动服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-533">此命令需要几秒钟才能启动服务。</span><span class="sxs-lookup"><span data-stu-id="0d60e-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="0d60e-534">确信服务状态</span><span class="sxs-lookup"><span data-stu-id="0d60e-534">Determine a service's status</span></span>

<span data-ttu-id="0d60e-535">要检查服务的状态，请使用以下 PowerShell 6 命令：</span><span class="sxs-lookup"><span data-stu-id="0d60e-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="0d60e-536">状态报告为以下值之一：</span><span class="sxs-lookup"><span data-stu-id="0d60e-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="0d60e-537">停止服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-537">Stop a service</span></span>

<span data-ttu-id="0d60e-538">使用以下 Powershell 6 命令停止服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="0d60e-539">删除服务</span><span class="sxs-lookup"><span data-stu-id="0d60e-539">Remove a service</span></span>

<span data-ttu-id="0d60e-540">在停止服务一小段时间后，使用以下 Powershell 6 命令删除服务：</span><span class="sxs-lookup"><span data-stu-id="0d60e-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="0d60e-541">处理启动和停止事件</span><span class="sxs-lookup"><span data-stu-id="0d60e-541">Handle starting and stopping events</span></span>

<span data-ttu-id="0d60e-542">处理 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>、<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 和 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 事件：</span><span class="sxs-lookup"><span data-stu-id="0d60e-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="0d60e-543">使用 `OnStarting`、`OnStarted` 和 `OnStopping` 方法创建从 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> 派生的类：</span><span class="sxs-lookup"><span data-stu-id="0d60e-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="0d60e-544">创建可将 `CustomWebHostService` 传递给 <xref:System.ServiceProcess.ServiceBase.Run*> 的 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="0d60e-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="0d60e-545">在 `Program.Main` 中，调用 `RunAsCustomService` 扩展方法，而不是 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>：</span><span class="sxs-lookup"><span data-stu-id="0d60e-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="0d60e-546">若要在 `Program.Main` 中查看 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 的位置，请参阅[部署类型](#deployment-type)部分中所示的代码示例。</span><span class="sxs-lookup"><span data-stu-id="0d60e-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0d60e-547">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="0d60e-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0d60e-548">与来自 Internet 或公司网络的请求进行交互且在代理或负载均衡器后方的服务可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="0d60e-549">有关详细信息，请参阅 <xref:host-and-deploy/proxy-load-balancer>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="0d60e-550">配置终结点</span><span class="sxs-lookup"><span data-stu-id="0d60e-550">Configure endpoints</span></span>

<span data-ttu-id="0d60e-551">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="0d60e-552">通过设置 `ASPNETCORE_URLS` 环境变量来配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="0d60e-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="0d60e-553">有关其他 URL 和端口配置方法，请参阅相关服务器文章：</span><span class="sxs-lookup"><span data-stu-id="0d60e-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="0d60e-554">上述指南介绍了对 HTTPS 终结点的支持。</span><span class="sxs-lookup"><span data-stu-id="0d60e-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="0d60e-555">例如，在使用 Windows 服务进行身份验证时，为 HTTPS 配置应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="0d60e-556">不支持使用 ASP.NET Core HTTPS 开发证书保护服务终结点。</span><span class="sxs-lookup"><span data-stu-id="0d60e-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="0d60e-557">当前目录和内容根</span><span class="sxs-lookup"><span data-stu-id="0d60e-557">Current directory and content root</span></span>

<span data-ttu-id="0d60e-558">通过为 Windows 服务调用 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的当前工作目录是 C:\\WINDOWS\\system32 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0d60e-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="0d60e-559">system32 文件夹不是存储服务文件（如设置文件）的合适位置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="0d60e-560">使用以下方法之一来维护和访问服务的资产和设置文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="0d60e-561">设置应用文件夹的内容根路径</span><span class="sxs-lookup"><span data-stu-id="0d60e-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="0d60e-562"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> 是创建服务时提供给 `binPath` 参数的同一路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="0d60e-563">请调用包含应用[内容根目录](xref:fundamentals/index#content-root)的路径的 <xref:System.IO.Directory.SetCurrentDirectory*>，而不是调用 `GetCurrentDirectory` 来创建设置文件的路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="0d60e-564">在 `Program.Main` 中，确定服务可执行文件的文件夹路径，并使用该路径来建立应用的内容根：</span><span class="sxs-lookup"><span data-stu-id="0d60e-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="0d60e-565">将服务的文件存储在磁盘中的合适位置</span><span class="sxs-lookup"><span data-stu-id="0d60e-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="0d60e-566">使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 时，使用 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> 指定到包含文件的文件夹的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="0d60e-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0d60e-567">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0d60e-567">Troubleshoot</span></span>

<span data-ttu-id="0d60e-568">若要排除 Windows 服务应用的故障，请参阅 <xref:test/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="0d60e-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="0d60e-569">常见错误</span><span class="sxs-lookup"><span data-stu-id="0d60e-569">Common errors</span></span>

* <span data-ttu-id="0d60e-570">正在使用 PowerShell 的早期版本或预发布版本。</span><span class="sxs-lookup"><span data-stu-id="0d60e-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="0d60e-571">注册的服务未使用来自 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令的应用的已发布输出。</span><span class="sxs-lookup"><span data-stu-id="0d60e-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="0d60e-572">应用部署不支持 [dotnet build](/dotnet/core/tools/dotnet-build) 命令的输出。</span><span class="sxs-lookup"><span data-stu-id="0d60e-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="0d60e-573">已发布的资产位于以下文件夹之一中，具体取决于部署类型：</span><span class="sxs-lookup"><span data-stu-id="0d60e-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="0d60e-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="0d60e-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="0d60e-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="0d60e-576">服务未处于“正在运行”状态。</span><span class="sxs-lookup"><span data-stu-id="0d60e-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="0d60e-577">应用使用的资源（例如证书）的路径不正确。</span><span class="sxs-lookup"><span data-stu-id="0d60e-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="0d60e-578">Windows 服务的基本路径是“c:\\Windows\\System32”。</span><span class="sxs-lookup"><span data-stu-id="0d60e-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="0d60e-579">用户没有“以服务身份登录”权限。</span><span class="sxs-lookup"><span data-stu-id="0d60e-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="0d60e-580">在执行 `New-Service` PowerShell 命令时，用户密码已过期，或以不正确的方式传递。</span><span class="sxs-lookup"><span data-stu-id="0d60e-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="0d60e-581">应用需要进行 ASP.NET Core 身份验证，但是未配置安全连接 (HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="0d60e-582">请求 URL 端口不正确或未在应用中正确配置。</span><span class="sxs-lookup"><span data-stu-id="0d60e-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="0d60e-583">系统和应用程序事件日志</span><span class="sxs-lookup"><span data-stu-id="0d60e-583">System and Application Event Logs</span></span>

<span data-ttu-id="0d60e-584">访问系统和应用程序事件日志：</span><span class="sxs-lookup"><span data-stu-id="0d60e-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="0d60e-585">打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="0d60e-586">在“事件查看器”中，打开“Windows 日志”节点。</span><span class="sxs-lookup"><span data-stu-id="0d60e-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="0d60e-587">选择“系统”，以打开系统事件日志。</span><span class="sxs-lookup"><span data-stu-id="0d60e-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="0d60e-588">选择“应用程序”以打开应用程序事件日志。</span><span class="sxs-lookup"><span data-stu-id="0d60e-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="0d60e-589">搜索与失败应用相关联的错误。</span><span class="sxs-lookup"><span data-stu-id="0d60e-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="0d60e-590">在命令提示符处运行应用</span><span class="sxs-lookup"><span data-stu-id="0d60e-590">Run the app at a command prompt</span></span>

<span data-ttu-id="0d60e-591">许多启动错误未在事件日志中生成有用信息。</span><span class="sxs-lookup"><span data-stu-id="0d60e-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="0d60e-592">可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。</span><span class="sxs-lookup"><span data-stu-id="0d60e-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="0d60e-593">若要记录应用中的其他详细信息，则降低[日志级别](xref:fundamentals/logging/index#log-level)，或在[开发环境](xref:fundamentals/environments)中运行此应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="0d60e-594">清除包缓存</span><span class="sxs-lookup"><span data-stu-id="0d60e-594">Clear package caches</span></span>

<span data-ttu-id="0d60e-595">正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内更改包版本后可能会立即出现故障。</span><span class="sxs-lookup"><span data-stu-id="0d60e-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="0d60e-596">在某些情况下，不同的包可能在执行主要升级时中断应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="0d60e-597">可以按照以下说明来修复其中大部分问题：</span><span class="sxs-lookup"><span data-stu-id="0d60e-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="0d60e-598">删除 bin 和 obj 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0d60e-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="0d60e-599">通过从命令行界面执行 [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) 清除包缓存。</span><span class="sxs-lookup"><span data-stu-id="0d60e-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="0d60e-600">清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。</span><span class="sxs-lookup"><span data-stu-id="0d60e-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="0d60e-601">*nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。</span><span class="sxs-lookup"><span data-stu-id="0d60e-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="0d60e-602">还原并重新生成项目。</span><span class="sxs-lookup"><span data-stu-id="0d60e-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="0d60e-603">在重新部署应用前，在服务器上删除部署文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="0d60e-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="0d60e-604">应用缓慢或挂起</span><span class="sxs-lookup"><span data-stu-id="0d60e-604">Slow or hanging app</span></span>

<span data-ttu-id="0d60e-605">故障转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。</span><span class="sxs-lookup"><span data-stu-id="0d60e-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="0d60e-606">应用崩溃或引发异常</span><span class="sxs-lookup"><span data-stu-id="0d60e-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="0d60e-607">从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：</span><span class="sxs-lookup"><span data-stu-id="0d60e-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="0d60e-608">创建文件夹，将崩溃转储文件保存在 `c:\dumps`。</span><span class="sxs-lookup"><span data-stu-id="0d60e-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="0d60e-609">使用应用程序可执行文件名称运行 [EnableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="0d60e-609">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="0d60e-610">在造成崩溃的条件下运行应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="0d60e-611">出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)：</span><span class="sxs-lookup"><span data-stu-id="0d60e-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="0d60e-612">在应用崩溃并完成转储收集后，即可正常终止应用。</span><span class="sxs-lookup"><span data-stu-id="0d60e-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="0d60e-613">PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="0d60e-614">崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。</span><span class="sxs-lookup"><span data-stu-id="0d60e-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="0d60e-615">应用挂起、在启动期间失败或正常运行</span><span class="sxs-lookup"><span data-stu-id="0d60e-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="0d60e-616">如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行 *hangs*，请参阅 [用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。</span><span class="sxs-lookup"><span data-stu-id="0d60e-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="0d60e-617">分析转储</span><span class="sxs-lookup"><span data-stu-id="0d60e-617">Analyze the dump</span></span>

<span data-ttu-id="0d60e-618">可采用几种方法来分析转储。</span><span class="sxs-lookup"><span data-stu-id="0d60e-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="0d60e-619">有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。</span><span class="sxs-lookup"><span data-stu-id="0d60e-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d60e-620">其他资源</span><span class="sxs-lookup"><span data-stu-id="0d60e-620">Additional resources</span></span>

* <span data-ttu-id="0d60e-621">[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)（包括 HTTPS 配置和 SNI 支持）</span><span class="sxs-lookup"><span data-stu-id="0d60e-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
