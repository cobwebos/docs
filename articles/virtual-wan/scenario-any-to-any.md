---
title: 方案： any 到 any
titleSuffix: Azure Virtual WAN
description: 用于路由的方案 - 任意位置到任意位置
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6115ca375c3e5bf2be3335fe2231628ec7bf309f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267731"
---
# <a name="scenario-any-to-any"></a>方案：任意位置到任意位置

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在“任意位置到任意位置”方案中，任意分支都可以联系另一个分支。 如果存在多个中心，默认情况下会在标准虚拟 WAN 中启用中心到中心路由（也称为“中心间”）。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>设计

为了确定虚拟 WAN 方案中将会需要多少路由表，可以构建一个连接矩阵，其中每个单元格都表示源（行）是否可以与目标（列）通信。 此方案中的连接矩阵很普通，但我们已将其包含在内，以便与其他方案保持一致。

| 源 |   目标 |  VNet | *分支* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;|      X     |     X    |
| 分支   | &#8594;|    X     |     X    |

上表中每个单元格都描述了虚拟 WAN 连接（流的“源”端，表中的行标题）是否为特定的流量流获取目标前缀（流的“目标”端，表中斜体的列标题），其中“X”表示由虚拟 WAN 提供连接。

由于来自 VNet 和分支（VPN、ExpressRoute 和用户 VPN）的所有连接都具有相同的连接要求，因此需要单个路由表。 这样，所有的连接都将会关联，并将传播到同一个路由表，即 Default 路由表：

* 虚拟网络：
  * 关联的路由表：**Default**
  * 传播到路由表：**Default**
* 分支：
  * 关联的路由表：**Default**
  * 传播到路由表：**Default**

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>体系结构

在图 1 中，所有 VNet 和分支（VPN、ExpressRoute、P2S）都可以相互联系。 在虚拟中心，连接的工作方式如下：

* VPN 连接将 VPN 站点连接到 VPN 网关。
* 虚拟网络连接将虚拟网络连接到虚拟中心。 虚拟中心的路由器提供 VNet 之间的传输功能。
* ExpressRoute 连接将 ExpressRoute 线路连接到 ExpressRoute 网关。

这些连接（默认在创建时）都会关联到 Default 路由表，除非已将连接的路由配置设置为“无”，或者设置为自定义路由表。 这些连接也传播路由，默认情况下传播到 Default 路由表。 这种方式实现了“任意位置到任意位置”方案，其中任何分支（VNet、VPN、ER、P2S）都可以相互联系。

**图 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="图 1":::

## <a name="workflow"></a><a name="workflow"></a>工作流

对于标准虚拟 WAN，默认情况下会启用此方案。 如果 WAN 配置中禁用了分支到分支的设置，将会禁止分支之间的连接。 VPN/ExpressRoute/用户 VPN 在虚拟 WAN 中被视为分支

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
