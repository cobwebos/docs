---
title: 传递身份验证请求中的自定义状态（MSAL） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）在身份验证请求中传递自定义状态参数值。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: be2dd887358aa00c87a4b5668a99c425d83b59dc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696021"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>使用 MSAL 在身份验证请求中传递自定义状态

由 OAuth 2.0 定义的*状态*参数包含在身份验证请求中，并且也在令牌响应中返回，以防止跨站点请求伪造攻击。 默认情况下，适用于 JavaScript 的 Microsoft 身份验证库（MSAL）在身份验证请求中传递随机生成的唯一*状态*参数值。

State 参数还可用于在重定向前对应用状态的信息进行编码。 可以在应用中传递用户的状态，例如，作为此参数的输入，例如它们所在的页面或视图。 MSAL 库允许将自定义状态作为状态参数传递到 `Request` 对象中：

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> 如果要跳过缓存的令牌并转到服务器，请将布尔 `forceRefresh` 传入到用于发出登录/令牌请求的 AuthenticationParameters 对象。
> 由于对应用程序性能的影响，默认情况下不应使用 `forceRefresh`。
> 依靠缓存可为用户提供更好的体验。
> 跳过缓存只应在知道当前缓存的数据没有最新信息的情况下使用。
> 例如，管理工具将角色添加到需要使用更新角色获取新令牌的用户。

例如：

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

发送请求时，传递的状态将追加到由 MSAL 设置的唯一 GUID。 返回响应时，MSAL 会检查状态匹配，然后以 `accountState`的形式返回 `Response` 对象中的自定义传入状态。

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

若要了解详细信息，请参阅使用 MSAL[生成单页应用程序（SPA）](scenario-spa-overview.md) 。