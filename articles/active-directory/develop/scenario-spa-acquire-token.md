---
title: 单页应用程序（获取用于调用 API 的令牌）- Microsoft 标识平台
description: 了解如何构建单页应用程序（获取用于调用 API 的令牌）
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135714"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>单页应用程序 - 获取用于调用 API 的令牌

使用 MSAL.js 获取 API 的令牌时，其模式是首先使用 `acquireTokenSilent` 方法来尝试无提示令牌请求。 调用此方法时，该库会首先检查浏览器存储中的缓存，看是否存在有效的令牌，在有的情况下会将其返回。 当缓存中没有有效的令牌时，它会从隐藏的 iframe 向 Azure Active Directory (Azure AD) 发送一个无提示令牌请求。 库也可以通过此方法来续订令牌。 有关 Azure AD 中的单一登录会话和令牌生存期值的详细信息, 请参阅[令牌生存期](active-directory-configurable-token-lifetimes.md)。

可能会因某些原因（例如 Azure AD 会话过期，或者密码已更改）而导致以无提示方式向 Azure AD 请求令牌失败。 在这种情况下，可以调用某个交互方法（会提示用户）来获取令牌。

* 使用 `acquireTokenPopup` [通过弹出窗口获取令牌](#acquire-token-with-a-pop-up-window)
* 使用 `acquireTokenRedirect` [通过重定向方式获取令牌](#acquire-token-with-redirect)

**在弹出窗口或重定向体验之间进行选择**

 不能在应用程序中将弹出窗口和重定向方法组合在一起使用。 在弹出窗口和重定向体验之间进行的选择取决于应用程序流。

* 如果不希望用户在身份验证期间从主应用程序页以导航的方式离开，建议使用弹出窗口方法。 由于身份验证重定向发生在弹出窗口中，因此会保留主应用程序的状态。

* 在某些情况下，可能需要使用重定向方法。 如果应用程序用户的浏览器约束或策略禁用了弹出窗口，则可使用重定向方法。 另外还建议对 Internet Explorer 浏览器使用重定向方法，因为在处理弹出窗口时存在某些 [Internet Explorer 已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

可以设置 API 作用域，在生成访问令牌请求时需要访问令牌包括这些作用域。 请注意, 可能不会在访问令牌中授予所有请求的作用域, 并取决于用户的同意。

## <a name="acquire-token-with-a-pop-up-window"></a>通过弹出窗口获取令牌

### <a name="javascript"></a>JavaScript

此模式如上所述，使用适用于弹出窗口体验的方法：

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
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

### <a name="angular"></a>Angular

MSAL 角包装提供添加 HTTP 侦听器的便利, 这将自动自动获取访问令牌, 并将其附加到 Api 的 HTTP 请求。

可以在`protectedResourceMap` config 选项中指定 api 的作用域, MsalInterceptor 将在自动获取令牌时请求此作用域。

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

以无提示方式获取令牌时，不管是成功还是失败，MSAL Angular 都会提供供你订阅的回叫。 此外还要记住取消订阅。

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

另外，也可通过显式方式使用获取令牌方法来获取令牌，如核心 MSAL.js 库中所述。

## <a name="acquire-token-with-redirect"></a>通过重定向方式获取令牌

### <a name="javascript"></a>JavaScript

此模式如上所述，但显示的是如何使用重定向方法以交互方式获取令牌。 你将需要注册如上所述回拨, 如上文所述。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-for-optional-claims"></a>请求提供可选声明
可以在应用中请求可选声明, 以指定要在应用程序的令牌中包含的其他声明。 为了请求 id_token 中的可选声明, 可以将字符串化声明对象发送到 AuthenticationParameters 类的 claimsRequest 字段。

可以使用可选声明来实现以下目的:

- 在应用程序的令牌中包含附加声明。
- 更改 Azure AD 在令牌中返回的某些声明的行为。
- 添加和访问应用程序的自定义声明。


### <a name="javascript"></a>JavaScript
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
若要详细了解可选声明, 请签出[可选声明](active-directory-optional-claims.md)


### <a name="angular"></a>Angular

这与上述方法相同。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 Web API](scenario-spa-call-api.md)
