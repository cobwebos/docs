---
title: 向 VNet 添加多个 VPN 网关站点到站点连接：Azure 门户：Resource Manager | Microsoft Docs
description: 将多站点 S2S 连接添加到包含现有连接的 VPN 网关
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 5830b3a4bdcd12c01626d9ff3f814d2e7612eaaa
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29398594"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>将站点到站点连接添加到包含现有 VPN 网关连接的 VNet

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell（经典）](vpn-gateway-multi-site.md)
>
> 

本文可帮助你通过 Azure 门户将站点到站点 (S2S) 连接添加到现已具有连接的 VPN 网关。 这种类型的连接通常称为“多站点”配置。 可将 S2S 连接添加到已有 S2S 连接、点到站点连接或 VNet 到 VNet 连接的 VNet。 添加连接时，请注意一些限制。 在开始之前，请查看本文的[开始之前](#before)部分检查配置。 

本文适用于具有基于路由的 VPN 网关的资源管理器 VNet。 本文中的步骤不适用于 ExpressRoute/站点到站点共存连接配置。 有关共存连接的信息，请参阅 [ExpressRoute/S2S 共存连接](../expressroute/expressroute-howto-coexist-resource-manager.md)。

### <a name="deployment-models-and-methods"></a>部署模型和方法
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

当我们发布有关此配置的新文章和其他可用工具时，会更新此表格。 有相关的文章发布时，我们会直接从此表格链接到该文章。

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>准备工作
确认以下各项：

* 创建的不是 ExpressRoute/S2S 共存连接。
* 有一个使用 Resource Manager 部署模型创建的、包含现有连接的虚拟网络。
* VNet 的虚拟网络网关是 RouteBased 类型。 如果使用 PolicyBased VPN 网关，必须先删除虚拟网络网关，然后创建新的 RouteBased VPN 网关。
* 此 VNet 连接到的任何 VNet 都不存在地址范围重叠的情况。
* 有一台兼容的 VPN 设备，并且可对其进行配置。 请参阅 [关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。 如果不熟悉 VPN 设备的配置，或者不熟悉本地网络配置中的 IP 地址范围，则需咨询能够提供此类详细信息的人员。
* VPN 设备有一个面向外部的公共 IP 地址。 此 IP 地址不得位于 NAT 之后。

## <a name="part1"></a>第 1 部分 - 配置连接
1. 从浏览器导航到 [Azure 门户](http://portal.azure.com)，并在必要时用 Azure 帐户登录。
2. 单击“**所有资源**”，从资源列表中找到“**虚拟网络网关**”并单击它。
3. 在“虚拟网络网关”页面上，单击“连接”。
   
    ![“连接”页](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "“连接”页")<br>
4. 在“连接”页面上，单击“+添加”。
   
    ![添加连接按钮](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "添加连接按钮")<br>
5. 在“添加连接”页面上，填写以下字段：
   
   * **名称：** 想与其建立连接的站点的名称。
   * **连接类型：** 选择“**站点到站点(IPsec)**”。
     
     ![“添加连接”页面](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection page")<br>

## <a name="part2"></a>第 2 部分 - 添加本地网络网关
1. 单击“**本地网络网关**”“***选择本地网络网关***”。 这将打开“选择本地网络网关”页面。
   
    ![选择本地网络网关](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "选择本地网络网关")<br>
2. 单击“新建”，打开“创建本地网络网关”页面。
   
    ![“创建本地网络网关”页面](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>
3. 在“创建本地网络网关”页面上，填写以下字段：
   
   * **名称：** 要分配给本地网络网关资源的名称。
   * **IP 地址：** 站点上要连接到的 VPN 设备的公共 IP 地址。
   * **地址空间：** 要路由到新本地网络站点的地址空间。
4. 在“创建本地网络网关”页面上单击“确定”保存所做更改。

## <a name="part3"></a>第 3 部分 - 添加共享密钥并创建连接
1. 在“添加连接”页面上，添加要用于创建连接的共享密钥。 可以从 VPN 设备获取共享密钥，或者在此边栏选项卡中创建一个共享密钥，然后将 VPN 设备配置为使用这个共享密钥。 重要的一点是，这两个密钥必须完全相同。
   
    ![共享密钥](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "共享密钥")<br>
2. 在页面底部，单击“确定”以创建连接。

## <a name="part4"></a>第 4 部分 - 验证 VPN 连接


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅虚拟机 [学习路径](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) 。
