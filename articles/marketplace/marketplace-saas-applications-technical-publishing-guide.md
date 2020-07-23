---
title: SaaS 应用程序发布指南 - Microsoft 商业市场
description: 向 Microsoft AppSource 和 Azure 市场发布 SaaS 应用程序套餐的要求和资源。
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: c19799265679eeead96bf95943f274aa32c75ff2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121565"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS 应用程序产品/服务发布指南

可以使用三种不同的号召性用语，在商业市场上发布 SaaS 应用程序：“与我联系”，“立即试用”和“立即获取”。 本文介绍这三个选项，包括每个选项的要求。 

## <a name="offer-overview"></a>产品/服务概述  

SaaS 应用程序可用于 Microsoft AppSource 和 Azure 市场。  两个商店均支持正式上架、试用和事务处理套餐。

**列表：** “列表”发布选项包含“与我联系”产品/服务类型，在试用级别或交易级别的参与不可行时使用。 此方法的好处是帮助在市场中发布解决方案的发布者立即开始获取潜在顾客，这些潜在客户可能会产生交易，从而扩大你的业务。  
**试用/交易：** 客户可以选择直接购买，也可以请求试用解决方案。 提供试用体验可以提升客户参与度，让客户在购买前体验解决方案。 借助面向客户的试用体验，发布者能获得更好的店面促销商机，随着参与客户的增多，可获得越来越多的潜在顾客。 试用版必须包含免费支持（至少在试用时间内）。  

| SaaS 应用产品/服务 | 业务要求 | 技术要求 |  
| --- | --- | --- |  
| **联系我们** | 是 | 否 |  
| **Power BI/Dynamics** | 是 | 是(Azure AD 集成) |  
| **SaaS 应用**| 是 | 是(Azure AD 集成) |     

## <a name="saas-list"></a>SaaS 列表

没有试用且没有计费功能的 SaaS 列表的号召性用语是“与我联系”。 

无需配置 Azure Active Directory 即可列出 SaaS 应用程序。 

|要求  |详细信息  |
|---------|---------|
|你的应用是 SaaS 产品/服务  |   解决方案是 SaaS 产品/服务并提供多租户 SaaS 产品。      |


## <a name="saas-trial"></a>SaaS 试用

使用免费试用的、基于软件即服务 (SaaS) 的试用提供解决方案或应用。 免费试用产品/服务可表示为使用受限或者使用时间有限的试用帐户。 


|要求  |详细信息  |
|---------|---------|
|你的应用是 SaaS 产品/服务  |   解决方案是 SaaS 产品/服务并提供多租户 SaaS 产品。      |
|你的应用已启用 AAD     |   客户将重定向到你的域，你将直接与客户进行交易       |


## <a name="saas-trial-technical-requirements"></a>SaaS 试用版技术要求

SaaS 应用程序的技术要求非常简单。 发布者只需与要发布的 Azure Active Directory (Azure AD) 集成。 Azure AD 与应用程序的集成已有完善的文档，并且 Microsoft 可以提供多个 SDK 和资源来实现此目的。  

若要开始，我们建议专门使用一个订阅进行 Azure 市场发布，使发布工作与其他活动相互独立。 完成后即可在此订阅中部署 SaaS 应用程序，以便开始开发工作。  

以下站点提供最佳 Azure Active Directory 文档、示例和指南： 

* [Azure Active Directory 开发人员指南](../active-directory/develop/index.yml)

* [与 Azure Active Directory 集成](../active-directory/develop/active-directory-how-to-integrate.md)

