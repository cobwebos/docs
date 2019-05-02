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
ms.openlocfilehash: d067f1af3d5479aef28ddf2290cebe3fe45726b0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733770"
---
| 资源 | 默认值/最大限制 |
| --- | --- |
| 每个订阅的 ExpressRoute 线路数 |10 |
| 每个区域每个订阅，使用 Azure 资源管理器中的 ExpressRoute 线路 |10 |
| Azure 专用对等互连具有 ExpressRoute Standard 的路由的最大数目 |4,000 |
| Azure 专用对等互连的 ExpressRoute 高级版外接程序的路由的最大数目 |10,000 |
| Azure 专用对等互连的 ExpressRoute 连接的 VNet 地址空间中的路由的最大数目 |200 | 
| 具有 ExpressRoute Standard 的 Microsoft Azure 对等互连的路由的最大数目 |200 |
| 最大数量的 Microsoft Azure ExpressRoute 高级版外接程序与对等互连的路由 |200 |
| ExpressRoute 线路链接到同一虚拟网络对等互连在同一位置的最大数目 |4 |
| 链接到不同对等互连位置中相同虚拟网络的最大 ExpressRoute 线路数 |> 4，取决于 GatewaySubnet 大小|
| 每个 ExpressRoute 线路允许的虚拟网络链接数 |请参阅下表。 |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>每个 ExpressRoute 线路的虚拟网络的数量
| **线路大小** | **标准的虚拟网络链接数** | **高级版附加组件的虚拟网络链接数** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |

