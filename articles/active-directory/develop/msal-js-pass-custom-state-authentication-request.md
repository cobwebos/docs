---
title: 传递身份验证请求中的自定义状态（JavaScript 的 Microsoft 身份验证库）
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）在身份验证请求中传递自定义状态参数值。
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
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d2fa7d7d294d38d29ce8ace744e13bd1bf2d533
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803038"
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
};
```

例如：

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
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