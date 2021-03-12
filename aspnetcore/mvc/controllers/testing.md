---
title: ASP.NET Core 中的测试控制器逻辑
author: ardalis
description: 了解如何使用 Moq 和 xUnit 测试 ASP.NET Core 中的控制器逻辑。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: mvc/controllers/testing
ms.openlocfilehash: 0f4fcceefdef946a5f29633c069cbb64ec345a3d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586951"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a><span data-ttu-id="09163-103">ASP.NET Core 中的单元测试控制器逻辑</span><span class="sxs-lookup"><span data-stu-id="09163-103">Unit test controller logic in ASP.NET Core</span></span>

<span data-ttu-id="09163-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="09163-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="09163-105">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)涉及通过基础结构和依赖项单独测试应用的一部分。</span><span class="sxs-lookup"><span data-stu-id="09163-105">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="09163-106">单元测试控制器逻辑时，仅测试单个操作的内容，不测试其依赖项或框架自身的行为。</span><span class="sxs-lookup"><span data-stu-id="09163-106">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

## <a name="unit-testing-controllers"></a><span data-ttu-id="09163-107">单元测试控制器</span><span class="sxs-lookup"><span data-stu-id="09163-107">Unit testing controllers</span></span>

<span data-ttu-id="09163-108">将控制器操作的单元测试设置为专注于控制器的行为。</span><span class="sxs-lookup"><span data-stu-id="09163-108">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="09163-109">控制器单元测试将避开[筛选器](xref:mvc/controllers/filters)、[路由](xref:fundamentals/routing)或[模型绑定](xref:mvc/models/model-binding)等方案。</span><span class="sxs-lookup"><span data-stu-id="09163-109">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="09163-110">涵盖共同响应请求的组件之间的交互的测试由 *集成测试* 处理。</span><span class="sxs-lookup"><span data-stu-id="09163-110">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="09163-111">有关集成测试的详细信息，请参阅<xref:test/integration-tests>。</span><span class="sxs-lookup"><span data-stu-id="09163-111">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="09163-112">如果编写自定义筛选器和路由，应对其单独进行单元测试，而不是在测试特定控制器操作时进行。</span><span class="sxs-lookup"><span data-stu-id="09163-112">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="09163-113">要演示控制器单元测试，请查看以下示例应用中的控制器。</span><span class="sxs-lookup"><span data-stu-id="09163-113">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> 

<span data-ttu-id="09163-114">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="09163-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="09163-115">主控制器显示集体讨论会话列表并允许使用 POST 请求创建新的集体讨论会话：</span><span class="sxs-lookup"><span data-stu-id="09163-115">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="09163-116">前面的控制器：</span><span class="sxs-lookup"><span data-stu-id="09163-116">The preceding controller:</span></span>

* <span data-ttu-id="09163-117">遵循[显式依赖关系原则](/dotnet/architecture/modern-web-apps-azure/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="09163-117">Follows the [Explicit Dependencies Principle](/dotnet/architecture/modern-web-apps-azure/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="09163-118">期望[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 提供 `IBrainstormSessionRepository` 的实例。</span><span class="sxs-lookup"><span data-stu-id="09163-118">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="09163-119">可以通过使用 mock 对象框架（如 [Moq](https://www.nuget.org/packages/Moq/)）的模拟 `IBrainstormSessionRepository` 服务进行测试。</span><span class="sxs-lookup"><span data-stu-id="09163-119">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="09163-120">*模拟对象* 是由一组预先确定的用于测试的属性和方法行为的对象。</span><span class="sxs-lookup"><span data-stu-id="09163-120">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="09163-121">有关详细信息，请参阅[集成测试简介](xref:test/integration-tests#introduction-to-integration-tests)。</span><span class="sxs-lookup"><span data-stu-id="09163-121">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="09163-122">`HTTP GET Index` 方法没有循环或分支，且仅调用一个方法。</span><span class="sxs-lookup"><span data-stu-id="09163-122">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="09163-123">此操作的单元测试：</span><span class="sxs-lookup"><span data-stu-id="09163-123">The unit test for this action:</span></span>

* <span data-ttu-id="09163-124">使用 `GetTestSessions` 方法模拟 `IBrainstormSessionRepository` 服务。</span><span class="sxs-lookup"><span data-stu-id="09163-124">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="09163-125">`GetTestSessions` 使用日期和会话名称创建两个 mock 集体讨论会话。</span><span class="sxs-lookup"><span data-stu-id="09163-125">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="09163-126">执行 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="09163-126">Executes the `Index` method.</span></span>
* <span data-ttu-id="09163-127">根据该方法返回的结果进行断言：</span><span class="sxs-lookup"><span data-stu-id="09163-127">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="09163-128">将返回 <xref:Microsoft.AspNetCore.Mvc.ViewResult>。</span><span class="sxs-lookup"><span data-stu-id="09163-128">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="09163-129">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) 是 `StormSessionViewModel`。</span><span class="sxs-lookup"><span data-stu-id="09163-129">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="09163-130">有两个集体讨论会话存储在 `ViewDataDictionary.Model` 中。</span><span class="sxs-lookup"><span data-stu-id="09163-130">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="09163-131">主控制器的 `HTTP POST Index` 方法测试验证：</span><span class="sxs-lookup"><span data-stu-id="09163-131">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="09163-132">当 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) 为 `false` 时，操作方法将返回有相应数据的 *400 错误请求* <xref:Microsoft.AspNetCore.Mvc.ViewResult>。</span><span class="sxs-lookup"><span data-stu-id="09163-132">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="09163-133">当 `ModelState.IsValid` 为 `true` 时：</span><span class="sxs-lookup"><span data-stu-id="09163-133">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="09163-134">将调用存储库上的 `Add` 方法。</span><span class="sxs-lookup"><span data-stu-id="09163-134">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="09163-135">将返回有正确参数的 <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>。</span><span class="sxs-lookup"><span data-stu-id="09163-135">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="09163-136">通过使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 添加错误来测试无效模型状态，如下第一个测试所示：</span><span class="sxs-lookup"><span data-stu-id="09163-136">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="09163-137">当 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 无效时，将返回与 GET 请求相同的 `ViewResult`。</span><span class="sxs-lookup"><span data-stu-id="09163-137">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="09163-138">测试不会尝试传入无效模型。</span><span class="sxs-lookup"><span data-stu-id="09163-138">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="09163-139">传入无效模型不是有效的方法，因为不会运行模型绑定（尽管[集成测试](xref:test/integration-tests)使用模型绑定）。</span><span class="sxs-lookup"><span data-stu-id="09163-139">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="09163-140">在本例中，不测试模型绑定。</span><span class="sxs-lookup"><span data-stu-id="09163-140">In this case, model binding isn't tested.</span></span> <span data-ttu-id="09163-141">这些单元测试仅测试操作方法中的代码。</span><span class="sxs-lookup"><span data-stu-id="09163-141">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="09163-142">第二个测试验证 `ModelState` 有效时的情况：</span><span class="sxs-lookup"><span data-stu-id="09163-142">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="09163-143">已通过存储库添加新的 `BrainstormSession`。</span><span class="sxs-lookup"><span data-stu-id="09163-143">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="09163-144">该方法将返回带有所需属性的 `RedirectToActionResult`。</span><span class="sxs-lookup"><span data-stu-id="09163-144">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="09163-145">通常会忽略未调用的模拟调用，但在设置调用末尾调用 `Verifiable` 就可以在测试中进行 mock 验证。</span><span class="sxs-lookup"><span data-stu-id="09163-145">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="09163-146">这通过对 `mockRepo.Verify` 的调用来完成，进行这种调用时，如果未调用所需方法，则测试将失败。</span><span class="sxs-lookup"><span data-stu-id="09163-146">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="09163-147">通过此示例中使用的 Moq 库，可以混合可验证（或称“严格”）mock 和非可验证 mock（也称为“宽松”mock 或存根）。</span><span class="sxs-lookup"><span data-stu-id="09163-147">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="09163-148">详细了解[使用 Moq 自定义 Mock 行为](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)。</span><span class="sxs-lookup"><span data-stu-id="09163-148">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="09163-149">示例应用中的 [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) 显示与特定集体讨论会话相关的信息。</span><span class="sxs-lookup"><span data-stu-id="09163-149">[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="09163-150">该控制器包含用于处理无效 `id` 值的逻辑（以下示例中有两个 `return` 方案可用来应对这些情况）。</span><span class="sxs-lookup"><span data-stu-id="09163-150">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="09163-151">最后的 `return` 语句向视图 (Controllers/SessionController.cs) 返回一个新的 `StormSessionViewModel`：</span><span class="sxs-lookup"><span data-stu-id="09163-151">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="09163-152">单元测试包括对会话控制器 `Index` 操作中的每个 `return` 方案执行一个测试：</span><span class="sxs-lookup"><span data-stu-id="09163-152">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="09163-153">移动到想法控制器，应用会将功能公开为 `api/ideas` 路由上的 Web API：</span><span class="sxs-lookup"><span data-stu-id="09163-153">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="09163-154">`ForSession` 方法将返回与集体讨论会话关联的想法列表 (`IdeaDTO`)。</span><span class="sxs-lookup"><span data-stu-id="09163-154">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="09163-155">`Create` 方法会向会话中添加新想法。</span><span class="sxs-lookup"><span data-stu-id="09163-155">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="09163-156">避免直接通过 API 调用返回企业域实体。</span><span class="sxs-lookup"><span data-stu-id="09163-156">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="09163-157">域实体：</span><span class="sxs-lookup"><span data-stu-id="09163-157">Domain entities:</span></span>

* <span data-ttu-id="09163-158">包含的数据通常比客户端所需的数据更多。</span><span class="sxs-lookup"><span data-stu-id="09163-158">Often include more data than the client requires.</span></span>
* <span data-ttu-id="09163-159">无需将应用的内部域模型与公开的 API 结合。</span><span class="sxs-lookup"><span data-stu-id="09163-159">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="09163-160">可以执行域实体与返回到客户端的类型之间的映射：</span><span class="sxs-lookup"><span data-stu-id="09163-160">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="09163-161">手动执行 LINQ `Select`，如同示例应用所用的那样。</span><span class="sxs-lookup"><span data-stu-id="09163-161">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="09163-162">有关详细信息，请参阅 [LINQ（语言集成查询）](/dotnet/standard/using-linq)。</span><span class="sxs-lookup"><span data-stu-id="09163-162">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="09163-163">自动生成库，如 [AutoMapper](https://github.com/AutoMapper/AutoMapper)。</span><span class="sxs-lookup"><span data-stu-id="09163-163">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="09163-164">接着，示例应用会演示想法控制器的 `Create` 和 `ForSession` API 方法的单元测试。</span><span class="sxs-lookup"><span data-stu-id="09163-164">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="09163-165">示例应用包含两个 `ForSession` 测试。</span><span class="sxs-lookup"><span data-stu-id="09163-165">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="09163-166">第一个测试可确定 `ForSession` 是否返回无效会话的 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>（找不到 HTTP）：</span><span class="sxs-lookup"><span data-stu-id="09163-166">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="09163-167">第二个 `ForSession` 测试可确定 `ForSession` 是否返回有效会话的会话想法列表 (`<List<IdeaDTO>>`)。</span><span class="sxs-lookup"><span data-stu-id="09163-167">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="09163-168">这些测试还会检查第一个想法，以确认其 `Name` 属性正确：</span><span class="sxs-lookup"><span data-stu-id="09163-168">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="09163-169">若要测试 `Create` 方法在 `ModelState` 无效时的行为，示例应用会在测试中将模型错误添加到控制器。</span><span class="sxs-lookup"><span data-stu-id="09163-169">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="09163-170">请勿在单元测试中尝试测试模型有效性或模型绑定&mdash;仅测试操作方法在遇到无效 `ModelState` 时的行为：</span><span class="sxs-lookup"><span data-stu-id="09163-170">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="09163-171">`Create` 的第二个测试依赖存储库返回 `null`，所以 mock 存储库配置为返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="09163-171">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="09163-172">无需创建测试数据库（在内存中或其他位置）并构建将返回此结果的查询。</span><span class="sxs-lookup"><span data-stu-id="09163-172">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="09163-173">该测试可以在单个语句中完成，如示例代码所示：</span><span class="sxs-lookup"><span data-stu-id="09163-173">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="09163-174">第三个 `Create` 测试 `Create_ReturnsNewlyCreatedIdeaForSession` 验证调用了存储库的 `UpdateAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="09163-174">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="09163-175">使用 `Verifiable` 调用 mock，然后调用模拟存储库的 `Verify` 方法，以确认执行了可验证的方法。</span><span class="sxs-lookup"><span data-stu-id="09163-175">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="09163-176">确保 `UpdateAsync` 方法保存了数据不是单元测试的职责&mdash;这可以通过集成测试完成。</span><span class="sxs-lookup"><span data-stu-id="09163-176">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a><span data-ttu-id="09163-177">测试 ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="09163-177">Test ActionResult\<T></span></span>

<span data-ttu-id="09163-178">在 ASP.NET Core 2.1 或更高版本中， [ActionResult \<T> ](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) 使你可以返回派生自 `ActionResult` 或返回特定类型的类型。</span><span class="sxs-lookup"><span data-stu-id="09163-178">In ASP.NET Core 2.1 or later, [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="09163-179">示例应用包含将返回给定会话 `id` 的 `List<IdeaDTO>` 的方法。</span><span class="sxs-lookup"><span data-stu-id="09163-179">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="09163-180">如果会话 `id` 不存在，控制器将返回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>：</span><span class="sxs-lookup"><span data-stu-id="09163-180">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="09163-181">`ApiIdeasControllerTests` 中包含 `ForSessionActionResult` 控制器的两个测试。</span><span class="sxs-lookup"><span data-stu-id="09163-181">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="09163-182">第一个测试可确认控制器将返回 `ActionResult`，而不是不存在会话 `id` 的不存在想法列表：</span><span class="sxs-lookup"><span data-stu-id="09163-182">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="09163-183">`ActionResult` 类型为 `ActionResult<List<IdeaDTO>>`。</span><span class="sxs-lookup"><span data-stu-id="09163-183">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="09163-184"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> 为 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>。</span><span class="sxs-lookup"><span data-stu-id="09163-184">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="09163-185">对于有效会话 `id`，第二个测试可确认该方法将返回：</span><span class="sxs-lookup"><span data-stu-id="09163-185">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="09163-186">类型为 `List<IdeaDTO>` 的 `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="09163-186">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="09163-187">[ActionResult \<T> 。值](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)为 `List<IdeaDTO>` 类型。</span><span class="sxs-lookup"><span data-stu-id="09163-187">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="09163-188">列表中的第一项是与 mock 会话中存储的想法匹配的有效想法（通过调用 `GetTestSession` 获取）。</span><span class="sxs-lookup"><span data-stu-id="09163-188">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="09163-189">示例应用还包含用于为给定会话创建新的 `Idea` 的方法。</span><span class="sxs-lookup"><span data-stu-id="09163-189">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="09163-190">控制器将返回：</span><span class="sxs-lookup"><span data-stu-id="09163-190">The controller returns:</span></span>

* <span data-ttu-id="09163-191"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>（对于无效模型）。</span><span class="sxs-lookup"><span data-stu-id="09163-191"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="09163-192"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>（如果会话不存在）。</span><span class="sxs-lookup"><span data-stu-id="09163-192"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="09163-193"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>（当使用新想法更新会话时）。</span><span class="sxs-lookup"><span data-stu-id="09163-193"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="09163-194">`ApiIdeasControllerTests` 中包含 `CreateActionResult` 的三个测试。</span><span class="sxs-lookup"><span data-stu-id="09163-194">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="09163-195">第一个测试可确认将返回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>（对于无效模型）。</span><span class="sxs-lookup"><span data-stu-id="09163-195">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="09163-196">第二个测试可确认将返回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>（如果会话不存在）。</span><span class="sxs-lookup"><span data-stu-id="09163-196">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="09163-197">对于有效会话 `id`，最后一个测试可确认：</span><span class="sxs-lookup"><span data-stu-id="09163-197">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="09163-198">该方法将返回类型为 `BrainstormSession` 的 `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="09163-198">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="09163-199">[ActionResult \<T> 。Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*)为 <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> 。</span><span class="sxs-lookup"><span data-stu-id="09163-199">The [ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="09163-200">`CreatedAtActionResult` 类似于包含 `Location` 标头的 *201 Created* 响应。</span><span class="sxs-lookup"><span data-stu-id="09163-200">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="09163-201">[ActionResult \<T> 。值](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)为 `BrainstormSession` 类型。</span><span class="sxs-lookup"><span data-stu-id="09163-201">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="09163-202">调用了用于更新会话 `UpdateAsync(testSession)` 的 mock 调用。</span><span class="sxs-lookup"><span data-stu-id="09163-202">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="09163-203">通过执行断言中的 `mockRepo.Verify()` 来检查 `Verifiable` 方法调用。</span><span class="sxs-lookup"><span data-stu-id="09163-203">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="09163-204">将返回该会话的两个 `Idea` 对象。</span><span class="sxs-lookup"><span data-stu-id="09163-204">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="09163-205">最后一项（通过对 `UpdateAsync` 的 mock 调用而添加的 `Idea`）与添加到测试中的会话的 `newIdea` 匹配。</span><span class="sxs-lookup"><span data-stu-id="09163-205">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="09163-206">[控制器](xref:mvc/controllers/actions)在任何 ASP.NET Core MVC 应用中起着核心作用。</span><span class="sxs-lookup"><span data-stu-id="09163-206">[Controllers](xref:mvc/controllers/actions) play a central role in any ASP.NET Core MVC app.</span></span> <span data-ttu-id="09163-207">因此，应该对控制器表现达到预期怀有信心。</span><span class="sxs-lookup"><span data-stu-id="09163-207">As such, you should have confidence that controllers behave as intended.</span></span> <span data-ttu-id="09163-208">在将应用部署到生产环境之前，自动测试可以检测到错误。</span><span class="sxs-lookup"><span data-stu-id="09163-208">Automated tests can detect errors before the app is deployed to a production environment.</span></span>

<span data-ttu-id="09163-209">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="09163-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/testing/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="unit-tests-of-controller-logic"></a><span data-ttu-id="09163-210">控制器逻辑的单元测试</span><span class="sxs-lookup"><span data-stu-id="09163-210">Unit tests of controller logic</span></span>

<span data-ttu-id="09163-211">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)涉及通过基础结构和依赖项单独测试应用的一部分。</span><span class="sxs-lookup"><span data-stu-id="09163-211">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="09163-212">单元测试控制器逻辑时，仅测试单个操作的内容，不测试其依赖项或框架自身的行为。</span><span class="sxs-lookup"><span data-stu-id="09163-212">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

<span data-ttu-id="09163-213">将控制器操作的单元测试设置为专注于控制器的行为。</span><span class="sxs-lookup"><span data-stu-id="09163-213">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="09163-214">控制器单元测试将避开[筛选器](xref:mvc/controllers/filters)、[路由](xref:fundamentals/routing)或[模型绑定](xref:mvc/models/model-binding)等方案。</span><span class="sxs-lookup"><span data-stu-id="09163-214">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="09163-215">涵盖共同响应请求的组件之间的交互的测试由 *集成测试* 处理。</span><span class="sxs-lookup"><span data-stu-id="09163-215">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="09163-216">有关集成测试的详细信息，请参阅<xref:test/integration-tests>。</span><span class="sxs-lookup"><span data-stu-id="09163-216">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="09163-217">如果编写自定义筛选器和路由，应对其单独进行单元测试，而不是在测试特定控制器操作时进行。</span><span class="sxs-lookup"><span data-stu-id="09163-217">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="09163-218">要演示控制器单元测试，请查看以下示例应用中的控制器。</span><span class="sxs-lookup"><span data-stu-id="09163-218">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> <span data-ttu-id="09163-219">主控制器显示集体讨论会话列表并允许使用 POST 请求创建新的集体讨论会话：</span><span class="sxs-lookup"><span data-stu-id="09163-219">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="09163-220">前面的控制器：</span><span class="sxs-lookup"><span data-stu-id="09163-220">The preceding controller:</span></span>

* <span data-ttu-id="09163-221">遵循[显式依赖关系原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="09163-221">Follows the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="09163-222">期望[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 提供 `IBrainstormSessionRepository` 的实例。</span><span class="sxs-lookup"><span data-stu-id="09163-222">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="09163-223">可以通过使用 mock 对象框架（如 [Moq](https://www.nuget.org/packages/Moq/)）的模拟 `IBrainstormSessionRepository` 服务进行测试。</span><span class="sxs-lookup"><span data-stu-id="09163-223">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="09163-224">*模拟对象* 是由一组预先确定的用于测试的属性和方法行为的对象。</span><span class="sxs-lookup"><span data-stu-id="09163-224">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="09163-225">有关详细信息，请参阅[集成测试简介](xref:test/integration-tests#introduction-to-integration-tests)。</span><span class="sxs-lookup"><span data-stu-id="09163-225">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="09163-226">`HTTP GET Index` 方法没有循环或分支，且仅调用一个方法。</span><span class="sxs-lookup"><span data-stu-id="09163-226">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="09163-227">此操作的单元测试：</span><span class="sxs-lookup"><span data-stu-id="09163-227">The unit test for this action:</span></span>

* <span data-ttu-id="09163-228">使用 `GetTestSessions` 方法模拟 `IBrainstormSessionRepository` 服务。</span><span class="sxs-lookup"><span data-stu-id="09163-228">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="09163-229">`GetTestSessions` 使用日期和会话名称创建两个 mock 集体讨论会话。</span><span class="sxs-lookup"><span data-stu-id="09163-229">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="09163-230">执行 `Index` 方法。</span><span class="sxs-lookup"><span data-stu-id="09163-230">Executes the `Index` method.</span></span>
* <span data-ttu-id="09163-231">根据该方法返回的结果进行断言：</span><span class="sxs-lookup"><span data-stu-id="09163-231">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="09163-232">将返回 <xref:Microsoft.AspNetCore.Mvc.ViewResult>。</span><span class="sxs-lookup"><span data-stu-id="09163-232">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="09163-233">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) 是 `StormSessionViewModel`。</span><span class="sxs-lookup"><span data-stu-id="09163-233">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="09163-234">有两个集体讨论会话存储在 `ViewDataDictionary.Model` 中。</span><span class="sxs-lookup"><span data-stu-id="09163-234">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="09163-235">主控制器的 `HTTP POST Index` 方法测试验证：</span><span class="sxs-lookup"><span data-stu-id="09163-235">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="09163-236">当 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) 为 `false` 时，操作方法将返回有相应数据的 *400 错误请求* <xref:Microsoft.AspNetCore.Mvc.ViewResult>。</span><span class="sxs-lookup"><span data-stu-id="09163-236">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="09163-237">当 `ModelState.IsValid` 为 `true` 时：</span><span class="sxs-lookup"><span data-stu-id="09163-237">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="09163-238">将调用存储库上的 `Add` 方法。</span><span class="sxs-lookup"><span data-stu-id="09163-238">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="09163-239">将返回有正确参数的 <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>。</span><span class="sxs-lookup"><span data-stu-id="09163-239">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="09163-240">通过使用 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> 添加错误来测试无效模型状态，如下第一个测试所示：</span><span class="sxs-lookup"><span data-stu-id="09163-240">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="09163-241">当 [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 无效时，将返回与 GET 请求相同的 `ViewResult`。</span><span class="sxs-lookup"><span data-stu-id="09163-241">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="09163-242">测试不会尝试传入无效模型。</span><span class="sxs-lookup"><span data-stu-id="09163-242">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="09163-243">传入无效模型不是有效的方法，因为不会运行模型绑定（尽管[集成测试](xref:test/integration-tests)使用模型绑定）。</span><span class="sxs-lookup"><span data-stu-id="09163-243">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="09163-244">在本例中，不测试模型绑定。</span><span class="sxs-lookup"><span data-stu-id="09163-244">In this case, model binding isn't tested.</span></span> <span data-ttu-id="09163-245">这些单元测试仅测试操作方法中的代码。</span><span class="sxs-lookup"><span data-stu-id="09163-245">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="09163-246">第二个测试验证 `ModelState` 有效时的情况：</span><span class="sxs-lookup"><span data-stu-id="09163-246">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="09163-247">已通过存储库添加新的 `BrainstormSession`。</span><span class="sxs-lookup"><span data-stu-id="09163-247">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="09163-248">该方法将返回带有所需属性的 `RedirectToActionResult`。</span><span class="sxs-lookup"><span data-stu-id="09163-248">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="09163-249">通常会忽略未调用的模拟调用，但在设置调用末尾调用 `Verifiable` 就可以在测试中进行 mock 验证。</span><span class="sxs-lookup"><span data-stu-id="09163-249">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="09163-250">这通过对 `mockRepo.Verify` 的调用来完成，进行这种调用时，如果未调用所需方法，则测试将失败。</span><span class="sxs-lookup"><span data-stu-id="09163-250">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="09163-251">通过此示例中使用的 Moq 库，可以混合可验证（或称“严格”）mock 和非可验证 mock（也称为“宽松”mock 或存根）。</span><span class="sxs-lookup"><span data-stu-id="09163-251">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="09163-252">详细了解[使用 Moq 自定义 Mock 行为](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)。</span><span class="sxs-lookup"><span data-stu-id="09163-252">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="09163-253">示例应用中的 [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) 显示与特定集体讨论会话相关的信息。</span><span class="sxs-lookup"><span data-stu-id="09163-253">[SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="09163-254">该控制器包含用于处理无效 `id` 值的逻辑（以下示例中有两个 `return` 方案可用来应对这些情况）。</span><span class="sxs-lookup"><span data-stu-id="09163-254">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="09163-255">最后的 `return` 语句向视图 (Controllers/SessionController.cs) 返回一个新的 `StormSessionViewModel`：</span><span class="sxs-lookup"><span data-stu-id="09163-255">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="09163-256">单元测试包括对会话控制器 `Index` 操作中的每个 `return` 方案执行一个测试：</span><span class="sxs-lookup"><span data-stu-id="09163-256">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="09163-257">移动到想法控制器，应用会将功能公开为 `api/ideas` 路由上的 Web API：</span><span class="sxs-lookup"><span data-stu-id="09163-257">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="09163-258">`ForSession` 方法将返回与集体讨论会话关联的想法列表 (`IdeaDTO`)。</span><span class="sxs-lookup"><span data-stu-id="09163-258">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="09163-259">`Create` 方法会向会话中添加新想法。</span><span class="sxs-lookup"><span data-stu-id="09163-259">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="09163-260">避免直接通过 API 调用返回企业域实体。</span><span class="sxs-lookup"><span data-stu-id="09163-260">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="09163-261">域实体：</span><span class="sxs-lookup"><span data-stu-id="09163-261">Domain entities:</span></span>

* <span data-ttu-id="09163-262">包含的数据通常比客户端所需的数据更多。</span><span class="sxs-lookup"><span data-stu-id="09163-262">Often include more data than the client requires.</span></span>
* <span data-ttu-id="09163-263">无需将应用的内部域模型与公开的 API 结合。</span><span class="sxs-lookup"><span data-stu-id="09163-263">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="09163-264">可以执行域实体与返回到客户端的类型之间的映射：</span><span class="sxs-lookup"><span data-stu-id="09163-264">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="09163-265">手动执行 LINQ `Select`，如同示例应用所用的那样。</span><span class="sxs-lookup"><span data-stu-id="09163-265">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="09163-266">有关详细信息，请参阅 [LINQ（语言集成查询）](/dotnet/standard/using-linq)。</span><span class="sxs-lookup"><span data-stu-id="09163-266">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="09163-267">自动生成库，如 [AutoMapper](https://github.com/AutoMapper/AutoMapper)。</span><span class="sxs-lookup"><span data-stu-id="09163-267">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="09163-268">接着，示例应用会演示想法控制器的 `Create` 和 `ForSession` API 方法的单元测试。</span><span class="sxs-lookup"><span data-stu-id="09163-268">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="09163-269">示例应用包含两个 `ForSession` 测试。</span><span class="sxs-lookup"><span data-stu-id="09163-269">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="09163-270">第一个测试可确定 `ForSession` 是否返回无效会话的 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>（找不到 HTTP）：</span><span class="sxs-lookup"><span data-stu-id="09163-270">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="09163-271">第二个 `ForSession` 测试可确定 `ForSession` 是否返回有效会话的会话想法列表 (`<List<IdeaDTO>>`)。</span><span class="sxs-lookup"><span data-stu-id="09163-271">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="09163-272">这些测试还会检查第一个想法，以确认其 `Name` 属性正确：</span><span class="sxs-lookup"><span data-stu-id="09163-272">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="09163-273">若要测试 `Create` 方法在 `ModelState` 无效时的行为，示例应用会在测试中将模型错误添加到控制器。</span><span class="sxs-lookup"><span data-stu-id="09163-273">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="09163-274">请勿在单元测试中尝试测试模型有效性或模型绑定&mdash;仅测试操作方法在遇到无效 `ModelState` 时的行为：</span><span class="sxs-lookup"><span data-stu-id="09163-274">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="09163-275">`Create` 的第二个测试依赖存储库返回 `null`，所以 mock 存储库配置为返回 `null`。</span><span class="sxs-lookup"><span data-stu-id="09163-275">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="09163-276">无需创建测试数据库（在内存中或其他位置）并构建将返回此结果的查询。</span><span class="sxs-lookup"><span data-stu-id="09163-276">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="09163-277">该测试可以在单个语句中完成，如示例代码所示：</span><span class="sxs-lookup"><span data-stu-id="09163-277">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="09163-278">第三个 `Create` 测试 `Create_ReturnsNewlyCreatedIdeaForSession` 验证调用了存储库的 `UpdateAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="09163-278">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="09163-279">使用 `Verifiable` 调用 mock，然后调用模拟存储库的 `Verify` 方法，以确认执行了可验证的方法。</span><span class="sxs-lookup"><span data-stu-id="09163-279">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="09163-280">确保 `UpdateAsync` 方法保存了数据不是单元测试的职责&mdash;这可以通过集成测试完成。</span><span class="sxs-lookup"><span data-stu-id="09163-280">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a><span data-ttu-id="09163-281">测试 ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="09163-281">Test ActionResult\<T></span></span>

<span data-ttu-id="09163-282">在 ASP.NET Core 2.1 或更高版本中， [ActionResult \<T> ](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) 使你可以返回派生自 `ActionResult` 或返回特定类型的类型。</span><span class="sxs-lookup"><span data-stu-id="09163-282">In ASP.NET Core 2.1 or later, [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="09163-283">示例应用包含将返回给定会话 `id` 的 `List<IdeaDTO>` 的方法。</span><span class="sxs-lookup"><span data-stu-id="09163-283">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="09163-284">如果会话 `id` 不存在，控制器将返回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>：</span><span class="sxs-lookup"><span data-stu-id="09163-284">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="09163-285">`ApiIdeasControllerTests` 中包含 `ForSessionActionResult` 控制器的两个测试。</span><span class="sxs-lookup"><span data-stu-id="09163-285">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="09163-286">第一个测试可确认控制器将返回 `ActionResult`，而不是不存在会话 `id` 的不存在想法列表：</span><span class="sxs-lookup"><span data-stu-id="09163-286">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="09163-287">`ActionResult` 类型为 `ActionResult<List<IdeaDTO>>`。</span><span class="sxs-lookup"><span data-stu-id="09163-287">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="09163-288"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> 为 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>。</span><span class="sxs-lookup"><span data-stu-id="09163-288">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="09163-289">对于有效会话 `id`，第二个测试可确认该方法将返回：</span><span class="sxs-lookup"><span data-stu-id="09163-289">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="09163-290">类型为 `List<IdeaDTO>` 的 `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="09163-290">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="09163-291">[ActionResult \<T> 。值](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)为 `List<IdeaDTO>` 类型。</span><span class="sxs-lookup"><span data-stu-id="09163-291">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="09163-292">列表中的第一项是与 mock 会话中存储的想法匹配的有效想法（通过调用 `GetTestSession` 获取）。</span><span class="sxs-lookup"><span data-stu-id="09163-292">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="09163-293">示例应用还包含用于为给定会话创建新的 `Idea` 的方法。</span><span class="sxs-lookup"><span data-stu-id="09163-293">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="09163-294">控制器将返回：</span><span class="sxs-lookup"><span data-stu-id="09163-294">The controller returns:</span></span>

* <span data-ttu-id="09163-295"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>（对于无效模型）。</span><span class="sxs-lookup"><span data-stu-id="09163-295"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="09163-296"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>（如果会话不存在）。</span><span class="sxs-lookup"><span data-stu-id="09163-296"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="09163-297"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>（当使用新想法更新会话时）。</span><span class="sxs-lookup"><span data-stu-id="09163-297"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="09163-298">`ApiIdeasControllerTests` 中包含 `CreateActionResult` 的三个测试。</span><span class="sxs-lookup"><span data-stu-id="09163-298">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="09163-299">第一个测试可确认将返回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>（对于无效模型）。</span><span class="sxs-lookup"><span data-stu-id="09163-299">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="09163-300">第二个测试可确认将返回 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>（如果会话不存在）。</span><span class="sxs-lookup"><span data-stu-id="09163-300">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="09163-301">对于有效会话 `id`，最后一个测试可确认：</span><span class="sxs-lookup"><span data-stu-id="09163-301">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="09163-302">该方法将返回类型为 `BrainstormSession` 的 `ActionResult`。</span><span class="sxs-lookup"><span data-stu-id="09163-302">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="09163-303">[ActionResult \<T> 。Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*)为 <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> 。</span><span class="sxs-lookup"><span data-stu-id="09163-303">The [ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="09163-304">`CreatedAtActionResult` 类似于包含 `Location` 标头的 *201 Created* 响应。</span><span class="sxs-lookup"><span data-stu-id="09163-304">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="09163-305">[ActionResult \<T> 。值](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)为 `BrainstormSession` 类型。</span><span class="sxs-lookup"><span data-stu-id="09163-305">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="09163-306">调用了用于更新会话 `UpdateAsync(testSession)` 的 mock 调用。</span><span class="sxs-lookup"><span data-stu-id="09163-306">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="09163-307">通过执行断言中的 `mockRepo.Verify()` 来检查 `Verifiable` 方法调用。</span><span class="sxs-lookup"><span data-stu-id="09163-307">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="09163-308">将返回该会话的两个 `Idea` 对象。</span><span class="sxs-lookup"><span data-stu-id="09163-308">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="09163-309">最后一项（通过对 `UpdateAsync` 的 mock 调用而添加的 `Idea`）与添加到测试中的会话的 `newIdea` 匹配。</span><span class="sxs-lookup"><span data-stu-id="09163-309">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="09163-310">其他资源</span><span class="sxs-lookup"><span data-stu-id="09163-310">Additional resources</span></span>

* <xref:test/integration-tests>
* [<span data-ttu-id="09163-311">用 Visual Studio 创建和运行单元测试</span><span class="sxs-lookup"><span data-stu-id="09163-311">Create and run unit tests with Visual Studio</span></span>](/visualstudio/test/unit-test-your-code)
* <span data-ttu-id="09163-312">[MyTested AspNetCore-ASP.NET CORE mvc 的流畅测试库](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc)：强类型单元测试库，提供用于测试 Mvc 和 web API 应用的流畅界面。</span><span class="sxs-lookup"><span data-stu-id="09163-312">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="09163-313">（*不由 Microsoft 进行支持或维护*。）</span><span class="sxs-lookup"><span data-stu-id="09163-313">(*Not maintained or supported by Microsoft.*)</span></span>
* <span data-ttu-id="09163-314">[JustMockLite](https://github.com/telerik/JustMockLite)：面向 .NET 开发人员的模拟框架。</span><span class="sxs-lookup"><span data-stu-id="09163-314">[JustMockLite](https://github.com/telerik/JustMockLite): A mocking framework for .NET developers.</span></span> <span data-ttu-id="09163-315">（*不由 Microsoft 进行支持或维护*。）</span><span class="sxs-lookup"><span data-stu-id="09163-315">(*Not maintained or supported by Microsoft.*)</span></span>

