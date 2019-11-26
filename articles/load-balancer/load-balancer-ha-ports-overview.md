---
title: Azure 中的高可用性端口概述
titleSuffix: Azure Load Balancer
description: 了解在内部负载均衡器上进行负载均衡的高可用性端口。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: c6529e2585a7fca2d160d093d303afa02e6f9379
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215072"
---
# <a name="high-availability-ports-overview"></a>高可用性端口概述

使用内部负载均衡器时，Azure 标准负载均衡器可帮助同时对所有端口上的 TCP 和 UDP 流进行负载均衡。 

高可用性 (HA) 端口负载均衡规则是在内部标准负载均衡器上配置的负载均衡规则的变体。 可以通过提供单个规则对到达内部标准负载均衡器的所有端口的所有 TCP 和 UDP 流进行负载均衡，来简化负载均衡器的使用。 按流进行负载均衡决策。 此操作基于以下五元组连接：“源 IP 地址”、“源端口”、“目标 IP 地址”、“目标端口”和“协议”

HA 端口负载均衡规则可帮助你实现关键方案，例如虚拟网络中网络虚拟设备 (NVA) 的高可用性和缩放。 当大量端口必须进行负载均衡时，此功能也可以帮助完成。 

将前端和后端端口设置为 **0** 并将协议设置为 **All** 时，将需要配置 HA 端口负载均衡规则。 然后，不管端口号是什么，内部负载均衡器资源都会均衡所有 TCP 和 UDP 流

## <a name="why-use-ha-ports"></a>为何使用 HA 端口？

### <a name="nva"></a>网络虚拟设备

可以使用 NVA 来保护 Azure 工作负荷免受多种类型的安全威胁。 如果在这些方案中使用 NVA，这些设备必须可靠、高度可用且可根据需要横向扩展。

只需将 NVA 实例添加到内部负载均衡器后端池，并配置 HA 端口负载均衡器规则，即可实现这些目标。

对于 NVA HA 方案，HA 端口具有以下优点：
- 可根据实例运行状况探测快速故障转移到正常的实例
- 通过横向扩展到 n 个主动实例来提高性能
- 提供 n 个主动和主动-被动方案
- 无需使用复杂解决方案，例如，使用 Apache ZooKeeper 节点来监视设备

下图显示了中心辐射型虚拟网络部署。 在离开受信任空间之前，辐射使用强制隧道将其流量发送到中心虚拟网络并通过 NVA。 NVA 在采用 HA 端口配置的内部标准负载均衡器后面。 可以处理并相应地转发所有流量。 当如下图所示进行了配置时，HA 端口负载均衡规则还会另外针对入口和出口流量提供流对称。

<a node="diagram"></a>
![包含以 HA 模式部署的 NVA 的中心辐射型虚拟网络的示意图](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> 如果使用 NVA，请咨询其提供商来了解如何最好地使用 HA 端口，以及支持哪些方案。

### <a name="load-balancing-large-numbers-of-ports"></a>对大量端口进行负载均衡

对于需要负载均衡大量端口的应用程序，也可以使用 HA 端口。 可以通过将内部[标准负载均衡器](load-balancer-standard-overview.md)与 HA 端口配合使用来简化这些方案。 单个负载均衡规则可替换多个单独的负载均衡规则（每个端口一个）。

## <a name="region-availability"></a>上市区域

HA 端口功能在所有全局 Azure 区域中均可用。

## <a name="supported-configurations"></a>支持的配置

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>内部标准负载均衡器上的一个非浮动 IP（非直接服务器返回）HA 端口配置

此配置是一个基本 HA 端口配置。 执行以下操作可为单个前端 IP 地址配置 HA 端口负载均衡规则：
1. 配置标准负载均衡器时，请在负载均衡器规则配置中选中“HA 端口”复选框。
2. 对于“浮动 IP”，请选择“禁用”。

进行此配置后，无法为当前负载均衡器资源配置任何其他的负载均衡规则。 并且无法为给定的一组后端实例配置其他的内部负载均衡器资源。

但是，除了此 HA 端口规则外，还可以为后端实例配置公共标准负载均衡器。

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>内部标准负载均衡器上的一个浮动 IP（直接服务器返回）HA 端口配置

同样，可以将负载均衡器配置为将负载均衡规则与具有单个前端的“HA 端口”配合使用，并将“浮动 IP”设置为“启用”。 

使用此配置，可添加更多浮动 IP 负载均衡规则和/或公共负载均衡器。 但是，无法在此配置之上使用非浮动 IP、HA 端口负载均衡配置。

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>内部标准负载均衡器上的多个 HA 端口配置

如果方案需要为同一后端池配置多个 HA 端口前端，则可执行以下操作： 
- 为单个内部标准负载均衡器资源配置多个前端专用 IP 地址。
- 配置多个负载均衡规则，为其中的每个规则选择一个唯一的前端 IP 地址。
- 对于所有负载均衡规则，选择“HA 端口”选项，并将“浮动 IP”设置为“启用”。

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>相同后端实例上具有 HA 端口的内部负载均衡器和公共负载均衡器

可以为后端资源配置一个公共标准负载均衡器资源以及单个具有 HA 端口的内部标准负载均衡器。

>[!NOTE]
>此功能目前可通过 Azure 资源管理器模板（而不是通过 Azure 门户）使用。

## <a name="limitations"></a>限制

- HA 端口负载均衡规则仅适用于内部标准负载均衡器。
- 不支持将 HA 端口负载均衡规则与非 HA 端口负载均衡规则组合使用。
- 现有 IP 片段将由 HA 端口负载均衡规则转发到与第一个数据包相同的目标。  不支持对 UDP 或 TCP 数据包进行 IP 分段。
- HA 端口负载均衡规则不适用于 IPv6。
- 只有使用方式如上方的示意图所示并且使用了 HA 端口负载均衡规则时，才会通过后端实例和单一 NIC（以及单 IP 配置）来支持流对称（主要是针对 NVA 方案）。 任何其他方案中都不提供此功能。 这意味着，两个或多个负载均衡器资源和及其各自的规则都独立做出决策，永远不会进行协调。 请参阅[网络虚拟设备](#nva)的说明和示意图。 如果使用了多个 NIC 或者将 NVA 置于公共负载均衡器与内部负载均衡器之间，则流对称功能不可用。  通过对发往设备 IP 的传入流执行来源 NAT 操作以允许回复到达同一 NVA，也许能够解决此问题。  但是，强烈建议使用单一 NIC，并使用上方示意图中所示的参考体系结构。


## <a name="next-steps"></a>后续步骤

- [在内部标准负载均衡器中配置 HA 端口](load-balancer-configure-ha-ports.md)
- [了解标准负载均衡器](load-balancer-standard-overview.md)
