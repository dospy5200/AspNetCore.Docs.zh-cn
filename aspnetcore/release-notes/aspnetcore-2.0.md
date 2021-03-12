---
title: ASP.NET Core 2.0 中的新增功能
author: rick-anderson
description: 了解 ASP.NET Core 2.0 的新增功能。
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
uid: aspnetcore-2.0
ms.openlocfilehash: b7515bcd8b15199770a4245469d00d10da5566f8
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605680"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="30d01-103">ASP.NET Core 2.0 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="30d01-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="30d01-104">本文重点介绍 ASP.NET Core 2.0 中最重要的更改，并提供相关文档的链接。</span><span class="sxs-lookup"><span data-stu-id="30d01-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="30d01-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="30d01-105">Razor Pages</span></span>

<span data-ttu-id="30d01-106">Razor Pages 是 ASP.NET Core MVC 的一个新功能，它可以使基于页面的编码方式更简单高效。</span><span class="sxs-lookup"><span data-stu-id="30d01-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="30d01-107">有关详细信息，请参阅相关介绍和教程：</span><span class="sxs-lookup"><span data-stu-id="30d01-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="30d01-108">Razor Pages 简介</span><span class="sxs-lookup"><span data-stu-id="30d01-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="30d01-109">Razor Pages 入门</span><span class="sxs-lookup"><span data-stu-id="30d01-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="30d01-110">ASP.NET Core 元包</span><span class="sxs-lookup"><span data-stu-id="30d01-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="30d01-111">新的 ASP.NET Core 元包包含 ASP.NET Core 和 Entity Framework 团队生成和提供支持的所有包及其内部和第三方依赖项。</span><span class="sxs-lookup"><span data-stu-id="30d01-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="30d01-112">无需再通过包选择单个 ASP.NET Core 功能。</span><span class="sxs-lookup"><span data-stu-id="30d01-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="30d01-113">[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 包中包含所有的功能。</span><span class="sxs-lookup"><span data-stu-id="30d01-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="30d01-114">默认模板使用此包。</span><span class="sxs-lookup"><span data-stu-id="30d01-114">The default templates use this package.</span></span>

<span data-ttu-id="30d01-115">有关详细信息，请参阅 [ASP.NET Core 2.0 的 Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)。</span><span class="sxs-lookup"><span data-stu-id="30d01-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="30d01-116">运行时存储</span><span class="sxs-lookup"><span data-stu-id="30d01-116">Runtime Store</span></span>

<span data-ttu-id="30d01-117">使用 `Microsoft.AspNetCore.All` 元包的应用程序会自动使用新的 .NET Core 运行时存储。</span><span class="sxs-lookup"><span data-stu-id="30d01-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="30d01-118">此存储包含运行 ASP.NET Core 2.0 应用程序所需的所有运行时资产。</span><span class="sxs-lookup"><span data-stu-id="30d01-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="30d01-119">使用 `Microsoft.AspNetCore.All` 元包时，应用程序不会部署引用的 ASP.NET Core NuGet 包中的任何资产，因为目标系统中已存在这些资产。</span><span class="sxs-lookup"><span data-stu-id="30d01-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="30d01-120">运行时存储中的资产也已经过预编译，以便缩短应用程序启动时间。</span><span class="sxs-lookup"><span data-stu-id="30d01-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="30d01-121">有关详细信息，请参阅[运行时存储](/dotnet/core/deploying/runtime-store)</span><span class="sxs-lookup"><span data-stu-id="30d01-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="30d01-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="30d01-122">.NET Standard 2.0</span></span>

<span data-ttu-id="30d01-123">ASP.NET Core 2.0 包面向 NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="30d01-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="30d01-124">这些包可以由其他 .NET Standard 2.0 库引用，也可以在兼容 .NET Standard 2.0 的 .NET 实现上运行，其中包括 .NET Core 2.0 和 .NET Framework 4.6.1。</span><span class="sxs-lookup"><span data-stu-id="30d01-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="30d01-125">`Microsoft.AspNetCore.All` 元包仅面向 .NET Core 2.0，因为它旨在与 .NET Core 2.0 运行时存储一起使用。</span><span class="sxs-lookup"><span data-stu-id="30d01-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="30d01-126">配置更新</span><span class="sxs-lookup"><span data-stu-id="30d01-126">Configuration update</span></span>

