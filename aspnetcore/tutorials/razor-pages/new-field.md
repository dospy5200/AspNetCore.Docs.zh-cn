---
title: 第 7 部分，添加新字段
author: rick-anderson
description: Razor 页面教程系列第 7 部分。
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: cd637507e19735f020b4c28e6f22de0e7e772040
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588316"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a>第 7 部分，将新字段添加到 ASP.NET Core 中的 Razor 页面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。

在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：

* 将新字段添加到模型。
* 将新字段架构更改迁移到数据库。

使用 EF Code First 自动创建数据库时，Code First 将：

* 向数据库添加 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 表格，以跟踪数据库的架构是否与从生成它的模型类同步。
* 如果该模型类未与数据库同步，EF 将引发异常。

自动验证架构与模型是否同步可以更容易地发现不一致的数据库代码问题。

## <a name="adding-a-rating-property-to-the-movie-model"></a>向电影模型添加分级属性

1. 打开 Models/Movie.cs 文件，并添加 `Rating` 属性：

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. 构建应用程序。

1. 编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. 更新以下页面：
   1. 将 `Rating` 字段添加到“删除”和“详细信息”页面。
   1. 使用 `Rating` 字段更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml)。
   1. 将 `Rating` 字段添加到“编辑”页面。

在数据库更新为包括新字段之前，应用将不会正常工作。 在不更新数据库的情况下运行应用会引发 `SqlException`：

`SqlException: Invalid column name 'Rating'.`

`SqlException` 异常是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。 数据库表中没有 `Rating` 列。

可通过几种方法解决此错误：

1. 让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。 此方法在开发周期早期很方便，通过该方法可以一起快速改进模型和数据库架构。 此方法的缺点是会导致数据库中的现有数据丢失。 请勿对生产数据库使用此方法！ 在架构更改时丢弃数据库，并使用初始化表达式通过测试数据自动设定数据库种子，这通常是开发应用的有效方式。

2. 对现有数据库架构进行显式修改，使它与模型类相匹配。 此方法的优点是可以保留数据。 可以手动或通过创建数据库更改脚本进行此更改。

3. 使用 Code First 迁移更新数据库架构。

对于本教程，请使用 Code First 迁移。

更新 `SeedData` 类，使它提供新列的值。 示例更改如下所示，但对每个 `new Movie` 块做出此更改。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)。

生成解决方案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>添加用于评级字段的迁移

1. 从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 
2. 在 PMC 中，输入以下命令：

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

`Add-Migration` 命令会通知框架执行以下操作：

* 将 `Movie` 模型与 `Movie` 数据库架构进行比较。
* 创建代码以将数据库架构迁移到新模型。

名称“Rating”是任意的，用于对迁移文件进行命名。 为迁移文件使用有意义的名称是有帮助的。

`Update-Database` 命令指示框架将架构更改应用到数据库并保留现有数据。

<a name="ssox"></a>

如果删除数据库中的所有记录，初始化表达式会设定数据库种子，并将包括 `Rating` 字段。 可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。

另一个方案是删除数据库，并使用迁移来重新创建该数据库。 删除 SSOX 中的数据库：

1. 在 SSOX 中选择数据库。
1. 右键单击数据库，并选择“删除”。
1. 检查“关闭现有连接”。
1. 选择“确定”。
1. 在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>删除并重新创建数据库

