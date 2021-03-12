---
title: ASP.NET Core 中的运行状况检查
author: rick-anderson
description: 了解如何为 ASP.NET Core 基础结构（如应用和数据库）设置运行状况检查。
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
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
uid: host-and-deploy/health-checks
ms.openlocfilehash: 272f1f098ca90434f26d6c057859a00b5519602e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588901"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="0921a-103">ASP.NET Core 中的运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="0921a-104">作者：[Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="0921a-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0921a-105">ASP.NET Core 提供运行状况检查中间件和库，以用于报告应用基础结构组件的运行状况。</span><span class="sxs-lookup"><span data-stu-id="0921a-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="0921a-106">运行状况检查由应用程序作为 HTTP 终结点公开。</span><span class="sxs-lookup"><span data-stu-id="0921a-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="0921a-107">可以为各种实时监视方案配置运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="0921a-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="0921a-108">运行状况探测可以由容器业务流程协调程和负载均衡器用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="0921a-109">例如，容器业务流程协调程序可以通过停止滚动部署或重新启动容器来响应失败的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="0921a-110">负载均衡器可以通过将流量从失败的实例路由到正常实例，来应对不正常的应用。</span><span class="sxs-lookup"><span data-stu-id="0921a-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="0921a-111">可以监视内存、磁盘和其他物理服务器资源的使用情况来了解是否处于正常状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="0921a-112">运行状况检查可以测试应用的依赖项（如数据库和外部服务终结点）以确认是否可用和正常工作。</span><span class="sxs-lookup"><span data-stu-id="0921a-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="0921a-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0921a-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0921a-114">示例应用包含本主题中所述的方案示例。</span><span class="sxs-lookup"><span data-stu-id="0921a-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="0921a-115">若要运行给定方案的示例应用，请在命令行界面中从项目文件夹中使用 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="0921a-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="0921a-116">请参阅示例应用的 README.md 文件和本主题中的方案说明，以了解有关如何使用示例应用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="0921a-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0921a-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="0921a-117">Prerequisites</span></span>

