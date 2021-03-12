---
title: ASP.NET Core 中的静态文件
author: rick-anderson
description: 了解如何在 ASP.NET Core Web 应用中提供和保护静态文件，以及如何配置静态文件托管中间件行为。
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
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
uid: fundamentals/static-files
ms.openlocfilehash: 807cffb2f9b3bf89ff06c62e76d51d4040b8d91a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589005"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="c104d-103">ASP.NET Core 中的静态文件</span><span class="sxs-lookup"><span data-stu-id="c104d-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c104d-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="c104d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="c104d-105">默认情况下，静态文件（如 HTML、CSS、图像和 JavaScript）是 ASP.NET Core 应用直接提供给客户端的资产。</span><span class="sxs-lookup"><span data-stu-id="c104d-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="c104d-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c104d-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="c104d-107">提供静态文件</span><span class="sxs-lookup"><span data-stu-id="c104d-107">Serve static files</span></span>

<span data-ttu-id="c104d-108">静态文件存储在项目的 [Web 根](xref:fundamentals/index#web-root)目录中。</span><span class="sxs-lookup"><span data-stu-id="c104d-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="c104d-109">默认目录为 `{content root}/wwwroot`，但可通过 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 方法更改目录。</span><span class="sxs-lookup"><span data-stu-id="c104d-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="c104d-110">有关详细信息，请参阅[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="c104d-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="c104d-111">采用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 方法可将内容根目录设置为当前目录：</span><span class="sxs-lookup"><span data-stu-id="c104d-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="c104d-112">上述代码是使用 Web 应用程序模板创建的。</span><span class="sxs-lookup"><span data-stu-id="c104d-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="c104d-113">可通过 [Web 根目录](xref:fundamentals/index#web-root)的相关路径访问静态文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="c104d-114">例如，Web 应用程序项目模板包含 `wwwroot` 文件夹中的多个文件夹：</span><span class="sxs-lookup"><span data-stu-id="c104d-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="c104d-115">请考虑创建 wwwroot/images 文件夹，并添加 wwwroot/images/MyImage 文件。 </span><span class="sxs-lookup"><span data-stu-id="c104d-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="c104d-116">用于访问 `images` 文件夹中的文件的 URI 格式为 `https://<hostname>/images/<image_file_name>`。</span><span class="sxs-lookup"><span data-stu-id="c104d-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="c104d-117">例如，`https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="c104d-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="c104d-118">在 Web 根目录中提供文件</span><span class="sxs-lookup"><span data-stu-id="c104d-118">Serve files in web root</span></span>

<span data-ttu-id="c104d-119">默认 Web 应用模板在 `Startup.Configure` 中调用 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 方法，这将允许提供静态文件：</span><span class="sxs-lookup"><span data-stu-id="c104d-119">The default web app templates call the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="c104d-120">无参数 `UseStaticFiles` 方法重载将 [Web 根目录](xref:fundamentals/index#web-root)中的文件标记为可用。</span><span class="sxs-lookup"><span data-stu-id="c104d-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="c104d-121">以下标记引用 wwwroot/images/MyImage.jpg：</span><span class="sxs-lookup"><span data-stu-id="c104d-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="c104d-122">在上面的代码中，波形符 `~/` 指向 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="c104d-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="c104d-123">提供 Web 根目录外的文件</span><span class="sxs-lookup"><span data-stu-id="c104d-123">Serve files outside of web root</span></span>

<span data-ttu-id="c104d-124">考虑一个目录层次结构，其中要提供的静态文件位于 [Web 根目录](xref:fundamentals/index#web-root)之外：</span><span class="sxs-lookup"><span data-stu-id="c104d-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="c104d-125">按如下方式配置静态文件中间件后，请求可访问 `red-rose.jpg` 文件：</span><span class="sxs-lookup"><span data-stu-id="c104d-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="c104d-126">在前面的代码中，MyStaticFiles 目录层次结构通过 StaticFiles URI 段公开 。</span><span class="sxs-lookup"><span data-stu-id="c104d-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="c104d-127">对 `https://<hostname>/StaticFiles/images/red-rose.jpg` 的请求将提供 red-rose.jpg 文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="c104d-128">以下标记引用 MyStaticFiles/images/red-rose.jpg：</span><span class="sxs-lookup"><span data-stu-id="c104d-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="c104d-129">设置 HTTP 响应标头</span><span class="sxs-lookup"><span data-stu-id="c104d-129">Set HTTP response headers</span></span>

<span data-ttu-id="c104d-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 对象可用于设置 HTTP 响应标头。</span><span class="sxs-lookup"><span data-stu-id="c104d-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="c104d-131">除配置从 [Web 根目录](xref:fundamentals/index#web-root)提供静态文件外，以下代码还设置 `Cache-Control` 标头：</span><span class="sxs-lookup"><span data-stu-id="c104d-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="c104d-132">静态文件可在 600 秒内公开缓存：</span><span class="sxs-lookup"><span data-stu-id="c104d-132">Static files are publicly cacheable for 600 seconds:</span></span>

![已添加显示 Cache-Control 标头的响应头](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="c104d-134">静态文件授权</span><span class="sxs-lookup"><span data-stu-id="c104d-134">Static file authorization</span></span>

<span data-ttu-id="c104d-135">ASP.NET Core 模板在调用 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 之前调用 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>。</span><span class="sxs-lookup"><span data-stu-id="c104d-135">The ASP.NET Core templates call <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> before calling <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>.</span></span> <span data-ttu-id="c104d-136">大多数应用都遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="c104d-136">Most apps follow this pattern.</span></span> <span data-ttu-id="c104d-137">如果在授权中间件之前调用静态文件中间件：</span><span class="sxs-lookup"><span data-stu-id="c104d-137">When the Static File Middleware is called before the authorization middleware:</span></span>

  * <span data-ttu-id="c104d-138">不会对静态文件执行任何授权检查。</span><span class="sxs-lookup"><span data-stu-id="c104d-138">No authorization checks are performed on the static files.</span></span>
  * <span data-ttu-id="c104d-139">静态文件中间件提供的静态文件（例如 `wwwroot` 下的文件）可公开访问。</span><span class="sxs-lookup"><span data-stu-id="c104d-139">Static files served by the Static File Middleware, such as those those under `wwwroot`, are publicly accessible.</span></span>
  
<span data-ttu-id="c104d-140">根据授权提供静态文件：</span><span class="sxs-lookup"><span data-stu-id="c104d-140">To serve static files based on authorization:</span></span>

  * <span data-ttu-id="c104d-141">将它们存储在 `wwwroot` 之外。</span><span class="sxs-lookup"><span data-stu-id="c104d-141">Store them outside of `wwwroot`.</span></span>
  * <span data-ttu-id="c104d-142">调用 `UseAuthorization` 之后调用 `UseStaticFiles`，以指定路径。</span><span class="sxs-lookup"><span data-stu-id="c104d-142">Call `UseStaticFiles`, specifying a path, after calling `UseAuthorization`.</span></span>
  * <span data-ttu-id="c104d-143">设置[回退授权策略](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)。</span><span class="sxs-lookup"><span data-stu-id="c104d-143">Set the [fallback authorization policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  <span data-ttu-id="c104d-144">在前面的代码中，回退授权策略要求所有用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="c104d-144">In the preceding code, the fallback authorization policy requires ***all*** users to be authenticated.</span></span> <span data-ttu-id="c104d-145">用于指定其自己的授权要求的终结点（如控制器、Razor Pages 等）不使用回退授权策略。</span><span class="sxs-lookup"><span data-stu-id="c104d-145">Endpoints such as controllers, Razor Pages, etc that specify their own authorization requirements don't use the fallback authorization policy.</span></span> <span data-ttu-id="c104d-146">例如，具有 `[AllowAnonymous]` 或 `[Authorize(PolicyName="MyPolicy")]` 的 Razor Pages、控制器或操作方法使用应用的授权属性，而不是回退授权策略。</span><span class="sxs-lookup"><span data-stu-id="c104d-146">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authorization attribute rather than the fallback authorization policy.</span></span>

  <span data-ttu-id="c104d-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 将 <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> 添加到当前实例，这将强制对当前用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="c104d-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

  <span data-ttu-id="c104d-148">`wwwroot` 下的静态资产是可公开访问的，因为在 `UseAuthentication` 之前会调用默认静态文件中间件 (`app.UseStaticFiles();`)。</span><span class="sxs-lookup"><span data-stu-id="c104d-148">Static assets under `wwwroot` are publicly accessible because the default Static File Middleware (`app.UseStaticFiles();`) is called before `UseAuthentication`.</span></span> <span data-ttu-id="c104d-149">MyStaticFiles 文件夹中的静态资产需要身份验证。</span><span class="sxs-lookup"><span data-stu-id="c104d-149">Static assets in the *MyStaticFiles* folder require authentication.</span></span> <span data-ttu-id="c104d-150">[示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples)对此进行了演示。</span><span class="sxs-lookup"><span data-stu-id="c104d-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) demonstrates this.</span></span>

<span data-ttu-id="c104d-151">还有一种根据授权提供文件的方法是：</span><span class="sxs-lookup"><span data-stu-id="c104d-151">An alternative approach to serve files based on authorization is to:</span></span>

  * <span data-ttu-id="c104d-152">将文件存储在 `wwwroot` 和静态文件中间件可访问的任何目录之外。</span><span class="sxs-lookup"><span data-stu-id="c104d-152">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
  * <span data-ttu-id="c104d-153">通过应用授权的操作方法为其提供服务，并返回 <xref:Microsoft.AspNetCore.Mvc.FileResult> 对象：</span><span class="sxs-lookup"><span data-stu-id="c104d-153">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="c104d-154">目录浏览</span><span class="sxs-lookup"><span data-stu-id="c104d-154">Directory browsing</span></span>

<span data-ttu-id="c104d-155">目录浏览允许在指定目录中列出目录。</span><span class="sxs-lookup"><span data-stu-id="c104d-155">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="c104d-156">出于安全考虑，目录浏览默认处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="c104d-156">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="c104d-157">有关详细信息，请参阅[注意事项](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="c104d-157">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="c104d-158">通过以下方式启用目录浏览：</span><span class="sxs-lookup"><span data-stu-id="c104d-158">Enable directory browsing with:</span></span>

* <span data-ttu-id="c104d-159">`Startup.ConfigureServices` 中的 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>。</span><span class="sxs-lookup"><span data-stu-id="c104d-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="c104d-160">`Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A>。</span><span class="sxs-lookup"><span data-stu-id="c104d-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="c104d-161">上述代码允许使用 URL `https://<hostname>/MyImages` 浏览 wwwroot/images 文件夹的目录，并链接到每个文件和文件夹：</span><span class="sxs-lookup"><span data-stu-id="c104d-161">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![目录浏览](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="c104d-163">提供默认文档</span><span class="sxs-lookup"><span data-stu-id="c104d-163">Serve default documents</span></span>

<span data-ttu-id="c104d-164">设置默认页面为访问者提供网站的起点。</span><span class="sxs-lookup"><span data-stu-id="c104d-164">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="c104d-165">若要在没有完全限定 URI 的情况下提供 `wwwroot` 的默认页面，请调用 <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="c104d-165">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="c104d-166">要提供默认文件，必须在 `UseStaticFiles` 前调用 `UseDefaultFiles`。</span><span class="sxs-lookup"><span data-stu-id="c104d-166">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="c104d-167">`UseDefaultFiles` 是不提供文件的 URL 重写工具。</span><span class="sxs-lookup"><span data-stu-id="c104d-167">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="c104d-168">使用 `UseDefaultFiles` 请求对 `wwwroot` 中的文件夹搜索：</span><span class="sxs-lookup"><span data-stu-id="c104d-168">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="c104d-169">default.htm</span><span class="sxs-lookup"><span data-stu-id="c104d-169">*default.htm*</span></span>
* <span data-ttu-id="c104d-170">default.html</span><span class="sxs-lookup"><span data-stu-id="c104d-170">*default.html*</span></span>
* <span data-ttu-id="c104d-171">index.htm</span><span class="sxs-lookup"><span data-stu-id="c104d-171">*index.htm*</span></span>
* <span data-ttu-id="c104d-172">index.html </span><span class="sxs-lookup"><span data-stu-id="c104d-172">*index.html*</span></span>

<span data-ttu-id="c104d-173">将请求视为完全限定 URI，提供在列表中找到的第一个文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-173">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="c104d-174">浏览器 URL 继续反映请求的 URI。</span><span class="sxs-lookup"><span data-stu-id="c104d-174">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="c104d-175">以下代码将默认文件名更改为 mydefault.html：</span><span class="sxs-lookup"><span data-stu-id="c104d-175">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="c104d-176">以下代码通过上述代码演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="c104d-176">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="c104d-177">默认文档的 UseFileServer</span><span class="sxs-lookup"><span data-stu-id="c104d-177">UseFileServer for default documents</span></span>

<span data-ttu-id="c104d-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 结合了 `UseStaticFiles`、`UseDefaultFiles` 和 `UseDirectoryBrowser`（可选）的功能。</span><span class="sxs-lookup"><span data-stu-id="c104d-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="c104d-179">调用 `app.UseFileServer` 以提供静态文件和默认文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-179">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="c104d-180">未启用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="c104d-180">Directory browsing isn't enabled.</span></span> <span data-ttu-id="c104d-181">以下代码通过 `UseFileServer` 演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="c104d-181">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="c104d-182">以下代码提供静态文件、默认文件和目录浏览：</span><span class="sxs-lookup"><span data-stu-id="c104d-182">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="c104d-183">以下代码通过上述代码演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="c104d-183">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="c104d-184">考虑以下目录层次结构：</span><span class="sxs-lookup"><span data-stu-id="c104d-184">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="c104d-185">以下代码提供静态文件、默认文件和 `MyStaticFiles` 的目录浏览：</span><span class="sxs-lookup"><span data-stu-id="c104d-185">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="c104d-186">必须在 `EnableDirectoryBrowsing` 属性值为 `true` 时调用 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>。</span><span class="sxs-lookup"><span data-stu-id="c104d-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="c104d-187">使用文件层次结构和前面的代码，URL 解析如下：</span><span class="sxs-lookup"><span data-stu-id="c104d-187">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="c104d-188">URI</span><span class="sxs-lookup"><span data-stu-id="c104d-188">URI</span></span>            |      <span data-ttu-id="c104d-189">响应</span><span class="sxs-lookup"><span data-stu-id="c104d-189">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="c104d-190">MyStaticFiles/images/MyImage.jpg</span><span class="sxs-lookup"><span data-stu-id="c104d-190">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="c104d-191">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="c104d-191">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="c104d-192">如果 MyStaticFiles 目录中不存在默认命名文件，则 `https://<hostname>/StaticFiles` 返回包含可单击链接的目录列表：</span><span class="sxs-lookup"><span data-stu-id="c104d-192">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![静态文件列表](static-files/_static/db2.png)

<span data-ttu-id="c104d-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 和 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 执行从不带尾部 `/` 的目标 URI 到带尾部 `/` 的目标 URI 的客户端重定向。</span><span class="sxs-lookup"><span data-stu-id="c104d-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="c104d-195">例如，从 `https://<hostname>/StaticFiles` 到 `https://<hostname>/StaticFiles/`。</span><span class="sxs-lookup"><span data-stu-id="c104d-195">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="c104d-196">如果没有尾部斜杠 (`/`)，StaticFiles 目录中的相对 URL 无效。</span><span class="sxs-lookup"><span data-stu-id="c104d-196">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="c104d-197">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="c104d-197">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="c104d-198"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 类包含 `Mappings` 属性，用作文件扩展名到 MIME 内容类型的映射。</span><span class="sxs-lookup"><span data-stu-id="c104d-198">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="c104d-199">在以下示例中，多个文件扩展名映射到了已知的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="c104d-199">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="c104d-200">替换了 .rtf 扩展名，删除了 .mp4 ：</span><span class="sxs-lookup"><span data-stu-id="c104d-200">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="c104d-201">以下代码通过上述代码演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="c104d-201">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="c104d-202">请参阅 [MIME 内容类型](https://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="c104d-202">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="c104d-203">非标准内容类型</span><span class="sxs-lookup"><span data-stu-id="c104d-203">Non-standard content types</span></span>

<span data-ttu-id="c104d-204">静态文件中间件可理解近 400 种已知文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="c104d-204">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="c104d-205">如果用户请求文件类型未知的文件，则静态文件中间件将请求传递给管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="c104d-205">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="c104d-206">如果没有中间件处理请求，则返回“404 未找到”响应。</span><span class="sxs-lookup"><span data-stu-id="c104d-206">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="c104d-207">如果启用了目录浏览，则在目录列表中会显示该文件的链接。</span><span class="sxs-lookup"><span data-stu-id="c104d-207">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="c104d-208">以下代码提供未知类型，并以图像形式呈现未知文件：</span><span class="sxs-lookup"><span data-stu-id="c104d-208">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="c104d-209">以下代码通过上述代码演示了 `Startup.Configure`：</span><span class="sxs-lookup"><span data-stu-id="c104d-209">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="c104d-210">使用前面的代码，请求的文件含未知内容类型时，以图像形式返回请求。</span><span class="sxs-lookup"><span data-stu-id="c104d-210">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="c104d-211">启用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 会形成安全隐患。</span><span class="sxs-lookup"><span data-stu-id="c104d-211">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="c104d-212">它默认处于禁用状态，不建议使用。</span><span class="sxs-lookup"><span data-stu-id="c104d-212">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="c104d-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 提供了更安全的替代方法来提供含非标准扩展名的文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="c104d-214">从多个位置提供文件</span><span class="sxs-lookup"><span data-stu-id="c104d-214">Serve files from multiple locations</span></span>

<span data-ttu-id="c104d-215">`UseStaticFiles` 和 `UseFileServer` 默认为指向 `wwwroot` 的文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="c104d-215">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="c104d-216">可使用其他文件提供程序提供 `UseStaticFiles` 和 `UseFileServer` 的其他实例，从多个位置提供文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-216">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="c104d-217">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。</span><span class="sxs-lookup"><span data-stu-id="c104d-217">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="c104d-218">静态文件的安全注意事项</span><span class="sxs-lookup"><span data-stu-id="c104d-218">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="c104d-219">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能会泄漏机密。</span><span class="sxs-lookup"><span data-stu-id="c104d-219">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="c104d-220">强烈建议在生产中禁用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="c104d-220">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="c104d-221">请仔细查看 `UseStaticFiles` 或 `UseDirectoryBrowser` 启用了哪些目录。</span><span class="sxs-lookup"><span data-stu-id="c104d-221">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="c104d-222">整个目录及其子目录均可公开访问。</span><span class="sxs-lookup"><span data-stu-id="c104d-222">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="c104d-223">将适合公开的文件存储在专用目录中，如 `<content_root>/wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="c104d-223">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="c104d-224">将这些文件与 MVC 视图、Razor Pages 和配置文件等分开。</span><span class="sxs-lookup"><span data-stu-id="c104d-224">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="c104d-225">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公开的内容的 URL 受大小写和基础文件系统字符限制的影响。</span><span class="sxs-lookup"><span data-stu-id="c104d-225">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="c104d-226">例如，Windows 不区分大小写，但 macOS 和 Linux 区分大小写。</span><span class="sxs-lookup"><span data-stu-id="c104d-226">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="c104d-227">托管于 IIS 中的 ASP.NET Core 应用使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将所有请求转发到应用，包括静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="c104d-227">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="c104d-228">不使用 IIS 静态文件处理程序，并且没有机会处理请求。</span><span class="sxs-lookup"><span data-stu-id="c104d-228">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="c104d-229">在 IIS Manager 中完成以下步骤，删除服务器或网站级别的 IIS 静态文件处理程序：</span><span class="sxs-lookup"><span data-stu-id="c104d-229">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="c104d-230">转到“模块”功能。</span><span class="sxs-lookup"><span data-stu-id="c104d-230">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="c104d-231">在列表中选择 StaticFileModule。</span><span class="sxs-lookup"><span data-stu-id="c104d-231">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="c104d-232">单击“操作”侧栏中的“删除” 。</span><span class="sxs-lookup"><span data-stu-id="c104d-232">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="c104d-233">如果启用了 IIS 静态文件处理程序且 ASP.NET Core 模块配置不正确，则会提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-233">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="c104d-234">例如，如果未部署 web.config 文件，则会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="c104d-234">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="c104d-235">将代码文件（包括 .cs 和 .cshtml）放在应用项目的 [Web 根目录](xref:fundamentals/index#web-root)之外 。</span><span class="sxs-lookup"><span data-stu-id="c104d-235">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="c104d-236">这样就在应用的客户端内容和基于服务器的代码间创建了逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="c104d-236">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="c104d-237">可以防止服务器端代码泄漏。</span><span class="sxs-lookup"><span data-stu-id="c104d-237">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c104d-238">其他资源</span><span class="sxs-lookup"><span data-stu-id="c104d-238">Additional resources</span></span>

* [<span data-ttu-id="c104d-239">中间件</span><span class="sxs-lookup"><span data-stu-id="c104d-239">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c104d-240">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="c104d-240">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c104d-241">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="c104d-241">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="c104d-242">静态文件（如 HTML、CSS、图像和 JavaScript）是 ASP.NET Core 应用直接提供给客户端的资产。</span><span class="sxs-lookup"><span data-stu-id="c104d-242">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="c104d-243">需要进行一些配置才能提供这些文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-243">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="c104d-244">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c104d-244">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="c104d-245">提供静态文件</span><span class="sxs-lookup"><span data-stu-id="c104d-245">Serve static files</span></span>

<span data-ttu-id="c104d-246">静态文件存储在项目的 [Web 根](xref:fundamentals/index#web-root)目录中。</span><span class="sxs-lookup"><span data-stu-id="c104d-246">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="c104d-247">默认目录是 {content root}/wwwroot，但可通过 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> 方法更改目录。</span><span class="sxs-lookup"><span data-stu-id="c104d-247">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="c104d-248">有关详细信息，请参阅[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="c104d-248">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="c104d-249">应用的 Web 主机必须识别内容根目录。</span><span class="sxs-lookup"><span data-stu-id="c104d-249">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="c104d-250">采用 `WebHost.CreateDefaultBuilder` 方法可将内容根目录设置为当前目录：</span><span class="sxs-lookup"><span data-stu-id="c104d-250">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="c104d-251">可通过 [Web 根目录](xref:fundamentals/index#web-root)的相关路径访问静态文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-251">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="c104d-252">例如，Web 应用程序项目模板包含 `wwwroot` 文件夹中的多个文件夹：</span><span class="sxs-lookup"><span data-stu-id="c104d-252">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="c104d-253">用于访问 images 子文件夹中的文件的 URI 格式为 http://\<server_address>/images/\<image_file_name> 。</span><span class="sxs-lookup"><span data-stu-id="c104d-253">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="c104d-254">例如， *http://localhost:9189/images/banner3.svg* 。</span><span class="sxs-lookup"><span data-stu-id="c104d-254">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="c104d-255">如果以 .NET Framework 为目标，请将 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="c104d-255">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="c104d-256">如果以 .NET Core 为目标，[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)将包括此包。</span><span class="sxs-lookup"><span data-stu-id="c104d-256">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="c104d-257">配置提供静态文件的[中间件](xref:fundamentals/middleware/index)。</span><span class="sxs-lookup"><span data-stu-id="c104d-257">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="c104d-258">提供 Web 根目录内的文件</span><span class="sxs-lookup"><span data-stu-id="c104d-258">Serve files inside of web root</span></span>

<span data-ttu-id="c104d-259">调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="c104d-259">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="c104d-260">无参数 `UseStaticFiles` 方法重载将 [Web 根目录](xref:fundamentals/index#web-root)中的文件标记为可用。</span><span class="sxs-lookup"><span data-stu-id="c104d-260">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="c104d-261">以下标记引用 wwwroot/images/banner1.svg：</span><span class="sxs-lookup"><span data-stu-id="c104d-261">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="c104d-262">在上面的代码中，波形符 `~/` 指向 [Web 根目录](xref:fundamentals/index#web-root)。</span><span class="sxs-lookup"><span data-stu-id="c104d-262">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="c104d-263">提供 Web 根目录外的文件</span><span class="sxs-lookup"><span data-stu-id="c104d-263">Serve files outside of web root</span></span>

<span data-ttu-id="c104d-264">考虑一个目录层次结构，其中要提供的静态文件位于 [Web 根目录](xref:fundamentals/index#web-root)之外：</span><span class="sxs-lookup"><span data-stu-id="c104d-264">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="c104d-265">按如下方式配置静态文件中间件后，请求可访问 banner1.svg 文件：</span><span class="sxs-lookup"><span data-stu-id="c104d-265">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="c104d-266">在前面的代码中，MyStaticFiles 目录层次结构通过 StaticFiles URI 段公开 。</span><span class="sxs-lookup"><span data-stu-id="c104d-266">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="c104d-267">请求 http://\<server_address>/StaticFiles/images/banner1.svg 提供 banner1.svg 文件 。</span><span class="sxs-lookup"><span data-stu-id="c104d-267">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="c104d-268">以下标记引用 MyStaticFiles/images/banner1.svg：</span><span class="sxs-lookup"><span data-stu-id="c104d-268">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="c104d-269">设置 HTTP 响应标头</span><span class="sxs-lookup"><span data-stu-id="c104d-269">Set HTTP response headers</span></span>

<span data-ttu-id="c104d-270"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions> 对象可用于设置 HTTP 响应标头。</span><span class="sxs-lookup"><span data-stu-id="c104d-270">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="c104d-271">除配置从 [Web 根目录](xref:fundamentals/index#web-root)提供静态文件外，以下代码还设置 `Cache-Control` 标头：</span><span class="sxs-lookup"><span data-stu-id="c104d-271">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="c104d-272"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> 方法位于 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 包中。</span><span class="sxs-lookup"><span data-stu-id="c104d-272">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="c104d-273">在开发环境中可公开缓存这些文件 10 分钟（600 秒）：</span><span class="sxs-lookup"><span data-stu-id="c104d-273">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![已添加显示 Cache-Control 标头的响应头](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="c104d-275">静态文件授权</span><span class="sxs-lookup"><span data-stu-id="c104d-275">Static file authorization</span></span>

<span data-ttu-id="c104d-276">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="c104d-276">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="c104d-277">可公开访问由静态文件中间件提供的任何文件，包括 wwwroot 下的文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-277">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="c104d-278">根据授权提供文件：</span><span class="sxs-lookup"><span data-stu-id="c104d-278">To serve files based on authorization:</span></span>

* <span data-ttu-id="c104d-279">将文件存储在 wwwroot 和静态文件中间件可访问的任何目录之外。</span><span class="sxs-lookup"><span data-stu-id="c104d-279">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="c104d-280">通过有授权的操作方法提供文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-280">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="c104d-281">返回一个 <xref:Microsoft.AspNetCore.Mvc.FileResult> 对象：</span><span class="sxs-lookup"><span data-stu-id="c104d-281">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="c104d-282">启用目录浏览</span><span class="sxs-lookup"><span data-stu-id="c104d-282">Enable directory browsing</span></span>

<span data-ttu-id="c104d-283">通过目录浏览，Web 应用的用户可查看目录列表和指定目录中的文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-283">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="c104d-284">出于安全考虑，目录浏览默认处于禁用状态（请参阅[注意事项](#considerations)）。</span><span class="sxs-lookup"><span data-stu-id="c104d-284">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="c104d-285">调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> 方法来启用目录浏览：</span><span class="sxs-lookup"><span data-stu-id="c104d-285">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="c104d-286">通过从 `Startup.ConfigureServices` 调用 <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> 方法添加所需服务：</span><span class="sxs-lookup"><span data-stu-id="c104d-286">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="c104d-287">上述代码允许使用 URL http://\<server_address>/MyImages 浏览 wwwroot/images 文件夹的目录，并链接到每个文件和文件夹 ：</span><span class="sxs-lookup"><span data-stu-id="c104d-287">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![目录浏览](static-files/_static/dir-browse.png)

<span data-ttu-id="c104d-289">有关启用浏览时的安全风险，请参阅[注意事项](#considerations)。</span><span class="sxs-lookup"><span data-stu-id="c104d-289">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="c104d-290">请注意以下示例中的两个 `UseStaticFiles` 调用。</span><span class="sxs-lookup"><span data-stu-id="c104d-290">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="c104d-291">第一个调用提供 wwwroot 文件夹中的静态文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-291">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="c104d-292">第二个调用使用 URL http://\<server_address>/MyImages 浏览 wwwroot/images 文件夹的目录 ：</span><span class="sxs-lookup"><span data-stu-id="c104d-292">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="c104d-293">提供默认文档</span><span class="sxs-lookup"><span data-stu-id="c104d-293">Serve a default document</span></span>

<span data-ttu-id="c104d-294">设置默认主页为访问者访问网站时提供了逻辑起点。</span><span class="sxs-lookup"><span data-stu-id="c104d-294">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="c104d-295">若要在用户不完全限定 URI 的情况下提供默认页面，请调用 `Startup.Configure` 中的 <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> 方法：</span><span class="sxs-lookup"><span data-stu-id="c104d-295">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="c104d-296">要提供默认文件，必须在 `UseStaticFiles` 前调用 `UseDefaultFiles`。</span><span class="sxs-lookup"><span data-stu-id="c104d-296">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="c104d-297">`UseDefaultFiles` 实际上用于重写 URL，不提供文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-297">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="c104d-298">通过 `UseStaticFiles` 启用静态文件中间件来提供文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-298">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="c104d-299">使用 `UseDefaultFiles` 请求文件夹搜索：</span><span class="sxs-lookup"><span data-stu-id="c104d-299">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="c104d-300">default.htm</span><span class="sxs-lookup"><span data-stu-id="c104d-300">*default.htm*</span></span>
* <span data-ttu-id="c104d-301">default.html</span><span class="sxs-lookup"><span data-stu-id="c104d-301">*default.html*</span></span>
* <span data-ttu-id="c104d-302">index.htm</span><span class="sxs-lookup"><span data-stu-id="c104d-302">*index.htm*</span></span>
* <span data-ttu-id="c104d-303">index.html </span><span class="sxs-lookup"><span data-stu-id="c104d-303">*index.html*</span></span>

<span data-ttu-id="c104d-304">将请求视为完全限定 URI，提供在列表中找到的第一个文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-304">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="c104d-305">浏览器 URL 继续反映请求的 URI。</span><span class="sxs-lookup"><span data-stu-id="c104d-305">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="c104d-306">以下代码将默认文件名更改为 mydefault.html：</span><span class="sxs-lookup"><span data-stu-id="c104d-306">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="c104d-307">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="c104d-307">UseFileServer</span></span>

<span data-ttu-id="c104d-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> 结合了 `UseStaticFiles`、`UseDefaultFiles` 和 `UseDirectoryBrowser`（可选）的功能。</span><span class="sxs-lookup"><span data-stu-id="c104d-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="c104d-309">以下代码提供静态文件和默认文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-309">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="c104d-310">未启用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="c104d-310">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="c104d-311">以下代码通过启用目录浏览基于无参数重载进行构建：</span><span class="sxs-lookup"><span data-stu-id="c104d-311">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="c104d-312">考虑以下目录层次结构：</span><span class="sxs-lookup"><span data-stu-id="c104d-312">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="c104d-313">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="c104d-313">**wwwroot**</span></span>
  * <span data-ttu-id="c104d-314">**css**</span><span class="sxs-lookup"><span data-stu-id="c104d-314">**css**</span></span>
  * <span data-ttu-id="c104d-315">**images**</span><span class="sxs-lookup"><span data-stu-id="c104d-315">**images**</span></span>
  * <span data-ttu-id="c104d-316">**js**</span><span class="sxs-lookup"><span data-stu-id="c104d-316">**js**</span></span>
* <span data-ttu-id="c104d-317">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="c104d-317">**MyStaticFiles**</span></span>
  * <span data-ttu-id="c104d-318">**images**</span><span class="sxs-lookup"><span data-stu-id="c104d-318">**images**</span></span>
    * <span data-ttu-id="c104d-319">banner1.svg</span><span class="sxs-lookup"><span data-stu-id="c104d-319">*banner1.svg*</span></span>
  * <span data-ttu-id="c104d-320">default.html</span><span class="sxs-lookup"><span data-stu-id="c104d-320">*default.html*</span></span>

<span data-ttu-id="c104d-321">以下代码启用静态文件、默认文件和及 `MyStaticFiles` 的目录浏览：</span><span class="sxs-lookup"><span data-stu-id="c104d-321">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="c104d-322">`EnableDirectoryBrowsing` 属性值为 `true` 时必须调用 `AddDirectoryBrowser`：</span><span class="sxs-lookup"><span data-stu-id="c104d-322">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="c104d-323">使用文件层次结构和前面的代码，URL 解析如下：</span><span class="sxs-lookup"><span data-stu-id="c104d-323">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="c104d-324">URI</span><span class="sxs-lookup"><span data-stu-id="c104d-324">URI</span></span>            |                             <span data-ttu-id="c104d-325">响应</span><span class="sxs-lookup"><span data-stu-id="c104d-325">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="c104d-326"> http://\<server_address>/StaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="c104d-326">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="c104d-327">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="c104d-327">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="c104d-328"> http://\<server_address>/StaticFiles</span><span class="sxs-lookup"><span data-stu-id="c104d-328">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="c104d-329">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="c104d-329">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="c104d-330">如果 MyStaticFiles 目录中不存在默认命名文件，则 http://\<server_address>/StaticFiles 返回包含可单击链接的目录列表 ：</span><span class="sxs-lookup"><span data-stu-id="c104d-330">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![静态文件列表](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="c104d-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 和 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> 执行从 `http://{SERVER ADDRESS}/StaticFiles`（不带尾部斜杠）到 `http://{SERVER ADDRESS}/StaticFiles/`（带尾部斜杠）的客户端重定向。</span><span class="sxs-lookup"><span data-stu-id="c104d-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="c104d-333">如果没有尾部斜杠，StaticFiles 目录中的相对 URL 无效。</span><span class="sxs-lookup"><span data-stu-id="c104d-333">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="c104d-334">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="c104d-334">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="c104d-335"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> 类包含 `Mappings` 属性，用作文件扩展名到 MIME 内容类型的映射。</span><span class="sxs-lookup"><span data-stu-id="c104d-335">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="c104d-336">在以下示例中，多个文件扩展名注册到了已知的 MIME 类型。</span><span class="sxs-lookup"><span data-stu-id="c104d-336">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="c104d-337">替换了 .rtf 扩展名，删除了 .mp4 。</span><span class="sxs-lookup"><span data-stu-id="c104d-337">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="c104d-338">请参阅 [MIME 内容类型](https://www.iana.org/assignments/media-types/media-types.xhtml)。</span><span class="sxs-lookup"><span data-stu-id="c104d-338">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="c104d-339">若要了解如何使用自定义 <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>，或者要配置 Blazor Server 应用中的其他 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>，请参阅 <xref:blazor/fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="c104d-339">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="c104d-340">非标准内容类型</span><span class="sxs-lookup"><span data-stu-id="c104d-340">Non-standard content types</span></span>

<span data-ttu-id="c104d-341">静态文件中间件可理解近 400 种已知文件内容类型。</span><span class="sxs-lookup"><span data-stu-id="c104d-341">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="c104d-342">如果用户请求文件类型未知的文件，则静态文件中间件将请求传递给管道中的下一个中间件。</span><span class="sxs-lookup"><span data-stu-id="c104d-342">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="c104d-343">如果没有中间件处理请求，则返回“404 未找到”响应。</span><span class="sxs-lookup"><span data-stu-id="c104d-343">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="c104d-344">如果启用了目录浏览，则在目录列表中会显示该文件的链接。</span><span class="sxs-lookup"><span data-stu-id="c104d-344">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="c104d-345">以下代码提供未知类型，并以图像形式呈现未知文件：</span><span class="sxs-lookup"><span data-stu-id="c104d-345">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="c104d-346">使用前面的代码，请求的文件含未知内容类型时，以图像形式返回请求。</span><span class="sxs-lookup"><span data-stu-id="c104d-346">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="c104d-347">启用 <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> 会形成安全隐患。</span><span class="sxs-lookup"><span data-stu-id="c104d-347">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="c104d-348">它默认处于禁用状态，不建议使用。</span><span class="sxs-lookup"><span data-stu-id="c104d-348">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="c104d-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) 提供了更安全的替代方法来提供含非标准扩展名的文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="c104d-350">从多个位置提供文件</span><span class="sxs-lookup"><span data-stu-id="c104d-350">Serve files from multiple locations</span></span>

<span data-ttu-id="c104d-351">`UseStaticFiles` 和 `UseFileServer` 默认为指向 wwwroot 的文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="c104d-351">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="c104d-352">可使用其他文件提供程序提供 `UseStaticFiles` 和 `UseFileServer` 的其他实例，从多个位置提供文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-352">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="c104d-353">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/15578)。</span><span class="sxs-lookup"><span data-stu-id="c104d-353">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="c104d-354">注意事项</span><span class="sxs-lookup"><span data-stu-id="c104d-354">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="c104d-355">`UseDirectoryBrowser` 和 `UseStaticFiles` 可能会泄漏机密。</span><span class="sxs-lookup"><span data-stu-id="c104d-355">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="c104d-356">强烈建议在生产中禁用目录浏览。</span><span class="sxs-lookup"><span data-stu-id="c104d-356">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="c104d-357">请仔细查看 `UseStaticFiles` 或 `UseDirectoryBrowser` 启用了哪些目录。</span><span class="sxs-lookup"><span data-stu-id="c104d-357">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="c104d-358">整个目录及其子目录均可公开访问。</span><span class="sxs-lookup"><span data-stu-id="c104d-358">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="c104d-359">将适合公开的文件存储在专用目录中，如 \<content_root>/wwwroot。</span><span class="sxs-lookup"><span data-stu-id="c104d-359">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="c104d-360">将这些文件与 MVC 视图、Razor 页面（仅限 2.x）和配置文件等分开。</span><span class="sxs-lookup"><span data-stu-id="c104d-360">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="c104d-361">使用 `UseDirectoryBrowser` 和 `UseStaticFiles` 公开的内容的 URL 受大小写和基础文件系统字符限制的影响。</span><span class="sxs-lookup"><span data-stu-id="c104d-361">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="c104d-362">例如，Windows 不区分大小写 &mdash; macOS 和 Linux 却要区分。</span><span class="sxs-lookup"><span data-stu-id="c104d-362">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="c104d-363">托管于 IIS 中的 ASP.NET Core 应用使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将所有请求转发到应用，包括静态文件请求。</span><span class="sxs-lookup"><span data-stu-id="c104d-363">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="c104d-364">未使用 IIS 静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="c104d-364">The IIS static file handler isn't used.</span></span> <span data-ttu-id="c104d-365">在模块处理请求前，处理程序没有机会处理请求。</span><span class="sxs-lookup"><span data-stu-id="c104d-365">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="c104d-366">在 IIS Manager 中完成以下步骤，删除服务器或网站级别的 IIS 静态文件处理程序：</span><span class="sxs-lookup"><span data-stu-id="c104d-366">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="c104d-367">转到“模块”功能。</span><span class="sxs-lookup"><span data-stu-id="c104d-367">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="c104d-368">在列表中选择 StaticFileModule。</span><span class="sxs-lookup"><span data-stu-id="c104d-368">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="c104d-369">单击“操作”侧栏中的“删除” 。</span><span class="sxs-lookup"><span data-stu-id="c104d-369">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="c104d-370">如果启用了 IIS 静态文件处理程序且 ASP.NET Core 模块配置不正确，则会提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="c104d-370">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="c104d-371">例如，如果未部署 web.config 文件，则会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="c104d-371">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="c104d-372">将代码文件（包括 .cs 和 .cshtml ）放在应用项目的 [Web 根目录](xref:fundamentals/index#web-root)之外 。</span><span class="sxs-lookup"><span data-stu-id="c104d-372">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="c104d-373">这样就在应用的客户端内容和基于服务器的代码间创建了逻辑分隔。</span><span class="sxs-lookup"><span data-stu-id="c104d-373">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="c104d-374">可以防止服务器端代码泄漏。</span><span class="sxs-lookup"><span data-stu-id="c104d-374">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c104d-375">其他资源</span><span class="sxs-lookup"><span data-stu-id="c104d-375">Additional resources</span></span>

* [<span data-ttu-id="c104d-376">中间件</span><span class="sxs-lookup"><span data-stu-id="c104d-376">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c104d-377">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="c104d-377">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
