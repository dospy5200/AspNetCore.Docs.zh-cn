---
title: " (CORS) 启用跨域请求 ASP.NET Core"
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中允许或拒绝跨源请求的标准。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 7afa8105e0ab007153d5c3e8238765d4e9f22641
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586795"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="4f0fb-103"> (CORS) 启用跨域请求 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f0fb-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4f0fb-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4f0fb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="4f0fb-105">本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="4f0fb-106">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="4f0fb-107">此限制称为同域策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="4f0fb-108">同域策略可防止恶意站点从另一站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="4f0fb-109">有时，你可能想要允许其他站点对你的应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="4f0fb-110">有关详细信息，请参阅 [MOZILLA CORS 一文](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="4f0fb-111">[跨源资源共享](https://www.w3.org/TR/cors/) (CORS) ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="4f0fb-112">是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="4f0fb-113">**不** 是一项安全功能，CORS 放宽 security。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="4f0fb-114">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="4f0fb-115">有关详细信息，请参阅 [CORS 的工作](#how-cors)原理。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="4f0fb-116">允许服务器明确允许一些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="4f0fb-117">比早期的技术（如 [JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="4f0fb-118">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4f0fb-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="4f0fb-119">同一原点</span><span class="sxs-lookup"><span data-stu-id="4f0fb-119">Same origin</span></span>

<span data-ttu-id="4f0fb-120">如果两个 Url 具有相同的方案、主机和端口 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，则它们具有相同的源。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="4f0fb-121">这两个 Url 具有相同的源：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="4f0fb-122">这些 Url 的起源不同于前两个 Url：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="4f0fb-123">`https://example.net`：不同的域</span><span class="sxs-lookup"><span data-stu-id="4f0fb-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="4f0fb-124">`https://www.example.com/foo.html`：不同的子域</span><span class="sxs-lookup"><span data-stu-id="4f0fb-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="4f0fb-125">`http://example.com/foo.html`：不同方案</span><span class="sxs-lookup"><span data-stu-id="4f0fb-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="4f0fb-126">`https://example.com:9000/foo.html`：不同的端口</span><span class="sxs-lookup"><span data-stu-id="4f0fb-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="4f0fb-127">启用 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-127">Enable CORS</span></span>

