---
title: 方案： any 到 any
titleSuffix: Azure Virtual WAN
description: 路由的方案-任意到任意
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567128"
---
# <a name="scenario-any-to-any"></a>方案： Any 到 any

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在任何情况下，任何分支都可以到达另一个分支。 如果存在多个集线器，则默认情况下，在标准虚拟 WAN 中启用集线器路由（也称为集线器）。 

在此方案中，VPN、ExpressRoute 和用户 VPN 连接关联到相同的路由表。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-architecture"></a><a name="architecture"></a>方案体系结构

在**图 1**中，所有 Vnet 和分支（VPN、EXPRESSROUTE、P2S）都可以相互联系。 在虚拟中心中，连接的工作方式如下：

* VPN 连接将 VPN 站点连接到 VPN 网关。
* 虚拟网络连接将虚拟网络连接到虚拟中心。 虚拟中心的路由器提供 Vnet 之间的传输功能。
* ExpressRoute 连接将 ExpressRoute 线路连接到 ExpressRoute 网关。

这些连接（默认情况下在创建时）与默认路由表相关联，除非将连接的路由配置设置为 "**无**" 或自定义路由表。 默认情况下，这些连接还会将路由传播到默认路由表。 这就实现了任意到任意方案，其中任何辐射（VNet、VPN、ER、P2S）都可以相互访问。

**图1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="图1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

默认情况下，为标准虚拟 WAN 启用此方案。 如果 WAN 配置中禁用了分支到分支的设置，则不会在分支轮辐之间建立连接。 VPN/ExpressRoute/用户 VPN 被视为虚拟 WAN 中的分支轮辐

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
