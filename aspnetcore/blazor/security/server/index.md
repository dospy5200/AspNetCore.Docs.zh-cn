---
title: 保护 ASP.NET Core Blazor Server应用
author: guardrex
description: 了解如何将 Blazor Server应用作为 ASP.NET Core 应用来保护。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 41b588acdef3eedd9fc081f50040d160147bab4b
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109645"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="c34ed-103">保护 ASP.NET Core Blazor Server应用</span><span class="sxs-lookup"><span data-stu-id="c34ed-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="c34ed-104">Blazor Server应用的安全配置方式与 ASP.NET Core 应用相同。</span><span class="sxs-lookup"><span data-stu-id="c34ed-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="c34ed-105">有关详细信息，请参阅 <xref:security/index> 下的文章。</span><span class="sxs-lookup"><span data-stu-id="c34ed-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="c34ed-106">此“概述”下的主题特别适用于 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="c34ed-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="c34ed-107">Blazor Server项目模板</span><span class="sxs-lookup"><span data-stu-id="c34ed-107">Blazor Server project template</span></span>

<span data-ttu-id="c34ed-108">创建项目后，可配置 Blazor Server项目模板来进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="c34ed-108">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c34ed-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c34ed-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c34ed-110">请按照 <xref:blazor/tooling> 中的 Visual Studio 指南操作，新建具有身份验证机制的 Blazor Server项目。</span><span class="sxs-lookup"><span data-stu-id="c34ed-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="c34ed-111">在“新建 ASP.NET Core Web 应用”对话框中选择“Blazor Server应用”模板后，选择“身份验证”下的“更改”。</span><span class="sxs-lookup"><span data-stu-id="c34ed-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="c34ed-112">此时将打开一个对话框，为其他 ASP.NET Core 项目提供一组相同的身份验证机制：</span><span class="sxs-lookup"><span data-stu-id="c34ed-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="c34ed-113">**无身份验证**</span><span class="sxs-lookup"><span data-stu-id="c34ed-113">**No Authentication**</span></span>
* <span data-ttu-id="c34ed-114">**个人用户帐户**：可存储用户帐户：</span><span class="sxs-lookup"><span data-stu-id="c34ed-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="c34ed-115">在使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统的应用中存储。</span><span class="sxs-lookup"><span data-stu-id="c34ed-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="c34ed-116">使用 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 存储。</span><span class="sxs-lookup"><span data-stu-id="c34ed-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="c34ed-117">**工作或学校帐户**</span><span class="sxs-lookup"><span data-stu-id="c34ed-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="c34ed-118">**Windows 身份验证**</span><span class="sxs-lookup"><span data-stu-id="c34ed-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c34ed-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c34ed-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c34ed-120">请按照 <xref:blazor/tooling> 中的 Visual Studio Code 指南操作，新建具有身份验证机制的 Blazor Server项目：</span><span class="sxs-lookup"><span data-stu-id="c34ed-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c34ed-121">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="c34ed-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c34ed-122">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="c34ed-122">Authentication mechanism</span></span> | <span data-ttu-id="c34ed-123">描述</span><span class="sxs-lookup"><span data-stu-id="c34ed-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c34ed-124">`None`（默认值）</span><span class="sxs-lookup"><span data-stu-id="c34ed-124">`None` (default)</span></span>         | <span data-ttu-id="c34ed-125">无身份验证</span><span class="sxs-lookup"><span data-stu-id="c34ed-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c34ed-126">使用 ASP.NET Core Identity 将用户存储在应用中</span><span class="sxs-lookup"><span data-stu-id="c34ed-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c34ed-127">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中</span><span class="sxs-lookup"><span data-stu-id="c34ed-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c34ed-128">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="c34ed-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c34ed-129">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="c34ed-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c34ed-130">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="c34ed-130">Windows Authentication</span></span> |

<span data-ttu-id="c34ed-131">使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c34ed-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c34ed-132">为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="c34ed-132">Create a folder for the project.</span></span>
* <span data-ttu-id="c34ed-133">为该项目命名。</span><span class="sxs-lookup"><span data-stu-id="c34ed-133">Name the project.</span></span>

