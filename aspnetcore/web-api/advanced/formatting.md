---
title: 设置 ASP.NET Core Web API 中响应数据的格式
author: rick-anderson
description: 了解如何设置 ASP.NET Core Web API 中响应数据的格式。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: e3e176072032494f94f120efabb95d6c127fb5e6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587809"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="dd997-103">设置 ASP.NET Core Web API 中响应数据的格式</span><span class="sxs-lookup"><span data-stu-id="dd997-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="dd997-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dd997-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dd997-105">ASP.NET Core MVC 支持设置响应数据的格式。</span><span class="sxs-lookup"><span data-stu-id="dd997-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="dd997-106">可以使用特定格式或响应客户端请求的格式，来设置响应数据的格式。</span><span class="sxs-lookup"><span data-stu-id="dd997-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="dd997-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/formatting)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="dd997-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="dd997-108">特定于格式的操作结果</span><span class="sxs-lookup"><span data-stu-id="dd997-108">Format-specific Action Results</span></span>

<span data-ttu-id="dd997-109">一些操作结果类型特定于特殊格式，例如 <xref:Microsoft.AspNetCore.Mvc.JsonResult> 和 <xref:Microsoft.AspNetCore.Mvc.ContentResult>。</span><span class="sxs-lookup"><span data-stu-id="dd997-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="dd997-110">操作可以返回使用特定格式设置格式的结果，而不考虑客户端首选项。</span><span class="sxs-lookup"><span data-stu-id="dd997-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="dd997-111">例如，返回 `JsonResult`，将返回 JSON 格式的数据。</span><span class="sxs-lookup"><span data-stu-id="dd997-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="dd997-112">返回 `ContentResult` 或字符串，将返回纯文本格式的字符串数据。</span><span class="sxs-lookup"><span data-stu-id="dd997-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="dd997-113">无需操作返回任意特定类型。</span><span class="sxs-lookup"><span data-stu-id="dd997-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="dd997-114">ASP.NET Core 支持任何对象返回值。</span><span class="sxs-lookup"><span data-stu-id="dd997-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="dd997-115">返回非 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 类型对象的操作结果将使用相应的 <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> 实现来进行序列化。</span><span class="sxs-lookup"><span data-stu-id="dd997-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="dd997-116">有关详细信息，请参阅 <xref:web-api/action-return-types>。</span><span class="sxs-lookup"><span data-stu-id="dd997-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="dd997-117">内置的帮助程序方法 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> 返回 JSON 格式的数据：[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="dd997-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="dd997-118">示例下载返回作者列表。</span><span class="sxs-lookup"><span data-stu-id="dd997-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="dd997-119">在 F12 浏览器开发人员工具或 [Postman](https://www.getpostman.com/tools) 中使用上述代码：</span><span class="sxs-lookup"><span data-stu-id="dd997-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="dd997-120">将显示包含内容类型的响应标头。 `application/json; charset=utf-8`</span><span class="sxs-lookup"><span data-stu-id="dd997-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="dd997-121">将显示请求标头。</span><span class="sxs-lookup"><span data-stu-id="dd997-121">The request headers are displayed.</span></span> <span data-ttu-id="dd997-122">例如 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="dd997-122">For example, the `Accept` header.</span></span> <span data-ttu-id="dd997-123">上述代码将忽略 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="dd997-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="dd997-124">若要返回纯文本格式数据，请使用 <xref:Microsoft.AspNetCore.Mvc.ContentResult> 和 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> 帮助程序：</span><span class="sxs-lookup"><span data-stu-id="dd997-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="dd997-125">在上述代码中，返回的 `text/plain` 为 `Content-Type`。</span><span class="sxs-lookup"><span data-stu-id="dd997-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="dd997-126">返回字符串，将提供 `text/plain` 类型的 `Content-Type`：</span><span class="sxs-lookup"><span data-stu-id="dd997-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="dd997-127">对于包含多个返回类型的操作，将返回 `IActionResult`。</span><span class="sxs-lookup"><span data-stu-id="dd997-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="dd997-128">例如，基于执行的操作的结果返回不同的 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="dd997-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="dd997-129">内容协商</span><span class="sxs-lookup"><span data-stu-id="dd997-129">Content negotiation</span></span>

<span data-ttu-id="dd997-130">当客户端指定 [Accept 标头](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)时，会发生内容协商。</span><span class="sxs-lookup"><span data-stu-id="dd997-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="dd997-131">ASP.NET Core 使用的默认格式是 [JSON](https://json.org/)。</span><span class="sxs-lookup"><span data-stu-id="dd997-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="dd997-132">内容协商有以下特点：</span><span class="sxs-lookup"><span data-stu-id="dd997-132">Content negotiation is:</span></span>

* <span data-ttu-id="dd997-133">由 <xref:Microsoft.AspNetCore.Mvc.ObjectResult> 实现。</span><span class="sxs-lookup"><span data-stu-id="dd997-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="dd997-134">内置于从帮助程序方法返回的特定于状态代码的操作结果中。</span><span class="sxs-lookup"><span data-stu-id="dd997-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="dd997-135">操作结果帮助程序方法基于 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="dd997-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="dd997-136">返回模型类型后，返回类型为 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="dd997-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="dd997-137">以下操作方法使用 `Ok` 和 `NotFound` 帮助程序方法：</span><span class="sxs-lookup"><span data-stu-id="dd997-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="dd997-138">默认情况下，ASP.NET Core 支持 `application/json`、`text/json` 和 `text/plain` 媒体类型。</span><span class="sxs-lookup"><span data-stu-id="dd997-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="dd997-139">[Fiddler](https://www.telerik.com/fiddler) 或 [Postman](https://www.getpostman.com/tools) 等工具可以设置 `Accept` 请求标头，来指定返回格式。</span><span class="sxs-lookup"><span data-stu-id="dd997-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="dd997-140">`Accept` 标头包含服务器支持的类型时，将返回该类型。</span><span class="sxs-lookup"><span data-stu-id="dd997-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="dd997-141">下一节将介绍如何添加其他格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="dd997-142">控制器操作可以返回 POCO（普通旧 CLR 对象）。</span><span class="sxs-lookup"><span data-stu-id="dd997-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="dd997-143">返回 POCO 时，运行时自动创建包装该对象的 `ObjectResult`。</span><span class="sxs-lookup"><span data-stu-id="dd997-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="dd997-144">客户端将获得已格式化和序列化的对象。</span><span class="sxs-lookup"><span data-stu-id="dd997-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="dd997-145">若将返回的对象为 `null`，将返回 `204 No Content` 响应。</span><span class="sxs-lookup"><span data-stu-id="dd997-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="dd997-146">返回对象类型：</span><span class="sxs-lookup"><span data-stu-id="dd997-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="dd997-147">在前面的代码中，请求有效作者别名将返回具有作者数据的 `200 OK` 响应。</span><span class="sxs-lookup"><span data-stu-id="dd997-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="dd997-148">请求无效别名将返回 `204 No Content` 响应。</span><span class="sxs-lookup"><span data-stu-id="dd997-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="dd997-149">Accept 标头</span><span class="sxs-lookup"><span data-stu-id="dd997-149">The Accept header</span></span>

<span data-ttu-id="dd997-150">内容协商在 `Accept` 标头出现在请求中时发生。</span><span class="sxs-lookup"><span data-stu-id="dd997-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="dd997-151">请求包含 Accept 标头时，ASP.NET Core 将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="dd997-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="dd997-152">按首选顺序枚举 Accept 标头中的媒体类型。</span><span class="sxs-lookup"><span data-stu-id="dd997-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="dd997-153">尝试找到可以生成某种指定格式的响应的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="dd997-154">若未找到可以满足客户端请求的格式化程序，ASP.NET Core 将指定以下操作：</span><span class="sxs-lookup"><span data-stu-id="dd997-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="dd997-155">`406 Not Acceptable`如果 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> 设置为，则返回 `true` ; 否则返回-</span><span class="sxs-lookup"><span data-stu-id="dd997-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> is set to `true`, or -</span></span>
* <span data-ttu-id="dd997-156">尝试找到第一个可以生成响应的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="dd997-157">如果没有配置实现所请求格式的格式化程序，那么使用第一个可以设置对象格式的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="dd997-158">若请求中没有 `Accept` 标头：</span><span class="sxs-lookup"><span data-stu-id="dd997-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="dd997-159">将使用第一个可以处理对象的格式化程序来将响应序列化。</span><span class="sxs-lookup"><span data-stu-id="dd997-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="dd997-160">不执行任何协商。</span><span class="sxs-lookup"><span data-stu-id="dd997-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="dd997-161">服务器将决定要返回的格式。</span><span class="sxs-lookup"><span data-stu-id="dd997-161">The server is determining what format to return.</span></span>

<span data-ttu-id="dd997-162">如果 Accept 标头包含 `*/*`，则将忽略该标头，除非 `RespectBrowserAcceptHeader` 在 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 上设置为 true。</span><span class="sxs-lookup"><span data-stu-id="dd997-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="dd997-163">浏览器和内容协商</span><span class="sxs-lookup"><span data-stu-id="dd997-163">Browsers and content negotiation</span></span>

<span data-ttu-id="dd997-164">与典型的 API 客户端不同的是，Web 浏览器提供 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="dd997-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="dd997-165">Web 浏览器指定多种格式，包括通配符。</span><span class="sxs-lookup"><span data-stu-id="dd997-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="dd997-166">默认情况下，当框架检测到请求来自浏览器时，将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="dd997-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="dd997-167">忽略 `Accept` 标头。</span><span class="sxs-lookup"><span data-stu-id="dd997-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="dd997-168">若未另行配置，将使用 JSON 返回内容。</span><span class="sxs-lookup"><span data-stu-id="dd997-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="dd997-169">这样，在使用 API 时，浏览器中的体验将更加一致。</span><span class="sxs-lookup"><span data-stu-id="dd997-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="dd997-170">若要将应用配置为采用浏览器 Accept 标头，请将 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="dd997-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="dd997-171">配置格式化程序</span><span class="sxs-lookup"><span data-stu-id="dd997-171">Configure formatters</span></span>

<span data-ttu-id="dd997-172">需要支持其他格式的应用可以添加相应的 NuGet 包，并配置支持。</span><span class="sxs-lookup"><span data-stu-id="dd997-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="dd997-173">输入和输出的格式化程序不同。</span><span class="sxs-lookup"><span data-stu-id="dd997-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="dd997-174">[模型绑定](xref:mvc/models/model-binding)使用输入格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="dd997-175">格式响应使用输出格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="dd997-176">有关创建自定义格式化程序的信息，请参阅[自定义格式化程序](xref:web-api/advanced/custom-formatters)。</span><span class="sxs-lookup"><span data-stu-id="dd997-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="dd997-177">添加 XML 格式支持</span><span class="sxs-lookup"><span data-stu-id="dd997-177">Add XML format support</span></span>

<span data-ttu-id="dd997-178">调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> 来配置使用 <xref:System.Xml.Serialization.XmlSerializer> 实现的 XML 格式化程序：</span><span class="sxs-lookup"><span data-stu-id="dd997-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="dd997-179">前面的代码将使用 `XmlSerializer` 将结果序列化。</span><span class="sxs-lookup"><span data-stu-id="dd997-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="dd997-180">使用前面的代码时，控制器方法会基于请求的 `Accept` 标头返回相应的格式。</span><span class="sxs-lookup"><span data-stu-id="dd997-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="dd997-181">配置 `System.Text.Json` 基于的格式化程序</span><span class="sxs-lookup"><span data-stu-id="dd997-181">Configure `System.Text.Json` based formatters</span></span>

<span data-ttu-id="dd997-182">`System.Text.Json`可以使用配置基于的格式化程序的功能 <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> 。</span><span class="sxs-lookup"><span data-stu-id="dd997-182">Features for the `System.Text.Json` based formatters can be configured using <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName>.</span></span> <span data-ttu-id="dd997-183">默认格式为 camelCase。</span><span class="sxs-lookup"><span data-stu-id="dd997-183">The default formatting is camelCase.</span></span> <span data-ttu-id="dd997-184">以下突出显示的代码设置 PascalCase 格式设置：</span><span class="sxs-lookup"><span data-stu-id="dd997-184">The following highlighted code sets PascalCase formatting:</span></span>

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="dd997-185">以下操作方法会调用 [ControllerBase](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) ，以创建 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> 响应：</span><span class="sxs-lookup"><span data-stu-id="dd997-185">The following action method calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response:</span></span>

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

<span data-ttu-id="dd997-186">带有前面的代码：</span><span class="sxs-lookup"><span data-stu-id="dd997-186">With the preceding code:</span></span>

  * <span data-ttu-id="dd997-187">`https://localhost:5001/WeatherForecast/temperature` 返回 PascalCase。</span><span class="sxs-lookup"><span data-stu-id="dd997-187">`https://localhost:5001/WeatherForecast/temperature` returns PascalCase.</span></span>
  * <span data-ttu-id="dd997-188">`https://localhost:5001/WeatherForecast/error` 返回 camelCase。</span><span class="sxs-lookup"><span data-stu-id="dd997-188">`https://localhost:5001/WeatherForecast/error` returns camelCase.</span></span> <span data-ttu-id="dd997-189">即使应用程序将格式设置为 PascalCase，也始终会 camelCase 错误响应。</span><span class="sxs-lookup"><span data-stu-id="dd997-189">The error response is always camelCase, even when the app sets the format to PascalCase.</span></span> <span data-ttu-id="dd997-190">`ProblemDetails` 遵循 [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A)，它指定小写</span><span class="sxs-lookup"><span data-stu-id="dd997-190">`ProblemDetails` follows [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), which specifies lower case</span></span>

<span data-ttu-id="dd997-191">下面的代码设置 PascalCase 并添加一个自定义转换器：</span><span class="sxs-lookup"><span data-stu-id="dd997-191">The following code sets PascalCase and adds a custom converter:</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="dd997-192">可以使用 `JsonResult` 配置基于每个操作的输出序列化选项。</span><span class="sxs-lookup"><span data-stu-id="dd997-192">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="dd997-193">例如：</span><span class="sxs-lookup"><span data-stu-id="dd997-193">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="dd997-194">添加基于 Newtonsoft.Json 的 JSON 格式支持</span><span class="sxs-lookup"><span data-stu-id="dd997-194">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="dd997-195">ASP.NET Core 3.0 之前的版本中，默认设置使用通过 `Newtonsoft.Json` 包实现的 JSON 格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-195">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="dd997-196">在 ASP.NET Core 3.0 或更高版本中，默认 JSON 格式化程序基于 `System.Text.Json`。</span><span class="sxs-lookup"><span data-stu-id="dd997-196">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="dd997-197">`Newtonsoft.Json`通过安装 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 包并在中进行配置，可获得对基于的格式化程序和功能的支持 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="dd997-197">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="dd997-198">在前面的代码中，对的调用将 `AddNewtonsoftJson` 配置以下 WEB API、MVC 和 Razor 页面功能以供使用 `Newtonsoft.Json` ：</span><span class="sxs-lookup"><span data-stu-id="dd997-198">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="dd997-199">用于读取和写入 JSON 的输入和输出格式化程序</span><span class="sxs-lookup"><span data-stu-id="dd997-199">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="dd997-200">JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="dd997-200">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="dd997-201">TempData</span><span class="sxs-lookup"><span data-stu-id="dd997-201">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="dd997-202">某些功能可能不适用于基于 `System.Text.Json` 的格式化程序，而需要引用基于 `Newtonsoft.Json` 的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-202">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="dd997-203">若应用符合以下情况，请继续使用基于 `Newtonsoft.Json` 的格式化程序：</span><span class="sxs-lookup"><span data-stu-id="dd997-203">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="dd997-204">使用 `Newtonsoft.Json` 属性。</span><span class="sxs-lookup"><span data-stu-id="dd997-204">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="dd997-205">例如 `[JsonProperty]` 或 `[JsonIgnore]`。</span><span class="sxs-lookup"><span data-stu-id="dd997-205">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="dd997-206">自定义序列化设置。</span><span class="sxs-lookup"><span data-stu-id="dd997-206">Customizes the serialization settings.</span></span>
* <span data-ttu-id="dd997-207">依赖 `Newtonsoft.Json` 提供的功能。</span><span class="sxs-lookup"><span data-stu-id="dd997-207">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="dd997-208">配置 `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`。</span><span class="sxs-lookup"><span data-stu-id="dd997-208">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="dd997-209">ASP.NET Core 3.0 之前的版本中，`JsonResult.SerializerSettings` 接受特定于 `Newtonsoft.Json` 的 `JsonSerializerSettings` 的实例。</span><span class="sxs-lookup"><span data-stu-id="dd997-209">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="dd997-210">生成 [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) 文档。</span><span class="sxs-lookup"><span data-stu-id="dd997-210">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="dd997-211">可以使用 `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` 配置基于 `Newtonsoft.Json` 的格式化程序的功能：</span><span class="sxs-lookup"><span data-stu-id="dd997-211">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="dd997-212">可以使用 `JsonResult` 配置基于每个操作的输出序列化选项。</span><span class="sxs-lookup"><span data-stu-id="dd997-212">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="dd997-213">例如：</span><span class="sxs-lookup"><span data-stu-id="dd997-213">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="dd997-214">添加 XML 格式支持</span><span class="sxs-lookup"><span data-stu-id="dd997-214">Add XML format support</span></span>

<span data-ttu-id="dd997-215">XML 格式需要 [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="dd997-215">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="dd997-216">调用 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> 来配置使用 <xref:System.Xml.Serialization.XmlSerializer> 实现的 XML 格式化程序：</span><span class="sxs-lookup"><span data-stu-id="dd997-216">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="dd997-217">前面的代码将使用 `XmlSerializer` 将结果序列化。</span><span class="sxs-lookup"><span data-stu-id="dd997-217">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="dd997-218">使用前面的代码时，控制器方法应基于请求的 `Accept` 标头返回相应的格式。</span><span class="sxs-lookup"><span data-stu-id="dd997-218">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="dd997-219">指定格式</span><span class="sxs-lookup"><span data-stu-id="dd997-219">Specify a format</span></span>

<span data-ttu-id="dd997-220">若要限制响应格式，请应用 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 筛选器。</span><span class="sxs-lookup"><span data-stu-id="dd997-220">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="dd997-221">与大多数 [筛选器](xref:mvc/controllers/filters)一样， `[Produces]` 可以在操作、控制器或全局范围内应用：</span><span class="sxs-lookup"><span data-stu-id="dd997-221">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="dd997-222">上述 [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) 筛选器：</span><span class="sxs-lookup"><span data-stu-id="dd997-222">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="dd997-223">强制控制器内的所有操作返回 JSON 格式的响应。</span><span class="sxs-lookup"><span data-stu-id="dd997-223">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="dd997-224">若已配置其他格式化程序，并且客户端指定了其他格式，将返回 JSON。</span><span class="sxs-lookup"><span data-stu-id="dd997-224">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="dd997-225">有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)。</span><span class="sxs-lookup"><span data-stu-id="dd997-225">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="dd997-226">特例格式化程序</span><span class="sxs-lookup"><span data-stu-id="dd997-226">Special case formatters</span></span>

<span data-ttu-id="dd997-227">一些特例是使用内置格式化程序实现的。</span><span class="sxs-lookup"><span data-stu-id="dd997-227">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="dd997-228">默认情况下，`string` 返回类型的格式将设为 text/plain（如果通过 `Accept` 标头请求则为 text/html）。</span><span class="sxs-lookup"><span data-stu-id="dd997-228">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="dd997-229">可以通过删除 <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> 删除此行为。</span><span class="sxs-lookup"><span data-stu-id="dd997-229">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="dd997-230">在 `ConfigureServices` 方法中删除格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-230">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="dd997-231">有模型对象返回类型的操作将在返回 `null` 时返回 `204 No Content`。</span><span class="sxs-lookup"><span data-stu-id="dd997-231">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="dd997-232">可以通过删除 <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> 删除此行为。</span><span class="sxs-lookup"><span data-stu-id="dd997-232">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="dd997-233">以下代码删除 `StringOutputFormatter` 和 `HttpNoContentOutputFormatter`。</span><span class="sxs-lookup"><span data-stu-id="dd997-233">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="dd997-234">如果没有 `StringOutputFormatter`，内置 JSON 格式化程序将设置 `string` 返回类型的格式。</span><span class="sxs-lookup"><span data-stu-id="dd997-234">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="dd997-235">如果删除了内置 JSON 格式化程序并提供了 XML 格式化程序，则 XML 格式化程序将设置 `string` 返回类型的格式。</span><span class="sxs-lookup"><span data-stu-id="dd997-235">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="dd997-236">否则，`string` 返回类型返回 `406 Not Acceptable`。</span><span class="sxs-lookup"><span data-stu-id="dd997-236">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="dd997-237">没有 `HttpNoContentOutputFormatter`，null 对象将使用配置的格式化程序来进行格式设置。</span><span class="sxs-lookup"><span data-stu-id="dd997-237">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="dd997-238">例如：</span><span class="sxs-lookup"><span data-stu-id="dd997-238">For example:</span></span>

* <span data-ttu-id="dd997-239">JSON 格式化程序返回正文为 `null` 的响应。</span><span class="sxs-lookup"><span data-stu-id="dd997-239">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="dd997-240">设置属性 `xsi:nil="true"` 时，XML 格式化程序返回空 XML 元素。</span><span class="sxs-lookup"><span data-stu-id="dd997-240">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="dd997-241">响应格式 URL 映射</span><span class="sxs-lookup"><span data-stu-id="dd997-241">Response format URL mappings</span></span>

<span data-ttu-id="dd997-242">客户端可以在 URL 中请求特定格式，例如：</span><span class="sxs-lookup"><span data-stu-id="dd997-242">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="dd997-243">在查询字符串中，或在路径中。</span><span class="sxs-lookup"><span data-stu-id="dd997-243">In the query string or part of the path.</span></span>
* <span data-ttu-id="dd997-244">使用格式特定的文件扩展名，如 .xml 或 .json。</span><span class="sxs-lookup"><span data-stu-id="dd997-244">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="dd997-245">请求路径的映射必须在 API 使用的路由中指定。</span><span class="sxs-lookup"><span data-stu-id="dd997-245">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="dd997-246">例如：</span><span class="sxs-lookup"><span data-stu-id="dd997-246">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="dd997-247">上述路由将允许指定所请求格式为可选文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="dd997-247">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="dd997-248">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)特性检查中的格式值是否存在 `RouteData` ，并在创建响应时将响应格式映射到适当的格式化程序。</span><span class="sxs-lookup"><span data-stu-id="dd997-248">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="dd997-249">路由</span><span class="sxs-lookup"><span data-stu-id="dd997-249">Route</span></span>        |             <span data-ttu-id="dd997-250">格式化程序</span><span class="sxs-lookup"><span data-stu-id="dd997-250">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="dd997-251">默认输出格式化程序</span><span class="sxs-lookup"><span data-stu-id="dd997-251">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="dd997-252">JSON 格式化程序（如配置）</span><span class="sxs-lookup"><span data-stu-id="dd997-252">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="dd997-253">XML 格式化程序（如配置）</span><span class="sxs-lookup"><span data-stu-id="dd997-253">The XML formatter (if configured)</span></span>  |
