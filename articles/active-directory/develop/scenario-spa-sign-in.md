---
title: 单页应用登录 & 注销-Microsoft 标识平台 |Microsoft
description: 了解如何生成单页面应用程序（登录）
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3d7afdc634649ca3baf5f43d5e2376fb5d8b05d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423648"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>单页应用程序：登录和注销

了解如何在单页面应用程序的代码中添加登录。

你需要经过身份验证的用户上下文，然后才能获取令牌来访问应用程序中的 Api。 可以通过两种方式将用户登录到 MSAL 中的应用程序：

* [弹出式窗口](#sign-in-with-a-pop-up-window)，方法是使用 `loginPopup` 方法
* 使用 `loginRedirect` 方法进行[重定向](#sign-in-with-redirect)

你还可以选择传递你在登录时需要用户同意的 Api 的作用域。

> [!NOTE]
> 如果你的应用程序已有权访问经过身份验证的用户上下文或 ID 令牌，则可以跳过该登录步骤并直接获取令牌。 有关详细信息，请参阅[没有 MSAL 登录名的 SSO](msal-js-sso.md#sso-without-msaljs-login)。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>在弹出或重定向体验之间进行选择

不能在应用程序中同时使用弹出和重定向方法。 弹出或重定向体验之间的选择取决于你的应用程序流：

* 如果你不希望用户在身份验证过程中从主应用程序页面移开，则建议你执行弹出方法。 由于身份验证重定向发生在弹出窗口中，因此将保留主应用程序的状态。

* 如果用户具有禁用弹出窗口的浏览器约束或策略，则可以使用重定向方法。 在 Internet Explorer 浏览器中使用重定向方法，因为[Internet explorer 上的弹出窗口存在已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

## <a name="sign-in-with-a-pop-up-window"></a>使用弹出窗口登录

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

可以通过将 `MsalGuard` 添加到路由定义，来保护应用程序中的特定路由。 此防护将调用方法，以便在访问该路由时进行登录。

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

对于弹出窗口体验，启用 `popUp` 配置选项。 你还可以传递需要许可的作用域，如下所示：

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>重定向的登录

### <a name="javascript"></a>JavaScript

重定向方法不会返回承诺，因为这是从主应用中移开的。 若要处理并访问返回的令牌，需要在调用重定向方法之前注册成功和错误回调。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

此处的代码与有关使用弹出窗口登录的部分中所述的代码相同。 默认流为 "重定向"。

> [!NOTE]
> ID 令牌不包含许可范围并仅代表经过身份验证的用户。 许可范围在访问令牌中返回，你将在下一步中获取该令牌。

## <a name="sign-out"></a>注销

MSAL 库提供了一个 `logout` 方法，该方法可在浏览器存储中清除缓存并向 Azure Active Directory （Azure AD）发送注销请求。 注销后，默认情况下，库将重定向回应用程序起始页。

您可以通过设置 `postLogoutRedirectUri`来配置在注销后应重定向到的 URI。 此 URI 还应在应用程序注册中注册为注销 URI。

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [正在获取应用的令牌](scenario-spa-acquire-token.md)
