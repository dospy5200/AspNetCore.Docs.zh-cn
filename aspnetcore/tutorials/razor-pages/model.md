---
title: 第 2 部分，添加模型
author: rick-anderson
description: Razor 页面教程系列的第 2 部分。 在此部分，将添加模型类。
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 3677cd6fe5c2ff901a17c9dccdc749d8eb2709f2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588661"
---
# <a name="part-2-add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>第 2 部分，在 ASP.NET Core 中向 Razor 页面应用添加模型

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

在本节中，添加了用于管理数据库中的电影的类。 应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。 EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。 首先要编写模型类，然后 EF Core 将创建数据库。

模型类称为 POCO 类（源自“简单传统 CLR 对象”   ），因为它们与 EF Core 没有任何依赖关系。 它们定义数据库中存储的数据属性。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。

## <a name="add-a-data-model"></a>添加数据模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 在“解决方案资源管理器”中，右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”。 将文件夹命名为“Models”。
1. 右键单击“Models”文件夹。 选择“添加” > “类” 。 将类命名“Movie”。
1. 向 `Movie` 类添加以下属性：

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在日期字段中输入时间信息。
  * 仅显示日期，而非时间信息。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 添加名为“Models”的文件夹。
1. 将类添加到名为“Movie.cs”的“Models”文件夹。

向 `Movie` 类添加以下属性：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>添加 NuGet 包和 EF 工具

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>添加数据库上下文类

1. 在 RazorPagesMovie 项目中，创建名为“Data”的文件夹。
1. 在“Data”文件夹中，使用以下代码添加一个名为“RazorPagesMovieContext.cs”的文件：

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   前面的代码为实体集创建 `DbSet` 属性。 在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。 直至在后续步骤中添加依赖项，代码才可编译。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>添加数据库连接字符串

向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>注册数据库上下文

1. 将以下 `using` 语句添加到 Startup.cs 顶部：

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. 使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文：

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。
1. 按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件...”。
1. 在“新建文件”对话框中：
   1. 在左侧窗格中，选择“常规”。
   1. 在中间窗格中，选择“空类”。
   1. 将此类命名为“Movie”，然后选择“新建”。

1. 向 `Movie` 类添加以下属性：

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

---

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。

生成项目以验证没有任何编译错误。

## <a name="scaffold-the-movie-model"></a>搭建“电影”模型的基架

在此部分，将搭建“电影”模型的基架。 确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 创建“Pages/Movies”文件夹：
   1. 右键单击“Pages”文件夹 >“添加”>“新建文件夹”。
   1. 将文件夹命名为“Movies”。

1. 右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。

   ![上述说明的图像。](model/_static/5/sca.png)

1. 在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  

   ![上述说明的图像。](model/_static/add_scaffold.png)

1. 完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：
   1. 在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。
   1. 在“数据上下文类”行中，选择 +（加号） 。
      1. 在“添加数据上下文”对话框中，生成类名 `RazorPagesMovie.Data.RazorPagesMovieContext`。
   1. 选择“添加”。

   ![上述说明的图像。](model/_static/3/arp.png)

*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令行界面。

* **对于 Windows**：运行下面的命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **对于 macOS 和 Linux**：运行下面的命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> 下表详细说明了 ASP.NET Core 代码生成器选项。

| 选项               | 说明|
| ----------------- | ------------ |
| `-m`  | 模型的名称。 |
| `-dc`  | 要使用的 `DbContext` 类。 |
| `-udl` | 使用默认布局。 |
| `-outDir` | 用于创建视图的相对输出文件夹路径。 |
| `--referenceScriptLibraries` | 向“编辑”和“创建”页面添加 `_ValidationScriptsPartial` |

使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。

### <a name="use-sqlite-for-development-sql-server-for-production"></a>将 SQLite 用于开发，将 SQL Server 用于生产

选择 SQLite 后，模板生成的代码便可用于开发。 下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 `Startup` 中。 注入 `IWebHostEnvironment`，以便应用可以在开发中使用 SQLite 并在生产中使用 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 创建“Pages/Movies”文件夹：
   1. 按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。
   1. 将文件夹命名为“Movies”。

