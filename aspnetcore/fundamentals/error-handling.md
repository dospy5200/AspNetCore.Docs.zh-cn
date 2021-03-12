---
title: 处理 ASP.NET Core 中的错误
author: rick-anderson
description: 了解如何处理 ASP.NET Core 应用中的错误。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: 1687195bc5d80d50289db51a5e8ac6113ca8b05f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586119"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="ee268-103">处理 ASP.NET Core 中的错误</span><span class="sxs-lookup"><span data-stu-id="ee268-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ee268-104">作者：[Kirk Larkin](https://twitter.com/serpent5)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ee268-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ee268-105">本文介绍了处理 ASP.NET Core Web 应用中常见错误的一些方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="ee268-106">有关 Web API，请参阅 <xref:web-api/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="ee268-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="ee268-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="ee268-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="ee268-108">（[下载方法](xref:index#how-to-download-a-sample)。）在测试示例应用时，F12 浏览器开发人员工具上的网络选项卡非常有用。</span><span class="sxs-lookup"><span data-stu-id="ee268-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="ee268-109">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="ee268-109">Developer Exception Page</span></span>

<span data-ttu-id="ee268-110">开发人员异常页显示请求异常的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="ee268-111">ASP.NET Core 模板会生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="ee268-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="ee268-112">当应用在[开发环境](xref:fundamentals/environments)中运行时，前面突出显示的代码启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="ee268-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ee268-113">模板在中间件管道的前面部分放置 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>，以便它可以捕获在后面的中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="ee268-114">仅当应用程序在开发环境中运行时，前面的代码才启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="ee268-114">The preceding code enables the Developer Exception Page ***only*** when the app runs in the Development environment.</span></span> <span data-ttu-id="ee268-115">当应用在生产环境中运行时，不应公开显示详细的异常信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="ee268-116">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="ee268-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ee268-117">开发人员异常页包括关于异常和请求的以下信息：</span><span class="sxs-lookup"><span data-stu-id="ee268-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="ee268-118">堆栈跟踪</span><span class="sxs-lookup"><span data-stu-id="ee268-118">Stack trace</span></span>
* <span data-ttu-id="ee268-119">查询字符串参数（如果有）</span><span class="sxs-lookup"><span data-stu-id="ee268-119">Query string parameters if any</span></span>
* <span data-ttu-id="ee268-120">Cookie（如果有）</span><span class="sxs-lookup"><span data-stu-id="ee268-120">Cookies if any</span></span>
* <span data-ttu-id="ee268-121">标头</span><span class="sxs-lookup"><span data-stu-id="ee268-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="ee268-122">异常处理程序页</span><span class="sxs-lookup"><span data-stu-id="ee268-122">Exception handler page</span></span>

<span data-ttu-id="ee268-123">若要为[生产环境](xref:fundamentals/environments)配置自定义错误处理页，请调用 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>。</span><span class="sxs-lookup"><span data-stu-id="ee268-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="ee268-124">此异常处理中间件：</span><span class="sxs-lookup"><span data-stu-id="ee268-124">This exception handling middleware:</span></span>

* <span data-ttu-id="ee268-125">捕获并记录异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="ee268-126">使用指示的路径在备用管道中重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="ee268-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="ee268-127">如果响应已启动，则不会重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="ee268-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="ee268-128">模板生成的代码使用 `/Error` 路径重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="ee268-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="ee268-129">在下面的示例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 在非开发环境中添加异常处理中间件：</span><span class="sxs-lookup"><span data-stu-id="ee268-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="ee268-130">Razor Pages 应用模板在 Pages 文件夹中提供了一个“错误”页面 (.cshtml) 和 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类 (`ErrorModel`) 。</span><span class="sxs-lookup"><span data-stu-id="ee268-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="ee268-131">对于 MVC 应用，项目模板包括 `Error` 操作方法和主控制器的错误视图。</span><span class="sxs-lookup"><span data-stu-id="ee268-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="ee268-132">异常处理中间件使用原始 HTTP 方法重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="ee268-132">The exception handling middleware re-executes the request using the *original* HTTP method.</span></span> <span data-ttu-id="ee268-133">如果错误处理程序终结点限制为一组特定的 HTTP 方法，那么只会对这些 HTTP 方法运行。</span><span class="sxs-lookup"><span data-stu-id="ee268-133">If an error handler endpoint is restricted to a specific set of HTTP methods, it runs only for those HTTP methods.</span></span> <span data-ttu-id="ee268-134">例如，使用 `[HttpGet]` 属性的 MVC 控制器操作仅对 GET 请求运行。</span><span class="sxs-lookup"><span data-stu-id="ee268-134">For example, an MVC controller action that uses the `[HttpGet]` attribute runs only for GET requests.</span></span> <span data-ttu-id="ee268-135">若要确保所有请求都到达自定义错误处理页，请不要将它们限制为一组特定的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-135">To ensure that *all* requests reach the custom error handling page, don't restrict them to a specific set of HTTP methods.</span></span>

<span data-ttu-id="ee268-136">根据原始 HTTP 方法以不同方式处理异常：</span><span class="sxs-lookup"><span data-stu-id="ee268-136">To handle exceptions differently based on the original HTTP method:</span></span>

* <span data-ttu-id="ee268-137">对于 Razor Pages，创建多个处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-137">For Razor Pages, create multiple handler methods.</span></span> <span data-ttu-id="ee268-138">例如，使用 `OnGet` 处理 GET 异常，使用 `OnPost` 处理 POST 异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-138">For example, use `OnGet` to handle GET exceptions and use `OnPost` to handle POST exceptions.</span></span>
* <span data-ttu-id="ee268-139">对于 MVC，请将 HTTP 谓词属性应用于多个操作。</span><span class="sxs-lookup"><span data-stu-id="ee268-139">For MVC, apply HTTP verb attributes to multiple actions.</span></span> <span data-ttu-id="ee268-140">例如，使用 `[HttpGet]` 处理 GET 异常，使用 `[HttpPost]` 处理 POST 异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-140">For example, use `[HttpGet]` to handle GET exceptions and use `[HttpPost]` to handle POST exceptions.</span></span>

<span data-ttu-id="ee268-141">若要允许未经身份验证的用户查看自定义错误处理页，请确保它支持匿名访问。</span><span class="sxs-lookup"><span data-stu-id="ee268-141">To allow unauthenticated users to view the custom error handling page, ensure that it supports anonymous access.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="ee268-142">访问异常</span><span class="sxs-lookup"><span data-stu-id="ee268-142">Access the exception</span></span>

<span data-ttu-id="ee268-143">使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 访问错误处理程序中的异常和原始请求路径。</span><span class="sxs-lookup"><span data-stu-id="ee268-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="ee268-144">以下代码将 `ExceptionMessage` 添加到由 ASP.NET Core 模板生成的默认 Pages/Error.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="ee268-144">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="ee268-145">请勿向客户端提供敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-145">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="ee268-146">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="ee268-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="ee268-147">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中测试异常，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="ee268-147">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="ee268-148">将环境设置为生产环境。</span><span class="sxs-lookup"><span data-stu-id="ee268-148">Set the environment to production.</span></span>
* <span data-ttu-id="ee268-149">从 Program.cs 中的 `webBuilder.UseStartup<Startup>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="ee268-149">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="ee268-150">在主页上选择“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="ee268-150">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="ee268-151">异常处理程序 lambda</span><span class="sxs-lookup"><span data-stu-id="ee268-151">Exception handler lambda</span></span>

<span data-ttu-id="ee268-152">[自定义异常处理程序页](#exception-handler-page)的替代方法是向 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 提供 lambda。</span><span class="sxs-lookup"><span data-stu-id="ee268-152">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="ee268-153">使用 lambda，可以在返回响应前访问错误。</span><span class="sxs-lookup"><span data-stu-id="ee268-153">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="ee268-154">以下代码使用 lambda 处理异常：</span><span class="sxs-lookup"><span data-stu-id="ee268-154">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="ee268-155">不要向客户端提供来自 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 的敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-155">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="ee268-156">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="ee268-156">Serving errors is a security risk.</span></span>

<span data-ttu-id="ee268-157">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中测试异常处理 lambda：</span><span class="sxs-lookup"><span data-stu-id="ee268-157">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="ee268-158">将环境设置为生产环境。</span><span class="sxs-lookup"><span data-stu-id="ee268-158">Set the environment to production.</span></span>
* <span data-ttu-id="ee268-159">从 Program.cs 中的 `webBuilder.UseStartup<StartupLambda>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="ee268-159">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="ee268-160">在主页上选择“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="ee268-160">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="ee268-161">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="ee268-161">UseStatusCodePages</span></span>

