---
title: "将虚拟网络网关添加到 ExpressRoute 的 VNet：门户：Azure | Microsoft Docs"
description: "本文演示如何将虚拟网络网关添加到已为 ExpressRoute 创建的 Resource Manager VNet。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2017
ms.author: cherylmc
ms.openlocfilehash: 2bd0cf8be87937044ad515a2c6f253b1711bb2bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>使用 Azure 门户配置 ExpressRoute 的虚拟网络网关
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [经典 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [视频 - Azure 门户](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

本文演示如何为预先存在的 VNet 添加虚拟网络网关。 本文将演示为预先存在的 VNet 添加虚拟网络 (VNet) 网关、重设其大小并进行删除的步骤。 此配置的步骤专用于使用 Resource Manager 部署模型创建的、会在 ExpressRoute 配置中使用的 VNet。 有关 ExpressRoute 的虚拟网络网关和网关配置设置的详细信息，请参阅[关于 ExpressRoute 的虚拟网络网关](expressroute-about-virtual-network-gateways.md)。 


## <a name="before-beginning"></a>开始之前

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
* 网关 IP 名称 =“MyERGWVIP”
* 网关类型 =“ExpressRoute” ExpressRoute 配置需要此类型。
* 网关公共 IP 名称 =“MyERGWVIP”

可以先观看这些步骤的[视频](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)，再开始配置。

## <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在[门户](http://portal.azure.com)中，导航到要为其创建虚拟网关的 Resource Manager 虚拟网络。
2. 在 VNet 边栏选项卡的“设置”部分中，单击“子网”以展开“子网”边栏选项卡。
3. 在“子网”边栏选项卡中，单击“+网关子网”打开“添加子网”边栏选项卡。 
   
    ![添加网关子网](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "添加网关子网")


4. 子网的“名称”自动填充为值“GatewaySubnet”。 Azure 需要此值才能识别作为网关子网的子网。 调整自动填充的**地址范围**值，使其匹配配置要求。 建议使用 /27 或更大（/26、/25 等）创建网关子网。 然后，单击“确定”保存这些值并创建网关子网。

    ![添加子网](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "添加子网")

## <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在门户左侧单击 **+**，并在搜索框中键入“虚拟网关”。 在搜索返回结果中找到“虚拟网络网关”，并单击该条目。 单击“虚拟网络网关”边栏选项卡底部的“创建”。 这会打开“创建虚拟网络网关”边栏选项卡。
2. 在“创建虚拟网络网关”边栏选项卡中，填写虚拟网络网关的值。

    ![创建虚拟网络网关边栏选项卡字段](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "创建虚拟网络网关边栏选项卡字段")
3. **名称**：为网关命名。 这与为网关子网命名不同。 它是要创建的网关对象的名称。
4. **网关类型**：选择“ExpressRoute”。
5. **SKU**：从下拉列表中选择网关 SKU。
6. **位置**：调整“位置”字段，使其指向虚拟网络所在的位置。 如果该位置未指向虚拟网络所在的区域，该虚拟网络将不会显示在“选择虚拟网络”下拉列表中。
7. 选择要将此网关添加到其中的虚拟网络。 单击“虚拟网络”打开“选择虚拟网络”边栏选项卡。 选择 VNet。 如果看不到 VNet，请确保“位置”字段指向虚拟网络所在的区域。
9. 选择公共 IP 地址。 单击“公共 IP 地址”打开“选择公共 IP 地址”边栏选项卡。 单击“+新建”打开“创建公共 IP 地址”边栏选项卡。 输入公共 IP 地址的名称。 此边栏选项卡会创建一个公共 IP 地址对象，将向其动态分配公共 IP 地址。 单击“确定”保存对此边栏选项卡所做的更改。
10. **订阅**：确保选择正确的订阅。
11. **资源组**：此设置取决于所选的虚拟网络。
12. 指定上述设置后请不要调整“位置”。
13. 验证设置。 如果希望网关显示在仪表板上，可以在边栏选项卡底部选择“固定到仪表板”。
14. 单击“创建”  开始创建网关。 此时会验证设置并部署网关。 创建虚拟网络网关可能需要多达 45 分钟才能完成。

## <a name="next-steps"></a>后续步骤
创建 VNet 网关之后，可以将 VNet 链接到 ExpressRoute 线路。 请参阅[将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)。