1. 按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新基架...”。

   ![上述说明的图像。](model/_static/scaMac.png)

1. 在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  

   ![上述说明的图像。](model/_static/add_scaffoldMac.png)

1. 完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：
   1. 在“要使用的 DbContext 类:”行中，将类命名为 `RazorPagesMovie.Data.RazorPagesMovieContext`。
   1. 选择“完成”  。

   ![上述说明的图像。](model/_static/5/arpMac.png)

*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。

### <a name="use-sqlite-for-development-sql-server-for-production"></a>将 SQLite 用于开发，将 SQL Server 用于生产

选择 SQLite 后，模板生成的代码便可用于开发。 下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 `Startup` 中。 注入 `IWebHostEnvironment`，以便应用可以在开发中使用 SQLite 并在生产中使用 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>创建的文件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在搭建基架时，会创建并更新以下文件：

* *Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。
* Data/RazorPagesMovieContext.cs

### <a name="updated"></a>已更新

* *Startup.cs*

创建和更新的文件将在下一节中说明。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在搭建基架时，会创建以下文件：

* *Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。

创建的文件将在下一节中说明。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

在搭建基架时，会创建并更新以下文件：

* *Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。
* Data/RazorPagesMovieContext.cs

### <a name="updated"></a>已更新

* *Startup.cs*

创建和更新的文件将在下一节中说明。

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a>使用 EF 的迁移功能创建初始数据库架构

Entity Framework Core 中的迁移功能提供了一种方法来执行以下操作：

* 创建初始数据库架构。
* 以增量的方式更新数据库架构，使其与应用程序的数据模型保持同步。  保存数据库中的现有数据。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在此部分中，程序包管理器控制台 (PMC) 窗口用于：

* 添加初始迁移。
* 使用初始迁移来更新数据库。

1. 从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。

   ![PMC 菜单](model/_static/5/pmc.png)

1. 在 PMC 中，输入以下命令：

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* 运行以下 .NET CLI 命令：

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”

忽略警告，因为它将在后面的步骤中得到解决。

`migrations` 命令生成用于创建初始数据库架构的代码。 该架构基于在 `DbContext` 中指定的模型。 `InitialCreate` 参数用于为迁移命名。 可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。

`update` 命令在尚未应用的迁移中运行 `Up` 方法。 在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>检查通过依赖关系注入注册的上下文

ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。 在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。 需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。 本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。

基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。

检查 `Startup.ConfigureServices` 方法。 基架添加了突出显示的行：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。 数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。 数据上下文指定数据模型中包含哪些实体。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

前面的代码为实体集创建 [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) 属性。 在实体框架术语中，实体集通常与数据表相对应。 实体对应表中的行。

通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。 进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

检查 `Up` 方法。

---

<a name="test"></a>

## <a name="test-the-app"></a>测试应用

