---
title: 教程：Azure ExpressRoute - 向 VNet 添加网关（Azure 门户）
description: 本教程演示如何使用 Azure 门户将虚拟网络网关添加到 ExpressRoute 的 VNet。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 843d0b8cfd75e8cbdf45ac535cc9486aa42442d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761766"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>教程：使用 Azure 门户配置 ExpressRoute 的虚拟网络网关
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [经典 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [视频 - Azure 门户](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

本教程演示如何为预先存在的 VNet 添加虚拟网络网关。 本文将演示为预先存在的 VNet 添加虚拟网络 (VNet) 网关、重设其大小并进行删除的步骤。 此配置的步骤专用于使用 Resource Manager 部署模型创建的、会在 ExpressRoute 配置中使用的 VNet。 有关 ExpressRoute 的虚拟网络网关和网关配置设置的详细信息，请参阅[关于 ExpressRoute 的虚拟网络网关](expressroute-about-virtual-network-gateways.md)。 

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 创建网关子网。
> - 创建虚拟网络网关。

## <a name="prerequisites"></a>先决条件

此任务的步骤使用的 VNet 基于以下配置参考列表中的值。 我们将此列表用于我们的示例步骤。 可以复制列表作为参考，并将列表中的值替换为自己的值。

**配置参考列表**

* 虚拟网络名称 = “TestVNet”
* 虚拟网络地址空间 = 192.168.0.0/16
* 子网名称 =“FrontEnd” 
    * 子网地址范围 =“192.168.1.0/24”
* 资源组 = “TestRG”
* 位置 =“美国东部”
* 网关子网名称：“GatewaySubnet”必须始终将网关子网命名为 *GatewaySubnet*。
    * 网关子网地址空间 = “192.168.200.0/26”
* 网关名称 =“ERGW”
* 网关公共 IP 名称 =“MyERGWVIP”
* 网关类型 =“ExpressRoute” ExpressRoute 配置需要此类型。

可以先观看这些步骤的[视频](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)，再开始配置。

## <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在[门户](https://portal.azure.com)中，导航到要为其创建虚拟网关的 Resource Manager 虚拟网络。
1. 在 VNet 的“设置”部分，选择“子网”以展开子网设置 。
1. 在子网设置中，选择“+网关子网”来添加网关子网 。 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="添加网关子网":::

1. 子网的“名称”**** 自动填充为值“GatewaySubnet”。 Azure 需要此值才能识别作为网关子网的子网。 调整自动填充的地址范围值，使其符合配置要求。 建议使用 /27 或更大（/26、/25 等）创建网关子网。 然后，单击“确定”来保存这些值并创建网关子网。

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="添加网关子网":::

## <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在门户左侧选择“创建资源”，然后在搜索框中键入“虚拟网络网关”。 在搜索返回的结果中找到“虚拟网关”，并选择该条目  。 在“虚拟网关”页上，选择“创建”   。
1. 在“创建虚拟网络网关”页面上，输入或选择以下设置：

    | 设置 | 值 |
    | --------| ----- |
    | 订阅 | 确保选择正确的订阅。 |
    | 资源组 | 选择虚拟网络后，将自动选择资源组。 | 
    | 名称 | 为网关命名。 这与为网关子网命名不同。 它是要创建的网关对象的名称。|
    | 区域 | 更改“区域”字段，使其指向虚拟网络所在的位置。 如果该位置未指向虚拟网络所在的区域，那么该虚拟网络将不在“选择虚拟网络”下拉列表中显示。 |
    | 网关类型 | 选择“ExpressRoute”|
    | SKU | 从下拉列表中选择网关 SKU。 |
    | 虚拟网络 | 选择 TestVNet。 |
    | 公共 IP 地址 | 选择“新建”。|
    | 公共 IP 地址名称 | 为公共 IP 地址提供一个名称。 |

1. 依次选择“查看 + 创建”和“创建”，开始创建网关 。 此时会验证设置并部署网关。 创建虚拟网络网关可能需要多达 45 分钟才能完成。

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="添加网关子网":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要 ExpressRoute 网关，请在虚拟网络资源组中找到该网关，然后选择“删除”。 请确保该网关没有任何到线路的连接。

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="添加网关子网":::

## <a name="next-steps"></a>后续步骤
创建 VNet 网关后，可将 VNet 链接到 ExpressRoute 线路。 

> [!div class="nextstepaction"]
> [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)
