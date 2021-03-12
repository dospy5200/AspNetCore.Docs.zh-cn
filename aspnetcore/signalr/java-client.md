---
title: ASP.NET Core SignalR Java 客户端
author: mikaelm12
description: 了解如何使用 ASP.NET Core SignalR Java 客户端。
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: bdfaf50895612e739eb5ca068a76755f97cf24c2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588069"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="f274e-103">ASP.NET Core SignalR Java 客户端</span><span class="sxs-lookup"><span data-stu-id="f274e-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="f274e-104">作者：[Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="f274e-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="f274e-105">Java 客户端允许 SignalR 从 java 代码（包括 Android 应用）连接到 ASP.NET Core 服务器。</span><span class="sxs-lookup"><span data-stu-id="f274e-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="f274e-106">与 [JavaScript 客户端](xref:signalr/javascript-client) 和 [.net 客户](xref:signalr/dotnet-client)端一样，Java 客户端允许您实时接收消息并向中心发送消息。</span><span class="sxs-lookup"><span data-stu-id="f274e-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="f274e-107">Java 客户端在 ASP.NET Core 2.2 及更高版本中可用。</span><span class="sxs-lookup"><span data-stu-id="f274e-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="f274e-108">本文中引用的示例 Java 控制台应用使用 SignalR java 客户端。</span><span class="sxs-lookup"><span data-stu-id="f274e-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="f274e-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/java-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f274e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="f274e-110">安装 SignalR Java 客户端包</span><span class="sxs-lookup"><span data-stu-id="f274e-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="f274e-111">*Signalr-1.0.0* JAR 文件允许客户端连接到 SignalR 中心。</span><span class="sxs-lookup"><span data-stu-id="f274e-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="f274e-112">若要查找最新的 JAR 文件版本号，请参阅 [Maven 搜索结果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)。</span><span class="sxs-lookup"><span data-stu-id="f274e-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="f274e-113">如果使用 Gradle，请将以下行添加到 `dependencies` *Gradle* 文件的部分：</span><span class="sxs-lookup"><span data-stu-id="f274e-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="f274e-114">如果使用的是 Maven，请在pom.xml文件的元素中添加以下行 `<dependencies>` ： </span><span class="sxs-lookup"><span data-stu-id="f274e-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="f274e-115">连接到集线器</span><span class="sxs-lookup"><span data-stu-id="f274e-115">Connect to a hub</span></span>

<span data-ttu-id="f274e-116">若要建立 `HubConnection` ， `HubConnectionBuilder` 应使用。</span><span class="sxs-lookup"><span data-stu-id="f274e-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="f274e-117">在建立连接时，可以配置中心 URL 和日志级别。</span><span class="sxs-lookup"><span data-stu-id="f274e-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="f274e-118">在之前调用任何方法，配置任何所需的选项 `HubConnectionBuilder` `build` 。</span><span class="sxs-lookup"><span data-stu-id="f274e-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="f274e-119">启动与的连接 `start` 。</span><span class="sxs-lookup"><span data-stu-id="f274e-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="f274e-120">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="f274e-120">Call hub methods from client</span></span>

<span data-ttu-id="f274e-121">调用以调用 `send` 集线器方法。</span><span class="sxs-lookup"><span data-stu-id="f274e-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="f274e-122">将 hub 方法名称和在 hub 方法中定义的任何自变量传递给 `send` 。</span><span class="sxs-lookup"><span data-stu-id="f274e-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="f274e-123">仅 SignalR 在 *默认* 模式下使用 Azure 服务时，才支持从客户端调用中心方法。</span><span class="sxs-lookup"><span data-stu-id="f274e-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="f274e-124">有关详细信息，请参阅 [Signalr GitHub 存储库)  (常见问题解答 ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。</span><span class="sxs-lookup"><span data-stu-id="f274e-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="f274e-125">从中心调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="f274e-125">Call client methods from hub</span></span>

<span data-ttu-id="f274e-126">用于 `hubConnection.on` 在客户端上定义中心可调用的方法。</span><span class="sxs-lookup"><span data-stu-id="f274e-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="f274e-127">在生成之后但在开始连接之前定义方法。</span><span class="sxs-lookup"><span data-stu-id="f274e-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="f274e-128">添加日志记录</span><span class="sxs-lookup"><span data-stu-id="f274e-128">Add logging</span></span>

<span data-ttu-id="f274e-129">SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="f274e-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="f274e-130">这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。</span><span class="sxs-lookup"><span data-stu-id="f274e-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="f274e-131">下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="f274e-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="f274e-132">如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：</span><span class="sxs-lookup"><span data-stu-id="f274e-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="f274e-133">可以安全地忽略此情况。</span><span class="sxs-lookup"><span data-stu-id="f274e-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="f274e-134">Android 开发说明</span><span class="sxs-lookup"><span data-stu-id="f274e-134">Android development notes</span></span>

<span data-ttu-id="f274e-135">对于客户端功能 Android SDK 兼容性 SignalR ，在指定目标 Android SDK 版本时，请考虑以下各项：</span><span class="sxs-lookup"><span data-stu-id="f274e-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="f274e-136">SignalRJava 客户端将在 ANDROID API Level 16 和更高版本上运行。</span><span class="sxs-lookup"><span data-stu-id="f274e-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="f274e-137">通过 Azure 服务连接 SignalR 将需要 ANDROID API 级别20和更高版本，因为 [Azure SignalR 服务](/azure/azure-signalr/signalr-overview) 需要 TLS 1.2，并且不支持基于 sha-1 的密码套件。</span><span class="sxs-lookup"><span data-stu-id="f274e-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="f274e-138">Android [增加了对 SHA-256 (及更) 高](https://developer.android.com/reference/javax/net/ssl/SSLSocket) 版本的支持。</span><span class="sxs-lookup"><span data-stu-id="f274e-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="f274e-139">配置持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="f274e-139">Configure bearer token authentication</span></span>

<span data-ttu-id="f274e-140">在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)提供 "访问令牌工厂" 来配置用于身份验证的持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="f274e-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="f274e-141">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="f274e-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="f274e-142">如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="f274e-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a><span data-ttu-id="f274e-143">在 Java 中传递类信息</span><span class="sxs-lookup"><span data-stu-id="f274e-143">Passing Class information in Java</span></span>