<span data-ttu-id="30d01-127">在 ASP.NET Core 2.0 中，已默认将 `IConfiguration` 实例添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="30d01-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="30d01-128">服务容器中的 `IConfiguration` 可以使应用程序更轻松地从容器中检索配置值。</span><span class="sxs-lookup"><span data-stu-id="30d01-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="30d01-129">有关已规划文档的状态的信息，请参阅 [GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/3387)。</span><span class="sxs-lookup"><span data-stu-id="30d01-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="30d01-130">日志记录更新</span><span class="sxs-lookup"><span data-stu-id="30d01-130">Logging update</span></span>

<span data-ttu-id="30d01-131">在 ASP.NET Core 2.0 中，已默认将日志记录并入依存关系注入 (DI) 系统。</span><span class="sxs-lookup"><span data-stu-id="30d01-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="30d01-132">在 Program.cs  文件（而非 Startup.cs  文件）中添加提供程序并配置筛选。</span><span class="sxs-lookup"><span data-stu-id="30d01-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="30d01-133">此外，默认的 `ILoggerFactory` 支持进行筛选，并且你可以使用灵活的方式来进行跨提供程序筛选和特定于提供程序的筛选。</span><span class="sxs-lookup"><span data-stu-id="30d01-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="30d01-134">有关详细信息，请参阅[日志记录介绍](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="30d01-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="30d01-135">身份验证更新</span><span class="sxs-lookup"><span data-stu-id="30d01-135">Authentication update</span></span>

<span data-ttu-id="30d01-136">新的身份验证模型简化了使用 DI 为应用程序配置身份验证的过程。</span><span class="sxs-lookup"><span data-stu-id="30d01-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="30d01-137">使用 [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) 为 Web 应用和 Web API 配置身份验证时可使用新模板。</span><span class="sxs-lookup"><span data-stu-id="30d01-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="30d01-138">有关已规划文档的状态的信息，请参阅 [GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/3054)。</span><span class="sxs-lookup"><span data-stu-id="30d01-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="30d01-139">Identity 更新</span><span class="sxs-lookup"><span data-stu-id="30d01-139">Identity update</span></span>

<span data-ttu-id="30d01-140">在 ASP.NET Core 2.0 中，我们简化了使用 Identity 生成安全的 Web API 的过程。</span><span class="sxs-lookup"><span data-stu-id="30d01-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="30d01-141">可以使用 [Microsoft 身份验证库 (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client)获取用于访问 Web API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="30d01-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="30d01-142">有关 2.0 中的身份验证更改的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="30d01-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="30d01-143">ASP.NET Core 中的帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="30d01-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="30d01-144">为 ASP.NET Core 中的验证器应用启用 QR 码生成</span><span class="sxs-lookup"><span data-stu-id="30d01-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="30d01-145">将身份验证和 Identity 迁移到 ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="30d01-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="30d01-146">SPA 模板</span><span class="sxs-lookup"><span data-stu-id="30d01-146">SPA templates</span></span>

