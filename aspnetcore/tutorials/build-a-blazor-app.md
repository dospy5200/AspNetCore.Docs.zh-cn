---
title: 生成 Blazor 待办事项列表应用
author: guardrex
description: 逐步生成 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2021
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 260d921316d6fadecbd42db11048593b19a5ddee
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394429"
---
# <a name="build-a-blazor-todo-list-app"></a>生成 Blazor 待办事项列表应用

本教程演示如何生成和修改 Blazor 应用。 您将学习如何：

> [!div class="checklist"]
> * 创建待办事项列表 Blazor 应用项目
> * 修改 Razor 组件
> * 在组件中使用事件处理和数据绑定
> * 在 Blazor 应用中使用路由

在本教程结束时，你将拥有一个正常运行的待办事项列表应用。

## <a name="prerequisites"></a>必备条件

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a>创建待办事项列表 Blazor 应用

1. 在命令行界面中创建名为 `TodoList` 的新 Blazor 应用：

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   上述命令将创建一个带有 `-o|--output` 选项的 `TodoList` 文件夹来保存应用。 `TodoList` 文件夹是项目的根文件夹。 使用以下命令将目录切换到 `TodoList` 文件夹：

   ```dotnetcli
   cd TodoList
   ```

1. 使用以下命令向应用添加一个新的 `Todo` Razor 组件：

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   上述命令中的 `-n|--name` 指定了新的 Razor 组件的名称。 新组件是在项目具有 `-o|--output` 选项的 `Pages` 文件夹中创建的。

   > [!IMPORTANT]
   > Razor 组件文件名要求首字母大写。 打开 `Pages` 文件夹，确认 `Todo` 组件文件名以大写字母 `T` 开头。 文件名应为 `Todo.razor`。

1. 在任何文件编辑器中打开 `Todo`，添加 `@page` Razor 指令添加到具有 `/todo` 的相对 URL 的文件顶部。

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   保存 `Pages/Todo.razor` 文件。

1. 将 `Todo` 组件添加到导航栏。

   `NavMenu` 组件用于应用的布局。 布局是可避免应用中出现重复内容的组件。 当应用加载组件 URL 时，`NavLink` 组件会在应用的 UI 中提供提示。

   在 `NavMenu` 组件的未排序列表 (`<ul>...</ul>`) 中，为 `Todo` 组件添加以下列表项 (`<li>...</li>`) 和 `NavLink` 组件。

   在 `Shared/NavMenu.razor`中：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   保存 `Shared/NavMenu.razor` 文件。

1. 从 `TodoList` 文件夹，在命令行界面中执行 [`dotnet watch run`](xref:tutorials/dotnet-watch) 命令，以生成并运行应用。 应用运行后，请在应用的导航栏中选择 `Todo` 链接来访问新的 Todo 页面，该链接将在 `/todo` 处加载页面。

   让应用继续运行命令行界面。 每次保存文件时，都将自动重新生成应用。 在编译和重启时，浏览器会暂时断开与应用的连接。 重新建立连接后，会自动重新加载浏览器中的页面。

1. 向项目的根目录添加 `TodoItem.cs` 文件（`TodoList` 文件夹），以保存一个用于表示待办项的类。 为 `TodoItem` 类使用以下 C# 代码。

   `TodoItem.cs`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > 如果使用 Visual Studio 创建 `TodoItem.cs` 文件和 `TodoItem` 类，请使用以下方法之一：
   >
   > * 删除 Visual Studio 为类生成的命名空间。
   > * 使用前面代码块中的“复制”按钮，并替换 Visual Studio 生成的文件的全部内容。

1. 返回 `Todo` 组件并执行以下任务：

   * 在 `@code` 块中为待办项添加一个字段。 `Todo` 组件使用此字段来维护待办项列表的状态。
   * 添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项 (`<li>`)。

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. 该应用需要 UI 元素来将待办项添加到列表。 在未排序列表 (`<ul>...</ul>`) 下方添加一个文本输入 (`<input>`) 和一个按钮 (`<button>`)：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. 保存 `TodoItem.cs` 文件和更新的 `Pages/Todo.razor` 文件。 在命令行界面中，保存文件时，将自动重新生成应用。 浏览器会暂时断开与该应用的连接，并在重新建立连接后重新加载页面。

1. 选择“`Add todo`”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。

1. 向 `Todo` 组件添加 `AddTodo` 方法，并使用 `@onclick` 属性注册该方法来选择按钮。 选择按钮时，会调用 `AddTodo` C# 方法：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. 若要获取新的待办项的标题，请在 `@code` 块的顶部添加一个 `newTodo` 字符串：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   修改文本 `<input>` 元素，使用 `@bind` 属性来绑定 `newTodo`：

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. 更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。 通过将 `newTodo` 设置为空字符串来清除文本输入的值：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. 保存 `Pages/Todo.razor` 文件。 应用程序会在命令行界面中自动重新生成。 浏览器重新连接到应用后，页面会在浏览器中重新加载。

1. 每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。 为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。 将 `@todo.Title` 更改为 `<input>` 元素，后者通过 `@bind` 绑定到 `todo.Title`：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. 更新 `<h3>` 标头，显示尚未完成的待办项数目（`IsDone` 是 `false`）。

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. 已完成的 `Todo` 组件 (`Pages/Todo.razor`)：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. 保存 `Pages/Todo.razor` 文件。 应用程序会在命令行界面中自动重新生成。 浏览器重新连接到应用后，页面会在浏览器中重新加载。

1. 添加项、编辑项，并将待办标记为“已完成”来测试组件。

1. 完成后，请在命令行界面中关闭应用。 许多命令行界面接受键盘命令 <kbd>Ctrl</kbd>+<kbd>c</kbd> 来停止应用。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建待办事项列表 Blazor 应用项目
> * 修改 Razor 组件
> * 在组件中使用事件处理和数据绑定
> * 在 Blazor 应用中使用路由

了解用于 ASP.NET Core Blazor 的工具：

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
