---
title: ASP.NET Core Blazor 环境
author: guardrex
description: 了解 Blazor 中的环境，包括如何设置 Blazor WebAssembly 应用的环境。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 86484fe2e2ce8e20e397a78f118d8b1bbe0cc88a
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395105"
---
# <a name="aspnet-core-blazor-environments"></a><span data-ttu-id="8f893-103">ASP.NET Core Blazor 环境</span><span class="sxs-lookup"><span data-stu-id="8f893-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="8f893-104">本主题适用于 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="8f893-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="8f893-105">有关 ASP.NET Core 应用配置的常规指导（其中描述了对 Blazor Server应用使用的方法），请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8f893-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8f893-106">在本地运行应用时，环境默认为开发环境。</span><span class="sxs-lookup"><span data-stu-id="8f893-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="8f893-107">发布应用时，环境默认为生产环境。</span><span class="sxs-lookup"><span data-stu-id="8f893-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="8f893-108">托管的 Blazor WebAssembly 解决方案的客户端 Blazor 应用 (`Client`) 通过将环境传递到浏览器的中间件，来根据解决方案的 `Server` 应用确定环境 。</span><span class="sxs-lookup"><span data-stu-id="8f893-108">The client-side Blazor app (**`Client`**) of a hosted Blazor WebAssembly solution determines the environment from the **`Server`** app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="8f893-109">`Server` 应用使用该环境作为标头值来添加一个名为 `blazor-environment` 的标头。</span><span class="sxs-lookup"><span data-stu-id="8f893-109">The **`Server`** app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="8f893-110">`Client` 应用读取标头。</span><span class="sxs-lookup"><span data-stu-id="8f893-110">The **`Client`** app reads the header.</span></span> <span data-ttu-id="8f893-111">解决方案的 `Server` 应用是一个 ASP.NET Core 应用；若要详细了解如何配置环境，可查看 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="8f893-111">The **`Server`** app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="8f893-112">对于在本地运行的独立 Blazor WebAssembly 应用，开发服务器会添加 `blazor-environment` 标头来指定开发环境。</span><span class="sxs-lookup"><span data-stu-id="8f893-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="8f893-113">要为其他宿主环境指定环境，请添加 `blazor-environment` 标头。</span><span class="sxs-lookup"><span data-stu-id="8f893-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="8f893-114">在下面的 IIS 示例中，将自定义标头 (`blazor-environment`) 添加到已发布的 `web.config` 文件中。</span><span class="sxs-lookup"><span data-stu-id="8f893-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="8f893-115">`web.config` 文件位于 `bin/Release/{TARGET FRAMEWORK}/publish` 文件夹中，占位符 `{TARGET FRAMEWORK}` 为目标框架：</span><span class="sxs-lookup"><span data-stu-id="8f893-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="8f893-116">要对 IIS 使用在将应用发布到 `publish` 文件夹时不会被覆盖的自定义 `web.config` 文件，请参阅 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="8f893-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="8f893-117">通过注入 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 并读取 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 属性，在组件中获取应用的环境。</span><span class="sxs-lookup"><span data-stu-id="8f893-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="8f893-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="8f893-118">`Pages/ReadEnvironment.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

<span data-ttu-id="8f893-119">在启动过程中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> 会通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 属性公开 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>，这会在主机生成器代码中启用环境特定的逻辑。</span><span class="sxs-lookup"><span data-stu-id="8f893-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="8f893-120">在 `Program.cs` 的 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="8f893-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="8f893-121">使用通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> 提供的下列便捷扩展方法，可在当前环境中检查开发环境、生产环境、暂存环境和自定义环境名称：</span><span class="sxs-lookup"><span data-stu-id="8f893-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="8f893-122">在 `Program.cs` 的 `Program.Main` 中：</span><span class="sxs-lookup"><span data-stu-id="8f893-122">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="8f893-123">如果 <xref:Microsoft.AspNetCore.Components.NavigationManager> 服务不可用，则启动期间可使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 属性。</span><span class="sxs-lookup"><span data-stu-id="8f893-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8f893-124">其他资源</span><span class="sxs-lookup"><span data-stu-id="8f893-124">Additional resources</span></span>

* <xref:fundamentals/environments>
