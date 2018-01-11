---
title: "Azure DDoS 保护标准概述 | Microsoft Docs"
description: "了解 Azure DDoS 保护服务。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 6b15be022ba3b8373cfb852be8fc6915824801dc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS 保护标准概述

分布式拒绝服务 (DDoS) 攻击是将应用程序移动到云的客户所面临的最大的可用性和安全性问题之一。 DDoS 攻击尝试耗尽应用程序的资源，使应用程序对于合法用户不可用。 DDoS 攻击可能会将任何可通过 Internet 公开访问的终结点作为目标。

Azure DDoS 保护与应用程序设计最佳做法相结合，可提供对 DDoS 攻击的防御。 Azure DDos 保护提供了以下服务层： 

- **基本 Azure DDoS 保护**：在 Azure 平台中自动启用，无需额外付费。 始终可用的流量监控和常见网络级别攻击的实时风险缓解提供了 Microsoft 联机服务所利用的相同防御。 整个 Azure 全球网络的规模可用于跨区域分散和缓解攻击流量。 为 IPv4 和 IPv6 Azure [公共 IP 地址](virtual-network-public-ip-address.md)提供保护。
- **标准 Azure DDoS 保护**：提供专门针对 Azure 虚拟网络资源优化的其他缓解功能。 它易于启用，无需更改应用程序。 保护策略通过专用的流量监控和机器学习算法进行优化，并应用到与虚拟网络中部署的资源（例如 Azure 负载均衡器、Azure 应用程序网关和 Azure Service Fabric 实例）关联的公共 IP。 可在攻击期间通过 Azure Monitor 视图并针对历史记录获得实时遥测。 可通过[应用程序网关 Web 应用防火墙](https://azure.microsoft.com/services/application-gateway)来添加应用程序层保护。 为 IPv4 Azure [公共 IP 地址](virtual-network-public-ip-address.md)提供保护。 

![Azure DDoS 保护标准](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

> [!IMPORTANT]
> Azure DDoS 保护标准当前为预览版。 保护关联了 Azure 公共 IP 地址的 Azure 资源，例如虚拟机、负载均衡器和应用程序网关。 需要针对该服务进行[注册](http://aka.ms/ddosprotection)，才能为订阅启用标准 DDoS 保护。 注册后，Azure DDoS 团队会与你联系，并指导你完成启用过程。 标准 DDoS 保护仅适用于美国东部、美国东部 2、美国西部、美国中西部、北欧、西欧、日本西部、日本东部、东亚和东南亚区域。 在预览版期间，使用该服务无需付费。

我们建议你在开发、测试或生产环境中试用 DDoS 保护标准。 使用以下资源提供有关体验的反馈：
- [Microsoft Azure 论坛上的 Azure DDoS 保护](https://feedback.azure.com/forums/905032-azure-ddos-protection)。 
- [MSDN 论坛上的 Azure DDoS 保护](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Stack Overflow 上的 Azure DDoS 保护](https://stackoverflow.com/tags/azure-ddos/info)

有关支持问题，可[打开 Azure 支持票证](../azure-supportability/how-to-create-azure-support-request.md)。 虽然 DDoS 保护标准处于预览状态，但会尽力提供支持。

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS 保护标准可缓解的 DDoS 攻击类型

DDoS 保护标准可缓解以下攻击类型：

- **容量耗尽攻击**：攻击的目标是借助大量看似合法的流量涌入网络层。 它包括 UDP 洪水、放大洪水以及其他欺骗性数据包洪水。 标准 DDoS 保护通过自动利用 Azure 的全球网络规模吸收和清理这些潜在的数千兆字节攻击，从而缓解这些攻击。 
- **协议攻击**：这些攻击通过利用第 3 层和第 4 层协议堆栈中的漏洞，使目标无法访问。 它包括 SYN 洪水攻击、反射攻击和其他协议攻击。 标准 DDoS 保护通过与客户端交互来区分恶意流量和合法流量并阻止恶意流量，从而缓解这些攻击。 
- **应用程序层攻击**：这些攻击以 Web 应用程序数据包为目标来中断主机之间的数据传输。 它包括 HTTP 协议冲突、SQL 注入、跨站点脚本和其他第 7 层攻击。 使用 Azure [应用程序网关 Web 应用程序防火墙](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)与标准 DDoS 保护可提供对这些攻击的防御。 

标准 DDoS 保护可保护虚拟网络中的资源，包括与虚拟机、负载均衡器和应用程序网关相关联的公共 IP 地址。 与应用程序网关 Web 应用程序防火墙结合使用时，标准 DDoS 保护可提供从第 3 层到第 7 层的完整缓解功能。

## <a name="ddos-protection-standard-features"></a>DDoS 保护标准功能

![DDoS 功能](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS 保护标准功能包括： 

- **本机平台集成：**以本机方式集成到 Azure 并包含通过 Azure 门户和 PowerShell 完成的配置。 DDoS 保护标准了解你的资源和资源配置。
- **始终可用的流量监控：**应用程序流量模式将全天候受到监控，以寻找 DDoS 攻击的迹象。 将在超出保护策略范围时执行缓解措施。
- **统包保护：**一旦启用 DDoS 保护标准，简化后的配置会立即保护虚拟网络上的所有资源。 要求没有干预或用户定义。 一旦检测到攻击，标准 DDoS 保护会立即自动减轻攻击。
- **自适应优化：**智能流量分析了解不同时段的应用程序流量，并选择和更新最适合服务的配置文件。 当流量随时间变化时，配置文件将进行调整。
- **第 3 层到第 7 层保护：**与应用程序网关配合使用时，提供完整的堆栈 DDoS 保护。
- **广泛的缓解规模：**可以使用全球容量缓解超过 60 种不同攻击类型，从而防止最大的已知 DDoS 攻击。 
- **攻击指标：**可以通过 Azure Monitor 访问每个攻击的汇总指标。
- **攻击警报：**可以使用内置攻击指标在攻击开始和停止时以及攻击持续期间配置警报。 警报会集成到操作软件，如 Microsoft Operations Management Suite、Splunk、Azure 存储、电子邮件和 Azure 门户。
- **成本保证：**记录的 DDoS 攻击的数据传输和应用程序横向扩展服务信用度。

## <a name="ddos-protection-standard-mitigation"></a>DDoS 保护标准缓解

Microsoft DDoS 保护服务可监控实际流量利用率，并不断将其与 DDoS 策略中定义的阈值进行比较。 当超过该流量阈值时，将自动启动 DDoS 缓解。 当流量回到阈值以下时，缓解将移除。

在缓解期间，DDoS 保护服务会重定向发送到受保护资源的流量，并会执行多个检查。 这些检查通常执行以下功能：

- 确保数据包符合 Internet 规范，并且格式正确。
- 与客户端交互，确定该流量是否可能是欺骗性数据包（例如：SYN Auth 或 SYN Cookie，或者通过丢弃数据包，让源重新传输它）。
- 如果没有其他可以执行的强制方法，将对数据包进行速率限制。

DDoS 保护会阻止攻击流量并将剩余流转至预期目的地。 在检测到攻击的几分钟内，会使用 Azure Monitor 指标通知你。 通过在 DDoS 保护标准遥测上配置日志记录，可将日志写入可用选项以供将来分析。 对于 DDoS 保护标准，Azure Monitor 中的指标数据当前会保留 30 天。

我们不建议客户自行模拟 DDoS 攻击。 不过，客户可以使用支持渠道，请求由 Azure 网络服务执行 DDoS 攻击模拟。 工程师会与你联系来详细了解 DDoS 攻击（端口、协议、目标 IP），并安排时间来计划测试。

## <a name="next-steps"></a>后续步骤

- 了解有关使用 [Azure PowerShell](ddos-protection-manage-ps.md) 或 [Azure 门户](ddos-protection-manage-portal.md)来管理 DDoS 保护标准的详细信息。
