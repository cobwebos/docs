---
title: "Azure 应用商店卖家指南 | Microsoft Docs"
description: "本指南适用于想要向 IT 专业人员和开发人员销售 Azure 认证虚拟机映像和托管应用程序的独立软件供应商 (ISV) 业务用户和产品经理。"
documentationcenter: 
author: divacc
manager: prkumar
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: divacc@microsoft.com
ms.openlocfilehash: 0985c7da429202cc6003df73c6ca17714b5a1287
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="azure-marketplace-seller-guide"></a>Azure 应用商店卖家指南

欢迎使用 Azure 应用商店卖家指南。 本指南专为想要向 IT 专业人员和开发人员销售 Azure 认证虚拟机映像和托管应用程序的独立软件供应商 (ISV) 业务用户和产品经理而设。 [Azure Marketplace](https://azuremarketplace.microsoft.com/) 能够为世界各地的 Azure 客户提供极佳的产品市场宣传和曝光度。

> [!NOTE]
> 如果想要销售[托管应用程序](https://docs.microsoft.com/en-us/azure/managed-applications/overview)，应按照说明将其在 Azure Marketplace 的[此处](https://docs.microsoft.com/en-us/azure/managed-applications/publish-marketplace-app)列出。

> [!NOTE]
> 如果有意向 IT 专业人员和开发人员出售软件即服务 (SaaS) 成品，可联系 saas-on-amp@microsoft.com 详细了解相关选择。

> [!NOTE]
> 如果有意向业务用户销售服务型软件 (SaaS) 成品，可以研究要使用哪种选项在 [AppSource](https://appsource.microsoft.com) 列出产品。


完成本指南后，就会知道可在何处找到有关这些主题的详细信息：

- 什么是 Azure 应用商店？
- 如何确定我的产品是否适合在应用商店中销售？
- 在应用商店销售产品有哪些优势？
- 在应用商店销售产品需要满足哪些先决条件（技术和非技术方面）？
- 如何构建与 Azure 兼容的虚拟硬盘 (VHD)？
- 如何以卖方身份申请和注册？
- 如何创建和发布产品？
- 如何投放市场和查找可用资源？
- 应用商店提供哪些报告和见解？
- 可在何处获得帮助和支持？

让我们开始吧。

## <a name="whats-the-azure-marketplace"></a>什么是 Azure 应用商店？

Azure 应用商店是一个在线应用程序和服务市场，其中各种规模的 ISV（从创业公司到大型企业）可向全球 Azure 客户提供解决方案。 Marketplace 允许 Azure 发布者向希望在 Azure 中快速开发基于云的应用程序和移动解决方案的其他专业人员分发和销售虚拟机映像、托管应用程序和 SaaS 应用程序。 Azure Marketplace 支持多种多样的产品，从支持数据处理的端到端数据分析应用程序、数据存储、分析层到分层电子商务应用（数据、服务和 Internet）。

云客户在搜索符合独特需求的解决方案时面临着多种挑战。 Azure Marketplace 提供一种方法来解决这些挑战，可让客户与创新的 ISV 解决方案互动，如下表中所述：

| 客户需求 | Azure 应用商店解决方案 |
| --- | --- |
| 需要使用其他云平台功能来解决业务和技术需求 | 在 Azure 上提供一套不断扩充的互补性应用程序和服务产品组合 |
| 发现很难找到适当的应用程序或服务 | 提供一站式商店让客户发现、搜索和购买应用程序与服务 |
| 需要对第三方应用程序和服务使用可缩放的部署机制 | 允许对第三方应用程序和服务创建和配置可缩放的部署 |
| 需要使用新的应用程序与服务来集成和使用现有解决方案 | 轻松地将第三方应用程序和服务与 Azure 上的现有解决方案集成 |

Azure Marketplace 为全球客户打造了优质、多样化和强健的 Azure 合作伙伴生态系统。 主要优势包括：

- 在统一的位置供应 Microsoft 与合作伙伴开发的基于 Azure 的产品。
- 产品超过 5,000 个。
- 集成的平台体验。
- 简化的配置、部署和管理。

## <a name="is-azure-marketplace-right-for-my-business"></a>Azure Marketplace 是否适合我的业务？

此时此刻，可能会疑惑，Azure 应用商店是否适合业务。 如果适合，它能够带来哪种优势？ 应用商店创造了新的销售机会：

- 利用 Azure 上扩展的解决方案产品组合打入新的客户市场。 使用 Azure 服务向 Microsoft 在线订阅计划 (MOSP) 和 Microsoft 企业协议客户追加销售和交叉销售应用商店产品。 可以轻松地将应用商店产品整合到客户解决方案和 Azure 方案推介产品中。
- 提高业务价值并增加与现有和新客户帐户的交易规模。 Marketplace 有助于扩大交易规模、解决客户在将工作负荷移到云中时遇到的难题，并增加交易收益。 可以根据客户的要求销售配套的解决方案并解决 Azure 云平台差异，从而提高业务价值。
- 通过销售应用商店应用程序和服务吸引大量潜在客户。 应用商店使得查找和留住新客户更加方便。 当今的许多企业需要将工作负荷转移到云中，适应不断变化的基础结构环境。 可以提供适当的应用程序和服务来帮助这些企业弥补环境的差异。
- 通过将应用商店产品与 Azure 服务进行绑定，补充并扩展 Azure 功能。 Marketplace 有助于设计与客户进行基于应用场景的对话。 还可以探讨端到端解决方案，应对具体的平台差异和客户需求。 最后，通过销售解决方案捆绑包，可以使用 Azure 平台生态系统来解决各种客户问题，同时增大销售额。

## <a name="what39s-the-customer-base-for-the-marketplace"></a>应用商店面向哪种客户群？

应用商店面向多种客户群。 此外，Azure 具有所有云提供商中增长最快的客户群之一。 可以接触 IT 专业人员和开发人员，这些人员涉及多个行业，就职于公共和私营部门，服务的公司从初创企业横跨到大型企业。

## <a name="how-does-marketplace-work"></a>如何使用 Marketplace？

操作非常容易。 获得批准后，即可创建 Azure 认证的虚拟机映像或托管应用程序并将其发布到 Marketplace。 在这里，Azure 客户几分钟内即可查找、购买并部署产品。 更棒的是，客户可以放心部署解决方案。 他们知道配置的环境可在 Azure 上顺利运行，并且基础结构在几分钟内就能启动。

云合作伙伴门户是在应用商店中创建产品的中心。 若要认证产品/服务，使其随时可供发布，必须满足某些先决条件。 详情请参见下一节。


## <a name="whats-next"></a>后续步骤

可能认为 Azure 应用商店确实很适合产品。 那么，如何开始？ 本部分重点介绍在 Marketplace（图 1）上启动并运行虚拟机映像的方法： 
* 通过 Azure 认证。
* 获取销售产品的批准。
* 通过云合作伙伴门户创建并发布产品/服务。 

![在 Azure 应用商店中销售产品的过程](./media/cloud-partner-portal-seller-guide/processforselling.png)

图 1：在 Azure 应用商店中销售产品的过程

根据计划发布的工作负载（虚拟机或托管应用程序），按照不同过程来发布产品/服务。 以下内容主要介绍虚拟机产品/服务的发布。

> [!NOTE]
> 如果想要销售[托管应用程序](https://docs.microsoft.com/en-us/azure/managed-applications/overview)，应按照说明将其在 Azure Marketplace 的[此处](https://docs.microsoft.com/en-us/azure/managed-applications/publish-marketplace-app)列出。

> [!NOTE]
> 如果有意向 IT 专业人员和开发人员出售软件即服务 (SaaS) 成品，可联系 saas-on-amp@microsoft.com 详细了解相关选择。


首先，需要满足一系列技术和非技术先决条件，并准备好虚拟机映像。 然后，指定产品的名称并注册为卖方。 最后，添加市场营销内容并提交发布。 在应用商店中推出产品之前，可以先在预览/过渡环境中检查产品。

首次创建要在 Azure 应用商店中销售的产品时，需规划约 4 周时间进行基本登记。 如果可能，请在推出产品之前花 6 周时间进行构建，匀出时间进行媒体宣传和发布任务。

## <a name="how-do-i-become-azure-certified"></a>如何通过 Azure 认证？

若创建要在 Azure 应用商店中销售的产品，首先是通过 Azure 认证。 这意味着，需要编写公司信息、同意参与策略、下载必要的工具并构建技术组件（图 2）。

![通过 Azure 认证规定的要求](./media/cloud-partner-portal-seller-guide/azurecertified.png)

图 2：通过 Azure 认证规定的要求


### <a name="technical-prerequisites"></a>技术先决条件

推出产品之前，请仔细查看并满足所有技术先决条件。 需要访问 Windows 或 Linux，并访问链接到测试工具的 Azure 兼容 VHD。

建议使用远程桌面协议在云中直接开发 Azure VHD。 但是，可下载 VHD 并使用[本地基础结构](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise)进行开发（若只能如此操作）。

有关详细信息，请参阅[技术先决条件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)页。

### <a name="nontechnical-prerequisites"></a>非技术性先决条件

若要加入应用商店，需要满足一些非技术先决条件。 首先，查看并同意 [Azure 应用商店参与策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)的条款。 在应用商店中提供的软件和服务必须至少满足以下要求之一：

- 在 Azure 上运行。 软件或服务的主要功能必须在 Microsoft Azure 上运行。
- 部署到 Azure。 在产品列表信息中，必须说明如何在 Azure 上部署该软件或服务。
- 与 Azure 服务集成或扩展该服务。 必须在产品列表信息中支指出：
  - 软件或服务要与哪项 Azure 服务集成或扩展哪项服务
  - 软件或服务如何与 Azure 服务集成或如何扩展该服务

此外，还需要满足 Azure 应用商店参与策略中所述的以下业务要求：

- 公司（或其子公司）必须位于应用商店支持的销售国家/地区。
- 产品必须按与应用商店支持的计费模型兼容的方式获得许可。
- 需要负责以商业上合理的方式向客户提供技术支持，支持的形式包括免费、付费或社区支持。
- 需要购买自己的软件和任何第三方软件依赖项的许可证。
- 内容必须满足 [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) 和 Azure 门户中列出的特定产品准则。

最后，需要同意[使用条款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隐私声明](http://www.microsoft.com/privacystatement/default.aspx)和 [Microsoft Azure 认证计划协议](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)。 

有关常见问题的列表，请参阅 [Azure 应用商店常见问题解答](https://azure.microsoft.com/marketplace/faq/)。


### <a name="azure-certification"></a>Azure 认证

获得了“Azure 认证”标志即表示登记过程已成功完成。 这种状态可让客户相信其 IT 专业人员和开发员购买的优质解决方案是受信任合作伙伴提供的，可以配合 Azure 技术运行或者是使用这种技术构建的。 Azure 认证的解决方案包括：

- 全球审核。
- 确定与 Azure 平台的兼容性。
- 联机映像安全符合性。
- 无病毒或恶意软件。
- 支持的计费模式。

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>如何指定产品名称并获得批准？

现在，可以请求批准在应用商店中销售产品（图 3）。 

在 Microsoft 服务中可以轻松指定产品的名称、完成发布过程并注册为卖方。 你将经历不同的提名流程，具体取决于计划发布的工作负载（虚拟机或托管应用程序）。

![获得在 Azure 应用商店中销售产品的批准](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

图 3：获得在 Azure 应用商店中销售产品的批准

若要发布虚拟机产品/服务，可在[此处](https://createopportunity.azurewebsites.net/)提名你的产品。 批准最长需要 3 个工作日。

获得批准后，将收到以下信息：

- 一封电子邮件回执，其中包含一个可用于抵消 99 美元开发中心申请费的促销代码，以及要在云合作伙伴门户中填写的档案资料。
- Azure 认证状态的技术预先审批资料，以及创建产品和认证 VHD 的选项。 （只有开发中心申请获得批准后，才能创建产品。）
- 有关云合作伙伴门户访问说明，以及发布过程的概述。
- 是否符合致电 Microsoft 登记团队，让他们引导完成登记过程和解决问题的条件。
- 是否能够发布二次产品。 二次产品不需要审批。 可将它们直接加入云合作伙伴门户，但虚拟机仍必须通过发布过程认证。
- 有关请求发布方面的帮助的指导。 （问题应会定向到应用商店发布者[支持链接](https://support.microsoft.com/en-us/getsupport?oaspworkflow=start_1.0.0.0&wf=0&wfName=productselection&prid=16230&ccsid=636282352448485256)。）

最后，将[帐户注册](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)为 Microsoft 卖方。 审批和审核最长可能需要两周，请利用这段时间在云合作伙伴门户中创建 Azure 应用商店产品。

## <a name="how-do-i-publish-my-offer-on-the-azure-marketplace"></a>如何在 Azure 应用商店发布我的产品/服务？

现在，可以认证虚拟机映像并发布产品/服务。 为此，可以使用云合作伙伴门户。 可将云合作伙伴门户视为发布和管理解决方案的中心。 基本上，只需上传你的 VHD、添加营销内容和 SKU 详细信息，并提交产品/服务进行认证和审阅。 可以预览产品/服务，查看其在应用商店推出之前的外观。

## <a name="what-about-best-practices"></a>最佳做法是什么？

下面的一些最佳做法有助于 Marketplace 上的卖家获取最大价值。

### <a name="azure-test-drives"></a>Azure 体验版

[Azure 体验版](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/test-drives?page=1)是向潜在客户展示产品，并让他们在购买之前试用的极佳方式。 体验版可帮助提高转换率和发展潜在客户。

提供联系信息后，客户便可访问预生成的体验版。 他们可在真实的应用场景中亲自手动、自行试用产品的主要功能和优势。 还有更好的？ 发布与产品/服务相关的体验版，你可获得多达 36％ 的潜在顾客。

在你加入时，我们会为你提供在云合作伙伴门户发布体验版所需的所有指导。 详细了解 [Azure 体验版](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)。

###<a name="lead-generation"></a>开发潜在顾客

在 Azure Marketplace 上列出产品的一大好处是，对于对你的产品感兴趣或已经部署你的产品的客户，我们会将相关的高质量线索发送给你。 此外，我们会将这些线索直接发送到你的 CRM，以便你可以快速采取行动并跟进，创造更多的商机，达成更多的交易。 销售线索生成的整合需要几分钟时间，并在云合作伙伴门户的发布过程中完成。

### <a name="go-to-market-checklist"></a>上市查检表

详细了解我们的[上市计划](https://partner.microsoft.com/go-to-market/)，帮助组织拓展全球覆盖范围。 还可以利用[合作伙伴营销中心](http://smartpartnermarketing.microsoft.com/isv)的资源。

在推出产品之前，建议执行几个步骤，让应用商店产品快速吸引注意力。 使用此清单确定是否准备就绪：

- 我已通过发表博客文章、发送电子邮件或发行新闻稿，宣布我的产品已在应用商店中推出。
- 在我自己的网站上促销我的产品/服务，引导客户在应用商店中选购我的产品/服务。
- **我已发布体验版**，使客户能够花费片刻时间体验在 Azure 中实际运行产品的情景。
- **我已启用按需商机生成**，每当用户通过单击部署我的应用程序时，我就会收到客户的姓名和联系信息。
- **我已与 Microsoft 的合作伙伴经理（如果有）联系**，让他们帮助我发掘更多商机。

## <a name="what-about-reports"></a>如何使用报告？

Marketplace 会提供与订单、使用情况和客户相关的报告，可通过云合作伙伴门户的“见解”部分访问这些报告。 除了有帮助的见解和分析外，可导航的表格中提供了原始数据，且此数据可下载为 CSV 或 XLS 文件。

“见解”的一些功能和好处包括：

- 报告类型：“见解”主页上提供订单、使用情况、部署和客户趋势的摘要快照。
- 详细的订单、使用情况和客户数据。
- 以每月摘要或六个月趋势视图形式显示的订单和使用情况。
- 按以下要素列出的用途/订单信息：
  - 市场
  - 通道
  - 潮流产品
  - 应用商店许可证类型

详细报告会显示客户信息（例如公司名称、地理位置，甚至邮政编码），因此你可比较客户，为卖家提供补偿。 以下列表包含我们提供的有关客户的特定属性：

- Reseller
- FirstName
- LastName
- 电子邮件
- CompanyName
- TransactionDate
- SubscriptionName
- AzureSubscriptionId（仅适用于 PAYG 客户）
- CloudInstanceName
- OrderCount
- CustomerCountryRegion
- CustomerCity
- CustomerCommunicationCulture
- CustomerZipCode

我们还通过帮助文档、词汇表和录制的演示提供培训。 如果需要有关报告的帮助或支持，可随时通过云合作伙伴门户开具支持票证。

欢迎加入我们的 ISV 卖方社区，期待看到产品/服务。

---
