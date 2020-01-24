---
title: 避免页面重载（MSAL） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何在使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）以无提示方式获取和续订令牌时避免页面重新加载。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e68798861d5799a4314bd9cd9b2eeeadb926a90f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696140"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>当使用 MSAL 以静默方式获取和续订令牌时避免页面重新加载
适用于 JavaScript 的 Microsoft 身份验证库（MSAL）使用隐藏 `iframe` 元素在后台无提示地获取和续订令牌。 Azure AD 返回令牌请求中指定的注册 redirect_uri 的令牌（默认情况下，这是应用的根页面）。 由于响应为302，因此会生成与 `iframe`中加载的 `redirect_uri` 对应的 HTML。 通常，应用的 `redirect_uri` 是根页面，这将导致重新加载。

在其他情况下，如果导航到应用程序的根页需要身份验证，则可能会导致嵌套 `iframe` 元素或 `X-Frame-Options: deny` 错误。

由于 MSAL 无法消除由 Azure AD 颁发的302，因此处理返回的令牌需要此，它无法阻止 `redirect_uri` 加载到 `iframe`中。

若要避免再次加载整个应用或导致此错误的其他错误，请遵循以下解决方法。

## <a name="specify-different-html-for-the-iframe"></a>为 iframe 指定不同的 HTML

将配置上的 `redirect_uri` 属性设置为简单页面，这不需要进行身份验证。 必须确保它与 Azure 门户中注册的 `redirect_uri` 匹配。 这不会影响用户的登录体验，因为 MSAL 会在用户开始登录过程时保存起始页，并在登录完成后重定向回确切位置。

## <a name="initialization-in-your-main-app-file"></a>在主应用文件中初始化

如果你的应用程序的结构化，并且有一个用于定义应用程序初始化、路由和其他内容的中央 Javascript 文件，则你可以根据应用程序是否正在 `iframe` 加载来有条件地加载应用模块。 例如：

In AngularJS： node.js

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

角： app.config

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
详细了解如何使用 MSAL[生成单页应用程序（SPA）](scenario-spa-overview.md) 。