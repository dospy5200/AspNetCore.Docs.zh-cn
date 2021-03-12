---
title: 使用文件观察程序开发 ASP.NET Core 应用
author: rick-anderson
description: 本教程演示如何在 ASP.NET Core 应用中安装和使用 .NET Core CLI 的文件观察程序 (dotnet watch) 工具。
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 84cae3b3babe28c2ebf6dba50023b020112d1bb3
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587575"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="ec77c-103">使用文件观察程序开发 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="ec77c-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="ec77c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="ec77c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="ec77c-105">`dotnet watch` 是一种在源文件更改时运行 [.NET Core CLI](/dotnet/core/tools) 命令的工具。</span><span class="sxs-lookup"><span data-stu-id="ec77c-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="ec77c-106">例如，文件更改可能触发编译、测试执行或部署。</span><span class="sxs-lookup"><span data-stu-id="ec77c-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="ec77c-107">本教程使用一个现有 Web API 和两个终结点：分别返回两个数的总和以及乘积。</span><span class="sxs-lookup"><span data-stu-id="ec77c-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="ec77c-108">乘积的方法有一个 bug，本教程将会对其进行修复。</span><span class="sxs-lookup"><span data-stu-id="ec77c-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="ec77c-109">下载[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/dotnet-watch/sample)。</span><span class="sxs-lookup"><span data-stu-id="ec77c-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="ec77c-110">它包含两个项目：WebApp (ASP.NET Core Web API) 和 WebAppTests（用于 Web API 的单元测试）。</span><span class="sxs-lookup"><span data-stu-id="ec77c-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="ec77c-111">在命令行界面中，导航到 WebApp 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ec77c-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="ec77c-112">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="ec77c-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="ec77c-113">可使用 `dotnet run --project <PROJECT>` 来指定要运行的项目。</span><span class="sxs-lookup"><span data-stu-id="ec77c-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="ec77c-114">例如，从示例应用的根路径运行 `dotnet run --project WebApp` 还会运行 WebApp 项目。</span><span class="sxs-lookup"><span data-stu-id="ec77c-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="ec77c-115">控制台输出会显示如下类似的消息（表示应用正在运行且正在等待请求）：</span><span class="sxs-lookup"><span data-stu-id="ec77c-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="ec77c-116">在 Web 浏览器中，导航到 `http://localhost:<port number>/api/math/sum?a=4&b=5`。</span><span class="sxs-lookup"><span data-stu-id="ec77c-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="ec77c-117">应该会显示结果 `9`。</span><span class="sxs-lookup"><span data-stu-id="ec77c-117">You should see the result of `9`.</span></span>

<span data-ttu-id="ec77c-118">导航到产品 API (`http://localhost:<port number>/api/math/product?a=4&b=5`)。</span><span class="sxs-lookup"><span data-stu-id="ec77c-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="ec77c-119">它会返回 `9`，而不是所预期的 `20`。</span><span class="sxs-lookup"><span data-stu-id="ec77c-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="ec77c-120">本教程的后面部分将解决该问题。</span><span class="sxs-lookup"><span data-stu-id="ec77c-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="ec77c-121">将 `dotnet watch` 添加到项目</span><span class="sxs-lookup"><span data-stu-id="ec77c-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="ec77c-122">`dotnet watch` 文件观察程序工具随 2.1.300 版本的 .NET Core SDK 一同提供。</span><span class="sxs-lookup"><span data-stu-id="ec77c-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="ec77c-123">使用早期版本的 .NET Core SDK 时，需要执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="ec77c-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="ec77c-124">将 `Microsoft.DotNet.Watcher.Tools` 包引用添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="ec77c-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="ec77c-125">运行以下命令，安装 `Microsoft.DotNet.Watcher.Tools` 包：</span><span class="sxs-lookup"><span data-stu-id="ec77c-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="ec77c-126">使用 `dotnet watch` 运行 .NET Core CLI 命令</span><span class="sxs-lookup"><span data-stu-id="ec77c-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="ec77c-127">`dotnet watch` 可用于运行任何 [.NET Core CLI 命令](/dotnet/core/tools#cli-commands)</span><span class="sxs-lookup"><span data-stu-id="ec77c-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="ec77c-128">例如：</span><span class="sxs-lookup"><span data-stu-id="ec77c-128">For example:</span></span>

| <span data-ttu-id="ec77c-129">命令</span><span class="sxs-lookup"><span data-stu-id="ec77c-129">Command</span></span> | <span data-ttu-id="ec77c-130">带 watch 的命令</span><span class="sxs-lookup"><span data-stu-id="ec77c-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="ec77c-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="ec77c-131">dotnet run</span></span> | <span data-ttu-id="ec77c-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="ec77c-132">dotnet watch run</span></span> |
| <span data-ttu-id="ec77c-133">dotnet run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="ec77c-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="ec77c-134">dotnet watch run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="ec77c-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="ec77c-135">dotnet run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="ec77c-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="ec77c-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="ec77c-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="ec77c-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="ec77c-137">dotnet test</span></span> | <span data-ttu-id="ec77c-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="ec77c-138">dotnet watch test</span></span> |

