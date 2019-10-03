---
title: 使用 Azure Active Directory 启用 Microsoft AppSource 和 Azure 市场列表 | Azure
description: 在 Azure 市场和 AppSource 中使用 Azure Active Directory 为应用和服务发布者启用列表类型。
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: pabutler
ms.openlocfilehash: 0b68687a2bbaa817f2776757ccab9571638c0fd5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876003"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>使用 Azure Active Directory 启用 AppSource 和市场列表

 Azure Active Directory (Azure AD) 是一种云标识服务，它使用 Microsoft 帐户进行身份验证。 Azure AD 使用行业标准框架。 [详细了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory)。

## <a name="azure-ad-benefits"></a>Azure AD 优势

Microsoft AppSource 和 Azure 市场客户使用产品内体验来搜索列表目录。 这将要求客户登录该产品。 Azure AD 集成具有以下优势：

- 更快的参与和优化的客户体验
- 针对数百万企业用户的单一登录 (SSO)
- 跨不同合作伙伴发布的应用程序的一致的登录体验
- 针对移动应用和云应用的跨平台可缩放身份验证

## <a name="offers-that-require-azure-ad"></a>需要 Azure AD 的产品/服务

AppSource 和 Azure 市场的各种[列表选项和产品/服务类型](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)对 Azure AD 实现有不同的要求。 有关详细信息，请参阅下表：

| **产品/服务类型**    | **是否需要 Azure AD SSO？**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | 与我联系 | 试用 | 体验版 | 事务处理 |
| 虚拟机 | 不可用 | 否 | 否 | 否 |
| Azure 应用（解决方案模板）  | 不可用 | 不可用 | 不可用 | 不可用 |
| 托管应用  | 不可用 | 不可用 | 不可用 | 否 |
| SaaS  | 否 | 是 | 是 | 是 |
| 容器  | 不可用 | 不可用 | 不可用 | 否 |
| 咨询服务  | 否 | 不可用 | 不可用 | 不可用 |

有关 SaaS 技术需求的详细信息，请参阅 [SaaS 应用程序产品/服务发布指南](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)。

## <a name="azure-ad-integration"></a>Azure AD 集成

- 有关如何通过将 Azure AD 集成到列表中来启用单一登录的信息，请参阅[针对开发者的 Azure Active Directory]( https://aka.ms/aaddev)。
- 若要获取有关 Azure AD 单一登录的详细信息，请参阅[Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="enable-a-trial-listing"></a>启用试用列表

自动化客户设置可以增加转换的可能性。 当客户选择你的试用列表并重定向到试用环境时，可直接设置客户，而无需其他登录步骤。

在身份验证期间，Azure AD 会向应用或产品/服务发送令牌。 令牌提供的用户信息可在应用或产品/服务中创建用户帐户。 若要了解更多信息，请参阅[示例令牌](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。

使用 Azure AD 在应用或试用列表中启用一键式身份验证时，可以：

- 简化从市场到试用列表的客户体验。
- 保持“产品内体验”观感，即使是当用户从市场重定向到你的域或试用环境中时也是如此。
- 由于没有其他登录步骤，因此降低了用户在重定向时放弃的可能性。
- 减少因 Azure AD 用户过多而产生的部署困难。

## <a name="verify-azure-ad-integration"></a>验证 Azure AD 集成

### <a name="multitenant-solutions"></a>多租户解决方案

使用 Azure AD 支持以下操作：

- 在某个市场店面中注册应用。 查看[应用注册](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)或 [AppSource 认证](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)以获取详细信息。
- 启用 Azure AD 中的多租户支持功能，以获取一键式试用体验。

如果对使用 Azure AD 联合单一登录不熟悉，请执行以下步骤：

1. 在市场中注册应用。
1. 使用 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) 或 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 通过 Azure AD 开发 SSO。
1. 启用 Azure AD 中的多租户支持功能，以提供一键式试用体验。

### <a name="single-tenant-solutions"></a>单租户解决方案

使用 Azure AD 支持以下操作之一：

- 使用 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) 将访客用户添加到目录。
- 使用“与我联系”发布选项手动为客户设置试用版  。
- 开发针对每个用户的体验版。
- 生成使用 SSO 的多租户示例演示应用。

## <a name="next-steps"></a>后续步骤

- 确保[已在 Azure 市场中注册](https://azuremarketplace.microsoft.com/sell)。
- 有关如何创建或完成产品/服务的详细信息, 请参阅[如何创建合作伙伴中心帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)。
