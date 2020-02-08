---
title: 清除令牌缓存（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）清除令牌缓存。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084765"
---
# <a name="clear-the-token-cache-using-msalnet"></a>使用 MSAL.NET 清除令牌缓存

使用用于 .NET 的 Microsoft 身份验证库（MSAL.NET）[获取访问令牌](msal-acquire-cache-tokens.md)时，会缓存令牌。 当应用程序需要令牌时，它应首先调用 `AcquireTokenSilent` 方法来验证缓存中是否有可接受的令牌。 

清除缓存是通过从缓存中删除帐户来实现的。 不过，这不会删除浏览器中的会话 Cookie。  下面的示例实例化一个公共客户端应用程序，获取该应用程序的帐户，然后删除这些帐户。

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

若要详细了解如何获取和缓存令牌，请参阅[获取访问令牌](msal-acquire-cache-tokens.md)。
