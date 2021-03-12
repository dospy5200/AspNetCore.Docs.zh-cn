---
title: 将配置迁移到 ASP.NET Core
author: ardalis
description: 了解如何将配置从 ASP.NET MVC 项目迁移到 ASP.NET Core MVC 项目。
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
uid: migration/configuration
ms.openlocfilehash: c3957bf45dddcead24f7bb0f2702bf1a08950bdd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587938"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="7ee89-103">将配置迁移到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ee89-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="7ee89-104">作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="7ee89-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="7ee89-105">在前面的文章中，我们开始将 [ASP.NET mvc 项目迁移到 ASP.NET CORE mvc](xref:migration/mvc)。</span><span class="sxs-lookup"><span data-stu-id="7ee89-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="7ee89-106">本文将迁移配置。</span><span class="sxs-lookup"><span data-stu-id="7ee89-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="7ee89-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7ee89-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="7ee89-108">设置配置</span><span class="sxs-lookup"><span data-stu-id="7ee89-108">Setup configuration</span></span>

<span data-ttu-id="7ee89-109">ASP.NET Core 不再使用以前版本的 ASP.NET 使用的 *global.asax* 和 *web.config* 文件。</span><span class="sxs-lookup"><span data-stu-id="7ee89-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="7ee89-110">在早期版本的 ASP.NET 中，应用程序启动逻辑放置在 `Application_StartUp` *global.asax* 内的方法中。</span><span class="sxs-lookup"><span data-stu-id="7ee89-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="7ee89-111">稍后，在 ASP.NET MVC 中， *Startup.cs* 文件包含在项目的根目录中;并在应用程序启动时调用。</span><span class="sxs-lookup"><span data-stu-id="7ee89-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="7ee89-112">ASP.NET Core 通过将所有启动逻辑放在 *Startup.cs* 文件中来完全采用这种方法。</span><span class="sxs-lookup"><span data-stu-id="7ee89-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="7ee89-113">*web.config* 文件也已替换为 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="7ee89-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="7ee89-114">配置本身现在可以配置为 *Startup.cs* 中所述的应用程序启动过程的一部分。</span><span class="sxs-lookup"><span data-stu-id="7ee89-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="7ee89-115">配置仍可利用 XML 文件，但通常 ASP.NET Core 项目会将配置值放入 JSON 格式的文件中，例如 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="7ee89-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="7ee89-116">ASP.NET Core 的配置系统还可以轻松地访问环境变量，从而为特定于环境的值提供 [更安全、更可靠的位置](xref:security/app-secrets) 。</span><span class="sxs-lookup"><span data-stu-id="7ee89-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="7ee89-117">对于不应签入源控件的机密（如连接字符串和 API 密钥），尤其如此。</span><span class="sxs-lookup"><span data-stu-id="7ee89-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="7ee89-118">若要详细了解 ASP.NET Core 中的配置，请参阅 [配置](xref:fundamentals/configuration/index) 。</span><span class="sxs-lookup"><span data-stu-id="7ee89-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="7ee89-119">对于本文，我们将从 [上一篇文章](xref:migration/mvc)中的部分迁移 ASP.NET Core 项目开始。</span><span class="sxs-lookup"><span data-stu-id="7ee89-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="7ee89-120">若要设置配置，请将以下构造函数和属性添加到位于项目根目录中的 *Startup.cs* 文件：</span><span class="sxs-lookup"><span data-stu-id="7ee89-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="7ee89-121">请注意，此时， *Startup.cs* 文件不会进行编译，因为我们仍需要添加以下 `using` 语句：</span><span class="sxs-lookup"><span data-stu-id="7ee89-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="7ee89-122">*appsettings.json* 使用适当的项模板，将文件添加到项目的根目录：</span><span class="sxs-lookup"><span data-stu-id="7ee89-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![添加 AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="7ee89-124">从 web.config 迁移配置设置</span><span class="sxs-lookup"><span data-stu-id="7ee89-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="7ee89-125">在元素的 *web.config* 中，我们的 ASP.NET MVC 项目包含所需的数据库连接字符串 `<connectionStrings>` 。</span><span class="sxs-lookup"><span data-stu-id="7ee89-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="7ee89-126">在 ASP.NET Core 项目中，我们要将此信息存储在文件中 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="7ee89-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="7ee89-127">打开 *appsettings.json* ，请注意，它已包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="7ee89-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="7ee89-128">在上面所示的突出显示的行中，将数据库的名称从 **_CHANGE_ME** 更改为数据库的名称。</span><span class="sxs-lookup"><span data-stu-id="7ee89-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="7ee89-129">总结</span><span class="sxs-lookup"><span data-stu-id="7ee89-129">Summary</span></span>

<span data-ttu-id="7ee89-130">ASP.NET Core 将应用程序的所有启动逻辑放在一个文件中，可以在其中定义和配置所需的服务和依赖项。</span><span class="sxs-lookup"><span data-stu-id="7ee89-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="7ee89-131">它将 *web.config* 文件替换为灵活的配置功能，该功能可利用各种文件格式（如 JSON）以及环境变量。</span><span class="sxs-lookup"><span data-stu-id="7ee89-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
