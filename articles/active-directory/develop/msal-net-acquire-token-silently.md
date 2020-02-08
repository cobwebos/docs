---
title: 从缓存中获取令牌（MSAL.NET）
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）以无提示方式（从令牌缓存）获取访问令牌。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084838"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>使用 MSAL.NET 从令牌缓存获取令牌

使用用于 .NET 的 Microsoft 身份验证库（MSAL.NET）获取访问令牌时，会缓存令牌。 当应用程序需要令牌时，它应首先调用 `AcquireTokenSilent` 方法来验证缓存中是否有可接受的令牌。 在许多情况下，可以根据缓存中的令牌获取具有更多作用域的另一个令牌。 还可以在令牌接近过期时刷新令牌（因为令牌缓存还包含刷新令牌）。

建议的模式是先调用 `AcquireTokenSilent` 方法。  如果 `AcquireTokenSilent` 失败，则使用其他方法获取令牌。

在下面的示例中，应用程序首先尝试从令牌缓存获取令牌。  如果引发 `MsalUiRequiredException` 异常，应用程序将以交互方式获取令牌。 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
