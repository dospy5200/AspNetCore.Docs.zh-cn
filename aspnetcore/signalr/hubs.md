---
title: 在 ASP.NET Core 中使用中心 SignalR
author: bradygaster
description: 了解如何在 ASP.NET Core 中使用集线器 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 872b88cc3c87137365de8c50a37bf5dd5fd9fe10
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587861"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="3a046-103">使用中 SignalR 的中心进行 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a046-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="3a046-104">作者： [Rachel Appel](https://twitter.com/rachelappel) 和 [古柯 Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="3a046-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="3a046-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ )[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3a046-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="3a046-106">什么是 SignalR 中心</span><span class="sxs-lookup"><span data-stu-id="3a046-106">What is a SignalR hub</span></span>

<span data-ttu-id="3a046-107">SignalR利用中心 API，你可以从服务器对连接的客户端调用方法。</span><span class="sxs-lookup"><span data-stu-id="3a046-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="3a046-108">在服务器代码中，您将定义由客户端调用的方法。</span><span class="sxs-lookup"><span data-stu-id="3a046-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="3a046-109">在客户端代码中，您将定义从服务器调用的方法。</span><span class="sxs-lookup"><span data-stu-id="3a046-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="3a046-110">SignalR 处理幕后的所有事情，使客户端到服务器和服务器到客户端的实时通信成为可能。</span><span class="sxs-lookup"><span data-stu-id="3a046-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="3a046-111">配置 SignalR 中心</span><span class="sxs-lookup"><span data-stu-id="3a046-111">Configure SignalR hubs</span></span>

<span data-ttu-id="3a046-112">SignalR中间件需要一些服务，这些服务通过调用配置 `services.AddSignalR` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a046-113">向 SignalR ASP.NET Core 应用程序添加功能时， SignalR 通过 `endpoint.MapHub` 在 `Startup.Configure` 方法的回调中调用来设置路由 `app.UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3a046-114">将 SignalR 功能添加到 ASP.NET Core 的应用时， SignalR 通过 `app.UseSignalR` 在方法中调用来设置路由 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="3a046-115">创建和使用集线器</span><span class="sxs-lookup"><span data-stu-id="3a046-115">Create and use hubs</span></span>

<span data-ttu-id="3a046-116">通过声明从继承的类 `Hub` ，并向其添加公共方法来创建中心。</span><span class="sxs-lookup"><span data-stu-id="3a046-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="3a046-117">客户端可以调用定义为的方法 `public` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="3a046-118">可以指定返回类型和参数（包括复杂类型和数组），就像在任何 c # 方法中一样。</span><span class="sxs-lookup"><span data-stu-id="3a046-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="3a046-119">SignalR 处理参数和返回值中的复杂对象和数组的序列化和反序列化。</span><span class="sxs-lookup"><span data-stu-id="3a046-119">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="3a046-120">中心是暂时性的：</span><span class="sxs-lookup"><span data-stu-id="3a046-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="3a046-121">不要将状态存储在 hub 类的属性中。</span><span class="sxs-lookup"><span data-stu-id="3a046-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="3a046-122">每个中心方法调用在新的中心实例上执行。</span><span class="sxs-lookup"><span data-stu-id="3a046-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="3a046-123">`await`调用依赖于中心保持活动状态的异步方法时使用。</span><span class="sxs-lookup"><span data-stu-id="3a046-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="3a046-124">例如， `Clients.All.SendAsync(...)` 如果在未调用的情况下调用，则方法会失败， `await` 并且在完成前集线器方法完成 `SendAsync` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="3a046-125">上下文对象</span><span class="sxs-lookup"><span data-stu-id="3a046-125">The Context object</span></span>

