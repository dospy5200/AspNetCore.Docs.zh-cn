---
title: razor 语法参考 ASP.NET Core
author: rick-anderson
description: 了解将 Razor 基于服务器的代码嵌入网页的标记语法。
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: 60471232b3373039404b27c4afd1a1725d4d21eb
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586860"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="f0a82-103">ASP.NET Core 的 Razor 语法参考</span><span class="sxs-lookup"><span data-stu-id="f0a82-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="f0a82-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Taylor Mullen](https://twitter.com/ntaylormullen)和 [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="f0a82-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="f0a82-105">Razor 是一个用于将基于服务器的代码嵌入到网页中的标记语法。</span><span class="sxs-lookup"><span data-stu-id="f0a82-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="f0a82-106">Razor语法由 Razor 标记、c # 和 HTML 组成。</span><span class="sxs-lookup"><span data-stu-id="f0a82-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="f0a82-107">通常包含 Razor 的文件的扩展名为 *...*</span><span class="sxs-lookup"><span data-stu-id="f0a82-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="f0a82-108">Razor还可在 [ Razor 组件](xref:blazor/components/index)*文件 () 中找到。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="f0a82-109">呈现 HTML</span><span class="sxs-lookup"><span data-stu-id="f0a82-109">Rendering HTML</span></span>

<span data-ttu-id="f0a82-110">默认 Razor 语言为 HTML。</span><span class="sxs-lookup"><span data-stu-id="f0a82-110">The default Razor language is HTML.</span></span> <span data-ttu-id="f0a82-111">从标记呈现 HTML 与 Razor 从 html 文件呈现 html 没有什么不同。</span><span class="sxs-lookup"><span data-stu-id="f0a82-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="f0a82-112">在中， *cshtml* 文件中的 HTML 标记 Razor 不变。</span><span class="sxs-lookup"><span data-stu-id="f0a82-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="f0a82-113">Razor 语法</span><span class="sxs-lookup"><span data-stu-id="f0a82-113">Razor syntax</span></span>

<span data-ttu-id="f0a82-114">Razor 支持 c #，并使用 `@` 符号从 HTML 转换为 c #。</span><span class="sxs-lookup"><span data-stu-id="f0a82-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="f0a82-115">Razor 计算 c # 表达式并在 HTML 输出中呈现。</span><span class="sxs-lookup"><span data-stu-id="f0a82-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="f0a82-116">当 `@` 符号后跟[ Razor 保留关键字](#razor-reserved-keywords)时，它会转换为 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="f0a82-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="f0a82-117">否则会转换为纯 C#。</span><span class="sxs-lookup"><span data-stu-id="f0a82-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="f0a82-118">若要对 `@` 标记中的符号进行转义 Razor ，请使用第二个 `@` 符号：</span><span class="sxs-lookup"><span data-stu-id="f0a82-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="f0a82-119">该代码在 HTML 中使用单个 `@` 符号呈现：</span><span class="sxs-lookup"><span data-stu-id="f0a82-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="f0a82-120">包含电子邮件地址的 HTML 属性和内容不将 `@` 符号视为转换字符。</span><span class="sxs-lookup"><span data-stu-id="f0a82-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="f0a82-121">以下示例中的电子邮件地址将通过分析来保持不变 Razor ：</span><span class="sxs-lookup"><span data-stu-id="f0a82-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="f0a82-122">隐式 Razor 表达式</span><span class="sxs-lookup"><span data-stu-id="f0a82-122">Implicit Razor expressions</span></span>

<span data-ttu-id="f0a82-123">隐式 Razor 表达式以开头， `@` 后跟 c # 代码：</span><span class="sxs-lookup"><span data-stu-id="f0a82-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="f0a82-124">隐式表达式不能包含空格，但 C# `await` 关键字除外。</span><span class="sxs-lookup"><span data-stu-id="f0a82-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="f0a82-125">如果该 C# 语句具有明确的结束标记，则可以混用空格：</span><span class="sxs-lookup"><span data-stu-id="f0a82-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="f0a82-126">隐式表达式 **不能** 包含 C# 泛型，因为括号 (`<>`) 内的字符会被解释为 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="f0a82-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="f0a82-127">以下代码 **无** 效：</span><span class="sxs-lookup"><span data-stu-id="f0a82-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="f0a82-128">上述代码生成与以下错误之一类似的编译器错误：</span><span class="sxs-lookup"><span data-stu-id="f0a82-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="f0a82-129">"int" 元素未结束。</span><span class="sxs-lookup"><span data-stu-id="f0a82-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="f0a82-130">所有元素都必须自结束或具有匹配的结束标记。</span><span class="sxs-lookup"><span data-stu-id="f0a82-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="f0a82-131">无法将方法组 "GenericMethod" 转换为非委托类型 "object"。</span><span class="sxs-lookup"><span data-stu-id="f0a82-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="f0a82-132">是否希望调用此方法?\`</span><span class="sxs-lookup"><span data-stu-id="f0a82-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="f0a82-133">泛型方法调用必须在[显式 Razor 表达式](#explicit-razor-expressions)或[ Razor 代码块](#razor-code-blocks)中进行包装。</span><span class="sxs-lookup"><span data-stu-id="f0a82-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="f0a82-134">显式 Razor 表达式</span><span class="sxs-lookup"><span data-stu-id="f0a82-134">Explicit Razor expressions</span></span>

