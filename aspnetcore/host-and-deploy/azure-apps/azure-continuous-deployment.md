---
title: 使用 Visual Studio 和 Git 将 ASP.NET Core 持续部署到 Azure
author: rick-anderson
description: 了解如何使用 Visual Studio 创建 ASP.NET Core Web 应用并使用 Git 将它部署到 Azure 应用服务以实现持续部署。
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
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
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 561f41dd9d50df0651c060fac4f75d9d59244029
ms.sourcegitcommit: acfe51c35497a204f75c2a61125c9408c04493e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102605641"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="1e766-103">使用 Visual Studio 和 Git 将 ASP.NET Core 持续部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="1e766-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="1e766-104">作者：[Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="1e766-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="1e766-105">本教程演示如何使用 Visual Studio 创建 ASP.NET Core Web 应用并使用持续部署将其从 Visual Studio 部署到 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="1e766-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="1e766-106">另请参阅[使用 Azure Pipelines 创建你的第一个管道](/azure/devops/pipelines/get-started-yaml)，该文档介绍如何使用 Azure DevOps Services 为 [Azure 应用服务](/azure/app-service/app-service-web-overview)配置持续交付 (CD) 工作流。</span><span class="sxs-lookup"><span data-stu-id="1e766-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="1e766-107">Azure Pipelines（一种 Azure DevOps Services 服务）简化了可靠部署管道的设置，便于为 Azure 应用服务内托管的应用发布更新。</span><span class="sxs-lookup"><span data-stu-id="1e766-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="1e766-108">可以从 Azure 门户配置管道以生成、运行测试、部署到过渡槽，然后部署到生产。</span><span class="sxs-lookup"><span data-stu-id="1e766-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="1e766-109">若要完成本教程，需要一个 Microsoft Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="1e766-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="1e766-110">要获取帐户，可[激活 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F)或[注册免费试用版](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F)。</span><span class="sxs-lookup"><span data-stu-id="1e766-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1e766-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="1e766-111">Prerequisites</span></span>

<span data-ttu-id="1e766-112">本教程假定已安装以下软件：</span><span class="sxs-lookup"><span data-stu-id="1e766-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="1e766-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e766-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="1e766-114">用于 Windows 的 [Git](https://git-scm.com/downloads)</span><span class="sxs-lookup"><span data-stu-id="1e766-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="1e766-115">创建 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="1e766-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="1e766-116">启动 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="1e766-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="1e766-117">从“文件”菜单中选择“新建” > “项目”  。</span><span class="sxs-lookup"><span data-stu-id="1e766-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="1e766-118">选择“ASP.NET Core Web 应用程序”项目模板。</span><span class="sxs-lookup"><span data-stu-id="1e766-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="1e766-119">它出现在“已安装” > “模板” > “Visual C#” > “.NET Core”下。   </span><span class="sxs-lookup"><span data-stu-id="1e766-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="1e766-120">将项目命名为 `SampleWebAppDemo`。</span><span class="sxs-lookup"><span data-stu-id="1e766-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="1e766-121">选择“新建 Git 存储库”选项，然后单击“确定” 。</span><span class="sxs-lookup"><span data-stu-id="1e766-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![“新建项目”对话框](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="1e766-123">在“新建 ASP.NET Core 项目”对话框中，选择 ASP.NET Core 的“空”模板，然后单击“确定”。  </span><span class="sxs-lookup"><span data-stu-id="1e766-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![新的 ASP.NET Core 项目对话框](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="1e766-125">.NET Core 的最新版本为 2.0。</span><span class="sxs-lookup"><span data-stu-id="1e766-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="1e766-126">本地运行 Web 应用</span><span class="sxs-lookup"><span data-stu-id="1e766-126">Running the web app locally</span></span>

1. <span data-ttu-id="1e766-127">Visual Studio 完成创建应用后，请选择“调试” > “启动调试”以运行该应用。 </span><span class="sxs-lookup"><span data-stu-id="1e766-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="1e766-128">作为替代方法，也可以按 F5。</span><span class="sxs-lookup"><span data-stu-id="1e766-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="1e766-129">可能需要一些时间对 Visual Studio 和新应用进行初始化。</span><span class="sxs-lookup"><span data-stu-id="1e766-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="1e766-130">完成后，浏览器将显示正在运行的应用。</span><span class="sxs-lookup"><span data-stu-id="1e766-130">Once it's complete, the browser shows the running app.</span></span>

   ![显示正在运行的应用程序（显示“Hello World!”）的浏览器窗口](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="1e766-132">查看完正在运行的 Web 应用后，关闭浏览器，然后选择 Visual Studio 工具栏中的“停止调试”图标以停止应用。</span><span class="sxs-lookup"><span data-stu-id="1e766-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="1e766-133">在 Azure 门户中创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="1e766-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="1e766-134">按照以下步骤在 Azure 门户中创建 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="1e766-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="1e766-135">登录到 [Azure 门户](https://portal.azure.com)。</span><span class="sxs-lookup"><span data-stu-id="1e766-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="1e766-136">选择门户界面左上方的“新建”。</span><span class="sxs-lookup"><span data-stu-id="1e766-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="1e766-137">选择“Web + 移动” > “Web 应用” 。</span><span class="sxs-lookup"><span data-stu-id="1e766-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure 门户：“新建”按钮：“市场”下的“Web + 移动”：“特色应用”下的“Web 应用”按钮](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="1e766-139">在“Web 应用”边栏选项卡中，输入“应用服务名称”的唯一值。 </span><span class="sxs-lookup"><span data-stu-id="1e766-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![“Web 应用”边栏选项卡](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="1e766-141">“应用服务名称”的名称必须是唯一的。</span><span class="sxs-lookup"><span data-stu-id="1e766-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="1e766-142">门户在提供名称时强制执行此规则。</span><span class="sxs-lookup"><span data-stu-id="1e766-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="1e766-143">在提供一个不同的值后，需要为在本教程中每次出现的“SampleWebAppDemo”替换该值。</span><span class="sxs-lookup"><span data-stu-id="1e766-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="1e766-144">另外，在“Web 应用”边栏选项卡中，选择现有“应用服务计划/位置”或新建一个。 </span><span class="sxs-lookup"><span data-stu-id="1e766-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="1e766-145">如果创建新的计划，请选择“定价层”、“位置”和其他选项。</span><span class="sxs-lookup"><span data-stu-id="1e766-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="1e766-146">有关应用服务计划的详细信息，请参阅 [Azure App Service 计划深入概述](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)。</span><span class="sxs-lookup"><span data-stu-id="1e766-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="1e766-147">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="1e766-147">Select **Create**.</span></span> <span data-ttu-id="1e766-148">Azure 将预配并启动 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1e766-148">Azure will provision and start the web app.</span></span>

   ![Azure 门户：示例 Web 应用演示 01 Essentials 边栏选项卡](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="1e766-150">为新 Web 应用启用 Git 发布</span><span class="sxs-lookup"><span data-stu-id="1e766-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="1e766-151">Git 是一个分布式版本控制系统，可用来部署 Azure App Service Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1e766-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="1e766-152">Web 应用代码存储在本地 Git 存储库中，并通过推送到远程存储库将代码部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="1e766-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="1e766-153">登录到 [Azure 门户](https://portal.azure.com)。</span><span class="sxs-lookup"><span data-stu-id="1e766-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="1e766-154">选择“应用服务”查看与 Azure 订阅关联的应用服务列表。</span><span class="sxs-lookup"><span data-stu-id="1e766-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="1e766-155">选择在本教程的前一部分中创建的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1e766-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="1e766-156">在“部署”边栏选项卡中，选择“部署选项” > “选择源” > “本地 Git 存储库”。   </span><span class="sxs-lookup"><span data-stu-id="1e766-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![“设置”边栏选项卡：“部署源”边栏选项卡：“选择源”边栏选项卡](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="1e766-158">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="1e766-158">Select **OK**.</span></span>

1. <span data-ttu-id="1e766-159">如果事先未设置用于发布 Web 应用的部署凭据或其他应用服务应用，请立即设置：</span><span class="sxs-lookup"><span data-stu-id="1e766-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="1e766-160">选择“设置” > “部署凭据”。 </span><span class="sxs-lookup"><span data-stu-id="1e766-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="1e766-161">“设置部署凭据”边栏选项卡将显示。</span><span class="sxs-lookup"><span data-stu-id="1e766-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="1e766-162">创建用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="1e766-162">Create a user name and password.</span></span> <span data-ttu-id="1e766-163">在设置 Git 时保存密码供以供将来使用。</span><span class="sxs-lookup"><span data-stu-id="1e766-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="1e766-164">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="1e766-164">Select **Save**.</span></span>

1. <span data-ttu-id="1e766-165">在“Web 应用”边栏选项卡中，选择“设置” > “属性”。  </span><span class="sxs-lookup"><span data-stu-id="1e766-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="1e766-166">要部署到的远程 Git 存储库的 URL 会显示在“GIT URL”下。</span><span class="sxs-lookup"><span data-stu-id="1e766-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="1e766-167">复制“GIT URL”的值，稍后将在本教程中用到。</span><span class="sxs-lookup"><span data-stu-id="1e766-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure 门户：应用程序“属性”边栏选项卡](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="1e766-169">将 Web 应用发布到 Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1e766-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="1e766-170">在本部分中，使用 Visual Studio 创建本地 Git 存储库并从该存储库推送到 Azure 以部署 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1e766-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="1e766-171">涉及到的步骤如下：</span><span class="sxs-lookup"><span data-stu-id="1e766-171">The steps involved include the following:</span></span>

* <span data-ttu-id="1e766-172">使用 GIT URL 值添加远程存储库设置，这样本地存储库就可部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="1e766-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="1e766-173">提交项目更改。</span><span class="sxs-lookup"><span data-stu-id="1e766-173">Commit project changes.</span></span>
* <span data-ttu-id="1e766-174">将项目更改从本地存储库推送到 Azure 上的远程存储库。</span><span class="sxs-lookup"><span data-stu-id="1e766-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="1e766-175">在“解决方案资源管理器”中右键单击“解决方案 ‘SampleWebAppDemo’”并选择“提交”。  </span><span class="sxs-lookup"><span data-stu-id="1e766-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="1e766-176">此时将显示“团队资源管理器”。</span><span class="sxs-lookup"><span data-stu-id="1e766-176">The **Team Explorer** is displayed.</span></span>

   ![“团队资源管理器连接”选项卡](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="1e766-178">在“团队资源管理器”中，选择“主页”（主页图标）>“设置” > “存储库设置”。   </span><span class="sxs-lookup"><span data-stu-id="1e766-178">In **Team Explorer**, select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="1e766-179">在“存储库设置”的“远程”部分中选择“添加”。  </span><span class="sxs-lookup"><span data-stu-id="1e766-179">In the **Remotes** section of the **Repository Settings**, select **Add**.</span></span> <span data-ttu-id="1e766-180">将显示“添加远程”对话框。</span><span class="sxs-lookup"><span data-stu-id="1e766-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="1e766-181">将远程的“名称”设置为“Azure-SampleApp”。 </span><span class="sxs-lookup"><span data-stu-id="1e766-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="1e766-182">将“提取”的值设置为在本教程前面部分从 Azure 复制的“Git URL”。 </span><span class="sxs-lookup"><span data-stu-id="1e766-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="1e766-183">请注意，此 URL 是以 .git 结尾的。</span><span class="sxs-lookup"><span data-stu-id="1e766-183">Note that this is the URL that ends with **.git**.</span></span>

   ![“编辑远程”对话框](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="1e766-185">作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入命令从“命令窗口”指定远程存储库。 </span><span class="sxs-lookup"><span data-stu-id="1e766-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the command.</span></span> <span data-ttu-id="1e766-186">示例：</span><span class="sxs-lookup"><span data-stu-id="1e766-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="1e766-187">选择“主页”（主页图标）>“设置” > “全局设置”。  </span><span class="sxs-lookup"><span data-stu-id="1e766-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="1e766-188">确认名称和电子邮件地址已设置。</span><span class="sxs-lookup"><span data-stu-id="1e766-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="1e766-189">如有必要，则选择“更新”。</span><span class="sxs-lookup"><span data-stu-id="1e766-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="1e766-190">选择“主页” > “更改”以返回到“更改”视图。  </span><span class="sxs-lookup"><span data-stu-id="1e766-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="1e766-191">输入提交消息，如“初始推送 #1”，然后选择“提交”。 </span><span class="sxs-lookup"><span data-stu-id="1e766-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="1e766-192">此操作将本地创建“提交”。</span><span class="sxs-lookup"><span data-stu-id="1e766-192">This action creates a *commit* locally.</span></span>

   ![“团队资源管理器连接”选项卡](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="1e766-194">作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入 git 命令从“命令窗口”提交更改。 </span><span class="sxs-lookup"><span data-stu-id="1e766-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="1e766-195">示例：</span><span class="sxs-lookup"><span data-stu-id="1e766-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="1e766-196">选择“主页” > “同步” > “操作” > “打开命令提示符”。   </span><span class="sxs-lookup"><span data-stu-id="1e766-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="1e766-197">命令提示符打开到项目目录。</span><span class="sxs-lookup"><span data-stu-id="1e766-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="1e766-198">在命令窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="1e766-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp main`

1. <span data-ttu-id="1e766-199">输入之前在 Azure 中创建的 Azure“部署凭据”密码。</span><span class="sxs-lookup"><span data-stu-id="1e766-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="1e766-200">此命令将启动将本地项目文件推送到 Azure 的进程。</span><span class="sxs-lookup"><span data-stu-id="1e766-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="1e766-201">上述命令的输出以部署成功的消息结尾。</span><span class="sxs-lookup"><span data-stu-id="1e766-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      main -> main
   Branch main set up to track remote branch main from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="1e766-202">如果项目需要协作，请考虑推送到 [GitHub](https://github.com)，然后再推送到 Azure。</span><span class="sxs-lookup"><span data-stu-id="1e766-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="1e766-203">验证活动的部署</span><span class="sxs-lookup"><span data-stu-id="1e766-203">Verify the Active Deployment</span></span>

<span data-ttu-id="1e766-204">验证 Web 应用从本地环境传输到 Azure 是否成功。</span><span class="sxs-lookup"><span data-stu-id="1e766-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="1e766-205">在 [Azure 门户](https://portal.azure.com)中，选择“Web 应用”。</span><span class="sxs-lookup"><span data-stu-id="1e766-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="1e766-206">然后，选择“部署” > “部署选项”。</span><span class="sxs-lookup"><span data-stu-id="1e766-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure 门户：“设置”边栏选项卡：显示成功部署的“部署”边栏选项卡](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="1e766-208">在 Azure 中运行此应用</span><span class="sxs-lookup"><span data-stu-id="1e766-208">Run the app in Azure</span></span>

<span data-ttu-id="1e766-209">现在该 Web 应用已部署到 Azure，请运行该应用。</span><span class="sxs-lookup"><span data-stu-id="1e766-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="1e766-210">这可以通过以下两种方式实现：</span><span class="sxs-lookup"><span data-stu-id="1e766-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="1e766-211">在 Azure 门户中，找到 Web 应用的“Web 应用”边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="1e766-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="1e766-212">选择“浏览”以在默认浏览器中查看该应用程序。</span><span class="sxs-lookup"><span data-stu-id="1e766-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="1e766-213">打开浏览器并输入 Web 应用的 URL。</span><span class="sxs-lookup"><span data-stu-id="1e766-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="1e766-214">示例：`http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="1e766-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="1e766-215">更新 Web 应用并重新发布</span><span class="sxs-lookup"><span data-stu-id="1e766-215">Update the web app and republish</span></span>

<span data-ttu-id="1e766-216">更改本地代码后重新发布：</span><span class="sxs-lookup"><span data-stu-id="1e766-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="1e766-217">在 Visual Studio 的“解决方案资源管理器”中，打开 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="1e766-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="1e766-218">在 `Configure` 方法中，修改 `Response.WriteAsync` 方法，使它显示以下内容：</span><span class="sxs-lookup"><span data-stu-id="1e766-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="1e766-219">将更改保存到 Startup.cs。</span><span class="sxs-lookup"><span data-stu-id="1e766-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="1e766-220">在“解决方案资源管理器”中右键单击“解决方案 ‘SampleWebAppDemo’”并选择“提交”。  </span><span class="sxs-lookup"><span data-stu-id="1e766-220">In **Solution Explorer**, right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="1e766-221">此时将显示“团队资源管理器”。</span><span class="sxs-lookup"><span data-stu-id="1e766-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="1e766-222">输入提交消息，如 `Update #2`。</span><span class="sxs-lookup"><span data-stu-id="1e766-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="1e766-223">按“提交”按钮以提交项目更改。</span><span class="sxs-lookup"><span data-stu-id="1e766-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="1e766-224">选择“主页” > “同步” > “操作” > “推送”。   </span><span class="sxs-lookup"><span data-stu-id="1e766-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="1e766-225">作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入 git 命令从“命令窗口”推送更改。 </span><span class="sxs-lookup"><span data-stu-id="1e766-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window**, changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="1e766-226">示例：</span><span class="sxs-lookup"><span data-stu-id="1e766-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp main`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="1e766-227">在 Azure 中查看更新的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="1e766-227">View the updated web app in Azure</span></span>

<span data-ttu-id="1e766-228">通过从 Azure 门户中的“Web 应用”边栏选项卡选择“浏览”，或通过打开浏览器并输入 Web 应用的 URL 以查看更新的 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1e766-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="1e766-229">示例：`http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="1e766-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1e766-230">其他资源</span><span class="sxs-lookup"><span data-stu-id="1e766-230">Additional resources</span></span>

* [<span data-ttu-id="1e766-231">使用 Azure Pipelines 创建你的第一个管道</span><span class="sxs-lookup"><span data-stu-id="1e766-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="1e766-232">项目 Kudu</span><span class="sxs-lookup"><span data-stu-id="1e766-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