1. 运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。

   如果收到以下错误：

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   缺少[迁移步骤](#pmc)。

1. 测试“创建”链接。

   ![创建页面](model/_static/conan5.png)

   > [!NOTE]
   > 可能无法在 `Price` 字段中输入十进制逗号。 若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。 有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。

1. 测试“编辑”、“详细信息”和“删除”链接。

下一个教程介绍由基架创建的文件。

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

在该部分，会添加类管理影片。 应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。 EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。

模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。 它们定义数据库中存储的数据属性。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。

## <a name="add-a-data-model"></a>添加数据模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。 将文件夹命名为“Models”。

右键单击“Models”文件夹。 选择“添加” > “类” 。 将类命名“Movie”。

向 `Movie` 类添加以下属性：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 添加名为“Models”的文件夹。
* 将类添加到名为“Movie.cs”的“Models”文件夹。

向 `Movie` 类添加以下属性：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>添加 NuGet 包和 EF 工具

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>添加数据库上下文类

* 在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。
* 将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

前面的代码为实体集创建 `DbSet` 属性。 在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。 直至在后续步骤中添加依赖项，代码才可编译。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>添加数据库连接字符串

向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>注册数据库上下文

将以下 `using` 语句添加到 Startup.cs 顶部：

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”。将文件夹命名为“Models”。
* 右键单击“Models”文件夹，然后选择“添加”>“新建文件...”。
* 在“新建文件”对话框中：

  * 在左侧窗格中，选择“常规”。
  * 在中间窗格中，选择“空类”。
  * 将此类命名为“Movie”，然后选择“新建”。

向 `Movie` 类添加以下属性：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

---

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。

生成项目以验证没有任何编译错误。

## <a name="scaffold-the-movie-model"></a>搭建“电影”模型的基架

在此部分，将搭建“电影”模型的基架。 确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

创建“Pages/Movies”文件夹：

* 右键单击“Pages”文件夹 >“添加”>“新建文件夹”。
* 将文件夹命名为“Movies”。

右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。

![上述说明的图像。](model/_static/sca.png)

在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  

![上述说明的图像。](model/_static/add_scaffold.png)

完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：

* 在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。
* 在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models.RazorPagesMovieContext 更改为 RazorPagesMovie.Data.RazorPagesMovieContext   。 不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。 它创建具有正确命名空间的数据库上下文类。
* 选择“添加”。

![上述说明的图像。](model/_static/3/arp.png)

*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。

* **对于 Windows**：运行下面的命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **对于 macOS 和 Linux**：运行下面的命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> 下表详细说明了 ASP.NET Core 代码生成器选项：

| 选项               | 说明|
| ----------------- | ------------ |
| `-m`  | 模型的名称。 |
| `-dc`  | 要使用的 `DbContext` 类。 |
| `-udl` | 使用默认布局。 |
| `-outDir` | 用于创建视图的相对输出文件夹路径。 |
| `--referenceScriptLibraries` | 向“编辑”和“创建”页面添加 `_ValidationScriptsPartial` |

使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。

### <a name="use-sqlite-for-development-sql-server-for-production"></a>将 SQLite 用于开发，将 SQL Server 用于生产

选择 SQLite 后，模板生成的代码便可用于开发。 下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。 注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

创建“Pages/Movies”文件夹：

* 右键单击“Pages”文件夹 >“添加”>“新建文件夹”。
* 将文件夹命名为“Movies”。

右键单击“Pages/Movies”文件夹 >“添加”>“新基架...”。

![上述说明的图像。](model/_static/scaMac.png)

在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  

![上述说明的图像。](model/_static/add_scaffoldMac.png)

完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：

* 在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)” 。
* 在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。  不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。 它创建具有正确命名空间的数据库上下文类。
* 选择“添加”。

![上述说明的图像。](model/_static/arpMac.png)

*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。

### <a name="add-ef-tools"></a>添加 EF 工具

运行以下 .NET Core CLI 命令：

```dotnetcli
dotnet tool install --global dotnet-ef
```

