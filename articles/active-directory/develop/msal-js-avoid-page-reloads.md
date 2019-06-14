---
title: 避免页面重新加载 （适用于 JavaScript 的 Microsoft 身份验证库） |Azure
description: 了解如何避免页面重新加载时获取和续订令牌以无提示方式使用 JavaScript (MSAL.js) Microsoft 身份验证库。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66420463"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>避免页面重新加载时获取和续订令牌以无提示方式使用 MSAL.js
Microsoft Authentication Library for JavaScript (MSAL.js) 使用隐藏`iframe`元素来获取和续订令牌以无提示方式在后台。 Azure AD 返回的标记返回到令牌请求中指定的已注册 redirect_uri （默认情况下为应用程序的根页面）。 由于 302 响应，这会导致对应的 HTML`redirect_uri`中加载`iframe`。 通常应用的`redirect_uri`是根页，并且这会使它重新加载。

在其他情况下，如果导航到应用程序的根页要求身份验证，则可能会导致为嵌套`iframe`元素或`X-Frame-Options: deny`错误。

MSAL.js 无法关闭 Azure AD 颁发的 302，并且需要处理返回的令牌，因为它不能阻止`redirect_uri`从中加载`iframe`。

若要避免再次重新加载整个应用程序或其他错误引起的这样，请执行以下解决方法。

## <a name="specify-different-html-for-the-iframe"></a>指定不同 HTML iframe

设置`redirect_uri`到一个简单的页面，不需要身份验证配置的属性。 您必须确保它符合`redirect_uri`在 Azure 门户中注册。 这不会影响用户的登录体验，如 MSAL 将保存的起始页，当用户开始登录过程并完成登录后，重定向回准确位置。

## <a name="initialization-in-your-main-app-file"></a>主应用程序文件中的初始化

如果您的应用程序在结构上，只有一个中央 Javascript 文件，用于定义应用程序的初始化、 路由以及其他内容，则可以有条件地加载基于应用程序中的加载是否在应用模块`iframe`与否。 例如：

在 AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

在 Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>后续步骤
详细了解如何[生成单页面应用程序 (SPA)](scenario-spa-overview.md)使用 MSAL.js。