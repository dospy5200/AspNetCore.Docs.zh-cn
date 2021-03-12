---
title: ASP.NET Core Blazor WebAssembly 与 Azure Active Directory 组和角色
author: guardrex
description: 了解如何配置 Blazor WebAssembly 以使用 Azure Active Directory 组和角色。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: b725a60a310be23f7ceb626d4c543d0df6fadf62
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394754"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Azure Active Directory (AAD) 组、管理员角色和应用角色

Azure Active Directory (AAD) 提供了多种授权方法，它们可与 ASP.NET Core Identity 结合使用：

* 组
  * 安全性
  * Microsoft 365
  * 分布
* 角色
  * AAD 管理员角色
  * 应用角色

本文中的指南适用于以下主题中所述的 Blazor WebAssembly AAD 部署方案：

* [包含 Microsoft 帐户的独立产品](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [包含 AAD 的独立产品](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [由 AAD 托管](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

本文的指南提供了有关客户端和服务器应用的说明：

* CLIENT：独立 Blazor WebAssembly 应用或托管 Blazor 解决方案的 `Client` 应用。
* SERVER：独立 ASP.NET Core 服务器 API/Web API 应用或托管 Blazor 解决方案的 `Server` 应用。

## <a name="scopes"></a>作用域

若要允许 [Microsoft 图像 API](/graph/use-the-api) 调用用户配置文件、角色分配和组成员身份数据，需要在 Azure 门户中为 CLIENT 配置 (`https://graph.microsoft.com/User.Read`) [图像 API 权限（作用域）](/graph/permissions-reference)。

在 Azure 门户中，向为角色和组成员身份数据调用图形 API 的 SERVER 应用授予 `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [图形 API 权限（作用域）](/graph/permissions-reference)。

除了本文第一节中列出的主题所述的 AAD 部署方案中所需的作用域之外，还需要这些作用域。

> [!NOTE]
> “权限”和“作用域”术语在 Azure 门户和各种 Microsoft 和外部文档集中均可互换使用。 本文为针对在 Azure 门户中分配给应用的权限通篇使用“作用域”一词。

## <a name="group-membership-claims-attribute"></a>组成员身份声明属性

在 Azure 门户的 CLIENT 和 SERVER 应用的应用清单中，将 [`groupMembershipClaims` 属性](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)设置为 `All`。 如果值为 `All`，将获得已登录用户所属的所有安全组、通讯组和角色。

1. 打开应用的 Azure 门户注册。
1. 在边栏中选择“管理” > “清单”。
1. 查找 `groupMembershipClaims` 属性。
1. 将值设置为 `All`。
1. 选择“保存”按钮。

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>自定义用户帐户

将用户分配到 Azure 门户中的 AAD 安全组和 AAD 管理员角色。

本文中的示例：

* 假设将用户分配到 Azure 门户 AAD 租户中的 AAD 计费管理员角色，以授权访问服务器 API 数据。
* 使用[授权策略](xref:security/authorization/policies)在 CLIENT 和 SERVER 应用中控制访问。

在 CLIENT 应用中，将 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 扩展为包含以下属性：

* `Roles`：AAD 应用角色数组（包含在[应用角色](#app-roles)部分）
* `Wids`：[已知 ID 声明 (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims) 中的 AAD 管理员角色
* `Oid`：不可变[对象标识符声明 (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims)（唯一标识租户内和跨租户的用户）

为每个数组属性分配一个空数组，这样在 `foreach` 循环中使用这些属性时，无需再检查 `null`。

`CustomUserAccount.cs`:

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

向 [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) 的 CLIENT 应用项目文件添加包引用。

添加 <xref:blazor/security/webassembly/graph-api#graph-sdk> 一文中“Graph SDK”部分的 Graph SDK 实用工具类和配置。 在 `GraphClientExtensions` 类中，在 `AuthenticateRequestAsync` 方法中指定访问令牌的 `User.Read` 作用域：

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

将以下自定义用户帐户工厂添加到 CLIENT 应用。 自定义用户工厂用于建立以下内容：

* 应用角色数组 (`appRole`)（包含在[应用角色](#app-roles)部分）
* AAD 管理员角色声明 (`directoryRole`)
* 用户移动电话号码 (`mobilePhone`) 的用户配置文件数据声明示例
* AAD 组声明 (`directoryGroup`)

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

上面的代码不包含可传递的成员身份。 如果应用需要直接且可传递的组成员身份声明，请将 `MemberOf` 属性 (`IUserMemberOfCollectionWithReferencesRequestBuilder`) 替换为 `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`)。

前面的代码将忽略作为 AAD 管理员角色（`#microsoft.graph.directoryRole` 类型）的组成员身份声明 (`groups`)，因为 Microsoft Identity Platform 2.0 返回的 GUID 值为 AAD 管理员角色实体 ID 而不是[角色模板 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)。 实体 ID 在 Microsoft Identity Platform 2.0 的租户中不稳定，因此不应用于为应用中的用户创建授权策略。 始终使用 `wids` 声明提供的 AAD 管理员角色的角色模板 ID。

在 CLIENT 应用的 `Program.Main` 中，将 MSAL 身份验证配置为使用自定义用户帐户工厂。

`Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a>授权配置

在 CLIENT 应用中，为每个[应用角色](#app-roles)、AAD 管理员角色或 `Program.Main` 中的安全组创建[策略](xref:security/authorization/policies)。 以下示例为 AAD 计费管理员角色创建策略：

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

有关 AAD 管理员角色 ID 的完整列表，请参阅 Azure 文档中的[角色模板 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)。 有关授权策略的详细信息，请参阅 <xref:security/authorization/policies>。

在以下示例中，CLIENT 应用使用前面的策略来授权用户。

[`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)适用于以下策略：

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

对整个组件的访问可以基于使用 [`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) 的策略：

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

如果用户未登录，则会将他们重定向到 AAD 登录页，然后在他们登录后再返回到组件。

策略检查也可以[在具有过程逻辑的代码中执行](xref:blazor/security/index#procedural-logic)。

`Pages/CheckPolicy.razor`:

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a>授权服务器 API/web API 访问

当访问令牌包含 `groups`、`wids` 和 `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` 声明时，SERVER API 应用可以通过[授权策略](xref:security/authorization/policies)授权用户访问安全组、AAD 管理员角色和应用角色的安全 API 终结点。 下面的示例使用 `wids`（已知 ID/角色模板 ID）声明，在 `Startup.ConfigureServices` 中为 AAD 计费管理员角色创建策略：

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

有关 AAD 管理员角色 ID 的完整列表，请参阅 Azure 文档中的[角色模板 ID](/azure/active-directory/roles/permissions-reference#role-template-ids)。 有关授权策略的详细信息，请参阅 <xref:security/authorization/policies>。

对 SERVER 应用中的控制器的访问可以基于将 [`[Authorize]` 属性](xref:security/authorization/simple) 与策略名称（API 文档：<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>）结合使用。

下面的示例使用名为 `BillingAdministrator` 的策略，对 `BillingDataController` 中计费数据的访问限制为 Azure 计费管理员：

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

有关详细信息，请参阅 <xref:security/authorization/policies>。

## <a name="app-roles"></a>应用角色

要在 Azure 门户中配置应用以提供应用角色成员资格声明，请参阅[如何：在应用程序中添加应用角色并在令牌中接收它们](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。

下面的示例假定 CLIENT 和 SERVER 应用配置了两个角色，并将角色分配给测试用户：

* `admin`
* `developer`

> [!NOTE]
> 当开发托管 Blazor WebAssembly 应用或独立应用的客户端服务器对（独立 Blazor WebAssembly 应用和独立 ASP.NET Core 服务器 API/Web API 应用）时，客户端和服务器 Azure 门户应用注册的 `appRoles` 清单属性必须包含相同的已配置角色。 在客户端应用清单中建立角色后，请将它们全部复制到服务器应用清单中。 如果未在客户端和服务器应用注册之间映射清单 `appRoles`，则不会为服务器 API/Web API 的经过身份验证的用户建立角色声明，即使其访问令牌具有正确的角色声明也是如此。

> [!NOTE]
> 尽管无法将角色分配给没有 Azure AD Premium 帐户的组，但你可以将角色分配给用户，并为具有标准 Azure 帐户的用户接收角色声明。 本部分中的指南不需要 AAD Premium 帐户。
>
> 通过为其他每个角色分配重新添加用户，在 Azure 门户中分配多个角色。

[自定义用户帐户](#custom-user-account)部分中显示的 `CustomAccountFactory` 设置为使用 JSON 数组值应对 `roles` 声明。 在 CLIENT 应用中添加并注册 `CustomAccountFactory`，如[自定义用户帐户](#custom-user-account)部分所示。 无需提供代码来删除原始 `roles` 声明，因为框架会自动删除它。

在 CLIENT 应用的 `Program.Main` 中，指定名为“`appRole`”的声明作为 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> 检查的角色声明：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> 如果希望使用 `directoryRoles` 声明（ADD 管理员角色），请将“`directoryRoles`”分配给 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>。

在 SERVER 应用的 `Startup.ConfigureServices` 中，指定名为“`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`”的声明作为 <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> 检查的角色声明：

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> 如果希望使用 `wids` 声明（ADD 管理员角色），请将“`wids`”分配给 <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>。

组件授权方法此时有效。 CLIENT 应用的组件中的任何授权机制都可以使用 `admin` 角色来授权用户：

* [`AuthorizeView` 组件](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [过程逻辑](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

支持多个角色测试：

* 要求用户为 `admin` 或 `developer` 角色，并具有 `AuthorizeView` 组件：

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* 要求用户同时为 `admin` 和 `developer` 角色，并具有 `AuthorizeView` 组件：

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* 要求用户为 `admin` 或 `developer` 角色，并具有 `[Authorize]` 属性：

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* 要求用户同时为 `admin` 和 `developer` 角色，并具有 `[Authorize]` 属性：

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* 要求用户为 `admin` 或 `developer` 角色，并具有过程代码：

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* 要求用户同时为 `admin` 或 `developer` 角色，并具有过程代码，方法是将 [条件 OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) 更改为上述示例中的 [条件 AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)：

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

SERVER 应用的控制器中的任何授权机制都可以使用 `admin` 角色来授权用户：

* [`[Authorize]` 属性指令](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [过程逻辑](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

支持多个角色测试：

* 要求用户为 `admin` 或 `developer` 角色，并具有 `[Authorize]` 属性：

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* 要求用户同时为 `admin` 和 `developer` 角色，并具有 `[Authorize]` 属性：

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* 要求用户为 `admin` 或 `developer` 角色，并具有过程代码：

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* 要求用户同时为 `admin` 或 `developer` 角色，并具有过程代码，方法是将 [条件 OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) 更改为上述示例中的 [条件 AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators)：

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>其他资源

* [角色模板 ID（Azure 文档）](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [`groupMembershipClaims` 属性（Azure 文档）](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [如何：在应用程序中添加应用角色并在令牌中接收它们（Azure 文档）](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [应用程序角色（Azure 文档）](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
