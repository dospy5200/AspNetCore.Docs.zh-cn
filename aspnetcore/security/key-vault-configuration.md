---
title: ASP.NET Core 中的 Azure Key Vault 配置提供程序
author: rick-anderson
description: 了解如何使用 Azure Key Vault 配置提供程序通过在运行时加载的名称/值对来配置应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: ab3a462a3e09113e96c6bdd0c034bff3e0bebdfa
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588290"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="0e3ba-103">ASP.NET Core 中的 Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="0e3ba-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="0e3ba-104">作者： [Andrew Stanton](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="0e3ba-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0e3ba-105">本文档说明如何使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="0e3ba-106">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="0e3ba-107">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="0e3ba-108">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="0e3ba-109">满足 FIPS 140-2 第2级验证的硬件安全模块在存储配置数据时 (HSM) 的要求。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="0e3ba-110">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0e3ba-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="0e3ba-111">包</span><span class="sxs-lookup"><span data-stu-id="0e3ba-111">Packages</span></span>

<span data-ttu-id="0e3ba-112">添加以下包的包引用：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-112">Add package references for the following packages:</span></span>

* [<span data-ttu-id="0e3ba-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span><span class="sxs-lookup"><span data-stu-id="0e3ba-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="0e3ba-114">[Microsoft.Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="0e3ba-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="0e3ba-115">示例应用</span><span class="sxs-lookup"><span data-stu-id="0e3ba-115">Sample app</span></span>

<span data-ttu-id="0e3ba-116">该示例应用在由 `#define` *Program.cs* 文件顶部的语句确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-116">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="0e3ba-117">`Certificate`：演示如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-117">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="0e3ba-118">可以从部署到 Azure App Service 的任何位置运行此版本的示例，也可以从部署到 ASP.NET Core 应用程序的任何主机上运行。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-118">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="0e3ba-119">`Managed`：演示如何使用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview) 对应用进行身份验证，以通过 Azure AD 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-119">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="0e3ba-120">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码 (客户端密钥) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="0e3ba-121">`Managed`必须将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="0e3ba-122">按照 [使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="0e3ba-123">有关如何使用预处理器指令 () 配置示例应用的详细信息 `#define` ，请参阅 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-123">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="0e3ba-124">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="0e3ba-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="0e3ba-125">使用 [机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-125">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="0e3ba-126">在开发环境中的本地计算机上运行示例应用时，会从本地用户机密存储区中加载机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="0e3ba-127">机密管理器工具需要 `<UserSecretsId>` 应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-127">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="0e3ba-128">将属性值 (`{GUID}`) 设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="0e3ba-129">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="0e3ba-130">配置节 (层次结构值) `:` 在 [ASP.NET Core 配置](xref:fundamentals/configuration/index) 项名称中将 (冒号) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="0e3ba-131">机密管理器用于从打开的命令 shell 到项目的 [内容根目录](xref:fundamentals/index#content-root)，其中 `{SECRET NAME}` 是名称， `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-131">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="0e3ba-132">从项目的 [内容根](xref:fundamentals/index#content-root) 在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="0e3ba-133">如果这些机密存储在 [生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure Key Vault 中，并 Azure Key Vault 部分，则 `_dev` 后缀将更改为 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="0e3ba-134">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="0e3ba-135">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="0e3ba-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="0e3ba-136">本 [快速入门：使用 Azure CLI 主题的 Azure Key Vault 设置和检索机密](/azure/key-vault/quick-create-cli) 的说明，请参阅此处，以创建 Azure Key Vault 和存储示例应用使用的机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-136">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="0e3ba-137">有关更多详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-137">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="0e3ba-138">使用 [Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-138">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="0e3ba-139">选择代码块右上角的“试用”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="0e3ba-140">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="0e3ba-141">在浏览器中打开 Cloud Shell，并提供 " **启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="0e3ba-142">选择 Azure 门户右上角菜单中的 " **Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="0e3ba-143">有关详细信息，请参阅 Azure Cloud Shell [Azure CLI](/cli/azure/) 和 [概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="0e3ba-144">如果尚未通过身份验证，请在命令中登录 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="0e3ba-145">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的资源组名称， `{LOCATION}` 是 Azure 区域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="0e3ba-146">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称， `{LOCATION}` 是 Azure 区域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="0e3ba-147">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="0e3ba-148">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="0e3ba-149">配置节 (层次结构值) 使用 `--` (两个短划线) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-149">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="0e3ba-150">冒号（通常用于在 [ASP.NET Core 配置](xref:fundamentals/configuration/index)中的子项中分隔部分）在 key vault 机密名称中不允许使用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-150">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="0e3ba-151">因此，当机密加载到应用的配置中时，将使用两个短划线并为冒号交换。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-151">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="0e3ba-152">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="0e3ba-153">这些值包含 `_prod` 后缀，以将其与 `_dev` 从用户机密的开发环境中加载的后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="0e3ba-154">将替换 `{KEY VAULT NAME}` 为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-154">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="0e3ba-155">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="0e3ba-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="0e3ba-156">配置 Azure AD、Azure Key Vault 和应用，以便在 **Azure 外托管应用时** 使用 Azure Active Directory 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="0e3ba-157">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="0e3ba-158">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但在 Azure 中托管应用程序时，我们建议使用 [Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="0e3ba-159">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-159">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="0e3ba-160">当 `#define` *Program.cs* 文件顶部的语句设置为时，示例应用使用应用程序 ID 和 x.509 证书 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-160">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="0e3ba-161">创建 PKCS # 12 存档 (*.pfx*) 证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-161">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="0e3ba-162">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-162">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="0e3ba-163">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-163">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="0e3ba-164">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-164">Marking the key as exportable is optional.</span></span> <span data-ttu-id="0e3ba-165">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-165">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="0e3ba-166">将 PKCS # 12 存档 (*.pfx*) 证书导 () *.cer* 编码的证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-166">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="0e3ba-167">将应用注册 Azure AD (**应用注册**) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-167">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="0e3ba-168">将 DER 编码的证书 (*.cer*) 上传到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-168">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="0e3ba-169">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-169">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="0e3ba-170">导航到 " **证书" & "机密**"。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-170">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="0e3ba-171">选择 " **上传证书** "，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-171">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="0e3ba-172">*.Cer*、 *pem* 或 *.crt* 证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-172">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="0e3ba-173">在应用的文件中存储密钥保管库名称、应用程序 ID 和证书指纹 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-173">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="0e3ba-174">导航到 Azure 门户中的 **密钥保管库** 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-174">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="0e3ba-175">选择在 [生产环境中的机密存储中](#secret-storage-in-the-production-environment-with-azure-key-vault) 创建的密钥保管库，其中 Azure Key Vault "部分。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-175">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="0e3ba-176">选择“访问策略”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-176">Select **Access policies**.</span></span>
1. <span data-ttu-id="0e3ba-177">选择“添加访问策略”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-177">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="0e3ba-178">打开 **机密权限** ，并为应用提供 **Get** 和 **List** 权限。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="0e3ba-179">选择 " **选择主体** "，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-179">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="0e3ba-180">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-180">Select the **Select** button.</span></span>
1. <span data-ttu-id="0e3ba-181">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-181">Select **OK**.</span></span>
1. <span data-ttu-id="0e3ba-182">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-182">Select **Save**.</span></span>
1. <span data-ttu-id="0e3ba-183">部署应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-183">Deploy the app.</span></span>

<span data-ttu-id="0e3ba-184">`Certificate`示例应用从中获取其配置值，其 `IConfigurationRoot` 名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-184">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="0e3ba-185">非分层值：通过获取的值 `SecretName` `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-185">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="0e3ba-186">) 部分 (层次结构值：使用 `:` (冒号) 表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-186">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="0e3ba-187">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-187">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="0e3ba-188">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-188">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="0e3ba-189">应用使用文件提供的值调用 **AddAzureKeyVault** *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-189">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="0e3ba-190">示例值：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-190">Example values:</span></span>

* <span data-ttu-id="0e3ba-191">密钥保管库名称： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="0e3ba-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="0e3ba-192">应用程序 ID： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="0e3ba-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="0e3ba-193">证书指纹： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="0e3ba-193">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="0e3ba-194">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0e3ba-194">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="0e3ba-195">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-195">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="0e3ba-196">在开发环境中，机密值用后缀进行加载 `_dev` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-196">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="0e3ba-197">在生产环境中，值以后缀进行加载 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-197">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="0e3ba-198">使用 Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="0e3ba-198">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="0e3ba-199">**部署到 azure 的应用** 可以利用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用使用 Azure AD 身份验证（不含凭据 (应用程序 ID 和密码/客户端密钥) 存储在应用中）对 Azure Key Vault 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-199">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="0e3ba-200">当 `#define` *Program.cs* 文件顶部的语句设置为时，示例应用使用 Azure 资源的托管标识 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-200">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="0e3ba-201">在应用的文件中输入保管库名称 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-201">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="0e3ba-202">当设置为版本时，示例应用不需要应用程序 ID 和密码 (的客户端机密) `Managed` ，因此你可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-202">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="0e3ba-203">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在文件中的保管库名称访问 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-203">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="0e3ba-204">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-204">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="0e3ba-205">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-205">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="0e3ba-206">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-206">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="0e3ba-207">对象 ID 显示在应用服务面板上的 "Azure 门户中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-207">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="0e3ba-208">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` `get` 访问密钥保管库的和权限：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-208">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="0e3ba-209">使用 Azure CLI、PowerShell 或 Azure 门户 **重启应用**。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-209">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="0e3ba-210">示例应用：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-210">The sample app:</span></span>

* <span data-ttu-id="0e3ba-211">创建类的实例 `DefaultAzureCredential` ，凭据将尝试从 Azure 资源的环境中获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-211">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="0e3ba-212">[`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets)使用实例创建新的 `DefaultAzureCredential` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-212">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="0e3ba-213">`Azure.Security.KeyVault.Secrets.Secrets`实例与加载所有机密值的的默认实现一起使用， `Azure.Extensions.AspNetCore.Configuration.Secrets` 并将 `--` 冒号 () 替换为 `:` 键名称中 () 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-213">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="0e3ba-214">Key vault 名称示例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="0e3ba-214">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="0e3ba-215">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0e3ba-215">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="0e3ba-216">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-216">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="0e3ba-217">在开发环境中，机密值具有 `_dev` 后缀，因为它们是由用户机密提供的。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-217">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="0e3ba-218">在生产环境中，值加载时使用 `_prod` 后缀，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-218">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="0e3ba-219">如果收到 `Access denied` 错误，请确认该应用已注册到 Azure AD，并提供对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-219">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="0e3ba-220">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-220">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="0e3ba-221">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅使用 [托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-221">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="0e3ba-222">配置选项</span><span class="sxs-lookup"><span data-stu-id="0e3ba-222">Configuration options</span></span>

<span data-ttu-id="0e3ba-223">AddAzureKeyVault 可以接受 AzureKeyVaultConfigurationOptions：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-223">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="0e3ba-224">Property</span><span class="sxs-lookup"><span data-stu-id="0e3ba-224">Property</span></span>         | <span data-ttu-id="0e3ba-225">描述</span><span class="sxs-lookup"><span data-stu-id="0e3ba-225">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="0e3ba-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` 用于控制机密加载的实例。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="0e3ba-227">`Timespan` 如果为，则在轮询密钥保管库以进行更改之间等待。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="0e3ba-228">默认值为 `null` (不) 重新加载配置。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-228">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="0e3ba-229">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="0e3ba-229">Use a key name prefix</span></span>

<span data-ttu-id="0e3ba-230">AddAzureKeyVault 提供一个重载，该重载接受的实现 `Azure.Extensions.AspNetCore.Configuration.Secrets` ，这允许你控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-230">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="0e3ba-231">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-231">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="0e3ba-232">例如，你可以根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-232">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="0e3ba-233">不要对 key vault 机密使用前缀，将多个应用的机密放入同一密钥保管库，或放置环境机密 (例如， *开发* 与 *生产* 机密) 到同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-233">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="0e3ba-234">建议不同的应用和开发/生产环境使用不同的密钥保管库，以便隔离应用环境以获得最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-234">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="0e3ba-235">在下面的示例中，密钥保管库中建立了机密 (和使用开发环境的机密管理器工具) 对于 `5000-AppSecret` 密钥保管库机密名称) 中不允许使用 (期间。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-235">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="0e3ba-236">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-236">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="0e3ba-237">对于其他版本的应用，5.1.0.0 会将机密添加到密钥保管库 (并使用机密管理器工具) `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-237">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="0e3ba-238">每个应用版本会将其版本控制的机密值加载到其配置中，并在 `AppSecret` 加载机密时去除版本。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-238">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="0e3ba-239">使用自定义调用 AddAzureKeyVault `Azure.Extensions.AspNetCore.Configuration.Secrets` ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-239">AddAzureKeyVault is called with a custom `Azure.Extensions.AspNetCore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="0e3ba-240">该 `Azure.Extensions.AspNetCore.Configuration.Secrets` 实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-240">The `Azure.Extensions.AspNetCore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="0e3ba-241">`Load` 当机密名称以前缀开头时，加载密码。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-241">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="0e3ba-242">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-242">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="0e3ba-243">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="0e3ba-243">`GetKey`:</span></span>
  * <span data-ttu-id="0e3ba-244">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-244">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="0e3ba-245">将任何名称中的两个短划线替换为 `KeyDelimiter` ，它是在配置 (中使用的分隔符，通常为冒号) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-245">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="0e3ba-246">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-246">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="0e3ba-247">`Load`方法由提供程序算法调用，该算法循环访问保管库机密以查找具有版本前缀的文件。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-247">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="0e3ba-248">如果找到了版本前缀 `Load` ，则该算法将使用 `GetKey` 方法来返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-248">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="0e3ba-249">它从机密名称中去除版本前缀，并返回密钥名称的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-249">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="0e3ba-250">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-250">When this approach is implemented:</span></span>

1. <span data-ttu-id="0e3ba-251">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-251">The app's version specified in the app's project file.</span></span> <span data-ttu-id="0e3ba-252">在以下示例中，应用的版本设置为 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-252">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="0e3ba-253">确认 `<UserSecretsId>` 应用的项目文件中存在属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-253">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="0e3ba-254">用 [机密管理器工具](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-254">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="0e3ba-255">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-255">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="0e3ba-256">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-256">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="0e3ba-257">的字符串机密与 `5000-AppSecret` 应用程序的项目文件中指定的应用版本匹配 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-257">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="0e3ba-258">`5000`用破折号)  (的版本将从键名称中去除。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-258">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="0e3ba-259">在整个应用程序中，通过密钥读取配置会 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-259">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="0e3ba-260">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` ，并再次运行应用程序，则返回的机密值位于 `5.1.0.0_secret_value_dev` 开发环境和生产环境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-260">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="0e3ba-261">你还可以向 AddAzureKeyVault 提供自己的 <xref:Azure.Security.KeyVault.Secrets.SecretClient> 实现。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-261">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="0e3ba-262">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-262">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="0e3ba-263">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="0e3ba-263">Bind an array to a class</span></span>

<span data-ttu-id="0e3ba-264">提供程序能够将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-264">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="0e3ba-265">当从允许键包含冒号 () 分隔符的配置源中进行读取时 `:` ，将使用数字键段来区分组成数组 (`:0:` 、) 的键 `:1:` &hellip; `:{n}:` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-265">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="0e3ba-266">有关详细信息，请参阅 [配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-266">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="0e3ba-267">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-267">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="0e3ba-268">本主题中所述的方法使用双短划线 (`--`) 作为层次结构值 (节) 的分隔符。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-268">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="0e3ba-269">数组键存储在 Azure Key Vault 中 `--0--` ， (，，) 为双短划线和数值段 `--1--` &hellip; `--{n}--` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-269">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="0e3ba-270">检查 JSON 文件提供的以下 [Serilog](https://serilog.net/) 日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-270">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="0e3ba-271">在数组中定义了两个对象文本 `WriteTo` ，它们反映了两个 Serilog *接收器*，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-271">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="0e3ba-272">前面的 JSON 文件中所示的配置将存储在 Azure Key Vault 中，使用双划线 (`--`) 表示法和数值段：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-272">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="0e3ba-273">Key</span><span class="sxs-lookup"><span data-stu-id="0e3ba-273">Key</span></span> | <span data-ttu-id="0e3ba-274">值</span><span class="sxs-lookup"><span data-stu-id="0e3ba-274">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="0e3ba-275">重载机密</span><span class="sxs-lookup"><span data-stu-id="0e3ba-275">Reload secrets</span></span>

<span data-ttu-id="0e3ba-276">在调用之前会缓存机密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-276">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="0e3ba-277">在执行之前，应用程序不会考虑到密钥保管库中已过期、已禁用和更新的机密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-277">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="0e3ba-278">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="0e3ba-278">Disabled and expired secrets</span></span>

<span data-ttu-id="0e3ba-279">禁用和过期的机密会引发 <xref:Azure.RequestFailedException> 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-279">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="0e3ba-280">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-280">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0e3ba-281">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0e3ba-281">Troubleshoot</span></span>

<span data-ttu-id="0e3ba-282">当应用无法使用访问接口加载配置时，会将错误消息写入 [ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-282">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0e3ba-283">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-283">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="0e3ba-284">Azure Active Directory 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-284">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="0e3ba-285">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-285">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="0e3ba-286">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-286">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="0e3ba-287">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-287">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="0e3ba-288">在密钥保管库中，配置数据 (名称-值对) 错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-288">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="0e3ba-289">应用具有错误的密钥保管库名称 (`KeyVaultName`) 、Azure AD 应用程序 Id () 或 Azure AD () Azure AD () 的 `AzureADApplicationId` 证书指纹 `AzureADCertThumbprint` `AzureADDirectoryId` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-289">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="0e3ba-290">要尝试加载的值在应用中 (名称) 的配置密钥不正确。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-290">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="0e3ba-291">向密钥保管库添加应用的访问策略时，策略已创建，但未在 **访问策略** UI 中选择 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-291">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e3ba-292">其他资源</span><span class="sxs-lookup"><span data-stu-id="0e3ba-292">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="0e3ba-293">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="0e3ba-293">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="0e3ba-294">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="0e3ba-294">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="0e3ba-295">如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="0e3ba-295">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="0e3ba-296">KeyVaultClient 类</span><span class="sxs-lookup"><span data-stu-id="0e3ba-296">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="0e3ba-297">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="0e3ba-297">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="0e3ba-298">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="0e3ba-298">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0e3ba-299">本文档说明如何使用 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 配置提供程序从 Azure Key Vault 机密加载应用配置值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-299">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="0e3ba-300">Azure Key Vault 是一项基于云的服务，可帮助保护应用程序和服务使用的加密密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-300">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="0e3ba-301">将 Azure Key Vault 用于 ASP.NET Core 应用的常见方案包括：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-301">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="0e3ba-302">控制对敏感配置数据的访问。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-302">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="0e3ba-303">满足 FIPS 140-2 第2级验证的硬件安全模块在存储配置数据时 (HSM) 的要求。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-303">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="0e3ba-304">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0e3ba-304">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="0e3ba-305">包</span><span class="sxs-lookup"><span data-stu-id="0e3ba-305">Packages</span></span>

<span data-ttu-id="0e3ba-306">向Microsoft.Extensions.Configu 添加包引用 [ 。AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) 包。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-306">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="0e3ba-307">示例应用</span><span class="sxs-lookup"><span data-stu-id="0e3ba-307">Sample app</span></span>

<span data-ttu-id="0e3ba-308">该示例应用在由 `#define` *Program.cs* 文件顶部的语句确定的两种模式中运行：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-308">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="0e3ba-309">`Certificate`：演示如何使用 Azure Key Vault 的客户端 ID 和 x.509 证书来访问存储在 Azure Key Vault 中的机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-309">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="0e3ba-310">可以从部署到 Azure App Service 的任何位置运行此版本的示例，也可以从部署到 ASP.NET Core 应用程序的任何主机上运行。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-310">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="0e3ba-311">`Managed`：演示如何使用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview) 对应用进行身份验证，以通过 Azure AD 身份验证 Azure Key Vault，而不会在应用的代码或配置中存储凭据。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-311">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="0e3ba-312">使用托管标识进行身份验证时，不需要 Azure AD 的应用程序 ID 和密码 (客户端密钥) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-312">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="0e3ba-313">`Managed`必须将示例的版本部署到 Azure。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-313">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="0e3ba-314">按照 [使用 Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="0e3ba-315">有关如何使用预处理器指令 () 配置示例应用的详细信息 `#define` ，请参阅 <xref:index#preprocessor-directives-in-sample-code> 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-315">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="0e3ba-316">开发环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="0e3ba-316">Secret storage in the Development environment</span></span>

<span data-ttu-id="0e3ba-317">使用 [机密管理器工具](xref:security/app-secrets)在本地设置机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-317">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="0e3ba-318">在开发环境中的本地计算机上运行示例应用时，会从本地用户机密存储区中加载机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-318">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="0e3ba-319">机密管理器工具需要 `<UserSecretsId>` 应用的项目文件中的属性。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-319">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="0e3ba-320">将属性值 (`{GUID}`) 设置为任何唯一 GUID：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-320">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="0e3ba-321">机密以名称-值对的形式创建。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-321">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="0e3ba-322">配置节 (层次结构值) `:` 在 [ASP.NET Core 配置](xref:fundamentals/configuration/index) 项名称中将 (冒号) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-322">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="0e3ba-323">机密管理器用于从打开的命令 shell 到项目的 [内容根目录](xref:fundamentals/index#content-root)，其中 `{SECRET NAME}` 是名称， `{SECRET VALUE}` 是值：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-323">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="0e3ba-324">从项目的 [内容根](xref:fundamentals/index#content-root) 在命令外壳中执行以下命令，为示例应用设置机密：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-324">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="0e3ba-325">如果这些机密存储在 [生产环境中的机密存储](#secret-storage-in-the-production-environment-with-azure-key-vault) Azure Key Vault 中，并 Azure Key Vault 部分，则 `_dev` 后缀将更改为 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-325">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="0e3ba-326">后缀在应用的输出中提供视觉提示，指示配置值的源。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-326">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="0e3ba-327">Azure Key Vault 中的生产环境中的机密存储</span><span class="sxs-lookup"><span data-stu-id="0e3ba-327">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="0e3ba-328">本 [快速入门：使用 Azure CLI 主题的 Azure Key Vault 设置和检索机密](/azure/key-vault/quick-create-cli) 的说明，请参阅此处，以创建 Azure Key Vault 和存储示例应用使用的机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-328">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="0e3ba-329">有关更多详细信息，请参阅主题。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-329">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="0e3ba-330">使用 [Azure 门户](https://portal.azure.com/)中的以下方法之一打开 Azure Cloud shell：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-330">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="0e3ba-331">选择代码块右上角的“试用”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-331">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="0e3ba-332">在文本框中使用搜索字符串 "Azure CLI"。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-332">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="0e3ba-333">在浏览器中打开 Cloud Shell，并提供 " **启动 Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-333">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="0e3ba-334">选择 Azure 门户右上角菜单中的 " **Cloud Shell** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-334">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="0e3ba-335">有关详细信息，请参阅 Azure Cloud Shell [Azure CLI](/cli/azure/) 和 [概述](/azure/cloud-shell/overview)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-335">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="0e3ba-336">如果尚未通过身份验证，请在命令中登录 `az login` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-336">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="0e3ba-337">使用以下命令创建资源组，其中 `{RESOURCE GROUP NAME}` 是新资源组的资源组名称， `{LOCATION}` 是 Azure 区域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-337">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="0e3ba-338">使用以下命令在资源组中创建密钥保管库，其中 `{KEY VAULT NAME}` 是新密钥保管库的名称， `{LOCATION}` 是 Azure 区域 (datacenter) ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-338">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="0e3ba-339">在密钥保管库中，以名称-值对的形式创建机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-339">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="0e3ba-340">Azure Key Vault 机密名称仅限字母数字字符和短划线。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-340">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="0e3ba-341">配置节 (层次结构值) 使用 `--` (两个短划线) 作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-341">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="0e3ba-342">冒号（通常用于在 [ASP.NET Core 配置](xref:fundamentals/configuration/index)中的子项中分隔部分）在 key vault 机密名称中不允许使用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-342">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="0e3ba-343">因此，当机密加载到应用的配置中时，将使用两个短划线并为冒号交换。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-343">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="0e3ba-344">以下机密用于示例应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-344">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="0e3ba-345">这些值包含 `_prod` 后缀，以将其与 `_dev` 从用户机密的开发环境中加载的后缀值区分开来。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-345">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="0e3ba-346">将替换 `{KEY VAULT NAME}` 为在上一步中创建的密钥保管库的名称：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-346">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="0e3ba-347">为非 Azure 托管的应用使用应用程序 ID 和 x.509 证书</span><span class="sxs-lookup"><span data-stu-id="0e3ba-347">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="0e3ba-348">配置 Azure AD、Azure Key Vault 和应用，以便在 **Azure 外托管应用时** 使用 Azure Active Directory 应用程序 ID 和 x.509 证书对密钥保管库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-348">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="0e3ba-349">有关详细信息，请参阅[关于密钥、机密和证书](/azure/key-vault/about-keys-secrets-and-certificates)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-349">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="0e3ba-350">尽管在 Azure 中托管的应用程序支持使用应用程序 ID 和 x.509 证书，但在 Azure 中托管应用程序时，我们建议使用 [Azure 资源的托管标识](#use-managed-identities-for-azure-resources) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-350">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="0e3ba-351">托管标识不需要在应用或开发环境中存储证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-351">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="0e3ba-352">当 `#define` *Program.cs* 文件顶部的语句设置为时，示例应用使用应用程序 ID 和 x.509 证书 `Certificate` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-352">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="0e3ba-353">创建 PKCS # 12 存档 (*.pfx*) 证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-353">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="0e3ba-354">用于创建证书的选项包括 Windows 和[OpenSSL](https://www.openssl.org/)[上的 MakeCert](/windows/desktop/seccrypto/makecert) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-354">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="0e3ba-355">将证书安装到当前用户的个人证书存储中。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-355">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="0e3ba-356">将密钥标记为可导出是可选的。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-356">Marking the key as exportable is optional.</span></span> <span data-ttu-id="0e3ba-357">记下证书的指纹，此过程将在此过程中使用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-357">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="0e3ba-358">将 PKCS # 12 存档 (*.pfx*) 证书导 () *.cer* 编码的证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-358">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="0e3ba-359">将应用注册 Azure AD (**应用注册**) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-359">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="0e3ba-360">将 DER 编码的证书 (*.cer*) 上传到 Azure AD：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-360">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="0e3ba-361">在 Azure AD 中选择应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-361">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="0e3ba-362">导航到 " **证书" & "机密**"。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-362">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="0e3ba-363">选择 " **上传证书** "，上传包含公钥的证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-363">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="0e3ba-364">*.Cer*、 *pem* 或 *.crt* 证书是可接受的。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-364">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="0e3ba-365">在应用的文件中存储密钥保管库名称、应用程序 ID 和证书指纹 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-365">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="0e3ba-366">导航到 Azure 门户中的 **密钥保管库** 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-366">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="0e3ba-367">选择在 [生产环境中的机密存储中](#secret-storage-in-the-production-environment-with-azure-key-vault) 创建的密钥保管库，其中 Azure Key Vault "部分。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-367">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="0e3ba-368">选择“访问策略”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-368">Select **Access policies**.</span></span>
1. <span data-ttu-id="0e3ba-369">选择“添加访问策略”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-369">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="0e3ba-370">打开 **机密权限** ，并为应用提供 **Get** 和 **List** 权限。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-370">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="0e3ba-371">选择 " **选择主体** "，并按名称选择注册的应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-371">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="0e3ba-372">选择“选择”按钮  。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-372">Select the **Select** button.</span></span>
1. <span data-ttu-id="0e3ba-373">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-373">Select **OK**.</span></span>
1. <span data-ttu-id="0e3ba-374">选择“保存”。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-374">Select **Save**.</span></span>
1. <span data-ttu-id="0e3ba-375">部署应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-375">Deploy the app.</span></span>

<span data-ttu-id="0e3ba-376">`Certificate`示例应用从中获取其配置值，其 `IConfigurationRoot` 名称与机密名称相同：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-376">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="0e3ba-377">非分层值：通过获取的值 `SecretName` `config["SecretName"]` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-377">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="0e3ba-378">) 部分 (层次结构值：使用 `:` (冒号) 表示法或 `GetSection` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-378">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="0e3ba-379">使用以下任一方法来获取配置值：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-379">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="0e3ba-380">X.509 证书由操作系统管理。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-380">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="0e3ba-381">应用 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用文件提供的值调用 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-381">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="0e3ba-382">示例值：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-382">Example values:</span></span>

* <span data-ttu-id="0e3ba-383">密钥保管库名称： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="0e3ba-383">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="0e3ba-384">应用程序 ID： `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="0e3ba-384">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="0e3ba-385">证书指纹： `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="0e3ba-385">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="0e3ba-386">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0e3ba-386">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="0e3ba-387">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-387">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="0e3ba-388">在开发环境中，机密值用后缀进行加载 `_dev` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-388">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="0e3ba-389">在生产环境中，值以后缀进行加载 `_prod` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-389">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="0e3ba-390">使用 Azure 资源的托管标识</span><span class="sxs-lookup"><span data-stu-id="0e3ba-390">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="0e3ba-391">**部署到 azure 的应用** 可以利用 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，这允许应用使用 Azure AD 身份验证（不含凭据 (应用程序 ID 和密码/客户端密钥) 存储在应用中）对 Azure Key Vault 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-391">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="0e3ba-392">当 `#define` *Program.cs* 文件顶部的语句设置为时，示例应用使用 Azure 资源的托管标识 `Managed` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-392">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="0e3ba-393">在应用的文件中输入保管库名称 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-393">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="0e3ba-394">当设置为版本时，示例应用不需要应用程序 ID 和密码 (的客户端机密) `Managed` ，因此你可以忽略这些配置条目。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-394">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="0e3ba-395">应用将部署到 Azure，Azure 将对应用进行身份验证，以便仅使用存储在文件中的保管库名称访问 Azure Key Vault *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-395">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="0e3ba-396">将示例应用部署到 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-396">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="0e3ba-397">在创建服务时，会自动向 Azure AD 注册部署到 Azure App Service 的应用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-397">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="0e3ba-398">从部署中获取对象 ID 以在下面的命令中使用。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-398">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="0e3ba-399">对象 ID 显示在应用服务面板上的 "Azure 门户中 **Identity** 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-399">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="0e3ba-400">使用 Azure CLI 和应用程序的对象 ID，为应用程序提供 `list` `get` 访问密钥保管库的和权限：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-400">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="0e3ba-401">使用 Azure CLI、PowerShell 或 Azure 门户 **重启应用**。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-401">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="0e3ba-402">示例应用：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-402">The sample app:</span></span>

* <span data-ttu-id="0e3ba-403">创建 `AzureServiceTokenProvider` 不带连接字符串的类的实例。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-403">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="0e3ba-404">如果未提供连接字符串，提供程序将尝试从 Azure 资源的托管标识获取访问令牌。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-404">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="0e3ba-405"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>使用 `AzureServiceTokenProvider` 实例标记回调创建新的。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-405">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="0e3ba-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>实例与加载所有机密值的的默认实现一起使用， <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 并将 `--` 冒号 () 替换为 `:` 键名称中 () 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-406">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="0e3ba-407">Key vault 名称示例值： `contosovault`</span><span class="sxs-lookup"><span data-stu-id="0e3ba-407">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="0e3ba-408">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0e3ba-408">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="0e3ba-409">运行应用时，网页将显示加载的机密值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-409">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="0e3ba-410">在开发环境中，机密值具有 `_dev` 后缀，因为它们是由用户机密提供的。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-410">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="0e3ba-411">在生产环境中，值加载时使用 `_prod` 后缀，因为它们由 Azure Key Vault 提供。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-411">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="0e3ba-412">如果收到 `Access denied` 错误，请确认该应用已注册到 Azure AD，并提供对密钥保管库的访问权限。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-412">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="0e3ba-413">确认已在 Azure 中重新启动该服务。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-413">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="0e3ba-414">有关将提供程序与托管标识和 Azure DevOps 管道一起使用的信息，请参阅使用 [托管服务标识创建与 VM 的 Azure 资源管理器服务连接](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-414">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="0e3ba-415">使用密钥名称前缀</span><span class="sxs-lookup"><span data-stu-id="0e3ba-415">Use a key name prefix</span></span>

<span data-ttu-id="0e3ba-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 提供一个重载，该重载接受的实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ，该实现允许您控制密钥保管库机密如何转换为配置密钥。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="0e3ba-417">例如，你可以实现接口，以便基于在应用启动时提供的前缀值加载机密值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-417">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="0e3ba-418">例如，你可以根据应用程序的版本加载机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-418">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="0e3ba-419">不要对 key vault 机密使用前缀，将多个应用的机密放入同一密钥保管库，或放置环境机密 (例如， *开发* 与 *生产* 机密) 到同一保管库中。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-419">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="0e3ba-420">建议不同的应用和开发/生产环境使用不同的密钥保管库，以便隔离应用环境以获得最高级别的安全性。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-420">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="0e3ba-421">在下面的示例中，密钥保管库中建立了机密 (和使用开发环境的机密管理器工具) 对于 `5000-AppSecret` 密钥保管库机密名称) 中不允许使用 (期间。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-421">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="0e3ba-422">此机密代表应用程序版本5.0.0.0 的应用程序机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-422">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="0e3ba-423">对于其他版本的应用，5.1.0.0 会将机密添加到密钥保管库 (并使用机密管理器工具) `5100-AppSecret` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-423">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="0e3ba-424">每个应用版本会将其版本控制的机密值加载到其配置中，并在 `AppSecret` 加载机密时去除版本。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-424">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="0e3ba-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 使用自定义调用 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="0e3ba-426">该 <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> 实现将对机密的版本前缀做出反应，以将适当的机密加载到配置中：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-426">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="0e3ba-427">`Load` 当机密名称以前缀开头时，加载密码。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-427">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="0e3ba-428">未加载其他机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-428">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="0e3ba-429">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="0e3ba-429">`GetKey`:</span></span>
  * <span data-ttu-id="0e3ba-430">从机密名称中删除前缀。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-430">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="0e3ba-431">将任何名称中的两个短划线替换为 `KeyDelimiter` ，它是在配置 (中使用的分隔符，通常为冒号) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-431">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="0e3ba-432">Azure Key Vault 不允许在机密名称中使用冒号。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-432">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="0e3ba-433">`Load`方法由提供程序算法调用，该算法循环访问保管库机密以查找具有版本前缀的文件。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-433">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="0e3ba-434">如果找到了版本前缀 `Load` ，则该算法将使用 `GetKey` 方法来返回密钥名称的配置名称。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-434">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="0e3ba-435">它从机密名称中去除版本前缀，并返回密钥名称的其余部分，以便加载到应用的配置名称-值对中。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-435">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="0e3ba-436">实现此方法时：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-436">When this approach is implemented:</span></span>

1. <span data-ttu-id="0e3ba-437">应用的项目文件中指定的应用版本。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-437">The app's version specified in the app's project file.</span></span> <span data-ttu-id="0e3ba-438">在以下示例中，应用的版本设置为 `5.0.0.0` ：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-438">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="0e3ba-439">确认 `<UserSecretsId>` 应用的项目文件中存在属性，其中 `{GUID}` 是用户提供的 GUID：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-439">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="0e3ba-440">用 [机密管理器工具](xref:security/app-secrets)本地保存以下机密：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-440">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="0e3ba-441">使用以下 Azure CLI 命令在 Azure Key Vault 中保存机密：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-441">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="0e3ba-442">当应用运行时，将加载密钥保管库机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-442">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="0e3ba-443">的字符串机密与 `5000-AppSecret` 应用程序的项目文件中指定的应用版本匹配 (`5.0.0.0`) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-443">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="0e3ba-444">`5000`用破折号)  (的版本将从键名称中去除。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-444">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="0e3ba-445">在整个应用程序中，通过密钥读取配置会 `AppSecret` 加载机密值。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-445">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="0e3ba-446">如果在项目文件中将应用程序的版本更改为 `5.1.0.0` ，并再次运行应用程序，则返回的机密值位于 `5.1.0.0_secret_value_dev` 开发环境和生产环境中 `5.1.0.0_secret_value_prod` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-446">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="0e3ba-447">你还可以向提供自己的 <xref:Microsoft.Azure.KeyVault.KeyVaultClient> 实现 <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-447">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="0e3ba-448">自定义客户端允许跨应用共享客户端的单个实例。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-448">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="0e3ba-449">将数组绑定至类</span><span class="sxs-lookup"><span data-stu-id="0e3ba-449">Bind an array to a class</span></span>

<span data-ttu-id="0e3ba-450">提供程序能够将配置值读入数组，以便绑定到 POCO 数组。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-450">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="0e3ba-451">当从允许键包含冒号 () 分隔符的配置源中进行读取时 `:` ，将使用数字键段来区分组成数组 (`:0:` 、) 的键 `:1:` &hellip; `:{n}:` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-451">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="0e3ba-452">有关详细信息，请参阅 [配置：将数组绑定到类](xref:fundamentals/configuration/index#bind-an-array-to-a-class)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-452">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="0e3ba-453">Azure Key Vault 密钥不能使用冒号作为分隔符。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-453">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="0e3ba-454">本主题中所述的方法使用双短划线 (`--`) 作为层次结构值 (节) 的分隔符。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-454">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="0e3ba-455">数组键存储在 Azure Key Vault 中 `--0--` ， (，，) 为双短划线和数值段 `--1--` &hellip; `--{n}--` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-455">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="0e3ba-456">检查 JSON 文件提供的以下 [Serilog](https://serilog.net/) 日志记录提供程序配置。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-456">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="0e3ba-457">在数组中定义了两个对象文本 `WriteTo` ，它们反映了两个 Serilog *接收器*，它们描述了日志记录输出的目标：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-457">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="0e3ba-458">前面的 JSON 文件中所示的配置将存储在 Azure Key Vault 中，使用双划线 (`--`) 表示法和数值段：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-458">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="0e3ba-459">Key</span><span class="sxs-lookup"><span data-stu-id="0e3ba-459">Key</span></span> | <span data-ttu-id="0e3ba-460">值</span><span class="sxs-lookup"><span data-stu-id="0e3ba-460">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="0e3ba-461">重载机密</span><span class="sxs-lookup"><span data-stu-id="0e3ba-461">Reload secrets</span></span>

<span data-ttu-id="0e3ba-462">在调用之前会缓存机密 `IConfigurationRoot.Reload()` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-462">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="0e3ba-463">在执行之前，应用程序不会考虑到密钥保管库中已过期、已禁用和更新的机密 `Reload` 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-463">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="0e3ba-464">禁用和过期的机密</span><span class="sxs-lookup"><span data-stu-id="0e3ba-464">Disabled and expired secrets</span></span>

<span data-ttu-id="0e3ba-465">禁用和过期的机密会引发 <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-465">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="0e3ba-466">若要防止应用程序引发，请使用不同的配置提供程序提供配置，或更新禁用或过期的机密。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-466">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0e3ba-467">疑难解答</span><span class="sxs-lookup"><span data-stu-id="0e3ba-467">Troubleshoot</span></span>

<span data-ttu-id="0e3ba-468">当应用无法使用访问接口加载配置时，会将错误消息写入 [ASP.NET Core 日志记录基础结构](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-468">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0e3ba-469">以下条件将阻止加载配置：</span><span class="sxs-lookup"><span data-stu-id="0e3ba-469">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="0e3ba-470">Azure Active Directory 中未正确配置应用或证书。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-470">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="0e3ba-471">Azure Key Vault 中不存在密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-471">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="0e3ba-472">应用无权访问密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-472">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="0e3ba-473">访问策略不包括 `Get` 和 `List` 权限。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-473">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="0e3ba-474">在密钥保管库中，配置数据 (名称-值对) 错误地命名、缺失、禁用或过期。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-474">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="0e3ba-475">应用具有错误的密钥保管库名称 (`KeyVaultName`) 、Azure AD 应用程序 Id (`AzureADApplicationId`) 或 Azure AD 证书指纹 (`AzureADCertThumbprint`) 。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-475">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="0e3ba-476">要尝试加载的值在应用中 (名称) 的配置密钥不正确。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-476">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="0e3ba-477">向密钥保管库添加应用的访问策略时，策略已创建，但未在 **访问策略** UI 中选择 "**保存**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="0e3ba-477">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e3ba-478">其他资源</span><span class="sxs-lookup"><span data-stu-id="0e3ba-478">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="0e3ba-479">Microsoft Azure： Key Vault</span><span class="sxs-lookup"><span data-stu-id="0e3ba-479">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="0e3ba-480">Microsoft Azure： Key Vault 文档</span><span class="sxs-lookup"><span data-stu-id="0e3ba-480">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="0e3ba-481">如何为 Azure Key Vault 生成和传输受 HSM 保护的密钥</span><span class="sxs-lookup"><span data-stu-id="0e3ba-481">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="0e3ba-482">KeyVaultClient 类</span><span class="sxs-lookup"><span data-stu-id="0e3ba-482">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="0e3ba-483">快速入门：使用 .NET Web 应用在 Azure Key Vault 中设置和检索机密</span><span class="sxs-lookup"><span data-stu-id="0e3ba-483">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="0e3ba-484">教程：如何将 Azure Key Vault 与通过 .NET 编写的 Azure Windows 虚拟机配合使用</span><span class="sxs-lookup"><span data-stu-id="0e3ba-484">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
