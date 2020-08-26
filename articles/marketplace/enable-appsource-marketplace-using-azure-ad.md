---
title: 将 Microsoft 商业市场产品/服务与 Azure Active Directory 集成
description: 使用 Azure Active Directory 实现 Microsoft AppSource 和 Azure 市场产品/服务的身份验证。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: a6e304e5ffeab8f0a44cbdfe1566465f2b9bf34a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607414"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>将商业市场列表与 Azure Active Directory 集成

 本文介绍将商业市场列表或产品/服务与 Azure Active Directory (Azure AD) 进行集成的相关要求。 Azure AD 是一种云标识服务，它使用行业标准框架来实现 Microsoft 帐户的身份验证。 [详细了解 Azure Active Directory](https://azure.microsoft.com/services/active-directory)。

## <a name="azure-ad-benefits"></a>Azure AD 优势

Microsoft AppSource 和 Azure Marketplace 客户使用产品内体验搜索在线商店列表目录。 这将要求客户登录该产品。 Azure AD 集成具有以下优势：

- 更快的参与和优化的客户体验
- 针对数百万企业用户的单一登录 (SSO)
- 跨不同合作伙伴发布的应用程序的一致的登录体验
- 针对移动应用和云应用的跨平台可缩放身份验证

## <a name="offers-that-require-azure-ad"></a>需要 Azure AD 的产品/服务

各种商业市场[列表选项和产品/服务类型](determine-your-listing-type.md)对 Azure AD 的实现有不同的要求。 有关详细信息，请参阅下表。

| 产品/服务类型    | 需要 Azure AD SSO 才能联系我？  | 需要 Azure AD SSO？ | 需要 Azure AD 用于测试驱动器的 SSO？  | 需要 Azure AD SSO 才能实现 |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| 虚拟机 | 空值 | 否 | 否 | 否 |
| Azure 应用（解决方案模板）  | 空值 | 空值 | 空值 | 空值 |
| 托管应用  | 空值 | 空值 | 空值 | 否 |
| SaaS  | 否 | 是 | 是 | 是 |
| 容器  | 空值 | 空值 | 空值 | 否 |
| 咨询服务  | 否 | 空值 | 空值 | 空值 |

有关 SaaS 技术要求的详细信息，请参阅 [商业应用商店中的 Azure AD 和事务 SaaS 产品](./azure-ad-saas.md)。

## <a name="azure-ad-integration"></a>Azure AD 集成

- 若要详细了解如何集成事务 software as service (SaaS) 产品的 Azure AD，请参阅 [商业应用商店中的 Azure AD 和事务 SaaS 产品](./azure-ad-saas.md)。
- 有关如何通过将 Azure AD 集成到列表中来启用单一登录的信息，请参阅[针对开发者的 Azure Active Directory](../active-directory/develop/index.yml)。
- 若要获取有关 Azure AD 单一登录的详细信息，请参阅[Azure Active Directory 的应用程序访问与单一登录是什么？](../active-directory/manage-apps/what-is-single-sign-on.md)。

## <a name="enable-a-trial-listing"></a>启用试用列表

自动化客户设置可以增加转换的可能性。 当客户选择你的试用列表并重定向到试用环境时，可直接设置客户，而无需其他登录步骤。

在身份验证期间，Azure AD 会向应用或产品/服务发送令牌。 令牌提供的用户信息可在应用或产品/服务中创建用户帐户。 若要了解更多信息，请参阅[示例令牌](../active-directory/develop/id-tokens.md)。

使用 Azure AD 在应用或试用列表中启用一键式身份验证时，可以：

- 简化从商业应用商店到试用版列表的客户体验。
- 即使将用户从商用 marketplace 重定向到你的域或试用环境，仍可保持产品内体验的外观。
- 由于没有其他登录步骤，因此降低了用户在重定向时放弃的可能性。
- 减少因 Azure AD 用户过多而产生的部署困难。

## <a name="verify-azure-ad-integration"></a>验证 Azure AD 集成

### <a name="multitenant-solutions"></a>多租户解决方案

使用 Azure AD 支持以下操作：

- 在一家商业市场在线商店中注册你的应用。 查看[应用注册](../active-directory/develop/quickstart-register-app.md)或 [AppSource 认证](../active-directory/azuread-dev/howto-get-appsource-certified.md)以获取详细信息。
- 启用 Azure AD 中的多租户支持功能，以获取一键式试用体验。

如果对使用 Azure AD 联合单一登录不熟悉，请执行以下步骤：

1. 在商业应用商店中注册你的应用。
1. 使用 [OAuth 2.0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) 或 [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md) 通过 Azure AD 开发 SSO。
1. 启用 Azure AD 中的多租户支持功能，以提供一键式试用体验。

### <a name="single-tenant-solutions"></a>单租户解决方案

使用 Azure AD 支持以下操作之一：

- 使用 [Azure AD B2B](../active-directory/b2b/what-is-b2b.md) 将访客用户添加到目录。
- 使用“与我联系”发布选项手动为客户设置试用版。
- 开发针对每个用户的体验版。
- 生成使用 SSO 的多租户示例演示应用。

## <a name="next-steps"></a>后续步骤

如果尚未注册， 

- [了解](https://azuremarketplace.microsoft.com/sell) 商用 marketplace。

要在合作伙伴中心注册，请开始创建新产品/服务或处理现有产品/服务：

- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建或完成产品/服务。
