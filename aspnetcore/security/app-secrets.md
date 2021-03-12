---
title: ASP.NET Core 中的开发中安全存储应用机密
author: rick-anderson
description: 了解如何在开发 ASP.NET Core 应用过程中存储和检索敏感信息。
ms.author: scaddie
ms.custom: mvc, contperf-fy21q2
ms.date: 11/24/2020
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
uid: security/app-secrets
ms.openlocfilehash: b309b834bc7156b901447c8697e6d2b0156a30f1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585807"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="c9f9c-103">ASP.NET Core 中的开发中安全存储应用机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9f9c-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Daniel Roth](https://github.com/danroth27)和 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c9f9c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="c9f9c-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c9f9c-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c9f9c-106">本文档介绍如何管理开发计算机上的 ASP.NET Core 应用程序的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="c9f9c-107">切勿在源代码中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="c9f9c-108">生产机密不应用于开发或测试。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="c9f9c-109">机密不应与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="c9f9c-110">相反，应通过受控的方式（如环境变量或 Azure Key Vault）访问生产机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="c9f9c-111">可使用 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="c9f9c-112">环境变量</span><span class="sxs-lookup"><span data-stu-id="c9f9c-112">Environment variables</span></span>

<span data-ttu-id="c9f9c-113">环境变量用于避免在代码中或在本地配置文件中存储应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="c9f9c-114">环境变量会重写所有以前指定的配置源的配置值。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="c9f9c-115">请考虑一个 ASP.NET Core web 应用，其中启用了 **单个用户帐户** 安全。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="c9f9c-116">带有密钥的项目文件中包含默认的数据库连接字符串 *appsettings.json* `DefaultConnection` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="c9f9c-117">默认连接字符串用于 LocalDB，后者在用户模式下运行，不需要密码。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="c9f9c-118">在应用程序部署过程中， `DefaultConnection` 可使用环境变量的值覆盖密钥值。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="c9f9c-119">环境变量可以存储具有敏感凭据的完整连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="c9f9c-120">环境变量通常以未加密的纯文本格式存储。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="c9f9c-121">如果计算机或进程受到危害，则不受信任方可以访问环境变量。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="c9f9c-122">可能需要其他措施来防止泄露用户机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="c9f9c-123">机密管理器</span><span class="sxs-lookup"><span data-stu-id="c9f9c-123">Secret Manager</span></span>

