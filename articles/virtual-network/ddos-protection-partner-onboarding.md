---
title: 与 Azure DDoS 保护标准的合作
description: 了解 Azure DDoS 保护标准启用的合作机会。
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849677"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>与 Azure DDoS 保护标准的合作
本文介绍 Azure DDoS 保护标准启用的合作机会。 本文旨在帮助产品经理和业务开发角色理解投资途径，并深入了解合作价值主张。

## <a name="background"></a>背景
分布式拒绝服务（DDoS）攻击是客户将应用程序移动到云的最重要的可用性和安全性问题之一。 由于 extortion 和黑客主义是 DDoS 攻击的常见动机，因此它们在类型、规模和发生频率方面一直在不断提高，因为它们相对简单且价格低廉。

Azure DDoS 防护针对最复杂的 DDoS 威胁提供对策，利用 Azure 网络的全球规模。 该服务为虚拟网络中部署的应用程序和资源提供增强的 DDoS 缓解功能。

技术合作伙伴可以通过 Azure DDoS 保护标准来保护其客户的资源，以解决 DDoS 攻击导致的可用性和可靠性问题。

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS 保护标准简介
Azure DDoS 保护标准针对第3层和第4层 DDoS 攻击提供增强的 DDoS 缓解功能。 下面是 DDoS 保护标准服务的主要功能。

### <a name="adaptive-real-time-tuning"></a>自适应实时优化
对于每个受保护的应用程序，Azure DDoS 防护标准会根据应用程序的流量配置文件模式自动调整 DDoS 缓解策略阈值。 该服务使用两项见解来实现这种自定义：

- 自动学习每个客户（每个 IP）的第 3 层和第 4 层流量模式。
- 尽量减少误报，因为 Azure 的规模可让它吸收大量的流量。

![自适应实时优化](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>攻击分析、遥测、监视和警报
Azure DDoS 防护识别并缓解 DDoS 攻击，无需任何用户干预。

- 如果受保护的资源位于 Azure 安全中心下的订阅中，则每当检测到 DDoS 攻击并针对受保护的应用程序缓解时，DDoS 保护标准会自动将警报发送到安全中心。
- 或者，若要在受保护的公共 IP 存在活动缓解措施时获得通知，可以在 "DDoS 攻击" 下针对指标[配置警报](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics)。
- 此外，还可以选择为其他 DDoS 指标创建警报，并[配置攻击分析](manage-ddos-protection.md#configure-ddos-attack-analytics)，以了解攻击的规模、丢弃的流量、攻击媒介、顶级参与者以及其他详细信息。

![DDoS 指标](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS 快速响应（DRR）
DDoS 保护标准客户在主动攻击期间有权访问[快速响应团队](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/)。 DRR 可帮助进行攻击调查、攻击过程中的自定义缓解和攻击后的分析。

### <a name="sla-guarantee-and-cost-protection"></a>SLA 保证和成本保护
DDoS 保护标准服务涵盖了99.99% 的 SLA，且成本保护提供了在记录的攻击中横向扩展的资源信用额度。 有关详细信息，请参阅[Azure DDoS 保护的 SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)。

## <a name="featured-partner-scenarios"></a>特色合作伙伴方案
下面是与 Azure DDoS 保护标准集成的主要优点：

- 合作伙伴向其客户提供的服务（负载均衡器、web 应用程序防火墙、防火墙等）通过后端的 Azure DDoS 防护标准自动保护（标有白色）。
- 合作伙伴有权访问 Azure DDoS 防护标准攻击分析和遥测，它们可以与自己的产品集成，提供统一的客户体验。  
- 即使在没有 Azure 快速响应的情况下，合作伙伴也可以访问 DDoS 相关问题。
- 合作伙伴受保护的应用程序受 DDoS SLA 保证和在出现 DDoS 攻击时的成本保护的支持。

## <a name="technical-integration-overview"></a>技术集成概述
Azure DDoS 保护标准合作机会可通过 Azure 门户、Api 和 CLI/PS 获得。

### <a name="integrate-with-ddos-protection-standard"></a>与 DDoS 保护标准集成
若要配置与 Azure DDoS 保护标准的集成，需要执行以下步骤：
1. 在所需的（合作伙伴）订阅中创建 DDoS 保护计划。 有关分步说明，请参阅[创建 DDoS 标准保护计划](manage-ddos-protection.md#create-a-ddos-protection-plan)。
   > [!NOTE]
   > 对于给定租户，只需创建一个 DDoS 保护计划。 
2. 使用（合作伙伴）订阅中的公共终结点（例如负载均衡器、防火墙和 web 应用程序防火墙）来部署服务。 
3. 使用第一步中创建的 DDoS 防护计划在具有公共终结点的服务的虚拟网络上启用 Azure DDoS 保护标准。 有关 stpe 的详细说明，请参阅[启用 DDoS 标准保护计划](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > 在虚拟网络上启用 Azure DDoS 保护标准后，该虚拟网络中的所有公共 Ip 都将自动受到保护。 这些公共 Ip 的来源可以是 Azure 中的 Azure （客户端订阅），也可以是 Azure 外部的。 
4. （可选）在特定于应用程序的面向客户的仪表板中集成 Azure DDoS 保护标准遥测和攻击分析。 有关使用遥测的详细信息，请参阅[使用 DDoS 保护遥测](manage-ddos-protection.md#use-ddos-protection-telemetry)。 有关配置攻击分析的详细信息，请参阅[配置 DDoS 攻击分析](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>载入指南和技术文档

- [Azure DDoS 防护产品页](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS 防护文档](ddos-protection-overview.md)
- [Azure DDoS 保护 API 参考](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 虚拟网络 API 参考](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>获取帮助

- 如果对使用 Azure DDoS 保护标准的应用程序、服务或产品集成有疑问，请联系[azure 安全社区](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)。
- 跟踪[Stack Overflow](https://stackoverflow.com/tags/azure-ddos/)上的讨论。

### <a name="get-to-market"></a>进入市场

- 与 Microsoft 合作的主要程序是[Microsoft 合作伙伴网络](https://partner.microsoft.com/)。 – Microsoft Graph 安全集成属于[MPN 独立软件供应商（ISV）](https://partner.microsoft.com/saas-solution-guide) 。
- [Microsoft 智能安全关联](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1)是专门为 Microsoft 安全合作伙伴提供的程序，旨在帮助用户丰富安全产品并改善客户对 microsoft 安全产品的集成。

## <a name="next-steps"></a>后续步骤
查看现有合作伙伴集成：

- [Barracuda WAF 服务](https://www.barracuda.com/waf-as-a-service)
- [来自 Radware 的 Azure 云 WAF](https://www.radware.com/resources/microsoft-azure/)
