---
title: 从 ASP.NET Web API 迁移到 ASP.NET Core
author: ardalis
description: 了解如何将 web API 实现从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC。
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 3bd34f677271ddfc00e6e65ddf3a5e3c10eec863
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588778"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="c55eb-103">从 ASP.NET Web API 迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c55eb-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="c55eb-104">作者： [Scott Addie](https://twitter.com/scott_addie) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c55eb-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c55eb-105">ASP.NET 4.x Web API 是一种 HTTP 服务，它可达到各种客户端，包括浏览器和移动设备。</span><span class="sxs-lookup"><span data-stu-id="c55eb-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="c55eb-106">ASP.NET Core 将 ASP.NET 4.x 的 MVC 和 Web API 应用模型组合到称为 ASP.NET Core MVC 的单一编程模型中。</span><span class="sxs-lookup"><span data-stu-id="c55eb-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="c55eb-107">本文演示从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC 所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="c55eb-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="c55eb-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/webapi/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c55eb-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="c55eb-109">先决条件</span><span class="sxs-lookup"><span data-stu-id="c55eb-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="c55eb-110">查看 ASP.NET 4.x Web API 项目</span><span class="sxs-lookup"><span data-stu-id="c55eb-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="c55eb-111">本文使用 [ASP.NET Web API 2 入门](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)中创建的 *ProductsApp* 项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="c55eb-112">在该项目中，基本的 ASP.NET 4.x Web API 项目配置如下。</span><span class="sxs-lookup"><span data-stu-id="c55eb-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="c55eb-113">在 *Global.asax.cs* 中，对进行调用 `WebApiConfig.Register` ：</span><span class="sxs-lookup"><span data-stu-id="c55eb-113">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="c55eb-114">`WebApiConfig`类位于 *App_Start* 文件夹中，并具有一个静态 `Register` 方法：</span><span class="sxs-lookup"><span data-stu-id="c55eb-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="c55eb-115">前面的类：</span><span class="sxs-lookup"><span data-stu-id="c55eb-115">The preceding class:</span></span>

