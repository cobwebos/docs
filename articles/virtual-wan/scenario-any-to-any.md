---
title: 方案： any 到 any
titleSuffix: Azure Virtual WAN
description: 路由的方案-任意到任意
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 95fa7a8c6abd0ad65b367cacef15b8faa16da640
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553421"
---
# <a name="scenario-any-to-any"></a>方案： Any 到 any

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在任何情况下，任何分支都可以到达另一个分支。 当存在多个中心时，集线器路由 (也称为集线器间) 在标准虚拟 WAN 中默认启用。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>设计

为了确定虚拟 WAN 方案中需要多少个路由表，你可以构建一个连接矩阵，其中每个单元表示源 (行) 是否可以与) 的目标 (列通信。 此方案中的连接矩阵非常简单，但我们已将其包含在内，以便与其他方案保持一致。

| From |   功能 |  *Vnet* | *分支* |
| -------------- | -------- | ---------- | ---|
| VNet     | &#8594;|      X     |     X    |
| 分支   | &#8594;|    X     |     X    |

上表中的每个单元都说明了虚拟 WAN 连接 (流的 "From" 端、表中的行标题) 了解流的 "To" 端 (目标前缀，表) 中的列标题用于特定的流量流。

由于 Vnet 和分支中的所有连接都 (VPN、ExpressRoute 和用户 VPN) 具有相同的连接要求，因此需要一个路由表。 因此，所有连接都将关联并传播到同一个路由表，默认路由表如下：

* 虚拟网络：
  * 关联的路由表：**默认值**
  * 传播到路由表：**默认值**
* 转
  * 关联的路由表：**默认值**
  * 传播到路由表：**默认值**

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>体系结构

在**图 1**中，所有 Vnet 和分支 (VPN、EXPRESSROUTE、P2S) 可以相互联系。 在虚拟中心中，连接的工作方式如下：

* VPN 连接将 VPN 站点连接到 VPN 网关。
* 虚拟网络连接将虚拟网络连接到虚拟中心。 虚拟中心的路由器提供 Vnet 之间的传输功能。
* ExpressRoute 连接将 ExpressRoute 线路连接到 ExpressRoute 网关。

默认情况) 下，这些连接 (与默认路由表相关联，除非将连接的路由配置设置为 "**无**" 或自定义路由表。 默认情况下，这些连接还会将路由传播到默认路由表。 这可以实现任意到任意方案，其中任何分支 (VNet、VPN、ER、P2S) 可以相互联系。

**图 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="图1":::

## <a name="workflow"></a><a name="workflow"></a>工作流

默认情况下，为标准虚拟 WAN 启用此方案。 如果 WAN 配置中禁用了分支到分支的设置，则不会在分支轮辐之间建立连接。 VPN/ExpressRoute/用户 VPN 被视为虚拟 WAN 中的分支轮辐

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
