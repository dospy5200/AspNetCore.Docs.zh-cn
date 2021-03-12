---
title: ASP.NET Core 中的文件提供程序
author: rick-anderson
description: 了解 ASP.NET Core 如何通过文件提供程序来抽象化文件系统访问。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: c66c35e93991333229e367e9f371b125d8067131
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588212"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="eb62e-103">ASP.NET Core 中的文件提供程序</span><span class="sxs-lookup"><span data-stu-id="eb62e-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="eb62e-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="eb62e-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb62e-105">ASP.NET Core 通过文件提供程序来抽象化文件系统访问。</span><span class="sxs-lookup"><span data-stu-id="eb62e-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="eb62e-106">在 ASP.NET Core 框架中使用文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="eb62e-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="eb62e-107">例如：</span><span class="sxs-lookup"><span data-stu-id="eb62e-107">For example:</span></span>

* <span data-ttu-id="eb62e-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 将应用的[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)作为 `IFileProvider` 类型公开。</span><span class="sxs-lookup"><span data-stu-id="eb62e-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="eb62e-109">[静态文件中间件](xref:fundamentals/static-files)使用文件提供程序来查找静态文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="eb62e-110">[Razor](xref:mvc/views/razor) 使用文件提供程序来查找页面和视图。</span><span class="sxs-lookup"><span data-stu-id="eb62e-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="eb62e-111">.NET Core 工具使用文件提供程序和 glob 模式来指定应该发布哪些文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="eb62e-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/file-providers/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="eb62e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="eb62e-113">文件提供程序接口</span><span class="sxs-lookup"><span data-stu-id="eb62e-113">File Provider interfaces</span></span>

