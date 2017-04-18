---
title: "将本地网络连接到 Azure 虚拟网络：站点到站点 VPN：门户 | Microsoft 文档"
description: "通过公共 Internet 创建从本地网络到 Azure 虚拟网络的 IPsec 连接的步骤。 这些步骤将帮助你使用门户创建跨界站点到站点 VPN 网关连接。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d6f4caebeeced1286f24dd5fcb4f5fc7d8591785
ms.lasthandoff: 04/12/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>在 Azure 门户中创建站点到站点连接

站点到站点 (S2S) VPN 网关连接是通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道建立的连接。 此类型的连接要求位于本地的 VPN 设备分配有一个公共 IP 地址，并且不位于 NAT 后面。 站点到站点连接可以用于跨界和混合配置。

![站点到站点 VPN 网关跨界连接示意图](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

本文逐步讲解如何使用 Azure Resource Manager 部署模型和 Azure 门户创建一个虚拟网络和一个连接到本地网络的站点到站点 VPN 网关连接。 也可使用不同的部署工具创建该配置，而如果使用的是经典部署模型，则只需从以下列表中选择另一选项即可：

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [经典 - Azure 门户](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [经典 - 经典门户](vpn-gateway-site-to-site-create.md)
>
>


#### <a name="additional-configurations"></a>其他配置
如果你想要将多个 VNet 连接到一起，但又不想创建连接到本地位置的连接，则请参阅 [配置 VNet 到 VNet 连接](vpn-gateway-vnet-vnet-rm-ps.md)。 如果想要向已具有连接的 VNet 添加站点到站点连接，请参阅[使用现有 VPN 网关连接将 S2S 连接添加到 VNet](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

在开始配置之前，请确认你具有以下各项：

* 一台兼容的 VPN 设备和能够对其进行配置的人员。 请参阅 [关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。
* 如果不熟悉本地网络中的 IP 地址空间，则需咨询能够为你提供此类详细信息的人员。 站点到站点连接的地址空间不能重叠。
* 一个用于 VPN 设备的面向外部的公共 IP 地址。 此 IP 地址不得位于 NAT 之后。
* Azure 订阅。 如果你还没有 Azure 订阅，可以激活 [MSDN 订户权益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](http://azure.microsoft.com/pricing/free-trial)。

### <a name="values"></a>示例值
使用这些步骤作为练习时，可以使用以下示例值：

* **VNet 名称：**TestVNet1
* **地址空间：** 
    * 10.11.0.0/16
    * 10.12.0.0/16（可选，适用于本练习）
* **子网：**
  * FrontEnd：10.11.0.0/24
  * BackEnd：10.12.0.0/24（可选，适用于本练习）
  * GatewaySubnet：10.11.255.0/27
* **资源组：**TestRG1
* **位置：**美国东部
* **DNS 服务器：**DNS 服务器的 IP 地址
* **虚拟网关名称：**VNet1GW
* **公共 IP：**VNet1GWIP
* **VPN 类型：**基于路由
* **连接类型：**站点到站点 (IPsec)
* **网关类型：**VPN
* **局域网网关名称：**Site2
* **连接名称：**VNet1toSite2

## <a name="CreatVNet"></a>1.创建虚拟网络

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2.指定 DNS 服务器
站点到站点连接不需要 DNS。 但是，如果希望对部署到虚拟网络的资源进行名称解析，则应指定 DNS 服务器。 可以通过此设置指定 DNS 服务器，以便将其用于此虚拟网络的名称解析。 此设置不创建 DNS 服务器。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3.创建网关子网
必须为 VPN 网关创建一个网关子网。 网关子网包含 VPN 网关服务使用的 IP 地址。 在可能情况下，请使用 CIDR 块 /28 或 /27 创建网关子网。 这样可确保你有足够的 IP 地址来应对未来的网关功能。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4.创建虚拟网络网关

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5.创建本地网络网关
本地网关是指本地位置。 为本地网关指定的设置决定了可以路由到本地 VPN 设备的地址空间。

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6.配置 VPN 设备
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7.创建站点到站点 VPN 连接

在此步骤中，你在虚拟网关和本地 VPN 设备之间创建站点到站点 VPN 连接。 在开始本部分之前，请确认虚拟网络网关与局域网网关已完成创建。

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8.验证 VPN 连接

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>后续步骤
*  连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
*  有关 BGP 的信息，请参阅 [BGP 概述](vpn-gateway-bgp-overview.md)和[如何配置 BGP](vpn-gateway-bgp-resource-manager-ps.md)。


