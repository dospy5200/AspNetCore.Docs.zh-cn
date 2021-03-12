---
title: 教程：创建复杂数据模型 - ASP.NET MVC 和 EF Core
description: 本教程将添加更多实体和关系，并通过指定格式设置、验证和映射规则来自定义数据模型。
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: a881ff28d41a272ade559c60efbd884f2a3c4e3e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587783"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a><span data-ttu-id="1e72c-103">教程：创建复杂数据模型 - ASP.NET MVC 和 EF Core</span><span class="sxs-lookup"><span data-stu-id="1e72c-103">Tutorial: Create a complex data model - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="1e72c-104">之前的教程介绍了由三个实体组成的简单数据模型。</span><span class="sxs-lookup"><span data-stu-id="1e72c-104">In the previous tutorials, you worked with a simple data model that was composed of three entities.</span></span> <span data-ttu-id="1e72c-105">本教程将添加更多实体和关系，并通过指定格式化、验证和数据库映射规则来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="1e72c-105">In this tutorial, you'll add more entities and relationships and you'll customize the data model by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="1e72c-106">完成本教程后，实体类将构成下图所示的完整数据模型：</span><span class="sxs-lookup"><span data-stu-id="1e72c-106">When you're finished, the entity classes will make up the completed data model that's shown in the following illustration:</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="1e72c-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="1e72c-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1e72c-109">自定义数据模型</span><span class="sxs-lookup"><span data-stu-id="1e72c-109">Customize the Data model</span></span>
> * <span data-ttu-id="1e72c-110">更改 Student 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-110">Make changes to Student entity</span></span>
> * <span data-ttu-id="1e72c-111">创建 Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-111">Create Instructor entity</span></span>
> * <span data-ttu-id="1e72c-112">创建 OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-112">Create OfficeAssignment entity</span></span>
> * <span data-ttu-id="1e72c-113">修改 Course 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-113">Modify Course entity</span></span>
> * <span data-ttu-id="1e72c-114">创建 Department 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-114">Create Department entity</span></span>
> * <span data-ttu-id="1e72c-115">修改 Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-115">Modify Enrollment entity</span></span>
> * <span data-ttu-id="1e72c-116">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="1e72c-116">Update the database context</span></span>
> * <span data-ttu-id="1e72c-117">使用测试数据设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="1e72c-117">Seed database with test data</span></span>
> * <span data-ttu-id="1e72c-118">添加迁移</span><span class="sxs-lookup"><span data-stu-id="1e72c-118">Add a migration</span></span>
> * <span data-ttu-id="1e72c-119">更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="1e72c-119">Change the connection string</span></span>
> * <span data-ttu-id="1e72c-120">更新数据库</span><span class="sxs-lookup"><span data-stu-id="1e72c-120">Update the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1e72c-121">先决条件</span><span class="sxs-lookup"><span data-stu-id="1e72c-121">Prerequisites</span></span>

* [<span data-ttu-id="1e72c-122">使用 EF Core 迁移</span><span class="sxs-lookup"><span data-stu-id="1e72c-122">Using EF Core migrations</span></span>](migrations.md)

## <a name="customize-the-data-model"></a><span data-ttu-id="1e72c-123">自定义数据模型</span><span class="sxs-lookup"><span data-stu-id="1e72c-123">Customize the Data model</span></span>

<span data-ttu-id="1e72c-124">本节介绍如何使用指定格式化、验证和数据库映射规则的特性来自定义数据模型。</span><span class="sxs-lookup"><span data-stu-id="1e72c-124">In this section you'll see how to customize the data model by using attributes that specify formatting, validation, and database mapping rules.</span></span> <span data-ttu-id="1e72c-125">随后在接下来的几节中创建完整的学校数据模型，创建方法：向已创建的类添加特性，并为模型中剩余的实体类型创建新类。</span><span class="sxs-lookup"><span data-stu-id="1e72c-125">Then in several of the following sections you'll create the complete School data model by adding attributes to the classes you already created and creating new classes for the remaining entity types in the model.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="1e72c-126">DataType 特性</span><span class="sxs-lookup"><span data-stu-id="1e72c-126">The DataType attribute</span></span>

<span data-ttu-id="1e72c-127">对于学生注册日期，目前所有网页都显示有时间和日期，尽管对此字段而言重要的只是日期。</span><span class="sxs-lookup"><span data-stu-id="1e72c-127">For student enrollment dates, all of the web pages currently display the time along with the date, although all you care about for this field is the date.</span></span> <span data-ttu-id="1e72c-128">使用数据注释特性，可更改一次代码，修复每个视图中数据的显示格式。</span><span class="sxs-lookup"><span data-stu-id="1e72c-128">By using data annotation attributes, you can make one code change that will fix the display format in every view that shows the data.</span></span> <span data-ttu-id="1e72c-129">若要查看如何执行此操作，请向 `Student` 类的 `EnrollmentDate` 属性添加一个特性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-129">To see an example of how to do that, you'll add an attribute to the `EnrollmentDate` property in the `Student` class.</span></span>