<span data-ttu-id="f0a82-135">显式 Razor 表达式由 `@` 带对称括号的符号组成。</span><span class="sxs-lookup"><span data-stu-id="f0a82-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="f0a82-136">若要呈现上周的时间，请 Razor 使用以下标记：</span><span class="sxs-lookup"><span data-stu-id="f0a82-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="f0a82-137">将计算 `@()` 括号中的所有内容，并将其呈现到输出中。</span><span class="sxs-lookup"><span data-stu-id="f0a82-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="f0a82-138">前面部分中所述的隐式表达式通常不能包含空格。</span><span class="sxs-lookup"><span data-stu-id="f0a82-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="f0a82-139">在下面的代码中，不会从当前时间减去一周：</span><span class="sxs-lookup"><span data-stu-id="f0a82-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="f0a82-140">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="f0a82-141">可以使用显式表达式将文本与表达式结果串联起来：</span><span class="sxs-lookup"><span data-stu-id="f0a82-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="f0a82-142">如果不使用显式表达式，`<p>Age@joe.Age</p>` 会被视为电子邮件地址，因此会呈现 `<p>Age@joe.Age</p>`。</span><span class="sxs-lookup"><span data-stu-id="f0a82-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="f0a82-143">如果编写为显式表达式，则呈现 `<p>Age33</p>`。</span><span class="sxs-lookup"><span data-stu-id="f0a82-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="f0a82-144">显式表达式可用于从 *.cshtml* 文件中的泛型方法呈现输出。</span><span class="sxs-lookup"><span data-stu-id="f0a82-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="f0a82-145">以下标记显示了如何更正之前出现的由 C# 泛型的括号引起的错误。</span><span class="sxs-lookup"><span data-stu-id="f0a82-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="f0a82-146">此代码以显式表达式的形式编写：</span><span class="sxs-lookup"><span data-stu-id="f0a82-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="f0a82-147">表达式编码</span><span class="sxs-lookup"><span data-stu-id="f0a82-147">Expression encoding</span></span>

<span data-ttu-id="f0a82-148">计算结果为字符串的 C# 表达式采用 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="f0a82-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="f0a82-149">计算结果为 `IHtmlContent` 的 C# 表达式直接通过 `IHtmlContent.WriteTo` 呈现。</span><span class="sxs-lookup"><span data-stu-id="f0a82-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="f0a82-150">计算结果不为 `IHtmlContent` 的 C# 表达式通过 `ToString` 转换为字符串，并在呈现前进行编码。</span><span class="sxs-lookup"><span data-stu-id="f0a82-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="f0a82-151">前面的代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="f0a82-152">HTML 在浏览器中显示为纯文本：</span><span class="sxs-lookup"><span data-stu-id="f0a82-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="f0a82-153">&lt;跨越 &gt; Hello World &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="f0a82-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="f0a82-154">`HtmlHelper.Raw` 输出不进行编码，但呈现为 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="f0a82-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="f0a82-155">对未经审查的用户输入使用 `HtmlHelper.Raw` 会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="f0a82-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="f0a82-156">用户输入可能包含恶意的 JavaScript 或其他攻击。</span><span class="sxs-lookup"><span data-stu-id="f0a82-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="f0a82-157">审查用户输入比较困难。</span><span class="sxs-lookup"><span data-stu-id="f0a82-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="f0a82-158">应避免对用户输入使用 `HtmlHelper.Raw`。</span><span class="sxs-lookup"><span data-stu-id="f0a82-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="f0a82-159">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="f0a82-160">Razor 代码块</span><span class="sxs-lookup"><span data-stu-id="f0a82-160">Razor code blocks</span></span>

<span data-ttu-id="f0a82-161">Razor 代码块以开头 `@` ，并由括起来 `{}` 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="f0a82-162">代码块内的 C# 代码不会呈现，这点与表达式不同。</span><span class="sxs-lookup"><span data-stu-id="f0a82-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="f0a82-163">一个视图中的代码块和表达式共享相同的作用域并按顺序进行定义：</span><span class="sxs-lookup"><span data-stu-id="f0a82-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="f0a82-164">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0a82-165">在代码块中，使用标记将[本地函数](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)声明为用作模板化方法：</span><span class="sxs-lookup"><span data-stu-id="f0a82-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="f0a82-166">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="f0a82-167">隐式转换</span><span class="sxs-lookup"><span data-stu-id="f0a82-167">Implicit transitions</span></span>

<span data-ttu-id="f0a82-168">代码块中的默认语言是 c #，但 Razor 页面可转换回 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="f0a82-169">带分隔符的显式转换</span><span class="sxs-lookup"><span data-stu-id="f0a82-169">Explicit delimited transition</span></span>

