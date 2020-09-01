---
title: include 文件
description: include 文件
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 146a257a9b8f31e79283d71ede1e1c5c0355765c
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147811"
---
| 资源 | 限制 |
| --- | --- |
| 每个订阅的 ExpressRoute 线路数 |10 个 |
| Azure 资源管理器中每个订阅的每个区域的 ExpressRoute 线路数 |10 个 |
| 使用 ExpressRoute Standard 播发到 Azure 专用对等互连的最大路由数 |4,000 |
| 使用 ExpressRoute Premium 附加产品播发到 Azure 专用对等互连的最大路由数 |10,000 |
| 从 ExpressRoute 连接的 VNet 地址空间通过 Azure 专用对等互连播发的最大路由数 |200 |
| 使用 ExpressRoute Standard 播发到 Microsoft 对等互连的最大路由数 |200 |
| 使用 ExpressRoute Premium 附加产品播发到 Microsoft 专用对等互连的最大路由数 |200 |
| 链接到标准性能/ErGw1AZ 网关的最大 ExpressRoute 线路数 |4 | 
| 链接到高性能/ErGw2AZ 网关的最大 ExpressRoute 线路数 |8 | 
| 链接到超高性能/ErGw3AZ 网关的最大 ExpressRoute 线路数 |16 | 
| 链接到不同对等互连位置（所有 SKU）中相同虚拟网络的最大 ExpressRoute 线路数 |4 |
| 每个 ExpressRoute 线路允许的虚拟网络链接数 |请参阅[每个 ExpressRoute 线路的虚拟网络数](#vnetpercircuit)表。  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> 每个 ExpressRoute 线路的虚拟网络数
|  线路大小 |  标准版的虚拟网络链接数 |  高级版附加设备的虚拟网络链接数 |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**100 Gbps，仅限 ExpressRoute Direct*

> [!NOTE]
> Global Reach 连接计数与每条 ExpressRoute 线路的虚拟网络连接数限制。 例如，一条 10 Gbps 的高级线路允许有 5 个 Global Reach 连接和 95 个到 ExpressRoute 网关的连接、95 个 Global Reach 连接和 5 个到 ExpressRoute 网关的连接，或者不超过该线路 100 个连接限制的其他任何组合。
