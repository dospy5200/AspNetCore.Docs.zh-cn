---
title: 在 ASP.NET Core 中创作标记帮助程序
author: rick-anderson
description: 了解如何在 ASP.NET Core 中创作标记帮助程序。
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: e0c9f450e4eded49694cbbb0e9fa2614a221ab14
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586821"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="23f53-103">在 ASP.NET Core 中创作标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="23f53-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="23f53-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23f53-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="23f53-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="23f53-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="23f53-106">标记帮助程序入门</span><span class="sxs-lookup"><span data-stu-id="23f53-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="23f53-107">本教程介绍标记帮助程序编程。</span><span class="sxs-lookup"><span data-stu-id="23f53-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="23f53-108">[标记帮助程序简介](intro.md)描述了标记帮助程序提供的优势。</span><span class="sxs-lookup"><span data-stu-id="23f53-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="23f53-109">标记帮助程序是实现 `ITagHelper` 接口的任何类。</span><span class="sxs-lookup"><span data-stu-id="23f53-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="23f53-110">但是，在创作标记帮助程序时，通常从 `TagHelper` 派生，这样可以访问 `Process` 方法。</span><span class="sxs-lookup"><span data-stu-id="23f53-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="23f53-111">创建一个名为 AuthoringTagHelpers 的新 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="23f53-111">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="23f53-112">此项目不需要身份验证。</span><span class="sxs-lookup"><span data-stu-id="23f53-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="23f53-113">创建一个名为“TagHelpers”的文件夹来保存标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="23f53-113">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="23f53-114">“TagHelpers”文件夹不是必需的，但它是合理的约定。</span><span class="sxs-lookup"><span data-stu-id="23f53-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="23f53-115">现在让我们开始编写一些简单的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="23f53-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="23f53-116">最小的标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="23f53-116">A minimal Tag Helper</span></span>

<span data-ttu-id="23f53-117">在本部分中，你将编写一个更新电子邮件标记的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="23f53-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="23f53-118">例如：</span><span class="sxs-lookup"><span data-stu-id="23f53-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="23f53-119">服务器将使用电子邮件标记帮助程序将该标记转换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="23f53-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="23f53-120">即，使其成为电子邮件链接的定位标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="23f53-121">如果你正在编写博客引擎，并且需要它将营销、支持和其他联系人的电子邮件全部发送到同一个域，则可能需要执行此操作。</span><span class="sxs-lookup"><span data-stu-id="23f53-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="23f53-122">将以下 `EmailTagHelper` 类添加到“TagHelpers”文件夹。</span><span class="sxs-lookup"><span data-stu-id="23f53-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="23f53-123">标记帮助程序使用面向根类名称的元素的命名约定（减去类名称的 TagHelper 部分）。</span><span class="sxs-lookup"><span data-stu-id="23f53-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="23f53-124">在此示例中， **EmailTagHelper** 的根名称为 *email*，因此 `<email>` 标记将针对。</span><span class="sxs-lookup"><span data-stu-id="23f53-124">In this example, the root name of **EmailTagHelper** is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="23f53-125">此命名约定应适用于大多数标记帮助程序，稍后将介绍如何重写它。</span><span class="sxs-lookup"><span data-stu-id="23f53-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="23f53-126">`EmailTagHelper` 类从 `TagHelper` 派生。</span><span class="sxs-lookup"><span data-stu-id="23f53-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="23f53-127">`TagHelper` 类提供编写标记帮助程序的方法和属性。</span><span class="sxs-lookup"><span data-stu-id="23f53-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="23f53-128">重写的 `Process` 方法控制标记帮助程序在执行时的操作。</span><span class="sxs-lookup"><span data-stu-id="23f53-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="23f53-129">`TagHelper` 类还提供具有相同参数的异步版本 (`ProcessAsync`)。</span><span class="sxs-lookup"><span data-stu-id="23f53-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="23f53-130">`Process`（和 `ProcessAsync`）的上下文参数包含与执行当前 HTML 标记相关的信息。</span><span class="sxs-lookup"><span data-stu-id="23f53-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="23f53-131">`Process`（和 `ProcessAsync`）的输出参数包含监控状态的 HTML 元素，它代表用于生成 HTML 标记和内容的原始源。</span><span class="sxs-lookup"><span data-stu-id="23f53-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="23f53-132">类名称的后缀是 TagHelper，这不是必需的，但被认为是最佳做法约定。</span><span class="sxs-lookup"><span data-stu-id="23f53-132">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="23f53-133">可将类声明为：</span><span class="sxs-lookup"><span data-stu-id="23f53-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="23f53-134">若要使 `EmailTagHelper` 类可用于所有 Razor 视图，请将 `addTagHelper` 指令添加到 *views/_ViewImports cshtml* 文件中：</span><span class="sxs-lookup"><span data-stu-id="23f53-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="23f53-135">上面的代码使用通配符语法来指定程序集中的所有标记帮助程序都将可用。</span><span class="sxs-lookup"><span data-stu-id="23f53-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="23f53-136">`@addTagHelper` 之后的第一个字符串指定要加载的标记帮助程序（对所有标记帮助程序使用“\*”），第二个字符串“AuthoringTagHelpers”指定标记帮助程序所在的程序集。</span><span class="sxs-lookup"><span data-stu-id="23f53-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="23f53-137">另请注意，第二行将使用通配符语法的 ASP.NET Core MVC 标记帮助程序 (在 [标记帮助程序简介](intro.md)中讨论了这些帮助程序。 ) 这是 `@addTagHelper` 使标记帮助程序可用于该视图的指令 Razor 。</span><span class="sxs-lookup"><span data-stu-id="23f53-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="23f53-138">或者，也可以提供标记帮助程序的完全限定的名称 (FQN)，如下所示：</span><span class="sxs-lookup"><span data-stu-id="23f53-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="23f53-139">若要使用 FQN 将标记帮助程序添加到视图中，请依次添加 FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) 和程序集名称（AuthoringTagHelpers，不一定是 `namespace`）。</span><span class="sxs-lookup"><span data-stu-id="23f53-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** (*AuthoringTagHelpers*, not necessarily the `namespace`).</span></span> <span data-ttu-id="23f53-140">大多数开发者更喜欢使用通配符语法。</span><span class="sxs-lookup"><span data-stu-id="23f53-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="23f53-141">[标记帮助程序简介](intro.md)详细介绍了标记帮助程序的添加和删除方法，以及层次结构和通配符语法。</span><span class="sxs-lookup"><span data-stu-id="23f53-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="23f53-142">使用以下更改更新 Views/Home/Contact.cshtml 文件中的标记：</span><span class="sxs-lookup"><span data-stu-id="23f53-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="23f53-143">运行应用并使用你喜爱的浏览器来查看 HTML 源，以便验证电子邮件标记是否替换为定位标记（例如，`<a>Support</a>`）。</span><span class="sxs-lookup"><span data-stu-id="23f53-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="23f53-144">Support 和 Marketing 呈现为链接，但它们不具备使其正常工作的 `href` 属性。</span><span class="sxs-lookup"><span data-stu-id="23f53-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="23f53-145">此问题将在下一部分得以解决。</span><span class="sxs-lookup"><span data-stu-id="23f53-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="23f53-146">SetAttribute 和 SetContent</span><span class="sxs-lookup"><span data-stu-id="23f53-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="23f53-147">在本部分中，我们将更新 `EmailTagHelper`，使其能够为电子邮件创建有效的定位标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="23f53-148">我们将对其进行更新，以 Razor 使用属性) 形式从视图中获取信息 `mail-to` ，并在生成定位点时使用该 (。</span><span class="sxs-lookup"><span data-stu-id="23f53-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="23f53-149">使用以下内容更新 `EmailTagHelper` 类：</span><span class="sxs-lookup"><span data-stu-id="23f53-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="23f53-150">标记帮助程序采用 Pascal 大小写格式的类和属性名将转换为各自相应的[短横线格式](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)。</span><span class="sxs-lookup"><span data-stu-id="23f53-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="23f53-151">因此，要使用 `MailTo` 属性，请使用 `<email mail-to="value"/>` 等效项。</span><span class="sxs-lookup"><span data-stu-id="23f53-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="23f53-152">最后一行为最小功能标记帮助程序设置已完成的内容。</span><span class="sxs-lookup"><span data-stu-id="23f53-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="23f53-153">突出显示的行显示了添加属性的语法：</span><span class="sxs-lookup"><span data-stu-id="23f53-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="23f53-154">只要属性集合中当前不存在“href”属性，该方法就适用于此属性。</span><span class="sxs-lookup"><span data-stu-id="23f53-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="23f53-155">也可使用 `output.Attributes.Add` 方法将标记帮助程序属性添加到标记属性集合的末尾。</span><span class="sxs-lookup"><span data-stu-id="23f53-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="23f53-156">使用以下更改更新 Views/Home/Contact.cshtml 文件中的标记：</span><span class="sxs-lookup"><span data-stu-id="23f53-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="23f53-157">运行应用并验证它是否生成正确的链接。</span><span class="sxs-lookup"><span data-stu-id="23f53-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="23f53-158">如果打算编写电子邮件标记自结束 (`<email mail-to="Rick" />`)，最终输出也将为自结束。</span><span class="sxs-lookup"><span data-stu-id="23f53-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="23f53-159">要启用只使用开始标记 (`<email mail-to="Rick">`) 来编写标记的功能，必须用以下内容来标记类：</span><span class="sxs-lookup"><span data-stu-id="23f53-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must mark the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="23f53-160">鉴于自结束电子邮件标记帮助程序，输出将为 `<a href="mailto:Rick@contoso.com" />`。</span><span class="sxs-lookup"><span data-stu-id="23f53-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="23f53-161">自结束定位标记不是有效的 HTML，因此你不想创建这样的标记，但你可能想要创建一个自结束的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="23f53-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="23f53-162">标记帮助程序在读取标记后设置 `TagMode` 属性的类型。</span><span class="sxs-lookup"><span data-stu-id="23f53-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="23f53-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="23f53-163">ProcessAsync</span></span>

