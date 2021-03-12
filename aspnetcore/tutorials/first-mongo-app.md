---
title: 使用 ASP.NET Core 和 MongoDB 创建 Web API
author: prkhandelwal
description: 本教程演示如何使用 MongoDB NoSQL 数据库创建 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 489a15f2011e44ffd9f9bfe7d5410b7d79a10632
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587601"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="7d862-103">使用 ASP.NET Core 和 MongoDB 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="7d862-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="7d862-104">作者 [Pratik Khandelwal](https://twitter.com/K2Prk) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="7d862-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7d862-105">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="7d862-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="7d862-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="7d862-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7d862-107">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-107">Configure MongoDB</span></span>
> * <span data-ttu-id="7d862-108">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="7d862-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="7d862-109">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="7d862-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="7d862-110">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="7d862-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="7d862-111">自定义 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="7d862-111">Customize JSON serialization</span></span>

<span data-ttu-id="7d862-112">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7d862-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d862-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="7d862-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d862-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d862-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="7d862-115">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7d862-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="7d862-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="7d862-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="7d862-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d862-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d862-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="7d862-119">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7d862-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7d862-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d862-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="7d862-121">用于 Visual Studio Code 的 C#</span><span class="sxs-lookup"><span data-stu-id="7d862-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="7d862-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d862-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d862-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="7d862-124">.NET Core SDK 3.0 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7d862-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7d862-125">Visual Studio for Mac 版本 7.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7d862-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="7d862-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="7d862-127">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-127">Configure MongoDB</span></span>

<span data-ttu-id="7d862-128">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。</span><span class="sxs-lookup"><span data-stu-id="7d862-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="7d862-129">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="7d862-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="7d862-130">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="7d862-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="7d862-131">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="7d862-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="7d862-132">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="7d862-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="7d862-133">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="7d862-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="7d862-134">例如，Windows 上的 C:\\BooksData。</span><span class="sxs-lookup"><span data-stu-id="7d862-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="7d862-135">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="7d862-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="7d862-136">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="7d862-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="7d862-137">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="7d862-137">Open a command shell.</span></span> <span data-ttu-id="7d862-138">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="7d862-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="7d862-139">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="7d862-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="7d862-140">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="7d862-140">Open another command shell instance.</span></span> <span data-ttu-id="7d862-141">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="7d862-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="7d862-142">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7d862-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="7d862-143">如果它不存在，则将创建名为 BookstoreDb 的数据库。</span><span class="sxs-lookup"><span data-stu-id="7d862-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="7d862-144">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="7d862-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="7d862-145">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="7d862-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="7d862-146">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="7d862-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="7d862-147">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="7d862-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="7d862-148">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="7d862-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="7d862-149">本文所示的 ID 与运行此示例时的 ID 不匹配。</span><span class="sxs-lookup"><span data-stu-id="7d862-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="7d862-150">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="7d862-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="7d862-151">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="7d862-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="7d862-152">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="7d862-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="7d862-153">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="7d862-153">The database is ready.</span></span> <span data-ttu-id="7d862-154">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="7d862-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="7d862-155">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="7d862-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d862-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d862-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7d862-157">转到“文件”>“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="7d862-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="7d862-158">选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="7d862-159">将项目命名为“BooksApi”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="7d862-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="7d862-160">选择“.NET Core”目标框架和“ASP.NET Core 3.0”。</span><span class="sxs-lookup"><span data-stu-id="7d862-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="7d862-161">选择“API”项目模板，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="7d862-162">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="7d862-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7d862-163">在“包管理器控制台”窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="7d862-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="7d862-164">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="7d862-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d862-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d862-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="7d862-166">在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7d862-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="7d862-167">将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="7d862-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="7d862-168">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。是否添加它们?”。</span><span class="sxs-lookup"><span data-stu-id="7d862-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="7d862-169">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="7d862-169">Select **Yes**.</span></span>
1. <span data-ttu-id="7d862-170">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="7d862-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7d862-171">打开“集成终端”并导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="7d862-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="7d862-172">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="7d862-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d862-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d862-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7d862-174">在早于版本 8.6 的 Visual Studio for Mac 中，从边栏中依次选择“文件” > “新建解决方案” > “.NET Core” > “应用”。   </span><span class="sxs-lookup"><span data-stu-id="7d862-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="7d862-175">在版本 8.6 或更高版本中，从边栏中依次选择“文件” > “新建解决方案” > “Web 和控制台” > “应用”。   </span><span class="sxs-lookup"><span data-stu-id="7d862-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="7d862-176">依次选择“ASP.NET Core”>“API”C# 项目模板，然后选择“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="7d862-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="7d862-177">从“目标框架”下拉列表中选择“.NET Core 3.1”，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="7d862-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="7d862-178">输入“BooksApi”作为“项目名称”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="7d862-179">在“解决方案”面板中，右键单击项目的“依赖项”节点，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="7d862-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="7d862-180">在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="7d862-181">在“许可证接受”对话框中，选择“接受”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7d862-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="7d862-182">添加实体模型</span><span class="sxs-lookup"><span data-stu-id="7d862-182">Add an entity model</span></span>

1. <span data-ttu-id="7d862-183">将 Models 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="7d862-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="7d862-184">使用以下代码将 `Book` 类添加到 Models 目录：</span><span class="sxs-lookup"><span data-stu-id="7d862-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="7d862-185">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="7d862-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="7d862-186">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="7d862-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="7d862-187">使用 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="7d862-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="7d862-188">使用 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而非 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构传递。</span><span class="sxs-lookup"><span data-stu-id="7d862-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="7d862-189">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="7d862-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="7d862-190">`BookName` 属性使用 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。</span><span class="sxs-lookup"><span data-stu-id="7d862-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="7d862-191">`Name` 的属性值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="7d862-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="7d862-192">添加配置模型</span><span class="sxs-lookup"><span data-stu-id="7d862-192">Add a configuration model</span></span>

1. <span data-ttu-id="7d862-193">向 appsettings.json 添加以下数据库配置值：</span><span class="sxs-lookup"><span data-stu-id="7d862-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="7d862-194">使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录 ：</span><span class="sxs-lookup"><span data-stu-id="7d862-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="7d862-195">前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值。</span><span class="sxs-lookup"><span data-stu-id="7d862-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="7d862-196">JSON 和 C# 具有相同的属性名称，目的是简化映射过程。</span><span class="sxs-lookup"><span data-stu-id="7d862-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="7d862-197">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="7d862-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="7d862-198">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="7d862-198">In the preceding code:</span></span>

   * <span data-ttu-id="7d862-199">appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册。</span><span class="sxs-lookup"><span data-stu-id="7d862-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="7d862-200">例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充。</span><span class="sxs-lookup"><span data-stu-id="7d862-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="7d862-201">`IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。</span><span class="sxs-lookup"><span data-stu-id="7d862-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="7d862-202">在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。</span><span class="sxs-lookup"><span data-stu-id="7d862-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="7d862-203">在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用：</span><span class="sxs-lookup"><span data-stu-id="7d862-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="7d862-204">添加 CRUD 操作服务</span><span class="sxs-lookup"><span data-stu-id="7d862-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="7d862-205">将 Services 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="7d862-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="7d862-206">使用以下代码将 `BookService` 类添加到 Services 目录：</span><span class="sxs-lookup"><span data-stu-id="7d862-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="7d862-207">上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。</span><span class="sxs-lookup"><span data-stu-id="7d862-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="7d862-208">使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分中添加的 appsettings.json 配置值。</span><span class="sxs-lookup"><span data-stu-id="7d862-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="7d862-209">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="7d862-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="7d862-210">上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="7d862-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="7d862-211">单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="7d862-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="7d862-212">根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="7d862-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="7d862-213">在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用：</span><span class="sxs-lookup"><span data-stu-id="7d862-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="7d862-214">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="7d862-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="7d862-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：读取用于执行数据库操作的服务器实例。</span><span class="sxs-lookup"><span data-stu-id="7d862-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="7d862-216">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="7d862-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="7d862-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：表示用于执行操作的 Mongo 数据库。</span><span class="sxs-lookup"><span data-stu-id="7d862-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="7d862-218">本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。</span><span class="sxs-lookup"><span data-stu-id="7d862-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="7d862-219">调用此方法后，对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="7d862-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="7d862-220">在 `GetCollection<TDocument>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="7d862-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="7d862-221">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="7d862-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="7d862-222">`TDocument` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="7d862-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="7d862-223">`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。</span><span class="sxs-lookup"><span data-stu-id="7d862-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="7d862-224">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="7d862-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="7d862-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="7d862-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="7d862-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="7d862-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="7d862-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="7d862-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="7d862-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="7d862-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7d862-229">添加控制器</span><span class="sxs-lookup"><span data-stu-id="7d862-229">Add a controller</span></span>

<span data-ttu-id="7d862-230">使用以下代码将 `BooksController` 类添加到 Controllers 目录：</span><span class="sxs-lookup"><span data-stu-id="7d862-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="7d862-231">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="7d862-231">The preceding web API controller:</span></span>

* <span data-ttu-id="7d862-232">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="7d862-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="7d862-233">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7d862-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="7d862-234">在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。</span><span class="sxs-lookup"><span data-stu-id="7d862-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="7d862-235">状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="7d862-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="7d862-236">`CreatedAtRoute` 还会将 `Location` 标头添加到响应中。</span><span class="sxs-lookup"><span data-stu-id="7d862-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="7d862-237">`Location` 标头指定新建书籍的 URI。</span><span class="sxs-lookup"><span data-stu-id="7d862-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="7d862-238">测试 Web API</span><span class="sxs-lookup"><span data-stu-id="7d862-238">Test the web API</span></span>

1. <span data-ttu-id="7d862-239">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="7d862-239">Build and run the app.</span></span>

1. <span data-ttu-id="7d862-240">导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="7d862-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="7d862-241">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="7d862-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="7d862-242">导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="7d862-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="7d862-243">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="7d862-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="7d862-244">配置 JSON 序列化选项</span><span class="sxs-lookup"><span data-stu-id="7d862-244">Configure JSON serialization options</span></span>

<span data-ttu-id="7d862-245">关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：</span><span class="sxs-lookup"><span data-stu-id="7d862-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="7d862-246">应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。</span><span class="sxs-lookup"><span data-stu-id="7d862-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="7d862-247">`bookName` 属性应作为 `Name` 返回。</span><span class="sxs-lookup"><span data-stu-id="7d862-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="7d862-248">为满足上述要求，请进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="7d862-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="7d862-249">JSON.NET 已从 ASP.NET 共享框架中删除。</span><span class="sxs-lookup"><span data-stu-id="7d862-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="7d862-250">将包引用添加到 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)。</span><span class="sxs-lookup"><span data-stu-id="7d862-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="7d862-251">在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddControllers` 方法调用：</span><span class="sxs-lookup"><span data-stu-id="7d862-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="7d862-252">通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="7d862-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="7d862-253">例如，`Book` 类的 `Author` 属性序列化为 `Author`。</span><span class="sxs-lookup"><span data-stu-id="7d862-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="7d862-254">在 Models/Book.cs 中，使用以下 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性：</span><span class="sxs-lookup"><span data-stu-id="7d862-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="7d862-255">`[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="7d862-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="7d862-256">在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用：</span><span class="sxs-lookup"><span data-stu-id="7d862-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="7d862-257">重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。</span><span class="sxs-lookup"><span data-stu-id="7d862-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="7d862-258">注意 JSON 属性名称中的区别。</span><span class="sxs-lookup"><span data-stu-id="7d862-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7d862-259">本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。</span><span class="sxs-lookup"><span data-stu-id="7d862-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="7d862-260">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="7d862-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7d862-261">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-261">Configure MongoDB</span></span>
> * <span data-ttu-id="7d862-262">创建 MongoDB 数据库</span><span class="sxs-lookup"><span data-stu-id="7d862-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="7d862-263">定义 MongoDB 集合和架构</span><span class="sxs-lookup"><span data-stu-id="7d862-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="7d862-264">从 Web API 执行 MongoDB CRUD 操作</span><span class="sxs-lookup"><span data-stu-id="7d862-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="7d862-265">自定义 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="7d862-265">Customize JSON serialization</span></span>

<span data-ttu-id="7d862-266">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7d862-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7d862-267">先决条件</span><span class="sxs-lookup"><span data-stu-id="7d862-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d862-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d862-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="7d862-269">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="7d862-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="7d862-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="7d862-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="7d862-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d862-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d862-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="7d862-273">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="7d862-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7d862-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d862-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="7d862-275">用于 Visual Studio Code 的 C#</span><span class="sxs-lookup"><span data-stu-id="7d862-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="7d862-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d862-277">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d862-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="7d862-278">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="7d862-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="7d862-279">Visual Studio for Mac 版本 7.7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7d862-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="7d862-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="7d862-281">配置 MongoDB</span><span class="sxs-lookup"><span data-stu-id="7d862-281">Configure MongoDB</span></span>

<span data-ttu-id="7d862-282">如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。</span><span class="sxs-lookup"><span data-stu-id="7d862-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="7d862-283">将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。</span><span class="sxs-lookup"><span data-stu-id="7d862-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="7d862-284">通过此更改可以从开发计算机上的任意位置访问 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="7d862-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="7d862-285">使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。</span><span class="sxs-lookup"><span data-stu-id="7d862-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="7d862-286">有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="7d862-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="7d862-287">选择开发计算机上用于存储数据的目录。</span><span class="sxs-lookup"><span data-stu-id="7d862-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="7d862-288">例如，Windows 上的 C:\\BooksData。</span><span class="sxs-lookup"><span data-stu-id="7d862-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="7d862-289">创建目录（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="7d862-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="7d862-290">mongo Shell 不会创建新目录。</span><span class="sxs-lookup"><span data-stu-id="7d862-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="7d862-291">打开命令行界面。</span><span class="sxs-lookup"><span data-stu-id="7d862-291">Open a command shell.</span></span> <span data-ttu-id="7d862-292">运行以下命令以连接到默认端口 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="7d862-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="7d862-293">请记得将 `<data_directory_path>` 替换为上一步中选择的目录。</span><span class="sxs-lookup"><span data-stu-id="7d862-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="7d862-294">打开另一个命令行界面实例。</span><span class="sxs-lookup"><span data-stu-id="7d862-294">Open another command shell instance.</span></span> <span data-ttu-id="7d862-295">通过运行以下命令来连接到默认测试数据库：</span><span class="sxs-lookup"><span data-stu-id="7d862-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="7d862-296">在命令行界面中运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7d862-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="7d862-297">如果它不存在，则将创建名为 BookstoreDb 的数据库。</span><span class="sxs-lookup"><span data-stu-id="7d862-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="7d862-298">如果该数据库存在，则将为事务打开其连接。</span><span class="sxs-lookup"><span data-stu-id="7d862-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="7d862-299">使用以下命令创建 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="7d862-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="7d862-300">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="7d862-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="7d862-301">使用以下命令定义 `Books` 集合的架构并插入两个文档：</span><span class="sxs-lookup"><span data-stu-id="7d862-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="7d862-302">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="7d862-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="7d862-303">本文所示的 ID 与运行此示例时的 ID 不匹配。</span><span class="sxs-lookup"><span data-stu-id="7d862-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="7d862-304">使用以下命令查看数据库中的文档：</span><span class="sxs-lookup"><span data-stu-id="7d862-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="7d862-305">显示以下结果：</span><span class="sxs-lookup"><span data-stu-id="7d862-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="7d862-306">该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。</span><span class="sxs-lookup"><span data-stu-id="7d862-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="7d862-307">数据库可供使用了。</span><span class="sxs-lookup"><span data-stu-id="7d862-307">The database is ready.</span></span> <span data-ttu-id="7d862-308">你可以开始创建 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="7d862-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="7d862-309">创建 ASP.NET Core Web API 项目</span><span class="sxs-lookup"><span data-stu-id="7d862-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d862-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d862-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7d862-311">转到“文件”>“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="7d862-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="7d862-312">选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="7d862-313">将项目命名为“BooksApi”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="7d862-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="7d862-314">选择“.NET Core”目标框架和“ASP.NET Core 2.2”。</span><span class="sxs-lookup"><span data-stu-id="7d862-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="7d862-315">选择“API”项目模板，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="7d862-316">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="7d862-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7d862-317">在“包管理器控制台”窗口中，导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="7d862-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="7d862-318">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="7d862-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7d862-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7d862-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="7d862-320">在命令行界面中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7d862-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="7d862-321">将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。</span><span class="sxs-lookup"><span data-stu-id="7d862-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="7d862-322">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。是否添加它们?”。</span><span class="sxs-lookup"><span data-stu-id="7d862-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="7d862-323">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="7d862-323">Select **Yes**.</span></span>
1. <span data-ttu-id="7d862-324">访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。</span><span class="sxs-lookup"><span data-stu-id="7d862-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="7d862-325">打开“集成终端”并导航到项目根。</span><span class="sxs-lookup"><span data-stu-id="7d862-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="7d862-326">运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：</span><span class="sxs-lookup"><span data-stu-id="7d862-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7d862-327">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7d862-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7d862-328">在早于版本 8.6 的 Visual Studio for Mac 中，从边栏中依次选择“文件” > “新建解决方案” > “.NET Core” > “应用”。   </span><span class="sxs-lookup"><span data-stu-id="7d862-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="7d862-329">在版本 8.6 或更高版本中，从边栏中依次选择“文件” > “新建解决方案” > “Web 和控制台” > “应用”。   </span><span class="sxs-lookup"><span data-stu-id="7d862-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="7d862-330">选择“ASP.NET Core Web API”C# 项目模板，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="7d862-331">从“目标框架”下拉列表中选择“.NET Core 2.2”，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="7d862-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="7d862-332">输入“BooksApi”作为“项目名称”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="7d862-333">在“解决方案”面板中，右键单击项目的“依赖项”节点，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="7d862-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="7d862-334">在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包” 。</span><span class="sxs-lookup"><span data-stu-id="7d862-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="7d862-335">在“许可证接受”对话框中，选择“接受”按钮 。</span><span class="sxs-lookup"><span data-stu-id="7d862-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="7d862-336">添加实体模型</span><span class="sxs-lookup"><span data-stu-id="7d862-336">Add an entity model</span></span>

1. <span data-ttu-id="7d862-337">将 Models 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="7d862-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="7d862-338">使用以下代码将 `Book` 类添加到 Models 目录：</span><span class="sxs-lookup"><span data-stu-id="7d862-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="7d862-339">在前面的类中，`Id`属性：</span><span class="sxs-lookup"><span data-stu-id="7d862-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="7d862-340">需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。</span><span class="sxs-lookup"><span data-stu-id="7d862-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="7d862-341">使用 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。</span><span class="sxs-lookup"><span data-stu-id="7d862-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="7d862-342">使用 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而非 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构传递。</span><span class="sxs-lookup"><span data-stu-id="7d862-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="7d862-343">Mongo 处理从 `string` 到 `ObjectId` 的转换。</span><span class="sxs-lookup"><span data-stu-id="7d862-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="7d862-344">`BookName` 属性使用 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。</span><span class="sxs-lookup"><span data-stu-id="7d862-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="7d862-345">`Name` 的属性值表示 MongoDB 集合中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="7d862-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="7d862-346">添加配置模型</span><span class="sxs-lookup"><span data-stu-id="7d862-346">Add a configuration model</span></span>

1. <span data-ttu-id="7d862-347">向 appsettings.json 添加以下数据库配置值：</span><span class="sxs-lookup"><span data-stu-id="7d862-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="7d862-348">使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录 ：</span><span class="sxs-lookup"><span data-stu-id="7d862-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="7d862-349">前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值。</span><span class="sxs-lookup"><span data-stu-id="7d862-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="7d862-350">JSON 和 C# 具有相同的属性名称，目的是简化映射过程。</span><span class="sxs-lookup"><span data-stu-id="7d862-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="7d862-351">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="7d862-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="7d862-352">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="7d862-352">In the preceding code:</span></span>

   * <span data-ttu-id="7d862-353">appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册。</span><span class="sxs-lookup"><span data-stu-id="7d862-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="7d862-354">例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充。</span><span class="sxs-lookup"><span data-stu-id="7d862-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="7d862-355">`IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。</span><span class="sxs-lookup"><span data-stu-id="7d862-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="7d862-356">在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。</span><span class="sxs-lookup"><span data-stu-id="7d862-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="7d862-357">在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用：</span><span class="sxs-lookup"><span data-stu-id="7d862-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="7d862-358">添加 CRUD 操作服务</span><span class="sxs-lookup"><span data-stu-id="7d862-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="7d862-359">将 Services 目录添加到项目根。</span><span class="sxs-lookup"><span data-stu-id="7d862-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="7d862-360">使用以下代码将 `BookService` 类添加到 Services 目录：</span><span class="sxs-lookup"><span data-stu-id="7d862-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="7d862-361">上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。</span><span class="sxs-lookup"><span data-stu-id="7d862-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="7d862-362">使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分中添加的 appsettings.json 配置值。</span><span class="sxs-lookup"><span data-stu-id="7d862-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="7d862-363">将以下突出显示的代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="7d862-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="7d862-364">上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。</span><span class="sxs-lookup"><span data-stu-id="7d862-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="7d862-365">单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="7d862-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="7d862-366">根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。</span><span class="sxs-lookup"><span data-stu-id="7d862-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="7d862-367">在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用：</span><span class="sxs-lookup"><span data-stu-id="7d862-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="7d862-368">`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：</span><span class="sxs-lookup"><span data-stu-id="7d862-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="7d862-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：读取用于执行数据库操作的服务器实例。</span><span class="sxs-lookup"><span data-stu-id="7d862-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="7d862-370">此类的构造函数提供了 MongoDB 连接字符串：</span><span class="sxs-lookup"><span data-stu-id="7d862-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="7d862-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：表示用于执行操作的 Mongo 数据库。</span><span class="sxs-lookup"><span data-stu-id="7d862-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="7d862-372">本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。</span><span class="sxs-lookup"><span data-stu-id="7d862-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="7d862-373">调用此方法后，对集合执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="7d862-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="7d862-374">在 `GetCollection<TDocument>(collection)` 方法调用中：</span><span class="sxs-lookup"><span data-stu-id="7d862-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="7d862-375">`collection` 表示集合名称。</span><span class="sxs-lookup"><span data-stu-id="7d862-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="7d862-376">`TDocument` 表示存储在集合中的 CLR 对象类型。</span><span class="sxs-lookup"><span data-stu-id="7d862-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="7d862-377">`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。</span><span class="sxs-lookup"><span data-stu-id="7d862-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="7d862-378">在本教程中，对集合调用以下方法：</span><span class="sxs-lookup"><span data-stu-id="7d862-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="7d862-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：删除与提供的搜索条件匹配的单个文档。</span><span class="sxs-lookup"><span data-stu-id="7d862-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="7d862-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：返回集合中与提供的搜索条件匹配的所有文档。</span><span class="sxs-lookup"><span data-stu-id="7d862-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="7d862-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：插入提供的对象作为集合中的新文档。</span><span class="sxs-lookup"><span data-stu-id="7d862-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="7d862-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：将与提供的搜索条件匹配的单个文档替换为提供的对象。</span><span class="sxs-lookup"><span data-stu-id="7d862-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7d862-383">添加控制器</span><span class="sxs-lookup"><span data-stu-id="7d862-383">Add a controller</span></span>

<span data-ttu-id="7d862-384">使用以下代码将 `BooksController` 类添加到 Controllers 目录：</span><span class="sxs-lookup"><span data-stu-id="7d862-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="7d862-385">前面的 Web API 控制器：</span><span class="sxs-lookup"><span data-stu-id="7d862-385">The preceding web API controller:</span></span>

* <span data-ttu-id="7d862-386">使用 `BookService` 类执行 CRUD 操作。</span><span class="sxs-lookup"><span data-stu-id="7d862-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="7d862-387">包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7d862-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="7d862-388">在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。</span><span class="sxs-lookup"><span data-stu-id="7d862-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="7d862-389">状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。</span><span class="sxs-lookup"><span data-stu-id="7d862-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="7d862-390">`CreatedAtRoute` 还会将 `Location` 标头添加到响应中。</span><span class="sxs-lookup"><span data-stu-id="7d862-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="7d862-391">`Location` 标头指定新建书籍的 URI。</span><span class="sxs-lookup"><span data-stu-id="7d862-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="7d862-392">测试 Web API</span><span class="sxs-lookup"><span data-stu-id="7d862-392">Test the web API</span></span>

1. <span data-ttu-id="7d862-393">生成并运行应用。</span><span class="sxs-lookup"><span data-stu-id="7d862-393">Build and run the app.</span></span>

1. <span data-ttu-id="7d862-394">导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="7d862-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="7d862-395">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="7d862-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="7d862-396">导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="7d862-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="7d862-397">将显示下面的 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="7d862-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="7d862-398">配置 JSON 序列化选项</span><span class="sxs-lookup"><span data-stu-id="7d862-398">Configure JSON serialization options</span></span>

<span data-ttu-id="7d862-399">关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：</span><span class="sxs-lookup"><span data-stu-id="7d862-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="7d862-400">应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。</span><span class="sxs-lookup"><span data-stu-id="7d862-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="7d862-401">`bookName` 属性应作为 `Name` 返回。</span><span class="sxs-lookup"><span data-stu-id="7d862-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="7d862-402">为满足上述要求，请进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="7d862-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="7d862-403">在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddMvc` 方法调用：</span><span class="sxs-lookup"><span data-stu-id="7d862-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="7d862-404">通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。</span><span class="sxs-lookup"><span data-stu-id="7d862-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="7d862-405">例如，`Book` 类的 `Author` 属性序列化为 `Author`。</span><span class="sxs-lookup"><span data-stu-id="7d862-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="7d862-406">在 Models/Book.cs 中，使用以下 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性：</span><span class="sxs-lookup"><span data-stu-id="7d862-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="7d862-407">`[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。</span><span class="sxs-lookup"><span data-stu-id="7d862-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="7d862-408">在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用：</span><span class="sxs-lookup"><span data-stu-id="7d862-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="7d862-409">重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。</span><span class="sxs-lookup"><span data-stu-id="7d862-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="7d862-410">注意 JSON 属性名称中的区别。</span><span class="sxs-lookup"><span data-stu-id="7d862-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="7d862-411">向 Web API 添加身份验证支持</span><span class="sxs-lookup"><span data-stu-id="7d862-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="7d862-412">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7d862-412">Next steps</span></span>

<span data-ttu-id="7d862-413">有关构建 ASP.NET Core Web API 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="7d862-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="7d862-414">本文的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="7d862-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
* [<span data-ttu-id="7d862-415">Microsoft Learn：使用 ASP.NET Core 创建 Web API</span><span class="sxs-lookup"><span data-stu-id="7d862-415">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
