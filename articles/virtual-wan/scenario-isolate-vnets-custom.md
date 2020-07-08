---
title: 方案： Vnet 的自定义隔离
titleSuffix: Azure Virtual WAN
description: 路由方案-阻止所选 Vnet 相互联系
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567125"
---
# <a name="scenario-custom-isolation-for-vnets"></a>方案： Vnet 的自定义隔离

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在 Vnet 的自定义隔离方案中，目标是防止特定 Vnet 集能够访问其他特定的 Vnet 集。 但是，需要 Vnet 来访问所有分支（VPN/ER/用户 VPN）。

在此方案中，VPN、ExpressRoute 和用户 VPN 连接（统称为分支）关联到相同的路由表（默认路由表）。 所有 VPN、ExpressRoute 和用户 VPN 连接都将路由传播到同一组路由表。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="architecture"></a>方案工作流

在**图 1**中，有蓝色和红色 VNet 连接。

* 蓝色连接的 Vnet 可以相互联系，还可以访问所有分支（VPN/ER/P2S）连接。
* 红色 Vnet 可以相互联系，还可以访问所有分支（VPN/ER/P2S）连接。

设置路由时，请考虑以下步骤。

1. 在 Azure 门户中创建两个自定义路由表， **RT_BLUE**和**RT_RED**。
2. 对于路由表**RT_BLUE**，在：
   * **关联**：选择所有蓝色 vnet
   * **传播**：对于 "分支"，请选择 "分支" 选项，"表示分支（VPN/ER/P2S）连接" 会将路由传播到此路由表。
3. 对**RT_RED**适用于 RED vnet 和分支的路由表（VPN/ER/P2S）重复相同的步骤。

这将导致路由配置更改，如下图所示

**图1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="图1":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
