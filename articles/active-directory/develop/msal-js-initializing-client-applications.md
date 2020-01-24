---
title: 初始化 MSAL 客户端应用 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）初始化客户端应用程序。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e350f4fc3d40b45a1308e1edd9331dc7f71399c5
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696123"
---
# <a name="initialize-client-applications-using-msaljs"></a>使用 MSAL 初始化客户端应用程序
本文介绍如何使用用户代理应用程序的实例初始化适用于 JavaScript 的 Microsoft 身份验证库（MSAL）。 用户代理应用程序是公用客户端应用程序的一种形式，其中客户端代码在用户代理（如 web 浏览器）中执行。 这些客户端不存储机密，因为浏览器上下文可公开访问。 若要了解有关客户端应用程序类型和应用程序配置选项的详细信息，请阅读[概述](msal-client-applications.md)。

## <a name="prerequisites"></a>必备组件
在初始化应用程序之前，首先需要将[其注册到 Azure 门户](scenario-spa-app-registration.md)，以便可以将应用程序与 Microsoft 标识平台集成。 注册后，你可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID （表示应用程序的 GUID 的字符串）
- 应用程序的标识提供程序 URL （名为实例）和登录受众。 这两个参数统称为颁发机构。
- 如果你只是为你的组织编写业务线应用程序（也称为单租户应用程序），则租户 ID。
- 对于 web 应用，你还必须设置 redirectUri，其中标识提供程序将使用安全令牌返回到你的应用程序。

## <a name="initializing-applications"></a>初始化应用程序

可以在普通 JavaScript/Typescript 应用程序中使用 MSAL。 通过使用配置对象实例化 `UserAgentApplication` 初始化 MSAL authentication 上下文。 用于初始化 MSAL 的最低要求配置是应用程序的 clientID，你应该从应用程序注册门户获取此配置。

对于具有重定向流（`loginRedirect` 和 `acquireTokenRedirect`）的身份验证方法，你将需要通过 `handleRedirectCallback()` 方法显式注册一个成功或错误的回调。 这是必需的，因为重定向流不会将承诺作为带有弹出体验的方法返回。

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL 设计为具有 `UserAgentApplication` 的单个实例和配置，以表示单个身份验证上下文。 不建议使用多个实例，因为它们会导致浏览器中的缓存条目和行为发生冲突。

## <a name="configuration-options"></a>配置选项

MSAL 具有如下所示的配置对象，该对象提供一组可用于创建 `UserAgentApplication`实例的可配置选项。

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

下面是当前在 config 对象中支持的一组可配置选项的总数：

- **clientID**：必填。 应用程序的 clientID，你应该从应用程序注册门户获取此应用程序。

- **颁发机构**：可选。 一个 URL，指示 MSAL 可以从其请求令牌的目录。 默认值为 `https://login.microsoftonline.com/common`。
    * 在 Azure AD 中，它的格式为 https://&lt;instance&gt;/&lt;受众&gt;，其中 &lt;实例&gt; 是标识提供者域（例如 `https://login.microsoftonline.com`），&lt;受众&gt; 是表示登录受众的标识符。 这可以是以下值：
        * `https://login.microsoftonline.com/<tenant>`-租户是与租户关联的域（如 contoso.onmicrosoft.com）或 GUID，表示仅用于登录特定组织用户的目录的 `TenantID` 属性。
        * `https://login.microsoftonline.com/common`-用于通过工作和学校帐户或 Microsoft 个人帐户来登录用户。
        * `https://login.microsoftonline.com/organizations/`-用于使用工作和学校帐户登录用户。
        * `https://login.microsoftonline.com/consumers/`-用于仅使用个人 Microsoft 帐户（live）登录用户。
    * 在 Azure AD B2C 中，它的格式为 `https://<instance>/tfp/<tenant>/<policyName>/`，其中实例是 Azure AD B2C 域，即 {b2clogin}. .com 是 Azure AD B2C 租户的名称，即 "onmicrosoft"，policyName 是要应用的 B2C 策略的名称，是要应用的 B2C 策略的名称。


- **validateAuthority**：可选。  验证令牌的颁发者。 默认为 `true`。 对于 B2C 应用程序，由于授权值已知并且每个策略可能不同，因此，验证机构验证将不起作用，并且必须设置为 `false`。

- **redirectUri**：可选。  应用的重定向 URI，应用可通过此 URI 发送和接收身份验证响应。 它必须与在门户中注册的重定向 Uri 之一完全匹配。 默认为 `window.location.href`。

- **postLogoutRedirectUri**：可选。  注销后，将用户重定向到 `postLogoutRedirectUri`。默认值为 `redirectUri`。

- **navigateToLoginRequestUrl**：可选。 在登录后关闭默认导航到起始页的功能。 默认值为 true。 这仅用于重定向流。

- **cacheLocation**：可选。  将浏览器存储设置为 `localStorage` 或 `sessionStorage`。 默认为 `sessionStorage`。

- **storeAuthStateInCookie**：可选。  此标志是在 MSAL v 0.2.2 中引入的，用于解决 Microsoft Internet Explorer 和 Microsoft Edge 上的[身份验证循环问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones)。 启用标志 `storeAuthStateInCookie` 为 true 以利用此修补程序。 启用此功能后，MSAL 将存储在浏览器 cookie 中验证身份验证流所需的身份验证请求状态。 默认情况下，此标志设置为 `false`。

- **记录器**：可选。  具有回调实例的记录器对象，开发人员可以提供该对象以自定义方式使用和发布日志。 有关传递记录器对象的详细信息，请参阅[通过 msal 进行日志记录](msal-logging.md)。

- **loadFrameTimeout**：可选。  应将 Azure AD 令牌续订响应之前处于非活动状态的时间（以毫秒为单位）视为超时。默认值为6秒。

- **tokenRenewalOffsetSeconds**：可选。 设置在到期之前续订令牌所需的偏移量的毫秒数。 默认值为300毫秒。

- **navigateFrameWait**：可选。 将隐藏 iframe 导航到目标之前的等待时间设置的毫秒数。 默认值为500毫秒。

这些仅适用于从 MSAL 角包装库中向下传递：
- **unprotectedResources**：可选。  未受保护的资源的 Uri 数组。 MSAL 不会将令牌附加到具有这些 URI 的传出请求。 默认为 `null`。

- **protectedResourceMap**：可选。  这就是将资源映射到 MSAL 使用的作用域，以便在 web API 调用中自动连接访问令牌。 为资源获取单个访问令牌。 因此, 可以按如下所示映射特定的资源路径: "https://graph.microsoft.com/v1.0/me" {""、["用户读取"]} 或资源的应用程序 URL: {"https://graph.microsoft.com/"、["用户读取"、"邮件发送"]}。 这是 CORS 调用所必需的。 默认为 `null`。
