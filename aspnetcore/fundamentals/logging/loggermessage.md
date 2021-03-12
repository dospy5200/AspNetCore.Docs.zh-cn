---
title: 在 ASP.NET Core 中使用 LoggerMessage 的高性能日志记录
author: rick-anderson
description: 了解如何使用 LoggerMessage 创建可缓存的委托。对于高性能日志记录方案，这些委托需要更少的对象分配。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
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
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: d6fae8d2e056c0a578b64322bb82027523904254
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587393"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>在 ASP.NET Core 中使用 LoggerMessage 的高性能日志记录

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage> 功能创建可缓存的委托，该功能比[记录器扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>）需要的对象分配和计算开销少。 对于高性能日志记录方案，请使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。

与记录器扩展方法相比，<xref:Microsoft.Extensions.Logging.LoggerMessage> 具有以下性能优势：

* 记录器扩展方法需要将值类型（例如 `int`）“装箱”（转换）到 `object` 中。 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式使用带强类型参数的静态 <xref:System.Action> 字段和扩展方法来避免装箱。
* 记录器扩展方法每次写入日志消息时必须分析消息模板（命名的格式字符串）。 如果已定义消息，那么 <xref:Microsoft.Extensions.Logging.LoggerMessage> 只需分析一次模板即可。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/logging/loggermessage/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

此示例应用通过基本引号跟踪系统演示 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。 此应用使用内存中数据库添加和删除引号。 发生这些操作时，通过 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式生成日志消息。

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define（LogLevel、EventId、字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)创建用于记录消息的 <xref:System.Action> 委托。 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 重载允许向命名的格式字符串（模板）传递最多六个类型参数。

提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的字符串是一个模板，而不是内插字符串。 占位符按照指定类型的顺序填充。 模板中的占位符名称在各个模板中应当具备描述性和一致性。 它们在结构化的日志数据中充当属性名称。 对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。 例如：`{Count}`、`{FirstName}`。

每条日志消息都是一个 <xref:System.Action>，保存在由 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 创建的静态字段中。 例如，示例应用创建一个字段来为索引页 (Internal/LoggerExtensions.cs) 描述 GET 请求的日志消息：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

对于 <xref:System.Action>，指定：

* 日志级别。
* 具有静态扩展方法名称的唯一事件标识符 (<xref:Microsoft.Extensions.Logging.EventId>)。
* 消息模板（命名的格式字符串）。 

对示例应用的索引页的请求设置：

* 将日志级别设置为 `Information`。
* 将事件 ID 设置为具有 `IndexPageRequested` 方法名称的 `1`。
* 将消息模板（命名的格式字符串）设置为字符串。

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

结构化日志记录存储可以使用事件名称（当它获得事件 ID 时）来丰富日志记录。 例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 使用该事件名称。

通过强类型扩展方法调用 <xref:System.Action>。 `IndexPageRequested` 方法在示例应用中记录索引页 GET 请求的消息：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

在 Pages/Index.cshtml.cs 的 `OnGetAsync` 方法中，在记录器上调用 `IndexPageRequested`：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

检查应用的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

要将参数传递给日志消息，创建静态字段时最多定义六种类型。 通过为 <xref:System.Action> 字段定义 `string` 类型来添加引号时，示例应用会记录一个字符串：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

委托的日志消息模板从提供的类型接收其占位符值。 示例应用定义一个委托，用于在 quote 参数是 `string` 的位置添加引号：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

用于添加引号的静态扩展方法 `QuoteAdded` 接收 quote 参数值并将其传递给 <xref:System.Action> 委托：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

在索引页的页面模型 (Pages/Index.cshtml.cs) 中，调用 `QuoteAdded` 来记录消息：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

检查应用的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

本示例应用实现用于删除引号的 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式。 为成功的删除操作记录提示性信息。 引发异常时，为删除操作记录错误消息。 针对未成功的删除操作，日志消息包括异常堆栈跟踪 (Internal/LoggerExtensions.cs)：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

请注意异常如何传递到 `QuoteDeleteFailed` 中的委托：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