<span data-ttu-id="ec77c-139">运行“WebApp”文件夹中的 `dotnet watch run`。</span><span class="sxs-lookup"><span data-stu-id="ec77c-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="ec77c-140">控制台输出指示 `watch` 已启动。</span><span class="sxs-lookup"><span data-stu-id="ec77c-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="ec77c-141">在 Web 应用上运行 `dotnet watch run` 将启动一个浏览器，一旦准备就绪，该浏览器将导航到应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="ec77c-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="ec77c-142">`dotnet watch` 通过读取应用的控制台输出并等待 <xref:Microsoft.AspNetCore.WebHost> 显示的就绪消息来完成此操作。</span><span class="sxs-lookup"><span data-stu-id="ec77c-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="ec77c-143">当 `dotnet watch` 检测到监视文件的更改时刷新浏览器。</span><span class="sxs-lookup"><span data-stu-id="ec77c-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="ec77c-144">为此，watch 命令向应用注入一个中间件，用于修改应用创建的 HTML 响应。</span><span class="sxs-lookup"><span data-stu-id="ec77c-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="ec77c-145">中间件将 JavaScript 脚本块添加到页面，使 `dotnet watch` 可以指示浏览器进行刷新。</span><span class="sxs-lookup"><span data-stu-id="ec77c-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="ec77c-146">目前，对所有监视的文件（包括静态内容，如 .html 和 .css）的更改都会导致重建应用 。</span><span class="sxs-lookup"><span data-stu-id="ec77c-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="ec77c-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="ec77c-147">`dotnet watch`:</span></span>

