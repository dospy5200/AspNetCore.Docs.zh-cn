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
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core 中的开发中安全存储应用机密

::: moniker range=">= aspnetcore-3.0"

作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)、 [Daniel Roth](https://github.com/danroth27)和 [Scott Addie](https://github.com/scottaddie)

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）

本文档介绍如何管理开发计算机上的 ASP.NET Core 应用程序的敏感数据。 切勿在源代码中存储密码或其他敏感数据。 生产机密不应用于开发或测试。 机密不应与应用一起部署。 相反，应通过受控的方式（如环境变量或 Azure Key Vault）访问生产机密。 可使用 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。

## <a name="environment-variables"></a>环境变量

环境变量用于避免在代码中或在本地配置文件中存储应用程序机密。 环境变量会重写所有以前指定的配置源的配置值。

请考虑一个 ASP.NET Core web 应用，其中启用了 **单个用户帐户** 安全。 带有密钥的项目文件中包含默认的数据库连接字符串 *appsettings.json* `DefaultConnection` 。 默认连接字符串用于 LocalDB，后者在用户模式下运行，不需要密码。 在应用程序部署过程中， `DefaultConnection` 可使用环境变量的值覆盖密钥值。 环境变量可以存储具有敏感凭据的完整连接字符串。

> [!WARNING]
> 环境变量通常以未加密的纯文本格式存储。 如果计算机或进程受到危害，则不受信任方可以访问环境变量。 可能需要其他措施来防止泄露用户机密。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>机密管理器

机密管理器工具在开发 ASP.NET Core 项目的过程中存储敏感数据。 在此上下文中，一段敏感数据是应用程序机密。 应用密钥存储在与项目树不同的位置。 应用程序机密与特定项目关联或在多个项目之间共享。 应用密码未签入源控件。

> [!WARNING]
> 机密管理器工具不会加密存储的机密，也不应将其视为受信任的存储区。 它仅用于开发目的。 密钥和值存储在用户配置文件目录中的 JSON 配置文件中。

## <a name="how-the-secret-manager-tool-works"></a>机密管理器工具的工作原理

机密管理器工具隐藏了实现的详细信息，例如存储值的位置和方式。 您可以使用该工具，而无需了解这些实现细节。 这些值存储在本地计算机的用户配置文件文件夹中的 JSON 文件中：

# <a name="windows"></a>Windows

文件系统路径：

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

文件系统路径：

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

在前面的文件路径中，将替换 `<user_secrets_id>` 为 `UserSecretsId` 项目文件中指定的值。

不要编写依赖于通过机密管理器工具保存的数据的位置或格式的代码。 这些实现细节可能会发生变化。 例如，机密值不会加密，但可能会在将来。

## <a name="enable-secret-storage"></a>启用密钥存储

机密管理器工具对存储在用户配置文件中的特定于项目的配置设置进行操作。

机密管理器工具 `init` 在 .NET Core SDK 3.0.100 或更高版本中包含命令。 若要使用用户机密，请在项目目录中运行以下命令：

```dotnetcli
dotnet user-secrets init
```

前面的命令将在 `UserSecretsId` 项目文件的中添加一个元素 `PropertyGroup` 。 默认情况下，的内部文本 `UserSecretsId` 是 GUID。 内部文本是任意的，但对项目是唯一的。

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

在 Visual Studio 中，右键单击 "解决方案资源管理器中的项目，然后从上下文菜单中选择" **管理用户机密** "。 此笔势将 `UserSecretsId` 使用 GUID 填充的元素添加到项目文件。

## <a name="set-a-secret"></a>设置机密

定义包含密钥及其值的应用密码。 机密与项目的值相关联 `UserSecretsId` 。 例如，从项目文件所在的目录运行以下命令：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

在前面的示例中，冒号表示 `Movies` 是具有属性的对象文本 `ServiceApiKey` 。

机密管理器工具也可用于其他目录。 使用 `--project` 选项可提供项目文件所在的文件系统路径。 例如：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio 中的 JSON 结构平展

Visual Studio 的 " **管理用户机密** " 手势在文本编辑器中打开文件的 *secrets.js* 。 将 *上secrets.js* 的内容替换为要存储的键值对。 例如：

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 结构是通过或进行修改后平展的 `dotnet user-secrets remove` `dotnet user-secrets set` 。 例如，运行会 `dotnet user-secrets remove "Movies:ConnectionString"` 折叠 `Movies` 对象文本。 修改后的文件类似于以下 JSON：

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>设置多个机密

可以通过管道 JSON 将密码批设置为 `set` 命令。 在下面的示例中，将文件的内容的 *input.js* 传递给 `set` 命令。

# <a name="windows"></a>Windows

打开命令 shell，然后执行以下命令：

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

打开命令 shell，然后执行以下命令：

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>访问机密

若要访问密钥，请完成以下步骤：

1. [注册用户机密配置源](#register-the-user-secrets-configuration-source)
1. [通过配置 API 读取机密](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a>注册用户机密配置源

用户机密 [配置提供程序](/dotnet/core/extensions/configuration-providers) 向 .NET [配置 API](xref:fundamentals/configuration/index)注册适当的配置源。

当项目调用时，用户机密配置源会自动以开发模式添加 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> 。 `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>当为时 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> 调用 <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> ：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

如果 `CreateDefaultBuilder` 未调用，则通过在中调用来显式添加用户机密配置源 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 。 `AddUserSecrets`仅在开发环境中运行应用时调用，如以下示例中所示：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a>通过配置 API 读取机密

如果注册了用户机密配置源，则 .NET 配置 API 可以读取机密。 [构造函数注入](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) 可用于获取对 .NET 配置 API 的访问。 请考虑以下读取密钥的示例 `Movies:ServiceApiKey` ：

**Startup 类：**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

**Razor 页面页面模型：**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

有关详细信息，请参阅 " [在启动](xref:fundamentals/configuration/index#access-configuration-in-startup) 和 [访问配置" Razor 页中](xref:fundamentals/configuration/index#access-configuration-in-razor-pages)的访问配置。

## <a name="map-secrets-to-a-poco"></a>将机密映射到 POCO

将整个对象文本映射到 POCO (具有属性) 的简单 .NET 类对于聚合相关属性十分有用。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

若要将上述机密映射到 POCO，请使用 .NET 配置 API 的 [对象图绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph) 功能。 下面的代码绑定到自定义 `MovieSettings` POCO 并访问 `ServiceApiKey` 属性值：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`和 `Movies:ServiceApiKey` 机密映射到中的相应属性 `MovieSettings` ：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>用机密替换字符串

以纯文本形式存储密码是不安全的。 例如，存储在中的数据库连接字符串 *appsettings.json* 可能包含指定用户的密码：

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

更安全的方法是将密码存储为机密。 例如：

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`从中的连接字符串中移除键值对 *appsettings.json* 。 例如：

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

可以对对象的属性设置机密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成连接字符串：

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>列出机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从项目文件所在的目录运行以下命令：

```dotnetcli
dotnet user-secrets list
```

随即显示以下输出：

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

在前面的示例中，键名称中的冒号表示 *secrets.js上* 的中的对象层次结构。

## <a name="remove-a-single-secret"></a>删除单个机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从项目文件所在的目录运行以下命令：

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

已修改文件 *上* 应用的secrets.js，以删除与密钥关联的键值对 `MoviesConnectionString` ：

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` 显示以下消息：

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>删除所有机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从项目文件所在的目录运行以下命令：

```dotnetcli
dotnet user-secrets clear
```

此应用的所有用户机密已从文件中的 *secrets.js* 删除：

```json
{}
```

运行 `dotnet user-secrets list` 将显示以下消息：

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>其他资源

* 有关从 IIS 访问用户机密的信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)和 [Scott Addie](https://github.com/scottaddie)

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/app-secrets/samples)（[如何下载](xref:index#how-to-download-a-sample)）

本文档介绍如何管理开发计算机上的 ASP.NET Core 应用程序的敏感数据。 切勿在源代码中存储密码或其他敏感数据。 生产机密不应用于开发或测试。 机密不应与应用一起部署。 相反，应通过受控的方式（如环境变量或 Azure Key Vault）访问生产机密。 可使用 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。

## <a name="environment-variables"></a>环境变量

环境变量用于避免在代码中或在本地配置文件中存储应用程序机密。 环境变量会重写所有以前指定的配置源的配置值。

请考虑一个 ASP.NET Core web 应用，其中启用了 **单个用户帐户** 安全。 带有密钥的项目文件中包含默认的数据库连接字符串 *appsettings.json* `DefaultConnection` 。 默认连接字符串用于 LocalDB，后者在用户模式下运行，不需要密码。 在应用程序部署过程中， `DefaultConnection` 可使用环境变量的值覆盖密钥值。 环境变量可以存储具有敏感凭据的完整连接字符串。

> [!WARNING]
> 环境变量通常以未加密的纯文本格式存储。 如果计算机或进程受到危害，则不受信任方可以访问环境变量。 可能需要其他措施来防止泄露用户机密。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>机密管理器

机密管理器工具在开发 ASP.NET Core 项目的过程中存储敏感数据。 在此上下文中，一段敏感数据是应用程序机密。 应用密钥存储在与项目树不同的位置。 应用程序机密与特定项目关联或在多个项目之间共享。 应用密码未签入源控件。

> [!WARNING]
> 机密管理器工具不会加密存储的机密，也不应将其视为受信任的存储区。 它仅用于开发目的。 密钥和值存储在用户配置文件目录中的 JSON 配置文件中。

## <a name="how-the-secret-manager-tool-works"></a>机密管理器工具的工作原理

机密管理器工具隐藏了实现的详细信息，例如存储值的位置和方式。 您可以使用该工具，而无需了解这些实现细节。 这些值存储在本地计算机的用户配置文件文件夹中的 JSON 文件中：

# <a name="windows"></a>Windows

文件系统路径：

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

文件系统路径：

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

在前面的文件路径中，将替换 `<user_secrets_id>` 为 `UserSecretsId` 项目文件中指定的值。

不要编写依赖于通过机密管理器工具保存的数据的位置或格式的代码。 这些实现细节可能会发生变化。 例如，机密值不会加密，但可能会在将来。

## <a name="enable-secret-storage"></a>启用密钥存储

机密管理器工具对存储在用户配置文件中的特定于项目的配置设置进行操作。

若要使用用户机密，请在 `UserSecretsId` 项目文件的中定义一个元素 `PropertyGroup` 。 的内部文本 `UserSecretsId` 是任意的，但对项目是唯一的。 开发人员通常会生成的 GUID `UserSecretsId` 。

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> 在 Visual Studio 中，右键单击 "解决方案资源管理器中的项目，然后从上下文菜单中选择" **管理用户机密** "。 此笔势将 `UserSecretsId` 使用 GUID 填充的元素添加到项目文件。

## <a name="set-a-secret"></a>设置机密

定义包含密钥及其值的应用密码。 机密与项目的值相关联 `UserSecretsId` 。 例如，从项目文件所在的目录运行以下命令：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

在前面的示例中，冒号表示 `Movies` 是具有属性的对象文本 `ServiceApiKey` 。

机密管理器工具也可用于其他目录。 使用 `--project` 选项可提供项目文件所在的文件系统路径。 例如：

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio 中的 JSON 结构平展

Visual Studio 的 " **管理用户机密** " 手势在文本编辑器中打开文件的 *secrets.js* 。 将 *上secrets.js* 的内容替换为要存储的键值对。 例如：

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 结构是通过或进行修改后平展的 `dotnet user-secrets remove` `dotnet user-secrets set` 。 例如，运行会 `dotnet user-secrets remove "Movies:ConnectionString"` 折叠 `Movies` 对象文本。 修改后的文件类似于以下 JSON：

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>设置多个机密

可以通过管道 JSON 将密码批设置为 `set` 命令。 在下面的示例中，将文件的内容的 *input.js* 传递给 `set` 命令。

# <a name="windows"></a>Windows

打开命令 shell，然后执行以下命令：

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

打开命令 shell，然后执行以下命令：

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>访问机密

[配置 API](xref:fundamentals/configuration/index)提供对用户机密的访问权限。

如果项目以 .NET Framework 为目标，请安装 [Microsoft.Extensions.Configu。UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 包。

在 ASP.NET Core 2.0 或更高版本中，当项目调用时，用户机密配置源会自动以开发模式添加 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> 。 `CreateDefaultBuilder`<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>当为时 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> 调用 <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> ：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

`CreateDefaultBuilder`不调用时，通过 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> 在构造函数中调用来显式添加用户机密配置源 `Startup` 。 `AddUserSecrets`仅在开发环境中运行应用时调用，如以下示例中所示：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

可以通过 .NET 配置 API 检索用户机密：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>将机密映射到 POCO

将整个对象文本映射到 POCO (具有属性) 的简单 .NET 类对于聚合相关属性十分有用。

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

若要将上述机密映射到 POCO，请使用 .NET 配置 API 的 [对象图绑定](xref:fundamentals/configuration/index#bind-to-an-object-graph) 功能。 下面的代码绑定到自定义 `MovieSettings` POCO 并访问 `ServiceApiKey` 属性值：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`和 `Movies:ServiceApiKey` 机密映射到中的相应属性 `MovieSettings` ：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>用机密替换字符串

以纯文本形式存储密码是不安全的。 例如，存储在中的数据库连接字符串 *appsettings.json* 可能包含指定用户的密码：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

更安全的方法是将密码存储为机密。 例如：

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

`Password`从中的连接字符串中移除键值对 *appsettings.json* 。 例如：

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

可以对对象的属性设置机密的值 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> ，以完成连接字符串：

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>列出机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从项目文件所在的目录运行以下命令：

```dotnetcli
dotnet user-secrets list
```

随即显示以下输出：

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

在前面的示例中，键名称中的冒号表示 *secrets.js上* 的中的对象层次结构。

## <a name="remove-a-single-secret"></a>删除单个机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从项目文件所在的目录运行以下命令：

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

已修改文件 *上* 应用的secrets.js，以删除与密钥关联的键值对 `MoviesConnectionString` ：

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

运行 `dotnet user-secrets list` 将显示以下消息：

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>删除所有机密

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

从项目文件所在的目录运行以下命令：

```dotnetcli
dotnet user-secrets clear
```

此应用的所有用户机密已从文件中的 *secrets.js* 删除：

```json
{}
```

运行 `dotnet user-secrets list` 将显示以下消息：

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>其他资源

* 有关从 IIS 访问用户机密的信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 。
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end