<span data-ttu-id="4f0fb-128">有三种方法可启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="4f0fb-129">使用 [命名策略](#np) 或 [默认策略](#dp)的中间件。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="4f0fb-130">使用 [终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="4f0fb-131">带有 [[EnableCors]](#attr) 属性的。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="4f0fb-132">通过命名策略使用 [[EnableCors]](#attr) 属性，可在限制支持 CORS 的终结点时提供最佳控制。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="4f0fb-133"><xref:Owin.CorsExtensions.UseCors%2A> 使用时，必须调用 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> `UseResponseCaching` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="4f0fb-134">以下各节详细介绍了每种方法。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="4f0fb-135">具有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="4f0fb-136">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="4f0fb-137">以下代码将 CORS 策略应用到具有指定来源的所有应用的终结点：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="4f0fb-138">上述代码：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-138">The preceding code:</span></span>

* <span data-ttu-id="4f0fb-139">将策略名称设置为 `_myAllowSpecificOrigins` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="4f0fb-140">策略名称为任意名称。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="4f0fb-141">调用 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 扩展方法并指定  `_myAllowSpecificOrigins` CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="4f0fb-142">`UseCors` 添加 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="4f0fb-143">必须将对的调用 `UseCors` 置于之后 `UseRouting` 但在之前 `UseAuthorization` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="4f0fb-144">有关详细信息，请参阅 [中间件顺序](xref:fundamentals/middleware/index#middleware-order)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="4f0fb-145"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="4f0fb-146">Lambda 采用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 对象。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="4f0fb-147">本文稍后将介绍[配置选项](#cors-policy-options)，如 `WithOrigins` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="4f0fb-148">启用 `_myAllowSpecificOrigins` 所有控制器终结点的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="4f0fb-149">请参阅 [终结点路由](#ecors) ，将 CORS 策略应用到特定终结点。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="4f0fb-150">使用 [响应缓存中间件](xref:performance/caching/middleware)时，请 <xref:Owin.CorsExtensions.UseCors%2A> 先调用 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="4f0fb-151">通过终结点路由，CORS 中间件 **必须** 配置为在对和的调用之间执行 `UseRouting` `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="4f0fb-152">有关测试代码的说明，请参阅 [测试 CORS](#testc) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="4f0fb-153"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="4f0fb-154">有关详细信息，请参阅本文档中的 [CORS 策略选项](#cpo) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="4f0fb-155">这些 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 方法可以链接在一起，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="4f0fb-156">注意：指定的 URL **不** 能包含尾随斜杠 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="4f0fb-157">如果 URL 以结尾 `/` ，则比较返回， `false` 不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="4f0fb-158">具有默认策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="4f0fb-159">以下突出显示的代码将启用默认 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="4f0fb-160">前面的代码将默认的 CORS 策略应用到所有控制器终结点。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="4f0fb-161">通过终结点路由启用 Cors</span><span class="sxs-lookup"><span data-stu-id="4f0fb-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="4f0fb-162">目前使用每个终结点启用 CORS 不 `RequireCors` 支持[自动预检请求](#apf)。 </span><span class="sxs-lookup"><span data-stu-id="4f0fb-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="4f0fb-163">有关详细信息，请参阅 [此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/20709) 和 [测试与终结点路由和 [HTTPOPTIONS] 的 CORS](#tcer)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="4f0fb-164">使用终结点路由，可以使用一组扩展方法在每个终结点上启用 CORS <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="4f0fb-165">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-165">In the preceding code:</span></span>

* <span data-ttu-id="4f0fb-166">`app.UseCors` 启用 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="4f0fb-167">由于尚未配置默认策略，因此 `app.UseCors()` 单独不启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="4f0fb-168">`/echo`和控制器端点允许使用指定策略的跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="4f0fb-169">`/echo2`和 Razor Pages 终结点 **不** 允许跨源请求，因为未指定默认策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="4f0fb-170">[[DisableCors]](#dc)特性 **不** 会禁用通过终结点路由启用的 CORS `RequireCors` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="4f0fb-171">请参阅 [测试与终结点路由和 [HttpOptions] 的 CORS](#tcer) ，获取与前面类似的代码测试说明。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="4f0fb-172">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-172">Enable CORS with attributes</span></span>

<span data-ttu-id="4f0fb-173">使用 [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) 属性启用 CORS，并将命名策略应用到只有那些需要 CORS 的终结点提供了精细的控制。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="4f0fb-174">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了一种用于全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="4f0fb-175">`[EnableCors]`特性启用所选终结点的 CORS，而不是所有终结点：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="4f0fb-176">`[EnableCors]` 指定默认策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="4f0fb-177">`[EnableCors("{Policy String}")]` 指定命名策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="4f0fb-178">`[EnableCors]`特性可应用于：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="4f0fb-179">Razor 分页 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="4f0fb-179">Razor Page `PageModel`</span></span>
* <span data-ttu-id="4f0fb-180">控制器</span><span class="sxs-lookup"><span data-stu-id="4f0fb-180">Controller</span></span>
* <span data-ttu-id="4f0fb-181">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="4f0fb-181">Controller action method</span></span>

<span data-ttu-id="4f0fb-182">可以将不同的策略应用到具有属性的控制器、页面模型或操作方法 `[EnableCors]` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="4f0fb-183">如果将 `[EnableCors]` 属性应用于控制器、页面模型或操作方法，并在中间件中启用了 CORS，则会应用 **这两种** 策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="4f0fb-184">**建议不要结合策略。使用** `[EnableCors]` **特性或中间件，而不是在同一应用中。**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="4f0fb-185">下面的代码将不同的策略应用于每个方法：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="4f0fb-186">下面的代码创建两个 CORS 策略：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="4f0fb-187">对于限制 CORS 请求的最佳控制：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="4f0fb-188">`[EnableCors("MyPolicy")]`与命名策略一起使用。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="4f0fb-189">不要定义默认策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-189">Don't define a default policy.</span></span>
* <span data-ttu-id="4f0fb-190">请勿使用 [终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="4f0fb-191">下一节中的代码满足前面的列表。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="4f0fb-192">有关测试代码的说明，请参阅 [测试 CORS](#testc) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="4f0fb-193">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-193">Disable CORS</span></span>

<span data-ttu-id="4f0fb-194">[[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) **特性不会禁用已** 通过 [终结点路由](#ecors)启用的 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="4f0fb-195">以下代码定义 CORS 策略 `"MyPolicy"` ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="4f0fb-196">以下代码禁用操作的 CORS `GetValues2` ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="4f0fb-197">上述代码：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-197">The preceding code:</span></span>

* <span data-ttu-id="4f0fb-198">不会使用 [终结点路由](#ecors)启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="4f0fb-199">不定义 [默认的 CORS 策略](#dp)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="4f0fb-200">使用 [[EnableCors ( "MyPolicy" ) ]](#attr) 启用控制器的 `"MyPolicy"` CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="4f0fb-201">为方法禁用 CORS `GetValues2` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="4f0fb-202">有关测试上述代码的说明，请参阅 [测试 CORS](#testc) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="4f0fb-203">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="4f0fb-203">CORS policy options</span></span>

<span data-ttu-id="4f0fb-204">本部分介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="4f0fb-205">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="4f0fb-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="4f0fb-206">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="4f0fb-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="4f0fb-207">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="4f0fb-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="4f0fb-208">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="4f0fb-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="4f0fb-209">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="4f0fb-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="4f0fb-210">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="4f0fb-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="4f0fb-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中调用 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4f0fb-212">对于某些选项，最好先阅读 [CORS 如何工作](#how-cors) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="4f0fb-213">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="4f0fb-213">Set the allowed origins</span></span>

<span data-ttu-id="4f0fb-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允许所有来源的 CORS 请求与任何方案 (`http` 或 `https`) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="4f0fb-215">`AllowAnyOrigin` 是不安全的，因为 *任何网站* 都可以向应用程序发出跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="4f0fb-216">指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的配置，并可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="4f0fb-217">使用这两种方法配置应用时，CORS 服务将返回无效的 CORS 响应。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="4f0fb-218">`AllowAnyOrigin` 影响预检请求和 `Access-Control-Allow-Origin` 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="4f0fb-219">有关详细信息，请参阅 [预检请求](#preflight-requests) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="4f0fb-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：将 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 策略的属性设置为一个函数，当计算是否允许源时，此函数允许源匹配已配置的通配符域。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="4f0fb-221">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="4f0fb-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="4f0fb-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="4f0fb-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="4f0fb-223">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="4f0fb-224">影响预检请求和 `Access-Control-Allow-Methods` 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="4f0fb-225">有关详细信息，请参阅 [预检请求](#preflight-requests) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="4f0fb-226">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="4f0fb-226">Set the allowed request headers</span></span>

<span data-ttu-id="4f0fb-227">若要允许在 CORS 请求中发送特定标头（称为 [作者请求标头](https://xhr.spec.whatwg.org/#request)），请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="4f0fb-228">若要允许所有 [作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="4f0fb-229">`AllowAnyHeader` 影响预检请求和 [访问控制请求标](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) 头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="4f0fb-230">有关详细信息，请参阅 [预检请求](#preflight-requests) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="4f0fb-231">`WithHeaders`仅当发送的标头 `Access-Control-Request-Headers` 与中所述的标头完全匹配时，才可以使用 CORS 中间件策略匹配指定的特定标头 `WithHeaders` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="4f0fb-232">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="4f0fb-233">CORS 中间件使用以下请求标头拒绝预检请求，因为 `Content-Language`) 中未列出 ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage) `WithHeaders` ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="4f0fb-234">应用返回 *200 OK* 响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="4f0fb-235">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="4f0fb-236">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="4f0fb-236">Set the exposed response headers</span></span>

<span data-ttu-id="4f0fb-237">默认情况下，浏览器不会向应用程序公开所有的响应标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="4f0fb-238">有关详细信息，请参阅 [W3C 跨域资源共享 (术语) ：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="4f0fb-239">默认情况下可用的响应标头包括：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="4f0fb-240">CORS 规范将这些标头称为 *简单的响应标头*。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-240">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="4f0fb-241">若要使其他标头可用于应用程序，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="4f0fb-242">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="4f0fb-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="4f0fb-243">凭据需要在 CORS 请求中进行特殊处理。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="4f0fb-244">默认情况下，浏览器不会使用跨域请求发送凭据。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="4f0fb-245">凭据包括 cookie s 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="4f0fb-246">若要使用跨域请求发送凭据，客户端必须设置 `XMLHttpRequest.withCredentials` 为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="4f0fb-247">`XMLHttpRequest`直接使用：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="4f0fb-248">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="4f0fb-249">使用 [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="4f0fb-250">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-250">The server must allow the credentials.</span></span> <span data-ttu-id="4f0fb-251">若要允许跨域凭据，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="4f0fb-252">HTTP 响应包含一个 `Access-Control-Allow-Credentials` 标头，通知浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="4f0fb-253">如果浏览器发送凭据，但响应不包含有效的 `Access-Control-Allow-Credentials` 标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="4f0fb-254">允许跨域凭据会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="4f0fb-255">另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="4f0fb-256">CORS 规范还指出， `"*"` 如果 `Access-Control-Allow-Credentials` 标头存在，则 (所有源) 的设置源无效。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="4f0fb-257">预检请求</span><span class="sxs-lookup"><span data-stu-id="4f0fb-257">Preflight requests</span></span>

<span data-ttu-id="4f0fb-258">对于某些 CORS 请求，浏览器会在发出实际请求之前发送额外的 [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="4f0fb-259">此请求称为 [预检请求](https://developer.mozilla.org/docs/Glossary/Preflight_request)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="4f0fb-260">如果满足以下 **所有** 条件，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="4f0fb-261">请求方法为 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="4f0fb-262">应用不会设置、、、或以外的请求标头 `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="4f0fb-263">`Content-Type`标头（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="4f0fb-264">为客户端请求设置的请求标头上的规则适用于应用通过在对象上调用来设置的标头 `setRequestHeader` `XMLHttpRequest` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="4f0fb-265">CORS 规范调用这些标头 [作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="4f0fb-266">此规则不适用于浏览器可以设置的标头，如 `User-Agent` 、 `Host` 或 `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="4f0fb-267">下面是一个示例响应，它类似于在本文档的 "[测试 CORS](#testc) " 部分中通过 " **Put test** " 按钮发出的预检请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="4f0fb-268">预检请求使用 [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) 方法。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="4f0fb-269">它可能包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-269">It may include the following headers:</span></span>

* <span data-ttu-id="4f0fb-270">[访问控制-请求-方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="4f0fb-271">[访问控制-请求标头](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="4f0fb-272">如前文所述，这不包含浏览器设置的标头，如 `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="4f0fb-273">访问控制-允许-方法</span><span class="sxs-lookup"><span data-stu-id="4f0fb-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="4f0fb-274">如果预检请求被拒绝，应用将返回响应， `200 OK` 但不会设置 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="4f0fb-275">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="4f0fb-276">有关拒绝的预检请求的示例，请参阅本文档的 [测试 CORS](#testc) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="4f0fb-277">使用 F12 工具时，控制台应用会显示类似于以下内容之一的错误，具体取决于浏览器：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="4f0fb-278">Firefox：跨源请求被阻止：相同的源策略不允许读取上的远程资源 `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="4f0fb-279"> (原因： CORS 请求未成功) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="4f0fb-280">了解详细信息</span><span class="sxs-lookup"><span data-stu-id="4f0fb-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="4f0fb-281">基于 Chromium：派生自源 "" 的 "" 访问权限已被 https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net CORS 策略阻止：响应预检请求未通过访问控制检查：请求的资源上没有 "访问控制-允许-源" 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="4f0fb-282">如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="4f0fb-283">若要允许特定标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="4f0fb-284">若要允许所有 [作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="4f0fb-285">浏览器的设置方式并不一致 `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="4f0fb-286">如果：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-286">If either:</span></span>

* <span data-ttu-id="4f0fb-287">标头设置为以外的任何内容 `"*"`</span><span class="sxs-lookup"><span data-stu-id="4f0fb-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="4f0fb-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> 调用：至少包含 `Accept` 、 `Content-Type` 和 `Origin` ，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="4f0fb-289">自动预检请求代码</span><span class="sxs-lookup"><span data-stu-id="4f0fb-289">Automatic preflight request code</span></span>

<span data-ttu-id="4f0fb-290">应用 CORS 策略的时间：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="4f0fb-291">通过 `app.UseCors` 在中调用 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="4f0fb-292">使用 `[EnableCors]` 特性。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="4f0fb-293">ASP.NET Core 对 "预检选项" 请求做出响应。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="4f0fb-294">目前使用每个终结点启用 CORS 不 `RequireCors` 支持自动预检请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="4f0fb-295">本文档的 " [测试 CORS](#testc) " 部分说明了此行为。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="4f0fb-296">用于预检请求的 [HttpOptions] 属性</span><span class="sxs-lookup"><span data-stu-id="4f0fb-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="4f0fb-297">如果为 CORS 启用了适当的策略，ASP.NET Core 通常会自动响应 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="4f0fb-298">在某些情况下，可能不会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="4f0fb-299">例如，将 [CORS 用于终结点路由](#ecors)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="4f0fb-300">下面的代码使用 [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) 特性为 OPTIONS 请求创建终结点：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="4f0fb-301">有关测试上述代码的说明，请参阅 [通过终结点路由测试 CORS 和 [HttpOptions]](#tcer) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="4f0fb-302">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="4f0fb-302">Set the preflight expiration time</span></span>

<span data-ttu-id="4f0fb-303">`Access-Control-Max-Age`标头指定可缓存对预检请求的响应的时间长度。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="4f0fb-304">若要设置此标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="4f0fb-305">CORS 如何工作</span><span class="sxs-lookup"><span data-stu-id="4f0fb-305">How CORS works</span></span>

<span data-ttu-id="4f0fb-306">本部分介绍 HTTP 消息级别的 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="4f0fb-307">CORS **不** 是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="4f0fb-308">CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="4f0fb-309">例如，恶意执行组件可能对站点使用 [跨站点脚本 (XSS) ](xref:security/cross-site-scripting) ，并向启用了 CORS 的站点执行跨站点请求来窃取信息。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="4f0fb-310">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="4f0fb-311">它由客户端 (浏览器) 来强制执行 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="4f0fb-312">服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="4f0fb-313">例如，以下任何工具都将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="4f0fb-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="4f0fb-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="4f0fb-315">Postman</span><span class="sxs-lookup"><span data-stu-id="4f0fb-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="4f0fb-316">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="4f0fb-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="4f0fb-317">Web 浏览器，方法是在地址栏中输入 URL。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="4f0fb-318">这是一种方法，使服务器能够允许浏览器执行跨源 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 或 [获取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 请求，否则将被禁止。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="4f0fb-319">没有 CORS 的浏览器不能执行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="4f0fb-320">在 CORS 之前，使用 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 来绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="4f0fb-321">JSONP 不使用 XHR，而是使用 `<script>` 标记接收响应。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="4f0fb-322">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="4f0fb-323">[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="4f0fb-324">如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="4f0fb-325">若要启用 CORS，无需自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="4f0fb-326">部署的[示例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)上的 " [PUT 测试" 按钮](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="4f0fb-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="4f0fb-327">下面是一个从 " [值](https://cors3.azurewebsites.net/) " 测试按钮到的跨源请求的示例 `https://cors1.azurewebsites.net/api/values` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="4f0fb-328">`Origin`标头：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-328">The `Origin` header:</span></span>

* <span data-ttu-id="4f0fb-329">提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="4f0fb-330">是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="4f0fb-331">**常规标头**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="4f0fb-332">**响应标头**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="4f0fb-333">**请求标头**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="4f0fb-334">在 `OPTIONS` 请求中，服务器设置响应中的 **响应标头** `Access-Control-Allow-Origin: {allowed origin}` 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="4f0fb-335">例如，已部署的 [示例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` 请求包含以下标头：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="4f0fb-336">**常规标头**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="4f0fb-337">**响应标头**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="4f0fb-338">**请求标头**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="4f0fb-339">在前面的 **响应标头** 中，服务器设置响应中的 [访问控制允许源](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-339">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="4f0fb-340">`https://cors1.azurewebsites.net`此标头的值与 `Origin` 请求中的标头相匹配。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="4f0fb-341">如果 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> 调用了，则将 `Access-Control-Allow-Origin: *` 返回通配符值。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="4f0fb-342">`AllowAnyOrigin` 允许任何源。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="4f0fb-343">如果响应不包含 `Access-Control-Allow-Origin` 标头，则跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="4f0fb-344">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="4f0fb-345">即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="4f0fb-346">显示选项请求</span><span class="sxs-lookup"><span data-stu-id="4f0fb-346">Display OPTIONS requests</span></span>

<span data-ttu-id="4f0fb-347">默认情况下，Chrome 和 Edge 浏览器不会在 F12 工具的 "网络" 选项卡上显示 "请求" 选项。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="4f0fb-348">若要在这些浏览器中显示选项请求：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="4f0fb-349">`chrome://flags/#out-of-blink-cors` 或 `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="4f0fb-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="4f0fb-350">禁用标志。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-350">disable the flag.</span></span>
* <span data-ttu-id="4f0fb-351">重新启动.</span><span class="sxs-lookup"><span data-stu-id="4f0fb-351">restart.</span></span>

<span data-ttu-id="4f0fb-352">默认情况下，Firefox 显示 "选项请求"。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="4f0fb-353">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-353">CORS in IIS</span></span>

<span data-ttu-id="4f0fb-354">部署到 IIS 时，如果未将服务器配置为允许匿名访问，则必须在 Windows 身份验证之前运行 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="4f0fb-355">若要支持此方案，需要为应用安装和配置 [IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="4f0fb-356">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-356">Test CORS</span></span>

<span data-ttu-id="4f0fb-357">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)包含测试 CORS 的代码。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="4f0fb-358">请参阅[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="4f0fb-359">该示例是一个 API 项目，其中 Razor 添加了页面：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="4f0fb-360">`WithOrigins("https://localhost:<port>");` 应仅用于测试示例应用程序，类似于 [下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="4f0fb-361">下面 `ValuesController` 提供用于测试的终结点：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="4f0fb-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) 由 [Rick.DocRouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 包提供，并显示路由信息。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="4f0fb-363">使用以下方法之一测试前面的示例代码：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="4f0fb-364">使用部署的示例应用 [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="4f0fb-365">无需下载示例。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="4f0fb-366">`dotnet run`使用的默认 URL 运行示例 `https://localhost:5001` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="4f0fb-367">从 Visual Studio 运行示例，并将端口设置为44398，查找的 URL `https://localhost:44398` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="4f0fb-368">使用带有 F12 工具的浏览器：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="4f0fb-369">选择 " **值** " 按钮，然后查看 " **网络** " 选项卡中的标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="4f0fb-370">选择 " **放置测试** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-370">Select the **PUT test** button.</span></span> <span data-ttu-id="4f0fb-371">请参阅 [显示选项请求](#options) ，以获取有关显示选项请求的说明。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="4f0fb-372">**PUT 测试** 创建两个请求：一个选项预检请求和 PUT 请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="4f0fb-373">选择此 **`GetValues2 [DisableCors]`** 按钮可触发失败的 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="4f0fb-374">如文档中所述，响应返回200成功，但不进行 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="4f0fb-375">选择 " **控制台** " 选项卡以查看 CORS 错误。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="4f0fb-376">根据浏览器，将显示类似于以下内容的错误：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="4f0fb-377">`'https://cors1.azurewebsites.net/api/values/GetValues2'`CORS 策略已阻止从原始位置获取的访问权限 `'https://cors3.azurewebsites.net'` ：请求的资源上没有 "访问控制-允许" 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="4f0fb-378">如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="4f0fb-379">可以使用[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)等工具来测试启用了[CORS 的终结](https://curl.haxx.se/)点。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="4f0fb-380">使用工具时，标头指定的请求源 `Origin` 必须与接收请求的主机不同。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="4f0fb-381">如果请求不是基于标头值 *跨* 域的，则 `Origin` ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="4f0fb-382">不需要 CORS 中间件来处理请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="4f0fb-383">不会在响应中返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="4f0fb-384">以下命令使用 `curl` 发出带有以下信息的选项请求：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="4f0fb-385">通过终结点路由和 [HttpOptions] 测试 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="4f0fb-386">目前使用每个终结点启用 CORS 不 `RequireCors` 支持[自动预检请求](#apf)。 </span><span class="sxs-lookup"><span data-stu-id="4f0fb-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="4f0fb-387">请考虑以下代码，它使用 [终结点路由启用 CORS](#ecors)：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="4f0fb-388">下面 `TodoItems1Controller` 提供用于测试的终结点：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="4f0fb-389">从已部署[示例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI)的[测试页](https://cors1.azurewebsites.net/test?number=1)测试前面的代码。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="4f0fb-390">**Delete [EnableCors]** 和 **GET [EnableCors]** 按钮成功，因为终结点具有 `[EnableCors]` 和响应预检请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="4f0fb-391">其他终结点失败。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-391">The other endpoints fails.</span></span> <span data-ttu-id="4f0fb-392">" **获取** " 按钮失败，因为 [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) 发送：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="4f0fb-393">下面 `TodoItems2Controller` 提供了类似的终结点，但包含响应选项请求的显式代码：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="4f0fb-394">从已部署示例的 [测试页](https://cors1.azurewebsites.net/test?number=2) 测试前面的代码。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="4f0fb-395">在 " **控制器** " 下拉列表中，选择 " **预检** "，然后 **设置 "控制器**"。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="4f0fb-396">对终结点的所有 CORS 调用都将 `TodoItems2Controller` 成功。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f0fb-397">其他资源</span><span class="sxs-lookup"><span data-stu-id="4f0fb-397">Additional resources</span></span>

* [<span data-ttu-id="4f0fb-398"> (CORS) 跨域资源共享 </span><span class="sxs-lookup"><span data-stu-id="4f0fb-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="4f0fb-399">IIS CORS 模块入门</span><span class="sxs-lookup"><span data-stu-id="4f0fb-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4f0fb-400">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4f0fb-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4f0fb-401">本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="4f0fb-402">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="4f0fb-403">此限制称为同域策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-403">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="4f0fb-404">同域策略可防止恶意站点从另一站点读取敏感数据。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="4f0fb-405">有时，你可能想要允许其他站点对你的应用进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="4f0fb-406">有关详细信息，请参阅 [MOZILLA CORS 一文](https://developer.mozilla.org/docs/Web/HTTP/CORS)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="4f0fb-407">[跨源资源共享](https://www.w3.org/TR/cors/) (CORS) ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="4f0fb-408">是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="4f0fb-409">**不** 是一项安全功能，CORS 放宽 security。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="4f0fb-410">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="4f0fb-411">有关详细信息，请参阅 [CORS 的工作](#how-cors)原理。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="4f0fb-412">允许服务器明确允许一些跨源请求，同时拒绝其他请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="4f0fb-413">比早期的技术（如 [JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="4f0fb-414">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4f0fb-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="4f0fb-415">同一原点</span><span class="sxs-lookup"><span data-stu-id="4f0fb-415">Same origin</span></span>

<span data-ttu-id="4f0fb-416">如果两个 Url 具有相同的方案、主机和端口 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，则它们具有相同的源。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="4f0fb-417">这两个 Url 具有相同的源：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="4f0fb-418">这些 Url 的起源不同于前两个 Url：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="4f0fb-419">`https://example.net`：不同的域</span><span class="sxs-lookup"><span data-stu-id="4f0fb-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="4f0fb-420">`https://www.example.com/foo.html`：不同的子域</span><span class="sxs-lookup"><span data-stu-id="4f0fb-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="4f0fb-421">`http://example.com/foo.html`：不同方案</span><span class="sxs-lookup"><span data-stu-id="4f0fb-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="4f0fb-422">`https://example.com:9000/foo.html`：不同的端口</span><span class="sxs-lookup"><span data-stu-id="4f0fb-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="4f0fb-423">比较来源时，Internet Explorer 不会考虑该端口。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="4f0fb-424">具有命名策略和中间件的 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="4f0fb-425">CORS 中间件处理跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="4f0fb-426">以下代码通过指定源为整个应用启用 CORS：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="4f0fb-427">上述代码：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-427">The preceding code:</span></span>

* <span data-ttu-id="4f0fb-428">将策略名称设置为 " \_ myAllowSpecificOrigins"。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="4f0fb-429">策略名称为任意名称。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="4f0fb-430">调用 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 扩展方法，该方法启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="4f0fb-431"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>使用[lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="4f0fb-432">Lambda 采用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 对象。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="4f0fb-433">本文稍后将介绍[配置选项](#cors-policy-options)，如 `WithOrigins` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="4f0fb-434"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>方法调用将 CORS 服务添加到应用的服务容器：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="4f0fb-435">有关详细信息，请参阅本文档中的 [CORS 策略选项](#cpo) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="4f0fb-436"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>方法可以链接方法，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="4f0fb-437">注意： URL **不** 能包含尾随斜杠 (`/`) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="4f0fb-438">如果 URL 以结尾 `/` ，则比较返回， `false` 不返回任何标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="4f0fb-439">以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="4f0fb-440">注意： `UseCors` 必须先调用 `UseMvc` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="4f0fb-441">请参阅 [在页面 Razor 、控制器和操作方法中启用 cors，](#ecors) 以在页面/控制器/操作级别应用 cors 策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="4f0fb-442">有关测试代码的说明，请参阅 [测试 CORS](#test) ，如以上代码所示。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="4f0fb-443">使用属性启用 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-443">Enable CORS with attributes</span></span>

<span data-ttu-id="4f0fb-444">[ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了一种用于全局应用 CORS 的替代方法。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="4f0fb-445">`[EnableCors]`属性为所选终结点启用 CORS，而不是所有终结点。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="4f0fb-446">使用 `[EnableCors]` 指定默认策略并 `[EnableCors("{Policy String}")]` 指定策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="4f0fb-447">`[EnableCors]`特性可应用于：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="4f0fb-448">Razor 分页 `PageModel`</span><span class="sxs-lookup"><span data-stu-id="4f0fb-448">Razor Page `PageModel`</span></span>
* <span data-ttu-id="4f0fb-449">控制器</span><span class="sxs-lookup"><span data-stu-id="4f0fb-449">Controller</span></span>
* <span data-ttu-id="4f0fb-450">控制器操作方法</span><span class="sxs-lookup"><span data-stu-id="4f0fb-450">Controller action method</span></span>

<span data-ttu-id="4f0fb-451">您可以使用属性将不同的策略应用到控制器/页模型/操作  `[EnableCors]` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="4f0fb-452">如果将 `[EnableCors]` 属性应用于控制器/页面模型/操作方法，并在中间件中启用了 CORS，则会应用 **这两种** 策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="4f0fb-453">建议 **不要** 合并策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="4f0fb-454">使用 `[EnableCors]` 特性或中间件， **不能同时使用两者**。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-454">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="4f0fb-455">使用时 `[EnableCors]` ，请 **不要** 定义默认策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="4f0fb-456">下面的代码将不同的策略应用于每个方法：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="4f0fb-457">以下代码创建 CORS 默认策略和名为的策略 `"AnotherPolicy"` ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="4f0fb-458">禁用 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-458">Disable CORS</span></span>

<span data-ttu-id="4f0fb-459">[ &lbrack; DisableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性为控制器/页模型/操作禁用 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="4f0fb-460">CORS 策略选项</span><span class="sxs-lookup"><span data-stu-id="4f0fb-460">CORS policy options</span></span>

<span data-ttu-id="4f0fb-461">本部分介绍可在 CORS 策略中设置的各种选项：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="4f0fb-462">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="4f0fb-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="4f0fb-463">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="4f0fb-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="4f0fb-464">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="4f0fb-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="4f0fb-465">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="4f0fb-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="4f0fb-466">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="4f0fb-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="4f0fb-467">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="4f0fb-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="4f0fb-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在中调用 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4f0fb-469">对于某些选项，最好先阅读 [CORS 如何工作](#how-cors) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="4f0fb-470">设置允许的来源</span><span class="sxs-lookup"><span data-stu-id="4f0fb-470">Set the allowed origins</span></span>

<span data-ttu-id="4f0fb-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>：允许所有来源的 CORS 请求与任何方案 (`http` 或 `https`) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="4f0fb-472">`AllowAnyOrigin` 是不安全的，因为 *任何网站* 都可以向应用程序发出跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="4f0fb-473">指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的配置，并可能导致跨站点请求伪造。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="4f0fb-474">对于安全应用，如果客户端必须授权自身访问服务器资源，请指定准确的来源列表。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="4f0fb-475">`AllowAnyOrigin` 影响预检请求和 `Access-Control-Allow-Origin` 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="4f0fb-476">有关详细信息，请参阅 [预检请求](#preflight-requests) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="4f0fb-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>：将 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 策略的属性设置为一个函数，当计算是否允许源时，此函数允许源匹配已配置的通配符域。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="4f0fb-478">设置允许的 HTTP 方法</span><span class="sxs-lookup"><span data-stu-id="4f0fb-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="4f0fb-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="4f0fb-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="4f0fb-480">允许任何 HTTP 方法：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="4f0fb-481">影响预检请求和 `Access-Control-Allow-Methods` 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="4f0fb-482">有关详细信息，请参阅 [预检请求](#preflight-requests) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="4f0fb-483">设置允许的请求标头</span><span class="sxs-lookup"><span data-stu-id="4f0fb-483">Set the allowed request headers</span></span>

<span data-ttu-id="4f0fb-484">若要允许在 CORS 请求中发送特定标头（称为 *作者请求标头*），请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 并指定允许的标头：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-484">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="4f0fb-485">若要允许所有作者请求标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="4f0fb-486">此设置会影响预检请求和 `Access-Control-Request-Headers` 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="4f0fb-487">有关详细信息，请参阅 [预检请求](#preflight-requests) 部分。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="4f0fb-488">CORS 中间件始终允许发送四个标头， `Access-Control-Request-Headers` 而不考虑在 CorsPolicy 中配置的值。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="4f0fb-489">此标头列表包括：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="4f0fb-490">例如，考虑按如下方式配置的应用：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="4f0fb-491">CORS 中间件使用以下请求标头成功响应到预检请求，因为 `Content-Language` 始终允许：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="4f0fb-492">设置公开的响应标头</span><span class="sxs-lookup"><span data-stu-id="4f0fb-492">Set the exposed response headers</span></span>

<span data-ttu-id="4f0fb-493">默认情况下，浏览器不会向应用程序公开所有的响应标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="4f0fb-494">有关详细信息，请参阅 [W3C 跨域资源共享 (术语) ：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="4f0fb-495">默认情况下可用的响应标头包括：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="4f0fb-496">CORS 规范将这些标头称为 *简单的响应标头*。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-496">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="4f0fb-497">若要使其他标头可用于应用程序，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="4f0fb-498">跨域请求中的凭据</span><span class="sxs-lookup"><span data-stu-id="4f0fb-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="4f0fb-499">凭据需要在 CORS 请求中进行特殊处理。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="4f0fb-500">默认情况下，浏览器不会使用跨域请求发送凭据。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="4f0fb-501">凭据包括 cookie s 和 HTTP 身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="4f0fb-502">若要使用跨域请求发送凭据，客户端必须设置 `XMLHttpRequest.withCredentials` 为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="4f0fb-503">`XMLHttpRequest`直接使用：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="4f0fb-504">使用 jQuery：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="4f0fb-505">使用 [提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="4f0fb-506">服务器必须允许凭据。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-506">The server must allow the credentials.</span></span> <span data-ttu-id="4f0fb-507">若要允许跨域凭据，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="4f0fb-508">HTTP 响应包含一个 `Access-Control-Allow-Credentials` 标头，通知浏览器服务器允许跨源请求的凭据。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="4f0fb-509">如果浏览器发送凭据，但响应不包含有效的 `Access-Control-Allow-Credentials` 标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="4f0fb-510">允许跨域凭据会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="4f0fb-511">另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="4f0fb-512">CORS 规范还指出， `"*"` 如果 `Access-Control-Allow-Credentials` 标头存在，则 (所有源) 的设置源无效。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="4f0fb-513">预检请求</span><span class="sxs-lookup"><span data-stu-id="4f0fb-513">Preflight requests</span></span>

<span data-ttu-id="4f0fb-514">对于某些 CORS 请求，浏览器会在发出实际请求之前发送其他请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="4f0fb-515">此请求称为 *预检请求*。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-515">This request is called a *preflight request*.</span></span> <span data-ttu-id="4f0fb-516">如果满足以下条件，浏览器可以跳过预检请求：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="4f0fb-517">请求方法为 GET、HEAD 或 POST。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="4f0fb-518">应用不会设置、、、或以外的请求标头 `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="4f0fb-519">`Content-Type`标头（如果已设置）具有以下值之一：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="4f0fb-520">为客户端请求设置的请求标头上的规则适用于应用通过在对象上调用来设置的标头 `setRequestHeader` `XMLHttpRequest` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="4f0fb-521">CORS 规范调用这些标头 *作者请求标头*。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-521">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="4f0fb-522">此规则不适用于浏览器可以设置的标头，如 `User-Agent` 、 `Host` 或 `Content-Length` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="4f0fb-523">下面是预检请求的示例：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="4f0fb-524">预航班请求使用 HTTP OPTIONS 方法。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="4f0fb-525">它包括两个特殊标头：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-525">It includes two special headers:</span></span>

* <span data-ttu-id="4f0fb-526">`Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="4f0fb-527">`Access-Control-Request-Headers`：应用在实际请求上设置的请求标头的列表。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="4f0fb-528">如前文所述，这不包含浏览器设置的标头，如 `User-Agent` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="4f0fb-529">如果为 CORS 启用了适当的策略，ASP.NET Core 一般会自动响应 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="4f0fb-530">请参阅 [[HttpOptions] 属性以获取预检请求](#pro)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="4f0fb-531">CORS 预检请求可能包含一个 `Access-Control-Request-Headers` 标头，该标头向服务器指示与实际请求一起发送的标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="4f0fb-532">若要允许特定标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="4f0fb-533">若要允许所有作者请求标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="4f0fb-534">浏览器在设置方式上并不完全一致 `Access-Control-Request-Headers` 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="4f0fb-535">如果将标头设置为 (以外的任何内容 `"*"` 或使用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>) ，则应至少包含 `Accept` 、 `Content-Type` 、和 `Origin` ，以及要支持的任何自定义标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="4f0fb-536">下面是 (假定服务器允许) 请求的预检请求的示例响应：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="4f0fb-537">响应包含一个 `Access-Control-Allow-Methods` 标头，其中列出了允许的方法，还可以选择 `Access-Control-Allow-Headers` 标头，其中列出了允许的标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="4f0fb-538">如果预检请求成功，则浏览器发送实际请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="4f0fb-539">如果预检请求被拒绝，应用将返回 *200 OK* 响应，但不会向后发送 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="4f0fb-540">因此，浏览器不会尝试跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="4f0fb-541">设置预检过期时间</span><span class="sxs-lookup"><span data-stu-id="4f0fb-541">Set the preflight expiration time</span></span>

<span data-ttu-id="4f0fb-542">`Access-Control-Max-Age`标头指定可缓存对预检请求的响应的时间长度。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="4f0fb-543">若要设置此标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="4f0fb-544">CORS 如何工作</span><span class="sxs-lookup"><span data-stu-id="4f0fb-544">How CORS works</span></span>

<span data-ttu-id="4f0fb-545">本部分介绍 HTTP 消息级别的 [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) 请求中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="4f0fb-546">CORS **不** 是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="4f0fb-547">CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="4f0fb-548">例如，恶意执行组件可能会对站点使用 [阻止跨站点脚本 (XSS) ](xref:security/cross-site-scripting) ，并向启用了 CORS 的站点执行跨站点请求来窃取信息。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="4f0fb-549">API 不能通过允许 CORS 来更安全。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="4f0fb-550">它由客户端 (浏览器) 来强制执行 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="4f0fb-551">服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="4f0fb-552">例如，以下任何工具都将显示服务器响应：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="4f0fb-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="4f0fb-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="4f0fb-554">Postman</span><span class="sxs-lookup"><span data-stu-id="4f0fb-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="4f0fb-555">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="4f0fb-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="4f0fb-556">Web 浏览器，方法是在地址栏中输入 URL。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="4f0fb-557">这是一种方法，使服务器能够允许浏览器执行跨源 [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) 或 [获取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 请求，否则将被禁止。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="4f0fb-558">没有 CORS) 的浏览器 (无法进行跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="4f0fb-559">在 CORS 之前，使用 [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) 来绕过此限制。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="4f0fb-560">JSONP 不使用 XHR，而是使用 `<script>` 标记接收响应。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="4f0fb-561">允许跨源加载脚本。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="4f0fb-562">[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="4f0fb-563">如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="4f0fb-564">若要启用 CORS，无需自定义 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="4f0fb-565">下面是一个跨源请求的示例。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="4f0fb-566">`Origin`标头提供发出请求的站点的域。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="4f0fb-567">`Origin`标头是必需的，并且必须与主机不同。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="4f0fb-568">如果服务器允许该请求，则会 `Access-Control-Allow-Origin` 在响应中设置标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="4f0fb-569">此标头的值可以与 `Origin` 请求中的标头相匹配，也可以是通配符值 `"*"` ，这意味着允许任何源：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="4f0fb-570">如果响应不包含 `Access-Control-Allow-Origin` 标头，则跨域请求会失败。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="4f0fb-571">具体而言，浏览器不允许该请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="4f0fb-572">即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="4f0fb-573">测试 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-573">Test CORS</span></span>

<span data-ttu-id="4f0fb-574">测试 CORS：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-574">To test CORS:</span></span>

1. <span data-ttu-id="4f0fb-575">[创建 API 项目](xref:tutorials/first-web-api)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="4f0fb-576">或者，您也可以 [下载该示例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="4f0fb-577">使用本文档中的方法之一启用 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="4f0fb-578">例如：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="4f0fb-579">`WithOrigins("https://localhost:<port>");` 应仅用于测试示例应用程序，类似于 [下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="4f0fb-580"> (Razor 页面或 MVC) 创建 web 应用项目。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="4f0fb-581">该示例使用 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="4f0fb-582">可以在与 API 项目相同的解决方案中创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="4f0fb-583">将以下突出显示的代码添加到 *索引 cshtml* 文件中：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="4f0fb-584">在上面的代码中，将替换 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 为已部署应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="4f0fb-585">部署 API 项目。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-585">Deploy the API project.</span></span> <span data-ttu-id="4f0fb-586">例如， [部署到 Azure](xref:host-and-deploy/azure-apps/index)。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="4f0fb-587">Razor从桌面运行页面或 MVC 应用，并单击 "**测试**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="4f0fb-588">使用 F12 工具查看错误消息。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="4f0fb-589">从中删除 localhost 源 `WithOrigins` 并部署应用。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="4f0fb-590">或者，使用其他端口运行客户端应用。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="4f0fb-591">例如，在 Visual Studio 中运行。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="4f0fb-592">与客户端应用程序进行测试。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-592">Test with the client app.</span></span> <span data-ttu-id="4f0fb-593">CORS 故障返回一个错误，但错误消息不能用于 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="4f0fb-594">使用 F12 工具中的 "控制台" 选项卡查看错误。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="4f0fb-595">根据浏览器，你会在 "F12 工具" 控制台 (出现错误，如下所示) ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="4f0fb-596">使用 Microsoft Edge：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="4f0fb-597">**SEC7120： [CORS] 源在 `https://localhost:44375` `https://localhost:44375` 跨域资源的访问控制允许源响应标头中找不到 `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="4f0fb-598">使用 Chrome：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-598">Using Chrome:</span></span>

     <span data-ttu-id="4f0fb-599">**`https://webapi.azurewebsites.net/api/values/1`CORS 策略已阻止从原始位置访问 XMLHttpRequest `https://localhost:44375` ：请求的资源上没有 "访问控制-允许" 标头。**</span><span class="sxs-lookup"><span data-stu-id="4f0fb-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="4f0fb-600">可以使用工具（如 [Fiddler](https://www.telerik.com/fiddler) 或 [Postman](https://www.getpostman.com/)）测试启用 CORS 的终结点。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="4f0fb-601">使用工具时，标头指定的请求源 `Origin` 必须与接收请求的主机不同。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="4f0fb-602">如果请求不是基于标头值 *跨* 域的，则 `Origin` ：</span><span class="sxs-lookup"><span data-stu-id="4f0fb-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="4f0fb-603">不需要 CORS 中间件来处理请求。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="4f0fb-604">不会在响应中返回 CORS 标头。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="4f0fb-605">IIS 中的 CORS</span><span class="sxs-lookup"><span data-stu-id="4f0fb-605">CORS in IIS</span></span>

<span data-ttu-id="4f0fb-606">部署到 IIS 时，如果未将服务器配置为允许匿名访问，则必须在 Windows 身份验证之前运行 CORS。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="4f0fb-607">若要支持此方案，需要为应用安装和配置 [IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module) 。</span><span class="sxs-lookup"><span data-stu-id="4f0fb-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f0fb-608">其他资源</span><span class="sxs-lookup"><span data-stu-id="4f0fb-608">Additional resources</span></span>

* [<span data-ttu-id="4f0fb-609"> (CORS) 跨域资源共享 </span><span class="sxs-lookup"><span data-stu-id="4f0fb-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="4f0fb-610">IIS CORS 模块入门</span><span class="sxs-lookup"><span data-stu-id="4f0fb-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