* <span data-ttu-id="ec77c-148">默认情况下，仅监视影响生成的文件。</span><span class="sxs-lookup"><span data-stu-id="ec77c-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="ec77c-149">任何（通过配置）额外监视的文件仍然会导致生成发生。</span><span class="sxs-lookup"><span data-stu-id="ec77c-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="ec77c-150">有关配置的详细信息，请参阅本文档中的 [dotnet-watch 配置](#dotnet-watch-configuration)</span><span class="sxs-lookup"><span data-stu-id="ec77c-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="ec77c-151">可使用 `dotnet watch --project <PROJECT>` 来指定要监视的项目。</span><span class="sxs-lookup"><span data-stu-id="ec77c-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="ec77c-152">例如，从示例应用的根路径运行 `dotnet watch --project WebApp run` 还会运行并监视 WebApp 项目。</span><span class="sxs-lookup"><span data-stu-id="ec77c-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="ec77c-153">使用 `dotnet watch` 执行更改</span><span class="sxs-lookup"><span data-stu-id="ec77c-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="ec77c-154">确保 `dotnet watch` 正在运行。</span><span class="sxs-lookup"><span data-stu-id="ec77c-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="ec77c-155">修复 MathController 的 `Product` 方法中的 bug，使其返回乘积而非总和。</span><span class="sxs-lookup"><span data-stu-id="ec77c-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="ec77c-156">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="ec77c-156">Save the file.</span></span> <span data-ttu-id="ec77c-157">控制台输出指示 `dotnet watch` 已检测到文件更改并已重启应用。</span><span class="sxs-lookup"><span data-stu-id="ec77c-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="ec77c-158">验证 `http://localhost:<port number>/api/math/product?a=4&b=5` 是否返回正确结果。</span><span class="sxs-lookup"><span data-stu-id="ec77c-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="ec77c-159">使用 `dotnet watch` 运行测试</span><span class="sxs-lookup"><span data-stu-id="ec77c-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="ec77c-160">将 MathController.cs 的 `Product` 方法改回返回总和。</span><span class="sxs-lookup"><span data-stu-id="ec77c-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="ec77c-161">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="ec77c-161">Save the file.</span></span>
1. <span data-ttu-id="ec77c-162">在命令外壳中，导航到“WebAppTests”文件夹。</span><span class="sxs-lookup"><span data-stu-id="ec77c-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="ec77c-163">运行 [dotnet restore](/dotnet/core/tools/dotnet-restore)。</span><span class="sxs-lookup"><span data-stu-id="ec77c-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="ec77c-164">运行 `dotnet watch test`。</span><span class="sxs-lookup"><span data-stu-id="ec77c-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="ec77c-165">其输出指示测试失败且观察程序正在等待文件更改：</span><span class="sxs-lookup"><span data-stu-id="ec77c-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="ec77c-166">修复 `Product` 方法代码，使其返回乘积。</span><span class="sxs-lookup"><span data-stu-id="ec77c-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="ec77c-167">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="ec77c-167">Save the file.</span></span>

<span data-ttu-id="ec77c-168">`dotnet watch` 检测到文件更改并重新运行测试。</span><span class="sxs-lookup"><span data-stu-id="ec77c-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="ec77c-169">控制台输出指示测试通过。</span><span class="sxs-lookup"><span data-stu-id="ec77c-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="ec77c-170">自定义要监视的文件列表</span><span class="sxs-lookup"><span data-stu-id="ec77c-170">Customize files list to watch</span></span>

<span data-ttu-id="ec77c-171">默认情况下，`dotnet-watch` 跟踪与以下 glob 模式匹配的所有文件：</span><span class="sxs-lookup"><span data-stu-id="ec77c-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="ec77c-172">通过编辑 .csproj 文件，可向监视列表添加更多项。</span><span class="sxs-lookup"><span data-stu-id="ec77c-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="ec77c-173">可以单独指定项或使用 glob 模式指定。</span><span class="sxs-lookup"><span data-stu-id="ec77c-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="ec77c-174">从要监视的文件中排除</span><span class="sxs-lookup"><span data-stu-id="ec77c-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="ec77c-175">`dotnet-watch` 可配置为忽略其默认设置。</span><span class="sxs-lookup"><span data-stu-id="ec77c-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="ec77c-176">要忽略特定文件，请在 .csproj 文件中向某项的定义中添加 `Watch="false"` 特性：</span><span class="sxs-lookup"><span data-stu-id="ec77c-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="ec77c-177">自定义监视项目</span><span class="sxs-lookup"><span data-stu-id="ec77c-177">Custom watch projects</span></span>

<span data-ttu-id="ec77c-178">`dotnet-watch` 不仅限于 C# 项目。</span><span class="sxs-lookup"><span data-stu-id="ec77c-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="ec77c-179">可以创建自定义监视项目来处理不同的方案。</span><span class="sxs-lookup"><span data-stu-id="ec77c-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="ec77c-180">假设项目布局如下：</span><span class="sxs-lookup"><span data-stu-id="ec77c-180">Consider the following project layout:</span></span>

* <span data-ttu-id="ec77c-181">**test/**</span><span class="sxs-lookup"><span data-stu-id="ec77c-181">**test/**</span></span>
  * <span data-ttu-id="ec77c-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="ec77c-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="ec77c-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="ec77c-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="ec77c-184">如果目标是监视这两个项目，请创建配置为监视这两个项目的自定义项目文件：</span><span class="sxs-lookup"><span data-stu-id="ec77c-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="ec77c-185">要对这两个项目启动文件监视，请更改为 test 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ec77c-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="ec77c-186">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ec77c-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="ec77c-187">当任一测试项目中的任何文件发生更改时，将会执行 VSTest。</span><span class="sxs-lookup"><span data-stu-id="ec77c-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="ec77c-188">dotnet-watch 配置</span><span class="sxs-lookup"><span data-stu-id="ec77c-188">dotnet-watch configuration</span></span>

<span data-ttu-id="ec77c-189">一些配置选项可以通过环境变量传递给 `dotnet watch`。</span><span class="sxs-lookup"><span data-stu-id="ec77c-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="ec77c-190">可用变量有：</span><span class="sxs-lookup"><span data-stu-id="ec77c-190">The available variables are:</span></span>

| <span data-ttu-id="ec77c-191">设置</span><span class="sxs-lookup"><span data-stu-id="ec77c-191">Setting</span></span>  | <span data-ttu-id="ec77c-192">说明</span><span class="sxs-lookup"><span data-stu-id="ec77c-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="ec77c-193">如果设置为“1”或“true”，则 `dotnet watch` 使用轮询文件观察程序，而不是 CoreFx 的 `FileSystemWatcher`。</span><span class="sxs-lookup"><span data-stu-id="ec77c-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="ec77c-194">在监视网络共享或 Docker 装入卷上的文件时使用。</span><span class="sxs-lookup"><span data-stu-id="ec77c-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="ec77c-195">默认情况下，`dotnet watch` 通过避免某些操作（如在每次文件更改时运行还原或重新评估监视的文件集）来优化生成。</span><span class="sxs-lookup"><span data-stu-id="ec77c-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="ec77c-196">如果设置为“1”或“true”，则禁用这些优化。</span><span class="sxs-lookup"><span data-stu-id="ec77c-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="ec77c-197">`dotnet watch run` 尝试为在 launchSettings.json 中配置了 `launchBrowser` 的 Web 应用启动浏览器。</span><span class="sxs-lookup"><span data-stu-id="ec77c-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="ec77c-198">如果设置为“1”或“true”，则抑制此行为。</span><span class="sxs-lookup"><span data-stu-id="ec77c-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="ec77c-199">`dotnet watch run` 检测到文件更改时尝试刷新浏览器。</span><span class="sxs-lookup"><span data-stu-id="ec77c-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="ec77c-200">如果设置为“1”或“true”，则抑制此行为。</span><span class="sxs-lookup"><span data-stu-id="ec77c-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="ec77c-201">如果设置了 `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`，则也会抑制此行为。</span><span class="sxs-lookup"><span data-stu-id="ec77c-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |
