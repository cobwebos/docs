---
title: 使用 Azure Active Directory 启用 Microsoft AppSource 和 Azure 市场列表 | Azure
description: 在 Azure 市场和 AppSource 中使用 Azure Active Directory 为应用和服务发布者启用列表类型。
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: d7fd09928c0a687755d216e7f10f7eac23677c63
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987338"
---
# <a name="enable-a-microsoft-appsource-and-azure-marketplace-listing-by-using-azure-active-directory"></a>使用 Azure Active Directory 启用 Microsoft AppSource 和 Azure 市场列表

Microsoft Azure Active Directory (Azure AD) 是一种云标识服务，它使用行业标准框架实现使用 Microsoft 帐户进行身份验证。  有关 Azure AD 的详细信息，请参阅 [Azure Active Directory](https://azure.microsoft.com/services/active-directory)。

## <a name="benefits-of-using-azure-active-directory"></a>使用 Azure Active Directory 的好处

Microsoft AppSource 和 Azure Marketplace 客户使用产品内体验来搜索列表目录，这将要求他们登录到该产品。  通过将应用程序与 Azure AD 集成，可以加快参与并优化客户体验。 Azure AD：

- 为数百万企业用户启用单一登录 (SSO)。
- 在不同合作伙伴发布的应用程序之间实现一致的用户登录体验。
- 针对移动应用和云应用提供跨平台可缩放身份验证。

如以下部分所述，实施 Azure AD 以发布到市场需要某些产品/服务。

## <a name="azure-active-directory-requirements"></a>Azure Active Directory 要求

针对 Microsoft AppSource 和 Azure 市场，有不同的[列表选项和产品/服务类型](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)。  这些列表选项和产品/服务类型的 Azure AD 要求如下所示：

| **产品/服务类型**    | **是否需要 AAD SSO？**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | 与我联系 | 试用 | 体验版 | 事务处理 |
| 虚拟机 | 不适用 | 否 | 否 | 否 |
| Azure 应用（解决方案模板）  | 不适用 | 不适用 | 不适用 | 不适用 |
| 托管应用  | 不适用 | 不适用 | 不适用 | 否 |
| SaaS  | 否 | 是 | 是 | 是 |
| 容器  | 不适用 | 不适用 | 不适用 | 否 |
| 咨询服务  | 否 | 不适用 | 不适用 | 不适用 |

有关 SaaS 技术需求的详细信息，请参阅 [SaaS 应用程序产品/服务发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)。

## <a name="integration-with-azure-active-directory"></a>与 Azure Active Directory 集成

有关如何与 Azure AD 集成以启用 SSO 的信息，请访问 https://aka.ms/aaddev。

有关 Azure AD SSO 的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="enable-a-trial-listing-by-using-azure-active-directory"></a>使用 Azure Active Directory 启用试用列表

客户在市场上选择你的试用列表后，系统会将其重定向到你的试用环境。 在你的试用环境中，你可以直接设置你的客户，而无需额外的登录步骤。 你的应用或产品/服务在身份验证期间从 Azure AD 接收令牌。 该令牌包含用于在你的应用或产品/服务中创建用户帐户的有价值用户信息。 你可以自动执行客户设置，并增加转换的可能性。

有关在身份验证期间从 Azure AD 发送的令牌的详细信息，请参阅[示例令牌](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims#sample-tokens)。

使用 Azure AD 为应用或试用启用一键式身份验证。 Azure AD 可提供以下优势： 
*   简化从市场到试用的客户体验。
*   保持“产品内体验”观感，即使是当用户从市场重定向到你的域或试用环境中时也是如此。
*   降低用户在重定向时放弃的可能性，因为无需其他登录步骤。
*   减少因 Azure AD 用户过多而产生的部署困难。

### <a name="verify-your-azure-ad-integration-in-the-marketplace-multitenant-apps"></a>在市场中验证 Azure AD 集成：多租户应用
使用 Azure AD 对解决方案支持以下选项：
*   在市场的店面中注册应用。
*   启用 Azure AD 中的多租户支持功能，以获取一键式试用体验。

有关应用注册的详细信息，请参阅[将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)。

如果你对使用 Azure AD 联合单一登录 (SSO) 不熟悉，请完成以下步骤：
1.  在市场中注册应用。 
2.  使用 OAuth 2.0 或 OpenID Connect 开发 Azure AD 的 SSO。
    *   有关 OAuth 2.0 的详细信息，请参阅 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)。
    *   有关 Open ID Connect 的详细信息，请参阅 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)。
3.  启用 Azure AD 中的多租户支持功能，以提供一键式试用体验。
    
    有关 AppSource 认证的详细信息，请参阅 [AppSource 认证](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)。 

### <a name="verify-your-azure-ad-integration-in-the-marketplace-single-tenant-apps"></a>在市场中验证 Azure AD 集成：单租户应用
使用 Azure AD 对单租户解决方案支持以下选项之一： 
*   使用 Azure Active Directory B2B (Azure AD B2B) 将用户作为来宾用户添加到目录中。 有关 Azure AD B2B 的详细信息，请参阅[什么是 Azure AD B2B 协作](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。
*   使用“与我联系”发布选项手动为客户设置试用版。
*   开发针对每个用户的体验版。
*   生成使用 SSO 的多租户示例演示应用。

## <a name="next-steps"></a>后续步骤

如果尚未注册， 
- 请在市场中[注册](https://azuremarketplace.microsoft.com/sell)。

如果已注册并正在创建新套餐或正在使用现有套餐，
- [登录到云合作伙伴门户](https://cloudpartner.azure.com/)，创建或完成套餐。