<span data-ttu-id="c9f9c-124">机密管理器工具在开发 ASP.NET Core 项目的过程中存储敏感数据。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="c9f9c-125">在此上下文中，一段敏感数据是应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="c9f9c-126">应用密钥存储在与项目树不同的位置。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="c9f9c-127">应用程序机密与特定项目关联或在多个项目之间共享。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="c9f9c-128">应用密码未签入源控件。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="c9f9c-129">机密管理器工具不会加密存储的机密，也不应将其视为受信任的存储区。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="c9f9c-130">它仅用于开发目的。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-130">It's for development purposes only.</span></span> <span data-ttu-id="c9f9c-131">密钥和值存储在用户配置文件目录中的 JSON 配置文件中。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="c9f9c-132">机密管理器工具的工作原理</span><span class="sxs-lookup"><span data-stu-id="c9f9c-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="c9f9c-133">机密管理器工具隐藏了实现的详细信息，例如存储值的位置和方式。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="c9f9c-134">您可以使用该工具，而无需了解这些实现细节。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="c9f9c-135">这些值存储在本地计算机的用户配置文件文件夹中的 JSON 文件中：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a><span data-ttu-id="c9f9c-136">Windows</span><span class="sxs-lookup"><span data-stu-id="c9f9c-136">[Windows](#tab/windows)</span></span>

<span data-ttu-id="c9f9c-137">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="c9f9c-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c9f9c-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c9f9c-139">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="c9f9c-140">在前面的文件路径中，将替换 `<user_secrets_id>` 为 `UserSecretsId` 项目文件中指定的值。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="c9f9c-141">不要编写依赖于通过机密管理器工具保存的数据的位置或格式的代码。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="c9f9c-142">这些实现细节可能会发生变化。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-142">These implementation details may change.</span></span> <span data-ttu-id="c9f9c-143">例如，机密值不会加密，但可能会在将来。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="c9f9c-144">启用密钥存储</span><span class="sxs-lookup"><span data-stu-id="c9f9c-144">Enable secret storage</span></span>

<span data-ttu-id="c9f9c-145">机密管理器工具对存储在用户配置文件中的特定于项目的配置设置进行操作。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="c9f9c-146">机密管理器工具 `init` 在 .NET Core SDK 3.0.100 或更高版本中包含命令。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="c9f9c-147">若要使用用户机密，请在项目目录中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="c9f9c-148">前面的命令将在 `UserSecretsId` 项目文件的中添加一个元素 `PropertyGroup` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="c9f9c-149">默认情况下，的内部文本 `UserSecretsId` 是 GUID。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="c9f9c-150">内部文本是任意的，但对项目是唯一的。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="c9f9c-151">在 Visual Studio 中，右键单击 "解决方案资源管理器中的项目，然后从上下文菜单中选择" **管理用户机密** "。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="c9f9c-152">此笔势将 `UserSecretsId` 使用 GUID 填充的元素添加到项目文件。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="c9f9c-153">设置机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-153">Set a secret</span></span>

<span data-ttu-id="c9f9c-154">定义包含密钥及其值的应用密码。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="c9f9c-155">机密与项目的值相关联 `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="c9f9c-156">例如，从项目文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="c9f9c-157">在前面的示例中，冒号表示 `Movies` 是具有属性的对象文本 `ServiceApiKey` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="c9f9c-158">机密管理器工具也可用于其他目录。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="c9f9c-159">使用 `--project` 选项可提供项目文件所在的文件系统路径。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="c9f9c-160">例如：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="c9f9c-161">Visual Studio 中的 JSON 结构平展</span><span class="sxs-lookup"><span data-stu-id="c9f9c-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="c9f9c-162">Visual Studio 的 " **管理用户机密** " 手势在文本编辑器中打开文件的 *secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="c9f9c-163">将 *上secrets.js* 的内容替换为要存储的键值对。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="c9f9c-164">例如：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c9f9c-165">JSON 结构是通过或进行修改后平展的 `dotnet user-secrets remove` `dotnet user-secrets set` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="c9f9c-166">例如，运行会 `dotnet user-secrets remove "Movies:ConnectionString"` 折叠 `Movies` 对象文本。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="c9f9c-167">修改后的文件类似于以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="c9f9c-168">设置多个机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-168">Set multiple secrets</span></span>

<span data-ttu-id="c9f9c-169">可以通过管道 JSON 将密码批设置为 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="c9f9c-170">在下面的示例中，将文件的内容的 *input.js* 传递给 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a><span data-ttu-id="c9f9c-171">Windows</span><span class="sxs-lookup"><span data-stu-id="c9f9c-171">[Windows](#tab/windows)</span></span>

<span data-ttu-id="c9f9c-172">打开命令 shell，然后执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="c9f9c-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c9f9c-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c9f9c-174">打开命令 shell，然后执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="c9f9c-175">访问机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-175">Access a secret</span></span>

<span data-ttu-id="c9f9c-176">若要访问密钥，请完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="c9f9c-177">注册用户机密配置源</span><span class="sxs-lookup"><span data-stu-id="c9f9c-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="c9f9c-178">通过配置 API 读取机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="c9f9c-179">注册用户机密配置源</span><span class="sxs-lookup"><span data-stu-id="c9f9c-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="c9f9c-180">用户机密 [配置提供程序](/dotnet/core/extensions/configuration-providers) 向 .NET [配置 API](xref:fundamentals/configuration/index)注册适当的配置源。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="c9f9c-181">当项目调用时，用户机密配置源会自动以开发模式添加 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="c9f9c-182">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>当为时 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> 调用 <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> ：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="c9f9c-183">如果 `CreateDefaultBuilder` 未调用，则通过在中调用来显式添加用户机密配置源 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="c9f9c-184">`AddUserSecrets`仅在开发环境中运行应用时调用，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="c9f9c-185">通过配置 API 读取机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="c9f9c-186">如果注册了用户机密配置源，则 .NET 配置 API 可以读取机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="c9f9c-187">[构造函数注入](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) 可用于获取对 .NET 配置 API 的访问。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="c9f9c-188">请考虑以下读取密钥的示例 `Movies:ServiceApiKey` ：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="c9f9c-189">**Startup 类：**</span><span class="sxs-lookup"><span data-stu-id="c9f9c-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="c9f9c-190">**Razor 页面页面模型：**</span><span class="sxs-lookup"><span data-stu-id="c9f9c-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="c9f9c-191">有关详细信息，请参阅 " [在启动](xref:fundamentals/configuration/index#access-configuration-in-startup) 和 [访问配置" Razor 页中](xref:fundamentals/configuration/index#access-configuration-in-razor-pages)的访问配置。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="c9f9c-192">将机密映射到 POCO</span><span class="sxs-lookup"><span data-stu-id="c9f9c-192">Map secrets to a POCO</span></span>

<span data-ttu-id="c9f9c-193">将整个对象文本映射到 POCO (具有属性) 的简单 .NET 类对于聚合相关属性十分有用。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c9f9c-194">若要将上述机密映射到 POCO，请使用 .NET 配置 API 的 [对象图绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph) 功能。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="c9f9c-195">下面的代码绑定到自定义 `MovieSettings` POCO 并访问 `ServiceApiKey` 属性值：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="c9f9c-196">`Movies:ConnectionString`和 `Movies:ServiceApiKey` 机密映射到中的相应属性 `MovieSettings` ：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="c9f9c-197">用机密替换字符串</span><span class="sxs-lookup"><span data-stu-id="c9f9c-197">String replacement with secrets</span></span>

<span data-ttu-id="c9f9c-198">以纯文本形式存储密码是不安全的。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="c9f9c-199">例如，存储在中的数据库连接字符串 *appsettings.json* 可能包含指定用户的密码：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="c9f9c-200">更安全的方法是将密码存储为机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="c9f9c-201">例如：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="c9f9c-202">`Password`从中的连接字符串中移除键值对 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="c9f9c-203">例如：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="c9f9c-204">可以对对象的属性设置机密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成连接字符串：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="c9f9c-205">列出机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c9f9c-206">从项目文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="c9f9c-207">随即显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="c9f9c-208">在前面的示例中，键名称中的冒号表示 *secrets.js上* 的中的对象层次结构。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="c9f9c-209">删除单个机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c9f9c-210">从项目文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="c9f9c-211">已修改文件 *上* 应用的secrets.js，以删除与密钥关联的键值对 `MoviesConnectionString` ：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c9f9c-212">`dotnet user-secrets list` 显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="c9f9c-213">删除所有机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c9f9c-214">从项目文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="c9f9c-215">此应用的所有用户机密已从文件中的 *secrets.js* 删除：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="c9f9c-216">运行 `dotnet user-secrets list` 将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="c9f9c-217">其他资源</span><span class="sxs-lookup"><span data-stu-id="c9f9c-217">Additional resources</span></span>

* <span data-ttu-id="c9f9c-218">有关从 IIS 访问用户机密的信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c9f9c-219">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)和 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c9f9c-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="c9f9c-220">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c9f9c-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c9f9c-221">本文档介绍如何管理开发计算机上的 ASP.NET Core 应用程序的敏感数据。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="c9f9c-222">切勿在源代码中存储密码或其他敏感数据。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="c9f9c-223">生产机密不应用于开发或测试。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="c9f9c-224">机密不应与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="c9f9c-225">相反，应通过受控的方式（如环境变量或 Azure Key Vault）访问生产机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="c9f9c-226">可使用 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="c9f9c-227">环境变量</span><span class="sxs-lookup"><span data-stu-id="c9f9c-227">Environment variables</span></span>

<span data-ttu-id="c9f9c-228">环境变量用于避免在代码中或在本地配置文件中存储应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="c9f9c-229">环境变量会重写所有以前指定的配置源的配置值。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="c9f9c-230">请考虑一个 ASP.NET Core web 应用，其中启用了 **单个用户帐户** 安全。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="c9f9c-231">带有密钥的项目文件中包含默认的数据库连接字符串 *appsettings.json* `DefaultConnection` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="c9f9c-232">默认连接字符串用于 LocalDB，后者在用户模式下运行，不需要密码。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="c9f9c-233">在应用程序部署过程中， `DefaultConnection` 可使用环境变量的值覆盖密钥值。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="c9f9c-234">环境变量可以存储具有敏感凭据的完整连接字符串。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="c9f9c-235">环境变量通常以未加密的纯文本格式存储。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="c9f9c-236">如果计算机或进程受到危害，则不受信任方可以访问环境变量。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="c9f9c-237">可能需要其他措施来防止泄露用户机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="c9f9c-238">机密管理器</span><span class="sxs-lookup"><span data-stu-id="c9f9c-238">Secret Manager</span></span>

<span data-ttu-id="c9f9c-239">机密管理器工具在开发 ASP.NET Core 项目的过程中存储敏感数据。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="c9f9c-240">在此上下文中，一段敏感数据是应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="c9f9c-241">应用密钥存储在与项目树不同的位置。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="c9f9c-242">应用程序机密与特定项目关联或在多个项目之间共享。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="c9f9c-243">应用密码未签入源控件。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="c9f9c-244">机密管理器工具不会加密存储的机密，也不应将其视为受信任的存储区。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="c9f9c-245">它仅用于开发目的。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-245">It's for development purposes only.</span></span> <span data-ttu-id="c9f9c-246">密钥和值存储在用户配置文件目录中的 JSON 配置文件中。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="c9f9c-247">机密管理器工具的工作原理</span><span class="sxs-lookup"><span data-stu-id="c9f9c-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="c9f9c-248">机密管理器工具隐藏了实现的详细信息，例如存储值的位置和方式。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="c9f9c-249">您可以使用该工具，而无需了解这些实现细节。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="c9f9c-250">这些值存储在本地计算机的用户配置文件文件夹中的 JSON 文件中：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a><span data-ttu-id="c9f9c-251">Windows</span><span class="sxs-lookup"><span data-stu-id="c9f9c-251">[Windows](#tab/windows)</span></span>

<span data-ttu-id="c9f9c-252">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="c9f9c-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c9f9c-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c9f9c-254">文件系统路径：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="c9f9c-255">在前面的文件路径中，将替换 `<user_secrets_id>` 为 `UserSecretsId` 项目文件中指定的值。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="c9f9c-256">不要编写依赖于通过机密管理器工具保存的数据的位置或格式的代码。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="c9f9c-257">这些实现细节可能会发生变化。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-257">These implementation details may change.</span></span> <span data-ttu-id="c9f9c-258">例如，机密值不会加密，但可能会在将来。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="c9f9c-259">启用密钥存储</span><span class="sxs-lookup"><span data-stu-id="c9f9c-259">Enable secret storage</span></span>

<span data-ttu-id="c9f9c-260">机密管理器工具对存储在用户配置文件中的特定于项目的配置设置进行操作。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="c9f9c-261">若要使用用户机密，请在 `UserSecretsId` 项目文件的中定义一个元素 `PropertyGroup` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="c9f9c-262">的内部文本 `UserSecretsId` 是任意的，但对项目是唯一的。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="c9f9c-263">开发人员通常会生成的 GUID `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="c9f9c-264">在 Visual Studio 中，右键单击 "解决方案资源管理器中的项目，然后从上下文菜单中选择" **管理用户机密** "。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="c9f9c-265">此笔势将 `UserSecretsId` 使用 GUID 填充的元素添加到项目文件。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="c9f9c-266">设置机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-266">Set a secret</span></span>

<span data-ttu-id="c9f9c-267">定义包含密钥及其值的应用密码。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="c9f9c-268">机密与项目的值相关联 `UserSecretsId` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="c9f9c-269">例如，从项目文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="c9f9c-270">在前面的示例中，冒号表示 `Movies` 是具有属性的对象文本 `ServiceApiKey` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="c9f9c-271">机密管理器工具也可用于其他目录。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="c9f9c-272">使用 `--project` 选项可提供项目文件所在的文件系统路径。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="c9f9c-273">例如：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="c9f9c-274">Visual Studio 中的 JSON 结构平展</span><span class="sxs-lookup"><span data-stu-id="c9f9c-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="c9f9c-275">Visual Studio 的 " **管理用户机密** " 手势在文本编辑器中打开文件的 *secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="c9f9c-276">将 *上secrets.js* 的内容替换为要存储的键值对。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="c9f9c-277">例如：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c9f9c-278">JSON 结构是通过或进行修改后平展的 `dotnet user-secrets remove` `dotnet user-secrets set` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="c9f9c-279">例如，运行会 `dotnet user-secrets remove "Movies:ConnectionString"` 折叠 `Movies` 对象文本。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="c9f9c-280">修改后的文件类似于以下 JSON：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="c9f9c-281">设置多个机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-281">Set multiple secrets</span></span>

<span data-ttu-id="c9f9c-282">可以通过管道 JSON 将密码批设置为 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="c9f9c-283">在下面的示例中，将文件的内容的 *input.js* 传递给 `set` 命令。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a><span data-ttu-id="c9f9c-284">Windows</span><span class="sxs-lookup"><span data-stu-id="c9f9c-284">[Windows](#tab/windows)</span></span>

<span data-ttu-id="c9f9c-285">打开命令 shell，然后执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="c9f9c-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c9f9c-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c9f9c-287">打开命令 shell，然后执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="c9f9c-288">访问机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-288">Access a secret</span></span>

<span data-ttu-id="c9f9c-289">[配置 API](xref:fundamentals/configuration/index)提供对用户机密的访问权限。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="c9f9c-290">如果项目以 .NET Framework 为目标，请安装 [Microsoft.Extensions.Configu。UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="c9f9c-291">在 ASP.NET Core 2.0 或更高版本中，当项目调用时，用户机密配置源会自动以开发模式添加 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="c9f9c-292">`CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>当为时 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> 调用 <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> ：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="c9f9c-293">`CreateDefaultBuilder`不调用时，通过 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 在构造函数中调用来显式添加用户机密配置源 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="c9f9c-294">`AddUserSecrets`仅在开发环境中运行应用时调用，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="c9f9c-295">可以通过 .NET 配置 API 检索用户机密：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="c9f9c-296">将机密映射到 POCO</span><span class="sxs-lookup"><span data-stu-id="c9f9c-296">Map secrets to a POCO</span></span>

<span data-ttu-id="c9f9c-297">将整个对象文本映射到 POCO (具有属性) 的简单 .NET 类对于聚合相关属性十分有用。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c9f9c-298">若要将上述机密映射到 POCO，请使用 .NET 配置 API 的 [对象图绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph) 功能。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="c9f9c-299">下面的代码绑定到自定义 `MovieSettings` POCO 并访问 `ServiceApiKey` 属性值：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="c9f9c-300">`Movies:ConnectionString`和 `Movies:ServiceApiKey` 机密映射到中的相应属性 `MovieSettings` ：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="c9f9c-301">用机密替换字符串</span><span class="sxs-lookup"><span data-stu-id="c9f9c-301">String replacement with secrets</span></span>

<span data-ttu-id="c9f9c-302">以纯文本形式存储密码是不安全的。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="c9f9c-303">例如，存储在中的数据库连接字符串 *appsettings.json* 可能包含指定用户的密码：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="c9f9c-304">更安全的方法是将密码存储为机密。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="c9f9c-305">例如：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="c9f9c-306">`Password`从中的连接字符串中移除键值对 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="c9f9c-307">例如：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="c9f9c-308">可以对对象的属性设置机密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成连接字符串：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="c9f9c-309">列出机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c9f9c-310">从项目文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="c9f9c-311">随即显示以下输出：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="c9f9c-312">在前面的示例中，键名称中的冒号表示 *secrets.js上* 的中的对象层次结构。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="c9f9c-313">删除单个机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c9f9c-314">从项目文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="c9f9c-315">已修改文件 *上* 应用的secrets.js，以删除与密钥关联的键值对 `MoviesConnectionString` ：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c9f9c-316">运行 `dotnet user-secrets list` 将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="c9f9c-317">删除所有机密</span><span class="sxs-lookup"><span data-stu-id="c9f9c-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c9f9c-318">从项目文件所在的目录运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="c9f9c-319">此应用的所有用户机密已从文件中的 *secrets.js* 删除：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="c9f9c-320">运行 `dotnet user-secrets list` 将显示以下消息：</span><span class="sxs-lookup"><span data-stu-id="c9f9c-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="c9f9c-321">其他资源</span><span class="sxs-lookup"><span data-stu-id="c9f9c-321">Additional resources</span></span>

* <span data-ttu-id="c9f9c-322">有关从 IIS 访问用户机密的信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 。</span><span class="sxs-lookup"><span data-stu-id="c9f9c-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end