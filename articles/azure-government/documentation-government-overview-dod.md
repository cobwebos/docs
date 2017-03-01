---
title: "Azure Governmnet DoD 概述 | Microsoft Docs"
description: "这里提供了针对 Azure Government 的应用程序开发使用到的功能和指南对比"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 71e8742d71ce6ebab08988751a886df4df000f8a
ms.openlocfilehash: 4e815c5fce563155f7a747262a3695a5180706e3


---
# <a name="department-of-defense-dod-in-azure-government"></a>Azure 政府版中的国防部 (DoD)
## <a name="overview"></a>概述
Azure 政府版由国防部 (DoD) 实体用于部署各种工作负荷和解决方案，包括 <a href="http://iasecontent.disa.mil/cloud/SRG/index.html">DoD 云计算安全要求指南（版本 1，发行版 2）</a>中涵盖的工作负荷（影响级别 4 (L4) 和影响级别 5 (L5)）。

Azure 政府版是第一个（也是唯一一个）荣获美国国防信息系统局信息影响级别 5 DoD 临时授权的超大规模商用云服务。 此外，专用于美国国防部客户工作负荷的 Azure 政府版区域现已正式推出。

将 DoD 移动到云的关键驱动因素之一是为了使各组织能够专注于各自的任务，尽量减少构建和管理内部 IT 解决方案的干扰。

基于 Azure 政府版的云体系结构允许 DoD 人员专注于任务目标和管理 IT 商品服务，如 SharePoint 以及其他应用程序工作负荷。  这允许重组关键 IT 资源以专注于应用程序部署、分析和网络安全。

Azure 提供的灵活机动性为 DoD 客户提供了巨大好处。 在云中扩展工作负荷比在本地或 DoD 数据中心工作时进行传统硬件和服务采购过程更简单、更快速且更具成本效益。 例如，要采购新的多服务器硬件，即使是针对测试环境，也可能需要几个月，且还需要获得大量资金支出的批准。 相比之下，使用 Azure，现有工作负荷的测试迁移能以经济高效的方式在几周、甚至几天之内完成配置（测试完成时，可关闭该环境，无需持续成本）。

这种灵活性非常重要。 通过移动到 Azure，DoD 客户不只可节约资金，云还会提供新的机会。 例如，要运转一个测试环境以了解新技术，这很容易，可先迁移某应用程序并在 Azure 中测试它，再将它提交到云中的生产部署。 任务所有者可更轻松地探索更多经济高效的选项，且没有任何风险。

安全性是另一个重要方面，尽管所有云部署都需要进行适当的计划以确保服务交付安全可靠，但实际上 Azure 政府版中大部分基于云、且已进行适当配置的工作负荷（最多 L4 工作负荷）会比 DoD 位置和数据中心中的许多传统部署更安全。 这是因为国防机构具有实际保护所有资产的经验和专业知识；但 IT 表层领域提出了不同的挑战。 网络安全领域瞬息万变，需要专业技能按需快速开发和部署应对措施。 Azure 商业和政府平台现均可支持成千上万的客户，这一规模让 Microsoft 可以快速检测不断变化的攻击途径，然后将其资源用于快速开发和实施适当的防御。

## <a name="dod-region-qa"></a>DoD 区域常见问题解答

### <a name="what-are-the-azure-government-dod-regions"></a>什么是 Azure 政府版 DoD 区域？ 
DoD 美国东部和 DoD 美国中部区域是物理隔离的 Microsoft Azure 区域，旨在满足美国国防部 (DoD) 在云计算方面提出的安全要求，专门用于处理根据《DoD 云计算安全要求指南》(SRG) 指定为 DoD 影响级别 5 的数据。   

### <a name="what-is-the-difference-between-azure-government-and-the-azure-government-dod-regions"></a>Azure 政府版与 Azure 政府版 DoD 区域之间有什么差别？ 
Azure 政府版属于美国政府社区云，为联邦、州、当地政府客户、遵守 ITAR 的部落和实体，以及代表这些实体执行工作的解决方案提供商提供服务。 所有 Azure 政府版区域的构建与运营都必须符合 DoD 影响级别 5 数据和 FedRAMP High 标准的安全要求。

