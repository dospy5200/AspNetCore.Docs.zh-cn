---
title: ASP.NET Core 中的哈希密码
author: rick-anderson
description: 了解如何使用 ASP.NET Core 数据保护 Api 为密码进行哈希处理。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: b88abd076febae95f8990bcd730ce0d4517e92d2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587757"
---
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="815ac-103">ASP.NET Core 中的哈希密码</span><span class="sxs-lookup"><span data-stu-id="815ac-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="815ac-104">数据保护基本代码包括一个包含加密密钥派生函数的 *AspNetCore 密钥派生* 。</span><span class="sxs-lookup"><span data-stu-id="815ac-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="815ac-105">此包是一个独立的组件，与数据保护系统的其余部分没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="815ac-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="815ac-106">它可以完全独立地使用。</span><span class="sxs-lookup"><span data-stu-id="815ac-106">It can be used completely independently.</span></span> <span data-ttu-id="815ac-107">为了方便起见，源与数据保护代码库并存。</span><span class="sxs-lookup"><span data-stu-id="815ac-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="815ac-108">包当前提供了一个方法 `KeyDerivation.Pbkdf2` ，该方法允许使用 [PBKDF2 算法](https://tools.ietf.org/html/rfc2898#section-5.2)对密码进行哈希处理。</span><span class="sxs-lookup"><span data-stu-id="815ac-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="815ac-109">此 API 与 .NET Framework 现有的 [Rfc2898DeriveBytes 类型](/dotnet/api/system.security.cryptography.rfc2898derivebytes)非常相似，但有三个重要的区别：</span><span class="sxs-lookup"><span data-stu-id="815ac-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="815ac-110">`KeyDerivation.Pbkdf2`方法支持在当前、和)  (使用多个 PRFs， `HMACSHA1` `HMACSHA256` `HMACSHA512` 而 `Rfc2898DeriveBytes` 类型仅支持 `HMACSHA1` 。</span><span class="sxs-lookup"><span data-stu-id="815ac-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="815ac-111">`KeyDerivation.Pbkdf2`方法将检测当前操作系统，并尝试选择最适合的例程实现，在某些情况下提供更好的性能。</span><span class="sxs-lookup"><span data-stu-id="815ac-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="815ac-112"> (在 Windows 8 上，它提供的吞吐量大约为10倍 `Rfc2898DeriveBytes` 。 ) </span><span class="sxs-lookup"><span data-stu-id="815ac-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="815ac-113">`KeyDerivation.Pbkdf2`方法要求调用方指定 (盐、PRF 和迭代计数) 的所有参数。</span><span class="sxs-lookup"><span data-stu-id="815ac-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="815ac-114">`Rfc2898DeriveBytes`类型为这些提供默认值。</span><span class="sxs-lookup"><span data-stu-id="815ac-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="815ac-115">查看[](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) ASP.NET Core Identity `PasswordHasher` 真实用例的类型的源代码。</span><span class="sxs-lookup"><span data-stu-id="815ac-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
