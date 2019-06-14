---
title: 传递身份验证请求 （适用于 JavaScript 的 Microsoft 身份验证库） 中的自定义状态 |Azure
description: 了解如何使用 JavaScript (MSAL.js) Microsoft 身份验证库身份验证请求中传递的自定义状态参数值。
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
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420493"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>将使用 MSAL.js 的身份验证请求中的自定义状态传递
*状态*参数定义的 OAuth 2.0 身份验证请求中包含和也会返回令牌响应，以防止跨站点请求伪造攻击。 默认情况下，JavaScript (MSAL.js) 的 Microsoft 身份验证库将传递随机生成唯一*状态*身份验证请求中的参数值。

此外可以使用状态参数之前重定向的应用程序的状态信息进行编码。 可以将传递在应用中，例如用户之前，作为此参数的输入页面或视图的用户的状态。 MSAL.js 库，可将自定义状态作为状态参数中传递`Request`对象：

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

传入的状态将追加到 MSAL.js 发送请求时设置的唯一 GUID。 时返回的响应，MSAL.js 检查以确定状态匹配，然后返回传入的状态中的自定义`Response`对象作为`accountState`。

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

若要了解详细信息，请阅读有关[生成单页面应用程序 (SPA)](scenario-spa-overview.md)使用 MSAL.js。