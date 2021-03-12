---
title: 教程第 5 部分 - 将迁移应用于 Contoso University 示例
description: Contoso University 教程系列的第 5 部分。 使用 EF Core 迁移功能管理 ASP.NET Core MVC 应用中的数据模型更改。
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
ms.topic: tutorial
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: aebbc3f29b0356c7993abd83869ab21d3613bf61
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589343"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="848b2-104">教程：第 5 部分 - 将迁移应用于 Contoso University 示例</span><span class="sxs-lookup"><span data-stu-id="848b2-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="848b2-105">本教程使用 EF Core 迁移功能管理数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="848b2-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="848b2-106">后续教程将在更改数据模型时添加更多迁移。</span><span class="sxs-lookup"><span data-stu-id="848b2-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="848b2-107">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="848b2-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="848b2-108">了解迁移相关信息</span><span class="sxs-lookup"><span data-stu-id="848b2-108">Learn about migrations</span></span>
> * <span data-ttu-id="848b2-109">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="848b2-109">Create an initial migration</span></span>
> * <span data-ttu-id="848b2-110">检查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="848b2-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="848b2-111">了解数据模型快照</span><span class="sxs-lookup"><span data-stu-id="848b2-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="848b2-112">应用迁移</span><span class="sxs-lookup"><span data-stu-id="848b2-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="848b2-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="848b2-113">Prerequisites</span></span>

