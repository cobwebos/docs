---
title: 关于 Azure 快速路由快速路径
description: 了解 Azure 快速路由快速路径，绕过网关发送网络流量
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282845"
---
# <a name="about-expressroute-fastpath"></a>关于 ExpressRoute FastPath

ExpressRoute虚拟网络网关旨在交换网络路由和路由网络流量。 FastPath 旨在提高本地网络和虚拟网络之间的数据路径性能。 启用后，FastPath 会绕过网关将网络流量直接发送到虚拟网络中的虚拟机。

## <a name="requirements"></a>要求

### <a name="circuits"></a>电路

快速路径在所有快速路由电路上都可用。

### <a name="gateways"></a>网关

FastPath 仍然需要创建虚拟网络网关来交换虚拟网络和本地网络之间的路由。 有关虚拟网络网关和 ExpressRoute 的详细信息（包括性能信息和网关 SKU），请参阅[ExpressRoute 虚拟网络网关](expressroute-about-virtual-network-gateways.md)。

要配置 FastPath，虚拟网络网关必须为：

* 超高性能
* ErGw3AZ

## <a name="supported-features"></a>支持的功能

虽然 FastPath 支持大多数配置，但它不支持以下功能：

* 网关子网上的 UDR：如果将 UDR 应用于虚拟网络的网关子网，则来自本地网络的网络流量将继续发送到虚拟网络网关。

* VNet 对等互连：如果其他虚拟网络与连接到 ExpressRoute 的网络对等，则从本地网络到其他虚拟网络（即所谓的"分支"VNet）的网络流量将继续发送到虚拟网络网关。 解决方法是将所有虚拟网络直接连接到 ExpressRoute 电路。

* 基本负载均衡器：如果在虚拟网络中部署基本内部负载均衡器或在虚拟网络中部署的 Azure PaaS 服务使用基本内部负载均衡器，则从本地网络到托管在基本负载均衡器将发送到虚拟网络网关。 解决方案是将基本负载均衡器升级到[标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

* 专用链路：如果从本地网络连接到虚拟网络中的[专用终结点](../private-link/private-link-overview.md)，则连接将经过虚拟网络网关。
 
## <a name="next-steps"></a>后续步骤

要启用 FastPath，请参阅[将虚拟网络链接到 ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。
