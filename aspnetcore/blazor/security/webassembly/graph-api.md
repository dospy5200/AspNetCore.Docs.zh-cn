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
# <a name="use-graph-api-with-aspnet-core-blazor-webassembly"></a><span data-ttu-id="4fae1-103">将 Graph API 和 ASP.NET Core Blazor WebAssembly 结合使用</span><span class="sxs-lookup"><span data-stu-id="4fae1-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4fae1-104">[Microsoft Graph API](/graph/use-the-api) 属于 RESTful Web API，使 Blazor 及其他 .NET Framework 应用能够访问 Microsoft 云服务资源。</span><span class="sxs-lookup"><span data-stu-id="4fae1-104">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="4fae1-105">Graph SDK</span><span class="sxs-lookup"><span data-stu-id="4fae1-105">Graph SDK</span></span>

<span data-ttu-id="4fae1-106">[Microsoft Graph SDK](/graph/sdks/sdks-overview) 用于简化构建访问 Microsoft Graph 的高质量、高效且可复原应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="4fae1-106">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="4fae1-107">本部分的示例要求独立应用或 `Client` 应用的项目文件中包含对以下包的包引用：</span><span class="sxs-lookup"><span data-stu-id="4fae1-107">The examples in this section require package references for the following packages in the project file of the standalone or **`Client`** app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="4fae1-108">本文的以下各小节均使用以下实用工具类和配置：</span><span class="sxs-lookup"><span data-stu-id="4fae1-108">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="4fae1-109">使用 Graph SDK 从组件调用 Graph API</span><span class="sxs-lookup"><span data-stu-id="4fae1-109">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="4fae1-110">使用 Graph SDK 自定义用户声明</span><span class="sxs-lookup"><span data-stu-id="4fae1-110">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="4fae1-111">在 Azure 门户的 AAD 区域中添加 Microsoft Graph API 范围后：</span><span class="sxs-lookup"><span data-stu-id="4fae1-111">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="4fae1-112">将以下 `GraphClientExtensions.cs` 类添加到托管 Blazor 解决方案的独立应用或 `Client` 应用。</span><span class="sxs-lookup"><span data-stu-id="4fae1-112">Add the following `GraphClientExtensions.cs` class to the standalone app or **`Client`** app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="4fae1-113">向 `AuthenticateRequestAsync` 方法中 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> 的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> 属性提供所需的范围。</span><span class="sxs-lookup"><span data-stu-id="4fae1-113">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="4fae1-114">在下面的示例中，指定了 `User.Read` 范围以匹配本文后面部分的示例。</span><span class="sxs-lookup"><span data-stu-id="4fae1-114">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

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

<span data-ttu-id="4fae1-115">前面代码中的范围占位符 `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` 表示一个或多个允许的范围。</span><span class="sxs-lookup"><span data-stu-id="4fae1-115">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="4fae1-116">例如，将 `Scopes` 设置为本文以下部分各示例的任一 `User.Read` 范围的字符串数组：</span><span class="sxs-lookup"><span data-stu-id="4fae1-116">For example, set `Scopes` to a string array of one scope for `User.Read` for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="4fae1-117">在 `Program.Main` (`Program.cs`) 中，使用 `AddGraphClient` 扩展方法添加 Graph 客户端服务和配置：</span><span class="sxs-lookup"><span data-stu-id="4fae1-117">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

