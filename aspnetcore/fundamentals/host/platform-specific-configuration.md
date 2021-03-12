---
title: 在 ASP.NET Core 中使用承载启动程序集
author: rick-anderson
description: 了解如何使用 IHostingStartup 从外部程序集增强 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: b39215a70f990afeb7d3fe0a62981113b154354e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588238"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>在 ASP.NET Core 中使用承载启动程序集

作者：[Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>（承载启动）实现，在启动时从外部程序集向应用添加增强功能。 例如，外部库可使用承载启动实现为应用提供其他配置提供程序或服务。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="hostingstartup-attribute"></a>HostingStartup 属性

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性表示存在要在运行时激活的承载启动程序集。

将自动扫描输入程序集或包含 `Startup` 类的程序集以查找 `HostingStartup` 属性。 用于搜索 `HostingStartup` 属性的程序集列表在运行时从 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 中的配置加载。 要从发现中排除的程序集列表从 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) 加载。

在以下示例中，承载启动程序集的命名空间为 `StartupEnhancement`。 包含承载启动代码的类是 `StartupEnhancementHostingStartup`：

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup` 属性通常位于承载启动程序集的 `IHostingStartup` 实现类文件中。

## <a name="discover-loaded-hosting-startup-assemblies"></a>发现加载的承载启动程序集

要发现加载的承载启动程序集，请启用日志记录并检查应用的日志。 记录加载程序集时发生的错误。 会在调试级别记录加载的承载启动程序集，并记录所有错误。

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>禁用承载启动程序集的自动加载

要禁用承载启动程序集的自动加载，请使用以下方法之一：

* 要阻止加载所有承载启动程序集，请将以下内容之一设置为 `true` 或 `1`：

  * 阻止承载启动主机配置设置：

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` 环境变量。

* 要阻止加载特定的承载启动程序集，请将以下之一设置为以分号分隔的承载启动程序集字符串，以便在启动时排除：

  * 承载启动排除程序集承载配置设置：

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 环境变量。

如果同时设置了主机配置设置和环境变量，则主机设置将控制行为。

如果使用主机设置或环境变量来禁用承载启动程序集，将全局禁用程序集，并可能会禁用应用的多个特征。

## <a name="project"></a>项目

使用以下任一项目类型创建承载启动：

* [类库](#class-library)
* [无入口点的控制台应用](#console-app-without-an-entry-point)

### <a name="class-library"></a>类库

可在类库中提供承载启动增强。 库包含 `HostingStartup` 属性。

[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)包括 Razor Pages 应用、HostingStartupApp 和类库 HostingStartupLibrary 。 类库：

* 包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。 `ServiceKeyInjection` 使用内存中配置提供程序 ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) 将一对服务字符串添加到应用的配置中。
* 包含 `HostingStartup` 属性，用于标识承载启动的命名空间和类。

`ServiceKeyInjection` 类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。

HostingStartupLibrary/ServiceKeyInjection.cs：

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

应用的索引页读取并呈现类库承载启动程序集设置的两个键的配置值：

HostingStartupApp/Pages/Index.cshtml.cs：

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)还包括一个 NuGet 包项目，该项目提供单独的承载启动 HostingStartupPackage。 该包具有与前述类库相同的特征。 包：

* 包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。 `ServiceKeyInjection` 将一对服务字符串添加到应用的配置中。
* 包含 `HostingStartup` 属性。

HostingStartupPackage/ServiceKeyInjection.cs：

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

应用的索引页读取并呈现包承载启动程序集设置的两个键的配置值：

HostingStartupApp/Pages/Index.cshtml.cs：

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>无入口点的控制台应用

此方法仅适用于 .NET Core 应用，不适用于 .NET Framework。

可在包含 `HostingStartup` 属性的无入口点的控制台应用中提供动态承载启动增强功能，该功能无需编译时引用进行激活。 发布控制台应用会生成可从运行时存储中使用的承载启动程序集。

此过程中使用没有入口点的控制台应用，因为：

