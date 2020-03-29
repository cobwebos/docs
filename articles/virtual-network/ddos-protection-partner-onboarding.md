---
title: 与 Azure DDoS 保护标准合作
description: 了解 Azure DDoS 保护标准启用的合作伙伴商机。
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849677"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>与 Azure DDoS 保护标准合作
本文介绍 Azure DDoS 保护标准启用的合作伙伴机会。 本文旨在帮助产品经理和业务开发角色了解投资路径，并提供对合作伙伴价值主张的洞察。

## <a name="background"></a>背景
分布式拒绝服务 （DDoS） 攻击是客户将其应用程序迁移到云时表达的首要可用性和安全问题之一。 由于敲诈勒索和黑客主义是 DDoS 攻击的常见动机，因此在类型、规模和发生频率方面一直在不断增加，因为它们相对容易且成本较低。

Azure DDoS 保护利用 Azure 网络的全球规模，针对最复杂的 DDoS 威胁提供对策。 该服务为部署在虚拟网络中的应用程序和资源提供了增强的 DDoS 缓解功能。

技术合作伙伴可以使用 Azure DDoS 保护标准从本地保护客户的资源，以解决由于 DDoS 攻击而导致的可用性和可靠性问题。

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS 保护标准简介
Azure DDoS 保护标准针对第 3 层和第 4 层 DDoS 攻击提供了增强的 DDoS 缓解功能。 以下是 DDoS 保护标准服务的主要功能。

### <a name="adaptive-real-time-tuning"></a>自适应实时优化
对于每个受保护的应用程序，Azure DDoS 保护标准会根据应用程序的流量配置文件模式自动调整 DDoS 缓解策略阈值。 该服务使用两项见解来实现这种自定义：

- 自动学习每个客户（每个 IP）的第 3 层和第 4 层流量模式。
- 尽量减少误报，因为 Azure 的规模可让它吸收大量的流量。

![自适应实时调谐](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>攻击分析、遥测、监视和警报
Azure DDoS 保护无需任何用户干预即可识别和缓解 DDoS 攻击。

- 如果受保护的资源位于 Azure 安全中心涵盖的订阅中，则每当检测到 DDoS 攻击并针对受保护的应用程序缓解时，DDoS 保护标准会自动向安全中心发送警报。
- 或者，要在受保护公共 IP 有活动缓解时收到通知，可以在 DDoS 攻击下的指标上[配置警报](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics)。
- 此外，您还可以选择为其他 DDoS 指标创建警报，并[配置攻击分析](manage-ddos-protection.md#configure-ddos-attack-analytics)以了解攻击的规模、丢弃的流量、攻击媒介、顶级贡献者和其他详细信息。

![DDoS 指标](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS 快速响应 （DRR）
DDoS 保护标准客户在主动攻击期间可以访问[快速响应团队](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/)。 DRR 可帮助进行攻击调查、攻击期间的自定义缓解以及攻击后分析。

### <a name="sla-guarantee-and-cost-protection"></a>SLA 保证和成本保护
DDoS 保护标准服务由 99.99% SLA 覆盖，成本保护为在有文档记录的攻击期间横向扩展提供了资源积分。 有关详细信息，请参阅[Azure DDoS 保护 的 SLA。](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)

## <a name="featured-partner-scenarios"></a>精选合作伙伴方案
以下是通过与 Azure DDoS 保护标准集成可以获得的主要优势：

- 合作伙伴提供给其客户的服务（负载均衡器、Web 应用程序防火墙、防火墙等）由后端的 Azure DDoS 保护标准自动保护（白色标记）。
- 合作伙伴可以访问 Azure DDoS 保护标准攻击分析和遥测，他们可以与自己的产品集成，提供统一的客户体验。  
- 合作伙伴可以访问 DDoS 快速响应支持，即使在 Azure 没有快速响应的情况下，也可以访问与 DDoS 相关的问题。
- 合作伙伴的受保护应用程序在 DDoS 攻击时由 DDoS SLA 保证和成本保护提供支持。

## <a name="technical-integration-overview"></a>技术集成概述
Azure DDoS 保护标准合作伙伴机会可通过 Azure 门户、API 和 CLI/PS 提供。

### <a name="integrate-with-ddos-protection-standard"></a>与 DDoS 保护标准集成
合作伙伴需要以下步骤来配置与 Azure DDoS 保护标准的集成：
1. 在所需的（合作伙伴）订阅中创建 DDoS 保护计划。 有关分步说明，请参阅[创建 DDoS 标准保护计划](manage-ddos-protection.md#create-a-ddos-protection-plan)。
   > [!NOTE]
   > 只需为给定租户创建 1 个 DDoS 保护计划。 
2. 在（合作伙伴）订阅中部署具有公共终结点的服务，例如负载均衡器、防火墙和 Web 应用程序防火墙。 
3. 使用第一步中创建的 DDoS 保护计划，在具有公共终结点的服务的虚拟网络上启用 Azure DDoS 保护标准。 有关分步说明，请参阅[启用 DDoS 标准保护计划](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > 在虚拟网络上启用 Azure DDoS 保护标准后，该虚拟网络中的所有公共 IP 将自动受到保护。 这些公共 IP 的源可以是 Azure（客户端订阅）内，也可以是 Azure 之外。 
4. 或者，将 Azure DDoS 保护标准遥测和攻击分析集成到特定于应用程序的面向客户的仪表板中。 有关使用遥测的详细信息，请参阅[使用 DDoS 保护遥测](manage-ddos-protection.md#use-ddos-protection-telemetry)。 有关配置攻击分析的详细信息，请参阅[配置 DDoS 攻击分析](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>入职指南和技术文档

- [Azure DDoS 防护产品页](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS 防护文档](ddos-protection-overview.md)
- [Azure DDoS 保护 API 引用](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 虚拟网络 API 引用](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>获取帮助

- 如果对使用 Azure DDoS 保护标准的应用程序、服务或产品集成有疑问，请联系 Azure[安全社区](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)。
- 关注关于[堆栈溢出](https://stackoverflow.com/tags/azure-ddos/)的讨论。

### <a name="get-to-market"></a>进入市场

- 与微软合作的主要计划是[微软合作伙伴网络](https://partner.microsoft.com/)。 • Microsoft 图形安全集成属于[MPN 独立软件供应商 （ISV）](https://partner.microsoft.com/saas-solution-guide)轨道。
- [Microsoft 智能安全协会](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1)是专门针对 Microsoft 安全合作伙伴的计划，旨在帮助您丰富安全产品并提高客户对 Microsoft 安全产品集成的可发现性。

## <a name="next-steps"></a>后续步骤
查看现有合作伙伴集成：

- [梭子鱼 WAF 服务](https://www.barracuda.com/waf-as-a-service)
- [来自 Radware 的 Azure 云 WAF](https://www.radware.com/resources/microsoft-azure/)
