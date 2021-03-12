---
title: 在 ASP.NET Core 中将依赖项注入到视图
author: ardalis
description: 了解 ASP.NET Core 如何支持将依赖项注入到 MVC 视图。
ms.author: riande
ms.date: 10/14/2016
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
uid: mvc/views/dependency-injection
ms.openlocfilehash: 9d4920a7b572163b048bfb06029d913cee3f9e5a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587246"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="a72ea-103">在 ASP.NET Core 中将依赖项注入到视图</span><span class="sxs-lookup"><span data-stu-id="a72ea-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="a72ea-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a72ea-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a72ea-105">ASP.NET Core 支持将[依赖关系注入](xref:fundamentals/dependency-injection)到视图。</span><span class="sxs-lookup"><span data-stu-id="a72ea-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="a72ea-106">这对于视图特定服务很有用，例如仅为填充视图元素所需的本地化或数据。</span><span class="sxs-lookup"><span data-stu-id="a72ea-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="a72ea-107">应尽量在控制器和视图之间保持[问题分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)。</span><span class="sxs-lookup"><span data-stu-id="a72ea-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="a72ea-108">视图显示的大部分数据应该从控制器传入。</span><span class="sxs-lookup"><span data-stu-id="a72ea-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="a72ea-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="a72ea-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="a72ea-110">配置注入</span><span class="sxs-lookup"><span data-stu-id="a72ea-110">Configuration injection</span></span>

<span data-ttu-id="a72ea-111">*appsettings.json* 可以直接将值注入到视图中。</span><span class="sxs-lookup"><span data-stu-id="a72ea-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="a72ea-112">*appsettings.json* 文件示例：</span><span class="sxs-lookup"><span data-stu-id="a72ea-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="a72ea-113">`@inject` 的语法：`@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="a72ea-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="a72ea-114">使用 `@inject` 的示例：</span><span class="sxs-lookup"><span data-stu-id="a72ea-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="a72ea-115">服务注入</span><span class="sxs-lookup"><span data-stu-id="a72ea-115">Service injection</span></span>

<span data-ttu-id="a72ea-116">可以使用 `@inject` 指令将服务注入到视图。</span><span class="sxs-lookup"><span data-stu-id="a72ea-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="a72ea-117">可以将 `@inject` 视为向视图添加属性，然后使用 DI 填充属性。</span><span class="sxs-lookup"><span data-stu-id="a72ea-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="a72ea-118">此视图显示 `ToDoItem` 实例的列表，以及显示总体统计信息的摘要。</span><span class="sxs-lookup"><span data-stu-id="a72ea-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="a72ea-119">摘要从已注入的 `StatisticsService` 中填充。</span><span class="sxs-lookup"><span data-stu-id="a72ea-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="a72ea-120">在 Startup.cs 的 `ConfigureServices` 中为依赖关系注入注册此服务：</span><span class="sxs-lookup"><span data-stu-id="a72ea-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="a72ea-121">`StatisticsService` 通过存储库访问 `ToDoItem` 实例集并执行某些计算：</span><span class="sxs-lookup"><span data-stu-id="a72ea-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="a72ea-122">示例存储库使用内存中集合。</span><span class="sxs-lookup"><span data-stu-id="a72ea-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="a72ea-123">建议不将上示实现（对内存中的所有数据进行操作）用于远程访问的大型数据集。</span><span class="sxs-lookup"><span data-stu-id="a72ea-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="a72ea-124">该示例显示绑定到视图的模型数据以及注入到视图中的服务：</span><span class="sxs-lookup"><span data-stu-id="a72ea-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![列出项总数、已完成项、平均优先级以及任务列表（具有其优先级别和表示完成的布尔值）的“To Do”视图。](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="a72ea-126">填充查找数据</span><span class="sxs-lookup"><span data-stu-id="a72ea-126">Populating Lookup Data</span></span>

<span data-ttu-id="a72ea-127">视图注入可用于填充 UI 元素（如下拉列表）中的选项。</span><span class="sxs-lookup"><span data-stu-id="a72ea-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="a72ea-128">请考虑这样的用户个人资料窗体，其中包含用于指定性别、状态和其他首选项的选项。</span><span class="sxs-lookup"><span data-stu-id="a72ea-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="a72ea-129">使用标准 MVC 方法呈现这样的窗体，需让控制器为每组选项请求数据访问服务，然后用要绑定的每组选项填充模型或 `ViewBag`。</span><span class="sxs-lookup"><span data-stu-id="a72ea-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="a72ea-130">另一种方法是将服务直接注入视图以获取选项。</span><span class="sxs-lookup"><span data-stu-id="a72ea-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="a72ea-131">这最大限度地减少了控制器所需的代码量，将此视图元素构造逻辑移入视图本身。</span><span class="sxs-lookup"><span data-stu-id="a72ea-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="a72ea-132">显示个人资料编辑窗体的控制器操作只需要传递个人资料实例的窗体：</span><span class="sxs-lookup"><span data-stu-id="a72ea-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="a72ea-133">用于更新这些首选项的 HTML 窗体包括三个属性的下拉列表：</span><span class="sxs-lookup"><span data-stu-id="a72ea-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![用窗体更新个人资料视图，可输入姓名、性别、状态和喜爱的颜色。](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="a72ea-135">这些列表由已注入视图的服务填充：</span><span class="sxs-lookup"><span data-stu-id="a72ea-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="a72ea-136">`ProfileOptionsService` 是 UI 级别的服务，旨在准确提供此窗体所需的数据：</span><span class="sxs-lookup"><span data-stu-id="a72ea-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="a72ea-137">请记得在 `Startup.ConfigureServices` 中注册通过依赖项注入请求的类型。</span><span class="sxs-lookup"><span data-stu-id="a72ea-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a72ea-138">注销的类型将在运行时引发异常，因为服务提供程序通过 [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice) 接受内部查询。</span><span class="sxs-lookup"><span data-stu-id="a72ea-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="a72ea-139">替代服务</span><span class="sxs-lookup"><span data-stu-id="a72ea-139">Overriding Services</span></span>

<span data-ttu-id="a72ea-140">除了注入新的服务之外，此方法也可用于替代以前在页面上注入的服务。</span><span class="sxs-lookup"><span data-stu-id="a72ea-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="a72ea-141">下图显示了第一个示例中使用的页面上的所有可用字段：</span><span class="sxs-lookup"><span data-stu-id="a72ea-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![类型化 @ symbol 列表 Html 上的 IntelliSense 上下文菜单, 组件, StatsService, 以及 Url 字段](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="a72ea-143">如你所见，包括默认字段 `Html`、`Component` 和 `Url`（以及我们注入的 `StatsService`）。</span><span class="sxs-lookup"><span data-stu-id="a72ea-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="a72ea-144">如果想用自己的 HTML 帮助程序替换默认的 HTML 帮助程序，可使用 `@inject` 轻松完成：</span><span class="sxs-lookup"><span data-stu-id="a72ea-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="a72ea-145">如果想扩展现有的服务，用自己的方法继承或包装现有的实现时，只需使用此方法。</span><span class="sxs-lookup"><span data-stu-id="a72ea-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="a72ea-146">另请参阅</span><span class="sxs-lookup"><span data-stu-id="a72ea-146">See Also</span></span>

* <span data-ttu-id="a72ea-147">Simon Timms 的博客：[在视图中查找数据](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="a72ea-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
