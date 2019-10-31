---
title: 单一登录（适用于 JavaScript 的 Microsoft 身份验证库）
titleSuffix: Microsoft identity platform
description: 了解如何使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）构建单一登录体验。
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
ms.openlocfilehash: da39b8435acdd11108a945c6bac5147dc8b6ad50
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150574"
---
# <a name="single-sign-on-with-msaljs"></a>使用 MSAL.js 的单一登录

通过单一登录（SSO），用户可以输入自己的凭据一次登录并建立会话，该会话可在多个应用程序之间重复使用，而无需再次进行身份验证。 这为用户提供了无缝体验，并减少了凭据的重复提示。

Azure AD 通过在用户首次进行身份验证时设置会话 cookie 来向应用程序提供 SSO 功能。 MSAL 库允许应用程序以几种方式利用此功能。

## <a name="sso-between-browser-tabs"></a>浏览器选项卡之间的 SSO

当应用程序在多个选项卡中打开并且首次在一个选项卡上登录用户时，用户也会在其他选项卡上登录，而不会出现提示。 MSAL 在浏览器中缓存用户的 ID 标记 `localStorage`，并在其他打开的选项卡上将用户登录到应用程序。

默认情况下，MSAL 使用不允许在选项卡之间共享会话的 `sessionStorage`。 若要获取选项卡之间的 SSO，请确保将 MSAL 中的 `cacheLocation` 设置为 `localStorage`，如下所示。

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

用户进行身份验证时，会在浏览器中的 Azure AD 域上设置会话 cookie。 MSAL 依赖此会话 cookie 为用户提供不同应用程序之间的 SSO。 MSAL 还会在每个应用程序域的浏览器存储中缓存用户的 ID 令牌和访问令牌。 因此，不同情况下 SSO 行为会有所不同：  

### <a name="applications-on-the-same-domain"></a>同一域中的应用程序

当应用程序托管在同一个域中时，用户可以登录到应用一次，然后在不提示的情况下对其他应用进行身份验证。 MSAL 利用为域中的用户缓存的令牌来提供 SSO。

### <a name="applications-on-different-domain"></a>不同域上的应用程序

当应用程序托管在不同的域中时，域 A 中缓存的令牌无法通过域 B 中的 MSAL 访问。

这意味着，当用户登录到域 A 时，将导航到域 B 上的应用程序，这些用户将被重定向或提示 Azure AD 页面。 由于 Azure AD 仍具有用户会话 cookie，因此它将以用户身份登录，并且不需要重新输入凭据。 如果用户在与 Azure AD 的会话中有多个用户帐户，则系统将提示用户选择用于登录的相关帐户。

### <a name="automatically-select-account-on-azure-ad"></a>自动选择 Azure AD 上的帐户

在某些情况下，应用程序有权访问用户的身份验证上下文，并且想要在登录多个帐户时避免 Azure AD 帐户选择提示。  可以通过几种不同的方式完成此操作：

**使用会话 ID （SID）**

会话 ID 是可以在 ID 令牌中配置的[可选声明](active-directory-optional-claims.md)。 此声明允许应用程序独立于用户的帐户名或用户名识别用户的 Azure AD 会话。 可以将请求参数中的 SID 传递到 `acquireTokenSilent` 调用。 这将允许 Azure AD 绕过帐户选择。 SID 绑定到会话 cookie，而不会跨浏览器上下文。

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
> SID 只能与 MSAL 中 `acquireTokenSilent` 调用发出的无提示身份验证请求一起使用。
可在[此处](active-directory-optional-claims.md)找到配置应用程序清单中的可选声明的步骤。

**使用登录提示**

如果未配置 SID 声明，或需要在交互式身份验证调用中跳过帐户选择提示，可以通过在请求参数中提供 `login_hint`，并在 MSAL 交互方法中提供 `extraQueryParameters` @no （可选） `domain_hint` 来执行此操作。__t_3_、`loginRedirect`、`acquireTokenPopup` 和 `acquireTokenRedirect`）。 例如：

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

可以通过读取在用户的 ID 令牌中返回的声明来获取 login_hint 和 domain_hint 的值。

* 应将**loginHint**设置为 ID 令牌中的 `preferred_username` 声明。

* 仅当使用/common 颁发机构时，才需要**domain_hint** 。 域提示由租户 ID （tid）决定。  如果 ID 令牌中的 `tid` 声明 `9188040d-6c67-4c5b-b112-36a304b66dad` 则使用者。 否则为组织。

请参阅[此处](v2-oauth2-implicit-grant-flow.md)，了解有关登录提示和域提示的值的详细信息。

> [!Note]
> 不能同时传递 SID 和 login_hint。 这将导致错误响应。

## <a name="sso-without-msaljs-login"></a>不带 MSAL 登录名的 SSO

按照设计，MSAL 要求在获取 Api 令牌之前调用登录方法来建立用户上下文。 由于登录方法是交互式的，因此用户会看到一条提示。

在某些情况下，应用程序可以通过在另一应用程序中启动的身份验证访问经过身份验证的用户的上下文或 ID 令牌，并希望利用 SSO 获取令牌，而无需首先通过 MSAL 登录。

这种情况的一个示例是：用户登录到一个父 web 应用程序，该应用程序作为另一个作为加载项或插件运行的 JavaScript 应用程序。

在此方案中，可按如下所示实现 SSO 体验：

如果可用（或 `login_hint`，`domain_hint`）作为 MSAL `acquireTokenSilent` 调用的请求参数，请将 `sid` 传递给，如下所示：

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>将 ADAL 中的 SSO 升级到 MSAL

MSAL 为 Azure AD 身份验证方案引入了针对 ADAL 的功能奇偶校验。 若要使从 ADAL 到 MSAL 的迁移变得很简单，并避免提示用户重新登录，库将在 ADAL 缓存中读取代表用户会话的 ID 令牌，并在 MSAL 中无缝地登录用户。  

若要利用从 ADAL 进行更新时的单一登录（SSO）行为，需确保库使用 `localStorage` 来缓存令牌。 将 `cacheLocation` 设置为在初始化时在 MSAL 和 ADAL 配置中 `localStorage`，如下所示：


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

完成此配置后，MSAL 将能够读取 ADAL 中经过身份验证的用户的缓存状态，并使用该状态在 MSAL 中提供 SSO。

## <a name="next-steps"></a>后续步骤

详细了解 Azure AD 中的[单一登录会话和令牌生存期](active-directory-configurable-token-lifetimes.md)值。
