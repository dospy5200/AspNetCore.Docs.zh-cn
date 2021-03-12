---
title: 使用 ASP.NET Core 中的更改令牌检测更改
author: rick-anderson
description: 了解如何使用更改令牌来跟踪更改。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
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
uid: fundamentals/change-tokens
ms.openlocfilehash: df2be1b89ad9681ff70dd71cb3026786f59c8b2a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589382"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>使用 ASP.NET Core 中的更改令牌检测更改

::: moniker range=">= aspnetcore-3.0"

更改令牌是用于跟踪状态更改的通用、低级别构建基块。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/change-tokens/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="ichangetoken-interface"></a>IChangeToken 接口

<xref:Microsoft.Extensions.Primitives.IChangeToken> 传播已发生更改的通知。 `IChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。 将 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包隐式提供给 ASP.NET Core 应用。

`IChangeToken` 具有以下两个属性：

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>，指示令牌是否主动引发回调。 如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。 如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>，接收一个指示是否发生更改的值。

`IChangeToken` 接口具有 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 方法，用于注册在令牌更改时调用的回调。 调用回调之前，必须设置 `HasChanged`。

## <a name="changetoken-class"></a>ChangeToken 类

<xref:Microsoft.Extensions.Primitives.ChangeToken> 是静态类，用于传播已发生更改的通知。 `ChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。 将 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包隐式提供给 ASP.NET Core 应用。