在索引页的页面模型中，成功删除引号时会在记录器上调用 `QuoteDeleted` 方法。 如果找不到要删除的引号，则会引发 <xref:System.ArgumentNullException>。 通过 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并在 [catch](/dotnet/csharp/language-reference/keywords/try-catch) 块 (Pages/Index.cshtml.cs) 中调用记录器上的 `QuoteDeleteFailed` 方法来记录异常：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

成功删除引号时，检查应用的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

引号删除失败时，检查应用的控制台输出。 请注意，异常包括在日志消息中：

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope（字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*)创建一个用于定义[日志作用域](xref:fundamentals/logging/index#log-scopes)的 <xref:System.Func%601> 委托。 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 重载允许向命名的格式字符串（模板）传递最多三个类型参数。

<xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法也一样，提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法的字符串是一个模板，而不是内插字符串。 占位符按照指定类型的顺序填充。 模板中的占位符名称在各个模板中应当具备描述性和一致性。 它们在结构化的日志数据中充当属性名称。 对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。 例如：`{Count}`、`{FirstName}`。

使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法定义一个[日志作用域](xref:fundamentals/logging/index#log-scopes)，以应用到一系列日志消息中。

示例应用含有一个“全部清除”按钮，用于删除数据库中的所有引号。 通过一次删除一个引号来将其删除。 每当删除一个引号时，都会在记录器上调用 `QuoteDeleted` 方法。 在这些日志消息中会添加一个日志作用域。

在 appsettings.json 的控制台记录器部分中启用 `IncludeScopes`：

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

要创建日志作用域，请添加一个字段来保存该作用域的 <xref:System.Func%601> 委托。 示例应用创建一个名为 `_allQuotesDeletedScope` (Internal/LoggerExtensions.cs) 的字段：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 来创建委托。 调用委托时最多可以指定三种类型作为模板参数使用。 示例应用使用包含删除的引号数量的消息模板（`int` 类型）：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

为日志消息提供一种静态扩展方法。 包含已命名属性的任何类型参数（这些参数出现在消息模板中）。 示例应用采用引号的 `count`，以删除并返回 `_allQuotesDeletedScope`：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

该作用域将日志记录扩展调用包装在 [using](/dotnet/csharp/language-reference/keywords/using-statement) 块中：

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

检查应用控制台输出中的日志消息。 以下结果显示删除的三个引号，以及包含的日志作用域消息：

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage> 功能创建可缓存的委托，该功能比[记录器扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)（例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>）需要的对象分配和计算开销少。 对于高性能日志记录方案，请使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。

与记录器扩展方法相比，<xref:Microsoft.Extensions.Logging.LoggerMessage> 具有以下性能优势：

* 记录器扩展方法需要将值类型（例如 `int`）“装箱”（转换）到 `object` 中。 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式使用带强类型参数的静态 <xref:System.Action> 字段和扩展方法来避免装箱。
* 记录器扩展方法每次写入日志消息时必须分析消息模板（命名的格式字符串）。 如果已定义消息，那么 <xref:Microsoft.Extensions.Logging.LoggerMessage> 只需分析一次模板即可。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/logging/loggermessage/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

此示例应用通过基本引号跟踪系统演示 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。 此应用使用内存中数据库添加和删除引号。 发生这些操作时，通过 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式生成日志消息。

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define（LogLevel、EventId、字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*)创建用于记录消息的 <xref:System.Action> 委托。 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 重载允许向命名的格式字符串（模板）传递最多六个类型参数。

提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的字符串是一个模板，而不是内插字符串。 占位符按照指定类型的顺序填充。 模板中的占位符名称在各个模板中应当具备描述性和一致性。 它们在结构化的日志数据中充当属性名称。 对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。 例如：`{Count}`、`{FirstName}`。

每条日志消息都是一个 <xref:System.Action>，保存在由 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 创建的静态字段中。 例如，示例应用创建一个字段来为索引页 (Internal/LoggerExtensions.cs) 描述 GET 请求的日志消息：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

对于 <xref:System.Action>，指定：

* 日志级别。
* 具有静态扩展方法名称的唯一事件标识符 (<xref:Microsoft.Extensions.Logging.EventId>)。
* 消息模板（命名的格式字符串）。 

对示例应用的索引页的请求设置：

* 将日志级别设置为 `Information`。
* 将事件 ID 设置为具有 `IndexPageRequested` 方法名称的 `1`。
* 将消息模板（命名的格式字符串）设置为字符串。

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

结构化日志记录存储可以使用事件名称（当它获得事件 ID 时）来丰富日志记录。 例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 使用该事件名称。

通过强类型扩展方法调用 <xref:System.Action>。 `IndexPageRequested` 方法在示例应用中记录索引页 GET 请求的消息：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

在 Pages/Index.cshtml.cs 的 `OnGetAsync` 方法中，在记录器上调用 `IndexPageRequested`：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

检查应用的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

要将参数传递给日志消息，创建静态字段时最多定义六种类型。 通过为 <xref:System.Action> 字段定义 `string` 类型来添加引号时，示例应用会记录一个字符串：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

委托的日志消息模板从提供的类型接收其占位符值。 示例应用定义一个委托，用于在 quote 参数是 `string` 的位置添加引号：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

用于添加引号的静态扩展方法 `QuoteAdded` 接收 quote 参数值并将其传递给 <xref:System.Action> 委托：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

在索引页的页面模型 (Pages/Index.cshtml.cs) 中，调用 `QuoteAdded` 来记录消息：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

检查应用的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

本示例应用实现用于删除引号的 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式。 为成功的删除操作记录提示性信息。 引发异常时，为删除操作记录错误消息。 针对未成功的删除操作，日志消息包括异常堆栈跟踪 (Internal/LoggerExtensions.cs)：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

请注意异常如何传递到 `QuoteDeleteFailed` 中的委托：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

在索引页的页面模型中，成功删除引号时会在记录器上调用 `QuoteDeleted` 方法。 如果找不到要删除的引号，则会引发 <xref:System.ArgumentNullException>。 通过 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并在 [catch](/dotnet/csharp/language-reference/keywords/try-catch) 块 (Pages/Index.cshtml.cs) 中调用记录器上的 `QuoteDeleteFailed` 方法来记录异常：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

成功删除引号时，检查应用的控制台输出：

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

引号删除失败时，检查应用的控制台输出。 请注意，异常包括在日志消息中：

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope（字符串）](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*)创建一个用于定义[日志作用域](xref:fundamentals/logging/index#log-scopes)的 <xref:System.Func%601> 委托。 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 重载允许向命名的格式字符串（模板）传递最多三个类型参数。

<xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法也一样，提供给 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法的字符串是一个模板，而不是内插字符串。 占位符按照指定类型的顺序填充。 模板中的占位符名称在各个模板中应当具备描述性和一致性。 它们在结构化的日志数据中充当属性名称。 对于占位符名称，建议使用[帕斯卡拼写法](/dotnet/standard/design-guidelines/capitalization-conventions)。 例如：`{Count}`、`{FirstName}`。

使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法定义一个[日志作用域](xref:fundamentals/logging/index#log-scopes)，以应用到一系列日志消息中。

示例应用含有一个“全部清除”按钮，用于删除数据库中的所有引号。 通过一次删除一个引号来将其删除。 每当删除一个引号时，都会在记录器上调用 `QuoteDeleted` 方法。 在这些日志消息中会添加一个日志作用域。

在 appsettings.json 的控制台记录器部分中启用 `IncludeScopes`：

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

要创建日志作用域，请添加一个字段来保存该作用域的 <xref:System.Func%601> 委托。 示例应用创建一个名为 `_allQuotesDeletedScope` (Internal/LoggerExtensions.cs) 的字段：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 来创建委托。 调用委托时最多可以指定三种类型作为模板参数使用。 示例应用使用包含删除的引号数量的消息模板（`int` 类型）：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

为日志消息提供一种静态扩展方法。 包含已命名属性的任何类型参数（这些参数出现在消息模板中）。 示例应用采用引号的 `count`，以删除并返回 `_allQuotesDeletedScope`：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

该作用域将日志记录扩展调用包装在 [using](/dotnet/csharp/language-reference/keywords/using-statement) 块中：

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

检查应用控制台输出中的日志消息。 以下结果显示删除的三个引号，以及包含的日志作用域消息：

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [日志记录](xref:fundamentals/logging/index)