> [!NOTE]
> 在本教程中，使用 Entity Framework Core 迁移功能（若可行）。 迁移会更新数据库架构，使其与数据模型中的更改相匹配。 但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。 例如，支持添加列，但不支持删除或更改列。 如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。 由于上述限制，本教程不对 SQLite 架构更改使用迁移。 转而在架构更改时，放弃并重新创建数据库。
>
>要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。 表重新生成涉及：
>
>* 创建新表。
>* 将旧表中的数据复制到新表中。
>* 放弃旧表。
>* 为新表重命名。
>
>有关更多信息，请参见以下资源：
>
> * [SQLite EF Core 数据库提供程序限制](/ef/core/providers/sqlite/limitations)
> * [自定义迁移代码](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [数据种子设定](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 语句](https://sqlite.org/lang_altertable.html)

1. 删除迁移文件夹。  

1. 使用以下命令重新创建数据库。

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。 如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一步：添加验证](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。

在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：

* 将新字段添加到模型。
* 将新字段架构更改迁移到数据库。

使用 EF Code First 自动创建数据库时，Code First 将：

* 向数据库添加 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 表格，以跟踪数据库的架构是否与从生成它的模型类同步。
* 如果该模型类未与数据库同步，EF 将引发异常。

自动验证架构与模型是否同步可以更容易地发现不一致的数据库代码问题。

## <a name="adding-a-rating-property-to-the-movie-model"></a>向电影模型添加分级属性

1. 打开 Models/Movie.cs 文件，并添加 `Rating` 属性：

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. 构建应用程序。

1. 编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. 更新以下页面：
   1. 将 `Rating` 字段添加到“删除”和“详细信息”页面。
   1. 使用 `Rating` 字段更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)。
   1. 将 `Rating` 字段添加到“编辑”页面。

在数据库更新为包括新字段之前，应用将不会正常工作。 在不更新数据库的情况下运行应用会引发 `SqlException`：

`SqlException: Invalid column name 'Rating'.`

`SqlException` 异常是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。 数据库表中没有 `Rating` 列。

可通过几种方法解决此错误：

1. 让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。 此方法在开发周期早期很方便，通过该方法可以一起快速改进模型和数据库架构。 此方法的缺点是会导致数据库中的现有数据丢失。 请勿对生产数据库使用此方法！ 在架构更改时丢弃数据库，并使用初始化表达式通过测试数据自动设定数据库种子，这通常是开发应用的有效方式。

2. 对现有数据库架构进行显式修改，使它与模型类相匹配。 此方法的优点是可以保留数据。 可以手动或通过创建数据库更改脚本进行此更改。

3. 使用 Code First 迁移更新数据库架构。

对于本教程，请使用 Code First 迁移。

更新 `SeedData` 类，使它提供新列的值。 示例更改如下所示，但对每个 `new Movie` 块做出此更改。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs)。

生成解决方案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>添加用于评级字段的迁移

1. 从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 
2. 在 PMC 中，输入以下命令：

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

`Add-Migration` 命令会通知框架执行以下操作：

* 将 `Movie` 模型与 `Movie` 数据库架构进行比较。
* 创建代码以将数据库架构迁移到新模型。

名称“Rating”是任意的，用于对迁移文件进行命名。 为迁移文件使用有意义的名称是有帮助的。

`Update-Database` 命令指示框架将架构更改应用到数据库并保留现有数据。

<a name="ssox"></a>

如果删除数据库中的所有记录，初始化表达式会设定数据库种子，并将包括 `Rating` 字段。 可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。

另一个方案是删除数据库，并使用迁移来重新创建该数据库。 删除 SSOX 中的数据库：

* 在 SSOX 中选择数据库。
* 右键单击数据库，并选择“删除”。
* 检查“关闭现有连接”。
* 选择“确定”。
* 在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>删除并重新创建数据库

