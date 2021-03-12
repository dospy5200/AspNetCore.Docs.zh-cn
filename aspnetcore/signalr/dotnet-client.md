---
title: ASP.NET Core SignalR .Net 客户端
author: bradygaster
description: 有关 ASP.NET Core SignalR .Net 客户端的信息
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: db2bfa6d3aa440615c5a9c17ae843dbe22755c97
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589239"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="ff9da-103">ASP.NET Core SignalR .Net 客户端</span><span class="sxs-lookup"><span data-stu-id="ff9da-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="ff9da-104">通过 ASP.NET Core SignalR .net 客户端库，你可以 SignalR 从 .net 应用程序与中心进行通信。</span><span class="sxs-lookup"><span data-stu-id="ff9da-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="ff9da-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/dotnet-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ff9da-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ff9da-106">本文中的代码示例是使用 .Net 客户端 ASP.NET Core 的 WPF 应用程序 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="ff9da-107">安装 SignalR .net 客户端包</span><span class="sxs-lookup"><span data-stu-id="ff9da-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="ff9da-108">[AspNetCore. SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)若要连接到集线器，.net 客户端需要客户端包 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff9da-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff9da-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff9da-110">若要安装客户端库，请在 " **包管理器控制台** " 窗口中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ff9da-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="ff9da-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ff9da-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ff9da-112">若要安装客户端库，请在命令 shell 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ff9da-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="ff9da-113">连接到集线器</span><span class="sxs-lookup"><span data-stu-id="ff9da-113">Connect to a hub</span></span>

<span data-ttu-id="ff9da-114">若要建立连接，请创建 `HubConnectionBuilder` 并调用 `Build` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="ff9da-115">在建立连接时，可以配置中心 URL、协议、传输类型、日志级别、标头和其他选项。</span><span class="sxs-lookup"><span data-stu-id="ff9da-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="ff9da-116">通过在中插入任意方法来配置任何所需的选项 `HubConnectionBuilder` `Build` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="ff9da-117">启动与的连接 `StartAsync` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="ff9da-118">处理丢失的连接</span><span class="sxs-lookup"><span data-stu-id="ff9da-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="ff9da-119">自动重新连接</span><span class="sxs-lookup"><span data-stu-id="ff9da-119">Automatically reconnect</span></span>

