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
# <a name="aspnet-core-blazor-environments"></a>ASP.NET Core Blazor 环境

> [!NOTE]
> 本主题适用于 Blazor WebAssembly。 有关 ASP.NET Core 应用配置的常规指导（其中描述了对 Blazor Server应用使用的方法），请参阅 <xref:fundamentals/environments>。

在本地运行应用时，环境默认为开发环境。 发布应用时，环境默认为生产环境。

托管的 Blazor WebAssembly 解决方案的客户端 Blazor 应用 (`Client`) 通过将环境传递到浏览器的中间件，来根据解决方案的 `Server` 应用确定环境 。 `Server` 应用使用该环境作为标头值来添加一个名为 `blazor-environment` 的标头。 `Client` 应用读取标头。 解决方案的 `Server` 应用是一个 ASP.NET Core 应用；若要详细了解如何配置环境，可查看 <xref:fundamentals/environments>。

对于在本地运行的独立 Blazor WebAssembly 应用，开发服务器会添加 `blazor-environment` 标头来指定开发环境。 要为其他宿主环境指定环境，请添加 `blazor-environment` 标头。

在下面的 IIS 示例中，将自定义标头 (`blazor-environment`) 添加到已发布的 `web.config` 文件中。 `web.config` 文件位于 `bin/Release/{TARGET FRAMEWORK}/publish` 文件夹中，占位符 `{TARGET FRAMEWORK}` 为目标框架：

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
> 要对 IIS 使用在将应用发布到 `publish` 文件夹时不会被覆盖的自定义 `web.config` 文件，请参阅 <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>。

通过注入 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> 并读取 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> 属性，在组件中获取应用的环境。

`Pages/ReadEnvironment.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

在启动过程中，<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> 会通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> 属性公开 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment>，这会在主机生成器代码中启用环境特定的逻辑。

在 `Program.cs` 的 `Program.Main` 中：

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

使用通过 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> 提供的下列便捷扩展方法，可在当前环境中检查开发环境、生产环境、暂存环境和自定义环境名称：

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

在 `Program.cs` 的 `Program.Main` 中：

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

如果 <xref:Microsoft.AspNetCore.Components.NavigationManager> 服务不可用，则启动期间可使用 <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> 属性。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/environments>