Azure 政府版 DoD 区域提供 DoD 客户专用的计算和存储基础结构，旨在支持影响级别 5 数据的物理隔离要求。  

#### <a name="what-is-the-difference-between-impact-level-4-and-impact-level-5-data"></a>影响级别 4 与影响级别 5 数据之间有什么差别？  
影响级别 4 数据属于受控未分类信息 (CUI)，可能包括遵守出口管制的数据、隐私信息、受保护的人身健康信息和其他需要经过明确 CUI 规定的数据（例如，“仅限官方使用”、“执法机密信息”、“机密安全信息”）。

影响级别 5 数据包括要求信息所有者、公共法律或政府监管部门采取必要的更高保护级别的受控未分类信息 (CUI)。  影响级别 5 数据包括未分类的国家安全系统。  《DoD 云计算安全要求指南》第 3 部分提供了有关 SRG 影响级别及其辨别要求和特征的详细信息。  

### <a name="what-data-is-categorized-as-impact-level-5"></a>哪些数据归类为影响级别 5？ 
级别 5 包括要求信息所有者、公共法律或其他政府监管部门采取比级别 4 更高的保护级别的受控未分类信息 (CUI)。 级别 5 还支持未分类的国家安全系统 (NSS)。  此级别包括根据 CNSSI-1253 分类的 NSS 和 CUI 信息，甚至包括中等保密性和中等完整性 (M-M-x) 的信息。

### <a name="what-is-microsoft-doing-differently-to-support-impact-level-5-data"></a>Microsoft 采取了哪些不同的措施来支持影响级别 5 数据？ 
根据定义，影响级别 5 数据只能在确保可将 DoD 客户与非联邦政府租户进行物理隔离的专用基础结构中予以处理。  在设立 DoD 美国东部和 DoD 美国中部区域时，Microsoft 将为 DoD 客户提供一个专用的服务，该服务符合的标准甚至高于 DoD 规定的要求，超过了其他任何超大规模商用云解决方案提供的保护级别和能力。

### <a name="do-these-regions-support-classified-data-requirements"></a>这些区域是否支持分类数据要求？ 
这些 Azure 政府版 DoD 区域仅支持不超过影响级别 5（含）的未分类数据。  影响级别 6 数据定义为分类信息，其级别最高为“机密”。

### <a name="what-organizations-in-the-dod-can-use-the-azure-government-dod-regions"></a>DoD 中的哪些组织可以使用 Azure 政府版 DoD 区域？ 
DoD 美国东部和 DoD 美国中部区域旨在支持美国国防部客户群。  这包括：
* 国防部长办公室
* 参谋长联席会议
* 联合参谋部
* 国防局
* 国防部现场活动机构
* 陆军部
* 海军部（包括美国海军陆战队）
* 空军部
* 美国海岸警卫队
* 联合作战司令部
* 受上述任何获批实体控制或监督的其他办公室、部门、活动机构和指挥部

### <a name="are-the-dod-regions-more-secure"></a>DoD 区域是否更安全？ 
Microsoft 根据当地和国际安全与合规标准运营其所有 Azure 数据中心及支持基础结构 – 在合规性投资和成就上，其商用云平台处于领先地位。  这些新的 DoD 区域提供具体的保障和承诺，符合 DoD SRG 针对云计算规定的要求。

### <a name="why-are-there-multiple-dod-regions"></a>为何要设立多个 DoD 区域？ 
通过设立多个 DoD 区域，Microsoft 使客户能够在多个区域构建解决方案来实现灾难恢复方案，确保业务连续性，满足系统认证要求。  此外，客户可在最靠近其实际位置的地理区域部署解决方案，从而优化性能。

### <a name="are-these-dod-regions-connected-to-the-niprnet"></a>这些 DoD 区域是否与 NIPRNet 连接？ 
DoD 规定，用于 CUI 的商用云服务必须通过云接入点 (CAP) 连接到客户。  因此，Azure DoD 区域已通过与多个地理分散的 CAP 建立冗余连接，来连接到 NIPRNet。  DoD CAP 是网络边界保护系统和监视设备，为 DoD 信息系统网络和服务提供保护。

### <a name="what-does-general-availability-mean"></a>“正式推出”是什么意思？ 
“正式推出”表示 Azure 政府版中的 DoD 区域可用于支持生产工作负荷，区域中部署的、同样已正式推出的所有服务将享受到有经济保障的 SLA。

