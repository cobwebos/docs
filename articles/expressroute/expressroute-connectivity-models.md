---
title: Azure ExpressRoute：连接模型
description: 查看客户网络、Microsoft Azure 和 Microsoft 365 服务之间的连接。 客户可以使用 MPLS 提供商、云交换和以太网。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978742"
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute 连接模型
你可以通过四种不同的方式在本地网络与 Microsoft 云之间创建连接， [: Cloudexchange 归置](#CloudExchange)、 [点到点以太网连接](#Ethernet)、 [任意到任意 (IPVPN) 连接](#IPVPN)和 [ExpressRoute 直接](#Direct)连接。 连接提供商可以提供一个或多个连接模型。 可以咨询连接服务提供商以选择最合适的模型。
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="ExpressRoute 连接模型示意图":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>共置于云交换位置
如果你在使用云交换的设施中共存，则可以通过共同位置提供商的以太网交换，将虚拟交叉连接订购到 Microsoft 云。 共同租用提供商可以在共置设施中的基础结构与 Microsoft 云之间提供第 2 层交叉连接或托管的第 3 层交叉连接。

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>点到点以太网连接
可以通过点到点以太网链路，将本地数据中心/办公室连接到 Microsoft 云。 点到点以太网提供商可以在站点与 Microsoft 云之间提供第 2 层连接或托管的第 3 层连接。

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>任意位置之间的 (IPVPN) 网络
可以将 WAN 集成到 Microsoft 云。 IPVPN 提供商（通常为 MPLS VPN）可在分支机构与数据中心之间提供任意位置之间的连接。 Microsoft 云可与 WAN 互连，就如同它是其他任何一个分支机构。 WAN 提供商通常提供托管的第 3 层连接。 在上述所有连接模型中，ExpressRoute 功能与特性完全相同。

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>直接从 ExpressRoute 站点
你可以在战略分散在世界各地的对等位置直接连接到 Microsoft 的全球网络。 [ExpressRoute Direct](expressroute-erdirect-about.md) 提供双 100 Gbps 或 10 gbps 连接，支持大规模主动/主动连接。

## <a name="next-steps"></a>后续步骤
* 了解 ExpressRoute 连接和路由域。 请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
* 了解 ExpressRoute 功能。 请参阅 [ExpressRoute 技术概述](expressroute-introduction.md)
* 查找服务提供商。 请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。
* 请参阅[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的要求。
* 配置 ExpressRoute 连接。
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md)
  * [配置路由](expressroute-howto-routing-portal-resource-manager.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)