<span data-ttu-id="eb62e-114">主接口为 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="eb62e-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="eb62e-115">`IFileProvider` 公开方法以实现以下目的：</span><span class="sxs-lookup"><span data-stu-id="eb62e-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="eb62e-116">获取文件信息 (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="eb62e-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="eb62e-117">获取目录信息 (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="eb62e-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="eb62e-118">设置更改通知（使用 <xref:Microsoft.Extensions.Primitives.IChangeToken>）。</span><span class="sxs-lookup"><span data-stu-id="eb62e-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="eb62e-119">`IFileInfo` 提供用于处理文件的方法和属性：</span><span class="sxs-lookup"><span data-stu-id="eb62e-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="eb62e-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>（以字节为单位）</span><span class="sxs-lookup"><span data-stu-id="eb62e-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="eb62e-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 日期</span><span class="sxs-lookup"><span data-stu-id="eb62e-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="eb62e-122">可以使用 <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> 方法从该文件读取数据。</span><span class="sxs-lookup"><span data-stu-id="eb62e-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="eb62e-123">FileProviderSample 示例应用演示了如何在 `Startup.ConfigureServices` 中配置文件提供程序，以便通过[依赖项注入](xref:fundamentals/dependency-injection)在应用中使用。</span><span class="sxs-lookup"><span data-stu-id="eb62e-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="eb62e-124">文件提供程序实现</span><span class="sxs-lookup"><span data-stu-id="eb62e-124">File Provider implementations</span></span>

<span data-ttu-id="eb62e-125">下表列出了 `IFileProvider` 的实现。</span><span class="sxs-lookup"><span data-stu-id="eb62e-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="eb62e-126">实现</span><span class="sxs-lookup"><span data-stu-id="eb62e-126">Implementation</span></span> | <span data-ttu-id="eb62e-127">描述</span><span class="sxs-lookup"><span data-stu-id="eb62e-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="eb62e-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="eb62e-129">用于提供对一个或多个其他提供程序中的文件和目录的合并访问。</span><span class="sxs-lookup"><span data-stu-id="eb62e-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="eb62e-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="eb62e-131">用于访问嵌入在程序集中的文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="eb62e-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="eb62e-133">用于访问系统的物理文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="eb62e-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-134">PhysicalFileProvider</span></span>

<span data-ttu-id="eb62e-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供对物理文件系统的访问。</span><span class="sxs-lookup"><span data-stu-id="eb62e-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="eb62e-136">`PhysicalFileProvider` 使用 <xref:System.IO.File?displayProperty=fullName> 类型（适用于物理提供程序）并将所有路径范围限定在一个目录及其子目录中。</span><span class="sxs-lookup"><span data-stu-id="eb62e-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="eb62e-137">此范围界定可防止访问指定目录和子目录之外的文件系统。</span><span class="sxs-lookup"><span data-stu-id="eb62e-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="eb62e-138">创建和使用 `PhysicalFileProvider` 的最常见方案是通过[依赖关系注入](xref:fundamentals/dependency-injection)请求构造函数中的 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="eb62e-139">直接实例化此提供程序时，必须提供绝对目录路径并将其作为使用提供程序发出的所有请求的基路径。</span><span class="sxs-lookup"><span data-stu-id="eb62e-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="eb62e-140">此目录路径中不支持 glob 模式。</span><span class="sxs-lookup"><span data-stu-id="eb62e-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="eb62e-141">以下代码演示如何使用 `PhysicalFileProvider` 来获取目录内容和文件信息：</span><span class="sxs-lookup"><span data-stu-id="eb62e-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="eb62e-142">前面的示例中的类型：</span><span class="sxs-lookup"><span data-stu-id="eb62e-142">Types in the preceding example:</span></span>

* <span data-ttu-id="eb62e-143">`provider` 是 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="eb62e-144">`contents` 是 `IDirectoryContents`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="eb62e-145">`fileInfo` 是 `IFileInfo`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="eb62e-146">文件提供程序可用于循环访问 `applicationRoot` 指定的目录或调用 `GetFileInfo` 来获取文件信息。</span><span class="sxs-lookup"><span data-stu-id="eb62e-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="eb62e-147">glob 模式无法传递给 `GetFileInfo` 方法。</span><span class="sxs-lookup"><span data-stu-id="eb62e-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="eb62e-148">该文件提供程序无法访问 `applicationRoot` 目录外部的任何内容。</span><span class="sxs-lookup"><span data-stu-id="eb62e-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="eb62e-149">FileProviderSample 示例应用使用 <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> 在 `Startup.ConfigureServices` 方法中创建该提供程序：</span><span class="sxs-lookup"><span data-stu-id="eb62e-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="eb62e-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="eb62e-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> 用于访问嵌入在程序集中的文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="eb62e-152">`ManifestEmbeddedFileProvider` 使用编译到程序集中的某个清单来重建嵌入的文件的原始路径。</span><span class="sxs-lookup"><span data-stu-id="eb62e-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="eb62e-153">若要生成嵌入文件的清单，请按照以下步骤操作：</span><span class="sxs-lookup"><span data-stu-id="eb62e-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="eb62e-154">将 [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet 包添加到项目中。</span><span class="sxs-lookup"><span data-stu-id="eb62e-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="eb62e-155">将 `<GenerateEmbeddedFilesManifest>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="eb62e-156">指定要使用 [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) 嵌入的文件：</span><span class="sxs-lookup"><span data-stu-id="eb62e-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="eb62e-157">使用 [glob 模式](#glob-patterns)指定要嵌入到程序集中的一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="eb62e-158">FileProviderSample 示例应用创建 `ManifestEmbeddedFileProvider`，并将当前正在执行的程序集传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="eb62e-159">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb62e-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="eb62e-160">其他重载使你能够：</span><span class="sxs-lookup"><span data-stu-id="eb62e-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="eb62e-161">指定相对文件路径。</span><span class="sxs-lookup"><span data-stu-id="eb62e-161">Specify a relative file path.</span></span>
* <span data-ttu-id="eb62e-162">将文件范围限制到上次修改日期。</span><span class="sxs-lookup"><span data-stu-id="eb62e-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="eb62e-163">为包含嵌入文件清单的嵌入资源命名。</span><span class="sxs-lookup"><span data-stu-id="eb62e-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="eb62e-164">重载</span><span class="sxs-lookup"><span data-stu-id="eb62e-164">Overload</span></span> | <span data-ttu-id="eb62e-165">描述</span><span class="sxs-lookup"><span data-stu-id="eb62e-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="eb62e-166">接受一个可选的 `root` 相对路径参数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="eb62e-167">指定 `root` 将对 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> 的调用范围限制为提供的路径下的那些资源。</span><span class="sxs-lookup"><span data-stu-id="eb62e-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="eb62e-168">接受一个可选的 `root` 相对路径参数和一个 `lastModified` 日期 (<xref:System.DateTimeOffset>) 参数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="eb62e-169">`lastModified` 日期限制 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 返回的 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 实例的上次修改日期范围。</span><span class="sxs-lookup"><span data-stu-id="eb62e-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="eb62e-170">接受一个可选的 `root` 相对路径、`lastModified` 日期和 `manifestName` 参数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="eb62e-171">`manifestName` 表示包含清单的嵌入资源的名称。</span><span class="sxs-lookup"><span data-stu-id="eb62e-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="eb62e-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-172">CompositeFileProvider</span></span>

<span data-ttu-id="eb62e-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 合并 `IFileProvider` 实例，以便公开一个接口来处理多个提供程序中的文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="eb62e-174">创建 `CompositeFileProvider` 时，将一个或多个 `IFileProvider` 实例传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="eb62e-175">在 FileProviderSample 示例应用中，`PhysicalFileProvider` 和 `ManifestEmbeddedFileProvider` 向在应用的服务容器中注册的 `CompositeFileProvider` 提供文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="eb62e-176">以下代码可在项目的 `Startup.ConfigureServices` 方法中找到：</span><span class="sxs-lookup"><span data-stu-id="eb62e-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="eb62e-177">监视更改</span><span class="sxs-lookup"><span data-stu-id="eb62e-177">Watch for changes</span></span>

<span data-ttu-id="eb62e-178"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> 方法提供一种方法来监视一个或多个文件或目录的更改。</span><span class="sxs-lookup"><span data-stu-id="eb62e-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="eb62e-179">`Watch` 方法：</span><span class="sxs-lookup"><span data-stu-id="eb62e-179">The `Watch` method:</span></span>

* <span data-ttu-id="eb62e-180">接受文件路径字符串（可以使用 [glob 模式](#glob-patterns)指定多个文件）。</span><span class="sxs-lookup"><span data-stu-id="eb62e-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="eb62e-181">返回 <xref:Microsoft.Extensions.Primitives.IChangeToken>。</span><span class="sxs-lookup"><span data-stu-id="eb62e-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="eb62e-182">生成的更改令牌会公开以下内容：</span><span class="sxs-lookup"><span data-stu-id="eb62e-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="eb62e-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>：可检查此属性以确定是否已发生更改。</span><span class="sxs-lookup"><span data-stu-id="eb62e-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="eb62e-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>：检测到指定的路径字符串发生更改时调用此属性。</span><span class="sxs-lookup"><span data-stu-id="eb62e-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="eb62e-185">每个更改令牌仅调用其关联的回调来响应单个更改。</span><span class="sxs-lookup"><span data-stu-id="eb62e-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="eb62e-186">若要启用持续监视，请使用 <xref:System.Threading.Tasks.TaskCompletionSource`1>（如下所示）或重新创建 `IChangeToken` 实例以响应更改。</span><span class="sxs-lookup"><span data-stu-id="eb62e-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="eb62e-187">每当 TextFiles 目录中的 .txt 文件被修改时，WatchConsole 示例应用都会写入一条消息  ：</span><span class="sxs-lookup"><span data-stu-id="eb62e-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="eb62e-188">某些文件系统（例如 Docker 容器和网络共享）可能无法可靠地发送更改通知。</span><span class="sxs-lookup"><span data-stu-id="eb62e-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="eb62e-189">将 `DOTNET_USE_POLLING_FILE_WATCHER` 环境变量设置为 `1` 或 `true` 以每隔四秒轮询一次文件系统，确认是否发生更改（不可配置）。</span><span class="sxs-lookup"><span data-stu-id="eb62e-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="eb62e-190">glob 模式</span><span class="sxs-lookup"><span data-stu-id="eb62e-190">Glob patterns</span></span>

<span data-ttu-id="eb62e-191">文件系统路径使用名为 glob（或通配）模式的通配符模式。</span><span class="sxs-lookup"><span data-stu-id="eb62e-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="eb62e-192">使用这些模式指定文件的组。</span><span class="sxs-lookup"><span data-stu-id="eb62e-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="eb62e-193">两个通配符分别是 `*` 和 `**`：</span><span class="sxs-lookup"><span data-stu-id="eb62e-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="eb62e-194">匹配当前文件夹级别的任何内容、任何文件名或任何文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="eb62e-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="eb62e-195">匹配由文件路径中的 `/` 和 `.` 字符终止。</span><span class="sxs-lookup"><span data-stu-id="eb62e-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="eb62e-196">匹配多个目录级别的任何内容。</span><span class="sxs-lookup"><span data-stu-id="eb62e-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="eb62e-197">可用于以递归方式匹配目录层次结构中的许多文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="eb62e-198">下表提供了 glob 模式的常见示例。</span><span class="sxs-lookup"><span data-stu-id="eb62e-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="eb62e-199">模式</span><span class="sxs-lookup"><span data-stu-id="eb62e-199">Pattern</span></span>  |<span data-ttu-id="eb62e-200">描述</span><span class="sxs-lookup"><span data-stu-id="eb62e-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="eb62e-201">匹配特定目录中的特定文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="eb62e-202">匹配特定目录中带 .txt 扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="eb62e-203">匹配正好位于“目录”文件夹中下一级目录中的所有 appsettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="eb62e-204">匹配在 directory 文件夹下任何位置找到的带 .txt 扩展名的所有文件 。</span><span class="sxs-lookup"><span data-stu-id="eb62e-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb62e-205">ASP.NET Core 通过文件提供程序来抽象化文件系统访问。</span><span class="sxs-lookup"><span data-stu-id="eb62e-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="eb62e-206">在 ASP.NET Core 框架中使用文件提供程序：</span><span class="sxs-lookup"><span data-stu-id="eb62e-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="eb62e-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> 将应用的[内容根目录](xref:fundamentals/index#content-root)和 [Web 根目录](xref:fundamentals/index#web-root)作为 `IFileProvider` 类型公开。</span><span class="sxs-lookup"><span data-stu-id="eb62e-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="eb62e-208">[静态文件中间件](xref:fundamentals/static-files)使用文件提供程序来查找静态文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="eb62e-209">[Razor](xref:mvc/views/razor) 使用文件提供程序来查找页面和视图。</span><span class="sxs-lookup"><span data-stu-id="eb62e-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="eb62e-210">.NET Core 工具使用文件提供程序和 glob 模式来指定应该发布哪些文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="eb62e-211">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/file-providers/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="eb62e-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="eb62e-212">文件提供程序接口</span><span class="sxs-lookup"><span data-stu-id="eb62e-212">File Provider interfaces</span></span>

<span data-ttu-id="eb62e-213">主接口为 <xref:Microsoft.Extensions.FileProviders.IFileProvider>。</span><span class="sxs-lookup"><span data-stu-id="eb62e-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="eb62e-214">`IFileProvider` 公开方法以实现以下目的：</span><span class="sxs-lookup"><span data-stu-id="eb62e-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="eb62e-215">获取文件信息 (<xref:Microsoft.Extensions.FileProviders.IFileInfo>)。</span><span class="sxs-lookup"><span data-stu-id="eb62e-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="eb62e-216">获取目录信息 (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>)。</span><span class="sxs-lookup"><span data-stu-id="eb62e-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="eb62e-217">设置更改通知（使用 <xref:Microsoft.Extensions.Primitives.IChangeToken>）。</span><span class="sxs-lookup"><span data-stu-id="eb62e-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="eb62e-218">`IFileInfo` 提供用于处理文件的方法和属性：</span><span class="sxs-lookup"><span data-stu-id="eb62e-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="eb62e-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>（以字节为单位）</span><span class="sxs-lookup"><span data-stu-id="eb62e-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="eb62e-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 日期</span><span class="sxs-lookup"><span data-stu-id="eb62e-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="eb62e-221">可以使用 [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 方法从文件读取内容。</span><span class="sxs-lookup"><span data-stu-id="eb62e-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="eb62e-222">示例应用演示了如何在 `Startup.ConfigureServices` 中配置文件提供程序，以便通过[依赖关系注入](xref:fundamentals/dependency-injection)在应用中使用。</span><span class="sxs-lookup"><span data-stu-id="eb62e-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="eb62e-223">文件提供程序实现</span><span class="sxs-lookup"><span data-stu-id="eb62e-223">File Provider implementations</span></span>

<span data-ttu-id="eb62e-224">`IFileProvider` 有三种实现。</span><span class="sxs-lookup"><span data-stu-id="eb62e-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="eb62e-225">实现</span><span class="sxs-lookup"><span data-stu-id="eb62e-225">Implementation</span></span> | <span data-ttu-id="eb62e-226">描述</span><span class="sxs-lookup"><span data-stu-id="eb62e-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="eb62e-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="eb62e-228">物理提供程序用于访问系统的物理文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="eb62e-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="eb62e-230">清单嵌入式提供程序用于访问程序集中嵌入的文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="eb62e-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="eb62e-232">复合式提供程序提供对一个或多个其他提供程序中的文件和目录的合并访问。</span><span class="sxs-lookup"><span data-stu-id="eb62e-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="eb62e-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-233">PhysicalFileProvider</span></span>

<span data-ttu-id="eb62e-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 提供对物理文件系统的访问。</span><span class="sxs-lookup"><span data-stu-id="eb62e-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="eb62e-235">`PhysicalFileProvider` 使用 <xref:System.IO.File?displayProperty=fullName> 类型（适用于物理提供程序）并将所有路径范围限定在一个目录及其子目录中。</span><span class="sxs-lookup"><span data-stu-id="eb62e-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="eb62e-236">此范围界定可防止访问指定目录和子目录之外的文件系统。</span><span class="sxs-lookup"><span data-stu-id="eb62e-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="eb62e-237">创建和使用 `PhysicalFileProvider` 的最常见方案是通过[依赖关系注入](xref:fundamentals/dependency-injection)请求构造函数中的 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="eb62e-238">直接实例化此提供程序时，必须提供目录路径并将其作为使用提供程序发出的所有请求的基路径。</span><span class="sxs-lookup"><span data-stu-id="eb62e-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="eb62e-239">下面的代码演示如何创建 `PhysicalFileProvider` 并用它来获取目录内容和文件信息：</span><span class="sxs-lookup"><span data-stu-id="eb62e-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="eb62e-240">前面的示例中的类型：</span><span class="sxs-lookup"><span data-stu-id="eb62e-240">Types in the preceding example:</span></span>

* <span data-ttu-id="eb62e-241">`provider` 是 `IFileProvider`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="eb62e-242">`contents` 是 `IDirectoryContents`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="eb62e-243">`fileInfo` 是 `IFileInfo`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="eb62e-244">文件提供程序可用于循环访问 `applicationRoot` 指定的目录或调用 `GetFileInfo` 来获取文件信息。</span><span class="sxs-lookup"><span data-stu-id="eb62e-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="eb62e-245">该文件提供程序无法访问 `applicationRoot` 目录外部的任何内容。</span><span class="sxs-lookup"><span data-stu-id="eb62e-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="eb62e-246">示例应用使用 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) 在应用的 `Startup.ConfigureServices` 类中创建提供程序：</span><span class="sxs-lookup"><span data-stu-id="eb62e-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="eb62e-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="eb62e-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> 用于访问嵌入在程序集中的文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="eb62e-249">`ManifestEmbeddedFileProvider` 使用编译到程序集中的某个清单来重建嵌入的文件的原始路径。</span><span class="sxs-lookup"><span data-stu-id="eb62e-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="eb62e-250">若要生成嵌入的文件清单，请将 `<GenerateEmbeddedFilesManifest>` 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="eb62e-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="eb62e-251">指定要使用 [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) 嵌入的文件：</span><span class="sxs-lookup"><span data-stu-id="eb62e-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="eb62e-252">使用 [glob 模式](#glob-patterns)指定要嵌入到程序集中的一个或多个文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="eb62e-253">示例应用创建 `ManifestEmbeddedFileProvider` 并将当前正在执行的程序集传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="eb62e-254">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="eb62e-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="eb62e-255">其他重载使你能够：</span><span class="sxs-lookup"><span data-stu-id="eb62e-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="eb62e-256">指定相对文件路径。</span><span class="sxs-lookup"><span data-stu-id="eb62e-256">Specify a relative file path.</span></span>
* <span data-ttu-id="eb62e-257">将文件范围限制到上次修改日期。</span><span class="sxs-lookup"><span data-stu-id="eb62e-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="eb62e-258">为包含嵌入文件清单的嵌入资源命名。</span><span class="sxs-lookup"><span data-stu-id="eb62e-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="eb62e-259">重载</span><span class="sxs-lookup"><span data-stu-id="eb62e-259">Overload</span></span> | <span data-ttu-id="eb62e-260">描述</span><span class="sxs-lookup"><span data-stu-id="eb62e-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="eb62e-261">接受一个可选的 `root` 相对路径参数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="eb62e-262">指定 `root` 将对 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> 的调用范围限制为提供的路径下的那些资源。</span><span class="sxs-lookup"><span data-stu-id="eb62e-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="eb62e-263">接受一个可选的 `root` 相对路径参数和一个 `lastModified` 日期 (<xref:System.DateTimeOffset>) 参数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="eb62e-264">`lastModified` 日期限制 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 返回的 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 实例的上次修改日期范围。</span><span class="sxs-lookup"><span data-stu-id="eb62e-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="eb62e-265">接受一个可选的 `root` 相对路径、`lastModified` 日期和 `manifestName` 参数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="eb62e-266">`manifestName` 表示包含清单的嵌入资源的名称。</span><span class="sxs-lookup"><span data-stu-id="eb62e-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="eb62e-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="eb62e-267">CompositeFileProvider</span></span>

<span data-ttu-id="eb62e-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 合并 `IFileProvider` 实例，以便公开一个接口来处理多个提供程序中的文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="eb62e-269">创建 `CompositeFileProvider` 时，将一个或多个 `IFileProvider` 实例传递给其构造函数。</span><span class="sxs-lookup"><span data-stu-id="eb62e-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="eb62e-270">在示例应用中，`PhysicalFileProvider` 和 `ManifestEmbeddedFileProvider` 向在应用的服务容器中注册的 `CompositeFileProvider` 提供文件：</span><span class="sxs-lookup"><span data-stu-id="eb62e-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="eb62e-271">监视更改</span><span class="sxs-lookup"><span data-stu-id="eb62e-271">Watch for changes</span></span>

<span data-ttu-id="eb62e-272">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 方法提供了一个方案来监视一个或多个文件或目录是否发生更改。</span><span class="sxs-lookup"><span data-stu-id="eb62e-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="eb62e-273">`Watch` 接受路径字符串，它可以使用 [glob 模式](#glob-patterns)指定多个文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="eb62e-274">`Watch` 返回 <xref:Microsoft.Extensions.Primitives.IChangeToken>。</span><span class="sxs-lookup"><span data-stu-id="eb62e-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="eb62e-275">更改令牌会公开以下内容：</span><span class="sxs-lookup"><span data-stu-id="eb62e-275">The change token exposes:</span></span>

* <span data-ttu-id="eb62e-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>：可检查此属性以确定是否已发生更改。</span><span class="sxs-lookup"><span data-stu-id="eb62e-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="eb62e-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>：检测到指定的路径字符串发生更改时调用此属性。</span><span class="sxs-lookup"><span data-stu-id="eb62e-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="eb62e-278">每个更改令牌仅调用其关联的回调来响应单个更改。</span><span class="sxs-lookup"><span data-stu-id="eb62e-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="eb62e-279">若要启用持续监视，请使用 <xref:System.Threading.Tasks.TaskCompletionSource`1>（如下所示）或重新创建 `IChangeToken` 实例以响应更改。</span><span class="sxs-lookup"><span data-stu-id="eb62e-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="eb62e-280">在示例应用中，WatchConsole 控制台应用配置为每次修改了文本文件时显示一条消息：</span><span class="sxs-lookup"><span data-stu-id="eb62e-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="eb62e-281">某些文件系统（例如 Docker 容器和网络共享）可能无法可靠地发送更改通知。</span><span class="sxs-lookup"><span data-stu-id="eb62e-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="eb62e-282">将 `DOTNET_USE_POLLING_FILE_WATCHER` 环境变量设置为 `1` 或 `true` 以每隔四秒轮询一次文件系统，确认是否发生更改（不可配置）。</span><span class="sxs-lookup"><span data-stu-id="eb62e-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="eb62e-283">glob 模式</span><span class="sxs-lookup"><span data-stu-id="eb62e-283">Glob patterns</span></span>

<span data-ttu-id="eb62e-284">文件系统路径使用名为 glob（或通配）模式的通配符模式。</span><span class="sxs-lookup"><span data-stu-id="eb62e-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="eb62e-285">使用这些模式指定文件的组。</span><span class="sxs-lookup"><span data-stu-id="eb62e-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="eb62e-286">两个通配符分别是 `*` 和 `**`：</span><span class="sxs-lookup"><span data-stu-id="eb62e-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="eb62e-287">匹配当前文件夹级别的任何内容、任何文件名或任何文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="eb62e-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="eb62e-288">匹配由文件路径中的 `/` 和 `.` 字符终止。</span><span class="sxs-lookup"><span data-stu-id="eb62e-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="eb62e-289">匹配多个目录级别的任何内容。</span><span class="sxs-lookup"><span data-stu-id="eb62e-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="eb62e-290">可用于以递归方式匹配目录层次结构中的许多文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="eb62e-291">**glob 模式示例**</span><span class="sxs-lookup"><span data-stu-id="eb62e-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="eb62e-292">匹配特定目录中的特定文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="eb62e-293">匹配特定目录中带 .txt 扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="eb62e-294">匹配正好位于“目录”文件夹中下一级目录中的所有 `appsettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="eb62e-295">匹配在“目录”文件夹下任何位置找到的带 .txt 扩展名的所有文件。</span><span class="sxs-lookup"><span data-stu-id="eb62e-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
