---
title: 结合使用 ASP.NET Core SignalR 和 Blazor
author: guardrex
description: 创建结合使用 ASP.NET Core SignalR 和 Blazor 的聊天应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: e9bb79eb5bd8f56fe8014981de76e210eb35c2de
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589525"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="3b336-103">结合使用 ASP.NET Core SignalR 和 Blazor</span><span class="sxs-lookup"><span data-stu-id="3b336-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="3b336-104">本教程介绍结合使用 SignalR 和 Blazor 生成实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="3b336-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="3b336-105">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="3b336-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3b336-106">创建 Blazor 项目</span><span class="sxs-lookup"><span data-stu-id="3b336-106">Create a Blazor project</span></span>
> * <span data-ttu-id="3b336-107">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="3b336-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="3b336-108">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="3b336-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="3b336-109">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="3b336-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="3b336-110">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="3b336-110">Add Razor component code for chat</span></span>

<span data-ttu-id="3b336-111">在本教程结束时，你将拥有一个正常运行的聊天应用。</span><span class="sxs-lookup"><span data-stu-id="3b336-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="3b336-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-blazor/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3b336-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3b336-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="3b336-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b336-115">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.8 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="3b336-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3b336-118">Visual Studio for Mac 8.8 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3b336-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-119">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3b336-121">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.6 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="3b336-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3b336-124">Visual Studio for Mac 版本 8.6 或更高版本</span><span class="sxs-lookup"><span data-stu-id="3b336-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="3b336-126">创建托管 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="3b336-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="3b336-127">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="3b336-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3b336-129">需要 Visual Studio 16.8 或更高版本以及 .NET Core SDK 5.0.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3b336-130">需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="3b336-131">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="3b336-131">Create a new project.</span></span>

1. <span data-ttu-id="3b336-132">选择“Blazor 应用”，然后选择“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="3b336-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="3b336-133">在“项目名称”字段中键入 `BlazorWebAssemblySignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="3b336-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="3b336-134">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="3b336-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3b336-135">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3b336-135">Select **Create**.</span></span>

1. <span data-ttu-id="3b336-136">选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="3b336-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="3b336-137">在“高级”下选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="3b336-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="3b336-138">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3b336-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3b336-140">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="3b336-141">`-ho|--hosted` 选项将创建托管 Blazor WebAssembly 解决方案。</span><span class="sxs-lookup"><span data-stu-id="3b336-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="3b336-142">有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。</span><span class="sxs-lookup"><span data-stu-id="3b336-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="3b336-143">`-o|--output` 选项为解决方案创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b336-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="3b336-144">如果已为解决方案创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建解决方案的 `-o|--output` 选项和值。</span><span class="sxs-lookup"><span data-stu-id="3b336-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="3b336-145">在 Visual Studio Code 中打开应用的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b336-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="3b336-146">当显示添加资产以生成和调试应用的对话框时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="3b336-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="3b336-147">Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="3b336-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3b336-149">安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="3b336-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="3b336-150">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="3b336-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3b336-151">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="3b336-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="3b336-152">选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="3b336-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3b336-153">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="3b336-153">Select **Next**.</span></span>

1. <span data-ttu-id="3b336-154">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="3b336-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="3b336-155">选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="3b336-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="3b336-156">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="3b336-156">Select **Next**.</span></span>

