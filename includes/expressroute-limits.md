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
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335105"
---
| 资源 | 限制 |
| --- | --- |
| 每个订阅的 ExpressRoute 线路数 |10 |
| 每个订阅的 ExpressRoute 电路，使用 Azure 资源管理器 |10 |
| 使用 ExpressRoute 标准通告到 Azure 专用对等互连的最大路由数 |4,000 |
| 使用 ExpressRoute 高级加载项通告到 Azure 专用对等互连的最大路由数 |10,000 |
| 从 ExpressRoute 连接的 VNet 地址空间从 Azure 专用对等互连通告的最大路由数 |200 |
| 使用 ExpressRoute 标准向 Microsoft 对等互连通告的最大路由数 |200 |
| 使用 ExpressRoute 高级加载项向 Microsoft 通告对等路由的最大路由数 |200 |
| 在同一对等位置连接到同一虚拟网络的最大 ExpressRoute 电路数 |4 |
| 链接到不同对等互连位置中相同虚拟网络的最大 ExpressRoute 线路数 |4 |
| 每个 ExpressRoute 线路允许的虚拟网络链接数 |请参阅[每个 ExpressRoute 电路表的虚拟网络数](#vnetpercircuit)。  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>每个 ExpressRoute 电路的虚拟网络数
| **电路尺寸** | **标准虚拟网络链接数** | **使用高级加载项的虚拟网络链接数** |
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

**仅限 100 Gbps 快速路由*

> [!NOTE]
> 全局覆盖连接计入每个 ExpressRoute 电路的虚拟网络连接限制。 例如，10 Gbps 高级电路将允许 5 个全局到达连接和 95 个到 ExpressRoute 网关的连接或 95 个全局到达连接，以及 5 个到 ExpressRoute 网关的连接，或到 100 个连接限制的任何其他组合电路。
