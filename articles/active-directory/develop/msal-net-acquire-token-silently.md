---
title: 以无提示方式获取令牌 （适用于.NET 的 Microsoft 身份验证库） |Azure
description: 了解如何获取访问令牌以无提示方式 （从令牌缓存中） 使用 Microsoft 身份验证库.NET (MSAL.NET)。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544235"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>从令牌缓存使用 MSAL.NET 获取令牌

获取使用 Microsoft 身份验证库.NET (MSAL.NET) 的访问令牌，缓存的令牌。 当应用程序需要令牌时，应首先调用`AcquireTokenSilent`方法，若要验证的可接受的令牌是否在缓存中。 在许多情况下，很可能与多个作用域根据令牌缓存中获取其他令牌。 还有可能 （因为令牌缓存还包含一个刷新令牌） 获取即将到期时刷新令牌。

建议的模式是调用`AcquireTokenSilent`方法第一个。  如果`AcquireTokenSilent`失败，然后获取令牌使用其他方法。

在以下示例中，应用程序首先尝试从令牌缓存获取令牌。  如果`MsalUiRequiredException`引发异常，该应用程序获取令牌以交互方式。 

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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