<span data-ttu-id="f274e-144">`on` `invoke` `stream` 在 Java 客户端中调用的、或方法时 `HubConnection` ，用户应传递 `Type` 对象而不是 `Class<?>` 对象来描述 `Object` 传递给方法的任何泛型。</span><span class="sxs-lookup"><span data-stu-id="f274e-144">When calling the `on`, `invoke`, or `stream` methods of `HubConnection` in the Java client, users should pass a `Type` object rather than a `Class<?>` object to describe any generic `Object` passed to the method.</span></span> <span data-ttu-id="f274e-145">`Type`可以使用提供的类来获取 `TypeReference` 。</span><span class="sxs-lookup"><span data-stu-id="f274e-145">A `Type` can be acquired using the provided `TypeReference` class.</span></span> <span data-ttu-id="f274e-146">例如，使用名为的自定义泛型类 `Foo<T>` ，下面的代码获取 `Type` ：</span><span class="sxs-lookup"><span data-stu-id="f274e-146">For example, using a custom generic class named `Foo<T>`, the following code gets the `Type`:</span></span>

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

<span data-ttu-id="f274e-147">对于非泛型，如基元或其他非参数化类型（如 `String` ），只需使用内置 `.class` 。</span><span class="sxs-lookup"><span data-stu-id="f274e-147">For non-generics, such as primitives or other non-parameterized types like `String`, you can simply use the built-in `.class`.</span></span>

<span data-ttu-id="f274e-148">使用一个或多个对象类型调用这些方法之一时，请在调用方法时使用泛型语法。</span><span class="sxs-lookup"><span data-stu-id="f274e-148">When calling one of these methods with one or more object types, use the generics syntax when invoking the method.</span></span> <span data-ttu-id="f274e-149">例如，当注册 `on` 一个名为的方法 `func` （该方法采用字符串和对象作为参数）时，请 `Foo<String>` 使用以下代码设置一个操作来打印参数：</span><span class="sxs-lookup"><span data-stu-id="f274e-149">For example, when registering an `on` handler for a method named `func`, which takes as arguments a String and a `Foo<String>` object, use the following code to set an action to print the arguments:</span></span>

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

<span data-ttu-id="f274e-150">此约定是必需的，因为我们无法通过 `Object.getClass` Java 中的类型擦除来检索有关复杂类型的完整信息。</span><span class="sxs-lookup"><span data-stu-id="f274e-150">This convention is necessary because we can not retrieve complete information about complex types with the `Object.getClass` method due to type erasure in Java.</span></span> <span data-ttu-id="f274e-151">例如， `getClass` 对的调用 `ArrayList<String>` 将不会返回 `Class<ArrayList<String>>` ，而是不会 `Class<ArrayList>` 为反序列化程序提供足够的信息来正确地反序列化传入消息。</span><span class="sxs-lookup"><span data-stu-id="f274e-151">For example, calling `getClass` on an `ArrayList<String>` would not return `Class<ArrayList<String>>`, but rather `Class<ArrayList>`, which does not give the deserializer enough information to correctly deserialize an incoming message.</span></span> <span data-ttu-id="f274e-152">对于自定义对象也是如此。</span><span class="sxs-lookup"><span data-stu-id="f274e-152">The same is true for custom objects.</span></span>

::: moniker-end

## <a name="known-limitations"></a><span data-ttu-id="f274e-153">已知的限制</span><span class="sxs-lookup"><span data-stu-id="f274e-153">Known limitations</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="f274e-154">传输回退和服务器发送事件传输不受支持。</span><span class="sxs-lookup"><span data-stu-id="f274e-154">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* <span data-ttu-id="f274e-155">传输回退和服务器发送事件传输不受支持。</span><span class="sxs-lookup"><span data-stu-id="f274e-155">Transport fallback and the Server Sent Events transport aren't supported.</span></span>
* <span data-ttu-id="f274e-156">仅支持 JSON 协议。</span><span class="sxs-lookup"><span data-stu-id="f274e-156">Only the JSON protocol is supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="f274e-157">仅支持 JSON 协议。</span><span class="sxs-lookup"><span data-stu-id="f274e-157">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="f274e-158">仅支持 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="f274e-158">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="f274e-159">目前尚不支持流式处理。</span><span class="sxs-lookup"><span data-stu-id="f274e-159">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f274e-160">其他资源</span><span class="sxs-lookup"><span data-stu-id="f274e-160">Additional resources</span></span>

* [<span data-ttu-id="f274e-161">Java API 参考</span><span class="sxs-lookup"><span data-stu-id="f274e-161">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="f274e-162">Azure SignalR Service 无服务器文档</span><span class="sxs-lookup"><span data-stu-id="f274e-162">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