* <span data-ttu-id="c55eb-116">配置 [属性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，但实际上并没有使用它。</span><span class="sxs-lookup"><span data-stu-id="c55eb-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="c55eb-117">配置路由表。</span><span class="sxs-lookup"><span data-stu-id="c55eb-117">Configures the routing table.</span></span>
<span data-ttu-id="c55eb-118">示例代码需要 Url 来匹配格式，这 `/api/{controller}/{id}` `{id}` 是可选的。</span><span class="sxs-lookup"><span data-stu-id="c55eb-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="c55eb-119">以下部分演示了如何将 Web API 项目迁移到 ASP.NET Core MVC。</span><span class="sxs-lookup"><span data-stu-id="c55eb-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="c55eb-120">创建目标项目</span><span class="sxs-lookup"><span data-stu-id="c55eb-120">Create the destination project</span></span>

<span data-ttu-id="c55eb-121">在 Visual Studio 中创建新的空白解决方案并添加 ASP.NET 4.x Web API 项目以进行迁移：</span><span class="sxs-lookup"><span data-stu-id="c55eb-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="c55eb-122">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="c55eb-122">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="c55eb-123">选择 " **空白解决方案** " 模板，然后选择 " **下一步**"。</span><span class="sxs-lookup"><span data-stu-id="c55eb-123">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="c55eb-124">将解决方案命名为 *WebAPIMigration*。</span><span class="sxs-lookup"><span data-stu-id="c55eb-124">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="c55eb-125">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="c55eb-125">Select **Create**.</span></span>
1. <span data-ttu-id="c55eb-126">将现有的 *ProductsApp* 项目添加到解决方案。</span><span class="sxs-lookup"><span data-stu-id="c55eb-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="c55eb-127">添加要迁移到的新 API 项目：</span><span class="sxs-lookup"><span data-stu-id="c55eb-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="c55eb-128">向解决方案添加新的 **ASP.NET Core Web 应用程序** 项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="c55eb-129">在 " **配置新项目** " 对话框中，将项目命名为 *ProductsCore*，然后选择 " **创建**"。</span><span class="sxs-lookup"><span data-stu-id="c55eb-129">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="c55eb-130">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”  。</span><span class="sxs-lookup"><span data-stu-id="c55eb-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="c55eb-131">选择“API”项目模板，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-131">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="c55eb-132">从新的 *ProductsCore* 项目中删除 *WeatherForecast.cs* 和 controller */WeatherForecastController* 示例文件。</span><span class="sxs-lookup"><span data-stu-id="c55eb-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="c55eb-133">解决方案现在包含两个项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-133">The solution now contains two projects.</span></span> <span data-ttu-id="c55eb-134">以下各节介绍了如何将 *ProductsApp* 项目的内容迁移到 *ProductsCore* 项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="c55eb-135">迁移配置</span><span class="sxs-lookup"><span data-stu-id="c55eb-135">Migrate configuration</span></span>

<span data-ttu-id="c55eb-136">ASP.NET Core 不使用 *App_Start* 文件夹或 *global.asax* 文件。</span><span class="sxs-lookup"><span data-stu-id="c55eb-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="c55eb-137">此外，还会在发布时添加 *web.config* 文件。</span><span class="sxs-lookup"><span data-stu-id="c55eb-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="c55eb-138">`Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="c55eb-138">The `Startup` class:</span></span>

* <span data-ttu-id="c55eb-139">替换 *global.asax*。</span><span class="sxs-lookup"><span data-stu-id="c55eb-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="c55eb-140">处理所有应用启动任务。</span><span class="sxs-lookup"><span data-stu-id="c55eb-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="c55eb-141">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="c55eb-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="c55eb-142">迁移模型和控制器</span><span class="sxs-lookup"><span data-stu-id="c55eb-142">Migrate models and controllers</span></span>

<span data-ttu-id="c55eb-143">下面的代码演示 `ProductsController` 要为 ASP.NET Core 更新的：</span><span class="sxs-lookup"><span data-stu-id="c55eb-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="c55eb-144">更新 `ProductsController` ASP.NET Core 的：</span><span class="sxs-lookup"><span data-stu-id="c55eb-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="c55eb-145">将 *控制器/ProductsController* 和 *模型* 文件夹从原始项目复制到新项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="c55eb-146">将复制的文件的根命名空间更改为 `ProductsCore` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="c55eb-147">将 `using ProductsApp.Models;` 语句更新到 `using ProductsCore.Models;` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="c55eb-148">ASP.NET Core 中不存在下列组件：</span><span class="sxs-lookup"><span data-stu-id="c55eb-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="c55eb-149">`ApiController` 类</span><span class="sxs-lookup"><span data-stu-id="c55eb-149">`ApiController` class</span></span>
* <span data-ttu-id="c55eb-150">`System.Web.Http` 命名空间</span><span class="sxs-lookup"><span data-stu-id="c55eb-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="c55eb-151">`IHttpActionResult` 接口</span><span class="sxs-lookup"><span data-stu-id="c55eb-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="c55eb-152">进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="c55eb-152">Make the following changes:</span></span>

1. <span data-ttu-id="c55eb-153">将 `ApiController` 更改为 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。</span><span class="sxs-lookup"><span data-stu-id="c55eb-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="c55eb-154">添加 `using Microsoft.AspNetCore.Mvc;` 以解析 `ControllerBase` 引用。</span><span class="sxs-lookup"><span data-stu-id="c55eb-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="c55eb-155">删除 `using System.Web.Http;`。</span><span class="sxs-lookup"><span data-stu-id="c55eb-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="c55eb-156">将 `GetProduct` 操作的返回类型从更改 `IHttpActionResult` 为 `ActionResult<Product>` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="c55eb-157">简化 `GetProduct` 操作的 `return` 语句：</span><span class="sxs-lookup"><span data-stu-id="c55eb-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="c55eb-158">配置路由</span><span class="sxs-lookup"><span data-stu-id="c55eb-158">Configure routing</span></span>

<span data-ttu-id="c55eb-159">ASP.NET Core *API* 项目模板在生成的代码中包含终结点路由配置。</span><span class="sxs-lookup"><span data-stu-id="c55eb-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="c55eb-160">以下 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> 和 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 调用：</span><span class="sxs-lookup"><span data-stu-id="c55eb-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="c55eb-161">在 [中间件](xref:fundamentals/middleware/index) 管道中注册路由匹配和终结点执行。</span><span class="sxs-lookup"><span data-stu-id="c55eb-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="c55eb-162">替换 *ProductsApp* 项目的 *App_Start/webapiconfig.cs* 文件。</span><span class="sxs-lookup"><span data-stu-id="c55eb-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="c55eb-163">按如下所示配置路由：</span><span class="sxs-lookup"><span data-stu-id="c55eb-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="c55eb-164">`ProductsController`用以下特性标记类：</span><span class="sxs-lookup"><span data-stu-id="c55eb-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="c55eb-165">上述 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 属性配置控制器的属性路由模式。</span><span class="sxs-lookup"><span data-stu-id="c55eb-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="c55eb-166">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)特性使特性路由成为此控制器中所有操作的要求。</span><span class="sxs-lookup"><span data-stu-id="c55eb-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="c55eb-167">特性路由支持标记，例如 `[controller]` 和 `[action]` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="c55eb-168">在运行时，每个标记分别替换为应用了属性的控制器或操作的名称。</span><span class="sxs-lookup"><span data-stu-id="c55eb-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="c55eb-169">令牌：</span><span class="sxs-lookup"><span data-stu-id="c55eb-169">The tokens:</span></span>
    * <span data-ttu-id="c55eb-170">减少项目中的幻数字符串的数目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="c55eb-171">应用自动重命名重构时，请确保路由与相应的控制器和操作保持同步。</span><span class="sxs-lookup"><span data-stu-id="c55eb-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="c55eb-172">为操作启用 HTTP Get 请求 `ProductController` ：</span><span class="sxs-lookup"><span data-stu-id="c55eb-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="c55eb-173">将特性应用于 [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) `GetAllProducts` 操作。</span><span class="sxs-lookup"><span data-stu-id="c55eb-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="c55eb-174">将特性应用于 `[HttpGet("{id}")]` `GetProduct` 操作。</span><span class="sxs-lookup"><span data-stu-id="c55eb-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="c55eb-175">运行迁移的项目，并浏览到 `/api/products` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="c55eb-176">此时会显示三个产品的完整列表。</span><span class="sxs-lookup"><span data-stu-id="c55eb-176">A full list of three products appears.</span></span> <span data-ttu-id="c55eb-177">浏览到 `/api/products/1`。</span><span class="sxs-lookup"><span data-stu-id="c55eb-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="c55eb-178">第一个产品随即出现。</span><span class="sxs-lookup"><span data-stu-id="c55eb-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c55eb-179">其他资源</span><span class="sxs-lookup"><span data-stu-id="c55eb-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="c55eb-180">必备条件</span><span class="sxs-lookup"><span data-stu-id="c55eb-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="c55eb-181">查看 ASP.NET 4.x Web API 项目</span><span class="sxs-lookup"><span data-stu-id="c55eb-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="c55eb-182">本文使用 [ASP.NET Web API 2 入门](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)中创建的 *ProductsApp* 项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="c55eb-183">在该项目中，基本的 ASP.NET 4.x Web API 项目配置如下。</span><span class="sxs-lookup"><span data-stu-id="c55eb-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="c55eb-184">在 *Global.asax.cs* 中，对进行调用 `WebApiConfig.Register` ：</span><span class="sxs-lookup"><span data-stu-id="c55eb-184">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="c55eb-185">`WebApiConfig`类位于 *App_Start* 文件夹中，并具有一个静态 `Register` 方法：</span><span class="sxs-lookup"><span data-stu-id="c55eb-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="c55eb-186">此类配置 [属性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，不过实际上它并不是在项目中使用。</span><span class="sxs-lookup"><span data-stu-id="c55eb-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="c55eb-187">它还配置 ASP.NET Web API 使用的路由表。</span><span class="sxs-lookup"><span data-stu-id="c55eb-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="c55eb-188">在这种情况下，ASP.NET 4.x Web API 需要 Url 来匹配格式 `/api/{controller}/{id}` ，这 `{id}` 是可选的。</span><span class="sxs-lookup"><span data-stu-id="c55eb-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="c55eb-189">以下部分演示了如何将 Web API 项目迁移到 ASP.NET Core MVC。</span><span class="sxs-lookup"><span data-stu-id="c55eb-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="c55eb-190">创建目标项目</span><span class="sxs-lookup"><span data-stu-id="c55eb-190">Create the destination project</span></span>

<span data-ttu-id="c55eb-191">在 Visual Studio 中完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="c55eb-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="c55eb-192">中转到 "**文件**" "  >  **新建**  >  **项目**" "  >  **其他项目类型**" "  >  **Visual Studio 解决方案**"。</span><span class="sxs-lookup"><span data-stu-id="c55eb-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="c55eb-193">选择 " **空白解决方案**"，并将解决方案命名为 " *WebAPIMigration*"。</span><span class="sxs-lookup"><span data-stu-id="c55eb-193">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="c55eb-194">单击“确定”按钮。</span><span class="sxs-lookup"><span data-stu-id="c55eb-194">Click the **OK** button.</span></span>
* <span data-ttu-id="c55eb-195">将现有的 *ProductsApp* 项目添加到解决方案。</span><span class="sxs-lookup"><span data-stu-id="c55eb-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="c55eb-196">向解决方案添加新的 **ASP.NET Core Web 应用程序** 项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="c55eb-197">从下拉选择 " **.Net Core** 目标框架"，然后选择 " **API** 项目" 模板。</span><span class="sxs-lookup"><span data-stu-id="c55eb-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="c55eb-198">将项目命名为 " *ProductsCore*"，然后单击 **"确定"** 按钮。</span><span class="sxs-lookup"><span data-stu-id="c55eb-198">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="c55eb-199">解决方案现在包含两个项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-199">The solution now contains two projects.</span></span> <span data-ttu-id="c55eb-200">以下各节介绍了如何将 *ProductsApp* 项目的内容迁移到 *ProductsCore* 项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="c55eb-201">迁移配置</span><span class="sxs-lookup"><span data-stu-id="c55eb-201">Migrate configuration</span></span>

<span data-ttu-id="c55eb-202">ASP.NET Core 不使用：</span><span class="sxs-lookup"><span data-stu-id="c55eb-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="c55eb-203">*App_Start* 文件夹或 *global.asax* 文件</span><span class="sxs-lookup"><span data-stu-id="c55eb-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="c55eb-204">在发布时添加 *web.config* 文件。</span><span class="sxs-lookup"><span data-stu-id="c55eb-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="c55eb-205">`Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="c55eb-205">The `Startup` class:</span></span>

* <span data-ttu-id="c55eb-206">替换 *global.asax*。</span><span class="sxs-lookup"><span data-stu-id="c55eb-206">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="c55eb-207">处理所有应用启动任务。</span><span class="sxs-lookup"><span data-stu-id="c55eb-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="c55eb-208">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="c55eb-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="c55eb-209">在 ASP.NET Core MVC 中，当在中调用时，默认情况下包含特性路由 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="c55eb-210">以下 `UseMvc` 调用将替换 *ProductsApp* 项目的 *App_Start/webapiconfig.cs* 文件：</span><span class="sxs-lookup"><span data-stu-id="c55eb-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="c55eb-211">迁移模型和控制器</span><span class="sxs-lookup"><span data-stu-id="c55eb-211">Migrate models and controllers</span></span>

<span data-ttu-id="c55eb-212">下面的代码演示 `ProductsController` ASP.NET Core 的更新： [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="c55eb-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="c55eb-213">更新 `ProductsController` ASP.NET Core 的：</span><span class="sxs-lookup"><span data-stu-id="c55eb-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="c55eb-214">将 *控制器/ProductsController* 从原始项目复制到新项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="c55eb-215">将 *模型* 文件夹从原始项目复制到新项目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="c55eb-216">将复制的文件的根命名空间更改为 `ProductsCore` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="c55eb-217">将 `using ProductsApp.Models;` 语句更新到 `using ProductsCore.Models;` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="c55eb-218">ASP.NET Core 中不存在下列组件：</span><span class="sxs-lookup"><span data-stu-id="c55eb-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="c55eb-219">`ApiController` 类</span><span class="sxs-lookup"><span data-stu-id="c55eb-219">`ApiController` class</span></span>
* <span data-ttu-id="c55eb-220">`System.Web.Http` 命名空间</span><span class="sxs-lookup"><span data-stu-id="c55eb-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="c55eb-221">`IHttpActionResult` 接口</span><span class="sxs-lookup"><span data-stu-id="c55eb-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="c55eb-222">进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="c55eb-222">Make the following changes:</span></span>

1. <span data-ttu-id="c55eb-223">将 `ApiController` 更改为 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。</span><span class="sxs-lookup"><span data-stu-id="c55eb-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="c55eb-224">添加 `using Microsoft.AspNetCore.Mvc;` 以解析 `ControllerBase` 引用。</span><span class="sxs-lookup"><span data-stu-id="c55eb-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="c55eb-225">删除 `using System.Web.Http;`。</span><span class="sxs-lookup"><span data-stu-id="c55eb-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="c55eb-226">将 `GetProduct` 操作的返回类型从更改 `IHttpActionResult` 为 `ActionResult<Product>` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="c55eb-227">简化 `GetProduct` 操作的 `return` 语句：</span><span class="sxs-lookup"><span data-stu-id="c55eb-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="c55eb-228">配置路由</span><span class="sxs-lookup"><span data-stu-id="c55eb-228">Configure routing</span></span>

<span data-ttu-id="c55eb-229">按如下所示配置路由：</span><span class="sxs-lookup"><span data-stu-id="c55eb-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="c55eb-230">`ProductsController`用以下特性标记类：</span><span class="sxs-lookup"><span data-stu-id="c55eb-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="c55eb-231">上述 [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 属性配置控制器的属性路由模式。</span><span class="sxs-lookup"><span data-stu-id="c55eb-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="c55eb-232">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)特性使特性路由成为此控制器中所有操作的要求。</span><span class="sxs-lookup"><span data-stu-id="c55eb-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="c55eb-233">特性路由支持标记，例如 `[controller]` 和 `[action]` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="c55eb-234">在运行时，每个标记分别替换为应用了属性的控制器或操作的名称。</span><span class="sxs-lookup"><span data-stu-id="c55eb-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="c55eb-235">这些标记减少了项目中的幻字符串的数目。</span><span class="sxs-lookup"><span data-stu-id="c55eb-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="c55eb-236">标记还可确保在应用自动重命名重构时，路由与相应的控制器和操作保持同步。</span><span class="sxs-lookup"><span data-stu-id="c55eb-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="c55eb-237">将项目的兼容模式设置为 ASP.NET Core 2.2：</span><span class="sxs-lookup"><span data-stu-id="c55eb-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="c55eb-238">上述更改：</span><span class="sxs-lookup"><span data-stu-id="c55eb-238">The preceding change:</span></span>

    * <span data-ttu-id="c55eb-239">需要 `[ApiController]` 在控制器级别使用特性。</span><span class="sxs-lookup"><span data-stu-id="c55eb-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="c55eb-240">在 ASP.NET Core 2.2 中引入了可能中断的行为。</span><span class="sxs-lookup"><span data-stu-id="c55eb-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="c55eb-241">为操作启用 HTTP Get 请求 `ProductController` ：</span><span class="sxs-lookup"><span data-stu-id="c55eb-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="c55eb-242">将特性应用于 [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) `GetAllProducts` 操作。</span><span class="sxs-lookup"><span data-stu-id="c55eb-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="c55eb-243">将特性应用于 `[HttpGet("{id}")]` `GetProduct` 操作。</span><span class="sxs-lookup"><span data-stu-id="c55eb-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="c55eb-244">运行迁移的项目，并浏览到 `/api/products` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="c55eb-245">此时会显示三个产品的完整列表。</span><span class="sxs-lookup"><span data-stu-id="c55eb-245">A full list of three products appears.</span></span> <span data-ttu-id="c55eb-246">浏览到 `/api/products/1`。</span><span class="sxs-lookup"><span data-stu-id="c55eb-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="c55eb-247">第一个产品随即出现。</span><span class="sxs-lookup"><span data-stu-id="c55eb-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="c55eb-248">兼容性填充码</span><span class="sxs-lookup"><span data-stu-id="c55eb-248">Compatibility shim</span></span>

<span data-ttu-id="c55eb-249">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)库提供兼容性填充码，以将 ASP.NET 4.X Web API 项目移动到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="c55eb-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="c55eb-250">兼容性填充码扩展 ASP.NET Core，以支持 ASP.NET 4.x Web API 2 中的一些约定。</span><span class="sxs-lookup"><span data-stu-id="c55eb-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="c55eb-251">本文档前面的示例移植的基本操作足以确保兼容性填充程序是不必要的。</span><span class="sxs-lookup"><span data-stu-id="c55eb-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="c55eb-252">对于较大的项目，使用兼容性填充码对于临时桥接 ASP.NET Core 与 ASP.NET 4.x Web API 2 之间的 API 间隙非常有用。</span><span class="sxs-lookup"><span data-stu-id="c55eb-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="c55eb-253">Web API 兼容性填充码旨在用作一种临时度量，以支持将大型 ASP.NET 4.x Web API 项目迁移到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="c55eb-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="c55eb-254">随着时间的推移，应将项目更新为使用 ASP.NET Core 模式，而不是依赖于兼容性填充码。</span><span class="sxs-lookup"><span data-stu-id="c55eb-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="c55eb-255">中包含的兼容性功能 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 包括：</span><span class="sxs-lookup"><span data-stu-id="c55eb-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="c55eb-256">添加一个 `ApiController` 类型，以便不需要更新控制器的基类型。</span><span class="sxs-lookup"><span data-stu-id="c55eb-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="c55eb-257">启用 Web API 样式的模型绑定。</span><span class="sxs-lookup"><span data-stu-id="c55eb-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="c55eb-258">默认情况下，ASP.NET Core MVC 模型绑定函数与 ASP.NET 4.x MVC 5 的绑定函数类似。</span><span class="sxs-lookup"><span data-stu-id="c55eb-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="c55eb-259">兼容性填充码更改模型绑定，更类似于 ASP.NET 4.x Web API 2 模型绑定约定。</span><span class="sxs-lookup"><span data-stu-id="c55eb-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="c55eb-260">例如，复杂类型会自动从请求正文进行绑定。</span><span class="sxs-lookup"><span data-stu-id="c55eb-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="c55eb-261">扩展模型绑定，以便控制器操作可以接受类型的参数 `HttpRequestMessage` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="c55eb-262">添加消息格式化程序，以允许操作返回类型为的结果 `HttpResponseMessage` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="c55eb-263">添加 Web API 2 操作可能用于提供响应的其他响应方法：</span><span class="sxs-lookup"><span data-stu-id="c55eb-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="c55eb-264">`HttpResponseMessage` 生成器</span><span class="sxs-lookup"><span data-stu-id="c55eb-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="c55eb-265">操作结果方法：</span><span class="sxs-lookup"><span data-stu-id="c55eb-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="c55eb-266">将的实例添加 `IContentNegotiator` 到应用的依赖项注入 (DI) 容器，并使 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)中的内容协商相关类型可用。</span><span class="sxs-lookup"><span data-stu-id="c55eb-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="c55eb-267">此类类型的示例包括 `DefaultContentNegotiator` 和 `MediaTypeFormatter` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="c55eb-268">使用兼容性填充码：</span><span class="sxs-lookup"><span data-stu-id="c55eb-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="c55eb-269">安装 [AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c55eb-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="c55eb-270">通过调用在中，将兼容性填充程序的服务注册到应用的 DI 容器 `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="c55eb-271">`MapWebApiRoute` `IRouteBuilder` 在应用的调用中，使用在上定义特定于 web API 的路由 `IApplicationBuilder.UseMvc` 。</span><span class="sxs-lookup"><span data-stu-id="c55eb-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c55eb-272">其他资源</span><span class="sxs-lookup"><span data-stu-id="c55eb-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
