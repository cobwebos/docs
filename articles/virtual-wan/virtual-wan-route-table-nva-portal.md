---
title: 虚拟 WAN：创建到 NVA 的虚拟中心路由表：Azure 门户
description: 虚拟 WAN 虚拟中心路由表用于通过门户将流量引导到网络虚拟设备。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8de7ad2808e5319819410b3125472e28496647b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267136"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>创建 NVA 的虚拟 WAN 中心路由表：Azure 门户

本文介绍如何通过网络虚拟设备 (NVA) 将流量从连接到虚拟 WAN 中心的分支（本地站点）引导到辐射虚拟网络 (VNet)。

![虚拟 WAN 示意图](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>开始之前

验证是否符合以下条件：

*  已有一个网络虚拟设备 (NVA)。 网络虚拟设备是所选的第三方软件，它通常是通过虚拟网络中的 Azure 市场预配的。

    * 必须向 NVA 网络接口分配一个专用 IP 地址。

    * 该 NVA 不是部署在虚拟中心内。 它必须部署在单独的虚拟网络中。

    *  可将一个或多个虚拟网络连接到 NVA 虚拟网络。 本文将 NVA 虚拟网络称作“间接辐射 VNet”。 可以使用 VNet 对等互连将这些虚拟网络连接到 NVA VNet。 在上图中，VNet 1、VNet 2 和 NVA VNet 之间的 VNet 对等互连链路用黑色箭头表示。
*  你已创建了两个虚拟网络。 这些 VNet 将用作辐射 VNet。

    * VNet 辐射地址空间为：VNet1：10.0.2.0/24，VNet2：10.0.3.0/24。 有关如何创建虚拟网络的信息，请参阅[创建虚拟网络](../virtual-network/quick-create-portal.md)。

    * 确保上述任何 VNet 不包含虚拟网络网关。

    * VNet 不需要网关子网。

## <a name="1-sign-in"></a><a name="signin"></a>1.登录

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2.创建虚拟 WAN

创建虚拟 WAN。 请使用以下示例值：

* **虚拟 WAN 名称：** myVirtualWAN
* **资源组：** testRG
* **位置：** 美国西部

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3.创建中心

创建中心。 使用以下示例值：

* **位置：** 美国西部
* **名称：** westushub
* **中心专用地址空间：** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4.创建并应用中心路由表

使用中心路由表更新中心。 请使用以下示例值：

* **辐射 VNet 地址空间：** （VNet1 和 VNet2）10.0.2.0/24 和 10.0.3.0/24
* **外围网络 NVA 网络接口专用 IP 地址：** 10.0.4.5

1. 导航到你的虚拟 WAN。
2. 单击要为其创建路由表的中心。
3. 依次单击“...”、“编辑虚拟中心”。**** ****
4. 在“编辑虚拟中心”页上，向下滚动并选中“使用表路由”复选框。**** ****
5. 在“如果目标前缀为”列中添加地址空间。**** 在“发送到下一跃点”列中，添加外围网络 NVA 网络接口专用 IP 地址。****

   > [!NOTE]
   > DMZ NVA 网络适用于本地中心。
   
6. 单击“确认”，以使用路由表设置更新中心资源。

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5.创建 VNet 连接

从每个间接辐射 VNet（VNet1 和 VNet2）创建到中心的虚拟网络连接。 这些虚拟网络连接在上图中用蓝色箭头表示。 然后，创建从 NVA VNet 到中心的 VNet 连接（图中的黑色箭头）。

 对于此步骤，可以使用以下值：

| 虚拟网络名称| 连接名称|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

针对要连接的每个虚拟网络重复以下过程。

1. 在虚拟 WAN 的页面上，单击“虚拟网络连接”。
2. 在虚拟网络连接页上，单击“+添加连接”。
3. 在“添加连接”页上填写以下字段  ：

    * **连接名称** - 为连接命名。
    * **中心** - 选择要与此连接关联的中心。
    * **订阅** - 验证订阅。
    * **虚拟网络** - 选择要连接到此中心的虚拟网络。 此虚拟网络不能包含现有的虚拟网络网关。
4. 单击“确定”创建连接。****

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
