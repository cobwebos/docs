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
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eed0527b69dcaacd3a8cd0cf7cd178aa2aca3468
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89433902"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 的新应用注册体验

Azure Active Directory B2C (Azure AD B2C) 的新[应用注册](https://aka.ms/b2cappregistrations)体验现已正式发布。 如果你更熟悉 Azure AD B2C 应用程序注册过程的应用程序体验（此处称为“旧体验”），可借助本指南了解如何使用新体验。

## <a name="overview"></a>概述
以前，你需要使用旧体验将 Azure AD B2C 面向使用者的应用程序与其余的应用程序分开管理。 这意味着在 Azure 中，不同的位置可能有不同的应用创建体验。

新体验在一个位置显示所有 Azure AD B2C 应用注册和 Azure AD 应用注册，并提供一致的管理方法。 从创建面向使用者的应用到使用 Microsoft Graph 资源管理权限管理应用，你只需要掌握一种方法就能执行全部操作。

获得新体验的方法是从 Azure 门户中的 Azure AD B2C 或 Azure Active Directory 服务导航到 Azure AD B2C 租户中的“应用注册”  。

Azure AD B2C 应用注册体验在适用于所有 Azure AD 租户的常规[应用注册体验](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)的基础上构建，是专为 Azure AD B2C 租户设计的。

## <a name="whats-not-changing"></a>哪些未更改？
- 你的应用程序和相关配置在新体验中按原样保留。 无需再次注册应用程序，而且应用程序的用户无需再次登录。

> [!NOTE]
> 若要查看所有以前创建的应用程序，可导航到“应用注册”边栏选项卡，然后选择“所有应用程序”选项卡 。随即会显示曾在旧体验中、新体验中以及 Azure AD 服务中创建的所有应用。

## <a name="key-new-features"></a>主要的新功能

-   **统一的应用列表**会在一个便利位置显示所有使用 Azure AD B2C 和 Azure AD 进行身份验证的应用程序。 此外，可以利用已可用于 Azure AD 应用程序的功能，包括“创建时间”、“证书和密码”状态、搜索栏等等 。

-   **组合的应用注册**可帮助你快速注册应用，无论该应用是面向客户的还是用于访问 Microsoft Graph 的应用。

- **终结点**窗格有助于快速识别方案的相关终结点，包括 OpenID connect 配置、SAML 元数据、Microsoft Graph API 和 [OAuth 2.0 用户流终结点](tokens-overview.md#endpoints)。

- **API 权限**和**公开 API** 提供更广泛的范围、权限和同意管理。 现在还可将 MS Graph 和 Azure AD Graph 权限分配给应用。

-   **所有者**和**清单**现可用于使用 Azure AD B2C 进行身份验证的应用。 可为注册添加所有者，并[使用清单编辑器](../active-directory/develop/reference-app-manifest.md)直接编辑应用程序属性。


## <a name="new-supported-account-types"></a>新的受支持的帐户类型

在新体验中，从以下选项中选择支持帐户类型：
- 仅此组织目录中的帐户。
- 任何组织目录中的帐户 (任何 Azure AD 目录–多租户) 。
- 任何组织目录或任何标识提供者中的帐户。 可用于通过 Azure AD B2C 对用户进行身份验证。

若要了解不同的帐户类型，请选择创建体验中的“帮我选择”。

在旧体验中，创建的应用始终是面向客户的应用程序。 对于这些应用，将账户类型设置为“任何组织目录中或任何标识提供者中的帐户 **。** 用于通过 Azure AD B2C 对用户进行身份验证”。
> [!NOTE]
> 若要对此应用程序的用户进行身份验证，需要此选项才能运行 Azure AD B2C 用户流。 了解[如何注册应用程序以便能使用用户流。](tutorial-register-applications.md)

你还可以使用此选项将 Azure AD B2C 用作 SAML 服务提供程序。 [了解详细信息](identity-provider-adfs2016-custom.md)。

## <a name="applications-for-devops-scenarios"></a>适用于 DevOps 方案的应用程序
可使用其他帐户类型来创建用于管理 DevOps 方案（如使用 Microsoft Graph 上传 Identity Experience Framework 策略或预配用户）的应用。 了解[如何注册 Microsoft Graph 应用程序来管理 Azure AD B2C 资源](microsoft-graph-get-started.md)。

你可能看不到所有 Microsoft Graph 权限，因为其中许多权限不适用于 Azure B2C 使用者用户。 [详细了解如何使用 Microsoft Graph 管理用户](manage-user-accounts-graph-api.md)。

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>管理员同意和 offline_access+openid 范围
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

Openid 范围是 Azure AD B2C 成功将用户登录到应用所必需的。 若要为用户颁发刷新令牌，则需要 offline_access 范围。 这些范围在默认情况下已添加并获得管理员同意。 现在，可以在创建过程中轻松地为这些范围添加权限，方法是确保已选中“向 openid 和 offline_access 权限授予管理员同意”选项。 另外，可以在现有应用的“API 权限”设置中，在已获得管理员同意的情况下，添加 Microsoft Graph 权限。

详细了解[权限和同意](../active-directory/develop/v2-permissions-and-consent.md)。

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>平台/身份验证：回复 URL/重定向 URI
在旧体验中，各种平台类型在“属性”下作为 Web 应用/API 的回复 URL 和本机客户端的重定向 URI 进行管理。 “本机客户端”也称为“公共客户端”，包括 iOS、macOS、Android 应用以及其他移动和桌面应用程序类型的应用。

在新体验中，回复 URL 和重定向 URI 均称为重定向 URI，可在应用的“身份验证”部分中查看。 应用注册不局限于 Web 应用 或本机应用。 注册相应的重定向 URI，即可对所有这些平台类型使用同一个应用注册。

重定向 URI 需要与应用类型（web 或公共，即移动和桌面）相关联。 [详细了解重定向 URI](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

iOS/macOS 和 Android 平台是公共客户端 。 它们提供了一种简单的方法来配置 iOS/macOS 或 Android 应用（使用相应的重定向 URI），使其能与 MSAL 配合使用。 详细了解[应用程序配置选项](../active-directory/develop/msal-client-applications.md)。


## <a name="application-certificates--secrets"></a>应用程序证书和密码

在新体验中，可使用“证书和密码”边栏选项卡（而不是“密钥”）来管理证书和密码 。 借助证书和密码，当在 Web 可寻址位置接收令牌时（使用 HTTPS 方案），应用程序能够向身份验证服务证明身份。 在进行 Azure AD 身份验证时，建议为客户端凭据方案使用证书而不是客户端密码。 不能使用证书对 Azure AD B2C 进行身份验证。


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>不适用于 Azure AD B2C 租户的功能
以下 Azure AD 应用注册功能不适用或不可用于 Azure AD B2C 租户：
- **角色和管理员** - 这需要 Azure AD Premium P1 或 P2 许可证，但该许可证目前不可用于 Azure AD B2C。
- **品牌** - UI/UX 自定义在“公司品牌”体验中配置或作为用户流的一部分进行配置。 了解如何[自定义 Azure Active Directory B2C 中的用户界面](customize-ui-overview.md)。
- **发布服务器域验证** -你的应用程序在 *onmicrosoft.com*上注册，后者不是已验证的域。 此外，发布服务器域主要用于授予用户同意，这对于 Azure AD B2C 应用进行用户身份验证并不适用。 [详细了解发布服务器域](https://docs.microsoft.com/azure/active-directory/develop/howto-configure-publisher-domain)。
- **令牌配置** - 令牌配置为用户流而不是应用的一部分。
- **快速入门**体验目前不适用于 Azure AD B2C 租户。
- **集成助手**边栏选项卡目前不适用于 Azure AD B2C 租户。


## <a name="limitations"></a>限制
新体验具有以下限制：
- 目前，Azure AD B2C 并不区分可以为隐式流颁发访问令牌还是 ID 令牌；如果在“身份验证”边栏选项卡中选择“ID 令牌”选项，则这两种类型的令牌都可用于隐式授权流 。
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- 不支持在 UI 中更改受支持帐户的值。 除非在 Azure AD 单租户与多租户之间切换，否则需要使用应用清单。

## <a name="next-steps"></a>后续步骤

若要开始使用新的应用注册体验：
* 了解[如何注册 Web 应用程序](tutorial-register-applications.md)。
* 了解[如何注册 Web API](add-web-api-application.md)。
* 了解[如何注册本机客户端应用程序](add-native-application.md)。
* 了解[如何注册 Microsoft Graph 应用程序来管理 Azure AD B2C 资源](microsoft-graph-get-started.md)。
* 了解 [如何使用 Azure AD B2C 作为 SAML 服务提供程序。](identity-provider-adfs2016-custom.md)
* 了解[应用程序类型](application-types.md)。
