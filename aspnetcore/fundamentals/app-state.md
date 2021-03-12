---
title: ASP.NET Core 中的会话
author: rick-anderson
description: 发现保留请求间会话的方法。
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
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
uid: fundamentals/app-state
ms.openlocfilehash: ab09867a1b1aa73e423babbe295036e292fc733f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586574"
---
# <a name="session-and-state-management-in-aspnet-core"></a>ASP.NET Core 中的会话和状态管理

::: moniker range=">= aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5) 和 [Diana LaRose](https://github.com/DianaLaRose)

HTTP 是无状态的协议。 默认情况下，HTTP 请求是不保留用户值的独立消息。 本文介绍了几种保留请求间用户数据的方法。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/app-state/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="state-management"></a>状态管理

可以使用几种方法存储状态。 本主题稍后将对每个方法进行介绍。

| 存储方法 | 存储机制 |
| ---------------- | ----------------- |
| [Cookie](#cookies) | HTTP cookie。 可能包括使用服务器端应用代码存储的数据。 |
| [Session State](#session-state) | HTTP cookie 和服务器端应用代码 |
| [TempData](#tempdata) | HTTP cookie 或会话状态 |
| [Query Strings](#query-strings) | HTTP 查询字符串 |
| [Hidden Fields](#hidden-fields) | HTTP 窗体字段 |
| [HttpContext.Items](#httpcontextitems) | 服务器端应用代码 |
| [Cache](#cache) | 服务器端应用代码 |

## <a name="cookies"></a>Cookies

Cookie 存储请求之间的数据。 因为 cookie 是随每个请求发送的，所以它们的大小应该保持在最低限度。 理想情况下，仅标识符应存储在 cookie 中，而数据则由应用存储。 大多数浏览器 cookie 大小限制为 4096 个字节。 每个域仅有有限数量的 cookie 可用。

由于 cookie 易被篡改，因此它们必须由服务器进行验证。 客户端上的 Cookie 可能被用户删除或者过期。 但是，cookie 通常是客户端上最持久的数据暂留形式。

Cookie 通常用于个性化设置，其中的内容是为已知用户定制的。 大多数情况下，仅标识用户，但不对其进行身份验证。 cookie 可以存储用户名、帐户名或唯一的用户 ID（例如 GUID）。 cookie 可用于访问用户的个性化设置，例如首选的网站背景色。

发布 cookie 和处理隐私问题时，请参阅[欧盟一般数据保护条例 (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection)。 有关详细信息，请参阅 [ASP.NET Core 中的一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。

## <a name="session-state"></a>会话状态

会话状态是在用户浏览 Web 应用时用来存储用户数据的 ASP.NET Core 方案。 会话状态使用应用维护的存储来保存客户端所有请求的数据。 会话数据由缓存提供支持，并被视为临时数据。 站点应在没有会话数据的情况下继续运行。 关键应用程序数据应存储在用户数据库中，并仅作为性能优化缓存在会话中。

[SignalR](xref:signalr/index) 应用不支持会话，因为 [SignalR 中心](xref:signalr/hubs)可能独立于 HTTP 上下文执行。 例如，当中心打开的长轮询请求超出请求的 HTTP 上下文的生存期时，可能发生这种情况。

ASP.NET Core 通过向客户端提供包含会话 ID 的 cookie 来维护会话状态。 cookie 会话 ID：

* 会随每个请求发送到应用。
* 由应用用于提取会话数据。

会话状态具有以下行为：

* 会话 cookie 特定于浏览器。 会话不会跨浏览器进行共享。
* 浏览器会话结束时删除会话 cookie。
* 如果收到过期的会话 cookie，则创建使用相同会话 cookie 的新会话。
* 不会保留空会话。 会话中必须设置了至少一个值以保存所有请求的会话。 会话未保留时，为每个新的请求生成新会话 ID。
* 应用在上次请求后保留会话的时间有限。 应用设置会话超时，或者使用 20 分钟的默认值。 在以下情况下，会话状态适合存储用户数据：
  * 特定于某个特定会话。
  * 数据不需要跨会话永久存储。
* 会话数据在调用 <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> 实现或会话到期时删除。
* 没有默认机制告知客户端浏览器已关闭或者客户端上的会话 cookie 被删除或过期的应用代码。
* 默认情况下，会话状态 cookie 不标记为“基本”。 除非站点访问者允许跟踪，否则会话状态不起作用。 有关详细信息，请参阅 <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>。

> [!WARNING]
> 请勿将敏感数据存储在会话状态中。 用户可能不会关闭浏览器或清除会话 cookie。 某些浏览器会保留浏览器窗口之间的有效会话 cookie。 会话可能不限于单个用户。 下一个用户可能继续使用同一会话 cookie 浏览应用。

内存中缓存提供程序在应用驻留的服务器内存中存储会话数据。 在服务器场方案中：

* 使用粘性会话将每个会话加入到单独服务器上的特定应用实例。 默认情况下，[Azure 应用服务](https://azure.microsoft.com/services/app-service/)使用[应用程序请求路由 (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 强制实施粘性会话。 然而，粘性会话可能会影响可伸缩性，并使 Web 应用更新变得复杂。 更好的方法是使用 Redis 或 SQL Server 分布式缓存，它们不需要粘性会话。 有关详细信息，请参阅 <xref:performance/caching/distributed>。
* 会话 cookie 通过 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 进行加密。 必须正确配置数据保护，以在每台计算机上读取会话 cookie。 有关详细信息，请参阅 <xref:security/data-protection/introduction> 和[密钥存储提供程序](xref:security/data-protection/implementation/key-storage-providers)。

### <a name="configure-session-state"></a>配置会话状态

[Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 包：

* 由框架隐式包含。
* 提供用于管理会话状态的中间件。

若要启用会话中间件，`Startup` 必须包含：

* 任何 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 内存缓存。 `IDistributedCache` 实现用作会话后备存储。 有关详细信息，请参阅 <xref:performance/caching/distributed>。
* 对 `ConfigureServices` 中 <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> 的调用。
* 对 `Configure` 中 <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> 的调用。

以下代码演示如何使用 `IDistributedCache` 的默认内存中实现设置内存中会话提供程序：

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

前面的代码设置较短的超时来简化测试。

中间件的顺序很重要。  在 `UseRouting` 之后和 `UseEndpoints` 之前调用 `UseSession`。 请参阅[中间件排序](xref:fundamentals/middleware/index#order)。

配置会话状态后，[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) 可用。

调用 `UseSession` 以前无法访问 `HttpContext.Session`。

在应用已经开始写入到响应流之后，不能创建有新会话 cookie 的新会话。 此异常记录在 Web 服务器日志中但不显示在浏览器中。

### <a name="load-session-state-asynchronously"></a>以异步方式加载会话状态

只有当 <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> 方法是先于 <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>、<xref:Microsoft.AspNetCore.Http.ISession.Set%2A> 或 <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> 方法显式调用时，ASP.NET Core 中的默认会话提供程序才会从基础 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 后备存储中异步加载会话记录。 如果未先调用 `LoadAsync`，则会同步加载基础会话记录，这可能对性能产生大规模影响。

若要让应用强制执行此模式，请使用在 `LoadAsync` 方法没有先于 `TryGetValue`、`Set` 或 `Remove` 调用时抛出异常的版本来包装 <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> 和 <xref:Microsoft.AspNetCore.Session.DistributedSession> 实现。 在服务容器中注册的已包装的版本。

### <a name="session-options"></a>会话选项

若要重写会话默认值，请使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions>。

| 选项 | 描述 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | 确定用于创建 cookie 的设置。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> 默认为 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`)。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> 默认为 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`)。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> 默认为 <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`)。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 默认为 `true`。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 默认为 `false`。 |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` 显示放弃其内容前，内容可以空闲多长时间。 每个会话访问都会重置超时。 此设置仅适用于会话内容，不适用于 cookie。 默认为 20 分钟。 |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | 允许从存储加载会话或者将其提交回存储的最大时长。 此设置可能仅适用于异步操作。 可以使用 <xref:System.Threading.Timeout.InfiniteTimeSpan> 来禁用此超时。 默认值为 1 分钟。 |

会话使用 cookie 跟踪和标识来自单个浏览器的请求。 默认情况下，此 cookie 名为 `.AspNetCore.Session`，并使用路径 `/`。 由于 cookie 默认值没有指定域，因此页面上的客户端脚本无法使用它（因为 <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 默认为 `true`）。

若要重写 cookie 会话默认值，请使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions>：

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

应用使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> 属性来确定在会话空闲多长时间后它在服务器缓存中的内容就会被放弃。 此属性独立于 cookie 到期时间。 通过[会话中间件](xref:Microsoft.AspNetCore.Session.SessionMiddleware)传递的每个请求都会重置超时。

会话状态为“非锁定”。 如果两个请求同时尝试修改同一会话的内容，则后一个请求替代前一个请求。 `Session` 是作为一个连贯会话实现的，这意味着所有内容都存储在一起。 两个请求试图修改不同的会话值时，后一个请求可能替代前一个做出的会话更改。

### <a name="set-and-get-session-values"></a>设置和获取会话值

会话状态是通过 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类或包含 <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> 的 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 类进行访问。 此属性是 <xref:Microsoft.AspNetCore.Http.ISession> 实现。

`ISession` 实现提供用于设置和检索整数和字符串值的若干扩展方法。 扩展方法位于 <xref:Microsoft.AspNetCore.Http> 命名空间中。

`ISession` 扩展方法：

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

以下示例在 Razor Pages 页中检索 `IndexModel.SessionKeyName` 键（示例应用中的 `_Name`）的会话值：

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

以下示例显示如何设置和获取整数和字符串：

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

必须对所有会话数据进行序列化以启用分布式缓存方案，即使是在使用内存中缓存的时候。 字符串和整数序列化程序是由 <xref:Microsoft.AspNetCore.Http.ISession> 的扩展方法提供。 用户必须使用另一种机制（例如 JSON）序列化复杂类型。

使用以下示例代码序列化对象：

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

以下示例演示如何使用 `SessionExtensions` 类设置和获取可序列化的对象：

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core 公开 Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) 或控制器 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>。 在另一个请求读取数据之前，此属性将读取此数据。 [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 和 [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) 方法可用于检查数据，而无需在请求结束时删除。 [Keep](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 将标记字典中的所有项以进行保留。 `TempData` 为：

* 在多个请求需要数据的情况下对重定向很有用。
* 使用 cookie 或会话状态通过 `TempData` 提供程序进行实现。

## <a name="tempdata-samples"></a>TempData 示例

考虑创建客户的以下页面：

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

以下页面显示 `TempData["Message"]`：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

在前面的标记中，在请求结束时，不会删除 `TempData["Message"]`，因为正在使用 `Peek`。 刷新页面将显示 `TempData["Message"]` 的内容。

以下标记类似于前面的代码，但使用 `Keep` 在请求结束时保留数据：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

在 IndexPeek 和 IndexKeep 页面之间导航不会删除 `TempData["Message"]`。

以下代码显示 `TempData["Message"]`，但请求结束时，将删除 `TempData["Message"]`：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData 提供程序

默认情况下使用基于 cookie 的 TempData 提供程序将 TempData 存储于 cookie。

cookie 数据是先使用 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>（用 <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> 编码）进行加密，再进行区块处理。 由于加密和分块，最大 cookie 大小小于 [4096 个字节](http://www.faqs.org/rfcs/rfc2965.html)。 未压缩 cookie 数据，因为压缩加密的数据会导致安全问题，如 [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻击。 若要详细了解基于 cookie 的 TempData 提供程序，请参阅 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>。

### <a name="choose-a-tempdata-provider"></a>选择 TempData 提供程序

选择 TempData 提供程序涉及几个注意事项，例如：

* 应用是否已使用会话状态？ 如果是，使用会话状态 TempData 提供程序对应用没有额外的成本（除了数据的大小）。
* 应用是否只对相对较小的数据量（最多 500 个字节）使用 TempData？ 如果是，cookie TempData 提供程序将为每个携带 TempData 的请求增加较小的成本。 如果不是，会话状态 TempData 提供程序有助于在使用 TempData 前，避免在每个请求中来回切换大量数据。
* 应用是否在多个服务器上的服务器场中运行？ 如果是，无需其他任何配置，即可在数据保护外使用 cookie TempData 提供程序（请参阅 <xref:security/data-protection/introduction>和[密钥存储提供程序](xref:security/data-protection/implementation/key-storage-providers)）。

大多数 Web 客户端（如 Web 浏览器）针对每个 cookie 的最大大小和 cookie 总数强制实施限制。 使用 cookie TempData 提供程序时，请验证应用未超过[这些限制](http://www.faqs.org/rfcs/rfc2965.html)。 考虑数据的总大小。 解释加密和分块导致的 cookie 大小增加。

### <a name="configure-the-tempdata-provider"></a>配置 TempData 提供程序

默认情况下启用基于 cookie 的 TempData 提供程序。

若要启用基于会话的 TempData 提供程序，请使用 <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> 扩展方法。 只需要调用 `AddSessionStateTempDataProvider`：

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>查询字符串

可以将有限的数据从一个请求传递到另一个请求，方法是将其添加到新请求的查询字符串中。 这有利于以一种持久的方式捕获状态，这种方式允许通过电子邮件或社交网络共享嵌入式状态的链接。 由于 URL 查询字符串是公共的，因此请勿对敏感数据使用查询字符串。

除了意外共享之外，在查询字符串中包含数据还会使应用遭受[跨站点请求伪造 (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 攻击。 任何保留的会话状态必须防止 CSRF 攻击。 有关详细信息，请参阅 <xref:security/anti-request-forgery>。

## <a name="hidden-fields"></a>隐藏字段

数据可以保存在隐藏的表单域中，并在下一个请求上回发。 这在多页窗体中很常见。 由于客户端可能篡改数据，因此应用必须始终重新验证存储在隐藏字段中的数据。

## <a name="httpcontextitems"></a>HttpContext.Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> 集合用于在处理单个请求时存储数据。 处理请求后，放弃集合的内容。 通常使用 `Items` 集合允许组件或中间件在请求期间在不同时间点操作且没有直接传递参数的方法时进行通信。

在下面示例中，[中间件](xref:fundamentals/middleware/index)将 `isVerified` 添加到 `Items` 集合：

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

对于只在单个应用中使用的中间件，固定 `string` 键是可以接受的。 应用间共享的中间件应使用唯一的对象键以避免键冲突。 以下示例演示如何使用中间件类中定义的唯一对象键：

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

其他代码可以使用通过中间件类公开的键访问存储在 `HttpContext.Items` 中的值：

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

此方法还有避免在代码中使用关键字符串的优势。

## <a name="cache"></a>缓存

缓存是存储和检索数据的有效方法。 应用可以控制缓存项的生存期。 有关详细信息，请参阅 <xref:performance/caching/response>。

缓存数据未与特定请求、用户或会话相关联。 请不要缓存可能由其他用户请求检索的特定于用户的数据。

若要缓存应用程序范围内的数据，请参阅 <xref:performance/caching/memory>。

## <a name="common-errors"></a>常见错误

* “在尝试激活‘Microsoft.AspNetCore.Session.DistributedSessionStore’时无法为类型‘Microsoft.Extensions.Caching.Distributed.IDistributedCache’解析服务。”

  这通常是由于不能配置至少一个 `IDistributedCache` 实现而造成的。 有关详细信息，请参阅 <xref:performance/caching/distributed> 和 <xref:performance/caching/memory>。

如果会话中间件无法保留会话：

* 中间件记录异常而请求继续正常进行。
* 这会导致不可预知的行为。

如果后备存储不可用，则会话中间件可能无法保留会话。 例如，用户将购物车存储在会话中。 用户将商品添加到购物车，但提交失败。 应用不知道有此失败，因此它向用户报告商品已添加到购物车，但事实并非如此。

检查此类错误的建议方法是完成将应用写入到该会话后，调用 `await feature.Session.CommitAsync`。 如果后备存储不可用，则 <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> 引发异常。 如果 `CommitAsync` 失败，应用可以处理异常。 在与数据存储不可用的相同的条件下，<xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*> 引发异常。
  
## <a name="signalr-and-session-state"></a>SignalR 和会话状态

SignalR 应用不应使用会话状态来存储信息。 SignalR 应用可以将每个连接状态存储在中心的 `Context.Items` 中。 <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>其他资源

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)、[Diana LaRose](https://github.com/DianaLaRose) 和 [Luke Latham](https://github.com/guardrex)

HTTP 是无状态的协议。 不采取其他步骤的情况下，HTTP 请求是不保留用户值或应用状态的独立消息。 本文介绍了几种保留请求间用户数据和应用状态的方法。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/app-state/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="state-management"></a>状态管理

可以使用几种方法存储状态。 本主题稍后将对每个方法进行介绍。

| 存储方法 | 存储机制 |
| ---------------- | ----------------- |
| [Cookie](#cookies) | HTTP cookie（可能包括使用服务器端应用代码存储的数据） |
| [Session State](#session-state) | HTTP cookie 和服务器端应用代码 |
| [TempData](#tempdata) | HTTP cookie 或会话状态 |
| [Query Strings](#query-strings) | HTTP 查询字符串 |
| [Hidden Fields](#hidden-fields) | HTTP 窗体字段 |
| [HttpContext.Items](#httpcontextitems) | 服务器端应用代码 |
| [Cache](#cache) | 服务器端应用代码 |
| [Dependency Injection](#dependency-injection) | 服务器端应用代码 |

## <a name="cookies"></a>Cookies

Cookie 存储请求之间的数据。 因为 cookie 是随每个请求发送的，所以它们的大小应该保持在最低限度。 理想情况下，仅标识符应存储在 cookie 中，而数据则由应用存储。 大多数浏览器 cookie 大小限制为 4096 个字节。 每个域仅有有限数量的 cookie 可用。

由于 cookie 易被篡改，因此它们必须由服务器进行验证。 客户端上的 Cookie 可能被用户删除或者过期。 但是，cookie 通常是客户端上最持久的数据暂留形式。

Cookie 通常用于个性化设置，其中的内容是为已知用户定制的。 大多数情况下，仅标识用户，但不对其进行身份验证。 cookie 可以存储用户名、帐户名或唯一的用户 ID（例如 GUID）。 然后，可以使用 cookie 访问用户的个性化设置，例如首选的网站背景色。

发布 cookie 和处理隐私问题时，请留意[欧盟一般数据保护条例 (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection)。 有关详细信息，请参阅 [ASP.NET Core 中的一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。

## <a name="session-state"></a>会话状态

会话状态是在用户浏览 Web 应用时用来存储用户数据的 ASP.NET Core 方案。 会话状态使用应用维护的存储来保存客户端所有请求的数据。 会话数据由缓存支持并被视为临时数据 - 站点应在没有会话数据的情况下继续运行。 关键应用程序数据应存储在用户数据库中，并仅作为性能优化缓存在会话中。

> [!NOTE]
> [SignalR](xref:signalr/index) 应用不支持会话，因为 [SignalR 中心](xref:signalr/hubs)可能独立于 HTTP 上下文执行。 例如，当中心打开的长轮询请求超出请求的 HTTP 上下文的生存期时，可能发生这种情况。

ASP.NET Core 通过向客户端提供包含会话 ID 的 cookie 来维护会话状态，该会话 ID 与每个请求一起发送到应用。 应用使用会话 ID 来获取会话数据。

会话状态具有以下行为：

* 由于会话 cookie 是特定于浏览器的，因此不能跨浏览器共享会话。
* 浏览器会话结束时删除会话 cookie。
* 如果收到过期的会话 cookie，则创建使用相同会话 cookie 的新会话。
* 不会保留空会话 - 会话中必须设置了至少一个值以保存所有请求的会话。 会话未保留时，为每个新的请求生成新会话 ID。
* 应用在上次请求后保留会话的时间有限。 应用设置会话超时，或者使用 20 分钟的默认值。 会话状态适用于存储特定于特定会话的用户数据，但该数据无需永久的会话存储。
* 会话数据在调用 <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> 实现或会话到期时删除。
* 没有默认机制告知客户端浏览器已关闭或者客户端上的会话 cookie 被删除或过期的应用代码。
* ASP.NET Core MVC 和 Razor Pages 模板包括对一般数据保护条例 (GDPR) 的支持。 默认情况下，会话状态 cookie 不标记为“基本”，因此，除非站点访问者允许跟踪，否则会话状态不起作用。 有关详细信息，请参阅 <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>。

> [!WARNING]
> 请勿将敏感数据存储在会话状态中。 用户可能不会关闭浏览器或清除会话 cookie。 某些浏览器会保留浏览器窗口之间的有效会话 cookie。 会话可能不限于单个用户 &mdash; 下一个用户可能继续使用同一会话 cookie 浏览应用。

内存中缓存提供程序在应用驻留的服务器内存中存储会话数据。 在服务器场方案中：

* 使用粘性会话将每个会话加入到单独服务器上的特定应用实例。 默认情况下，[Azure 应用服务](https://azure.microsoft.com/services/app-service/)使用[应用程序请求路由 (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 强制实施粘性会话。 然而，粘性会话可能会影响可伸缩性，并使 Web 应用更新变得复杂。 更好的方法是使用 Redis 或 SQL Server 分布式缓存，它们不需要粘性会话。 有关详细信息，请参阅 <xref:performance/caching/distributed>。
* 会话 cookie 通过 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 进行加密。 必须正确配置数据保护，以在每台计算机上读取会话 cookie。 有关详细信息，请参阅 <xref:security/data-protection/introduction> 和[密钥存储提供程序](xref:security/data-protection/implementation/key-storage-providers)。

### <a name="configure-session-state"></a>配置会话状态

[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含的 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 包提供中间件来管理会话状态。 若要启用会话中间件，`Startup` 必须包含：

* 任何 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 内存缓存。 `IDistributedCache` 实现用作会话后备存储。 有关详细信息，请参阅 <xref:performance/caching/distributed>。
* 对 `ConfigureServices` 中 <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> 的调用。
* 对 `Configure` 中 <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> 的调用。

以下代码演示如何使用 `IDistributedCache` 的默认内存中实现设置内存中会话提供程序：

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

中间件的顺序很重要。 在前面的示例中，在 `UseMvc` 之后调用 `UseSession` 时会发生 `InvalidOperationException` 异常。 有关详细信息，请参阅[中间件排序](xref:fundamentals/middleware/index#order)。

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> 在会话状态配置后可用。

调用 `UseSession` 以前无法访问 `HttpContext.Session`。

在应用已经开始写入到响应流之后，不能创建有新会话 cookie 的新会话。 此异常记录在 Web 服务器日志中但不显示在浏览器中。

### <a name="load-session-state-asynchronously"></a>以异步方式加载会话状态

只有当 <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> 方法是先于 <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>、<xref:Microsoft.AspNetCore.Http.ISession.Set%2A> 或 <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> 方法显式调用时，ASP.NET Core 中的默认会话提供程序才会从基础 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 后备存储中异步加载会话记录。 如果未先调用 `LoadAsync`，则会同步加载基础会话记录，这可能对性能产生大规模影响。

若要让应用强制执行此模式，请使用在 `LoadAsync` 方法没有先于 `TryGetValue`、`Set` 或 `Remove` 调用时抛出异常的版本来包装 <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> 和 <xref:Microsoft.AspNetCore.Session.DistributedSession> 实现。 在服务容器中注册的已包装的版本。

### <a name="session-options"></a>会话选项

若要重写会话默认值，请使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions>。

| 选项 | 描述 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | 确定用于创建 cookie 的设置。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> 默认为 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`)。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> 默认为 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`)。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> 默认为 <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`)。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 默认为 `true`。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 默认为 `false`。 |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` 显示放弃其内容前，内容可以空闲多长时间。 每个会话访问都会重置超时。 此设置仅适用于会话内容，不适用于 cookie。 默认为 20 分钟。 |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | 允许从存储加载会话或者将其提交回存储的最大时长。 此设置可能仅适用于异步操作。 可以使用 <xref:System.Threading.Timeout.InfiniteTimeSpan> 来禁用此超时。 默认值为 1 分钟。 |

会话使用 cookie 跟踪和标识来自单个浏览器的请求。 默认情况下，此 cookie 名为 `.AspNetCore.Session`，并使用路径 `/`。 由于 cookie 默认值没有指定域，因此页面上的客户端脚本无法使用它（因为 <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 默认为 `true`）。

若要重写 cookie 会话默认值，请使用 `SessionOptions`：

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

应用使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> 属性来确定在会话空闲多长时间后它在服务器缓存中的内容就会被放弃。 此属性独立于 cookie 到期时间。 通过[会话中间件](xref:Microsoft.AspNetCore.Session.SessionMiddleware)传递的每个请求都会重置超时。

会话状态为“非锁定”。 如果两个请求同时尝试修改同一会话的内容，则后一个请求替代前一个请求。 `Session` 是作为一个连贯会话实现的，这意味着所有内容都存储在一起。 两个请求试图修改不同的会话值时，后一个请求可能替代前一个做出的会话更改。

### <a name="set-and-get-session-values"></a>设置和获取会话值

会话状态是通过 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类或包含 <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> 的 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 类进行访问。 此属性是 <xref:Microsoft.AspNetCore.Http.ISession> 实现。

`ISession` 实现提供用于设置和检索整数和字符串值的若干扩展方法。 当项目引用 [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) 包时，扩展方法位于 <xref:Microsoft.AspNetCore.Http> 命名空间中（添加 `using Microsoft.AspNetCore.Http;` 语句可以获取对扩展方法的访问权限）。 这两个包均包括在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。

`ISession` 扩展方法：

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

以下示例在 Razor Pages 页中检索 `IndexModel.SessionKeyName` 键（示例应用中的 `_Name`）的会话值：

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

以下示例显示如何设置和获取整数和字符串：

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

必须对所有会话数据进行序列化以启用分布式缓存方案，即使是在使用内存中缓存的时候。 字符串和整数序列化程序是由 <xref:Microsoft.AspNetCore.Http.ISession> 的扩展方法提供。 用户必须使用另一种机制（例如 JSON）序列化复杂类型。

添加以下扩展方法以设置和获取可序列化的对象：

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

以下示例演示如何使用扩展方法设置和获取可序列化的对象：

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core 公开 Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) 或控制器 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>。 在另一个请求读取数据之前，此属性将读取此数据。 [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 和 [Peek(string)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) 方法可用于检查数据，而无需在请求结束时删除。 [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 将标记字典中的所有项以进行保留。 当多个请求需要数据时，`TempData` 非常有助于进行重定向。 使用 cookie 或会话状态通过 `TempData` 提供程序实现 `TempData`。

## <a name="tempdata-samples"></a>TempData 示例

考虑创建客户的以下页面：

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

以下页面显示 `TempData["Message"]`：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

在前面的标记中，在请求结束时，不会删除 `TempData["Message"]`，因为正在使用 `Peek`。 刷新页面将显示 `TempData["Message"]`。

以下标记类似于前面的代码，但使用 `Keep` 在请求结束时保留数据：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

在 IndexPeek 和 IndexKeep 页面之间导航不会删除 `TempData["Message"]`。

以下代码显示 `TempData["Message"]`，但请求结束时，将删除 `TempData["Message"]`：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData 提供程序

默认情况下使用基于 cookie 的 TempData 提供程序将 TempData 存储于 cookie。

cookie 数据是先使用 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>（用 <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> 编码）进行加密，再进行区块处理。 因为 cookie 进行了分块，所以 ASP.NET Core 1.x 中的单个 cookie 大小限制不适用。 未压缩 cookie 数据，因为压缩加密的数据会导致安全问题，如 [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻击。 若要详细了解基于 cookie 的 TempData 提供程序，请参阅 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>。

### <a name="choose-a-tempdata-provider"></a>选择 TempData 提供程序

选择 TempData 提供程序涉及几个注意事项，例如：

1. 应用是否已使用会话状态？ 如果是，使用会话状态 TempData 提供程序对应用没有额外的成本（除了数据的大小）。
2. 应用是否只对相对较小的数据量（最多 500 个字节）使用 TempData？ 如果是，cookie TempData 提供程序将为每个携带 TempData 的请求增加较小的成本。 如果不是，会话状态 TempData 提供程序有助于在使用 TempData 前，避免在每个请求中来回切换大量数据。
3. 应用是否在多个服务器上的服务器场中运行？ 如果是，无需其他任何配置，即可在数据保护外使用 cookie TempData 提供程序（请参阅 <xref:security/data-protection/introduction>和[密钥存储提供程序](xref:security/data-protection/implementation/key-storage-providers)）。

> [!NOTE]
> 大多数 Web 客户端（如 Web 浏览器）针对每个 cookie 的最大大小、cookie 总数（或两者）强制实施限制。 使用 cookie TempData 提供程序时，请验证应用未超过这些限制。 考虑数据的总大小。 解释加密和分块导致的 cookie 大小增加。

### <a name="configure-the-tempdata-provider"></a>配置 TempData 提供程序

默认情况下启用基于 cookie 的 TempData 提供程序。

若要启用基于会话的 TempData 提供程序，请使用 <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> 扩展方法：

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

中间件的顺序很重要。 在前面的示例中，在 `UseMvc` 之后调用 `UseSession` 时会发生 `InvalidOperationException` 异常。 有关详细信息，请参阅[中间件排序](xref:fundamentals/middleware/index#order)。

> [!IMPORTANT]
> 如果面向 .NET Framework 并使用基于会话的 TempData 提供程序，请将 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 包添加到项目。

## <a name="query-strings"></a>查询字符串

可以将有限的数据从一个请求传递到另一个请求，方法是将其添加到新请求的查询字符串中。 这有利于以一种持久的方式捕获状态，这种方式允许通过电子邮件或社交网络共享嵌入式状态的链接。 由于 URL 查询字符串是公共的，因此请勿对敏感数据使用查询字符串。

除了意外的共享，在查询字符串中包含数据还会为[跨站点请求伪造 (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 攻击创造机会，从而欺骗用户在通过身份验证时访问恶意网站。 然后，攻击者可以从应用中窃取用户数据，或者代表用户采取恶意操作。 任何保留的应用或会话状态必须防止 CSRF 攻击。 有关详细信息，请参阅 <xref:security/anti-request-forgery>。

## <a name="hidden-fields"></a>隐藏字段

数据可以保存在隐藏的表单域中，并在下一个请求上回发。 这在多页窗体中很常见。 由于客户端可能篡改数据，因此应用必须始终重新验证存储在隐藏字段中的数据。

## <a name="httpcontextitems"></a>HttpContext.Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> 集合用于在处理单个请求时存储数据。 处理请求后，放弃集合的内容。 通常使用 `Items` 集合允许组件或中间件在请求期间在不同时间点操作且没有直接传递参数的方法时进行通信。

在下面示例中，[中间件](xref:fundamentals/middleware/index)将 `isVerified` 添加到 `Items` 集合。

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

然后，在管道中，另一个中间件可以访问 `isVerified` 的值：

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

对于只供单个应用使用的中间件，`string` 键是可以接受的。 应用实例间共享的中间件应使用唯一的对象键以避免键冲突。 以下示例演示如何使用中间件类中定义的唯一对象键：

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

其他代码可以使用通过中间件类公开的键访问存储在 `HttpContext.Items` 中的值：

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

此方法还有避免在代码中使用关键字符串的优势。

## <a name="cache"></a>缓存

缓存是存储和检索数据的有效方法。 应用可以控制缓存项的生存期。

缓存数据未与特定请求、用户或会话相关联。 **请注意不要缓存可能由其他用户请求检索的特定于用户的数据。**

有关详细信息，请参阅 <xref:performance/caching/response>。

## <a name="dependency-injection"></a>依赖关系注入

使用[依赖关系注入](xref:fundamentals/dependency-injection)可向所有用户提供数据：

1. 定义一项包含数据的服务。 例如，定义一个名为 `MyAppData` 的类：

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. 将服务类添加到 `Startup.ConfigureServices`：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. 使用数据服务类：

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>常见错误

* “在尝试激活‘Microsoft.AspNetCore.Session.DistributedSessionStore’时无法为类型‘Microsoft.Extensions.Caching.Distributed.IDistributedCache’解析服务。”

  这通常是由于不能配置至少一个 `IDistributedCache` 实现而造成的。 有关详细信息，请参阅 <xref:performance/caching/distributed> 和 <xref:performance/caching/memory>。

* 在会话中间件保存会话失败的事件中（例如，如果后备存储不可用），中间件记录异常而请求继续正常进行。 这会导致不可预知的行为。

  例如，用户将购物车存储在会话中。 用户将商品添加到购物车，但提交失败。 应用不知道有此失败，因此它向用户报告商品已添加到购物车，但事实并非如此。

  检查此类错误的建议方法是完成将应用写入到该会话后，从应用代码调用 `await feature.Session.CommitAsync();`。 如果后备存储不可用，则 `CommitAsync` 引发异常。 如果 `CommitAsync` 失败，应用可以处理异常。 在与数据存储不可用的相同的条件下，`LoadAsync` 引发异常。
  
## <a name="signalr-and-session-state"></a>SignalR 和会话状态

SignalR 应用不应使用会话状态来存储信息。 SignalR 应用可以将每个连接状态存储在中心的 `Context.Items` 中。 <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>其他资源

<xref:host-and-deploy/web-farm>
::: moniker-end
