---
title: 避免页面重载（适用于 JavaScript 的 Microsoft 身份验证库）| Azure
description: 了解如何在使用适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 以无提示方式获取和续订令牌时避免页面重载。
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
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c382c78cf631def74272768b78ee489e49820d04
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532832"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>在使用 MSAL.js 以无提示方式获取和续订令牌时避免页面重载
适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 使用隐藏的 `iframe` 元素在后台中以无提示方式获取和续订令牌。 Azure AD 将令牌返回到在令牌请求中指定的已注册 redirect_uri（默认情况下，这是应用的根页面）。 由于响应是 302，因此会生成 HTML，与在 `iframe` 中加载的 `redirect_uri` 相对应。 通常情况下，应用的 `redirect_uri` 是根页面，这样会导致它重载。

在其他情况下，如果导航到应用的根页面需要身份验证，则可能导致嵌套的 `iframe` 元素或 `X-Frame-Options: deny` 错误。

由于 MSAL.js 不能消除 Azure AD 发出的 302 且是处理返回的令牌所必需的，因此它不能防止 `redirect_uri` 在 `iframe` 中加载。

若要避免整个应用再次重载，或者避免因此导致的其他错误，请执行以下解决方法。

## <a name="specify-different-html-for-the-iframe"></a>为 iframe 指定不同的 HTML

将配置中的 `redirect_uri` 属性设置为一个简单页面，这不需要身份验证。 必须确保它与在 Azure 门户中注册的 `redirect_uri` 匹配。 这不会影响用户的登录体验，因为 MSAL 会在用户开始登录过程时保存启动页，并在登录完成后重定向回确切的位置。

## <a name="initialization-in-your-main-app-file"></a>主应用文件中的初始化

如果在设置应用的结构时，使用一个中心 Javascript 文件来定义应用的初始化、路由和其他事项，则可根据应用是否在 `iframe` 中加载来有条件性地加载应用模块。 例如：

在 AngularJS 中：app.js

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

在 Angular 中：app.module.ts

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

MsalComponent：

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
详细了解如何使用 MSAL.js [生成单页应用程序 (SPA)](scenario-spa-overview.md)。