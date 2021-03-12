---
title: ASP.NET Core 中的 .NET 通用主机
author: rick-anderson
description: 使用 ASP.NET Core 应用中的 .NET Core 通用主机。  通用主机负责应用启动和生存期管理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: ceb766cae1f8a1735f5b578a44f0c481927e47a5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586743"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="4890d-104">ASP.NET Core 中的 .NET 通用主机</span><span class="sxs-lookup"><span data-stu-id="4890d-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4890d-105">ASP.NET Core 模板会创建一个 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)。</span><span class="sxs-lookup"><span data-stu-id="4890d-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="4890d-106">本主题介绍如何使用 ASP.NET Core 中的 .NET 通用主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="4890d-107">若要了解如何使用控制台应用中的 .NET 通用主机，请参阅 [.NET 通用主机](/dotnet/core/extensions/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="4890d-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="4890d-108">主机定义</span><span class="sxs-lookup"><span data-stu-id="4890d-108">Host definition</span></span>

<span data-ttu-id="4890d-109">主机是封装应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="4890d-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4890d-110">依赖关系注入 (DI)</span><span class="sxs-lookup"><span data-stu-id="4890d-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="4890d-111">Logging</span><span class="sxs-lookup"><span data-stu-id="4890d-111">Logging</span></span>
* <span data-ttu-id="4890d-112">Configuration</span><span class="sxs-lookup"><span data-stu-id="4890d-112">Configuration</span></span>
* <span data-ttu-id="4890d-113">`IHostedService` 实现</span><span class="sxs-lookup"><span data-stu-id="4890d-113">`IHostedService` implementations</span></span>

<span data-ttu-id="4890d-114">当主机启动时，它将对在托管服务的服务容器集合中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="4890d-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="4890d-115">在 web 应用中，其中一个 `IHostedService` 实现是启动 [HTTP 服务器实现](xref:fundamentals/index#servers)的 web 服务。</span><span class="sxs-lookup"><span data-stu-id="4890d-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4890d-116">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="4890d-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4890d-117">设置主机</span><span class="sxs-lookup"><span data-stu-id="4890d-117">Set up a host</span></span>

<span data-ttu-id="4890d-118">主机通常由 `Program` 类中的代码配置、生成和运行。</span><span class="sxs-lookup"><span data-stu-id="4890d-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4890d-119">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="4890d-119">The `Main` method:</span></span>

* <span data-ttu-id="4890d-120">调用 `CreateHostBuilder` 方法以创建和配置生成器对象。</span><span class="sxs-lookup"><span data-stu-id="4890d-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4890d-121">对生成器对象调用 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="4890d-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4890d-122">ASP.NET Core Web 模板会生成以下代码来创建一个主机：</span><span class="sxs-lookup"><span data-stu-id="4890d-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="4890d-123">以下代码会使用添加到 DI 容器中的 `IHostedService` 实现创建一个非 HTTP 工作负载。</span><span class="sxs-lookup"><span data-stu-id="4890d-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="4890d-124">对于 HTTP 工作负荷，`Main` 方法相同，但 `CreateHostBuilder` 调用 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="4890d-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4890d-125">如果应用使用 Entity Framework Core，不要更改 `CreateHostBuilder` 方法的名称或签名。</span><span class="sxs-lookup"><span data-stu-id="4890d-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4890d-126">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)应查找一个无需运行应用即可配置主机的 `CreateHostBuilder` 方法。</span><span class="sxs-lookup"><span data-stu-id="4890d-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4890d-127">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="4890d-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4890d-128">默认生成器设置</span><span class="sxs-lookup"><span data-stu-id="4890d-128">Default builder settings</span></span>

