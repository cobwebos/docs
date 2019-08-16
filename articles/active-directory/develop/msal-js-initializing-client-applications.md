---
title: 初始化客户端应用程序（适用于 JavaScript 的 Microsoft 身份验证库）| Azure
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 初始化客户端应用程序。
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
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7df2f9aa56e22bd4060c823b02900fa914a0fd7f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532794"
---
# <a name="initialize-client-applications-using-msaljs"></a>使用 MSAL.js 初始化客户端应用程序
本文介绍如何使用用户代理应用程序的实例初始化适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js)。 该用户代理应用程序是某种形式的公共客户端应用程序，其中的客户端代码在 Web 浏览器等用户代理中执行。 这些客户端不存储机密，因为浏览器上下文可公开访问。 若要详细了解客户端应用程序类型和应用程序配置选项，请阅读[概述](msal-client-applications.md)。

## <a name="prerequisites"></a>先决条件
在初始化应用程序之前，首先需要[使用 Azure 门户将其注册](scenario-spa-app-registration.md)，使应用能够与 Microsoft 标识平台集成。 注册后，可能需要以下信息（可在 Azure 门户中找到）：

- 客户端 ID（表示应用程序 GUID 的字符串）
- 标识提供者 URL（为实例命名）和应用程序的登录受众。 这两个参数统称为颁发机构。
- 租户 ID：如果你编写的业务线应用程序（也称为单租户应用程序）专用于自己的组织。
- 对于 Web 应用，还必须设置 redirectUri，标识提供者将通过此 URI 向应用程序返回安全令牌。

## <a name="initializing-applications"></a>初始化应用程序

在单纯的 JavaScript/Typescript 应用程序中，可按如下所示使用 MSAL.js。 通过使用配置对象实例化 `UserAgentApplication` 来初始化 MSAL 身份验证上下文。 用于初始化 MSAL 的最低要求配置是应用程序的 clientID, 你应该从应用程序注册门户获取此配置。

对于使用重定向流的身份验证方法（`loginRedirect` 和 `acquireTokenRedirect`），需要通过 `handleRedirectCallback()` 方法显式注册一个返回成功或错误结果的回调。 之所以需要这样做，是因为重定向流不会像弹出窗口体验中的方法那样返回约定。

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
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

MSAL.js 在设计上采用 `UserAgentApplication` 的单个实例和配置表示单个身份验证上下文。 不建议使用多个实例，因为它们会导致浏览器中出现有冲突的缓存条目和行为。

## <a name="configuration-options"></a>配置选项

MSAL.js 包含如下所示的配置对象，该对象提供可用于创建 `UserAgentApplication` 实例的可配置选项分组。

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

下面是配置对象中当前支持的整个可配置选项集：

- **clientID**：必需。 应用程序的 clientID, 你应该从应用程序注册门户获取此应用程序。

- **authority**：可选。 一个 URL，表示 MSAL 可从中请求令牌的目录。 默认值为 `https://login.microsoftonline.com/common`。
    * 在 Azure AD 中，该 URL 采用 https://&lt;instance&gt;/&lt;audience&gt; 格式，其中，&lt;instance&gt; 是标识提供者域（例如 `https://login.microsoftonline.com`），&lt;audience&gt; 是表示登录受众的标识符。 可设置为以下值：
        * `https://login.microsoftonline.com/<tenant>`-租户是与租户关联的域, 如 contoso.onmicrosoft.com 或 GUID, 它表示`TenantID`仅用于登录特定组织用户的目录的属性。
        * `https://login.microsoftonline.com/common`-用于使用工作和学校帐户或 Microsoft 个人帐户来登录用户。
        * `https://login.microsoftonline.com/organizations/` - 用于通过工作和学校帐户将用户登录。
        * `https://login.microsoftonline.com/consumers/`-用于仅使用个人 Microsoft 帐户 (live) 登录用户。
    * 在 Azure AD B2C 中，此 URL 采用 `https://<instance>/tfp/<tenant>/<policyName>/` 格式，其中，instance 是 Azure AD B2C 域，tenant 是 Azure AD B2C 租户的名称，policyName 是要应用的 B2C 策略的名称。


- **validateAuthority**：可选。  验证令牌的颁发者。 默认为 `true`。 对于 B2C 应用程序，由于颁发机构值是已知的，并且根据不同的策略而异，因此，颁发机构验证不起作用，必须设置为 `false`。

- **redirectUri**：可选。  应用的重定向 URI，应用可通过此 URI 发送和接收身份验证响应。 其必须与门户中注册的其中一个重定向 URI 完全匹配，否则必须经过 URL 编码。 默认为 `window.location.href`。

- **postLogoutRedirectUri**：可选。  注销后将用户重定向到 `postLogoutRedirectUri`。默认值为 `redirectUri`。

- **navigateToLoginRequestUrl**：可选。 登录后可以关闭默认导航到起始页的行为。 默认值为 true。 这仅适用于重定向流。

- **cacheLocation**：可选。  将浏览器存储设置为 `localStorage` 或 `sessionStorage`。 默认值为 `sessionStorage`。

- **storeAuthStateInCookie**：可选。  此标志已在 MSAL.js v0.2.2 中引入，修复了 Microsoft Internet Explorer 和 Microsoft Edge 中的[身份验证循环问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones)。 将标志 `storeAuthStateInCookie` 设置为 true（启用）可以利用此修复措施。 启用此标志后，MSAL.js 将存储身份验证请求状态，在浏览器 Cookie 中验证身份验证流时需要此状态。 此标志默认设置为 `false`。

- **logger**：可选。  一个包含回调实例的记录器对象。开发人员可以提供该实例以自定义方式使用和发布日志。 有关传递记录器对象的详细信息，请参阅[使用 MSAL.js 进行日志记录](msal-logging.md)。

- **loadFrameTimeout**：可选。  处于非活动状态的毫秒数，如果 Azure AD 在此时限内未返回令牌续订响应，则被视为超时。默认值为 6 秒。

- **tokenRenewalOffsetSeconds**：可选。 在令牌过期之前续订令牌的偏差时限，以毫秒为单位。 默认值为 300 毫秒。

这些值只适合从 MSAL Angular 包装器库向下传递：
- **unprotectedResources**：可选。  不受保护资源的 URI 数组。 MSAL 不会将令牌附加到包含这些 URI 的传出请求。 默认为 `null`。

- **protectedResourceMap**：可选。  资源到范围的映射，MSAL 在 Web API 调用中使用这种映射自动附加访问令牌。 将获取资源的单个访问令牌。 因此, 可以按如下所示映射特定的资源路径: https://graph.microsoft.com/v1.0/me {""、["用户读取"]} 或资源的应用程序 URL: {"https://graph.microsoft.com/"、["用户读取"、"邮件发送"]}。 对于 CORS 调用，必须进行这种映射。 默认为 `null`。
