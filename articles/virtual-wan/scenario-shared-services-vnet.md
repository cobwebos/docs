---
title: 方案：路由到共享服务 Vnet
titleSuffix: Azure Virtual WAN
description: 路由设置的方案-设置路由以访问共享服务 VNet，其中包含你希望每个 VNet 和分支访问的工作负荷。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567421"
---
# <a name="scenario-route-to-shared-services-vnets"></a>方案：路由到共享服务 Vnet

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此方案中，目标是将路由设置为访问**共享服务**VNet，其中包含你希望每个 VNet 和分支（VPN/ER/P2S）访问的工作负荷。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

若要配置此方案，请考虑以下步骤：

1. 标识**共享服务**VNet。
2. 创建自定义路由表。 在此示例中，我们将此路由表称为**RT_SHARED**。 有关创建路由表的步骤，请参阅[如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。 使用以下值作为指导原则：

   * **关联**
     * 对于**vnet （共享服务 VNet*除外***），请选择要隔离的 vnet。 这意味着，所有这些 Vnet （共享服务 VNet 除外）都可以根据 RT_SHARED 路由表的路由来访问目标。

   * **传播**
      * 对于**分支**，将路由传播到此路由表，以及你可能已选择的任何其他路由表。 由于此步骤，RT_SHARED 路由表将从所有分支连接（VPN/ER/用户 VPN）了解路由。
      * 对于**vnet**，请选择 "**共享服务 VNet**"。 由于此步骤，RT_SHARED 路由表将从共享服务 VNet 连接中了解路由。

     这将导致路由配置更改，如下图所示

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="共享服务 VNet":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
