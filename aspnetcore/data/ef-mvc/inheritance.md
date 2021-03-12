---
title: 教程：实现继承 - ASP.NET MVC 和 EF Core
description: 本教程将演示如何使用 ASP.NET Core 应用程序中的 Entity Framework Core 在数据模型中实现继承。
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 299e00b223d287c4e2ece3d1e250581e2a7565e5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586366"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="7af68-103">教程：实现继承 - ASP.NET MVC 和 EF Core</span><span class="sxs-lookup"><span data-stu-id="7af68-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="7af68-104">在上一个教程中，已经处理了并发异常。</span><span class="sxs-lookup"><span data-stu-id="7af68-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="7af68-105">本教程将演示如何在数据模型中实现继承。</span><span class="sxs-lookup"><span data-stu-id="7af68-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="7af68-106">在面向对象的编程中，可以使用继承以便于重用代码。</span><span class="sxs-lookup"><span data-stu-id="7af68-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="7af68-107">在本教程中，将更改 `Instructor` 和 `Student` 类，以便从 `Person` 基类中派生，该基类包含教师和学生所共有的属性（如 `LastName`）。</span><span class="sxs-lookup"><span data-stu-id="7af68-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="7af68-108">不会添加或更改任何网页，但会更改部分代码，并将在数据库中自动反映这些更改。</span><span class="sxs-lookup"><span data-stu-id="7af68-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="7af68-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="7af68-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7af68-110">会将继承映射到数据库</span><span class="sxs-lookup"><span data-stu-id="7af68-110">Map inheritance to database</span></span>
> * <span data-ttu-id="7af68-111">创建 Person 类</span><span class="sxs-lookup"><span data-stu-id="7af68-111">Create the Person class</span></span>
> * <span data-ttu-id="7af68-112">更新 Instructor 和 Student</span><span class="sxs-lookup"><span data-stu-id="7af68-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="7af68-113">向模型添加 Person</span><span class="sxs-lookup"><span data-stu-id="7af68-113">Add Person to the model</span></span>
> * <span data-ttu-id="7af68-114">创建和更新迁移</span><span class="sxs-lookup"><span data-stu-id="7af68-114">Create and update migrations</span></span>
> * <span data-ttu-id="7af68-115">测试实现</span><span class="sxs-lookup"><span data-stu-id="7af68-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7af68-116">先决条件</span><span class="sxs-lookup"><span data-stu-id="7af68-116">Prerequisites</span></span>

