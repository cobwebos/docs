---
title: Azure 市场 SaaS 应用程序技术发布指南
description: 将 SaaS 应用程序发布到 Azure 市场的分步指南和发布查检表
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 2ac8119e36843e38e334fb5772ea4ade9962b4f9
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809465"
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS 应用程序技术发布指南

欢迎使用 Azure 市场 SaaS 应用程序技术发布指南。 本指南旨在帮助候选发布者和现有发布者使用 SaaS 应用程序产品将其应用程序和服务列入 Azure 市场。 

本指南分为以下部分，目的是方便你了解如何发布 SaaS 产品/服务：
* 产品/服务概述
* 业务要求
* 技术要求
* 发布过程
* 使用 Azure Active Directory 启用试用
* 为市场认证 Azure AD 集成

## <a name="offer-overview"></a>产品/服务概述  

SaaS 应用程序在两个 Azure 店面中均提供。下表介绍了当前的可用选项：

| 店面选项 | 商品 | 试用/交易 |  
| --- | --- | --- |  
| AppSource | 是(与我联系) | 是(PowerBI/Dynamics) |
| Azure 市场 | 否 | 是(SaaS 应用) |   

**商品**：“商品”发布选项包含“与我联系”产品/服务类型，是在试用级别或交易级别的参与不可行时使用的。 此方法的好处是帮助在市场中发布解决方案的发布者立即开始获取潜在顾客，这些潜在客户可能会产生交易，从而扩大你的业务。  
**试用/交易**：客户可以选择直接购买，也可以请求试用解决方案。 提供试用体验可以提升客户参与度，让客户在购买前体验解决方案。 借助面向客户的试用体验，发布者能获得更好的店面促销商机，随着参与客户的增多，可获得越来越多的潜在顾客。 试用版必须包含免费支持（至少在试用时间内）。  

| SaaS 应用产品/服务 | 业务要求 | 技术要求 |  
| --- | --- | --- |  
| **联系我们** | 是 | 否 |  
| **PowerBI/Dynamics** | 是 | 是(Azure AD 集成) |  
| **SaaS 应用**| 是 | 是(Azure AD 集成) |     

