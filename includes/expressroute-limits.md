---
title: include 文件
description: include 文件
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ad75b333fc7d5d37a4ac074727e4d54b67bd0321
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2018
ms.locfileid: "45637277"
---
#### <a name="expressroute-limits"></a>ExpressRoute 限制
下列限制适用于每个订阅的 ExpressRoute 资源。

| 资源 | 默认限制/最大限制 |
| --- | --- |
| 每个订阅的 ExpressRoute 线路数 |10 |
| 每个订阅每个区域的 ExpressRoute 线路数（Azure 资源管理器） |10 |
| 具有 ExpressRoute Standard 的 Azure 私用对等互连的最大路由数 |4,000 |
| 具有 ExpressRoute Premium 附加设备的 Azure 私用对等互连的最大路由数 |10,000 |
| 具有 ExpressRoute Standard 的 Azure Microsoft 对等互连的最大路由数 |200 |
| 具有 ExpressRoute Premium 附加设备的 Azure Microsoft 对等互连的最大路由数 |200 |
| 链接到不同对等互连位置中相同虚拟网络的最大 ExpressRoute 线路数 |4 |
| 每个 ExpressRoute 线路允许的虚拟网络链接数 |请参阅下表 |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>每个 ExpressRoute 线路的虚拟网络数
| **线路大小** | **标准版 VNet 链接数** | **高级版附加设备的 VNet 链接数** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |

