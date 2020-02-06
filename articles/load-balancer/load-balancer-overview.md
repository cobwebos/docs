---
title: 什么是 Azure 负载均衡器？
titleSuffix: Azure Load Balancer
description: Azure 负载均衡器功能、体系结构和实现概述。 了解负载均衡器的工作原理，以及如何在云中使用它。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 376741005f0755d01c95baad8d3a3d33e9952933
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023899"
---
# <a name="what-is-azure-load-balancer"></a>什么是 Azure 负载均衡器？

“负载均衡”是指在一组后端资源或服务器之间均衡地分配负载（传入网络流量）。  

Azure 负载均衡器在开放式系统互连 (OSI) 模型的第四层上运行。 它是客户端的单一联系点。 负载均衡器将抵达负载均衡器前端的入站流量分配到后端池实例。 这些流取决于所配置的负载均衡规则和运行状况探测。 后端池实例可以是 Azure 虚拟机，或虚拟机规模集中的实例。

**[公共负载均衡器](./concepts-limitations.md#publicloadbalancer)** 可以为虚拟网络中的虚拟机 (VM) 提供出站连接。 可以通过将专用 IP 地址转换为公共 IP 地址来实现这些连接。 公共负载均衡器用于对传入 VM 的 Internet 流量进行负载均衡。

**[内部（或专用）负载平衡器](./concepts-limitations.md#internalloadbalancer)** 用于仅在前端需要专用 IP 的情况。 内部负载均衡器用于对虚拟网络内部的流量进行负载均衡。 负载均衡器前端可以在混合方案中从本地网络进行访问。

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

图：*使用公共和内部负载均衡器对多层应用程序进行均衡*

有关各个负载均衡器组件的详细信息，请参阅 [Azure 负载均衡器组件和限制](./concepts-limitations.md)

>[!NOTE]
> Azure 为方案提供了一套完全托管的负载均衡解决方案。 如需高性能、低延迟的 4 层负载均衡，请参阅[什么是 Azure 负载均衡器？](../load-balancer/load-balancer-overview.md) 如果正在查找全局 DNS 负载均衡，请查看[什么是流量管理器？](../traffic-manager/traffic-manager-overview.md) 端到端场景可从结合这些解决方案中受益。
>
> 有关 Azure 负载平衡选项的比较，请参阅 [Overview of load-balancing options in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)（Azure 中的负载平衡选项概述）。

## <a name="why-use-azure-load-balancer"></a>为什么使用 Azure 负载均衡器？
使用标准负载均衡器，你可以缩放应用程序，并创建高度可用的服务。 负载均衡器支持入站和出站方案。 负载均衡器提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。

可以使用标准负载均衡器完成的关键方案包括：

- 对发往 Azure 虚拟机的 **[内部](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** 和 **[外部](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** 流量进行负载均衡。

- 通过在区域 **[内部](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** 和区域 **[之间](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** 分配资源，提高可用性。

- 配置 Azure 虚拟机的 **[出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** 。

- 使用 **[运行状况探测](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** 来监视已实现负载均衡的资源。

- 使用 **[端口转发](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** 通过公共 IP 地址和端口访问虚拟网络中的虚拟机。

- 启用对 **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** **[负载均衡](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** 的支持。

- 标准负载均衡器通过 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 提供多维度指标。  可以就给定维度对这些指标进行筛选、分组和细分。  可便于深入了解服务的当前及历史性能和运行状况。  还支持资源运行状况。 有关更多详细信息，请查看 **[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)** 。

- 对 **[多个端口和/或多个 IP 地址](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** 上的服务进行负载均衡。

- 跨 Azure 区域移动 **[内部](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** 和 **[外部](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** 负载均衡器资源。

- 使用 **[HA 端口](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** ，同时对所有端口上的 TCP 和 UDP 流进行负载均衡。

### <a name="securebydefault"></a>默认保护

标准负载均衡器的核心是零信任网络安全模型。 标准负载均衡器默认情况下保护你的虚拟网络并且是虚拟网络的一部分。 虚拟网络是一个专用的隔离网络。  这意味着除非由网络安全组打开，否则，标准负载均衡器和标准公用 IP 地址将对入站流关闭。 NSG 用来显式允许以及将允许的流量加入允许列表。  如果虚拟机资源的子网或 NIC 上没有 NSG，禁止流量到达此资源。 若要详细了解 NSG 以及如何将其应用于自己的方案，请参阅[网络安全组](../virtual-network/security-overview.md)。
默认情况下，基本负载均衡器对 Internet 是开放的。


## <a name="pricing-and-sla"></a>定价和 SLA

有关标准负载均衡器的定价信息，请参阅[负载均衡器定价](https://azure.microsoft.com/pricing/details/load-balancer/)。
基本负载均衡器是免费提供的。
请参阅[负载均衡器的 SLA](https://aka.ms/lbsla)。 基本负载均衡器没有 SLA。

## <a name="next-steps"></a>后续步骤

请参阅[创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)，开始使用负载均衡器。

有关 Azure 负载均衡器限制和组件的详细信息，请参阅 [Azure 负载均衡器的概念和限制](./concepts-limitations.md)
