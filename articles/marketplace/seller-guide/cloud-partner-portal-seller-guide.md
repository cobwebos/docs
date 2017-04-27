---
title: "Microsoft Azure 应用商店卖方指南 | Microsoft Docs"
description: "本指南适用于想要向 IT 专业人员和开发员销售 Azure 认证虚拟机映像的独立软件供应商 (ISV) 业务用户和产品经理。"
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: aa6ae2b7e6ad0c6f91503d026ec8fd6933b6cfbb
ms.lasthandoff: 04/12/2017


---
# <a name="azure-marketplace-sellers-guide"></a>Azure 应用商店卖方指南

欢迎参阅 Microsoft Azure 应用商店卖方指南。 本指南适用于想要向 IT 专业人员和开发员销售 Azure 认证虚拟机映像的独立软件供应商 (ISV) 业务用户和产品经理。 [Azure 应用商店](https://azuremarketplace.microsoft.com/)能够为世界各地的 Azure 客户提供极佳的产品市场宣传和曝光度。


> [!NOTE]
> 如果你有意向业务用户销售软件即服务 (SaaS) 成品产品，可以研究要使用哪种选项将你的产品列在 [AppSource](https://appsource.microsoft.com) 中。

完成本指南后，你就会知道可在何处找到以下方面的详细信息：

- 什么是 Azure 应用商店？
- 如何确定我的产品是否适合在 Azure 应用商店中销售？
- 在 Azure 应用商店销售产品有哪些优势？
- 在 Azure 应用商店销售产品需要满足哪些先决条件（技术和非技术方面）？
- 如何构建与 Azure 兼容的虚拟硬盘 (VHD)？
- 如何以卖方身份申请和注册？
- 如何创建和发布产品？
- 如何投放市场和查找可用资源？
- Azure 应用商店提供哪些报告和见解？
- 可在何处获得帮助和支持？

让我们开始吧！

## <a name="whats-the-azure-marketplace"></a>什么是 Azure 应用商店？

Azure 应用商店是一个在线应用程序和服务市场，可让各种规模的 ISV（从创业公司到大型企业）向全球 Azure 客户提供解决方案。 Azure 应用商店允许 Azure 发布者向希望快速在 Azure 中开发基于云的应用程序和移动解决方案的其他开发人员、ISV 和 IT 专业人员分发和销售创新的虚拟机映像。 Azure 应用商店支持多种多样的产品，例如，支持数据处理的端到端数据分析应用程序、数据存储、分析层或分层电子商务应用（数据、服务和 Internet）。

云客户在搜索符合独特需求的适当解决方案时面临着多种挑战。 Azure 应用商店提供一种机制来解决这些挑战，可让客户与创新的 ISV 解决方案互动，如下表中所述。

| 客户需求 | Azure 应用商店解决方案 |
| --- | --- |
| 需要使用其他云平台功能来解决业务和技术需求 | 在 Azure 上提供一套不断扩充的互补性应用程序和服务产品组合 |
| 发现很难找到适当的应用程序或服务 | 提供一站式商店让客户发现、搜索和购买应用程序与服务 |
| 需要对第三方应用程序和服务使用可缩放的部署机制 | 允许对第三方应用程序和服务创建和配置可缩放的部署 |
| 需要使用新的应用程序与服务来集成和使用现有解决方案 | 轻松地将第三方应用程序和服务与 Azure 上的现有解决方案集成 |

Azure 应用商店为全球客户打造了优质、多样化和强健的 Azure 合作伙伴生态系统。 主要优势包括：

- 在统一的位置供应 Microsoft 与合作伙伴开发的基于 Azure 的产品
- 产品超过 5,000 个
- 集成的平台体验
- 简化的配置、部署和管理

## <a name="is-the-azure-marketplace-right-for-my-business"></a>Azure 应用商店是否适合我的业务？

此时此刻，你可能会疑惑，Azure 应用商店是否适合你的业务，如果适合，它能够带来哪种优势？ Azure 应用商店能够促成新的商机，让你向客户提供 Azure 上的软件和服务：

- **使用 Azure 上扩展的解决方案产品组合打入新的客户市场**：这是使用 Azure 服务向 Microsoft 在线订阅计划 (MOSP) 和 Microsoft 企业协议客户追加销售和交叉销售应用商店产品的机会。 你可以轻松地将应用商店产品整合到客户解决方案和 Azure 方案推介产品中。
- **使用现有和新的客户帐户提高业务价值和增大交易规模**：Azure 应用商店可以帮助增大交易规模，解决客户将工作负荷转移到云时遇到的难题，并增加交易收益。 简而言之，可以根据客户的要求销售配套的解决方案并解决关键的 Azure 云平台差异，从而提高业务价值。
- **通过销售应用商店应用程序和服务来迎合更广泛潜在客户的需要**：使用 Azure 应用商店可以更容易地寻找和保留新的客户。 当今的许多企业需要将工作负荷转移到云中，适应不断变化的基础结构环境。 你可以提供适当的应用程序和服务来帮助这些企业弥补环境的差异。
- **通过捆绑销售应用商店产品和 Azure 服务来补充和扩展 Azure 功能**：Azure 应用商店可以帮助你与客户建立基于情景的对话，使你们之间能够保持真实、具体的互动。 你还可以探讨双方关心的端到端 (E2E) 解决方案，解决具体的平台差异和客户需求。 最后，通过销售解决方案捆绑包，可以充分利用 Azure 平台生态系统来解决几乎任何客户问题，同时增大销售额。

## <a name="what39s-the-customer-base-for-the-azure-marketplace"></a>Azure 应用商店面向哪种客户群？

Azure 应用商店的客户群体是多样化的。 Azure 是客户群扩充速度最快的云提供程序之一，因此，你将有机会接触到各行业的属于公共和私营单位的无数实体（从创业公司到大型企业）中的 IT 专业人员与开发员。

## <a name="how-does-the-azure-marketplace-work"></a>如何使用 Azure 应用商店？

用法相当简单：你的申请经过批准后，便可以创建 Azure 认证的虚拟机映像并将其发布到 Azure 应用商店，Azure 客户可在其中查找、购买你的产品并在几分钟内完成部署。 更有利的是，客户知道配置的环境可在 Azure 上顺利运行，并且基础结构在几分钟内就能启动，因此，可以放心部署你的解决方案。

云合作伙伴门户 (CPP) 是在 Azure 应用商店中创建产品的中心。 虚拟机映像中已预先配置完整安装的操作系统以及一个或多个应用程序。 若要认证映像使其随时可供发布，必须满足某些先决条件，本指南的下一部分对此做了介绍。


## <a name="whats-next"></a>后续步骤

学到本指南的这个环节时，你可能会问自己：&quot;我觉得 Azure 应用商店_确实_很适合我的产品，但要如何开始使用它呢？&quot; 这就是本部分所要介绍的内容。 下面是有关在 Azure 应用商店中开始销售产品的信息：通过 Azure 的认证，获得销售产品的批准，然后在 CPP 中创建产品（图 1）。

![在 Azure 应用商店中销售产品的过程](./media/cloud-partner-portal-seller-guide/processforselling.png)

图 1：在 Azure 应用商店中销售产品的过程

首先，需要满足一系列技术和非技术先决条件，并准备好虚拟机映像。 然后，指定产品的名称并注册为卖方。 最后，添加市场营销内容并提交发布。 在 Azure 应用商店中推出产品之前，可以先在预览/过渡环境中检查产品。

首次创建要在 Azure 应用商店中销售的产品时，应该花费大约_四周_时间来规划基本的登记手续。 如果可能，请在推出产品之前花费_六周_时间来生成产品，以便有更多的时间可以考虑媒体宣传和发布事项。

## <a name="how-do-i-become-azure-certified"></a>如何通过 Azure 认证？

创建要在 Azure 应用商店中销售的产品的第一个步骤是通过 Azure 认证。 这意味着，需要编写公司信息、同意参与策略、下载必要的工具并构建技术组件（图 2）。

![通过 Azure 认证所要满足的要求](./media/cloud-partner-portal-seller-guide/azurecertified.png)

图 2：通过 Azure 认证所要满足的要求

### <a name="technical-prerequisites"></a>技术先决条件

在推出产品之前，请务必仔细查看并满足所有技术先决条件。 需要访问 Windows 或 Linux，以及链接到测试工具的、与 Azure 兼容的 VHD。

尽管可以在云中或本地开发 Azure VHD，但我们建议使用远程桌面协议直接在云中开发 Azure VHD。 但是，在万不得已的情况下，可以下载一个 VHD 并使用[本地基础结构](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-on-premise)进行开发。

有关详细信息，请参阅[技术先决条件](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)页。

### <a name="non-technical-prerequisites"></a>非技术性先决条件

若要加入 Azure 应用商店，需要满足一些非技术先决条件。 首先，查看并同意 [Azure 应用商店参与策略](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)的条款。 请注意，在 Azure 应用商店中提供的软件和服务必须至少满足以下要求之一：

- **在 Microsoft Azure 上运行**：软件或服务的主要功能必须在 Microsoft Azure 上运行。
- **部署到 Microsoft Azure**：必须在产品列表信息中说明如何在 Microsoft Azure 上部署该软件或服务。
- **集成或扩展 Microsoft Azure 服务**：必须在产品列表信息中指明该软件或服务可以集成或扩展_哪个_ Azure 服务，以及该软件或服务_如何_集成或扩展该 Azure 服务。

此外，还需要满足 Azure 应用商店参与策略中所述的以下业务要求：

- 你的公司（或子公司）必须位于 Azure 应用商店支持的销售国家/地区。
- 产品必须以兼容 Azure 应用商店支持的计费模式的方式获得许可。
- 你需要负责以商业上合理的方式向客户提供技术支持，支持的形式包括免费、付费或社区支持。
- 你需要购买自己的软件和任何第三方软件依赖项的许可证。
- 必须提供满足 [azure.microsoft.com](../../C:/Users/Lisa.Rosenberger/Desktop/azure.microsoft.com) 和 Azure 管理门户中列出的产品准则的内容。

最后，需要同意[使用条款](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft 隐私声明](http://www.microsoft.com/privacystatement/default.aspx)和 [Microsoft Azure Certified 计划协议](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/) 

有关常见问题的列表，请参阅 [Azure 应用商店常见问题解答](https://azure.microsoft.com/marketplace/faq/)。

### <a name="virtual-machine-image-preparation"></a>虚拟机映像准备

查看先决条件并完成所需的任务后，可以根据[虚拟机映像发布指南](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation)中的详述继续创建虚拟机映像产品。

此指南可帮助你准备好要部署到 Azure 应用商店、用作 SKU 基础的 VHD。 

> [!NOTE]
> 此过程各有不同，具体取决于提供的是基于 Linux 还是基于 Windows 的 SKU。

> [!NOTE]
>  此过程可与下面所述的[帐户创建和注册](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)过程一起执行。

### <a name="azure-certification"></a>Azure 认证

获得了“Azure certified”标志即表示登记过程已成功完成。 这种状态可让客户相信其 IT 专业人员和开发员购买的优质解决方案是受信任合作伙伴提供的，可以配合 Azure 技术运行或者是使用这种技术构建的。 Azure 认证的解决方案包括：

- 全球审核
- 确定与 Azure 平台的兼容性
- 联机映像安全符合性
- 无病毒或恶意软件
- 支持的计费模式

## <a name="how-do-i-nominate-my-product-and-get-approved"></a>如何指定产品名称并获得批准？

现在，可以请求批准在 Azure 应用商店中销售你的产品（图 3）。 在 Microsoft 服务中可以轻松指定产品的名称、完成发布过程并注册为卖方。

![获得在 Azure 应用商店中销售产品的批准](./media/cloud-partner-portal-seller-guide/gettingapprovedsteps.png)

图 3：获得在 Azure 应用商店中销售产品的批准

获得批准的第一个步骤是在注册和发布之前为产品[命名](https://createopportunity.azurewebsites.net/)。 请注意，批准_最长需要三个工作日_。

获得批准后，你将收到以下信息：

- 一封电子邮件回执，其中包含一个可用于抵消 99 美元开发中心申请费的促销代码，以及要在 CPP 中填写的档案资料。
- Azure 认证状态的技术预先审批资料，以及创建产品和认证 VHD 的选项。 （只有开发中心申请获得批准后，才能创建产品。）
- 有关发布门户帐户的详细说明，以及发布过程的概述。
- 是否符合致电 Microsoft 登记团队，让他们引导完成登记过程和解决问题的条件。
- 是否能够发布二次产品。 二次产品不需要审批。 可将它们直接加入 CPP，但虚拟机仍必须通过发布过程认证。
- 有关请求发布方面的帮助的指导。 （问题应会定向到应用商店发布者[支持链接](https://support.microsoft.com/getsupport?wf=0&amp;tenant=ClassicCommercial&amp;oaspworkflow=start_1.0.0.0&amp;locale=en-us&amp;supportregion=en-us&amp;pesid=15635&amp;ccsid=636233723471685249)。）

最后，将[你的帐户注册](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)为 Microsoft 卖方。 审批和审核_最长可能需要两周_，请利用这段时间在 CPP 中创建 Azure 应用商店产品。

## <a name="how-do-i-create-my-offer-in-the-cloud-partner-portal"></a>如何在云合作伙伴门户中创建产品？

现在，可以创建产品并认证虚拟机映像了！ 为此，可以使用云合作伙伴门户（图 4）。 可将 CPP 视为[发布和管理解决方案](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-getting-started)的中心。

![通过云合作伙伴门户创建新产品](./media/cloud-partner-portal-seller-guide/newoffersetup.png)

图 4：通过云合作伙伴门户设置新产品

## <a name="what-about-best-practices"></a>最佳做法是什么？

为了帮助你充分利用 Azure 应用商店的卖方身份，我们开发了一些可在短时间内发挥极大作用的工具和最佳做法。

## <a name="azure-test-drives"></a>Azure 体验版

[Azure 体验版](https://azure.microsoft.com/marketplace/test-drives/)是向潜在客户展示产品，并让他们在购买之前试用的极佳方式。 这有助于增加对话，提高成交的机会。

提供联系信息后，客户可以访问预先生成的体验版：自己亲手试用在真实的实施场合中演示的产品主要功能和优势。 因此，Azure 体验版可让你直接面对客户展示产品的作用。

目前，只能在经典发布门户中发布产品的体验版。 导航到[此网页](https://github.com/Azure/AzureTestDrive/wiki)可以找到有关_如何发布新体验版_的文档。

在[此处](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf)详细了解 Azure 体验版。

## <a name="go-to-market-checklist"></a>上市查检表

详细了解我们的[上市计划](https://partner.microsoft.com/go-to-market/)，帮助你的组织拓展全球覆盖范围。 还可以利用[合作伙伴营销中心](http://smartpartnermarketing.microsoft.com/isv)的资源。

在推出产品之前，我们建议执行几个步骤，让你的 Azure 应用商店产品快速吸引注意力。 使用一个快速查检表确定是否已做好产品上市的准备：

- 我已通过发表博客文章、发送电子邮件或发行新闻稿，**宣布我的产品已在 Azure 应用商店中推出**。
- **我将在自己的网站上推广我的产品**，将客户引领到我在 Azure 应用商店中发布的产品，然后，他们可在应用商店中了解、试用、购买和部署该产品。
- **我已发布体验版**，使客户能够花费片刻时间体验在 Azure 中实际运行产品的情景。
- **我已启用按需商机生成**，每当用户通过单击部署我的应用程序时，我就会收到客户的姓名和联系信息。
- **我已与 Microsoft 的合作伙伴经理（如果有）联系**，让他们帮助我发掘更多商机。

## <a name="what-about-reports"></a>如何使用报告？

Azure 应用商店针对订单、用途和客户提供可通过 Azure 应用商店[发布者报告门户](https://reports.azure.com)访问的报告（图 5）。 原始数据在可导航的表中提供，可下载其 CSV 或 XLS 文件。

![通过发布者报告门户访问报告](./media/cloud-partner-portal-seller-guide/accessingreports.png)

图 5：通过发布者报告门户访问报告

此[视频](https://player.vimeo.com/video/200859918)提供了报告功能和优势的概况，其中包括：

- 报告类型：在主页上提供订单、用途和客户趋势的摘要快照
- 详细订单、用途和客户数据
- 以每月摘要或六个月趋势视图形式显示的订单和用途
- 以标准形式显示的多项见解
- 按以下要素列出的用途/订单信息：

-
  - 市场
  - 通道
  - 潮流产品
  - 应用商店许可证类型

详细报告将显示客户信息（例如公司名称、联系信息和地理位置，甚至包括邮政编码），帮助你与其他卖方竞争。 以下列表包含我们提供的有关客户的特定属性：

- Reseller
- FirstName
- LastName
- 电子邮件
- CompanyName
- TransactionDate
- SubscriptionName
- AzureSubscriptionId
- CloudInstanceName
- 订单计数
- CustomerCountryRegion
- CustomerCity
- CustomerCommunicationCulture
- CustomerZipCode

我们还通过帮助文档、词汇表和录制的演示提供培训。 如果在报告方面需要帮助或支持，请开具[支持票证](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=636233723471685249)。

