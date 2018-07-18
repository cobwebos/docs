---
title: Azure DDoS 保护标准概述 | Microsoft Docs
description: 了解 Azure DDoS 保护服务。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: 705f69f9143e3d2b27a3099f340218aaa12931f8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601893"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS 保护标准概述

分布式拒绝服务 (DDoS) 攻击是将应用程序移动到云的客户所面临的一些最大的可用性和安全性问题。 DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。

Azure DDoS 保护与应用程序设计最佳做法相结合，可提供针对 DDoS 攻击的防御。 Azure DDos 保护提供了以下服务层：

- **基本**：作为 Azure 平台的一部分自动启用，无需额外付费。 始终可用的流量监控和常见网络级别攻击的实时风险缓解提供了 Microsoft 联机服务所利用的相同防御。 整个 Azure 全球网络的规模可用于跨区域分散和缓解攻击流量。 为 IPv4 和 IPv6 Azure [公共 IP 地址](virtual-network-public-ip-address.md)提供保护。
- **标准**：在专门针对 Azure 虚拟网络资源优化的基本服务层上提供其他风险缓解功能。 DDoS 保护标准易于启用，无需更改应用程序。 通过专用流量监控和机器学习算法优化保护策略。 策略应用到与部署在虚拟网络中资源相关的公共 IP 地址，例如 Azure 负载均衡器、Azure 应用程序网关和 Azure Service Fabric 实例。 可在攻击期间通过 Azure Monitor 视图并针对历史记录获得实时遥测。 可通过 [Azure 应用程序网关 Web 应用程序防火墙](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来添加应用程序层保护。 为 IPv4 Azure [公共 IP 地址](virtual-network-public-ip-address.md)提供保护。

![Azure DDoS 保护标准](./media/ddos-protection-overview/ddospic.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS 保护标准可缓解的 DDoS 攻击类型

DDoS 保护标准可缓解以下攻击类型：

- **容量耗尽攻击**：攻击的目标是借助大量看似合法的流量涌入网络层。 它包括 UDP 洪水、放大洪水以及其他欺骗性数据包洪水。 借助 Azure 的全球网络规模，DDoS 保护标准通过自动吸收和清理这些潜在的数千兆字节攻击，从而缓解这些攻击。
- **协议攻击**：这些攻击通过利用第 3 层和第 4 层协议堆栈中的漏洞，使目标无法访问。 它包括 SYN 洪水攻击、反射攻击和其他协议攻击。 DDoS 保护标准通过与客户端交互来区分恶意流量和合法流量并阻止恶意流量，从而缓解这些攻击。 
- **资源（应用程序）层攻击**：这些攻击以 Web 应用程序数据包为目标来中断主机之间的数据传输。 这些攻击包括 HTTP 协议冲突、SQL 注入、跨站点脚本和其他第 7 层攻击。 使用 Azure [应用程序网关 Web 应用程序防火墙](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)与标准 DDoS 保护可提供对这些攻击的防御。 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)中还提供了第三方 Web 应用程序防火墙产品。

标准 DDoS 保护可保护虚拟网络中的资源，包括与虚拟机、负载均衡器和应用程序网关相关联的公共 IP 地址。 与应用程序网关 Web 应用程序防火墙结合使用时，标准 DDoS 保护可提供从第 3 层到第 7 层的完整缓解功能。

## <a name="ddos-protection-standard-features"></a>DDoS 保护标准功能

![DDoS 功能](./media/ddos-protection-overview/ddosfeatures.png)

DDoS 保护标准功能包括：

- **本机平台集成：** 本机集成到 Azure 中。 包括通过 Azure 门户进行配置。 DDoS 保护标准了解你的资源和资源配置。
- **统包保护：** 一旦启用 DDoS 保护标准，简化后的配置会立即保护虚拟网络上的所有资源。 要求没有干预或用户定义。 一旦检测到攻击，标准 DDoS 保护会立即自动减轻攻击。
- **始终可用的流量监控：** 应用程序流量模式将全天候受到监控，以寻找 DDoS 攻击的迹象。 将在超出保护策略范围时执行缓解措施。
- **自适应优化：** 智能流量分析了解不同时段的应用程序流量，并选择和更新最适合服务的配置文件。 当流量随时间变化时，配置文件将进行调整。
- **第 3 层到第 7 层保护：** 与 Web 应用程序防火墙配合使用时，提供完整的堆栈 DDoS 保护。
- **广泛的缓解规模：** 可以使用全球容量缓解超过 60 种不同攻击类型，从而防止最大的已知 DDoS 攻击。
- **攻击指标：** 可以通过 Azure Monitor 访问每个攻击的汇总指标。
- **攻击警报：** 可以使用内置攻击指标在攻击开始和停止时以及攻击持续期间配置警报。 警报会集成到操作软件中，如 Microsoft Azure Log Analytics、Splunk、Azure 存储、电子邮件和 Azure 门户。
- **成本保证：** 记录的 DDoS 攻击的数据传输和应用程序横向扩展服务信用度。

## <a name="ddos-protection-standard-mitigation"></a>DDoS 保护标准缓解

DDoS 保护标准监控实际流量利用率，并不断将其与 DDoS 策略中定义的阈值进行比较。 当超过流量阈值时，将自动启动 DDoS 缓解。 当流量回到阈值以下时，缓解将移除。

![缓解措施](./media/ddos-protection-overview/mitigation.png)

在风险缓解期间，DDoS 保护服务重定向发送到受保护资源的流量，并执行多个检查，如以下检查：

- 确保数据包符合 Internet 规范且格式正确。
- 与客户端交互，确定该流量是否可能是欺骗性数据包（例如：SYN Auth 或 SYN Cookie，或者通过丢弃数据包，让源重新传输它）。
- 如果没有其他可以执行的强制方法，将对数据包进行速率限制。

DDoS 保护会阻止攻击流量并将剩余流转至预期目的地。 在检测到攻击的几分钟内，会使用 Azure Monitor 指标通知你。 通过在 DDoS 保护标准遥测上配置日志记录，可将日志写入可用选项以供将来分析。 Azure Monitor 中 DDoS 保护标准的指标数据会保留 30 天。

Mircosoft 已与 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) 合作构建接口，用户可在其中针对已启用 DDoS 保护的公共 IP 地址生成用于模拟的流量。 借助 BreakPoint Cloud 模拟，你可以：

- 验证 Microsoft Azure DDoS 防护标准如何保护 Azure 资源免受 DDoS 攻击
- 受到 DDoS 攻击时优化事件响应过程
- 阐述 DDoS 符合性
- 培训网络安全团队

## <a name="next-steps"></a>后续步骤

- [配置 DDoS 保护标准](manage-ddos-protection.md)