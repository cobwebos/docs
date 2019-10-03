---
title: 为多租户应用程序选择适当的联合身份验证协议
description: 独立软件供应商与 Azure Active Directory 集成的指南
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c5975b57b6f960badf747e33deb238adf260199
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967212"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>为多租户应用程序选择适当的联合身份验证协议

当你开发软件即服务 (SaaS) 应用程序时, 必须选择最能满足你和你的客户需求的联合身份验证协议。 此决定基于开发平台, 并且你希望将与客户的 Office 365 和 Azure AD 生态系统内可用的数据集成。

请参阅[适用于 SSO 集成](what-is-single-sign-on.md)Azure Active Directory 的协议的完整列表。
下表比较 
* 开放式身份验证 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* 安全断言标记语言 (SAML)
* Web Services 联合身份验证 (WSFed)

| 能力| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| 基于 Web 的单一登录| √| √ |
| 基于 Web 的单一注销| √| √ |
| 基于移动设备的单一登录| √| √* |
| 基于移动的单一登录| √| √* |
| 移动应用程序的条件性访问策略| √| X |
| 移动应用程序的无缝 MFA 体验| √| X |
| 访问 Microsoft Graph| √| X |

\* 但 Microsoft 不提供示例或指导。

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 和 Open ID Connect

OAuth 2.0 是一种[行业标准](https://oauth.net/2/)的授权协议。 OIDC (OpenID Connect) 是在 Oath 2.0 协议基础之上构建的[行业标准](https://openid.net/connect/)标识身份验证层。

### <a name="benefits"></a>优点

Microsoft 建议使用 OIDC/OAuth 2.0, 因为它们具有内置于协议的身份验证和授权。 对于 SAML, 还必须实现授权。

利用这些协议中的固有授权, 应用程序可以通过 Microsoft Graph API 访问丰富的用户和组织数据并与之集成。

使用 OAuth 2.0 和 OIDC 可简化客户在为应用程序采用 SSO 时的最终用户体验。 您可以轻松地定义必要的权限集, 然后这些权限集会自动代表管理员或最终用户同意。

此外, 使用这些协议, 你的客户可以使用条件性访问和 MFA 策略来控制对应用程序的访问。 Microsoft[跨多个技术平台](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)提供库和代码示例, 以帮助你进行开发。  

### <a name="implementation"></a>实现

向 Microsoft 标识注册应用程序, 这是一个 OAuth 2.0 提供程序。 然后, 你还可以向要与之集成的任何其他标识提供程序注册基于 OAuth 2.0 的应用程序。 

有关如何注册应用程序并为 SSO 实现 web 应用程序实现这些协议的信息, 请参阅[使用 OpenID connect 和 Azure Active Directory 向 web 应用程序授予访问权限](../develop/sample-v2-code.md)。  有关如何在移动应用中为 SSO 实现这些协议的信息, 请参阅以下内容: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [通用 Windows 平台](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 和 WSFed

安全断言标记语言 (SAML) 通常用于 web 应用程序。 有关概述, 请参阅[Azure 如何使用 SAML 协议](../develop/active-directory-saml-protocol-reference.md)。 

Web Services 联合身份验证 (WSFed) 是一种[行业标准](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html), 通常用于使用 .net 平台开发的 web 应用程序。

### <a name="benefits"></a>优点

SAML 2.0 是一种成熟的标准, 大多数技术平台支持 SAML 2.0 的开源库。 可以为客户提供管理界面来配置 SAML SSO。 它们可以配置 SAML SSO for Microsoft Azure AD 和任何其他支持 SAML 2 的标识提供者

### <a name="trade-offs"></a>权衡

将 SAML 2.0 或 WSFed 协议用于移动应用程序时, 某些条件访问策略 (包括多重身份验证 (MFA)) 将具有降级的体验。 此外, 如果你想要访问 Microsoft Graph, 则需要通过 OAuth 2.0 实现授权才能生成所需的令牌。 

### <a name="implementation"></a>实现

Microsoft 不提供 SAML 实现的库或推荐特定的库。 有许多开源库可用。

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 和使用 Microsoft Graph Rest API 

Microsoft Graph 是跨所有 Microsoft 365 的数据结构, 包括 Office 365、Windows 10 和企业移动性和安全性, 以及 Dynamics 365 等其他产品。 这包括诸如用户、组、日历、邮件、文件等实体的核心架构, 这些架构可提高用户的工作效率。 Microsoft Graph 为开发人员提供了三个接口, 这是基于 REST 的 API、Microsoft Graph 数据连接和连接器, 使开发人员能够将他们自己的数据添加到 Microsoft Graph 中。  

使用任何上述 SSO 协议, 你的应用程序都可通过 Microsoft Graph REST API 访问丰富的数据。 这样, 你的客户就可以在 Microsoft 365 投资中获得更多价值。 例如, 你的应用程序可以调用 Microsoft Graph API, 以便与你的客户的 Office 365 实例和应用程序中的用户 Microsoft Office 和 SharePoint 项相集成。 

如果你使用 Open ID Connect 进行身份验证, 则你的开发体验是无缝的, 因为你将使用 OAuth2 (开放 ID Connect 的基础) 来获取令牌, 以用于调用 Microsoft Graph Api。 如果你的应用程序使用的是 SAML 或 WSFed, 则必须在应用程序中添加其他代码, 以使这些 OAuth2 获取调用 Microsoft Graph Api 所需的令牌。 

## <a name="next-steps"></a>后续步骤

[为多租户应用程序启用 SSO](isv-sso-content.md)

[为多租户应用程序创建文档](isv-create-sso-documentation.md)