1. <span data-ttu-id="3b336-157">在“项目名称”字段中，将应用命名为 `BlazorWebAssemblySignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="3b336-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="3b336-158">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3b336-158">Select **Create**.</span></span>

   <span data-ttu-id="3b336-159">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="3b336-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3b336-160">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="3b336-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="3b336-161">通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。</span><span class="sxs-lookup"><span data-stu-id="3b336-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3b336-163">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="3b336-164">`-ho|--hosted` 选项将创建托管 Blazor WebAssembly 解决方案。</span><span class="sxs-lookup"><span data-stu-id="3b336-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="3b336-165">`-o|--output` 选项为解决方案创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b336-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="3b336-166">如果已为解决方案创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建解决方案的 `-o|--output` 选项和值。</span><span class="sxs-lookup"><span data-stu-id="3b336-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3b336-167">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="3b336-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3b336-169">在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3b336-170">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3b336-171">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3b336-172">在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包。</span><span class="sxs-lookup"><span data-stu-id="3b336-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="3b336-173">将版本设置为与应用共享框架匹配的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="3b336-174">选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="3b336-174">Select **Install**.</span></span>

1. <span data-ttu-id="3b336-175">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="3b336-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3b336-176">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="3b336-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3b336-178">在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="3b336-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="3b336-179">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-180">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3b336-181">在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3b336-182">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3b336-183">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3b336-184">在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包旁边的复选框。</span><span class="sxs-lookup"><span data-stu-id="3b336-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="3b336-185">将版本设置为与应用共享框架匹配的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="3b336-186">选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="3b336-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="3b336-187">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="3b336-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-188">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3b336-189">在解决方案文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="3b336-190">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="3b336-191">添加 System.Text.Encodings.Web 包</span><span class="sxs-lookup"><span data-stu-id="3b336-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="3b336-192">本部分仅适用于 ASP.NET Core 版本 3.x 的应用。</span><span class="sxs-lookup"><span data-stu-id="3b336-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="3b336-193">由于在 ASP.NET Core 3.x 应用中使用 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x 时出现包解析问题，`BlazorWebAssemblySignalRApp.Server` 项目需要 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="3b336-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="3b336-194">在未来的 .NET 5 修补程序版本中，将解决基础问题。</span><span class="sxs-lookup"><span data-stu-id="3b336-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="3b336-195">有关详细信息，请参阅 [System.Text.Json 定义无依赖项的 netcoreapp3.0（dotnet/运行时 #45560）](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="3b336-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="3b336-196">若要将 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 添加到 ASP.NET Core 3.1 托管 Blazor 解决方案的 `BlazorWebAssemblySignalRApp.Server` 项目，请按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="3b336-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3b336-198">在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Server` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3b336-199">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3b336-200">选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="3b336-201">在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包。</span><span class="sxs-lookup"><span data-stu-id="3b336-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="3b336-202">选择与正在使用的共享框架相匹配的包版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="3b336-203">选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="3b336-203">Select **Install**.</span></span>

1. <span data-ttu-id="3b336-204">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="3b336-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3b336-205">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="3b336-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3b336-207">在“集成终端”（工具栏上的“视图”>“终端”）中，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="3b336-208">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3b336-210">在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Server` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3b336-211">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3b336-212">选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="3b336-213">在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包旁边的复选框，选择与正在使用的共享框架相匹配的包的正确版本，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="3b336-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="3b336-214">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="3b336-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-215">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3b336-216">在解决方案文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="3b336-217">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="3b336-218">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="3b336-218">Add a SignalR hub</span></span>

<span data-ttu-id="3b336-219">在 `BlazorWebAssemblySignalRApp.Server` 项目中，创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：</span><span class="sxs-lookup"><span data-stu-id="3b336-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="3b336-220">为 SignalR 中心添加服务和终结点</span><span class="sxs-lookup"><span data-stu-id="3b336-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="3b336-221">在 `BlazorWebAssemblySignalRApp.Server` 项目中打开 `Startup.cs` 文件。</span><span class="sxs-lookup"><span data-stu-id="3b336-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="3b336-222">将 `ChatHub` 类的命名空间添加到文件顶部：</span><span class="sxs-lookup"><span data-stu-id="3b336-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="3b336-223">将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="3b336-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="3b336-224">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="3b336-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3b336-225">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="3b336-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3b336-226">在控制器终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="3b336-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="3b336-227">将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="3b336-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="3b336-228">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="3b336-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3b336-229">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="3b336-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3b336-230">在控制器终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="3b336-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="3b336-231">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="3b336-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="3b336-232">在 `BlazorWebAssemblySignalRApp.Client` 项目中打开 `Pages/Index.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="3b336-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="3b336-233">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="3b336-233">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="3b336-234">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="3b336-234">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="3b336-235">运行应用</span><span class="sxs-lookup"><span data-stu-id="3b336-235">Run the app</span></span>

