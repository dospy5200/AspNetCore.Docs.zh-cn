---
title: 在 ASP.NET Core 中将依赖项注入到控制器
author: ardalis
description: 了解 ASP.NET Core MVC 控制器如何使用 ASP.NET Core 中的依赖项注入通过构造函数显式请求其依赖项。
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: f3654e008733c57b4cd4cd34a52b747af2258be1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589070"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="888b2-103">在 ASP.NET Core 中将依赖项注入到控制器</span><span class="sxs-lookup"><span data-stu-id="888b2-103">Dependency injection into controllers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="888b2-104">作者：[Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="888b2-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="888b2-105">ASP.NET Core MVC 控制器通过构造函数显式请求依赖关系。</span><span class="sxs-lookup"><span data-stu-id="888b2-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="888b2-106">ASP.NET Core 内置有对[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 的支持。</span><span class="sxs-lookup"><span data-stu-id="888b2-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="888b2-107">DI 使应用更易于测试和维护。</span><span class="sxs-lookup"><span data-stu-id="888b2-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="888b2-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="888b2-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="888b2-109">构造函数注入</span><span class="sxs-lookup"><span data-stu-id="888b2-109">Constructor Injection</span></span>

<span data-ttu-id="888b2-110">服务作为构造函数参数添加，并且运行时从服务容器中解析服务。</span><span class="sxs-lookup"><span data-stu-id="888b2-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="888b2-111">通常使用接口来定义服务。</span><span class="sxs-lookup"><span data-stu-id="888b2-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="888b2-112">例如，考虑需要当前时间的应用。</span><span class="sxs-lookup"><span data-stu-id="888b2-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="888b2-113">以下接口公开 `IDateTime` 服务：</span><span class="sxs-lookup"><span data-stu-id="888b2-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="888b2-114">以下代码实现 `IDateTime` 接口：</span><span class="sxs-lookup"><span data-stu-id="888b2-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="888b2-115">将服务添加到服务容器中：</span><span class="sxs-lookup"><span data-stu-id="888b2-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="888b2-116">有关 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> 的详细信息，请参阅 [DI 服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="888b2-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="888b2-117">以下代码根据一天中的时间向用户显示问候语：</span><span class="sxs-lookup"><span data-stu-id="888b2-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="888b2-118">运行应用并且系统将根据时间显示一条消息。</span><span class="sxs-lookup"><span data-stu-id="888b2-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="888b2-119">FromServices 的操作注入</span><span class="sxs-lookup"><span data-stu-id="888b2-119">Action injection with FromServices</span></span>

<span data-ttu-id="888b2-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> 允许将服务直接注入到操作方法，而无需使用构造函数注入：</span><span class="sxs-lookup"><span data-stu-id="888b2-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="888b2-121">从控制器访问设置</span><span class="sxs-lookup"><span data-stu-id="888b2-121">Access settings from a controller</span></span>

<span data-ttu-id="888b2-122">从控制器中访问应用或配置设置是一种常见模式。</span><span class="sxs-lookup"><span data-stu-id="888b2-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="888b2-123"><xref:fundamentals/configuration/options> 中所述的选项模式是管理设置的首选方法。</span><span class="sxs-lookup"><span data-stu-id="888b2-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="888b2-124">通常情况下，不直接将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入到控制器。</span><span class="sxs-lookup"><span data-stu-id="888b2-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="888b2-125">创建表示选项的类。</span><span class="sxs-lookup"><span data-stu-id="888b2-125">Create a class that represents the options.</span></span> <span data-ttu-id="888b2-126">例如：</span><span class="sxs-lookup"><span data-stu-id="888b2-126">For example:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="888b2-127">将配置类添加到服务集合中：</span><span class="sxs-lookup"><span data-stu-id="888b2-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="888b2-128">将应用配置为从 JSON 格式文件中读取设置：</span><span class="sxs-lookup"><span data-stu-id="888b2-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="888b2-129">以下代码从服务容器请求 `IOptions<SampleWebSettings>` 设置，并通过 `Index` 方法使用它们：</span><span class="sxs-lookup"><span data-stu-id="888b2-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="888b2-130">其他资源</span><span class="sxs-lookup"><span data-stu-id="888b2-130">Additional resources</span></span>

* <span data-ttu-id="888b2-131">请参阅 <xref:mvc/controllers/testing>，了解如何显式请求控制器中的依赖关系，以更轻松地测试代码。</span><span class="sxs-lookup"><span data-stu-id="888b2-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="888b2-132">[将默认依赖关系注入容器替换为第三方实现](xref:fundamentals/dependency-injection#default-service-container-replacement)。</span><span class="sxs-lookup"><span data-stu-id="888b2-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="888b2-133">作者：[Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="888b2-133">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="888b2-134">ASP.NET Core MVC 控制器通过构造函数显式请求依赖关系。</span><span class="sxs-lookup"><span data-stu-id="888b2-134">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="888b2-135">ASP.NET Core 内置有对[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 的支持。</span><span class="sxs-lookup"><span data-stu-id="888b2-135">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="888b2-136">DI 使应用更易于测试和维护。</span><span class="sxs-lookup"><span data-stu-id="888b2-136">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="888b2-137">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="888b2-137">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="888b2-138">构造函数注入</span><span class="sxs-lookup"><span data-stu-id="888b2-138">Constructor Injection</span></span>

<span data-ttu-id="888b2-139">服务作为构造函数参数添加，并且运行时从服务容器中解析服务。</span><span class="sxs-lookup"><span data-stu-id="888b2-139">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="888b2-140">通常使用接口来定义服务。</span><span class="sxs-lookup"><span data-stu-id="888b2-140">Services are typically defined using interfaces.</span></span> <span data-ttu-id="888b2-141">例如，考虑需要当前时间的应用。</span><span class="sxs-lookup"><span data-stu-id="888b2-141">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="888b2-142">以下接口公开 `IDateTime` 服务：</span><span class="sxs-lookup"><span data-stu-id="888b2-142">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="888b2-143">以下代码实现 `IDateTime` 接口：</span><span class="sxs-lookup"><span data-stu-id="888b2-143">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="888b2-144">将服务添加到服务容器中：</span><span class="sxs-lookup"><span data-stu-id="888b2-144">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="888b2-145">有关 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> 的详细信息，请参阅 [DI 服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="888b2-145">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="888b2-146">以下代码根据一天中的时间向用户显示问候语：</span><span class="sxs-lookup"><span data-stu-id="888b2-146">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="888b2-147">运行应用并且系统将根据时间显示一条消息。</span><span class="sxs-lookup"><span data-stu-id="888b2-147">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="888b2-148">FromServices 的操作注入</span><span class="sxs-lookup"><span data-stu-id="888b2-148">Action injection with FromServices</span></span>

<span data-ttu-id="888b2-149"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> 允许将服务直接注入到操作方法，而无需使用构造函数注入：</span><span class="sxs-lookup"><span data-stu-id="888b2-149">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="888b2-150">从控制器访问设置</span><span class="sxs-lookup"><span data-stu-id="888b2-150">Access settings from a controller</span></span>

<span data-ttu-id="888b2-151">从控制器中访问应用或配置设置是一种常见模式。</span><span class="sxs-lookup"><span data-stu-id="888b2-151">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="888b2-152"><xref:fundamentals/configuration/options> 中所述的选项模式是管理设置的首选方法。</span><span class="sxs-lookup"><span data-stu-id="888b2-152">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="888b2-153">通常情况下，不直接将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入到控制器。</span><span class="sxs-lookup"><span data-stu-id="888b2-153">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="888b2-154">创建表示选项的类。</span><span class="sxs-lookup"><span data-stu-id="888b2-154">Create a class that represents the options.</span></span> <span data-ttu-id="888b2-155">例如：</span><span class="sxs-lookup"><span data-stu-id="888b2-155">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="888b2-156">将配置类添加到服务集合中：</span><span class="sxs-lookup"><span data-stu-id="888b2-156">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="888b2-157">将应用配置为从 JSON 格式文件中读取设置：</span><span class="sxs-lookup"><span data-stu-id="888b2-157">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="888b2-158">以下代码从服务容器请求 `IOptions<SampleWebSettings>` 设置，并通过 `Index` 方法使用它们：</span><span class="sxs-lookup"><span data-stu-id="888b2-158">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="888b2-159">其他资源</span><span class="sxs-lookup"><span data-stu-id="888b2-159">Additional resources</span></span>

* <span data-ttu-id="888b2-160">请参阅 <xref:mvc/controllers/testing>，了解如何显式请求控制器中的依赖关系，以更轻松地测试代码。</span><span class="sxs-lookup"><span data-stu-id="888b2-160">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="888b2-161">[将默认依赖关系注入容器替换为第三方实现](xref:fundamentals/dependency-injection#default-service-container-replacement)。</span><span class="sxs-lookup"><span data-stu-id="888b2-161">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>

::: moniker-end