<span data-ttu-id="3a046-126">`Hub`类具有一个 `Context` 属性，该属性包含有关连接的信息的以下属性：</span><span class="sxs-lookup"><span data-stu-id="3a046-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="3a046-127">Property</span><span class="sxs-lookup"><span data-stu-id="3a046-127">Property</span></span> | <span data-ttu-id="3a046-128">描述</span><span class="sxs-lookup"><span data-stu-id="3a046-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="3a046-129">获取由分配的连接的唯一 ID SignalR 。</span><span class="sxs-lookup"><span data-stu-id="3a046-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="3a046-130">每个连接都有一个连接 ID。</span><span class="sxs-lookup"><span data-stu-id="3a046-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="3a046-131">获取 [用户标识符](xref:signalr/groups)。</span><span class="sxs-lookup"><span data-stu-id="3a046-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="3a046-132">默认情况下， SignalR 将 `ClaimTypes.NameIdentifier` 从 `ClaimsPrincipal` 与连接关联的中用作用户标识符。</span><span class="sxs-lookup"><span data-stu-id="3a046-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="3a046-133">获取 `ClaimsPrincipal` 与当前用户关联的。</span><span class="sxs-lookup"><span data-stu-id="3a046-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="3a046-134">获取可用于在此连接的范围内共享数据的键/值集合。</span><span class="sxs-lookup"><span data-stu-id="3a046-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="3a046-135">数据可以存储在此集合中，它将在不同的集线器方法调用中持久保存。</span><span class="sxs-lookup"><span data-stu-id="3a046-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="3a046-136">获取连接上的可用功能的集合。</span><span class="sxs-lookup"><span data-stu-id="3a046-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="3a046-137">目前，在大多数情况下不需要此集合，因此不会对其进行详细介绍。</span><span class="sxs-lookup"><span data-stu-id="3a046-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="3a046-138">获取一个 `CancellationToken` ，它将在连接中止时通知。</span><span class="sxs-lookup"><span data-stu-id="3a046-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="3a046-139">`Hub.Context` 还包含以下方法：</span><span class="sxs-lookup"><span data-stu-id="3a046-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="3a046-140">方法</span><span class="sxs-lookup"><span data-stu-id="3a046-140">Method</span></span> | <span data-ttu-id="3a046-141">描述</span><span class="sxs-lookup"><span data-stu-id="3a046-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="3a046-142">返回 `HttpContext` 连接的， `null` 如果连接不与 HTTP 请求关联，则为。</span><span class="sxs-lookup"><span data-stu-id="3a046-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="3a046-143">对于 HTTP 连接，可以使用此方法来获取 HTTP 标头和查询字符串等信息。</span><span class="sxs-lookup"><span data-stu-id="3a046-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="3a046-144">中止连接。</span><span class="sxs-lookup"><span data-stu-id="3a046-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="3a046-145">Clients 对象</span><span class="sxs-lookup"><span data-stu-id="3a046-145">The Clients object</span></span>

<span data-ttu-id="3a046-146">`Hub`类具有一个 `Clients` 属性，该属性包含服务器和客户端之间的通信的以下属性：</span><span class="sxs-lookup"><span data-stu-id="3a046-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="3a046-147">Property</span><span class="sxs-lookup"><span data-stu-id="3a046-147">Property</span></span> | <span data-ttu-id="3a046-148">描述</span><span class="sxs-lookup"><span data-stu-id="3a046-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="3a046-149">在所有连接的客户端上调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="3a046-150">在调用集线器方法的客户端上调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="3a046-151">在所有连接的客户端上调用方法，但调用方法的客户端除外</span><span class="sxs-lookup"><span data-stu-id="3a046-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="3a046-152">`Hub.Clients` 还包含以下方法：</span><span class="sxs-lookup"><span data-stu-id="3a046-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="3a046-153">方法</span><span class="sxs-lookup"><span data-stu-id="3a046-153">Method</span></span> | <span data-ttu-id="3a046-154">描述</span><span class="sxs-lookup"><span data-stu-id="3a046-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="3a046-155">在所有连接的客户端（指定的连接除外）上调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="3a046-156">在特定连接的客户端上调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="3a046-157">在特定连接的客户端上调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="3a046-158">对指定组中的所有连接调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="3a046-159">对指定组中的所有连接调用方法，指定的连接除外</span><span class="sxs-lookup"><span data-stu-id="3a046-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="3a046-160">在多组连接上调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="3a046-161">对一组连接调用方法，而不包括调用该集线器方法的客户端</span><span class="sxs-lookup"><span data-stu-id="3a046-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="3a046-162">对与特定用户关联的所有连接调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="3a046-163">对与指定用户相关联的所有连接调用方法</span><span class="sxs-lookup"><span data-stu-id="3a046-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="3a046-164">前面的表中的每个属性或方法都返回一个包含方法的对象 `SendAsync` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="3a046-165">`SendAsync`方法允许你提供要调用的客户端方法的名称和参数。</span><span class="sxs-lookup"><span data-stu-id="3a046-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="3a046-166">向客户端发送消息</span><span class="sxs-lookup"><span data-stu-id="3a046-166">Send messages to clients</span></span>

