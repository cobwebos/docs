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
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027030"
---
# <a name="initialize-client-applications-using-msaljs"></a>使用 MSAL.js 初始化客户端应用程序

本文介绍如何使用用户代理应用程序的实例初始化适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js)。

该用户代理应用程序是某种形式的公共客户端应用程序，其中的客户端代码在 Web 浏览器等用户代理中执行。 此类客户端不会存储机密，因为浏览器上下文可公开访问。

若要了解有关客户端应用程序类型和应用程序配置选项的详细信息，请参阅[MSAL 中的公共和机密客户端应用](msal-client-applications.md)。

## <a name="prerequisites"></a>先决条件

在初始化应用程序之前，首先需要将[其注册到 Azure 门户](scenario-spa-app-registration.md)，从而在应用程序和 Microsoft 标识平台之间建立信任关系。

注册应用后，需要在 Azure 门户中找到的以下部分或所有值。

| 值 | 必填 | 说明 |
|:----- | :------: | :---------- |
| 应用程序（客户端）ID | 必填 | 一个 GUID，用于唯一标识 Microsoft 标识平台中的应用程序。 |
| 颁发机构 | 可选 | 应用程序的标识提供程序 URL （*实例*）和*登录受众*。 当连接时，实例和登录受众构成*授权机构*。 |
| 目录（租户）ID | 可选 | 如果要构建仅用于组织的业务线应用程序（通常称为*单租户应用*程序），请指定此项。 |
| 重定向 URI | 可选 | 如果要构建 web 应用，则 `redirectUri` 指定标识提供者（Microsoft 标识平台）应在何处返回已颁发的安全令牌。 |

## <a name="initialize-msaljs-2x-apps"></a>初始化 MSAL.js 2.x 应用

通过使用[配置][msal-js-configuration]对象实例化[PublicClientApplication][msal-js-publicclientapplication]初始化 MSAL authentication 上下文。 所需的最低配置属性是 `clientID` 应用程序的，在 Azure 门户的应用程序注册的 "**概述**" 页上显示为**应用程序（客户端） ID** 。

下面是一个示例配置对象和实例化 `PublicClientApplication` ：

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

当应用程序使用重定向流时调用[handleRedirectPromise][msal-js-handleredirectpromise] 。 使用重定向流时， `handleRedirectPromise` 应在每次加载页面时运行。

承诺有三个可能的结果：

- `.then`被调用并且 `tokenResponse` 是 Truthy 的：该应用程序是从成功的重定向操作返回的。
- `.then`被调用并且 `tokenResponse` 为 falsey （ `null` ）：应用程序不是从重定向操作返回的。
- `.catch`被调用：应用程序从重定向操作返回，出现错误。

## <a name="initialize-msaljs-1x-apps"></a>初始化 MSAL.js 1.x 应用

通过使用配置对象实例化[UserAgentApplication][msal-js-useragentapplication] ，初始化 MSAL 1.x 身份验证上下文。 所需的最低配置属性是 `clientID` 应用程序的，在 Azure 门户的应用程序注册的 "**概述**" 页上显示为**应用程序（客户端） ID** 。

对于在 MSAL.js 1.2. x 或更早版本中具有重定向流（[loginRedirect][msal-js-loginredirect]和[acquireTokenRedirect][msal-js-acquiretokenredirect]）的身份验证方法，必须通过方法显式注册一个成功或错误的回调 `handleRedirectCallback()` 。 在 MSAL.js 1.2. x 和更早版本中，必须显式注册回调，因为重定向流不会像弹出体验这样的方法那样返回承诺。 在 MSAL.js 版本 1.3. x 和更高版本中，注册回调是*可选*的。

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

## <a name="single-instance-and-configuration"></a>单个实例和配置

MSAL.js 1.x 和2.x 都设计为分别具有或的单个实例和配置， `UserAgentApplication` `PublicClientApplication` 以表示单个身份验证上下文。

不建议使用或的多个实例， `UserAgentApplication` `PublicClientApplication` 因为它们会导致浏览器中的缓存条目和行为发生冲突。

## <a name="next-steps"></a>后续步骤

GitHub 上的此 MSAL.js 1.x 代码示例演示如何使用[配置][msal-js-configuration]对象实例化[PublicClientApplication][msal-js-publicclientapplication] ：

[Azure-示例/ms-标识-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_configuration_.html
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/classes/_useragentapplication_.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
