---
title: ASP.NET Core 中的配置
author: rick-anderson
description: 理解如何使用配置 API 配置 ASP.NET Core 应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 1/29/2021
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
uid: fundamentals/configuration/index
ms.openlocfilehash: fa7fbf1ae88b28cd36c5aece626240d849669a96
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586340"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="99ce0-103">ASP.NET Core 中的配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="99ce0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="99ce0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="99ce0-105">ASP.NET Core 中的配置是使用一个或多个[配置提供程序](#cp)执行的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="99ce0-106">配置提供程序使用各种配置源从键值对读取配置数据：</span><span class="sxs-lookup"><span data-stu-id="99ce0-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="99ce0-107">设置文件，例如 appsettings.json</span><span class="sxs-lookup"><span data-stu-id="99ce0-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="99ce0-108">环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-108">Environment variables</span></span>
* <span data-ttu-id="99ce0-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="99ce0-109">Azure Key Vault</span></span>
* <span data-ttu-id="99ce0-110">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-110">Azure App Configuration</span></span>
* <span data-ttu-id="99ce0-111">命令行参数</span><span class="sxs-lookup"><span data-stu-id="99ce0-111">Command-line arguments</span></span>
* <span data-ttu-id="99ce0-112">已安装或已创建的自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="99ce0-113">目录文件</span><span class="sxs-lookup"><span data-stu-id="99ce0-113">Directory files</span></span>
* <span data-ttu-id="99ce0-114">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="99ce0-114">In-memory .NET objects</span></span>

<span data-ttu-id="99ce0-115">本主题介绍 ASP.NET Core 中的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-115">This topic provides information on configuration in ASP.NET Core.</span></span> <span data-ttu-id="99ce0-116">若要了解如何使用控制台应用中的配置，请参阅 [.NET 配置](/dotnet/core/extensions/configuration)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-116">For information on using configuration in console apps, see [.NET Configuration](/dotnet/core/extensions/configuration).</span></span>

<span data-ttu-id="99ce0-117">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="99ce0-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="99ce0-118">默认配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-118">Default configuration</span></span>

