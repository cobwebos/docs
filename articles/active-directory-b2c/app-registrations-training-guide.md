---
title: Azure AD B2C 中的新应用注册体验
description: Azure AD B2C 中的新应用注册体验简介。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c89ed98d8100df270f09f1d2d1b621e71e326fe3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85386294"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的新应用注册体验

Azure Active Directory B2C （Azure AD B2C）的新**[应用注册](https://aka.ms/b2cappregistrations)** 体验现已正式发布。 如果更熟悉注册应用程序 Azure AD B2C 的**应用程序**体验，这里称为 "旧体验"，本指南将帮助你开始使用新体验。

## <a name="overview"></a>概述
以前，你必须使用旧的体验从你的应用程序的其余部分分别管理 Azure AD B2C 面向消费者的应用程序。 这意味着在 Azure 中的不同位置创建不同的应用程序。

新体验显示了所有 Azure AD B2C 应用注册并在一个位置 Azure AD 应用注册，并为管理这些注册提供了一致的方法。 从创建面向客户的应用程序到管理具有资源管理 Microsoft Graph 权限的应用程序，你只需了解一种执行操作的方法。

你可以通过导航到 Azure 门户中**Azure AD B2C**或**Azure Active Directory**服务的 Azure AD B2C 租户中的**应用注册**来达到新的体验。

Azure AD B2C 应用注册体验基于适用于任何 Azure AD 租户的常规[应用注册体验](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)，但针对 Azure AD B2C 租户进行了定制。

## <a name="whats-not-changing"></a>什么不更改？
- 你的应用程序和相关配置可以在新体验中按原样找到。 无需再次注册应用程序，应用程序的用户将不需要再次登录。 

> [!NOTE]
> 若要查看所有以前创建的应用程序，请导航到**应用注册**边栏选项卡，并选择 "**所有应用程序**" 选项卡。这会显示在旧版体验中创建的应用、新体验，以及在 Azure AD 服务中创建的应用。

## <a name="key-new-features"></a>关键新功能

-   **统一应用列表**显示了所有使用 Azure AD B2C 进行身份验证的应用程序，并将其 Azure AD 在一个方便的位置。 此外，你可以利用已可用于 Azure AD 应用程序的功能，包括 "**创建**日期"、"**证书 & 机密**状态" 和 "搜索栏" 等。

-   通过**组合应用注册**，你可以快速注册应用，无论该应用是面向客户的应用还是用于访问 Microsoft Graph 的应用。

- "**终结点**" 窗格可用于快速标识方案的相关终结点，其中包括 OpenID connect 配置、SAML 元数据、Microsoft Graph API 和[OAuth 2.0 用户流终结点](tokens-overview.md#endpoints)。 

- **Api 权限**和**公开 api**提供更广泛的范围、权限和同意管理。 你现在可以将 MS Graph 和 Azure AD Graph 权限分配给应用程序。

-   **所有者**和**清单**现在可用于使用 Azure AD B2C 进行身份验证的应用。 你可以[使用清单编辑器](../active-directory/develop/reference-app-manifest.md)为注册添加所有者并直接编辑应用程序属性。


## <a name="new-supported-account-types"></a>新支持的帐户类型

在新体验中，从以下选项中选择支持帐户类型：
- 仅限此组织目录中的帐户。
- 任何组织目录中的帐户（任何 Azure AD 目录–多租户）。
- 任何组织目录或任何标识提供者中的帐户。 用于对具有 Azure AD B2C 的用户进行身份验证。

若要了解不同的帐户类型，请选择 "**帮助我选择**创建体验"。 

在旧体验中，应用始终创建为面向客户的应用程序。 对于这些应用，将帐户类型设置为**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证**。
> [!NOTE]
> 若要对此应用程序的用户进行身份验证，需要此选项才能运行 Azure AD B2C 用户流。 了解[如何注册应用程序以用于用户流。](tutorial-register-applications.md)

你还可以使用此选项将 Azure AD B2C 用作 SAML 服务提供程序。 [了解详细信息](identity-provider-adfs2016-custom.md)。

## <a name="applications-for-devops-scenarios"></a>适用于 DevOps 方案的应用程序
可以使用其他帐户类型来创建应用，以管理 DevOps 方案，如使用 Microsoft Graph 上载标识体验框架策略或预配用户。 了解[如何注册 Microsoft Graph 应用程序来管理 Azure AD B2C 资源](microsoft-graph-get-started.md)。

你可能看不到所有 Microsoft Graph 权限，因为其中许多权限不适用于 Azure B2C 消费者用户。 [阅读有关使用 Microsoft Graph 管理用户的详细信息](manage-user-accounts-graph-api.md)。  

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>管理员许可和 offline_access + openid 范围  
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

需要**openid**范围以便 Azure AD B2C 可以将用户登录到应用。 为用户颁发刷新令牌需要**offline_access**范围。 这些作用域在默认情况下已添加并被授予管理员许可。 现在，你可以在创建过程中轻松地为这些作用域添加权限，方法是确保已选中 "**授予管理员同意 openid 并 offline_access 权限**" 选项。 否则，可以在现有应用的**API 权限**设置中使用管理员许可添加 Microsoft Graph 权限。

了解有关[权限和许可](../active-directory/develop/v2-permissions-and-consent.md)的详细信息。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平台/身份验证：回复 Url/重定向 Uri
在旧的体验中，各种平台类型在 "**属性**" 下作为 "web 应用/api" 的 "回复 url" 进行管理，而用于本机客户端的重定向 URI。 "Native clients" 也称为 "公用客户端"，并包括适用于 iOS、macOS、Android 和其他移动和桌面应用程序类型的应用。 

在新体验中，回复 Url 和重定向 Uri 均称为重定向 Uri，可以在应用的**身份验证**部分找到。 应用注册并不局限于 web 应用程序或本机应用程序。 可以通过注册相应的重定向 Uri，对所有这些平台类型使用相同的应用注册。 

重定向 Uri 需要与应用类型（"web" 或 "公共"，即 "移动" 和 "桌面"）相关联。 [了解重定向 Uri 的详细信息](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

**IOS/macOS**和**Android**平台是一种公共客户端。 它们提供了一种简单的方法来使用相应的重定向 Uri 来配置 iOS/macOS 或 Android 应用，以用于 MSAL。 了解有关[应用程序配置选项](../active-directory/develop/msal-client-applications.md)的详细信息。


## <a name="application-certificates--secrets"></a>& 机密的应用程序证书

在新体验中，使用 "**证书 & 机密**" 边栏选项卡来管理证书和机密，而不是**密钥**。 证书 & 密码允许应用程序在 web 可寻址位置（使用 HTTPS 方案）接收令牌时向身份验证服务标识自己。 在针对 Azure AD 进行身份验证时，建议使用证书，而不是客户端凭据的客户端密码。 证书不能用于对 Azure AD B2C 进行身份验证。


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>功能 Azure AD B2C 租户中不适用
以下 Azure AD 应用注册功能不适用于 Azure AD B2C 租户：
- **角色和管理员**-这需要 Azure AD Premium P1 或 P2 许可证，该许可证当前不可用于 Azure AD B2C。
- **品牌**-UI/UX 自定义在**公司品牌**体验或用户流中进行配置。 了解如何[自定义 Azure Active Directory B2C 中的用户界面](customize-ui-overview.md)。
- **发布服务器域验证**-你的应用程序在*onmicrosoft.com*上注册，后者不是已验证的域。 此外，发布者域主要用于授予用户同意，这不适用于用户身份验证 Azure AD B2C 应用。 [了解有关发布者域的详细信息](https://docs.microsoft.com/azure/active-directory/develop/howto-configure-publisher-domain)。
- **令牌配置**-令牌配置为用户流的一部分，而不是应用。
- **快速入门**体验目前不适用于 Azure AD B2C 租户。
- **集成助手**边栏选项卡当前不可用于 Azure AD B2C 租户。


## <a name="limitations"></a>限制
新体验具有以下限制：
- 目前，Azure AD B2C 不区分是否能够为隐式流发出访问令牌或 ID 令牌;如果在 "**身份验证**" 边栏选项卡中选择了 " **ID 令牌**" 选项，则这两种类型的令牌可用于隐式授权流。
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- UI 中不支持更改支持的帐户的值。 你需要使用应用程序清单，除非你要在 Azure AD 单租户和多租户之间进行切换。

## <a name="next-steps"></a>后续步骤

若要开始新的应用注册体验：
* 了解[如何注册 web 应用程序](tutorial-register-applications.md)。
* 了解[如何注册 WEB API](add-web-api-application.md)。
* 了解[如何注册 native client 应用程序](add-native-application.md)。
* 了解[如何注册 Microsoft Graph 应用程序来管理 Azure AD B2C 资源](microsoft-graph-get-started.md)。
* 了解[如何使用 Azure AD B2C 作为 SAML 服务提供程序。](identity-provider-adfs2016-custom.md)
* 了解[应用程序类型](application-types.md)。