<span data-ttu-id="1e72c-130">在 Models/Student.cs 中，为 `System.ComponentModel.DataAnnotations` 命名空间添加一个 `using` 语句，将 `DataType` 和 `DisplayFormat` 特性添加到 `EnrollmentDate` 属性，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="1e72c-130">In *Models/Student.cs*, add a `using` statement for the `System.ComponentModel.DataAnnotations` namespace and add `DataType` and `DisplayFormat` attributes to the `EnrollmentDate` property, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="1e72c-131">`DataType` 属性用于指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="1e72c-131">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="1e72c-132">在此示例中，我们只想跟踪日期，而不是日期和时间。</span><span class="sxs-lookup"><span data-stu-id="1e72c-132">In this case we only want to keep track of the date, not the date and time.</span></span> <span data-ttu-id="1e72c-133">`DataType` 枚举提供了多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。</span><span class="sxs-lookup"><span data-stu-id="1e72c-133">The  `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="1e72c-134">应用程序还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="1e72c-134">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="1e72c-135">例如，可以为 `DataType.EmailAddress` 创建 `mailto:` 链接，并且可以在支持 HTML5 的浏览器中为 `DataType.Date` 提供日期选择器。</span><span class="sxs-lookup"><span data-stu-id="1e72c-135">For example, a `mailto:` link can be created for `DataType.EmailAddress`, and a date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="1e72c-136">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器理解。</span><span class="sxs-lookup"><span data-stu-id="1e72c-136">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers can understand.</span></span> <span data-ttu-id="1e72c-137">`DataType` 特性不提供任何验证。</span><span class="sxs-lookup"><span data-stu-id="1e72c-137">The `DataType` attributes don't provide any validation.</span></span>

<span data-ttu-id="1e72c-138">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="1e72c-138">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="1e72c-139">默认情况下，数据字段根据默认格式显示，后者以服务器的 CultureInfo 为基础。</span><span class="sxs-lookup"><span data-stu-id="1e72c-139">By default, the data field is displayed according to the default formats based on the server's CultureInfo.</span></span>

<span data-ttu-id="1e72c-140">`DisplayFormat` 特性用于显式指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="1e72c-140">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="1e72c-141">`ApplyFormatInEditMode` 设置指定在文本框中显示值以进行编辑时也应用格式。</span><span class="sxs-lookup"><span data-stu-id="1e72c-141">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied when the value is displayed in a text box for editing.</span></span> <span data-ttu-id="1e72c-142">（你可能不想为某些字段执行此操作 — 例如，对于货币值，你可能不希望文本框中的货币符号可编辑。）</span><span class="sxs-lookup"><span data-stu-id="1e72c-142">(You might not want that for some fields -- for example, for currency values, you might not want the currency symbol in the text box for editing.)</span></span>

<span data-ttu-id="1e72c-143">可单独使用 `DisplayFormat` 特性，但通常建议同时使用 `DataType` 特性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-143">You can use the `DisplayFormat` attribute by itself, but it's generally a good idea to use the `DataType` attribute also.</span></span> <span data-ttu-id="1e72c-144">`DataType` 特性传达数据的语义而不是传达数据在屏幕上的呈现方式，并提供 `DisplayFormat` 不具备的以下优势：</span><span class="sxs-lookup"><span data-stu-id="1e72c-144">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with `DisplayFormat`:</span></span>

* <span data-ttu-id="1e72c-145">浏览器可启用 HTML5 功能（例如，显示日历控件、区域设置适用的货币符号、电子邮件链接、某种客户端输入验证等）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-145">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, some client-side input validation, etc.).</span></span>

* <span data-ttu-id="1e72c-146">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="1e72c-146">By default, the browser will render data using the correct format based on your locale.</span></span>

<span data-ttu-id="1e72c-147">有关详细信息，请参阅 [\<input> 标记帮助程序文档](../../mvc/views/working-with-forms.md#the-input-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="1e72c-147">For more information, see the [\<input> tag helper documentation](../../mvc/views/working-with-forms.md#the-input-tag-helper).</span></span>

<span data-ttu-id="1e72c-148">运行应用，进入“学生”索引页，注意注册日期中不再显示时间。</span><span class="sxs-lookup"><span data-stu-id="1e72c-148">Run the app, go to the Students Index page and notice that times are no longer displayed for the enrollment dates.</span></span> <span data-ttu-id="1e72c-149">任何使用“学生”模型的视图都是如此。</span><span class="sxs-lookup"><span data-stu-id="1e72c-149">The same will be true for any view that uses the Student model.</span></span>

![“学生”索引页显示不带时间的日期](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="1e72c-151">StringLength 特性</span><span class="sxs-lookup"><span data-stu-id="1e72c-151">The StringLength attribute</span></span>

<span data-ttu-id="1e72c-152">还可使用特性指定数据验证规则和验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="1e72c-152">You can also specify data validation rules and validation error messages using attributes.</span></span> <span data-ttu-id="1e72c-153">`StringLength` 特性设置数据库中的最大长度，并为 ASP.NET Core MVC 提供客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="1e72c-153">The `StringLength` attribute sets the maximum length  in the database and provides client side and server side validation for ASP.NET Core MVC.</span></span> <span data-ttu-id="1e72c-154">还可在此属性中指定最小字符串长度，但最小值对数据库架构没有影响。</span><span class="sxs-lookup"><span data-stu-id="1e72c-154">You can also specify the minimum string length in this attribute, but the minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="1e72c-155">假设要确保用户输入的名称不超过 50 个字符。</span><span class="sxs-lookup"><span data-stu-id="1e72c-155">Suppose you want to ensure that users don't enter more than 50 characters for a name.</span></span> <span data-ttu-id="1e72c-156">若要添加此限制，请将 `StringLength` 特性添加到 `LastName` 和 `FirstMidName` 属性，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="1e72c-156">To add this limitation, add `StringLength` attributes to the `LastName` and `FirstMidName` properties, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="1e72c-157">`StringLength` 特性不会阻止用户在名称中输入空格。</span><span class="sxs-lookup"><span data-stu-id="1e72c-157">The `StringLength` attribute won't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="1e72c-158">可使用 `RegularExpression` 特性应用输入限制。</span><span class="sxs-lookup"><span data-stu-id="1e72c-158">You can use the `RegularExpression` attribute to apply restrictions to the input.</span></span> <span data-ttu-id="1e72c-159">例如，以下代码要求第一个字符为大写，其余字符按字母顺序排列：</span><span class="sxs-lookup"><span data-stu-id="1e72c-159">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="1e72c-160">`MaxLength` 特性的作用与 `StringLength` 特性类似，但不提供客户端验证。</span><span class="sxs-lookup"><span data-stu-id="1e72c-160">The `MaxLength` attribute provides functionality similar to the `StringLength` attribute but doesn't provide client side validation.</span></span>

<span data-ttu-id="1e72c-161">现在数据库模型发生了变化，需要更改数据库架构。</span><span class="sxs-lookup"><span data-stu-id="1e72c-161">The database model has now changed in a way that requires a change in the database schema.</span></span> <span data-ttu-id="1e72c-162">请使用迁移来更新架构，迁移时不会丢失通过应用程序 UI 添加到数据库的任何数据。</span><span class="sxs-lookup"><span data-stu-id="1e72c-162">You'll use migrations to update the schema without losing any data that you may have added to the database by using the application UI.</span></span>

<span data-ttu-id="1e72c-163">保存更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="1e72c-163">Save your changes and build the project.</span></span> <span data-ttu-id="1e72c-164">随后打开项目文件夹中的命令窗口并输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="1e72c-164">Then open the command window in the project folder and enter the following commands:</span></span>

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="1e72c-165">`migrations add` 命令发出警告：可能出现数据丢失，因为更改后，有两列的最大长度缩短。</span><span class="sxs-lookup"><span data-stu-id="1e72c-165">The `migrations add` command warns that data loss may occur, because the change makes the maximum length shorter for two columns.</span></span>  <span data-ttu-id="1e72c-166">迁移时会创建一个名为 \<timeStamp>_MaxLengthOnNames.cs 的文件。</span><span class="sxs-lookup"><span data-stu-id="1e72c-166">Migrations creates a file named *\<timeStamp>_MaxLengthOnNames.cs*.</span></span> <span data-ttu-id="1e72c-167">此文件包含 `Up` 方法中的代码，该代码将更新数据库以匹配当前数据模型。</span><span class="sxs-lookup"><span data-stu-id="1e72c-167">This file contains code in the `Up` method that will update the database to match the current data model.</span></span> <span data-ttu-id="1e72c-168">`database update` 命令运行该代码。</span><span class="sxs-lookup"><span data-stu-id="1e72c-168">The `database update` command ran that code.</span></span>

<span data-ttu-id="1e72c-169">Entity Framework 使用迁移文件名的前缀时间戳发出迁移命令。</span><span class="sxs-lookup"><span data-stu-id="1e72c-169">The timestamp prefixed to the migrations file name is used by Entity Framework to order the migrations.</span></span> <span data-ttu-id="1e72c-170">可在运行 update-database 命令前创建多个迁移，然后按照创建顺序应用所有迁移。</span><span class="sxs-lookup"><span data-stu-id="1e72c-170">You can create multiple migrations before running the update-database command, and then all of the migrations are applied in the order in which they were created.</span></span>

<span data-ttu-id="1e72c-171">运行该应用，选择“学生”选项卡，单击“新建”，然后尝试输入名称（超过 50 个字符）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-171">Run the app, select the **Students** tab, click **Create New**, and try to enter either name longer than 50 characters.</span></span> <span data-ttu-id="1e72c-172">应用程序应该会阻止你执行此操作。</span><span class="sxs-lookup"><span data-stu-id="1e72c-172">The application should prevent you from doing this.</span></span> 

### <a name="the-column-attribute"></a><span data-ttu-id="1e72c-173">Column 特性</span><span class="sxs-lookup"><span data-stu-id="1e72c-173">The Column attribute</span></span>

<span data-ttu-id="1e72c-174">还可使用特性来控制类和属性映射到数据库的方式。</span><span class="sxs-lookup"><span data-stu-id="1e72c-174">You can also use attributes to control how your classes and properties are mapped to the database.</span></span> <span data-ttu-id="1e72c-175">假设在名字字段使用了 `FirstMidName`，这是因为该字段也可能包含中间名。</span><span class="sxs-lookup"><span data-stu-id="1e72c-175">Suppose you had used the name `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span> <span data-ttu-id="1e72c-176">但却希望将数据库列命名为 `FirstName`，因为要针对数据库编写即席查询的用户习惯使用该姓名。</span><span class="sxs-lookup"><span data-stu-id="1e72c-176">But you want the database column to be named `FirstName`, because users who will be writing ad-hoc queries against the database are accustomed to that name.</span></span> <span data-ttu-id="1e72c-177">若要进行此映射，可使用 `Column` 特性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-177">To make this mapping, you can use the `Column` attribute.</span></span>

