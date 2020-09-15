---
title: '方案：通过网络虚拟设备路由流量 (NVA) '
titleSuffix: Azure Virtual WAN
description: 通过 NVA 路由流量
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 46ffb5bfe52fe4f398594a1dfed76a6ea6c0fd81
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530788"
---
# <a name="scenario-route-traffic-through-an-nva"></a>方案：通过 NVA 路由流量

使用虚拟 WAN 虚拟中心路由时，有很多可用方案。 在此 NVA 方案中，目标是将流量路由到分支到 VNet 的 NVA (网络虚拟) 设备，并将 VNet 路由到分支。 有关虚拟中心路由的信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

> [!NOTE]
> 如果已设置了具有新功能的新功能之前的路由， [如何配置虚拟中心路由](how-to-virtual-hub-routing.md) ，请使用以下文章中的步骤：
>* [Azure 门户文章](virtual-wan-route-table-nva-portal.md)
>* [PowerShell 文章](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>设计

在此方案中，我们将使用命名约定：

* 对于用户已部署 NVA 并已将其他虚拟网络作为辐射 (VNet 2 和 VNet 4 的虚拟网络 **，请在) 下面的 "** NVA vnet"。
* 连接到 NVA VNet 的虚拟网络的 "NVA 轮辐" (VNet 5、VNet 6、VNet 7 和中的 VNet 8 **，如下所) **示。
* 对于连接到未使用 NVA 或其他 Vnet 对等互连的虚拟 WAN 的虚拟网络，"NVA Vnet" 在 **连接矩阵**中 (VNet 1 和 vnet 3，请在下面) 。
* 适用于 Microsoft 托管的虚拟 WAN 中心的 "中心"，其中 NVA Vnet 连接到。 NVA 轮辐 Vnet 无需连接到虚拟 WAN 中心，只能连接到 NVA Vnet。

下面的连接矩阵汇总了此方案中支持的流：

**连接矩阵**

| 从             | 到:|   *NVA 轮辐*|*NVA Vnet*|*非 NVA Vnet*|*分支*|
|---|---|---|---|---|---|
| **NVA 轮辐**   | &#8594; | 0/0 UDR  |  对等互连 |   0/0 UDR    |  0/0 UDR  |
| **NVA Vnet**    | &#8594; |   静态 |      X   |        X     |      X    |
| **非 NVA Vnet**| &#8594; |   静态 |      X   |        X     |      X    |
| **分支**     | &#8594; |   静态 |      X   |        X     |      X    |

连接矩阵中的每个单元都说明了虚拟 WAN 连接 (流的 "From" 端、表中的行标题) 了解流的 "To" 端 (目标前缀，表) 中的列标题用于特定的流量流。 "X" 表示虚拟 WAN 在本机提供连接，而 "静态" 表示虚拟 WAN 使用静态路由提供连接。 考虑以下情况：

* NVA 轮辐不受虚拟 WAN 的管理。 因此，用户将维护与其他 Vnet 或分支通信的机制。 与 NVA VNet 的连接是由 VNet 对等互连提供的，默认路由到 0.0.0.0/0，并将其作为下一个跃点指向 NVA
* NVA Vnet 将知道自己的 NVA 轮辐，但不能了解连接到其他 NVA Vnet 的 NVA 轮辐。 例如，在表1中，VNet 2 知道 VNet 5 和 VNet 6，而不是关于 VNet 7 和 VNet 8 的其他分支。 需要使用静态路由将其他轮辐前缀注入到 NVA Vnet 中。
* 同样，分支和非 NVA Vnet 将不知道任何 NVA 轮辐，因为 NVA 轮辐未连接到 VWAN 集线器。 因此，此处还需要静态路由。

考虑到 NVA 轮辐不受虚拟 WAN 管理，所有其他行都显示相同的连接模式。 因此，单个路由表 (默认) 会执行以下操作：

* 虚拟网络 (非中心 Vnet 和用户中心 Vnet) ：
  * 关联的路由表： **默认值**
  * 传播到路由表： **默认值**
* 转
  * 关联的路由表： **默认值**
  * 传播到路由表： **默认值**

但是，在此方案中，我们需要考虑要配置哪些静态路由。 每个静态路由都将具有两个组件，一个部分位于虚拟 WAN 集线器中，用于指示虚拟 WAN 组件要用于每个辐射的连接，另一个部分位于该特定连接中，指向分配给 NVA (的具体 IP 地址，或指向多个 Nva) 前面的负载均衡器，如 **图 1** 所示：

**图 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="图 1":::

这样，在默认表中要将流量发送到 NVA VNet 后面的 NVA 轮辐的静态路由如下：

| 说明 | 路由表 | 静态路由              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | 默认     | 10.2.0.0/16-> eastusconn |
| VNet 4       | 默认     | 10.4.0.0/16-> weconn     |

现在，虚拟 WAN 知道要将数据包发送到的连接，但连接需要知道在接收这些数据包时要执行的操作：这是使用连接路由表的位置。 此处，我们将使用较短的前缀 (/24，而不是较长/16) ，以确保这些路由具有从 NVA Vnet (VNet 2 和 VNet 4) 中导入的路由的优先顺序：

| 说明 | 连接 | 静态路由            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24-> 10.2.0。5 |
| VNet 6       | eastusconn | 10.2.2.0/24-> 10.2.0。5 |
| VNet 7       | weconn     | 10.4.1.0/24-> 10.4.0。5 |
| VNet 8       | weconn     | 10.4.2.0/24-> 10.4.0。5 |

现在，NVA Vnet、非 NVA Vnet 和分支知道如何访问所有 NVA 轮辐。 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>体系结构

在 **图 2**中，有两个中心： **Hub1** 和 **Hub2**。

* **Hub1** 和 **Hub2** 直接连接到 NVA Vnet **VNet 2** 和 **vnet 4**。

* **Vnet 5** 和 **Vnet 6** 与 **vnet 2**对等互连。

* **Vnet 7** 和 **Vnet 8** 与 **vnet 4**对等互连。

* **Vnet 5、6、7、8** 是间接轮辐，不直接连接到虚拟中心。

**图 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="图 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>方案工作流

若要通过 NVA 设置路由，请执行以下步骤：

1. 标识 NVA 辐射 VNet 连接。 在 **图 2**中，它们是 **vnet 2 连接 (Eastusconn) ** 和 **VNet 4 连接 (weconn) **。

   确保已设置 Udr：
   * 从 VNet 5 和 VNet 6 到 VNet 2 NVA IP
   * 从 VNet 7 和 VNet 8 到 VNet 4 NVA IP 
   
   不需要将 Vnet 5、6、7和8直接连接到虚拟中心。 确保 Vnet 5、6、7、8中的 Nsg 允许分支 (VPN/ER/P2S) 或 Vnet 连接到其远程 Vnet 的流量。 例如，Vnet 5，6必须确保 Nsg 允许与连接到远程中心2的本地地址前缀和 Vnet 7、8的流量。

虚拟 WAN 不支持 Vnet 5、6连接到虚拟中心并通过 VNet 2 NVA IP 进行通信的方案;因此，需要将 Vnet 5、6到 VNet2 和类似的 VNet 7、8连接到 VNet 4。

2. 将 Vnet 2、5、6的聚合静态路由条目添加到中心1的默认路由表。

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="示例":::

3. 为 VNet 2 的虚拟网络连接中的 Vnet 5、6配置静态路由。 若要为虚拟网络连接设置路由配置，请参阅 [虚拟中心路由](how-to-virtual-hub-routing.md#routing-configuration)。

4. 将 Vnet 4、7、8的聚合静态路由条目添加到中心1的默认路由表。

5. 对于中心2的默认路由表，重复步骤2、3和4。

这将导致路由配置更改，如下面的 **图 3**所示。

**图 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="图 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>后续步骤

* 有关虚拟 WAN 的详细信息，请参阅[常见问题解答](virtual-wan-faq.md)。
* 有关虚拟中心路由的详细信息，请参阅[关于虚拟中心路由](about-virtual-hub-routing.md)。