<span data-ttu-id="c34ed-134">有关详细信息，请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="c34ed-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c34ed-135">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c34ed-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c34ed-136">请按照 <xref:blazor/tooling> 中的 Visual Studio for Mac 指南进行操作。</span><span class="sxs-lookup"><span data-stu-id="c34ed-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="c34ed-137">在“配置新的 Blazor Server应用”步骤中，从“身份验证”下拉列表中选择“个人身份验证(应用内)”。</span><span class="sxs-lookup"><span data-stu-id="c34ed-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="c34ed-138">此应用是使用 ASP.NET Core Identity 为应用中存储的个人用户创建的。</span><span class="sxs-lookup"><span data-stu-id="c34ed-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c34ed-139">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c34ed-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="c34ed-140">在命令行界面中使用以下命令，新建具有身份验证机制的 Blazor Server项目：</span><span class="sxs-lookup"><span data-stu-id="c34ed-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="c34ed-141">下表中显示了可能的身份验证值 (`{AUTHENTICATION}`)。</span><span class="sxs-lookup"><span data-stu-id="c34ed-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="c34ed-142">身份验证机制</span><span class="sxs-lookup"><span data-stu-id="c34ed-142">Authentication mechanism</span></span> | <span data-ttu-id="c34ed-143">描述</span><span class="sxs-lookup"><span data-stu-id="c34ed-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="c34ed-144">`None`（默认值）</span><span class="sxs-lookup"><span data-stu-id="c34ed-144">`None` (default)</span></span>         | <span data-ttu-id="c34ed-145">无身份验证</span><span class="sxs-lookup"><span data-stu-id="c34ed-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="c34ed-146">使用 ASP.NET Core Identity 将用户存储在应用中</span><span class="sxs-lookup"><span data-stu-id="c34ed-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="c34ed-147">用户存储在 [Azure AD B2C](xref:security/authentication/azure-ad-b2c) 中</span><span class="sxs-lookup"><span data-stu-id="c34ed-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="c34ed-148">对一个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="c34ed-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="c34ed-149">对多个租户进行组织身份验证</span><span class="sxs-lookup"><span data-stu-id="c34ed-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="c34ed-150">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="c34ed-150">Windows Authentication</span></span> |

<span data-ttu-id="c34ed-151">使用 `-o|--output` 选项，该命令使用为 `{APP NAME}` 占位符提供的值来执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c34ed-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="c34ed-152">为项目创建文件夹。</span><span class="sxs-lookup"><span data-stu-id="c34ed-152">Create a folder for the project.</span></span>
* <span data-ttu-id="c34ed-153">为该项目命名。</span><span class="sxs-lookup"><span data-stu-id="c34ed-153">Name the project.</span></span>

<span data-ttu-id="c34ed-154">更多相关信息：</span><span class="sxs-lookup"><span data-stu-id="c34ed-154">For more information:</span></span>

* <span data-ttu-id="c34ed-155">请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="c34ed-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="c34ed-156">在命令行界面中为 Blazor Server模板 (`blazorserver`) 执行 help 命令：</span><span class="sxs-lookup"><span data-stu-id="c34ed-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="c34ed-157">设置Identity的基架</span><span class="sxs-lookup"><span data-stu-id="c34ed-157">Scaffold Identity</span></span>

<span data-ttu-id="c34ed-158">将 Identity 架构到 Blazor Server项目中：</span><span class="sxs-lookup"><span data-stu-id="c34ed-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="c34ed-159">[当前没有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)。</span><span class="sxs-lookup"><span data-stu-id="c34ed-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="c34ed-160">[有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="c34ed-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-claims-and-tokens-from-external-providers"></a><span data-ttu-id="c34ed-161">来自外部提供程序的额外声明和令牌</span><span class="sxs-lookup"><span data-stu-id="c34ed-161">Additional claims and tokens from external providers</span></span>

<span data-ttu-id="c34ed-162">若要存储来自外部提供程序的额外声明，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="c34ed-162">To store additional claims from external providers, see <xref:security/authentication/social/additional-claims>.</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="c34ed-163">使用 Identity 服务器的 Linux 上的 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="c34ed-163">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="c34ed-164">使用 Identity 服务器部署到 Linux 上的 Azure 应用服务时，显式指定颁发者。</span><span class="sxs-lookup"><span data-stu-id="c34ed-164">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="c34ed-165">有关详细信息，请参阅 <xref:security/authentication/identity/spa#azure-app-service-on-linux>。</span><span class="sxs-lookup"><span data-stu-id="c34ed-165">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c34ed-166">其他资源</span><span class="sxs-lookup"><span data-stu-id="c34ed-166">Additional resources</span></span>

* [<span data-ttu-id="c34ed-167">快速入门：将 Microsoft 登录添加到 ASP.NET Core Web 应用</span><span class="sxs-lookup"><span data-stu-id="c34ed-167">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="c34ed-168">快速入门：使用 Microsoft 标识平台保护 ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="c34ed-168">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="c34ed-169"><xref:host-and-deploy/proxy-load-balancer>：包含有关以下内容的指导：</span><span class="sxs-lookup"><span data-stu-id="c34ed-169"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="c34ed-170">使用转接头中间件跨代理服务器和内部网络保留 HTTPS 方案信息。</span><span class="sxs-lookup"><span data-stu-id="c34ed-170">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="c34ed-171">其他方案和用例，包括手动方案配置、请求路径更改以进行正确请求路由，以及转发适用于 Linux 和非 IIS 反向代理的请求方案。</span><span class="sxs-lookup"><span data-stu-id="c34ed-171">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
