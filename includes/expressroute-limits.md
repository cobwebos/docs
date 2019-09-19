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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "67622084"
---
| Resource | 默认/最大限制 |
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

#### <a name="vnetpercircuit"></a>每个 ExpressRoute 线路的虚拟网络数
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
