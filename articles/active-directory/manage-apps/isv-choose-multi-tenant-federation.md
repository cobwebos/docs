---
title: 选择你的多租户应用程序的正确的联合身份验证协议
description: 在与 Azure Active Directory 集成的独立软件供应商的指南
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
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795201"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>选择你的多租户应用程序的正确的联合身份验证协议

当开发软件即服务 (SaaS) 应用程序时，必须选择最适合你和你的客户的需求的联合身份验证协议。 此决定基于你的开发平台，并根据需要与客户的 Office 365 和 Azure AD 生态系统中可用的数据集成。

请参阅的完整列表[协议可用于 SSO 集成](what-is-single-sign-on.md)与 Azure Active Directory。
将以下表进行比较， 
* 打开身份验证 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* 安全断言标记语言 (SAML)
* Web Services 联合身份验证 (WSFed)

| 功能| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| 基于 web 的单一登录| √| √ |
| 基于 web 的单一注销| √| √ |
| 基于 Mobile 的单一登录| √| √* |
| 基于 Mobile 的单一注销| √| √* |
| 移动应用程序的的条件性访问策略| √| X |
| 移动应用程序的的无缝 MFA 体验| √| X |
| Access Microsoft Graph| √| X |

\* 可行的但是 Microsoft 不提供示例或指导。

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 和 Openid 连接

OAuth 2.0 是[行业标准](https://oauth.net/2/)协议进行授权。 是 (OpenID Connect) 的 OIDC[行业标准](https://openid.net/connect/)基于 Oath 2.0 协议的标识身份验证层。

### <a name="benefits"></a>优点

Microsoft 建议使用 OIDC/OAuth 2.0，因为它们的身份验证和内置于协议的授权。 使用 SAML，必须另外实现授权。

这些协议中的固有授权使应用程序可以访问并与丰富的用户和组织数据通过 Microsoft Graph API 集成。

为应用程序采用 SSO 时，使用 OAuth 2.0 和 OIDC 简化了客户的最终用户体验。 轻松地可以定义权限集有必要，这会自动然后呈现给管理员或最终用户许可。

此外，使用这些协议，客户使用条件性访问和 MFA 策略来控制对应用程序的访问。 Microsoft 提供了库和[跨多个技术平台的代码示例](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)以帮助您的开发。  

### <a name="implementation"></a>实现

向 Microsoft 标识，它是 OAuth 2.0 提供程序注册应用程序。 你可以然后还基于 OAuth 2.0 的应用程序注册到你想要将集成与任何其他标识提供者。 

有关如何注册你的应用程序和 web 应用的 SSO 方式实现这些协议的信息，请参阅[授权访问 web 应用程序使用 OpenID Connect 和 Azure Active Directory](../develop/sample-v2-code.md)。  有关如何在移动应用中进行的 SSO 实现这些协议的信息，请参阅以下文章： 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [通用 Windows 平台](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 和 WSFed

安全断言标记语言 (SAML) 通常用于 web 应用程序。 请参阅[Azure 如何使用 SAML 协议](../develop/active-directory-saml-protocol-reference.md)概述。 

Web Services 联合身份验证 (WSFed) 是[行业标准](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)通常用于使用.Net 平台开发的 web 应用程序。

### <a name="benefits"></a>优点

SAML 2.0 是成熟标准和 SAML 2.0 的大多数技术平台支持的开源库。 若要配置 SAML SSO 的管理接口可以提供给客户。 它们可以为 Microsoft Azure AD 和其他标识提供程序支持 SAML 2 配置 SAML SSO

### <a name="trade-offs"></a>权衡

时为移动应用程序中使用 SAML 2.0 或 WSFed 协议，包括多重身份验证 (MFA) 的某些条件性访问策略具有降级的体验。 此外，如果你想要访问 Microsoft Graph，您需要实现通过 OAuth 2.0 授权以生成必要的令牌。 

### <a name="implementation"></a>实现

Microsoft 不会为 SAML 实现提供库或建议特定的库。 有许多开放源代码库。

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 和使用 Microsoft Graph Rest API 

Microsoft Graph 是 Microsoft 365，包括 Office 365、 Windows 10 和企业移动性和安全性和 Dynamics 365 等其他产品的所有数据结构。 这包括用户、 组、 日历、 邮件、 文件和的详细信息，该驱动器用户工作效率等实体的核心架构。 Microsoft Graph 提供了三个界面，开发人员 REST 基于 API，Microsoft Graph 数据连接，并且连接器允许开发人员将他们自己的数据添加到 Microsoft Graph。  

使用任何更高版本的协议提供 SSO 可以通过 Microsoft Graph REST API 提供的丰富数据应用程序的访问。 这样，客户从他们在 Microsoft 365 中的投资中获取更多价值。 例如，你的应用程序可以调用 Microsoft Graph API 来集成客户的 Office 365 实例和图面上用户的 Microsoft Office 和 SharePoint 应用程序中的项。 

如果使用 Open ID Connect 进行身份验证，则您的开发体验是无缝的因为您将使用 OAuth2，基础的 Open ID Connect，若要获取的令牌，可用于调用 Microsoft Graph Api。 如果你的应用程序使用 SAML 或 WSFed，必须在应用程序，若要获取这些 OAuth2 获取所需的调用 Microsoft Graph Api 的令牌中添加额外的代码。 

## <a name="next-steps"></a>后续步骤

[为多租户应用程序启用 SSO](isv-sso-content.md)

[创建多租户应用程序的文档](isv-create-sso-documentation.md)
