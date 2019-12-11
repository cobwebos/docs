---
title: MSAL.js 与 ADAL.js 的差异 | Azure
titleSuffix: Microsoft identity platform
description: 了解适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 与适用于 JavaScript 的 Azure AD 身份验证库 (ADAL.js) 之间的差异，以及如何在两者之间做出选择。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d156ccd4b3f81081433962dcd939a91ab1bc7143
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963217"
---
# <a name="differences-between-msal-js-and-adal-js"></a>MSAL JS 与 ADAL JS 的差异

适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 与适用于 JavaScript 的 Azure AD 身份验证库 (ADAL.js) 用于对 Azure AD 实体进行身份验证，以及从 Azure AD 请求令牌。 截止目前，大多数开发人员都是通过 ADAL 来请求令牌，使用面向开发人员的 Azure AD (v1.0) 来对 Azure AD 标识（工作和学校帐户）进行身份验证。 现在，使用 MSAL.js 可以通过 Microsoft 标识平台 (v2.0) 对更广泛的 Microsoft 标识（Azure AD 标识和 Microsoft 帐户，以及通过 Azure AD B2C 使用的社交和本地帐户）进行身份验证。

本文介绍如何在适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 与适用于 JavaScript 的 Azure AD 身份验证库 (ADAL.js) 之间进行选择，并对这两个库做了比较。

## <a name="choosing-between-adaljs-and-msaljs"></a>在 ADAL.js 与 MSAL.js 之间做出选择

在大多数情况下都可以使用 Microsoft 标识平台和 MSAL.js，这是最新一代的 Microsoft 身份验证库。 使用 MSAL.js 可以获取通过 Azure AD（工作和学校帐户）、Microsoft（个人）帐户 (MSA) 或 Azure AD B2C 登录到应用程序的用户的令牌。

如果你已熟悉 v1.0 终结点（和 ADAL.js），请阅读 [v2.0 终结点有何不同？](active-directory-v2-compare.md)。

但是，如果应用程序需要使用早期版本的 [Active Directory 联合身份验证服务 (ADFS)](/windows-server/identity/active-directory-federation-services) 将用户登录，则你仍然需要使用 ADAL.js。

## <a name="key-differences-in-authentication-with-msaljs"></a>使用 MSAL.js 进行身份验证的重要差异

### <a name="core-api"></a>核心 API

* ADAL.js 使用 [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) 来表示应用程序实例通过颁发机构 URL 来与授权服务器或标识提供者建立的连接。 相比之下，MSAL.js API 是围绕用户代理客户端应用程序（某种形式的公共客户端应用程序，其中的客户端代码在 Web 浏览器等用户代理中执行）设计的。 它提供 `UserAgentApplication` 类来表示应用程序实例在授权服务器上的身份验证上下文。 有关更多详细信息，请参阅[使用 MSAL.js 初始化](msal-js-initializing-client-applications.md)。

* 在 ADAL.js 中，用于获取令牌的方法与 `AuthenticationContext` 中设置的单个颁发机构相关联。 在 MSAL.js 中，获取令牌请求可以采用其他颁发机构值，而不采用 `UserAgentApplication` 中设置的值。 这样，MSAL.js 便可以单独获取和缓存同一应用程序中多个租户和用户帐户的令牌。

* 在不提示用户的情况下以静默方式获取和续订令牌的方法在 ADAL.js 中名为 `acquireToken`。 在 MSAL.js 中，此方法名为 `acquireTokenSilent`，它更好地描述了其功能。

### <a name="authority-value-common"></a>颁发机构值 `common`

在 v1.0 中，使用 `https://login.microsoftonline.com/common` 颁发机构可让用户使用任何 Azure AD 帐户（适用于任何组织）登录。

在 v2.0 中，使用 `https://login.microsoftonline.com/common` 颁发机构可让用户使用任何 Azure AD 组织帐户或 Microsoft 个人帐户 (MSA) 登录。 若要限制为只能登录到 Azure AD 帐户（与 ADAL.js 中的行为类似），需要使用 `https://login.microsoftonline.com/organizations`。 有关详细信息，请参阅[使用 MSAL.js 初始化](msal-js-initializing-client-applications.md)中的 `authority` 配置选项。

### <a name="scopes-for-acquiring-tokens"></a>用于获取令牌的范围
* 身份验证请求中用于获取令牌的范围而不是资源参数

    v2.0 协议在请求中使用范围而不是资源。 换而言之，当应用程序需要使用某个资源（例如 MS Graph）的权限请求令牌时，传递给库方法的值的差异如下：

    v1.0：resource = https\://graph.microsoft.com

    v2.0：scope = https\://graph.microsoft.com/User.Read

    可以使用 API 的 URI 按“应用 ID URI/范围”格式请求任何资源 API 的范围。例如：https:\//mytenant.onmicrosoft.com/myapi/api.read

    仅在 MS 图形 API 中，作用域值 `user.read` 映射到 https：\//graph.microsoft.com/User.Read，并且可以互换使用。

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* 增量许可的动态范围。

    使用 v1.0 生成应用程序时，需要注册应用程序所需的完整权限集（静态范围），让用户在登录时许可这些权限。 在 v2.0 中，可以随时按需使用范围参数来请求权限。 这些范围称为动态范围。 这样，用户便可以提供对范围的增量许可。 因此，如果你最初只是希望用户登录到你的应用程序，而不需要任何类型的访问权限，则可以这样做。 如果后来需要读取用户的日历，则可以在 acquireToken 方法中请求日历范围，并获取用户的许可。 例如：

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* V1.0 API 的范围

    使用 MSAL.js 获取 V1.0 API 的令牌时，可以通过将 `.default` 作为范围追加到 API 的应用 ID URI，来请求该 API 中注册的所有静态范围。 例如：

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>后续步骤
有关详细信息，请阅读 [v1.0 与 v2.0 的比较](active-directory-v2-compare.md)。
