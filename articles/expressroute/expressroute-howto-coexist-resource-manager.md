---
title: "配置可以并存的 ExpressRoute 连接和站点到站点 VPN 连接：Resource Manager：Azure | Microsoft Docs"
description: "本文将指导你为 Resource Manager 模型配置可共存的 ExpressRoute 和站点到站点 VPN 连接。"
documentationcenter: na
services: expressroute
author: charwen
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: charwen,cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: ffa791cf4c4be15645a67fef4e94bf6ebdc42a6a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017


---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>设置 ExpressRoute 和站点到站点并存连接
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - 经典](expressroute-howto-coexist-classic.md)
> 
> 

配置站点到站点 VPN 和 ExpressRoute 共存连接具有多项优势。 可以将站点到站点 VPN 配置为 ExressRoute 的安全故障转移路径，或者使用站点到站点 VPN 连接到不是通过 ExpressRoute 进行连接的站点。 我们在本文中介绍这两种方案的配置步骤。 本文适用于 Resource Manager 部署模型并使用 PowerShell。 此配置在 Azure 门户中不可用。

> [!IMPORTANT]
> 按以下说明进行操作之前，必须预先配置 ExpressRoute 线路。 在继续操作之前，请务必遵循相关指南来[创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)和[配置路由](expressroute-howto-routing-arm.md)。
> 
> 

## <a name="limits-and-limitations"></a>限制和局限性
* **不支持传输路由。** 无法在通过站点到站点 VPN 连接的本地网络与通过 ExpressRoute 连接的本地网络之间进行路由（通过 Azure）。
* **不支持基本 SKU 网关。** 必须为 [ExpressRoute 网关](expressroute-about-virtual-network-gateways.md)和 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)使用非基本 SKU 网关。
* **仅支持基于路由的 VPN 网关。** 必须使用基于路由的 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
* **应该为 VPN 网关配置静态路由。** 如果本地网络同时连接到 ExpressRoute 和站点到站点 VPN，则必须在本地网络中配置静态路由，以便将站点到站点 VPN 连接路由到公共 Internet。
* **必须先配置 ExpressRoute 网关，然后再链接到线路。** 添加站点到站点 VPN 网关前，必须先创建 ExpressRoute 网关，然后再将其链接到线路。

## <a name="configuration-designs"></a>配置设计
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>将站点到站点 VPN 配置为 ExpressRoute 的故障转移路径
你可以将站点到站点 VPN 连接配置为 ExpressRoute 的备份。 这仅适用于链接到 Azure 专用对等路径的虚拟网络。 对于可通过 Azure 公共线路和 Microsoft 对等线路访问的服务，没有基于 VPN 的故障转移解决方案。 ExpressRoute 线路始终是主链接。 仅当 ExpressRoute 线路失败时，数据才会流经站点到站点 VPN 路径。

> [!NOTE]
> 虽然在两个路由相同的情况下 ExpressRoute 线路优先于站点到站点 VPN，Azure 仍会使用最长的前缀匹配来选择指向数据包目标的路由。
> 
> 

