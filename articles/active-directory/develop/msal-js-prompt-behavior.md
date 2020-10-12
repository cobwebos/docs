---
title: '交互式请求提示行为 ( # A0) |Microsoft'
titleSuffix: Microsoft identity platform
description: '了解如何使用适用于 JavaScript ( # A0) 的 Microsoft 身份验证库在交互式调用中自定义提示行为。'
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85477543"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js 交互式请求中的提示行为

当用户通过多个用户帐户建立一个活动的 Azure AD 会话时，Azure AD 登录页面会在默认情况下提示用户在继续登录之前选择一个帐户。 如果只有一个通过 Azure AD 进行的经身份验证的会话，则用户不会看到帐户选择体验。

MSAL.js 库（从 v0.2.4 开始）在交互式请求（`loginRedirect`、`loginPopup`、`acquireTokenRedirect` 和 `acquireTokenPopup`）过程中不发送 prompt 参数，因此不强制任何提示行为。 对于使用 `acquireTokenSilent` 方法的无提示令牌请求，MSAL.js 会传递一个设置为 `none` 的 prompt 参数。

可以根据应用程序方案控制交互式请求的提示行为，只需在传递给方法的请求参数中设置 prompt 参数即可。 例如，若要调用帐户选择体验，请执行以下操作：

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


使用 Azure AD 进行身份验证时，可以传递以下提示值：

**login:** 此值会强制用户在收到身份验证请求时输入凭据。

**select_account:** 此值会列出会话中的所有帐户，为用户提供帐户选择体验。

**consent:** 此值会调用 OAuth 许可对话框，让用户授予对应用的权限。

**none:** 此值会确保用户看不到任何交互式提示。 建议不要将此值传递给 MSAL.js 中的交互式方法，因为它可能导致意外的行为。 请改用 `acquireTokenSilent` 方法来实现无提示调用。

## <a name="next-steps"></a>后续步骤

详细了解 MSAL.js 库使用的 [OAuth 2.0 隐式授予](v2-oauth2-implicit-grant-flow.md)协议中的 `prompt` 参数。