<span data-ttu-id="ff9da-120"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection>可以将配置为使用上的方法自动重新连接 `WithAutomaticReconnect` <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="ff9da-121">默认情况下，它不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="ff9da-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="ff9da-122">在没有任何参数的情况下，会 `WithAutomaticReconnect()` 将客户端配置为分别等待0、2、10和30秒，然后再尝试重新连接尝试。</span><span class="sxs-lookup"><span data-stu-id="ff9da-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="ff9da-123">在开始任何重新连接尝试之前， `HubConnection` 将转换为 `HubConnectionState.Reconnecting` 状态，并触发 `Reconnecting` 事件。</span><span class="sxs-lookup"><span data-stu-id="ff9da-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="ff9da-124">这为用户提供警告连接已丢失并禁用 UI 元素的机会。</span><span class="sxs-lookup"><span data-stu-id="ff9da-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="ff9da-125">非交互式应用可以开始排队或删除消息。</span><span class="sxs-lookup"><span data-stu-id="ff9da-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="ff9da-126">如果客户端在其前四次尝试内成功重新连接，则 `HubConnection` 将转换回 `Connected` 状态并激发 `Reconnected` 事件。</span><span class="sxs-lookup"><span data-stu-id="ff9da-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="ff9da-127">这为用户提供了通知用户已重新建立连接并取消排队消息的排队的机会。</span><span class="sxs-lookup"><span data-stu-id="ff9da-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="ff9da-128">由于连接在服务器上看起来是全新的，因此 `ConnectionId` 将向事件处理程序提供一个新的 `Reconnected` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="ff9da-129">`Reconnected` `connectionId` 如果 `HubConnection` 配置为[跳过协商](xref:signalr/configuration#configure-client-options)，事件处理程序的参数将为 null。</span><span class="sxs-lookup"><span data-stu-id="ff9da-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="ff9da-130">`WithAutomaticReconnect()` 不会将配置 `HubConnection` 为重试初始启动失败，因此，需要手动处理启动失败：</span><span class="sxs-lookup"><span data-stu-id="ff9da-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="ff9da-131">如果客户端在其前四次尝试中未成功重新连接，则 `HubConnection` 将转换为 `Disconnected` 状态并触发 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 事件。</span><span class="sxs-lookup"><span data-stu-id="ff9da-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="ff9da-132">这为尝试手动重新启动连接或通知用户连接永久丢失有机会。</span><span class="sxs-lookup"><span data-stu-id="ff9da-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="ff9da-133">若要在断开连接或更改重新连接时间安排之前配置自定义的重新连接尝试次数，请 `WithAutomaticReconnect` 接受一个数字数组，表示在开始每次重新连接尝试之前等待的延迟（以毫秒为单位）。</span><span class="sxs-lookup"><span data-stu-id="ff9da-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="ff9da-134">前面的示例将配置 `HubConnection` 为在连接丢失后立即开始尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="ff9da-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="ff9da-135">这也适用于默认配置。</span><span class="sxs-lookup"><span data-stu-id="ff9da-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="ff9da-136">如果第一次重新连接尝试失败，则第二次重新连接尝试还会立即启动，而不是等待2秒，就像在默认配置中一样。</span><span class="sxs-lookup"><span data-stu-id="ff9da-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="ff9da-137">如果第二次重新连接尝试失败，则第三次重新连接尝试将在10秒内启动，这与默认配置相同。</span><span class="sxs-lookup"><span data-stu-id="ff9da-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="ff9da-138">然后，在第三次重新连接尝试失败后，自定义行为将再次从默认行为与其分离。</span><span class="sxs-lookup"><span data-stu-id="ff9da-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="ff9da-139">在默认配置中，将在另一个30秒内再次尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="ff9da-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="ff9da-140">如果需要更好地控制计时和自动重新连接尝试的次数，则 `WithAutomaticReconnect` 接受一个实现接口的 `IRetryPolicy` 对象，该对象具有一个名为的方法 `NextRetryDelay` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="ff9da-141">`NextRetryDelay` 采用类型为的单个自变量 `RetryContext` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="ff9da-142">`RetryContext`有三个属性： `PreviousRetryCount` 、 `ElapsedTime` 和 `RetryReason` ， `long` 分别为、和 `TimeSpan` `Exception` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="ff9da-143">第一次重新连接尝试之前， `PreviousRetryCount` 和均 `ElapsedTime` 为零， `RetryReason` 将是导致连接丢失的异常。</span><span class="sxs-lookup"><span data-stu-id="ff9da-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="ff9da-144">每次失败的重试次数递增一次后，将 `PreviousRetryCount` `ElapsedTime` 更新以反映到目前为止所用的时间， `RetryReason` 这是导致上次重新连接尝试失败的异常。</span><span class="sxs-lookup"><span data-stu-id="ff9da-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="ff9da-145">`NextRetryDelay` 必须返回一个 TimeSpan，表示在下一次重新连接尝试之前要等待的时间，或者 `null` ，如果应停止重新连接，则为 `HubConnection` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="ff9da-146">或者，你可以编写将手动重新连接客户端的代码，如 [手动重新连接](#manually-reconnect)中所示。</span><span class="sxs-lookup"><span data-stu-id="ff9da-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="ff9da-147">手动重新连接</span><span class="sxs-lookup"><span data-stu-id="ff9da-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="ff9da-148">在3.0 之前，.NET 客户端 SignalR 不会自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="ff9da-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="ff9da-149">必须编写代码来手动重新连接客户端。</span><span class="sxs-lookup"><span data-stu-id="ff9da-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="ff9da-150">使用 <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> 事件响应断开连接。</span><span class="sxs-lookup"><span data-stu-id="ff9da-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="ff9da-151">例如，你可能需要自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="ff9da-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="ff9da-152">`Closed`事件需要一个返回的委托 `Task` ，该委托允许异步代码在不使用的情况下运行 `async void` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="ff9da-153">若要在 `Closed` 同步运行的事件处理程序中满足委托签名，请返回 `Task.CompletedTask` ：</span><span class="sxs-lookup"><span data-stu-id="ff9da-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="ff9da-154">异步支持的主要原因是，可以重启连接。</span><span class="sxs-lookup"><span data-stu-id="ff9da-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="ff9da-155">启动连接是一种异步操作。</span><span class="sxs-lookup"><span data-stu-id="ff9da-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="ff9da-156">在 `Closed` 重启连接的处理程序中，考虑等待一些随机延迟以防止服务器过载，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="ff9da-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="ff9da-157">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="ff9da-157">Call hub methods from client</span></span>

<span data-ttu-id="ff9da-158">`InvokeAsync` 在集线器上调用方法。</span><span class="sxs-lookup"><span data-stu-id="ff9da-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="ff9da-159">将 hub 方法名称和在 hub 方法中定义的任何自变量传递给 `InvokeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="ff9da-160">SignalR 是异步的，因此 `async` 在 `await` 进行调用时使用和。</span><span class="sxs-lookup"><span data-stu-id="ff9da-160">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="ff9da-161">`InvokeAsync`方法返回一个在 `Task` 服务器方法返回时完成的。</span><span class="sxs-lookup"><span data-stu-id="ff9da-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="ff9da-162">返回值（如果有）作为的结果提供 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="ff9da-163">服务器上的方法所引发的任何异常都将导致出错 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="ff9da-164">使用 `await` 语法等待服务器方法完成，并使用 `try...catch` 语法来处理错误。</span><span class="sxs-lookup"><span data-stu-id="ff9da-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="ff9da-165">`SendAsync`方法返回一个，该方法在 `Task` 消息已发送到服务器时完成。</span><span class="sxs-lookup"><span data-stu-id="ff9da-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="ff9da-166">不会提供返回值，因为它 `Task` 不会等到服务器方法完成。</span><span class="sxs-lookup"><span data-stu-id="ff9da-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="ff9da-167">发送消息时，在客户端上引发的任何异常都会产生出错 `Task` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="ff9da-168">使用 `await` 和 `try...catch` 语法处理发送错误。</span><span class="sxs-lookup"><span data-stu-id="ff9da-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="ff9da-169">仅 SignalR 在 *默认* 模式下使用 Azure 服务时，才支持从客户端调用中心方法。</span><span class="sxs-lookup"><span data-stu-id="ff9da-169">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="ff9da-170">有关详细信息，请参阅 [Signalr GitHub 存储库)  (常见问题解答 ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。</span><span class="sxs-lookup"><span data-stu-id="ff9da-170">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="ff9da-171">从中心调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="ff9da-171">Call client methods from hub</span></span>

<span data-ttu-id="ff9da-172">定义集线器在 `connection.On` 生成之后、启动连接之前调用的方法。</span><span class="sxs-lookup"><span data-stu-id="ff9da-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="ff9da-173">`connection.On`当服务器端代码使用方法调用时，上面的代码将运行 `SendAsync` 。</span><span class="sxs-lookup"><span data-stu-id="ff9da-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="ff9da-174">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="ff9da-174">Error handling and logging</span></span>

<span data-ttu-id="ff9da-175">使用 try-catch 语句处理错误。</span><span class="sxs-lookup"><span data-stu-id="ff9da-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="ff9da-176">检查 `Exception` 对象，确定发生错误后要采取的适当操作。</span><span class="sxs-lookup"><span data-stu-id="ff9da-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="ff9da-177">其他资源</span><span class="sxs-lookup"><span data-stu-id="ff9da-177">Additional resources</span></span>

* [<span data-ttu-id="ff9da-178">中心</span><span class="sxs-lookup"><span data-stu-id="ff9da-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ff9da-179">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="ff9da-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="ff9da-180">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="ff9da-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="ff9da-181">Azure SignalR Service 无服务器文档</span><span class="sxs-lookup"><span data-stu-id="ff9da-181">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
