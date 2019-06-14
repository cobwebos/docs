---
title: 提示在交互式请求 （适用于 JavaScript 的 Microsoft 身份验证库） 中的行为 |Azure
description: 了解有关自定义交互式调用 javascript (MSAL.js) 使用 Microsoft 身份验证库中的提示行为。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544271"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js 交互式请求中的提示行为

用户已建立有效的 Azure AD 会话与多个用户帐户，Azure AD 登录页将默认情况下提示用户选择一个帐户，然后再继续操作以登录。 用户不会看到仅与 Azure AD 的单个经过身份验证的会话如果遇到帐户选择。

MSAL.js 库 （从 v0.2.4） 不会在交互式请求期间发送提示参数 (`loginRedirect`， `loginPopup`，`acquireTokenRedirect`和`acquireTokenPopup`)，并因此不会强制任何提示的行为。 对于使用无提示令牌请求`acquireTokenSilent`方法，在将传递给提示参数设置为 MSAL.js `none`。

根据你的应用场景，可以控制用于交互式请求设置中的请求参数的提示参数传递给方法的提示行为。 例如，如果你想要调用的帐户选择体验：

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


与 Azure AD 进行身份验证时，可以传递以下提示值：

**登录名：** 此值会强制用户在身份验证请求上输入凭据。

**select_account:** 此值将为用户提供在会话中列出所有帐户的帐户选择体验。

**许可：** 此值将会调用 OAuth 同意对话框，允许用户向应用授予权限。

**None:** 此值可确保用户不会看到任何交互提示。 我们建议不将此值传递给 MSAL.js 中的交互式方法，因为它会产生意外的行为。 请改用`acquireTokenSilent`方法来实现无提示的调用。

## <a name="next-steps"></a>后续步骤

详细了解`prompt`中的参数[OAuth 2.0 隐式授权](v2-oauth2-implicit-grant-flow.md)哪些 MSAL.js 库使用的协议。
