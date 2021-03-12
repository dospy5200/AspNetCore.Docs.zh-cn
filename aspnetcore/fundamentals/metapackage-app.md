---
title: ASP.NET Core 的 Microsoft.AspNetCore.App 元包
author: Rick-Anderson
description: Microsoft.AspNetCore.App 共享框架
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
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
uid: fundamentals/metapackage-app
ms.openlocfilehash: 5d9d9cd446a61cc3e573712a4626af04dc284e99
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589577"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a>Microsoft.AspNetCore.App for ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

 ASP.NET Core 共享框架 (`Microsoft.AspNetCore.App`) 包含由 Microsoft 开发和支持的程序集。 当安装 [NET Core 3.0 或更高版本 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) 时，安装 `Microsoft.AspNetCore.App`。 共享框架是安装在计算机上并包括运行时组件和目标包的一组程序集（*.dll* 文件）。 有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。

* 面向 `Microsoft.NET.Sdk.Web` SDK 的项目隐式引用 `Microsoft.AspNetCore.App` 框架。

对于这些项目，不需要其他引用：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

ASP.NET Core 共享框架：

* 不包括第三方依赖项。
* 包括 ASP.NET Core 团队支持的所有包。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

此功能需要面向 .NET Core 2.x 的 ASP.NET Core 2.x。

ASP.NET Core 的 [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [元包](/dotnet/core/packages#metapackages)：

* 不包括第三方依赖项，[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)、[Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) 和 [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/) 除外。 为确保正常使用主要框架功能，这些第三方依赖项均为必要条件。
* 包括 ASP.NET Core 团队支持的所有软件包，包含第三方依赖项的软件包（不包括上文所述）除外。
* 包括 Entity Framework Core 团队支持的所有软件包，包含第三方依赖项的软件包（不包括上文所述）除外。

`Microsoft.AspNetCore.App` 包中包含了 ASP.NET Core 2.x 和 Entity Framework Core 2.x 的所有功能。 面向 ASP.NET Core 2.x 的默认项目模板使用此包。 建议面向 ASP.NET Core 2.x 和 Entity Framework Core 2.x 的应用程序使用 `Microsoft.AspNetCore.App` 包。

`Microsoft.AspNetCore.App` 元包的版本号表示最低 ASP.NET Core 版本和 Entity Framework Core 版本。

使用 `Microsoft.AspNetCore.App` 元包可提供用于保护应用的版本限制：

* 如果包含的包对 `Microsoft.AspNetCore.App` 中的包具有可传递的（非直接）依赖项，且这些版本号不同，则 NuGet 将生成错误。
* 添加到应用的其他包无法更改 `Microsoft.AspNetCore.App` 中包含的包版本。
* 版本一致性能确保可靠的体验。 `Microsoft.AspNetCore.App` 旨在防止在同一应用中结合使用未经测试的相关位版本组合。

使用 `Microsoft.AspNetCore.App` 元包的应用程序会自动使用 ASP.NET Core 共享框架。 使用 `Microsoft.AspNetCore.App` 元包时，应用程序不部署所引用的 ASP.NET Core NuGet 包中的任何资产 &mdash; .NET Core 共享框架包含这些资产。 共享框架中的资产已经过预编译，以便缩短应用程序启动时间。 有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。

以下项目文件为 ASP.NET Core 引用 `Microsoft.AspNetCore.App` 元包，，它表示一种典型的 ASP.NET Core 2.2 模板：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

前面的标记表示典型的 ASP.NET Core 2.x 模板。 它不会为 `Microsoft.AspNetCore.App` 包引用指定版本号。 如果未指定版本，SDK 会指定[隐式](https://github.com/dotnet/core/blob/main/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md)版本，即 `Microsoft.NET.Sdk.Web`。 建议使用 SDK 指定的隐式版本，而不是在包引用上显式设置版本号。 如果对这种方法有任何疑问，可以在 [Microsoft.AspNetCore.App 隐式版本讨论](https://github.com/dotnet/AspNetCore.Docs/issues/6430)上发表 GitHub 评论。

对于便携式应用，隐式版本设置为 `major.minor.0`。 共享框架前滚机制将在安装的共享框架的最新兼容版本上运行应用。 为确保在开发、测试和生产中使用相同的版本，请确保在所有环境中都安装相同版本的共享框架。 对于独立应用，将隐式版本号设置为在已安装的 SDK 中捆绑的共享框架的 `major.minor.patch`。

在 `Microsoft.AspNetCore.App` 引用上指定版本号，不能保证将选择该共享框架版本。 例如，假设指定的版本是“2.2.1”，但安装的是“2.2.3”。 这种情况下，应用将使用"2.2.3"。 不过不建议这样做，你可以禁用前滚（修补程序和/或次要版本）。 有关 dotnet 主机前滚以及如何配置其行为的详细信息，请参阅 [dotnet 主机前滚](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)。

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`<Project Sdk` 必须设置为 `Microsoft.NET.Sdk.Web` 以使用隐式版本 `Microsoft.AspNetCore.App`。 使用 `<Project Sdk="Microsoft.NET.Sdk">`（不带尾随 `.Web`）时：

* 生成以下警告：

  警告 NU1604：项目依赖项 Microsoft.AspNetCore.App 不包括包含下限。请在依赖项版本中包括下限，以确保一致的还原结果。

* 这是 .NET Core 2.1 SDK 的一个已知问题。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a>更新 ASP.NET Core

`Microsoft.AspNetCore.App` [元包](/dotnet/core/packages#metapackages)不是从 NuGet 更新的传统包。 类似于 `Microsoft.NETCore.App`，`Microsoft.AspNetCore.App` 表示共享运行时，它具有在 NuGet 之外处理的特殊版本控制语义。 有关详细信息，请参阅[包、元包和框架](/dotnet/core/packages)。

更新 ASP.NET Core：

* 在开发计算机和构建服务器上：下载并安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。
* 在部署服务器上：下载并安装 [.NET Core 运行时](https://dotnet.microsoft.com/download)。

 在应用程序重启时，应用程序将前滚到最新安装的版本。 无需更新项目文件中的 `Microsoft.AspNetCore.App` 版本号。 有关详细信息，请参阅[依赖于框架的应用会前滚](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward)。

如果应用程序之前使用 `Microsoft.AspNetCore.All`，请参阅[从 Microsoft.AspNetCore.All 迁移到 Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate)。

::: moniker-end
