---
title: 交互式请求中的提示行为（适用于 JavaScript 的 Microsoft 身份验证库）
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）在交互式调用中自定义提示行为。
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
ms.openlocfilehash: 42d6c4415a3eeb28c999d95b838c6dd7c0f6e606
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803024"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL 交互式请求中的提示行为

如果用户已与多个用户帐户建立活动 Azure AD 会话，则在继续登录之前，Azure AD 登录页将默认提示用户选择帐户。 如果仅有一个经过身份验证的会话与 Azure AD，用户将看不到帐户选择体验。

MSAL 库（从 v 0.2.4 开始）在交互式请求（`loginRedirect`、`loginPopup`、`acquireTokenRedirect` 和 `acquireTokenPopup`）期间不发送 prompt 参数，因此不会强制执行任何提示行为。 对于使用 `acquireTokenSilent` 方法的无提示令牌请求，MSAL 会将 prompt 参数设置传递给 `none`。

根据应用程序方案，可以通过在传递到方法的请求参数中设置 prompt 参数来控制交互式请求的提示行为。 例如，如果想要调用帐户选择体验：

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


在对 Azure AD 进行身份验证时，可以传递以下提示值：

**登录名：** 此值将强制用户输入身份验证请求的凭据。

**select_account：** 此值将为用户提供一个帐户选择体验，其中列出了会话中的所有帐户。

**同意：** 此值将调用允许用户向应用授予权限的 OAuth 许可对话。

**无：** 此值将确保用户不会看到任何交互式提示。 建议不要将此值传递给 MSAL 中的交互式方法，因为它可能会产生意外行为。 请改用 `acquireTokenSilent` 方法来实现无提示调用。

## <a name="next-steps"></a>后续步骤

阅读 MSAL 库使用的[OAuth 2.0 隐式授予](v2-oauth2-implicit-grant-flow.md)协议中有关 `prompt` 参数的详细信息。