<span data-ttu-id="ee268-162">默认情况下，ASP.NET Core 应用不会为 HTTP 错误状态代码（如“404 - 未找到”）提供状态代码页。</span><span class="sxs-lookup"><span data-stu-id="ee268-162">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="ee268-163">当应用遇到没有正文的 HTTP 400-599 错误状态代码时，它将返回状态代码和空响应正文。</span><span class="sxs-lookup"><span data-stu-id="ee268-163">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="ee268-164">若要提供状态代码页，请使用状态代码页中间件。</span><span class="sxs-lookup"><span data-stu-id="ee268-164">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="ee268-165">若要启用常见错误状态代码的默认纯文本处理程序，请在 `Startup.Configure` 方法中调用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：</span><span class="sxs-lookup"><span data-stu-id="ee268-165">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="ee268-166">在请求处理中间件之前调用 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="ee268-166">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="ee268-167">例如，在静态文件中间件和端点中间件之前调用 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="ee268-167">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="ee268-168">未使用 `UseStatusCodePages` 时，导航到没有终结点的 URL 会返回一条与浏览器相关的错误消息，指示找不到终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-168">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="ee268-169">例如，导航到 `Home/Privacy2`。</span><span class="sxs-lookup"><span data-stu-id="ee268-169">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="ee268-170">调用 `UseStatusCodePages` 时，浏览器返回：</span><span class="sxs-lookup"><span data-stu-id="ee268-170">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="ee268-171">`UseStatusCodePages` 通常不在生产中使用，因为它返回对用户没有用的消息。</span><span class="sxs-lookup"><span data-stu-id="ee268-171">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="ee268-172">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`：</span><span class="sxs-lookup"><span data-stu-id="ee268-172">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="ee268-173">将环境设置为生产环境。</span><span class="sxs-lookup"><span data-stu-id="ee268-173">Set the environment to production.</span></span>
* <span data-ttu-id="ee268-174">从 Program.cs 中的 `webBuilder.UseStartup<StartupUseStatusCodePages>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="ee268-174">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="ee268-175">选择主页上的链接。</span><span class="sxs-lookup"><span data-stu-id="ee268-175">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="ee268-176">状态代码页中间件不捕获异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-176">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="ee268-177">若要提供自定义错误处理页，请使用[异常处理程序页](#exception-handler-page)。</span><span class="sxs-lookup"><span data-stu-id="ee268-177">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="ee268-178">包含格式字符串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="ee268-178">UseStatusCodePages with format string</span></span>

<span data-ttu-id="ee268-179">若要自定义响应内容类型和文本，请利用需要使用内容类型和格式字符串的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="ee268-179">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="ee268-180">在前面的代码中，`{0}` 是错误代码的占位符。</span><span class="sxs-lookup"><span data-stu-id="ee268-180">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="ee268-181">具有格式字符串的 `UseStatusCodePages` 通常不在生产环境中使用，因为它返回对用户没有用的消息。</span><span class="sxs-lookup"><span data-stu-id="ee268-181">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="ee268-182">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`，请从 Program.cs 中的 `webBuilder.UseStartup<StartupFormat>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="ee268-182">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="ee268-183">包含 lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="ee268-183">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="ee268-184">若要指定自定义错误处理和响应写入代码，请利用需要使用 lambda 表达式的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="ee268-184">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="ee268-185">使用 lambda 的 `UseStatusCodePages` 通常不在生产中使用，因为它返回对用户没有用的消息。</span><span class="sxs-lookup"><span data-stu-id="ee268-185">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="ee268-186">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`，请从 Program.cs 中的 `webBuilder.UseStartup<StartupStatusLambda>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="ee268-186">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="ee268-187">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="ee268-187">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="ee268-188"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ee268-188">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="ee268-189">向客户端发送“302 - 已找到”状态代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-189">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="ee268-190">将客户端重定向到 URL 模板中提供的错误处理终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-190">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="ee268-191">错误处理终结点通常会显示错误信息并返回 HTTP 200。</span><span class="sxs-lookup"><span data-stu-id="ee268-191">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="ee268-192">URL 模板可能会包括状态代码的 `{0}` 占位符，如前面的代码中所示。</span><span class="sxs-lookup"><span data-stu-id="ee268-192">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="ee268-193">如果 URL 模板以波形符 `~`（代字号）开头，则 `~` 会替换为应用的 `PathBase`。</span><span class="sxs-lookup"><span data-stu-id="ee268-193">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="ee268-194">在应用中指定终结点时，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="ee268-194">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="ee268-195">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中的 [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)。</span><span class="sxs-lookup"><span data-stu-id="ee268-195">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="ee268-196">使用此方法通常是当应用：</span><span class="sxs-lookup"><span data-stu-id="ee268-196">This method is commonly used when the app:</span></span>

* <span data-ttu-id="ee268-197">应将客户端重定向到不同的终结点（通常在不同的应用处理错误的情况下）。</span><span class="sxs-lookup"><span data-stu-id="ee268-197">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="ee268-198">对于 Web 应用，客户端的浏览器地址栏反映重定向终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-198">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="ee268-199">不应保留原始状态代码并通过初始重定向响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-199">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="ee268-200">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`，请从 Program.cs 中的 `webBuilder.UseStartup<StartupSCredirect>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="ee268-200">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="ee268-201">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="ee268-201">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="ee268-202"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ee268-202">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="ee268-203">向客户端返回原始状态代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-203">Returns the original status code to the client.</span></span>
* <span data-ttu-id="ee268-204">通过使用备用路径重新执行请求管道，从而生成响应正文。</span><span class="sxs-lookup"><span data-stu-id="ee268-204">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="ee268-205">如果在应用中指定终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="ee268-205">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="ee268-206">确保将 `UseStatusCodePagesWithReExecute` 放置在 `UseRouting` 之前，以便可以将请求重新路由到状态页。</span><span class="sxs-lookup"><span data-stu-id="ee268-206">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="ee268-207">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中的 [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)。</span><span class="sxs-lookup"><span data-stu-id="ee268-207">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="ee268-208">使用此方法通常是当应用应：</span><span class="sxs-lookup"><span data-stu-id="ee268-208">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="ee268-209">处理请求，但不重定向到不同终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-209">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="ee268-210">对于 Web 应用，客户端的浏览器地址栏反映最初请求的终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-210">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="ee268-211">保留原始状态代码并通过响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-211">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="ee268-212">URL 模板和查询字符串模板可能包括状态代码的占位符 `{0}`。</span><span class="sxs-lookup"><span data-stu-id="ee268-212">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="ee268-213">URL 模板必须以 `/` 开头。</span><span class="sxs-lookup"><span data-stu-id="ee268-213">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="ee268-214">错误处理终结点可以获取生成错误的原始 URL，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="ee268-214">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="ee268-215">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中的 [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)。</span><span class="sxs-lookup"><span data-stu-id="ee268-215">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="ee268-216">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`，请从 Program.cs 中的 `webBuilder.UseStartup<StartupSCreX>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="ee268-216">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="ee268-217">禁用状态代码页</span><span class="sxs-lookup"><span data-stu-id="ee268-217">Disable status code pages</span></span>

<span data-ttu-id="ee268-218">若要禁用 MVC 控制器或操作方法的状态代码页，请使用 [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="ee268-218">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="ee268-219">若要禁用 Razor Pages 处理程序方法或 MVC 控制器中的特定请求的状态代码页，请使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>：</span><span class="sxs-lookup"><span data-stu-id="ee268-219">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="ee268-220">异常处理代码</span><span class="sxs-lookup"><span data-stu-id="ee268-220">Exception-handling code</span></span>

<span data-ttu-id="ee268-221">异常处理页中的代码可能也会引发异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-221">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="ee268-222">应彻底测试生产错误页面，并格外小心，避免引发其自己的异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-222">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="ee268-223">响应头</span><span class="sxs-lookup"><span data-stu-id="ee268-223">Response headers</span></span>

<span data-ttu-id="ee268-224">在响应头发送后：</span><span class="sxs-lookup"><span data-stu-id="ee268-224">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="ee268-225">应用无法更改响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-225">The app can't change the response's status code.</span></span>
* <span data-ttu-id="ee268-226">任何异常页或处理程序都无法运行。</span><span class="sxs-lookup"><span data-stu-id="ee268-226">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="ee268-227">必须完成响应或中止连接。</span><span class="sxs-lookup"><span data-stu-id="ee268-227">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="ee268-228">服务器异常处理</span><span class="sxs-lookup"><span data-stu-id="ee268-228">Server exception handling</span></span>

<span data-ttu-id="ee268-229">除了应用中的异常处理逻辑外，[HTTP 服务器实现](xref:fundamentals/servers/index)还能处理一些异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-229">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="ee268-230">如果服务器在发送响应标头之前捕获到异常，服务器将发送不包含响应正文的 `500 - Internal Server Error` 响应。</span><span class="sxs-lookup"><span data-stu-id="ee268-230">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="ee268-231">如果服务器在发送响应标头后捕获到异常，服务器会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="ee268-231">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="ee268-232">应用程序无法处理的请求将由服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="ee268-232">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="ee268-233">当服务器处理请求时，发生的任何异常都将由服务器的异常处理进行处理。</span><span class="sxs-lookup"><span data-stu-id="ee268-233">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="ee268-234">应用的自定义错误页面、异常处理中间件和筛选器都不会影响此行为。</span><span class="sxs-lookup"><span data-stu-id="ee268-234">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="ee268-235">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="ee268-235">Startup exception handling</span></span>

<span data-ttu-id="ee268-236">应用程序启动期间发生的异常仅可在承载层进行处理。</span><span class="sxs-lookup"><span data-stu-id="ee268-236">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="ee268-237">可以将主机配置为，[捕获启动错误](xref:fundamentals/host/web-host#capture-startup-errors)和[捕获详细错误](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="ee268-237">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="ee268-238">仅当错误在主机地址/端口绑定后出现时，托管层才能显示捕获的启动错误的错误页。</span><span class="sxs-lookup"><span data-stu-id="ee268-238">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="ee268-239">如果绑定失败：</span><span class="sxs-lookup"><span data-stu-id="ee268-239">If binding fails:</span></span>

* <span data-ttu-id="ee268-240">托管层将记录关键异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-240">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="ee268-241">dotnet 进程崩溃。</span><span class="sxs-lookup"><span data-stu-id="ee268-241">The dotnet process crashes.</span></span>
* <span data-ttu-id="ee268-242">不会在 HTTP 服务器为 [Kestrel](xref:fundamentals/servers/kestrel) 时显示任何错误页。</span><span class="sxs-lookup"><span data-stu-id="ee268-242">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="ee268-243">在 [IIS](/iis)（或 Azure 应用服务）或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上运行应用时，如果无法启动进程，[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将返回“502.5 - 进程失败”。</span><span class="sxs-lookup"><span data-stu-id="ee268-243">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="ee268-244">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="ee268-244">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="ee268-245">数据库错误页</span><span class="sxs-lookup"><span data-stu-id="ee268-245">Database error page</span></span>

<span data-ttu-id="ee268-246">数据库开发人员页面异常筛选器 `AddDatabaseDeveloperPageExceptionFilter` 捕获可以使用 Entity Framework Core 迁移解决的与数据库相关的异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-246">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="ee268-247">当这些异常出现时，便会生成 HTML 响应，其中包含用于解决问题的可能操作的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-247">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="ee268-248">仅在开发环境中启用此页。</span><span class="sxs-lookup"><span data-stu-id="ee268-248">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="ee268-249">在指定个人用户帐户时，ASP.NET Core Razor Pages 模板生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="ee268-249">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="ee268-250">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="ee268-250">Exception filters</span></span>

<span data-ttu-id="ee268-251">在 MVC 应用中，可以全局配置异常筛选器，也可以为每个控制器或每个操作单独配置。</span><span class="sxs-lookup"><span data-stu-id="ee268-251">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="ee268-252">在 Razor Pages 应用中，可以全局配置异常筛选器，也可以为每个页面模型单独配置。</span><span class="sxs-lookup"><span data-stu-id="ee268-252">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="ee268-253">这些筛选器处理在执行控制器操作或其他筛选器时出现的任何未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-253">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="ee268-254">有关详细信息，请参阅 <xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="ee268-254">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="ee268-255">异常筛选器适合捕获 MVC 操作内发生的异常，但它们不如内置[异常处理中间件](https://github.com/dotnet/aspnetcore/blob/main/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` 灵活。</span><span class="sxs-lookup"><span data-stu-id="ee268-255">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/main/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="ee268-256">我们建议使用 `UseExceptionHandler`，除非你需要根据选择的 MVC 操作以不同的方式执行错误处理。</span><span class="sxs-lookup"><span data-stu-id="ee268-256">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="ee268-257">模型状态错误</span><span class="sxs-lookup"><span data-stu-id="ee268-257">Model state errors</span></span>

