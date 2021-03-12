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
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="e6faa-103">Microsoft.AspNetCore.App for ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6faa-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="e6faa-104">ASP.NET Core 共享框架 (`Microsoft.AspNetCore.App`) 包含由 Microsoft 开发和支持的程序集。</span><span class="sxs-lookup"><span data-stu-id="e6faa-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="e6faa-105">当安装 [NET Core 3.0 或更高版本 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) 时，安装 `Microsoft.AspNetCore.App`。</span><span class="sxs-lookup"><span data-stu-id="e6faa-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="e6faa-106">共享框架是安装在计算机上并包括运行时组件和目标包的一组程序集（*.dll* 文件）。</span><span class="sxs-lookup"><span data-stu-id="e6faa-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="e6faa-107">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="e6faa-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="e6faa-108">面向 `Microsoft.NET.Sdk.Web` SDK 的项目隐式引用 `Microsoft.AspNetCore.App` 框架。</span><span class="sxs-lookup"><span data-stu-id="e6faa-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="e6faa-109">对于这些项目，不需要其他引用：</span><span class="sxs-lookup"><span data-stu-id="e6faa-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="e6faa-110">ASP.NET Core 共享框架：</span><span class="sxs-lookup"><span data-stu-id="e6faa-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="e6faa-111">不包括第三方依赖项。</span><span class="sxs-lookup"><span data-stu-id="e6faa-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="e6faa-112">包括 ASP.NET Core 团队支持的所有包。</span><span class="sxs-lookup"><span data-stu-id="e6faa-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e6faa-113">此功能需要面向 .NET Core 2.x 的 ASP.NET Core 2.x。</span><span class="sxs-lookup"><span data-stu-id="e6faa-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="e6faa-114">ASP.NET Core 的 [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [元包](/dotnet/core/packages#metapackages)：</span><span class="sxs-lookup"><span data-stu-id="e6faa-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="e6faa-115">不包括第三方依赖项，[Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)、[Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) 和 [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/) 除外。</span><span class="sxs-lookup"><span data-stu-id="e6faa-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="e6faa-116">为确保正常使用主要框架功能，这些第三方依赖项均为必要条件。</span><span class="sxs-lookup"><span data-stu-id="e6faa-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="e6faa-117">包括 ASP.NET Core 团队支持的所有软件包，包含第三方依赖项的软件包（不包括上文所述）除外。</span><span class="sxs-lookup"><span data-stu-id="e6faa-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="e6faa-118">包括 Entity Framework Core 团队支持的所有软件包，包含第三方依赖项的软件包（不包括上文所述）除外。</span><span class="sxs-lookup"><span data-stu-id="e6faa-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="e6faa-119">`Microsoft.AspNetCore.App` 包中包含了 ASP.NET Core 2.x 和 Entity Framework Core 2.x 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="e6faa-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="e6faa-120">面向 ASP.NET Core 2.x 的默认项目模板使用此包。</span><span class="sxs-lookup"><span data-stu-id="e6faa-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="e6faa-121">建议面向 ASP.NET Core 2.x 和 Entity Framework Core 2.x 的应用程序使用 `Microsoft.AspNetCore.App` 包。</span><span class="sxs-lookup"><span data-stu-id="e6faa-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="e6faa-122">`Microsoft.AspNetCore.App` 元包的版本号表示最低 ASP.NET Core 版本和 Entity Framework Core 版本。</span><span class="sxs-lookup"><span data-stu-id="e6faa-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="e6faa-123">使用 `Microsoft.AspNetCore.App` 元包可提供用于保护应用的版本限制：</span><span class="sxs-lookup"><span data-stu-id="e6faa-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="e6faa-124">如果包含的包对 `Microsoft.AspNetCore.App` 中的包具有可传递的（非直接）依赖项，且这些版本号不同，则 NuGet 将生成错误。</span><span class="sxs-lookup"><span data-stu-id="e6faa-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="e6faa-125">添加到应用的其他包无法更改 `Microsoft.AspNetCore.App` 中包含的包版本。</span><span class="sxs-lookup"><span data-stu-id="e6faa-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="e6faa-126">版本一致性能确保可靠的体验。</span><span class="sxs-lookup"><span data-stu-id="e6faa-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="e6faa-127">`Microsoft.AspNetCore.App` 旨在防止在同一应用中结合使用未经测试的相关位版本组合。</span><span class="sxs-lookup"><span data-stu-id="e6faa-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="e6faa-128">使用 `Microsoft.AspNetCore.App` 元包的应用程序会自动使用 ASP.NET Core 共享框架。</span><span class="sxs-lookup"><span data-stu-id="e6faa-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="e6faa-129">使用 `Microsoft.AspNetCore.App` 元包时，应用程序不部署所引用的 ASP.NET Core NuGet 包中的任何资产 &mdash; .NET Core 共享框架包含这些资产。</span><span class="sxs-lookup"><span data-stu-id="e6faa-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="e6faa-130">共享框架中的资产已经过预编译，以便缩短应用程序启动时间。</span><span class="sxs-lookup"><span data-stu-id="e6faa-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="e6faa-131">有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。</span><span class="sxs-lookup"><span data-stu-id="e6faa-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="e6faa-132">以下项目文件为 ASP.NET Core 引用 `Microsoft.AspNetCore.App` 元包，，它表示一种典型的 ASP.NET Core 2.2 模板：</span><span class="sxs-lookup"><span data-stu-id="e6faa-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

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

