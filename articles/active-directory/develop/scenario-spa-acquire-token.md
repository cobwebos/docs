---
title: 获取令牌以调用 Web API（单页应用） - Microsoft 标识平台
description: 了解如何构建单页应用程序（获取用于调用 API 的令牌）
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: eeba01a609a1a21ed564c0b9cb78a28a4ad5c95a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882312"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>单页应用程序：获取用于调用 API 的令牌

使用 MSAL.js 获取 API 的令牌时，其模式是首先使用 `acquireTokenSilent` 方法来尝试无提示令牌请求。 调用此方法时，该库会首先检查浏览器存储中的缓存，看是否存在有效的令牌，在有的情况下会将其返回。 如果缓存中没有有效的令牌，则会从隐藏的 iframe 向 Azure Active Directory (Azure AD) 发送一个无提示令牌请求。 库也可以通过此方法来续订令牌。 有关 Azure AD 中的单一登录会话和令牌生存期值的详细信息，请参阅[令牌生存期](active-directory-configurable-token-lifetimes.md)。

可能会因某些原因（例如 Azure AD 会话过期，或者密码已更改）而导致以无提示方式向 Azure AD 请求令牌失败。 在这种情况下，可以调用某个交互方法（会提示用户）来获取令牌：

* [弹出窗口](#acquire-a-token-with-a-pop-up-window)，此方法使用 `acquireTokenPopup`
* [重定向](#acquire-a-token-with-a-redirect)，此方法使用 `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>在弹出窗口或重定向体验之间进行选择

 不能在应用程序中同时使用弹出窗口和重定向方法。 在弹出窗口和重定向体验之间进行的选择取决于应用程序流：

* 如果不希望用户在身份验证期间离开主应用程序页，建议使用弹出窗口方法。 由于身份验证重定向发生在弹出窗口中，系统会保留主应用程序的状态。

* 如果用户的浏览器约束或策略禁用了弹出窗口，则可使用重定向方法。 请对 Internet Explorer 浏览器使用重定向方法，因为 [Internet Explorer 上具有弹出窗口的已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

可以设置 API 作用域，在生成访问令牌请求时需要访问令牌包括这些作用域。 请注意，可能不会在访问令牌中授予所有请求的作用域。 具体取决于用户的许可。

## <a name="acquire-a-token-with-a-pop-up-window"></a>通过弹出窗口获取令牌

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下代码将前面描述的模式与弹出体验的方法结合起来：

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL Angular 包装器提供 HTTP 侦听器，后者会自动以无提示方式获取访问令牌并将其附加到针对 API 的 HTTP 请求。

可以在 `protectedResourceMap` 配置选项中指定 API 的作用域。 `MsalInterceptor` 将在自动获取令牌时请求这些作用域。

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

以无提示方式获取令牌时，不管是成功还是失败，MSAL Angular 都会提供回叫，可进行订阅。 此外还要记住取消订阅。

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

另外，也可通过显式方式使用获取令牌方法来获取令牌，如核心 MSAL.js 库中所述。

---

## <a name="acquire-a-token-with-a-redirect"></a>通过重定向获取令牌

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下模式如前文所述，但显示的是如何使用重定向方法以交互方式获取令牌。 你需注册重定向回叫，如前文所述。

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>请求可选声明

可以使用可选声明来实现以下目的：

- 在应用程序的令牌中包括其他声明。
- 更改 Azure AD 在令牌中返回的某些声明的行为。
- 添加和访问应用程序的自定义声明。

若要请求 `IdToken` 中的可选声明，可以将一个字符串化声明对象发送到 `AuthenticationParameters.ts` 类的 `claimsRequest` 字段。

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

如需了解详细信息，请参阅[可选声明](active-directory-optional-claims.md)。

# <a name="angular"></a>[Angular](#tab/angular)

此代码与前文所述相同。

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API](scenario-spa-call-api.md)
