---
title: 结合使用 MSAL 与 Azure Active Directory B2CLearn |Microsoft
titleSuffix: Microsoft identity platform
description: 借助 Microsoft 身份验证库 (MSAL)，应用程序可以与 Azure AD B2C进行交互，并获取令牌来调用受保护的 Web API。 这些 Web API 可以是 Microsoft Graph、其他 Microsoft API、其他来源的 Web API 或你自己的 Web API。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f080c14cd0aa20bd312b4be8d9eacd8d901b7cef
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917023"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>使用 Microsoft 身份验证库与 Azure Active Directory B2C 交互

借助 Microsoft 身份验证库 (MSAL)，应用程序开发人员可以使用 [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) 通过社交和本地标识对用户进行身份验证。 Azure AD B2C 是一个标识管理服务。 使用该服务，可以在客户使用你的应用程序时，自定义和控制他们的注册和登录方式以及管理其个人资料。

通过 Azure AD B2C，你还可为应用程序的 UI 提供标志并进行自定义，以便为你的客户提供无缝体验。

本教程演示如何使用 MSAL 与 Azure AD B2C 进行交互。

## <a name="prerequisites"></a>必备组件

如果尚未创建自己的 [Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)，请立即创建一个。 还可以使用现有的 Azure AD B2C 租户。

## <a name="javascript"></a>JavaScript

下面的步骤演示了单页应用程序可以如何使用 Azure AD B2C 进行注册和登录，以及如何调用受保护的 Web API。

### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序

若要实现身份验证，首先需要注册你的应用程序。 请遵循[注册应用程序](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c)来执行详细步骤。

### <a name="step-2-download-the-sample-application"></a>步骤2：下载示例应用程序

下载示例作为 zip 文件，或从 GitHub 克隆：

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>步骤3：配置身份验证

1. 打开示例中的 index.html 文件。

1. 在注册应用程序时，用之前记录的客户端 ID 和密钥配置示例。 更改以下代码行，将值替换为你的目录和 API 的名称：

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

本教程中的[用户流](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies)的名称为 B2C_1_signupsignin1。 如果使用了不同的用户流名称，请将 authority 值设置为该名称。

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>步骤4：将应用程序配置为使用 `b2clogin.com`

可以使用 `b2clogin.com` 而不是 `login.microsoftonline.com` 作为重定向 URL。 设置标识提供者以进行注册和登录时，会在 Azure AD B2C 应用程序中执行此操作。

使用 `https://your-tenant-name.b2clogin.com/your-tenant-guid` 的上下文中的 `b2clogin.com` 具有以下效果：

- Microsoft 服务在 Cookie 标头占用的空间更少。
- URL 不再包括对 Microsoft 的引用。 例如，你的 Azure AD B2C 应用程序可能会引用 `login.microsoftonline.com`。

 若要使用 `b2clogin.com`，需要更新应用程序的配置。  

- 将 validateAuthority 属性设置为 `false`，以便可以使用 `b2clogin.com` 进行重定向。

以下示例展示了如何设置此属性：

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Azure AD B2C 应用程序可能在多个地方引用了 `login.microsoftonline.com`，例如用户流引用和令牌终结点。 请确保授权终结点、令牌终结点和证书颁发者都已更新为使用 `your-tenant-name.b2clogin.com`。

请按照[此 MSAL JavaScript 示例](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c)中关于如何使用适用于 JavaScript 的 MSAL 预览版 (MSAL.js) 的步骤进行操作。 此示例获取访问令牌并调用受 Azure AD B2C 保护的 API。

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

- [自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [用户界面自定义](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)