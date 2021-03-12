---
title: 在 ASP.NET Core 中使用托管服务实现后台任务
author: rick-anderson
description: 了解如何在 ASP.NET Core 中使用托管服务实现后台任务。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
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
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c0492c0c5b660e1387b0d0a4f6be405ded49ee92
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587471"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>在 ASP.NET Core 中使用托管服务实现后台任务

作者：[Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

在 ASP.NET Core 中，后台任务作为托管服务实现。 托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。 本主题提供了三个托管服务示例：

* 在计时器上运行的后台任务。
* 激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。 有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)。
* 按顺序运行的已排队后台任务。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="worker-service-template"></a>辅助角色服务模板

ASP.NET Core 辅助角色服务模板可作为编写长期服务应用的起点。 通过辅助角色服务模板创建的应用将在其项目文件中指定 Worker SDK：

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

要使用该模板作为编写托管服务应用的基础：

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Package

基于辅助角色服务模板的应用使用 `Microsoft.NET.Sdk.Worker` SDK，并且具有对 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包的显式包引用。 有关示例，请参阅示例应用的项目文件 (*BackgroundTasksSample.csproj*)。

对于使用 `Microsoft.NET.Sdk.Web` SDK 的 Web 应用，通过共享框架隐式引用 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。 在应用的项目文件中不需要显式包引用。

## <a name="ihostedservice-interface"></a>IHostedService 接口

<xref:Microsoft.Extensions.Hosting.IHostedService> 接口为主机托管的对象定义了两种方法：

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*)：`StartAsync` 包含用于启动后台任务的逻辑。 在以下操作之前调用 `StartAsync`：

  * 已配置应用的请求处理管道 (`Startup.Configure`)。
  * 已启动服务器且已触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*)。

  可以更改默认行为，以便在配置应用的管道并调用 `ApplicationStarted` 之后，运行托管服务的 `StartAsync`。 若要更改默认行为，请在调用 `ConfigureWebHostDefaults` 后添加托管服务（以下示例中的 `VideosWatcher`）：

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)：当主机执行正常关机时触发。 `StopAsync` 包含结束后台任务的逻辑。 实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。

  默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。 在令牌上请求取消时：

  * 应中止应用正在执行的任何剩余后台操作。
  * `StopAsync` 中调用的任何方法都应及时返回。

  但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。

  如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。 因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。

  若要延长默认值为 5 秒的关闭超时值，请设置：

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。
  * 使用 Web 主机时为关闭超时值主机配置设置。 有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。

托管服务在应用启动时激活一次，在应用关闭时正常关闭。 如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。

## <a name="backgroundservice-base-class"></a>BackgroundService 基类

<xref:Microsoft.Extensions.Hosting.BackgroundService> 是用于实现长时间运行的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的基类。

