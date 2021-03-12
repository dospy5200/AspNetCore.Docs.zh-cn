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
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>使用 Visual Studio 和 Git 将 ASP.NET Core 持续部署到 Azure

作者：[Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

本教程演示如何使用 Visual Studio 创建 ASP.NET Core Web 应用并使用持续部署将其从 Visual Studio 部署到 Azure 应用服务。

另请参阅[使用 Azure Pipelines 创建你的第一个管道](/azure/devops/pipelines/get-started-yaml)，该文档介绍如何使用 Azure DevOps Services 为 [Azure 应用服务](/azure/app-service/app-service-web-overview)配置持续交付 (CD) 工作流。 Azure Pipelines（一种 Azure DevOps Services 服务）简化了可靠部署管道的设置，便于为 Azure 应用服务内托管的应用发布更新。 可以从 Azure 门户配置管道以生成、运行测试、部署到过渡槽，然后部署到生产。

> [!NOTE]
> 若要完成本教程，需要一个 Microsoft Azure 帐户。 要获取帐户，可[激活 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F)或[注册免费试用版](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本教程假定已安装以下软件：

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* 用于 Windows 的 [Git](https://git-scm.com/downloads)

## <a name="create-an-aspnet-core-web-app"></a>创建 ASP.NET Core Web 应用

1. 启动 Visual Studio。

1. 从“文件”菜单中选择“新建” > “项目”  。

1. 选择“ASP.NET Core Web 应用程序”项目模板。 它出现在“已安装” > “模板” > “Visual C#” > “.NET Core”下。    将项目命名为 `SampleWebAppDemo`。 选择“新建 Git 存储库”选项，然后单击“确定” 。

   ![“新建项目”对话框](azure-continuous-deployment/_static/01-new-project.png)

1. 在“新建 ASP.NET Core 项目”对话框中，选择 ASP.NET Core 的“空”模板，然后单击“确定”。  

   ![新的 ASP.NET Core 项目对话框](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> .NET Core 的最新版本为 2.0。

### <a name="running-the-web-app-locally"></a>本地运行 Web 应用

1. Visual Studio 完成创建应用后，请选择“调试” > “启动调试”以运行该应用。  作为替代方法，也可以按 F5。

   可能需要一些时间对 Visual Studio 和新应用进行初始化。 完成后，浏览器将显示正在运行的应用。

   ![显示正在运行的应用程序（显示“Hello World!”）的浏览器窗口](azure-continuous-deployment/_static/04-browser-runapp.png)

1. 查看完正在运行的 Web 应用后，关闭浏览器，然后选择 Visual Studio 工具栏中的“停止调试”图标以停止应用。

## <a name="create-a-web-app-in-the-azure-portal"></a>在 Azure 门户中创建 Web 应用

按照以下步骤在 Azure 门户中创建 Web 应用：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择门户界面左上方的“新建”。

1. 选择“Web + 移动” > “Web 应用” 。

   ![Microsoft Azure 门户：“新建”按钮：“市场”下的“Web + 移动”：“特色应用”下的“Web 应用”按钮](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. 在“Web 应用”边栏选项卡中，输入“应用服务名称”的唯一值。 

   ![“Web 应用”边栏选项卡](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > “应用服务名称”的名称必须是唯一的。 门户在提供名称时强制执行此规则。 在提供一个不同的值后，需要为在本教程中每次出现的“SampleWebAppDemo”替换该值。

   另外，在“Web 应用”边栏选项卡中，选择现有“应用服务计划/位置”或新建一个。  如果创建新的计划，请选择“定价层”、“位置”和其他选项。 有关应用服务计划的详细信息，请参阅 [Azure App Service 计划深入概述](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)。

1. 选择“创建”。 Azure 将预配并启动 Web 应用。

   ![Azure 门户：示例 Web 应用演示 01 Essentials 边栏选项卡](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>为新 Web 应用启用 Git 发布

Git 是一个分布式版本控制系统，可用来部署 Azure App Service Web 应用。 Web 应用代码存储在本地 Git 存储库中，并通过推送到远程存储库将代码部署到 Azure。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“应用服务”查看与 Azure 订阅关联的应用服务列表。

1. 选择在本教程的前一部分中创建的 Web 应用。

1. 在“部署”边栏选项卡中，选择“部署选项” > “选择源” > “本地 Git 存储库”。   

   ![“设置”边栏选项卡：“部署源”边栏选项卡：“选择源”边栏选项卡](azure-continuous-deployment/_static/deployment-options.png)

1. 选择“确定”。

1. 如果事先未设置用于发布 Web 应用的部署凭据或其他应用服务应用，请立即设置：

   * 选择“设置” > “部署凭据”。  “设置部署凭据”边栏选项卡将显示。
   * 创建用户名和密码。 在设置 Git 时保存密码供以供将来使用。
   * 选择“保存”。

1. 在“Web 应用”边栏选项卡中，选择“设置” > “属性”。   要部署到的远程 Git 存储库的 URL 会显示在“GIT URL”下。

1. 复制“GIT URL”的值，稍后将在本教程中用到。

   ![Azure 门户：应用程序“属性”边栏选项卡](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>将 Web 应用发布到 Azure App Service

在本部分中，使用 Visual Studio 创建本地 Git 存储库并从该存储库推送到 Azure 以部署 Web 应用。 涉及到的步骤如下：

* 使用 GIT URL 值添加远程存储库设置，这样本地存储库就可部署到 Azure。
* 提交项目更改。
* 将项目更改从本地存储库推送到 Azure 上的远程存储库。

1. 在“解决方案资源管理器”中右键单击“解决方案 ‘SampleWebAppDemo’”并选择“提交”。   此时将显示“团队资源管理器”。

   ![“团队资源管理器连接”选项卡](azure-continuous-deployment/_static/10-team-explorer.png)

1. 在“团队资源管理器”中，选择“主页”（主页图标）>“设置” > “存储库设置”。   

1. 在“存储库设置”的“远程”部分中选择“添加”。   将显示“添加远程”对话框。

1. 将远程的“名称”设置为“Azure-SampleApp”。 

1. 将“提取”的值设置为在本教程前面部分从 Azure 复制的“Git URL”。  请注意，此 URL 是以 .git 结尾的。

   ![“编辑远程”对话框](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > 作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入命令从“命令窗口”指定远程存储库。  示例：
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. 选择“主页”（主页图标）>“设置” > “全局设置”。   确认名称和电子邮件地址已设置。 如有必要，则选择“更新”。

1. 选择“主页” > “更改”以返回到“更改”视图。  

1. 输入提交消息，如“初始推送 #1”，然后选择“提交”。  此操作将本地创建“提交”。

   ![“团队资源管理器连接”选项卡](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > 作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入 git 命令从“命令窗口”提交更改。  示例：
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. 选择“主页” > “同步” > “操作” > “打开命令提示符”。    命令提示符打开到项目目录。

1. 在命令窗口中输入以下命令：

   `git push -u Azure-SampleApp main`

1. 输入之前在 Azure 中创建的 Azure“部署凭据”密码。

   此命令将启动将本地项目文件推送到 Azure 的进程。 上述命令的输出以部署成功的消息结尾。

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      main -> main
   Branch main set up to track remote branch main from Azure-SampleApp.
   ```

   > [!NOTE]
   > 如果项目需要协作，请考虑推送到 [GitHub](https://github.com)，然后再推送到 Azure。
 
### <a name="verify-the-active-deployment"></a>验证活动的部署

验证 Web 应用从本地环境传输到 Azure 是否成功。

在 [Azure 门户](https://portal.azure.com)中，选择“Web 应用”。 然后，选择“部署” > “部署选项”。

![Azure 门户：“设置”边栏选项卡：显示成功部署的“部署”边栏选项卡](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>在 Azure 中运行此应用

现在该 Web 应用已部署到 Azure，请运行该应用。

这可以通过以下两种方式实现：

* 在 Azure 门户中，找到 Web 应用的“Web 应用”边栏选项卡。 选择“浏览”以在默认浏览器中查看该应用程序。
* 打开浏览器并输入 Web 应用的 URL。 示例：`http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>更新 Web 应用并重新发布

更改本地代码后重新发布：

1. 在 Visual Studio 的“解决方案资源管理器”中，打开 Startup.cs 文件。

1. 在 `Configure` 方法中，修改 `Response.WriteAsync` 方法，使它显示以下内容：

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. 将更改保存到 Startup.cs。

1. 在“解决方案资源管理器”中右键单击“解决方案 ‘SampleWebAppDemo’”并选择“提交”。   此时将显示“团队资源管理器”。

1. 输入提交消息，如 `Update #2`。

1. 按“提交”按钮以提交项目更改。

1. 选择“主页” > “同步” > “操作” > “推送”。   

> [!NOTE]
> 作为替代方法，可以通过打开“命令窗口”、更改为项目目录并输入 git 命令从“命令窗口”推送更改。  示例：
> 
> `git push -u Azure-SampleApp main`

## <a name="view-the-updated-web-app-in-azure"></a>在 Azure 中查看更新的 Web 应用

通过从 Azure 门户中的“Web 应用”边栏选项卡选择“浏览”，或通过打开浏览器并输入 Web 应用的 URL 以查看更新的 Web 应用。 示例：`http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>其他资源

* [使用 Azure Pipelines 创建你的第一个管道](/azure/devops/pipelines/get-started-yaml)
* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
