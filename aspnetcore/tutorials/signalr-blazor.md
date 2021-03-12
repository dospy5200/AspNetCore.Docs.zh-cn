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
# <a name="use-aspnet-core-signalr-with-blazor"></a>结合使用 ASP.NET Core SignalR 和 Blazor

本教程介绍结合使用 SignalR 和 Blazor 生成实时应用的基础知识。 您将学习如何：

> [!div class="checklist"]
> * 创建 Blazor 项目
> * 添加 SignalR 客户端库
> * 添加 SignalR 集线器
> * 添加 SignalR 服务和 SignalR 中心的终结点
> * 添加用于聊天的 Razor 组件代码

在本教程结束时，你将拥有一个正常运行的聊天应用。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/signalr-blazor/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.8 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [Visual Studio for Mac 8.8 或更高版本](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.6 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [Visual Studio for Mac 版本 8.6 或更高版本](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a>创建托管 Blazor WebAssembly 应用

按照所选工具的指南进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 需要 Visual Studio 16.8 或更高版本以及 .NET Core SDK 5.0.0 或更高版本。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> 需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。

::: moniker-end

1. 创建新项目。

1. 选择“Blazor 应用”，然后选择“下一步”。 

1. 在“项目名称”字段中键入 `BlazorWebAssemblySignalRApp`。 确认“位置”条目正确无误或为项目提供位置。 选择“创建”。

1. 选择“Blazor WebAssembly 应用”模板。

1. 在“高级”下选中“托管的 ASP.NET Core”复选框。

1. 选择“创建”。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   `-ho|--hosted` 选项将创建托管 Blazor WebAssembly 解决方案。 有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。

   `-o|--output` 选项为解决方案创建文件夹。 如果已为解决方案创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建解决方案的 `-o|--output` 选项和值。

1. 在 Visual Studio Code 中打开应用的项目文件夹。

1. 当显示添加资产以生成和调试应用的对话框时，选择“是”。 Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：

1. 选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。

1. 在边栏中，选择“Web 和控制台” > “应用”。 

1. 选择“Blazor WebAssembly 应用”模板。 选择“下一步”。

1. 确认已将“身份验证”设置为“无身份验证”。 选中“托管的 ASP.NET Core”复选框。 选择“下一步”。

1. 在“项目名称”字段中，将应用命名为 `BlazorWebAssemblySignalRApp`。 选择“创建”。

   如果出现信任开发证书的提示，请信任证书并继续操作。 信任证书需要使用用户密码和密钥链密码。

1. 通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在命令行界面中执行以下命令：

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

`-ho|--hosted` 选项将创建托管 Blazor WebAssembly 解决方案。

`-o|--output` 选项为解决方案创建文件夹。 如果已为解决方案创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建解决方案的 `-o|--output` 选项和值。

---

## <a name="add-the-signalr-client-library"></a>添加 SignalR 客户端库

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。

1. 在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。

1. 选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。

1. 在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包。 将版本设置为与应用共享框架匹配的版本。 选择“安装”  。

1. 如果出现“预览更改”对话框，则选择“确定”。

1. 如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令  ：

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。

1. 在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。

1. 选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。

1. 在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包旁边的复选框。 将版本设置为与应用共享框架匹配的版本。 选择“添加包”。

1. 出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在解决方案文件夹的命令行界面中执行以下命令：

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>添加 System.Text.Encodings.Web 包

本部分仅适用于 ASP.NET Core 版本 3.x 的应用。

由于在 ASP.NET Core 3.x 应用中使用 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x 时出现包解析问题，`BlazorWebAssemblySignalRApp.Server` 项目需要 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 的包引用。 在未来的 .NET 5 修补程序版本中，将解决基础问题。 有关详细信息，请参阅 [System.Text.Json 定义无依赖项的 netcoreapp3.0（dotnet/运行时 #45560）](https://github.com/dotnet/runtime/issues/45560)。

若要将 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 添加到 ASP.NET Core 3.1 托管 Blazor 解决方案的 `BlazorWebAssemblySignalRApp.Server` 项目，请按照所选工具的指导进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Server` 项目，然后选择“管理 NuGet 包” 。

1. 在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。

1. 选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。

1. 在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包。 选择与正在使用的共享框架相匹配的包版本。 选择“安装”  。

1. 如果出现“预览更改”对话框，则选择“确定”。

1. 如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在“集成终端”（工具栏上的“视图”>“终端”）中，执行以下命令：

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在“解决方案资源管理器”中，右键单击 `BlazorWebAssemblySignalRApp.Server` 项目，然后选择“管理 NuGet 包” 。

1. 在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。

1. 选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。

1. 在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包旁边的复选框，选择与正在使用的共享框架相匹配的包的正确版本，然后选择“添加包”。

1. 出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在解决方案文件夹的命令行界面中执行以下命令：

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>添加 SignalR 集线器

在 `BlazorWebAssemblySignalRApp.Server` 项目中，创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>为 SignalR 中心添加服务和终结点

1. 在 `BlazorWebAssemblySignalRApp.Server` 项目中打开 `Startup.cs` 文件。

1. 将 `ChatHub` 类的命名空间添加到文件顶部：

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. 将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. 在 `Startup.Configure`中：

   * 使用处理管道的配置顶部的“响应压缩中间件”。
   * 在控制器终结点和客户端回退之间，为中心添加一个终结点。

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. 在 `Startup.Configure`中：

   * 使用处理管道的配置顶部的“响应压缩中间件”。
   * 在控制器终结点和客户端回退之间，为中心添加一个终结点。

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>添加用于聊天的 Razor 组件代码

1. 在 `BlazorWebAssemblySignalRApp.Client` 项目中打开 `Pages/Index.razor` 文件。

::: moniker range=">= aspnetcore-5.0"

1. 将标记替换为以下代码：

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 将标记替换为以下代码：

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>运行应用

按照工具的指南进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 在“解决方案资源管理器”中，选择 `BlazorWebAssemblySignalRApp.Server` 项目。 按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择按钮发送消息。 两个页面上立即显示名称和消息：

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。

1. 按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择按钮发送消息。 两个页面上立即显示名称和消息：

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在“解决方案”边栏中，选择 `BlazorWebAssemblySignalRApp.Server` 项目。 按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择按钮发送消息。 两个页面上立即显示名称和消息：

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 在解决方案文件夹的命令行界面中执行以下命令：

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择按钮发送消息。 两个页面上立即显示名称和消息：

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a>创建 Blazor Server 应用

按照所选工具的指南进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> 需要 Visual Studio 16.8 或更高版本以及 .NET Core SDK 5.0.0 或更高版本。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> 需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。

::: moniker-end

1. 创建新项目。

1. 选择“Blazor 应用”，然后选择“下一步”。 

1. 在“项目名称”字段中键入 `BlazorServerSignalRApp`。 确认“位置”条目正确无误或为项目提供位置。 选择“创建”。

1. 选择“Blazor Server 应用”模板。

1. 选择“创建”。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   `-o|--output` 选项为项目创建文件夹。 如果已为项目创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建项目的 `-o|--output` 选项和值。

1. 在 Visual Studio Code 中打开应用的项目文件夹。

1. 当显示添加资产以生成和调试应用的对话框时，选择“是”。 Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。 有关配置 `.vscode` 文件夹中 VS Code 资产的信息，请参阅 <xref:blazor/tooling> 中的 Linux 操作系统指南。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：

1. 选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。

1. 在边栏中，选择“Web 和控制台” > “应用”。 

1. 选择“Blazor Server 应用”模板。 选择“下一步”。

1. 确认已将“身份验证”设置为“无身份验证”。 选择“下一步”。

1. 在“项目名称”字段中，将应用命名为 `BlazorServerSignalRApp`。 选择“创建”。

   如果出现信任开发证书的提示，请信任证书并继续操作。 信任证书需要使用用户密码和密钥链密码。

1. 通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在命令行界面中执行以下命令：

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

`-o|--output` 选项为项目创建文件夹。 如果已为项目创建了文件夹，并在该文件夹中打开了命令行界面，那么请忽略用于创建项目的 `-o|--output` 选项和值。

---

## <a name="add-the-signalr-client-library"></a>添加 SignalR 客户端库

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。

1. 在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。

1. 选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。

1. 在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包。 将版本设置为与应用共享框架匹配的版本。 选择“安装”  。

1. 如果出现“预览更改”对话框，则选择“确定”。

1. 如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令  ：

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。

1. 在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。

1. 选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。

1. 在搜索结果中，选择 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包旁边的复选框。 将版本设置为与应用共享框架匹配的版本。 选择“添加包”。

1. 出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在项目文件夹的命令行界面中执行以下命令：

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>添加 System.Text.Encodings.Web 包

本部分仅适用于 ASP.NET Core 版本 3.x 的应用。

由于在 ASP.NET Core 3.x 应用中使用 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x 时出现包解析问题，项目需要 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 的包引用。 在未来的 .NET 5 修补程序版本中，将解决基础问题。 有关详细信息，请参阅 [System.Text.Json 定义无依赖项的 netcoreapp3.0（dotnet/运行时 #45560）](https://github.com/dotnet/runtime/issues/45560)。

若要将 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 添加到项目，请按照所选工具的指南进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. 在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。

1. 在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。

1. 选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。

1. 在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包。 选择与正在使用的共享框架相匹配的包版本。 选择“安装”  。

1. 如果出现“预览更改”对话框，则选择“确定”。

1. 如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

在“集成终端”（工具栏上的“视图”>“终端”）中，执行以下命令：

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在“解决方案资源管理器”中，右键单击 `BlazorServerSignalRApp` 项目，然后选择“管理 NuGet 包” 。

1. 在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。

1. 选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。

1. 在搜索结果中，选择 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包旁边的复选框，选择与正在使用的共享框架相匹配的包的正确版本，然后选择“添加包”。

1. 出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

在项目文件夹的命令行界面中执行以下命令：

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

若要添加较早版本的包，请提供 `--version {VERSION}` 选项，其中 `{VERSION}` 占位符为要添加的包的版本。

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>添加 SignalR 集线器

创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>为 SignalR 中心添加服务和终结点

1. 打开 `Startup.cs` 文件。

1. 将 <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> 和 `ChatHub` 类的命名空间添加到文件的顶部：

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. 将响应压缩中间件服务添加到 `Startup.ConfigureServices`：

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. 在 `Startup.Configure`中：

   * 使用处理管道的配置顶部的“响应压缩中间件”。
   * 在映射 Blazor 中心的终结点和客户端回退之间，为中心添加一个终结点。

   [!code-csharp[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 将响应压缩中间件服务添加到 `Startup.ConfigureServices`：

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. 在 `Startup.Configure`中：

   * 使用处理管道的配置顶部的“响应压缩中间件”。
   * 在映射 Blazor 中心的终结点和客户端回退之间，为中心添加一个终结点。

   [!code-csharp[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>添加用于聊天的 Razor 组件代码

1. 打开 `Pages/Index.razor` 文件。

::: moniker range=">= aspnetcore-5.0"

1. 将标记替换为以下代码：

   [!code-razor[](~/tutorials/signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. 将标记替换为以下代码：

   [!code-razor[](~/tutorials/signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>运行应用

按照工具的指南进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择按钮发送消息。 两个页面上立即显示名称和消息：

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择按钮发送消息。 两个页面上立即显示名称和消息：

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择按钮发送消息。 两个页面上立即显示名称和消息：

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 在命令行界面中从项目文件夹执行以下命令：

   ```dotnetcli
   dotnet run
   ```

1. 从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。

1. 选择任一浏览器，输入名称和消息，然后选择按钮发送消息。 两个页面上立即显示名称和消息：

   ![SignalR Blazor 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor/_static/signalr-blazor-finished.png)

   Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Blazor 项目
> * 添加 SignalR 客户端库
> * 添加 SignalR 集线器
> * 添加 SignalR 服务和 SignalR 中心的终结点
> * 添加用于聊天的 Razor 组件代码

若要了解有关生成 Blazor 应用的详细信息，请参阅 Blazor 文档：

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [具有 Identity 服务器、WebSocket 和服务器发送事件的持有者令牌身份验证](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>其他资源

* <xref:signalr/introduction>
* [SignalR 用于身份验证的跨源协商](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