<span data-ttu-id="23f53-164">在本部分中，我们将编写异步电子邮件帮助程序。</span><span class="sxs-lookup"><span data-stu-id="23f53-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="23f53-165">将 `EmailTagHelper` 类替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="23f53-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="23f53-166">**注意：**</span><span class="sxs-lookup"><span data-stu-id="23f53-166">**Notes:**</span></span>

   * <span data-ttu-id="23f53-167">此版本使用异步 `ProcessAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="23f53-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="23f53-168">异步 `GetChildContentAsync` 返回包含 `TagHelperContent` 的 `Task`。</span><span class="sxs-lookup"><span data-stu-id="23f53-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="23f53-169">使用 `output` 参数获取 HTML 元素的内容。</span><span class="sxs-lookup"><span data-stu-id="23f53-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="23f53-170">对 Views/Home/Contact.cshtml 文件进行以下更改，以便标记帮助程序能够获取目标电子邮件。</span><span class="sxs-lookup"><span data-stu-id="23f53-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="23f53-171">运行应用并验证它是否生成有效的电子邮件链接。</span><span class="sxs-lookup"><span data-stu-id="23f53-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="23f53-172">RemoveAll、PreContent.SetHtmlContent 和 PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="23f53-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="23f53-173">将以下 `BoldTagHelper` 类添加到“TagHelpers”文件夹。</span><span class="sxs-lookup"><span data-stu-id="23f53-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="23f53-174">`[HtmlTargetElement]` 属性传递一个属性参数，该参数指定包含名为“bold”的 HTML 属性的任何 HTML 元素都将匹配，并且该类中的 `Process` 重写方法将会运行。</span><span class="sxs-lookup"><span data-stu-id="23f53-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="23f53-175">在此示例中，`Process` 方法删除了“bold”属性，并用 `<strong></strong>` 围住了包含的标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="23f53-176">因为你不想替换现有的标记内容，所以必须用 `PreContent.SetHtmlContent` 方法编写开头的 `<strong>` 标记，并用 `PostContent.SetHtmlContent` 方法编写结尾的 `</strong>` 标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="23f53-177">修改 About.cshtml 视图，以包含 `bold` 属性值。</span><span class="sxs-lookup"><span data-stu-id="23f53-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="23f53-178">完成的代码如下所示。</span><span class="sxs-lookup"><span data-stu-id="23f53-178">The completed code is shown below.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="23f53-179">运行应用。</span><span class="sxs-lookup"><span data-stu-id="23f53-179">Run the app.</span></span> <span data-ttu-id="23f53-180">可以使用你喜爱的浏览器来检查源并验证标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="23f53-181">上面的 `[HtmlTargetElement]` 属性仅针对提供属性名称“bold”的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="23f53-182">标记帮助程序未修改 `<bold>` 元素。</span><span class="sxs-lookup"><span data-stu-id="23f53-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="23f53-183">标注出 `[HtmlTargetElement]` 属性行，它将默认为目标 `<bold>` 标记，也就是 `<bold>` 格式的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="23f53-184">请记住，默认的命名约定会将类名称 BoldTagHelper 与 `<bold>` 标记相匹配。</span><span class="sxs-lookup"><span data-stu-id="23f53-184">Remember, the default naming convention will match the class name **Bold** TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="23f53-185">运行应用并验证 `<bold>` 标记是否由标记帮助程序进行处理。</span><span class="sxs-lookup"><span data-stu-id="23f53-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="23f53-186">用多个 `[HtmlTargetElement]` 属性修饰类会导致目标出现逻辑 OR。</span><span class="sxs-lookup"><span data-stu-id="23f53-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="23f53-187">例如，使用下面的代码时，系统将匹配出 bold 标记或 bold 属性。</span><span class="sxs-lookup"><span data-stu-id="23f53-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="23f53-188">将多个属性添加到同一语句时，运行时会将其视为逻辑 AND。</span><span class="sxs-lookup"><span data-stu-id="23f53-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="23f53-189">例如，在下面的代码中，HTML 元素必须命名为“bold”并具有名为“bold”的属性 (`<bold bold />`) 才能匹配。</span><span class="sxs-lookup"><span data-stu-id="23f53-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="23f53-190">也可使用 `[HtmlTargetElement]` 更改目标元素的名称。</span><span class="sxs-lookup"><span data-stu-id="23f53-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="23f53-191">例如，如果你希望 `BoldTagHelper` 以 `<MyBold>` 标记为目标，则可使用以下属性：</span><span class="sxs-lookup"><span data-stu-id="23f53-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="23f53-192">将模型传递到标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="23f53-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="23f53-193">添加“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="23f53-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="23f53-194">将以下 `WebsiteContext` 类添加到“模型”文件夹：</span><span class="sxs-lookup"><span data-stu-id="23f53-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="23f53-195">将以下 `WebsiteInformationTagHelper` 类添加到“TagHelpers”文件夹。</span><span class="sxs-lookup"><span data-stu-id="23f53-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="23f53-196">如前所述，标记帮助程序会将标记帮助程序采用 Pascal 大小写格式的 C# 类名和属性转换为[短横线格式](https://wiki.c2.com/?KebabCase)。</span><span class="sxs-lookup"><span data-stu-id="23f53-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="23f53-197">因此，若要 `WebsiteInformationTagHelper` 在中使用 Razor ，你将编写 `<website-information />` 。</span><span class="sxs-lookup"><span data-stu-id="23f53-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="23f53-198">未显式标识具有 `[HtmlTargetElement]` 属性的目标元素，因此 `website-information` 的默认值将成为目标元素。</span><span class="sxs-lookup"><span data-stu-id="23f53-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="23f53-199">如果应用了以下属性（请注意，它虽不是短横线格式，但却与类名相匹配）：</span><span class="sxs-lookup"><span data-stu-id="23f53-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="23f53-200">短横线格式标记 `<website-information />` 不匹配。</span><span class="sxs-lookup"><span data-stu-id="23f53-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="23f53-201">若要使用 `[HtmlTargetElement]` 属性，请使用短横线格式，如下所示：</span><span class="sxs-lookup"><span data-stu-id="23f53-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="23f53-202">自结束的元素没有任何内容。</span><span class="sxs-lookup"><span data-stu-id="23f53-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="23f53-203">在此示例中， Razor 标记将使用一个自结束标记，但标记帮助程序将创建一个 [节](https://www.w3.org/TR/html5/sections.html#the-section-element) 元素，该 (元素不会自行关闭，并且你在) 元素内编写内容 `section` 。</span><span class="sxs-lookup"><span data-stu-id="23f53-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="23f53-204">因此，需要将 `TagMode` 设置为 `StartTagAndEndTag` 以写入输出。</span><span class="sxs-lookup"><span data-stu-id="23f53-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="23f53-205">或者，可以标注出行设置 `TagMode` 并用结束标记编写标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="23f53-206">（本教程后面将提供示例标记。）</span><span class="sxs-lookup"><span data-stu-id="23f53-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="23f53-207">下一行中的 `$`（美元符号）使用[内插字符串](/dotnet/csharp/language-reference/keywords/interpolated-strings)：</span><span class="sxs-lookup"><span data-stu-id="23f53-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="23f53-208">将以下标记添加到 About.cshtml 视图。</span><span class="sxs-lookup"><span data-stu-id="23f53-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="23f53-209">突出显示的标记显示 Web 站点信息。</span><span class="sxs-lookup"><span data-stu-id="23f53-209">The highlighted markup displays the web site information.</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="23f53-210">在 Razor 下面显示的标记中：</span><span class="sxs-lookup"><span data-stu-id="23f53-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="23f53-211">Razor 知道 `info` 属性是一个类，而不是字符串，并且您想要编写 c # 代码。</span><span class="sxs-lookup"><span data-stu-id="23f53-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="23f53-212">编写任何非字符串标记帮助程序属性时，都不应使用 `@` 字符。</span><span class="sxs-lookup"><span data-stu-id="23f53-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="23f53-213">运行应用，并导航到“关于”视图查看 Web 站点信息。</span><span class="sxs-lookup"><span data-stu-id="23f53-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="23f53-214">可使用带有结束标记的以下标记，并在标记帮助程序中删除带有 `TagMode.StartTagAndEndTag` 的行：</span><span class="sxs-lookup"><span data-stu-id="23f53-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="23f53-215">条件标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="23f53-215">Condition Tag Helper</span></span>

<span data-ttu-id="23f53-216">条件标记帮助程序在传递 true 值时呈现输出。</span><span class="sxs-lookup"><span data-stu-id="23f53-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="23f53-217">将以下 `ConditionTagHelper` 类添加到“TagHelpers”文件夹。</span><span class="sxs-lookup"><span data-stu-id="23f53-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="23f53-218">将 Views/Home/Index.cshtml 文件的内容替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="23f53-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="23f53-219">将 `Home` 控制器中的 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="23f53-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="23f53-220">运行应用并浏览到主页。</span><span class="sxs-lookup"><span data-stu-id="23f53-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="23f53-221">条件 `div` 中的标记不会呈现。</span><span class="sxs-lookup"><span data-stu-id="23f53-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="23f53-222">将查询字符串 `?approved=true` 追加到 URL（例如，`http://localhost:1235/Home/Index?approved=true`）。</span><span class="sxs-lookup"><span data-stu-id="23f53-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="23f53-223">`approved` 设置为 true，并将显示条件标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="23f53-224">使用 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 运算符将属性指定为目标，而不是像使用 bold 标记帮助程序那样指定字符串：</span><span class="sxs-lookup"><span data-stu-id="23f53-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="23f53-225">如果代码被重构，[nameof](/dotnet/csharp/language-reference/keywords/nameof) 运算符将保护它（可能需要将名称更改为 `RedCondition`）。</span><span class="sxs-lookup"><span data-stu-id="23f53-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="23f53-226">避免标记帮助程序冲突</span><span class="sxs-lookup"><span data-stu-id="23f53-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="23f53-227">在本部分中，你将编写一对自动链接标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="23f53-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="23f53-228">第一个标记帮助程序会将包含以 HTTP 开头的 URL 的标记替换为包含相同 URL 的 HTML 定位标记（从而产生指向 URL 的链接）。</span><span class="sxs-lookup"><span data-stu-id="23f53-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="23f53-229">第二个标记帮助程序也会对以 WWW 开头的 URL 执行相同的操作。</span><span class="sxs-lookup"><span data-stu-id="23f53-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="23f53-230">由于这两个帮助程序密切相关，并且你将来可能会重构它们，因此可将其保存在同一文件中。</span><span class="sxs-lookup"><span data-stu-id="23f53-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="23f53-231">将以下 `AutoLinkerHttpTagHelper` 类添加到“TagHelpers”文件夹。</span><span class="sxs-lookup"><span data-stu-id="23f53-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="23f53-232">`AutoLinkerHttpTagHelper` 类以 `p` 元 素为目标，并使用[正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)来创建定位点。</span><span class="sxs-lookup"><span data-stu-id="23f53-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="23f53-233">将以下标记添加到 Views/Home/Contact.cshtml 文件的末尾：</span><span class="sxs-lookup"><span data-stu-id="23f53-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="23f53-234">运行应用并验证标记帮助程序是否正确呈现定位点。</span><span class="sxs-lookup"><span data-stu-id="23f53-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="23f53-235">更新 `AutoLinker` 类以包含 `AutoLinkerWwwTagHelper`，这会将 www 文本转换为还包含原始 www 文本的定位标记。</span><span class="sxs-lookup"><span data-stu-id="23f53-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="23f53-236">更新后的代码在下方突出显示：</span><span class="sxs-lookup"><span data-stu-id="23f53-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="23f53-237">运行应用。</span><span class="sxs-lookup"><span data-stu-id="23f53-237">Run the app.</span></span> <span data-ttu-id="23f53-238">请注意 www 文本呈现为链接，但 HTTP 文本不是。</span><span class="sxs-lookup"><span data-stu-id="23f53-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="23f53-239">如果将中断点放在这两个类中，可以看到 HTTP 标记帮助程序类首先运行。</span><span class="sxs-lookup"><span data-stu-id="23f53-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="23f53-240">问题是，标记帮助程序输出已缓存，当运行 WWW 标记帮助程序时，它会覆盖 HTTP 标记帮助程序的缓存输出。</span><span class="sxs-lookup"><span data-stu-id="23f53-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="23f53-241">本教程稍后将介绍如何控制标记帮助程序中的运行顺序。</span><span class="sxs-lookup"><span data-stu-id="23f53-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="23f53-242">我们将用以下方法修复代码：</span><span class="sxs-lookup"><span data-stu-id="23f53-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="23f53-243">在自动链接标记帮助程序的第一版中，使用以下代码获取了目标的内容：</span><span class="sxs-lookup"><span data-stu-id="23f53-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="23f53-244">也就是说，使用传递到 `ProcessAsync` 方法的 `TagHelperOutput` 调用 `GetChildContentAsync`。</span><span class="sxs-lookup"><span data-stu-id="23f53-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="23f53-245">如前所述，由于输出已缓存，因此将调用最后运行的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="23f53-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="23f53-246">使用以下代码解决了这个问题：</span><span class="sxs-lookup"><span data-stu-id="23f53-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="23f53-247">上面的代码检查内容是否已修改，如果已修改，则从输出缓冲区获取内容。</span><span class="sxs-lookup"><span data-stu-id="23f53-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="23f53-248">运行应用并验证这两个链接是否按预期工作。</span><span class="sxs-lookup"><span data-stu-id="23f53-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="23f53-249">虽然它可能显示自动链接器标记帮助程序是正确且完整的，但它有一个细微的问题。</span><span class="sxs-lookup"><span data-stu-id="23f53-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="23f53-250">如果首先运行 WWW 标记帮助程序，则 www 链接不正确。</span><span class="sxs-lookup"><span data-stu-id="23f53-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="23f53-251">通过添加 `Order` 重载更新代码来控制标记运行的顺序。</span><span class="sxs-lookup"><span data-stu-id="23f53-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="23f53-252">`Order` 属性确定相对于面向同一元素的其他标记帮助程序的执行顺序。</span><span class="sxs-lookup"><span data-stu-id="23f53-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="23f53-253">默认顺序值为零，并首先执行具有较低值的实例。</span><span class="sxs-lookup"><span data-stu-id="23f53-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="23f53-254">上面的代码可以保证 HTTP 标记帮助程序在 WWW 标记帮助程序之前运行。</span><span class="sxs-lookup"><span data-stu-id="23f53-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="23f53-255">将 `Order` 更改为 `MaxValue` 并验证为 WWW 标记生成的标记是否不正确。</span><span class="sxs-lookup"><span data-stu-id="23f53-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="23f53-256">检查和检索子内容</span><span class="sxs-lookup"><span data-stu-id="23f53-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="23f53-257">标记帮助程序提供多个属性来检索内容。</span><span class="sxs-lookup"><span data-stu-id="23f53-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="23f53-258">可将 `GetChildContentAsync` 的结果追加到 `output.Content`。</span><span class="sxs-lookup"><span data-stu-id="23f53-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="23f53-259">可使用 `GetContent` 检查 `GetChildContentAsync` 的结果。</span><span class="sxs-lookup"><span data-stu-id="23f53-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="23f53-260">如果修改 `output.Content`，则不会执行或呈现 TagHelper 主体，除非像自动链接器示例中那样调用 `GetChildContentAsync`：</span><span class="sxs-lookup"><span data-stu-id="23f53-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="23f53-261">对 `GetChildContentAsync` 的多次调用返回相同的值，且不重新执行 `TagHelper` 主体，除非传入一个指示不使用缓存结果的 false 参数。</span><span class="sxs-lookup"><span data-stu-id="23f53-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="23f53-262">加载缩小的分部视图 TagHelper</span><span class="sxs-lookup"><span data-stu-id="23f53-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="23f53-263">在生产环境中，可以通过加载缩小的分部视图来提升性能。</span><span class="sxs-lookup"><span data-stu-id="23f53-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="23f53-264">若要在生产环境中利用缩小的分部视图，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="23f53-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="23f53-265">创建/设置缩小分部视图的预生成过程。</span><span class="sxs-lookup"><span data-stu-id="23f53-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="23f53-266">使用以下代码在非开发环境中加载缩小的分部视图。</span><span class="sxs-lookup"><span data-stu-id="23f53-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
