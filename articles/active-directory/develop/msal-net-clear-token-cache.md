---
title: 通过使用适用于.NET 的 Azure Microsoft 身份验证库清除令牌缓存
description: 了解如何清除令牌缓存使用 Microsoft 身份验证库.NET (MSAL.NET)。
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
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6763c6b2b1f9b4de7d8669a50a4979a7aac00c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544116"
---
# <a name="clear-the-token-cache-using-msalnet"></a>清除令牌缓存使用 MSAL.NET

当您[获取访问令牌](msal-acquire-cache-tokens.md)使用 Microsoft 身份验证库.NET (MSAL.NET)，缓存令牌。 当应用程序需要令牌时，应首先调用`AcquireTokenSilent`方法，若要验证的可接受的令牌是否在缓存中。 

清除缓存通过从缓存中删除帐户。 不过，这不会删除浏览器中的会话 Cookie。  下面的示例实例化的公共客户端应用程序、 应用程序，可以获取帐户并删除帐户。

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

若要了解有关获取和缓存令牌的详细信息，请阅读[获取访问令牌](msal-acquire-cache-tokens.md)。
