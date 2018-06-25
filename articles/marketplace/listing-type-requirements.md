---
title: 按商品类型列出的要求 | Azure
description: 本文为希望了解如何将应用发布到 Azure 市场的合作伙伴介绍了合格条件和发布要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: dbb14854f00fb133c3604a1dd529d42120371fd2
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825812"
---
# <a name="requirements-by-listing-type"></a>按商品信息类型列出的要求  
技术和营销内容要求因店面、产品/服务类型和商品信息类型而异。 请查看以下规范，以确认是否合规。  
1. 店面要求：  
    *   [AppSource](#storefront-requirements:-appSource)  
    *   [Azure 市场](#storefront-requirements:-azure-marketplace)  
2. 商品类型和产品/服务类型要求：  
    *   有关商品类型和产品/服务类型的详细信息，请访问“确定解决方案的商品类型”页：[docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md)。  

## <a name="storefront-requirements-appsource"></a>店面要求：AppSource  
下表描述了在 AppSource 中发布商品的先决条件要求。  
| 要求 | 详细信息 | 必需还是建议 |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | 在已启用许可的情况下，应用须允许使用 Azure Active Directory 联合单一登录（Azure AD 联合 SSO）。<ul> <li>有关启用 Azure AD 联合 SSO 的详细信息，请访问“针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录”页：[docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)。</li> </ul> | 必选 |   
| ***与 Microsoft 云服务集成*** | 应用应该与其他 Microsoft 云服务（例如 Microsoft Power BI、Cortana Intelligence）或 Microsoft Azure 服务集成。<ul> <li>物联网就是 Microsoft 云服务的一个示例。</li> </ul> | 建议 |  
| ***受众*** | 应用必须适用于业务线用户和业务所有者。 | 必选 | 
| ***适合企业的软件即服务 (SaaS) 应用*** | 应用必须满足以下要求。<ul> <li>是业务线 SaaS 应用</li> <li>专注于业务流程</li> <li>以企业客户为目标</li> <li>允许用户使用其工作凭据（用户名和密码）登录</li> </ul> | 必选 |  
| ***免费试用期和试用体验*** | 应用必须包含以下选项之一，使客户能够在有限的时间内免费使用你的应用。<ul> <li>提供 `try` 选项，使客户能够在 AppSource 中开始试用应用</li> <li>在 AppSource 中提供 `request trial` 选项，使客户能够请求应用的试用版</li> </ul>提供的免费试用版必须为客户提供一段预先规定的时间让他们试用应用，且不收取额外的费用。 | 必选 |  
| ***可轻松配置、随时可用的解决方案*** | 应用的配置和设置必须便捷，不需要自定义。 | 必选 |  
| ***商机管理*** | 在从店面收获商机之前，必须启用 CRM 来接收商机数据。<ul> <li>CRM 的例子包括 Marketo、Microsoft Dynamics 或 Salesforce</li> </ul> | 必选 |  
| ***隐私策略和使用条款*** | 应用必须使用公共 URL 提供隐私政策页面的链接。 发布期间必须以文本形式提供使用条款。 | 必选 |  
| ***支持*** | 应用必须使用公共 URL 提供客户支持页面的链接。 如果应用是试用版，则必须在试用期间提供支持，且不收取额外的费用。 | 必选 |  

## <a name="storefront-requirements-azure-marketplace"></a>店面要求：Azure 市场  
下面是 Azure 市场中商品类型的先决条件要求。  
| 要求 | 详细信息 | 商品类型 |  
|:--- |:--- |:--- |  
| ***参与策略*** | 必须遵循 Azure 市场参与政策。<ul> <li>有关参与政策的详细信息，请访问“Azure 市场参与政策”页：[azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies)。</li></ul> | list<br />事务处理<br />试用 |  
| ***与 Microsoft 集成*** | 产品/服务应利用或扩展 Microsoft Azure 服务（例如计算、网络或存储）。 产品/服务应按现有 Azure 市场类别进行分类（例如数据库、安全性或网络等）。<ul> <li>有关市场产品/服务的详细信息，请访问“市场应用”页：[azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps)。</li> </ul> | list<br />事务处理<br />试用 |  
| ***受众*** | 产品/服务必须面向 IT 专业人员、云开发人员或其他技术型客户角色。 | list<br />事务处理<br />试用 |  
| ***商机管理*** | 在从店面收获商机之前，必须启用 CRM（Marketo、Microsoft Dynamics 或 Salesforce）来接收商机数据。 | list<br />事务处理<br />试用 |  
| ***隐私策略和使用条款*** | 应用必须使用公共 URL 提供隐私政策页面的链接。 发布期间必须以文本形式提供使用条款。 | list<br />事务处理<br />试用 |  
| ***支持*** | 产品/服务必须使用公共 URL 提供客户支持页面的链接。 如果产品/服务是试用版，则必须在试用期间提供支持，且不收取额外的费用。 | 事务处理<br />试用 |    

## <a name="non-transact-listings"></a>非事务处理商品  
本部分描述不使用“事务处理”商品类型的所有产品/服务类型。 

### <a name="list"></a>列出  
“列表”商品类型包括市场中店面内的以下产品/服务类型。  

| 产品/服务类型 | 店面 | 详细信息 |  
|:---        |:---        |:---     |  
| 咨询服务 | AppSource | [要求：AppSource：列表：咨询服务](#requirements:-appsource:-list:-consulting-services) |  
| 咨询服务 | Azure 市场 | [要求：Azure 市场：列表：咨询服务](#requirements:-azure-marketplace:-list:-consulting-services) |  
| 与我联系 | AppSource | [](#) |  
| 与我联系 | Azure 市场 | [要求：AppSource：列表：与我联系](#requirements:-azure-marketplace:-list:-contact-me) |  

#### <a name="requirements-appsource-list-consulting-service"></a>要求：AppSource：列表：咨询服务  

| 要求 | 详细信息 |  
|:--- |:--- |  
| 服务产品/服务特点 | 咨询服务必须满足以下条件。<ul> <li>按固定范围、固定有效期和固定价格（或免费）提供。</li> <li>主要面向售前。</li> <li>限于单个客户。</li> <li>在现场执行。</li> </ul> |  
| 咨询服务的合作伙伴要求 | 符合服务相关领域的条件。<table><tr><th>解决方案领域</th><th>条件</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>具有银级或金级云客户关系管理资格。</td></tr><tr><td>Dynamics 365 for Finance and Operations Enterprise Edition</td><td>具有银级或金级企业资源规划资格，后续 12 个月的云运营收入达到 25,000 美元或以上。</td></tr><tr><td>Dynamics 365 for Finance and Operations Business Edition</td><td>充当一个或多个客户的云服务提供商 (CSP) 或数字记录合作伙伴 (DPOR)。</td></tr><tr><td>Power BI</td><td>满足解决方案合作伙伴的条件。</td></tr><tr><td>PowerApps</td><td>具有合作伙伴展示解决方案。</td></tr></table><ul> <li>有关客户关系管理的详细信息，请访问云客户关系管理页 [partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency)。</li> <li>有关资源规划的详细信息，请访问企业资源规划页 [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency)。</li> <li>有关 CSP 的详细信息，请访问云服务提供商页 [partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider)。</li> <li>有关 DPOR 的详细信息，请访问“数字记录合作伙伴和合作伙伴联盟”页 [partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record)。</li> <li>有关解决方案合作伙伴条件的详细信息，请访问解决方案合作伙伴概述和激励文档 [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf)。</li> <li>有关伙伴展示的详细信息，请访问合作伙伴展示页 [powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase)。</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>要求：Azure 市场：列表：咨询服务  

| 要求 | 详细信息 |  
|:--- |:--- |  
| 服务产品/服务特点 | 咨询服务必须满足以下条件。<ul> <li>按固定范围、固定有效期和固定价格（或免费）提供。</li> <li>主要面向售前。</li> <li>限于单个客户。</li> <li>在现场执行。</li> </ul> |  
| 咨询服务的合作伙伴要求 | 必须在服务相关领域拥有以下银级或金级资格之一。 <table><tr><th>解决方案领域</th><th>资质</th></tr><td>云平台和基础结构</td><td>云平台<br />数据中心</td><tr><td>应用程序开发和 ISV</td><td>应用程序开发<br />应用程序集成<br />DevOps</td></tr><tr><td>数据管理和分析</td><td>数据分析<br />数据平台</td></tr></table><ul> <li>有关资格的详细信息，请访问“通过 Microsoft 合作伙伴网络获得资格”页 [partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies)。</li> <li>有关商品的详细信息，请访问 Azure 市场咨询服务页 [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services)。</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---   

### <a name="trial"></a>试用  

| 产品/服务类型 | 店面 | 详细信息 |  
|:---        |:---        |:---     |  
| 免费/SaaS 试用 | AppSource | [商品类型要求：试用](#listing-type-requirements:-trial) |  
| 免费/SaaS 试用 | Azure 市场 | [要求：Azure 市场：试用：免费试用/SaaS 试用](#requirements:-azure-marketplace:-trial:-free-trial-/-saas-trial) |  
| 互动演示 | AppSource | [商品类型要求：试用](#listing-type-requirements:-trial) |  
| 互动演示 | Azure 市场 | [要求：Azure 市场：试用：互动演示](#requirements:-azure-marketplace:-trial:-interactive-demo) |  
| 体验版 | AppSource | [商品类型要求：试用](#listing-type-requirements:-trial) |  
| 体验版 | Azure 市场 | [要求：Azure 市场：试用：体验版](#requirements:-azure-marketplace:-trial:-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>要求：Azure 市场：试用  

| 要求 | 详细信息 |  
|:--- |:--- |  
| 免费试用期和试用体验 | 客户可以在有限时间内免费使用你的应用。<br /><br />客户不需要支付产品/服务或应用的任何许可证或订阅费用。 客户不需要支付基础 Microsoft 第一方产品或服务的费用。 所有试用选项部署到 Azure 订阅。 你可以独自控制成本优化和管理功能的试用版。<br /><br />可以选择免费试用、互动演示或体验版。 不管选择哪种，都必须向客户提供规定时间的免费应用试用。<ul> <li>若要开始创建体验版，请向 [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com) 发送电子邮件。</li> </ul>请注意：Azure 市场 SaaS 试用体验必须允许用户使用工作凭据登录。<ul> <li>有关详细信息，请访问 [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences) 上的 AppSource 试用体验部分。</li> </ul> |  
| 可轻松配置、随时可用的解决方案 | 应用的配置和设置须便捷。 |  
| 可用性/运行时间 | SaaS 应用或平台的运行时间须不低于 99.9%。 |  
| Azure Active Directory | 在已启用许可的情况下，产品/服务须允许使用 Azure Active Directory (Azure AD) 联合单一登录 (SSO)（Azure AD 联合 SSO）。 |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>要求：Azure 市场：试用：免费试用/SaaS 试用  

| 优势 | 要求 |  
|:--- |:--- |  
| 允许客户在购买前试用产品，然后再自动转换为付费使用。 此外，为客户启用概念证明，让客户能够与 Microsoft 销售团队合作。 | 解决方案是虚拟机或解决方案模板。<br /><br />解决方案是 SaaS 产品/服务并提供多租户 SaaS 产品。<br /><br />设有首批运行体验，可让单个客户快速体验。<br /><br />有单个租户但将客户添加为来宾用户。 |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>要求：Azure 市场：试用：互动演示  

| 优势 | 要求 |  
|:--- |:--- |  
| 让客户无需进行复杂的设置就能体验解决方案。 | 解决方案需要复杂的设置，且难以在试用时间内完成。 |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>要求：Azure 市场：试用：体验版  

| 优势 | 要求 |  
|:--- |:--- |  
| 让客户能在购买前试用产品。<br /><br />在预配置的设置中提供解决方案的引导式体验。<br /><br />使用体验版还能获得以下附加优势：<ul> <li>市场中有 27% 的用户搜索将搜索结果限于仅显示体验版的产品/服务。</li> <li>提供体验版的产品/服务比其他不提供体验版的产品/服务要多产生 38% 的潜在顾客。</li> <li>通过市场获取的新客户中有 36% 来自于使用过体验版的客户</li> <li>体验版启用 Microsoft 卖场销售，更好地了解如何通过共同销售售卖产品。</li> </ul> | 解决方案是虚拟机、解决方案模板或单租户的 SaaS 应用，或预配起来很复杂。 <br /><br />没有将试用版转换为付费产品/服务的方法。 |  

---

## <a name="transact-specific-listings"></a>事务处理特定的商品

### <a name="transact"></a>事务处理  

| 产品/服务类型 | 店面 | 详细信息 |   
|:---        |:---        | :--- |  
| Azure 应用：托管应用 | Azure 市场 | [要求：Azure 市场：事务处理：Azure 应用：托管应用](#requirements:-azure-marketplace:-transact:-azure-apps:-managed-app) |  
| Azure 应用：解决方案模板 | Azure 市场 | [要求：Azure 市场：事务处理：Azure 应用：解决方案模板](#requirements:-azure-marketplace:-transact:-azure-apps:-solution-template) |  
| 容器 | Azure 市场 | [要求：Azure 市场：事务处理：容器](#requirements:-azure-marketplace:-transact:-container) |  
| SaaS 应用  | Azure 市场 | [要求：Azure 市场：事务处理：SaaS 应用](#requirements:-azure-marketplace:-transact:-saas-app) |  
| 虚拟机 | Azure 市场 | [要求：Azure 市场：事务处理：虚拟机](#requirements:-azure-marketplace:-transact:-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>要求：Azure 市场：事务处理：容器  

| 要求 | 详细信息 |  
|:--- |:--- |  
| 计费和计量 | 支持免费或 BYOL 计费模型。 |  
| 基于 Docker 的映像 | 容器映像必须基于 Docker 映像格式，并且必须从 Azure 容器注册表提取。 |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>要求：Azure 市场：事务处理：SaaS 应用  

| 要求 | 详细信息 |  
|:--- |:--- |  
| 计费和计量 | 产品/服务按固定月费定价。 目前不支持基于用量的定价和基于用量的“校准”选项。 |  
| 取消 | 客户可以随时取消你的产品/服务。 |  
| 事务登陆页 | 托管 Azure 联合品牌的事务登陆页。 登陆页可让客户创建和管理 SaaS 服务帐户。 |  
| SaaS 订阅 API | 提供一个服务用来与 SaaS 订阅交互，以创建、更新和删除用户帐户和服务计划。 必须在 24 小时内支持所有关键 API 更改。 所有非关键 API 更改将定期更新。 |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>要求：Azure 市场：事务处理：虚拟机  

| 要求 | 详细信息 |  
|:--- |:--- | 
| 计费和计量 | VM 必须支持 BYOL 或即用即付每月计费。 |  
| 与 Azure 兼容的虚拟硬盘 (VHD) | 必须基于 Windows 或 Linux 构建 VM。<ul> <li>有关创建 Linux VHD 的详细信息，请访问 [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based) 上的“创建与 Azure 兼容的 VHD（基于 Linux）”部分。</li> <li>有关创建 Windows VHD 的详细信息，请访问 [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based) 上的“创建与 Azure 兼容的 VHD（基于 Windows）”部分。</li> </ul> |  

## <a name="next-steps"></a>后续步骤
*   访问 [Azure 市场和 AppSource 发布者指南](./marketplace-publishers-guide.md)页。  
 
---  
