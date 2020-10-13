---
title: Azure 负载均衡器的出站规则
description: 本文介绍如何通过 Azure 负载均衡器配置出站规则，以控制 internet 流量的出口。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002623"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Azure 负载均衡器的出站规则

出站规则允许配置公共标准负载均衡器出站 SNAT (源网络地址转换) 。 此配置允许你将负载均衡器的公共 IP (作为代理使用) 。

此配置启用：

* IP 伪装
* 简化允许列表。
* 减少用于部署的公共 IP 资源数。

对于出站规则，您对出站 internet 连接具有完全声明性控制。 出站规则允许你根据特定需求缩放和优化此功能。 

仅当后端 VM 没有实例级公共 IP 地址 (ILPIP) 时，才会执行出站规则。

![负载均衡器出站规则](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

对于出站规则，您可以显式定义出站 **SNAT** 行为。

使用出站规则可以控制：

* **哪些虚拟机将被转换为哪些公共 IP 地址。**
     * 后端池 A 使用 IP 地址 A 和 B，后端池 B 使用 IP 地址 C 和 D。
* **如何为出站 SNAT 端口提供。**
     * 后端池 B 是建立出站连接的唯一池，将所有 SNAT 端口向后端池 B 和无连接到后端池 A。
* **为其提供出站转换的协议。**
     * 后端池 B 需要 UDP 端口才能进行出站。 后端池 A 需要 TCP。 为 A 和 UDP 端口指定 B 的 TCP 端口。
* **要用于出站连接空闲超时的持续时间 (4-120 分钟) 。**
     * 如果有长时间运行的与 keepalive 的连接，请为长时间运行的连接保留空闲端口长达120分钟。 假设过时连接被放弃，并在4分钟内针对新连接释放端口 
* **是否在空闲超时后发送 TCP 重置。**
     * 当超时空闲连接时，是否向客户端和服务器发送 TCP RST，以便他们知道是否放弃了流？

## <a name="outbound-rule-definition"></a>出站规则定义

出站规则遵循与负载均衡和入站 NAT 规则相同的熟悉语法：**前端**  +  **参数**  +  **后端池**。 

出站规则为后端池识别的、要转换为前端的所有虚拟机配置出站 NAT。   

参数针对出站 NAT 算法提供更精细的控制。

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>使用多个 IP 地址缩放出站 NAT

前端提供的每个附加 IP 地址都为负载均衡器提供附加的64000临时端口，用作 SNAT 端口。 

使用多个 IP 地址来规划大规模方案。 使用出站规则可减轻 [SNAT 消耗](troubleshoot-outbound-connection.md#snatexhaust)。 

你还可以直接在出站规则中使用[公共 IP 前缀](https://aka.ms/lbpublicipprefix)。 

公共 IP 前缀增加了部署规模。 可以将前缀添加到来源于 Azure 资源的流的允许列表中。 可以在负载均衡器中配置前端 IP 配置，以引用公共 IP 地址前缀。  

负载均衡器控制公共 IP 前缀。 出站规则将自动使用公共 IP 前缀中包含的所有公共 IP 地址进行出站连接。 

公共 IP 前缀中的每个 IP 地址都为负载均衡器的每个 IP 地址提供了额外的64000个临时端口，用作 SNAT 端口。

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 出站流空闲超时和 TCP 重置

出站规则提供一个配置参数用于控制出站流空闲超时，并使该超时符合应用程序的需求。 出站空闲超时默认为 4 分钟。 有关详细信息，请参阅 [配置空闲超时](load-balancer-tcp-idle-timeout.md)。 

负载均衡器的默认行为是在达到出站空闲超时时，无提示地删除流。 `enableTCPReset`参数实现可预测的应用程序行为和控制。 参数指示是否将双向 TCP 重置 (TCP RST) 发送到出站空闲超时的超时。 

查看[在空闲超时时 TCP 重置](https://aka.ms/lbtcpreset)，了解详细信息，包括区域可用性。

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>显式保护和控制出站连接

负载均衡规则提供出站 NAT 的自动编程。 某些方案受益或要求您禁用负载均衡规则对出站 NAT 的自动编程。 通过规则禁用可允许您控制或修改行为。  

可通过两种方式使用此参数：

1. 阻止出站 SNAT 的入站 IP 地址。 禁用负载均衡规则中的出站 SNAT。
  
2. 同时调整用于入站和出站的 IP 地址的出站 **SNAT** 参数。 必须禁用自动出站 NAT 才能控制出站规则。 若要更改用于入站的地址的 SNAT 端口分配，则 `disableOutboundSnat` 必须将参数设置为 true。 

如果尝试重新定义用于入站的 IP 地址，则配置出站规则的操作将失败。  首先禁用负载均衡规则的出站 NAT。

>[!IMPORTANT]
> 如果将此参数设置为 true，但没有任何出站规则来定义出站连接，则虚拟机将不会建立出站连接。  VM或应用程序的某些操作可能依赖于公网连接。 请务必了解方案的依赖关系，并考虑此项更改造成的影响。

有时，VM 创建出站流是不必要的。 可能需要管理哪些目标会接收出站流，或哪些目标开始入站流。 使用 [网络安全组](../virtual-network/security-overview.md) 来管理 VM 达到的目标。 使用 Nsg 管理要启动入站流的公共目标。

将 NSG 应用于负载均衡的 VM 时，需要注意[服务标记](../virtual-network/security-overview.md#service-tags)和[默认安全规则](../virtual-network/security-overview.md#default-security-rules)。 

确保 VM 可以接收来自 Azure 负载均衡器的运行状况探测请求。

如果 NSG 阻止来自 AZURE_LOADBALANCER 默认标记的运行状况探测请求，则 VM 运行状况探测失败，VM 被标记为不可用。 负载均衡器会停止向该 VM 发送新流。

## <a name="limitations"></a>限制

- 每个前端 IP 地址的最大可用临时端口数为 64,000。
- 可配置的出站空闲超时范围为 4 到 120 分钟（240 到 7200 秒）。
- 负载均衡器不支持对出站 NAT 进行 ICMP。
- 出站规则只能应用于 NIC 的主 IP 配置。  不能为 VM 或 NVA 的辅助 IP 创建出站规则。 支持多个 NIC。
- **可用性集中**的所有虚拟机都必须添加到后端池以实现出站连接。 
- 必须将 **虚拟机规模集中** 的所有虚拟机添加到后端池以实现出站连接。

## <a name="next-steps"></a>后续步骤

- 了解有关[Azure 标准负载均衡器](load-balancer-overview.md)的详细信息
- 查看[有关 Azure 负载均衡器](load-balancer-faqs.md)的常见问题