有关市场店面的详细信息以及每个发布选项的说明，请参阅 [市场发布者指南](https://aka.ms/sellerguide)和[发布选项](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option)。

## <a name="business-requirements"></a>业务要求
SaaS 产品/服务业务要求可以与技术要求一并完成。 大多数业务要求和信息是在云合作伙伴门户中生成 SaaS 产品/服务时收集的。 业务要求如下： 
* 同意参与策略
* 与 Microsoft 集成 
* 确定产品/服务的受众
* 定义并确定要使用的潜在客户管理
* 设置隐私策略和使用条款
* 定义支持部门联系人  

有关详细信息，可参阅主题：[市场发布的先决条件](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing)

## <a name="technical-requirements"></a>技术要求

SaaS 应用程序的技术要求非常简单。 发布者只需与要发布的 Azure Active Directory (Azure AD) 集成。 Azure AD 与应用程序的集成已有完善的文档，并且 Microsoft 可以提供多个 SDK 和资源来实现此目的。  

若要开始，我们建议专门使用一个订阅进行 Azure 市场发布，使发布工作与其他活动相互独立。 完成后即可在此订阅中部署 SaaS 应用程序，以便开始开发工作。  

以下站点提供最佳 Azure Active Directory 文档、示例和指南： 

* [Azure Active Directory 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure 路线图 - 安全和标识](https://azure.microsoft.com/roadmap/?category=security-identity)

如需视频教程，请访问以下网站：

* [使用 Vittorio Bertocci 进行 Azure Active Directory 身份验证](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory 标识技术简介 - 第 1/2 部分](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory 标识技术简介 - 第 2/2 部分](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [使用 Microsoft Azure Active Directory 生成应用](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [侧重于 Active Directory 的 Microsoft Azure 视频](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

以下网站提供免费的 Azure Active Directory 培训：  
* [面向 IT 专业人士的 Microsoft Azure 内容系列：Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

另外，Azure Active Directory 还提供一个用于查找服务更新的站点   
* [Azure AD 服务更新](https://azure.microsoft.com/updates/?product=active-directory)

可以使用以下资源获取支持：
* [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>发布过程

SaaS 发布过程包含技术步骤和业务步骤。  在开发和集成 Azure Active Directory 过程中完成的大多数工作可以与履行产品/服务的业务要求所需完成的工作一并完成。 大多数业务要求是云合作伙伴门户中 SaaS 应用产品/服务配置的一部分。  
下图显示了试用/交易产品/服务的主要发布步骤：  

![SaaS 发布步骤](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

下表介绍了每个主要的发布步骤：  

| 发布步骤 | 说明 |   
| --- | --- |  
| **创建 SaaS 应用程序** | 登录到云合作伙伴门户，选择“新建”，然后选择“SaaS 应用”产品/服务。 |  
| **创建与 Azure AD 的集成** | 按照上一部分介绍的技术要求，将 SaaS 产品/服务与 Azure AD 集成。 |  
| **设置产品/服务设置**| 输入所有 SaaS 产品/服务的初始信息。 要使用的产品/服务 ID 和产品/服务名称。 |     
| **设置技术信息** | 输入产品/服务的技术信息。 就 SaaS 应用程序来说，解决方案的 URI 和产品/服务的获取按钮类型（“免费”、“试用”或“与我联系”）是必填的。 |  
| **体验（可选）** | 这是一种可选的试用类型，主要用于其他类型的市场产品/服务。 它可以用于将试用版部署在发布者的订阅中，供最终客户试用。 |  
| **设置产品/服务店面资料**| 在此部分，发布者需链接并上传徽标、营销资料、法律文档并配置潜在客户管理系统。 |
| **设置产品/服务联系人** | 输入适用于 SaaS 产品/服务的工程部门联系人和支持部门联系人的信息。 |  
| **验证 SaaS 应用 Azure AD 集成** | 在提交 SaaS 应用进行发布之前，必须验证该应用是否已与 Azure AD 集成 |  
| **发布产品/服务**| 完成产品/服务和技术资产以后，即可发布该产品/服务。 这样就会启动发布过程，对解决方案模板进行测试、验证、认证和审批，这些都是进行发布所必需的。 |

## <a name="using-azure-active-directory-to-enable-trials"></a>使用 Azure Active Directory 启用试用  

Microsoft 通过 Azure AD 对所有 市场用户进行身份验证，当已验证的用户单击市场中的试用商品并重定向到试用环境时，可将用户直接预配到一个试用版中，而无需执行其他登录步骤。 身份验证期间应用从 Azure AD 接收的令牌包含有用的客户信息，可使用这些信息在应用内创建用户帐户，这样可以自动获得预配体验并提高转换率。 有关令牌的详细信息，请参阅[示例令牌](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。

使用 Azure AD 为应用或试用启用一键式身份验证具有以下影响：  
* 简化从市场到试用的客户体验。  
* 保持“产品内体验”观感，即使是当用户从市场重定向到你的域或试用环境中时也是如此。  
* 降低用户在重定向时放弃的可能性，因为无需其他登录步骤。  
* 减少因 Azure AD 用户过多而产生的部署困难。  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>为市场认证 Azure AD 集成  

可以采用多种其他的方式来认证 Azure AD 集成，具体取决于应用程序是单租户还是多租户，以及你是尚未使用过 Azure AD 联合单一登录 (SSO) 还是已经启用此功能。  

**对于多租户应用程序：**  

如果已启用 Azure AD，请执行以下操作：
1.  在 Azure 门户中注册你的应用程序
2.  启用 Azure AD 中的多租户支持功能，以获取“一键式”试用体验。 [此处](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)提供更具体的信息。  

如果不熟悉 Azure AD 联合 SSO，请执行以下操作： 
1.  在 Azure 门户中注册你的应用程序
2.  使用 [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 或 [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) 开发 Azure AD 的 SSO。
3.  启用 AAD 中的多租户支持功能，以获取“一键式”试用体验。[此处](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified)提供更具体的信息。  

**对于单租户应用程序，请使用以下任意选项：**  
* 使用 [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) 将用户作为来宾用户添加到目录
* 通过“与我联系”为客户手动预配试用版
* 开发针对客户的“体验版”
* 使用 SSO 生成多租户示例演示应用

