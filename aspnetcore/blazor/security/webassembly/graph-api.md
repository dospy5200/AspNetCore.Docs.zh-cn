---
title: 将 Graph API 和 ASP.NET Core Blazor WebAssembly 结合使用
author: guardrex
description: 了解如何将 Graph API 和 Blazor WebAssemlby 应用结合使用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: b9e95afc2ccff95c6acd215a70c8b0f92fb1b8c0
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394728"
---
# <a name="use-graph-api-with-aspnet-core-blazor-webassembly"></a>将 Graph API 和 ASP.NET Core Blazor WebAssembly 结合使用

::: moniker range=">= aspnetcore-5.0"

[Microsoft Graph API](/graph/use-the-api) 属于 RESTful Web API，使 Blazor 及其他 .NET Framework 应用能够访问 Microsoft 云服务资源。

## <a name="graph-sdk"></a>Graph SDK

[Microsoft Graph SDK](/graph/sdks/sdks-overview) 用于简化构建访问 Microsoft Graph 的高质量、高效且可复原应用程序的过程。

本部分的示例要求独立应用或 `Client` 应用的项目文件中包含对以下包的包引用：

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

本文的以下各小节均使用以下实用工具类和配置：

* [使用 Graph SDK 从组件调用 Graph API](#call-graph-api-from-a-component-using-the-graph-sdk)
* [使用 Graph SDK 自定义用户声明](#customize-user-claims-with-the-graph-sdk)

在 Azure 门户的 AAD 区域中添加 Microsoft Graph API 范围后：

* 将以下 `GraphClientExtensions.cs` 类添加到托管 Blazor 解决方案的独立应用或 `Client` 应用。
* 向 `AuthenticateRequestAsync` 方法中 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> 的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> 属性提供所需的范围。 在下面的示例中，指定了 `User.Read` 范围以匹配本文后面部分的示例。

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = new[] { "{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}" }
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

前面代码中的范围占位符 `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` 表示一个或多个允许的范围。 例如，将 `Scopes` 设置为本文以下部分各示例的任一 `User.Read` 范围的字符串数组：

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

在 `Program.Main` (`Program.cs`) 中，使用 `AddGraphClient` 扩展方法添加 Graph 客户端服务和配置：

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

前面代码中的范围占位符 `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` 表示一个或多个允许的范围。 例如，将 `User.Read` 范围传递到本文以下部分各示例的 `AddGraphClient`：

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>使用 Graph SDK 从组件调用 Graph API

本部分使用本文前面所述的[实用工具类 (`GraphClientExtensions.cs`)](#graph-sdk)。 以下 `GraphExample` 组件使用注入的 `GraphServiceClient` 来获取用户的 AAD 个人资料数据并显示其手机号码：

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a>使用 Graph SDK 自定义用户声明

本部分使用本文前面所述的[实用工具类 (`GraphClientExtensions.cs`)](#graph-sdk)。

在下面的示例中，应用基于用户 AAD 个人资料中的手机号码为用户创建手机号码声明。 应用必须在 AAD 中配置 `User.Read` Graph API 范围。

在下面的自定义用户帐户工厂中，框架的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 表示用户的帐户。 如果应用需要扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请将下面代码中的自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>。

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
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
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
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

在 `Program.Main` (`Program.cs`) 中，将 MSAL 身份验证配置为使用自定义用户帐户工厂：如果应用使用扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请使用以下代码将自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>：

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a>使用 Graph API 的命名客户端

本部分的示例使用 Graph API 的命名 <xref:System.Net.Http.HttpClient> 来获取用户的手机号码以处理呼叫。

本部分的示例要求独立应用或 `Client` 应用的项目文件中包含对 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 的包引用。

创建以下类和项目配置以使用 Graph API。 本文的以下各小节均使用以下类和配置：

* [从组件调用 Graph API](#call-graph-api-from-a-component)
* [使用 Graph API 和命名客户端自定义用户声明](#customize-user-claims-with-graph-api-and-a-named-client)

在 Azure 门户的 AAD 区域中添加 Microsoft Graph API 范围后，向应用为 Graph API 配置的处理程序提供所需的范围。 下面的示例为 `User.Read` 范围配置处理程序。 可以添加更多范围。

`GraphAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

在 `Program.Main` (`Program.cs`) 中，为 Graph API 配置命名 <xref:System.Net.Http.HttpClient>：

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>从组件调用 Graph API

本部分使用本文前面所述的 [Graph 授权消息处理程序 (`GraphAuthorizationMessageHandler.cs`) 和应用的 `Program.Main` 添加项](#named-client-with-graph-api)，为 Graph API 提供命名 <xref:System.Net.Http.HttpClient>。

在 Razor 组件中：

* 为 Graph API 创建 <xref:System.Net.Http.HttpClient>，并发出用户个人资料数据请求。
* `UserInfo.cs` 类指定所需的用户个人资料属性（通过 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 特性以及 AAD 用于这些属性的 JSON 名称来实现）。

`Pages/CallUser.razor`:

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                // Use userInfo.MobilePhone and callInfo.Message to make a call

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>使用 Graph API 和命名客户端自定义用户声明

本部分使用本文前面所述的 [Graph 授权消息处理程序 (`GraphAuthorizationMessageHandler.cs`) 和应用的 `Program.Main` 添加项](#named-client-with-graph-api)，为 Graph API 提供命名 <xref:System.Net.Http.HttpClient>。

在下面的示例中，应用基于用户 AAD 个人资料中的手机号码为用户创建手机号码声明。 应用必须在 AAD 中配置 `User.Read` Graph API 范围。

向应用添加 `UserInfo.cs` 类，并指定所需的用户个人资料属性（通过 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 特性以及 AAD 用于这些属性的 JSON 名称来实现）：

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

在下面的自定义用户帐户工厂中，框架的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 表示用户的帐户。 如果应用需要扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请将下面代码中的自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>。

`CustomAccountFactory.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

在 `Program.Main` (`Program.cs`) 中，将 MSAL 身份验证配置为使用自定义用户帐户工厂：如果应用使用扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请使用以下代码将自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>：

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

前面的示例适用于使用 AAD 身份验证和 MSAL 的应用。 对于 OIDC 和 API 身份验证，也存在类似的模式。 有关详细信息，请参阅[使用有效负载声明自定义用户](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim)部分的示例。