<span data-ttu-id="3a046-167">若要调用特定的客户端，请使用对象的属性 `Clients` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="3a046-168">在下面的示例中，有三种集线器方法：</span><span class="sxs-lookup"><span data-stu-id="3a046-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="3a046-169">`SendMessage` 使用将消息发送到所有连接的客户端 `Clients.All` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="3a046-170">`SendMessageToCaller` 使用将消息发回给调用方 `Clients.Caller` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="3a046-171">`SendMessageToGroups` 向组中的所有客户端发送一条消息 `SignalR Users` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="3a046-172">强类型中心</span><span class="sxs-lookup"><span data-stu-id="3a046-172">Strongly typed hubs</span></span>

<span data-ttu-id="3a046-173">使用的缺点 `SendAsync` 是它依赖于幻字符串来指定要调用的客户端方法。</span><span class="sxs-lookup"><span data-stu-id="3a046-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="3a046-174">如果客户端中的方法名称拼写错误或缺失，则这会使代码对运行时错误开放。</span><span class="sxs-lookup"><span data-stu-id="3a046-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="3a046-175">使用的替代方法 `SendAsync` 是使用强类型 `Hub` <xref:Microsoft.AspNetCore.SignalR.Hub%601> 。</span><span class="sxs-lookup"><span data-stu-id="3a046-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="3a046-176">在下面的示例中， `ChatHub` 客户端方法已提取到名为的接口 `IChatClient` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="3a046-177">此接口可用于重构前面的 `ChatHub` 示例。</span><span class="sxs-lookup"><span data-stu-id="3a046-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="3a046-178">通过使用 `Hub<IChatClient>` ，可以对客户端方法进行编译时检查。</span><span class="sxs-lookup"><span data-stu-id="3a046-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="3a046-179">这可以防止由于使用神奇字符串而导致的问题，因为 `Hub<T>` 只能提供对在接口中定义的方法的访问。</span><span class="sxs-lookup"><span data-stu-id="3a046-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="3a046-180">使用强类型 `Hub<T>` 禁用功能 `SendAsync` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="3a046-181">接口上定义的任何方法仍可以定义为异步方法。</span><span class="sxs-lookup"><span data-stu-id="3a046-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="3a046-182">事实上，其中每个方法应返回 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="3a046-183">由于它是一个接口，因此请勿使用 `async` 关键字。</span><span class="sxs-lookup"><span data-stu-id="3a046-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="3a046-184">例如：</span><span class="sxs-lookup"><span data-stu-id="3a046-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="3a046-185">`Async`后缀不会从方法名称中去除。</span><span class="sxs-lookup"><span data-stu-id="3a046-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="3a046-186">除非您的客户端方法是使用定义的 `.on('MyMethodAsync')` ，否则不应使用 `MyMethodAsync` 作为名称。</span><span class="sxs-lookup"><span data-stu-id="3a046-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="3a046-187">更改集线器方法的名称</span><span class="sxs-lookup"><span data-stu-id="3a046-187">Change the name of a hub method</span></span>

