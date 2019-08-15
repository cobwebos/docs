---
title: 单页应用程序（登录）-Microsoft 标识平台
description: 了解如何生成单页应用程序（登录）
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
ms.openlocfilehash: 7bf614a5523e78fc72918db973ef8d738a171fff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031791"
---
# <a name="single-page-application---sign-in"></a>单页应用程序 - 登录

了解如何将登录添加到单页应用程序的代码中。

在应用程序中获取用于访问 API 的令牌之前，需要经身份验证的用户上下文。 可以在 MSAL.js 中以两种方式将用户登录到应用程序：

* 使用 `loginPopup`方法 [通过弹出窗口登录](#sign-in-with-a-pop-up-window)
* 使用 `loginRedirect`方法 [通过重定向登录](#sign-in-with-redirect)

也可选择传递 API 的作用域，该作用域需在用户登录时获得用户许可。

> [!NOTE]
> 如果应用程序已经可以访问经身份验证的用户上下文或 ID 令牌，则可跳过登录步骤，直接获取令牌。 有关更多详细信息，请参阅[在不使用 msal.js 登录名的情况下进行 SSO](msal-js-sso.md#sso-without-msaljs-login)。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>在弹出窗口或重定向体验之间进行选择

不能在应用程序中将弹出窗口和重定向方法组合在一起使用。 在弹出窗口和重定向体验之间进行的选择取决于应用程序流。

* 如果不希望用户在身份验证期间从主应用程序页以导航的方式离开，建议使用弹出窗口方法。 由于身份验证重定向发生在弹出窗口中，因此会保留主应用程序的状态。

* 在某些情况下，可能需要使用重定向方法。 如果应用程序用户的浏览器约束或策略禁用了弹出窗口，则可使用重定向方法。 请对 Internet Explorer 浏览器使用重定向方法，因为在处理弹出窗口时存在某些 [Internet Explorer 已知问题](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)。

## <a name="sign-in-with-a-pop-up-window"></a>通过弹出窗口登录

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

有了 MSAL Angular 包装器，只需将 `MsalGuard` 添加到路由定义即可确保应用程序中特定路由的安全。 访问该路由时，此防护会调用用于登录的方法。

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

对于弹出窗口体验，请启用 `popUp` 配置选项。 也可传递需要许可的作用域，如下所示：

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

## <a name="sign-in-with-redirect"></a>使用重定向登录

### <a name="javascript"></a>JavaScript

重定向方法不会返回承诺，因为已经通过导航从主应用离开。 若要处理并访问返回的令牌，需要在调用重定向方法之前注册成功和错误回叫。

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

此处的代码与上述代码（位于使用弹出窗口登录的部分）相同。 默认流为重定向。

> [!NOTE]
> ID 令牌不包含许可的作用域，仅代表经身份验证的用户。 许可的作用域在访问令牌中返回，该令牌会在下一步获取。

## <a name="sign-out"></a>注销

MSAL 库提供一个 `logout` 方法，该方法会清除浏览器中的缓存并将注销请求发送到 Azure AD。 在注销后，它会默认重定向回应用程序启动页。

可以通过设置 `postLogoutRedirectUri` 来配置此 URI（在注销后应该重定向到此 URI）。 还应该在应用程序注册中将此 URI 注册为“注销 URI”。

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
> [获取应用的令牌](scenario-spa-acquire-token.md)