* [Azure 路线图 - 安全和标识](https://azure.microsoft.com/roadmap/?category=security-identity)

如需视频教程，请访问以下网站：

* [使用 Vittorio Bertocci 进行 Azure Active Directory 身份验证](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory 标识技术简介 - 第 1/2 部分](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory 标识技术简介 - 第 2/2 部分](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [使用 Microsoft Azure Active Directory 生成应用](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [侧重于 Active Directory 的 Microsoft Azure 视频](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

以下网站提供免费的 Azure Active Directory 培训：  
* [面向 IT 专业人士的 Microsoft Azure 内容系列：Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

另外，Azure Active Directory 还提供一个用于查找服务更新的站点   
* [Azure AD 服务更新](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>使用 Azure Active Directory 启用试用  

Microsoft 通过 Azure AD 对所有市场用户进行身份验证，因此当已验证的用户单击市场中的试用商品并重定向到试用环境时，可将用户直接预配到一个试用版中，而无需执行其他登录步骤。 身份验证期间应用从 Azure AD 接收的令牌包含有用的客户信息，可使用这些信息在应用内创建用户帐户，这样可以自动获得预配体验并提高转换率。 有关令牌的详细信息，请参阅[示例令牌](../active-directory/develop/active-directory-token-and-claims.md)。

使用 Azure AD 为应用或试用启用一键式身份验证具有以下影响：  
* 简化从市场到试用的客户体验。  
* 保持“产品内体验”观感，即使是当用户从市场重定向到你的域或试用环境中时也是如此。  
* 降低用户在重定向时放弃的可能性，因为无需其他登录步骤。  
* 减少因 Azure AD 用户过多而产生的部署困难。  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>为市场认证 Azure AD 集成  

可以采用多种其他的方式来认证 Azure AD 集成，具体取决于应用程序是单租户还是多租户，以及你是尚未使用过 Azure AD 联合单一登录 (SSO) 还是已经启用此功能。  

**对于多租户应用程序：**  

如果已启用 Azure AD，请执行以下操作：
1.    在 Azure 门户中注册你的应用程序
2.    启用 Azure AD 中的多租户支持功能，以获取“一键式”试用体验。 [此处](../active-directory/develop/active-directory-integrating-applications.md)提供更具体的信息。  

如果不熟悉 Azure AD 联合 SSO，请执行以下操作： 
1.  在 Azure 门户中注册你的应用程序
2.  使用 [OpenID Connect](../active-directory/develop/active-directory-protocols-openid-connect-code.md) 或 [OAuth 2.0](../active-directory/develop/active-directory-protocols-oauth-code.md) 开发 Azure AD 的 SSO。
3.  启用 AAD 中的多租户支持功能，以获取“一键式”试用体验。[此处](../active-directory/develop/active-directory-devhowto-appsource-certified.md)提供更具体的信息。  

**对于单租户应用程序，请使用以下任意选项：**  
* 使用 [Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) 将用户作为来宾用户添加到目录
* 通过“与我联系”为客户手动预配试用版
* 开发针对客户的“体验版”
* 使用 SSO 生成多租户示例演示应用

## <a name="saas-subscriptions"></a>SaaS 订阅

使用 SaaS 应用产品/服务类型可让客户以订阅的形式购买基于 SaaS 的技术解决方案。 SaaS 应用必须满足以下要求：
- 按统一价格（每月或每年）或按用户费率对服务进行定价和计费。
- 提供随时升级或取消服务的方法。
Microsoft 托管商业事务。 Microsoft 代你向客户计费。 要将 SaaS 应用作为订阅提供，必须与 SaaS 履行 API 集成。  服务必须支持预配、升级和取消。

| 要求 | 详细信息 |  
|:--- |:--- |  
|计费和计量 | 你的套餐将根据你在发布之前选择的定价模型（统一费率或用户费率）进行定价。  如果使用统一费率模型，则可以选择包含其他维度，用于向客户收取统一费率中未包含的使用费。 |  
|取消 | 客户可以随时取消你的产品/服务。 |  
|事务登陆页 | 托管一个 Azure 联合品牌事务登陆页，用户可以在其中创建并管理其 SaaS 服务帐户。 |   
| 订阅 API | 公开一个服务，该服务可以通过与 SaaS 订阅交互来创建、更新和删除用户帐户和服务计划。 必须在 24 小时内支持关键 API 更改。 非关键 API 更改将定期发布。 |  

>[!Note]
>云解决方案提供商 (CSP) 合作伙伴渠道选择加入现已可用。  请参阅[云解决方案提供商](./cloud-solution-providers.md)，以获取有关通过 Microsoft CSP 合作伙伴渠道推广套餐的详细信息。

## <a name="next-steps"></a>后续步骤
如果尚未注册，

* [了解](https://azuremarketplace.microsoft.com/sell)有关市场的信息。

要在合作伙伴中心注册，请开始创建新产品/服务或处理现有产品/服务：

* [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建或完成产品/服务。
* 请参阅[创建 SaaS 应用程序套餐](./partner-center-portal/create-new-saas-offer.md)获取详细信息。