* 需要依赖项文件来使用承载启动程序集中的承载启动。 依赖项文件是一种可运行的应用资产，它通过发布应用而不是库来生成。
* 无法直接将库添加到[运行时包存储](/dotnet/core/deploying/runtime-store)，该过程需要一个以已共享运行时为目标的可运行项目。

创建动态承载启动过程中：

* 从控制台应用创建承载启动程序集，无需以下入口点：
  * 包含 `IHostingStartup` 实现的类。
  * 包含用于识别 `IHostingStartup` 实现类的 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性。
* 发布控制台应用，获取承载启动的依赖项。 发布控制台应用的结果是从依赖项文件中删除了未使用的依赖项。
* 修改依赖项文件以设置承载启动程序集的运行时位置。
* 承载启动程序集及其依赖项文件位于运行时包存储中。 要发现承载启动程序集及其依赖项文件，它们将在一对环境变量中列出。

控制台应用引用 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 包：

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

生成 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 时，[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性将类标识为 `IHostingStartup` 的实现，用于加载和执行。 在下面的示例中，命名空间为 `StartupEnhancement`，类为 `StartupEnhancementHostingStartup`：

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

类实现 `IHostingStartup`。 该类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。 用户代码中 `Startup.Configure` 之前的运行时调用托管启动程序集中的 `IHostingStartup.Configure`，允许用户代码覆盖托管启动程序集提供的任何配置。

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

生成 `IHostingStartup` 项目时，依赖项文件 (.deps.json) 将程序集的 `runtime` 位置设为 bin 文件夹 ：

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

仅显示部分文件。 示例中程序集的名称是 `StartupEnhancement`。

## <a name="configuration-provided-by-the-hosting-startup"></a>托管启动提供的配置

处理配置有两种方法，具体取决于是希望托管启动的配置优先还是应用的配置优先：

1. 使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行后加载配置。 使用此方法，托管启动配置优先于应用的配置。
1. 使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行之前加载配置。 使用此方法，应用的配置值优先于托管启动程序提供的值。

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>指定承载启动程序集

对于类库或控制台应用提供的承载启动，请在 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中指定承载启动程序集的名称。 环境变量是以分号分隔的程序集列表。

仅扫描承载启动程序集以查找 `HostingStartup` 属性。 对于示例应用 HostingStartupApp，要发现前述的承载启动，请将环境变量设置为以下值：

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

还可使用承载启动程序集主机配置设置来设置此承载启动程序集：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

存在多个托管启动程序集时，将按列出程序集的顺序执行 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法。

## <a name="activation"></a>激活

承载启动激活的选项包括：

* [运行时存储](#runtime-store)：激活无需用于激活的编译时引用。 示例应用将承载启动程序集和依赖项文件放入文件夹“deployment”，以便在多计算机环境中部署承载启动。 “deployment”文件夹还包括 PowerShell 脚本，该脚本可在部署系统上创建或修改环境变量以启用承载启动。
* 激活所需的编译时引用
  * [NuGet 包](#nuget-package)
  * [项目 bin 文件夹](#project-bin-folder)

### <a name="runtime-store"></a>运行时存储

承载启动实现位于[运行时存储](/dotnet/core/deploying/runtime-store)中。 增强型应用无需对程序集进行编译时引用。

构建承载启动后，使用清单项目文件和 [dotnet store](/dotnet/core/tools/dotnet-store) 命令生成运行时存储。

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

在示例应用（*RuntimeStore* 项目）中，使用以下命令：

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

对于发现运行时存储的运行时，运行时存储的位置将添加到 `DOTNET_SHARED_STORE` 环境变量中。

**修改并放置承载启动的依赖项文件**

要在未包引用增强功能的情况下激活增强功能，请使用 `additionalDeps` 为运行时指定附加依赖项。 使用 `additionalDeps`，你可以：

* 通过提供一组附加的 .deps.json 文件来扩展应用的库图，以便在启动时与应用自身的 .deps.json 文件合并 。
* 使承载启动程序集可被发现并可加载。

生成附加依赖项文件的推荐方法是：

 1. 对上一节中引用的运行时存储清单文件执行 `dotnet publish`。
 1. 从库中删除清单引用，以及生成的 deps.json 文件的 `runtime` 部分。

在示例项目中，`store.manifest/1.0.0` 属性已从 `targets` 和 `libraries` 部分中删除：

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

将 .deps.json 文件放入以下位置：

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`：添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量的位置。
* `{SHARED FRAMEWORK NAME}`：此附加依赖项文件所必需的共享框架。
* `{SHARED FRAMEWORK VERSION}`：最低共享框架版本。
* `{ENHANCEMENT ASSEMBLY NAME}`：增强程序集名称。

在示例应用（*RuntimeStore*  项目）中，附加依赖项文件放于以下位置：

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

对于发现运行时存储位置的运行时，附加依赖项文件位置将添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量中。

在示例应用（*RuntimeStore* 项目）中，使用 [PowerShell](/powershell/scripting/powershell-scripting) 脚本完成构建运行时存储并生成附加依赖项文件。

有关如何设置各种操作系统的环境变量的示例，请参阅[使用多个环境](xref:fundamentals/environments)。

**部署**

为了便于在多计算机环境中部署托承载启动，示例应用在已发布的输出中创建一个“deployment”文件夹，其中包含：

* 承载启动运行时存储。
* 承载启动依赖项文件。
* PowerShell 脚本，用于创建或修改 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE` 和 `DOTNET_ADDITIONAL_DEPS` 以支持激活承载启动。 从部署系统上的管理 PowerShell 命令提示符运行脚本。

### <a name="nuget-package"></a>NuGet 程序包

可在 NuGet 包中提供承载启动增强。 该包含有 `HostingStartup` 属性。 包提供的承载启动类型使用以下任一方法提供给应用：

* 增强型应用的项目文件在应用的项目文件（编译时引用）中为承载启动提供了包引用。 有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。 此方法适用于已发布到 [nuget.org](https://www.nuget.org/) 的承载启动程序集包。
* 承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。

有关 NuGet 包和运行时存储的详细信息，请参阅以下主题：

* [如何使用跨平台工具创建 NuGet 包](/dotnet/core/deploying/creating-nuget-packages)
* [发布包](/nuget/create-packages/publish-a-package)
* [运行时包存储](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>项目 bin 文件夹

承载启动增强功能可通过增强型应用中的 bin -deployed 程序集提供。 程序集提供的承载启动类型使用以下方法之一提供给应用：

* 增强型应用的项目文件对承载启动进行了程序集引用（编译时引用）。 有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。 当部署方案要求对承载启动程序集（.dll 文件）进行编译时引用并将程序集移动到以下任一位置时，此方法适用：
  * 进行中的项目。
  * 进行中的项目可访问的位置。
* 承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。
* 在以 .NET Framework 为目标时，程序集可在默认加载上下文中加载，这在 .NET Framework 上表示程序集位于以下任一位置：
  * 应用程序基路径：应用的可执行文件 (.exe) 所在的 bin 文件夹。
  * 全局程序集缓存 (GAC)：GAC 存储多个 .NET Framework 应用共享的程序集。 有关详细信息，请参阅[如何：将程序集安装到 .NET Framework 文档中的全局程序集缓存](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)中。

## <a name="sample-code"></a>示例代码

[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）演示了承载启动实现方案：

* 两个承载启动程序集（类库）分别设置一对内存中配置键值对：
  * NuGet 包 (HostingStartupPackage)
  * 类库 (HostingStartupLibrary)
* 从运行时存储部署的程序集 (StartupDiagnostics) 激活承载启动。 该程序集在启动时将两个中间件添加到应用，用于提供以下内容的诊断信息：
  * 已注册服务
  * 地址（方案、主机、基路径、路径、查询字符串）
  * 连接（远程 IP、远程端口、本地 IP、本地端口、客户端证书）
  * 请求标头
  * 环境变量

运行示例：

**从 NuGet 包激活**

1. 使用 [dotnet pack](/dotnet/core/tools/dotnet-pack) 命令编译 HostingStartupPackage 包。
1. 将包的程序集名称 HostingStartupPackage 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。
1. 编译并运行应用。 增强型应用中存在包引用（编译时引用）。 应用项目文件中的 `<PropertyGroup>` 指定包项目的输出 (../HostingStartupPackage/bin/Debug) 作为包源。 这允许应用使用该包而无需将包上传到 [nuget.org](https://www.nuget.org/)。有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. 观察到索引页呈现的服务配置键值与包的 `ServiceKeyInjection.Configure` 方法设置的值匹配。

如果更改并重新编译 HostingStartupPackage 项目，请清除本地 NuGet 包缓存，确保 HostingStartupApp 从本地缓存中收到更新后的包而不是旧包 。 要清除本地 NuGet 缓存，请执行以下 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 命令：

```dotnetcli
dotnet nuget locals all --clear
```

**从类库激活**

1. 使用 [dotnet build](/dotnet/core/tools/dotnet-build) 命令编译 HostingStartupLibrary 类库。
1. 将类库的程序集名称 HostingStartupLibrary 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。
1. bin - 通过将类库编译输出中的 HostingStartupLibrary.dll 文件复制到应用的 bin/Debug 文件夹，将类库程序集部署到应用  。
1. 编译并运行应用。 应用项目文件中的 `<ItemGroup>` 引用类库的程序集 (.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll)（编译时引用）。 有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. 观察到索引页呈现的服务配置键值与类库的 `ServiceKeyInjection.Configure` 方法设置的值匹配。

**从运行时存储部署的程序集激活**

1. StartupDiagnostics 项目使用 [PowerShell](/powershell/scripting/powershell-scripting) 修改其 StartupDiagnostics.deps.json 文件 。 默认情况下，Windows 7 SP1 和 Windows Server 2008 R2 SP1 及以后版本的 Windows 上安装有 PowerShell。 若要在其他平台上获取 PowerShell，请参阅[安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell)。
1. 执行 RuntimeStore文件夹中的 build.ps1 脚本 。 脚本：
   * 在 obj\packages 文件夹中生成 `StartupDiagnostics` 包。
   * 在 store 文件夹中生成 `StartupDiagnostics` 的运行时存储。 该脚本中的 `dotnet store` 命令使用 `win7-x64` [运行时标识符 (RID)](/dotnet/core/rid-catalog) 将托管启动部署到 Windows。 为其他运行时提供托管启动程序集时，在脚本第 37 行上替换为正确的 RID。 `StartupDiagnostics` 的运行时存储稍后会移动到将使用程序集的计算机上的用户或系统的运行时存储。 `StartupDiagnostics` 程序集的用户运行时存储安装位置是 .dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll。
   * 在 additionalDeps 文件夹中生成 `StartupDiagnostics` 的 `additionalDeps`。 附加依赖项稍后会移动到用户或系统的附加依赖项。 用户 `StartupDiagnostics` 附加依赖项安装位置是 .dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json。
   * 将 deploy.ps1 文件放置在 deployment 文件夹中。
1. 运行 deployment 文件夹中的 deploy.ps1 脚本 。 脚本将：
   * `StartupDiagnostics` 追加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量。
   * `DOTNET_ADDITIONAL_DEPS` 环境变量的承载启动依赖项路径（在 RuntimeStore 项目的 deployment 文件夹中）。
   * `DOTNET_SHARED_STORE` 环境变量的运行时存储路径（在 RuntimeStore 项目的 deployment 文件夹中）。
1. 运行示例应用。
1. 请求 `/services` 终结点以查看应用的注册服务。 请求 `/diag` 终结点以查看诊断信息。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>（承载启动）实现，在启动时从外部程序集向应用添加增强功能。 例如，外部库可使用承载启动实现为应用提供其他配置提供程序或服务。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="hostingstartup-attribute"></a>HostingStartup 属性

[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性表示存在要在运行时激活的承载启动程序集。

将自动扫描输入程序集或包含 `Startup` 类的程序集以查找 `HostingStartup` 属性。 用于搜索 `HostingStartup` 属性的程序集列表在运行时从 [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) 中的配置加载。 要从发现中排除的程序集列表从 [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) 加载。 有关详细信息，请参阅 [Web 主机：承载启动程序集](xref:fundamentals/host/web-host#hosting-startup-assemblies)和 [Web 主机：承载启动排除程序集](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)。

在以下示例中，承载启动程序集的命名空间为 `StartupEnhancement`。 包含承载启动代码的类是 `StartupEnhancementHostingStartup`：

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

`HostingStartup` 属性通常位于承载启动程序集的 `IHostingStartup` 实现类文件中。

## <a name="discover-loaded-hosting-startup-assemblies"></a>发现加载的承载启动程序集

要发现加载的承载启动程序集，请启用日志记录并检查应用的日志。 记录加载程序集时发生的错误。 会在调试级别记录加载的承载启动程序集，并记录所有错误。

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>禁用承载启动程序集的自动加载

要禁用承载启动程序集的自动加载，请使用以下方法之一：

* 要阻止加载所有承载启动程序集，请将以下内容之一设置为 `true` 或 `1`：
  * [阻止承载启动](xref:fundamentals/host/web-host#prevent-hosting-startup)主机配置设置。
  * `ASPNETCORE_PREVENTHOSTINGSTARTUP` 环境变量。
* 要阻止加载特定的承载启动程序集，请将以下之一设置为以分号分隔的承载启动程序集字符串，以便在启动时排除：
  * [承载启动排除程序集](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)承载配置设置。
  * `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 环境变量。

如果同时设置了主机配置设置和环境变量，则主机设置将控制行为。

如果使用主机设置或环境变量来禁用承载启动程序集，将全局禁用程序集，并可能会禁用应用的多个特征。

## <a name="project"></a>项目

使用以下任一项目类型创建承载启动：

* [类库](#class-library)
* [无入口点的控制台应用](#console-app-without-an-entry-point)

### <a name="class-library"></a>类库

可在类库中提供承载启动增强。 库包含 `HostingStartup` 属性。

[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)包括 Razor Pages 应用、HostingStartupApp 和类库 HostingStartupLibrary 。 类库：

* 包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。 `ServiceKeyInjection` 使用内存中配置提供程序 ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) 将一对服务字符串添加到应用的配置中。
* 包含 `HostingStartup` 属性，用于标识承载启动的命名空间和类。

`ServiceKeyInjection` 类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。

HostingStartupLibrary/ServiceKeyInjection.cs：

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

应用的索引页读取并呈现类库承载启动程序集设置的两个键的配置值：

HostingStartupApp/Pages/Index.cshtml.cs：

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)还包括一个 NuGet 包项目，该项目提供单独的承载启动 HostingStartupPackage。 该包具有与前述类库相同的特征。 包：

* 包含承载启动类 `ServiceKeyInjection`，用于实现 `IHostingStartup`。 `ServiceKeyInjection` 将一对服务字符串添加到应用的配置中。
* 包含 `HostingStartup` 属性。

HostingStartupPackage/ServiceKeyInjection.cs：

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

应用的索引页读取并呈现包承载启动程序集设置的两个键的配置值：

HostingStartupApp/Pages/Index.cshtml.cs：

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>无入口点的控制台应用

此方法仅适用于 .NET Core 应用，不适用于 .NET Framework。

可在包含 `HostingStartup` 属性的无入口点的控制台应用中提供动态承载启动增强功能，该功能无需编译时引用进行激活。 发布控制台应用会生成可从运行时存储中使用的承载启动程序集。

此过程中使用没有入口点的控制台应用，因为：

* 需要依赖项文件来使用承载启动程序集中的承载启动。 依赖项文件是一种可运行的应用资产，它通过发布应用而不是库来生成。
* 无法直接将库添加到[运行时包存储](/dotnet/core/deploying/runtime-store)，该过程需要一个以已共享运行时为目标的可运行项目。

创建动态承载启动过程中：

* 从控制台应用创建承载启动程序集，无需以下入口点：
  * 包含 `IHostingStartup` 实现的类。
  * 包含用于识别 `IHostingStartup` 实现类的 [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性。
* 发布控制台应用，获取承载启动的依赖项。 发布控制台应用的结果是从依赖项文件中删除了未使用的依赖项。
* 修改依赖项文件以设置承载启动程序集的运行时位置。
* 承载启动程序集及其依赖项文件位于运行时包存储中。 要发现承载启动程序集及其依赖项文件，它们将在一对环境变量中列出。

控制台应用引用 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 包：

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

生成 <xref:Microsoft.AspNetCore.Hosting.IWebHost> 时，[HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) 属性将类标识为 `IHostingStartup` 的实现，用于加载和执行。 在下面的示例中，命名空间为 `StartupEnhancement`，类为 `StartupEnhancementHostingStartup`：

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

类实现 `IHostingStartup`。 该类的 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法使用 <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> 向应用添加增强功能。 用户代码中 `Startup.Configure` 之前的运行时调用托管启动程序集中的 `IHostingStartup.Configure`，允许用户代码覆盖托管启动程序集提供的任何配置。

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

生成 `IHostingStartup` 项目时，依赖项文件 (.deps.json) 将程序集的 `runtime` 位置设为 bin 文件夹 ：

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

仅显示部分文件。 示例中程序集的名称是 `StartupEnhancement`。

## <a name="configuration-provided-by-the-hosting-startup"></a>托管启动提供的配置

处理配置有两种方法，具体取决于是希望托管启动的配置优先还是应用的配置优先：

1. 使用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行后加载配置。 使用此方法，托管启动配置优先于应用的配置。
1. 使用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> 为应用提供配置，在应用的 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 委托执行之前加载配置。 使用此方法，应用的配置值优先于托管启动程序提供的值。

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>指定承载启动程序集

对于类库或控制台应用提供的承载启动，请在 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中指定承载启动程序集的名称。 环境变量是以分号分隔的程序集列表。

仅扫描承载启动程序集以查找 `HostingStartup` 属性。 对于示例应用 HostingStartupApp，要发现前述的承载启动，请将环境变量设置为以下值：

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

还可使用[承载启动程序集](xref:fundamentals/host/web-host#hosting-startup-assemblies)主机配置设置来设置此承载启动程序集。

存在多个托管启动程序集时，将按列出程序集的顺序执行 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> 方法。

## <a name="activation"></a>激活

承载启动激活的选项包括：

* [运行时存储](#runtime-store)：激活无需用于激活的编译时引用。 示例应用将承载启动程序集和依赖项文件放入文件夹“deployment”，以便在多计算机环境中部署承载启动。 “deployment”文件夹还包括 PowerShell 脚本，该脚本可在部署系统上创建或修改环境变量以启用承载启动。
* 激活所需的编译时引用
  * [NuGet 包](#nuget-package)
  * [项目 bin 文件夹](#project-bin-folder)

### <a name="runtime-store"></a>运行时存储

承载启动实现位于[运行时存储](/dotnet/core/deploying/runtime-store)中。 增强型应用无需对程序集进行编译时引用。

构建承载启动后，使用清单项目文件和 [dotnet store](/dotnet/core/tools/dotnet-store) 命令生成运行时存储。

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

在示例应用（*RuntimeStore* 项目）中，使用以下命令：

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

对于发现运行时存储的运行时，运行时存储的位置将添加到 `DOTNET_SHARED_STORE` 环境变量中。

**修改并放置承载启动的依赖项文件**

要在未包引用增强功能的情况下激活增强功能，请使用 `additionalDeps` 为运行时指定附加依赖项。 使用 `additionalDeps`，你可以：

* 通过提供一组附加的 .deps.json 文件来扩展应用的库图，以便在启动时与应用自身的 .deps.json 文件合并 。
* 使承载启动程序集可被发现并可加载。

生成附加依赖项文件的推荐方法是：

 1. 对上一节中引用的运行时存储清单文件执行 `dotnet publish`。
 1. 从库中删除清单引用，以及生成的 deps.json 文件的 `runtime` 部分。

在示例项目中，`store.manifest/1.0.0` 属性已从 `targets` 和 `libraries` 部分中删除：

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

将 .deps.json 文件放入以下位置：

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`：添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量的位置。
* `{SHARED FRAMEWORK NAME}`：此附加依赖项文件所必需的共享框架。
* `{SHARED FRAMEWORK VERSION}`：最低共享框架版本。
* `{ENHANCEMENT ASSEMBLY NAME}`：增强程序集名称。

在示例应用（*RuntimeStore*  项目）中，附加依赖项文件放于以下位置：

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

对于发现运行时存储位置的运行时，附加依赖项文件位置将添加到 `DOTNET_ADDITIONAL_DEPS` 环境变量中。

在示例应用（*RuntimeStore* 项目）中，使用 [PowerShell](/powershell/scripting/powershell-scripting) 脚本完成构建运行时存储并生成附加依赖项文件。

有关如何设置各种操作系统的环境变量的示例，请参阅[使用多个环境](xref:fundamentals/environments)。

**部署**

为了便于在多计算机环境中部署托承载启动，示例应用在已发布的输出中创建一个“deployment”文件夹，其中包含：

* 承载启动运行时存储。
* 承载启动依赖项文件。
* PowerShell 脚本，用于创建或修改 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`、`DOTNET_SHARED_STORE` 和 `DOTNET_ADDITIONAL_DEPS` 以支持激活承载启动。 从部署系统上的管理 PowerShell 命令提示符运行脚本。

### <a name="nuget-package"></a>NuGet 程序包

可在 NuGet 包中提供承载启动增强。 该包含有 `HostingStartup` 属性。 包提供的承载启动类型使用以下任一方法提供给应用：

* 增强型应用的项目文件在应用的项目文件（编译时引用）中为承载启动提供了包引用。 有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。 此方法适用于已发布到 [nuget.org](https://www.nuget.org/) 的承载启动程序集包。
* 承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。

有关 NuGet 包和运行时存储的详细信息，请参阅以下主题：

* [如何使用跨平台工具创建 NuGet 包](/dotnet/core/deploying/creating-nuget-packages)
* [发布包](/nuget/create-packages/publish-a-package)
* [运行时包存储](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>项目 bin 文件夹

承载启动增强功能可通过增强型应用中的 bin -deployed 程序集提供。 程序集提供的承载启动类型使用以下方法之一提供给应用：

* 增强型应用的项目文件对承载启动进行了程序集引用（编译时引用）。 有了编译时引用，承载启动程序集及其所有依赖项都会合并到应用的依赖项文件 (.deps.json) 中。 当部署方案要求对承载启动程序集（.dll 文件）进行编译时引用并将程序集移动到以下任一位置时，此方法适用：
  * 进行中的项目。
  * 进行中的项目可访问的位置。
* 承载启动的依赖项文件可用于增强型应用，如[运行时存储](#runtime-store)部分所述（无编译时引用）。
* 在以 .NET Framework 为目标时，程序集可在默认加载上下文中加载，这在 .NET Framework 上表示程序集位于以下任一位置：
  * 应用程序基路径：应用的可执行文件 (.exe) 所在的 bin 文件夹。
  * 全局程序集缓存 (GAC)：GAC 存储多个 .NET Framework 应用共享的程序集。 有关详细信息，请参阅[如何：将程序集安装到 .NET Framework 文档中的全局程序集缓存](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)中。

## <a name="sample-code"></a>示例代码

[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)（[如何下载](xref:index#how-to-download-a-sample)）演示了承载启动实现方案：

* 两个承载启动程序集（类库）分别设置一对内存中配置键值对：
  * NuGet 包 (HostingStartupPackage)
  * 类库 (HostingStartupLibrary)
* 从运行时存储部署的程序集 (StartupDiagnostics) 激活承载启动。 该程序集在启动时将两个中间件添加到应用，用于提供以下内容的诊断信息：
  * 已注册服务
  * 地址（方案、主机、基路径、路径、查询字符串）
  * 连接（远程 IP、远程端口、本地 IP、本地端口、客户端证书）
  * 请求标头
  * 环境变量

运行示例：

**从 NuGet 包激活**

1. 使用 [dotnet pack](/dotnet/core/tools/dotnet-pack) 命令编译 HostingStartupPackage 包。
1. 将包的程序集名称 HostingStartupPackage 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。
1. 编译并运行应用。 增强型应用中存在包引用（编译时引用）。 应用项目文件中的 `<PropertyGroup>` 指定包项目的输出 (../HostingStartupPackage/bin/Debug) 作为包源。 这允许应用使用该包而无需将包上传到 [nuget.org](https://www.nuget.org/)。有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. 观察到索引页呈现的服务配置键值与包的 `ServiceKeyInjection.Configure` 方法设置的值匹配。

如果更改并重新编译 HostingStartupPackage 项目，请清除本地 NuGet 包缓存，确保 HostingStartupApp 从本地缓存中收到更新后的包而不是旧包 。 要清除本地 NuGet 缓存，请执行以下 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 命令：

```dotnetcli
dotnet nuget locals all --clear
```

**从类库激活**

1. 使用 [dotnet build](/dotnet/core/tools/dotnet-build) 命令编译 HostingStartupLibrary 类库。
1. 将类库的程序集名称 HostingStartupLibrary 添加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量中。
1. bin - 通过将类库编译输出中的 HostingStartupLibrary.dll 文件复制到应用的 bin/Debug 文件夹，将类库程序集部署到应用  。
1. 编译并运行应用。 应用项目文件中的 `<ItemGroup>` 引用类库的程序集 (.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll)（编译时引用）。 有关详细信息，请参阅 HostingStartupApp 项目文件中的说明。

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. 观察到索引页呈现的服务配置键值与类库的 `ServiceKeyInjection.Configure` 方法设置的值匹配。

**从运行时存储部署的程序集激活**

1. StartupDiagnostics 项目使用 [PowerShell](/powershell/scripting/powershell-scripting) 修改其 StartupDiagnostics.deps.json 文件 。 默认情况下，Windows 7 SP1 和 Windows Server 2008 R2 SP1 及以后版本的 Windows 上安装有 PowerShell。 若要在其他平台上获取 PowerShell，请参阅[安装各种版本的 PowerShell](/powershell/scripting/install/installing-powershell)。
1. 执行 RuntimeStore文件夹中的 build.ps1 脚本 。 脚本：
   * 在 obj\packages 文件夹中生成 `StartupDiagnostics` 包。
   * 在 store 文件夹中生成 `StartupDiagnostics` 的运行时存储。 该脚本中的 `dotnet store` 命令使用 `win7-x64` [运行时标识符 (RID)](/dotnet/core/rid-catalog) 将托管启动部署到 Windows。 为其他运行时提供托管启动程序集时，在脚本第 37 行上替换为正确的 RID。 `StartupDiagnostics` 的运行时存储稍后会移动到将使用程序集的计算机上的用户或系统的运行时存储。 `StartupDiagnostics` 程序集的用户运行时存储安装位置是 .dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll。
   * 在 additionalDeps 文件夹中生成 `StartupDiagnostics` 的 `additionalDeps`。 附加依赖项稍后会移动到用户或系统的附加依赖项。 用户 `StartupDiagnostics` 附加依赖项安装位置是 .dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json。
   * 将 deploy.ps1 文件放置在 deployment 文件夹中。
1. 运行 deployment 文件夹中的 deploy.ps1 脚本 。 脚本将：
   * `StartupDiagnostics` 追加到 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 环境变量。
   * `DOTNET_ADDITIONAL_DEPS` 环境变量的承载启动依赖项路径（在 RuntimeStore 项目的 deployment 文件夹中）。
   * `DOTNET_SHARED_STORE` 环境变量的运行时存储路径（在 RuntimeStore 项目的 deployment 文件夹中）。
1. 运行示例应用。
1. 请求 `/services` 终结点以查看应用的注册服务。 请求 `/diag` 终结点以查看诊断信息。

::: moniker-end