<span data-ttu-id="f0a82-170">若要定义应呈现 HTML 的代码块的子节，请将字符括在标记后 Razor `<text>` ：</span><span class="sxs-lookup"><span data-stu-id="f0a82-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="f0a82-171">使用此方法可呈现未被 HTML 标记括起来的 HTML。</span><span class="sxs-lookup"><span data-stu-id="f0a82-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="f0a82-172">如果没有 HTML 或 Razor 标记，则 Razor 会发生运行时错误。</span><span class="sxs-lookup"><span data-stu-id="f0a82-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="f0a82-173">`<text>` 标记可用于在呈现内容时控制空格：</span><span class="sxs-lookup"><span data-stu-id="f0a82-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="f0a82-174">仅呈现 `<text>` 标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="f0a82-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="f0a82-175">`<text>` 标记之前或之后的空格不会显示在 HTML 输出中。</span><span class="sxs-lookup"><span data-stu-id="f0a82-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="f0a82-176">显式行转换</span><span class="sxs-lookup"><span data-stu-id="f0a82-176">Explicit line transition</span></span>

<span data-ttu-id="f0a82-177">要在代码块内以 HTML 形式呈现整个行的其余内容，请使用 `@:` 语法：</span><span class="sxs-lookup"><span data-stu-id="f0a82-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="f0a82-178">如果 `@:` 代码中没有，则 Razor 会生成运行时错误。</span><span class="sxs-lookup"><span data-stu-id="f0a82-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="f0a82-179">`@`文件中的额外字符 Razor 可能会导致在块中后面的语句中出现编译器错误。</span><span class="sxs-lookup"><span data-stu-id="f0a82-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="f0a82-180">这些编译器错误可能难以理解，因为实际错误发生在报告的错误之前。</span><span class="sxs-lookup"><span data-stu-id="f0a82-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="f0a82-181">将多个隐式/显式表达式合并到单个代码块以后，经常会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="f0a82-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="f0a82-182">控制结构</span><span class="sxs-lookup"><span data-stu-id="f0a82-182">Control structures</span></span>

<span data-ttu-id="f0a82-183">控制结构是对代码块的扩展。</span><span class="sxs-lookup"><span data-stu-id="f0a82-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="f0a82-184">代码块的各个方面（转换为标记、内联 C#）同样适用于以下结构：</span><span class="sxs-lookup"><span data-stu-id="f0a82-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="f0a82-185">条件语句 `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="f0a82-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="f0a82-186">`@if` 控制何时运行代码：</span><span class="sxs-lookup"><span data-stu-id="f0a82-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="f0a82-187">`else` 和 `else if` 不需要 `@` 符号：</span><span class="sxs-lookup"><span data-stu-id="f0a82-187">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="f0a82-188">以下标记展示如何使用 switch 语句：</span><span class="sxs-lookup"><span data-stu-id="f0a82-188">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="f0a82-189">Hal `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="f0a82-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="f0a82-190">可以使用循环控制语句呈现模板化 HTML。</span><span class="sxs-lookup"><span data-stu-id="f0a82-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="f0a82-191">若要呈现一组人员：</span><span class="sxs-lookup"><span data-stu-id="f0a82-191">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="f0a82-192">支持以下循环语句：</span><span class="sxs-lookup"><span data-stu-id="f0a82-192">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="f0a82-193">复合语句 `@using`</span><span class="sxs-lookup"><span data-stu-id="f0a82-193">Compound `@using`</span></span>

<span data-ttu-id="f0a82-194">在 C# 中，`using` 语句用于确保释放对象。</span><span class="sxs-lookup"><span data-stu-id="f0a82-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="f0a82-195">在中 Razor ，使用相同的机制来创建包含其他内容的 HTML 帮助器。</span><span class="sxs-lookup"><span data-stu-id="f0a82-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="f0a82-196">在下面的代码中，HTML 帮助程序使用 `@using` 语句呈现 `<form>` 标记：</span><span class="sxs-lookup"><span data-stu-id="f0a82-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

<span data-ttu-id="f0a82-197">异常处理与 C# 类似：</span><span class="sxs-lookup"><span data-stu-id="f0a82-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="f0a82-198">Razor 能够用 lock 语句保护关键部分：</span><span class="sxs-lookup"><span data-stu-id="f0a82-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="f0a82-199">注释</span><span class="sxs-lookup"><span data-stu-id="f0a82-199">Comments</span></span>

<span data-ttu-id="f0a82-200">Razor 支持 c # 和 HTML 注释：</span><span class="sxs-lookup"><span data-stu-id="f0a82-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="f0a82-201">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="f0a82-202">Razor 在呈现网页之前，服务器将删除注释。</span><span class="sxs-lookup"><span data-stu-id="f0a82-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="f0a82-203">Razor 用于 `@*  *@` 分隔注释。</span><span class="sxs-lookup"><span data-stu-id="f0a82-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="f0a82-204">以下代码已被注释禁止，因此服务器不呈现任何标记：</span><span class="sxs-lookup"><span data-stu-id="f0a82-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="f0a82-205">指令</span><span class="sxs-lookup"><span data-stu-id="f0a82-205">Directives</span></span>