<span data-ttu-id="4890d-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 方法：</span><span class="sxs-lookup"><span data-stu-id="4890d-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="4890d-130">将[内容根目录](xref:fundamentals/index#content-root)设置为由 <xref:System.IO.Directory.GetCurrentDirectory*> 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="4890d-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="4890d-131">通过以下项加载主机配置：</span><span class="sxs-lookup"><span data-stu-id="4890d-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="4890d-132">前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="4890d-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4890d-133">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="4890d-133">Command-line arguments.</span></span>
* <span data-ttu-id="4890d-134">通过以下对象加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="4890d-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="4890d-135">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4890d-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="4890d-136">appsettings.{Environment}.json。</span><span class="sxs-lookup"><span data-stu-id="4890d-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4890d-137">应用在 `Development` 环境中运行时的[用户机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="4890d-137">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4890d-138">环境变量。</span><span class="sxs-lookup"><span data-stu-id="4890d-138">Environment variables.</span></span>
  * <span data-ttu-id="4890d-139">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="4890d-139">Command-line arguments.</span></span>
* <span data-ttu-id="4890d-140">添加以下[日志记录](xref:fundamentals/logging/index)提供程序：</span><span class="sxs-lookup"><span data-stu-id="4890d-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4890d-141">控制台</span><span class="sxs-lookup"><span data-stu-id="4890d-141">Console</span></span>
  * <span data-ttu-id="4890d-142">调试</span><span class="sxs-lookup"><span data-stu-id="4890d-142">Debug</span></span>
  * <span data-ttu-id="4890d-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="4890d-143">EventSource</span></span>
  * <span data-ttu-id="4890d-144">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="4890d-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4890d-145">当环境为“开发”时，启用[范围验证](xref:fundamentals/dependency-injection#scope-validation)和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="4890d-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4890d-146"><xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> 方法：</span><span class="sxs-lookup"><span data-stu-id="4890d-146">The <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method:</span></span>

* <span data-ttu-id="4890d-147">从前缀为 `ASPNETCORE_` 的环境变量加载主机配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4890d-148">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器设置为 web 服务器并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4890d-149">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel/options>。</span><span class="sxs-lookup"><span data-stu-id="4890d-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel/options>.</span></span>
* <span data-ttu-id="4890d-150">添加[主机筛选中间件](xref:fundamentals/servers/kestrel/host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="4890d-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel/host-filtering).</span></span>
* <span data-ttu-id="4890d-151">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 等于 `true`，则添加[转接头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)。</span><span class="sxs-lookup"><span data-stu-id="4890d-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4890d-152">支持 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="4890d-152">Enables IIS integration.</span></span> <span data-ttu-id="4890d-153">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="4890d-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4890d-154">本文中后面的[所有应用类型的设置](#settings-for-all-app-types)和[ web 应用的设置](#settings-for-web-apps)部分介绍如何替代默认生成器设置。</span><span class="sxs-lookup"><span data-stu-id="4890d-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4890d-155">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="4890d-155">Framework-provided services</span></span>

<span data-ttu-id="4890d-156">自动注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="4890d-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4890d-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4890d-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4890d-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4890d-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4890d-160">有关框架提供的服务的详细信息，请参阅 <xref:fundamentals/dependency-injection#framework-provided-services>。</span><span class="sxs-lookup"><span data-stu-id="4890d-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4890d-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="4890d-162">将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>（以前称为 `IApplicationLifetime`）服务注入任何类以处理启动后和正常关闭任务。</span><span class="sxs-lookup"><span data-stu-id="4890d-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4890d-163">接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。</span><span class="sxs-lookup"><span data-stu-id="4890d-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4890d-164">该接口还包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="4890d-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4890d-165">以下示例是注册 `IHostApplicationLifetime` 事件的 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="4890d-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4890d-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-166">IHostLifetime</span></span>

<span data-ttu-id="4890d-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。</span><span class="sxs-lookup"><span data-stu-id="4890d-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4890d-168">使用了已注册的最后一个实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-168">The last implementation registered is used.</span></span>

<span data-ttu-id="4890d-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是默认的 `IHostLifetime` 实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4890d-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4890d-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4890d-171">侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="4890d-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="4890d-172">解除阻止 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="4890d-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4890d-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4890d-173">IHostEnvironment</span></span>

<span data-ttu-id="4890d-174">将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下设置的信息：</span><span class="sxs-lookup"><span data-stu-id="4890d-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4890d-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4890d-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4890d-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4890d-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4890d-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="4890d-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4890d-178">Web 应用实现 `IWebHostEnvironment` 接口，该接口继承 `IHostEnvironment` 并添加 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="4890d-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4890d-179">主机配置</span><span class="sxs-lookup"><span data-stu-id="4890d-179">Host configuration</span></span>

<span data-ttu-id="4890d-180">主机配置用于 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 实现的属性。</span><span class="sxs-lookup"><span data-stu-id="4890d-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4890d-181">主机配置可以从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 获取。</span><span class="sxs-lookup"><span data-stu-id="4890d-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="4890d-182">在 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为应用配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4890d-183">若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="4890d-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4890d-184">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="4890d-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4890d-185">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="4890d-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4890d-186">`CreateDefaultBuilder` 包含前缀为 `DOTNET_` 的环境变量提供程序和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="4890d-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4890d-187">对于 web 应用程序，添加前缀为 `ASPNETCORE_` 的环境变量提供程序。</span><span class="sxs-lookup"><span data-stu-id="4890d-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4890d-188">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="4890d-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4890d-189">例如，`ASPNETCORE_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="4890d-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4890d-190">以下示例创建主机配置：</span><span class="sxs-lookup"><span data-stu-id="4890d-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4890d-191">应用配置</span><span class="sxs-lookup"><span data-stu-id="4890d-191">App configuration</span></span>

<span data-ttu-id="4890d-192">通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4890d-193">可多次调用 `ConfigureAppConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="4890d-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4890d-194">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="4890d-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4890d-195">由 `ConfigureAppConfiguration` 创建的配置可以通过 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 获取以用于后续操作，也可以通过 DI 作为服务获取。</span><span class="sxs-lookup"><span data-stu-id="4890d-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4890d-196">主机配置也会添加到应用配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4890d-197">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="4890d-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4890d-198">适用于所有应用类型的设置</span><span class="sxs-lookup"><span data-stu-id="4890d-198">Settings for all app types</span></span>

<span data-ttu-id="4890d-199">本部分列出了适用于 HTTP 和非 HTTP 工作负荷的主机设置。</span><span class="sxs-lookup"><span data-stu-id="4890d-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4890d-200">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="4890d-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span> <span data-ttu-id="4890d-201">有关详细信息，请参阅[默认生成器设置](#default-builder-settings)部分。</span><span class="sxs-lookup"><span data-stu-id="4890d-201">For more information, see the [Default builder settings](#default-builder-settings) section.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4890d-202">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4890d-202">ApplicationName</span></span>

<span data-ttu-id="4890d-203">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="4890d-203">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4890d-204">键：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4890d-204">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4890d-205">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-205">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-206">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="4890d-206">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4890d-207">**环境变量**：`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4890d-207">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4890d-208">要设置此值，请使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="4890d-208">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4890d-209">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4890d-209">ContentRoot</span></span>

<span data-ttu-id="4890d-210">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 属性决定主机从什么位置开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="4890d-210">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4890d-211">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="4890d-211">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4890d-212">键：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4890d-212">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4890d-213">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-213">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-214">**默认**：应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="4890d-214">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4890d-215">**环境变量**：`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4890d-215">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4890d-216">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="4890d-216">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4890d-217">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="4890d-217">For more information, see:</span></span>

* [<span data-ttu-id="4890d-218">基础知识：内容根目录</span><span class="sxs-lookup"><span data-stu-id="4890d-218">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4890d-219">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4890d-219">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4890d-220">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4890d-220">EnvironmentName</span></span>

<span data-ttu-id="4890d-221">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 属性可以设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="4890d-221">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4890d-222">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="4890d-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4890d-223">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4890d-223">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4890d-224">键：`environment`</span><span class="sxs-lookup"><span data-stu-id="4890d-224">**Key**: `environment`</span></span>  
<span data-ttu-id="4890d-225">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-225">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-226">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="4890d-226">**Default**: `Production`</span></span>  
<span data-ttu-id="4890d-227">**环境变量**：`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4890d-227">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4890d-228">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="4890d-228">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4890d-229">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="4890d-229">ShutdownTimeout</span></span>

<span data-ttu-id="4890d-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时。</span><span class="sxs-lookup"><span data-stu-id="4890d-230">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4890d-231">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="4890d-231">The default value is five seconds.</span></span>  <span data-ttu-id="4890d-232">在超时时间段中，主机：</span><span class="sxs-lookup"><span data-stu-id="4890d-232">During the timeout period, the host:</span></span>

* <span data-ttu-id="4890d-233">触发 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="4890d-233">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4890d-234">尝试停止托管服务，对服务停止失败的错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="4890d-234">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4890d-235">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="4890d-235">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4890d-236">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="4890d-236">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4890d-237">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="4890d-237">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4890d-238">键：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4890d-238">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4890d-239">类型：`int`</span><span class="sxs-lookup"><span data-stu-id="4890d-239">**Type**: `int`</span></span>  
<span data-ttu-id="4890d-240">**默认**：5 秒</span><span class="sxs-lookup"><span data-stu-id="4890d-240">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4890d-241">**环境变量**：`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4890d-241">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4890d-242">若要设置此值，请使用环境变量或配置 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="4890d-242">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4890d-243">以下示例将超时设置为 20 秒：</span><span class="sxs-lookup"><span data-stu-id="4890d-243">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="4890d-244">禁用“在更改时重载应用配置”</span><span class="sxs-lookup"><span data-stu-id="4890d-244">Disable app configuration reload on change</span></span>

<span data-ttu-id="4890d-245">[默认情况下](xref:fundamentals/configuration/index#default)，appsettings.json 和 appsettings.{Environment}.json 会在文件更改时重载。</span><span class="sxs-lookup"><span data-stu-id="4890d-245">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="4890d-246">若要在 ASP.NET Core 5.0 或更高版本中禁用此重载行为，请将 `hostBuilder:reloadConfigOnChange` 键设置为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4890d-246">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="4890d-247">键：`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4890d-247">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4890d-248">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-248">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-249">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="4890d-249">**Default**: `true`</span></span>  
<span data-ttu-id="4890d-250">命令行参数：`hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4890d-250">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="4890d-251">**环境变量**：`<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="4890d-251">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="4890d-252">所有平台上的环境变量分层键都不支持冒号 (`:`) 分隔符。</span><span class="sxs-lookup"><span data-stu-id="4890d-252">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="4890d-253">有关详细信息，请参阅[环境变量](xref:fundamentals/configuration/index#environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="4890d-253">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="4890d-254">适用于 Web 应用的设置</span><span class="sxs-lookup"><span data-stu-id="4890d-254">Settings for web apps</span></span>

<span data-ttu-id="4890d-255">一些主机设置仅适用于 HTTP 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="4890d-255">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4890d-256">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="4890d-256">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4890d-257">`IWebHostBuilder` 上的扩展方法适用于这些设置。</span><span class="sxs-lookup"><span data-stu-id="4890d-257">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4890d-258">显示如何调用扩展方法的示例代码假定 `webBuilder` 是 `IWebHostBuilder` 的实例，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="4890d-258">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4890d-259">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4890d-259">CaptureStartupErrors</span></span>

<span data-ttu-id="4890d-260">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="4890d-260">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4890d-261">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="4890d-261">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4890d-262">键：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4890d-262">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4890d-263">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-263">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-264">**默认**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="4890d-264">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4890d-265">**环境变量**：`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4890d-265">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4890d-266">若要设置此值，使用配置或调用 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="4890d-266">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4890d-267">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="4890d-267">DetailedErrors</span></span>

<span data-ttu-id="4890d-268">如果启用，或环境为 `Development`，应用会捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="4890d-268">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4890d-269">键：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4890d-269">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4890d-270">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-270">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-271">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="4890d-271">**Default**: `false`</span></span>  
<span data-ttu-id="4890d-272">**环境变量**：`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4890d-272">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4890d-273">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-273">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4890d-274">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4890d-274">HostingStartupAssemblies</span></span>

<span data-ttu-id="4890d-275">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="4890d-275">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4890d-276">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="4890d-276">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4890d-277">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="4890d-277">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4890d-278">键：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4890d-278">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4890d-279">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-279">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-280">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="4890d-280">**Default**: Empty string</span></span>  
<span data-ttu-id="4890d-281">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4890d-281">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4890d-282">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-282">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4890d-283">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4890d-283">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4890d-284">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="4890d-284">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4890d-285">键：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4890d-285">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4890d-286">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-286">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-287">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="4890d-287">**Default**: Empty string</span></span>  
<span data-ttu-id="4890d-288">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4890d-288">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4890d-289">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-289">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4890d-290">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4890d-290">HTTPS_Port</span></span>

<span data-ttu-id="4890d-291">HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="4890d-291">The HTTPS redirect port.</span></span> <span data-ttu-id="4890d-292">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="4890d-292">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4890d-293">键：`https_port`</span><span class="sxs-lookup"><span data-stu-id="4890d-293">**Key**: `https_port`</span></span>  
<span data-ttu-id="4890d-294">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-294">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-295">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="4890d-295">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4890d-296">**环境变量**：`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4890d-296">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4890d-297">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-297">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4890d-298">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4890d-298">PreferHostingUrls</span></span>

<span data-ttu-id="4890d-299">指示主机是否应该侦听使用 `IWebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="4890d-299">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4890d-300">键：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4890d-300">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4890d-301">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-301">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-302">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="4890d-302">**Default**: `true`</span></span>  
<span data-ttu-id="4890d-303">**环境变量**：`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4890d-303">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4890d-304">若要设置此值，请使用环境变量或调用 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="4890d-304">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4890d-305">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4890d-305">PreventHostingStartup</span></span>

<span data-ttu-id="4890d-306">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="4890d-306">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4890d-307">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="4890d-307">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4890d-308">键：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4890d-308">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4890d-309">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-309">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-310">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="4890d-310">**Default**: `false`</span></span>  
<span data-ttu-id="4890d-311">**环境变量**：`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4890d-311">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4890d-312">若要设置此值，请使用环境变量或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-312">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4890d-313">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="4890d-313">StartupAssembly</span></span>

<span data-ttu-id="4890d-314">要搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="4890d-314">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4890d-315">键：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4890d-315">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4890d-316">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-316">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-317">**默认**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="4890d-317">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4890d-318">**环境变量**：`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4890d-318">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4890d-319">若要设置此值，请使用环境变量或调用 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="4890d-319">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4890d-320">`UseStartup` 可以采用程序集名称 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="4890d-320">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4890d-321">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="4890d-321">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4890d-322">URL</span><span class="sxs-lookup"><span data-stu-id="4890d-322">URLs</span></span>

<span data-ttu-id="4890d-323">IP 地址或主机地址的分号分隔列表，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="4890d-323">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4890d-324">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="4890d-324">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4890d-325">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="4890d-325">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4890d-326">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="4890d-326">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4890d-327">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="4890d-327">Supported formats vary among servers.</span></span>

<span data-ttu-id="4890d-328">键：`urls`</span><span class="sxs-lookup"><span data-stu-id="4890d-328">**Key**: `urls`</span></span>  
<span data-ttu-id="4890d-329">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-329">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-330">**默认值**：`http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4890d-330">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4890d-331">**环境变量**：`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4890d-331">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4890d-332">若要设置此值，请使用环境变量或调用 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="4890d-332">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4890d-333">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="4890d-333">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4890d-334">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel/endpoints>。</span><span class="sxs-lookup"><span data-stu-id="4890d-334">For more information, see <xref:fundamentals/servers/kestrel/endpoints>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4890d-335">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4890d-335">WebRoot</span></span>

<span data-ttu-id="4890d-336">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 属性可确定应用静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="4890d-336">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4890d-337">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="4890d-337">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4890d-338">键：`webroot`</span><span class="sxs-lookup"><span data-stu-id="4890d-338">**Key**: `webroot`</span></span>  
<span data-ttu-id="4890d-339">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-339">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-340">**默认**：默认值为 `wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="4890d-340">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4890d-341">{content root}/wwwroot 的路径必须存在。</span><span class="sxs-lookup"><span data-stu-id="4890d-341">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4890d-342">**环境变量**：`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4890d-342">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4890d-343">若要设置此值，请使用环境变量或对 `IWebHostBuilder` 调用 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="4890d-343">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4890d-344">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="4890d-344">For more information, see:</span></span>

* [<span data-ttu-id="4890d-345">基础知识：Web 根目录</span><span class="sxs-lookup"><span data-stu-id="4890d-345">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4890d-346">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4890d-346">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4890d-347">管理主机生存期</span><span class="sxs-lookup"><span data-stu-id="4890d-347">Manage the host lifetime</span></span>

<span data-ttu-id="4890d-348">对生成的 <xref:Microsoft.Extensions.Hosting.IHost> 实现调用方法，以启动和停止应用。</span><span class="sxs-lookup"><span data-stu-id="4890d-348">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4890d-349">这些方法会影响所有在服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-349">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4890d-350">运行</span><span class="sxs-lookup"><span data-stu-id="4890d-350">Run</span></span>

<span data-ttu-id="4890d-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4890d-352">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-352">RunAsync</span></span>

<span data-ttu-id="4890d-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="4890d-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4890d-354">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-354">RunConsoleAsync</span></span>

<span data-ttu-id="4890d-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="4890d-355"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4890d-356">Start</span><span class="sxs-lookup"><span data-stu-id="4890d-356">Start</span></span>

<span data-ttu-id="4890d-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-357"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4890d-358">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-358">StartAsync</span></span>

<span data-ttu-id="4890d-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动主机并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="4890d-359"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4890d-360">在 `StartAsync` 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="4890d-360"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4890d-361">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="4890d-361">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4890d-362">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-362">StopAsync</span></span>

<span data-ttu-id="4890d-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-363"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4890d-364">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4890d-364">WaitForShutdown</span></span>

<span data-ttu-id="4890d-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 阻止调用线程，直到 IHostLifetime 触发关闭，例如通过 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM。</span><span class="sxs-lookup"><span data-stu-id="4890d-365"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4890d-366">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-366">WaitForShutdownAsync</span></span>

<span data-ttu-id="4890d-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="4890d-367"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4890d-368">外部控件</span><span class="sxs-lookup"><span data-stu-id="4890d-368">External control</span></span>

<span data-ttu-id="4890d-369">使用可从外部调用的方法，能够实现对主机生存期的直接控制：</span><span class="sxs-lookup"><span data-stu-id="4890d-369">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4890d-370">ASP.NET Core 模板会创建一个 .NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)。</span><span class="sxs-lookup"><span data-stu-id="4890d-370">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="4890d-371">本主题介绍如何使用 ASP.NET Core 中的 .NET 通用主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-371">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="4890d-372">若要了解如何使用控制台应用中的 .NET 通用主机，请参阅 [.NET 通用主机](/dotnet/core/extensions/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="4890d-372">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="4890d-373">主机定义</span><span class="sxs-lookup"><span data-stu-id="4890d-373">Host definition</span></span>

<span data-ttu-id="4890d-374">主机是封装应用资源的对象，例如：</span><span class="sxs-lookup"><span data-stu-id="4890d-374">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="4890d-375">依赖关系注入 (DI)</span><span class="sxs-lookup"><span data-stu-id="4890d-375">Dependency injection (DI)</span></span>
* <span data-ttu-id="4890d-376">Logging</span><span class="sxs-lookup"><span data-stu-id="4890d-376">Logging</span></span>
* <span data-ttu-id="4890d-377">Configuration</span><span class="sxs-lookup"><span data-stu-id="4890d-377">Configuration</span></span>
* <span data-ttu-id="4890d-378">`IHostedService` 实现</span><span class="sxs-lookup"><span data-stu-id="4890d-378">`IHostedService` implementations</span></span>

<span data-ttu-id="4890d-379">当主机启动时，它将对在托管服务的服务容器集合中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 的每个实现调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="4890d-379">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="4890d-380">在 web 应用中，其中一个 `IHostedService` 实现是启动 [HTTP 服务器实现](xref:fundamentals/index#servers)的 web 服务。</span><span class="sxs-lookup"><span data-stu-id="4890d-380">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="4890d-381">一个对象中包含所有应用的相互依赖资源的主要原因是生存期管理：控制应用启动和正常关闭。</span><span class="sxs-lookup"><span data-stu-id="4890d-381">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4890d-382">设置主机</span><span class="sxs-lookup"><span data-stu-id="4890d-382">Set up a host</span></span>

<span data-ttu-id="4890d-383">主机通常由 `Program` 类中的代码配置、生成和运行。</span><span class="sxs-lookup"><span data-stu-id="4890d-383">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="4890d-384">`Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="4890d-384">The `Main` method:</span></span>

* <span data-ttu-id="4890d-385">调用 `CreateHostBuilder` 方法以创建和配置生成器对象。</span><span class="sxs-lookup"><span data-stu-id="4890d-385">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="4890d-386">对生成器对象调用 `Build` 和 `Run` 方法。</span><span class="sxs-lookup"><span data-stu-id="4890d-386">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="4890d-387">ASP.NET Core Web 模板会生成以下代码来创建泛型主机：</span><span class="sxs-lookup"><span data-stu-id="4890d-387">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
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
            });
}
```

<span data-ttu-id="4890d-388">以下代码会使用非 HTTP 工作负载创建一个泛型主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-388">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="4890d-389">`IHostedService` 实现会添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="4890d-389">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="4890d-390">对于 HTTP 工作负荷，`Main` 方法相同，但 `CreateHostBuilder` 调用 `ConfigureWebHostDefaults`：</span><span class="sxs-lookup"><span data-stu-id="4890d-390">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="4890d-391">上述代码是由 ASP.NET Core 模板生成的。</span><span class="sxs-lookup"><span data-stu-id="4890d-391">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="4890d-392">如果应用使用 Entity Framework Core，不要更改 `CreateHostBuilder` 方法的名称或签名。</span><span class="sxs-lookup"><span data-stu-id="4890d-392">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="4890d-393">[Entity Framework Core 工具](/ef/core/miscellaneous/cli/)应查找一个无需运行应用即可配置主机的 `CreateHostBuilder` 方法。</span><span class="sxs-lookup"><span data-stu-id="4890d-393">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="4890d-394">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="4890d-394">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="4890d-395">默认生成器设置</span><span class="sxs-lookup"><span data-stu-id="4890d-395">Default builder settings</span></span>

<span data-ttu-id="4890d-396"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="4890d-396">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="4890d-397">将[内容根目录](xref:fundamentals/index#content-root)设置为由 <xref:System.IO.Directory.GetCurrentDirectory%2A> 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="4890d-397">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="4890d-398">通过以下项加载主机配置：</span><span class="sxs-lookup"><span data-stu-id="4890d-398">Loads host configuration from:</span></span>
  * <span data-ttu-id="4890d-399">前缀为 `DOTNET_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="4890d-399">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="4890d-400">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="4890d-400">Command-line arguments.</span></span>
* <span data-ttu-id="4890d-401">通过以下对象加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="4890d-401">Loads app configuration from:</span></span>
  * <span data-ttu-id="4890d-402">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4890d-402">*appsettings.json*.</span></span>
  * <span data-ttu-id="4890d-403">appsettings.{Environment}.json。</span><span class="sxs-lookup"><span data-stu-id="4890d-403">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="4890d-404">应用在 `Development` 环境中运行时的[用户机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="4890d-404">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="4890d-405">环境变量。</span><span class="sxs-lookup"><span data-stu-id="4890d-405">Environment variables.</span></span>
  * <span data-ttu-id="4890d-406">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="4890d-406">Command-line arguments.</span></span>
* <span data-ttu-id="4890d-407">添加以下[日志记录](xref:fundamentals/logging/index)提供程序：</span><span class="sxs-lookup"><span data-stu-id="4890d-407">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="4890d-408">控制台</span><span class="sxs-lookup"><span data-stu-id="4890d-408">Console</span></span>
  * <span data-ttu-id="4890d-409">调试</span><span class="sxs-lookup"><span data-stu-id="4890d-409">Debug</span></span>
  * <span data-ttu-id="4890d-410">EventSource</span><span class="sxs-lookup"><span data-stu-id="4890d-410">EventSource</span></span>
  * <span data-ttu-id="4890d-411">EventLog（仅当在 Windows 上运行时）</span><span class="sxs-lookup"><span data-stu-id="4890d-411">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="4890d-412">当环境为“开发”时，启用[范围验证](xref:fundamentals/dependency-injection#scope-validation)和[依赖关系验证](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)。</span><span class="sxs-lookup"><span data-stu-id="4890d-412">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="4890d-413">`ConfigureWebHostDefaults` 方法：</span><span class="sxs-lookup"><span data-stu-id="4890d-413">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="4890d-414">从前缀为 `ASPNETCORE_` 的环境变量加载主机配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-414">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="4890d-415">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器设置为 web 服务器并对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-415">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="4890d-416">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="4890d-416">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="4890d-417">添加[主机筛选中间件](xref:fundamentals/servers/kestrel#host-filtering)。</span><span class="sxs-lookup"><span data-stu-id="4890d-417">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="4890d-418">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 等于 `true`，则添加[转接头中间件](xref:host-and-deploy/proxy-load-balancer#forwarded-headers)。</span><span class="sxs-lookup"><span data-stu-id="4890d-418">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="4890d-419">支持 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="4890d-419">Enables IIS integration.</span></span> <span data-ttu-id="4890d-420">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="4890d-420">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="4890d-421">本文中后面的[所有应用类型的设置](#settings-for-all-app-types)和[ web 应用的设置](#settings-for-web-apps)部分介绍如何替代默认生成器设置。</span><span class="sxs-lookup"><span data-stu-id="4890d-421">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="4890d-422">框架提供的服务</span><span class="sxs-lookup"><span data-stu-id="4890d-422">Framework-provided services</span></span>

<span data-ttu-id="4890d-423">自动注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="4890d-423">The following services are registered automatically:</span></span>

* [<span data-ttu-id="4890d-424">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-424">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="4890d-425">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-425">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="4890d-426">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4890d-426">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="4890d-427">有关框架提供的服务的详细信息，请参阅 <xref:fundamentals/dependency-injection#framework-provided-services>。</span><span class="sxs-lookup"><span data-stu-id="4890d-427">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="4890d-428">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-428">IHostApplicationLifetime</span></span>

<span data-ttu-id="4890d-429">将 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>（以前称为 `IApplicationLifetime`）服务注入任何类以处理启动后和正常关闭任务。</span><span class="sxs-lookup"><span data-stu-id="4890d-429">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="4890d-430">接口上的三个属性是用于注册应用启动和应用停止事件处理程序方法的取消令牌。</span><span class="sxs-lookup"><span data-stu-id="4890d-430">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="4890d-431">该接口还包括 `StopApplication` 方法。</span><span class="sxs-lookup"><span data-stu-id="4890d-431">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="4890d-432">以下示例是注册 `IHostApplicationLifetime` 事件的 `IHostedService` 实现：</span><span class="sxs-lookup"><span data-stu-id="4890d-432">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="4890d-433">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-433">IHostLifetime</span></span>

<span data-ttu-id="4890d-434"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 实现控制主机何时启动和何时停止。</span><span class="sxs-lookup"><span data-stu-id="4890d-434">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="4890d-435">使用了已注册的最后一个实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-435">The last implementation registered is used.</span></span>

<span data-ttu-id="4890d-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 是默认的 `IHostLifetime` 实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-436">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="4890d-437">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="4890d-437">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="4890d-438">侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="4890d-438">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="4890d-439">解除阻止 [RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="4890d-439">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="4890d-440">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="4890d-440">IHostEnvironment</span></span>

<span data-ttu-id="4890d-441">将 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 服务注册到一个类，获取关于以下设置的信息：</span><span class="sxs-lookup"><span data-stu-id="4890d-441">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="4890d-442">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4890d-442">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="4890d-443">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4890d-443">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="4890d-444">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="4890d-444">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="4890d-445">Web 应用实现 `IWebHostEnvironment` 接口，该接口继承 `IHostEnvironment` 并添加 [WebRootPath](#webroot)。</span><span class="sxs-lookup"><span data-stu-id="4890d-445">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4890d-446">主机配置</span><span class="sxs-lookup"><span data-stu-id="4890d-446">Host configuration</span></span>

<span data-ttu-id="4890d-447">主机配置用于 <xref:Microsoft.Extensions.Hosting.IHostEnvironment> 实现的属性。</span><span class="sxs-lookup"><span data-stu-id="4890d-447">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="4890d-448">主机配置可以从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 内的 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) 获取。</span><span class="sxs-lookup"><span data-stu-id="4890d-448">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="4890d-449">在 `ConfigureAppConfiguration` 后，`HostBuilderContext.Configuration` 被替换为应用配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-449">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="4890d-450">若要添加主机配置，请对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A>。</span><span class="sxs-lookup"><span data-stu-id="4890d-450">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="4890d-451">可多次调用 `ConfigureHostConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="4890d-451">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4890d-452">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="4890d-452">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4890d-453">`CreateDefaultBuilder` 包含前缀为 `DOTNET_` 的环境变量提供程序和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="4890d-453">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4890d-454">对于 web 应用程序，添加前缀为 `ASPNETCORE_` 的环境变量提供程序。</span><span class="sxs-lookup"><span data-stu-id="4890d-454">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="4890d-455">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="4890d-455">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4890d-456">例如，`ASPNETCORE_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="4890d-456">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4890d-457">以下示例创建主机配置：</span><span class="sxs-lookup"><span data-stu-id="4890d-457">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="4890d-458">应用配置</span><span class="sxs-lookup"><span data-stu-id="4890d-458">App configuration</span></span>

<span data-ttu-id="4890d-459">通过对 `IHostBuilder` 调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-459">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="4890d-460">可多次调用 `ConfigureAppConfiguration`，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="4890d-460">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="4890d-461">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="4890d-461">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="4890d-462">由 `ConfigureAppConfiguration` 创建的配置可以通过 [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 获取以用于后续操作，也可以通过 DI 作为服务获取。</span><span class="sxs-lookup"><span data-stu-id="4890d-462">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="4890d-463">主机配置也会添加到应用配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-463">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="4890d-464">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index#configureappconfiguration)。</span><span class="sxs-lookup"><span data-stu-id="4890d-464">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="4890d-465">适用于所有应用类型的设置</span><span class="sxs-lookup"><span data-stu-id="4890d-465">Settings for all app types</span></span>

<span data-ttu-id="4890d-466">本部分列出了适用于 HTTP 和非 HTTP 工作负荷的主机设置。</span><span class="sxs-lookup"><span data-stu-id="4890d-466">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="4890d-467">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="4890d-467">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="4890d-468">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="4890d-468">ApplicationName</span></span>

<span data-ttu-id="4890d-469">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="4890d-469">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="4890d-470">键：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4890d-470">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4890d-471">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-471">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-472">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="4890d-472">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="4890d-473">**环境变量**：`<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="4890d-473">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="4890d-474">要设置此值，请使用环境变量。</span><span class="sxs-lookup"><span data-stu-id="4890d-474">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="4890d-475">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4890d-475">ContentRoot</span></span>

<span data-ttu-id="4890d-476">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) 属性决定主机从什么位置开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="4890d-476">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="4890d-477">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="4890d-477">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="4890d-478">键：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4890d-478">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4890d-479">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-479">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-480">**默认**：应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="4890d-480">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="4890d-481">**环境变量**：`<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="4890d-481">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="4890d-482">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseContentRoot`：</span><span class="sxs-lookup"><span data-stu-id="4890d-482">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="4890d-483">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="4890d-483">For more information, see:</span></span>

* [<span data-ttu-id="4890d-484">基础知识：内容根目录</span><span class="sxs-lookup"><span data-stu-id="4890d-484">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="4890d-485">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4890d-485">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="4890d-486">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="4890d-486">EnvironmentName</span></span>

<span data-ttu-id="4890d-487">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) 属性可以设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="4890d-487">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="4890d-488">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="4890d-488">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4890d-489">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4890d-489">Values aren't case-sensitive.</span></span>

<span data-ttu-id="4890d-490">键：`environment`</span><span class="sxs-lookup"><span data-stu-id="4890d-490">**Key**: `environment`</span></span>  
<span data-ttu-id="4890d-491">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-491">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-492">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="4890d-492">**Default**: `Production`</span></span>  
<span data-ttu-id="4890d-493">**环境变量**：`<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="4890d-493">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="4890d-494">若要设置此值，请使用环境变量或对 `IHostBuilder` 调用 `UseEnvironment`：</span><span class="sxs-lookup"><span data-stu-id="4890d-494">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="4890d-495">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="4890d-495">ShutdownTimeout</span></span>

<span data-ttu-id="4890d-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时。</span><span class="sxs-lookup"><span data-stu-id="4890d-496">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4890d-497">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="4890d-497">The default value is five seconds.</span></span>  <span data-ttu-id="4890d-498">在超时时间段中，主机：</span><span class="sxs-lookup"><span data-stu-id="4890d-498">During the timeout period, the host:</span></span>

* <span data-ttu-id="4890d-499">触发 [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="4890d-499">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="4890d-500">尝试停止托管服务，对服务停止失败的错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="4890d-500">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="4890d-501">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="4890d-501">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="4890d-502">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="4890d-502">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="4890d-503">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="4890d-503">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="4890d-504">键：`shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="4890d-504">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="4890d-505">类型：`int`</span><span class="sxs-lookup"><span data-stu-id="4890d-505">**Type**: `int`</span></span>  
<span data-ttu-id="4890d-506">**默认**：5 秒</span><span class="sxs-lookup"><span data-stu-id="4890d-506">**Default**: 5 seconds</span></span>  
<span data-ttu-id="4890d-507">**环境变量**：`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="4890d-507">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="4890d-508">若要设置此值，请使用环境变量或配置 `HostOptions`。</span><span class="sxs-lookup"><span data-stu-id="4890d-508">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="4890d-509">以下示例将超时设置为 20 秒：</span><span class="sxs-lookup"><span data-stu-id="4890d-509">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="4890d-510">适用于 Web 应用的设置</span><span class="sxs-lookup"><span data-stu-id="4890d-510">Settings for web apps</span></span>

<span data-ttu-id="4890d-511">一些主机设置仅适用于 HTTP 工作负荷。</span><span class="sxs-lookup"><span data-stu-id="4890d-511">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="4890d-512">默认情况下，用来配置这些设置的环境变量可以具有 `DOTNET_` 或 `ASPNETCORE_` 前缀。</span><span class="sxs-lookup"><span data-stu-id="4890d-512">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="4890d-513">`IWebHostBuilder` 上的扩展方法适用于这些设置。</span><span class="sxs-lookup"><span data-stu-id="4890d-513">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="4890d-514">显示如何调用扩展方法的示例代码假定 `webBuilder` 是 `IWebHostBuilder` 的实例，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="4890d-514">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="4890d-515">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="4890d-515">CaptureStartupErrors</span></span>

<span data-ttu-id="4890d-516">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="4890d-516">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="4890d-517">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="4890d-517">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="4890d-518">键：`captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="4890d-518">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="4890d-519">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-519">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-520">**默认**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="4890d-520">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="4890d-521">**环境变量**：`<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="4890d-521">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="4890d-522">若要设置此值，使用配置或调用 `CaptureStartupErrors`：</span><span class="sxs-lookup"><span data-stu-id="4890d-522">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="4890d-523">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="4890d-523">DetailedErrors</span></span>

<span data-ttu-id="4890d-524">如果启用，或环境为 `Development`，应用会捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="4890d-524">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="4890d-525">键：`detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="4890d-525">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="4890d-526">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-526">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-527">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="4890d-527">**Default**: `false`</span></span>  
<span data-ttu-id="4890d-528">**环境变量**：`<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="4890d-528">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="4890d-529">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-529">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="4890d-530">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="4890d-530">HostingStartupAssemblies</span></span>

<span data-ttu-id="4890d-531">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="4890d-531">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="4890d-532">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="4890d-532">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="4890d-533">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="4890d-533">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="4890d-534">键：`hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4890d-534">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="4890d-535">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-535">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-536">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="4890d-536">**Default**: Empty string</span></span>  
<span data-ttu-id="4890d-537">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4890d-537">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="4890d-538">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-538">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="4890d-539">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="4890d-539">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="4890d-540">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="4890d-540">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="4890d-541">键：`hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="4890d-541">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="4890d-542">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-542">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-543">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="4890d-543">**Default**: Empty string</span></span>  
<span data-ttu-id="4890d-544">**环境变量**：`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="4890d-544">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="4890d-545">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-545">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="4890d-546">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="4890d-546">HTTPS_Port</span></span>

<span data-ttu-id="4890d-547">HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="4890d-547">The HTTPS redirect port.</span></span> <span data-ttu-id="4890d-548">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="4890d-548">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="4890d-549">键：`https_port`</span><span class="sxs-lookup"><span data-stu-id="4890d-549">**Key**: `https_port`</span></span>  
<span data-ttu-id="4890d-550">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-550">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-551">**默认**：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="4890d-551">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="4890d-552">**环境变量**：`<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="4890d-552">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="4890d-553">要设置此值，使用配置或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-553">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="4890d-554">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="4890d-554">PreferHostingUrls</span></span>

<span data-ttu-id="4890d-555">指示主机是否应该侦听使用 `IWebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="4890d-555">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="4890d-556">键：`preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="4890d-556">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="4890d-557">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-557">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-558">**默认**：`true`</span><span class="sxs-lookup"><span data-stu-id="4890d-558">**Default**: `true`</span></span>  
<span data-ttu-id="4890d-559">**环境变量**：`<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="4890d-559">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="4890d-560">若要设置此值，请使用环境变量或调用 `PreferHostingUrls`：</span><span class="sxs-lookup"><span data-stu-id="4890d-560">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="4890d-561">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="4890d-561">PreventHostingStartup</span></span>

<span data-ttu-id="4890d-562">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="4890d-562">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="4890d-563">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="4890d-563">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="4890d-564">键：`preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="4890d-564">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="4890d-565">类型：`bool`（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="4890d-565">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="4890d-566">**默认**：`false`</span><span class="sxs-lookup"><span data-stu-id="4890d-566">**Default**: `false`</span></span>  
<span data-ttu-id="4890d-567">**环境变量**：`<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="4890d-567">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="4890d-568">若要设置此值，请使用环境变量或调用 `UseSetting`：</span><span class="sxs-lookup"><span data-stu-id="4890d-568">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="4890d-569">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="4890d-569">StartupAssembly</span></span>

<span data-ttu-id="4890d-570">要搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="4890d-570">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="4890d-571">键：`startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="4890d-571">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="4890d-572">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-572">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-573">**默认**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="4890d-573">**Default**: The app's assembly</span></span>  
<span data-ttu-id="4890d-574">**环境变量**：`<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="4890d-574">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="4890d-575">若要设置此值，请使用环境变量或调用 `UseStartup`。</span><span class="sxs-lookup"><span data-stu-id="4890d-575">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="4890d-576">`UseStartup` 可以采用程序集名称 (`string`) 或类型 (`TStartup`)。</span><span class="sxs-lookup"><span data-stu-id="4890d-576">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="4890d-577">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="4890d-577">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="4890d-578">URL</span><span class="sxs-lookup"><span data-stu-id="4890d-578">URLs</span></span>

<span data-ttu-id="4890d-579">IP 地址或主机地址的分号分隔列表，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="4890d-579">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="4890d-580">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="4890d-580">For example, `http://localhost:123`.</span></span> <span data-ttu-id="4890d-581">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="4890d-581">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="4890d-582">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="4890d-582">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="4890d-583">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="4890d-583">Supported formats vary among servers.</span></span>

<span data-ttu-id="4890d-584">键：`urls`</span><span class="sxs-lookup"><span data-stu-id="4890d-584">**Key**: `urls`</span></span>  
<span data-ttu-id="4890d-585">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-585">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-586">**默认值**：`http://localhost:5000` 和 `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="4890d-586">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="4890d-587">**环境变量**：`<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="4890d-587">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="4890d-588">若要设置此值，请使用环境变量或调用 `UseUrls`：</span><span class="sxs-lookup"><span data-stu-id="4890d-588">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="4890d-589">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="4890d-589">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="4890d-590">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="4890d-590">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="4890d-591">WebRoot</span><span class="sxs-lookup"><span data-stu-id="4890d-591">WebRoot</span></span>

<span data-ttu-id="4890d-592">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) 属性可确定应用静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="4890d-592">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="4890d-593">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="4890d-593">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="4890d-594">键：`webroot`</span><span class="sxs-lookup"><span data-stu-id="4890d-594">**Key**: `webroot`</span></span>  
<span data-ttu-id="4890d-595">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-595">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-596">**默认**：默认值为 `wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="4890d-596">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="4890d-597">{content root}/wwwroot 的路径必须存在。</span><span class="sxs-lookup"><span data-stu-id="4890d-597">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="4890d-598">**环境变量**：`<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="4890d-598">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="4890d-599">若要设置此值，请使用环境变量或对 `IWebHostBuilder` 调用 `UseWebRoot`：</span><span class="sxs-lookup"><span data-stu-id="4890d-599">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="4890d-600">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="4890d-600">For more information, see:</span></span>

* [<span data-ttu-id="4890d-601">基础知识：Web 根目录</span><span class="sxs-lookup"><span data-stu-id="4890d-601">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="4890d-602">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="4890d-602">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="4890d-603">管理主机生存期</span><span class="sxs-lookup"><span data-stu-id="4890d-603">Manage the host lifetime</span></span>

<span data-ttu-id="4890d-604">对生成的 <xref:Microsoft.Extensions.Hosting.IHost> 实现调用方法，以启动和停止应用。</span><span class="sxs-lookup"><span data-stu-id="4890d-604">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="4890d-605">这些方法会影响所有在服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-605">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4890d-606">运行</span><span class="sxs-lookup"><span data-stu-id="4890d-606">Run</span></span>

<span data-ttu-id="4890d-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-607"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="4890d-608">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-608">RunAsync</span></span>

<span data-ttu-id="4890d-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="4890d-609"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="4890d-610">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-610">RunConsoleAsync</span></span>

<span data-ttu-id="4890d-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="4890d-611"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="4890d-612">Start</span><span class="sxs-lookup"><span data-stu-id="4890d-612">Start</span></span>

<span data-ttu-id="4890d-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-613"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="4890d-614">StartAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-614">StartAsync</span></span>

<span data-ttu-id="4890d-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动主机并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="4890d-615"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="4890d-616">在 `StartAsync` 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="4890d-616"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4890d-617">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="4890d-617">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="4890d-618">StopAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-618">StopAsync</span></span>

<span data-ttu-id="4890d-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-619"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="4890d-620">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4890d-620">WaitForShutdown</span></span>

<span data-ttu-id="4890d-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 阻止调用线程，直到 IHostLifetime 触发关闭，例如通过 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM。</span><span class="sxs-lookup"><span data-stu-id="4890d-621"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="4890d-622">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-622">WaitForShutdownAsync</span></span>

<span data-ttu-id="4890d-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="4890d-623"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="4890d-624">外部控件</span><span class="sxs-lookup"><span data-stu-id="4890d-624">External control</span></span>

<span data-ttu-id="4890d-625">使用可从外部调用的方法，能够实现对主机生存期的直接控制：</span><span class="sxs-lookup"><span data-stu-id="4890d-625">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4890d-626">ASP.NET Core 应用配置和启动主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-626">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="4890d-627">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="4890d-627">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="4890d-628">本文介绍 ASP.NET Core 泛型主机 (<xref:Microsoft.Extensions.Hosting.HostBuilder>)，该主机用于无法处理 HTTP 请求的应用。</span><span class="sxs-lookup"><span data-stu-id="4890d-628">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="4890d-629">泛型主机的用途是将 HTTP 管道从 Web 主机 API 中分离出来，从而启用更多的主机方案。</span><span class="sxs-lookup"><span data-stu-id="4890d-629">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="4890d-630">基于泛型主机的消息、后台任务和其他非 HTTP 工作负载可从横切功能（如配置、依赖关系注入 [DI] 和日志记录）中受益。</span><span class="sxs-lookup"><span data-stu-id="4890d-630">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="4890d-631">泛型主机是 ASP.NET Core 2.1 中的新增功能，不适用于 Web 承载方案。</span><span class="sxs-lookup"><span data-stu-id="4890d-631">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="4890d-632">对于 Web 承载方案，请使用 [Web 主机](xref:fundamentals/host/web-host)。</span><span class="sxs-lookup"><span data-stu-id="4890d-632">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4890d-633">泛型主机将在未来版本中替换 Web 主机，并在 HTTP 和非 HTTP 方案中充当主要的主机 API。</span><span class="sxs-lookup"><span data-stu-id="4890d-633">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="4890d-634">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4890d-634">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4890d-635">在 [Visual Studio Code](https://code.visualstudio.com/) 中运行示例应用时，请使用外部或集成终端。</span><span class="sxs-lookup"><span data-stu-id="4890d-635">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="4890d-636">请勿在 `internalConsole` 中运行示例。</span><span class="sxs-lookup"><span data-stu-id="4890d-636">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="4890d-637">在 Visual Studio Code 中设置控制台：</span><span class="sxs-lookup"><span data-stu-id="4890d-637">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="4890d-638">打开 .vscode/launch.json 文件。</span><span class="sxs-lookup"><span data-stu-id="4890d-638">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="4890d-639">在 .NET Core 启动（控制台）配置中，找到控制台条目 。</span><span class="sxs-lookup"><span data-stu-id="4890d-639">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="4890d-640">将值设置为 `externalTerminal` 或 `integratedTerminal`。</span><span class="sxs-lookup"><span data-stu-id="4890d-640">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="4890d-641">介绍</span><span class="sxs-lookup"><span data-stu-id="4890d-641">Introduction</span></span>

<span data-ttu-id="4890d-642">通用主机库位于 <xref:Microsoft.Extensions.Hosting> 命名空间中，由 [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="4890d-642">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="4890d-643">[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)（ASP.NET Core 2.1 或更高版本）中包括 `Microsoft.Extensions.Hosting` 包。</span><span class="sxs-lookup"><span data-stu-id="4890d-643">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="4890d-644"><xref:Microsoft.Extensions.Hosting.IHostedService> 是执行代码的入口点。</span><span class="sxs-lookup"><span data-stu-id="4890d-644"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="4890d-645">每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现都按照 [ConfigureServices 中服务注册](#configureservices)的顺序执行。</span><span class="sxs-lookup"><span data-stu-id="4890d-645">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="4890d-646">主机启动时，每个 <xref:Microsoft.Extensions.Hosting.IHostedService> 上都会调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>，主机正常关闭时，以反向注册顺序调用 <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="4890d-646"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="4890d-647">设置主机</span><span class="sxs-lookup"><span data-stu-id="4890d-647">Set up a host</span></span>

<span data-ttu-id="4890d-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> 是供库和应用初始化、生成和运行主机的主要组件：</span><span class="sxs-lookup"><span data-stu-id="4890d-648"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="4890d-649">选项</span><span class="sxs-lookup"><span data-stu-id="4890d-649">Options</span></span>

<span data-ttu-id="4890d-650"><xref:Microsoft.Extensions.Hosting.HostOptions> 配置 <xref:Microsoft.Extensions.Hosting.IHost> 的选项。</span><span class="sxs-lookup"><span data-stu-id="4890d-650"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="4890d-651">关闭超时值</span><span class="sxs-lookup"><span data-stu-id="4890d-651">Shutdown timeout</span></span>

<span data-ttu-id="4890d-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> 设置 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 的超时值。</span><span class="sxs-lookup"><span data-stu-id="4890d-652"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="4890d-653">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="4890d-653">The default value is five seconds.</span></span>

<span data-ttu-id="4890d-654">`Program.Main` 中的以下选项配置将默认值为 5 秒的关闭超时值增加至 20 秒：</span><span class="sxs-lookup"><span data-stu-id="4890d-654">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="4890d-655">默认服务</span><span class="sxs-lookup"><span data-stu-id="4890d-655">Default services</span></span>

<span data-ttu-id="4890d-656">在主机初始化期间注册以下服务：</span><span class="sxs-lookup"><span data-stu-id="4890d-656">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="4890d-657">[环境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="4890d-657">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="4890d-658">[配置](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="4890d-658">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="4890d-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4890d-659"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="4890d-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="4890d-660"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="4890d-661">[选项](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="4890d-661">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="4890d-662">[日志记录](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="4890d-662">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="4890d-663">主机配置</span><span class="sxs-lookup"><span data-stu-id="4890d-663">Host configuration</span></span>

<span data-ttu-id="4890d-664">主机配置的创建方式如下：</span><span class="sxs-lookup"><span data-stu-id="4890d-664">Host configuration is created by:</span></span>

* <span data-ttu-id="4890d-665">调用 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上的扩展方法以设置[“内容根”](#content-root)和[“环境”](#environment)。</span><span class="sxs-lookup"><span data-stu-id="4890d-665">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="4890d-666">从 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中的配置提供程序读取配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-666">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="4890d-667">扩展方法</span><span class="sxs-lookup"><span data-stu-id="4890d-667">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="4890d-668">应用程序键（名称）</span><span class="sxs-lookup"><span data-stu-id="4890d-668">Application key (name)</span></span>

<span data-ttu-id="4890d-669">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) 属性是在主机构造期间通过主机配置设定的。</span><span class="sxs-lookup"><span data-stu-id="4890d-669">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="4890d-670">要显式设置值，请使用 [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)：</span><span class="sxs-lookup"><span data-stu-id="4890d-670">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="4890d-671">键：`applicationName`</span><span class="sxs-lookup"><span data-stu-id="4890d-671">**Key**: `applicationName`</span></span>  
<span data-ttu-id="4890d-672">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-672">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-673">**默认**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="4890d-673">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="4890d-674">**设置使用**：`HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="4890d-674">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="4890d-675">**环境变量**：`<PREFIX_>APPLICATIONNAME`（`<PREFIX_>` 是 [用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="4890d-675">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="4890d-676">内容根</span><span class="sxs-lookup"><span data-stu-id="4890d-676">Content root</span></span>

<span data-ttu-id="4890d-677">此设置确定主机从哪里开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="4890d-677">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="4890d-678">键：`contentRoot`</span><span class="sxs-lookup"><span data-stu-id="4890d-678">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="4890d-679">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-679">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-680">**默认**：默认为应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="4890d-680">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="4890d-681">**设置使用**：`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="4890d-681">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="4890d-682">**环境变量**：`<PREFIX_>CONTENTROOT`（`<PREFIX_>` 是 [用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="4890d-682">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4890d-683">如果路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="4890d-683">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="4890d-684">有关详细信息，请参阅[基础知识：内容根目录](xref:fundamentals/index#content-root)。</span><span class="sxs-lookup"><span data-stu-id="4890d-684">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="4890d-685">环境</span><span class="sxs-lookup"><span data-stu-id="4890d-685">Environment</span></span>

<span data-ttu-id="4890d-686">设置应用的[环境](xref:fundamentals/environments)。</span><span class="sxs-lookup"><span data-stu-id="4890d-686">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="4890d-687">键：`environment`</span><span class="sxs-lookup"><span data-stu-id="4890d-687">**Key**: `environment`</span></span>  
<span data-ttu-id="4890d-688">类型：`string`</span><span class="sxs-lookup"><span data-stu-id="4890d-688">**Type**: `string`</span></span>  
<span data-ttu-id="4890d-689">**默认**：`Production`</span><span class="sxs-lookup"><span data-stu-id="4890d-689">**Default**: `Production`</span></span>  
<span data-ttu-id="4890d-690">**设置使用**：`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="4890d-690">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="4890d-691">**环境变量**：`<PREFIX_>ENVIRONMENT`（`<PREFIX_>` 是 [用户定义的可选前缀](#configurehostconfiguration)）</span><span class="sxs-lookup"><span data-stu-id="4890d-691">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="4890d-692">可将环境设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="4890d-692">The environment can be set to any value.</span></span> <span data-ttu-id="4890d-693">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="4890d-693">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="4890d-694">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="4890d-694">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="4890d-695">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="4890d-695">ConfigureHostConfiguration</span></span>

<span data-ttu-id="4890d-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为主机创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="4890d-696"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="4890d-697">主机配置用于初始化 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>，以供在应用的构建过程中使用。</span><span class="sxs-lookup"><span data-stu-id="4890d-697">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="4890d-698">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="4890d-698"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4890d-699">主机使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="4890d-699">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="4890d-700">默认情况下不包括提供程序。</span><span class="sxs-lookup"><span data-stu-id="4890d-700">No providers are included by default.</span></span> <span data-ttu-id="4890d-701">必须在 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> 中显式指定应用所需的任何配置提供程序，包括：</span><span class="sxs-lookup"><span data-stu-id="4890d-701">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="4890d-702">文件配置（例如，来自 hostsettings.json 文件）。</span><span class="sxs-lookup"><span data-stu-id="4890d-702">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="4890d-703">环境变量配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-703">Environment variable configuration.</span></span>
* <span data-ttu-id="4890d-704">命令行参数配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-704">Command-line argument configuration.</span></span>
* <span data-ttu-id="4890d-705">任何其他所需的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="4890d-705">Any other required configuration providers.</span></span>

<span data-ttu-id="4890d-706">通过使用 `SetBasePath` 指定应用的基本路径，然后调用其中一个[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)，可以启用主机的文件配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-706">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="4890d-707">示例应用使用 JSON 文件 hostsettings.json，并调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 来使用文件的主机配置设置。</span><span class="sxs-lookup"><span data-stu-id="4890d-707">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="4890d-708">要添加主机的[环境变量配置](xref:fundamentals/configuration/index#environment-variables-configuration-provider)，请在主机生成器上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>。</span><span class="sxs-lookup"><span data-stu-id="4890d-708">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="4890d-709">`AddEnvironmentVariables` 接受用户定义的前缀（可选）。</span><span class="sxs-lookup"><span data-stu-id="4890d-709">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="4890d-710">示例应用使用前缀 `PREFIX_`。</span><span class="sxs-lookup"><span data-stu-id="4890d-710">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="4890d-711">当系统读取环境变量时，便会删除前缀。</span><span class="sxs-lookup"><span data-stu-id="4890d-711">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="4890d-712">配置示例应用的主机后，`PREFIX_ENVIRONMENT` 的环境变量值就变成 `environment` 密钥的主机配置值。</span><span class="sxs-lookup"><span data-stu-id="4890d-712">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="4890d-713">在开发过程中，如果使用 [Visual Studio](https://visualstudio.microsoft.com) 或通过 `dotnet run` 运行应用，可能会在 Properties/launchSettings.json 文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="4890d-713">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="4890d-714">若在开发过程中使用 [Visual Studio Code](https://code.visualstudio.com/)，可能会在 .vscode/launch.json 文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="4890d-714">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="4890d-715">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="4890d-715">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4890d-716">通过调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 可添加[命令行配置](xref:fundamentals/configuration/index#command-line-configuration-provider)。</span><span class="sxs-lookup"><span data-stu-id="4890d-716">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="4890d-717">最后添加命令行配置以允许命令行参数替代之前配置提供程序提供的配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-717">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="4890d-718">hostsettings.json：</span><span class="sxs-lookup"><span data-stu-id="4890d-718">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="4890d-719">可以通过 [applicationName](#application-key-name) 和 [contentRoot](#content-root) 键提供其他配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-719">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="4890d-720">示例 `HostBuilder` 配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="4890d-720">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="4890d-721">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="4890d-721">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4890d-722">通过在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现上调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建应用配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-722">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="4890d-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 使用 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> 来为应用创建 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="4890d-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="4890d-724">可多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="4890d-724"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="4890d-725">应用使用上一次在一个给定键上设置值的选项。</span><span class="sxs-lookup"><span data-stu-id="4890d-725">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="4890d-726">[HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) 中提供 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 创建的配置，以供进行后续操作和在 <xref:Microsoft.Extensions.Hosting.IHost.Services*> 中使用。</span><span class="sxs-lookup"><span data-stu-id="4890d-726">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="4890d-727">应用配置会自动接收 [ConfigureHostConfiguration](#configurehostconfiguration) 提供的主机配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-727">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="4890d-728">示例应用配置使用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>：</span><span class="sxs-lookup"><span data-stu-id="4890d-728">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="4890d-729">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4890d-729">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="4890d-730">appsettings.Development.json：</span><span class="sxs-lookup"><span data-stu-id="4890d-730">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="4890d-731">appsettings.Production.json：</span><span class="sxs-lookup"><span data-stu-id="4890d-731">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="4890d-732">要将设置文件移动到输出目录，请在项目文件中将设置文件指定为 [MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)。</span><span class="sxs-lookup"><span data-stu-id="4890d-732">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="4890d-733">示例应用移动具有以下 `<Content>` 项的 JSON 应用设置文件和 hostsettings.json：</span><span class="sxs-lookup"><span data-stu-id="4890d-733">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="4890d-734">配置扩展方法（如 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 和 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>）需要其他 NuGet 包（如 [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) 和 [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)）。</span><span class="sxs-lookup"><span data-stu-id="4890d-734">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="4890d-735">除非应用使用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)，否则除了核心 [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) 包之外，还必须将这些包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="4890d-735">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="4890d-736">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="4890d-736">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="4890d-737">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="4890d-737">ConfigureServices</span></span>

<span data-ttu-id="4890d-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> 将服务添加到应用的[依赖关系](xref:fundamentals/dependency-injection)注入容器。</span><span class="sxs-lookup"><span data-stu-id="4890d-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4890d-739">可多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>，并得到累计结果。</span><span class="sxs-lookup"><span data-stu-id="4890d-739"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4890d-740">托管服务是一个类，具有实现 <xref:Microsoft.Extensions.Hosting.IHostedService> 接口的后台任务逻辑。</span><span class="sxs-lookup"><span data-stu-id="4890d-740">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="4890d-741">有关详细信息，请参阅 <xref:fundamentals/host/hosted-services>。</span><span class="sxs-lookup"><span data-stu-id="4890d-741">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="4890d-742">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/)使用 `AddHostedService` 扩展方法向应用添加生存期事件 `LifetimeEventsHostedService` 和定时后台任务 `TimedHostedService` 服务：</span><span class="sxs-lookup"><span data-stu-id="4890d-742">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="4890d-743">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="4890d-743">ConfigureLogging</span></span>

<span data-ttu-id="4890d-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> 添加了一个委托来配置提供的 <xref:Microsoft.Extensions.Logging.ILoggingBuilder>。</span><span class="sxs-lookup"><span data-stu-id="4890d-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="4890d-745">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>。</span><span class="sxs-lookup"><span data-stu-id="4890d-745"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="4890d-746">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="4890d-746">UseConsoleLifetime</span></span>

<span data-ttu-id="4890d-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 并调用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 来启动关闭进程。</span><span class="sxs-lookup"><span data-stu-id="4890d-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="4890d-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> 解除阻止 [ RunAsync](#runasync) 和 [WaitForShutdownAsync](#waitforshutdownasync) 等扩展。</span><span class="sxs-lookup"><span data-stu-id="4890d-748"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="4890d-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` 预注册为默认生存期实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-749">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="4890d-750">使用注册的最后一个生存期。</span><span class="sxs-lookup"><span data-stu-id="4890d-750">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="4890d-751">容器配置</span><span class="sxs-lookup"><span data-stu-id="4890d-751">Container configuration</span></span>

<span data-ttu-id="4890d-752">为支持插入其他容器中，主机可以接受 <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>。</span><span class="sxs-lookup"><span data-stu-id="4890d-752">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="4890d-753">提供工厂不属于 DI 容器注册，而是用于创建具体 DI 容器的主机内部函数。</span><span class="sxs-lookup"><span data-stu-id="4890d-753">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="4890d-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) 重写用于创建应用的服务提供程序的默认工厂。</span><span class="sxs-lookup"><span data-stu-id="4890d-754">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="4890d-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 方法托管自定义容器配置。</span><span class="sxs-lookup"><span data-stu-id="4890d-755">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="4890d-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> 提供在基础主机 API 的基础之上配置容器的强类型体验。</span><span class="sxs-lookup"><span data-stu-id="4890d-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="4890d-757">可以利用相加结果多次调用 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>。</span><span class="sxs-lookup"><span data-stu-id="4890d-757"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="4890d-758">为应用创建服务容器：</span><span class="sxs-lookup"><span data-stu-id="4890d-758">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="4890d-759">提供服务容器工厂：</span><span class="sxs-lookup"><span data-stu-id="4890d-759">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="4890d-760">使用该工厂并为应用配置自定义服务容器：</span><span class="sxs-lookup"><span data-stu-id="4890d-760">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="4890d-761">扩展性</span><span class="sxs-lookup"><span data-stu-id="4890d-761">Extensibility</span></span>

<span data-ttu-id="4890d-762">在 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 上使用扩展方法实现主机扩展性。</span><span class="sxs-lookup"><span data-stu-id="4890d-762">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="4890d-763">以下示例介绍扩展方法如何使用 <xref:fundamentals/host/hosted-services> 中所示的 [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) 示例来扩展 <xref:Microsoft.Extensions.Hosting.IHostBuilder> 实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-763">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="4890d-764">应用建立 `UseHostedService` 扩展方法，以注册在 `T` 中传递的托管服务：</span><span class="sxs-lookup"><span data-stu-id="4890d-764">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="4890d-765">管理主机</span><span class="sxs-lookup"><span data-stu-id="4890d-765">Manage the host</span></span>

<span data-ttu-id="4890d-766"><xref:Microsoft.Extensions.Hosting.IHost> 实现负责启动和停止服务容器中注册的 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现。</span><span class="sxs-lookup"><span data-stu-id="4890d-766">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="4890d-767">运行</span><span class="sxs-lookup"><span data-stu-id="4890d-767">Run</span></span>

<span data-ttu-id="4890d-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> 运行应用并阻止调用线程，直到关闭主机：</span><span class="sxs-lookup"><span data-stu-id="4890d-768"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="4890d-769">RunAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-769">RunAsync</span></span>

<span data-ttu-id="4890d-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> 运行应用并返回在触发取消令牌或关闭时完成的 <xref:System.Threading.Tasks.Task>：</span><span class="sxs-lookup"><span data-stu-id="4890d-770"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="4890d-771">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-771">RunConsoleAsync</span></span>

<span data-ttu-id="4890d-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> 启用控制台支持、生成和启动主机，以及等待 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM 关闭。</span><span class="sxs-lookup"><span data-stu-id="4890d-772"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="4890d-773">Start 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-773">Start and StopAsync</span></span>

<span data-ttu-id="4890d-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> 同步启动主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="4890d-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> 尝试在提供的超时时间内停止主机。</span><span class="sxs-lookup"><span data-stu-id="4890d-775"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="4890d-776">StartAsync 和 StopAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-776">StartAsync and StopAsync</span></span>

<span data-ttu-id="4890d-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 启动应用。</span><span class="sxs-lookup"><span data-stu-id="4890d-777"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="4890d-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 停止应用。</span><span class="sxs-lookup"><span data-stu-id="4890d-778"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="4890d-779">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="4890d-779">WaitForShutdown</span></span>

<span data-ttu-id="4890d-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 通过 <xref:Microsoft.Extensions.Hosting.IHostLifetime> 触发，例如 `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`（侦听 <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="4890d-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="4890d-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> 调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>。</span><span class="sxs-lookup"><span data-stu-id="4890d-781"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="4890d-782">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="4890d-782">WaitForShutdownAsync</span></span>

<span data-ttu-id="4890d-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> 返回在通过给定的令牌和调用 <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> 来触发关闭时完成的 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="4890d-783"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="4890d-784">外部控件</span><span class="sxs-lookup"><span data-stu-id="4890d-784">External control</span></span>

<span data-ttu-id="4890d-785">使用可从外部调用的方法，能够实现主机的外部控件：</span><span class="sxs-lookup"><span data-stu-id="4890d-785">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="4890d-786">在 <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> 开始时调用 <xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>，在继续之前，会一直等待该操作完成。</span><span class="sxs-lookup"><span data-stu-id="4890d-786"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="4890d-787">它可用于延迟启动，直到外部事件发出信号。</span><span class="sxs-lookup"><span data-stu-id="4890d-787">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="4890d-788">IHostingEnvironment 接口</span><span class="sxs-lookup"><span data-stu-id="4890d-788">IHostingEnvironment interface</span></span>

<span data-ttu-id="4890d-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 提供有关应用托管环境的信息。</span><span class="sxs-lookup"><span data-stu-id="4890d-789"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="4890d-790">使用[构造函数注入](xref:fundamentals/dependency-injection)获取 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 以使用其属性和扩展方法：</span><span class="sxs-lookup"><span data-stu-id="4890d-790">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="4890d-791">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="4890d-791">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="4890d-792">IApplicationLifetime 接口</span><span class="sxs-lookup"><span data-stu-id="4890d-792">IApplicationLifetime interface</span></span>

<span data-ttu-id="4890d-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 允许启动后和关闭活动，包括正常关闭请求。</span><span class="sxs-lookup"><span data-stu-id="4890d-793"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="4890d-794">接口上的三个属性是用于注册 <xref:System.Action> 方法（用于定义启动和关闭事件）的取消标记。</span><span class="sxs-lookup"><span data-stu-id="4890d-794">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="4890d-795">取消标记</span><span class="sxs-lookup"><span data-stu-id="4890d-795">Cancellation Token</span></span> | <span data-ttu-id="4890d-796">触发条件</span><span class="sxs-lookup"><span data-stu-id="4890d-796">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="4890d-797">主机已完全启动。</span><span class="sxs-lookup"><span data-stu-id="4890d-797">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="4890d-798">主机正在完成正常关闭。</span><span class="sxs-lookup"><span data-stu-id="4890d-798">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="4890d-799">应处理所有请求。</span><span class="sxs-lookup"><span data-stu-id="4890d-799">All requests should be processed.</span></span> <span data-ttu-id="4890d-800">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="4890d-800">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="4890d-801">主机正在执行正常关闭。</span><span class="sxs-lookup"><span data-stu-id="4890d-801">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="4890d-802">仍在处理请求。</span><span class="sxs-lookup"><span data-stu-id="4890d-802">Requests may still be processing.</span></span> <span data-ttu-id="4890d-803">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="4890d-803">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="4890d-804">构造函数将 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> 服务注入到任何类中。</span><span class="sxs-lookup"><span data-stu-id="4890d-804">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="4890d-805">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/)将构造函数注入到 `LifetimeEventsHostedService` 类（一个 <xref:Microsoft.Extensions.Hosting.IHostedService> 实现）中，用于注册事件。</span><span class="sxs-lookup"><span data-stu-id="4890d-805">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="4890d-806">LifetimeEventsHostedService.cs：</span><span class="sxs-lookup"><span data-stu-id="4890d-806">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="4890d-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 请求终止应用。</span><span class="sxs-lookup"><span data-stu-id="4890d-807"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="4890d-808">以下类在调用类的 `Shutdown` 方法时使用 <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> 正常关闭应用：</span><span class="sxs-lookup"><span data-stu-id="4890d-808">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4890d-809">其他资源</span><span class="sxs-lookup"><span data-stu-id="4890d-809">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