<span data-ttu-id="4fae1-118">前面代码中的范围占位符 `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` 表示一个或多个允许的范围。</span><span class="sxs-lookup"><span data-stu-id="4fae1-118">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="4fae1-119">例如，将 `User.Read` 范围传递到本文以下部分各示例的 `AddGraphClient`：</span><span class="sxs-lookup"><span data-stu-id="4fae1-119">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="4fae1-120">使用 Graph SDK 从组件调用 Graph API</span><span class="sxs-lookup"><span data-stu-id="4fae1-120">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="4fae1-121">本部分使用本文前面所述的[实用工具类 (`GraphClientExtensions.cs`)](#graph-sdk)。</span><span class="sxs-lookup"><span data-stu-id="4fae1-121">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="4fae1-122">以下 `GraphExample` 组件使用注入的 `GraphServiceClient` 来获取用户的 AAD 个人资料数据并显示其手机号码：</span><span class="sxs-lookup"><span data-stu-id="4fae1-122">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

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

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="4fae1-123">使用 Graph SDK 自定义用户声明</span><span class="sxs-lookup"><span data-stu-id="4fae1-123">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="4fae1-124">本部分使用本文前面所述的[实用工具类 (`GraphClientExtensions.cs`)](#graph-sdk)。</span><span class="sxs-lookup"><span data-stu-id="4fae1-124">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="4fae1-125">在下面的示例中，应用基于用户 AAD 个人资料中的手机号码为用户创建手机号码声明。</span><span class="sxs-lookup"><span data-stu-id="4fae1-125">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="4fae1-126">应用必须在 AAD 中配置 `User.Read` Graph API 范围。</span><span class="sxs-lookup"><span data-stu-id="4fae1-126">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="4fae1-127">在下面的自定义用户帐户工厂中，框架的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 表示用户的帐户。</span><span class="sxs-lookup"><span data-stu-id="4fae1-127">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="4fae1-128">如果应用需要扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请将下面代码中的自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>。</span><span class="sxs-lookup"><span data-stu-id="4fae1-128">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="4fae1-129">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="4fae1-129">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="4fae1-130">在 `Program.Main` (`Program.cs`) 中，将 MSAL 身份验证配置为使用自定义用户帐户工厂：如果应用使用扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请使用以下代码将自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>：</span><span class="sxs-lookup"><span data-stu-id="4fae1-130">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

## <a name="named-client-with-graph-api"></a><span data-ttu-id="4fae1-131">使用 Graph API 的命名客户端</span><span class="sxs-lookup"><span data-stu-id="4fae1-131">Named client with Graph API</span></span>

<span data-ttu-id="4fae1-132">本部分的示例使用 Graph API 的命名 <xref:System.Net.Http.HttpClient> 来获取用户的手机号码以处理呼叫。</span><span class="sxs-lookup"><span data-stu-id="4fae1-132">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="4fae1-133">本部分的示例要求独立应用或 `Client` 应用的项目文件中包含对 [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="4fae1-133">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or **`Client`** app's project file.</span></span>

<span data-ttu-id="4fae1-134">创建以下类和项目配置以使用 Graph API。</span><span class="sxs-lookup"><span data-stu-id="4fae1-134">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="4fae1-135">本文的以下各小节均使用以下类和配置：</span><span class="sxs-lookup"><span data-stu-id="4fae1-135">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="4fae1-136">从组件调用 Graph API</span><span class="sxs-lookup"><span data-stu-id="4fae1-136">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="4fae1-137">使用 Graph API 和命名客户端自定义用户声明</span><span class="sxs-lookup"><span data-stu-id="4fae1-137">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="4fae1-138">在 Azure 门户的 AAD 区域中添加 Microsoft Graph API 范围后，向应用为 Graph API 配置的处理程序提供所需的范围。</span><span class="sxs-lookup"><span data-stu-id="4fae1-138">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="4fae1-139">下面的示例为 `User.Read` 范围配置处理程序。</span><span class="sxs-lookup"><span data-stu-id="4fae1-139">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="4fae1-140">可以添加更多范围。</span><span class="sxs-lookup"><span data-stu-id="4fae1-140">Additional scopes can be added.</span></span>

<span data-ttu-id="4fae1-141">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="4fae1-141">`GraphAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="4fae1-142">在 `Program.Main` (`Program.cs`) 中，为 Graph API 配置命名 <xref:System.Net.Http.HttpClient>：</span><span class="sxs-lookup"><span data-stu-id="4fae1-142">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="4fae1-143">从组件调用 Graph API</span><span class="sxs-lookup"><span data-stu-id="4fae1-143">Call Graph API from a component</span></span>

<span data-ttu-id="4fae1-144">本部分使用本文前面所述的 [Graph 授权消息处理程序 (`GraphAuthorizationMessageHandler.cs`) 和应用的 `Program.Main` 添加项](#named-client-with-graph-api)，为 Graph API 提供命名 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="4fae1-144">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="4fae1-145">在 Razor 组件中：</span><span class="sxs-lookup"><span data-stu-id="4fae1-145">In a Razor component:</span></span>

* <span data-ttu-id="4fae1-146">为 Graph API 创建 <xref:System.Net.Http.HttpClient>，并发出用户个人资料数据请求。</span><span class="sxs-lookup"><span data-stu-id="4fae1-146">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="4fae1-147">`UserInfo.cs` 类指定所需的用户个人资料属性（通过 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 特性以及 AAD 用于这些属性的 JSON 名称来实现）。</span><span class="sxs-lookup"><span data-stu-id="4fae1-147">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="4fae1-148">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="4fae1-148">`Pages/CallUser.razor`:</span></span>

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

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="4fae1-149">使用 Graph API 和命名客户端自定义用户声明</span><span class="sxs-lookup"><span data-stu-id="4fae1-149">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="4fae1-150">本部分使用本文前面所述的 [Graph 授权消息处理程序 (`GraphAuthorizationMessageHandler.cs`) 和应用的 `Program.Main` 添加项](#named-client-with-graph-api)，为 Graph API 提供命名 <xref:System.Net.Http.HttpClient>。</span><span class="sxs-lookup"><span data-stu-id="4fae1-150">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="4fae1-151">在下面的示例中，应用基于用户 AAD 个人资料中的手机号码为用户创建手机号码声明。</span><span class="sxs-lookup"><span data-stu-id="4fae1-151">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="4fae1-152">应用必须在 AAD 中配置 `User.Read` Graph API 范围。</span><span class="sxs-lookup"><span data-stu-id="4fae1-152">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="4fae1-153">向应用添加 `UserInfo.cs` 类，并指定所需的用户个人资料属性（通过 <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 特性以及 AAD 用于这些属性的 JSON 名称来实现）：</span><span class="sxs-lookup"><span data-stu-id="4fae1-153">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="4fae1-154">在下面的自定义用户帐户工厂中，框架的 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 表示用户的帐户。</span><span class="sxs-lookup"><span data-stu-id="4fae1-154">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="4fae1-155">如果应用需要扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请将下面代码中的自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>。</span><span class="sxs-lookup"><span data-stu-id="4fae1-155">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="4fae1-156">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="4fae1-156">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="4fae1-157">在 `Program.Main` (`Program.cs`) 中，将 MSAL 身份验证配置为使用自定义用户帐户工厂：如果应用使用扩展 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> 的自定义用户帐户类，请使用以下代码将自定义用户帐户类替换为 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>：</span><span class="sxs-lookup"><span data-stu-id="4fae1-157">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

<span data-ttu-id="4fae1-158">前面的示例适用于使用 AAD 身份验证和 MSAL 的应用。</span><span class="sxs-lookup"><span data-stu-id="4fae1-158">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="4fae1-159">对于 OIDC 和 API 身份验证，也存在类似的模式。</span><span class="sxs-lookup"><span data-stu-id="4fae1-159">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="4fae1-160">有关详细信息，请参阅[使用有效负载声明自定义用户](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim)部分的示例。</span><span class="sxs-lookup"><span data-stu-id="4fae1-160">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