<span data-ttu-id="1e72c-178">`Column` 特性指定，创建数据库时，映射到 `FirstMidName` 属性的 `Student` 表的列将被命名为 `FirstName`。</span><span class="sxs-lookup"><span data-stu-id="1e72c-178">The `Column` attribute specifies that when the database is created, the column of the `Student` table that maps to the `FirstMidName` property will be named `FirstName`.</span></span> <span data-ttu-id="1e72c-179">换言之，在代码引用 `Student.FirstMidName` 时，数据来源将是 `Student` 表的 `FirstName` 列或在其中进行更新。</span><span class="sxs-lookup"><span data-stu-id="1e72c-179">In other words, when your code refers to `Student.FirstMidName`, the data will come from or be updated in the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="1e72c-180">如果不指定列名称，则其名称与属性名称相同。</span><span class="sxs-lookup"><span data-stu-id="1e72c-180">If you don't specify column names, they're given the same name as the property name.</span></span>

<span data-ttu-id="1e72c-181">在 Student.cs 文件中，为 `System.ComponentModel.DataAnnotations.Schema` 添加一个 `using` 语句，并将列名称特性添加到 `FirstMidName` 属性，如以下突出显示的代码所示：</span><span class="sxs-lookup"><span data-stu-id="1e72c-181">In the *Student.cs* file, add a `using` statement for `System.ComponentModel.DataAnnotations.Schema` and add the column name attribute to the `FirstMidName` property, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="1e72c-182">添加 `Column` 特性后，`SchoolContext` 的支持模型会发生改变，与数据库不再匹配。</span><span class="sxs-lookup"><span data-stu-id="1e72c-182">The addition of the `Column` attribute changes the model backing the `SchoolContext`, so it won't match the database.</span></span>

<span data-ttu-id="1e72c-183">保存更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="1e72c-183">Save your changes and build the project.</span></span> <span data-ttu-id="1e72c-184">随后打开项目文件夹中的命令窗口，输入以下命令，创建另一个迁移：</span><span class="sxs-lookup"><span data-stu-id="1e72c-184">Then open the command window in the project folder and enter the following commands to create another migration:</span></span>

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="1e72c-185">在 SQL Server 对象资源管理器中，双击 Student 表，打开 Student 表设计器 。</span><span class="sxs-lookup"><span data-stu-id="1e72c-185">In **SQL Server Object Explorer**, open the Student table designer by double-clicking the **Student** table.</span></span>