<span data-ttu-id="e6faa-133">前面的标记表示典型的 ASP.NET Core 2.x 模板。</span><span class="sxs-lookup"><span data-stu-id="e6faa-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="e6faa-134">它不会为 `Microsoft.AspNetCore.App` 包引用指定版本号。</span><span class="sxs-lookup"><span data-stu-id="e6faa-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="e6faa-135">如果未指定版本，SDK 会指定[隐式](https://github.com/dotnet/core/blob/main/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md)版本，即 `Microsoft.NET.Sdk.Web`。</span><span class="sxs-lookup"><span data-stu-id="e6faa-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/main/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="e6faa-136">建议使用 SDK 指定的隐式版本，而不是在包引用上显式设置版本号。</span><span class="sxs-lookup"><span data-stu-id="e6faa-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="e6faa-137">如果对这种方法有任何疑问，可以在 [Microsoft.AspNetCore.App 隐式版本讨论](https://github.com/dotnet/AspNetCore.Docs/issues/6430)上发表 GitHub 评论。</span><span class="sxs-lookup"><span data-stu-id="e6faa-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="e6faa-138">对于便携式应用，隐式版本设置为 `major.minor.0`。</span><span class="sxs-lookup"><span data-stu-id="e6faa-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="e6faa-139">共享框架前滚机制将在安装的共享框架的最新兼容版本上运行应用。</span><span class="sxs-lookup"><span data-stu-id="e6faa-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="e6faa-140">为确保在开发、测试和生产中使用相同的版本，请确保在所有环境中都安装相同版本的共享框架。</span><span class="sxs-lookup"><span data-stu-id="e6faa-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="e6faa-141">对于独立应用，将隐式版本号设置为在已安装的 SDK 中捆绑的共享框架的 `major.minor.patch`。</span><span class="sxs-lookup"><span data-stu-id="e6faa-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="e6faa-142">在 `Microsoft.AspNetCore.App` 引用上指定版本号，不能保证将选择该共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="e6faa-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="e6faa-143">例如，假设指定的版本是“2.2.1”，但安装的是“2.2.3”。</span><span class="sxs-lookup"><span data-stu-id="e6faa-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="e6faa-144">这种情况下，应用将使用"2.2.3"。</span><span class="sxs-lookup"><span data-stu-id="e6faa-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="e6faa-145">不过不建议这样做，你可以禁用前滚（修补程序和/或次要版本）。</span><span class="sxs-lookup"><span data-stu-id="e6faa-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="e6faa-146">有关 dotnet 主机前滚以及如何配置其行为的详细信息，请参阅 [dotnet 主机前滚](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)。</span><span class="sxs-lookup"><span data-stu-id="e6faa-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="e6faa-147">`<Project Sdk` 必须设置为 `Microsoft.NET.Sdk.Web` 以使用隐式版本 `Microsoft.AspNetCore.App`。</span><span class="sxs-lookup"><span data-stu-id="e6faa-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="e6faa-148">使用 `<Project Sdk="Microsoft.NET.Sdk">`（不带尾随 `.Web`）时：</span><span class="sxs-lookup"><span data-stu-id="e6faa-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="e6faa-149">生成以下警告：</span><span class="sxs-lookup"><span data-stu-id="e6faa-149">The following warning is generated:</span></span>

  <span data-ttu-id="e6faa-150">警告 NU1604：项目依赖项 Microsoft.AspNetCore.App 不包括包含下限。请在依赖项版本中包括下限，以确保一致的还原结果。</span><span class="sxs-lookup"><span data-stu-id="e6faa-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="e6faa-151">这是 .NET Core 2.1 SDK 的一个已知问题。</span><span class="sxs-lookup"><span data-stu-id="e6faa-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="e6faa-152">更新 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6faa-152">Update ASP.NET Core</span></span>

<span data-ttu-id="e6faa-153">`Microsoft.AspNetCore.App` [元包](/dotnet/core/packages#metapackages)不是从 NuGet 更新的传统包。</span><span class="sxs-lookup"><span data-stu-id="e6faa-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="e6faa-154">类似于 `Microsoft.NETCore.App`，`Microsoft.AspNetCore.App` 表示共享运行时，它具有在 NuGet 之外处理的特殊版本控制语义。</span><span class="sxs-lookup"><span data-stu-id="e6faa-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="e6faa-155">有关详细信息，请参阅[包、元包和框架](/dotnet/core/packages)。</span><span class="sxs-lookup"><span data-stu-id="e6faa-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="e6faa-156">更新 ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="e6faa-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="e6faa-157">在开发计算机和构建服务器上：下载并安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="e6faa-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="e6faa-158">在部署服务器上：下载并安装 [.NET Core 运行时](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="e6faa-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="e6faa-159">在应用程序重启时，应用程序将前滚到最新安装的版本。</span><span class="sxs-lookup"><span data-stu-id="e6faa-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="e6faa-160">无需更新项目文件中的 `Microsoft.AspNetCore.App` 版本号。</span><span class="sxs-lookup"><span data-stu-id="e6faa-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="e6faa-161">有关详细信息，请参阅[依赖于框架的应用会前滚](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward)。</span><span class="sxs-lookup"><span data-stu-id="e6faa-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="e6faa-162">如果应用程序之前使用 `Microsoft.AspNetCore.All`，请参阅[从 Microsoft.AspNetCore.All 迁移到 Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate)。</span><span class="sxs-lookup"><span data-stu-id="e6faa-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