前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。 有关详细信息，请参阅 [Entity Framework Core 工具参考 - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。

### <a name="use-sqlite-for-development-sql-server-for-production"></a>将 SQLite 用于开发，将 SQL Server 用于生产

选择 SQLite 后，模板生成的代码便可用于开发。 下面的代码演示如何将 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 注入到 Startup 中。 注入 `IWebHostEnvironment`，以便 `ConfigureServices` 可以在开发中使用 SQLite 并在生产中使用 SQL Server。

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>创建的文件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在搭建基架时，会创建并更新以下文件：

* *Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。
* Data/RazorPagesMovieContext.cs

### <a name="updated"></a>已更新

* *Startup.cs*

创建和更新的文件将在下一节中说明。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

在搭建基架时，会创建并更新以下文件：

* *Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。
* Data/RazorPagesMovieContext.cs

### <a name="updated"></a>已更新

* *Startup.cs*

创建和更新的文件将在下一节中说明。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在搭建基架时，会创建以下文件：

* *Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。

创建的文件将在下一节中说明。

---

<a name="pmc"></a>

## <a name="initial-migration"></a>初始迁移

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在此部分中，程序包管理器控制台 (PMC) 用于：

* 添加初始迁移。
* 使用初始迁移来更新数据库。

从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

在 PMC 中，输入以下命令：

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

运行以下 .NET Core CLI 命令：

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”

忽略警告，因为它将在后面的步骤中得到解决。

migrations 命令生成用于创建初始数据库架构的代码。 该架构基于在 `DbContext` 中指定的模型。 `InitialCreate` 参数用于为迁移命名。 可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。

`update` 命令在尚未应用的迁移中运行 `Up` 方法。 在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>检查通过依赖关系注入注册的上下文

ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。 在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。 需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。 本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。

基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。

检查 `Startup.ConfigureServices` 方法。 基架添加了突出显示的行：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。 数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。 数据上下文指定数据模型中包含哪些实体。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。 在实体框架术语中，实体集通常与数据表相对应。 实体对应表中的行。

通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。 进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

检查 `Up` 方法。

---

<a name="test"></a>

### <a name="test-the-app"></a>测试应用

* 运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。

如果收到错误：

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

缺少[迁移步骤](#pmc)。

* 测试“创建”链接。

  ![创建页面](model/_static/conan5.png)

  > [!NOTE]
  > 可能无法在 `Price` 字段中输入十进制逗号。 若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。 有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。

* 测试“编辑”、“详细信息”和“删除”链接。

下一个教程介绍由基架创建的文件。

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。 从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。 应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。 EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。

模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。 它们定义数据库中存储的数据属性。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。

## <a name="add-a-data-model"></a>添加数据模型

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。 将文件夹命名为“Models”。

右键单击“Models”文件夹。 选择“添加” > “类” 。 将类命名“Movie”。

向 `Movie` 类添加以下属性：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 添加名为“Models”的文件夹。
* 将类添加到名为“Movie.cs”的“Models”文件夹。

向 `Movie` 类添加以下属性：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>添加 NuGet 包和 EF 工具

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>添加数据库上下文类

在 RazorPagesMovie 项目中，创建一个名为“Data”的新文件夹。 
将以下 `RazorPagesMovieContext` 类添加到“Data”文件夹：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

前面的代码为实体集创建 `DbSet` 属性。 在 Entity Framework 中，实体集通常与数据表相对应，具体实体与表中的行相对应。 直至在后续步骤中添加依赖项，代码才可编译。

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>添加数据库连接字符串

向 appsettings.json 文件添加一个连接字符串，如以下突出显示的代码所示：

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>添加所需的 NuGet 包

运行以下 .NET Core CLI 命令，将 SQLite 和 CodeGeneration.Design 添加到项目中：

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

`Microsoft.VisualStudio.Web.CodeGeneration.Design` 包对基架是必需的。

<a name="reg"></a>

### <a name="register-the-database-context"></a>注册数据库上下文

将以下 `using` 语句添加到 Startup.cs 顶部：

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

使用 `Startup.ConfigureServices` 中的[依赖关系注入](xref:fundamentals/dependency-injection)容器注册数据库上下文。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

生成项目以检查错误。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在“解决方案工具窗口”中，按 Control 并单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”。 将文件夹命名为“Models”。
* 按 Control 并单击“Models”文件夹，然后选择“添加”>“新建文件”。
* 在“新建文件”对话框中：

  * 在左侧窗格中，选择“常规”。
  * 在中间窗格中，选择“空类”。
  * 将此类命名为“Movie”，然后选择“新建”。

向 `Movie` 类添加以下属性：

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie` 类包含：

* 数据库需要 `ID` 字段以获取主键。
* `[DataType(DataType.Date)]`：[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性指定数据的类型 (`Date`)。 通过此特性：

  * 用户无需在数据字段中输入时间信息。
  * 仅显示日期，而非时间信息。

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) 会在后续教程中介绍。

---

生成项目以验证没有任何编译错误。

## <a name="scaffold-the-movie-model"></a>搭建“电影”模型的基架

在此部分，将搭建“电影”模型的基架。 确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

创建“Pages/Movies”文件夹：

* 右键单击“Pages”文件夹 >“添加”>“新建文件夹”。
* 将文件夹命名为“Movies”。

右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。

![上述说明的图像。](model/_static/sca.png)

在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  

![上述说明的图像。](model/_static/add_scaffold.png)

完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* 在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。
* 在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”  。
* 选择“添加”。

![上述说明的图像。](model/_static/arp.png)

*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* 打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件）中的命令窗口。

* **对于 Windows**：运行下面的命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **对于 macOS 和 Linux**：运行下面的命令：

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> 下表详细说明了 ASP.NET Core 代码生成器选项：

| 选项               | 说明|
| ----------------- | ------------ |
| `-m`  | 模型的名称。 |
| `-dc`  | 要使用的 `DbContext` 类。 |
| `-udl` | 使用默认布局。 |
| `-outDir` | 用于创建视图的相对输出文件夹路径。 |
| `--referenceScriptLibraries` | 向“编辑”和“创建”页面添加 `_ValidationScriptsPartial` |

使用 `-h` 选项获取 `aspnet-codegenerator razorpage` 命令方面的帮助：

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

有关详细信息，请查看 [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

创建“Pages/Movies”文件夹：

* 按 Control 并单击“Pages”文件夹 >“添加”>“新建文件夹”。
* 将文件夹命名为“Movies”。

按 Control 并单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。

![上述说明的图像。](model/_static/scaMac.png)

在“添加新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  

![上述说明的图像。](model/_static/add_scaffoldMac.png)

完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：

* 在“模型类”下拉列表中，选择或键入“Movie” 。
* 在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类。 在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。
* 选择“添加”。

![上述说明的图像。](model/_static/arpMac.png)

*appsettings.json* 文件通过用于连接到本地数据的连接字符串进行更新。

---

在搭建基架时，会创建并更新以下文件：

### <a name="files-created"></a>创建的文件

* *Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和 Index。
* Data/RazorPagesMovieContext.cs

### <a name="file-updated"></a>文件已更新

* *Startup.cs*

创建和更新的文件将在下一节中说明。

<a name="pmc"></a>

## <a name="initial-migration"></a>初始迁移

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在此部分中，程序包管理器控制台 (PMC) 用于：

* 添加初始迁移。
* 使用初始迁移来更新数据库。

从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

在 PMC 中，输入以下命令：

```powershell
Add-Migration Initial
Update-Database
```

`Add-Migration` 命令生成用于创建初始数据库架构的代码。 此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。 `InitialCreate` 参数用于为迁移命名。 可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。 有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。

`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。 `Up` 方法会创建数据库。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

运行以下 .NET Core CLI 命令：

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”

忽略警告，因为它将在后面的步骤中得到解决。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>检查通过依赖关系注入注册的上下文

ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。 在应用程序启动过程中，通过依赖注入注册相关服务（例如 EF Core 数据库上下文）。 需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。 本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。

基架工具自动创建数据库上下文并将其注册到依赖关系注入容器。

检查 `Startup.ConfigureServices` 方法。 基架添加了突出显示的行：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能，例如“创建”、“读取”、“更新”和“删除”。 数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)。 数据上下文指定数据模型中包含哪些实体。

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。 在实体框架术语中，实体集通常与数据表相对应。 实体对应表中的行。

通过调用 [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) 对象中的一个方法将连接字符串名称传递到上下文。 进行本地开发时，[配置系统](xref:fundamentals/configuration/index)在 appsettings.json 文件中读取连接字符串。

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

检查 `Up` 方法。

---

<a name="test"></a>

### <a name="test-the-app"></a>测试应用

* 运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。

如果收到错误：

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

缺少[迁移步骤](#pmc)。

* 测试“创建”链接。

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > 可能无法在 `Price` 字段中输入十进制逗号。 若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。 有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。

* 测试“编辑”、“详细信息”和“删除”链接。

下一个教程介绍由基架创建的文件。

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
