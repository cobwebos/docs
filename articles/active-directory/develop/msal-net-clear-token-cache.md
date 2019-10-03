---
title: 使用适用于 .NET 的 Microsoft 身份验证库清除令牌缓存 - Azure
description: 了解如何使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 清除令牌缓存。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cee6443db0b019f79a80cf5b7c0e2a7a50240f2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532663"
---
# <a name="clear-the-token-cache-using-msalnet"></a>使用 MSAL.NET 清除令牌缓存

使用适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) [获取访问令牌](msal-acquire-cache-tokens.md)时，将缓存该令牌。 应用程序需要令牌时，应该首先调用 `AcquireTokenSilent` 方法来验证缓存中是否有可接受的令牌。 

通过从缓存中删除帐户来实现清除缓存。 不过，这不会删除浏览器中的会话 Cookie。  以下示例实例化公共客户端应用程序，获取应用程序的帐户，并删除帐户。

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

若要详细了解如何获取和缓存令牌，请阅读[获取访问令牌](msal-acquire-cache-tokens.md)。