<span data-ttu-id="3b336-236">按照工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="3b336-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3b336-238">在“解决方案资源管理器”中，选择 `BlazorWebAssemblySignalRApp.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="3b336-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="3b336-239">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="3b336-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3b336-240">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="3b336-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3b336-241">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="3b336-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3b336-242">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="3b336-242">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3b336-244">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3b336-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3b336-246">有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。</span><span class="sxs-lookup"><span data-stu-id="3b336-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="3b336-247">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="3b336-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3b336-248">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="3b336-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3b336-249">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="3b336-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3b336-250">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="3b336-250">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3b336-252">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3b336-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-253">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3b336-254">在“解决方案”边栏中，选择 `BlazorWebAssemblySignalRApp.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="3b336-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="3b336-255">按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="3b336-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3b336-256">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="3b336-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3b336-257">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="3b336-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3b336-258">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="3b336-258">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3b336-260">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3b336-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="3b336-262">在解决方案文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="3b336-263">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="3b336-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3b336-264">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="3b336-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3b336-265">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="3b336-265">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3b336-267">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3b336-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="3b336-268">创建 Blazor Server 应用</span><span class="sxs-lookup"><span data-stu-id="3b336-268">Create a Blazor Server app</span></span>

<span data-ttu-id="3b336-269">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="3b336-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3b336-271">需要 Visual Studio 16.8 或更高版本以及 .NET Core SDK 5.0.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="3b336-272">需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="3b336-273">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="3b336-273">Create a new project.</span></span>

1. <span data-ttu-id="3b336-274">选择“Blazor 应用”，然后选择“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="3b336-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="3b336-275">在“项目名称”字段中键入 `BlazorServerSignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="3b336-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="3b336-276">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="3b336-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3b336-277">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3b336-277">Select **Create**.</span></span>

1. <span data-ttu-id="3b336-278">选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="3b336-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="3b336-279">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3b336-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3b336-281">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="3b336-282">`-o|--output` 选项为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b336-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="3b336-283">如果已为项目创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建项目的 `-o|--output` 选项和值。</span><span class="sxs-lookup"><span data-stu-id="3b336-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="3b336-284">在 Visual Studio Code 中打开应用的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b336-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="3b336-285">当显示添加资产以生成和调试应用的对话框时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="3b336-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="3b336-286">Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="3b336-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="3b336-287">有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。</span><span class="sxs-lookup"><span data-stu-id="3b336-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-288">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3b336-289">安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="3b336-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="3b336-290">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="3b336-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3b336-291">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="3b336-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="3b336-292">选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="3b336-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3b336-293">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="3b336-293">Select **Next**.</span></span>

1. <span data-ttu-id="3b336-294">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="3b336-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="3b336-295">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="3b336-295">Select **Next**.</span></span>

