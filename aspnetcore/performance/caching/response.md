---
title: ASP.NET Core 中的响应缓存
author: rick-anderson
description: 了解如何通过响应缓存降低带宽要求和提高 ASP.NET Core 应用性能。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
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
uid: performance/caching/response
ms.openlocfilehash: 539ddb118279adb3a53394cdb0c2e5169092ebc0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589226"
---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="5a320-103">ASP.NET Core 中的响应缓存</span><span class="sxs-lookup"><span data-stu-id="5a320-103">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="5a320-104">作者： [John Luo](https://github.com/JunTaoLuo)、 [Rick Anderson](https://twitter.com/RickAndMSFT)和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5a320-104">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5a320-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/response/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5a320-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5a320-106">响应缓存可减少客户端或代理对 web 服务器发出的请求数。</span><span class="sxs-lookup"><span data-stu-id="5a320-106">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="5a320-107">响应缓存还减少了 web 服务器生成响应所需的工作量。</span><span class="sxs-lookup"><span data-stu-id="5a320-107">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="5a320-108">响应缓存由指定你希望客户端、代理和中间件缓存响应的方式的标头控制。</span><span class="sxs-lookup"><span data-stu-id="5a320-108">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="5a320-109">[ResponseCache 属性](#responsecache-attribute)参与设置响应缓存标头。</span><span class="sxs-lookup"><span data-stu-id="5a320-109">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="5a320-110">在 [HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)下，客户端和中间代理应遵循用于缓存响应的标头。</span><span class="sxs-lookup"><span data-stu-id="5a320-110">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="5a320-111">对于遵循 HTTP 1.1 缓存规范的服务器端缓存，请使用 [响应缓存中间件](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="5a320-111">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="5a320-112">中间件可以使用 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 属性来影响服务器端的缓存行为。</span><span class="sxs-lookup"><span data-stu-id="5a320-112">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="5a320-113">基于 HTTP 的响应缓存</span><span class="sxs-lookup"><span data-stu-id="5a320-113">HTTP-based response caching</span></span>

<span data-ttu-id="5a320-114">[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)描述了 Internet 缓存的行为方式。</span><span class="sxs-lookup"><span data-stu-id="5a320-114">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="5a320-115">用于缓存的主 HTTP 标头是 [缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)，它用于指定缓存 *指令*。</span><span class="sxs-lookup"><span data-stu-id="5a320-115">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="5a320-116">指令控制缓存行为作为请求从客户端发送到服务器，而作为响应，使其从服务器到客户端的方式。</span><span class="sxs-lookup"><span data-stu-id="5a320-116">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="5a320-117">请求和响应在代理服务器之间移动，并且代理服务器还必须符合 HTTP 1.1 缓存规范。</span><span class="sxs-lookup"><span data-stu-id="5a320-117">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="5a320-118">`Cache-Control`下表显示了常见的指令。</span><span class="sxs-lookup"><span data-stu-id="5a320-118">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="5a320-119">指令</span><span class="sxs-lookup"><span data-stu-id="5a320-119">Directive</span></span>                                                       | <span data-ttu-id="5a320-120">操作</span><span class="sxs-lookup"><span data-stu-id="5a320-120">Action</span></span> |
| --------------------------------------------------------------- | ------ |
| [<span data-ttu-id="5a320-121">public</span><span class="sxs-lookup"><span data-stu-id="5a320-121">public</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | <span data-ttu-id="5a320-122">缓存可以存储响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-122">A cache may store the response.</span></span> |
| [<span data-ttu-id="5a320-123">private</span><span class="sxs-lookup"><span data-stu-id="5a320-123">private</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | <span data-ttu-id="5a320-124">共享缓存不能存储响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-124">The response must not be stored by a shared cache.</span></span> <span data-ttu-id="5a320-125">专用缓存可以存储和重用响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-125">A private cache may store and reuse the response.</span></span> |
| [<span data-ttu-id="5a320-126">最大期限</span><span class="sxs-lookup"><span data-stu-id="5a320-126">max-age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | <span data-ttu-id="5a320-127">客户端不接受其期限大于指定秒数的响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-127">The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="5a320-128">示例： `max-age=60` (60 秒) ， `max-age=2592000` (1 个月) </span><span class="sxs-lookup"><span data-stu-id="5a320-128">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month)</span></span> |
| [<span data-ttu-id="5a320-129">no-cache</span><span class="sxs-lookup"><span data-stu-id="5a320-129">no-cache</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | <span data-ttu-id="5a320-130">**请求时**：缓存不能使用存储的响应来满足请求。</span><span class="sxs-lookup"><span data-stu-id="5a320-130">**On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="5a320-131">源服务器重新生成客户端的响应，中间件更新其缓存中存储的响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-131">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="5a320-132">**响应：在** 源服务器上没有验证的后续请求不得使用响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-132">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> |
| [<span data-ttu-id="5a320-133">无-商店</span><span class="sxs-lookup"><span data-stu-id="5a320-133">no-store</span></span>](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | <span data-ttu-id="5a320-134">**请求时**：缓存不能存储请求。</span><span class="sxs-lookup"><span data-stu-id="5a320-134">**On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="5a320-135">**响应**：缓存不能存储响应的任何部分。</span><span class="sxs-lookup"><span data-stu-id="5a320-135">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="5a320-136">下表显示了在缓存中扮演角色的其他缓存标头。</span><span class="sxs-lookup"><span data-stu-id="5a320-136">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="5a320-137">标头</span><span class="sxs-lookup"><span data-stu-id="5a320-137">Header</span></span>                                                     | <span data-ttu-id="5a320-138">函数</span><span class="sxs-lookup"><span data-stu-id="5a320-138">Function</span></span> |
| ---------------------------------------------------------- | -------- |
| [<span data-ttu-id="5a320-139">年龄</span><span class="sxs-lookup"><span data-stu-id="5a320-139">Age</span></span>](https://tools.ietf.org/html/rfc7234#section-5.1)     | <span data-ttu-id="5a320-140">在源服务器上生成或成功验证响应以来的时间量（以秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="5a320-140">An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> |
| [<span data-ttu-id="5a320-141">完</span><span class="sxs-lookup"><span data-stu-id="5a320-141">Expires</span></span>](https://tools.ietf.org/html/rfc7234#section-5.3) | <span data-ttu-id="5a320-142">响应被视为过时的时间。</span><span class="sxs-lookup"><span data-stu-id="5a320-142">The time after which the response is considered stale.</span></span> |
| [<span data-ttu-id="5a320-143">杂</span><span class="sxs-lookup"><span data-stu-id="5a320-143">Pragma</span></span>](https://tools.ietf.org/html/rfc7234#section-5.4)  | <span data-ttu-id="5a320-144">存在，以便向后兼容 HTTP/1.0 缓存以设置 `no-cache` 行为。</span><span class="sxs-lookup"><span data-stu-id="5a320-144">Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="5a320-145">如果该 `Cache-Control` 标头存在，则将 `Pragma` 忽略该标头。</span><span class="sxs-lookup"><span data-stu-id="5a320-145">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> |
| [<span data-ttu-id="5a320-146">大</span><span class="sxs-lookup"><span data-stu-id="5a320-146">Vary</span></span>](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | <span data-ttu-id="5a320-147">指定不能发送缓存的响应，除非 `Vary` 缓存响应的原始请求和新请求中的所有标头字段都匹配。</span><span class="sxs-lookup"><span data-stu-id="5a320-147">Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="5a320-148">基于 HTTP 的缓存遵从请求 Cache-Control 指令</span><span class="sxs-lookup"><span data-stu-id="5a320-148">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="5a320-149">[Cache-Control 标头的 HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)要求使用缓存来服从 `Cache-Control` 客户端发送的有效标头。</span><span class="sxs-lookup"><span data-stu-id="5a320-149">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="5a320-150">客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-150">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="5a320-151">`Cache-Control`如果考虑 HTTP 缓存的目标，则始终考虑客户端请求标头是有意义的。</span><span class="sxs-lookup"><span data-stu-id="5a320-151">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="5a320-152">在官方规范下，缓存旨在减少在客户端、代理和服务器网络中满足请求的延迟和网络开销。</span><span class="sxs-lookup"><span data-stu-id="5a320-152">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="5a320-153">它不一定是控制源服务器上的负载的一种方法。</span><span class="sxs-lookup"><span data-stu-id="5a320-153">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="5a320-154">使用 [响应缓存中间件](xref:performance/caching/middleware) 时，无开发人员对此缓存行为的控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="5a320-154">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="5a320-155">[中间件的计划增强功能](https://github.com/dotnet/AspNetCore/issues/2612) 是在 `Cache-Control` 决定为缓存的响应提供服务时，将中间件配置为忽略请求标头的机会。</span><span class="sxs-lookup"><span data-stu-id="5a320-155">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="5a320-156">计划的增强功能提供了一种更好地控制服务器负载的机会。</span><span class="sxs-lookup"><span data-stu-id="5a320-156">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="5a320-157">ASP.NET Core 中的其他缓存技术</span><span class="sxs-lookup"><span data-stu-id="5a320-157">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="5a320-158">内存中缓存</span><span class="sxs-lookup"><span data-stu-id="5a320-158">In-memory caching</span></span>

<span data-ttu-id="5a320-159">内存中缓存使用服务器内存来存储缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="5a320-159">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="5a320-160">这种类型的缓存适用于单个服务器或使用 *粘滞会话* 的多台服务器。</span><span class="sxs-lookup"><span data-stu-id="5a320-160">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="5a320-161">粘滞会话表示来自客户端的请求始终路由到同一服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="5a320-161">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="5a320-162">有关详细信息，请参阅 <xref:performance/caching/memory>。</span><span class="sxs-lookup"><span data-stu-id="5a320-162">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="5a320-163">分布式缓存</span><span class="sxs-lookup"><span data-stu-id="5a320-163">Distributed Cache</span></span>

<span data-ttu-id="5a320-164">当应用程序托管在云或服务器场中时，使用分布式缓存将数据存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="5a320-164">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="5a320-165">缓存在处理请求的服务器之间共享。</span><span class="sxs-lookup"><span data-stu-id="5a320-165">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="5a320-166">如果客户端的缓存数据可用，则客户端可以提交由组中的任何服务器处理的请求。</span><span class="sxs-lookup"><span data-stu-id="5a320-166">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="5a320-167">ASP.NET Core 适用于 SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)和 [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="5a320-167">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="5a320-168">有关详细信息，请参阅 <xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="5a320-168">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="5a320-169">缓存标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="5a320-169">Cache Tag Helper</span></span>

<span data-ttu-id="5a320-170">使用缓存标记帮助程序从 MVC 视图或页面缓存内容 Razor 。</span><span class="sxs-lookup"><span data-stu-id="5a320-170">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="5a320-171">缓存标记帮助程序使用内存中缓存来存储数据。</span><span class="sxs-lookup"><span data-stu-id="5a320-171">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="5a320-172">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="5a320-172">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="5a320-173">分布式缓存标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="5a320-173">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="5a320-174">Razor使用分布式缓存标记帮助程序在分布式云和 web 场方案中，通过 MVC 视图或页面缓存内容。</span><span class="sxs-lookup"><span data-stu-id="5a320-174">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="5a320-175">分布式缓存标记帮助程序使用 SQL Server、 [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)或 [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) 来存储数据。</span><span class="sxs-lookup"><span data-stu-id="5a320-175">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="5a320-176">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="5a320-176">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="5a320-177">ResponseCache 特性</span><span class="sxs-lookup"><span data-stu-id="5a320-177">ResponseCache attribute</span></span>

<span data-ttu-id="5a320-178"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>指定在响应缓存中设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="5a320-178">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="5a320-179">禁用包含经过身份验证的客户端信息的内容的缓存。</span><span class="sxs-lookup"><span data-stu-id="5a320-179">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="5a320-180">只应为不会根据用户身份更改或用户是否已登录的内容启用缓存。</span><span class="sxs-lookup"><span data-stu-id="5a320-180">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="5a320-181"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 根据给定的查询键列表的值，改变存储的响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-181"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="5a320-182">当提供了的单个值时 `*` ，中间件将根据所有请求查询字符串参数来改变响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-182">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="5a320-183">必须启用[响应缓存中间件](xref:performance/caching/middleware)才能设置 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 属性。</span><span class="sxs-lookup"><span data-stu-id="5a320-183">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="5a320-184">否则，会引发运行时异常。</span><span class="sxs-lookup"><span data-stu-id="5a320-184">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="5a320-185">此属性没有相应的 HTTP 标头 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 。</span><span class="sxs-lookup"><span data-stu-id="5a320-185">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="5a320-186">属性是由响应缓存中间件处理的 HTTP 功能。</span><span class="sxs-lookup"><span data-stu-id="5a320-186">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="5a320-187">对于用于缓存响应的中间件，查询字符串和查询字符串值必须与上一个请求匹配。</span><span class="sxs-lookup"><span data-stu-id="5a320-187">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="5a320-188">例如，请考虑下表中显示的请求和结果的顺序。</span><span class="sxs-lookup"><span data-stu-id="5a320-188">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="5a320-189">请求</span><span class="sxs-lookup"><span data-stu-id="5a320-189">Request</span></span>                          | <span data-ttu-id="5a320-190">结果</span><span class="sxs-lookup"><span data-stu-id="5a320-190">Result</span></span>                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | <span data-ttu-id="5a320-191">从服务器返回的。</span><span class="sxs-lookup"><span data-stu-id="5a320-191">Returned from the server.</span></span> |
| `http://example.com?key1=value1` | <span data-ttu-id="5a320-192">从中间件返回。</span><span class="sxs-lookup"><span data-stu-id="5a320-192">Returned from middleware.</span></span> |
| `http://example.com?key1=value2` | <span data-ttu-id="5a320-193">从服务器返回的。</span><span class="sxs-lookup"><span data-stu-id="5a320-193">Returned from the server.</span></span> |

<span data-ttu-id="5a320-194">第一个请求由服务器返回，并缓存在中间件中。</span><span class="sxs-lookup"><span data-stu-id="5a320-194">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="5a320-195">第二个请求是由中间件返回的，因为查询字符串与上一个请求匹配。</span><span class="sxs-lookup"><span data-stu-id="5a320-195">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="5a320-196">第三个请求不在中间件缓存中，因为查询字符串值与以前的请求不匹配。</span><span class="sxs-lookup"><span data-stu-id="5a320-196">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="5a320-197"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>用于通过)  (配置和创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-197">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="5a320-198">`ResponseCacheFilter`执行更新相应 HTTP 标头和响应功能的工作。</span><span class="sxs-lookup"><span data-stu-id="5a320-198">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="5a320-199">筛选器：</span><span class="sxs-lookup"><span data-stu-id="5a320-199">The filter:</span></span>

* <span data-ttu-id="5a320-200">删除、和的任何现有标头 `Vary` `Cache-Control` `Pragma` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-200">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="5a320-201">根据中设置的属性写出适当的标头 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="5a320-201">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="5a320-202">如果设置了，则更新响应缓存 HTTP 功能 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 。</span><span class="sxs-lookup"><span data-stu-id="5a320-202">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="5a320-203">大</span><span class="sxs-lookup"><span data-stu-id="5a320-203">Vary</span></span>

<span data-ttu-id="5a320-204">仅当设置了属性时，才写入此标头 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 。</span><span class="sxs-lookup"><span data-stu-id="5a320-204">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="5a320-205">属性设置为 `Vary` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="5a320-205">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="5a320-206">下面的示例使用 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 属性：</span><span class="sxs-lookup"><span data-stu-id="5a320-206">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="5a320-207">使用示例应用程序，使用浏览器的网络工具查看响应标头。</span><span class="sxs-lookup"><span data-stu-id="5a320-207">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="5a320-208">以下响应标头随 Cache1 页响应一起发送：</span><span class="sxs-lookup"><span data-stu-id="5a320-208">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="5a320-209">NoStore 和 Location。 None</span><span class="sxs-lookup"><span data-stu-id="5a320-209">NoStore and Location.None</span></span>

<span data-ttu-id="5a320-210"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 覆盖其他大多数属性。</span><span class="sxs-lookup"><span data-stu-id="5a320-210"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="5a320-211">当此属性设置为时 `true` ， `Cache-Control` 标头将设置为 `no-store` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-211">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="5a320-212">如果 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 设置为 `None` ：</span><span class="sxs-lookup"><span data-stu-id="5a320-212">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="5a320-213">将 `Cache-Control` 设置为 `no-store,no-cache`。</span><span class="sxs-lookup"><span data-stu-id="5a320-213">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="5a320-214">将 `Pragma` 设置为 `no-cache`。</span><span class="sxs-lookup"><span data-stu-id="5a320-214">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="5a320-215">如果 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 为 `false` 且 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 为 `None` ，则 `Cache-Control` 和 `Pragma` 均设置为 `no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-215">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="5a320-216"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 对于错误页，通常设置为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-216"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="5a320-217">示例应用中的 Cache2 页面生成响应标头，以指示客户端不存储响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-217">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="5a320-218">示例应用返回具有以下标头的 Cache2 页：</span><span class="sxs-lookup"><span data-stu-id="5a320-218">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="5a320-219">位置和持续时间</span><span class="sxs-lookup"><span data-stu-id="5a320-219">Location and Duration</span></span>

<span data-ttu-id="5a320-220">若要启用缓存， <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 必须将设置为正值，并且 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 必须 `Any` (默认值) 或 `Client` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-220">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="5a320-221">框架将 `Cache-Control` 标头设置为位置值，后跟 `max-age` 响应的。</span><span class="sxs-lookup"><span data-stu-id="5a320-221">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="5a320-222"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>的选项， `Any` 并 `Client` 分别转换为 `Cache-Control` 和的标头值 `public` `private` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-222"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="5a320-223">如 "NoStore" [和 "Location](#nostore-and-locationnone) " 部分所述，将设置 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 为，并将 `None` `Cache-Control` `Pragma` 标头设置为 `no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-223">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="5a320-224">`Location.Any` (`Cache-Control` 设置为 `public`) 指示 *客户端或任何中间代理* 可以缓存值，包括 [响应缓存中间件](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="5a320-224">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="5a320-225">`Location.Client` (`Cache-Control` 设置为 `private`) 指示 *只有客户端* 可以缓存该值。</span><span class="sxs-lookup"><span data-stu-id="5a320-225">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="5a320-226">中间缓存不应缓存值，包括 [响应缓存中间件](xref:performance/caching/middleware)。</span><span class="sxs-lookup"><span data-stu-id="5a320-226">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="5a320-227">缓存控制标头仅向客户端和中间代理提供指导，以及如何缓存响应。</span><span class="sxs-lookup"><span data-stu-id="5a320-227">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="5a320-228">不保证客户端和代理将遵循 [HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)。</span><span class="sxs-lookup"><span data-stu-id="5a320-228">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="5a320-229">[响应缓存中间件](xref:performance/caching/middleware) 始终遵循由规范布局的缓存规则。</span><span class="sxs-lookup"><span data-stu-id="5a320-229">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="5a320-230">下面的示例演示示例应用中的 Cache3 页模型以及通过设置 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 并保留默认值而生成的标头 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> ：</span><span class="sxs-lookup"><span data-stu-id="5a320-230">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="5a320-231">示例应用返回具有以下标头的 Cache3 页：</span><span class="sxs-lookup"><span data-stu-id="5a320-231">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="5a320-232">缓存配置文件</span><span class="sxs-lookup"><span data-stu-id="5a320-232">Cache profiles</span></span>

<span data-ttu-id="5a320-233">在中设置 MVC/Pages 时，可以将缓存配置文件配置为选项，而不是在许多控制器操作属性上复制响应缓存设置 Razor `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="5a320-233">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5a320-234">在引用的缓存配置文件中找到的值将用作默认值 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> ，并由特性上指定的任何属性重写。</span><span class="sxs-lookup"><span data-stu-id="5a320-234">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="5a320-235">设置缓存配置文件。</span><span class="sxs-lookup"><span data-stu-id="5a320-235">Set up a cache profile.</span></span> <span data-ttu-id="5a320-236">下面的示例显示了示例应用中的30秒缓存配置文件 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="5a320-236">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response/samples/3.x/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5a320-237">示例应用的 Cache4 页面模型引用 `Default30` 缓存配置文件：</span><span class="sxs-lookup"><span data-stu-id="5a320-237">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="5a320-238"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>可应用于：</span><span class="sxs-lookup"><span data-stu-id="5a320-238">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* <span data-ttu-id="5a320-239">Razor Pages：特性不能应用于处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="5a320-239">Razor Pages: Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="5a320-240">MVC 控制器。</span><span class="sxs-lookup"><span data-stu-id="5a320-240">MVC controllers.</span></span>
* <span data-ttu-id="5a320-241">MVC 操作方法：方法级特性替代类级特性中指定的设置。</span><span class="sxs-lookup"><span data-stu-id="5a320-241">MVC action methods: Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="5a320-242">缓存配置文件将生成的标头应用到 Cache4 页响应 `Default30` ：</span><span class="sxs-lookup"><span data-stu-id="5a320-242">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="5a320-243">其他资源</span><span class="sxs-lookup"><span data-stu-id="5a320-243">Additional resources</span></span>

* [<span data-ttu-id="5a320-244">在缓存中存储响应</span><span class="sxs-lookup"><span data-stu-id="5a320-244">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="5a320-245">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="5a320-245">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
