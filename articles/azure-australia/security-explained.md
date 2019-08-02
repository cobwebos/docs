---
title: Azure 澳大利亚安全性介绍
description: 有关澳大利亚地区和满足澳大利亚政府政策、法规和立法特定要求的信息。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 212d14e31c152e50c216f2f34fb225c29fe054b5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571427"
---
# <a name="azure-australia-security-explained"></a>Azure 澳大利亚安全性介绍

本文介绍了澳大利亚政府机构调查、设计和部署到 Microsoft Azure 澳大利亚的一些常见问题和感兴趣的领域。

## <a name="irap-and-certified-cloud-services-list-ccsl-documents"></a>IRAP 和认证的云服务列表 (CCSL) 文档

在将服务添加到 CCSL 时, 澳大利亚网络安全中心 (ACSC) 提供了一个证书信、一个认证报告和一个使用者指南。

Microsoft 目前在 CCSL for Azure、Office 365 和 Dynamics 365 CRM 上列出。

Microsoft 在[Microsoft 服务信任门户](https://aka.ms/au-irap)的澳大利亚特定页面上向客户和合作伙伴提供我们的审核、评估和 ACSC 认证文档。

## <a name="dissemination-limiting-markers-dlm-and-protected-certification"></a>传播限制标记 (DLM) 和受保护的证书

使系统 (包括云服务) 批准供政府组织使用的过程是在澳大利亚网络安全中心 (ACSC) 生成和发布的[信息安全手册 (ISM)](https://acsc.gov.au/infosec/ism/)中定义的。 澳大利亚网络安全中心 (ACSC) 是你的网络中的实体, 负责网络安全和云认证。

审批过程有两个步骤:

1. 安全评估 (IRAP)-一种过程, 注册的专业人员根据 ISM 中的技术控制评估系统、服务和解决方案, 并评估控件是否已有效实现。 评估还会在发布批准操作 (ATO) 之前, 确定批准机构要考虑的任何特定风险。

1. 批准操作–政府机构的高级官员正式将并接受系统对其处理、商店和交流信息的残留风险的过程。  此过程的输入是安全评估。

对于受保护级别的 Azure 服务评估, 确定存储和处理受保护的数据和以下数据所需的安全控制措施是否已到位并有效地运行。

## <a name="australian-data-classification-changes"></a>澳大利亚数据分类更改

2018年10月1日公开了对保护安全策略框架 (PSPF) 的更改, 特别是一种新的[敏感和保密的信息系统](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx)。

![修改后的 PSPF 分类](media/pspf-classifications.png)

在 PSPF 下运行的所有澳大利亚机构和组织都受这些更改的影响。 影响当前 IRAP/CCSL 认证的主要变化是当前的传播限制标记 (DLM) 已停用。 标记**官方:"** 敏感" 替换用于保护敏感信息的各种 DLMs。 此更改还引入了三种信息管理标记, 这些标记可应用于所有敏感度和分类级别的官方信息。 **受保护**的分类保持不变。

术语 "未分类" 将从新系统中删除, 而 "非政府版" 这一术语适用于非政府信息, 尽管它不需要正式标记。

## <a name="choosing-an-azure-region-for-my-official-sensitive-and-protected-workloads"></a>为我的官方选择 Azure 区域:敏感和受保护的工作负荷

Azure **官方:敏感**和**受保护**的认证服务部署到所有四个澳大利亚的数据中心区域 (澳大利亚东部、澳大利亚东部、澳大利亚中部和澳大利亚中部 2); 但是, 证书报告由如果服务可用, ACSC 建议将**受保护**的数据部署到堪培拉中的 Azure 政府区域。 有关**受保护**认证的 Azure 服务的更多详细信息, 请访问有关[STP 的澳大利亚页面](https://aka.ms/au-irap)。

>[!NOTE]
>Microsoft 建议将所有区分和分类的政府数据部署到澳大利亚中部和澳大利亚中部2区域, 因为它们专门满足政府的需求。

[Azure 澳大利亚中部区域](https://azure.microsoft.com/global-infrastructure/australia/)提供有关 Azure 澳大利亚地区的特殊特性的详细信息。

## <a name="how-microsoft-separates-classified-and-official-data"></a>Microsoft 如何分隔已分类和正式的数据

Microsoft 在澳大利亚运营 Azure 和 Office 365, 就像所有数据都是敏感和/或分类的一样, 将安全控制提升到该高条形。

支持 Azure 的基础结构可能提供多个分类的数据。  但正如我们的假设, 对客户数据进行分类后, 相应的控件就是这样。 对于我们的服务, Microsoft 采用了一种基准安全状态, 符合存储和处理**受保护**的保密信息的 PSPF 要求。

为了确保客户在 Azure 中有一个租户与其他租户不存在风险, Microsoft 实现了全面的深层防御控制。

除了在 Microsoft Azure 平台中实现的功能以外, 其他客户可配置的控制 (如使用客户管理的密钥、嵌套虚拟化以及实时管理访问等) 可以进一步降低风险。 在堪培拉的 Azure 政府版澳大利亚区域内, 正式允许列表的流程仅适用于澳大利亚 & 新西兰政府和国家关键基础结构组织。 此社区云为对 cotenant 风险敏感的组织提供额外的保障。

Microsoft Azure**受保护**的证书报表确认这些控件对于存储和处理**受保护**的分类数据及其隔离都有效。

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>与州政府和关键基础结构提供商的 IRAP/CCSL 的相关性

许多州政府和关键基础结构提供商将联邦政府要求纳入到其安全策略和保障框架中。 这些组织还处理**官方**的**官方:敏感**和一定数量的**受保护**的分类数据, 无论它们是与联邦政府的交互, 还是自己的权利。

澳大利亚政府越来越关注于对非政府数据的保护, 这一法规在本质上会影响澳大利亚的安全性和经济兴盛。 因此, Azure 澳大利亚地区和 CCSL 认证与所有这些行业相关。

![关键基础结构扇区](media/nci-sectors.png)

Microsoft 认证表明, Azure 服务已对安全保护进行了全面、严格且正式的评估, 并已批准处理此类高度敏感的数据。

## <a name="location-and-control-of-microsoft-data-centres"></a>Microsoft 数据中心的位置和控制

这是政府和关键基础结构的必需要求, 可明确了解用于处理其数据的云服务的数据中心位置和所有权。  Microsoft 在提供有关这些位置和所有权的广泛信息的超大规模云提供商中是唯一的。

Microsoft 的 Azure 澳大利亚地区 (澳大利亚中部和澳大利亚中部 2) 在 CDC 数据中心的设施内操作。  CDC 数据中心的所有权是澳大利亚控制的, 其中包含来自美国联邦 Superannuation 公司的 48% 的所有权, 从 Infratil 获得 48% 的所有权 (基于和 4% 的澳大利亚管理。  

CDC 数据中心的管理与澳大利亚政府限制将来转让所有权和控制的方式相符。 通过 Microsoft 与 CDC 数据中心的合作关系提供供应链和所有权的这一透明性, 这与[整个政府托管策略](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy)和**认证主权数据中心**定义的原理有关。

## <a name="the-azure-services-that-are-included-in-the-current-ccsl-certification"></a>当前 CCSL 认证中包含的 Azure 服务

2017年6月, ACSC 认证 41 Azure 服务用于存储和处理非**分类数据:DLM**级别。 2018年4月, 其中24项服务已针对**受保护**的分类数据进行认证。

在澳大利亚的 Azure 区域中 ACSC 认证的 Azure 服务的可用性如下 (以粗体显示**的):**

|Azure 澳大利亚中部区域|非区域服务和其他区域|
|---|---|
|API 管理, 应用网关, 应用程序服务,**自动化, Azure 门户, 备份, 批处理, 云服务**, Cosmos DB, 事件中心, **ExpressRoute**, HDInsight, **Key Vault**, 负载均衡器, Log Analytics,**多因素身份验证**、Redis 缓存、**资源管理器、服务总线、Service Fabric、SITE RECOVERY、SQL 数据库、存储**、流量管理器、**虚拟机、虚拟网络、VPN 网关**|**Azure Active Directory**, CDN, 数据目录,**导入导出, 信息保护, IOT 中心**, 机器学习, 媒体服务,**通知中心**, Power BI,**安全中心, 计划程序**, 搜索, 流分析|
|

Microsoft 公布了[Microsoft Azure 合规性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf), 其中列出了 Azure 的所有全球、政府、行业和区域合规性和评估流程的所有范围内服务, 包括 IRAP/CCSL。

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-i-need"></a>Azure 服务未在较低的级别上列出或评估

未经认证或已在**官方认证的服务:机密**但不**受保护**, 可以与托管**受保护**数据的解决方案的一部分一起使用, 前提是服务是:
1. 未加密存储或处理**受保护**的数据, 或
1. 您已经完成了风险评估, 并批准了该服务自行存储**受保护**的数据。

如果要使用不在 CCSL 中包含的服务来存储和处理**官方**数据, 则可以这样做, 但 ISM 要求您在使用云服务提供商输入或续订合同之前, 通知 ACSC 正在编写这种情况。

用于**受保护**工作负荷的机构所使用的任何服务仍需进行安全评估, 并与[DTA 安全云策略](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf)中的 ISM 和代理管理的 IRAP 评估过程中所述的过程进行了批准。

![DTA 安全的云策略认证过程](media/certification.png)

Microsoft 会持续评估我们的服务, 以确保该平台是安全的, 并且适用于澳大利亚政府用途, 因此, 如果您需要对当前不在**受保护**的 CCSL 上的服务的帮助, 请与 Microsoft 联系。

由于 Microsoft 在 CCSL 上提供了一系列服务, 同时在 "未**分类 DLM** " 和 "**受保护**" 分类上进行了认证, 因此, ISM 要求我们至少每两年执行一次 IRAP 的服务评估。 Microsoft undertakes 一年的评估, 这也是包括其他服务以进行考虑的机会。

## <a name="certified-protected-gateway-in-azure"></a>Azure 中经过认证的受保护网关

由于对网关合并计划下允许的 SIGs 数量存在限制, Microsoft 不会对政府认证的安全 Internet 网关 (SIG) 进行操作。  但可以在 Microsoft Azure 中配置 SIG 的预期和必要的功能。

通过 Azure 服务的**受保护**的证书, ACSC 对连接到 Azure 的机构提供特定建议, 并在安全域之间实现网络分段 (例如, 在**受保护**的和互联. 这些建议包括使用网络安全组、防火墙和虚拟专用网络。  ACSC 建议使用虚拟网关设备。 Azure 中有几个虚拟设备, 这些虚拟设备在 ".ASD 评估的产品" 列表上具有物理等效项, 或者根据通用标准保护配置文件进行评估, 并在通用标准门户中列出。 这些产品被可识别与通用标准识别方式 (CCRA) 的签字人相互排斥。

Microsoft 制定了有关实现基于 Azure 的功能的指南, 这些功能提供保护不同安全域之间的边界所需的安全功能, 这些功能结合在一起, 形成与认证的 SIG 等效的安全功能。 有很多合作伙伴可帮助设计和实现这些功能, 以及可实现相同操作的许多合作伙伴解决方案。

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Microsoft 支持人员的安全证明和公民

Microsoft 通过屏蔽式和训练有素的安全人员全球运营我们的服务。  对悉尼和墨尔本中的设施进行 unescorted 物理访问的人员具有澳大利亚政府基准安全证明。 澳大利亚中部和澳大利亚中部2地区的人员具有最小负审核 1 (NV1) 的间隙 (适用于**机密**数据)。 这为客户提供了额外的保障, 即运营 Azure 的数据中心内的人员非常可靠。

Microsoft 拥有一个零拥有访问策略, 该策略的访问权限是通过基于角色的访问控制, 通过一种基于角色的实时和足够的管理授予的。 在绝大多数情况下, 我们的管理员不需要对客户数据的访问权限或特权即可排除和维护服务。  用于远程执行的任务的高度自动化和脚本消除了直接访问客户数据的需要。

律师-一般的部门已确认 Microsoft 在 Azure 中的人员安全策略和过程与 PSPF 访问 INFOSEC-9 中的信息的意图相符。

## <a name="storing-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>存储 Arm 法规 (ITAR) 或出口管理法规 (EAR) 数据的国际流量

Azure 技术控制可帮助客户满足其对导出控制的数据的义务, 这在 Azure 中是全局的。 重要的是, 没有 "ITAR/双耳刻度" 框, 因为对于导出控制的数据没有正式的评估和认证框架。

对于 Azure 政府版和 Office 365 美国政府版防御, 我们已制定了其他合同和流程措施, 以支持客户导出控制。 对于澳大利亚, 这些额外的合同条款和 Azure 区域的有保证的美国国内支持和管理尚未就绪。

这并不意味着, 不能将澳大利亚的 Azure 用于 ITAR/耳, 但你需要清楚地了解通过出口许可证施加的限制, 并且你必须实现其他保护才能在使用 Azure 存储之前满足这些义务这些数据。 例如, 你可能需要将民族作为属性构建到 Azure Active Directory 中, 使用 Azure 信息保护来对数据强制实施加密规则, 并将其限制为仅限美国以及出口许可证上包含的任何其他各族。使用客户密钥或保留你自己的密钥来在 Azure 中存储之前, 使用客户密钥加密所有数据, 并在列表中 ITAR ... ... 。

由于 ITAR 不是正式的认证, 你需要了解与出口许可证关联的限制和限制是什么, 然后可以通过 Azure 中是否有足够的控件来满足这些要求。 在这种情况下, 需要认真考虑的一个问题是, 我们的工程师可能不会在出口许可证上批准民族。

## <a name="next-steps"></a>后续步骤

查看[AZURE VPN 网关](vpn-gateway.md)一文, 了解如何兼容 ISM, 并实现与 azure 澳大利亚的 VPN 连接