1. <span data-ttu-id="3b336-296">在“项目名称”字段中，将应用命名为 `BlazorServerSignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="3b336-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="3b336-297">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="3b336-297">Select **Create**.</span></span>

   <span data-ttu-id="3b336-298">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="3b336-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3b336-299">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="3b336-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="3b336-300">通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。</span><span class="sxs-lookup"><span data-stu-id="3b336-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-301">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3b336-302">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="3b336-303">`-o|--output` 选项为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="3b336-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="3b336-304">如果已为项目创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建项目的 `-o|--output` 选项和值。</span><span class="sxs-lookup"><span data-stu-id="3b336-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3b336-305">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="3b336-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3b336-307">在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3b336-308">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3b336-309">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3b336-310">在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包。</span><span class="sxs-lookup"><span data-stu-id="3b336-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="3b336-311">将版本设置为与应用共享框架匹配的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="3b336-312">选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="3b336-312">Select **Install**.</span></span>

1. <span data-ttu-id="3b336-313">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="3b336-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3b336-314">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="3b336-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3b336-316">在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="3b336-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="3b336-317">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-318">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3b336-319">在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3b336-320">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3b336-321">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="3b336-322">在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包旁边的复选框。</span><span class="sxs-lookup"><span data-stu-id="3b336-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="3b336-323">将版本设置为与应用共享框架匹配的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="3b336-324">选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="3b336-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="3b336-325">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="3b336-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-326">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3b336-327">在项目文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="3b336-328">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="3b336-329">添加 System.Text.Encodings.Web 包</span><span class="sxs-lookup"><span data-stu-id="3b336-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="3b336-330">本部分仅适用于 ASP.NET Core 版本 3.x 的应用。</span><span class="sxs-lookup"><span data-stu-id="3b336-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="3b336-331">由于在 ASP.NET Core 3.x 应用中使用 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x 时出现包解析问题，项目需要 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="3b336-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="3b336-332">在未来的 .NET 5 修补程序版本中，将解决基础问题。</span><span class="sxs-lookup"><span data-stu-id="3b336-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="3b336-333">有关详细信息，请参阅 [System.Text.Json 定义无依赖项的 netcoreapp3.0（dotnet/运行时 #45560）](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="3b336-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="3b336-334">若要将 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 添加到项目，请按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="3b336-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="3b336-336">在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3b336-337">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3b336-338">选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="3b336-339">在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包。</span><span class="sxs-lookup"><span data-stu-id="3b336-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="3b336-340">选择与正在使用的共享框架相匹配的包版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="3b336-341">选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="3b336-341">Select **Install**.</span></span>

1. <span data-ttu-id="3b336-342">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="3b336-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="3b336-343">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="3b336-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="3b336-345">在“集成终端”（工具栏上的“视图”>“终端”）中，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="3b336-346">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-347">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3b336-348">在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="3b336-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="3b336-349">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="3b336-350">选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。</span><span class="sxs-lookup"><span data-stu-id="3b336-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="3b336-351">在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包旁边的复选框，选择与正在使用的共享框架相匹配的包的正确版本，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="3b336-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="3b336-352">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="3b336-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-353">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3b336-354">在项目文件夹的命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="3b336-355">若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。</span><span class="sxs-lookup"><span data-stu-id="3b336-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="3b336-356">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="3b336-356">Add a SignalR hub</span></span>

<span data-ttu-id="3b336-357">创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：</span><span class="sxs-lookup"><span data-stu-id="3b336-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="3b336-358">为 SignalR 中心添加服务和终结点</span><span class="sxs-lookup"><span data-stu-id="3b336-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="3b336-359">打开 `Startup.cs` 文件。</span><span class="sxs-lookup"><span data-stu-id="3b336-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="3b336-360">将 <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> 和 `ChatHub` 类的命名空间添加到文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="3b336-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="3b336-361">将响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="3b336-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="3b336-362">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="3b336-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3b336-363">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="3b336-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3b336-364">在映射 Blazor 中心的终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="3b336-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="3b336-365">将响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="3b336-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="3b336-366">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="3b336-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="3b336-367">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="3b336-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="3b336-368">在映射 Blazor 中心的终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="3b336-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="3b336-369">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="3b336-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="3b336-370">打开 `Pages/Index.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="3b336-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="3b336-371">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="3b336-371">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="3b336-372">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="3b336-372">Replace the markup with the following code:</span></span>

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="3b336-373">运行应用</span><span class="sxs-lookup"><span data-stu-id="3b336-373">Run the app</span></span>

<span data-ttu-id="3b336-374">按照工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="3b336-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3b336-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3b336-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3b336-376">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="3b336-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3b336-377">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="3b336-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3b336-378">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="3b336-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3b336-379">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="3b336-379">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3b336-381">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3b336-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3b336-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3b336-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3b336-383">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="3b336-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3b336-384">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="3b336-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3b336-385">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="3b336-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3b336-386">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="3b336-386">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3b336-388">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3b336-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3b336-389">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3b336-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3b336-390">按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="3b336-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="3b336-391">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="3b336-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3b336-392">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="3b336-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3b336-393">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="3b336-393">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3b336-395">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3b336-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3b336-396">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3b336-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="3b336-397">在命令行界面中从项目文件夹执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="3b336-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="3b336-398">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="3b336-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="3b336-399">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="3b336-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="3b336-400">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="3b336-400">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="3b336-402">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="3b336-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="3b336-403">后续步骤</span><span class="sxs-lookup"><span data-stu-id="3b336-403">Next steps</span></span>

<span data-ttu-id="3b336-404">在本教程中，你了解了如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3b336-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3b336-405">创建 Blazor 项目</span><span class="sxs-lookup"><span data-stu-id="3b336-405">Create a Blazor project</span></span>
> * <span data-ttu-id="3b336-406">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="3b336-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="3b336-407">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="3b336-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="3b336-408">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="3b336-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="3b336-409">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="3b336-409">Add Razor component code for chat</span></span>

<span data-ttu-id="3b336-410">若要了解有关生成 Blazor 应用的详细信息，请参阅 Blazor 文档：</span><span class="sxs-lookup"><span data-stu-id="3b336-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="3b336-411"><xref:blazor/index>
> [具有 Identity 服务器、WebSocket 和服务器发送事件的持有者令牌身份验证](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="3b336-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b336-412">其他资源</span><span class="sxs-lookup"><span data-stu-id="3b336-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="3b336-413">SignalR 用于身份验证的跨源协商</span><span class="sxs-lookup"><span data-stu-id="3b336-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
