---
title: 为多租户应用程序选择正确的联合身份验证协议
description: 独立软件供应商与 Azure 活动目录集成指南
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
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443379"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>为多租户应用程序选择正确的联合身份验证协议

当您将软件开发为服务 （SaaS） 应用程序时，必须选择最能满足您和您的客户需求的联合协议。 此决策基于您的开发平台，以及您希望与客户 Office 365 和 Azure AD 生态系统中的数据集成。

请参阅可用于与 Azure 活动目录[进行 SSO 集成](what-is-single-sign-on.md)的协议的完整列表。
下表比较 
* 打开身份验证 2.0 （OAuth 2.0）
* 打开 ID 连接 （OIDC）
* 安全断言标记语言 (SAML)
* 网络服务联盟

| 功能| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| 基于 Web 的单登录| √| √ |
| 基于 Web 的单签| √| √ |
| 基于移动的单一登录| √| √* |
| 基于移动的单次注销| √| √* |
| 移动应用程序的条件访问策略| √| X |
| 适用于移动应用程序的无缝 MFA 体验| √| X |
| 访问微软图| √| X |

*可能，但微软不提供示例或指南。

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 和打开 ID 连接

OAuth 2.0 是一个[行业标准](https://oauth.net/2/)的授权协议。 OIDC（OpenID 连接）是构建在 OAuth 2.0 协议之上的[行业标准](https://openid.net/connect/)标识身份验证层。

### <a name="benefits"></a>优点

Microsoft 建议使用 OIDC/OAuth 2.0，因为它们具有内置到协议中的身份验证和授权。 使用 SAML 时，还必须额外实现授权。

这些协议固有的授权使应用程序能够通过 Microsoft 图形 API 访问和集成丰富的用户和组织数据。

使用 OAuth 2.0 和 OIDC 可简化客户在为您的应用使用 SSO 时的最终用户体验。 您可以轻松地定义所需的权限集，然后自动表示给管理员或最终用户同意。

此外，使用这些协议使您的客户能够使用条件访问和 MFA 策略来控制对应用程序的访问。 Microsoft[跨多个技术平台](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples)提供库和代码示例，以帮助您开发。  

### <a name="implementation"></a>实现

您将应用程序注册到 Microsoft 标识，这是 OAuth 2.0 提供程序。 然后，您还可以将基于 OAuth 2.0 的应用程序注册到您希望与任何其他标识提供商集成。 

有关如何注册应用程序并将 SSO 协议实现到 Web 应用程序的信息，请参阅[使用 OpenID 连接和 Azure 活动目录授权访问 Web 应用程序](../develop/sample-v2-code.md)。  有关如何在移动应用中为 SSO 实现这些协议的信息，请参阅以下内容： 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [通用 Windows 平台](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 和 WSFed

安全断言标记语言 （SAML） 通常用于 Web 应用程序。 请参阅[Azure 如何使用 SAML 协议](../develop/active-directory-saml-protocol-reference.md)进行概述。 

Web 服务联盟 （WSFed） 是一种[行业标准](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)，通常用于使用 .Net 平台开发的 Web 应用程序。

### <a name="benefits"></a>优点

SAML 2.0 是一个成熟的标准，大多数技术平台都支持 SAML 2.0 的开源库。 您可以为客户提供一个管理接口来配置 SAML SSO。 他们可以为 Microsoft Azure AD 配置 SAML SSO，以及支持 SAML 2 的任何其他标识提供程序

### <a name="trade-offs"></a>权衡

在移动应用程序中使用 SAML 2.0 或 WSFed 协议时，某些条件访问策略（包括多重身份验证 （MFA） 将具有降级体验。 此外，如果要访问 Microsoft 图形，则需要通过 OAuth 2.0 实现授权以生成必要的令牌。 

### <a name="implementation"></a>实现

Microsoft 不为 SAML 实现提供库，也不推荐特定库。 有许多开源库可用。

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 和使用微软图形静止 API 

Microsoft Graph 是跨所有 Microsoft 365 的数据交换矩阵，包括 Office 365、Windows 10 和企业移动性和安全性，以及其他产品（如 Dynamics 365）。 这包括实体的核心架构，如用户、组、日历、邮件、文件等，这些实体可提高用户工作效率。 Microsoft Graph 为开发人员提供了三个接口，即基于 REST 的 API、Microsoft 图形数据连接和连接器，允许开发人员将自己的数据添加到 Microsoft 图形中。  

使用上述任何协议进行 SSO 使应用程序能够访问通过 Microsoft 图形 REST API 提供的丰富数据。 这使您的客户能够从对 Microsoft 365 的投资中获得更多价值。 例如，您的应用程序可以调用 Microsoft 图形 API 来与客户的 Office 365 实例以及应用程序中的 Microsoft Office 和 SharePoint 项目集成。 

如果使用 Open ID 连接进行身份验证，则开发体验是无缝的，因为您将使用 Open ID Connect 的基础 OAuth2 获取令牌，可用于调用 Microsoft 图形 API。 如果应用程序使用 SAML 或 WSFed，则必须在应用程序中添加其他代码，以获取这些 OAuth2 以获取调用 Microsoft 图形 API 所需的令牌。 

## <a name="next-steps"></a>后续步骤

[为多租户应用程序启用 SSO](isv-sso-content.md)

[为多租户应用程序创建文档](isv-create-sso-documentation.md)
