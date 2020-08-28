---
title: 配置单页应用 - Microsoft 标识平台 | Azure
description: 了解如何生成单页应用程序（应用的代码配置）
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: b42d720a425b92ec9002f7c2b9797a91f70dafe2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003017"
---
# <a name="single-page-application-code-configuration"></a>单页应用程序：代码配置

了解如何为单页应用程序 (SPA) 配置代码。

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>用于 Spa 的 MSAL 库和支持的身份验证流

Microsoft 标识平台提供以下适用于 JavaScript 的 Microsoft 身份验证库 ( # A0) 使用符合行业建议的安全做法，通过 PKCE 支持隐式流和授权代码流：

| MSAL 库 | 流向 | 说明 |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2.x) ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | 授权代码流 (PKCE)  | 用于任何使用 JavaScript 或 SPA 框架（如 Angular、Vue.js 和 React.js）构建的客户端 Web 应用的纯 JavaScript 库。 |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1.x) ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | 隐式流 | 用于任何使用 JavaScript 或 SPA 框架（如 Angular、Vue.js 和 React.js）构建的客户端 Web 应用的纯 JavaScript 库。 |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 隐式流 | 核心 MSAL.js 库的包装器，用于简化在使用 Angular 框架构建的单页应用中的使用。 |

## <a name="application-code-configuration"></a>应用程序代码配置

在 MSAL 库中，应用程序注册信息在库初始化期间作为配置传递。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

有关可配置选项的更多信息，请参阅[使用 MSAL.js 初始化应用程序](msal-js-initializing-client-applications.md)。

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [登录和注销](scenario-spa-sign-in.md)
