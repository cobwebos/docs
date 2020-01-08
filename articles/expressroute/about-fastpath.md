---
title: 关于 Azure ExpressRoute FastPath
description: 通过绕过网关了解 Azure ExpressRoute FastPath 发送网络流量
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: c03be46207e7698d0557729c546488412c0cc5dd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437105"
---
# <a name="about-expressroute-fastpath"></a>关于 ExpressRoute FastPath

ExpressRoute 虚拟网络网关旨在交换网络路由和路由网络流量。 FastPath 旨在提高本地网络与虚拟网络之间的数据路径性能。 启用后，FastPath 会将网络流量直接发送到虚拟网络中的虚拟机，绕过网关。

## <a name="requirements"></a>要求

### <a name="circuits"></a>而言

FastPath 在所有 ExpressRoute 线路上都可用。

### <a name="gateways"></a>网关

FastPath 仍要求创建一个虚拟网络网关，以在虚拟网络和本地网络之间交换路由。 有关虚拟网络网关和 ExpressRoute 的详细信息，包括性能信息和网关 Sku，请参阅[ExpressRoute 虚拟网络网关](expressroute-about-virtual-network-gateways.md)。

若要配置 FastPath，虚拟网络网关必须是：

* 超高性能
* ErGw3AZ

#### <a name="aggthroughput"></a>预估性能（按网关 SKU）
下表显示网关类型和估计性能。 此表适用于 Resource Manager 与经典部署模型。

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> 应用程序性能取决于多种因素，例如端到端延迟和应用程序打开的流量数。 表中的数字表示应用程序在理想环境下理论上可达到的上限。
>
>

## <a name="supported-features"></a>支持的功能

尽管 FastPath 支持大多数配置，但它不支持以下功能：

* 网关子网中的 UDR：如果将 UDR 应用到虚拟网络的网关子网，则来自本地网络的网络流量将继续发送到虚拟网络网关。

* VNet 对等互连：如果有其他虚拟网络与连接到 ExpressRoute 的虚拟网络对等互连，则来自本地网络的网络流量与其他虚拟网络（即所谓的 "轮辐" Vnet）将继续发送到虚拟网络关子. 解决方法是直接将所有虚拟网络连接到 ExpressRoute 线路。

* 基本负载均衡器：如果在虚拟网络中部署基本的内部负载均衡器，或者在虚拟网络中部署的 Azure PaaS 服务使用基本的内部负载均衡器，则从本地网络到托管在基本负载均衡器将发送到虚拟网络网关。 解决方案是将基本负载均衡器升级到[标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

* 专用链接：如果从本地网络连接到虚拟网络中的[专用终结点](../private-link/private-link-overview.md)，则连接将通过虚拟网络网关。
 
## <a name="next-steps"></a>后续步骤

若要启用 FastPath，请参阅将[虚拟网络链接到 ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。
