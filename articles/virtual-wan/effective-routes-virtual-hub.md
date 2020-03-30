---
title: 查看虚拟中心的有效路由：Azure 虚拟 WAN |微软文档
description: 在 Azure 虚拟 WAN 中为虚拟中心提供有效路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515845"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>查看虚拟中心的有效路由

您可以在 Azure 门户中查看虚拟 WAN 中心的所有路由。 要查看路由，请导航到虚拟中心，然后选择路由 **->查看有效路由**。

## <a name="understanding-routes"></a><a name="understand"></a>了解路线

以下示例可帮助您更好地了解虚拟 WAN 路由的显示方式。

在此示例中，我们有一个具有三个集线器的虚拟 WAN。 第一个枢纽位于美国东部地区，第二个枢纽位于西欧地区，第三个枢纽位于美国西部区域。 在虚拟广域网中，所有集线器都是互连的。 在此示例中，我们将假定东美国和西欧中心具有来自本地分支（分支）和 Azure 虚拟网络（分支）的连接。

具有网络虚拟设备 （10.4.0.6） 的 Azure VNet 分支 （10.4.0.0/16） 进一步与 VNet （10.5.0.0/16） 对等。 有关中心路由表的详细信息，请参阅本文后面的[其他信息](#abouthubroute)。

在此示例中，我们还假定西欧分支 1 连接到美国东部中心以及西欧枢纽。 美国东部的 ExpressRoute 电路将第 2 分支连接到美国东部枢纽。

![示意图](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>查看有效路由

当您在门户中选择"查看有效路由"时，它将生成美国东部[中心集线表中](#routetable)显示的输出。

综上所述，第一行意味着美国东部中心由于 VPN*下一跃点类型*连接（"下一跃点"VPN 网关实例0 IP 10.1.0.6、实例1 IP 10.1.0.7）而了解了 10.20.1.0/24 （分支 1） 的路由。 *路由原点*指向资源 ID。 *AS 路径*指示分支 1 的 AS 路径。

### <a name="hub-route-table"></a><a name="routetable"></a>中心路由表

使用表底部的滚动条查看"AS 路径"。

| **前缀** |  **下一个跃点类型** | **下一跳** |  **路线原点** |**作为路径** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /订阅/`<sub>`资源组//`<rg>`提供商/微软.网络/vpn网关/343a19aa6ac74e4d81f05ccccf1536cf-Eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/订阅/`<sub>`资源组//`<rg>`提供商/微软.网络/快速路由网关/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/订阅/`<sub>`资源组//`<rg>`提供商/微软.网络/vpn网关/343a19aa6ac74e4d81f05ccccf1536cf-Eastus-gw|23000|
|10.4.0.0/16|虚拟网络连接| 链路上 |  |  |
|10.5.0.0/16| IP 地址| 10.4.0.6|/订阅/`<sub>`资源组//`<rg>`提供商/微软.网络/虚拟中心/easthub_1/路由表/table_1| |
|0.0.0.0/0| IP 地址| `<Azure Firewall IP>` |/订阅/`<sub>`资源组//`<rg>`提供商/微软.网络/虚拟中心/easthub_1/路由表/table_1| |
|10.22.1.0/16| 远程中心|10.8.0.6, 10.8.0.7|/订阅/`<sub>`资源组//`<rg>`提供商/微软.网络/虚拟中心/westhub_| 4848-22000 |
|10.9.0.0/16| 远程中心|  链路上 |/订阅/`<sub>`资源组//`<rg>`提供商/微软.网络/虚拟中心/westhub_1| |

>[!NOTE]
> 如果美国东部和西欧中心在示例拓扑中互不通信，则所学的路线 （10.9.0.0/16） 将不存在。 中心仅通告直接连接到它们的网络。
>

## <a name="additional-information"></a><a name="additional"></a>其他信息

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>关于中心路由表

可以创建一个虚拟中心路由，并将该路由应用于虚拟中心路由表。 可以将多个路由应用于虚拟中心路由表。 这允许您通过 IP 地址（通常是分支 VNet 中的网络虚拟设备 （NVA））设置目标 VNet 的路由。 有关 NVA 的详细信息，请参阅[将流量从虚拟集线器路由到 NVA](virtual-wan-route-table-portal.md)。

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>关于默认路由 （0.0.0.0/0）

如果连接上的标志为"已启用"，则虚拟中心能够将学习的默认路由传播到虚拟网络、站点到站点 VPN 和 ExpressRoute 连接。 编辑虚拟网络连接、VPN 连接或 ExpressRoute 连接时，此标志可见。 默认情况下，在集线器 VNet、ExpressRoute 和 VPN 连接上，"启用 Internet 安全"始终为 false。

默认路由不源自虚拟广域网中心。 如果虚拟 WAN 中心由于在集线器中部署防火墙而已了解默认路由，或者另一个连接站点已启用强制隧道，则将传播该路由。

## <a name="next-steps"></a>后续步骤

有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)。