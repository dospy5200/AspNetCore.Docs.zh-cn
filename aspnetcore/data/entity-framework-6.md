---
title: ASP.NET Core 和 Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 及更高版本适用于 ASP.NET Core 3.1 及更高版本。
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 44211ac7fa2acc7a7a9471ef362cff02f94fa2b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588264"
---
# <a name="aspnet-core-and-entity-framework-6"></a>ASP.NET Core 和 Entity Framework 6
::: moniker range=">= aspnetcore-3.0"

作者：[Patrick Goode](https://github.com/attrib75)

## <a name="using-entity-framework-6-with-aspnet-core"></a>将 Entity Framework 6 与 ASP.NET Core 结合使用

应在新开发中使用 [Entity Framework Core](/ef/)。 [下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample)使用 [Entity Framework 6 (EF6)](/ef/ef6)，其可用于将现有应用迁移到 ASP.NET Core。

## <a name="additional-resources"></a>其他资源

* [Entity Framework - 基于代码的配置](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Paweł Grudzień](https://github.com/pgrudzien12)、[Damien Pontifex](https://github.com/DamienPontifex) 和 [Tom Dykstra](https://github.com/tdykstra)

本文演示如何在 ASP.NET Core 应用程序中使用 Entity Framework 6。    

## <a name="overview"></a>概述 

若要使用 Entity Framework 6，则项目必须面向 .NET Framework 进行编译，因为 Entity Framework 6 不支持 .NET Core。 如果需要跨平台功能，需升级到 [Entity Framework Core](/ef/)。  

在 ASP.NET Core 应用程序中使用 Entity Framework 6 的推荐方法是：将 EF6 上下文和模型类放入面向 .NET Framework 的类库项目中。 添加对 ASP.NET Core 项目中的类库的引用。 请参阅示例[针对 EF6 和 ASP.NET Core 项目的 Visual Studio 解决方案](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/)。    

不能将 EF6 上下文放入 ASP.NET Core 项目，因为 .NET Core 项目不支持 EF6 命令（如 Enable-Migrations）所需的的各项功能。    

无论 EF6 上下文属于哪种项目类型，只有 EF6 命令行工具才能使用 EF6 上下文。 例如，`Scaffold-DbContext` 仅在 Entity Framework Core 中可用。 如果需要对数据库执行反向工程以使其成为 EF6 模型，请参阅<https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>。    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>在 ASP.NET Core 项目中引用完整框架和 EF6 

ASP.NET Core 项目需要面向 .NET Framework 和引用 EF6。 例如，ASP.NET Core 项目的 .csproj 文件将与以下示例类似（仅显示该文件的相关部分）。    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

创建新项目时，请使用 ASP.NET Core Web 应用程序（.NET Framework）模板。    

## <a name="handle-connection-strings"></a>处理连接字符串    

需通过默认构造函数在 EF6 类库项目中使用 EF6 命令行工具，以便它们能够实例化上下文。 但是，如果想指定要在 ASP.NET Core 项目中使用的连接字符串，则上下文构造函数必须具有可允许你在连接字符串中进行传递的参数。 示例如下。   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

由于 EF6 上下文不具有无参数构造函数，因此 EF6 项目必须提供 <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> 的实现。 EF6 命令行工具将查找和使用该实现，以便它们能够实例化上下文。 示例如下。   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

在此示例代码中，`IDbContextFactory` 实现将在硬编码的连接字符串中传递。 这是命令行工具要使用的连接字符串。 你需要实施策略以确保类库与调用应用程序使用相同的连接字符串。 例如，可从这两个项目的环境变量中获取值。   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>在 ASP.NET Core 项目中设置依赖项注入  

在 Core 项目的 Startup.cs 文件中，为 `ConfigureServices` 中的依赖项注入 (DI) 设置 EF6 上下文。 应将 EF 上下文对象的范围设置为按请求生存期。   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

然后即可使用 DI 在控制器中获取上下文的实例。 此代码与针对 EF Core 上下文编写的代码相似：    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a>示例应用程序   

若要获取有效的示例应用程序，请参阅本文随附的[示例 Visual Studio 解决方案](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/)。    

可在 Visual Studio 中按照以下步骤从头创建此示例：    

* 创建解决方案。    

* “添加”>“新建项目”>“Web”>“ASP.NET Core Web 应用程序”       
  * 在“项目模板选择”对话框的下拉列表中，选择 API 和 .NET Framework 

* “添加”>“新建项目”>“Windows 桌面”>“类库(.NET Framework)”     

* 在两个项目的“包管理器控制台”(PMC) 中运行 `Install-Package Entityframework` 命令。    

* 在类库项目中，创建数据模型类和上下文类，并创建 `IDbContextFactory` 的实现。    

* 在类库项目的 PMC 中，运行 `Enable-Migrations` 和 `Add-Migration Initial` 命令。 如果已将 ASP.NET Core 项目设置为启动项目，请向这些命令添加 `-StartupProjectName EF6`。 

* 在 Core 项目中，添加对类库项目的项目引用。    

* 在 Core 项目的 Startup.cs 中，为 DI 注册上下文。    

* 在 Core 项目的 *appsettings.json* 中，添加连接字符串。  

* 在 Core 项目中，添加控制器和视图以验证可读取和写入数据。 （请注意，ASP.NET Core MVC 基架不会使用从类库引用的 EF6 上下文。）

::: moniker-end
