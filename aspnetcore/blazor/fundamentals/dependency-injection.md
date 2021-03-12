---
title: ASP.NET Core Blazor 依赖关系注入
author: guardrex
description: 了解 Blazor 应用如何将服务注入组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: aefeac2f3a68a669b7c84cbeee2f6a4ec0621f6f
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109671"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Core Blazor 依赖关系注入

作者：[Rainer Stropek](https://www.timecockpit.com) 和 [Mike Rousos](https://github.com/mjrousos)

[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 是一种技术，它用于访问配置在中心位置的服务：

* 可将框架注册的服务直接注入到 Blazor 应用的组件中。
* Blazor 应用还可定义和注册自定义服务，并通过 DI 使其在整个应用中可用。

## <a name="default-services"></a>默认服务

下表中所示的服务通常在 Blazor 应用中使用。

| 服务 | 生存期 | 描述 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | 范围内 | <p>提供用于发送 HTTP 请求以及从 URI 标识的资源接收 HTTP 响应的方法。</p><p>Blazor WebAssembly 应用中 <xref:System.Net.Http.HttpClient> 的实例使用浏览器在后台处理 HTTP 流量。</p><p>默认情况下，Blazor Server 应用不包含配置为服务的 <xref:System.Net.Http.HttpClient>。 向 Blazor Server 应用提供 <xref:System.Net.Http.HttpClient>。</p><p>有关详细信息，请参阅 <xref:blazor/call-web-api>。</p><p><xref:System.Net.Http.HttpClient> 注册为作用域服务，而不是单一实例。 有关详细信息，请参阅[服务生存期](#service-lifetime)部分。</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly** ：单例</p><p>**Blazor Server** ：作用域</p> | 表示在其中调度 JavaScript 调用的 JavaScript 运行时实例。 有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet>。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly** ：单例</p><p>**Blazor Server** ：作用域</p> | 包含用于处理 URI 和导航状态的帮助程序。 有关详细信息，请参阅 [URI 和导航状态帮助程序](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)。 |

自定义服务提供程序不会自动提供表中列出的默认服务。 如果你使用自定义服务提供程序且需要表中所示的任何服务，请将所需服务添加到新的服务提供程序。

## <a name="add-services-to-an-app"></a>向应用添加服务

::: zone pivot="webassembly"

在 `Program.cs` 的 `Program.Main` 方法中配置应用服务集合的服务。 在下例中，为 `IMyDependency` 注册了 `MyDependency` 实现：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        ...

        await builder.Build().RunAsync();
    }
}
```

生成主机后，可在呈现任何组件之前从根 DI 范围使用服务。 这对于在呈现内容之前运行初始化逻辑而言很有用：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

主机会为应用提供中心配置实例。 在上述示例的基础上，天气服务的 URL 将从默认配置源（例如 `appsettings.json`）传递到 `InitializeWeatherAsync`：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

::: zone-end

::: zone pivot="server"

创建新应用后，请检查 `Startup.cs` 中的 `Startup.ConfigureServices` 方法：

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

向 <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> 方法传递 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>，它是[服务描述符](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor)对象列表。 通过向服务集合提供服务描述符来在 `ConfigureServices` 方法中添加服务。 下面的示例演示了 `IDataAccess` 接口的概念及其具体实现 `DataAccess`：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>服务生存期

可使用下表中所示的生存期配置服务。

| 生存期 | 描述 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Blazor WebAssembly 应用当前没有 DI 范围的概念。 已注册 `Scoped` 的服务的行为与 `Singleton` 服务类似。</p><p>Blazor Server 托管模型在 HTTP 请求中支持 `Scoped` 生存期，但在客户端上加载的组件中的 SignalR 连接/线路消息中则不支持。 在页面或视图之间或从页面或视图导航到组件时，应用的 Razor 页面或 MVC 部分会正常处理作用域服务并在每个 HTTP 请求上重新创建服务。 在客户端上的组件间导航时，作用域服务不会重建，其中与服务器之间的通信通过用户线路的 SignalR 连接进行，而不是通过 HTTP 请求进行。 在客户端上的以下组件方案中，将重建作用域服务，因为为用户创建了新线路：</p><ul><li>用户关闭了浏览器窗口。 用户打开了一个新窗口，并向后导航到该应用。</li><li>用户在浏览器窗口中关闭应用的最后一个选项卡。 用户打开了一个新的选项卡，并向后导航到该应用。</li><li>用户选择浏览器的重新加载/刷新按钮。</li></ul><p>若要详细了解如何在 Blazor Server 应用中跨作用域服务保留用户状态，请参阅 <xref:blazor/hosting-models?pivots=server>。</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI 创建服务的单个实例。 需要 `Singleton` 服务的所有组件都会接收同一服务的实例。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | 每当组件从服务容器获取 `Transient` 服务的实例时，它都会接收该服务的新实例。 |

DI 系统基于 ASP.NET Core 中的 DI 系统。 有关详细信息，请参阅 <xref:fundamentals/dependency-injection>。

## <a name="request-a-service-in-a-component"></a>在组件中请求服务

将服务添加到服务集合后，使用 [`@inject`](xref:mvc/views/razor#inject) Razor 指令将服务注入组件，该指令具有两个参数：

* 类型：要注入的服务的类型。
* 属性：接收注入的应用服务的属性的名称。 属性无需手动创建。 编译器会创建属性。

有关详细信息，请参阅 <xref:mvc/views/dependency-injection>。

使用多个 [`@inject`](xref:mvc/views/razor#inject) 语句来注入不同的服务。

下面的示例展示了如何使用 [`@inject`](xref:mvc/views/razor#inject)。 将实现 `Services.IDataAccess` 的服务注入组件的 `DataRepository` 属性中。 请注意代码是如何仅使用 `IDataAccess` 抽象的：

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

在内部，生成的属性 (`DataRepository`) 使用 [`[Inject]` 特性](xref:Microsoft.AspNetCore.Components.InjectAttribute)。 通常，不直接使用此特性。 如果组件需要基类，并且基类也需要注入的属性，请手动添加 [`[Inject]` 特性](xref:Microsoft.AspNetCore.Components.InjectAttribute)：

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

在派生自基类的组件中，不需要 [`@inject`](xref:mvc/views/razor#inject) 指令。 基类的 <xref:Microsoft.AspNetCore.Components.InjectAttribute> 就已足够：

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>在服务中使用 DI

复杂的服务可能需要其他服务。 在下述示例中，`DataAccess` 需要 <xref:System.Net.Http.HttpClient> 默认服务。 [`@inject`](xref:mvc/views/razor#inject)（或 [`[Inject]` 特性](xref:Microsoft.AspNetCore.Components.InjectAttribute)）在服务中不可用。 必须改用构造函数注入。 通过向服务的构造函数添加参数来添加所需服务。 当 DI 创建服务时，它会在构造函数中识别其所需的服务，并相应地提供这些服务。 在下面的示例中，构造函数通过 DI 接收 <xref:System.Net.Http.HttpClient>。 <xref:System.Net.Http.HttpClient> 是默认服务。

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

构造函数注入的先决条件：

* 必须存在一个构造函数，其参数可完全通过 DI 实现。 如果指定默认值，则允许使用 DI 未涵盖的其他参数。
* 适用的构造函数必须是 `public`。
* 必须存在一个适用的构造函数。 如果出现歧义，DI 会引发异常。

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>用于管理 DI 范围的实用工具基组件类

在 ASP.NET Core 应用中，Scoped 服务的范围通常限定为当前请求。 请求完成后，DI 系统将处置所有 Scoped 或 Transient 服务。 在 Blazor Server 应用中，请求范围会在客户端连接期间一直持续存在，这可能导致暂时性和范围内服务的生存期比预期要长得多。 在 Blazor WebAssembly 应用中，已注册范围内生存期的服务被视为单一实例，因此它们的生存期比典型 ASP.NET Core 应用中的范围内服务要长。

> [!NOTE]
> 若要在应用中检测可释放的暂时性服务，请参阅[检测暂时性可释放对象](#detect-transient-disposables)部分。

限制 Blazor 应用中服务生存期的一种方法是使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 类型。 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是派生自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 的一种抽象类型，它会创建与组件生存期相对应的 DI 范围。 通过使用此范围，可使用具有 Scoped 生存期的 DI 服务，并使其生存期与组件的生存期一样长。 销毁组件时，也会处置组件的 Scoped 服务提供程序提供的服务。 这对以下服务很有用：

* 由于 Transient 生存期不适用而应在组件中重复使用的服务。
* 由于 Singleton 生存期不适用而不得跨组件共享的服务。

可使用下面两个版本的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 类型：

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是 <xref:Microsoft.AspNetCore.Components.ComponentBase> 类型的抽象、可释放子级，其具有 <xref:System.IServiceProvider> 类型的受保护的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 属性。 此提供程序可用于解析范围限定为组件生存期的服务。

  使用 [`@inject`](xref:mvc/views/razor#inject) 或 [`[Inject]` 特性](xref:Microsoft.AspNetCore.Components.InjectAttribute)注入到组件中的 DI 服务不在组件的范围内创建。 要使用组件的范围，必须使用 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 或 <xref:System.IServiceProvider.GetService%2A> 解析服务。 任何使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 提供程序进行解析的服务都具有从同一范围提供的依赖关系。

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> 派生自 <xref:Microsoft.AspNetCore.Components.OwningComponentBase>，并添加从范围内 DI 提供程序返回 `T` 实例的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 属性。 当存在一项应用需要从使用组件范围的 DI 容器中获取的主服务时，不必使用 <xref:System.IServiceProvider> 的实例即可通过此类型便捷地访问 Scoped 服务。 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 属性可用，因此应用可获取其他类型的服务（如有必要）。

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>使用来自 DI 的 Entity Framework Core (EF Core) DbContext

有关详细信息，请参阅 <xref:blazor/blazor-server-ef-core>。

## <a name="detect-transient-disposables"></a>检测暂时性可释放对象

下面的示例演示如何在应使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 的应用中检测可释放的暂时性服务。 有关详细信息，请参阅[用于管理 DI 范围的实用工具基组件类](#utility-base-component-classes-to-manage-a-di-scope)部分。

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

在以下示例中检测到 `TransientDisposable` (`Program.cs`)：

::: moniker range=">= aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("#app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

向 `Program.cs` 添加 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 的命名空间：

```csharp
using Microsoft.Extensions.DependencyInjection;
```

在 `Program.cs` 的 `Program.CreateHostBuilder` 中：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .DetectIncorrectUsageOfTransients()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

在以下示例中检测到 `TransientDependency` (`Startup.cs`)：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
    services.AddServerSideBlazor();
    services.AddSingleton<WeatherForecastService>();
    services.AddTransient<TransientDependency>();
    services.AddTransient<ITransitiveTransientDisposableDependency, 
        TransitiveTransientDisposableDependency>();
}

public class TransitiveTransientDisposableDependency 
    : ITransitiveTransientDisposableDependency, IDisposable
{
    public void Dispose() { }
}

public interface ITransitiveTransientDisposableDependency
{
}

public class TransientDependency
{
    private readonly ITransitiveTransientDisposableDependency 
        _transitiveTransientDisposableDependency;

    public TransientDependency(ITransitiveTransientDisposableDependency 
        transitiveTransientDisposableDependency)
    {
        _transitiveTransientDisposableDependency = 
            transitiveTransientDisposableDependency;
    }
}
```

::: zone-end

应用可以注册暂时性可释放对象，而不会引发异常。 不过，这会尝试在 <xref:System.InvalidOperationException> 中解析暂时性可释放对象结果，如以下示例所示。

`Pages/TransientDisposable.razor`:

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

导航到 `/transient-disposable` 中的 `TransientDisposable` 组件，并在框架尝试构造 `TransientDisposable` 的实例时引发 <xref:System.InvalidOperationException>：

> System.InvalidOperationException：尝试解析错误范围内的暂时性可释放服务 TransientDisposable。 为尝试解析的服务“T”使用“OwningComponentBase\<T>”组件基类。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/dependency-injection>
* [暂时和共享实例的 `IDisposable` 指南](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
