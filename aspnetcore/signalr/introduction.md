---
title: ASP.NET Core SignalR 简介
author: bradygaster
description: 了解 ASP.NET Core 库如何 SignalR 简化将实时功能添加到应用程序。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 5e3a96d38424b895fa8f34897a4f0a1d818ff479
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588082"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="bc11d-103">ASP.NET Core SignalR 简介</span><span class="sxs-lookup"><span data-stu-id="bc11d-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="bc11d-104">什么是 SignalR？</span><span class="sxs-lookup"><span data-stu-id="bc11d-104">What is SignalR?</span></span>

<span data-ttu-id="bc11d-105">ASP.NET Core SignalR 是一种开放源代码库，可简化将实时 web 功能添加到应用程序的功能。</span><span class="sxs-lookup"><span data-stu-id="bc11d-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="bc11d-106">实时 web 功能使服务器端代码可以立即将内容推送到客户端。</span><span class="sxs-lookup"><span data-stu-id="bc11d-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="bc11d-107">适用于 SignalR ：</span><span class="sxs-lookup"><span data-stu-id="bc11d-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="bc11d-108">需要从服务器进行高频率更新的应用。</span><span class="sxs-lookup"><span data-stu-id="bc11d-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="bc11d-109">示例包括游戏、社交网络、投票、拍卖、地图和 GPS 应用。</span><span class="sxs-lookup"><span data-stu-id="bc11d-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="bc11d-110">仪表板和监视应用。</span><span class="sxs-lookup"><span data-stu-id="bc11d-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="bc11d-111">示例包括公司仪表板、即时销售更新或旅行警报。</span><span class="sxs-lookup"><span data-stu-id="bc11d-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="bc11d-112">协作应用。</span><span class="sxs-lookup"><span data-stu-id="bc11d-112">Collaborative apps.</span></span> <span data-ttu-id="bc11d-113">协作应用的示例包括白板应用和团队会议软件。</span><span class="sxs-lookup"><span data-stu-id="bc11d-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="bc11d-114">需要通知的应用。</span><span class="sxs-lookup"><span data-stu-id="bc11d-114">Apps that require notifications.</span></span> <span data-ttu-id="bc11d-115">社交网络、电子邮件、聊天、游戏、旅行警报和很多其他应用都需使用通知。</span><span class="sxs-lookup"><span data-stu-id="bc11d-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="bc11d-116">SignalR 提供一个 API，用于创建 (RPC) 的服务器到客户端 [远程过程调用 ](https://wikipedia.org/wiki/Remote_procedure_call)。</span><span class="sxs-lookup"><span data-stu-id="bc11d-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="bc11d-117">Rpc 通过服务器端 .NET Core 代码从客户端调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="bc11d-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="bc11d-118">下面是的某些功能 SignalR ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="bc11d-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="bc11d-119">自动处理连接管理。</span><span class="sxs-lookup"><span data-stu-id="bc11d-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="bc11d-120">将消息同时发送到所有连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="bc11d-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="bc11d-121">例如，聊天室。</span><span class="sxs-lookup"><span data-stu-id="bc11d-121">For example, a chat room.</span></span>
* <span data-ttu-id="bc11d-122">向特定客户端或客户端组发送消息。</span><span class="sxs-lookup"><span data-stu-id="bc11d-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="bc11d-123">可缩放以处理不断增加的流量。</span><span class="sxs-lookup"><span data-stu-id="bc11d-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="bc11d-124">源托管在[ SignalR GitHub 上的存储库](https://github.com/dotnet/AspNetCore/tree/main/src/SignalR)中。</span><span class="sxs-lookup"><span data-stu-id="bc11d-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/main/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="bc11d-125">传输</span><span class="sxs-lookup"><span data-stu-id="bc11d-125">Transports</span></span>

<span data-ttu-id="bc11d-126">SignalR 支持以下用于按正常回退)  (处理实时通信的技术：</span><span class="sxs-lookup"><span data-stu-id="bc11d-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="bc11d-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="bc11d-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="bc11d-128">Server-Sent 事件</span><span class="sxs-lookup"><span data-stu-id="bc11d-128">Server-Sent Events</span></span>
* <span data-ttu-id="bc11d-129">长轮询</span><span class="sxs-lookup"><span data-stu-id="bc11d-129">Long Polling</span></span>

<span data-ttu-id="bc11d-130">SignalR 自动选择服务器和客户端功能内的最佳传输方法。</span><span class="sxs-lookup"><span data-stu-id="bc11d-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="bc11d-131">中心</span><span class="sxs-lookup"><span data-stu-id="bc11d-131">Hubs</span></span>

<span data-ttu-id="bc11d-132">SignalR 使用 *集线器* 在客户端和服务器之间进行通信。</span><span class="sxs-lookup"><span data-stu-id="bc11d-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="bc11d-133">中心是一种高级管道，它允许客户端和服务器分别调用方法。</span><span class="sxs-lookup"><span data-stu-id="bc11d-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="bc11d-134">SignalR 自动处理跨计算机边界的调度，使客户端能够在服务器上调用方法，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="bc11d-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="bc11d-135">可以将强类型参数传递给方法，从而启用模型绑定。</span><span class="sxs-lookup"><span data-stu-id="bc11d-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="bc11d-136">SignalR 提供了两个内置的集线器协议：基于 JSON 的文本协议和基于 [MessagePack](https://msgpack.org/)的二进制协议。</span><span class="sxs-lookup"><span data-stu-id="bc11d-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="bc11d-137">与 JSON 相比，MessagePack 通常会创建较小的消息。</span><span class="sxs-lookup"><span data-stu-id="bc11d-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="bc11d-138">较早的浏览器必须支持 [XHR 级别 2](https://caniuse.com/#feat=xhr2) ，才能提供 MessagePack 协议支持。</span><span class="sxs-lookup"><span data-stu-id="bc11d-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="bc11d-139">中心通过发送包含客户端方法的名称和参数的消息来调用客户端代码。</span><span class="sxs-lookup"><span data-stu-id="bc11d-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="bc11d-140">作为方法参数发送的对象将使用配置的协议进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="bc11d-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="bc11d-141">客户端尝试将名称与客户端代码中的方法匹配。</span><span class="sxs-lookup"><span data-stu-id="bc11d-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="bc11d-142">当客户端找到匹配项时，它将调用方法并向其传递反序列化的参数数据。</span><span class="sxs-lookup"><span data-stu-id="bc11d-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bc11d-143">其他资源</span><span class="sxs-lookup"><span data-stu-id="bc11d-143">Additional resources</span></span>

* [<span data-ttu-id="bc11d-144">SignalRASP.NET Core 入门</span><span class="sxs-lookup"><span data-stu-id="bc11d-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="bc11d-145">支持的平台</span><span class="sxs-lookup"><span data-stu-id="bc11d-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="bc11d-146">中心</span><span class="sxs-lookup"><span data-stu-id="bc11d-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="bc11d-147">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="bc11d-147">JavaScript client</span></span>](xref:signalr/javascript-client)