<span data-ttu-id="3a046-188">默认情况下，服务器集线器方法名称是 .NET 方法的名称。</span><span class="sxs-lookup"><span data-stu-id="3a046-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="3a046-189">但是，可以使用 [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) 属性来更改此默认设置，并手动指定方法的名称。</span><span class="sxs-lookup"><span data-stu-id="3a046-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="3a046-190">调用方法时，客户端应使用此名称，而不是 .NET 方法名称。</span><span class="sxs-lookup"><span data-stu-id="3a046-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="3a046-191">处理连接事件</span><span class="sxs-lookup"><span data-stu-id="3a046-191">Handle events for a connection</span></span>

<span data-ttu-id="3a046-192">SignalR中心 API 提供 `OnConnectedAsync` 和 `OnDisconnectedAsync` 虚拟方法来管理和跟踪连接。</span><span class="sxs-lookup"><span data-stu-id="3a046-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="3a046-193">重写 `OnConnectedAsync` 虚拟方法，以便在客户端连接到集线器时执行操作，如将其添加到组。</span><span class="sxs-lookup"><span data-stu-id="3a046-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="3a046-194">重写 `OnDisconnectedAsync` 虚拟方法，以便在客户端断开连接时执行操作。</span><span class="sxs-lookup"><span data-stu-id="3a046-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="3a046-195">如果客户端通过调用 (特意断开连接 `connection.stop()` ，例如) ，则 `exception` 参数将为 `null` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="3a046-196">但是，如果客户端由于错误而断开连接 (例如，) 网络故障，则 `exception` 参数将包含描述失败的异常。</span><span class="sxs-lookup"><span data-stu-id="3a046-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a><span data-ttu-id="3a046-197">处理错误</span><span class="sxs-lookup"><span data-stu-id="3a046-197">Handle errors</span></span>

<span data-ttu-id="3a046-198">在中心方法中引发的异常将发送到调用方法的客户端。</span><span class="sxs-lookup"><span data-stu-id="3a046-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="3a046-199">在 JavaScript 客户端上，该 `invoke` 方法返回 [javascript 承诺](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)。</span><span class="sxs-lookup"><span data-stu-id="3a046-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="3a046-200">当客户端接收到使用附加到承诺的处理程序的错误时 `catch` ，它将作为 JavaScript 对象进行调用和传递 `Error` 。</span><span class="sxs-lookup"><span data-stu-id="3a046-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="3a046-201">如果中心引发异常，则不会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="3a046-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="3a046-202">默认情况下， SignalR 将向客户端返回一般性错误消息。</span><span class="sxs-lookup"><span data-stu-id="3a046-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="3a046-203">例如：</span><span class="sxs-lookup"><span data-stu-id="3a046-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="3a046-204">意外的异常通常包含敏感信息，例如数据库连接失败时触发的异常中的数据库服务器的名称。</span><span class="sxs-lookup"><span data-stu-id="3a046-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="3a046-205">SignalR 默认情况下，不会公开这些详细的错误消息作为安全措施。</span><span class="sxs-lookup"><span data-stu-id="3a046-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="3a046-206">有关抑制异常详细信息的原因的详细信息，请参阅 [安全注意事项一文](xref:signalr/security#exceptions) 。</span><span class="sxs-lookup"><span data-stu-id="3a046-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="3a046-207">如果 *要将* 异常情况传播到客户端，则可以使用 `HubException` 类。</span><span class="sxs-lookup"><span data-stu-id="3a046-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="3a046-208">如果 `HubException` 从中心方法引发，则 SignalR **会将** 整个消息发送到客户端（未修改）。</span><span class="sxs-lookup"><span data-stu-id="3a046-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="3a046-209">SignalR 仅将 `Message` 异常的属性发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="3a046-209">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="3a046-210">异常的堆栈跟踪和其他属性不适用于客户端。</span><span class="sxs-lookup"><span data-stu-id="3a046-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="3a046-211">相关资源</span><span class="sxs-lookup"><span data-stu-id="3a046-211">Related resources</span></span>

* [<span data-ttu-id="3a046-212">ASP.NET Core 简介 SignalR</span><span class="sxs-lookup"><span data-stu-id="3a046-212">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="3a046-213">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="3a046-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="3a046-214">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="3a046-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
