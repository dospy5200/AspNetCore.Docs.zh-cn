---
title: 使用 ASP.NET Core 中的第三方容器激活中间件
author: rick-anderson
description: 了解如何在基于工厂的激活和 ASP.NET Core 中的第三方容器中使用强类型中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: bbbfe0e9fba33eba9d69101e5615e931ea917bc1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587367"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>使用 ASP.NET Core 中的第三方容器激活中间件

::: moniker range=">= aspnetcore-3.0"

本文演示如何使用 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 和 <xref:Microsoft.AspNetCore.Http.IMiddleware> 作为使用第三方容器激活[中间件](xref:fundamentals/middleware/index)的可扩展点。 有关 `IMiddlewareFactory` 和 `IMiddleware` 的入门信息，请参阅 <xref:fundamentals/middleware/extensibility>。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

示例应用演示了使用 `IMiddlewareFactory`、`SimpleInjectorMiddlewareFactory` 实现激活的中间件。 此示例使用 [Simple Injector](https://simpleinjector.org) 依赖项注入 (DI) 容器。

此示例的中间件实现记录了查询字符串参数 (`key`) 提供的值。 中间件使用插入的数据库上下文（有作用域的服务）将查询字符串值记录在内存中数据库。

> [!NOTE]
> 此示例应用仅出于演示目的使用 [Simple Injector](https://github.com/simpleinjector/SimpleInjector)。 不认可使用 Simple Injector。 Simple Injector 文档中描述的中间件激活方法和 Simple Injector 维护人员推荐的 GitHub 问题。 有关详细信息，请参阅 [Simple Injector 文档](https://simpleinjector.readthedocs.io/en/latest/index.html)和 [Simple Injector GitHub 存储库](https://github.com/simpleinjector/SimpleInjector)。

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供中间件的创建方法。

在示例应用中，实现了中间件工厂以创建 `SimpleInjectorActivatedMiddleware` 实例。 中间件工厂使用 Simple Injector 容器来解析中间件：

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> 定义应用的请求管道的中间件。

由 `IMiddlewareFactory` 实现 (Middleware/SimpleInjectorActivatedMiddleware.cs) 激活的中间件：

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

为中间件创建扩展 (Middleware/MiddlewareExtensions.cs)：

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices` 必须执行多项任务：

* 设置 Simple Injector 容器。
* 注册工厂和中间件。
* 使 Simple Injector 容器提供应用的数据库上下文。

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

中间件在 `Startup.Configure` 的请求处理管道中注册：

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本文演示如何使用 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 和 <xref:Microsoft.AspNetCore.Http.IMiddleware> 作为使用第三方容器激活[中间件](xref:fundamentals/middleware/index)的可扩展点。 有关 `IMiddlewareFactory` 和 `IMiddleware` 的入门信息，请参阅 <xref:fundamentals/middleware/extensibility>。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

示例应用演示了使用 `IMiddlewareFactory`、`SimpleInjectorMiddlewareFactory` 实现激活的中间件。 此示例使用 [Simple Injector](https://simpleinjector.org) 依赖项注入 (DI) 容器。

此示例的中间件实现记录了查询字符串参数 (`key`) 提供的值。 中间件使用插入的数据库上下文（有作用域的服务）将查询字符串值记录在内存中数据库。

> [!NOTE]
> 此示例应用仅出于演示目的使用 [Simple Injector](https://github.com/simpleinjector/SimpleInjector)。 不认可使用 Simple Injector。 Simple Injector 文档中描述的中间件激活方法和 Simple Injector 维护人员推荐的 GitHub 问题。 有关详细信息，请参阅 [Simple Injector 文档](https://simpleinjector.readthedocs.io/en/latest/index.html)和 [Simple Injector GitHub 存储库](https://github.com/simpleinjector/SimpleInjector)。

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供中间件的创建方法。

在示例应用中，实现了中间件工厂以创建 `SimpleInjectorActivatedMiddleware` 实例。 中间件工厂使用 Simple Injector 容器来解析中间件：

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> 定义应用的请求管道的中间件。

由 `IMiddlewareFactory` 实现 (Middleware/SimpleInjectorActivatedMiddleware.cs) 激活的中间件：

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

为中间件创建扩展 (Middleware/MiddlewareExtensions.cs)：

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices` 必须执行多项任务：

* 设置 Simple Injector 容器。
* 注册工厂和中间件。
* 使 Simple Injector 容器提供应用的数据库上下文。

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

中间件在 `Startup.Configure` 的请求处理管道中注册：

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [中间件](xref:fundamentals/middleware/index)
* [基于工厂的中间件激活](xref:fundamentals/middleware/extensibility)
* [Simple Injector GitHub 存储库](https://github.com/simpleinjector/SimpleInjector)
* [Simple Injector 文档](https://simpleinjector.readthedocs.io/en/latest/index.html)
