---
title: 结合使用 MSAL 与 Azure Active Directory B2CLearn |Microsoft
titleSuffix: Microsoft identity platform
description: 使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL），应用程序可以与 Azure AD B2C 和获取令牌，以调用受保护的 web Api。 这些 Web API 可以是 Microsoft Graph、其他 Microsoft API、其他来源的 Web API 或你自己的 Web API。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534476"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>使用适用于 JavaScript 的 Microsoft 身份验证库来处理 Azure Active Directory B2C

使用[适用于 javascript 的 Microsoft 身份验证库（MSAL）](https://github.com/AzureAD/microsoft-authentication-library-for-js) ，javascript 开发人员可以使用[Azure Active Directory B2C （Azure AD B2C）](https://docs.microsoft.com/azure/active-directory-b2c/)对具有社交和本地标识的用户进行身份验证。 通过使用 Azure AD B2C 作为标识管理服务，你可以自定义和控制客户使用你的应用程序时，如何注册、登录和管理他们的配置文件。

Azure AD B2C 还可以在身份验证过程中对应用程序的 UI 进行标记和自定义，以便为客户提供无缝体验。

本文演示如何使用 MSAL 处理 Azure AD B2C 并汇总你应注意的要点。 有关完整的讨论和教程，请参阅[Azure AD B2C 文档](https://docs.microsoft.com/azure/active-directory-b2c/overview)。

## <a name="prerequisites"></a>先决条件

如果尚未创建自己的[Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)，请立即创建一个 Azure AD B2C （如果已有），请立即创建一个。

此演示包含以下两个部分：

- 如何保护 web API。
- 如何注册单页应用程序进行身份验证并调用*该*web API。

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> 目前，节点的 MSAL 仍在开发中（请参阅[路线图](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)）。 同时，我们建议使用[passport azure ad](https://github.com/AzureAD/passport-azure-ad)，这是 Microsoft 开发和支持的 node.js 的身份验证库。

以下步骤演示了**WEB API**如何使用 Azure AD B2C 来保护自身，并将所选作用域公开给客户端应用程序。

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

若要通过 Azure AD B2C 来保护 web API，首先需要注册它。 请遵循[注册应用程序](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications)来执行详细步骤。

### <a name="step-2-download-the-sample-application"></a>步骤2：下载示例应用程序

下载示例作为 zip 文件，或从 GitHub 克隆：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>步骤3：配置身份验证

1. 在示例中打开 `config.js` 文件。

2. 在注册应用程序时，用之前获取的应用程序凭据配置示例。 通过将值替换为 clientID、host、tenantId 和策略名称的名称来更改以下代码行。

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

有关详细信息，请查看此[NODE.JS B2C WEB API 示例](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)。

---

## <a name="javascript-spa"></a>JavaScript SPA

以下步骤演示如何使用 Azure AD B2C **single-page application**来注册、登录和调用受保护的 web API。

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

若要实现身份验证，首先需要注册你的应用程序。 请遵循[注册应用程序](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications)来执行详细步骤。

### <a name="step-2-download-the-sample-application"></a>步骤2：下载示例应用程序

下载示例作为 zip 文件，或从 GitHub 克隆：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>步骤3：配置身份验证

配置应用程序时有两个兴趣点：

- 配置 API 终结点和公开范围
- 配置身份验证参数和令牌作用域

1. 在示例中打开 `apiConfig.js` 文件。

2. 在注册 web API 时，用之前获得的参数配置示例。 通过将值替换为 web API 的地址和公开的作用域，更改以下代码行。

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. 在示例中打开 `authConfig.js` 文件。

4. 配置示例，其中包含之前在注册单页应用程序时获取的参数。 通过将值替换为 ClientId、颁发机构元数据和令牌请求范围来更改以下代码行。

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

有关详细信息，请查看此[JAVASCRIPT B2C 单页应用程序示例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)。

---

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：
- [用户流](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX 自定义](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
