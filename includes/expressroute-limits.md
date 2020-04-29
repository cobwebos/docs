---
title: 包含文件
description: include 文件
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335105"
---
| 资源 | 限制 |
| --- | --- |
| 每个订阅的 ExpressRoute 线路数 |10 |
| 每个订阅每个区域的 ExpressRoute 线路，Azure 资源管理器 |10 |
| 与 ExpressRoute Standard 一起播发到 Azure 专用对等互连的最大路由数 |4,000 |
| 与 ExpressRoute 高级版外接程序播发到 Azure 专用对等互连的最大路由数 |10,000 |
| 从 Azure 专用对等互连的用于 ExpressRoute 连接的 VNet 地址空间的最大路由数 |200 |
| 与 ExpressRoute Standard 一起播发到 Microsoft 对等互连的最大路由数 |200 |
| 与 ExpressRoute 高级版外接程序播发到 Microsoft 对等互连的最大路由数 |200 |
| 链接到同一对等位置中同一虚拟网络的 ExpressRoute 线路的最大数目 |4 |
| 链接到不同对等互连位置中相同虚拟网络的最大 ExpressRoute 线路数 |4 |
| 每个 ExpressRoute 线路允许的虚拟网络链接数 |查看[每个 ExpressRoute 线路的虚拟网络数](#vnetpercircuit)。  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>每个 ExpressRoute 线路的虚拟网络数
| **线路大小** | **标准的虚拟网络链接数** | **具有高级版外接程序的虚拟网络链接数** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps * |10 |100 |
| 100 Gbps * |10 |100 |

**100 Gbps ExpressRoute 直接*

> [!NOTE]
> Global Reach 连接数与每个 ExpressRoute 线路的虚拟网络连接数的限制。 例如，一条 10 Gbps 高级线路允许 5 Global Reach 连接，95连接到 ExpressRoute 网关或 95 Global Reach 连接，5次连接到 ExpressRoute 网关，或将5个连接设置为线路的100连接限制。