<span data-ttu-id="0921a-118">运行状况检查通常与外部监视服务或容器业务流程协调程序一起用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="0921a-119">向应用添加运行状况检查之前，需确定要使用的监视系统。</span><span class="sxs-lookup"><span data-stu-id="0921a-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="0921a-120">监视系统决定了要创建的运行状况检查类型以及配置其终结点的方式。</span><span class="sxs-lookup"><span data-stu-id="0921a-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="0921a-121">为 ASP.NET Core 应用隐式引用 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 包。</span><span class="sxs-lookup"><span data-stu-id="0921a-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="0921a-122">若要使用 Entity Framework Core 执行运行状况检查，请将包引用添加到 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) 包。</span><span class="sxs-lookup"><span data-stu-id="0921a-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="0921a-123">示例应用提供了启动代码来演示几个方案的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="0921a-124">[数据库探测](#database-probe)方案使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 检查数据库连接的运行状况。</span><span class="sxs-lookup"><span data-stu-id="0921a-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="0921a-125">[DbContext 探测](#entity-framework-core-dbcontext-probe)方案使用 EF Core `DbContext` 检查数据库。</span><span class="sxs-lookup"><span data-stu-id="0921a-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="0921a-126">若要探索数据库方案，示例应用将：</span><span class="sxs-lookup"><span data-stu-id="0921a-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="0921a-127">创建一个数据库，并在 appsettings.json 文件中提供其连接字符串。</span><span class="sxs-lookup"><span data-stu-id="0921a-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="0921a-128">其项目文件中具有以下包引用：</span><span class="sxs-lookup"><span data-stu-id="0921a-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="0921a-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="0921a-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="0921a-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0921a-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="0921a-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="0921a-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="0921a-132">另一个运行状况检查方案演示如何将运行状况检查筛选到某个管理端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="0921a-133">示例应用要求创建包含管理 URL 和管理端口的 Properties/launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="0921a-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="0921a-134">有关详细信息，请参阅[按端口筛选](#filter-by-port)部分。</span><span class="sxs-lookup"><span data-stu-id="0921a-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="0921a-135">基本运行状况探测</span><span class="sxs-lookup"><span data-stu-id="0921a-135">Basic health probe</span></span>

<span data-ttu-id="0921a-136">对于许多应用，报告应用在处理请求方面的可用性（运行情况）的基本运行状况探测配置足以发现应用的状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="0921a-137">基本配置会注册运行状况检查服务，并调用运行状况检查中间件以通过运行状况响应在 URL 终结点处进行响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="0921a-138">默认情况下，不会注册任何特定运行状况检查来测试任何特定依赖项或子系统。</span><span class="sxs-lookup"><span data-stu-id="0921a-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="0921a-139">如果能够在运行状况终结点 URL 处进行响应，则应用被视为正常。</span><span class="sxs-lookup"><span data-stu-id="0921a-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="0921a-140">默认响应编写器会以纯文本响应形式将状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 写回到客户端，以便指示 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="0921a-141">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0921a-142">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="0921a-143">在示例应用中，在 `/health` 处创建运行状况检查终结点 (BasicStartup.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="0921a-144">若要使用示例应用运行基本配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="0921a-145">Docker 示例</span><span class="sxs-lookup"><span data-stu-id="0921a-145">Docker example</span></span>

<span data-ttu-id="0921a-146">[Docker](xref:host-and-deploy/docker/index) 提供内置 `HEALTHCHECK` 指令，该指令可以用于检查使用基本运行状况检查配置的应用的状态：</span><span class="sxs-lookup"><span data-stu-id="0921a-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="0921a-147">创建运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-147">Create health checks</span></span>

<span data-ttu-id="0921a-148">运行状况检查通过实现 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口进行创建。</span><span class="sxs-lookup"><span data-stu-id="0921a-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="0921a-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 方法会返回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，它以 `Healthy`、`Degraded` 或 `Unhealthy` 的形式指示运行状况。</span><span class="sxs-lookup"><span data-stu-id="0921a-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="0921a-150">结果会使用可配置状态代码（配置在[运行状况检查选项](#health-check-options)部分中进行介绍）编写为纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="0921a-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 还可以返回可选的键值对。</span><span class="sxs-lookup"><span data-stu-id="0921a-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="0921a-152">下面的 `ExampleHealthCheck` 类演示运行状况检查的布局。</span><span class="sxs-lookup"><span data-stu-id="0921a-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="0921a-153">运行状况检查逻辑位于 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="0921a-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="0921a-154">以下示例将虚拟变量 `healthCheckResultHealthy` 设为 `true`。</span><span class="sxs-lookup"><span data-stu-id="0921a-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="0921a-155">如果 `healthCheckResultHealthy` 的值设为 `false`，则返回 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="0921a-156">注册运行状况检查服务</span><span class="sxs-lookup"><span data-stu-id="0921a-156">Register health check services</span></span>

<span data-ttu-id="0921a-157">`ExampleHealthCheck` 类型使用 `Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 添加到运行状况检查服务：</span><span class="sxs-lookup"><span data-stu-id="0921a-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="0921a-158">以下示例中显示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 重载会设置要在运行状况检查报告失败时报告的失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="0921a-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="0921a-159">如果失败状态设置为 `null`（默认值），则会报告 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0921a-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="0921a-160">此重载对于库创建者是一种十分有用的方案，在这种情况下，如果运行状况检查实现遵循该设置，则在发生运行状况检查失败时，应用会强制实施库所指示的失败状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="0921a-161">标记用于筛选运行状况检查（在[筛选运行状况检查](#filter-health-checks)部分中进行了进一步介绍）。</span><span class="sxs-lookup"><span data-stu-id="0921a-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="0921a-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 还可以执行 lambda 函数。</span><span class="sxs-lookup"><span data-stu-id="0921a-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="0921a-163">在以下示例中，运行状况检查名称指定为 `Example`，并且检查始终返回正常状态：</span><span class="sxs-lookup"><span data-stu-id="0921a-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="0921a-164">调用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> 将参数传递到运行状况检查实现。</span><span class="sxs-lookup"><span data-stu-id="0921a-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="0921a-165">在以下示例中，`TestHealthCheckWithArgs` 接受一个整数和一个字符串，以便在调用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 时使用：</span><span class="sxs-lookup"><span data-stu-id="0921a-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="0921a-166">`TestHealthCheckWithArgs` 通过使用传递到实现的整数和字符串调用 `AddTypeActivatedCheck` 来注册：</span><span class="sxs-lookup"><span data-stu-id="0921a-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="0921a-167">使用运行状况检查路由</span><span class="sxs-lookup"><span data-stu-id="0921a-167">Use Health Checks Routing</span></span>

<span data-ttu-id="0921a-168">在 `Startup.Configure` 内，使用终结点 URL 或相对路径在终结点生成器上调用 `MapHealthChecks`：</span><span class="sxs-lookup"><span data-stu-id="0921a-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="0921a-169">需要主机</span><span class="sxs-lookup"><span data-stu-id="0921a-169">Require host</span></span>

<span data-ttu-id="0921a-170">调用 `RequireHost` 以便为运行状况检查终结点指定一个或多个允许的主机。</span><span class="sxs-lookup"><span data-stu-id="0921a-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="0921a-171">主机应为 Unicode 而不是 punycode，且可以包含端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="0921a-172">如果未提供集合，则接受任何主机。</span><span class="sxs-lookup"><span data-stu-id="0921a-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="0921a-173">有关详细信息，请参阅[按端口筛选](#filter-by-port)部分。</span><span class="sxs-lookup"><span data-stu-id="0921a-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="0921a-174">需要授权</span><span class="sxs-lookup"><span data-stu-id="0921a-174">Require authorization</span></span>

<span data-ttu-id="0921a-175">调用 `RequireAuthorization` 以在状况检查请求终结点上运行身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="0921a-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="0921a-176">`RequireAuthorization` 重载接受一个或多个授权策略。</span><span class="sxs-lookup"><span data-stu-id="0921a-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="0921a-177">如果未提供策略，则使用默认的授权策略。</span><span class="sxs-lookup"><span data-stu-id="0921a-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="0921a-178">启用跨域请求 (CORS)</span><span class="sxs-lookup"><span data-stu-id="0921a-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="0921a-179">尽管从浏览器手动执行运行状况检查不是常见的使用方案，但可以通过在运行状况检查终结点上调用 `RequireCors` 来启用 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="0921a-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="0921a-180">`RequireCors` 重载接受 CORS 策略生成器委托 (`CorsPolicyBuilder`) 或策略名称。</span><span class="sxs-lookup"><span data-stu-id="0921a-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="0921a-181">如果未提供策略，则使用默认的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="0921a-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="0921a-182">有关详细信息，请参阅 <xref:security/cors>。</span><span class="sxs-lookup"><span data-stu-id="0921a-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="0921a-183">运行状况检查选项</span><span class="sxs-lookup"><span data-stu-id="0921a-183">Health check options</span></span>

<span data-ttu-id="0921a-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 使你可以自定义运行状况检查行为：</span><span class="sxs-lookup"><span data-stu-id="0921a-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="0921a-185">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="0921a-186">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="0921a-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="0921a-187">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="0921a-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="0921a-188">自定义输出</span><span class="sxs-lookup"><span data-stu-id="0921a-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="0921a-189">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-189">Filter health checks</span></span>

<span data-ttu-id="0921a-190">默认情况下，运行状况检查中间件会运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="0921a-191">若要运行运行状况检查的子集，请提供向 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 选项返回布尔值的函数。</span><span class="sxs-lookup"><span data-stu-id="0921a-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="0921a-192">在以下示例中，`Bar` 运行状况检查在函数条件语句 中由于其标记 (`bar_tag`) 而被筛选掉，在条件语句中，仅当运行状况检查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 属性与 `foo_tag` 或 `baz_tag` 匹配时才返回 `true`：</span><span class="sxs-lookup"><span data-stu-id="0921a-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="0921a-193">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="0921a-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="0921a-194">在 `Startup.Configure` 中，`Predicate` 筛选出“Bar”运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="0921a-195">仅 Foo 和 Baz 执行：</span><span class="sxs-lookup"><span data-stu-id="0921a-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="0921a-196">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="0921a-196">Customize the HTTP status code</span></span>

<span data-ttu-id="0921a-197">使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 可自定义运行状况状态到 HTTP 状态代码的映射。</span><span class="sxs-lookup"><span data-stu-id="0921a-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="0921a-198">以下 <xref:Microsoft.AspNetCore.Http.StatusCodes> 分配是中间件所使用的默认值。</span><span class="sxs-lookup"><span data-stu-id="0921a-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="0921a-199">更改状态代码值以满足要求。</span><span class="sxs-lookup"><span data-stu-id="0921a-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="0921a-200">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="0921a-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="0921a-201">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="0921a-201">Suppress cache headers</span></span>

<span data-ttu-id="0921a-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制运行状况检查中间件是否将 HTTP 标头添加到探测响应以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="0921a-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="0921a-203">如果值为 `false`（默认值），则中间件会设置或替代 `Cache-Control`、`Expires` 和 `Pragma` 标头以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="0921a-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="0921a-204">如果值为 `true`，则中间件不会修改响应的缓存标头。</span><span class="sxs-lookup"><span data-stu-id="0921a-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="0921a-205">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="0921a-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="0921a-206">自定义输出</span><span class="sxs-lookup"><span data-stu-id="0921a-206">Customize output</span></span>

<span data-ttu-id="0921a-207">在 `Startup.Configure` 中，将 [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) 选项设置为编写响应的委托：</span><span class="sxs-lookup"><span data-stu-id="0921a-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="0921a-208">默认委托会使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 字符串值编写最小的纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="0921a-209">以下自定义委托输出自定义 JSON 响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="0921a-210">示例应用中的第一个示例演示如何使用 <xref:System.Text.Json?displayProperty=fullName>：</span><span class="sxs-lookup"><span data-stu-id="0921a-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="0921a-211">第二个示例演示如何使用 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)：</span><span class="sxs-lookup"><span data-stu-id="0921a-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="0921a-212">在示例应用中，注释掉“CustomWriterStartup.cs”中的 `SYSTEM_TEXT_JSON` [预处理器指令](xref:index#preprocessor-directives-in-sample-code)，以启用 `WriteResponse` 的 `Newtonsoft.Json` 版本。</span><span class="sxs-lookup"><span data-stu-id="0921a-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="0921a-213">运行状况检查 API 不为复杂 JSON 返回格式提供内置支持，因为该格式特定于你选择的监视系统。</span><span class="sxs-lookup"><span data-stu-id="0921a-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="0921a-214">必要时自定义上述示例中的响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="0921a-215">有关使用 `System.Text.Json` 执行 JSON 序列化的详细信息，请参阅[如何在 .NET 中序列化和反序列化 JSON](/dotnet/standard/serialization/system-text-json-how-to)。</span><span class="sxs-lookup"><span data-stu-id="0921a-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="0921a-216">数据库探测</span><span class="sxs-lookup"><span data-stu-id="0921a-216">Database probe</span></span>

<span data-ttu-id="0921a-217">运行状况检查可以指定数据库查询作为布尔测试来运行，以指示数据库是否在正常响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="0921a-218">示例应用使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)（ASP.NET Core 应用的运行状况检查库）对 SQL Server 数据库执行运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="0921a-219">`AspNetCore.Diagnostics.HealthChecks` 对数据库执行 `SELECT 1` 查询以确认与数据库的连接是否正常。</span><span class="sxs-lookup"><span data-stu-id="0921a-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="0921a-220">使用查询检查数据库连接时，请选择快速返回的查询。</span><span class="sxs-lookup"><span data-stu-id="0921a-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="0921a-221">查询方法会面临使数据库过载和降低其性能的风险。</span><span class="sxs-lookup"><span data-stu-id="0921a-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="0921a-222">在大多数情况下，无需运行测试查询。</span><span class="sxs-lookup"><span data-stu-id="0921a-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="0921a-223">只需建立成功的数据库连接便足矣。</span><span class="sxs-lookup"><span data-stu-id="0921a-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="0921a-224">如果发现需要运行查询，请选择简单的 SELECT 查询，如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="0921a-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="0921a-225">包括对 [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="0921a-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="0921a-226">在示例应用的 appsettings.json 文件中提供有效数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="0921a-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="0921a-227">应用使用名为 `HealthCheckSample` 的 SQL Server 数据库：</span><span class="sxs-lookup"><span data-stu-id="0921a-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="0921a-228">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0921a-229">示例应用使用数据库的连接字符串 (DbHealthStartup.cs) 调用 `AddSqlServer` 方法：</span><span class="sxs-lookup"><span data-stu-id="0921a-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0921a-230">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="0921a-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="0921a-231">若要使用示例应用运行数据库探测方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="0921a-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="0921a-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="0921a-233">Entity Framework Core DbContext 探测</span><span class="sxs-lookup"><span data-stu-id="0921a-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="0921a-234">`DbContext` 检查确认应用可以与为 EF Core `DbContext` 配置的数据库通信。</span><span class="sxs-lookup"><span data-stu-id="0921a-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="0921a-235">满足以下条件的应用支持 `DbContext` 检查：</span><span class="sxs-lookup"><span data-stu-id="0921a-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="0921a-236">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="0921a-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="0921a-237">包括对 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="0921a-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="0921a-238">`AddDbContextCheck<TContext>` 为 `DbContext` 注册运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="0921a-239">`DbContext` 作为方法的 `TContext` 提供。</span><span class="sxs-lookup"><span data-stu-id="0921a-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="0921a-240">重载可用于配置失败状态、标记和自定义测试查询。</span><span class="sxs-lookup"><span data-stu-id="0921a-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="0921a-241">默认情况下：</span><span class="sxs-lookup"><span data-stu-id="0921a-241">By default:</span></span>

* <span data-ttu-id="0921a-242">`DbContextHealthCheck` 调用 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="0921a-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="0921a-243">可以自定义在使用 `AddDbContextCheck` 方法重载检查运行状况时运行的操作。</span><span class="sxs-lookup"><span data-stu-id="0921a-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="0921a-244">运行状况检查的名称是 `TContext` 类型的名称。</span><span class="sxs-lookup"><span data-stu-id="0921a-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="0921a-245">在示例应用中，`AppDbContext` 会提供给 `AddDbContextCheck`，并在 `Startup.ConfigureServices` 中注册为服务 (DbContextHealthStartup.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0921a-246">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="0921a-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="0921a-247">若要使用示例应用运行 `DbContext` 探测方案，请确认连接字符串指定的数据库在 SQL Server 实例中不存在。</span><span class="sxs-lookup"><span data-stu-id="0921a-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="0921a-248">如果该数据库存在，请删除它。</span><span class="sxs-lookup"><span data-stu-id="0921a-248">If the database exists, delete it.</span></span>

<span data-ttu-id="0921a-249">在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="0921a-250">在应用运行之后，在浏览器中对 `/health` 终结点发出请求，从而检查运行状况。</span><span class="sxs-lookup"><span data-stu-id="0921a-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="0921a-251">数据库和 `AppDbContext` 不存在，因此应用提供以下响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="0921a-252">触发示例应用以创建数据库。</span><span class="sxs-lookup"><span data-stu-id="0921a-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="0921a-253">向 `/createdatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="0921a-254">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="0921a-255">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="0921a-256">数据库和上下文存在，因此应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="0921a-257">触发示例应用以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="0921a-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="0921a-258">向 `/deletedatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="0921a-259">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="0921a-260">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="0921a-261">应用提供不正常的响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="0921a-262">单独的就绪情况和运行情况探测</span><span class="sxs-lookup"><span data-stu-id="0921a-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="0921a-263">在某些托管方案中，会使用一对区分两种应用状态的运行状况检查：</span><span class="sxs-lookup"><span data-stu-id="0921a-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="0921a-264">“就绪情况”指示情况是否为应用正常运行，但未准备好接收请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-264">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="0921a-265">“运行情况”指示情况是否为应用已崩溃且必须重启。</span><span class="sxs-lookup"><span data-stu-id="0921a-265">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="0921a-266">请看下面的示例：应用必须下载大型配置文件才能处理请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-266">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="0921a-267">如果初始下载失败，我们不希望重启该应用，因为该应用可以多次尝试下载该文件。</span><span class="sxs-lookup"><span data-stu-id="0921a-267">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="0921a-268">我们使用运行情况探测来描述进程的运行情况，不执行其他检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-268">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="0921a-269">我们还想要在配置文件下载成功之前阻止将请求发送到应用。</span><span class="sxs-lookup"><span data-stu-id="0921a-269">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="0921a-270">我们使用就绪情况探测指示“未就绪”状态，直到下载成功并且应用已准备好接收请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-270">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="0921a-271">示例应用包含运行状况检查，以报告[托管服务](xref:fundamentals/host/hosted-services)中长时间运行的启动任务的完成。</span><span class="sxs-lookup"><span data-stu-id="0921a-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="0921a-272">`StartupHostedServiceHealthCheck` 公开了属性 `StartupTaskCompleted`，托管服务在其长时间运行的任务完成时可以将该属性设置为 `true` (StartupHostedServiceHealthCheck.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="0921a-273">长时间运行的后台任务由[托管服务](xref:fundamentals/host/hosted-services) (Services/StartupHostedService) 启动。</span><span class="sxs-lookup"><span data-stu-id="0921a-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="0921a-274">在该任务结束时，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="0921a-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="0921a-275">运行状况检查在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 与托管服务一起注册。</span><span class="sxs-lookup"><span data-stu-id="0921a-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="0921a-276">因为托管服务必须对运行状况检查设置该属性，所以运行状况检查也会在服务容器 (LivenessProbeStartup.cs) 中进行注册：</span><span class="sxs-lookup"><span data-stu-id="0921a-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0921a-277">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="0921a-278">在示例应用中，在以下位置创建运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="0921a-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="0921a-279">`/health/ready`（用于就绪状态检查）。</span><span class="sxs-lookup"><span data-stu-id="0921a-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="0921a-280">就绪情况检查会将运行状况检查筛选到具有 `ready` 标记的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="0921a-281">`/health/live`（用于运行情况检查）。</span><span class="sxs-lookup"><span data-stu-id="0921a-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="0921a-282">运行情况检查通过在 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 中返回 `false` 来筛选出 `StartupHostedServiceHealthCheck`（有关详细信息，请参阅[筛选运行状况检查](#filter-health-checks)）</span><span class="sxs-lookup"><span data-stu-id="0921a-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="0921a-283">在以下示例代码中：</span><span class="sxs-lookup"><span data-stu-id="0921a-283">In the following example code:</span></span>

* <span data-ttu-id="0921a-284">就绪状态检查将所有已注册的检查与“ready”标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="0921a-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="0921a-285">`Predicate` 将排除所有检查并返回 200-Ok。</span><span class="sxs-lookup"><span data-stu-id="0921a-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="0921a-286">若要使用示例应用运行就绪情况/运行情况配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="0921a-287">在浏览器中，访问 `/health/ready` 几次，直到过了 15 秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="0921a-288">运行状况检查会在前 15 秒内报告“运行不正常”。</span><span class="sxs-lookup"><span data-stu-id="0921a-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="0921a-289">15 秒之后，终结点会报告“运行正常”，这反映托管服务完成了长时间运行的任务。</span><span class="sxs-lookup"><span data-stu-id="0921a-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="0921a-290">此示例还创建了一个运行第一个就绪检查的运行状况检查发布服务器（<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现），延迟时间为两秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="0921a-291">有关详细信息，请参阅[运行状况检查发布服务器](#health-check-publisher)部分。</span><span class="sxs-lookup"><span data-stu-id="0921a-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="0921a-292">Kubernetes 示例</span><span class="sxs-lookup"><span data-stu-id="0921a-292">Kubernetes example</span></span>

<span data-ttu-id="0921a-293">在诸如 [Kubernetes](https://kubernetes.io/) 这类环境中，使用单独的就绪情况和运行情况检查会十分有用。</span><span class="sxs-lookup"><span data-stu-id="0921a-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="0921a-294">在 Kubernetes 中，应用可能需要在接受请求之前执行耗时的启动工作，如基础数据库可用性测试。</span><span class="sxs-lookup"><span data-stu-id="0921a-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="0921a-295">使用单独检查使业务流程协调程序可以区分应用是否正常运行但尚未准备就绪，或是应用程序是否未能启动。</span><span class="sxs-lookup"><span data-stu-id="0921a-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="0921a-296">有关 Kubernetes 中的就绪情况和运行情况探测的详细信息，请参阅 Kubernetes 文档中的[配置运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。</span><span class="sxs-lookup"><span data-stu-id="0921a-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="0921a-297">以下示例演示如何使用 Kubernetes 就绪情况探测配置：</span><span class="sxs-lookup"><span data-stu-id="0921a-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="0921a-298">具有自定义响应编写器的基于指标的探测</span><span class="sxs-lookup"><span data-stu-id="0921a-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="0921a-299">示例应用演示具有自定义响应编写器的内存运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="0921a-300">如果应用使用的内存多于给定内存阈值（在示例应用中为 1 GB），则 `MemoryHealthCheck` 报告降级状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="0921a-301"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 包括应用的垃圾回收器 (GC) 信息 (MemoryHealthCheck.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="0921a-302">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0921a-303">`MemoryHealthCheck` 注册为服务，而不是通过将运行状况检查传递到 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 来启用它。</span><span class="sxs-lookup"><span data-stu-id="0921a-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="0921a-304">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 注册服务都可供运行状况检查服务和中间件使用。</span><span class="sxs-lookup"><span data-stu-id="0921a-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="0921a-305">建议将运行状况检查服务注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="0921a-306">在示例应用的“CustomWriterStartup.cs”中：</span><span class="sxs-lookup"><span data-stu-id="0921a-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="0921a-307">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="0921a-308">当执行运行状况检查时，将 `WriteResponse` 委托提供给 <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 属性，用于输出自定义 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="0921a-309">`WriteResponse` 委托将 `CompositeHealthCheckResult` 格式化为 JSON 对象，并生成运行状况检查响应的 JSON 输出。</span><span class="sxs-lookup"><span data-stu-id="0921a-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="0921a-310">有关详细信息，请参阅[自定义输出](#customize-output)部分。</span><span class="sxs-lookup"><span data-stu-id="0921a-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="0921a-311">若要使用示例应用运行具有自定义响应编写器输出的基于指标的探测，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="0921a-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包括基于指标的运行状况检查方案（包括磁盘存储和最大值运行情况检查）。</span><span class="sxs-lookup"><span data-stu-id="0921a-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="0921a-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="0921a-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="0921a-314">按端口筛选</span><span class="sxs-lookup"><span data-stu-id="0921a-314">Filter by port</span></span>

<span data-ttu-id="0921a-315">使用 URL 模式在 `MapHealthChecks` 上调用 `RequireHost`，该 URL 模式指定一个端口，以使运行状况检查请求限于指定端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="0921a-316">这通常用于在容器环境中公开用于监视服务的端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="0921a-317">示例应用使用[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables)配置端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="0921a-318">端口在 launchSettings.json 文件设置，并通过环境变量传递到配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="0921a-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="0921a-319">还必须配置服务器以在管理端口上侦听请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="0921a-320">若要使用示例应用演示管理端口配置，请在 Properties 文件夹中创建 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="0921a-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="0921a-321">示例应用中的以下 Properties/launchSettings.json 文件未包含在示例应用的项目文件中，必须手动创建：</span><span class="sxs-lookup"><span data-stu-id="0921a-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="0921a-322">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0921a-323">通过在 `Startup.Configure` 中调用 `MapHealthChecks` 来创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="0921a-324">在示例应用中，在 `Startup.Configure` 中的终结点上调用 `RequireHost` 将从配置中指定管理端口：</span><span class="sxs-lookup"><span data-stu-id="0921a-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="0921a-325">在示例应用中，在 `Startup.Configure` 中创建终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="0921a-326">在以下示例代码中：</span><span class="sxs-lookup"><span data-stu-id="0921a-326">In the following example code:</span></span>

* <span data-ttu-id="0921a-327">就绪状态检查将所有已注册的检查与“ready”标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="0921a-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="0921a-328">`Predicate` 将排除所有检查并返回 200-Ok。</span><span class="sxs-lookup"><span data-stu-id="0921a-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="0921a-329">可以通过在代码中显式设置管理端口，来避免在示例应用中创建 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="0921a-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="0921a-330">在创建 <xref:Microsoft.Extensions.Hosting.HostBuilder> 的 Program.cs中，添加对 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> 的调用并提供应用的管理端口终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="0921a-331">在 ManagementPortStartup.cs 的 `Configure` 中，使用 `RequireHost` 指定管理端口：</span><span class="sxs-lookup"><span data-stu-id="0921a-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="0921a-332">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="0921a-332">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="0921a-333">ManagementPortStartup.cs：</span><span class="sxs-lookup"><span data-stu-id="0921a-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="0921a-334">若要使用示例应用运行管理端口配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="0921a-335">分发运行状况检查库</span><span class="sxs-lookup"><span data-stu-id="0921a-335">Distribute a health check library</span></span>

<span data-ttu-id="0921a-336">将运行状况检查作为库进行分发：</span><span class="sxs-lookup"><span data-stu-id="0921a-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="0921a-337">编写将 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口作为独立类来实现的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="0921a-338">该类可以依赖于[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)类型激活和[命名选项](xref:fundamentals/configuration/options)来访问配置数据。</span><span class="sxs-lookup"><span data-stu-id="0921a-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="0921a-339">在 `CheckHealthAsync` 的运行状况检查逻辑中：</span><span class="sxs-lookup"><span data-stu-id="0921a-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="0921a-340">方法中使用 `data1` 和 `data2` 运行探测的运行状况检查逻辑。</span><span class="sxs-lookup"><span data-stu-id="0921a-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="0921a-341">`AccessViolationException` 已处理。</span><span class="sxs-lookup"><span data-stu-id="0921a-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="0921a-342">发生 <xref:System.AccessViolationException> 时，将返回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 和 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，以允许用户配置运行状况检查失败状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="0921a-343">使用参数编写一个扩展方法，所使用的应用会在其 `Startup.Configure` 方法中调用它。</span><span class="sxs-lookup"><span data-stu-id="0921a-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="0921a-344">在以下示例中，假设以下运行状况检查方法签名：</span><span class="sxs-lookup"><span data-stu-id="0921a-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="0921a-345">前面的签名指示 `ExampleHealthCheck` 需要其他数据来处理运行状况检查探测逻辑。</span><span class="sxs-lookup"><span data-stu-id="0921a-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="0921a-346">当运行状况检查向扩展方法注册时，数据会提供给用于创建运行状况检查实例的委托。</span><span class="sxs-lookup"><span data-stu-id="0921a-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="0921a-347">在以下示例中，调用方会指定可选的：</span><span class="sxs-lookup"><span data-stu-id="0921a-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="0921a-348">运行状况检查名称 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="0921a-348">health check name (`name`).</span></span> <span data-ttu-id="0921a-349">如果为 `null`，则使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="0921a-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="0921a-350">运行状况检查的字符串数据点 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="0921a-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="0921a-351">运行状况检查的整数数据点 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="0921a-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="0921a-352">如果为 `null`，则使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="0921a-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="0921a-353">失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="0921a-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="0921a-354">默认值为 `null`。</span><span class="sxs-lookup"><span data-stu-id="0921a-354">The default is `null`.</span></span> <span data-ttu-id="0921a-355">如果为 `null`，则报告失败状态 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0921a-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="0921a-356">标记 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="0921a-356">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="0921a-357">运行状况检查发布服务器</span><span class="sxs-lookup"><span data-stu-id="0921a-357">Health Check Publisher</span></span>

<span data-ttu-id="0921a-358">当 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 添加到服务容器时，运行状况检查系统，会定期执行运行状况检查并使用结果调用 `PublishAsync`。</span><span class="sxs-lookup"><span data-stu-id="0921a-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="0921a-359">在期望每个进程定期调用监视系统以便确定运行状况的基于推送的运行状况监视系统方案中，这十分有用。</span><span class="sxs-lookup"><span data-stu-id="0921a-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="0921a-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 接口具有单个方法：</span><span class="sxs-lookup"><span data-stu-id="0921a-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="0921a-361">使用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可设置：</span><span class="sxs-lookup"><span data-stu-id="0921a-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="0921a-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>：在应用启动后且在应用执行 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例之前所应用的初始延迟。</span><span class="sxs-lookup"><span data-stu-id="0921a-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="0921a-363">延迟在启动时应用一次，不适用于后续迭代。</span><span class="sxs-lookup"><span data-stu-id="0921a-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="0921a-364">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-364">The default value is five seconds.</span></span>
* <span data-ttu-id="0921a-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>：<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 执行的时间。</span><span class="sxs-lookup"><span data-stu-id="0921a-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="0921a-366">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="0921a-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>：如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> 为 `null`（默认值），则运行状况检查发布服务器服务运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="0921a-368">若要运行运行状况检查的子集，请提供用于筛选检查集的函数。</span><span class="sxs-lookup"><span data-stu-id="0921a-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="0921a-369">每个时间段都会评估谓词。</span><span class="sxs-lookup"><span data-stu-id="0921a-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="0921a-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>：执行所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例的运行状况检查的超时时间。</span><span class="sxs-lookup"><span data-stu-id="0921a-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="0921a-371">在不超时的情况下，使用 <xref:System.Threading.Timeout.InfiniteTimeSpan> 执行。</span><span class="sxs-lookup"><span data-stu-id="0921a-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="0921a-372">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="0921a-373">在示例应用中，`ReadinessPublisher` 是 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现。</span><span class="sxs-lookup"><span data-stu-id="0921a-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="0921a-374">在以下日志级别，针对每次检查记录运行状况检查状态：</span><span class="sxs-lookup"><span data-stu-id="0921a-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="0921a-375">如果运行状况检查状态为 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>，则为信息 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)。</span><span class="sxs-lookup"><span data-stu-id="0921a-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="0921a-376">如果状态为 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 或 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>，则为错误 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)。</span><span class="sxs-lookup"><span data-stu-id="0921a-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="0921a-377">在示例应用的 `LivenessProbeStartup` 示例中，`StartupHostedService` 就绪状态检查有两秒的启动延迟，并且每 30 秒运行一次检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="0921a-378">为激活 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现，示例将 `ReadinessPublisher` 注册为[依存关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中的单一实例服务：</span><span class="sxs-lookup"><span data-stu-id="0921a-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="0921a-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包括多个系统的发布服务器（包括 [Application Insights](/azure/application-insights/app-insights-overview)）。</span><span class="sxs-lookup"><span data-stu-id="0921a-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="0921a-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="0921a-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="0921a-381">使用 MapWhen 限制运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="0921a-382">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 对运行状况检查终结点的请求管道进行条件分支。</span><span class="sxs-lookup"><span data-stu-id="0921a-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="0921a-383">在以下示例中，如果收到 `api/HealthCheck` 终结点的 GET 请求，`MapWhen` 将对请求管道进行分支以激活运行状况检查中间件：</span><span class="sxs-lookup"><span data-stu-id="0921a-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="0921a-384">有关详细信息，请参阅 <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>。</span><span class="sxs-lookup"><span data-stu-id="0921a-384">For more information, see <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0921a-385">ASP.NET Core 提供运行状况检查中间件和库，以用于报告应用基础结构组件的运行状况。</span><span class="sxs-lookup"><span data-stu-id="0921a-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="0921a-386">运行状况检查由应用程序作为 HTTP 终结点公开。</span><span class="sxs-lookup"><span data-stu-id="0921a-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="0921a-387">可以为各种实时监视方案配置运行状况检查终结点：</span><span class="sxs-lookup"><span data-stu-id="0921a-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="0921a-388">运行状况探测可以由容器业务流程协调程和负载均衡器用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="0921a-389">例如，容器业务流程协调程序可以通过停止滚动部署或重新启动容器来响应失败的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="0921a-390">负载均衡器可以通过将流量从失败的实例路由到正常实例，来应对不正常的应用。</span><span class="sxs-lookup"><span data-stu-id="0921a-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="0921a-391">可以监视内存、磁盘和其他物理服务器资源的使用情况来了解是否处于正常状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="0921a-392">运行状况检查可以测试应用的依赖项（如数据库和外部服务终结点）以确认是否可用和正常工作。</span><span class="sxs-lookup"><span data-stu-id="0921a-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="0921a-393">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0921a-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0921a-394">示例应用包含本主题中所述的方案示例。</span><span class="sxs-lookup"><span data-stu-id="0921a-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="0921a-395">若要运行给定方案的示例应用，请在命令行界面中从项目文件夹中使用 [dotnet run](/dotnet/core/tools/dotnet-run) 命令。</span><span class="sxs-lookup"><span data-stu-id="0921a-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="0921a-396">请参阅示例应用的 README.md 文件和本主题中的方案说明，以了解有关如何使用示例应用的详细信息。</span><span class="sxs-lookup"><span data-stu-id="0921a-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0921a-397">先决条件</span><span class="sxs-lookup"><span data-stu-id="0921a-397">Prerequisites</span></span>

<span data-ttu-id="0921a-398">运行状况检查通常与外部监视服务或容器业务流程协调程序一起用于检查应用的状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="0921a-399">向应用添加运行状况检查之前，需确定要使用的监视系统。</span><span class="sxs-lookup"><span data-stu-id="0921a-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="0921a-400">监视系统决定了要创建的运行状况检查类型以及配置其终结点的方式。</span><span class="sxs-lookup"><span data-stu-id="0921a-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="0921a-401">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或将包引用添加到 [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) 包。</span><span class="sxs-lookup"><span data-stu-id="0921a-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="0921a-402">示例应用提供了启动代码来演示几个方案的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="0921a-403">[数据库探测](#database-probe)方案使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 检查数据库连接的运行状况。</span><span class="sxs-lookup"><span data-stu-id="0921a-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="0921a-404">[DbContext 探测](#entity-framework-core-dbcontext-probe)方案使用 EF Core `DbContext` 检查数据库。</span><span class="sxs-lookup"><span data-stu-id="0921a-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="0921a-405">若要探索数据库方案，示例应用将：</span><span class="sxs-lookup"><span data-stu-id="0921a-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="0921a-406">创建一个数据库，并在 appsettings.json 文件中提供其连接字符串。</span><span class="sxs-lookup"><span data-stu-id="0921a-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="0921a-407">其项目文件中具有以下包引用：</span><span class="sxs-lookup"><span data-stu-id="0921a-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="0921a-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="0921a-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="0921a-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="0921a-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="0921a-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="0921a-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="0921a-411">另一个运行状况检查方案演示如何将运行状况检查筛选到某个管理端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="0921a-412">示例应用要求创建包含管理 URL 和管理端口的 Properties/launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="0921a-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="0921a-413">有关详细信息，请参阅[按端口筛选](#filter-by-port)部分。</span><span class="sxs-lookup"><span data-stu-id="0921a-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="0921a-414">基本运行状况探测</span><span class="sxs-lookup"><span data-stu-id="0921a-414">Basic health probe</span></span>

<span data-ttu-id="0921a-415">对于许多应用，报告应用在处理请求方面的可用性（运行情况）的基本运行状况探测配置足以发现应用的状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="0921a-416">基本配置会注册运行状况检查服务，并调用运行状况检查中间件以通过运行状况响应在 URL 终结点处进行响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="0921a-417">默认情况下，不会注册任何特定运行状况检查来测试任何特定依赖项或子系统。</span><span class="sxs-lookup"><span data-stu-id="0921a-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="0921a-418">如果能够在运行状况终结点 URL 处进行响应，则应用被视为正常。</span><span class="sxs-lookup"><span data-stu-id="0921a-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="0921a-419">默认响应编写器会以纯文本响应形式将状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) 写回到客户端，以便指示 [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)、[HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 或 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) 状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="0921a-420">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0921a-421">在 `Startup.Configure` 的请求处理管道中，使用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 为运行状况检查中间件添加终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="0921a-422">在示例应用中，在 `/health` 处创建运行状况检查终结点 (BasicStartup.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="0921a-423">若要使用示例应用运行基本配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="0921a-424">Docker 示例</span><span class="sxs-lookup"><span data-stu-id="0921a-424">Docker example</span></span>

<span data-ttu-id="0921a-425">[Docker](xref:host-and-deploy/docker/index) 提供内置 `HEALTHCHECK` 指令，该指令可以用于检查使用基本运行状况检查配置的应用的状态：</span><span class="sxs-lookup"><span data-stu-id="0921a-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="0921a-426">创建运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-426">Create health checks</span></span>

<span data-ttu-id="0921a-427">运行状况检查通过实现 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口进行创建。</span><span class="sxs-lookup"><span data-stu-id="0921a-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="0921a-428"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> 方法会返回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，它以 `Healthy`、`Degraded` 或 `Unhealthy` 的形式指示运行状况。</span><span class="sxs-lookup"><span data-stu-id="0921a-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="0921a-429">结果会使用可配置状态代码（配置在[运行状况检查选项](#health-check-options)部分中进行介绍）编写为纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="0921a-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 还可以返回可选的键值对。</span><span class="sxs-lookup"><span data-stu-id="0921a-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="0921a-431">示例运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-431">Example health check</span></span>

<span data-ttu-id="0921a-432">下面的 `ExampleHealthCheck` 类演示运行状况检查的布局。</span><span class="sxs-lookup"><span data-stu-id="0921a-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="0921a-433">运行状况检查逻辑位于 `CheckHealthAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="0921a-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="0921a-434">以下示例将虚拟变量 `healthCheckResultHealthy` 设为 `true`。</span><span class="sxs-lookup"><span data-stu-id="0921a-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="0921a-435">如果 `healthCheckResultHealthy` 的值设为 `false`，则返回 [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) 状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="0921a-436">注册运行状况检查服务</span><span class="sxs-lookup"><span data-stu-id="0921a-436">Register health check services</span></span>

<span data-ttu-id="0921a-437">`ExampleHealthCheck` 类型在 `Startup.ConfigureServices` 中通过 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 添加到运行状况检查服务：</span><span class="sxs-lookup"><span data-stu-id="0921a-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="0921a-438">以下示例中显示的 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 重载会设置要在运行状况检查报告失败时报告的失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="0921a-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="0921a-439">如果失败状态设置为 `null`（默认值），则会报告 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0921a-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="0921a-440">此重载对于库创建者是一种十分有用的方案，在这种情况下，如果运行状况检查实现遵循该设置，则在发生运行状况检查失败时，应用会强制实施库所指示的失败状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="0921a-441">标记用于筛选运行状况检查（在[筛选运行状况检查](#filter-health-checks)部分中进行了进一步介绍）。</span><span class="sxs-lookup"><span data-stu-id="0921a-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="0921a-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 还可以执行 lambda 函数。</span><span class="sxs-lookup"><span data-stu-id="0921a-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="0921a-443">在以下 `Startup.ConfigureServices` 示例中，运行状况检查名称指定为 `Example`，并且检查始终返回正常状态：</span><span class="sxs-lookup"><span data-stu-id="0921a-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="0921a-444">使用运行状况检查中间件</span><span class="sxs-lookup"><span data-stu-id="0921a-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="0921a-445">在 `Startup.Configure` 内，在处理管道中使用终结点 URL 或相对路径调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>：</span><span class="sxs-lookup"><span data-stu-id="0921a-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="0921a-446">如果运行状况检查应侦听特定端口，则使用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 的重载设置端口（在[按端口筛选](#filter-by-port)部分中进行了进一步介绍）：</span><span class="sxs-lookup"><span data-stu-id="0921a-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="0921a-447">运行状况检查选项</span><span class="sxs-lookup"><span data-stu-id="0921a-447">Health check options</span></span>

<span data-ttu-id="0921a-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> 使你可以自定义运行状况检查行为：</span><span class="sxs-lookup"><span data-stu-id="0921a-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="0921a-449">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="0921a-450">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="0921a-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="0921a-451">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="0921a-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="0921a-452">自定义输出</span><span class="sxs-lookup"><span data-stu-id="0921a-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="0921a-453">筛选运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-453">Filter health checks</span></span>

<span data-ttu-id="0921a-454">默认情况下，运行状况检查中间件会运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="0921a-455">若要运行运行状况检查的子集，请提供向 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> 选项返回布尔值的函数。</span><span class="sxs-lookup"><span data-stu-id="0921a-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="0921a-456">在以下示例中，`Bar` 运行状况检查在函数条件语句 中由于其标记 (`bar_tag`) 而被筛选掉，在条件语句中，仅当运行状况检查的 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> 属性与 `foo_tag` 或 `baz_tag` 匹配时才返回 `true`：</span><span class="sxs-lookup"><span data-stu-id="0921a-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="0921a-457">自定义 HTTP 状态代码</span><span class="sxs-lookup"><span data-stu-id="0921a-457">Customize the HTTP status code</span></span>

<span data-ttu-id="0921a-458">使用 <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> 可自定义运行状况状态到 HTTP 状态代码的映射。</span><span class="sxs-lookup"><span data-stu-id="0921a-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="0921a-459">以下 <xref:Microsoft.AspNetCore.Http.StatusCodes> 分配是中间件所使用的默认值。</span><span class="sxs-lookup"><span data-stu-id="0921a-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="0921a-460">更改状态代码值以满足要求。</span><span class="sxs-lookup"><span data-stu-id="0921a-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="0921a-461">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="0921a-461">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="0921a-462">取消缓存标头</span><span class="sxs-lookup"><span data-stu-id="0921a-462">Suppress cache headers</span></span>

<span data-ttu-id="0921a-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> 控制运行状况检查中间件是否将 HTTP 标头添加到探测响应以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="0921a-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="0921a-464">如果值为 `false`（默认值），则中间件会设置或替代 `Cache-Control`、`Expires` 和 `Pragma` 标头以防止响应缓存。</span><span class="sxs-lookup"><span data-stu-id="0921a-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="0921a-465">如果值为 `true`，则中间件不会修改响应的缓存标头。</span><span class="sxs-lookup"><span data-stu-id="0921a-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="0921a-466">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="0921a-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="0921a-467">自定义输出</span><span class="sxs-lookup"><span data-stu-id="0921a-467">Customize output</span></span>

<span data-ttu-id="0921a-468"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> 选项可获取或设置用于编写响应的委托。</span><span class="sxs-lookup"><span data-stu-id="0921a-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="0921a-469">默认委托会使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 字符串值编写最小的纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="0921a-470">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="0921a-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="0921a-471">默认委托会使用 [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status) 字符串值编写最小的纯文本响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="0921a-472">以下自定义委托 `WriteResponse` 输出自定义 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="0921a-473">运行状况检查系统不为复杂 JSON 返回格式提供内置支持，因为该格式特定于你选择的监视系统。</span><span class="sxs-lookup"><span data-stu-id="0921a-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="0921a-474">可以根据需要在前面的示例中随意自定义 `JObject` 以满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="0921a-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="0921a-475">数据库探测</span><span class="sxs-lookup"><span data-stu-id="0921a-475">Database probe</span></span>

<span data-ttu-id="0921a-476">运行状况检查可以指定数据库查询作为布尔测试来运行，以指示数据库是否在正常响应。</span><span class="sxs-lookup"><span data-stu-id="0921a-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="0921a-477">示例应用使用 [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks)（ASP.NET Core 应用的运行状况检查库）对 SQL Server 数据库执行运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="0921a-478">`AspNetCore.Diagnostics.HealthChecks` 对数据库执行 `SELECT 1` 查询以确认与数据库的连接是否正常。</span><span class="sxs-lookup"><span data-stu-id="0921a-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="0921a-479">使用查询检查数据库连接时，请选择快速返回的查询。</span><span class="sxs-lookup"><span data-stu-id="0921a-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="0921a-480">查询方法会面临使数据库过载和降低其性能的风险。</span><span class="sxs-lookup"><span data-stu-id="0921a-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="0921a-481">在大多数情况下，无需运行测试查询。</span><span class="sxs-lookup"><span data-stu-id="0921a-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="0921a-482">只需建立成功的数据库连接便足矣。</span><span class="sxs-lookup"><span data-stu-id="0921a-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="0921a-483">如果发现需要运行查询，请选择简单的 SELECT 查询，如 `SELECT 1`。</span><span class="sxs-lookup"><span data-stu-id="0921a-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="0921a-484">包括对 [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="0921a-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="0921a-485">在示例应用的 appsettings.json 文件中提供有效数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="0921a-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="0921a-486">应用使用名为 `HealthCheckSample` 的 SQL Server 数据库：</span><span class="sxs-lookup"><span data-stu-id="0921a-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="0921a-487">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0921a-488">示例应用使用数据库的连接字符串 (DbHealthStartup.cs) 调用 `AddSqlServer` 方法：</span><span class="sxs-lookup"><span data-stu-id="0921a-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0921a-489">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件：</span><span class="sxs-lookup"><span data-stu-id="0921a-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="0921a-490">若要使用示例应用运行数据库探测方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="0921a-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="0921a-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="0921a-492">Entity Framework Core DbContext 探测</span><span class="sxs-lookup"><span data-stu-id="0921a-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="0921a-493">`DbContext` 检查确认应用可以与为 EF Core `DbContext` 配置的数据库通信。</span><span class="sxs-lookup"><span data-stu-id="0921a-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="0921a-494">满足以下条件的应用支持 `DbContext` 检查：</span><span class="sxs-lookup"><span data-stu-id="0921a-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="0921a-495">使用 [Entity Framework (EF) Core](/ef/core/)。</span><span class="sxs-lookup"><span data-stu-id="0921a-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="0921a-496">包括对 [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="0921a-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="0921a-497">`AddDbContextCheck<TContext>` 为 `DbContext` 注册运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="0921a-498">`DbContext` 作为方法的 `TContext` 提供。</span><span class="sxs-lookup"><span data-stu-id="0921a-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="0921a-499">重载可用于配置失败状态、标记和自定义测试查询。</span><span class="sxs-lookup"><span data-stu-id="0921a-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="0921a-500">默认情况下：</span><span class="sxs-lookup"><span data-stu-id="0921a-500">By default:</span></span>

* <span data-ttu-id="0921a-501">`DbContextHealthCheck` 调用 EF Core 的 `CanConnectAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="0921a-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="0921a-502">可以自定义在使用 `AddDbContextCheck` 方法重载检查运行状况时运行的操作。</span><span class="sxs-lookup"><span data-stu-id="0921a-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="0921a-503">运行状况检查的名称是 `TContext` 类型的名称。</span><span class="sxs-lookup"><span data-stu-id="0921a-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="0921a-504">在示例应用中，`AppDbContext` 会提供给 `AddDbContextCheck`，并在 `Startup.ConfigureServices` 中注册为服务 (DbContextHealthStartup.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0921a-505">在示例应用中，`UseHealthChecks` 在 `Startup.Configure` 中添加运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="0921a-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="0921a-506">若要使用示例应用运行 `DbContext` 探测方案，请确认连接字符串指定的数据库在 SQL Server 实例中不存在。</span><span class="sxs-lookup"><span data-stu-id="0921a-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="0921a-507">如果该数据库存在，请删除它。</span><span class="sxs-lookup"><span data-stu-id="0921a-507">If the database exists, delete it.</span></span>

<span data-ttu-id="0921a-508">在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="0921a-509">在应用运行之后，在浏览器中对 `/health` 终结点发出请求，从而检查运行状况。</span><span class="sxs-lookup"><span data-stu-id="0921a-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="0921a-510">数据库和 `AppDbContext` 不存在，因此应用提供以下响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="0921a-511">触发示例应用以创建数据库。</span><span class="sxs-lookup"><span data-stu-id="0921a-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="0921a-512">向 `/createdatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="0921a-513">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="0921a-514">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="0921a-515">数据库和上下文存在，因此应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="0921a-516">触发示例应用以删除数据库。</span><span class="sxs-lookup"><span data-stu-id="0921a-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="0921a-517">向 `/deletedatabase` 发出请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="0921a-518">应用会进行以下响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="0921a-519">向 `/health` 终结点发出请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="0921a-520">应用提供不正常的响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="0921a-521">单独的就绪情况和运行情况探测</span><span class="sxs-lookup"><span data-stu-id="0921a-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="0921a-522">在某些托管方案中，会使用一对区分两种应用状态的运行状况检查：</span><span class="sxs-lookup"><span data-stu-id="0921a-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="0921a-523">“就绪情况”指示情况是否为应用正常运行，但未准备好接收请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-523">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="0921a-524">“运行情况”指示情况是否为应用已崩溃且必须重启。</span><span class="sxs-lookup"><span data-stu-id="0921a-524">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="0921a-525">请看下面的示例：应用必须下载大型配置文件才能处理请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-525">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="0921a-526">如果初始下载失败，我们不希望重启该应用，因为该应用可以多次尝试下载该文件。</span><span class="sxs-lookup"><span data-stu-id="0921a-526">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="0921a-527">我们使用运行情况探测来描述进程的运行情况，不执行其他检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-527">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="0921a-528">我们还想要在配置文件下载成功之前阻止将请求发送到应用。</span><span class="sxs-lookup"><span data-stu-id="0921a-528">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="0921a-529">我们使用就绪情况探测指示“未就绪”状态，直到下载成功并且应用已准备好接收请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-529">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="0921a-530">示例应用包含运行状况检查，以报告[托管服务](xref:fundamentals/host/hosted-services)中长时间运行的启动任务的完成。</span><span class="sxs-lookup"><span data-stu-id="0921a-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="0921a-531">`StartupHostedServiceHealthCheck` 公开了属性 `StartupTaskCompleted`，托管服务在其长时间运行的任务完成时可以将该属性设置为 `true` (StartupHostedServiceHealthCheck.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="0921a-532">长时间运行的后台任务由[托管服务](xref:fundamentals/host/hosted-services) (Services/StartupHostedService) 启动。</span><span class="sxs-lookup"><span data-stu-id="0921a-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="0921a-533">在该任务结束时，`StartupHostedServiceHealthCheck.StartupTaskCompleted` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="0921a-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="0921a-534">运行状况检查在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 与托管服务一起注册。</span><span class="sxs-lookup"><span data-stu-id="0921a-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="0921a-535">因为托管服务必须对运行状况检查设置该属性，所以运行状况检查也会在服务容器 (LivenessProbeStartup.cs) 中进行注册：</span><span class="sxs-lookup"><span data-stu-id="0921a-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0921a-536">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="0921a-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="0921a-537">在示例应用中，在 `/health/ready` 处为就绪情况检查，并且在 `/health/live` 处为运行情况检查创建运行状况检查终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="0921a-538">就绪情况检查会将运行状况检查筛选到具有 `ready` 标记的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="0921a-539">运行情况检查通过在 [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) 中返回 `false` 来筛选出 `StartupHostedServiceHealthCheck`（有关详细信息，请参阅[筛选运行状况检查](#filter-health-checks)）：</span><span class="sxs-lookup"><span data-stu-id="0921a-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="0921a-540">若要使用示例应用运行就绪情况/运行情况配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="0921a-541">在浏览器中，访问 `/health/ready` 几次，直到过了 15 秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="0921a-542">运行状况检查会在前 15 秒内报告“运行不正常”。</span><span class="sxs-lookup"><span data-stu-id="0921a-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="0921a-543">15 秒之后，终结点会报告“运行正常”，这反映托管服务完成了长时间运行的任务。</span><span class="sxs-lookup"><span data-stu-id="0921a-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="0921a-544">此示例还创建了一个运行第一个就绪检查的运行状况检查发布服务器（<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现），延迟时间为两秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="0921a-545">有关详细信息，请参阅[运行状况检查发布服务器](#health-check-publisher)部分。</span><span class="sxs-lookup"><span data-stu-id="0921a-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="0921a-546">Kubernetes 示例</span><span class="sxs-lookup"><span data-stu-id="0921a-546">Kubernetes example</span></span>

<span data-ttu-id="0921a-547">在诸如 [Kubernetes](https://kubernetes.io/) 这类环境中，使用单独的就绪情况和运行情况检查会十分有用。</span><span class="sxs-lookup"><span data-stu-id="0921a-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="0921a-548">在 Kubernetes 中，应用可能需要在接受请求之前执行耗时的启动工作，如基础数据库可用性测试。</span><span class="sxs-lookup"><span data-stu-id="0921a-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="0921a-549">使用单独检查使业务流程协调程序可以区分应用是否正常运行但尚未准备就绪，或是应用程序是否未能启动。</span><span class="sxs-lookup"><span data-stu-id="0921a-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="0921a-550">有关 Kubernetes 中的就绪情况和运行情况探测的详细信息，请参阅 Kubernetes 文档中的[配置运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。</span><span class="sxs-lookup"><span data-stu-id="0921a-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="0921a-551">以下示例演示如何使用 Kubernetes 就绪情况探测配置：</span><span class="sxs-lookup"><span data-stu-id="0921a-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="0921a-552">具有自定义响应编写器的基于指标的探测</span><span class="sxs-lookup"><span data-stu-id="0921a-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="0921a-553">示例应用演示具有自定义响应编写器的内存运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="0921a-554">如果应用使用的内存多于给定内存阈值（在示例应用中为 1 GB），则 `MemoryHealthCheck` 报告运行不正常状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="0921a-555"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> 包括应用的垃圾回收器 (GC) 信息 (MemoryHealthCheck.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="0921a-556">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0921a-557">`MemoryHealthCheck` 注册为服务，而不是通过将运行状况检查传递到 <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> 来启用它。</span><span class="sxs-lookup"><span data-stu-id="0921a-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="0921a-558">所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 注册服务都可供运行状况检查服务和中间件使用。</span><span class="sxs-lookup"><span data-stu-id="0921a-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="0921a-559">建议将运行状况检查服务注册为单一实例服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="0921a-560">在示例应用 (CustomWriterStartup.cs) 中：</span><span class="sxs-lookup"><span data-stu-id="0921a-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="0921a-561">在 `Startup.Configure` 内，在应用处理管道中调用运行状况检查中间件。</span><span class="sxs-lookup"><span data-stu-id="0921a-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="0921a-562">一个 `WriteResponse` 委托提供给 `ResponseWriter` 属性，以在执行运行状况检查时输出自定义 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="0921a-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="0921a-563">`WriteResponse` 方法将 `CompositeHealthCheckResult` 格式化为 JSON 对象，并生成运行状况检查响应的 JSON 输出：</span><span class="sxs-lookup"><span data-stu-id="0921a-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="0921a-564">若要使用示例应用运行具有自定义响应编写器输出的基于指标的探测，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="0921a-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包括基于指标的运行状况检查方案（包括磁盘存储和最大值运行情况检查）。</span><span class="sxs-lookup"><span data-stu-id="0921a-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="0921a-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="0921a-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="0921a-567">按端口筛选</span><span class="sxs-lookup"><span data-stu-id="0921a-567">Filter by port</span></span>

<span data-ttu-id="0921a-568">使用端口调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 会将运行状况检查请求限制到指定端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="0921a-569">这通常用于在容器环境中公开用于监视服务的端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="0921a-570">示例应用使用[环境变量配置提供程序](xref:fundamentals/configuration/index#environment-variables-configuration-provider)配置端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="0921a-571">端口在 launchSettings.json 文件设置，并通过环境变量传递到配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="0921a-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="0921a-572">还必须配置服务器以在管理端口上侦听请求。</span><span class="sxs-lookup"><span data-stu-id="0921a-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="0921a-573">若要使用示例应用演示管理端口配置，请在 Properties 文件夹中创建 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="0921a-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="0921a-574">示例应用中的以下 Properties/launchSettings.json 文件未包含在示例应用的项目文件中，必须手动创建：</span><span class="sxs-lookup"><span data-stu-id="0921a-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="0921a-575">在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> 注册运行状况检查服务。</span><span class="sxs-lookup"><span data-stu-id="0921a-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0921a-576"><xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 调用指定管理端口 (ManagementPortStartup.cs)：</span><span class="sxs-lookup"><span data-stu-id="0921a-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="0921a-577">可以通过在代码中显式设置 URL 和管理端口，来避免在示例应用中创建 launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="0921a-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="0921a-578">在创建 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> 的 Program.cs 中，添加 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> 调用并提供应用的正常响应终结点和管理端口终结点。</span><span class="sxs-lookup"><span data-stu-id="0921a-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="0921a-579">在调用 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> 的 ManagementPortStartup.cs 中，显式指定管理端口。</span><span class="sxs-lookup"><span data-stu-id="0921a-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="0921a-580">Program.cs:</span><span class="sxs-lookup"><span data-stu-id="0921a-580">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="0921a-581">ManagementPortStartup.cs：</span><span class="sxs-lookup"><span data-stu-id="0921a-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="0921a-582">若要使用示例应用运行管理端口配置方案，请在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0921a-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="0921a-583">分发运行状况检查库</span><span class="sxs-lookup"><span data-stu-id="0921a-583">Distribute a health check library</span></span>

<span data-ttu-id="0921a-584">将运行状况检查作为库进行分发：</span><span class="sxs-lookup"><span data-stu-id="0921a-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="0921a-585">编写将 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> 接口作为独立类来实现的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="0921a-586">该类可以依赖于[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)类型激活和[命名选项](xref:fundamentals/configuration/options)来访问配置数据。</span><span class="sxs-lookup"><span data-stu-id="0921a-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="0921a-587">在 `CheckHealthAsync` 的运行状况检查逻辑中：</span><span class="sxs-lookup"><span data-stu-id="0921a-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="0921a-588">方法中使用 `data1` 和 `data2` 运行探测的运行状况检查逻辑。</span><span class="sxs-lookup"><span data-stu-id="0921a-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="0921a-589">`AccessViolationException` 已处理。</span><span class="sxs-lookup"><span data-stu-id="0921a-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="0921a-590">发生 <xref:System.AccessViolationException> 时，将返回 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> 和 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>，以允许用户配置运行状况检查失败状态。</span><span class="sxs-lookup"><span data-stu-id="0921a-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="0921a-591">使用参数编写一个扩展方法，所使用的应用会在其 `Startup.Configure` 方法中调用它。</span><span class="sxs-lookup"><span data-stu-id="0921a-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="0921a-592">在以下示例中，假设以下运行状况检查方法签名：</span><span class="sxs-lookup"><span data-stu-id="0921a-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="0921a-593">前面的签名指示 `ExampleHealthCheck` 需要其他数据来处理运行状况检查探测逻辑。</span><span class="sxs-lookup"><span data-stu-id="0921a-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="0921a-594">当运行状况检查向扩展方法注册时，数据会提供给用于创建运行状况检查实例的委托。</span><span class="sxs-lookup"><span data-stu-id="0921a-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="0921a-595">在以下示例中，调用方会指定可选的：</span><span class="sxs-lookup"><span data-stu-id="0921a-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="0921a-596">运行状况检查名称 (`name`)。</span><span class="sxs-lookup"><span data-stu-id="0921a-596">health check name (`name`).</span></span> <span data-ttu-id="0921a-597">如果为 `null`，则使用 `example_health_check`。</span><span class="sxs-lookup"><span data-stu-id="0921a-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="0921a-598">运行状况检查的字符串数据点 (`data1`)。</span><span class="sxs-lookup"><span data-stu-id="0921a-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="0921a-599">运行状况检查的整数数据点 (`data2`)。</span><span class="sxs-lookup"><span data-stu-id="0921a-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="0921a-600">如果为 `null`，则使用 `1`。</span><span class="sxs-lookup"><span data-stu-id="0921a-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="0921a-601">失败状态 (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>)。</span><span class="sxs-lookup"><span data-stu-id="0921a-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="0921a-602">默认值为 `null`。</span><span class="sxs-lookup"><span data-stu-id="0921a-602">The default is `null`.</span></span> <span data-ttu-id="0921a-603">如果为 `null`，则报告失败状态 [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)。</span><span class="sxs-lookup"><span data-stu-id="0921a-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="0921a-604">标记 (`IEnumerable<string>`)。</span><span class="sxs-lookup"><span data-stu-id="0921a-604">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="0921a-605">运行状况检查发布服务器</span><span class="sxs-lookup"><span data-stu-id="0921a-605">Health Check Publisher</span></span>

<span data-ttu-id="0921a-606">当 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 添加到服务容器时，运行状况检查系统，会定期执行运行状况检查并使用结果调用 `PublishAsync`。</span><span class="sxs-lookup"><span data-stu-id="0921a-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="0921a-607">在期望每个进程定期调用监视系统以便确定运行状况的基于推送的运行状况监视系统方案中，这十分有用。</span><span class="sxs-lookup"><span data-stu-id="0921a-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="0921a-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 接口具有单个方法：</span><span class="sxs-lookup"><span data-stu-id="0921a-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="0921a-609">使用 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> 可设置：</span><span class="sxs-lookup"><span data-stu-id="0921a-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="0921a-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>：在应用启动后且在应用执行 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例之前所应用的初始延迟。</span><span class="sxs-lookup"><span data-stu-id="0921a-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="0921a-611">延迟在启动时应用一次，不适用于后续迭代。</span><span class="sxs-lookup"><span data-stu-id="0921a-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="0921a-612">默认值为 5 秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-612">The default value is five seconds.</span></span>
* <span data-ttu-id="0921a-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>：<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 执行的时间。</span><span class="sxs-lookup"><span data-stu-id="0921a-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="0921a-614">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="0921a-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>：如果 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> 为 `null`（默认值），则运行状况检查发布服务器服务运行所有已注册的运行状况检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="0921a-616">若要运行运行状况检查的子集，请提供用于筛选检查集的函数。</span><span class="sxs-lookup"><span data-stu-id="0921a-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="0921a-617">每个时间段都会评估谓词。</span><span class="sxs-lookup"><span data-stu-id="0921a-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="0921a-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>：执行所有 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例的运行状况检查的超时时间。</span><span class="sxs-lookup"><span data-stu-id="0921a-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="0921a-619">在不超时的情况下，使用 <xref:System.Threading.Timeout.InfiniteTimeSpan> 执行。</span><span class="sxs-lookup"><span data-stu-id="0921a-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="0921a-620">默认值为 30 秒。</span><span class="sxs-lookup"><span data-stu-id="0921a-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="0921a-621">在 ASP.NET Core 2.2 版本中，<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现不支持设置 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>，它设置 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> 的值。</span><span class="sxs-lookup"><span data-stu-id="0921a-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="0921a-622">此问题已在 ASP.NET Core 3.0 中得到解决。</span><span class="sxs-lookup"><span data-stu-id="0921a-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="0921a-623">在示例应用中，`ReadinessPublisher` 是 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现。</span><span class="sxs-lookup"><span data-stu-id="0921a-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="0921a-624">针对每次检查，运行状况检查状态记录为：</span><span class="sxs-lookup"><span data-stu-id="0921a-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="0921a-625">如果运行状况检查状态为 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>，则为信息 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>)。</span><span class="sxs-lookup"><span data-stu-id="0921a-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="0921a-626">如果状态为 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> 或 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>，则为错误 (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>)。</span><span class="sxs-lookup"><span data-stu-id="0921a-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="0921a-627">在示例应用的 `LivenessProbeStartup` 示例中，`StartupHostedService` 就绪状态检查有两秒的启动延迟，并且每 30 秒运行一次检查。</span><span class="sxs-lookup"><span data-stu-id="0921a-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="0921a-628">为激活 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实现，示例将 `ReadinessPublisher` 注册为[依存关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中的单一实例服务：</span><span class="sxs-lookup"><span data-stu-id="0921a-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="0921a-629">以下解决方法允许在已将一个或多个其他托管服务添加到应用时，向服务容器添加 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> 实例。</span><span class="sxs-lookup"><span data-stu-id="0921a-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="0921a-630">ASP.NET Core 3.0 中无需此解决方法。</span><span class="sxs-lookup"><span data-stu-id="0921a-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="0921a-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 包括多个系统的发布服务器（包括 [Application Insights](/azure/application-insights/app-insights-overview)）。</span><span class="sxs-lookup"><span data-stu-id="0921a-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="0921a-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) 不由 Microsoft 维护或支持。</span><span class="sxs-lookup"><span data-stu-id="0921a-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="0921a-633">使用 MapWhen 限制运行状况检查</span><span class="sxs-lookup"><span data-stu-id="0921a-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="0921a-634">使用 <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 对运行状况检查终结点的请求管道进行条件分支。</span><span class="sxs-lookup"><span data-stu-id="0921a-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="0921a-635">在以下示例中，如果收到 `api/HealthCheck` 终结点的 GET 请求，`MapWhen` 将对请求管道进行分支以激活运行状况检查中间件：</span><span class="sxs-lookup"><span data-stu-id="0921a-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="0921a-636">有关详细信息，请参阅 <xref:fundamentals/middleware/index#use-run-and-map>。</span><span class="sxs-lookup"><span data-stu-id="0921a-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