<span data-ttu-id="f0a82-206">Razor 指令由带有符号后的保留关键字的隐式表达式表示 `@` 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="f0a82-207">指令通常用于更改视图分析方式或启用不同的功能。</span><span class="sxs-lookup"><span data-stu-id="f0a82-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="f0a82-208">了解如何 Razor 为视图生成代码可以更轻松地了解指令的工作方式。</span><span class="sxs-lookup"><span data-stu-id="f0a82-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="f0a82-209">该代码生成与下面类似的类：</span><span class="sxs-lookup"><span data-stu-id="f0a82-209">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="f0a82-210">在本文的后面部分中，将 [检查 Razor 为视图生成的 c # 类](#inspect-the-razor-c-class-generated-for-a-view) ，并说明如何查看此生成的类。</span><span class="sxs-lookup"><span data-stu-id="f0a82-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="f0a82-211">`@attribute` 指令将给定的属性添加到生成的页或视图的类中。</span><span class="sxs-lookup"><span data-stu-id="f0a82-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="f0a82-212">以下示例添加 `[Authorize]` 属性：</span><span class="sxs-lookup"><span data-stu-id="f0a82-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="f0a82-213">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-214">`@code`块使[ Razor 组件](xref:blazor/components/index)可以将 c # 成员添加 () 到组件的字段、属性和方法：</span><span class="sxs-lookup"><span data-stu-id="f0a82-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="f0a82-215">对于 Razor 组件， `@code` 是的别名， [`@functions`](#functions) 建议使用 `@functions` 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="f0a82-216">允许多个 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="f0a82-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="f0a82-217">`@functions` 指令允许将 C# 成员（字段、属性和方法）添加到生成的类中：</span><span class="sxs-lookup"><span data-stu-id="f0a82-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0a82-218">在[ Razor 组件](xref:blazor/components/index)中，使用 `@code` Over `@functions` 来添加 c # 成员。</span><span class="sxs-lookup"><span data-stu-id="f0a82-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="f0a82-219">例如：</span><span class="sxs-lookup"><span data-stu-id="f0a82-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="f0a82-220">该代码生成以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="f0a82-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="f0a82-221">下面的代码是生成的 Razor c # 类：</span><span class="sxs-lookup"><span data-stu-id="f0a82-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0a82-222">`@functions` 方法有标记时，会用作模板化方法：</span><span class="sxs-lookup"><span data-stu-id="f0a82-222">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="f0a82-223">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="f0a82-224">`@implements` 指令为生成的类实现接口。</span><span class="sxs-lookup"><span data-stu-id="f0a82-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="f0a82-225">以下示例实现 <xref:System.IDisposable?displayProperty=fullName>，以便可以调用 <xref:System.IDisposable.Dispose*> 方法：</span><span class="sxs-lookup"><span data-stu-id="f0a82-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### `@inherits`

<span data-ttu-id="f0a82-226">`@inherits` 指令对视图继承的类提供完全控制：</span><span class="sxs-lookup"><span data-stu-id="f0a82-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="f0a82-227">下面的代码是一个自定义的 Razor 页类型：</span><span class="sxs-lookup"><span data-stu-id="f0a82-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="f0a82-228">`CustomText` 显示在视图中：</span><span class="sxs-lookup"><span data-stu-id="f0a82-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="f0a82-229">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="f0a82-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="f0a82-230">`@model` 和 `@inherits` 可在同一视图中使用。</span><span class="sxs-lookup"><span data-stu-id="f0a82-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="f0a82-231">`@inherits` 可位于视图导入的 *_ViewImports.cshtml* 文件中：</span><span class="sxs-lookup"><span data-stu-id="f0a82-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="f0a82-232">下面的代码是一种强类型视图：</span><span class="sxs-lookup"><span data-stu-id="f0a82-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="f0a82-233">如果在模型中传递“rick@contoso.com”，视图将生成以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="f0a82-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="f0a82-234">`@inject`指令使 Razor 页面可以将服务从[服务容器](xref:fundamentals/dependency-injection)注入到视图。</span><span class="sxs-lookup"><span data-stu-id="f0a82-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="f0a82-235">有关详细信息，请参阅[视图中的依赖关系注入](xref:mvc/views/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f0a82-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="f0a82-236">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-237">`@layout`指令指定 Razor 具有指令的可路由组件的布局 [`@page`](#page) 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-237">The `@layout` directive specifies a layout for routable Razor components that have an [`@page`](#page) directive.</span></span> <span data-ttu-id="f0a82-238">布局组件用于避免代码重复和不一致。</span><span class="sxs-lookup"><span data-stu-id="f0a82-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="f0a82-239">有关详细信息，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="f0a82-240">*此方案仅适用于 Razor () 的 MVC 视图和页面。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f0a82-241">`@model` 指令指定传递到视图或页面的模型类型：</span><span class="sxs-lookup"><span data-stu-id="f0a82-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="f0a82-242">在 Razor 使用单独的用户帐户创建的 ASP.NET CORE MVC 或页面应用中， *Views/Account/Login。 cshtml* 包含以下模型声明：</span><span class="sxs-lookup"><span data-stu-id="f0a82-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="f0a82-243">生成的类继承自 `RazorPage<dynamic>`：</span><span class="sxs-lookup"><span data-stu-id="f0a82-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="f0a82-244">Razor 公开 `Model` 用于访问传递到视图的模型的属性：</span><span class="sxs-lookup"><span data-stu-id="f0a82-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="f0a82-245">`@model` 指令指定 `Model` 属性的类型。</span><span class="sxs-lookup"><span data-stu-id="f0a82-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="f0a82-246">该指令将 `RazorPage<T>` 中的 `T` 指定为生成的类，视图便派生自该类。</span><span class="sxs-lookup"><span data-stu-id="f0a82-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="f0a82-247">如果未指定 `@model` 指令，则 `Model` 属性的类型为 `dynamic`。</span><span class="sxs-lookup"><span data-stu-id="f0a82-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="f0a82-248">有关详细信息，请参阅[强类型模型和 @model 关键字](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)。</span><span class="sxs-lookup"><span data-stu-id="f0a82-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="f0a82-249">`@namespace` 指令：</span><span class="sxs-lookup"><span data-stu-id="f0a82-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="f0a82-250">设置生成的 Razor 页、MVC 视图或组件的类的命名空间 Razor 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="f0a82-251">在目录树中最近的导入文件中设置页面、视图或组件类的根派生命名空间， *_ViewImports*) 或 *_Imports razor* (组件)  (视图或页面。 Razor</span><span class="sxs-lookup"><span data-stu-id="f0a82-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="f0a82-252">对于 Razor 下表中所示的页面示例：</span><span class="sxs-lookup"><span data-stu-id="f0a82-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="f0a82-253">每个页面都导入 Pages/_ViewImports.cshtml。</span><span class="sxs-lookup"><span data-stu-id="f0a82-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="f0a82-254">Pages/_ViewImports.cshtml 包含 `@namespace Hello.World`。</span><span class="sxs-lookup"><span data-stu-id="f0a82-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="f0a82-255">每个页面都有 `Hello.World`，作为其命名空间的根。</span><span class="sxs-lookup"><span data-stu-id="f0a82-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="f0a82-256">页</span><span class="sxs-lookup"><span data-stu-id="f0a82-256">Page</span></span>                                        | <span data-ttu-id="f0a82-257">命名空间</span><span class="sxs-lookup"><span data-stu-id="f0a82-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="f0a82-258">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f0a82-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="f0a82-259">Pages/MorePages/Page.cshtml</span><span class="sxs-lookup"><span data-stu-id="f0a82-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="f0a82-260">Pages/MorePages/EvenMorePages/Page.cshtml</span><span class="sxs-lookup"><span data-stu-id="f0a82-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="f0a82-261">上述关系适用于导入与 MVC 视图和组件一起使用的文件 Razor 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="f0a82-262">当多个导入文件具有 `@namespace` 指令时，最靠近目录树中的页面、视图或组件的文件将用于设置根命名空间。</span><span class="sxs-lookup"><span data-stu-id="f0a82-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="f0a82-263">如果前面示例中的 EvenMorePages 文件夹具有包含 `@namespace Another.Planet` 的导入文件（或 Pages/MorePages/EvenMorePages/Page.cshtml 文件包含 `@namespace Another.Planet`），则结果如下表所示。</span><span class="sxs-lookup"><span data-stu-id="f0a82-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="f0a82-264">页</span><span class="sxs-lookup"><span data-stu-id="f0a82-264">Page</span></span>                                        | <span data-ttu-id="f0a82-265">命名空间</span><span class="sxs-lookup"><span data-stu-id="f0a82-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="f0a82-266">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f0a82-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="f0a82-267">Pages/MorePages/Page.cshtml</span><span class="sxs-lookup"><span data-stu-id="f0a82-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="f0a82-268">Pages/MorePages/EvenMorePages/Page.cshtml</span><span class="sxs-lookup"><span data-stu-id="f0a82-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0a82-269">`@page` 指令具有不同的效果，具体取决于其所在文件的类型。</span><span class="sxs-lookup"><span data-stu-id="f0a82-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="f0a82-270">指令：</span><span class="sxs-lookup"><span data-stu-id="f0a82-270">The directive:</span></span>

* <span data-ttu-id="f0a82-271">在 *cshtml* 文件中，指示该文件是一个 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="f0a82-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="f0a82-272">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)和 <xref:razor-pages/index>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="f0a82-273">指定 Razor 组件应直接处理请求。</span><span class="sxs-lookup"><span data-stu-id="f0a82-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="f0a82-274">有关详细信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f0a82-275">`@page` *Cshtml* 文件第一行的指令指示该文件是一个 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="f0a82-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="f0a82-276">有关详细信息，请参阅 <xref:razor-pages/index>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

<span data-ttu-id="f0a82-277">*此方案仅适用于 Razor (`.razor`) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-277">*This scenario only applies to Razor components (`.razor`).*</span></span>

<span data-ttu-id="f0a82-278">如果设置为 `false` (默认) ，则将在 Razor 以下情况下删除从组件 () 中呈现的标记中的空白 `.razor` ：</span><span class="sxs-lookup"><span data-stu-id="f0a82-278">When set to `false` (default), whitespace in the rendered markup from Razor components (`.razor`) is removed if:</span></span>

* <span data-ttu-id="f0a82-279">元素中的前导或尾随空白。</span><span class="sxs-lookup"><span data-stu-id="f0a82-279">Leading or trailing within an element.</span></span>
* <span data-ttu-id="f0a82-280">`RenderFragment` 参数中的前导或尾随空白。</span><span class="sxs-lookup"><span data-stu-id="f0a82-280">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="f0a82-281">例如，传递到另一个组件的子内容。</span><span class="sxs-lookup"><span data-stu-id="f0a82-281">For example, child content passed to another component.</span></span>
* <span data-ttu-id="f0a82-282">在 C# 代码块（例如 `@if` 和 `@foreach`）之前或之后。</span><span class="sxs-lookup"><span data-stu-id="f0a82-282">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="f0a82-283">*此方案仅适用于 Razor () 的 MVC 视图和页面。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-283">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f0a82-284">`@section`指令与[MVC 和 Razor 页面布局](xref:mvc/views/layout)结合使用，以使视图或页面能够在 HTML 页面的不同部分中呈现内容。</span><span class="sxs-lookup"><span data-stu-id="f0a82-284">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="f0a82-285">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-285">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="f0a82-286">`@using` 指令用于向生成的视图添加 C# `using` 指令：</span><span class="sxs-lookup"><span data-stu-id="f0a82-286">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0a82-287">在 " [ Razor 组件](xref:blazor/components/index)" 中， `@using` 还控制哪些组件在范围内。</span><span class="sxs-lookup"><span data-stu-id="f0a82-287">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="f0a82-288">指令属性</span><span class="sxs-lookup"><span data-stu-id="f0a82-288">Directive attributes</span></span>

<span data-ttu-id="f0a82-289">Razor 指令特性由带有符号后的保留关键字的隐式表达式表示 `@` 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-289">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="f0a82-290">指令特性通常会改变元素的分析方式，或实现不同的功能。</span><span class="sxs-lookup"><span data-stu-id="f0a82-290">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="f0a82-291">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-292">`@attributes` 允许组件呈现未声明的属性。</span><span class="sxs-lookup"><span data-stu-id="f0a82-292">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="f0a82-293">有关详细信息，请参阅 <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-293">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="f0a82-294">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-295">组件中的数据绑定通过 `@bind` 属性实现。</span><span class="sxs-lookup"><span data-stu-id="f0a82-295">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="f0a82-296">有关详细信息，请参阅 <xref:blazor/components/data-binding>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-296">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="f0a82-297">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-298">Razor 为组件提供事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="f0a82-298">Razor provides event handling features for components.</span></span> <span data-ttu-id="f0a82-299">有关详细信息，请参阅 <xref:blazor/components/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-299">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="f0a82-300">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-301">禁止事件的默认操作。</span><span class="sxs-lookup"><span data-stu-id="f0a82-301">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="f0a82-302">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-302">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-303">停止事件的事件传播。</span><span class="sxs-lookup"><span data-stu-id="f0a82-303">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="f0a82-304">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-305">`@key` 指令属性使组件比较算法保证基于键的值保留元素或组件。</span><span class="sxs-lookup"><span data-stu-id="f0a82-305">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="f0a82-306">有关详细信息，请参阅 <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-306">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="f0a82-307">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-308">组件引用 (`@ref`) 提供了一种引用组件实例的方法，以便可以向该实例发出命令。</span><span class="sxs-lookup"><span data-stu-id="f0a82-308">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="f0a82-309">有关详细信息，请参阅 <xref:blazor/components/index#capture-references-to-components>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-309">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="f0a82-310">*此方案仅适用于 Razor ( razor) 的组件。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-310">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="f0a82-311">`@typeparam` 指令声明生成的组件类的泛型类型参数。</span><span class="sxs-lookup"><span data-stu-id="f0a82-311">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="f0a82-312">有关详细信息，请参阅 <xref:blazor/components/templated-components>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-312">For more information, see <xref:blazor/components/templated-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="f0a82-313">模板化 Razor 委托</span><span class="sxs-lookup"><span data-stu-id="f0a82-313">Templated Razor delegates</span></span>

<span data-ttu-id="f0a82-314">Razor 模板允许使用以下格式定义 UI 代码段：</span><span class="sxs-lookup"><span data-stu-id="f0a82-314">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="f0a82-315">下面的示例演示如何将模板化 Razor 委托指定为 <xref:System.Func%602> 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-315">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="f0a82-316">为委托封装的方法的参数指定[动态类型](/dotnet/csharp/programming-guide/types/using-type-dynamic)。</span><span class="sxs-lookup"><span data-stu-id="f0a82-316">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="f0a82-317">将[对象类型](/dotnet/csharp/language-reference/keywords/object)指定为委托的返回值。</span><span class="sxs-lookup"><span data-stu-id="f0a82-317">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="f0a82-318">该模板与 `Pet`（具有 `Name` 属性）的 <xref:System.Collections.Generic.List%601> 一起使用。</span><span class="sxs-lookup"><span data-stu-id="f0a82-318">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="f0a82-319">使用 `foreach` 语句提供的 `pets` 呈现该模板：</span><span class="sxs-lookup"><span data-stu-id="f0a82-319">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="f0a82-320">呈现的输出：</span><span class="sxs-lookup"><span data-stu-id="f0a82-320">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="f0a82-321">你还可以将内联 Razor 模板作为参数提供给方法。</span><span class="sxs-lookup"><span data-stu-id="f0a82-321">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="f0a82-322">在下面的示例中， `Repeat` 方法接收 Razor 模板。</span><span class="sxs-lookup"><span data-stu-id="f0a82-322">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="f0a82-323">该方法使用模板生成 HTML 内容，其中包含列表中提供的重复项：</span><span class="sxs-lookup"><span data-stu-id="f0a82-323">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="f0a82-324">使用前面示例中的 pets 列表，调用 `Repeat` 方法以及：</span><span class="sxs-lookup"><span data-stu-id="f0a82-324">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="f0a82-325">`Pet` 的 <xref:System.Collections.Generic.List%601>。</span><span class="sxs-lookup"><span data-stu-id="f0a82-325"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="f0a82-326">每只宠物的重复次数。</span><span class="sxs-lookup"><span data-stu-id="f0a82-326">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="f0a82-327">用于无序列表的列表项的内联模板。</span><span class="sxs-lookup"><span data-stu-id="f0a82-327">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="f0a82-328">呈现的输出：</span><span class="sxs-lookup"><span data-stu-id="f0a82-328">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="f0a82-329">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="f0a82-329">Tag Helpers</span></span>

<span data-ttu-id="f0a82-330">*此方案仅适用于 Razor () 的 MVC 视图和页面。*</span><span class="sxs-lookup"><span data-stu-id="f0a82-330">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="f0a82-331">[标记帮助程序](xref:mvc/views/tag-helpers/intro)有三个相关指令。</span><span class="sxs-lookup"><span data-stu-id="f0a82-331">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="f0a82-332">指令</span><span class="sxs-lookup"><span data-stu-id="f0a82-332">Directive</span></span> | <span data-ttu-id="f0a82-333">函数</span><span class="sxs-lookup"><span data-stu-id="f0a82-333">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="f0a82-334">向视图提供标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="f0a82-334">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="f0a82-335">从视图中删除以前添加的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="f0a82-335">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="f0a82-336">指定标记前缀，以启用标记帮助程序支持并阐明标记帮助程序的用法。</span><span class="sxs-lookup"><span data-stu-id="f0a82-336">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="f0a82-337">Razor 保留关键字</span><span class="sxs-lookup"><span data-stu-id="f0a82-337">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="f0a82-338">Razor 字</span><span class="sxs-lookup"><span data-stu-id="f0a82-338">Razor keywords</span></span>

* <span data-ttu-id="f0a82-339">`page` (要求 ASP.NET Core 2.1 或更高版本) </span><span class="sxs-lookup"><span data-stu-id="f0a82-339">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="f0a82-340">`helper` ASP.NET Core 当前不支持 () </span><span class="sxs-lookup"><span data-stu-id="f0a82-340">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="f0a82-341">Razor 关键字通过 `@(Razor Keyword)` (进行转义，例如 `@(functions)`) 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-341">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="f0a82-342">C # Razor 关键字</span><span class="sxs-lookup"><span data-stu-id="f0a82-342">C# Razor keywords</span></span>

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

<span data-ttu-id="f0a82-343">C # Razor 关键字必须用 `@(@C# Razor Keyword)` (进行双转义，例如 `@(@case)`) 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-343">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="f0a82-344">第一个 `@` 转义 Razor 分析器。</span><span class="sxs-lookup"><span data-stu-id="f0a82-344">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="f0a82-345">第二个 `@` 对 C# 分析器转义。</span><span class="sxs-lookup"><span data-stu-id="f0a82-345">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="f0a82-346">不使用的保留关键字 Razor</span><span class="sxs-lookup"><span data-stu-id="f0a82-346">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="f0a82-347">检查 Razor 为视图生成的 c # 类</span><span class="sxs-lookup"><span data-stu-id="f0a82-347">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f0a82-348">对于 .NET Core SDK 2.1 或更高版本， [ Razor SDK](xref:razor-pages/sdk)会处理 Razor 文件的编译。</span><span class="sxs-lookup"><span data-stu-id="f0a82-348">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="f0a82-349">生成项目时，SDK 会 Razor 在项目根目录中生成一个 *<build_configuration>/<target_framework_moniker>Razor /* 目录。</span><span class="sxs-lookup"><span data-stu-id="f0a82-349">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="f0a82-350">目录中的目录结构 *Razor* 反映了项目的目录结构。</span><span class="sxs-lookup"><span data-stu-id="f0a82-350">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="f0a82-351">请考虑面向 .NET Core 2.1 的 ASP.NET Core 2.1 页项目中的以下目录结构 Razor ：</span><span class="sxs-lookup"><span data-stu-id="f0a82-351">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

<span data-ttu-id="f0a82-352">在 Debug 配置下生成项目将生成以下 obj 目录：</span><span class="sxs-lookup"><span data-stu-id="f0a82-352">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

<span data-ttu-id="f0a82-353">若要查看 *页面/索引* 生成的类，请打开 *obj/Debug/netcoreapp 2.1/ Razor /Pages/Index.g.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="f0a82-353">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="f0a82-354">将下面的类添加到 ASP.NET Core MVC 项目：</span><span class="sxs-lookup"><span data-stu-id="f0a82-354">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="f0a82-355">在 `Startup.ConfigureServices` 中，使用 `CustomTemplateEngine` 类替代 MVC 添加的 `RazorTemplateEngine`：</span><span class="sxs-lookup"><span data-stu-id="f0a82-355">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="f0a82-356">在 `CustomTemplateEngine` 的 `return csharpDocument;` 语句上设置断点。</span><span class="sxs-lookup"><span data-stu-id="f0a82-356">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="f0a82-357">当程序执行在断点处停止时，查看 `generatedCode` 的值。</span><span class="sxs-lookup"><span data-stu-id="f0a82-357">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode 的文本可视化工具视图](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="f0a82-359">视图查找和区分大小写</span><span class="sxs-lookup"><span data-stu-id="f0a82-359">View lookups and case sensitivity</span></span>

<span data-ttu-id="f0a82-360">Razor视图引擎为视图执行区分大小写的查找。</span><span class="sxs-lookup"><span data-stu-id="f0a82-360">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="f0a82-361">但是，实际查找取决于基础文件系统：</span><span class="sxs-lookup"><span data-stu-id="f0a82-361">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="f0a82-362">基于文件的源：</span><span class="sxs-lookup"><span data-stu-id="f0a82-362">File based source:</span></span>
  * <span data-ttu-id="f0a82-363">在使用不区分大小写的文件系统的操作系统（例如，Windows）上，物理文件提供程序查找不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f0a82-363">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="f0a82-364">例如，`return View("Test")` 可匹配 */Views/Home/Test.cshtml*、*/Views/home/test.cshtml* 以及任何其他大小写变体。</span><span class="sxs-lookup"><span data-stu-id="f0a82-364">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="f0a82-365">在区分大小写的文件系统（例如，Linux、OSX 以及使用 `EmbeddedFileProvider` 构建的文件系统）上，查找区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f0a82-365">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="f0a82-366">例如，`return View("Test")` 专门匹配 */Views/Home/Test.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="f0a82-366">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="f0a82-367">预编译视图：在 ASP.NET Core 2.0 及更高版本中，预编译视图查找在所有操作系统上均不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="f0a82-367">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="f0a82-368">该行为与 Windows 上物理文件提供程序的行为相同。</span><span class="sxs-lookup"><span data-stu-id="f0a82-368">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="f0a82-369">如果两个预编译视图仅大小写不同，则查找的结果具有不确定性。</span><span class="sxs-lookup"><span data-stu-id="f0a82-369">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="f0a82-370">建议开发人员将文件和目录名称的大小写与以下项的大小写匹配：</span><span class="sxs-lookup"><span data-stu-id="f0a82-370">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="f0a82-371">区域、控制器和操作名称。</span><span class="sxs-lookup"><span data-stu-id="f0a82-371">Area, controller, and action names.</span></span>
* <span data-ttu-id="f0a82-372">Razor 页.</span><span class="sxs-lookup"><span data-stu-id="f0a82-372">Razor Pages.</span></span>

<span data-ttu-id="f0a82-373">匹配大小写可确保无论使用哪种基础文件系统，部署都能找到其视图。</span><span class="sxs-lookup"><span data-stu-id="f0a82-373">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f0a82-374">其他资源</span><span class="sxs-lookup"><span data-stu-id="f0a82-374">Additional resources</span></span>

<span data-ttu-id="f0a82-375">[使用 Razor ASP.NET Web 编程简介语法](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) 提供了许多用语法编程的示例 Razor 。</span><span class="sxs-lookup"><span data-stu-id="f0a82-375">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
