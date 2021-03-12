---
title: 在 ASP.NET Core 项目中添加、下载和删除用户数据 Identity
author: rick-anderson
description: 了解如何将自定义用户数据添加到 Identity ASP.NET Core 项目中的。 删除每个 GDPR 的数据。
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: 2d921a0c72fb7c03cd88966077e2d33e4b19ffa1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585911"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="34c54-104">在 ASP.NET Core 项目中添加、下载和删除自定义用户数据 Identity</span><span class="sxs-lookup"><span data-stu-id="34c54-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="34c54-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="34c54-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="34c54-106">本文介绍以下操作：</span><span class="sxs-lookup"><span data-stu-id="34c54-106">This article shows how to:</span></span>

* <span data-ttu-id="34c54-107">向 ASP.NET Core web 应用添加自定义用户数据。</span><span class="sxs-lookup"><span data-stu-id="34c54-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="34c54-108">使用属性标记自定义用户数据模型 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> ，使其自动可供下载和删除。</span><span class="sxs-lookup"><span data-stu-id="34c54-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="34c54-109">使数据能够下载和删除有助于满足 [GDPR](xref:security/gdpr) 要求。</span><span class="sxs-lookup"><span data-stu-id="34c54-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="34c54-110">项目示例是从 Razor 页面 web 应用创建的，但 ASP.NET CORE MVC web 应用的说明类似。</span><span class="sxs-lookup"><span data-stu-id="34c54-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="34c54-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/add-user-data)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="34c54-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="34c54-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="34c54-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="34c54-113">创建 Razor web 应用</span><span class="sxs-lookup"><span data-stu-id="34c54-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="34c54-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="34c54-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="34c54-115">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="34c54-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="34c54-116">将项目命名为 " **WebApp1** " （如果你希望它与 [下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 代码的命名空间相匹配）。</span><span class="sxs-lookup"><span data-stu-id="34c54-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="34c54-117">选择 **ASP.NET Core Web 应用程序** > **正常**</span><span class="sxs-lookup"><span data-stu-id="34c54-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="34c54-118">在下拉列表中选择 **ASP.NET Core 3.0**</span><span class="sxs-lookup"><span data-stu-id="34c54-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="34c54-119">选择 **Web 应用程序** > **正常**</span><span class="sxs-lookup"><span data-stu-id="34c54-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="34c54-120">生成并运行该项目。</span><span class="sxs-lookup"><span data-stu-id="34c54-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="34c54-121">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="34c54-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="34c54-122">将项目命名为 " **WebApp1** " （如果你希望它与 [下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 代码的命名空间相匹配）。</span><span class="sxs-lookup"><span data-stu-id="34c54-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="34c54-123">选择 **ASP.NET Core Web 应用程序** > **正常**</span><span class="sxs-lookup"><span data-stu-id="34c54-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="34c54-124">在下拉列表中选择 **ASP.NET Core 2.2**</span><span class="sxs-lookup"><span data-stu-id="34c54-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="34c54-125">选择 **Web 应用程序** > **正常**</span><span class="sxs-lookup"><span data-stu-id="34c54-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="34c54-126">生成并运行该项目。</span><span class="sxs-lookup"><span data-stu-id="34c54-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="34c54-127">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="34c54-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="34c54-128">运行 Identity scaffolder</span><span class="sxs-lookup"><span data-stu-id="34c54-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="34c54-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="34c54-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="34c54-130">在 **解决方案资源管理器** 中，右键单击项目 > "**添加**  >  **新的基架项**"。</span><span class="sxs-lookup"><span data-stu-id="34c54-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="34c54-131">在 "**添加基架**" 对话框的左窗格中，选择 " **Identity**  >  **添加**"。</span><span class="sxs-lookup"><span data-stu-id="34c54-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="34c54-132">在 "**添加 Identity** " 对话框中，选择以下选项：</span><span class="sxs-lookup"><span data-stu-id="34c54-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="34c54-133">选择现有的布局文件  *~/Pages/Shared/_Layout cshtml*</span><span class="sxs-lookup"><span data-stu-id="34c54-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="34c54-134">选择以下要重写的文件：</span><span class="sxs-lookup"><span data-stu-id="34c54-134">Select the following files to override:</span></span>
    * <span data-ttu-id="34c54-135">**帐户/注册**</span><span class="sxs-lookup"><span data-stu-id="34c54-135">**Account/Register**</span></span>
    * <span data-ttu-id="34c54-136">**帐户/管理/索引**</span><span class="sxs-lookup"><span data-stu-id="34c54-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="34c54-137">选择 **+** 按钮以创建新的 **数据上下文类**。</span><span class="sxs-lookup"><span data-stu-id="34c54-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="34c54-138">如果项目命名为 **WebApp1**) ，则接受类型 (**WebApp1. WebApp1Context** 。</span><span class="sxs-lookup"><span data-stu-id="34c54-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="34c54-139">选择 **+** 按钮以创建新的 **用户类**。</span><span class="sxs-lookup"><span data-stu-id="34c54-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="34c54-140">如果项目命名为 **WebApp1**) > "**添加**"，则接受 " **WebApp1User** " (类型。</span><span class="sxs-lookup"><span data-stu-id="34c54-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="34c54-141">选择 **添加** 。</span><span class="sxs-lookup"><span data-stu-id="34c54-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="34c54-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="34c54-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="34c54-143">如果你之前未安装 ASP.NET Core scaffolder，请立即安装：</span><span class="sxs-lookup"><span data-stu-id="34c54-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="34c54-144">将对 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 的包引用添加到项目 ( .csproj) 文件中。</span><span class="sxs-lookup"><span data-stu-id="34c54-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="34c54-145">在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="34c54-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="34c54-146">运行以下命令以列出 Identity scaffolder 选项：</span><span class="sxs-lookup"><span data-stu-id="34c54-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="34c54-147">在项目文件夹中，运行 Identity scaffolder：</span><span class="sxs-lookup"><span data-stu-id="34c54-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="34c54-148">按照 " [迁移"、"UseAuthentication" 和 "布局](xref:security/authentication/scaffold-identity#efm) " 中的说明执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="34c54-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="34c54-149">创建迁移并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="34c54-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="34c54-150">已将 `UseAuthentication` 添加到 `Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="34c54-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="34c54-151">添加 `<partial name="_LoginPartial" />` 到布局文件中。</span><span class="sxs-lookup"><span data-stu-id="34c54-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="34c54-152">测试应用：</span><span class="sxs-lookup"><span data-stu-id="34c54-152">Test the app:</span></span>
  * <span data-ttu-id="34c54-153">注册用户</span><span class="sxs-lookup"><span data-stu-id="34c54-153">Register a user</span></span>
  * <span data-ttu-id="34c54-154">选择 " **注销** " 链接旁 ("新用户名") 。</span><span class="sxs-lookup"><span data-stu-id="34c54-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="34c54-155">可能需要展开窗口或选择导航栏图标来显示用户名和其他链接。</span><span class="sxs-lookup"><span data-stu-id="34c54-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="34c54-156">选择 " **个人数据** " 选项卡。</span><span class="sxs-lookup"><span data-stu-id="34c54-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="34c54-157">选择 " **下载** " 按钮，并检查文件 *上的PersonalData.js* 。</span><span class="sxs-lookup"><span data-stu-id="34c54-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="34c54-158">测试 **删除** 按钮，该按钮将删除已登录的用户。</span><span class="sxs-lookup"><span data-stu-id="34c54-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="34c54-159">向数据库添加自定义用户数据 Identity</span><span class="sxs-lookup"><span data-stu-id="34c54-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="34c54-160">`IdentityUser`用自定义属性更新派生类。</span><span class="sxs-lookup"><span data-stu-id="34c54-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="34c54-161">如果已将项目命名为 WebApp1，则该文件将命名为 *Areas/ Identity /Data/WebApp1User.cs*。</span><span class="sxs-lookup"><span data-stu-id="34c54-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="34c54-162">用以下代码更新文件：</span><span class="sxs-lookup"><span data-stu-id="34c54-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="34c54-163">具有 [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) 属性的属性为：</span><span class="sxs-lookup"><span data-stu-id="34c54-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="34c54-164">当 *Areas/ Identity /Pages/Account/Manage/DeletePersonalData.cshtml* Razor 页调用时删除 `UserManager.Delete` 。</span><span class="sxs-lookup"><span data-stu-id="34c54-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="34c54-165">由 *Areas/ Identity /Pages/Account/Manage/DownloadPersonalData.cshtml* 页包含在下载的数据中 Razor 。</span><span class="sxs-lookup"><span data-stu-id="34c54-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="34c54-166">更新 "帐户/管理/索引" 页</span><span class="sxs-lookup"><span data-stu-id="34c54-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="34c54-167">`InputModel`用以下突出显示的代码更新 *区域/ Identity /Pages/Account/Manage/Index.cshtml.cs* 中的：</span><span class="sxs-lookup"><span data-stu-id="34c54-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="34c54-168">用以下突出显示的标记更新 *区域/ Identity /Pages/Account/Manage/Index.cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="34c54-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="34c54-169">用以下突出显示的标记更新 *区域/ Identity /Pages/Account/Manage/Index.cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="34c54-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="34c54-170">更新帐户/注册. cshtml 页</span><span class="sxs-lookup"><span data-stu-id="34c54-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="34c54-171">`InputModel`用以下突出显示的代码更新 *区域/ Identity /Pages/Account/Register.cshtml.cs* 中的：</span><span class="sxs-lookup"><span data-stu-id="34c54-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="34c54-172">用以下突出显示的标记更新 *区域/ Identity /Pages/Account/Register.cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="34c54-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="34c54-173">用以下突出显示的标记更新 *区域/ Identity /Pages/Account/Register.cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="34c54-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="34c54-174">生成项目。</span><span class="sxs-lookup"><span data-stu-id="34c54-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="34c54-175">添加自定义用户数据的迁移</span><span class="sxs-lookup"><span data-stu-id="34c54-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="34c54-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="34c54-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="34c54-177">在 Visual Studio **包管理器控制台** 中：</span><span class="sxs-lookup"><span data-stu-id="34c54-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="34c54-178">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="34c54-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="34c54-179">测试创建、查看、下载、删除自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="34c54-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="34c54-180">测试应用：</span><span class="sxs-lookup"><span data-stu-id="34c54-180">Test the app:</span></span>

* <span data-ttu-id="34c54-181">注册新用户。</span><span class="sxs-lookup"><span data-stu-id="34c54-181">Register a new user.</span></span>
* <span data-ttu-id="34c54-182">在页面上查看自定义用户数据 `/Identity/Account/Manage` 。</span><span class="sxs-lookup"><span data-stu-id="34c54-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="34c54-183">从页面下载并查看用户的个人数据 `/Identity/Account/Manage/PersonalData` 。</span><span class="sxs-lookup"><span data-stu-id="34c54-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="34c54-184">Identity使用 IUserClaimsPrincipalFactory 添加声明<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="34c54-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="34c54-185">本部分不是上一教程的扩展。</span><span class="sxs-lookup"><span data-stu-id="34c54-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="34c54-186">若要将以下步骤应用到使用本教程构建的应用，请参阅 [此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/18797)。</span><span class="sxs-lookup"><span data-stu-id="34c54-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="34c54-187">可以通过使用接口向添加其他声明 ASP.NET Core Identity `IUserClaimsPrincipalFactory<T>` 。</span><span class="sxs-lookup"><span data-stu-id="34c54-187">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="34c54-188">可在方法中将此类添加到应用 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="34c54-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="34c54-189">添加类的自定义实现，如下所示：</span><span class="sxs-lookup"><span data-stu-id="34c54-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="34c54-190">演示代码使用 `ApplicationUser` 类。</span><span class="sxs-lookup"><span data-stu-id="34c54-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="34c54-191">此类添加一个 `IsAdmin` 属性，该属性用于添加其他声明。</span><span class="sxs-lookup"><span data-stu-id="34c54-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="34c54-192">`AdditionalUserClaimsPrincipalFactory` 实现 `UserClaimsPrincipalFactory` 接口。</span><span class="sxs-lookup"><span data-stu-id="34c54-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="34c54-193">新的角色声明将添加到中 `ClaimsPrincipal` 。</span><span class="sxs-lookup"><span data-stu-id="34c54-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="34c54-194">然后，可以在应用中使用其他声明。</span><span class="sxs-lookup"><span data-stu-id="34c54-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="34c54-195">在 Razor 页中， `IAuthorizationService` 可以使用实例访问声明值。</span><span class="sxs-lookup"><span data-stu-id="34c54-195">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