<span data-ttu-id="99ce0-119">通过 [dotnet new](/dotnet/core/tools/dotnet-new) 或 Visual Studio 创建的 ASP.NET Core Web 应用会生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="99ce0-119">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="99ce0-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-120"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="99ce0-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource)：添加现有 `IConfiguration` 作为源。</span><span class="sxs-lookup"><span data-stu-id="99ce0-121">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="99ce0-122">在默认配置示例中，添加[主机](#hvac)配置，并将它设置为应用配置的第一个源。</span><span class="sxs-lookup"><span data-stu-id="99ce0-122">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="99ce0-123">使用 [JSON 配置提供程序](#file-configuration-provider)的 [appsettings.json](#appsettingsjson)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-123">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="99ce0-124">使用 [JSON 配置提供程序](#file-configuration-provider)通过 appsettings.`Environment`.json 提供 。</span><span class="sxs-lookup"><span data-stu-id="99ce0-124">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="99ce0-125">例如，appsettings.Production.json 和 appsettings.Development.json 。</span><span class="sxs-lookup"><span data-stu-id="99ce0-125">For example, *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\*.</span></span>
1. <span data-ttu-id="99ce0-126">应用在 `Development` 环境中运行时的[应用机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-126">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="99ce0-127">使用[环境变量配置提供程序](#evcp)通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-127">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="99ce0-128">使用[命令行配置提供程序](#command-line)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-128">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="99ce0-129">后来添加的配置提供程序会替代之前的密钥设置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-129">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="99ce0-130">例如，如果 appsettings.json 和环境中都设置了 `MyKey`，则使用环境值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-130">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="99ce0-131">使用默认配置提供程序，[命令行配置提供程序](#clcp)将替代所有其他的提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-131">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="99ce0-132">若要详细了解 `CreateDefaultBuilder`，请参阅[默认生成器设置](xref:fundamentals/host/generic-host#default-builder-settings)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-132">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="99ce0-133">以下代码按添加顺序显示了已启用的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="99ce0-133">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### appsettings.json

<span data-ttu-id="99ce0-134">请考虑以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-134">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="99ce0-135">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-135">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-136">默认的 <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 会按以下顺序加载配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-136">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="99ce0-137">appsettings.`Environment`.json ：例如，appsettings.Production.json 和 appsettings.Development.json 。</span><span class="sxs-lookup"><span data-stu-id="99ce0-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production\*\*_._json* and *appsettings*.\*\*\*Development** _._json\* files.</span></span> <span data-ttu-id="99ce0-138">文件的环境版本是根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="99ce0-139">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="99ce0-140">appsettings.`Environment`.json 值将替代 appsettings.json 中的键  。</span><span class="sxs-lookup"><span data-stu-id="99ce0-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="99ce0-141">例如，默认情况下：</span><span class="sxs-lookup"><span data-stu-id="99ce0-141">For example, by default:</span></span>

* <span data-ttu-id="99ce0-142">在开发环境中，appsettings.Development.json 配置会覆盖在 appsettings.json 中找到的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-142">In development, *appsettings*.***Development** _._json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="99ce0-143">在生产环境中，appsettings.Production.json 配置会覆盖在 appsettings.json 中找到的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-143">In production, *appsettings*.***Production** _._json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="99ce0-144">例如，在将应用部署到 Azure 时。</span><span class="sxs-lookup"><span data-stu-id="99ce0-144">For example, when deploying the app to Azure.</span></span>

<span data-ttu-id="99ce0-145">如果必须保证配置值，请参阅 [GetValue](#getvalue)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-145">If a configuration value must be guaranteed, see [GetValue](#getvalue).</span></span> <span data-ttu-id="99ce0-146">前面的示例只读取字符串，不支持默认值</span><span class="sxs-lookup"><span data-stu-id="99ce0-146">The preceding example only reads strings and doesn’t support a default value</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="99ce0-147">使用选项模式绑定分层配置数据</span><span class="sxs-lookup"><span data-stu-id="99ce0-147">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="99ce0-148">使用[默认](#default)配置时，会通过 [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) 启用 appsettings.json 和 appsettings.`Environment`.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="99ce0-148">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="99ce0-149">应用启动后，对 appsettings.json 和 appsettings.`Environment`.json 文件所作的更改将由 [JSON 配置提供程序](#jcp)读取  。</span><span class="sxs-lookup"><span data-stu-id="99ce0-149">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="99ce0-150">有关添加其他 JSON 配置文件的信息，请参阅本文档中的 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-150">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="99ce0-151">合并服务集合</span><span class="sxs-lookup"><span data-stu-id="99ce0-151">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-user-secrets"></a><span data-ttu-id="99ce0-152">安全性和用户机密</span><span class="sxs-lookup"><span data-stu-id="99ce0-152">Security and user secrets</span></span>

<span data-ttu-id="99ce0-153">配置数据指南：</span><span class="sxs-lookup"><span data-stu-id="99ce0-153">Configuration data guidelines:</span></span>

* <span data-ttu-id="99ce0-154">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="99ce0-154">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="99ce0-155">[机密管理器](xref:security/app-secrets)工具可用于存储开发环境中的机密。</span><span class="sxs-lookup"><span data-stu-id="99ce0-155">The [Secret Manager](xref:security/app-secrets) tool can be used to store secrets in development.</span></span>
* <span data-ttu-id="99ce0-156">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="99ce0-156">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="99ce0-157">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="99ce0-157">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="99ce0-158">[默认](#default)情况下，将在 JSON 配置源后注册用户机密配置源。</span><span class="sxs-lookup"><span data-stu-id="99ce0-158">By [default](#default), the user secrets configuration source is registered after the JSON configuration sources.</span></span> <span data-ttu-id="99ce0-159">因此，用户机密密钥优先于 appsettings.json 和 appsettings.`Environment`.json 中的密钥。</span><span class="sxs-lookup"><span data-stu-id="99ce0-159">Therefore, user secrets keys take precedence over keys in *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="99ce0-160">有关存储密码或其他敏感数据的详细信息：</span><span class="sxs-lookup"><span data-stu-id="99ce0-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="99ce0-161"><xref:security/app-secrets>：包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="99ce0-161"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="99ce0-162">机密管理器工具使用[文件配置提供程序](#fcp)将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="99ce0-162">The Secret Manager tool uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="99ce0-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="99ce0-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="99ce0-164">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="99ce0-165">环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-165">Environment variables</span></span>

<span data-ttu-id="99ce0-166">使用[默认](#default)配置时，<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 会在读取 appsettings.json、appsettings.`Environment`.json 和[用户机密](xref:security/app-secrets)之后，从环境变量键值对中加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [user secrets](xref:security/app-secrets).</span></span> <span data-ttu-id="99ce0-167">因此，从环境中读取的键值会替代从 appsettings.json、appsettings.`Environment`.json 和用户机密中读取的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and user secrets.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="99ce0-168">以下 `set` 命令：</span><span class="sxs-lookup"><span data-stu-id="99ce0-168">The following `set` commands:</span></span>

* <span data-ttu-id="99ce0-169">在 Windows 上设置[上述示例](#appsettingsjson)的环境键和值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="99ce0-170">在使用[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)时测试设置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="99ce0-171">`dotnet run` 命令必须在项目目录中运行。</span><span class="sxs-lookup"><span data-stu-id="99ce0-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="99ce0-172">前面的环境设置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-172">The preceding environment settings:</span></span>

* <span data-ttu-id="99ce0-173">仅在进程中设置，这些进程是从设置进程的命令窗口启动的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="99ce0-174">不会由通过 Visual Studio 启动的浏览器读取。</span><span class="sxs-lookup"><span data-stu-id="99ce0-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="99ce0-175">以下 [setx](/windows-server/administration/windows-commands/setx) 命令可用于在 Windows 上设置环境键和值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="99ce0-176">与 `set` 不同，`setx` 设置是持久的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="99ce0-177">`/M` 在系统环境中设置变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="99ce0-178">如果未使用 `/M` 开关，则会设置用户环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```console
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="99ce0-179">若要测试前面的命令是否会替代 appsettings.json 和 appsettings.`Environment`.json  ：</span><span class="sxs-lookup"><span data-stu-id="99ce0-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="99ce0-180">使用 Visual Studio：退出并重启 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="99ce0-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="99ce0-181">使用 CLI：启动新的命令窗口并输入 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="99ce0-182">使用字符串调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 以指定环境变量的前缀：</span><span class="sxs-lookup"><span data-stu-id="99ce0-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="99ce0-183">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="99ce0-183">In the preceding code:</span></span>

* <span data-ttu-id="99ce0-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` 被添加到[默认配置提供程序](#default)之后。</span><span class="sxs-lookup"><span data-stu-id="99ce0-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="99ce0-185">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="99ce0-186">使用 `MyCustomPrefix_` 前缀设置的环境变量将替代[默认配置提供程序](#default)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="99ce0-187">这包括没有前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="99ce0-188">前缀会在读取配置键值对时被去除。</span><span class="sxs-lookup"><span data-stu-id="99ce0-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="99ce0-189">以下命令对自定义前缀进行测试：</span><span class="sxs-lookup"><span data-stu-id="99ce0-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="99ce0-190">[默认配置](#default)会加载前缀为 `DOTNET_` 和 `ASPNETCORE_` 的环境变量和命令行参数。</span><span class="sxs-lookup"><span data-stu-id="99ce0-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="99ce0-191">`DOTNET_` 和 `ASPNETCORE_` 前缀会由 ASP.NET Core 用于[主机和应用配置](xref:fundamentals/host/generic-host#host-configuration)，但不用于用户配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="99ce0-192">有关主机和应用配置的详细信息，请参阅 [.NET 通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="99ce0-193">在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)上，选择“设置”>“配置”页面上的“新应用程序设置” 。</span><span class="sxs-lookup"><span data-stu-id="99ce0-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="99ce0-194">Azure 应用服务应用程序设置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="99ce0-195">已静态加密且通过加密的通道进行传输。</span><span class="sxs-lookup"><span data-stu-id="99ce0-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="99ce0-196">已作为环境变量公开。</span><span class="sxs-lookup"><span data-stu-id="99ce0-196">Exposed as environment variables.</span></span>

<span data-ttu-id="99ce0-197">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="99ce0-198">有关 Azure 数据库连接字符串的信息，请参阅[连接字符串前缀](#constr)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="naming-of-environment-variables"></a><span data-ttu-id="99ce0-199">环境变量的命名</span><span class="sxs-lookup"><span data-stu-id="99ce0-199">Naming of environment variables</span></span>

<span data-ttu-id="99ce0-200">环境变量名称反映了 appsettings.json 文件的结构。</span><span class="sxs-lookup"><span data-stu-id="99ce0-200">Environment variable names reflect the structure of an *appsettings.json* file.</span></span> <span data-ttu-id="99ce0-201">层次结构中的每个元素由双下划线字符（更可取）或冒号分隔。</span><span class="sxs-lookup"><span data-stu-id="99ce0-201">Each element in the hierarchy is separated by a double underscore (preferable) or a colon.</span></span> <span data-ttu-id="99ce0-202">当元素结构包含数组时，应将数组索引视为此路径中的附加元素名称。</span><span class="sxs-lookup"><span data-stu-id="99ce0-202">When the element structure includes an array, the array index should be treated as an additional element name in this path.</span></span> <span data-ttu-id="99ce0-203">请考虑以下 appsettings.json 文件及其表示为环境变量的等效值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-203">Consider the following *appsettings.json* file and its equivalent values represented as environment variables.</span></span>

**appsettings.json**

```json
{
    "SmtpServer": "smtp.example.com",
    "Logging": [
        {
            "Name": "ToEmail",
            "Level": "Critical",
            "Args": {
                "FromAddress": "MySystem@example.com",
                "ToAddress": "SRE@example.com"
            }
        },
        {
            "Name": "ToConsole",
            "Level": "Information"
        }
    ]
}
```

<span data-ttu-id="99ce0-204">环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-204">**environment variables**</span></span>

```console
setx SmtpServer=smtp.example.com
setx Logging__0__Name=ToEmail
setx Logging__0__Level=Critical
setx Logging__0__Args__FromAddress=MySystem@example.com
setx Logging__0__Args__ToAddress=SRE@example.com
setx Logging__1__Name=ToConsole
setx Logging__1__Level=Information
```

### <a name="environment-variables-set-in-generated-launchsettingsjson"></a><span data-ttu-id="99ce0-205">在生成的 launchSettings.json 中设置的环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-205">Environment variables set in generated launchSettings.json</span></span>

<span data-ttu-id="99ce0-206">在 launchSettings.json 中设置的环境变量将替代在系统环境中设置的变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-206">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span> <span data-ttu-id="99ce0-207">例如，ASP.NET Core Web 模板会生成一个 launchSettings.json 文件，该文件将终结点配置设置为：</span><span class="sxs-lookup"><span data-stu-id="99ce0-207">For example, the ASP.NET Core web templates generate a *launchSettings.json* file that sets the endpoint configuration to:</span></span>

```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

<span data-ttu-id="99ce0-208">配置 `applicationUrl` 将设置 `ASPNETCORE_URLS` 环境变量并重写环境中设置的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-208">Configuring the `applicationUrl` sets the `ASPNETCORE_URLS` environment variable and overrides values set in the environment.</span></span>

### <a name="escape-environment-variables-on-linux"></a><span data-ttu-id="99ce0-209">在 Linux 上转义环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-209">Escape environment variables on Linux</span></span>

<span data-ttu-id="99ce0-210">在 Linux 上，必须转义 URL 环境变量的值，使 `systemd` 可以对其进行分析。</span><span class="sxs-lookup"><span data-stu-id="99ce0-210">On Linux, the value of URL environment variables must be escaped so `systemd` can parse it.</span></span> <span data-ttu-id="99ce0-211">使用 Linux 工具 `systemd-escape` 生成 `http:--localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="99ce0-211">Use the linux tool `systemd-escape` which yields `http:--localhost:5001`</span></span>
 
 ```cmd
 groot@terminus:~$ systemd-escape http://localhost:5001
 http:--localhost:5001
 ```

### <a name="display-environment-variables"></a><span data-ttu-id="99ce0-212">显示环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-212">Display environment variables</span></span>

<span data-ttu-id="99ce0-213">下面的代码显示了应用程序启动时的环境变量和值，这对调试环境设置很有帮助：</span><span class="sxs-lookup"><span data-stu-id="99ce0-213">The following code displays the environment variables and values on application startup, which can be helpful when debugging environment settings:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples_snippets/5.x/Program.cs?name=snippet)]

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="99ce0-214">命令行</span><span class="sxs-lookup"><span data-stu-id="99ce0-214">Command-line</span></span>

<span data-ttu-id="99ce0-215">使用[默认](#default)配置，<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 会从以下配置源后的命令行参数键值对中加载配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-215">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="99ce0-216">appsettings.json 和 appsettings.`Environment`.json 文件  。</span><span class="sxs-lookup"><span data-stu-id="99ce0-216">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="99ce0-217">开发环境中的[应用机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-217">[App secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="99ce0-218">环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-218">Environment variables.</span></span>

<span data-ttu-id="99ce0-219">[默认情况下](#default)，在命令行上设置的配置值会替代通过所有其他配置提供程序设置的配置值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-219">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="99ce0-220">命令行参数</span><span class="sxs-lookup"><span data-stu-id="99ce0-220">Command-line arguments</span></span>

<span data-ttu-id="99ce0-221">以下命令使用 `=` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-221">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="99ce0-222">以下命令使用 `/` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-222">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="99ce0-223">以下命令使用 `--` 设置键和值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-223">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="99ce0-224">键值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-224">The key value:</span></span>

* <span data-ttu-id="99ce0-225">必须后跟 `=`，或者当值后跟一个空格时，键必须具有一个 `--` 或 `/` 的前缀。</span><span class="sxs-lookup"><span data-stu-id="99ce0-225">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="99ce0-226">如果使用 `=`，则不是必需的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-226">Isn't required if `=` is used.</span></span> <span data-ttu-id="99ce0-227">例如 `MySetting=`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-227">For example, `MySetting=`.</span></span>

<span data-ttu-id="99ce0-228">在同一命令中，请勿将使用 `=` 的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-228">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="99ce0-229">交换映射</span><span class="sxs-lookup"><span data-stu-id="99ce0-229">Switch mappings</span></span>

<span data-ttu-id="99ce0-230">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="99ce0-230">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="99ce0-231">提供针对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法的交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="99ce0-231">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="99ce0-232">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-232">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="99ce0-233">如果在字典中找到了命令行键，则会传回字典值将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-233">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="99ce0-234">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-234">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="99ce0-235">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="99ce0-235">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="99ce0-236">交换必须以 `-` 或 `--` 开头。</span><span class="sxs-lookup"><span data-stu-id="99ce0-236">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="99ce0-237">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-237">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="99ce0-238">若要使用交换映射字典，请将它传递到对 `AddCommandLine` 的调用中：</span><span class="sxs-lookup"><span data-stu-id="99ce0-238">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="99ce0-239">下面的代码显示了替换后的键的键值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-239">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-240">以下命令可用于测试键替换：</span><span class="sxs-lookup"><span data-stu-id="99ce0-240">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="99ce0-241">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="99ce0-241">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="99ce0-242">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-242">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="99ce0-243">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="99ce0-243">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="99ce0-244">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="99ce0-244">Hierarchical configuration data</span></span>

<span data-ttu-id="99ce0-245">配置 API 在配置键中使用分隔符来展平分层数据，以此来读取分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="99ce0-245">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="99ce0-246">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-246">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="99ce0-247">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-247">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-248">读取分层配置数据的首选方法是使用选项模式。</span><span class="sxs-lookup"><span data-stu-id="99ce0-248">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="99ce0-249">有关详细信息，请参阅本文档中的[绑定分层配置数据](#optpat)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-249">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="99ce0-250"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="99ce0-250"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="99ce0-251">稍后将在 [GetSection、GetChildren 和 Exists](#getsection) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-251">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="99ce0-252">配置键和值</span><span class="sxs-lookup"><span data-stu-id="99ce0-252">Configuration keys and values</span></span>

<span data-ttu-id="99ce0-253">配置键：</span><span class="sxs-lookup"><span data-stu-id="99ce0-253">Configuration keys:</span></span>

* <span data-ttu-id="99ce0-254">不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="99ce0-254">Are case-insensitive.</span></span> <span data-ttu-id="99ce0-255">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-255">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="99ce0-256">如果在多个配置提供程序中设置了某一键和值，则会使用最后添加的提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-256">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="99ce0-257">有关详细信息，请参阅[默认配置](#default)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-257">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="99ce0-258">分层键</span><span class="sxs-lookup"><span data-stu-id="99ce0-258">Hierarchical keys</span></span>
  * <span data-ttu-id="99ce0-259">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="99ce0-259">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="99ce0-260">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="99ce0-260">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="99ce0-261">所有平台均支持采用双下划线 `__`，并且它会自动转换为冒号 `:`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-261">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="99ce0-262">在 Azure Key Vault 中，分层键使用 `--` 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="99ce0-262">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="99ce0-263">当机密加载到应用的配置中时，[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration) 会自动将 `--` 替换为 `:`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-263">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="99ce0-264"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="99ce0-264">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="99ce0-265">数组绑定将在[将数组绑定到类](#boa)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="99ce0-265">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="99ce0-266">配置值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-266">Configuration values:</span></span>

* <span data-ttu-id="99ce0-267">为字符串。</span><span class="sxs-lookup"><span data-stu-id="99ce0-267">Are strings.</span></span>
* <span data-ttu-id="99ce0-268">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="99ce0-268">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="99ce0-269">配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-269">Configuration providers</span></span>

<span data-ttu-id="99ce0-270">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-270">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="99ce0-271">提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-271">Provider</span></span> | <span data-ttu-id="99ce0-272">通过以下对象提供配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-272">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="99ce0-273">Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-273">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="99ce0-274">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="99ce0-274">Azure Key Vault</span></span> |
| [<span data-ttu-id="99ce0-275">Azure 应用配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-275">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="99ce0-276">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-276">Azure App Configuration</span></span> |
| [<span data-ttu-id="99ce0-277">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-277">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="99ce0-278">命令行参数</span><span class="sxs-lookup"><span data-stu-id="99ce0-278">Command-line parameters</span></span> |
| [<span data-ttu-id="99ce0-279">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-279">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="99ce0-280">自定义源</span><span class="sxs-lookup"><span data-stu-id="99ce0-280">Custom source</span></span> |
| [<span data-ttu-id="99ce0-281">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-281">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="99ce0-282">环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-282">Environment variables</span></span> |
| [<span data-ttu-id="99ce0-283">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-283">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="99ce0-284">INI、JSON 和 XML 文件</span><span class="sxs-lookup"><span data-stu-id="99ce0-284">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="99ce0-285">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-285">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="99ce0-286">目录文件</span><span class="sxs-lookup"><span data-stu-id="99ce0-286">Directory files</span></span> |
| [<span data-ttu-id="99ce0-287">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-287">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="99ce0-288">内存中集合</span><span class="sxs-lookup"><span data-stu-id="99ce0-288">In-memory collections</span></span> |
| [<span data-ttu-id="99ce0-289">用户机密</span><span class="sxs-lookup"><span data-stu-id="99ce0-289">User secrets</span></span>](xref:security/app-secrets) | <span data-ttu-id="99ce0-290">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="99ce0-290">File in the user profile directory</span></span> |

<span data-ttu-id="99ce0-291">按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="99ce0-291">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="99ce0-292">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="99ce0-292">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="99ce0-293">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="99ce0-293">A typical sequence of configuration providers is:</span></span>

1. *appsettings.json*
1. <span data-ttu-id="99ce0-294">appsettings.`Environment`.json</span><span class="sxs-lookup"><span data-stu-id="99ce0-294">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="99ce0-295">用户机密</span><span class="sxs-lookup"><span data-stu-id="99ce0-295">User secrets</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="99ce0-296">使用[环境变量配置提供程序](#evcp)通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-296">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="99ce0-297">使用[命令行配置提供程序](#command-line-configuration-provider)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-297">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="99ce0-298">通常的做法是将命令行配置提供程序添加到一系列提供程序的末尾，使命令行参数能够替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-298">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="99ce0-299">[默认配置](#default)中使用了上述提供程序顺序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-299">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="99ce0-300">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="99ce0-300">Connection string prefixes</span></span>

<span data-ttu-id="99ce0-301">对于四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="99ce0-301">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="99ce0-302">这些连接字符串涉及了为应用环境配置 Azure 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="99ce0-302">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="99ce0-303">使用[默认配置](#default)或没有向 `AddEnvironmentVariables` 应用前缀时，具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="99ce0-303">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="99ce0-304">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="99ce0-304">Connection string prefix</span></span> | <span data-ttu-id="99ce0-305">提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-305">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="99ce0-306">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-306">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="99ce0-307">MySQL</span><span class="sxs-lookup"><span data-stu-id="99ce0-307">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="99ce0-308">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="99ce0-308">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="99ce0-309">SQL Server</span><span class="sxs-lookup"><span data-stu-id="99ce0-309">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="99ce0-310">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="99ce0-310">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="99ce0-311">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-311">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="99ce0-312">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="99ce0-312">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="99ce0-313">环境变量键</span><span class="sxs-lookup"><span data-stu-id="99ce0-313">Environment variable key</span></span> | <span data-ttu-id="99ce0-314">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="99ce0-314">Converted configuration key</span></span> | <span data-ttu-id="99ce0-315">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="99ce0-315">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="99ce0-316">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="99ce0-316">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="99ce0-317">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-317">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="99ce0-318">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="99ce0-318">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="99ce0-319">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-319">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="99ce0-320">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="99ce0-320">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="99ce0-321">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-321">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="99ce0-322">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="99ce0-322">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="99ce0-323">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-323">File configuration provider</span></span>

<span data-ttu-id="99ce0-324"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="99ce0-324"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="99ce0-325">以下配置提供程序派生自 `FileConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-325">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="99ce0-326">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-326">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="99ce0-327">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-327">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="99ce0-328">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-328">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="99ce0-329">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-329">INI configuration provider</span></span>

<span data-ttu-id="99ce0-330"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-330">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="99ce0-331">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="99ce0-331">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="99ce0-332">在前面的代码中，MyIniConfig.ini 和 MyIniConfig.`Environment`.ini 文件中的设置会被以下提供程序中的设置替代  ：</span><span class="sxs-lookup"><span data-stu-id="99ce0-332">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="99ce0-333">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-333">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="99ce0-334">[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-334">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="99ce0-335">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyIniConfig.ini 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-335">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="99ce0-336">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-336">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="99ce0-337">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-337">JSON configuration provider</span></span>

<span data-ttu-id="99ce0-338"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-338">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="99ce0-339">重载可以指定：</span><span class="sxs-lookup"><span data-stu-id="99ce0-339">Overloads can specify:</span></span>

* <span data-ttu-id="99ce0-340">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="99ce0-340">Whether the file is optional.</span></span>
* <span data-ttu-id="99ce0-341">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-341">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="99ce0-342">考虑下列代码：</span><span class="sxs-lookup"><span data-stu-id="99ce0-342">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="99ce0-343">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="99ce0-343">The preceding code:</span></span>

* <span data-ttu-id="99ce0-344">通过以下选项将 JSON 配置提供程序配置为加载 MyConfig.json 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-344">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="99ce0-345">`optional: true`：文件是可选的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-345">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="99ce0-346">`reloadOnChange: true`：保存更改后会重载文件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-346">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="99ce0-347">读取 MyConfig.json 文件之前的[默认配置提供程序](#default)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-347">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="99ce0-348">MyConfig.json 文件中的设置会替代默认配置提供程序中的设置，包括[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-348">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="99ce0-349">通常情况下，你不会希望自定义 JSON 文件替代在[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)中设置的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-349">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="99ce0-350">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="99ce0-350">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="99ce0-351">在前面的代码中，MyConfig.json 和 MyConfig.`Environment`.json 文件中的设置  ：</span><span class="sxs-lookup"><span data-stu-id="99ce0-351">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="99ce0-352">会替代 appsettings.json 和 appsettings.`Environment`.json 文件中的设置  。</span><span class="sxs-lookup"><span data-stu-id="99ce0-352">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="99ce0-353">会被[环境变量配置提供程序](#evcp)和[命令行配置提供程序](#clcp)中的设置所替代。</span><span class="sxs-lookup"><span data-stu-id="99ce0-353">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="99ce0-354">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyConfig.json 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-354">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="99ce0-355">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-355">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="99ce0-356">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-356">XML configuration provider</span></span>

<span data-ttu-id="99ce0-357"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-357">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="99ce0-358">以下代码会清除所有配置提供程序并添加多个配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="99ce0-358">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="99ce0-359">在前面的代码中，MyXMLFile.xml 和 MyXMLFile.`Environment`.xml 文件中的设置会被以下提供程序中的设置替代  ：</span><span class="sxs-lookup"><span data-stu-id="99ce0-359">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="99ce0-360">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-360">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="99ce0-361">[命令行配置提供程序](#clcp)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-361">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="99ce0-362">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)包含以下 MyXMLFile.xml 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-362">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="99ce0-363">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述的一些配置设置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-363">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-364">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="99ce0-364">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="99ce0-365">以下代码会读取前面的配置文件并显示键和值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-365">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-366">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-366">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="99ce0-367">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="99ce0-367">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="99ce0-368">key:attribute</span><span class="sxs-lookup"><span data-stu-id="99ce0-368">key:attribute</span></span>
* <span data-ttu-id="99ce0-369">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="99ce0-369">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="99ce0-370">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-370">Key-per-file configuration provider</span></span>

<span data-ttu-id="99ce0-371"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-371">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="99ce0-372">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="99ce0-372">The key is the file name.</span></span> <span data-ttu-id="99ce0-373">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="99ce0-373">The value contains the file's contents.</span></span> <span data-ttu-id="99ce0-374">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="99ce0-374">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="99ce0-375">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-375">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="99ce0-376">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="99ce0-376">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="99ce0-377">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="99ce0-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="99ce0-378">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="99ce0-378">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="99ce0-379">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="99ce0-379">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="99ce0-380">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="99ce0-380">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="99ce0-381">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-381">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="99ce0-382">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-382">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="99ce0-383">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-383">Memory configuration provider</span></span>

<span data-ttu-id="99ce0-384"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-384">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="99ce0-385">以下代码将内存集合添加到配置系统中：</span><span class="sxs-lookup"><span data-stu-id="99ce0-385">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="99ce0-386">以下来自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)的代码显示了上述配置设置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-386">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-387">在前面的代码中，`config.AddInMemoryCollection(Dict)` 会被添加到[默认配置提供程序](#default)之后。</span><span class="sxs-lookup"><span data-stu-id="99ce0-387">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="99ce0-388">有关对配置提供程序进行排序的示例，请参阅 [JSON 配置提供程序](#jcp)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-388">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="99ce0-389">有关使用 `MemoryConfigurationProvider` 的其他示例，请参阅[绑定数组](#boa)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-389">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-5.0"

<a name="kestrel"></a>

## <a name="kestrel-endpoint-configuration"></a><span data-ttu-id="99ce0-390">Kestrel 终结点配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-390">Kestrel endpoint configuration</span></span>

<span data-ttu-id="99ce0-391">Kestrel 特定的终结点配置将覆盖所有[跨服务器](xref:fundamentals/servers/index)终结点配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-391">Kestrel specific endpoint configuration overrides all [cross-server](xref:fundamentals/servers/index) endpoint configurations.</span></span> <span data-ttu-id="99ce0-392">跨服务器终结点配置包括：</span><span class="sxs-lookup"><span data-stu-id="99ce0-392">Cross-server endpoint configurations include:</span></span>

  * [<span data-ttu-id="99ce0-393">UseUrls</span><span class="sxs-lookup"><span data-stu-id="99ce0-393">UseUrls</span></span>](xref:fundamentals/host/web-host#server-urls)
  * <span data-ttu-id="99ce0-394">[命令行](xref:fundamentals/configuration/index#command-line)上的 `--urls`</span><span class="sxs-lookup"><span data-stu-id="99ce0-394">`--urls` on the [command line](xref:fundamentals/configuration/index#command-line)</span></span>
  * <span data-ttu-id="99ce0-395">[环境变量](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="99ce0-395">The [environment variable](xref:fundamentals/configuration/index#environment-variables) `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="99ce0-396">请考虑在 ASP.NET Core Web 应用中使用的以下 appsettings.json 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-396">Consider the following *appsettings.json* file used in an ASP.NET Core web app:</span></span>

[!code-json[](~/fundamentals/configuration/index/samples_snippets/5.x/appsettings.json?highlight=2-8)]

<span data-ttu-id="99ce0-397">当在 ASP.NET Core Web 应用中使用前面突出显示的标记，并且应用在命令行上启动，且跨服务器端点配置如下时：</span><span class="sxs-lookup"><span data-stu-id="99ce0-397">When the preceding highlighted markup is used in an ASP.NET Core web app ***and*** the app is launched on the command line with the following cross-server endpoint configuration:</span></span>

`dotnet run --urls="https://localhost:7777"`

<span data-ttu-id="99ce0-398">Kestrel 将绑定到专门针对 appsettings.json 文件中的 Kestrel 配置的终结点 (`https://localhost:9999`)，而不是 `https://localhost:7777`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-398">Kestrel binds to the endpoint configured specifically for Kestrel in the *appsettings.json* file (`https://localhost:9999`) and not `https://localhost:7777`.</span></span>

<span data-ttu-id="99ce0-399">请考虑将 Kestrel 特定的终结点配置为环境变量：</span><span class="sxs-lookup"><span data-stu-id="99ce0-399">Consider the Kestrel specific endpoint configured as an environment variable:</span></span>

`set Kestrel__Endpoints__Https__Url=https://localhost:8888`

<span data-ttu-id="99ce0-400">在前面的环境变量中，`Https` 是 Kestrel 特定的终结点的名称。</span><span class="sxs-lookup"><span data-stu-id="99ce0-400">In the preceding environment variable, `Https` is the name of the Kestrel specific endpoint.</span></span> <span data-ttu-id="99ce0-401">前面的 appsettings.json 文件还定义了名为 `Https` 的 Kestrel 特定终结点。</span><span class="sxs-lookup"><span data-stu-id="99ce0-401">The preceding *appsettings.json* file also defines a Kestrel specific endpoint named `Https`.</span></span> <span data-ttu-id="99ce0-402">[默认](#default-configuration)情况下，将在 appsettings.`Environment`.json 后读取使用[环境变量配置提供程序](#evcp)的环境变量，因此，前面的环境变量用于 `Https` 终结点。</span><span class="sxs-lookup"><span data-stu-id="99ce0-402">By [default](#default-configuration), environment variables using the [Environment Variables configuration provider](#evcp) are read after *appsettings.*`Environment`*.json*, therefore, the preceding environment variable is used for the `Https` endpoint.</span></span>

::: moniker-end
::: moniker range=">= aspnetcore-3.0"

## <a name="getvalue"></a><span data-ttu-id="99ce0-403">GetValue</span><span class="sxs-lookup"><span data-stu-id="99ce0-403">GetValue</span></span>

<span data-ttu-id="99ce0-404">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将它转换为指定的类型：</span><span class="sxs-lookup"><span data-stu-id="99ce0-404">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-405">在前面的代码中，如果在配置中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-405">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="99ce0-406">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="99ce0-406">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="99ce0-407">对于下面的示例，请考虑以下 MySubsection.json 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-407">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="99ce0-408">以下代码将 MySubsection.json 添加到配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="99ce0-408">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="99ce0-409">GetSection</span><span class="sxs-lookup"><span data-stu-id="99ce0-409">GetSection</span></span>

<span data-ttu-id="99ce0-410">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 会返回具有指定子节键的配置子节。</span><span class="sxs-lookup"><span data-stu-id="99ce0-410">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="99ce0-411">以下代码将返回 `section1` 的值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-411">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-412">以下代码将返回 `section2:subsection0` 的值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-412">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-413">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-413">`GetSection` never returns `null`.</span></span> <span data-ttu-id="99ce0-414">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-414">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="99ce0-415">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="99ce0-415">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="99ce0-416">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="99ce0-416">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="99ce0-417">GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="99ce0-417">GetChildren and Exists</span></span>

<span data-ttu-id="99ce0-418">以下代码将调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 并返回 `section2:subsection0` 的值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-418">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-419">前面的代码将调用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 以验证该节是否存在：</span><span class="sxs-lookup"><span data-stu-id="99ce0-419">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="99ce0-420">绑定数组</span><span class="sxs-lookup"><span data-stu-id="99ce0-420">Bind an array</span></span>

<span data-ttu-id="99ce0-421">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="99ce0-421">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="99ce0-422">公开数值键段的任何数组格式都能够与 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="99ce0-422">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="99ce0-423">请考虑[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample)中的 MyArray.json：</span><span class="sxs-lookup"><span data-stu-id="99ce0-423">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="99ce0-424">以下代码将 MyArray.json 添加到配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="99ce0-424">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="99ce0-425">以下代码将读取配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-425">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-426">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="99ce0-426">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="99ce0-427">在前面的输出中，索引 3 具有值 `value40`，与 MyArray.json 中的 `"4": "value40",` 相对应。</span><span class="sxs-lookup"><span data-stu-id="99ce0-427">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="99ce0-428">绑定的数组索引是连续的，并且未绑定到配置键索引。</span><span class="sxs-lookup"><span data-stu-id="99ce0-428">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="99ce0-429">配置绑定器不能绑定 NULL 值，也不能在绑定的对象中创建 NULL 条目</span><span class="sxs-lookup"><span data-stu-id="99ce0-429">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="99ce0-430">以下代码将通过 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法加载 `array:entries` 配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-430">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="99ce0-431">以下代码将读取 `arrayDict` `Dictionary` 中的配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-431">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-432">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="99ce0-432">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="99ce0-433">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-433">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="99ce0-434">当绑定包含数组的配置数据时，配置键中的数组索引用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="99ce0-434">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="99ce0-435">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="99ce0-435">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="99ce0-436">可以在由任何读取索引 &num;3 键/值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="99ce0-436">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="99ce0-437">请考虑示例下载中的以下 Value3.json 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-437">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="99ce0-438">以下代码包含 Value3.json 和 `arrayDict` `Dictionary` 的配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-438">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="99ce0-439">以下代码将读取上述配置并显示值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-439">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-440">前面的代码会返回以下输出：</span><span class="sxs-lookup"><span data-stu-id="99ce0-440">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="99ce0-441">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="99ce0-441">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="99ce0-442">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-442">Custom configuration provider</span></span>

<span data-ttu-id="99ce0-443">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-443">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="99ce0-444">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="99ce0-444">The provider has the following characteristics:</span></span>

* <span data-ttu-id="99ce0-445">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-445">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="99ce0-446">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="99ce0-446">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="99ce0-447">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-447">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="99ce0-448">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="99ce0-448">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="99ce0-449">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="99ce0-449">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="99ce0-450">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="99ce0-450">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="99ce0-451">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="99ce0-451">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="99ce0-452">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-452">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="99ce0-453">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="99ce0-453">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="99ce0-454">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="99ce0-454">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="99ce0-455">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="99ce0-455">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="99ce0-456">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-456">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="99ce0-457">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="99ce0-457">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="99ce0-458">由于[配置密钥不区分大小写](#keys)，因此用来初始化数据库的字典是用不区分大小写的比较程序 ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)) 创建的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-458">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="99ce0-459">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="99ce0-459">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="99ce0-460">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-460">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="99ce0-461">Extensions/EntityFrameworkExtensions.cs：</span><span class="sxs-lookup"><span data-stu-id="99ce0-461">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="99ce0-462">下面的代码演示如何在 Program.cs 中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-462">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="99ce0-463">访问 Startup 中的配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-463">Access configuration in Startup</span></span>

<span data-ttu-id="99ce0-464">以下代码显示 `Startup` 方法中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="99ce0-464">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="99ce0-465">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-465">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="99ce0-466">访问 Razor Pages 中的配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-466">Access configuration in Razor Pages</span></span>

<span data-ttu-id="99ce0-467">以下代码显示 Razor Pages 中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="99ce0-467">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="99ce0-468">在以下代码中，`MyOptions` 已通过 <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> 被添加到了服务容器并已绑定到了配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-468">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="99ce0-469">以下标记使用 [`@inject`](xref:mvc/views/razor#inject) Razor 指令来解析和显示选项值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-469">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="99ce0-470">访问 MVC 视图文件中的配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-470">Access configuration in a MVC view file</span></span>

<span data-ttu-id="99ce0-471">以下代码显示 MVC 视图中的配置数据：</span><span class="sxs-lookup"><span data-stu-id="99ce0-471">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="99ce0-472">使用委托来配置选项</span><span class="sxs-lookup"><span data-stu-id="99ce0-472">Configure options with a delegate</span></span>

<span data-ttu-id="99ce0-473">在委托中配置的选项替代在配置提供程序中设置的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-473">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="99ce0-474">示例应用中的示例 2 展示了如何使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="99ce0-474">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="99ce0-475">在以下代码中，向服务容器添加了 <xref:Microsoft.Extensions.Options.IConfigureOptions%601> 服务。</span><span class="sxs-lookup"><span data-stu-id="99ce0-475">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="99ce0-476">它使用委托来配置 `MyOptions` 的值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-476">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="99ce0-477">以下代码显示选项值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-477">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="99ce0-478">在前面的示例中，`Option1` 和 `Option2` 的值在 appsettings.json 中指定，然后被配置的委托替代。</span><span class="sxs-lookup"><span data-stu-id="99ce0-478">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="99ce0-479">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-479">Host versus app configuration</span></span>

<span data-ttu-id="99ce0-480">在配置并启动应用之前，配置并启动主机。</span><span class="sxs-lookup"><span data-stu-id="99ce0-480">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="99ce0-481">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="99ce0-481">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="99ce0-482">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-482">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="99ce0-483">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-483">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="99ce0-484">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-484">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="99ce0-485">默认主机配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-485">Default host configuration</span></span>

<span data-ttu-id="99ce0-486">有关使用 [Web 主机](xref:fundamentals/host/web-host)时默认配置的详细信息，请参阅[本主题的 ASP.NET Core 2.2 版本](?view=aspnetcore-2.2&preserve-view=true)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-486">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](?view=aspnetcore-2.2&preserve-view=true).</span></span>

* <span data-ttu-id="99ce0-487">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="99ce0-487">Host configuration is provided from:</span></span>
  * <span data-ttu-id="99ce0-488">使用[环境变量配置提供程序](#environment-variables)通过前缀为 `DOTNET_`的环境变量（例如，`DOTNET_ENVIRONMENT`）提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-488">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="99ce0-489">在配置键值对加载后，前缀 (`DOTNET_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="99ce0-489">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="99ce0-490">使用[命令行配置提供程序](#command-line-configuration-provider)通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-490">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="99ce0-491">已建立 Web 主机默认配置 (`ConfigureWebHostDefaults`)：</span><span class="sxs-lookup"><span data-stu-id="99ce0-491">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="99ce0-492">Kestrel 用作 Web 服务器，并使用应用的配置提供程序对其进行配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-492">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="99ce0-493">添加主机筛选中间件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-493">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="99ce0-494">如果 `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 环境变量设置为 `true`，则添加转发的标头中间件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-494">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="99ce0-495">启用 IIS 集成。</span><span class="sxs-lookup"><span data-stu-id="99ce0-495">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="99ce0-496">其他配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-496">Other configuration</span></span>

<span data-ttu-id="99ce0-497">本主题仅与应用配置相关。</span><span class="sxs-lookup"><span data-stu-id="99ce0-497">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="99ce0-498">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-498">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="99ce0-499">launch.json/launchSettings.json 是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="99ce0-499">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="99ce0-500"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="99ce0-500">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="99ce0-501">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-501">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="99ce0-502">web.config 是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="99ce0-502">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="99ce0-503">在 launchSettings.json 中设置的环境变量将替代在系统环境中设置的变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-503">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="99ce0-504">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-504">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="99ce0-505">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-505">Add configuration from an external assembly</span></span>

<span data-ttu-id="99ce0-506">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="99ce0-506">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="99ce0-507">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-507">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99ce0-508">其他资源</span><span class="sxs-lookup"><span data-stu-id="99ce0-508">Additional resources</span></span>

* [<span data-ttu-id="99ce0-509">配置源代码</span><span class="sxs-lookup"><span data-stu-id="99ce0-509">Configuration source code</span></span>](https://github.com/dotnet/runtime/tree/main/src/libraries/Microsoft.Extensions.Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="99ce0-510">ASP.NET Core 中的应用配置基于配置提供程序建立的键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-510">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="99ce0-511">配置提供程序将配置数据从各种配置源读取到键值对：</span><span class="sxs-lookup"><span data-stu-id="99ce0-511">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="99ce0-512">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="99ce0-512">Azure Key Vault</span></span>
* <span data-ttu-id="99ce0-513">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-513">Azure App Configuration</span></span>
* <span data-ttu-id="99ce0-514">命令行参数</span><span class="sxs-lookup"><span data-stu-id="99ce0-514">Command-line arguments</span></span>
* <span data-ttu-id="99ce0-515">（已安装或已创建的）自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-515">Custom providers (installed or created)</span></span>
* <span data-ttu-id="99ce0-516">目录文件</span><span class="sxs-lookup"><span data-stu-id="99ce0-516">Directory files</span></span>
* <span data-ttu-id="99ce0-517">环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-517">Environment variables</span></span>
* <span data-ttu-id="99ce0-518">内存中的 .NET 对象</span><span class="sxs-lookup"><span data-stu-id="99ce0-518">In-memory .NET objects</span></span>
* <span data-ttu-id="99ce0-519">设置文件</span><span class="sxs-lookup"><span data-stu-id="99ce0-519">Settings files</span></span>

<span data-ttu-id="99ce0-520">[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含通用配置提供程序方案的配置包 ([Microsoft Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))。</span><span class="sxs-lookup"><span data-stu-id="99ce0-520">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="99ce0-521">后面的代码示例和示例应用中的代码示例使用 <xref:Microsoft.Extensions.Configuration> 命名空间：</span><span class="sxs-lookup"><span data-stu-id="99ce0-521">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="99ce0-522">选项模式是本主题中描述的配置概念的扩展。</span><span class="sxs-lookup"><span data-stu-id="99ce0-522">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="99ce0-523">选项使用类来表示相关设置的组。</span><span class="sxs-lookup"><span data-stu-id="99ce0-523">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="99ce0-524">有关详细信息，请参阅 <xref:fundamentals/configuration/options>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-524">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="99ce0-525">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="99ce0-525">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="99ce0-526">主机与应用配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-526">Host versus app configuration</span></span>

<span data-ttu-id="99ce0-527">在配置并启动应用之前，配置并启动主机。</span><span class="sxs-lookup"><span data-stu-id="99ce0-527">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="99ce0-528">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="99ce0-528">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="99ce0-529">应用和主机均使用本主题中所述的配置提供程序进行配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-529">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="99ce0-530">应用的配置中也包含主机配置键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-530">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="99ce0-531">有关在构建主机时如何使用配置提供程序以及配置源如何影响主机配置的详细信息，请参阅 <xref:fundamentals/index#host>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-531">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="99ce0-532">其他配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-532">Other configuration</span></span>

<span data-ttu-id="99ce0-533">本主题仅与应用配置相关。</span><span class="sxs-lookup"><span data-stu-id="99ce0-533">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="99ce0-534">运行和托管 ASP.NET Core 应用的其他方面是使用本主题中未包含的配置文件进行配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-534">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="99ce0-535">launch.json/launchSettings.json 是用于开发环境的工具配置文件，如</span><span class="sxs-lookup"><span data-stu-id="99ce0-535">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="99ce0-536"><xref:fundamentals/environments#development> 中所述。</span><span class="sxs-lookup"><span data-stu-id="99ce0-536">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="99ce0-537">整个文档集中的文件用于为开发方案配置 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-537">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="99ce0-538">web.config 是服务器配置文件，如以下主题中所述：</span><span class="sxs-lookup"><span data-stu-id="99ce0-538">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="99ce0-539">若要详细了解如何从旧版 ASP.NET 迁移应用配置，请参阅 <xref:migration/proper-to-2x/index#store-configurations>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-539">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="99ce0-540">默认配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-540">Default configuration</span></span>

<span data-ttu-id="99ce0-541">基于 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new)模板的 Web 应用在生成主机时会调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-541">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="99ce0-542">`CreateDefaultBuilder` 按照以下顺序为应用提供默认配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-542">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="99ce0-543">以下内容适用于使用 [Web 主机](xref:fundamentals/host/web-host)的应用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-543">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="99ce0-544">有关使用[通用主机](xref:fundamentals/host/generic-host)时默认配置的详细信息，请参阅[本主题的最新版本](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-544">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="99ce0-545">主机配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="99ce0-545">Host configuration is provided from:</span></span>
  * <span data-ttu-id="99ce0-546">使用[环境变量配置提供程序](#environment-variables-configuration-provider)，通过前缀为 `ASPNETCORE_`（例如，`ASPNETCORE_ENVIRONMENT`）的环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-546">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="99ce0-547">在配置键值对加载后，前缀 (`ASPNETCORE_`) 会遭去除。</span><span class="sxs-lookup"><span data-stu-id="99ce0-547">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="99ce0-548">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-548">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="99ce0-549">应用配置通过以下方式提供：</span><span class="sxs-lookup"><span data-stu-id="99ce0-549">App configuration is provided from:</span></span>
  * <span data-ttu-id="99ce0-550">使用[文件配置提供程序](#file-configuration-provider)的 appsettings.json。</span><span class="sxs-lookup"><span data-stu-id="99ce0-550">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="99ce0-551">使用[文件配置提供程序](#file-configuration-provider)，通过 appsettings.{Environment}.json 提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-551">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="99ce0-552">应用在使用入口程序集的 `Development` 环境中运行时的[用户机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-552">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="99ce0-553">使用 [ 环境变量配置提供程序](#environment-variables-configuration-provider)，通过环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-553">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="99ce0-554">使用 [ 命令行配置提供程序](#command-line-configuration-provider)，通过命令行参数提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-554">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="99ce0-555">安全性</span><span class="sxs-lookup"><span data-stu-id="99ce0-555">Security</span></span>

<span data-ttu-id="99ce0-556">采用以下做法来保护敏感配置数据：</span><span class="sxs-lookup"><span data-stu-id="99ce0-556">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="99ce0-557">请勿在配置提供程序代码或纯文本配置文件中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="99ce0-557">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="99ce0-558">不要在开发或测试环境中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="99ce0-558">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="99ce0-559">请在项目外部指定机密，避免将其意外提交到源代码存储库。</span><span class="sxs-lookup"><span data-stu-id="99ce0-559">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="99ce0-560">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="99ce0-560">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="99ce0-561"><xref:security/app-secrets>：包含有关如何使用环境变量来存储敏感数据的建议。</span><span class="sxs-lookup"><span data-stu-id="99ce0-561"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="99ce0-562">机密管理器工具使用文件配置提供程序将用户机密存储在本地系统上的 JSON 文件中。</span><span class="sxs-lookup"><span data-stu-id="99ce0-562">The Secret Manager tool uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="99ce0-563">本主题后面将介绍文件配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-563">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="99ce0-564">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 安全存储 ASP.NET Core 应用的应用机密。</span><span class="sxs-lookup"><span data-stu-id="99ce0-564">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="99ce0-565">有关详细信息，请参阅 <xref:security/key-vault-configuration>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-565">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="99ce0-566">分层配置数据</span><span class="sxs-lookup"><span data-stu-id="99ce0-566">Hierarchical configuration data</span></span>

<span data-ttu-id="99ce0-567">配置 API 能够通过在配置键中使用分隔符来展平分层数据以保持分层配置数据。</span><span class="sxs-lookup"><span data-stu-id="99ce0-567">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="99ce0-568">在以下 JSON 文件中，两个节的结构化层次结构中存在四个键：</span><span class="sxs-lookup"><span data-stu-id="99ce0-568">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="99ce0-569">将文件读入配置时，将创建唯一键以保持配置源的原始分层数据结构。</span><span class="sxs-lookup"><span data-stu-id="99ce0-569">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="99ce0-570">使用冒号 (`:`) 展平节和键以保持原始结构：</span><span class="sxs-lookup"><span data-stu-id="99ce0-570">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="99ce0-571">section0:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-571">section0:key0</span></span>
* <span data-ttu-id="99ce0-572">section0:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-572">section0:key1</span></span>
* <span data-ttu-id="99ce0-573">section1:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-573">section1:key0</span></span>
* <span data-ttu-id="99ce0-574">section1:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-574">section1:key1</span></span>

<span data-ttu-id="99ce0-575"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 和 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 方法可用于隔离各个节和配置数据中某节的子节。</span><span class="sxs-lookup"><span data-stu-id="99ce0-575"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="99ce0-576">稍后将在 [GetSection、GetChildren 和 Exists](#getsection-getchildren-and-exists) 中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-576">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="99ce0-577">约定</span><span class="sxs-lookup"><span data-stu-id="99ce0-577">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="99ce0-578">源和提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-578">Sources and providers</span></span>

<span data-ttu-id="99ce0-579">在应用启动时，将按照指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="99ce0-579">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="99ce0-580">实现更改检测的配置提供程序能够在基础设置更改时重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-580">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="99ce0-581">例如，文件配置提供程序（本主题后面将对此进行介绍）和 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)实现更改检测。</span><span class="sxs-lookup"><span data-stu-id="99ce0-581">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="99ce0-582">应用的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 容器中提供了 <xref:Microsoft.Extensions.Configuration.IConfiguration>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-582"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="99ce0-583"><xref:Microsoft.Extensions.Configuration.IConfiguration> 可注入到 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 或 MVC <xref:Microsoft.AspNetCore.Mvc.Controller> 中，以获取类的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-583"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="99ce0-584">在下面的示例中，使用 `_config` 字段来访问配置值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-584">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="99ce0-585">配置提供程序不能使用 DI，因为主机在设置这些提供程序时 DI 不可用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-585">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="99ce0-586">键</span><span class="sxs-lookup"><span data-stu-id="99ce0-586">Keys</span></span>

<span data-ttu-id="99ce0-587">配置键采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="99ce0-587">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="99ce0-588">键不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="99ce0-588">Keys are case-insensitive.</span></span> <span data-ttu-id="99ce0-589">例如，`ConnectionString` 和 `connectionstring` 被视为等效键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-589">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="99ce0-590">如果由相同或不同的配置提供程序设置相同键的值，则键上设置的最后一个值就是所使用的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-590">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="99ce0-591">要详细了解重复的 JSON 密钥，请参阅[此 GitHub 问题](https://github.com/dotnet/extensions/issues/2381)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-591">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="99ce0-592">分层键</span><span class="sxs-lookup"><span data-stu-id="99ce0-592">Hierarchical keys</span></span>
  * <span data-ttu-id="99ce0-593">在配置 API 中，冒号分隔符 (`:`) 适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="99ce0-593">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="99ce0-594">在环境变量中，冒号分隔符可能无法适用于所有平台。</span><span class="sxs-lookup"><span data-stu-id="99ce0-594">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="99ce0-595">所有平台均支持采用双下划线 (`__`)，并可以将其自动转换为冒号。</span><span class="sxs-lookup"><span data-stu-id="99ce0-595">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="99ce0-596">在 Azure Key Vault 中，分层键使用 `--`（两个破折号）作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="99ce0-596">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="99ce0-597">将机密加载到应用的配置中时，用冒号替换破折号。</span><span class="sxs-lookup"><span data-stu-id="99ce0-597">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="99ce0-598"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="99ce0-598">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="99ce0-599">数组绑定将在[将数组绑定到类](#bind-an-array-to-a-class)部分中进行介绍。</span><span class="sxs-lookup"><span data-stu-id="99ce0-599">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="99ce0-600">值</span><span class="sxs-lookup"><span data-stu-id="99ce0-600">Values</span></span>

<span data-ttu-id="99ce0-601">配置值采用以下约定：</span><span class="sxs-lookup"><span data-stu-id="99ce0-601">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="99ce0-602">值是字符串。</span><span class="sxs-lookup"><span data-stu-id="99ce0-602">Values are strings.</span></span>
* <span data-ttu-id="99ce0-603">NULL 值不能存储在配置中或绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="99ce0-603">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="99ce0-604">提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-604">Providers</span></span>

<span data-ttu-id="99ce0-605">下表显示了 ASP.NET Core 应用可用的配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-605">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="99ce0-606">提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-606">Provider</span></span> | <span data-ttu-id="99ce0-607">通过以下对象提供配置&hellip;</span><span class="sxs-lookup"><span data-stu-id="99ce0-607">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="99ce0-608">[Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="99ce0-608">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="99ce0-609">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="99ce0-609">Azure Key Vault</span></span> |
| <span data-ttu-id="99ce0-610">[Azure 应用程序配置提供程序](/azure/azure-app-configuration/quickstart-aspnet-core-app)（Azure 文档）</span><span class="sxs-lookup"><span data-stu-id="99ce0-610">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="99ce0-611">Azure 应用程序配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-611">Azure App Configuration</span></span> |
| [<span data-ttu-id="99ce0-612">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-612">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="99ce0-613">命令行参数</span><span class="sxs-lookup"><span data-stu-id="99ce0-613">Command-line parameters</span></span> |
| [<span data-ttu-id="99ce0-614">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-614">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="99ce0-615">自定义源</span><span class="sxs-lookup"><span data-stu-id="99ce0-615">Custom source</span></span> |
| [<span data-ttu-id="99ce0-616">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-616">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="99ce0-617">环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-617">Environment variables</span></span> |
| [<span data-ttu-id="99ce0-618">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-618">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="99ce0-619">文件（INI、JSON、XML）</span><span class="sxs-lookup"><span data-stu-id="99ce0-619">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="99ce0-620">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-620">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="99ce0-621">目录文件</span><span class="sxs-lookup"><span data-stu-id="99ce0-621">Directory files</span></span> |
| [<span data-ttu-id="99ce0-622">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-622">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="99ce0-623">内存中集合</span><span class="sxs-lookup"><span data-stu-id="99ce0-623">In-memory collections</span></span> |
| <span data-ttu-id="99ce0-624">[用户机密](xref:security/app-secrets)（安全主题）</span><span class="sxs-lookup"><span data-stu-id="99ce0-624">[User secrets](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="99ce0-625">用户配置文件目录中的文件</span><span class="sxs-lookup"><span data-stu-id="99ce0-625">File in the user profile directory</span></span> |

<span data-ttu-id="99ce0-626">按照启动时指定的配置提供程序的顺序读取配置源。</span><span class="sxs-lookup"><span data-stu-id="99ce0-626">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="99ce0-627">本主题中所述的配置提供程序按字母顺序进行介绍，而不是按代码排列顺序进行介绍。</span><span class="sxs-lookup"><span data-stu-id="99ce0-627">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="99ce0-628">代码中的配置提供程序应以特定顺序排列，从而满足应用所需的基础配置源的优先级。</span><span class="sxs-lookup"><span data-stu-id="99ce0-628">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="99ce0-629">配置提供程序的典型顺序为：</span><span class="sxs-lookup"><span data-stu-id="99ce0-629">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="99ce0-630">文件（appsettings.json、appsettings.{Environment}.json，其中 `{Environment}` 是应用的当前托管环境） </span><span class="sxs-lookup"><span data-stu-id="99ce0-630">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="99ce0-631">Azure 密钥保管库</span><span class="sxs-lookup"><span data-stu-id="99ce0-631">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="99ce0-632">[用户机密](xref:security/app-secrets)（仅限开发环境）</span><span class="sxs-lookup"><span data-stu-id="99ce0-632">[User secrets](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="99ce0-633">环境变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-633">Environment variables</span></span>
1. <span data-ttu-id="99ce0-634">命令行参数</span><span class="sxs-lookup"><span data-stu-id="99ce0-634">Command-line arguments</span></span>

<span data-ttu-id="99ce0-635">通常的做法是将命令行配置提供程序置于一系列提供程序的末尾，以允许命令行参数替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-635">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="99ce0-636">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，将使用上述提供程序序列。</span><span class="sxs-lookup"><span data-stu-id="99ce0-636">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="99ce0-637">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="99ce0-637">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="99ce0-638">用 UseConfiguration 配置主机生成器</span><span class="sxs-lookup"><span data-stu-id="99ce0-638">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="99ce0-639">若要配置主机生成器，请使用配置在主机生成器上调用 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-639">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="99ce0-640">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="99ce0-640">ConfigureAppConfiguration</span></span>

<span data-ttu-id="99ce0-641">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置提供程序以及 `CreateDefaultBuilder` 自动添加的配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="99ce0-641">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="99ce0-642">用命令行参数替代以前的配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-642">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="99ce0-643">若要提供命令行参数可替代的应用配置，最后请调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-643">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="99ce0-644">删除 CreateDefaultBuilder 添加的提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-644">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="99ce0-645">要删除 `CreateDefaultBuilder` 添加的提供程序，请先对 [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) 调用 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)：</span><span class="sxs-lookup"><span data-stu-id="99ce0-645">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="99ce0-646">在应用启动期间使用配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-646">Consume configuration during app startup</span></span>

<span data-ttu-id="99ce0-647">在应用启动期间，可以使用 `ConfigureAppConfiguration` 中提供给应用的配置，包括 `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-647">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="99ce0-648">有关详细信息，请参阅[在启动期间访问配置](#access-configuration-during-startup)部分。</span><span class="sxs-lookup"><span data-stu-id="99ce0-648">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="99ce0-649">命令行配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-649">Command-line Configuration Provider</span></span>

<span data-ttu-id="99ce0-650"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> 在运行时从命令行参数键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-650">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="99ce0-651">要激活命令行配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-651">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="99ce0-652">调用 `CreateDefaultBuilder(string [])` 时会自动调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-652">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="99ce0-653">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="99ce0-653">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="99ce0-654">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="99ce0-654">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="99ce0-655">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置 。</span><span class="sxs-lookup"><span data-stu-id="99ce0-655">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="99ce0-656">开发环境中的[用户机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-656">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="99ce0-657">环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-657">Environment variables.</span></span>

<span data-ttu-id="99ce0-658">`CreateDefaultBuilder` 最后添加命令行配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-658">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="99ce0-659">在运行时传递的命令行参数会替代由其他提供程序设置的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-659">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="99ce0-660">`CreateDefaultBuilder` 在构造主机时起作用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-660">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="99ce0-661">因此，`CreateDefaultBuilder` 激活的命令行配置可能会影响主机的配置方式。</span><span class="sxs-lookup"><span data-stu-id="99ce0-661">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="99ce0-662">对于基于 ASP.NET Core 模板的应用，`CreateDefaultBuilder` 已调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-662">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="99ce0-663">若要添加其他配置提供程序并保持能够用命令行参数替代这些提供程序的配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并最后调用 `AddCommandLine`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-663">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="99ce0-664">**示例**</span><span class="sxs-lookup"><span data-stu-id="99ce0-664">**Example**</span></span>

<span data-ttu-id="99ce0-665">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-665">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="99ce0-666">在项目的目录中打开命令提示符。</span><span class="sxs-lookup"><span data-stu-id="99ce0-666">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="99ce0-667">为 `dotnet run` 命令提供命令行参数 `dotnet run CommandLineKey=CommandLineValue`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-667">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="99ce0-668">应用运行后，在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="99ce0-668">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="99ce0-669">观察输出是否包含提供给 `dotnet run` 的配置命令行参数的键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-669">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="99ce0-670">自变量</span><span class="sxs-lookup"><span data-stu-id="99ce0-670">Arguments</span></span>

<span data-ttu-id="99ce0-671">该值必须后跟一个等号 (`=`)，否则当值后跟一个空格时，键必须具有前缀（`--` 或 `/`）。</span><span class="sxs-lookup"><span data-stu-id="99ce0-671">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="99ce0-672">如果使用等号（例如 `CommandLineKey=`），则不需要该值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-672">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="99ce0-673">键前缀</span><span class="sxs-lookup"><span data-stu-id="99ce0-673">Key prefix</span></span>               | <span data-ttu-id="99ce0-674">示例</span><span class="sxs-lookup"><span data-stu-id="99ce0-674">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="99ce0-675">无前缀</span><span class="sxs-lookup"><span data-stu-id="99ce0-675">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="99ce0-676">双划线 (`--`)</span><span class="sxs-lookup"><span data-stu-id="99ce0-676">Two dashes (`--`)</span></span>        | <span data-ttu-id="99ce0-677">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="99ce0-677">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="99ce0-678">正斜杠 (`/`)</span><span class="sxs-lookup"><span data-stu-id="99ce0-678">Forward slash (`/`)</span></span>      | <span data-ttu-id="99ce0-679">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="99ce0-679">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="99ce0-680">在同一命令中，不要将使用等号的命令行参数键值对与使用空格的键值对混合使用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-680">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="99ce0-681">示例命令：</span><span class="sxs-lookup"><span data-stu-id="99ce0-681">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="99ce0-682">交换映射</span><span class="sxs-lookup"><span data-stu-id="99ce0-682">Switch mappings</span></span>

<span data-ttu-id="99ce0-683">交换映射支持键名替换逻辑。</span><span class="sxs-lookup"><span data-stu-id="99ce0-683">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="99ce0-684">使用 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 手动构建配置时，需要为 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 方法提供交换替换字典。</span><span class="sxs-lookup"><span data-stu-id="99ce0-684">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="99ce0-685">当使用交换映射字典时，会检查字典中是否有与命令行参数提供的键匹配的键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-685">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="99ce0-686">如果在字典中找到命令行键，则传回字典值（键替换）以将键值对设置为应用的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-686">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="99ce0-687">对任何具有单划线 (`-`) 前缀的命令行键而言，交换映射都是必需的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-687">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="99ce0-688">交换映射字典键规则：</span><span class="sxs-lookup"><span data-stu-id="99ce0-688">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="99ce0-689">交换必须以单划线 (`-`) 或双划线 (`--`) 开头。</span><span class="sxs-lookup"><span data-stu-id="99ce0-689">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="99ce0-690">交换映射字典不得包含重复键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-690">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="99ce0-691">创建交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="99ce0-691">Create a switch mappings dictionary.</span></span> <span data-ttu-id="99ce0-692">在以下示例中，创建了两个交换映射：</span><span class="sxs-lookup"><span data-stu-id="99ce0-692">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="99ce0-693">生成主机后，使用交换映射字典来调用 `AddCommandLine`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-693">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="99ce0-694">对于使用交换映射的应用，调用 `CreateDefaultBuilder` 不应传递参数。</span><span class="sxs-lookup"><span data-stu-id="99ce0-694">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="99ce0-695">`CreateDefaultBuilder` 方法的 `AddCommandLine` 调用不包括映射的交换，并且无法将交换映射字典传递给 `CreateDefaultBuilder`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-695">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="99ce0-696">解决方案不是将参数传递给 `CreateDefaultBuilder`，而是允许 `ConfigurationBuilder` 方法的 `AddCommandLine` 方法处理参数和交换映射字典。</span><span class="sxs-lookup"><span data-stu-id="99ce0-696">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="99ce0-697">创建交换映射字典后，它将包含下表所示的数据。</span><span class="sxs-lookup"><span data-stu-id="99ce0-697">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="99ce0-698">密钥</span><span class="sxs-lookup"><span data-stu-id="99ce0-698">Key</span></span>       | <span data-ttu-id="99ce0-699">“值”</span><span class="sxs-lookup"><span data-stu-id="99ce0-699">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="99ce0-700">如果在启动应用时使用了交换映射的键，则配置将接收字典提供的密钥上的配置值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-700">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="99ce0-701">运行上述命令后，配置包含下表中显示的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-701">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="99ce0-702">键</span><span class="sxs-lookup"><span data-stu-id="99ce0-702">Key</span></span>               | <span data-ttu-id="99ce0-703">“值”</span><span class="sxs-lookup"><span data-stu-id="99ce0-703">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="99ce0-704">环境变量配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-704">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="99ce0-705"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> 在运行时从环境变量键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-705">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="99ce0-706">要激活环境变量配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-706">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="99ce0-707">借助 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)，可在 Azure 门户中设置使用环境变量配置提供程序替代应用配置的环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-707">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="99ce0-708">有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-708">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="99ce0-709">如果使用 [Web 主机](xref:fundamentals/host/web-host)初始化新的主机生成器，且调用 `CreateDefaultBuilder`，则使用 `AddEnvironmentVariables` 为[主机配置](#host-versus-app-configuration)加载前缀为 `ASPNETCORE_` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-709">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="99ce0-710">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="99ce0-710">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="99ce0-711">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="99ce0-711">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="99ce0-712">来自没有前缀的环境变量的应用配置，方法是通过调用不带前缀的 `AddEnvironmentVariables`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-712">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="99ce0-713">appsettings.json 和 appsettings.{Environment}.json 文件中的可选配置 。</span><span class="sxs-lookup"><span data-stu-id="99ce0-713">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="99ce0-714">开发环境中的[用户机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-714">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="99ce0-715">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="99ce0-715">Command-line arguments.</span></span>

<span data-ttu-id="99ce0-716">环境变量配置提供程序是在配置已根据用户机密和 appsettings 文件建立后调用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-716">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="99ce0-717">在此位置调用提供程序允许在运行时读取的环境变量替代由用户机密和 appsettings 文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-717">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="99ce0-718">要从其他环境变量提供应用配置，请在 `ConfigureAppConfiguration` 中调用应用的其他提供程序，并使用前缀调用 `AddEnvironmentVariables`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-718">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="99ce0-719">最后调用 `AddEnvironmentVariables`让带给定前缀的环境变量可替代其他提供程序中的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-719">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="99ce0-720">**示例**</span><span class="sxs-lookup"><span data-stu-id="99ce0-720">**Example**</span></span>

<span data-ttu-id="99ce0-721">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括一个对 `AddEnvironmentVariables` 的调用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-721">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="99ce0-722">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-722">Run the sample app.</span></span> <span data-ttu-id="99ce0-723">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="99ce0-723">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="99ce0-724">观察输出是否包含环境变量 `ENVIRONMENT` 的键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-724">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="99ce0-725">该值反映了应用运行的环境，在本地运行时通常为 `Development`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-725">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="99ce0-726">为了缩短应用呈现的环境变量列表，应用会筛选环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-726">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="99ce0-727">请参阅示例应用的“Pages/Index.cshtml.cs”文件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-727">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="99ce0-728">要公开应用可用的所有环境变量，请将 Pages/Index.cshtml.cs 中的 `FilteredConfiguration` 更改为以下内容：</span><span class="sxs-lookup"><span data-stu-id="99ce0-728">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="99ce0-729">前缀</span><span class="sxs-lookup"><span data-stu-id="99ce0-729">Prefixes</span></span>

<span data-ttu-id="99ce0-730">为 `AddEnvironmentVariables` 方法提供前缀时，将筛选加载到应用的配置中的环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-730">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="99ce0-731">例如，要筛选前缀 `CUSTOM_` 上的环境变量，请将前缀提供给配置提供程序：</span><span class="sxs-lookup"><span data-stu-id="99ce0-731">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="99ce0-732">创建配置键值对时，将去除前缀。</span><span class="sxs-lookup"><span data-stu-id="99ce0-732">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="99ce0-733">若已创建主机生成器，则主机配置由环境变量提供。</span><span class="sxs-lookup"><span data-stu-id="99ce0-733">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="99ce0-734">有关用于这些环境变量的前缀的详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="99ce0-734">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="99ce0-735">**连接字符串前缀**</span><span class="sxs-lookup"><span data-stu-id="99ce0-735">**Connection string prefixes**</span></span>

<span data-ttu-id="99ce0-736">针对为应用环境配置 Azure 连接字符串所涉及的四个连接字符串环境变量，配置 API 具有特殊的处理规则。</span><span class="sxs-lookup"><span data-stu-id="99ce0-736">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="99ce0-737">如果没有向 `AddEnvironmentVariables` 提供前缀，则具有表中所示前缀的环境变量将加载到应用中。</span><span class="sxs-lookup"><span data-stu-id="99ce0-737">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="99ce0-738">连接字符串前缀</span><span class="sxs-lookup"><span data-stu-id="99ce0-738">Connection string prefix</span></span> | <span data-ttu-id="99ce0-739">提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-739">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="99ce0-740">自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-740">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="99ce0-741">MySQL</span><span class="sxs-lookup"><span data-stu-id="99ce0-741">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="99ce0-742">Azure SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="99ce0-742">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="99ce0-743">SQL Server</span><span class="sxs-lookup"><span data-stu-id="99ce0-743">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="99ce0-744">当发现环境变量并使用表中所示的四个前缀中的任何一个加载到配置中时：</span><span class="sxs-lookup"><span data-stu-id="99ce0-744">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="99ce0-745">通过删除环境变量前缀并添加配置键节 (`ConnectionStrings`) 来创建配置键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-745">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="99ce0-746">创建一个新的配置键值对，表示数据库连接提供程序（`CUSTOMCONNSTR_` 除外，它没有声明的提供程序）。</span><span class="sxs-lookup"><span data-stu-id="99ce0-746">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="99ce0-747">环境变量键</span><span class="sxs-lookup"><span data-stu-id="99ce0-747">Environment variable key</span></span> | <span data-ttu-id="99ce0-748">转换的配置键</span><span class="sxs-lookup"><span data-stu-id="99ce0-748">Converted configuration key</span></span> | <span data-ttu-id="99ce0-749">提供程序配置条目</span><span class="sxs-lookup"><span data-stu-id="99ce0-749">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="99ce0-750">配置条目未创建。</span><span class="sxs-lookup"><span data-stu-id="99ce0-750">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="99ce0-751">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-751">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="99ce0-752">值：`MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="99ce0-752">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="99ce0-753">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-753">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="99ce0-754">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="99ce0-754">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="99ce0-755">键：`ConnectionStrings:{KEY}_ProviderName`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-755">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="99ce0-756">值：`System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="99ce0-756">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="99ce0-757">**示例**</span><span class="sxs-lookup"><span data-stu-id="99ce0-757">**Example**</span></span>

<span data-ttu-id="99ce0-758">在服务器上创建了一个自定义连接字符串环境变量：</span><span class="sxs-lookup"><span data-stu-id="99ce0-758">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="99ce0-759">名称：`CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="99ce0-759">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="99ce0-760">值：`Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="99ce0-760">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="99ce0-761">如果 `IConfiguration` 已引入并分配给名为 `_config` 的字段，请读取值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-761">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="99ce0-762">文件配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-762">File Configuration Provider</span></span>

<span data-ttu-id="99ce0-763"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> 是从文件系统加载配置的基类。</span><span class="sxs-lookup"><span data-stu-id="99ce0-763"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="99ce0-764">以下配置提供程序专用于特定文件类型：</span><span class="sxs-lookup"><span data-stu-id="99ce0-764">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="99ce0-765">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-765">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="99ce0-766">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-766">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="99ce0-767">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-767">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="99ce0-768">INI 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-768">INI Configuration Provider</span></span>

<span data-ttu-id="99ce0-769"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> 在运行时从 INI 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-769">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="99ce0-770">若要激活 INI 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-770">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="99ce0-771">冒号可用作 INI 文件配置中的节分隔符。</span><span class="sxs-lookup"><span data-stu-id="99ce0-771">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="99ce0-772">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="99ce0-772">Overloads permit specifying:</span></span>

* <span data-ttu-id="99ce0-773">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="99ce0-773">Whether the file is optional.</span></span>
* <span data-ttu-id="99ce0-774">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-774">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="99ce0-775"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-775">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="99ce0-776">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-776">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="99ce0-777">INI 配置文件的通用示例：</span><span class="sxs-lookup"><span data-stu-id="99ce0-777">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="99ce0-778">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="99ce0-778">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="99ce0-779">section0:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-779">section0:key0</span></span>
* <span data-ttu-id="99ce0-780">section0:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-780">section0:key1</span></span>
* <span data-ttu-id="99ce0-781">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="99ce0-781">section1:subsection:key</span></span>
* <span data-ttu-id="99ce0-782">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="99ce0-782">section2:subsection0:key</span></span>
* <span data-ttu-id="99ce0-783">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="99ce0-783">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="99ce0-784">JSON 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-784">JSON Configuration Provider</span></span>

<span data-ttu-id="99ce0-785"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> 在运行时期间从 JSON 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-785">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="99ce0-786">若要激活 JSON 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-786">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="99ce0-787">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="99ce0-787">Overloads permit specifying:</span></span>

* <span data-ttu-id="99ce0-788">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="99ce0-788">Whether the file is optional.</span></span>
* <span data-ttu-id="99ce0-789">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-789">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="99ce0-790"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-790">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="99ce0-791">使用 `CreateDefaultBuilder` 初始化新的主机生成器时，会自动调用两次 `AddJsonFile`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-791">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="99ce0-792">调用该方法来从以下文件加载配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-792">The method is called to load configuration from:</span></span>

* <span data-ttu-id="99ce0-793">appsettings.json：先读取此文件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-793">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="99ce0-794">文件的环境版本可替代 appsettings.json 文件提供的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-794">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="99ce0-795">appsettings.{Environment}.json：文件的环境版本是根据 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) 加载的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-795">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="99ce0-796">有关详细信息，请参阅[默认配置](#default-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="99ce0-796">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="99ce0-797">此外，`CreateDefaultBuilder` 也会加载：</span><span class="sxs-lookup"><span data-stu-id="99ce0-797">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="99ce0-798">环境变量。</span><span class="sxs-lookup"><span data-stu-id="99ce0-798">Environment variables.</span></span>
* <span data-ttu-id="99ce0-799">开发环境中的[用户机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-799">[User secrets](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="99ce0-800">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="99ce0-800">Command-line arguments.</span></span>

<span data-ttu-id="99ce0-801">首先建立 JSON 配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-801">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="99ce0-802">因此，用户机密、环境变量和命令行参数会替代由 appsettings 文件设置的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-802">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="99ce0-803">构建主机时，调用 `ConfigureAppConfiguration` 来指定除 appsettings.json 和 appsettings.{Environment}.json 以外的文件的应用配置 ：</span><span class="sxs-lookup"><span data-stu-id="99ce0-803">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="99ce0-804">**示例**</span><span class="sxs-lookup"><span data-stu-id="99ce0-804">**Example**</span></span>

<span data-ttu-id="99ce0-805">示例应用利用静态便捷方法 `CreateDefaultBuilder` 来生成主机，其中包括两个对 `AddJsonFile` 的调用：</span><span class="sxs-lookup"><span data-stu-id="99ce0-805">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="99ce0-806">第一次调用 `AddJsonFile` 会从 appsettings.json 加载配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-806">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="99ce0-807">第二次调用 `AddJsonFile` 会从 appsettings.{Environment}.json 加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-807">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="99ce0-808">对于示例应用中的 appsettings.Development.json，将加载以下文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-808">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="99ce0-809">运行示例应用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-809">Run the sample app.</span></span> <span data-ttu-id="99ce0-810">在 `http://localhost:5000` 打开应用的浏览器。</span><span class="sxs-lookup"><span data-stu-id="99ce0-810">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="99ce0-811">输出包含配置的键值对（由应用的环境而定）。</span><span class="sxs-lookup"><span data-stu-id="99ce0-811">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="99ce0-812">在开发环境中运行应用时，键 `Logging:LogLevel:Default` 的日志级别为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-812">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="99ce0-813">再次在生产环境中运行示例应用：</span><span class="sxs-lookup"><span data-stu-id="99ce0-813">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="99ce0-814">打开 Properties/launchSettings.json 文件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-814">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="99ce0-815">在 `ConfigurationSample` 配置文件中，将 `ASPNETCORE_ENVIRONMENT` 环境变量的值更改为 `Production`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-815">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="99ce0-816">保存文件，然后在命令外壳中使用 `dotnet run` 运行应用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-816">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="99ce0-817">appsettings.Development.json 中的设置不再会替代 appsettings.json 中的设置 。</span><span class="sxs-lookup"><span data-stu-id="99ce0-817">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="99ce0-818">键 `Logging:LogLevel:Default` 的日志级别为 `Warning`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-818">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="99ce0-819">XML 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-819">XML Configuration Provider</span></span>

<span data-ttu-id="99ce0-820"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> 在运行时从 XML 文件键值对加载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-820">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="99ce0-821">若要激活 XML 文件配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-821">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="99ce0-822">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="99ce0-822">Overloads permit specifying:</span></span>

* <span data-ttu-id="99ce0-823">文件是否可选。</span><span class="sxs-lookup"><span data-stu-id="99ce0-823">Whether the file is optional.</span></span>
* <span data-ttu-id="99ce0-824">如果文件更改，是否重载配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-824">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="99ce0-825"><xref:Microsoft.Extensions.FileProviders.IFileProvider> 用于访问该文件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-825">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="99ce0-826">创建配置键值对时，将忽略配置文件的根节点。</span><span class="sxs-lookup"><span data-stu-id="99ce0-826">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="99ce0-827">不要在文件中指定文档类型定义 (DTD) 或命名空间。</span><span class="sxs-lookup"><span data-stu-id="99ce0-827">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="99ce0-828">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-828">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="99ce0-829">XML 配置文件可以为重复节使用不同的元素名称：</span><span class="sxs-lookup"><span data-stu-id="99ce0-829">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="99ce0-830">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="99ce0-830">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="99ce0-831">section0:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-831">section0:key0</span></span>
* <span data-ttu-id="99ce0-832">section0:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-832">section0:key1</span></span>
* <span data-ttu-id="99ce0-833">section1:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-833">section1:key0</span></span>
* <span data-ttu-id="99ce0-834">section1:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-834">section1:key1</span></span>

<span data-ttu-id="99ce0-835">如果使用 `name` 属性来区分元素，则使用相同元素名称的重复元素可以正常工作：</span><span class="sxs-lookup"><span data-stu-id="99ce0-835">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="99ce0-836">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="99ce0-836">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="99ce0-837">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-837">section:section0:key:key0</span></span>
* <span data-ttu-id="99ce0-838">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-838">section:section0:key:key1</span></span>
* <span data-ttu-id="99ce0-839">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-839">section:section1:key:key0</span></span>
* <span data-ttu-id="99ce0-840">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-840">section:section1:key:key1</span></span>

<span data-ttu-id="99ce0-841">属性可用于提供值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-841">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="99ce0-842">以前的配置文件使用 `value` 加载以下键：</span><span class="sxs-lookup"><span data-stu-id="99ce0-842">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="99ce0-843">key:attribute</span><span class="sxs-lookup"><span data-stu-id="99ce0-843">key:attribute</span></span>
* <span data-ttu-id="99ce0-844">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="99ce0-844">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="99ce0-845">Key-per-file 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-845">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="99ce0-846"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> 使用目录的文件作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-846">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="99ce0-847">该键是文件名。</span><span class="sxs-lookup"><span data-stu-id="99ce0-847">The key is the file name.</span></span> <span data-ttu-id="99ce0-848">该值包含文件的内容。</span><span class="sxs-lookup"><span data-stu-id="99ce0-848">The value contains the file's contents.</span></span> <span data-ttu-id="99ce0-849">Key-per-file 配置提供程序用于 Docker 托管方案。</span><span class="sxs-lookup"><span data-stu-id="99ce0-849">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="99ce0-850">若要激活 Key-per-file 配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-850">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="99ce0-851">文件的 `directoryPath` 必须是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="99ce0-851">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="99ce0-852">重载允许指定：</span><span class="sxs-lookup"><span data-stu-id="99ce0-852">Overloads permit specifying:</span></span>

* <span data-ttu-id="99ce0-853">配置源的 `Action<KeyPerFileConfigurationSource>` 委托。</span><span class="sxs-lookup"><span data-stu-id="99ce0-853">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="99ce0-854">目录是否可选以及目录的路径。</span><span class="sxs-lookup"><span data-stu-id="99ce0-854">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="99ce0-855">双下划线字符 (`__`) 用作文件名中的配置键分隔符。</span><span class="sxs-lookup"><span data-stu-id="99ce0-855">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="99ce0-856">例如，文件名 `Logging__LogLevel__System` 生成配置键 `Logging:LogLevel:System`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-856">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="99ce0-857">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-857">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="99ce0-858">内存配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-858">Memory Configuration Provider</span></span>

<span data-ttu-id="99ce0-859"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> 使用内存中集合作为配置键值对。</span><span class="sxs-lookup"><span data-stu-id="99ce0-859">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="99ce0-860">若要激活内存中集合配置，请在 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 的实例上调用 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="99ce0-860">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="99ce0-861">可以使用 `IEnumerable<KeyValuePair<String,String>>` 初始化配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-861">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="99ce0-862">构建主机时调用 `ConfigureAppConfiguration` 以指定应用的配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-862">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="99ce0-863">在下面的示例中，创建了配置字典：</span><span class="sxs-lookup"><span data-stu-id="99ce0-863">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="99ce0-864">通过 `AddInMemoryCollection` 的调用使用字典，以提供配置：</span><span class="sxs-lookup"><span data-stu-id="99ce0-864">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="99ce0-865">GetValue</span><span class="sxs-lookup"><span data-stu-id="99ce0-865">GetValue</span></span>

<span data-ttu-id="99ce0-866">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) 从配置中提取一个具有指定键的值，并将它转换为指定的非集合类型。</span><span class="sxs-lookup"><span data-stu-id="99ce0-866">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="99ce0-867">重载接受默认值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-867">An overload accepts a default value.</span></span>

<span data-ttu-id="99ce0-868">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="99ce0-868">The following example:</span></span>

* <span data-ttu-id="99ce0-869">使用键 `NumberKey` 从配置中提取字符串值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-869">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="99ce0-870">如果在配置键中找不到 `NumberKey`，则使用默认值 `99`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-870">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="99ce0-871">键入值作为 `int`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-871">Types the value as an `int`.</span></span>
* <span data-ttu-id="99ce0-872">存储 `NumberConfig` 属性中的值，以供页面使用。</span><span class="sxs-lookup"><span data-stu-id="99ce0-872">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="99ce0-873">GetSection、GetChildren 和 Exists</span><span class="sxs-lookup"><span data-stu-id="99ce0-873">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="99ce0-874">对于下面的示例，请考虑以下 JSON 文件。</span><span class="sxs-lookup"><span data-stu-id="99ce0-874">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="99ce0-875">在两个节中找到四个键，其中一个包含一对子节：</span><span class="sxs-lookup"><span data-stu-id="99ce0-875">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="99ce0-876">将文件读入配置时，会创建以下唯一的分层键来保存配置值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-876">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="99ce0-877">section0:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-877">section0:key0</span></span>
* <span data-ttu-id="99ce0-878">section0:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-878">section0:key1</span></span>
* <span data-ttu-id="99ce0-879">section1:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-879">section1:key0</span></span>
* <span data-ttu-id="99ce0-880">section1:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-880">section1:key1</span></span>
* <span data-ttu-id="99ce0-881">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-881">section2:subsection0:key0</span></span>
* <span data-ttu-id="99ce0-882">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-882">section2:subsection0:key1</span></span>
* <span data-ttu-id="99ce0-883">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="99ce0-883">section2:subsection1:key0</span></span>
* <span data-ttu-id="99ce0-884">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="99ce0-884">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="99ce0-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="99ce0-885">GetSection</span></span>

<span data-ttu-id="99ce0-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) 使用指定的子节键提取配置子节。</span><span class="sxs-lookup"><span data-stu-id="99ce0-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="99ce0-887">若要返回仅包含 `section1` 中键值对的 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>，请调用 `GetSection` 并提供节名称：</span><span class="sxs-lookup"><span data-stu-id="99ce0-887">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="99ce0-888">`configSection` 不具有值，只有密钥和路径。</span><span class="sxs-lookup"><span data-stu-id="99ce0-888">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="99ce0-889">同样，若要获取 `section2:subsection0` 中键的值，请调用 `GetSection` 并提供节路径：</span><span class="sxs-lookup"><span data-stu-id="99ce0-889">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="99ce0-890">`GetSection` 永远不会返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-890">`GetSection` never returns `null`.</span></span> <span data-ttu-id="99ce0-891">如果找不到匹配的节，则返回空 `IConfigurationSection`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-891">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="99ce0-892">当 `GetSection` 返回匹配的部分时，<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> 未填充。</span><span class="sxs-lookup"><span data-stu-id="99ce0-892">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="99ce0-893">存在该部分时，返回一个 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 和 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> 部分。</span><span class="sxs-lookup"><span data-stu-id="99ce0-893">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="99ce0-894">GetChildren</span><span class="sxs-lookup"><span data-stu-id="99ce0-894">GetChildren</span></span>

<span data-ttu-id="99ce0-895">在 `section2` 上调用 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) 会获得 `IEnumerable<IConfigurationSection>`，其中包括：</span><span class="sxs-lookup"><span data-stu-id="99ce0-895">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="99ce0-896">存在</span><span class="sxs-lookup"><span data-stu-id="99ce0-896">Exists</span></span>

<span data-ttu-id="99ce0-897">使用 [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) 确定配置节是否存在：</span><span class="sxs-lookup"><span data-stu-id="99ce0-897">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="99ce0-898">给定示例数据，`sectionExists` 为 `false`，因为配置数据中没有 `section2:subsection2` 节。</span><span class="sxs-lookup"><span data-stu-id="99ce0-898">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="99ce0-899">绑定至对象图</span><span class="sxs-lookup"><span data-stu-id="99ce0-899">Bind to an object graph</span></span>

<span data-ttu-id="99ce0-900"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 能够绑定整个 POCO 对象图。</span><span class="sxs-lookup"><span data-stu-id="99ce0-900"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="99ce0-901">与绑定简单对象一样，只绑定公共读取/写入属性。</span><span class="sxs-lookup"><span data-stu-id="99ce0-901">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="99ce0-902">该示例包含 `TvShow` 模型，其对象图包含 `Metadata` 和 `Actors` 类 (Models/TvShow.cs)：</span><span class="sxs-lookup"><span data-stu-id="99ce0-902">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="99ce0-903">示例应用有一个包含配置数据的 tvshow.xml 文件：</span><span class="sxs-lookup"><span data-stu-id="99ce0-903">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="99ce0-904">使用 `Bind` 方法将配置绑定到整个 `TvShow` 对象图。</span><span class="sxs-lookup"><span data-stu-id="99ce0-904">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="99ce0-905">将绑定实例分配给用于呈现的属性：</span><span class="sxs-lookup"><span data-stu-id="99ce0-905">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="99ce0-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 绑定并返回指定的类型。</span><span class="sxs-lookup"><span data-stu-id="99ce0-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="99ce0-907">`Get<T>` 比使用 `Bind` 更方便。</span><span class="sxs-lookup"><span data-stu-id="99ce0-907">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="99ce0-908">以下代码演示了如何通过前述示例使用 `Get<T>`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-908">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="99ce0-909">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="99ce0-909">Bind an array to a class</span></span>

<span data-ttu-id="99ce0-910">示例应用演示了本部分中介绍的概念。</span><span class="sxs-lookup"><span data-stu-id="99ce0-910">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="99ce0-911"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> 支持使用配置键中的数组索引将数组绑定到对象。</span><span class="sxs-lookup"><span data-stu-id="99ce0-911">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="99ce0-912">公开数字键段（`:0:`、`:1:`、&hellip; `:{n}:`）的任何数组格式都能够与 POCO 类数组进行数组绑定。</span><span class="sxs-lookup"><span data-stu-id="99ce0-912">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="99ce0-913">绑定是按约定提供的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-913">Binding is provided by convention.</span></span> <span data-ttu-id="99ce0-914">不需要自定义配置提供程序实现数组绑定。</span><span class="sxs-lookup"><span data-stu-id="99ce0-914">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="99ce0-915">**内存中数组处理**</span><span class="sxs-lookup"><span data-stu-id="99ce0-915">**In-memory array processing**</span></span>

<span data-ttu-id="99ce0-916">请考虑下表中所示的配置键和值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-916">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="99ce0-917">键</span><span class="sxs-lookup"><span data-stu-id="99ce0-917">Key</span></span>             | <span data-ttu-id="99ce0-918">“值”</span><span class="sxs-lookup"><span data-stu-id="99ce0-918">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="99ce0-919">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="99ce0-919">array:entries:0</span></span> | <span data-ttu-id="99ce0-920">value0</span><span class="sxs-lookup"><span data-stu-id="99ce0-920">value0</span></span> |
| <span data-ttu-id="99ce0-921">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="99ce0-921">array:entries:1</span></span> | <span data-ttu-id="99ce0-922">value1</span><span class="sxs-lookup"><span data-stu-id="99ce0-922">value1</span></span> |
| <span data-ttu-id="99ce0-923">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="99ce0-923">array:entries:2</span></span> | <span data-ttu-id="99ce0-924">value2</span><span class="sxs-lookup"><span data-stu-id="99ce0-924">value2</span></span> |
| <span data-ttu-id="99ce0-925">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="99ce0-925">array:entries:4</span></span> | <span data-ttu-id="99ce0-926">value4</span><span class="sxs-lookup"><span data-stu-id="99ce0-926">value4</span></span> |
| <span data-ttu-id="99ce0-927">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="99ce0-927">array:entries:5</span></span> | <span data-ttu-id="99ce0-928">value5</span><span class="sxs-lookup"><span data-stu-id="99ce0-928">value5</span></span> |

<span data-ttu-id="99ce0-929">使用内存配置提供程序在示例应用中加载这些键和值：</span><span class="sxs-lookup"><span data-stu-id="99ce0-929">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="99ce0-930">该数组跳过索引 &num;3 的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-930">The array skips a value for index &num;3.</span></span> <span data-ttu-id="99ce0-931">配置绑定程序无法绑定 null 值，也无法在绑定对象中创建 null 条目，这在演示将此数组绑定到对象的结果时变得清晰。</span><span class="sxs-lookup"><span data-stu-id="99ce0-931">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="99ce0-932">在示例应用中，POCO 类可用于保存绑定的配置数据：</span><span class="sxs-lookup"><span data-stu-id="99ce0-932">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="99ce0-933">将配置数据绑定至对象：</span><span class="sxs-lookup"><span data-stu-id="99ce0-933">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="99ce0-934">还可以使用 [`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 语法，这样会得到更精简的代码：</span><span class="sxs-lookup"><span data-stu-id="99ce0-934">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="99ce0-935">绑定对象（`ArrayExample` 的实例）从配置接收数组数据。</span><span class="sxs-lookup"><span data-stu-id="99ce0-935">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="99ce0-936">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="99ce0-936">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="99ce0-937">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="99ce0-937">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="99ce0-938">0</span><span class="sxs-lookup"><span data-stu-id="99ce0-938">0</span></span>                            | <span data-ttu-id="99ce0-939">value0</span><span class="sxs-lookup"><span data-stu-id="99ce0-939">value0</span></span>                       |
| <span data-ttu-id="99ce0-940">1</span><span class="sxs-lookup"><span data-stu-id="99ce0-940">1</span></span>                            | <span data-ttu-id="99ce0-941">value1</span><span class="sxs-lookup"><span data-stu-id="99ce0-941">value1</span></span>                       |
| <span data-ttu-id="99ce0-942">2</span><span class="sxs-lookup"><span data-stu-id="99ce0-942">2</span></span>                            | <span data-ttu-id="99ce0-943">value2</span><span class="sxs-lookup"><span data-stu-id="99ce0-943">value2</span></span>                       |
| <span data-ttu-id="99ce0-944">3</span><span class="sxs-lookup"><span data-stu-id="99ce0-944">3</span></span>                            | <span data-ttu-id="99ce0-945">value4</span><span class="sxs-lookup"><span data-stu-id="99ce0-945">value4</span></span>                       |
| <span data-ttu-id="99ce0-946">4</span><span class="sxs-lookup"><span data-stu-id="99ce0-946">4</span></span>                            | <span data-ttu-id="99ce0-947">value5</span><span class="sxs-lookup"><span data-stu-id="99ce0-947">value5</span></span>                       |

<span data-ttu-id="99ce0-948">绑定对象中的索引 &num;3 保留 `array:4` 配置键的配置数据及其值 `value4`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-948">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="99ce0-949">当绑定包含数组的配置数据时，配置键中的数组索引仅用于在创建对象时迭代配置数据。</span><span class="sxs-lookup"><span data-stu-id="99ce0-949">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="99ce0-950">无法在配置数据中保留 null 值，并且当配置键中的数组跳过一个或多个索引时，不会在绑定对象中创建 null 值条目。</span><span class="sxs-lookup"><span data-stu-id="99ce0-950">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="99ce0-951">可以在由任何在配置中生成正确键值对的配置提供程序绑定到 `ArrayExample` 实例之前提供索引 &num;3 的缺失配置项。</span><span class="sxs-lookup"><span data-stu-id="99ce0-951">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="99ce0-952">如果示例包含具有缺失键值对的其他 JSON 配置提供程序，则 `ArrayExample.Entries` 与完整配置数组相匹配：</span><span class="sxs-lookup"><span data-stu-id="99ce0-952">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="99ce0-953">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="99ce0-953">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="99ce0-954">在 `ConfigureAppConfiguration`中：</span><span class="sxs-lookup"><span data-stu-id="99ce0-954">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="99ce0-955">将表中所示的键值对加载到配置中。</span><span class="sxs-lookup"><span data-stu-id="99ce0-955">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="99ce0-956">密钥</span><span class="sxs-lookup"><span data-stu-id="99ce0-956">Key</span></span>             | <span data-ttu-id="99ce0-957">“值”</span><span class="sxs-lookup"><span data-stu-id="99ce0-957">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="99ce0-958">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="99ce0-958">array:entries:3</span></span> | <span data-ttu-id="99ce0-959">value3</span><span class="sxs-lookup"><span data-stu-id="99ce0-959">value3</span></span> |

<span data-ttu-id="99ce0-960">如果在 JSON 配置提供程序包含索引 &num;3 的条目之后绑定 `ArrayExample` 类实例，则 `ArrayExample.Entries` 数组包含该值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-960">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="99ce0-961">`ArrayExample.Entries` 索引</span><span class="sxs-lookup"><span data-stu-id="99ce0-961">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="99ce0-962">`ArrayExample.Entries` 值</span><span class="sxs-lookup"><span data-stu-id="99ce0-962">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="99ce0-963">0</span><span class="sxs-lookup"><span data-stu-id="99ce0-963">0</span></span>                            | <span data-ttu-id="99ce0-964">value0</span><span class="sxs-lookup"><span data-stu-id="99ce0-964">value0</span></span>                       |
| <span data-ttu-id="99ce0-965">1</span><span class="sxs-lookup"><span data-stu-id="99ce0-965">1</span></span>                            | <span data-ttu-id="99ce0-966">value1</span><span class="sxs-lookup"><span data-stu-id="99ce0-966">value1</span></span>                       |
| <span data-ttu-id="99ce0-967">2</span><span class="sxs-lookup"><span data-stu-id="99ce0-967">2</span></span>                            | <span data-ttu-id="99ce0-968">value2</span><span class="sxs-lookup"><span data-stu-id="99ce0-968">value2</span></span>                       |
| <span data-ttu-id="99ce0-969">3</span><span class="sxs-lookup"><span data-stu-id="99ce0-969">3</span></span>                            | <span data-ttu-id="99ce0-970">value3</span><span class="sxs-lookup"><span data-stu-id="99ce0-970">value3</span></span>                       |
| <span data-ttu-id="99ce0-971">4</span><span class="sxs-lookup"><span data-stu-id="99ce0-971">4</span></span>                            | <span data-ttu-id="99ce0-972">value4</span><span class="sxs-lookup"><span data-stu-id="99ce0-972">value4</span></span>                       |
| <span data-ttu-id="99ce0-973">5</span><span class="sxs-lookup"><span data-stu-id="99ce0-973">5</span></span>                            | <span data-ttu-id="99ce0-974">value5</span><span class="sxs-lookup"><span data-stu-id="99ce0-974">value5</span></span>                       |

<span data-ttu-id="99ce0-975">**JSON 数组处理**</span><span class="sxs-lookup"><span data-stu-id="99ce0-975">**JSON array processing**</span></span>

<span data-ttu-id="99ce0-976">如果 JSON 文件包含数组，则会为具有从零开始的节索引的数组元素创建配置键。</span><span class="sxs-lookup"><span data-stu-id="99ce0-976">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="99ce0-977">在以下配置文件中，`subsection` 是一个数组：</span><span class="sxs-lookup"><span data-stu-id="99ce0-977">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="99ce0-978">JSON 配置提供程序将配置数据读入以下键值对：</span><span class="sxs-lookup"><span data-stu-id="99ce0-978">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="99ce0-979">键</span><span class="sxs-lookup"><span data-stu-id="99ce0-979">Key</span></span>                     | <span data-ttu-id="99ce0-980">“值”</span><span class="sxs-lookup"><span data-stu-id="99ce0-980">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="99ce0-981">json_array:key</span><span class="sxs-lookup"><span data-stu-id="99ce0-981">json_array:key</span></span>          | <span data-ttu-id="99ce0-982">valueA</span><span class="sxs-lookup"><span data-stu-id="99ce0-982">valueA</span></span> |
| <span data-ttu-id="99ce0-983">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="99ce0-983">json_array:subsection:0</span></span> | <span data-ttu-id="99ce0-984">valueB</span><span class="sxs-lookup"><span data-stu-id="99ce0-984">valueB</span></span> |
| <span data-ttu-id="99ce0-985">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="99ce0-985">json_array:subsection:1</span></span> | <span data-ttu-id="99ce0-986">valueC</span><span class="sxs-lookup"><span data-stu-id="99ce0-986">valueC</span></span> |
| <span data-ttu-id="99ce0-987">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="99ce0-987">json_array:subsection:2</span></span> | <span data-ttu-id="99ce0-988">valueD</span><span class="sxs-lookup"><span data-stu-id="99ce0-988">valueD</span></span> |

<span data-ttu-id="99ce0-989">在示例应用中，以下 POCO 类可用于绑定配置键值对：</span><span class="sxs-lookup"><span data-stu-id="99ce0-989">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="99ce0-990">绑定后，`JsonArrayExample.Key` 保存值 `valueA`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-990">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="99ce0-991">子节值存储在 POCO 数组属性 `Subsection` 中。</span><span class="sxs-lookup"><span data-stu-id="99ce0-991">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="99ce0-992">`JsonArrayExample.Subsection` 索引</span><span class="sxs-lookup"><span data-stu-id="99ce0-992">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="99ce0-993">`JsonArrayExample.Subsection` 值</span><span class="sxs-lookup"><span data-stu-id="99ce0-993">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="99ce0-994">0</span><span class="sxs-lookup"><span data-stu-id="99ce0-994">0</span></span>                                   | <span data-ttu-id="99ce0-995">valueB</span><span class="sxs-lookup"><span data-stu-id="99ce0-995">valueB</span></span>                              |
| <span data-ttu-id="99ce0-996">1</span><span class="sxs-lookup"><span data-stu-id="99ce0-996">1</span></span>                                   | <span data-ttu-id="99ce0-997">valueC</span><span class="sxs-lookup"><span data-stu-id="99ce0-997">valueC</span></span>                              |
| <span data-ttu-id="99ce0-998">2</span><span class="sxs-lookup"><span data-stu-id="99ce0-998">2</span></span>                                   | <span data-ttu-id="99ce0-999">valueD</span><span class="sxs-lookup"><span data-stu-id="99ce0-999">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="99ce0-1000">自定义配置提供程序</span><span class="sxs-lookup"><span data-stu-id="99ce0-1000">Custom configuration provider</span></span>

<span data-ttu-id="99ce0-1001">该示例应用演示了如何使用[实体框架 (EF)](/ef/core/) 创建从数据库读取配置键值对的基本配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1001">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="99ce0-1002">提供程序具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1002">The provider has the following characteristics:</span></span>

* <span data-ttu-id="99ce0-1003">EF 内存中数据库用于演示目的。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1003">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="99ce0-1004">若要使用需要连接字符串的数据库，请实现辅助 `ConfigurationBuilder` 以从另一个配置提供程序提供连接字符串。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1004">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="99ce0-1005">提供程序在启动时将数据库表读入配置。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1005">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="99ce0-1006">提供程序不会基于每个键查询数据库。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1006">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="99ce0-1007">未实现更改时重载，因此在应用启动后更新数据库对应用的配置没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1007">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="99ce0-1008">定义用于在数据库中存储配置值的 `EFConfigurationValue` 实体。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1008">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="99ce0-1009">*Models/EFConfigurationValue.cs*：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1009">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="99ce0-1010">添加 `EFConfigurationContext` 以存储和访问配置的值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1010">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="99ce0-1011">*EFConfigurationProvider/EFConfigurationContext.cs*：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1011">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="99ce0-1012">创建用于实现 <xref:Microsoft.Extensions.Configuration.IConfigurationSource> 的类。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1012">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="99ce0-1013">*EFConfigurationProvider/EFConfigurationSource.cs*：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1013">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="99ce0-1014">通过从 <xref:Microsoft.Extensions.Configuration.ConfigurationProvider> 继承来创建自定义配置提供程序。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1014">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="99ce0-1015">当数据库为空时，配置提供程序将对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1015">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="99ce0-1016">*EFConfigurationProvider/EFConfigurationProvider.cs*：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1016">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="99ce0-1017">可以使用 `AddEFConfiguration` 扩展方法将配置源添加到 `ConfigurationBuilder`。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1017">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="99ce0-1018">Extensions/EntityFrameworkExtensions.cs：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1018">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="99ce0-1019">下面的代码演示如何在 Program.cs 中使用自定义的 `EFConfigurationProvider`：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1019">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="99ce0-1020">在启动期间访问配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-1020">Access configuration during startup</span></span>

<span data-ttu-id="99ce0-1021">将 `IConfiguration` 注入 `Startup` 构造函数以访问 `Startup.ConfigureServices` 中的配置值。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1021">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="99ce0-1022">若要访问 `Startup.Configure` 中的配置，请将 `IConfiguration` 直接注入方法或使用构造函数中的实例：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1022">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="99ce0-1023">有关使用启动便捷方法访问配置的示例，请参阅[应用启动：便捷方法](xref:fundamentals/startup#convenience-methods)。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1023">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="99ce0-1024">在 Razor Pages 页面或 MVC 视图中访问配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-1024">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="99ce0-1025">若要访问 Razor Pages 页面或 MVC 视图中的配置设置，请为 [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) 命名空间添加 [using 指令](xref:mvc/views/razor#using)（[C# 参考：using 指令](/dotnet/csharp/language-reference/keywords/using-directive)）并将 <xref:Microsoft.Extensions.Configuration.IConfiguration> 注入该页面或视图。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1025">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="99ce0-1026">在 Razor Pages 页面中：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1026">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="99ce0-1027">在 MVC 视图中：</span><span class="sxs-lookup"><span data-stu-id="99ce0-1027">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="99ce0-1028">从外部程序集添加配置</span><span class="sxs-lookup"><span data-stu-id="99ce0-1028">Add configuration from an external assembly</span></span>

<span data-ttu-id="99ce0-1029">通过 <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 实现，可在启动时从应用 `Startup` 类之外的外部程序集向应用添加增强功能。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1029">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="99ce0-1030">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="99ce0-1030">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99ce0-1031">其他资源</span><span class="sxs-lookup"><span data-stu-id="99ce0-1031">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