<span data-ttu-id="ee268-258">若要了解如何处理模型状态错误，请参阅[模型绑定](xref:mvc/models/model-binding)和[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="ee268-258">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee268-259">其他资源</span><span class="sxs-lookup"><span data-stu-id="ee268-259">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ee268-260">作者：[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ee268-260">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ee268-261">本文介绍了处理 ASP.NET Core Web 应用中常见错误的一些方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-261">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="ee268-262">有关 Web API，请参阅 <xref:web-api/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="ee268-262">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="ee268-263">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="ee268-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="ee268-264">（[下载方法](xref:index#how-to-download-a-sample)。）</span><span class="sxs-lookup"><span data-stu-id="ee268-264">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="ee268-265">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="ee268-265">Developer Exception Page</span></span>

<span data-ttu-id="ee268-266">开发人员异常页显示请求异常的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-266">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="ee268-267">ASP.NET Core 模板会生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="ee268-267">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="ee268-268">当应用在[开发环境](xref:fundamentals/environments)中运行时，前面的代码启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="ee268-268">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ee268-269">模板将 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> 放在任何中间件之前，以便捕获后面的中间件中的异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-269">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="ee268-270">仅当应用程序在开发环境中运行时，前面的代码才启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="ee268-270">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="ee268-271">当应用在生产环境中运行时，不应公开显示详细的异常信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-271">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="ee268-272">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="ee268-272">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="ee268-273">开发人员异常页包括关于异常和请求的以下信息：</span><span class="sxs-lookup"><span data-stu-id="ee268-273">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="ee268-274">堆栈跟踪</span><span class="sxs-lookup"><span data-stu-id="ee268-274">Stack trace</span></span>
* <span data-ttu-id="ee268-275">查询字符串参数（如果有）</span><span class="sxs-lookup"><span data-stu-id="ee268-275">Query string parameters if any</span></span>
* <span data-ttu-id="ee268-276">Cookie（如果有）</span><span class="sxs-lookup"><span data-stu-id="ee268-276">Cookies if any</span></span>
* <span data-ttu-id="ee268-277">标头</span><span class="sxs-lookup"><span data-stu-id="ee268-277">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="ee268-278">异常处理程序页</span><span class="sxs-lookup"><span data-stu-id="ee268-278">Exception handler page</span></span>

<span data-ttu-id="ee268-279">若要为生产环境配置自定义错误处理页，请使用异常处理中间件。</span><span class="sxs-lookup"><span data-stu-id="ee268-279">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="ee268-280">中间件：</span><span class="sxs-lookup"><span data-stu-id="ee268-280">The middleware:</span></span>

* <span data-ttu-id="ee268-281">捕获并记录异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-281">Catches and logs exceptions.</span></span>
* <span data-ttu-id="ee268-282">在备用管道中为指定的页或控制器重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="ee268-282">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="ee268-283">如果响应已启动，则不会重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="ee268-283">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="ee268-284">模板生成的代码将请求重新执行到 `/Error`。</span><span class="sxs-lookup"><span data-stu-id="ee268-284">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="ee268-285">在下面的示例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 在非开发环境中添加异常处理中间件：</span><span class="sxs-lookup"><span data-stu-id="ee268-285">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="ee268-286">Razor Pages 应用模板在 Pages 文件夹中提供了一个“错误”页面 (.cshtml) 和 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类 (`ErrorModel`) 。</span><span class="sxs-lookup"><span data-stu-id="ee268-286">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="ee268-287">对于 MVC 应用，项目模板包括错误操作方法和主控制器的错误视图。</span><span class="sxs-lookup"><span data-stu-id="ee268-287">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="ee268-288">不要使用 HTTP 方法属性（如 `HttpGet`）标记错误处理程序操作方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-288">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="ee268-289">显式谓词可阻止某些请求访问方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-289">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="ee268-290">如果未经身份验证的用户应看到错误视图，则允许匿名访问该方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-290">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="ee268-291">访问异常</span><span class="sxs-lookup"><span data-stu-id="ee268-291">Access the exception</span></span>

<span data-ttu-id="ee268-292">使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 访问错误处理程序控制器或页中的异常和原始请求路径：</span><span class="sxs-lookup"><span data-stu-id="ee268-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="ee268-293">请勿向客户端提供敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-293">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="ee268-294">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="ee268-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="ee268-295">若要触发前面的异常处理页，请将环境设置为生产环境并强制引发异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-295">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="ee268-296">异常处理程序 lambda</span><span class="sxs-lookup"><span data-stu-id="ee268-296">Exception handler lambda</span></span>

<span data-ttu-id="ee268-297">[自定义异常处理程序页](#exception-handler-page)的替代方法是向 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 提供 lambda。</span><span class="sxs-lookup"><span data-stu-id="ee268-297">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="ee268-298">使用 lambda，可以在返回响应前访问错误。</span><span class="sxs-lookup"><span data-stu-id="ee268-298">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="ee268-299">下面的示例展示了如何使用 lambda 进行异常处理：</span><span class="sxs-lookup"><span data-stu-id="ee268-299">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="ee268-300">在前面的代码中，添加了 `await context.Response.WriteAsync(new string(' ', 512));`，以便 Internet Explorer 浏览器显示相应的错误消息，而非显示 IE 错误消息。</span><span class="sxs-lookup"><span data-stu-id="ee268-300">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="ee268-301">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16144)。</span><span class="sxs-lookup"><span data-stu-id="ee268-301">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="ee268-302">不要向客户端提供来自 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 的敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-302">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="ee268-303">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="ee268-303">Serving errors is a security risk.</span></span>

<span data-ttu-id="ee268-304">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples)中查看异常处理 lambda 的结果，请使用 `ProdEnvironment` 和 `ErrorHandlerLambda` 预处理器指令，并选择主页上的“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="ee268-304">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="ee268-305">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="ee268-305">UseStatusCodePages</span></span>

<span data-ttu-id="ee268-306">默认情况下，ASP.NET Core 应用不会为 HTTP 状态代码（如“404 - 未找到”）提供状态代码页。</span><span class="sxs-lookup"><span data-stu-id="ee268-306">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="ee268-307">应用返回状态代码和空响应正文。</span><span class="sxs-lookup"><span data-stu-id="ee268-307">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="ee268-308">若要提供状态代码页，请使用状态代码页中间件。</span><span class="sxs-lookup"><span data-stu-id="ee268-308">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="ee268-309">此中间件是通过 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="ee268-309">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="ee268-310">若要启用常见错误状态代码的默认纯文本处理程序，请在 `Startup.Configure` 方法中调用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：</span><span class="sxs-lookup"><span data-stu-id="ee268-310">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="ee268-311">在请求处理中间件（例如，静态文件中间件和 MVC 中间件）前面调用 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="ee268-311">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="ee268-312">未使用 `UseStatusCodePages` 时，导航到没有终结点的 URL 会返回一条与浏览器相关的错误消息，指示找不到终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-312">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="ee268-313">例如，导航到 `Home/Privacy2`。</span><span class="sxs-lookup"><span data-stu-id="ee268-313">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="ee268-314">调用 `UseStatusCodePages` 时，浏览器返回：</span><span class="sxs-lookup"><span data-stu-id="ee268-314">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="ee268-315">包含格式字符串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="ee268-315">UseStatusCodePages with format string</span></span>

<span data-ttu-id="ee268-316">若要自定义响应内容类型和文本，请利用需要使用内容类型和格式字符串的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="ee268-316">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="ee268-317">包含 lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="ee268-317">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="ee268-318">若要指定自定义错误处理和响应写入代码，请利用需要使用 lambda 表达式的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="ee268-318">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="ee268-319">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="ee268-319">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="ee268-320"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ee268-320">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="ee268-321">向客户端发送“302 - 已找到”状态代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-321">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="ee268-322">将客户端重定向到 URL 模板中的位置。</span><span class="sxs-lookup"><span data-stu-id="ee268-322">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="ee268-323">URL 模板可能会包括状态代码的 `{0}` 占位符，如上面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="ee268-323">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="ee268-324">如果 URL 模板以波形符 `~`（代字号）开头，则 `~` 会替换为应用的 `PathBase`。</span><span class="sxs-lookup"><span data-stu-id="ee268-324">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="ee268-325">如果在应用中指向终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="ee268-325">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="ee268-326">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples)中的 Pages/StatusCode.cshtml。</span><span class="sxs-lookup"><span data-stu-id="ee268-326">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="ee268-327">使用此方法通常是当应用：</span><span class="sxs-lookup"><span data-stu-id="ee268-327">This method is commonly used when the app:</span></span>

* <span data-ttu-id="ee268-328">应将客户端重定向到不同的终结点（通常在不同的应用处理错误的情况下）。</span><span class="sxs-lookup"><span data-stu-id="ee268-328">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="ee268-329">对于 Web 应用，客户端的浏览器地址栏反映重定向终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-329">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="ee268-330">不应保留原始状态代码并通过初始重定向响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-330">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="ee268-331">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="ee268-331">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="ee268-332"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="ee268-332">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="ee268-333">向客户端返回原始状态代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-333">Returns the original status code to the client.</span></span>
* <span data-ttu-id="ee268-334">通过使用备用路径重新执行请求管道，从而生成响应正文。</span><span class="sxs-lookup"><span data-stu-id="ee268-334">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="ee268-335">如果在应用中指向终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="ee268-335">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="ee268-336">确保将 `UseStatusCodePagesWithReExecute` 放置在 `UseRouting` 之前，以便可以将请求重新路由到状态页。</span><span class="sxs-lookup"><span data-stu-id="ee268-336">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="ee268-337">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples)中的 Pages/StatusCode.cshtml。</span><span class="sxs-lookup"><span data-stu-id="ee268-337">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="ee268-338">使用此方法通常是当应用应：</span><span class="sxs-lookup"><span data-stu-id="ee268-338">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="ee268-339">处理请求，但不重定向到不同终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-339">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="ee268-340">对于 Web 应用，客户端的浏览器地址栏反映最初请求的终结点。</span><span class="sxs-lookup"><span data-stu-id="ee268-340">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="ee268-341">保留原始状态代码并通过响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-341">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="ee268-342">URL 模板和查询字符串模板可能包括状态代码的占位符 (`{0}`)。</span><span class="sxs-lookup"><span data-stu-id="ee268-342">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="ee268-343">URL 模板必须以斜杠 (`/`) 开头。</span><span class="sxs-lookup"><span data-stu-id="ee268-343">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="ee268-344">若要在路径中使用占位符，请确认终结点（页或控制器）能否处理路径段。</span><span class="sxs-lookup"><span data-stu-id="ee268-344">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="ee268-345">例如，错误的 Razor 页面应通过 `@page` 指令接受可选路径段值：</span><span class="sxs-lookup"><span data-stu-id="ee268-345">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="ee268-346">错误处理终结点可以获取生成错误的原始 URL，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="ee268-346">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

<span data-ttu-id="ee268-347">不要使用 HTTP 方法属性（如 `HttpGet`）标记错误处理程序操作方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-347">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="ee268-348">显式谓词可阻止某些请求访问方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-348">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="ee268-349">如果未经身份验证的用户应看到错误视图，则允许匿名访问该方法。</span><span class="sxs-lookup"><span data-stu-id="ee268-349">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="ee268-350">禁用状态代码页</span><span class="sxs-lookup"><span data-stu-id="ee268-350">Disable status code pages</span></span>

<span data-ttu-id="ee268-351">若要禁用 MVC 控制器或操作方法的状态代码页，请使用 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="ee268-351">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="ee268-352">若要禁用 Razor Pages 处理程序方法或 MVC 控制器中的特定请求的状态代码页，请使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>：</span><span class="sxs-lookup"><span data-stu-id="ee268-352">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="ee268-353">异常处理代码</span><span class="sxs-lookup"><span data-stu-id="ee268-353">Exception-handling code</span></span>

<span data-ttu-id="ee268-354">异常处理页中的代码可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-354">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="ee268-355">建议在生产错误页面中包含纯静态内容。</span><span class="sxs-lookup"><span data-stu-id="ee268-355">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="ee268-356">响应头</span><span class="sxs-lookup"><span data-stu-id="ee268-356">Response headers</span></span>

<span data-ttu-id="ee268-357">在响应头发送后：</span><span class="sxs-lookup"><span data-stu-id="ee268-357">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="ee268-358">应用无法更改响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="ee268-358">The app can't change the response's status code.</span></span>
* <span data-ttu-id="ee268-359">任何异常页或处理程序都无法运行。</span><span class="sxs-lookup"><span data-stu-id="ee268-359">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="ee268-360">必须完成响应或中止连接。</span><span class="sxs-lookup"><span data-stu-id="ee268-360">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="ee268-361">服务器异常处理</span><span class="sxs-lookup"><span data-stu-id="ee268-361">Server exception handling</span></span>

<span data-ttu-id="ee268-362">除了应用中的异常处理逻辑外，[HTTP 服务器实现](xref:fundamentals/servers/index)还能处理一些异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-362">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="ee268-363">如果服务器在发送响应标头之前捕获到异常，服务器将发送不包含响应正文的“500 - 内部服务器错误”响应。</span><span class="sxs-lookup"><span data-stu-id="ee268-363">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="ee268-364">如果服务器在发送响应标头后捕获到异常，服务器会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="ee268-364">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="ee268-365">应用程序无法处理的请求将由服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="ee268-365">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="ee268-366">当服务器处理请求时，发生的任何异常都将由服务器的异常处理进行处理。</span><span class="sxs-lookup"><span data-stu-id="ee268-366">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="ee268-367">应用的自定义错误页面、异常处理中间件和筛选器都不会影响此行为。</span><span class="sxs-lookup"><span data-stu-id="ee268-367">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="ee268-368">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="ee268-368">Startup exception handling</span></span>

<span data-ttu-id="ee268-369">应用程序启动期间发生的异常仅可在承载层进行处理。</span><span class="sxs-lookup"><span data-stu-id="ee268-369">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="ee268-370">可以将主机配置为，[捕获启动错误](xref:fundamentals/host/web-host#capture-startup-errors)和[捕获详细错误](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="ee268-370">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="ee268-371">仅当错误在主机地址/端口绑定后出现时，托管层才能显示捕获的启动错误的错误页。</span><span class="sxs-lookup"><span data-stu-id="ee268-371">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="ee268-372">如果绑定失败：</span><span class="sxs-lookup"><span data-stu-id="ee268-372">If binding fails:</span></span>

* <span data-ttu-id="ee268-373">托管层将记录关键异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-373">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="ee268-374">dotnet 进程崩溃。</span><span class="sxs-lookup"><span data-stu-id="ee268-374">The dotnet process crashes.</span></span>
* <span data-ttu-id="ee268-375">不会在 HTTP 服务器为 [Kestrel](xref:fundamentals/servers/kestrel) 时显示任何错误页。</span><span class="sxs-lookup"><span data-stu-id="ee268-375">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="ee268-376">在 [IIS](/iis)（或 Azure 应用服务）或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上运行应用时，如果无法启动进程，[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将返回“502.5 - 进程失败”。</span><span class="sxs-lookup"><span data-stu-id="ee268-376">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="ee268-377">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="ee268-377">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="ee268-378">数据库错误页</span><span class="sxs-lookup"><span data-stu-id="ee268-378">Database error page</span></span>

<span data-ttu-id="ee268-379">数据库错误页中间件捕获与数据库相关的异常，可使用实体框架迁移来解析这些异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-379">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="ee268-380">当这些异常出现时，便会生成 HTML 响应，其中包含用于解决问题的可能操作的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ee268-380">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="ee268-381">应仅在开发环境中启用此页。</span><span class="sxs-lookup"><span data-stu-id="ee268-381">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="ee268-382">通过向 `Startup.Configure` 添加代码来启用此页：</span><span class="sxs-lookup"><span data-stu-id="ee268-382">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="ee268-383"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> 需要 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ee268-383"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="ee268-384">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="ee268-384">Exception filters</span></span>

<span data-ttu-id="ee268-385">在 MVC 应用中，可以全局配置异常筛选器，也可以为每个控制器或每个操作单独配置。</span><span class="sxs-lookup"><span data-stu-id="ee268-385">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="ee268-386">在 Razor Pages 应用中，可以全局配置异常筛选器，也可以为每个页面模型单独配置。</span><span class="sxs-lookup"><span data-stu-id="ee268-386">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="ee268-387">这些筛选器处理在执行控制器操作或其他筛选器时出现的任何未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="ee268-387">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="ee268-388">有关详细信息，请参阅 <xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="ee268-388">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="ee268-389">异常筛选器适合捕获 MVC 操作内发生的异常，但它们不如异常处理中间件灵活。</span><span class="sxs-lookup"><span data-stu-id="ee268-389">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="ee268-390">建议使用中间件。</span><span class="sxs-lookup"><span data-stu-id="ee268-390">We recommend using the middleware.</span></span> <span data-ttu-id="ee268-391">仅在需要根据选定 MVC 操作以不同方式执行错误处理时，才使用筛选器。</span><span class="sxs-lookup"><span data-stu-id="ee268-391">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="ee268-392">模型状态错误</span><span class="sxs-lookup"><span data-stu-id="ee268-392">Model state errors</span></span>

<span data-ttu-id="ee268-393">若要了解如何处理模型状态错误，请参阅[模型绑定](xref:mvc/models/model-binding)和[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="ee268-393">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ee268-394">其他资源</span><span class="sxs-lookup"><span data-stu-id="ee268-394">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