<span data-ttu-id="30d01-147">已提供适用于 Angular、Aurelia、Knockout.js、React.js 及 React.js 和 Redux 的单页应用程序 (SPA) 项目模板。</span><span class="sxs-lookup"><span data-stu-id="30d01-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="30d01-148">Angular 模板已更新至 Angular 4。</span><span class="sxs-lookup"><span data-stu-id="30d01-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="30d01-149">默认情况下，Angular 和 React 模板已可用；有关如何获取其他模板的信息，请参阅[新建 SPA 项目](xref:client-side/spa-services#create-a-new-project)。</span><span class="sxs-lookup"><span data-stu-id="30d01-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="30d01-150">要了解如何在 ASP.NET Core中构建 SPA，请参阅 <xref:client-side/spa-services>。</span><span class="sxs-lookup"><span data-stu-id="30d01-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="30d01-151">Kestrel 改进</span><span class="sxs-lookup"><span data-stu-id="30d01-151">Kestrel improvements</span></span>

<span data-ttu-id="30d01-152">Kestrel Web 服务器包含一项新功能，使其更适合作为面向 Internet 的服务器。</span><span class="sxs-lookup"><span data-stu-id="30d01-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="30d01-153">在 `KestrelServerOptions` 类的新 `Limits` 属性中添加大量服务器约束配置选项。</span><span class="sxs-lookup"><span data-stu-id="30d01-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="30d01-154">为以下内容添加限制：</span><span class="sxs-lookup"><span data-stu-id="30d01-154">Add limits for the following:</span></span>

* <span data-ttu-id="30d01-155">客户端最大连接数</span><span class="sxs-lookup"><span data-stu-id="30d01-155">Maximum client connections</span></span>
* <span data-ttu-id="30d01-156">请求正文最大大小</span><span class="sxs-lookup"><span data-stu-id="30d01-156">Maximum request body size</span></span>
* <span data-ttu-id="30d01-157">请求正文最小数据速率</span><span class="sxs-lookup"><span data-stu-id="30d01-157">Minimum request body data rate</span></span>

<span data-ttu-id="30d01-158">有关详细信息，请参阅 [ASP.NET Core 中的 Kestrel Web 服务器实现](xref:fundamentals/servers/kestrel)。</span><span class="sxs-lookup"><span data-stu-id="30d01-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="30d01-159">WebListener 已重命名为 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="30d01-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="30d01-160">`Microsoft.AspNetCore.Server.WebListener` 和 `Microsoft.Net.Http.Server` 包已合并为一个新包 `Microsoft.AspNetCore.Server.HttpSys`。</span><span class="sxs-lookup"><span data-stu-id="30d01-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="30d01-161">命名空间已进行更新以保持一致。</span><span class="sxs-lookup"><span data-stu-id="30d01-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="30d01-162">有关详细信息，请参阅 [ASP.NET Core 中的 HTTP.sys Web 服务器实现](xref:fundamentals/servers/httpsys)。</span><span class="sxs-lookup"><span data-stu-id="30d01-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="30d01-163">增强了 HTTP 标头支持</span><span class="sxs-lookup"><span data-stu-id="30d01-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="30d01-164">使用 MVC 传输 `FileStreamResult` 或 `FileContentResult` 时，现在可以选择对传输的内容设置 `ETag` 或 `LastModified` 日期。</span><span class="sxs-lookup"><span data-stu-id="30d01-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="30d01-165">可以使用如下所示的代码在返回的内容上设置这些值：</span><span class="sxs-lookup"><span data-stu-id="30d01-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="30d01-166">返回给访问者的文件带有 `ETag` 和 `LastModified` 值的适当 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="30d01-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="30d01-167">如果应用程序访问者使用范围请求标头请求内容，ASP.NET Core 将识别出该请求，并会处理该标头。</span><span class="sxs-lookup"><span data-stu-id="30d01-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="30d01-168">如果可以对请求的内容执行部分传输操作，ASP.NET Core 将适当地跳过一些内容，只返回请求的字节集。</span><span class="sxs-lookup"><span data-stu-id="30d01-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="30d01-169">不必为了采用或处理此功能而将任何特殊的处理程序写入方法；系统会自动处理。</span><span class="sxs-lookup"><span data-stu-id="30d01-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="30d01-170">托管启动和 Application Insights</span><span class="sxs-lookup"><span data-stu-id="30d01-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="30d01-171">托管环境现在可以在应用程序启动时插入额外的包依赖项并执行代码，而应用程序无需显式使用依赖项或调用任何方法。</span><span class="sxs-lookup"><span data-stu-id="30d01-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="30d01-172">可以使用此功能来允许某些环境“启用”该环境特有的功能，而应用程序无需提前获知。</span><span class="sxs-lookup"><span data-stu-id="30d01-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="30d01-173">在 ASP.NET Core 2.0 中，如果在 Visual Studio 中调试并且（选择加入后）在 Azure App Services 中运行，将使用此功能自动启用 Application Insights 诊断。</span><span class="sxs-lookup"><span data-stu-id="30d01-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="30d01-174">因此，默认情况下，项目模板不再添加 Application Insights 包和代码。</span><span class="sxs-lookup"><span data-stu-id="30d01-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="30d01-175">有关已规划文档的状态的信息，请参阅 [GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/3389)。</span><span class="sxs-lookup"><span data-stu-id="30d01-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="30d01-176">自动使用防伪标记</span><span class="sxs-lookup"><span data-stu-id="30d01-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="30d01-177">默认情况下，ASP.NET Core 始终在帮助对内容进行 HTML 编码，但是在新版本中，还采用了额外的措施来帮助预防跨网站请求伪造 (XSRF) 攻击。</span><span class="sxs-lookup"><span data-stu-id="30d01-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="30d01-178">现在在默认情况下，ASP.NET Core 会发出防伪标记，并在窗体 POST 操作和页面上验证它们，且无需其他配置。</span><span class="sxs-lookup"><span data-stu-id="30d01-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="30d01-179">有关详细信息，请参阅 <xref:security/anti-request-forgery>。</span><span class="sxs-lookup"><span data-stu-id="30d01-179">For more information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="30d01-180">自动预编译</span><span class="sxs-lookup"><span data-stu-id="30d01-180">Automatic precompilation</span></span>

<span data-ttu-id="30d01-181">默认情况下，会在发布时启用 Razor 视图预编译，以缩减发布输出大小和应用程序启动时间。</span><span class="sxs-lookup"><span data-stu-id="30d01-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="30d01-182">有关详细信息，请参阅 [ASP.NET Core 中的 Razor 视图编译和预编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="30d01-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="30d01-183">Razor 支持 C# 7.1</span><span class="sxs-lookup"><span data-stu-id="30d01-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="30d01-184">Razor 视图引擎已更新为可使用新的 Roslyn 编译器。</span><span class="sxs-lookup"><span data-stu-id="30d01-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="30d01-185">其中包含对 C# 7.1 功能的支持，例如默认表达式、推断元组名称和泛型模式匹配。</span><span class="sxs-lookup"><span data-stu-id="30d01-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="30d01-186">若要在项目中使用 C# 7.1，请在项目文件中添加以下属性，然后重新加载解决方案：</span><span class="sxs-lookup"><span data-stu-id="30d01-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="30d01-187">有关 C# 7.1 功能的状态的信息，请参阅 [Roslyn GitHub 存储库](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md)。</span><span class="sxs-lookup"><span data-stu-id="30d01-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="30d01-188">2\.0 的其他文档更新</span><span class="sxs-lookup"><span data-stu-id="30d01-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="30d01-189">用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件</span><span class="sxs-lookup"><span data-stu-id="30d01-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="30d01-190">密钥管理</span><span class="sxs-lookup"><span data-stu-id="30d01-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="30d01-191">配置 Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="30d01-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="30d01-192">配置 Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="30d01-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="30d01-193">配置 Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="30d01-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="30d01-194">配置 Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="30d01-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="30d01-195">迁移指南</span><span class="sxs-lookup"><span data-stu-id="30d01-195">Migration guidance</span></span>

<span data-ttu-id="30d01-196">有关如何将 ASP.NET Core 1.x 应用程序迁移到 ASP.NET Core 2.0 的指南，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="30d01-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="30d01-197">从 ASP.NET Core 1.x 迁移到 ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="30d01-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="30d01-198">将身份验证和 Identity 迁移到 ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="30d01-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="30d01-199">其他信息</span><span class="sxs-lookup"><span data-stu-id="30d01-199">Additional Information</span></span>

<span data-ttu-id="30d01-200">有关更改的完整列表，请参阅 [ASP.NET Core 2.0 发行说明](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0)。</span><span class="sxs-lookup"><span data-stu-id="30d01-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="30d01-201">若要实时了解 ASP.NET Core 开发团队的进度和计划，请收看 [ASP.NET Community Standup](https://live.asp.net/)。</span><span class="sxs-lookup"><span data-stu-id="30d01-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
