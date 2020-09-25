---
title: 方案：路由到共享服务 Vnet
titleSuffix: Azure Virtual WAN
description: 路由设置的方案-设置路由以访问共享服务 VNet，其中包含你希望每个 VNet 和分支访问的工作负荷。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: b8cc59b805cd757edce79a14d124ea244b4652a4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267476"
---
# <a name="scenario-route-to-shared-services-vnets"></a>方案：路由到共享服务 Vnet

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在这种情况下，目标是设置路由，以便通过要访问的每个 VNet 和分支 (VPN/ER/P2S) 访问 **共享服务** VNet。 这些共享工作负荷的示例可能包括具有域控制器或文件共享等服务的虚拟机，或通过 [Azure 专用终结点](../private-link/private-endpoint-overview.md)内部公开的 azure 服务。

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>设计

我们可以使用连接矩阵汇总此方案的要求。 在矩阵中，每个单元都说明了虚拟 WAN 连接 (流的 "From" 端、表中的行标题) 了解流的 "To" 端 (目标前缀，表中的列标题) 用于特定的流量流。 "X" 表示虚拟 WAN 提供连接：

**连接矩阵**

| 源             | 到:   |*独立 Vnet*|*共享 VNet*|*分支*|
|---|---|---|---|---|
|**独立 Vnet**|&#8594;|                |        X        |       X      |
|**共享 Vnet**  |&#8594;|       X        |        X        |       X      |
|**分支**      |&#8594;|       X        |        X        |       X      |

类似于 [隔离 VNet 方案](scenario-isolate-vnets.md)，此连接矩阵提供两个不同的行模式，它们转换为 (共享服务 vnet 的两个路由表，并且分支) 具有相同的连接要求。 虚拟 WAN 已经有一个默认路由表，因此我们将需要另一个自定义路由表，我们将在此示例中调用 **RT_SHARED** 。

Vnet 将关联到 **RT_SHARED** 路由表。 由于需要连接到分支和共享服务 Vnet，因此共享服务 VNet 和分支将需要传播到 **RT_SHARED** (否则，vnet 将不会了解分支和共享 VNet 前缀) 。 由于分支始终关联到默认的路由表，并且对于共享服务 Vnet 的连接要求相同，因此也将共享服务 Vnet 关联到默认路由表。

因此，这是最终设计：

* 隔离的虚拟网络：
  * 关联的路由表： **RT_SHARED**
  * 传播到路由表： **默认值**
* 共享服务虚拟网络：
  * 关联的路由表： **默认值**
  * 传播到路由表： **RT_SHARED** 和 **默认值**
* 转
  * 关联的路由表： **默认值**
  * 传播到路由表： **RT_SHARED** 和 **默认值**

> [!NOTE]
> 如果虚拟 WAN 部署在多个区域，则需要在每个中心创建 **RT_SHARED** 路由表，并且每个共享服务 VNet 中的路由和分支连接都需要使用传播标签传播到每个虚拟中心的路由表。

有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流

若要配置该方案，请考虑以下步骤：

1. 标识 **共享服务** VNet。
2. 创建自定义路由表。 在此示例中，我们将路由表称为 **RT_SHARED**。 有关创建路由表的步骤，请参阅 [如何配置虚拟中心路由](how-to-virtual-hub-routing.md)。 使用以下值作为指导原则：

   * **关联**
     * 对于**vnet （共享服务 VNet*除外***），请选择要隔离的 vnet。 这意味着除共享服务 VNet) 之外的所有这些 Vnet (都可以根据 RT_SHARED 路由表的路由来访问目标。

   * **传播**
      * 对于 **分支**，将路由传播到此路由表，以及你可能已选择的任何其他路由表。 由于此步骤，RT_SHARED 路由表将从 (VPN/ER/用户 VPN) 的所有分支连接中了解路由。
      * 对于 **vnet**，请选择 " **共享服务 VNet**"。 由于此步骤，RT_SHARED 路由表将从共享服务 VNet 连接中了解路由。

这将导致路由配置如下图所示：

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="共享服务 VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
