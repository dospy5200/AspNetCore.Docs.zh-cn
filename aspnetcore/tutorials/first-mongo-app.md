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
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>使用 ASP.NET Core 和 MongoDB 创建 Web API

作者 [Pratik Khandelwal](https://twitter.com/K2Prk) 和 [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。

在本教程中，你将了解：

> [!div class="checklist"]
> * 配置 MongoDB
> * 创建 MongoDB 数据库
> * 定义 MongoDB 集合和架构
> * 从 Web API 执行 MongoDB CRUD 操作
> * 自定义 JSON 序列化

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3.0 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3.0 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [用于 Visual Studio Code 的 C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET Core SDK 3.0 或更高版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio for Mac 版本 7.7 或更高版本](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>配置 MongoDB

如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。 将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。 通过此更改可以从开发计算机上的任意位置访问 MongoDB。

使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。 有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。

1. 选择开发计算机上用于存储数据的目录。 例如，Windows 上的 C:\\BooksData。 创建目录（如果不存在）。 mongo Shell 不会创建新目录。
1. 打开命令行界面。 运行以下命令以连接到默认端口 27017 上的 MongoDB。 请记得将 `<data_directory_path>` 替换为上一步中选择的目录。

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. 打开另一个命令行界面实例。 通过运行以下命令来连接到默认测试数据库：

   ```console
   mongo
   ```

1. 在命令行界面中运行下面的命令：

   ```console
   use BookstoreDb
   ```

   如果它不存在，则将创建名为 BookstoreDb 的数据库。 如果该数据库存在，则将为事务打开其连接。

1. 使用以下命令创建 `Books` 集合：

   ```console
   db.createCollection('Books')
   ```

   显示以下结果：

   ```console
   { "ok" : 1 }
   ```

1. 使用以下命令定义 `Books` 集合的架构并插入两个文档：

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   显示以下结果：

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
   > 本文所示的 ID 与运行此示例时的 ID 不匹配。

1. 使用以下命令查看数据库中的文档：

   ```console
   db.Books.find({}).pretty()
   ```

   显示以下结果：

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

   该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。

数据库可供使用了。 你可以开始创建 ASP.NET Core Web API。

## <a name="create-the-aspnet-core-web-api-project"></a>创建 ASP.NET Core Web API 项目

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 转到“文件”>“新建”>“项目”  。
1. 选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步” 。
1. 将项目命名为“BooksApi”，然后选择“创建”。
1. 选择“.NET Core”目标框架和“ASP.NET Core 3.0”。 选择“API”项目模板，然后选择“创建” 。
1. 访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。 在“包管理器控制台”窗口中，导航到项目根。 运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令行界面中运行以下命令：

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。

1. 状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。是否添加它们?”。 选择 **“是”** 。
1. 访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。 打开“集成终端”并导航到项目根。 运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在早于版本 8.6 的 Visual Studio for Mac 中，从边栏中依次选择“文件” > “新建解决方案” > “.NET Core” > “应用”。    在版本 8.6 或更高版本中，从边栏中依次选择“文件” > “新建解决方案” > “Web 和控制台” > “应用”。   
1. 依次选择“ASP.NET Core”>“API”C# 项目模板，然后选择“下一步”。  
1. 从“目标框架”下拉列表中选择“.NET Core 3.1”，然后选择“下一步”  。
1. 输入“BooksApi”作为“项目名称”，然后选择“创建” 。
1. 在“解决方案”面板中，右键单击项目的“依赖项”节点，然后选择“添加包”。
1. 在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包” 。
1. 在“许可证接受”对话框中，选择“接受”按钮 。

---

## <a name="add-an-entity-model"></a>添加实体模型

1. 将 Models 目录添加到项目根。
1. 使用以下代码将 `Book` 类添加到 Models 目录：

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

   在前面的类中，`Id`属性：

   * 需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。
   * 使用 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。
   * 使用 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而非 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构传递。 Mongo 处理从 `string` 到 `ObjectId` 的转换。

   `BookName` 属性使用 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。 `Name` 的属性值表示 MongoDB 集合中的属性名称。

## <a name="add-a-configuration-model"></a>添加配置模型

1. 向 appsettings.json 添加以下数据库配置值：

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. 使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录 ：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值。 JSON 和 C# 具有相同的属性名称，目的是简化映射过程。

1. 将以下突出显示的代码添加到 `Startup.ConfigureServices`：

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   在上述代码中：

   * appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册。 例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充。
   * `IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。 在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。

1. 在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>添加 CRUD 操作服务

1. 将 Services 目录添加到项目根。
1. 使用以下代码将 `BookService` 类添加到 Services 目录：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。 使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分中添加的 appsettings.json 配置值。

1. 将以下突出显示的代码添加到 `Startup.ConfigureServices`：

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。 单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。 根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。

1. 在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：读取用于执行数据库操作的服务器实例。 此类的构造函数提供了 MongoDB 连接字符串：

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：表示用于执行操作的 Mongo 数据库。 本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。 调用此方法后，对集合执行 CRUD 操作。 在 `GetCollection<TDocument>(collection)` 方法调用中：

  * `collection` 表示集合名称。
  * `TDocument` 表示存储在集合中的 CLR 对象类型。

`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。 在本教程中，对集合调用以下方法：

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：删除与提供的搜索条件匹配的单个文档。
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：返回集合中与提供的搜索条件匹配的所有文档。
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：插入提供的对象作为集合中的新文档。
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：将与提供的搜索条件匹配的单个文档替换为提供的对象。

## <a name="add-a-controller"></a>添加控制器

使用以下代码将 `BooksController` 类添加到 Controllers 目录：

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

前面的 Web API 控制器：

* 使用 `BookService` 类执行 CRUD 操作。
* 包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。
* 在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。 状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。 `CreatedAtRoute` 还会将 `Location` 标头添加到响应中。 `Location` 标头指定新建书籍的 URI。

## <a name="test-the-web-api"></a>测试 Web API

1. 生成并运行应用。

1. 导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。 将显示下面的 JSON 响应：

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

1. 导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。 将显示下面的 JSON 响应：

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>配置 JSON 序列化选项

关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：

* 应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。
* `bookName` 属性应作为 `Name` 返回。

为满足上述要求，请进行以下更改：

1. JSON.NET 已从 ASP.NET 共享框架中删除。 将包引用添加到 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)。

1. 在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddControllers` 方法调用：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。 例如，`Book` 类的 `Author` 属性序列化为 `Author`。

1. 在 Models/Book.cs 中，使用以下 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。

1. 在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. 重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。 注意 JSON 属性名称中的区别。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本教程创建对 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 数据库执行创建、读取、更新和删除 (CRUD) 操作的 Web API。

在本教程中，你将了解：

> [!div class="checklist"]
> * 配置 MongoDB
> * 创建 MongoDB 数据库
> * 定义 MongoDB 集合和架构
> * 从 Web API 执行 MongoDB CRUD 操作
> * 自定义 JSON 序列化

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 与 ASP.NET 和 Web 开发工作负载
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [用于 Visual Studio Code 的 C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio for Mac 版本 7.7 或更高版本](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>配置 MongoDB

如果使用的是 Windows，MongoDB 将默认安装在 C:\\Program Files\\MongoDB 中。 将 C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin 添加到 `Path` 环境变量中。 通过此更改可以从开发计算机上的任意位置访问 MongoDB。

使用以下步骤中的 mongo Shell 可以创建数据库、创建集合和存储文档。 有关 mongo Shell 命令的详细信息，请参阅[使用 mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。

1. 选择开发计算机上用于存储数据的目录。 例如，Windows 上的 C:\\BooksData。 创建目录（如果不存在）。 mongo Shell 不会创建新目录。
1. 打开命令行界面。 运行以下命令以连接到默认端口 27017 上的 MongoDB。 请记得将 `<data_directory_path>` 替换为上一步中选择的目录。

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. 打开另一个命令行界面实例。 通过运行以下命令来连接到默认测试数据库：

   ```console
   mongo
   ```

1. 在命令行界面中运行下面的命令：

   ```console
   use BookstoreDb
   ```

   如果它不存在，则将创建名为 BookstoreDb 的数据库。 如果该数据库存在，则将为事务打开其连接。

1. 使用以下命令创建 `Books` 集合：

   ```console
   db.createCollection('Books')
   ```

   显示以下结果：

   ```console
   { "ok" : 1 }
   ```

1. 使用以下命令定义 `Books` 集合的架构并插入两个文档：

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   显示以下结果：

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
   > 本文所示的 ID 与运行此示例时的 ID 不匹配。

1. 使用以下命令查看数据库中的文档：

   ```console
   db.Books.find({}).pretty()
   ```

   显示以下结果：

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

   该架构将为每个文档添加类型 `ObjectId` 的自动生成的 `_id` 属性。

数据库可供使用了。 你可以开始创建 ASP.NET Core Web API。

## <a name="create-the-aspnet-core-web-api-project"></a>创建 ASP.NET Core Web API 项目

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 转到“文件”>“新建”>“项目”  。
1. 选择“ASP.NET Core Web 应用程序”项目类型，然后选择“下一步” 。
1. 将项目命名为“BooksApi”，然后选择“创建”。
1. 选择“.NET Core”目标框架和“ASP.NET Core 2.2”。 选择“API”项目模板，然后选择“创建” 。
1. 访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。 在“包管理器控制台”窗口中，导航到项目根。 运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令行界面中运行以下命令：

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   将在 Visual Studio Code 中生成并打开以 .NET Core 为目标的新 ASP.NET Core Web API 项目。

1. 状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'BooksApi' 缺少生成和调试所需的资产。是否添加它们?”。 选择 **“是”** 。
1. 访问 [NuGet 库：MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) 来确定适用于 MongoDB 的 .NET 驱动程序的最新稳定版本。 打开“集成终端”并导航到项目根。 运行以下命令以安装适用于 MongoDB 的 .NET 驱动程序：

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在早于版本 8.6 的 Visual Studio for Mac 中，从边栏中依次选择“文件” > “新建解决方案” > “.NET Core” > “应用”。    在版本 8.6 或更高版本中，从边栏中依次选择“文件” > “新建解决方案” > “Web 和控制台” > “应用”。   
1. 选择“ASP.NET Core Web API”C# 项目模板，然后选择“下一步” 。
1. 从“目标框架”下拉列表中选择“.NET Core 2.2”，然后选择“下一步”  。
1. 输入“BooksApi”作为“项目名称”，然后选择“创建” 。
1. 在“解决方案”面板中，右键单击项目的“依赖项”节点，然后选择“添加包”。
1. 在搜索框中输入“MongoDB.Driver”，选择“MongoDB.Driver”包，然后选择“添加包” 。
1. 在“许可证接受”对话框中，选择“接受”按钮 。

---

## <a name="add-an-entity-model"></a>添加实体模型

1. 将 Models 目录添加到项目根。
1. 使用以下代码将 `Book` 类添加到 Models 目录：

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

   在前面的类中，`Id`属性：

   * 需要在将公共语言运行时 (CLR) 对象映射到 MongoDB 集合时使用。
   * 使用 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 进行批注，以将此属性指定为文档的主键。
   * 使用 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 进行批注，以允许将参数作为类型 `string` 而非 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 结构传递。 Mongo 处理从 `string` 到 `ObjectId` 的转换。

   `BookName` 属性使用 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 特性进行批注。 `Name` 的属性值表示 MongoDB 集合中的属性名称。

## <a name="add-a-configuration-model"></a>添加配置模型

1. 向 appsettings.json 添加以下数据库配置值：

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. 使用以下代码将 BookstoreDatabaseSettings.cs 文件添加到 Models 目录 ：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   前面的 `BookstoreDatabaseSettings` 类用于存储 appsettings.json 文件的 `BookstoreDatabaseSettings` 属性值。 JSON 和 C# 具有相同的属性名称，目的是简化映射过程。

1. 将以下突出显示的代码添加到 `Startup.ConfigureServices`：

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   在上述代码中：

   * appsettings.json 文件的 `BookstoreDatabaseSettings` 部分绑定到的配置实例在依赖关系注入 (DI) 容器中注册。 例如，`BookstoreDatabaseSettings` 对象的 `ConnectionString` 属性使用 appsettings.json 中的 `BookstoreDatabaseSettings:ConnectionString` 属性进行填充。
   * `IBookstoreDatabaseSettings` 接口使用单一实例[服务生存期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中注册。 在注入时，接口实例时将解析为 `BookstoreDatabaseSettings` 对象。

1. 在 Startup.cs 顶部添加以下代码，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 引用：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>添加 CRUD 操作服务

1. 将 Services 目录添加到项目根。
1. 使用以下代码将 `BookService` 类添加到 Services 目录：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   上面的代码通过构造函数注入从 DI 检索 `IBookstoreDatabaseSettings` 实例。 使用此方法可访问在[添加配置模型](#add-a-configuration-model)部分中添加的 appsettings.json 配置值。

1. 将以下突出显示的代码添加到 `Startup.ConfigureServices`：

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   上面的代码向 DI 注册了 `BookService` 类，以支持消费类中的构造函数注入。 单一实例服务生存期是最合适的，因为 `BookService` 直接依赖于 `MongoClient`。 根据官方 [Mongo Client 重用准则](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)，应使用单一实例服务生存期在 DI 中注册 `MongoClient`。

1. 在 Startup.cs 顶部添加以下代码，以解析 `BookService` 引用：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService` 类使用以下 `MongoDB.Driver` 成员对数据库执行 CRUD 操作：

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：读取用于执行数据库操作的服务器实例。 此类的构造函数提供了 MongoDB 连接字符串：

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：表示用于执行操作的 Mongo 数据库。 本教程在界面上使用泛型 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法来获取对特定集合中的数据的访问权限。 调用此方法后，对集合执行 CRUD 操作。 在 `GetCollection<TDocument>(collection)` 方法调用中：

  * `collection` 表示集合名称。
  * `TDocument` 表示存储在集合中的 CLR 对象类型。

`GetCollection<TDocument>(collection)` 返回表示集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 对象。 在本教程中，对集合调用以下方法：

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：删除与提供的搜索条件匹配的单个文档。
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：返回集合中与提供的搜索条件匹配的所有文档。
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：插入提供的对象作为集合中的新文档。
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：将与提供的搜索条件匹配的单个文档替换为提供的对象。

## <a name="add-a-controller"></a>添加控制器

使用以下代码将 `BooksController` 类添加到 Controllers 目录：

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

前面的 Web API 控制器：

* 使用 `BookService` 类执行 CRUD 操作。
* 包含操作方法以支持 GET、POST、PUT 和 DELETE HTTP 请求。
* 在 `Create` 操作方法中调用 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以返回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 响应。 状态代码 201 是在服务器上创建新资源的 HTTP POST 方法的标准响应。 `CreatedAtRoute` 还会将 `Location` 标头添加到响应中。 `Location` 标头指定新建书籍的 URI。

## <a name="test-the-web-api"></a>测试 Web API

1. 生成并运行应用。

1. 导航到 `http://localhost:<port>/api/books`，测试控制器的无参数 `Get` 操作方法。 将显示下面的 JSON 响应：

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

1. 导航到 `http://localhost:<port>/api/books/{id here}`，测试控制器的重载 `Get` 操作方法。 将显示下面的 JSON 响应：

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>配置 JSON 序列化选项

关于在[测试 Web API](#test-the-web-api) 部分中返回的 JSON 响应，有两个细节需要更改：

* 应更改属性名称的默认驼峰式大小写风格，以匹配 CLR 对象属性名称的 Pascal 大小写。
* `bookName` 属性应作为 `Name` 返回。

为满足上述要求，请进行以下更改：

1. 在 `Startup.ConfigureServices` 中，将以下突出显示的代码链接到 `AddMvc` 方法调用：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   通过上述更改，Web API 的序列化 JSON 响应中的属性名称与 CLR 对象类型中其相应的属性名称匹配。 例如，`Book` 类的 `Author` 属性序列化为 `Author`。

1. 在 Models/Book.cs 中，使用以下 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 特性批注 `BookName` 属性：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]` 属性的 `Name` 值表示 Web API 的序列化 JSON 响应中的属性名称。

1. 在 Models/Book.cs 顶部添加以下代码，以解析 `[JsonProperty]` 属性引用：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. 重复[测试 Web API](#test-the-web-api) 部分中定义的步骤。 注意 JSON 属性名称中的区别。

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>向 Web API 添加身份验证支持

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>后续步骤

有关构建 ASP.NET Core Web API 的详细信息，请参阅以下资源：

* [本文的 YouTube 版本](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
* [Microsoft Learn：使用 ASP.NET Core 创建 Web API](/learn/modules/build-web-api-aspnet-core/)
