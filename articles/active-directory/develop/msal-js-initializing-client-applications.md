---
title: 初始化 MSAL.js 客户端应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 初始化客户端应用程序。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5af5d3a88262792f4b32e2ce3d8143ac680f083a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87027030"
---
# <a name="initialize-client-applications-using-msaljs"></a>使用 MSAL.js 初始化客户端应用程序

本文介绍如何使用用户代理应用程序的实例初始化适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js)。

该用户代理应用程序是某种形式的公共客户端应用程序，其中的客户端代码在 Web 浏览器等用户代理中执行。 此类客户端不存储机密，因为浏览器上下文可公开访问。

若要详细了解客户端应用程序类型和应用程序配置选项，请参阅 [MSAL 中的公共和机密客户端应用](msal-client-applications.md)。

## <a name="prerequisites"></a>先决条件

在初始化应用程序之前，首先需要[在 Azure 门户中注册该应用程序](scenario-spa-app-registration.md)，从而在应用程序和 Microsoft 标识平台之间建立信任关系。

注册应用后，你需要以下部分值或所有值，这些可以在 Azure 门户中找到。

| 值 | 必选 | 说明 |
|:----- | :------: | :---------- |
| 应用程序（客户端）ID | 必选 | 在 Microsoft 标识平台中唯一标识你的应用程序的 GUID。 |
| 颁发机构 | 可选 | 标识提供者 URL（实例）和应用程序的登录受众 。 实例和登录受众连接起来后就组成了颁发机构。 |
| 目录（租户）ID | 可选 | 如果构建专用于组织的业务线应用程序（通常称为单租户应用程序），请指定此项。 |
| 重定向 URI | 可选 | 如果要构建 Web 应用，`redirectUri` 指定标识提供者（Microsoft 标识平台）应在何处返回其已颁发的安全令牌。 |

## <a name="initialize-msaljs-2x-apps"></a>初始化 MSAL.js 2.x 应用

通过使用[配置][msal-js-configuration]对象实例化 [PublicClientApplication][msal-js-publicclientapplication] 来初始化 MSAL 身份验证上下文。 所需的最低配置属性是应用程序的 `clientID`，在 Azure 门户中应用注册的“概述”页上显示为“应用程序(客户端) ID” 。

下面是一个示例配置对象和 `PublicClientApplication` 的实例化：

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

当应用程序使用重定向流时，调用 [handleRedirectPromise][msal-js-handleredirectpromise]。 使用重定向流时，应在每次加载页面时运行 `handleRedirectPromise`。

这个承诺有三种可能的结果：

- `.then` 被调用且 `tokenResponse` 为 truthy：应用程序从成功的重定向操作返回。
- `.then` 被调用且 `tokenResponse` 为 falsey (`null`)：应用程序未从重定向操作返回。
- `.catch` 被调用：应用程序从重定向操作返回，但出现错误。

## <a name="initialize-msaljs-1x-apps"></a>初始化 MSAL.js 1.x 应用

通过使用配置对象实例化 [UserAgentApplication][msal-js-useragentapplication] 来初始化 MSAL 1.x 身份验证上下文。 所需的最低配置属性是应用程序的 `clientID`，在 Azure 门户中应用注册的“概述”页上显示为“应用程序(客户端) ID” 。

对于 MSAL.js 1.2.x 或更早版本中使用重定向流（[loginRedirect][msal-js-loginredirect] 和 [acquireTokenRedirect][msal-js-acquiretokenredirect]）的身份验证方法，必须通过 `handleRedirectCallback()` 方法显式注册一个返回成功或错误结果的回调。 在 MSAL.js 1.2.x 和更早版本中，必须显式注册回调，因为重定向流不会像具有弹出体验的方法那样返回承诺。 在 MSAL.js 版本 1.3.x 和更高版本中注册回调是可选操作。

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>单实例和配置

MSAL.js 1.x 和 2.x 在设计上分别采用 `UserAgentApplication` 或 `PublicClientApplication` 的单个实例和配置，用于表示单个身份验证上下文。

不建议使用 `UserAgentApplication` 或 `PublicClientApplication` 的多个实例，因为它们会导致浏览器中出现有冲突的缓存条目和行为。

## <a name="next-steps"></a>后续步骤

GitHub 上的此 MSAL.js 2.x 代码示例演示如何使用[配置][msal-js-configuration]对象实例化 [PublicClientApplication][msal-js-publicclientapplication]：

[Azure-Samples/ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_configuration_.html
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