[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 方法注册令牌更改时要调用的 `Action`：

* `Func<IChangeToken>` 生成令牌。
* 令牌更改时，调用 `Action`。

[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 重载还具有一个 `TState` 参数，该参数传递给令牌使用者 `Action`。

`OnChange` 返回 <xref:System.IDisposable>。 调用 <xref:System.IDisposable.Dispose*> 将使令牌停止侦听更多更改并释放令牌的资源。

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core 中更改令牌的使用示例

更改令牌主要用于在 ASP.NET Core 中监视对象更改：

* 为了监视文件更改，<xref:Microsoft.Extensions.FileProviders.IFileProvider> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。
* 可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。
* 对于 `TOptions` 更改，<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> 的默认 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 实现有一个重载，可接受一个或多个 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 实例。 每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。

## <a name="monitor-for-configuration-changes"></a>监视配置更改

默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json、appsettings.Development.json 和 appsettings.Production.json）来加载应用配置设置。

使用接受 `reloadOnChange` 参数的 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 扩展方法配置这些文件。 `reloadOnChange` 指示文件更改时是否应该重载配置。 此设置出现在 <xref:Microsoft.Extensions.Hosting.Host> 便捷方法 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 中：

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

基于文件的配置由 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> 表示。 `FileConfigurationSource` 使用 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 来监视文件。

默认情况下，由使用 <xref:System.IO.FileSystemWatcher> 来监视配置文件更改的 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供 `IFileMonitor`。

示例应用演示监视配置更改的两个实现。 如果任一 appsettings 文件发生更改，这两个文件监视实现将执行自定义代码：示例应用向控制台写入一条消息。

配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。 为确保自定义代码仅在触发多个令牌回调时运行一次，示例的实现将检查文件哈希。 此示例使用 SHA1 文件哈希。 使用指数回退实现重试。 进行重试是因为可能发生文件锁定，暂时阻止对某个文件计算新的哈希。

Utilities/Utilities.cs：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>简单启动更改令牌

将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌。

在 `Startup.Configure`中：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` 提供令牌。 回调是 `InvokeChanged` 方法：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

回调的 `state` 用于在 `IWebHostEnvironment` 中传递，这可以帮助指定要监视的正确 appsettings 配置文件（例如开发环境中的 appsettings.Development.json）。 只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。

只要应用正在运行，该系统就会运行，并且用户不能禁用。

### <a name="monitor-configuration-changes-as-a-service"></a>将配置更改作为服务进行监视

示例实现：

* 基本启动令牌监视。
* 作为服务监视。
* 启用和禁用监视的机制。

示例建立 `IConfigurationMonitor` 接口。

Extensions/ConfigurationMonitor.cs：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` 提供令牌。 `InvokeChanged` 是回调方法。 此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。 使用了以下两个属性：

* `MonitoringEnabled`：指示回调是否应该运行其自定义代码。
* `CurrentState`：描述在 UI 中使用的当前监视状态。

`InvokeChanged` 方法类似于前面的方法，不同之处在于：

* 不运行其代码，除非 `MonitoringEnabled` 为 `true`。
* 输出其 `WriteConsole` 输出中的当前 `state`。

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

将实例 `ConfigurationMonitor` 作为服务在 `Startup.ConfigureServices` 中注册：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

索引页提供对配置监视的用户控制。 将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中。

Pages/Index.cshtml.cs：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

配置监视器 (`_monitor`) 用于启用或禁用监视，并设置 UI 反馈的当前状态：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。 触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。

UI 启用和禁用监视的按钮。

Pages/Index.cshtml：

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>监视缓存文件更改

可以使用 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 将文件内容缓存在内存中。 [内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。 无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧（过时）数据。

例如，当续订[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段造成陈旧缓存文件数据时，不考虑所缓存源文件的状态。 数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。 任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。

在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。 示例应用演示方法的实现。

示例使用 `GetFileContent` 来完成以下操作：

* 返回文件内容。
* 实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。

Utilities/Utilities.cs：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

创建 `FileService` 以处理缓存文件查找。 服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs)。

如果使用缓存键未找到缓存的内容，则将执行以下操作：

1. 使用 `GetFileContent` 获取文件内容。
1. 使用 [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 从文件提供程序获取更改令牌。 修改该文件时，会触发令牌的回调。
1. [可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段将缓存文件内容。 如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) 连接在一起，以逐出缓存条目。

在下面的示例中，文件存储在应用的[内容根目录](xref:fundamentals/index#content-root)中。 `IWebHostEnvironment.ContentRootFileProvider` 用于获取指向应用的 `IWebHostEnvironment.ContentRootPath` 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。 `filePath` 通过 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) 获取。

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

将 `FileService` 与内存缓存服务一起注册在服务容器中。

在 `Startup.ConfigureServices`中：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

页面模型使用服务加载文件内容。

在索引页的 `OnGet` 方法 (Pages/Index.cshtml.cs) 中：

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken 类

要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 类。

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。 如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。 如果发生多个并发更改事件，则调用一次复合更改回调。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

更改令牌是用于跟踪状态更改的通用、低级别构建基块。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/change-tokens/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="ichangetoken-interface"></a>IChangeToken 接口

<xref:Microsoft.Extensions.Primitives.IChangeToken> 传播已发生更改的通知。 `IChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。 对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将为 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包创建一个包引用。

`IChangeToken` 具有以下两个属性：

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>，指示令牌是否主动引发回调。 如果将 `ActiveChangedCallbacks` 设置为 `false`，则不会调用回调，并且应用必须轮询 `HasChanged` 获取更改。 如果未发生任何更改，或者丢弃或禁用基础更改侦听器，还可能永远不会取消令牌。
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>，接收一个指示是否发生更改的值。

`IChangeToken` 接口具有 [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) 方法，用于注册在令牌更改时调用的回调。 调用回调之前，必须设置 `HasChanged`。

## <a name="changetoken-class"></a>ChangeToken 类

<xref:Microsoft.Extensions.Primitives.ChangeToken> 是静态类，用于传播已发生更改的通知。 `ChangeToken` 驻留在 <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> 命名空间中。 对于不使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的应用，将为 [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet 包创建一个包引用。

[ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 方法注册令牌更改时要调用的 `Action`：

* `Func<IChangeToken>` 生成令牌。
* 令牌更改时，调用 `Action`。

[ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) 重载还具有一个 `TState` 参数，该参数传递给令牌使用者 `Action`。

`OnChange` 返回 <xref:System.IDisposable>。 调用 <xref:System.IDisposable.Dispose*> 将使令牌停止侦听更多更改并释放令牌的资源。

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>ASP.NET Core 中更改令牌的使用示例

更改令牌主要用于在 ASP.NET Core 中监视对象更改：

* 为了监视文件更改，<xref:Microsoft.Extensions.FileProviders.IFileProvider> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> 方法将为要监视的指定文件或文件夹创建 `IChangeToken`。
* 可以将 `IChangeToken` 令牌添加到缓存条目，以在更改时触发缓存逐出。
* 对于 `TOptions` 更改，<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> 的默认 <xref:Microsoft.Extensions.Options.OptionsMonitor`1> 实现有一个重载，可接受一个或多个 <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> 实例。 每个实例返回 `IChangeToken`，以注册用于跟踪选项更改的更改通知回调。

## <a name="monitor-for-configuration-changes"></a>监视配置更改

默认情况下，ASP.NET Core 模板使用 [JSON 配置文件](xref:fundamentals/configuration/index#json-configuration-provider)（appsettings.json、appsettings.Development.json 和 appsettings.Production.json）来加载应用配置设置。

使用接受 `reloadOnChange` 参数的 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 上的 [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) 扩展方法配置这些文件。 `reloadOnChange` 指示文件更改时是否应该重载配置。 此设置出现在 <xref:Microsoft.AspNetCore.WebHost> 便捷方法 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 中：

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

基于文件的配置由 <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> 表示。 `FileConfigurationSource` 使用 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 来监视文件。

默认情况下，由使用 <xref:System.IO.FileSystemWatcher> 来监视配置文件更改的 <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供 `IFileMonitor`。

示例应用演示监视配置更改的两个实现。 如果任一 appsettings 文件发生更改，这两个文件监视实现将执行自定义代码：示例应用向控制台写入一条消息。

配置文件的 `FileSystemWatcher` 可以触发多个令牌回调，以用于单个配置文件更改。 为确保自定义代码仅在触发多个令牌回调时运行一次，示例的实现将检查文件哈希。 此示例使用 SHA1 文件哈希。 使用指数回退实现重试。 进行重试是因为可能发生文件锁定，暂时阻止对某个文件计算新的哈希。

Utilities/Utilities.cs：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>简单启动更改令牌

将用于更改通知的令牌使用者 `Action` 回调注册到配置重载令牌。

在 `Startup.Configure`中：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` 提供令牌。 回调是 `InvokeChanged` 方法：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

回调的 `state` 用于在 `IHostingEnvironment` 中传递，这可以帮助指定要监视的正确 appsettings 配置文件（例如开发环境中的 appsettings.Development.json）。 只更改了一次配置文件时，文件哈希用于防止 `WriteConsole` 语句由于多个令牌回调而多次运行。

只要应用正在运行，该系统就会运行，并且用户不能禁用。

### <a name="monitor-configuration-changes-as-a-service"></a>将配置更改作为服务进行监视

示例实现：

* 基本启动令牌监视。
* 作为服务监视。
* 启用和禁用监视的机制。

示例建立 `IConfigurationMonitor` 接口。

Extensions/ConfigurationMonitor.cs：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

已实现的类的构造函数 `ConfigurationMonitor` 注册用于更改通知的回调：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` 提供令牌。 `InvokeChanged` 是回调方法。 此实例中的 `state` 是对 `IConfigurationMonitor` 实例的引用，用于访问监视状态。 使用了以下两个属性：

* `MonitoringEnabled`：指示回调是否应该运行其自定义代码。
* `CurrentState`：描述在 UI 中使用的当前监视状态。

`InvokeChanged` 方法类似于前面的方法，不同之处在于：

* 不运行其代码，除非 `MonitoringEnabled` 为 `true`。
* 输出其 `WriteConsole` 输出中的当前 `state`。

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

将实例 `ConfigurationMonitor` 作为服务在 `Startup.ConfigureServices` 中注册：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

索引页提供对配置监视的用户控制。 将 `IConfigurationMonitor` 的实例注入到 `IndexModel` 中。

Pages/Index.cshtml.cs：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

配置监视器 (`_monitor`) 用于启用或禁用监视，并设置 UI 反馈的当前状态：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

触发 `OnPostStartMonitoring` 时，会启用监视并清除当前状态。 触发 `OnPostStopMonitoring` 时，会禁用监视并设置状态以反映未进行监视。

UI 启用和禁用监视的按钮。

Pages/Index.cshtml：

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>监视缓存文件更改

可以使用 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 将文件内容缓存在内存中。 [内存中缓存](xref:performance/caching/memory)主题中介绍了在内存中缓存。 无需采取其他步骤（如下面所述的实现），如果源数据更改，将从缓存返回陈旧（过时）数据。

例如，当续订[可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段造成陈旧缓存文件数据时，不考虑所缓存源文件的状态。 数据的每个请求续订可调过期时段，但不会将文件重载到缓存中。 任何使用文件缓存内容的应用功能都可能会收到陈旧的内容。

在文件缓存方案中使用更改令牌可防止缓存中出现陈旧的文件内容。 示例应用演示方法的实现。

示例使用 `GetFileContent` 来完成以下操作：

* 返回文件内容。
* 实现具有指数回退的重试算法，以涵盖文件锁暂时阻止读取文件的情况。

Utilities/Utilities.cs：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

创建 `FileService` 以处理缓存文件查找。 服务的 `GetFileContent` 方法调用尝试从内存中缓存获取文件内容并将其返回到调用方 (Services/FileService.cs)。

如果使用缓存键未找到缓存的内容，则将执行以下操作：

1. 使用 `GetFileContent` 获取文件内容。
1. 使用 [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 从文件提供程序获取更改令牌。 修改该文件时，会触发令牌的回调。
1. [可调过期](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration)时段将缓存文件内容。 如果缓存文件时发生了更改，则将更改令牌与 [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) 连接在一起，以逐出缓存条目。

在下面的示例中，文件存储在应用的[内容根目录](xref:fundamentals/index#content-root)中。 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) 用于获取指向应用的 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> 的 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。 `filePath` 通过 [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) 获取。

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

将 `FileService` 与内存缓存服务一起注册在服务容器中。

在 `Startup.ConfigureServices`中：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

页面模型使用服务加载文件内容。

在索引页的 `OnGet` 方法 (Pages/Index.cshtml.cs) 中：

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken 类

要在单个对象中表示一个或多个 `IChangeToken` 实例，请使用 <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> 类。

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

如果任何表示的令牌 `HasChanged` 为 `true`，则复合令牌上的 `HasChanged` 报告 `true`。 如果任何表示的令牌 `ActiveChangeCallbacks` 为 `true`，则复合令牌上的 `ActiveChangeCallbacks` 报告 `true`。 如果发生多个并发更改事件，则调用一次复合更改回调。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
