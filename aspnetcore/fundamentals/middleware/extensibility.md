---
title: ASP.NET Core 中基于工厂的中间件激活
author: rick-anderson
description: 了解如何在 ASP.NET Core 中通过基于工厂的激活实现使用强类型中间件。
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: c4835d0d9b0cc20a1176bbb5b0aedf3c5b7d51af
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587184"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>ASP.NET Core 中基于工厂的中间件激活

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> 是[中间件](xref:fundamentals/middleware/index)激活的扩展点。

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 扩展方法检查中间件的已注册类型是否实现 <xref:Microsoft.AspNetCore.Http.IMiddleware>。 如果是，则使用在容器中注册的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 实例来解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 实现，而不使用基于约定的中间件激活逻辑。 中间件在应用的服务容器中注册为[作用域或瞬态](xref:fundamentals/dependency-injection#service-lifetimes)服务。

优点：

* 按客户端请求（作用域服务的注入）激活
* 让中间件强类型化

<xref:Microsoft.AspNetCore.Http.IMiddleware> 按客户端请求（连接）激活，因此作用域服务可以注入到中间件的构造函数中。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> 定义应用的请求管道的中间件。 [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) 方法处理请求，并返回代表中间件执行的 <xref:System.Threading.Tasks.Task>。

使用约定激活的中间件：

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

使用 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> 激活的中间件：

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

程序会为中间件创建扩展：

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

无法通过 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 将对象传递给工厂激活的中间件：

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

将工厂激活的中间件添加到 `Startup.ConfigureServices` 的内置容器中：

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

两个中间件均在 `Startup.Configure` 的请求处理管道中注册：

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供中间件的创建方法。 中间件工厂实现在容器中注册为作用域服务。

可在 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 包中找到默认的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 实现（即 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>）。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> 是[中间件](xref:fundamentals/middleware/index)激活的扩展点。

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 扩展方法检查中间件的已注册类型是否实现 <xref:Microsoft.AspNetCore.Http.IMiddleware>。 如果是，则使用在容器中注册的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 实例来解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 实现，而不使用基于约定的中间件激活逻辑。 中间件在应用的服务容器中注册为[作用域或瞬态](xref:fundamentals/dependency-injection#service-lifetimes)服务。

优点：

* 按客户端请求（作用域服务的注入）激活
* 让中间件强类型化

<xref:Microsoft.AspNetCore.Http.IMiddleware> 按客户端请求（连接）激活，因此作用域服务可以注入到中间件的构造函数中。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware/extensibility/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> 定义应用的请求管道的中间件。 [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) 方法处理请求，并返回代表中间件执行的 <xref:System.Threading.Tasks.Task>。

使用约定激活的中间件：

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

使用 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> 激活的中间件：

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

程序会为中间件创建扩展：

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

无法通过 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 将对象传递给工厂激活的中间件：

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

将工厂激活的中间件添加到 `Startup.ConfigureServices` 的内置容器中：

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

两个中间件均在 `Startup.Configure` 的请求处理管道中注册：

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 提供中间件的创建方法。 中间件工厂实现在容器中注册为作用域服务。

可在 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 包中找到默认的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 实现（即 <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>）。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