* [<span data-ttu-id="7af68-117">处理并发</span><span class="sxs-lookup"><span data-stu-id="7af68-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="7af68-118">会将继承映射到数据库</span><span class="sxs-lookup"><span data-stu-id="7af68-118">Map inheritance to database</span></span>

<span data-ttu-id="7af68-119">学校数据模型中的 `Instructor` 和 `Student` 类具有多个相同的属性：</span><span class="sxs-lookup"><span data-stu-id="7af68-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Student 和 Instructor 类](inheritance/_static/no-inheritance.png)

<span data-ttu-id="7af68-121">假设想要消除由 `Instructor` 和`Student` 实体共享的属性的冗余代码。</span><span class="sxs-lookup"><span data-stu-id="7af68-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="7af68-122">或者想要写入可以格式化姓名的服务，而无需关注该姓名来自教师还是学生。</span><span class="sxs-lookup"><span data-stu-id="7af68-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="7af68-123">可以创建只包含这些共享属性的 `Person` 基类，然后使 `Instructor` 和 `Student` 类继承该基类，如下图所示：</span><span class="sxs-lookup"><span data-stu-id="7af68-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![从 Person 类派生的 Student 和 Instructor 类](inheritance/_static/inheritance.png)

<span data-ttu-id="7af68-125">有多种方法可以在数据库中表示此继承结构。</span><span class="sxs-lookup"><span data-stu-id="7af68-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="7af68-126">可以创建一个 Person 表，将学生和教师的相关信息包含在一个表中。</span><span class="sxs-lookup"><span data-stu-id="7af68-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="7af68-127">某些列可能仅适用于教师 (HireDate)，某些列仅适用于学生 (EnrollmentDate)，某些列同时适用于两者（LastName、FirstName）。</span><span class="sxs-lookup"><span data-stu-id="7af68-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="7af68-128">通常情况下，将有一个鉴别器列来指示每行所代表的类型。</span><span class="sxs-lookup"><span data-stu-id="7af68-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="7af68-129">例如，鉴别器列可能包含“Instructor”来指示教师，包含“Student”来指示学生。</span><span class="sxs-lookup"><span data-stu-id="7af68-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![每个层次结构一张表示例](inheritance/_static/tph.png)

<span data-ttu-id="7af68-131">从单个数据库表生成实体继承结构的模式称为每个层次结构一张 (TPH) 继承。</span><span class="sxs-lookup"><span data-stu-id="7af68-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="7af68-132">另一种方法是使数据库看起来更像继承结构。</span><span class="sxs-lookup"><span data-stu-id="7af68-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="7af68-133">例如，可以仅将姓名字段包含到 Person 表中，在单独的 Instructor 和 Student 表中包含日期字段。</span><span class="sxs-lookup"><span data-stu-id="7af68-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

> [!WARNING]
> <span data-ttu-id="7af68-134">EF Core 3.x 不支持每个类型一个表 (TPT)，但 TPT 已在 [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan) 中实现。</span><span class="sxs-lookup"><span data-stu-id="7af68-134">Table Per Type (TPT) is not supported by EF Core 3.x, however it is has been implemented in [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan).</span></span>

![每种类型一个表继承](inheritance/_static/tpt.png)

<span data-ttu-id="7af68-136">为每个实体类创建数据库表的模式称为每个类型一张表 (TPT) 继承。</span><span class="sxs-lookup"><span data-stu-id="7af68-136">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="7af68-137">另一种方法是将所有非抽象类型映射到单独的表。</span><span class="sxs-lookup"><span data-stu-id="7af68-137">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="7af68-138">类的所有属性（包括继承的属性）映射到相应表的列。</span><span class="sxs-lookup"><span data-stu-id="7af68-138">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="7af68-139">此模式称为每个具体类一张表 (TPC) 继承。</span><span class="sxs-lookup"><span data-stu-id="7af68-139">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="7af68-140">如果为前面所示的 Person、Student 和 Instructor 类实现了 TPC 继承，那么在实现继承之后，Student 和 Instructor 表看起来将与以前没什么不同。</span><span class="sxs-lookup"><span data-stu-id="7af68-140">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="7af68-141">TPC 和 TPH 继承模式的性能通常比 TPT 继承模式好，因为 TPT 模式会导致复杂的联接查询。</span><span class="sxs-lookup"><span data-stu-id="7af68-141">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="7af68-142">本教程将演示如何实现 TPH 继承。</span><span class="sxs-lookup"><span data-stu-id="7af68-142">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="7af68-143">TPH 是 Entity Framework Core 唯一支持的继承模式。</span><span class="sxs-lookup"><span data-stu-id="7af68-143">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="7af68-144">需要执行的操作是创建 `Person` 类、将 `Instructor` 和 `Student` 类更改为从 `Person` 派生、将新的类添加到 `DbContext`，以及创建迁移。</span><span class="sxs-lookup"><span data-stu-id="7af68-144">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="7af68-145">在进行以下更改之前，请考虑保存项目的副本。</span><span class="sxs-lookup"><span data-stu-id="7af68-145">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="7af68-146">如果遇到问题并需要重新开始，可以更轻松地从已保存的项目开始，而不用反向操作本教程中的步骤或者返回到整个系列的开始。</span><span class="sxs-lookup"><span data-stu-id="7af68-146">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="7af68-147">创建 Person 类</span><span class="sxs-lookup"><span data-stu-id="7af68-147">Create the Person class</span></span>

<span data-ttu-id="7af68-148">在 Models 文件夹中，创建 Person.cs 并使用以下代码替换模板代码：</span><span class="sxs-lookup"><span data-stu-id="7af68-148">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="7af68-149">更新 Instructor 和 Student</span><span class="sxs-lookup"><span data-stu-id="7af68-149">Update Instructor and Student</span></span>

<span data-ttu-id="7af68-150">在 Instructor.cs 中，从 Person 类派生 Instructor 类并删除键和姓名字段。</span><span class="sxs-lookup"><span data-stu-id="7af68-150">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="7af68-151">代码将如下所示：</span><span class="sxs-lookup"><span data-stu-id="7af68-151">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="7af68-152">在 Student.cs 中做出相同更改。</span><span class="sxs-lookup"><span data-stu-id="7af68-152">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="7af68-153">向模型添加 Person</span><span class="sxs-lookup"><span data-stu-id="7af68-153">Add Person to the model</span></span>

<span data-ttu-id="7af68-154">将 Person 实体类型添加到 SchoolContext.cs。</span><span class="sxs-lookup"><span data-stu-id="7af68-154">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="7af68-155">新的行突出显示。</span><span class="sxs-lookup"><span data-stu-id="7af68-155">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="7af68-156">以上是 Entity Framework 配置每个层次结构一张表继承所需的全部操作。</span><span class="sxs-lookup"><span data-stu-id="7af68-156">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="7af68-157">正如将看到的，更新数据库时，将有一个 Person 表来代替 Student 和 Instructor 表。</span><span class="sxs-lookup"><span data-stu-id="7af68-157">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="7af68-158">创建和更新迁移</span><span class="sxs-lookup"><span data-stu-id="7af68-158">Create and update migrations</span></span>

<span data-ttu-id="7af68-159">保存更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="7af68-159">Save your changes and build the project.</span></span> <span data-ttu-id="7af68-160">随后在项目文件夹中打开命令窗口并输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="7af68-160">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="7af68-161">暂不运行 `database update` 命令。</span><span class="sxs-lookup"><span data-stu-id="7af68-161">Don't run the `database update` command yet.</span></span> <span data-ttu-id="7af68-162">该命令将导致数据丢失，因为它将删除 Instructor 表并将 Student 表重命名为 Person。</span><span class="sxs-lookup"><span data-stu-id="7af68-162">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="7af68-163">需要提供自定义代码来保留现有数据。</span><span class="sxs-lookup"><span data-stu-id="7af68-163">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="7af68-164">打开 Migrations/\<timestamp>_Inheritance.cs 并使用以下代码替换 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="7af68-164">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="7af68-165">此代码负责以下数据库更新任务：</span><span class="sxs-lookup"><span data-stu-id="7af68-165">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="7af68-166">删除指向 Student 表的外键约束和索引。</span><span class="sxs-lookup"><span data-stu-id="7af68-166">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="7af68-167">将 Instructor 表重命名为 Person，根据需要做出更改以存储学生数据：</span><span class="sxs-lookup"><span data-stu-id="7af68-167">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="7af68-168">为学生添加可为 NULL 的 EnrollmentDate。</span><span class="sxs-lookup"><span data-stu-id="7af68-168">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="7af68-169">添加鉴别器列来指示行代表学生还是教师。</span><span class="sxs-lookup"><span data-stu-id="7af68-169">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="7af68-170">HireDate 可为 NULL，因为学生行不会包含聘用日期。</span><span class="sxs-lookup"><span data-stu-id="7af68-170">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="7af68-171">添加临时字段，用于更新指向学生的外键。</span><span class="sxs-lookup"><span data-stu-id="7af68-171">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="7af68-172">将学生复制到 Person 表时，将获取新的主键值。</span><span class="sxs-lookup"><span data-stu-id="7af68-172">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="7af68-173">将数据从 Student 表复制到 Person 表。</span><span class="sxs-lookup"><span data-stu-id="7af68-173">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="7af68-174">这将使学生获取分配的新主键值。</span><span class="sxs-lookup"><span data-stu-id="7af68-174">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="7af68-175">修复指向学生的外键值。</span><span class="sxs-lookup"><span data-stu-id="7af68-175">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="7af68-176">重新创建外键约束和索引，现在将它们指向 Person 表。</span><span class="sxs-lookup"><span data-stu-id="7af68-176">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="7af68-177">（如果已使用 GUID 而不是整数作为主键类型，那么将不需要更改学生主键值，并且可能已省略其中多个步骤。）</span><span class="sxs-lookup"><span data-stu-id="7af68-177">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="7af68-178">运行 `database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="7af68-178">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="7af68-179">（在生产系统中，可以对 `Down` 方法进行相应更改，以防必须使用该方法返回到以前的数据库版本。</span><span class="sxs-lookup"><span data-stu-id="7af68-179">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="7af68-180">本教程中将不使用 `Down` 方法。）</span><span class="sxs-lookup"><span data-stu-id="7af68-180">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="7af68-181">在包含现有数据的数据库中更改架构时，可能会发生其他错误。</span><span class="sxs-lookup"><span data-stu-id="7af68-181">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="7af68-182">如果出现无法解决的迁移错误，可以在连接字符串中更改数据库名或者删除数据库。</span><span class="sxs-lookup"><span data-stu-id="7af68-182">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="7af68-183">若是新数据库，则没有要迁移的数据，因此在完成更新数据库命令时很可能不会出错。</span><span class="sxs-lookup"><span data-stu-id="7af68-183">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="7af68-184">若要删除数据库，请使用 SSOX 或运行 `database drop` CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="7af68-184">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="7af68-185">测试实现</span><span class="sxs-lookup"><span data-stu-id="7af68-185">Test the implementation</span></span>

<span data-ttu-id="7af68-186">运行应用并尝试各种页面。</span><span class="sxs-lookup"><span data-stu-id="7af68-186">Run the app and try various pages.</span></span> <span data-ttu-id="7af68-187">一切都和以前一样。</span><span class="sxs-lookup"><span data-stu-id="7af68-187">Everything works the same as it did before.</span></span>

<span data-ttu-id="7af68-188">在“SQL Server 对象资源管理器” 中，展开“数据连接/SchoolContext”和“表”，将看到 Student 和 Instructor 表已替换为 Person 表。</span><span class="sxs-lookup"><span data-stu-id="7af68-188">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="7af68-189">打开 Person 表设计器，将看到它包含在 Student 和 Instructor 表中使用的所有列。</span><span class="sxs-lookup"><span data-stu-id="7af68-189">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![SSOX 中的 Person 表](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="7af68-191">右键单击 Person 表，然后单击“显示表数据”以查看鉴别器列。</span><span class="sxs-lookup"><span data-stu-id="7af68-191">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![SSOX 中的 Person 表 - 表数据](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="7af68-193">获取代码</span><span class="sxs-lookup"><span data-stu-id="7af68-193">Get the code</span></span>

[<span data-ttu-id="7af68-194">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="7af68-194">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="7af68-195">其他资源</span><span class="sxs-lookup"><span data-stu-id="7af68-195">Additional resources</span></span>

<span data-ttu-id="7af68-196">若要详细了解 Entity Framework Core 中的继承，请参阅[继承](/ef/core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="7af68-196">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7af68-197">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7af68-197">Next steps</span></span>

<span data-ttu-id="7af68-198">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="7af68-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7af68-199">已将继承映射到数据库</span><span class="sxs-lookup"><span data-stu-id="7af68-199">Mapped inheritance to database</span></span>
> * <span data-ttu-id="7af68-200">已创建 Person 类</span><span class="sxs-lookup"><span data-stu-id="7af68-200">Created the Person class</span></span>
> * <span data-ttu-id="7af68-201">已更新 Instructor 和 Student</span><span class="sxs-lookup"><span data-stu-id="7af68-201">Updated Instructor and Student</span></span>
> * <span data-ttu-id="7af68-202">已向模型添加 Person</span><span class="sxs-lookup"><span data-stu-id="7af68-202">Added Person to the model</span></span>
> * <span data-ttu-id="7af68-203">已创建和更新迁移</span><span class="sxs-lookup"><span data-stu-id="7af68-203">Created and update migrations</span></span>
> * <span data-ttu-id="7af68-204">已测试实现</span><span class="sxs-lookup"><span data-stu-id="7af68-204">Tested the implementation</span></span>

<span data-ttu-id="7af68-205">请继续阅读下一篇教程，了解如何处理各种相对高级的 Entity Framework 方案。</span><span class="sxs-lookup"><span data-stu-id="7af68-205">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7af68-206">下一篇：高级主题</span><span class="sxs-lookup"><span data-stu-id="7af68-206">Next: Advanced topics</span></span>](advanced.md)