调用 [ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) 来运行后台服务。 实现返回一个 <xref:System.Threading.Tasks.Task>，其表示后台服务的整个生存期。 在 [ExecuteAsync 变为异步](https://github.com/dotnet/extensions/issues/2149)（例如通过调用 `await`）之前，不会启动任何其他服务。 避免在 `ExecuteAsync` 中执行长时间的阻塞初始化工作。 [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) 中的主机块等待完成 `ExecuteAsync`。

调用 [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) 时，将触发取消令牌。 当激发取消令牌以便正常关闭服务时，`ExecuteAsync` 的实现应立即完成。 否则，服务将在关闭超时后不正常关闭。 有关更多信息，请参阅 [IHostedService interface](#ihostedservice-interface) 部分。

## <a name="timed-background-tasks"></a>计时的后台任务

定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。 计时器触发任务的 `DoWork` 方法。 在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<xref:System.Threading.Timer> 不等待先前的 `DoWork` 执行完成，因此所介绍的方法可能并不适用于所有场景。 使用 [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) 以原子操作的形式将执行计数器递增，这可确保多个线程不会并行更新 `executionCount`。

已使用 `AddHostedService` 扩展方法在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册该服务：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>在后台任务中使用有作用域的服务

要在 [BackgroundService](#backgroundservice-base-class) 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建作用域。 默认情况下，不会为托管服务创建作用域。

作用域后台任务服务包含后台任务的逻辑。 如下示例中：

* 服务是异步的。 `DoWork` 方法返回 `Task`。 出于演示目的，在 `DoWork` 方法中等待 10 秒的延迟。
* <xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中。

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法。 `DoWork` 返回 `ExecuteAsync` 等待的 `Task`：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。 已使用 `AddHostedService` 扩展方法注册托管服务：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>排队的后台任务

后台任务队列基于 .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

在以下 `QueueHostedService` 示例中：

* `BackgroundProcessing` 方法返回 `ExecuteAsync` 中等待的 `Task`。
* 在 `BackgroundProcessing` 中，取消排队并执行队列中的后台任务。
* 服务在 `StopAsync` 中停止之前，将等待工作项。

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

每当在输入设备上选择 `w` 键时，`MonitorLoop` 服务将处理托管服务的排队任务：

* `IBackgroundTaskQueue` 注入到 `MonitorLoop` 服务中。
* 调用 `IBackgroundTaskQueue.QueueBackgroundWorkItem` 来将工作项排入队列。
* 工作项模拟长时间运行的后台任务：
  * 将执行三次 5 秒的延迟 (`Task.Delay`)。
  * 如果任务已取消，`try-catch` 语句将捕获 <xref:System.OperationCanceledException>。

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

已在 `IHostBuilder.ConfigureServices` (*Program.cs*) 中注册这些服务。 已使用 `AddHostedService` 扩展方法注册托管服务：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

已在 `Program.Main` 中启动 `MonitorLoop`：

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

在 ASP.NET Core 中，后台任务作为托管服务实现。 托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。 本主题提供了三个托管服务示例：

* 在计时器上运行的后台任务。
* 激活有[作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)的托管服务。 有作用域的服务可使用[依赖项注入 (DI)](xref:fundamentals/dependency-injection)
* 按顺序运行的已排队后台任务。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/hosted-services/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="package"></a>Package

引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) 包。

## <a name="ihostedservice-interface"></a>IHostedService 接口

托管服务实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口。 该接口为主机托管的对象定义了两种方法：

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*)：`StartAsync` 包含用于启动后台任务的逻辑。 当使用 [Web 主机](xref:fundamentals/host/web-host)时，会在启动服务器并触发 [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) 后调用 `StartAsync`。 当使用[通用主机](xref:fundamentals/host/generic-host)时，会在触发 `ApplicationStarted` 之前调用 `StartAsync`。

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*)：当主机执行正常关机时触发。 `StopAsync` 包含结束后台任务的逻辑。 实现 <xref:System.IDisposable> 和[终结器（析构函数）](/dotnet/csharp/programming-guide/classes-and-structs/destructors)以处置任何非托管资源。

  默认情况下，取消令牌会有五秒超时，以指示关闭进程不再正常。 在令牌上请求取消时：

  * 应中止应用正在执行的任何剩余后台操作。
  * `StopAsync` 中调用的任何方法都应及时返回。

  但是，在请求取消后，将不会放弃任务 &mdash; 调用方等待所有任务完成。

  如果应用意外关闭（例如，应用的进程失败），则可能不会调用 `StopAsync`。 因此，在 `StopAsync` 中执行的任何方法或操作都可能不会发生。

  若要延长默认值为 5 秒的关闭超时值，请设置：

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>（当使用通用主机时）。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host#shutdown-timeout>。
  * 使用 Web 主机时为关闭超时值主机配置设置。 有关详细信息，请参阅 <xref:fundamentals/host/web-host#shutdown-timeout>。

托管服务在应用启动时激活一次，在应用关闭时正常关闭。 如果在执行后台任务期间引发错误，即使未调用 `StopAsync`，也应调用 `Dispose`。

## <a name="timed-background-tasks"></a>计时的后台任务

定时后台任务使用 [System.Threading.Timer](xref:System.Threading.Timer) 类。 计时器触发任务的 `DoWork` 方法。 在 `StopAsync` 上禁用计时器，并在 `Dispose` 上处置服务容器时处置计时器：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<xref:System.Threading.Timer> 不等待先前的 `DoWork` 执行完成，因此所介绍的方法可能并不适用于所有场景。

已使用 `AddHostedService` 扩展方法在 `Startup.ConfigureServices` 中注册该服务：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>在后台任务中使用有作用域的服务

要在 `IHostedService` 中使用[有作用域的服务](xref:fundamentals/dependency-injection#service-lifetimes)，请创建一个作用域。 默认情况下，不会为托管服务创建作用域。

作用域后台任务服务包含后台任务的逻辑。 在以下示例中，将 <xref:Microsoft.Extensions.Logging.ILogger> 注入到服务中：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

托管服务创建一个作用域来解决作用域后台任务服务以调用其 `DoWork` 方法：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

已在 `Startup.ConfigureServices` 中注册这些服务。 已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>排队的后台任务

后台任务队列基于 .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>（[暂定为 ASP.NET Core 内置版本](https://github.com/aspnet/Hosting/issues/1280)）：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

在 `QueueHostedService` 中，队列中的后台任务会取消排队，并作为 [BackgroundService](#backgroundservice-base-class) 执行，此类是用于实现长时间运行 `IHostedService` 的基类：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

已在 `Startup.ConfigureServices` 中注册这些服务。 已使用 `AddHostedService` 扩展方法注册 `IHostedService` 实现：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

在索引页模型类中：

* 将 `IBackgroundTaskQueue` 注入构造函数并分配给 `Queue`。
* 注入 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> 并将其分配给 `_serviceScopeFactory`。 工厂用于创建 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 的实例，用于在范围内创建服务。 创建范围是为了使用应用的`AppDbContext`（[设置了范围的服务](xref:fundamentals/dependency-injection#service-lifetimes)），以在 `IBackgroundTaskQueue`（单一实例服务）中写入数据库记录。

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

在索引页上选择“添加任务”按钮时，会执行 `OnPostAddTask` 方法。 调用 `QueueBackgroundWorkItem` 来将工作项排入队列：

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [使用 IHostedService 和 BackgroundService 类在微服务中实现后台任务](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [在 Azure 应用服务中使用 WebJobs 运行后台任务](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
