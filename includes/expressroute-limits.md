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
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622084"
---
| Resource | 默认值/最大限制 |
| --- | --- |
| 每个订阅的 ExpressRoute 线路数 |10 |
| 每个区域每个订阅，使用 Azure 资源管理器中的 ExpressRoute 线路 |10 |
| 播发到 Azure 专用对等互连具有 ExpressRoute Standard 的最大路由数 |4,000 |
| 最大数量的路由播发到 Azure 专用对等互连的 ExpressRoute 高级版外接程序 |10,000 |
| 从 Azure 专用对等互连的 ExpressRoute 连接的 VNet 地址空间中的最大路由数播发 |200 |
| 播发到 Microsoft 对等互连具有 ExpressRoute Standard 的最大路由数 |200 |
| 最大数量的路由播发到 Microsoft 对等互连的 ExpressRoute 高级版外接程序 |200 |
| ExpressRoute 线路链接到同一虚拟网络对等互连在同一位置的最大数目 |4 |
| 链接到不同对等互连位置中相同虚拟网络的最大 ExpressRoute 线路数 |4 |
| 每个 ExpressRoute 线路允许的虚拟网络链接数 |请参阅[的每个 ExpressRoute 线路的虚拟网络数](#vnetpercircuit)表。  |

#### <a name="vnetpercircuit"></a> 每个 ExpressRoute 线路的虚拟网络的数量
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
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**仅 100 Gbps ExpressRoute 直接*