### <a name="how-does-a-dod-customer-acquire-azure-government-dod-services"></a>DoD 客户如何采购 Azure 政府版 DoD 服务？ 
符合条件的实体可通过 Azure 政府版采用的相同分销渠道采购 Azure 政府版 DoD 服务。  为了遵守 Microsoft 做出的简化云服务采购规划和成本估算的承诺，在正式推出时，Azure 政府版 DoD 区域定价将包含在 Azure 定价计算器中。  Azure 政府版 DoD 服务可以根据需要快速扩展或缩减，因此只需为使用的部分付费。
已在使用 Azure 政府版的企业协议客户不需要在合同方面做出任何修改。  

### <a name="how-are-the-dod-regions-priced"></a>DoD 区域的定价方式是怎样的？ 
DoD 区域服务按区域定价。  也就是说，已通过验证的 DoD 客户的服务成本根据运行工作负荷的 Azure 政府版区域计算。  有关具体的定价信息，请咨询 Microsoft 客户主管。  DoD 区域的定价今后将通过 Azure.com 计算器提供。

### <a name="how-does-a-dod-organization-get-validated-for-the-azure-government-dod-regions"></a>DoD 组织如何通过 Azure 政府版 DoD 区域的验证？ 
若要访问 Azure DoD 区域，客户必须完成预先资格认证过程，以便验证其组织以及 Azure DoD 环境的目标用途。  成功完成预先资格认证过程后，Microsoft 将为组织申请人提供后续说明，指导他们创建订阅、访问环境，以及向组织中的其他成员提供基于角色的访问控制。

### <a name="can-independent-software-vendors-and-solution-providers-building-on-azure-deploy-solutions-in-the-azure-government-dod-regions"></a>立足于 Azure 的独立软件供应商和解决方案提供商是否能够在 Azure 政府版 DoD 区域中部署解决方案？ 
立足于 Azure 使用云服务产品的解决方案提供商可在 Azure 政府版 DoD 区域中运行仅限 DoD 的单租户和多租户解决方案。  这些提供商首先必须通过提供与已获批 DoD 实体签署了合同的书面证据，或者出示获批 DoD 实体的担保函，来证明自己的资质。  在 Azure 政府版 DoD 区域中供应服务的提供商必须在其产品中包含计算机网络防护措施、事件报告方案，以及操作影响级别 5 信息处理解决方案的特选人员的信息。  在《DoD 云计算安全要求指南》中可以找到有关解决方案提供商的更多指引。

### <a name="will-office-365-or-microsoft-dynamics-365-be-a-part-of-this-offering"></a>此产品是否涵盖 Office 365 或 Microsoft Dynamics 365？ 
Microsoft 结合此产品为 DoD 提供 Office 365 服务，用于处理影响级别 5 数据。  Dynamics 365 已计划在将来某个日期通过 Azure DoD 区域提供影响级别 5 服务。

### <a name="how-do-i-connect-to-the-dod-regions-once-i-have-a-subscription"></a>获取订阅后如何连接到 DoD 区域？ 
可通过 Azure 政府版管理门户访问 Azure 政府版 DoD 区域。  部署可用的服务时，批准使用 DoD 区域的 DoD 客户将会看到以可用选项列出的区域。  有关管理 Azure 政府版订阅的一般指导，请参阅 Microsoft 文档。

### <a name="what-services-are-part-of-your-impact-level-5-accreditation-scope"></a>哪些服务属于影响级别 5 的认证范围？ 
Azure 是一个长盛不衰的服务，我们每周都会增加新的服务和功能，涵盖的服务数目正在不断扩大。  有关最新信息，请访问 <a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA">Microsoft 信任中心。


## <a name="next-steps"></a>后续步骤：
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA">Microsoft 信任中心 - DoD 网页</a>

<a href="http://iasecontent.disa.mil/cloud/SRG/index.html">DoD 云计算安全要求指南（版本 1，发行 2）</a>

<a href="https://azure.microsoft.com/en-us/offers/azure-government/">Azure 政府版分销渠道

<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure 政府博客。</a>




<!--HONumber=Jan17_HO3-->


