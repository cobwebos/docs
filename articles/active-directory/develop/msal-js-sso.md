---
title: '单一登录 ( # A0) |Microsoft'
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 构建单一登录体验。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84690772"
---
# <a name="single-sign-on-with-msaljs"></a>使用 MSAL.js 进行单一登录

借助单一登录 (SSO)，用户只需输入其凭据一次即可登录，并建立可在多个应用程序中重复使用的会话，而无需再次进行身份验证。 这样可为用户提供无缝的体验，并减少重复提示用户输入凭据的次数。

当用户首次进行身份验证时，Azure AD 将设置会话 Cookie，以此在应用程序中提供 SSO 功能。 MSAL.js 库允许应用程序以多种方式利用此功能。

## <a name="sso-between-browser-tabs"></a>浏览器标签页之间的 SSO

如果应用程序已在多个标签页中打开，当你首次在一个标签页中将用户登录时，该用户也会在其他标签页中登录，而不会看到提示。 MSAL.js 会在浏览器 `localStorage` 中缓存用户的 ID 令牌，并在其他打开的标签页中将用户登录到应用程序。

默认情况下，MSAL.js 使用 `sessionStorage`（不允许在标签页之间共享会话）。 若要在标签页之间实现 SSO，请务必将 MSAL.js 中的 `cacheLocation` 设置为 `localStorage`，如下所示。

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>应用之间的 SSO

当用户进行身份验证时，将在浏览器中的 Azure AD 域上设置会话 Cookie。 MSAL.js 依赖于此会话 Cookie 来为不同应用程序之间的用户提供 SSO。 MSAL.js 还会在每个应用程序域的浏览器存储中缓存用户的 ID 令牌和访问令牌。 因此，SSO 行为根据不同的情况而异：  

### <a name="applications-on-the-same-domain"></a>同一域中的应用程序

如果应用程序托管在同一个域中，则用户可以登录到应用一次，然后可在其他应用中完成身份验证，而不会看到提示。 MSAL.js 利用域中为用户缓存的令牌来提供 SSO。

### <a name="applications-on-different-domain"></a>不同域中的应用程序

如果应用程序托管在不同的域中，则域 B 中的 MSAL.js 无法访问域 A 中缓存的令牌。

这意味着，当已登录到域 A 的用户导航到域 B 中的应用程序时，他们将被重定向，或者在 Azure AD 页面中看到提示。 由于 Azure AD 仍包含用户会话 Cookie，它会将用户登录，而用户不需要重新输入凭据。 如果用户在 Azure AD 的会话中包含多个用户帐户，则系统会提示用户选择相关的帐户用于登录。

### <a name="automatically-select-account-on-azure-ad"></a>自动在 Azure AD 上选择帐户

在某些情况下，应用程序有权访问用户的身份验证上下文，并希望避免在登录了多个帐户时出现 Azure AD 帐户选择提示。  可通过多种不同的方式实现此目的：

**使用会话 ID (SID)**

会话 ID 是可以在 ID 令牌中配置的[可选声明](active-directory-optional-claims.md)。 不管用户的帐户名或用户名是什么，应用程序都可以使用此声明识别用户的 Azure AD 会话。 可将请求参数中的 SID 传递给 `acquireTokenSilent` 调用。 这样，Azure AD 就可以绕过帐户选择。 SID 绑定到会话 Cookie，不会跨越浏览器上下文。

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID 只能与 MSAL.js 中的 `acquireTokenSilent` 调用发出的静默身份验证请求配合使用。
可在[此处](active-directory-optional-claims.md)找到有关在应用程序清单中配置可选声明的步骤。

**使用登录提示**

如果未配置 SID 声明或者需要在交互式身份验证调用中绕过帐户选择提示，可以在请求参数中提供 `login_hint`，并选择性地在 MSAL.js 交互式方法（`loginPopup`、`loginRedirect`、`acquireTokenPopup` 和 `acquireTokenRedirect`）中提供 `extraQueryParameters` 形式的 `domain_hint`。 例如：

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

可以通过读取用户 ID 令牌中返回的声明来获取 login_hint 和 domain_hint 的值。

* **loginHint** 应设置为 ID 令牌中的 `preferred_username` 声明。

* 仅当使用 /common 颁发机构时，才需要传递 **domain_hint**。 域提示按租户 ID (TID) 确定。  如果 ID 令牌中的 `tid` 声明为 `9188040d-6c67-4c5b-b112-36a304b66dad`，则域提示为使用者。 否则为组织。

有关登录提示和域提示的值的详细信息，请参阅[此文](v2-oauth2-implicit-grant-flow.md)。

> [!Note]
> 不能同时传递 SID 和 login_hint。 这会导致错误响应。

## <a name="sso-without-msaljs-login"></a>不使用 MSAL.js 登录名进行 SSO

根据设计，在获取 API 的令牌之前，MSAL.js 需要调用某个登录方法来建立用户上下文。 由于登录方法是交互式的，用户会看到提示。

在某些情况下，应用程序有权通过另一应用程序中启动的身份验证访问经过身份验证的用户的上下文或 ID 令牌，并希望在不先通过 MSAL.js 登录的情况下，利用 SSO 获取令牌。

此方案的示例如下：用户已登录到父 Web 应用程序，该应用程序托管作为加载项或插件运行的另一个 JavaScript 应用程序。

可按如下所述实现此方案中的 SSO 体验：

将可用的 `sid`（或 `login_hint` 和可选的 `domain_hint`）作为请求参数传递给 MSAL.js `acquireTokenSilent` 调用，如下所示：

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>从 ADAL.js 更新到 MSAL.js 之后的 SSO

对于 Azure AD 身份验证方案，MSAL.js 引入了与 ADAL.js 的功能奇偶一致性。 为了轻松从 ADAL.js 迁移到 MSAL.js 并避免再次提示用户登录，库会在 ADAL.js 缓存中读取代表用户会话的 ID 令牌，并在 MSAL.js 中无缝地将用户登录。  

若要在从 ADAL.js 更新时利用单一登录 (SSO) 行为，需确保库使用 `localStorage` 来缓存令牌。 在初始化时，将 MSAL.js 和 ADAL.js 配置中的 `cacheLocation` 设置为 `localStorage`，如下所示：


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

完成配置后，MSAL.js 可以在 ADAL.js 中读取经过身份验证的用户的缓存状态，并使用该状态在 MSAL.js 中提供 SSO。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD 中的 [单一登录会话和令牌生存期](active-directory-configurable-token-lifetimes.md) 值。