![迁移后 SSOX 中的 Students 表](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="1e72c-187">在进行前两次迁移前，名称列的类型为 nvarchar (MAX)。</span><span class="sxs-lookup"><span data-stu-id="1e72c-187">Before you applied the first two migrations, the name columns were of type nvarchar(MAX).</span></span> <span data-ttu-id="1e72c-188">而现在则是 nvarchar (50)，列名从 FirstMidName 变为 FirstName。</span><span class="sxs-lookup"><span data-stu-id="1e72c-188">They're now nvarchar(50) and the column name has changed from FirstMidName to FirstName.</span></span>

> [!Note]
> <span data-ttu-id="1e72c-189">如果尚未按以下各节所述创建所有实体类就尝试进行编译，则可能会出现编译器错误。</span><span class="sxs-lookup"><span data-stu-id="1e72c-189">If you try to compile before you finish creating all of the entity classes in the following sections, you might get compiler errors.</span></span>

## <a name="changes-to-student-entity"></a><span data-ttu-id="1e72c-190">Student 实体的更改</span><span class="sxs-lookup"><span data-stu-id="1e72c-190">Changes to Student entity</span></span>

![Student 实体](complex-data-model/_static/student-entity.png)

<span data-ttu-id="1e72c-192">在 Models/Student.cs 中，将之前添加的代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="1e72c-192">In *Models/Student.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="1e72c-193">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="1e72c-193">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="1e72c-194">Required 特性</span><span class="sxs-lookup"><span data-stu-id="1e72c-194">The Required attribute</span></span>

<span data-ttu-id="1e72c-195">`Required` 特性使名称属性成为必填字段。</span><span class="sxs-lookup"><span data-stu-id="1e72c-195">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="1e72c-196">值类型（DateTime、int、double、float 等）等不可为 null 的类型不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-196">The `Required` attribute isn't needed for non-nullable types such as value types (DateTime, int, double, float, etc.).</span></span> <span data-ttu-id="1e72c-197">系统会将不可为 NULL 的类型自动视为必填字段。</span><span class="sxs-lookup"><span data-stu-id="1e72c-197">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="1e72c-198">`Required` 特性必须与 `MinimumLength` 结合使用才能强制执行 `MinimumLength`。</span><span class="sxs-lookup"><span data-stu-id="1e72c-198">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="1e72c-199">Display 特性</span><span class="sxs-lookup"><span data-stu-id="1e72c-199">The Display attribute</span></span>

<span data-ttu-id="1e72c-200">`Display` 特性指定文本框的标题应是“名”、“姓”、“全名”和“注册日期”，而不是每个实例中的属性名称（其中没有分隔单词的空格）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-200">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date" instead of the property name in each instance (which has no space dividing the words).</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="1e72c-201">FullName 计算属性</span><span class="sxs-lookup"><span data-stu-id="1e72c-201">The FullName calculated property</span></span>

<span data-ttu-id="1e72c-202">`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。</span><span class="sxs-lookup"><span data-stu-id="1e72c-202">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="1e72c-203">因此它只有一个 get 访问器，且数据库中不会生成 `FullName` 列。</span><span class="sxs-lookup"><span data-stu-id="1e72c-203">Therefore it has only a get accessor, and no `FullName` column will be generated in the database.</span></span>

## <a name="create-instructor-entity"></a><span data-ttu-id="1e72c-204">创建 Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-204">Create Instructor entity</span></span>

![Instructor 实体](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="1e72c-206">创建 Models/Instructor.cs，使用以下代码替换模板代码：</span><span class="sxs-lookup"><span data-stu-id="1e72c-206">Create *Models/Instructor.cs*, replacing the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

<span data-ttu-id="1e72c-207">请注意，在 Student 和 Instructor 实体中有几个属性是相同的。</span><span class="sxs-lookup"><span data-stu-id="1e72c-207">Notice that several properties are the same in the Student and Instructor entities.</span></span> <span data-ttu-id="1e72c-208">本系列后面的[实现继承](inheritance.md)教程将重构此代码以消除冗余。</span><span class="sxs-lookup"><span data-stu-id="1e72c-208">In the [Implementing Inheritance](inheritance.md) tutorial later in this series, you'll refactor this code to eliminate the redundancy.</span></span>

<span data-ttu-id="1e72c-209">可在一行中放置多个特性，因此也可以按如下所示编写 `HireDate` 特性：</span><span class="sxs-lookup"><span data-stu-id="1e72c-209">You can put multiple attributes on one line, so you could also write the `HireDate` attributes as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="1e72c-210">CourseAssignments 和 OfficeAssignment 导航属性</span><span class="sxs-lookup"><span data-stu-id="1e72c-210">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="1e72c-211">`CourseAssignments` 和 `OfficeAssignment` 是导航属性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-211">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="1e72c-212">一名讲师可以教授任意数量的课程，因此 `CourseAssignments` 定义为集合。</span><span class="sxs-lookup"><span data-stu-id="1e72c-212">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="1e72c-213">如果导航属性可包含多个实体，则其类型必须是可添加、可删除和可更新实体的列表。</span><span class="sxs-lookup"><span data-stu-id="1e72c-213">If a navigation property can hold multiple entities, its type must be a list in which entries can be added, deleted, and updated.</span></span>  <span data-ttu-id="1e72c-214">可指定 `ICollection<T>` 或诸如 `List<T>` 或 `HashSet<T>` 的类型。</span><span class="sxs-lookup"><span data-stu-id="1e72c-214">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="1e72c-215">如果指定 `ICollection<T>`，EF 默认会创建一个 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="1e72c-215">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="1e72c-216">下面在关于多对多关系的部分中解释了这些作为 `CourseAssignment` 实体的原因。</span><span class="sxs-lookup"><span data-stu-id="1e72c-216">The reason why these are `CourseAssignment` entities is explained below in the section about many-to-many relationships.</span></span>

<span data-ttu-id="1e72c-217">Contoso University 业务规则规定，讲师最多只能有一个办公室，因此 `OfficeAssignment` 属性拥有一个 OfficeAssignment 实体（如果未分配办公室，则该实体可能为 null）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-217">Contoso University business rules state that an instructor can only have at most one office, so the `OfficeAssignment` property holds a single OfficeAssignment entity (which may be null if no office is assigned).</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a><span data-ttu-id="1e72c-218">创建 OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-218">Create OfficeAssignment entity</span></span>

![OfficeAssignment 实体](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="1e72c-220">用以下代码创建 Models/OfficeAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="1e72c-220">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="1e72c-221">Key 特性</span><span class="sxs-lookup"><span data-stu-id="1e72c-221">The Key attribute</span></span>

<span data-ttu-id="1e72c-222">讲师与 OfficeAssignment 实体间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="1e72c-222">There's a one-to-zero-or-one relationship  between the Instructor and the OfficeAssignment entities.</span></span> <span data-ttu-id="1e72c-223">办公室分配仅与分配有办公室的讲师相关，因此其主键也是 Instructor 实体的外键。</span><span class="sxs-lookup"><span data-stu-id="1e72c-223">An office assignment only exists in relation to the instructor it's assigned to, and therefore its primary key is also its foreign key to the Instructor entity.</span></span> <span data-ttu-id="1e72c-224">但 Entity Framework 无法自动识别 InstructorID 作为此实体的主键，因为其名称不符合 ID 或 classnameID 命名约定。</span><span class="sxs-lookup"><span data-stu-id="1e72c-224">But the Entity Framework can't automatically recognize InstructorID as the primary key of this entity because its name doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="1e72c-225">因此，`Key` 特性用于将其识别为主键：</span><span class="sxs-lookup"><span data-stu-id="1e72c-225">Therefore, the `Key` attribute is used to identify it as the key:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="1e72c-226">如果实体具有自己的主键，但你希望使用 classnameID 或 ID 以外的其他属性名称，则也可使用 `Key` 特性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-226">You can also use the `Key` attribute if the entity does have its own primary key but you want to name the property something other than classnameID or ID.</span></span>

<span data-ttu-id="1e72c-227">默认情况下，EF 将键视为非数据库生成，因为该列面向的是识别关系。</span><span class="sxs-lookup"><span data-stu-id="1e72c-227">By default, EF treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="1e72c-228">Instructor 导航属性</span><span class="sxs-lookup"><span data-stu-id="1e72c-228">The Instructor navigation property</span></span>

<span data-ttu-id="1e72c-229">Instructor 实体具有可为 null `OfficeAssignment` 导航属性（因为可能未向讲师分配办公室），而 OfficeAssignment 实体具有不可为 null `Instructor` 导航属性 （因为在没有讲师的情况下不会分配办公室 - `InstructorID` 不可为 null）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-229">The Instructor entity has a nullable `OfficeAssignment` navigation property (because an instructor might not have an office assignment), and the OfficeAssignment entity has a non-nullable `Instructor` navigation property (because an office assignment can't exist without an instructor -- `InstructorID` is non-nullable).</span></span> <span data-ttu-id="1e72c-230">Instructor 实体具有相关 OfficeAssignment 实体时，每个实体都将在其导航属性中引用另一实体。</span><span class="sxs-lookup"><span data-stu-id="1e72c-230">When an Instructor entity has a related OfficeAssignment entity, each entity will have a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="1e72c-231">可向 Instructor 导航属性添加 `[Required]` 特性，指定必须有相关讲师，但这不是必须的，因为 `InstructorID` 外键（也是此表的键）不可为 null。</span><span class="sxs-lookup"><span data-stu-id="1e72c-231">You could put a `[Required]` attribute on the Instructor navigation property to specify that there must be a related instructor, but you don't have to do that because the `InstructorID` foreign key (which is also the key to this table) is non-nullable.</span></span>

## <a name="modify-course-entity"></a><span data-ttu-id="1e72c-232">修改 Course 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-232">Modify Course entity</span></span>

![Course 实体](complex-data-model/_static/course-entity.png)

<span data-ttu-id="1e72c-234">在 Models/Course.cs 中，将之前添加的代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="1e72c-234">In *Models/Course.cs*, replace the code you added earlier with the following code.</span></span> <span data-ttu-id="1e72c-235">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="1e72c-235">The changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="1e72c-236">Course 实体具有外键属性 `DepartmentID`，该属性指向相关 Department 实体，同时它还具有 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-236">The course entity has a foreign key property `DepartmentID` which points to the related Department entity and it has a `Department` navigation property.</span></span>

<span data-ttu-id="1e72c-237">如果拥有相关实体的导航属性，则 Entity Framework 不会要求为数据模型添加外键属性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-237">The Entity Framework doesn't require you to add a foreign key property to your data model when you have a navigation property for a related entity.</span></span>  <span data-ttu-id="1e72c-238">只要有需要，EF 就会自动在数据库中创建外键，并为其创建[阴影属性](/ef/core/modeling/shadow-properties)。</span><span class="sxs-lookup"><span data-stu-id="1e72c-238">EF automatically creates foreign keys in the database wherever they're needed and creates [shadow properties](/ef/core/modeling/shadow-properties) for them.</span></span> <span data-ttu-id="1e72c-239">但如果数据模型包含外键，则更新会变得更简单、更高效。</span><span class="sxs-lookup"><span data-stu-id="1e72c-239">But having the foreign key in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="1e72c-240">例如，提取 Course 实体进行编辑时，如果未加载该实体，那么 Department 实体为 null，因此，更新 Course 实体时，必须先提取 Department 实体。</span><span class="sxs-lookup"><span data-stu-id="1e72c-240">For example, when you fetch a course entity to edit, the  Department entity is null if you don't load it, so when you update the course entity, you would have to first fetch the Department entity.</span></span> <span data-ttu-id="1e72c-241">数据模型中包含外键属性 `DepartmentID` 时，更新前无需提取 Department 实体。</span><span class="sxs-lookup"><span data-stu-id="1e72c-241">When the foreign key property `DepartmentID` is included in the data model, you don't need to fetch the Department entity before you update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="1e72c-242">DatabaseGenerated 特性</span><span class="sxs-lookup"><span data-stu-id="1e72c-242">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="1e72c-243">`CourseID` 属性上具有 `None` 参数的 `DatabaseGenerated` 特性指定主键值由用户提供，而不是由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="1e72c-243">The `DatabaseGenerated` attribute with the `None` parameter on the `CourseID` property specifies that primary key values are provided by the user rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="1e72c-244">默认情况下，Entity Framework 假定主键值由数据库生成。</span><span class="sxs-lookup"><span data-stu-id="1e72c-244">By default, Entity Framework assumes that primary key values are generated by the database.</span></span> <span data-ttu-id="1e72c-245">大多数情况下，这是理想情况。</span><span class="sxs-lookup"><span data-stu-id="1e72c-245">That's what you want in most scenarios.</span></span> <span data-ttu-id="1e72c-246">但对 Course 实体而言，需使用用户指定的课程编号，例如一个系为 1000 系列，另一个系为 2000 系列等。</span><span class="sxs-lookup"><span data-stu-id="1e72c-246">However, for Course entities, you'll use a user-specified course number such as a 1000 series for one department, a 2000 series for another department, and so on.</span></span>

<span data-ttu-id="1e72c-247">`DatabaseGenerated` 特性也可用于生成默认值，如在用于记录行创建或更新日期的数据库列中。</span><span class="sxs-lookup"><span data-stu-id="1e72c-247">The `DatabaseGenerated` attribute can also be used to generate default values, as in the case of database columns used to record the date a row was created or updated.</span></span>  <span data-ttu-id="1e72c-248">有关详细信息，请参阅[生成的属性](/ef/core/modeling/generated-properties)。</span><span class="sxs-lookup"><span data-stu-id="1e72c-248">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="1e72c-249">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="1e72c-249">Foreign key and navigation properties</span></span>

<span data-ttu-id="1e72c-250">Course 实体中的外键属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="1e72c-250">The foreign key properties and navigation properties in the Course entity reflect the following relationships:</span></span>

<span data-ttu-id="1e72c-251">向一个系分配课程后，出于上述原因，会出现 `DepartmentID` 外键和 `Department` 导航属性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-251">A course is assigned to one department, so there's a `DepartmentID` foreign key and a `Department` navigation property for the reasons mentioned above.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="1e72c-252">参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：</span><span class="sxs-lookup"><span data-stu-id="1e72c-252">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="1e72c-253">一门课程可能有多位授课讲师，因此 `CourseAssignments` 导航属性是一个集合（[稍后](#many-to-many-relationships)会解释 `CourseAssignment` 类型）：</span><span class="sxs-lookup"><span data-stu-id="1e72c-253">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection (the type `CourseAssignment` is explained [later](#many-to-many-relationships)):</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a><span data-ttu-id="1e72c-254">创建 Department 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-254">Create Department entity</span></span>

![Department 实体](complex-data-model/_static/department-entity.png)

<span data-ttu-id="1e72c-256">用以下代码创建 Models/Department.cs：</span><span class="sxs-lookup"><span data-stu-id="1e72c-256">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="1e72c-257">Column 特性</span><span class="sxs-lookup"><span data-stu-id="1e72c-257">The Column attribute</span></span>

<span data-ttu-id="1e72c-258">`Column` 特性之前用于更改列名称映射。</span><span class="sxs-lookup"><span data-stu-id="1e72c-258">Earlier you used the `Column` attribute to change column name mapping.</span></span> <span data-ttu-id="1e72c-259">在 Department 实体的代码中，`Column` 特性用于更改 SQL 数据类型映射，以便使用数据库中的 SQL Server 货币类型来定义该列：</span><span class="sxs-lookup"><span data-stu-id="1e72c-259">In the code for the Department entity, the `Column` attribute is being used to change SQL data type mapping so that the column will be defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="1e72c-260">通常不需要列映射，因为 Entity Framework 会根据你为属性定义的 CLR 类型选择适当的 SQL Server 数据类型。</span><span class="sxs-lookup"><span data-stu-id="1e72c-260">Column mapping is generally not required, because the Entity Framework chooses the appropriate SQL Server data type based on the CLR type that you define for the property.</span></span> <span data-ttu-id="1e72c-261">CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。</span><span class="sxs-lookup"><span data-stu-id="1e72c-261">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="1e72c-262">但假如你知道该列要表示货币金额，那么货币数据类型会更加合适。</span><span class="sxs-lookup"><span data-stu-id="1e72c-262">But in this case you know that the column will be holding currency amounts, and the money data type is more appropriate for that.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="1e72c-263">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="1e72c-263">Foreign key and navigation properties</span></span>

<span data-ttu-id="1e72c-264">外键和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="1e72c-264">The foreign key and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="1e72c-265">一个系可能有也可能没有管理员，而管理员始终是讲师。</span><span class="sxs-lookup"><span data-stu-id="1e72c-265">A department may or may not have an administrator, and an administrator is always an instructor.</span></span> <span data-ttu-id="1e72c-266">因此 `InstructorID` 属性作为 Instructor 实体内外键，且 `int` 类型指定后跟有一个问号，将该属性标记为可为 null。</span><span class="sxs-lookup"><span data-stu-id="1e72c-266">Therefore the `InstructorID` property is included as the foreign key to the Instructor entity, and a question mark is added after the `int` type designation to mark the property as nullable.</span></span> <span data-ttu-id="1e72c-267">导航属性名为 `Administrator`，但其中包含 Instructor 实体：</span><span class="sxs-lookup"><span data-stu-id="1e72c-267">The navigation property is named `Administrator` but holds an Instructor entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="1e72c-268">一个系可以有多门课程，因此存在 Course 导航属性：</span><span class="sxs-lookup"><span data-stu-id="1e72c-268">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> <span data-ttu-id="1e72c-269">按照约定，Entity Framework 能针对不可为 null 的外键和多对多关系启用级联删除。</span><span class="sxs-lookup"><span data-stu-id="1e72c-269">By convention, the Entity Framework enables cascade delete for non-nullable foreign keys and for many-to-many relationships.</span></span> <span data-ttu-id="1e72c-270">这可能导致循环级联删除规则，尝试添加迁移时该规则会造成异常。</span><span class="sxs-lookup"><span data-stu-id="1e72c-270">This can result in circular cascade delete rules, which will cause an exception when you try to add a migration.</span></span> <span data-ttu-id="1e72c-271">例如，如果未将 Department.InstructorID 属性定义为可为 null，那么在删除讲师时，EF 会配置级联删除规则来删除系，这是并不希望发生的情况。</span><span class="sxs-lookup"><span data-stu-id="1e72c-271">For example, if you didn't define the Department.InstructorID property as nullable, EF would configure a cascade delete rule to delete the department when you delete the instructor, which isn't what you want to have happen.</span></span> <span data-ttu-id="1e72c-272">如果业务规则要求 `InstructorID` 属性不可为 null，则必须使用以下 Fluent API 语句禁用关系中的级联删除：</span><span class="sxs-lookup"><span data-stu-id="1e72c-272">If your business rules required the `InstructorID` property to be non-nullable, you would have to use the following fluent API statement to disable cascade delete on the relationship:</span></span>
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a><span data-ttu-id="1e72c-273">修改 Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-273">Modify Enrollment entity</span></span>

![Enrollment 实体](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="1e72c-275">在 Models/Enrollment.cs 中，将之前添加的代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="1e72c-275">In *Models/Enrollment.cs*, replace the code you added earlier with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="1e72c-276">外键和导航属性</span><span class="sxs-lookup"><span data-stu-id="1e72c-276">Foreign key and navigation properties</span></span>

<span data-ttu-id="1e72c-277">外键属性和导航属性可反映以下关系：</span><span class="sxs-lookup"><span data-stu-id="1e72c-277">The foreign key properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="1e72c-278">注册记录面向一门课程，因此存在 `CourseID` 外键属性和 `Course` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="1e72c-278">An enrollment record is for a single course, so there's a `CourseID` foreign key property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="1e72c-279">注册记录面向一名学生，因此存在 `StudentID` 外键属性和 `Student` 导航属性：</span><span class="sxs-lookup"><span data-stu-id="1e72c-279">An enrollment record is for a single student, so there's a `StudentID` foreign key property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="1e72c-280">多对多关系</span><span class="sxs-lookup"><span data-stu-id="1e72c-280">Many-to-Many relationships</span></span>

<span data-ttu-id="1e72c-281">Student 和 Course 实体间存在多对多关系，Enrollment 实体在数据库中充当带有效负载的多对多联接表。</span><span class="sxs-lookup"><span data-stu-id="1e72c-281">There's a many-to-many relationship between the Student and Course entities, and the Enrollment entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="1e72c-282">“带有效负载”是指 Enrollment 表包含除联接表外键之外的其他数据（在此示例中为主键和 Grade 属性）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-282">"With payload" means that the Enrollment table contains additional data besides foreign keys for the joined tables (in this case, a primary key and a Grade property).</span></span>

<span data-ttu-id="1e72c-283">下图显示这些关系在实体关系图中的外观。</span><span class="sxs-lookup"><span data-stu-id="1e72c-283">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="1e72c-284">（该图是使用 EF 6.x 的 Entity Framework Power Tools 生成的，教程未介绍如何创建该图，此处仅作为示例使用。）</span><span class="sxs-lookup"><span data-stu-id="1e72c-284">(This diagram was generated using the Entity Framework Power Tools for EF 6.x; creating the diagram isn't part of the tutorial, it's just being used here as an illustration.)</span></span>

![学生-课程之间的多对多关系](complex-data-model/_static/student-course.png)

<span data-ttu-id="1e72c-286">每条关系线的一端显示 1，另一端显示星号 (\*)，这表示一对多关系。</span><span class="sxs-lookup"><span data-stu-id="1e72c-286">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="1e72c-287">如果 Enrollment 表不包含年级信息，则它只需包含两个外键（CourseID 和 StudentID）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-287">If the Enrollment table didn't include grade information, it would only need to contain the two foreign keys CourseID and StudentID.</span></span> <span data-ttu-id="1e72c-288">在这种情况下，该表是数据库中不带有效负载的多对多联接表（或纯联接表）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-288">In that case, it would be a many-to-many join table without payload (or a pure join table) in the database.</span></span> <span data-ttu-id="1e72c-289">Instructor 和 Course 实体具有这种多对多关系，下一步是创建实体类，将其作为不带有效负载的联接表。</span><span class="sxs-lookup"><span data-stu-id="1e72c-289">The Instructor and Course entities have that kind of many-to-many relationship, and your next step is to create an entity class to function as a join table without payload.</span></span>

<span data-ttu-id="1e72c-290">（EF 6.x 支持多对多关系的隐式联接表，但 EF Core 不支持。</span><span class="sxs-lookup"><span data-stu-id="1e72c-290">(EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="1e72c-291">有关详细信息，请参阅 [EF Core GitHub 存储库中的讨论](https://github.com/aspnet/EntityFramework/issues/1368)。）</span><span class="sxs-lookup"><span data-stu-id="1e72c-291">For more information, see the [discussion in the EF Core GitHub repository](https://github.com/aspnet/EntityFramework/issues/1368).)</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="1e72c-292">CourseAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-292">The CourseAssignment entity</span></span>

![CourseAssignment 实体](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="1e72c-294">用以下代码创建 Models/CourseAssignment.cs：</span><span class="sxs-lookup"><span data-stu-id="1e72c-294">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a><span data-ttu-id="1e72c-295">联接实体名称</span><span class="sxs-lookup"><span data-stu-id="1e72c-295">Join entity names</span></span>

<span data-ttu-id="1e72c-296">数据库中的 Instructor 与 Course 多对多关系需要联接表，并且必须由实体集表示。</span><span class="sxs-lookup"><span data-stu-id="1e72c-296">A join table is required in the database for the Instructor-to-Courses many-to-many relationship, and it has to be represented by an entity set.</span></span> <span data-ttu-id="1e72c-297">将联接实体命名为 `EntityName1EntityName2` 很常见，在此示例中实体名为 `CourseInstructor`。</span><span class="sxs-lookup"><span data-stu-id="1e72c-297">It's common to name a join entity `EntityName1EntityName2`, which in this case would be `CourseInstructor`.</span></span> <span data-ttu-id="1e72c-298">但是，建议选择一个可描述关系的名称。</span><span class="sxs-lookup"><span data-stu-id="1e72c-298">However, we recommend that you choose a name that describes the relationship.</span></span> <span data-ttu-id="1e72c-299">数据模型开始很简单，且会不断增长，随后无有效负载联接会频繁获取有效负载。</span><span class="sxs-lookup"><span data-stu-id="1e72c-299">Data models start out simple and grow, with no-payload joins frequently getting payloads later.</span></span> <span data-ttu-id="1e72c-300">如果一开始实体名称为描述性名称，那么之后就不必更改名称。</span><span class="sxs-lookup"><span data-stu-id="1e72c-300">If you start with a descriptive entity name, you won't have to change the name later.</span></span> <span data-ttu-id="1e72c-301">理想情况下，联接实体在业务域中可能具有自己的自带名称（可能是单个字）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-301">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="1e72c-302">例如，可通过 Rating 链接 Book 和 Customer。</span><span class="sxs-lookup"><span data-stu-id="1e72c-302">For example, Books and Customers could be linked through Ratings.</span></span> <span data-ttu-id="1e72c-303">对于此关系，相比 `CourseInstructor`，`CourseAssignment` 是更好的选择。</span><span class="sxs-lookup"><span data-stu-id="1e72c-303">For this relationship, `CourseAssignment` is a better choice than `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="1e72c-304">组合键</span><span class="sxs-lookup"><span data-stu-id="1e72c-304">Composite key</span></span>

<span data-ttu-id="1e72c-305">由于外键不可为 null，且它们共同唯一标识表的每一行，因此不需要单独的主键。</span><span class="sxs-lookup"><span data-stu-id="1e72c-305">Since the foreign keys are not nullable and together uniquely identify each row of the table, there's no need for a separate primary key.</span></span> <span data-ttu-id="1e72c-306">InstructorID 和 CourseID 属性应充当组合主键 。</span><span class="sxs-lookup"><span data-stu-id="1e72c-306">The *InstructorID* and *CourseID* properties should function as a composite primary key.</span></span> <span data-ttu-id="1e72c-307">标识 EF 组合主键的唯一方法是使用 Fluent API（无法借助特性来完成）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-307">The only way to identify composite primary keys to EF is by using the *fluent API* (it can't be done by using attributes).</span></span> <span data-ttu-id="1e72c-308">下一节将介绍如何配置组合主键。</span><span class="sxs-lookup"><span data-stu-id="1e72c-308">You'll see how to configure the composite primary key in the next section.</span></span>

