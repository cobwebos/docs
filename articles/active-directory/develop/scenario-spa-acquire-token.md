---
title: 单页应用程序（获取用于调用 API 的令牌）-Microsoft 标识平台
description: 了解如何生成单页面应用程序（获取用于调用 API 的令牌）
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
ms.openlocfilehash: a0f1140d00671a706ce8839a73023dfad64d4663
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764757"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>单页应用程序：获取用于调用 API 的令牌

使用 MSAL 获取 Api 的令牌的模式是首先尝试使用 `acquireTokenSilent` 方法进行无提示令牌请求。 调用此方法时，库将首先检查浏览器存储中的缓存，以查看是否存在有效的令牌并将其返回。 如果缓存中没有有效的令牌，则会将无提示令牌请求从隐藏的 iframe 发送到 Azure Active Directory （Azure AD）。 此方法还允许库续订令牌。 有关 Azure AD 中的单一登录会话和令牌生存期值的详细信息，请参阅[令牌生存期](active-directory-configurable-token-lifetimes.md)。

Azure AD 的无提示令牌请求可能失败，原因如下： Azure AD 会话过期或密码更改。 在这种情况下，你可以调用其中一个交互式方法（将提示用户）获取令牌：

* [弹出式窗口](#acquire-a-token-with-a-pop-up-window)，方法是使用 `acquireTokenPopup`
* 使用 `acquireTokenRedirect`[重定向](#acquire-a-token-with-a-redirect)

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>在弹出或重定向体验之间进行选择

 不能在应用程序中同时使用弹出和重定向方法。 弹出或重定向体验之间的选择取决于你的应用程序流：

* 如果你不希望用户在身份验证过程中离开主应用程序页面，建议使用弹出方法。 由于身份验证重定向发生在弹出窗口中，因此将保留主应用程序的状态。

* 如果用户具有禁用了弹出式窗口的浏览器约束或策略，则可以使用重定向方法。 在 Internet Explorer 浏览器中使用重定向方法，因为[Internet explorer 上的弹出窗口存在已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

你可以设置要在生成访问令牌请求时使用的访问令牌所需的 API 作用域。 请注意，可能不会在访问令牌中授予所有请求的作用域。 这取决于用户的同意。

## <a name="acquire-a-token-with-a-pop-up-window"></a>使用弹出窗口获取令牌

### <a name="javascript"></a>JavaScript

下面的代码将前面所述的模式与用于弹出体验的方法相结合：

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

### <a name="angular"></a>Angular

MSAL 角包装提供 HTTP 侦听器，该侦听器会自动获取访问令牌并将其附加到 Api 的 HTTP 请求。

可以在 `protectedResourceMap` 配置选项中指定 Api 的作用域。 `MsalInterceptor` 将在自动获取令牌时请求这些作用域。

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

对于无提示令牌获取的成功和失败，MSAL 角提供可订阅的回调。 还必须记住取消订阅。

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

另外，还可以使用 MSAL 中所述的获取令牌方法，显式获取令牌。

## <a name="acquire-a-token-with-a-redirect"></a>使用重定向获取令牌

### <a name="javascript"></a>JavaScript

下面的模式如前文所述，但使用重定向方法显示以交互方式获取令牌。 如前文所述，需要注册重定向回拨。

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

- 在应用程序的令牌中包含附加声明。
- 更改 Azure AD 在令牌中返回的某些声明的行为。
- 添加和访问应用程序的自定义声明。 

若要在 `IdToken`中请求可选声明，可以将字符串化声明对象发送到 `AuthenticationParameters.ts` 类的 `claimsRequest` 字段。

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
若要了解详细信息，请参阅[可选声明](active-directory-optional-claims.md)。


### <a name="angular"></a>Angular

此代码与前面所述相同。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [调用 web API](scenario-spa-call-api.md)
