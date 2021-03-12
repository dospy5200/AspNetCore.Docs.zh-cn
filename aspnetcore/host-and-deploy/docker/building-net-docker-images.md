---
title: ASP.NET Core 的 Docker 映像
author: rick-anderson
description: 了解如何使用 Docker 注册表中发布的 ASP.NET Core Docker 映像。 拉取并生成你自己的映像。
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: b29ce03366e5c0e815de0874f5b96efb9ba5326c
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585950"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="ab322-104">ASP.NET Core 的 Docker 映像</span><span class="sxs-lookup"><span data-stu-id="ab322-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="ab322-105">本教程演示如何在 Docker 容器中运行 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="ab322-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="ab322-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="ab322-107">了解 ASP.NET Core Docker 映像</span><span class="sxs-lookup"><span data-stu-id="ab322-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="ab322-108">下载 ASP.NET Core 示例应用</span><span class="sxs-lookup"><span data-stu-id="ab322-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="ab322-109">本地运行示例应用</span><span class="sxs-lookup"><span data-stu-id="ab322-109">Run the sample app locally</span></span>
> * <span data-ttu-id="ab322-110">在 Linux 容器中运行示例应用</span><span class="sxs-lookup"><span data-stu-id="ab322-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="ab322-111">在 Windows 容器中运行示例应用</span><span class="sxs-lookup"><span data-stu-id="ab322-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="ab322-112">手动生成和部署</span><span class="sxs-lookup"><span data-stu-id="ab322-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="ab322-113">ASP.NET Core Docker 映像</span><span class="sxs-lookup"><span data-stu-id="ab322-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="ab322-114">在本教程中，你下载 ASP.NET Core 示例应用并在 Docker 容器中运行它。</span><span class="sxs-lookup"><span data-stu-id="ab322-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="ab322-115">此示例适用于 Linux 和 Windows 容器。</span><span class="sxs-lookup"><span data-stu-id="ab322-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="ab322-116">示例 Dockerfile 使用 [Docker 多阶段构建功能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)在不同的容器中生成和运行。</span><span class="sxs-lookup"><span data-stu-id="ab322-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="ab322-117">生成和运行容器是由 Microsoft 从 Docker 中心提供的映像中创建的：</span><span class="sxs-lookup"><span data-stu-id="ab322-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="ab322-118">此示例将此映像用于生成应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="ab322-119">此映像包含带有命令行工具 (CLI) 的 .NET SDK。</span><span class="sxs-lookup"><span data-stu-id="ab322-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="ab322-120">此映像对本地开发、调试和单元测试进行了优化。</span><span class="sxs-lookup"><span data-stu-id="ab322-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="ab322-121">为开发和编译而安装的工具使映像变得相对较大。</span><span class="sxs-lookup"><span data-stu-id="ab322-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="ab322-122">此示例将此映像用于生成应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="ab322-123">此映像包含带有命令行工具 (CLI) 的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="ab322-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="ab322-124">此映像对本地开发、调试和单元测试进行了优化。</span><span class="sxs-lookup"><span data-stu-id="ab322-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="ab322-125">为开发和编译而安装的工具使映像变得相对较大。</span><span class="sxs-lookup"><span data-stu-id="ab322-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="ab322-126">此示例将此映像用于运行应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="ab322-127">此映像包含 ASP.NET Core 运行时和库，并针对在生产中运行应用进行了优化。</span><span class="sxs-lookup"><span data-stu-id="ab322-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="ab322-128">此映像专为部署和应用启动的速度而设计，相对较小，因此优化了从 Docker 注册表到 Docker 主机的网络性能。</span><span class="sxs-lookup"><span data-stu-id="ab322-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="ab322-129">仅将运行应用所需的二进制文件和内容复制到容器。</span><span class="sxs-lookup"><span data-stu-id="ab322-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="ab322-130">已准备运行内容，以此实现从 `docker run` 到应用启动的最快时间。</span><span class="sxs-lookup"><span data-stu-id="ab322-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="ab322-131">Docker 模型中不需要动态代码编译。</span><span class="sxs-lookup"><span data-stu-id="ab322-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ab322-132">先决条件</span><span class="sxs-lookup"><span data-stu-id="ab322-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="ab322-133">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="ab322-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="ab322-134">.NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="ab322-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="ab322-135">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="ab322-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="ab322-136">Docker 客户端 18.03 或更高版本</span><span class="sxs-lookup"><span data-stu-id="ab322-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="ab322-137">Linux 分布</span><span class="sxs-lookup"><span data-stu-id="ab322-137">Linux distributions</span></span>
    * [<span data-ttu-id="ab322-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="ab322-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="ab322-139">Debian</span><span class="sxs-lookup"><span data-stu-id="ab322-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="ab322-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="ab322-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="ab322-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="ab322-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="ab322-142">macOS</span><span class="sxs-lookup"><span data-stu-id="ab322-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="ab322-143">Windows</span><span class="sxs-lookup"><span data-stu-id="ab322-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="ab322-144">Git</span><span class="sxs-lookup"><span data-stu-id="ab322-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="ab322-145">下载示例应用</span><span class="sxs-lookup"><span data-stu-id="ab322-145">Download the sample app</span></span>

* <span data-ttu-id="ab322-146">通过克隆 [.NET Docker 存储库](https://github.com/dotnet/dotnet-docker)下载示例：</span><span class="sxs-lookup"><span data-stu-id="ab322-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="ab322-147">本地运行应用</span><span class="sxs-lookup"><span data-stu-id="ab322-147">Run the app locally</span></span>

* <span data-ttu-id="ab322-148">导航到 dotnet-docker/samples/aspnetapp/aspnetapp  下的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="ab322-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="ab322-149">运行以下命令以本地生成并运行应用：</span><span class="sxs-lookup"><span data-stu-id="ab322-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="ab322-150">在浏览器中转到 `http://localhost:5000` 以测试应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="ab322-151">在命令提示符处按 Ctrl+C 以停止应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="ab322-152">在 Linux 容器中运行</span><span class="sxs-lookup"><span data-stu-id="ab322-152">Run in a Linux container</span></span>

* <span data-ttu-id="ab322-153">在 Docker 客户端中，[切换到 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。</span><span class="sxs-lookup"><span data-stu-id="ab322-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="ab322-154">导航到 dotnet-docker/samples/aspnetapp  下的 Dockerfile 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ab322-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="ab322-155">运行以下命令以在 Docker 中生成并运行示例：</span><span class="sxs-lookup"><span data-stu-id="ab322-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="ab322-156">`build` 命令参数：</span><span class="sxs-lookup"><span data-stu-id="ab322-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="ab322-157">将映像命名为 aspnetapp。</span><span class="sxs-lookup"><span data-stu-id="ab322-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="ab322-158">在当前文件夹（末尾的句点）中查找 Dockerfile。</span><span class="sxs-lookup"><span data-stu-id="ab322-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="ab322-159">运行命令参数：</span><span class="sxs-lookup"><span data-stu-id="ab322-159">The run command arguments:</span></span>
  * <span data-ttu-id="ab322-160">分配伪 TTY，即使未附加也使其保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="ab322-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="ab322-161">（与 `--interactive --tty` 的效果相同。）</span><span class="sxs-lookup"><span data-stu-id="ab322-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="ab322-162">容器在退出时自动删除。</span><span class="sxs-lookup"><span data-stu-id="ab322-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="ab322-163">将本地计算机上的端口 5000 映射到容器中的端口 80。</span><span class="sxs-lookup"><span data-stu-id="ab322-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="ab322-164">将容器命名为 aspnetcore_sample。</span><span class="sxs-lookup"><span data-stu-id="ab322-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="ab322-165">指定 aspnetapp 映像。</span><span class="sxs-lookup"><span data-stu-id="ab322-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="ab322-166">在浏览器中转到 `http://localhost:5000` 以测试应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="ab322-167">在 Windows 容器中运行</span><span class="sxs-lookup"><span data-stu-id="ab322-167">Run in a Windows container</span></span>

* <span data-ttu-id="ab322-168">在 Docker 客户端中，[切换到 Windows 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。</span><span class="sxs-lookup"><span data-stu-id="ab322-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="ab322-169">导航到 `dotnet-docker/samples/aspnetapp` 下的 docker 文件文件夹。</span><span class="sxs-lookup"><span data-stu-id="ab322-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="ab322-170">运行以下命令以在 Docker 中生成并运行示例：</span><span class="sxs-lookup"><span data-stu-id="ab322-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="ab322-171">对于 Windows 容器，你需要容器的 IP 地址（浏览到 `http://localhost:5000` 不起作用）：</span><span class="sxs-lookup"><span data-stu-id="ab322-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="ab322-172">打开另一个命令提示符。</span><span class="sxs-lookup"><span data-stu-id="ab322-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="ab322-173">运行 `docker ps` 以查看正在运行的容器。</span><span class="sxs-lookup"><span data-stu-id="ab322-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="ab322-174">验证其中是否包含“aspnetcore_sample”容器。</span><span class="sxs-lookup"><span data-stu-id="ab322-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="ab322-175">运行 `docker exec aspnetcore_sample ipconfig` 以显示容器的 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="ab322-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="ab322-176">该命令的输出如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="ab322-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="ab322-177">将容器 IPv4 地址（例如，172.29.245.43）复制并粘贴到浏览器地址栏以测试应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="ab322-178">手动生成和部署</span><span class="sxs-lookup"><span data-stu-id="ab322-178">Build and deploy manually</span></span>

<span data-ttu-id="ab322-179">在某些情况下，你可能希望通过复制运行时所需的应用资产来将应用部署到容器中。</span><span class="sxs-lookup"><span data-stu-id="ab322-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="ab322-180">本部分演示如何手动进行部署。</span><span class="sxs-lookup"><span data-stu-id="ab322-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="ab322-181">导航到 dotnet-docker/samples/aspnetapp/aspnetapp  下的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="ab322-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="ab322-182">运行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令：</span><span class="sxs-lookup"><span data-stu-id="ab322-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="ab322-183">命令参数：</span><span class="sxs-lookup"><span data-stu-id="ab322-183">The command arguments:</span></span>
  * <span data-ttu-id="ab322-184">在发布模式（默认为调试模式）下生成应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="ab322-185">在“已发布”文件夹中创建资产。</span><span class="sxs-lookup"><span data-stu-id="ab322-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="ab322-186">运行应用。</span><span class="sxs-lookup"><span data-stu-id="ab322-186">Run the app.</span></span>

  * <span data-ttu-id="ab322-187">Windows:</span><span class="sxs-lookup"><span data-stu-id="ab322-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="ab322-188">Linux：</span><span class="sxs-lookup"><span data-stu-id="ab322-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="ab322-189">浏览到 `http://localhost:5000` 以查看主页。</span><span class="sxs-lookup"><span data-stu-id="ab322-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="ab322-190">要在 Docker 容器中使用手动发布的应用，请创建新的 Dockerfile，并使用 `docker build .` 命令构建容器。</span><span class="sxs-lookup"><span data-stu-id="ab322-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="ab322-191">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="ab322-191">The Dockerfile</span></span>

<span data-ttu-id="ab322-192">下面是先前运行的 `docker build` 命令使用的 Dockerfile  。</span><span class="sxs-lookup"><span data-stu-id="ab322-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="ab322-193">它以本部分中所用的方式使用 `dotnet publish` 进行生成和部署。</span><span class="sxs-lookup"><span data-stu-id="ab322-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="ab322-194">在前面的 Dockerfile 中，将 `*.csproj` 文件作为不同的层进行复制和还原 。</span><span class="sxs-lookup"><span data-stu-id="ab322-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="ab322-195">当 `docker build` 命令生成映像时，它将使用内置缓存。</span><span class="sxs-lookup"><span data-stu-id="ab322-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="ab322-196">如果自上次运行 `docker build` 命令后，`*.csproj` 文件未发生更改，则 `dotnet restore` 命令无需再次运行。</span><span class="sxs-lookup"><span data-stu-id="ab322-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="ab322-197">但是，将重复使用相应 `dotnet restore` 层的内置缓存。</span><span class="sxs-lookup"><span data-stu-id="ab322-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="ab322-198">有关详细信息，请参阅[编写 Dockerfile 的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)。</span><span class="sxs-lookup"><span data-stu-id="ab322-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="ab322-199">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="ab322-199">The Dockerfile</span></span>

<span data-ttu-id="ab322-200">下面是先前运行的 `docker build` 命令使用的 Dockerfile  。</span><span class="sxs-lookup"><span data-stu-id="ab322-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="ab322-201">它以本部分中所用的方式使用 `dotnet publish` 进行生成和部署。</span><span class="sxs-lookup"><span data-stu-id="ab322-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="ab322-202">如前面的 Dockerfile 中所述，将 `*.csproj` 文件作为不同的层进行复制和还原  。</span><span class="sxs-lookup"><span data-stu-id="ab322-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="ab322-203">当 `docker build` 命令生成映像时，它将使用内置缓存。</span><span class="sxs-lookup"><span data-stu-id="ab322-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="ab322-204">如果自上次运行 `docker build` 命令后，`*.csproj` 文件未发生更改，则 `dotnet restore` 命令无需再次运行。</span><span class="sxs-lookup"><span data-stu-id="ab322-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="ab322-205">但是，将重复使用相应 `dotnet restore` 层的内置缓存。</span><span class="sxs-lookup"><span data-stu-id="ab322-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="ab322-206">有关详细信息，请参阅[编写 Dockerfile 的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)。</span><span class="sxs-lookup"><span data-stu-id="ab322-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="ab322-207">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="ab322-207">The Dockerfile</span></span>

<span data-ttu-id="ab322-208">下面是先前运行的 `docker build` 命令使用的 Dockerfile  。</span><span class="sxs-lookup"><span data-stu-id="ab322-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="ab322-209">它以本部分中所用的方式使用 `dotnet publish` 进行生成和部署。</span><span class="sxs-lookup"><span data-stu-id="ab322-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ab322-210">其他资源</span><span class="sxs-lookup"><span data-stu-id="ab322-210">Additional resources</span></span>

* [<span data-ttu-id="ab322-211">Docker 生成命令</span><span class="sxs-lookup"><span data-stu-id="ab322-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="ab322-212">Docker 运行命令</span><span class="sxs-lookup"><span data-stu-id="ab322-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="ab322-213">[ASP.NET Core Docker 示例](https://github.com/dotnet/dotnet-docker)（本教程中使用的示例。）</span><span class="sxs-lookup"><span data-stu-id="ab322-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="ab322-214">配置 ASP.NET Core 以使用代理服务器和负载均衡器</span><span class="sxs-lookup"><span data-stu-id="ab322-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="ab322-215">使用 Visual Studio Docker 工具</span><span class="sxs-lookup"><span data-stu-id="ab322-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="ab322-216">使用 Visual Studio Code 进行调试</span><span class="sxs-lookup"><span data-stu-id="ab322-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="ab322-217">使用 Docker 和小型容器的 GC</span><span class="sxs-lookup"><span data-stu-id="ab322-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="ab322-218">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ab322-218">Next steps</span></span>

<span data-ttu-id="ab322-219">包含示例应用的 Git 存储库还包括文档。</span><span class="sxs-lookup"><span data-stu-id="ab322-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="ab322-220">有关存储库中可用资源的概述，请参阅[自述文件](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md)。</span><span class="sxs-lookup"><span data-stu-id="ab322-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="ab322-221">特别是，了解如何实现 HTTPS：</span><span class="sxs-lookup"><span data-stu-id="ab322-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="ab322-222">使用 Docker over HTTPS 开发 ASP.NET Core 应用程序</span><span class="sxs-lookup"><span data-stu-id="ab322-222">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)