<span data-ttu-id="1e72c-309">在一个课程可以有多个行，一个讲师可以有多个行的情况下，组合键可确保同一讲师和课程不会有多个行。</span><span class="sxs-lookup"><span data-stu-id="1e72c-309">The composite key ensures that while you can have multiple rows for one course, and multiple rows for one instructor, you can't have multiple rows for the same instructor and course.</span></span> <span data-ttu-id="1e72c-310">`Enrollment` 联接实体定义其自己的主键，因此可能会出现此类重复。</span><span class="sxs-lookup"><span data-stu-id="1e72c-310">The `Enrollment` join entity defines its own primary key, so duplicates of this sort are possible.</span></span> <span data-ttu-id="1e72c-311">若要防止出现此类重复，可在外键字段上添加唯一索引，或使用类似于 `CourseAssignment` 的主组合键配置 `Enrollment`。</span><span class="sxs-lookup"><span data-stu-id="1e72c-311">To prevent such duplicates, you could add a unique index on the foreign key fields, or configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="1e72c-312">有关详细信息，请参阅[索引](/ef/core/modeling/indexes)。</span><span class="sxs-lookup"><span data-stu-id="1e72c-312">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="1e72c-313">更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="1e72c-313">Update the database context</span></span>

<span data-ttu-id="1e72c-314">将以下突出显示的代码添加到 Data/SchoolContext.cs 文件：</span><span class="sxs-lookup"><span data-stu-id="1e72c-314">Add the following highlighted code to the *Data/SchoolContext.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="1e72c-315">此代码添加新实体并配置 CourseAssignment 实体的组合主键。</span><span class="sxs-lookup"><span data-stu-id="1e72c-315">This code adds the new entities and configures the CourseAssignment entity's composite primary key.</span></span>

