---
title: 带有 ASP.NET Core 的 Visual Studio 容器工具
author: spboyer
description: 了解如何使用 Visual Studio 工具和用于 Windows 的 Docker 来容器化 ASP.NET Core 应用。
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: c11261fef84234a9caaf872ae9871932d02617fa
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585989"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="7e662-103">带有 ASP.NET Core 的 Visual Studio 容器工具</span><span class="sxs-lookup"><span data-stu-id="7e662-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="7e662-104">Visual Studio 2017 及更高版本支持构建、调试和运行面向 .NET Core 且已容器化的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="7e662-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="7e662-105">Windows 和 Linux 容器均受支持。</span><span class="sxs-lookup"><span data-stu-id="7e662-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="7e662-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7e662-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e662-107">先决条件</span><span class="sxs-lookup"><span data-stu-id="7e662-107">Prerequisites</span></span>

* [<span data-ttu-id="7e662-108">Docker for Windows</span><span class="sxs-lookup"><span data-stu-id="7e662-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="7e662-109">带有 .NET Core 跨平台开发工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="7e662-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="7e662-110">安装和设置</span><span class="sxs-lookup"><span data-stu-id="7e662-110">Installation and setup</span></span>

<span data-ttu-id="7e662-111">如需安装 Docker，请先通过[用于 Windows 的 Docker：安装须知](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)了解相关信息。</span><span class="sxs-lookup"><span data-stu-id="7e662-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="7e662-112">然后安装[用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/install/)。</span><span class="sxs-lookup"><span data-stu-id="7e662-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="7e662-113">Docker for Windows 中的[共享驱动器](https://docs.docker.com/docker-for-windows/#shared-drives)必须配置为支持卷映射和调试。</span><span class="sxs-lookup"><span data-stu-id="7e662-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="7e662-114">右键单击系统托盘中的 Docker 图标，单击“设置”，然后选择“共享驱动器”。</span><span class="sxs-lookup"><span data-stu-id="7e662-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="7e662-115">选择 Docker 存储文件的驱动器。</span><span class="sxs-lookup"><span data-stu-id="7e662-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="7e662-116">单击“应用”。</span><span class="sxs-lookup"><span data-stu-id="7e662-116">Click **Apply**.</span></span>

![为容器选择本地驱动器 C 作为共享驱动器的对话框](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="7e662-118">未配置共享驱动器时，Visual Studio 2017 15.6 及更高版本会发出提示。</span><span class="sxs-lookup"><span data-stu-id="7e662-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="7e662-119">向 Docker 容器添加项目</span><span class="sxs-lookup"><span data-stu-id="7e662-119">Add a project to a Docker container</span></span>

<span data-ttu-id="7e662-120">若要容器化 ASP.NET Core 项目，项目必须面向 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="7e662-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="7e662-121">同时支持 Linux 和 Windows 容器。</span><span class="sxs-lookup"><span data-stu-id="7e662-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="7e662-122">向项目添加 Docker 支持后，可选择 Windows 或 Linux 容器。</span><span class="sxs-lookup"><span data-stu-id="7e662-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="7e662-123">Docker 主机必须运行类型相同的容器。</span><span class="sxs-lookup"><span data-stu-id="7e662-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="7e662-124">要更改正在运行的 Docker 实例中的容器类型，请右键单击系统托盘中的 Docker 图标，再选择“切换到 Windows 容器...”或“切换到 Linux 容器...”   。</span><span class="sxs-lookup"><span data-stu-id="7e662-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="7e662-125">新应用</span><span class="sxs-lookup"><span data-stu-id="7e662-125">New app</span></span>

<span data-ttu-id="7e662-126">使用“ASP.NET Core Web 应用”项目模板新建应用时，请选中“启用 Docker 支持”复选框：</span><span class="sxs-lookup"><span data-stu-id="7e662-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![“启用 Docker 支持”复选框](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="7e662-128">如果目标框架是 .NET Core，可通过 OS 下拉列表选择容器类型。</span><span class="sxs-lookup"><span data-stu-id="7e662-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="7e662-129">现有应用</span><span class="sxs-lookup"><span data-stu-id="7e662-129">Existing app</span></span>

<span data-ttu-id="7e662-130">对于面向 .NET Core 的 ASP.NET Core 项目，可通过两个选项使用工具添加 Docker 支持。</span><span class="sxs-lookup"><span data-stu-id="7e662-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="7e662-131">在 Visual Studio 中打开项目，然后选择以下选项之一：</span><span class="sxs-lookup"><span data-stu-id="7e662-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="7e662-132">从“项目”菜单中选择“Docker 支持” 。</span><span class="sxs-lookup"><span data-stu-id="7e662-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="7e662-133">右键单击“解决方案资源管理器”中的项目，然后选择“添加” > “Docker 支持”  。</span><span class="sxs-lookup"><span data-stu-id="7e662-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="7e662-134">Visual Studio 容器工具不支持向面向 .NET Framework 的现有 ASP.NET Core 项目添加 Docker。</span><span class="sxs-lookup"><span data-stu-id="7e662-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="7e662-135">Dockerfile 概述</span><span class="sxs-lookup"><span data-stu-id="7e662-135">Dockerfile overview</span></span>

<span data-ttu-id="7e662-136">Dockerfile，用作创建最终 Docker 映像的方案，添加到项目根目录。</span><span class="sxs-lookup"><span data-stu-id="7e662-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="7e662-137">请参阅 [Dockerfile 引用](https://docs.docker.com/engine/reference/builder/)，了解其中的命令。</span><span class="sxs-lookup"><span data-stu-id="7e662-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="7e662-138">此特定 Dockerfile 使用[多阶段生成](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)，该生成包含四个不同的命名生成阶段：</span><span class="sxs-lookup"><span data-stu-id="7e662-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="7e662-139">前面的 Dockerfile 基于 [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) 映像。</span><span class="sxs-lookup"><span data-stu-id="7e662-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="7e662-140">此基础映像包括 ASP.NET Core 运行时和 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="7e662-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="7e662-141">对该包进行了实时 (JIT) 编译，以提高启动性能。</span><span class="sxs-lookup"><span data-stu-id="7e662-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="7e662-142">如果选中了新建项目对话框的“为 HTTPS 配置”复选框，则 Dockerfile 公开两个端口。</span><span class="sxs-lookup"><span data-stu-id="7e662-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="7e662-143">一个端口用于 HTTP 流量；另一个端口用于 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="7e662-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="7e662-144">如果未选中该复选框，则为 HTTP 流量公开单个端口 (80)。</span><span class="sxs-lookup"><span data-stu-id="7e662-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="7e662-145">前面的 Dockerfile 基于 [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) 映像。</span><span class="sxs-lookup"><span data-stu-id="7e662-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="7e662-146">此基础映像包括 ASP.NET Core NuGet 包，对该包进行了实时编译 (JIT)，以提高启动性能。</span><span class="sxs-lookup"><span data-stu-id="7e662-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="7e662-147">向应用添加容器业务流程协调程序支持</span><span class="sxs-lookup"><span data-stu-id="7e662-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="7e662-148">Visual Studio 2017 版本 15.7 或更早版本支持 [Docker Compose](https://docs.docker.com/compose/overview/) 作为唯一的容器业务流程解决方案。</span><span class="sxs-lookup"><span data-stu-id="7e662-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="7e662-149">可通过“添加” > “Docker 支持”添加 Docker Compose 。</span><span class="sxs-lookup"><span data-stu-id="7e662-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="7e662-150">Visual Studio 2017 版本 15.8 或更高版本仅在获得指示时添加业务流程解决方案。</span><span class="sxs-lookup"><span data-stu-id="7e662-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="7e662-151">右键单击“解决方案资源管理器”中的项目，然后选择“添加” > “容器业务流程协调程序支持”  。</span><span class="sxs-lookup"><span data-stu-id="7e662-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="7e662-152">可用选项如下：</span><span class="sxs-lookup"><span data-stu-id="7e662-152">The following choices are available:</span></span> 

* [<span data-ttu-id="7e662-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="7e662-153">Docker Compose</span></span>](#docker-compose)
* [<span data-ttu-id="7e662-154">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="7e662-154">Service Fabric</span></span>](#service-fabric)
* [<span data-ttu-id="7e662-155">Kubernetes/Helm </span><span class="sxs-lookup"><span data-stu-id="7e662-155">Kubernetes/Helm </span></span>](https://helm.sh/)

### <a name="docker-compose"></a><span data-ttu-id="7e662-156">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="7e662-156">Docker Compose</span></span>

<span data-ttu-id="7e662-157">Visual Studio 容器工具通过以下文件向解决方案添加 docker-compose 项目：</span><span class="sxs-lookup"><span data-stu-id="7e662-157">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="7e662-158">docker-compose.dcproj：表示项目的文件。</span><span class="sxs-lookup"><span data-stu-id="7e662-158">*docker-compose.dcproj*: The file representing the project.</span></span> <span data-ttu-id="7e662-159">包括 `<DockerTargetOS>` 元素，用于指定要使用的操作系统。</span><span class="sxs-lookup"><span data-stu-id="7e662-159">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="7e662-160">.dockerignore：列出在生成生成上下文时要排除的文件和目录模式。</span><span class="sxs-lookup"><span data-stu-id="7e662-160">*.dockerignore*: Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="7e662-161">docker-compose.yml：基本 [Docker Compose](https://docs.docker.com/compose/overview/) 文件，用于定义要分别通过 `docker-compose build` 和 `docker-compose run` 生成和运行的映像集合。</span><span class="sxs-lookup"><span data-stu-id="7e662-161">*docker-compose.yml*: The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="7e662-162">docker-compose.override.yml：一个可选文件，通过 Docker Compose 读取，包含服务的配置替代。</span><span class="sxs-lookup"><span data-stu-id="7e662-162">*docker-compose.override.yml*: An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="7e662-163">Visual Studio 执行 `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` 以合并这些文件。</span><span class="sxs-lookup"><span data-stu-id="7e662-163">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="7e662-164">docker-compose.yml 文件引用在项目运行时创建的映像的名称：</span><span class="sxs-lookup"><span data-stu-id="7e662-164">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="7e662-165">在前面的示例中，应用在“调试”模式下运行时，`image: hellodockertools` 生成映像 `hellodockertools:dev`。</span><span class="sxs-lookup"><span data-stu-id="7e662-165">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="7e662-166">应用在“发布”模式下运行时，生成 `hellodockertools:latest` 映像。</span><span class="sxs-lookup"><span data-stu-id="7e662-166">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="7e662-167">如果将映像推送到注册表，则需要添加 [Docker Hub](https://hub.docker.com/) 用户名（如 `dockerhubusername/hellodockertools`）作为映像名称的前缀名。</span><span class="sxs-lookup"><span data-stu-id="7e662-167">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="7e662-168">或者，更改映像名称，使其包含专用注册表 URL（如 `privateregistry.domain.com/hellodockertools`），具体取决于所用配置。</span><span class="sxs-lookup"><span data-stu-id="7e662-168">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="7e662-169">如果根据生成配置需要不同行为（例如，调试或发布），请添加特定于配置的 docker-compose 文件。</span><span class="sxs-lookup"><span data-stu-id="7e662-169">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="7e662-170">这些文件应根据生成配置进行命名（例如，docker-compose.vs.debug.yml 和 docker compose.vs.release.yml）并放置在与 docker-compose-override.yml 文件相同的位置。</span><span class="sxs-lookup"><span data-stu-id="7e662-170">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="7e662-171">使用特定于配置的替代文件，可以为调试和发布生成配置指定不同的配置设置（如环境变量或入口点）。</span><span class="sxs-lookup"><span data-stu-id="7e662-171">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="7e662-172">docker 项目必须是启动项目，Docker Compose 才会显示要在 Visual Studio 中显示的选项。</span><span class="sxs-lookup"><span data-stu-id="7e662-172">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="7e662-173">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="7e662-173">Service Fabric</span></span>

<span data-ttu-id="7e662-174">除了基础[必备组件](#prerequisites)外，[Service Fabric](/azure/service-fabric/) 业务流程解决方案还需要以下必备组件：</span><span class="sxs-lookup"><span data-stu-id="7e662-174">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="7e662-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) 版本 2.6 或更高版本</span><span class="sxs-lookup"><span data-stu-id="7e662-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="7e662-176">Visual Studio 的 Azure 开发工作负载</span><span class="sxs-lookup"><span data-stu-id="7e662-176">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="7e662-177">Service Fabric 不支持在 Windows 上的本地开发群集中运行 Linux 容器。</span><span class="sxs-lookup"><span data-stu-id="7e662-177">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="7e662-178">如果项目已在使用 Linux 容器，Visual Studio 会提示切换到 Windows 容器。</span><span class="sxs-lookup"><span data-stu-id="7e662-178">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="7e662-179">Visual Studio 容器工具执行以下任务：</span><span class="sxs-lookup"><span data-stu-id="7e662-179">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="7e662-180">向解决方案添加 &lt;project_name&gt;Application Service Fabric 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="7e662-180">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="7e662-181">向 ASP.NET Core 项目添加 Dockerfile 和 .dockerignore 文件 。</span><span class="sxs-lookup"><span data-stu-id="7e662-181">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="7e662-182">如果 ASP.NET Core 项目中已存在 Dockerfile，则其重命名为 Dockerfile.original 。</span><span class="sxs-lookup"><span data-stu-id="7e662-182">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="7e662-183">创建类似于以下形式的新 Dockerfile：</span><span class="sxs-lookup"><span data-stu-id="7e662-183">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="7e662-184">将 `<IsServiceFabricServiceProject>` 元素添加到 ASP.NET Core 项目的 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="7e662-184">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="7e662-185">向 ASP.NET Core 项目添加 PackageRoot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="7e662-185">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="7e662-186">该文件夹包含服务清单和新服务的设置。</span><span class="sxs-lookup"><span data-stu-id="7e662-186">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="7e662-187">有关详细信息，请参阅[将 Windows 容器中的 .NET 应用部署到 Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)。</span><span class="sxs-lookup"><span data-stu-id="7e662-187">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="7e662-188">调试</span><span class="sxs-lookup"><span data-stu-id="7e662-188">Debug</span></span>

<span data-ttu-id="7e662-189">在工具栏的调试下拉列表中选择“Docker”，然后开始调试应用。</span><span class="sxs-lookup"><span data-stu-id="7e662-189">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="7e662-190">“输出”窗口的“Docker”视图显示发生的以下操作 ：</span><span class="sxs-lookup"><span data-stu-id="7e662-190">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="7e662-191">已获取 microsoft/dotnet 运行时映像的 2.1-aspnetcore-runtime 标记（如果缓存中尚不存在） 。</span><span class="sxs-lookup"><span data-stu-id="7e662-191">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="7e662-192">该映像可安装 ASP.NET Core 和.NET Core 运行时及其关联的库。</span><span class="sxs-lookup"><span data-stu-id="7e662-192">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="7e662-193">在生产环境中针对运行 ASP.NET Core 应用对其进行了优化。</span><span class="sxs-lookup"><span data-stu-id="7e662-193">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="7e662-194">`ASPNETCORE_ENVIRONMENT` 环境变量设置为容器内的 `Development`。</span><span class="sxs-lookup"><span data-stu-id="7e662-194">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="7e662-195">公开了两个动态分配的端口：分别用于 HTTP 和 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="7e662-195">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="7e662-196">可以使用 `docker ps` 命令查询分配给本地主机的端口。</span><span class="sxs-lookup"><span data-stu-id="7e662-196">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="7e662-197">应用已复制到容器。</span><span class="sxs-lookup"><span data-stu-id="7e662-197">The app is copied to the container.</span></span>
* <span data-ttu-id="7e662-198">默认浏览器使用动态分配端口启动，带有附加到容器的调试程序。</span><span class="sxs-lookup"><span data-stu-id="7e662-198">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="7e662-199">最终得到的应用的 Docker 映像标记为“开发”。</span><span class="sxs-lookup"><span data-stu-id="7e662-199">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="7e662-200">该映像基于 microsoft/dotnet 基础映像的 2.1-aspnetcore-runtime 标记 。</span><span class="sxs-lookup"><span data-stu-id="7e662-200">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="7e662-201">在“包管理器控制台”(PMC) 窗口中运行 `docker images` 命令。</span><span class="sxs-lookup"><span data-stu-id="7e662-201">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="7e662-202">显示了计算机上的映像：</span><span class="sxs-lookup"><span data-stu-id="7e662-202">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="7e662-203">已获取 microsoft/aspnetcore 运行时映像（如果缓存中尚不存在）。</span><span class="sxs-lookup"><span data-stu-id="7e662-203">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="7e662-204">`ASPNETCORE_ENVIRONMENT` 环境变量设置为容器内的 `Development`。</span><span class="sxs-lookup"><span data-stu-id="7e662-204">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="7e662-205">端口 80 公开，并映射到 localhost 的动态分配端口。</span><span class="sxs-lookup"><span data-stu-id="7e662-205">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="7e662-206">该端口由 Docker 主机确定，并且可以使用 `docker ps` 进行查询。</span><span class="sxs-lookup"><span data-stu-id="7e662-206">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="7e662-207">应用已复制到容器。</span><span class="sxs-lookup"><span data-stu-id="7e662-207">The app is copied to the container.</span></span>
* <span data-ttu-id="7e662-208">默认浏览器使用动态分配端口启动，带有附加到容器的调试程序。</span><span class="sxs-lookup"><span data-stu-id="7e662-208">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="7e662-209">最终得到的应用的 Docker 映像标记为“开发”。</span><span class="sxs-lookup"><span data-stu-id="7e662-209">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="7e662-210">该映像基于 microsoft/aspnetcore 基础映像。</span><span class="sxs-lookup"><span data-stu-id="7e662-210">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="7e662-211">在“包管理器控制台”(PMC) 窗口中运行 `docker images` 命令。</span><span class="sxs-lookup"><span data-stu-id="7e662-211">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="7e662-212">显示了计算机上的映像：</span><span class="sxs-lookup"><span data-stu-id="7e662-212">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7e662-213">因为“调试”配置使用卷装载提供迭代体验，因此，开发映像中缺少应用内容。</span><span class="sxs-lookup"><span data-stu-id="7e662-213">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="7e662-214">要推送映像，请使用“发布”配置。</span><span class="sxs-lookup"><span data-stu-id="7e662-214">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="7e662-215">在 PMC 中运行 `docker ps` 命令。</span><span class="sxs-lookup"><span data-stu-id="7e662-215">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="7e662-216">请注意，应用使用容器运行：</span><span class="sxs-lookup"><span data-stu-id="7e662-216">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="7e662-217">编辑并继续</span><span class="sxs-lookup"><span data-stu-id="7e662-217">Edit and continue</span></span>

<span data-ttu-id="7e662-218">对静态文件和 Razor 视图的更改会自动更新，无需执行编译步骤。</span><span class="sxs-lookup"><span data-stu-id="7e662-218">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="7e662-219">进行更改，保存并在浏览器中刷新，以查看更新。</span><span class="sxs-lookup"><span data-stu-id="7e662-219">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="7e662-220">必须在容器内编译和重启 Kestrel，才能修改代码文件。</span><span class="sxs-lookup"><span data-stu-id="7e662-220">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="7e662-221">更改后，按 `CTRL+F5` 执行过程，并在容器内启动应用。</span><span class="sxs-lookup"><span data-stu-id="7e662-221">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="7e662-222">未重新生成或停止 Docker 容器。</span><span class="sxs-lookup"><span data-stu-id="7e662-222">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="7e662-223">在 PMC 中运行 `docker ps` 命令。</span><span class="sxs-lookup"><span data-stu-id="7e662-223">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="7e662-224">请注意，截至 10 分钟前，原始容器仍在运行：</span><span class="sxs-lookup"><span data-stu-id="7e662-224">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="7e662-225">发布 Docker 映像</span><span class="sxs-lookup"><span data-stu-id="7e662-225">Publish Docker images</span></span>

<span data-ttu-id="7e662-226">完成应用的开发和调试循环后，Visual Studio 容器工具可帮助创建应用的生产映像。</span><span class="sxs-lookup"><span data-stu-id="7e662-226">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="7e662-227">将配置下拉列表更改为“发布”，然后生成应用。</span><span class="sxs-lookup"><span data-stu-id="7e662-227">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="7e662-228">该工具从 Docker Hub 中获取 compile/publish 映像（如果缓存中尚不存在）。</span><span class="sxs-lookup"><span data-stu-id="7e662-228">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="7e662-229">生成了具有“latest”标签的映像，可以将其推送到专用注册表或 Docker Hub。</span><span class="sxs-lookup"><span data-stu-id="7e662-229">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="7e662-230">在 PMC 中运行 `docker images` 命令，查看映像列表。</span><span class="sxs-lookup"><span data-stu-id="7e662-230">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="7e662-231">显示了类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="7e662-231">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="7e662-232">自 .NET Core 2.1 起，前面的输出中列出的 `microsoft/aspnetcore-build` 和 `microsoft/aspnetcore` 映像替换为 `microsoft/dotnet` 映像。</span><span class="sxs-lookup"><span data-stu-id="7e662-232">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="7e662-233">有关详细信息，请参阅 [Docker 存储库迁移公告](https://github.com/aspnet/Announcements/issues/298)。</span><span class="sxs-lookup"><span data-stu-id="7e662-233">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7e662-234">`docker images` 命令返回存储库名称和标记标识为 *\<none>* （上面未列出）的中间映像。</span><span class="sxs-lookup"><span data-stu-id="7e662-234">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="7e662-235">这些未命名映像由[多阶段生成](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) Dockerfile 生成。</span><span class="sxs-lookup"><span data-stu-id="7e662-235">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="7e662-236">它们可提高生成最终映像的效率 &mdash; 发生更改时，仅重新生成必要的层。</span><span class="sxs-lookup"><span data-stu-id="7e662-236">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="7e662-237">不再需要中间映像时，请使用 [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) 命令将其删除。</span><span class="sxs-lookup"><span data-stu-id="7e662-237">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="7e662-238">可能希望生产或发布映像的大小比开发映像小。</span><span class="sxs-lookup"><span data-stu-id="7e662-238">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="7e662-239">由于卷映射，调试程序和应用从本地计算机运行，而不在容器内运行。</span><span class="sxs-lookup"><span data-stu-id="7e662-239">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="7e662-240">最新映像已打包必要的应用代码，以在主机上运行应用。</span><span class="sxs-lookup"><span data-stu-id="7e662-240">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="7e662-241">因此，增量是应用代码的大小。</span><span class="sxs-lookup"><span data-stu-id="7e662-241">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e662-242">其他资源</span><span class="sxs-lookup"><span data-stu-id="7e662-242">Additional resources</span></span>

* [<span data-ttu-id="7e662-243">利用 Visual Studio 进行容器开发</span><span class="sxs-lookup"><span data-stu-id="7e662-243">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="7e662-244">Azure Service Fabric：准备开发环境</span><span class="sxs-lookup"><span data-stu-id="7e662-244">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="7e662-245">将 Windows 容器中的 .NET 应用部署到 Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="7e662-245">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="7e662-246">使用 Docker 排查 Visual Studio 开发方面的问题</span><span class="sxs-lookup"><span data-stu-id="7e662-246">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="7e662-247">Visual Studio 容器工具 GitHub 存储库</span><span class="sxs-lookup"><span data-stu-id="7e662-247">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
* [<span data-ttu-id="7e662-248">使用 Docker 和小型容器的 GC</span><span class="sxs-lookup"><span data-stu-id="7e662-248">GC using Docker and small containers</span></span>](xref:performance/memory#sc)