> [!NOTE]
> 在本教程中，使用 Entity Framework Core 迁移功能（若可行）。 迁移会更新数据库架构，使其与数据模型中的更改相匹配。 但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。 例如，支持添加列，但不支持删除或更改列。 如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。 由于上述限制，本教程不对 SQLite 架构更改使用迁移。 转而在架构更改时，放弃并重新创建数据库。
>
>要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。 表重新生成涉及：
>
>* 创建新表。
>* 将旧表中的数据复制到新表中。
>* 放弃旧表。
>* 为新表重命名。
>
>有关更多信息，请参见以下资源：
>
> * [SQLite EF Core 数据库提供程序限制](/ef/core/providers/sqlite/limitations)
> * [自定义迁移代码](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [数据种子设定](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 语句](https://sqlite.org/lang_altertable.html)

1. 删除迁移文件夹。  

1. 使用以下命令重新创建数据库。

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。 如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。

## <a name="additional-resources"></a>其他资源

> [!div class="step-by-step"]
> [上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一步：添加验证](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。

在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：

* 将新字段添加到模型。
* 将新字段架构更改迁移到数据库。

使用 EF Code First 自动创建数据库时，Code First 将：

* 向数据库添加 [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) 表格，以跟踪数据库的架构是否与从生成它的模型类同步。
* 如果该模型类未与数据库同步，EF 将引发异常。

自动验证架构与模型是否同步可以更容易地发现不一致的数据库代码问题。

## <a name="adding-a-rating-property-to-the-movie-model"></a>向电影模型添加分级属性

打开 Models/Movie.cs 文件，并添加 `Rating` 属性：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

构建应用程序。

编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

更新以下页面：

* 将 `Rating` 字段添加到“删除”和“详细信息”页面。
* 使用 `Rating` 字段更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)。
* 将 `Rating` 字段添加到“编辑”页面。

在数据库更新为包括新字段之前，应用将不会正常工作。 如果应用立即运行，应用会引发 `SqlException`：

`SqlException: Invalid column name 'Rating'.`

此错误是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。 数据库表中没有 `Rating` 列。

可通过几种方法解决此错误：

1. 让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。 此方法在开发周期早期很方便，通过该方法可以一起快速改进模型和数据库架构。 此方法的缺点是会导致数据库中的现有数据丢失。 请勿对生产数据库使用此方法！ 在架构更改时丢弃数据库，并使用初始化表达式通过测试数据自动设定数据库种子，这通常是开发应用的有效方式。

2. 对现有数据库架构进行显式修改，使它与模型类相匹配。 此方法的优点是可以保留数据。 可以手动或通过创建数据库更改脚本进行此更改。

3. 使用 Code First 迁移更新数据库架构。

对于本教程，请使用 Code First 迁移。

更新 `SeedData` 类，使它提供新列的值。 示例更改如下所示，但对每个 `new Movie` 块做出此更改。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)。

生成解决方案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>添加用于评级字段的迁移

从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 
在 PMC 中，输入以下命令：

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` 命令会通知框架执行以下操作：

* 将 `Movie` 模型与 `Movie` 数据库架构进行比较。
* 创建代码以将数据库架构迁移到新模型。

名称“Rating”是任意的，用于对迁移文件进行命名。 为迁移文件使用有意义的名称是有帮助的。

`Update-Database` 命令指示框架将架构更改应用到数据库。

<a name="ssox"></a>

如果删除数据库中的所有记录，初始化表达式会设定数据库种子，并将包括 `Rating` 字段。 可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。

另一个方案是删除数据库，并使用迁移来重新创建该数据库。 删除 SSOX 中的数据库：

* 在 SSOX 中选择数据库。
* 右键单击数据库，并选择“删除”。
* 检查“关闭现有连接”。
* 选择“确定”。
* 在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>删除并重新创建数据库

> [!NOTE]
> 在本教程中，使用 Entity Framework Core 迁移功能（若可行）。 迁移会更新数据库架构，使其与数据模型中的更改相匹配。 但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。 例如，支持添加列，但不支持删除或更改列。 如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。 由于上述限制，本教程不对 SQLite 架构更改使用迁移。 转而在架构更改时，放弃并重新创建数据库。
>
>要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。 表重新生成涉及：
>
>* 创建新表。
>* 将旧表中的数据复制到新表中。
>* 放弃旧表。
>* 为新表重命名。
>
>有关更多信息，请参见以下资源：
>
> * [SQLite EF Core 数据库提供程序限制](/ef/core/providers/sqlite/limitations)
> * [自定义迁移代码](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [数据种子设定](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE 语句](https://sqlite.org/lang_altertable.html)

删除数据库并通过迁移重新创建数据库。 若要删除该数据库，请删除数据库文件 (MvcMovie.db)。 然后运行 `ef database update` 命令：

```dotnetcli
dotnet ef database update
```

---

运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。 如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一步：添加验证](xref:tutorials/razor-pages/validation)

::: moniker-end