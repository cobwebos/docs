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
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: e839f99d599d682c31bd4c6d03a6a58268f05b7c
ms.lasthandoff: 04/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>在 Azure 门户中创建站点到站点连接

本文介绍如何使用 Azure 门户创建站点到站点 VPN 网关连接，以便从本地网络连接到 VNet。 本文中的步骤适用于 Resource Manager 部署模型。 也可使用不同的部署工具或部署模型来创建此配置，方法是从以下列表中选择另一选项：

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager - CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [经典 - Azure 门户](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [经典 - 经典门户](vpn-gateway-site-to-site-create.md)
> 
>

![站点到站点 VPN 网关跨界连接示意图](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

使用站点到站点 VPN 网关连接，通过 IPsec/IKE（IKEv1 或 IKEv2）VPN 隧道将本地网络连接到 Azure 虚拟网络。 此类型的连接要求位于本地的 VPN 设备分配有一个面向外部的公共 IP 地址。 有关 VPN 网关的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请验证你是否符合以下条件：

* 验证你是否需要使用 Resource Manager 部署模型。 [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* 一台兼容的 VPN 设备和能够对其进行配置的人员。 有关兼容的 VPN 设备和设备配置的详细信息，请参阅[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)。
* 一个用于 VPN 设备的面向外部的公共 IPv4 IP 地址。 此 IP 地址不得位于 NAT 之后。
* 如果不熟悉本地网络配置中的 IP 地址范围，则需咨询能够为你提供此类详细信息的人员。 创建此配置时，必须指定 IP 地址范围前缀，Azure 会将该前缀路由到本地位置。 本地网络的任何子网都不得与要连接到的虚拟网络子网重叠。 

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

虚拟网关使用的网关子网包含 VPN 网关服务使用的 IP 地址。 创建网关子网时，必须将其命名为“GatewaySubnet”。 如果以其他名称为其命名，则连接配置将会失败。

指定的网关子网的大小取决于要创建的 VPN 网关配置。 尽管创建的网关子网最小可为 /29，但建议选择 /27 或 /28，创建包含更多地址的更大子网。 指定的网关子网的大小取决于要创建的 VPN 网关配置。 尽管创建的网关子网最小可为 /29，但建议选择 /27 或 /28，创建包含更多地址的更大子网。 使用更大的网关子网可以有足够的 IP 地址来应对未来可能会有的配置。

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]


## <a name="VNetGateway"></a>4.创建 VPN 网关

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5.创建本地网关

本地网络网关通常是指本地位置。 你可以为站点提供一个名称供 Azure 引用，然后指定本地 VPN 设备的 IP 地址，以便创建一个连接来连接到该设备。 此外还可指定 IP 地址前缀，以便通过 VPN 网关将其路由到 VPN 设备。 指定的地址前缀是位于本地网络的前缀。 如果本地网络出现变化，可以轻松更新这些前缀。

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6.配置 VPN 设备
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

若要使用 Azure 门户查找 VPN 网关的公共 IP 地址，请导航到“虚拟网关”，然后单击网关的名称。

## <a name="CreateConnection"></a>7.创建 VPN 连接

在虚拟网关和本地 VPN 设备之间创建站点到站点 VPN 连接。

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8.验证 VPN 连接

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>后续步骤
*  连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
*  有关 BGP 的信息，请参阅 [BGP 概述](vpn-gateway-bgp-overview.md)和[如何配置 BGP](vpn-gateway-bgp-resource-manager-ps.md)。


