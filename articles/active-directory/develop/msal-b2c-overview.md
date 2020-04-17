---
title: 将 MSAL 与 Azure 活动目录 B2C 学习一起使用 |蔚蓝
titleSuffix: Microsoft identity platform
description: 适用于 JavaScript 的 Microsoft 身份验证库 （MSAL.js） 使应用程序能够使用 Azure AD B2C 并获取令牌来调用安全的 Web API。 这些 Web API 可以是 Microsoft Graph、其他 Microsoft API、其他来源的 Web API 或你自己的 Web API。
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534476"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>使用 JavaScript 的 Microsoft 身份验证库使用 Azure 活动目录 B2C

[适用于 JavaScript （MSAL.js） 的 Microsoft 身份验证库](https://github.com/AzureAD/microsoft-authentication-library-for-js)使 JavaScript 开发人员能够使用[Azure 活动目录 B2C （Azure AD B2C）](https://docs.microsoft.com/azure/active-directory-b2c/)对具有社交和本地标识的用户进行身份验证。 通过使用 Azure AD B2C 作为身份管理服务，您可以自定义和控制客户在使用应用程序时注册、登录和管理其配置文件的方式。

Azure AD B2C 还使您能够在身份验证过程中对应用程序的 UI 进行品牌认证和自定义，以便为客户提供无缝体验。

本文演示如何使用 MSAL.js 使用 Azure AD B2C，并总结了您应该注意的要点。 有关完整的讨论和教程，请参阅[Azure AD B2C 文档](https://docs.microsoft.com/azure/active-directory-b2c/overview)。

## <a name="prerequisites"></a>先决条件

如果尚未创建自己的[Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)，请从现在创建租户开始（如果已有 Azure AD B2C 租户，也可以使用现有的 Azure AD B2C 租户）。

此演示包含两个部分：

- 如何保护 Web API。
- 如何注册单页应用程序以验证和调用*该*Web API。

## <a name="nodejs-web-api"></a>Node.js Web API

> [!NOTE]
> 此时，Node 的 MSAL.js 仍处于开发阶段（请参阅[路线图](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)）。 同时，我们建议使用由 Microsoft 开发和支持的 Node.js 的[身份验证库"护照 azure-ad"。](https://github.com/AzureAD/passport-azure-ad)

以下步骤演示了 Web **API**如何使用 Azure AD B2C 来保护自己并将选定的作用域公开给客户端应用程序。

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

要使用 Azure AD B2C 保护 Web API，首先需要注册它。 请遵循[注册应用程序](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications)来执行详细步骤。

### <a name="step-2-download-the-sample-application"></a>第 2 步：下载示例应用程序

下载示例作为 zip 文件，或从 GitHub 克隆：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>第 3 步：配置身份验证

1. 在示例中打开 `config.js` 文件。

2. 使用之前注册应用程序时获得的应用程序凭据配置示例。 通过将值替换为客户端 ID、主机、租户 Id 和策略名称的名称来更改以下代码行。

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

有关详细信息，请查看此[Node.js B2C Web API 示例](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)。

---

## <a name="javascript-spa"></a>JavaScript SPA

以下步骤演示**了单页应用程序**如何使用 Azure AD B2C 注册、登录和调用受保护的 Web API。

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

若要实现身份验证，首先需要注册你的应用程序。 请遵循[注册应用程序](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications)来执行详细步骤。

### <a name="step-2-download-the-sample-application"></a>第 2 步：下载示例应用程序

下载示例作为 zip 文件，或从 GitHub 克隆：

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>第 3 步：配置身份验证

配置应用程序有两点兴趣：

- 配置 API 终结点和公开作用域
- 配置身份验证参数和令牌作用域

1. 在示例中打开 `apiConfig.js` 文件。

2. 使用注册 Web API 时之前获得的参数配置示例。 通过将值替换为 Web API 地址和公开作用域，更改以下代码行。

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. 在示例中打开 `authConfig.js` 文件。

4. 使用之前在注册单页应用程序时获得的参数配置示例。 通过将值替换为 ClientId、权限元数据和令牌请求作用域来更改以下代码行。

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

有关详细信息，请查看此[JavaScript B2C 单页应用程序示例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)。

---

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：
- [用户流](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX 自定义](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)