## <a name="about-a-fluent-api-alternative"></a><span data-ttu-id="1e72c-316">Fluent API 替代方案</span><span class="sxs-lookup"><span data-stu-id="1e72c-316">About a fluent API alternative</span></span>

<span data-ttu-id="1e72c-317">`DbContext` 类的 `OnModelCreating` 方法中的代码使用 Fluent API 来配置 EF 行为。</span><span class="sxs-lookup"><span data-stu-id="1e72c-317">The code in the `OnModelCreating` method of the `DbContext` class uses the *fluent API* to configure EF behavior.</span></span> <span data-ttu-id="1e72c-318">API 称为“Fluent”，因为它通常在将一系列方法调用连接成单个语句后才能使用，如 [EF Core 文档](/ef/core/modeling/#use-fluent-api-to-configure-a-model) 中的此示例所示：</span><span class="sxs-lookup"><span data-stu-id="1e72c-318">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement, as in this example from the [EF Core documentation](/ef/core/modeling/#use-fluent-api-to-configure-a-model):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="1e72c-319">本教程仅将 Fluent API 用于数据库映射，这是无法使用特性实现的。</span><span class="sxs-lookup"><span data-stu-id="1e72c-319">In this tutorial, you're using the fluent API only for database mapping that you can't do with attributes.</span></span> <span data-ttu-id="1e72c-320">但 Fluent API 还可用于指定大多数格式化、验证和映射规则，这可通过特性完成。</span><span class="sxs-lookup"><span data-stu-id="1e72c-320">However, you can also use the fluent API to specify most of the formatting, validation, and mapping rules that you can do by using attributes.</span></span> <span data-ttu-id="1e72c-321">`MinimumLength` 等特性不能通过 Fluent API 应用。</span><span class="sxs-lookup"><span data-stu-id="1e72c-321">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="1e72c-322">如前所述，`MinimumLength` 不会更改架构，它只应用客户端和服务器端验证规则。</span><span class="sxs-lookup"><span data-stu-id="1e72c-322">As mentioned previously, `MinimumLength` doesn't change the schema, it only applies a client and server side validation rule.</span></span>

<span data-ttu-id="1e72c-323">某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。</span><span class="sxs-lookup"><span data-stu-id="1e72c-323">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="1e72c-324">如有需要，可混合使用特性和 Fluent API，且有些自定义只能通过 Fluent API 实现，但通常建议选择一种方法并尽可能坚持使用这一种。</span><span class="sxs-lookup"><span data-stu-id="1e72c-324">You can mix attributes and fluent API if you want, and there are a few customizations that can only be done by using fluent API, but in general the recommended practice is to choose one of these two approaches and use that consistently as much as possible.</span></span> <span data-ttu-id="1e72c-325">如果确实要使用两种，请注意，只要出现冲突，Fluent API 就会覆盖特性。</span><span class="sxs-lookup"><span data-stu-id="1e72c-325">If you do use both, note that wherever there's a conflict, Fluent API overrides attributes.</span></span>

<span data-ttu-id="1e72c-326">有关特性和 Fluent API 的详细信息，请参阅[配置方法](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="1e72c-326">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="1e72c-327">显示关系的实体关系图</span><span class="sxs-lookup"><span data-stu-id="1e72c-327">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="1e72c-328">下图显示 Entity Framework Power Tools 针对已完成的学校模型创建的关系图。</span><span class="sxs-lookup"><span data-stu-id="1e72c-328">The following illustration shows the diagram that the Entity Framework Power Tools create for the completed School model.</span></span>

![实体关系图](complex-data-model/_static/diagram.png)

<span data-ttu-id="1e72c-330">除一对多关系线（1到 \*）外，此处还显示了 Instructor 和 OfficeAssignment 实体间的一对零或一关系线（1 到 0..1），以及 Instructor 和 Department 实体间的零对多或一对多关系线（0..1 到 \*）。</span><span class="sxs-lookup"><span data-stu-id="1e72c-330">Besides the one-to-many relationship lines (1 to \*), you can see here the one-to-zero-or-one relationship line (1 to 0..1) between the Instructor and OfficeAssignment entities and the zero-or-one-to-many relationship line (0..1 to \*) between the Instructor and Department entities.</span></span>

## <a name="seed-database-with-test-data"></a><span data-ttu-id="1e72c-331">使用测试数据设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="1e72c-331">Seed database with test data</span></span>

<span data-ttu-id="1e72c-332">使用以下代码替换 Data/DbInitializer.cs 文件中的代码，从而为创建的新实体提供种子数据。</span><span class="sxs-lookup"><span data-stu-id="1e72c-332">Replace the code in the *Data/DbInitializer.cs* file with the following code in order to provide seed data for the new entities you've created.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="1e72c-333">如第一个教程所述，大部分此类代码仅创建新实体对象，并按测试要求将示例数据加载到属性中。</span><span class="sxs-lookup"><span data-stu-id="1e72c-333">As you saw in the first tutorial, most of this code simply creates new entity objects and loads sample data into properties as required for testing.</span></span> <span data-ttu-id="1e72c-334">注意多对多关系的处理方法：代码在 `Enrollments` 和 `CourseAssignment` 联接实体集中创建实体，以此来创建关系。</span><span class="sxs-lookup"><span data-stu-id="1e72c-334">Notice how the many-to-many relationships are handled: the code creates relationships by creating entities in the `Enrollments` and `CourseAssignment` join entity sets.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="1e72c-335">添加迁移</span><span class="sxs-lookup"><span data-stu-id="1e72c-335">Add a migration</span></span>

<span data-ttu-id="1e72c-336">保存更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="1e72c-336">Save your changes and build the project.</span></span> <span data-ttu-id="1e72c-337">然后打开项目文件夹中的命令窗口，输入 `migrations add` 命令（先不要执行 update-database 命令）：</span><span class="sxs-lookup"><span data-stu-id="1e72c-337">Then open the command window in the project folder and enter the `migrations add` command (don't do the update-database command yet):</span></span>

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

<span data-ttu-id="1e72c-338">会出现数据可能丢失的警告。</span><span class="sxs-lookup"><span data-stu-id="1e72c-338">You get a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="1e72c-339">如果此时尝试运行 `database update` 命令（先不要执行此操作），则会出现以下错误：</span><span class="sxs-lookup"><span data-stu-id="1e72c-339">If you tried to run the `database update` command at this point (don't do it yet), you would get the following error:</span></span>

> <span data-ttu-id="1e72c-340">ALTER TABLE 语句与 FOREIGN KEY 约束“FK_dbo.Course_dbo.Department_DepartmentID”冲突。</span><span class="sxs-lookup"><span data-stu-id="1e72c-340">The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID".</span></span> <span data-ttu-id="1e72c-341">冲突发生位置：数据库“ContosoUniversity”、表“dbo.Department”和列“DepartmentID”。</span><span class="sxs-lookup"><span data-stu-id="1e72c-341">The conflict occurred in database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.</span></span>

<span data-ttu-id="1e72c-342">有时使用现有数据执行迁移时，需将存根数据插入数据库，满足外键约束。</span><span class="sxs-lookup"><span data-stu-id="1e72c-342">Sometimes when you execute migrations with existing data, you need to insert stub data into the database to satisfy foreign key constraints.</span></span> <span data-ttu-id="1e72c-343">`Up` 方法中生成的代码将不可为 null 的 DepartmentID 外键添加到 Course 表中。</span><span class="sxs-lookup"><span data-stu-id="1e72c-343">The generated code in the `Up` method adds a non-nullable DepartmentID foreign key to the Course table.</span></span> <span data-ttu-id="1e72c-344">如果代码运行时，Course 表中已经有了行，则 `AddColumn` 操作失败，因为 SQL Server 不知道要向不可为 null 的列中放入什么值。</span><span class="sxs-lookup"><span data-stu-id="1e72c-344">If there are already rows in the Course table when the code runs, the `AddColumn` operation fails because SQL Server doesn't know what value to put in the column that can't be null.</span></span> <span data-ttu-id="1e72c-345">本教程将在新数据库上运行迁移，但在生产应用程序中，必须使迁移处理现有数据，因此下方通过示例介绍如何执行此操作。</span><span class="sxs-lookup"><span data-stu-id="1e72c-345">For this tutorial you'll run the migration on a new database, but in a production application you'd have to make the migration handle existing data, so the following directions show an example of how to do that.</span></span>

<span data-ttu-id="1e72c-346">为使此迁移处理现有数据，必须更改代码，赋予新列默认值，并创建一个名为“Temp”的存根系，作为默认系。</span><span class="sxs-lookup"><span data-stu-id="1e72c-346">To make this migration work with existing data you have to change the code to give the new column a default value, and create a stub department named "Temp" to act as the default department.</span></span> <span data-ttu-id="1e72c-347">之后，Course 行将在 `Up` 方法运行后与“Temp”系建立联系。</span><span class="sxs-lookup"><span data-stu-id="1e72c-347">As a result, existing Course rows will all be related to the "Temp" department after the `Up` method runs.</span></span>

* <span data-ttu-id="1e72c-348">打开 {timestamp}_ComplexDataModel.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="1e72c-348">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>

* <span data-ttu-id="1e72c-349">对将 DepartmentID 列添加到 Course 表的代码行添加注释。</span><span class="sxs-lookup"><span data-stu-id="1e72c-349">Comment out the line of code that adds the DepartmentID column to the Course table.</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* <span data-ttu-id="1e72c-350">在创建 Department 表的代码后添加以下突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="1e72c-350">Add the following highlighted code after the code that creates the Department table:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="1e72c-351">在生产应用程序中，可编写代码或脚本来添加 Department 行并将 Course 行与新 Department 行相关联。</span><span class="sxs-lookup"><span data-stu-id="1e72c-351">In a production application, you would write code or scripts to add Department rows and relate Course rows to the new Department rows.</span></span> <span data-ttu-id="1e72c-352">随后将不在需要“Temp”系或 Course.DepartmentID 列中的默认值。</span><span class="sxs-lookup"><span data-stu-id="1e72c-352">You would then no longer need the "Temp" department or the default value on the Course.DepartmentID column.</span></span>

<span data-ttu-id="1e72c-353">保存更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="1e72c-353">Save your changes and build the project.</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="1e72c-354">更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="1e72c-354">Change the connection string</span></span>

<span data-ttu-id="1e72c-355">现在 `DbInitializer` 类中就有了新代码，可将新实体的种子数据添加到空数据库。</span><span class="sxs-lookup"><span data-stu-id="1e72c-355">You now have new code in the `DbInitializer` class that adds seed data for the new entities to an empty database.</span></span> <span data-ttu-id="1e72c-356">若要让 EF 创建新的空数据库，请将 appsettings.json 中连接字符串内的数据库名称更改为 ContosoUniversity3 或正在使用的计算机上未使用过的其他名称。</span><span class="sxs-lookup"><span data-stu-id="1e72c-356">To make EF create a new empty database, change the name of the database in the connection string in *appsettings.json* to ContosoUniversity3 or some other name that you haven't used on the computer you're using.</span></span>

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

<span data-ttu-id="1e72c-357">保存对 appsettings.json 的更改。</span><span class="sxs-lookup"><span data-stu-id="1e72c-357">Save your change to *appsettings.json*.</span></span>

> [!NOTE]
> <span data-ttu-id="1e72c-358">除更改数据库名称外，删除数据库同样可行。</span><span class="sxs-lookup"><span data-stu-id="1e72c-358">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="1e72c-359">使用 SQL Server 对象资源管理器 (SSOX) 或 `database drop` CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="1e72c-359">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a><span data-ttu-id="1e72c-360">更新数据库</span><span class="sxs-lookup"><span data-stu-id="1e72c-360">Update the database</span></span>

<span data-ttu-id="1e72c-361">更改数据库名称或删除数据库后，在命令窗口运行 `database update` 命令，执行迁移。</span><span class="sxs-lookup"><span data-stu-id="1e72c-361">After you have changed the database name or deleted the database, run the `database update` command in the command window to execute the migrations.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="1e72c-362">运行应用，使 `DbInitializer.Initialize` 方法运行并填充新数据库。</span><span class="sxs-lookup"><span data-stu-id="1e72c-362">Run the app to cause the `DbInitializer.Initialize` method to run and populate the new database.</span></span>

<span data-ttu-id="1e72c-363">像之前一样在 SSOX 中打开数据库，然后展开 Tables 节点，查看是否已创建所有表。</span><span class="sxs-lookup"><span data-stu-id="1e72c-363">Open the database in SSOX as you did earlier, and expand the **Tables** node to see that all of the tables have been created.</span></span> <span data-ttu-id="1e72c-364">（如果之前打开的 SSOX 尚未关闭，请单击“刷新”按钮。）</span><span class="sxs-lookup"><span data-stu-id="1e72c-364">(If you still have SSOX open from the earlier time, click the **Refresh** button.)</span></span>

![SSOX 中的表](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="1e72c-366">运行应用，触发设定数据库种子的初始化代码。</span><span class="sxs-lookup"><span data-stu-id="1e72c-366">Run the app to trigger the initializer code that seeds the database.</span></span>

<span data-ttu-id="1e72c-367">右键单击“CourseAssignment”表，然后选择“查看数据”，验证其中是否存在数据 。</span><span class="sxs-lookup"><span data-stu-id="1e72c-367">Right-click the **CourseAssignment** table and select **View Data** to verify that it has data in it.</span></span>

![SSOX 中的 CourseAssignment 数据](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a><span data-ttu-id="1e72c-369">获取代码</span><span class="sxs-lookup"><span data-stu-id="1e72c-369">Get the code</span></span>

[<span data-ttu-id="1e72c-370">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="1e72c-370">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="1e72c-371">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1e72c-371">Next steps</span></span>

<span data-ttu-id="1e72c-372">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="1e72c-372">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1e72c-373">已自定义数据模型</span><span class="sxs-lookup"><span data-stu-id="1e72c-373">Customized the Data model</span></span>
> * <span data-ttu-id="1e72c-374">已更改 Student 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-374">Made changes to Student entity</span></span>
> * <span data-ttu-id="1e72c-375">已创建 Instructor 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-375">Created Instructor entity</span></span>
> * <span data-ttu-id="1e72c-376">已创建 OfficeAssignment 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-376">Created OfficeAssignment entity</span></span>
> * <span data-ttu-id="1e72c-377">已修改 Course 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-377">Modified Course entity</span></span>
> * <span data-ttu-id="1e72c-378">已创建 Department 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-378">Created Department entity</span></span>
> * <span data-ttu-id="1e72c-379">已修改 Enrollment 实体</span><span class="sxs-lookup"><span data-stu-id="1e72c-379">Modified Enrollment entity</span></span>
> * <span data-ttu-id="1e72c-380">已更新数据库上下文</span><span class="sxs-lookup"><span data-stu-id="1e72c-380">Updated the database context</span></span>
> * <span data-ttu-id="1e72c-381">已使用测试数据设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="1e72c-381">Seeded database with test data</span></span>
> * <span data-ttu-id="1e72c-382">已添加迁移</span><span class="sxs-lookup"><span data-stu-id="1e72c-382">Added a migration</span></span>
> * <span data-ttu-id="1e72c-383">已更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="1e72c-383">Changed the connection string</span></span>
> * <span data-ttu-id="1e72c-384">已更新数据库</span><span class="sxs-lookup"><span data-stu-id="1e72c-384">Updated the database</span></span>

<span data-ttu-id="1e72c-385">请继续阅读下一篇教程，详细了解如何访问相关数据。</span><span class="sxs-lookup"><span data-stu-id="1e72c-385">Advance to the next tutorial to learn more about how to access related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="1e72c-386">下一篇：访问相关数据</span><span class="sxs-lookup"><span data-stu-id="1e72c-386">Next: Access related data</span></span>](read-related-data.md)