![共存](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>配置站点到站点 VPN，以便连接到不通过 ExpressRoute 进行连接的站点
你可以对网络进行配置，使得部分站点通过站点到站点 VPN 直接连接到 Azure，部分站点通过 ExpressRoute 进行连接。 

![共存](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> 不能将虚拟网络配置为转换路由器。
> 
> 

## <a name="selecting-the-steps-to-use"></a>选择要使用的步骤
有两组不同的过程可供选择。 你选择的配置过程将取决于你有要连接到的现有虚拟网络，还是要创建新的虚拟网络。

* 我没有 VNet，需要创建一个。
  
    如果你还没有虚拟网络，此过程将指导你使用 Resource Manager 部署模型创建新的虚拟网络，然后创建新的 ExpressRoute 和站点到站点 VPN 连接。 若要配置虚拟网络，请遵循[创建新的虚拟网络和并存连接](#new)中的步骤。
* 我已有一个 Resource Manager 部署模型 VNet。
  
    你可能已在具有现有站点到站点 VPN 连接或 ExpressRoute 连接的位置拥有虚拟网络。 [为现有的 VNet 配置并存连接](#add) 部分将指导你删除网关，然后创建新的 ExpressRoute 连接和站点到站点 VPN 连接。 在创建新连接时，必须按照特定的顺序完成步骤。 不要按照其他文章中的说明来创建网关和连接。
  
    在此过程中，创建可以共存的连接需要你删除网关，然后配置新网关。 在删除并重新创建网关和连接时，跨界连接将会停止工作，但你无需将任何 VM 或服务迁移到新的虚拟网络。 在你配置网关时，如果进行了相应配置，你的 VM 和服务仍可以通过负载均衡器与外界通信。

## <a name="new"></a>创建新的虚拟网络和并存连接
本过程将指导你创建 VNet 以及将共存的站点到站点连接和 ExpressRoute 连接。

1. 安装最新版本的 Azure PowerShell cmdlet。 有关安装 cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 针对此配置使用的 cmdlet 可能与你熟悉的 cmdlet 稍有不同。 请务必使用说明内容中指定的 cmdlet。
2. 登录你的帐户并设置环境。

  ```powershell
  login-AzureRmAccount
  Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
  $location = "Central US"
  $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
  $VNetASN = 65010
  ```
3. 创建包括网关子网的虚拟网络。 有关虚拟网络配置的详细信息，请参阅 [Azure 虚拟网络配置](../virtual-network/virtual-networks-create-vnet-arm-ps.md)。
   
   > [!IMPORTANT]
   > 网关子网必须是 /27 或更短的前缀（例如 /26 或 /25）。
   > 
   > 
   
    创建新的 VNet。

  ```powershell
  $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
  ```
   
    添加子网。

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    保存 VNet 配置。

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
4. <a name="gw"></a>创建 ExpressRoute 网关。 有关 ExpressRoute 网关配置的详细信息，请参阅 [ExpressRoute 网关配置](expressroute-howto-add-gateway-resource-manager.md)。 GatewaySKU 必须是 *Standard*、*HighPerformance* 或 *UltraPerformance*。

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
  ```
5. 将 ExpressRoute 网关连接到 ExpressRoute 线路。 完成此步骤后，则已通过 ExpressRoute 建立本地网络与 Azure 之间的连接。 有关链接操作的详细信息，请参阅 [将 VNet 链接到 ExpressRoute](expressroute-howto-linkvnet-arm.md)。

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
  New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
  ```
6. <a name="vpngw"></a>接下来，创建站点到站点 VPN 网关。 有关 VPN 网关配置的详细信息，请参阅[使用站点到站点连接配置 VNet](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)。 GatewaySKU 必须是 *Standard*、*HighPerformance* 或 *UltraPerformance*。 VpnType 必须为 *RouteBased*。

  ```powershell
  $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
  $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
  $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
  New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
  ```
   
    Azure VPN 网关支持 BGP 路由协议。 通过在以下命令中添加 -Asn 开关，可为该虚拟网络指定 ASN（AS 编号）。 若未指定该参数，将默认为 AS 编号 65515。

  ```powershell
  $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -Asn $VNetASN
  ```
   
    可以在 $azureVpn.BgpSettings.BgpPeeringAddress 和 $azureVpn.BgpSettings.Asn 中找到 Azure 用于 VPN 网关的 BGP 对等 IP 和 AS 编号。 有关详细信息，请参阅为 [Azure VPN 网关配置 BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)。
7. 创建一个本地站点 VPN 网关实体。 此命令不会配置本地 VPN 网关， 而是允许你提供本地网关设置（如公共 IP 和本地地址空间），以便 Azure VPN 网关可以连接到它。
   
    如果本地 VPN 设备仅支持静态路由，可按以下方式配置静态路由：

  ```powershell
  $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
  ```
   
    如果本地 VPN 设备支持 BGP，并且想要启用动态路由，那么需要知道本地 VPN 设备使用的 BGP 对等 IP 和 AS 编号。

  ```powershell
  $localVPNPublicIP = "<Public IP>"
  $localBGPPeeringIP = "<Private IP for the BGP session>"
  $localBGPASN = "<ASN>"
  $localAddressPrefix = $localBGPPeeringIP + "/32"
  $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
  ```
8. 配置本地 VPN 设备以连接到新的 Azure VPN 网关。 有关 VPN 设备配置的详细信息，请参阅 [VPN 设备配置](../vpn-gateway/vpn-gateway-about-vpn-devices.md)。
9. 将 Azure 上的站点到站点 VPN 网关连接到本地网关。

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
  ```

## <a name="add"></a>为现有的 VNet 配置并存连接
如果你已经有了一个虚拟网络，请检查网关子网大小。 如果网关子网为 /28 或 /29，则必须先删除虚拟网络网关，然后增加网关子网大小。 本部分的步骤说明如何这样做。

如果网关子网为 /27 或更大，且虚拟网络是通过 ExpressRoute 连接的，则可跳过下面的步骤，转到前一部分的 [“步骤 6 - 创建站点到站点 VPN 网关”](#vpngw) 。 

> [!NOTE]
> 如果你删除的是现有网关，则当你进行此配置时，本地系统将失去与虚拟网络建立的连接。 
> 
> 

1. 你需要安装 Azure PowerShell cmdlet 的最新版本。 有关安装 cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 针对此配置使用的 cmdlet 可能与你熟悉的 cmdlet 稍有不同。 请务必使用说明内容中指定的 cmdlet。 
2. 删除现有的 ExpressRoute 或站点到站点 VPN 网关。

  ```powershell 
  Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
  ```
3. 删除网关子网。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
  ```
4. 添加为 /27 或更大的网关子网。
   
   > [!NOTE]
   > 如果你因为虚拟网络中没有剩余足够的 IP 地址而无法增加网关子网大小，则需增加 IP 地址空间。
   > 
   > 

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
  Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
  ```
   
    保存 VNet 配置。

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
5. 此时，你拥有不带网关的虚拟网络。 若要创建新网关并完成连接，可以转到 [步骤 4 - 创建 ExpressRoute 网关](#gw)（可以在前一组步骤中找到）。

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>将点到站点配置添加到 VPN 网关
可以按照下面的步骤将点到站点配置添加到共存设置中的 VPN 网关。

1. 添加 VPN 客户端地址池。

  ```powershell
  $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
  Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
  ```
2. 为你的 VPN 网关将 VPN 根证书上传到 Azure。 在此示例中，假定根证书存储在运行以下 PowerShell cmdlet 的本地计算机中。

  ```powershell
  $p2sCertFullName = "RootErVpnCoexP2S.cer" 
  $p2sCertMatchName = "RootErVpnCoexP2S" 
  $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
  if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
  $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
  ```

有关点到站点 VPN 的详细信息，请参阅 [配置点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)。

## <a name="next-steps"></a>后续步骤
有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

