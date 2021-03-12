---
title: 在中管理用户和组 SignalR
author: bradygaster
description: ASP.NET Core SignalR 用户和组管理的概述。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
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
uid: signalr/groups
ms.openlocfilehash: 8f9e91f34386bb5b4cf79bee429a39ae27c03c5e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589213"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="5376f-103">在中管理用户和组 SignalR</span><span class="sxs-lookup"><span data-stu-id="5376f-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="5376f-104">作者： [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="5376f-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="5376f-105">SignalR 允许将消息发送到与特定用户关联的所有连接以及命名连接组。</span><span class="sxs-lookup"><span data-stu-id="5376f-105">SignalR allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="5376f-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/groups/sample/)[（如何下载）](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5376f-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="5376f-107">用户 SignalR</span><span class="sxs-lookup"><span data-stu-id="5376f-107">Users in SignalR</span></span>

<span data-ttu-id="5376f-108">中的单个用户 SignalR 可以有多个到应用的连接。</span><span class="sxs-lookup"><span data-stu-id="5376f-108">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="5376f-109">例如，用户可以连接到其桌面以及他们的手机上。</span><span class="sxs-lookup"><span data-stu-id="5376f-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="5376f-110">每个设备都有一个单独的 SignalR 连接，但它们都与同一用户关联。</span><span class="sxs-lookup"><span data-stu-id="5376f-110">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="5376f-111">如果向用户发送一条消息，则所有与该用户关联的连接都将收到该消息。</span><span class="sxs-lookup"><span data-stu-id="5376f-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="5376f-112">中心中的属性可以访问连接的用户标识符 `Context.UserIdentifier` 。</span><span class="sxs-lookup"><span data-stu-id="5376f-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="5376f-113">默认情况下， SignalR 将 `ClaimTypes.NameIdentifier` 从 `ClaimsPrincipal` 与连接关联的中用作用户标识符。</span><span class="sxs-lookup"><span data-stu-id="5376f-113">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="5376f-114">若要自定义此行为，请参阅 [使用声明自定义标识处理](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling)。</span><span class="sxs-lookup"><span data-stu-id="5376f-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="5376f-115">向特定用户发送消息，方法是将用户标识符传递到 `User` 中心方法中的函数，如以下示例中所示：</span><span class="sxs-lookup"><span data-stu-id="5376f-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="5376f-116">用户标识符区分大小写。</span><span class="sxs-lookup"><span data-stu-id="5376f-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="5376f-117">组 SignalR</span><span class="sxs-lookup"><span data-stu-id="5376f-117">Groups in SignalR</span></span>

<span data-ttu-id="5376f-118">组是与某个名称关联的连接的集合。</span><span class="sxs-lookup"><span data-stu-id="5376f-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="5376f-119">可以将消息发送到组中的所有连接。</span><span class="sxs-lookup"><span data-stu-id="5376f-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="5376f-120">建议将组发送到一个或多个连接，因为这些组由应用程序管理。</span><span class="sxs-lookup"><span data-stu-id="5376f-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="5376f-121">连接可以是多个组的成员。</span><span class="sxs-lookup"><span data-stu-id="5376f-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="5376f-122">组是类似聊天应用程序的理想之选，其中每个会议室都可以表示为一个组。</span><span class="sxs-lookup"><span data-stu-id="5376f-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="5376f-123">通过和方法将连接添加到组或从组中删除连接 `AddToGroupAsync` `RemoveFromGroupAsync` 。</span><span class="sxs-lookup"><span data-stu-id="5376f-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="5376f-124">连接重新连接时，不会保留组成员身份。</span><span class="sxs-lookup"><span data-stu-id="5376f-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="5376f-125">重新建立组后，连接需要重新加入组。</span><span class="sxs-lookup"><span data-stu-id="5376f-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="5376f-126">不能对组的成员进行计数，因为如果将应用程序扩展到多台服务器，此信息不可用。</span><span class="sxs-lookup"><span data-stu-id="5376f-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="5376f-127">若要在使用组时保护对资源的访问，请在 ASP.NET Core 中使用 [身份验证和授权](xref:signalr/authn-and-authz) 功能。</span><span class="sxs-lookup"><span data-stu-id="5376f-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="5376f-128">如果用户仅当凭据对该组有效时才添加到组中，则发送到该组的消息将仅发送到授权用户。</span><span class="sxs-lookup"><span data-stu-id="5376f-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="5376f-129">但组不是一种安全功能。</span><span class="sxs-lookup"><span data-stu-id="5376f-129">However, groups are not a security feature.</span></span> <span data-ttu-id="5376f-130">身份验证声明具有组不具备的功能，如过期和吊销。</span><span class="sxs-lookup"><span data-stu-id="5376f-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="5376f-131">如果用户对组的访问权限被吊销，应用必须从组中显式删除用户。</span><span class="sxs-lookup"><span data-stu-id="5376f-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="5376f-132">组名区分大小写。</span><span class="sxs-lookup"><span data-stu-id="5376f-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="5376f-133">相关资源</span><span class="sxs-lookup"><span data-stu-id="5376f-133">Related resources</span></span>

* [<span data-ttu-id="5376f-134">入门</span><span class="sxs-lookup"><span data-stu-id="5376f-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="5376f-135">中心</span><span class="sxs-lookup"><span data-stu-id="5376f-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="5376f-136">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="5376f-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