* [<span data-ttu-id="848b2-114">排序、筛选和分页</span><span class="sxs-lookup"><span data-stu-id="848b2-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="848b2-115">关于迁移</span><span class="sxs-lookup"><span data-stu-id="848b2-115">About migrations</span></span>

<span data-ttu-id="848b2-116">开发新应用程序时，数据模型会频繁更改。每当模型更改时，模型都无法与数据库保持同步。</span><span class="sxs-lookup"><span data-stu-id="848b2-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="848b2-117">本系列教程首先配置 Entity Framework 以创建数据库（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="848b2-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="848b2-118">之后，每当更改数据模型（添加、删除或更改实体类或更改 DbContext 类）时，你都可以删除数据库，EF 将创建匹配该模型的新数据库并用测试数据为其设定种子。</span><span class="sxs-lookup"><span data-stu-id="848b2-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="848b2-119">这种使数据库与数据模型保持同步的方法适用于多种情况，但将应用程序部署到生产环境的情况除外。</span><span class="sxs-lookup"><span data-stu-id="848b2-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="848b2-120">当应用程序在生产环境中运行时，它通常会存储要保留的数据，以便不会在每次更改（如添加新列）时丢失所有数据。</span><span class="sxs-lookup"><span data-stu-id="848b2-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="848b2-121">EF Core 迁移功能可通过使 EF 更新数据库 架构而不是创建新数据库来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="848b2-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="848b2-122">要使用迁移，可使用“包管理器控制台”(PMC) 或 CLI 。</span><span class="sxs-lookup"><span data-stu-id="848b2-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="848b2-123">以下教程演示如何使用 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="848b2-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="848b2-124">有关 PMC 的信息，请转到[本教程末尾](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="848b2-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="848b2-125">删除数据库</span><span class="sxs-lookup"><span data-stu-id="848b2-125">Drop the database</span></span>

<span data-ttu-id="848b2-126">将 EF Core 工具安装为[全局工具](/ef/core/miscellaneous/cli/dotnet)并删除数据库：</span><span class="sxs-lookup"><span data-stu-id="848b2-126">Install EF Core tools as a [global tool](/ef/core/miscellaneous/cli/dotnet) and delete the database:</span></span>

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

<span data-ttu-id="848b2-127">下面的部分说明如何运行 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="848b2-127">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="848b2-128">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="848b2-128">Create an initial migration</span></span>

<span data-ttu-id="848b2-129">保存更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="848b2-129">Save your changes and build the project.</span></span> <span data-ttu-id="848b2-130">然后打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="848b2-130">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="848b2-131">下面是执行此操作的快速方法：</span><span class="sxs-lookup"><span data-stu-id="848b2-131">Here's a quick way to do that:</span></span>

* <span data-ttu-id="848b2-132">在解决方案资源管理器中，右键单击项目，然后从上下文菜单中选择“在文件资源管理器中打开文件夹”。</span><span class="sxs-lookup"><span data-stu-id="848b2-132">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![“在文件资源管理器中打开”菜单项](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="848b2-134">在地址栏中输入“cmd”，然后按 Enter。</span><span class="sxs-lookup"><span data-stu-id="848b2-134">Enter "cmd" in the address bar and press Enter.</span></span>

  ![打开命令窗口](migrations/_static/open-command-window.png)

<span data-ttu-id="848b2-136">在命令窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="848b2-136">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="848b2-137">在前面的命令中，会显示类似于以下内容的输出：</span><span class="sxs-lookup"><span data-stu-id="848b2-137">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="848b2-138">如果看到错误消息“无法访问文件...ContosoUniversity.dll，因为它正被另一个进程使用。”，请在 Windows 系统托盘中找到 IIS Express 图标并右键单击，然后单击“ContosoUniversity”>“停止站点”。</span><span class="sxs-lookup"><span data-stu-id="848b2-138">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="848b2-139">检查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="848b2-139">Examine Up and Down methods</span></span>

<span data-ttu-id="848b2-140">执行 `migrations add` 命令时，EF 已生成将用于从头创建数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="848b2-140">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="848b2-141">此代码位于“Migrations”文件夹中名为 \<timestamp>_InitialCreate.cs 的文件中 。</span><span class="sxs-lookup"><span data-stu-id="848b2-141">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="848b2-142">`InitialCreate` 类的 `Up` 的方法将创建与数据模型实体集相对应的数据库表，`Down` 方法将删除这些表，如下面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="848b2-142">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="848b2-143">迁移调用 `Up` 方法为迁移实现数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="848b2-143">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="848b2-144">输入用于回退更新的命令时，迁移调用 `Down` 方法。</span><span class="sxs-lookup"><span data-stu-id="848b2-144">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="848b2-145">此代码适用于输入 `migrations add InitialCreate` 命令时所创建的初始迁移。</span><span class="sxs-lookup"><span data-stu-id="848b2-145">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="848b2-146">迁移名称参数（本示例中为“InitialCreate”）用于指定文件名，并且你可以按需使用任何名称。</span><span class="sxs-lookup"><span data-stu-id="848b2-146">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="848b2-147">最好选择能概括迁移中所执行操作的字词或短语。</span><span class="sxs-lookup"><span data-stu-id="848b2-147">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="848b2-148">例如，可将后面的迁移命名为“AddDepartmentTable”。</span><span class="sxs-lookup"><span data-stu-id="848b2-148">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="848b2-149">如果创建初始迁移时已存在数据库，则会生成数据库创建代码，但此代码不必运行，因为数据库已与数据库模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="848b2-149">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="848b2-150">将应用部署到其中尚不存在数据库的其他环境时，此代码将运行以创建数据库，因此最好提前进行测试。</span><span class="sxs-lookup"><span data-stu-id="848b2-150">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="848b2-151">这也是提前更改连接字符串中数据库的名称的原因，这样迁移才能从头创建新数据库。</span><span class="sxs-lookup"><span data-stu-id="848b2-151">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="848b2-152">数据模型快照</span><span class="sxs-lookup"><span data-stu-id="848b2-152">The data model snapshot</span></span>

<span data-ttu-id="848b2-153">迁移在 Migrations/SchoolContextModelSnapshot.cs 中创建当前数据库架构的快照 。</span><span class="sxs-lookup"><span data-stu-id="848b2-153">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="848b2-154">添加迁移时，EF 会通过将数据模型与快照文件进行对比来确定已更改的内容。</span><span class="sxs-lookup"><span data-stu-id="848b2-154">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="848b2-155">使用 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 命令删除迁移。</span><span class="sxs-lookup"><span data-stu-id="848b2-155">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="848b2-156">`dotnet ef migrations remove` 删除迁移，并确保正确重置快照。</span><span class="sxs-lookup"><span data-stu-id="848b2-156">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="848b2-157">如果 `dotnet ef migrations remove` 失败，使用 `dotnet ef migrations remove -v` 获取失败的详细信息。</span><span class="sxs-lookup"><span data-stu-id="848b2-157">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="848b2-158">有关如何使用快照文件的详细信息，请参阅[团队环境中的 EF Core 迁移](/ef/core/managing-schemas/migrations/teams)。</span><span class="sxs-lookup"><span data-stu-id="848b2-158">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="848b2-159">应用迁移</span><span class="sxs-lookup"><span data-stu-id="848b2-159">Apply the migration</span></span>

<span data-ttu-id="848b2-160">在命令窗口中，输入以下命令以创建数据库并在其中创建表。</span><span class="sxs-lookup"><span data-stu-id="848b2-160">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="848b2-161">该命令的输出与 `migrations add` 命令的输出相似，但其中还包含设置该数据库的 SQL 命令的日志。</span><span class="sxs-lookup"><span data-stu-id="848b2-161">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="848b2-162">下面的示例输出中省略了大部分日志。</span><span class="sxs-lookup"><span data-stu-id="848b2-162">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="848b2-163">如果希望日志消息中不使用此详细级别，则可更改 appsettings.Development.json 文件中的日志级别。</span><span class="sxs-lookup"><span data-stu-id="848b2-163">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="848b2-164">有关详细信息，请参阅 <xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="848b2-164">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

<span data-ttu-id="848b2-165">使用 SQL Server 对象资源管理器检查数据库（与第一个教程中的做法相同）。</span><span class="sxs-lookup"><span data-stu-id="848b2-165">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="848b2-166">你会发现添加了 \_\_EFMigrationsHistory 表，该表可用于跟踪已应用到数据库的迁移。</span><span class="sxs-lookup"><span data-stu-id="848b2-166">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="848b2-167">查看该表中的数据，其中显示对应初始迁移的一行数据。</span><span class="sxs-lookup"><span data-stu-id="848b2-167">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="848b2-168">（上面的 CLI 输出示例中最后部分的日志显示了创建此行的 INSERT 语句。）</span><span class="sxs-lookup"><span data-stu-id="848b2-168">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="848b2-169">运行应用程序以验证所有内容照旧运行。</span><span class="sxs-lookup"><span data-stu-id="848b2-169">Run the application to verify that everything still works the same as before.</span></span>

![“学生索引”页](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="848b2-171">比较 CLI 和 PMC</span><span class="sxs-lookup"><span data-stu-id="848b2-171">Compare CLI and PMC</span></span>

<span data-ttu-id="848b2-172">可通过 .NET Core CLI 命令或 Visual Studio 包管理器控制台 (PMC) 窗口中的 PowerShell cmdlet 使用可管理迁移的 EF 工具。</span><span class="sxs-lookup"><span data-stu-id="848b2-172">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="848b2-173">本教程演示如何使用 CLI，但也可以根据喜好使用 PMC。</span><span class="sxs-lookup"><span data-stu-id="848b2-173">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="848b2-174">适用于 PMC 命令的 EF 命令位于 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 程序包中。</span><span class="sxs-lookup"><span data-stu-id="848b2-174">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="848b2-175">此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，因此，如果应用具有对 `Microsoft.AspNetCore.App` 的包引用，则无需添加包引用。</span><span class="sxs-lookup"><span data-stu-id="848b2-175">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="848b2-176">**重要提示：** 此包与通过编辑 .csproj 文件为 CLI 安装的包不同。</span><span class="sxs-lookup"><span data-stu-id="848b2-176">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="848b2-177">此程序包的名称以 `Tools` 结尾，而 CLI 程序包的名称以 `Tools.DotNet` 结尾。</span><span class="sxs-lookup"><span data-stu-id="848b2-177">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="848b2-178">有关 CLI 命令的详细信息，请参阅 [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="848b2-178">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="848b2-179">有关 PMC 命令的详细信息，请参阅[包管理器控制台 (Visual Studio)](/ef/core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="848b2-179">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="848b2-180">获取代码</span><span class="sxs-lookup"><span data-stu-id="848b2-180">Get the code</span></span>

[<span data-ttu-id="848b2-181">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="848b2-181">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="848b2-182">下一步</span><span class="sxs-lookup"><span data-stu-id="848b2-182">Next step</span></span>

<span data-ttu-id="848b2-183">请继续阅读下一篇教程，开始了解有关展开数据模型的更高级主题。</span><span class="sxs-lookup"><span data-stu-id="848b2-183">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="848b2-184">同时还将介绍创建并应用其他迁移的方法。</span><span class="sxs-lookup"><span data-stu-id="848b2-184">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="848b2-185">创建并应用其他迁移</span><span class="sxs-lookup"><span data-stu-id="848b2-185">Create and apply additional migrations</span></span>](complex-data-model.md)
