---
title: 创建基于路由的 VPN 网关：Azure 门户 | Microsoft Docs
description: 使用 Azure 门户快速创建基于路由的 VPN 网关
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: cherylmc
ms.openlocfilehash: 2d6133e974e24c8c4f769995d8245b30a29a3983
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>使用 Azure 门户创建基于路由的 VPN 网关

本文可帮助你使用 Azure 门户快速创建基于路由的 Azure VPN 网关。  创建与本地网络的 VPN 连接时使用 VPN 网关。 还可以使用 VPN 网关连接 VNet。 

本文中的步骤将创建 VNet、子网、网关子网和基于路由的 VPN 网关（虚拟网络网关）。 完成网关创建后，可以创建连接。 执行这些步骤需要 Azure 订阅。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="vnet"></a>创建虚拟网络

1. 从浏览器导航到 [Azure 门户](http://portal.azure.com)并使用 Azure 帐户登录。
2. 单击“创建资源”。 在“搜索 Marketplace”字段中，键入“虚拟网络”。 从返回的列表中找到“虚拟网络”，单击打开“虚拟网络”页。
3. 从靠近“虚拟网络”页底部的“选择部署模型”列表中，确认已从下拉列表中选择“资源管理器”，然后单击“创建”。 这会打开“创建虚拟网络”页。
4. 在“创建虚拟网络”页上，配置 VNet 设置。 填写字段时，如果在字段中输入的字符有效，红色感叹号标记会变成绿色对钩标记。 使用以下值：

  - **名称**：TestVNet1
  - **地址空间**：10.1.0.0/16
  - **订阅**：确认列出的订阅是你想要使用的订阅。 可以使用下拉列表更改订阅。
  - **资源组**：TestRG1
  - **位置**：美国东部
  - **子网**：Frontend
  - **地址范围**：10.1.0.0/24

  ![创建虚拟网络页](./media/create-routebased-vpn-gateway-portal/vnet1.png "创建虚拟网络页")
5. 输入值后，选择“固定到仪表板”，以便可以方便地在仪表板中找到你的 VNet，然后单击“创建”。 单击“创建”后，将看到仪表板上的磁贴反映了 VNet 的进度。 创建 VNet 时，该磁贴会更改。

## <a name="gwsubnet"></a>添加网关子网

网关子网包含虚拟网络网关服务使用的保留 IP 地址。 创建网关子网。

1. 在门户中，导航到要为其创建虚拟网关的虚拟网络。
2. 在“虚拟网络”页上，单击“子网”以展开“VNet1 - 子网”页。
3. 单击顶部的“+网关子网”打开“添加子网”页。

  ![添加网关子网](./media/create-routebased-vpn-gateway-portal/gateway_subnet.png "添加网关子网")
4. 子网的“名称”会自动填充为所需值“GatewaySubnet”。 调整自动填充的**地址范围**值，使其匹配以下值：

  **地址范围(CIDR 块)**：10.1.255.0/27

  ![添加网关子网](./media/create-routebased-vpn-gateway-portal/add_gw_subnet.png "添加网关子网")
5. 若要创建网关子网，请单击页底部的“确定”。

## <a name="gwvalues"></a>配置网关设置

1. 在门户页的左侧单击“+ 创建资源”，然后在搜索框中键入“虚拟网关”。 在“结果”中找到并单击“虚拟网关”。
2. 在“虚拟网关”页的底部，单击“创建”打开“创建虚拟网关”页。
3. 在“创建虚拟网关”页中，指定虚拟网关的值。

  - **名称**：Vnet1GW
  - **网关类型**：VPN 
  - **VPN 类型**：基于路由
  - **SKU**：VpnGw1
  - **位置**：美国东部
  - **虚拟网络**：单击“虚拟网络/选择虚拟网络”打开“选择虚拟网络”页。 选择 **VNet1**。

  ![配置网关设置](./media/create-routebased-vpn-gateway-portal/configure_gw.png "配置网关设置")

## <a name="pip"></a>创建公共 IP 地址

VPN 网关必须具有动态分配的公共 IP 地址。 创建与 VPN 网关的连接时，这是本地设备所连接到的 IP 地址。

1. 选择“第一个 IP 配置：创建网关 IP 配置”以请求一个公共 IP 地址。

  ![第一个 IP 配置](./media/create-routebased-vpn-gateway-portal/ip.png "第一个 IP 配置")
2. 在“选择公共 IP”页上，单击“+新建”打开“创建公共 IP 地址”页。
3. 为设置配置以下值：

  - **名称**：**VNet1GWPIP**
  - **SKU**：**基本**

  ![创建公共 IP](./media/create-routebased-vpn-gateway-portal/gw_ip.png "创建 PIP")
4. 单击此页底部的“确定”，保存所做的更改。

## <a name="creategw"></a>创建 VPN 网关

1. 在“创建虚拟网关”页上验证设置。 如有必要，请调整值。

  ![创建 VPN 网关](./media/create-routebased-vpn-gateway-portal/create_gw.png "创建 VPN 网关")
2. 在页面底部单击“创建”。

单击“创建”后，将会验证这些设置，并会在仪表板上显示“正在部署虚拟网关”磁贴。 VPN 网关可能最多需要 45 分钟。 可能需要刷新门户页才能看到完成状态。

## <a name="viewgw"></a>查看 VPN 网关

1. 创建网关后，请在门户中导航到 VNet1。 VPN 网关将作为已连接的设备显示在概述页上。

  ![已连接的设备](./media/create-routebased-vpn-gateway-portal/connected_devices.png "已连接的设备")

2. 在设备列表中，单击 **VNet1GW** 可查看详细信息。

  ![查看 VPN 网关](./media/create-routebased-vpn-gateway-portal/view_gw2.png "查看 VPN 网关")

## <a name="next-steps"></a>后续步骤

完成创建网关后，可以创建虚拟网络与另一个 VNet 之间的连接。 或者，创建虚拟网络与本地位置之间的连接。

> [!div class="nextstepaction"]
> [创建站点到站点连接](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> [创建点到站点连接](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> [创建与另一个 VNet 的连接](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)