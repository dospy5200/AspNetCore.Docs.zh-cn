---
title: 在 Visual Studio 中将 LibMan 与 ASP.NET Core 配合使用
author: scottaddie
description: 了解如何通过 Visual Studio 在 ASP.NET Core 项目中使用 LibMan。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: ae2bc34edaea2df6f329e47b00c7c02cc59d03bd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589265"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>在 Visual Studio 中将 LibMan 与 ASP.NET Core 配合使用

作者：[Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio 对 ASP.NET Core 项目中的 [LibMan](xref:client-side/libman/index) 提供内置支持，包括：

* 支持在生成时配置和运行 LibMan 还原操作。
* 用于触发 LibMan 还原和清理操作的菜单项。
* 用于查找库并将文件添加到项目的搜索对话框。
* 编辑对 libman.json（LibMan 清单文件）的支持。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/client-side/libman/samples/)[（如何下载）](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>先决条件

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载

## <a name="add-library-files"></a>添加库文件

可以通过两种不同的方式将库文件添加到 ASP.NET Core 项目中：

1. [使用“添加客户端库”对话框](#use-the-add-client-side-library-dialog)
1. [手动配置 LibMan 清单文件项](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>使用“添加客户端库”对话框

按照以下步骤安装客户端库：

* 在“解决方案资源管理器”中，右键单击要在其中添加文件的项目文件夹。 选择“添加” > “客户端库” 。 此时将显示“添加客户端库”对话框：

  ![“添加客户端库”对话框](_static/add-library-dialog.png)

* 从“提供程序”下拉列表中选择库提供程序。 CDNJS 是默认提供程序。
* 在“库”文本框中键入要提取的库名称。 IntelliSense 提供以所提供文本开头的库的列表。
* 从 IntelliSense 列表中选择库。 请注意，库名称后缀带有 `@` 符号和所选提供程序的已知最新稳定版本。
* 确定要包含的文件：
  * 选择“包含所有库文件”单选按钮以包含所有库文件。
  * 选择“选择特定文件”单选按钮以包含库文件的子集。 选中此单选按钮后，将启用文件选择器树。 选中要下载的文件名左侧的框。
* 在“目标位置”文本框中指定项目文件夹以存储文件。 建议将每个库存储在单独的文件夹中。

  建议的“目标位置”文件夹基于启动对话框的位置：

  * 如果从项目根启动：
    * 如果 wwwroot 存在，则使用 wwwroot/lib 。
    * 如果 wwwroot 不存在，则使用 lib 。
  * 如果从项目文件夹启动，则使用相应的文件夹名称。

  文件夹建议带有库名称后缀。 下表说明了在 Razor Pages 项目中安装 jQuery 时的文件夹建议。
  
  |启动位置                           |建议的文件夹      |
  |------------------------------------------|----------------------|
  |项目根（如果 wwwroot 存在）        |wwwroot/lib/jquery/ |
  |项目根（如果 wwwroot 不存在） |lib/jquery/         |
  |项目中的 Pages 文件夹                 |Pages/jquery/       |

* 单击“安装”按钮，根据 libman.json 中的配置下载文件。
* 有关安装详细信息，请查看“输出”窗口的“库管理器”源 。 例如：

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>手动配置 LibMan 清单文件项

Visual Studio 中的所有 LibMan 操作都基于项目根的 LibMan 清单 (libman.json) 的内容。 可以手动编辑 libman.json，以配置项目的库文件。 保存 libman.json 后，Visual Studio 将还原所有库文件。

若要打开 libman.json 进行编辑，可以选择以下选项：

* 在“解决方案资源管理器”中双击 libman.json 文件。
* 右键单击“解决方案资源管理器”中的项目，然后选择“管理客户端库” 。 &#8224;
* 从 Visual Studio“项目”菜单中选择“管理客户端库” 。 &#8224;

&#8224; 如果项目根中不存在 libman.json 文件，则将使用默认项模板内容创建该文件。

Visual Studio 提供丰富的 JSON 编辑支持，如着色、格式设置、IntelliSense 和架构验证。 LibMan 清单的 JSON 架构位于 [https://json.schemastore.org/libman](https://json.schemastore.org/libman)。

对于下面的清单文件，LibMan 根据 `libraries` 属性中定义的配置检索文件。 `libraries` 中定义的对象文字的说明如下所示：

* 从 CDNJS 提供程序检索 [jQuery](https://jquery.com/) 版本 3.3.1 的子集。 子集在 `files` 属性中定义&mdash;jquery node.js、jquery.js 和 jquery.min.map  。 文件位于项目的 wwwroot/lib/jquery 文件夹中。
* 检索整个[启动](https://getbootstrap.com/)版本 4.1.3，并放入 wwwroot/lib/bootstrap 文件夹中。 对象文字的 `provider` 属性将覆盖 `defaultProvider` 属性值。 LibMan 从 unpkg 提供程序检索启动文件。
* 组织内的管理正文已批准 [Lodash](https://lodash.com/) 的子集。 从本地文件系统 C:\\temp\\lodash\\ 检索 lodash.js 和 lodash.min.js 文件  。 文件将复制到项目的 wwwroot/lib/lodash 文件夹中。

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan 仅支持每个提供程序的每个库的一个版本。 如果 libman.json 文件包含两个库，并且库名称与给定提供程序的库名称相同，则架构验证失败。

## <a name="restore-library-files"></a>还原库文件

若要从 Visual Studio 中还原库文件，项目根中必须有一个有效的 libman.json 文件。 还原的文件放置在项目中每个库指定的位置。

可以通过两种方式在 ASP.NET Core 项目中还原库文件：

1. [在生成过程中还原文件](#restore-files-during-build)
1. [手动还原文件](#restore-files-manually)

### <a name="restore-files-during-build"></a>在生成过程中还原文件

LibMan 可以在生成过程中还原定义的库文件。 默认情况下，禁用 restore-on-build 行为。

启用和测试 restore-on-build 行为：

* 右键单击“解决方案资源管理器”中的 libman.json，然后从上下文菜单中选择“生成时启用还原客户端库” 。
* 系统提示安装 NuGet 包时，单击“是”按钮。 将 [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet 包添加到项目中：

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* 生成项目以确认 LibMan 文件已还原。 `Microsoft.Web.LibraryManager.Build` 包将插入在项目的生成操作期间运行 LibMan 的 MSBuild 目标。
* 查看 LibMan 活动日志“输出”窗口的“生成”源 ：

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

当启用 restore-on-build 行为时，libman.json 上下文菜单将显示“生成时禁用还原客户端库”选项。 选择此选项将从项目文件中删除 `Microsoft.Web.LibraryManager.Build` 包引用。 因此，在每次生成时将不再还原客户端库。

无论 restore-on-build 设置如何，都可以随时从 libman.json 上下文菜单中手动进行还原。 有关详细信息，请参阅[还原文件](#restore-files-manually)。

### <a name="restore-files-manually"></a>手动还原文件

手动还原库文件：

* 对于解决方案中的所有项目：
  * 右键单击“解决方案资源管理器”中的解决方案名称。
  * 选择“还原客户端库”选项。
* 对于特定项目：
  * 右键单击“解决方案资源管理器”中的 libman.json 文件。
  * 选择“还原客户端库”选项。

正在运行还原操作时：

* 将对 Visual Studio 状态栏上的任务状态中心 (TSC) 图标进行动画处理，并将显示“已启动还原操作”。 单击该图标将打开一个工具提示，其中列出了已知的后台任务。
* 消息将发送到状态栏和“输出”窗口的“库管理器”源 。 例如：

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>删除库文件

执行清理操作，该操作将删除之前在 Visual Studio 中还原的库文件：

* 右键单击“解决方案资源管理器”中的 libman.json 文件。
* 选择“清理客户端库”选项。

为防止意外删除非库文件，清理操作不会删除整个目录。 它仅删除先前还原中包含的文件。

正在运行清理操作时：

* 将对 Visual Studio 状态栏上的 TSC 图标进行动画处理，并将显示“已启动客户端库操作”。 单击该图标将打开一个工具提示，其中列出了已知的后台任务。
* 将消息发送到状态栏和“输出”窗口的“库管理器”源 。 例如：

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

清理操作仅从项目中删除文件。 库文件保留在缓存中，以便在将来的还原操作中更快地检索。 若要管理存储在本地计算机缓存中的库文件，请使用 [LibMan CLI](xref:client-side/libman/libman-cli)。

## <a name="uninstall-library-files"></a>卸载库文件

卸载库文件：

* 打开 libman.json。
* 将插入符号置于相应的 `libraries` 对象文字内。
* 单击左边距中显示的灯泡图标，然后选择“卸载 \<library_name>@\<library_version>”：

  ![卸载库上下文菜单选项](_static/uninstall-menu-option.png)

或者可以手动编辑并保存 LibMan 清单 (libman.json)。 保存文件后将运行[还原操作](#restore-library-files)。 libman.json 中不再定义的库文件将从项目中删除。

## <a name="update-library-version"></a>更新库版本

检查更新的库版本：

* 打开 libman.json。
* 将插入符号置于相应的 `libraries` 对象文字内。
* 单击左边距中显示的灯泡图标。 将鼠标悬停在“检查更新”上。

LibMan 检查库版本是否比安装的版本更新。 可能出现以下结果：

* 如果已安装最新版本，则不会显示“找不到更新”消息。
* 如果尚未安装，则会显示最新的稳定版本。

  ![检查更新上下文菜单选项](_static/update-menu-option.png)

* 如果有比已安装版本更新的预发行版可用，则会显示该预发行版。

若要降级到较旧的库版本，请手动编辑 libman.json 文件。 保存文件后，LibMan [还原操作](#restore-library-files)将执行以下操作：

* 删除先前版本中的冗余文件。
* 从新版本添加新文件和更新文件。

## <a name="additional-resources"></a>其他资源

* <xref:client-side/libman/libman-cli>
* [LibMan GitHub 存储库](https://github.com/aspnet/LibraryManager)
