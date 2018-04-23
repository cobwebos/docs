---
title: 使用 VNet 到 VNet 连接将 Azure 虚拟网络连接到另一 VNet：PowerShell | Microsoft Docs
description: 使用 VNet 到 VNet 连接和 PowerShell 将虚拟网络连接起来。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: a96326e23a2ed913ccab8a23666a4f408157d3da
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>使用 PowerShell 配置 VNet 到 VNet VPN 网关连接

本文介绍如何使用 VNet 到 VNet 连接类型来连接虚拟网络。 虚拟网络可以位于相同或不同的区域，也可以来自相同或不同的订阅。 从不同的订阅连接 VNet 时，订阅不需要与相同的 Active Directory 租户相关联。

本文中的步骤适用于 Resource Manager 部署模型并使用 PowerShell。 也可使用不同的部署工具或部署模型来创建此配置，方法是从以下列表中选择另一选项：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure 门户（经典）](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [连接不同的部署模型 - Azure 门户](vpn-gateway-connect-different-deployment-models-portal.md)
> * [连接不同的部署模型 - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about"></a>关于连接 VNet

可通过多种方式来连接 VNet。 以下各节介绍了如何通过不同方式来连接虚拟网络。

### <a name="vnet-to-vnet"></a>VNet 到 VNet

配置一个 VNet 到 VNet 连接即可轻松地连接 VNet。 使用 VNet 到 VNet 连接类型 (VNet2VNet) 将一个虚拟网络连接到另一个虚拟网络类似于创建到本地位置的站点到站点 IPsec 连接。  这两种连接类型都使用 VPN 网关来提供使用 IPsec/IKE 的安全隧道，二者在通信时使用同样的方式运行。 连接类型的差异在于本地网关的配置方式。 创建 VNet 到 VNet 连接时，看不到本地网关地址空间。 它是自动创建并填充的。 如果更新一个 VNet 的地址空间，另一个 VNet 会自动知道路由到更新的地址空间。 与在 VNet 之间创建站点到站点连接相比，创建 VNet 到 VNet 连接通常速度更快且更容易。

### <a name="site-to-site-ipsec"></a>站点到站点 (IPsec)

如果要进行复杂的网络配置，则与使用 VNet 到 VNet 步骤相比，使用[站点到站点](vpn-gateway-create-site-to-site-rm-powershell.md)步骤来连接 VNet 会更好。 使用站点到站点步骤时，可以手动创建和配置本地网关。 每个 VNet 的本地网关都将其他 VNet 视为本地站点。 这样可以为本地网关指定路由流量所需的其他地址空间。 如果 VNet 的地址空间更改，则需根据更改更新相应的本地网关。 它不自动进行更新。

### <a name="vnet-peering"></a>VNet 对等互连

可以考虑使用 VNet 对等互连来连接 VNet。 VNet 对等互连不使用 VPN 网关，并且有不同的约束。 另外，[VNet 对等互连定价](https://azure.microsoft.com/pricing/details/virtual-network)的计算不同于 [VNet 到 VNet VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway)的计算。 有关详细信息，请参阅 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)。

## <a name="why"></a>为何创建 VNet 到 VNet 连接？

你可能会出于以下原因而使用 VNet 到 VNet 连接来连接虚拟网络：

* **跨区域地域冗余和地域存在**

  * 可以使用安全连接设置自己的异地复制或同步，而无需借助于面向 Internet 的终结点。
  * 使用 Azure 流量管理器和负载均衡器，可以设置支持跨多个 Azure 区域实现异地冗余的高可用性工作负荷。 一个重要的示例就是对分布在多个 Azure 区域中的可用性组设置 SQL Always On。
* **具有隔离或管理边界的区域多层应用程序**

  * 在同一区域中，由于存在隔离或管理要求，可以设置多个虚拟网络连接在一起的多层应用程序。

可以将 VNet 到 VNet 通信与多站点配置组合使用。 这样，便可以建立将跨界连接与虚拟网络间连接相结合的网络拓扑。

## <a name="steps"></a>应使用哪些 VNet 到 VNet 步骤？

在本文中，可以看到两组不同的步骤。 一组步骤适用于[驻留在同一订阅中的 VNet](#samesub)，另一组适用于[驻留在不同订阅中的 VNet](#difsub)。
两组的主要差异是，配置位于不同订阅中的 VNet 的连接时，必须使用单独的 PowerShell 会话。 

就本练习来说，可以将配置组合起来，也可以只是选择要使用的配置。 所有配置使用 VNet 到 VNet 连接类型。 网络流量在彼此直接连接的 VNet 之间流动。 在此练习中，流量不从 TestVNet4 路由到 TestVNet5。

* [驻留在同一订阅中的 VNet](#samesub)：此配置的步骤使用 TestVNet1 和 TestVNet4。

  ![v2v 示意图](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [驻留在不同订阅中的 VNet](#difsub)：此配置的步骤使用 TestVNet1 和 TestVNet5。

  ![v2v 示意图](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="samesub"></a>如何连接同一订阅中的 VNet

### <a name="before-you-begin"></a>开始之前

在开始之前，需要安装最新版本的 Azure 资源管理器 PowerShell cmdlet（4.0 或更高版本）。 有关安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

### <a name="Step1"></a>步骤 1 - 规划 IP 地址范围

以下步骤将创建两个虚拟网络，以及它们各自的网关子网和配置。 然后在两个 VNet 之间创建 VPN 连接。 必须计划用于网络配置的 IP 地址范围。 请记住，必须确保没有任何 VNet 范围或本地网络范围存在任何形式的重叠。 在这些示例中，我们没有包括 DNS 服务器。 如果需要虚拟网络的名称解析，请参阅[名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

示例中使用以下值：

**TestVNet1 的值：**

* VNet 名称：TestVNet1
* 资源组：TestRG1
* 位置：美国东部
* TestVNet1：10.11.0.0/16 和 10.12.0.0/16
* FrontEnd：10.11.0.0/24
* BackEnd：10.12.0.0/24
* GatewaySubnet：10.12.255.0/27
* GatewayName：VNet1GW
* 公共 IP：VNet1GWIP
* VPNType：RouteBased
* Connection(1to4)：VNet1toVNet4
* Connection(1to5)：VNet1toVNet5（适用于不同订阅中的 VNet）
* ConnectionType：VNet2VNet

**TestVNet4 的值：**

* VNet 名称：TestVNet4
* TestVNet2：10.41.0.0/16 和 10.42.0.0/16
* FrontEnd：10.41.0.0/24
* BackEnd：10.42.0.0/24
* GatewaySubnet：10.42.255.0/27
* 资源组：TestRG4
* 位置：美国西部
* GatewayName：VNet4GW
* 公共 IP：VNet4GWIP
* VPNType：RouteBased
* 连接：VNet4toVNet1
* ConnectionType：VNet2VNet


### <a name="Step2"></a>步骤 2 - 创建并配置 TestVNet1

1. 声明变量。 本示例使用此练习中的值来声明变量。 在大多数情况下，应将这些值替换为自己的值。 但是，如果执行这些步骤的目的是熟悉这种类型的配置，可以直接使用这些变量。 根据需要修改变量，将变量复制并粘贴到 PowerShell 控制台中。

  ```powershell
  $Sub1 = "Replace_With_Your_Subcription_Name"
  $RG1 = "TestRG1"
  $Location1 = "East US"
  $VNetName1 = "TestVNet1"
  $FESubName1 = "FrontEnd"
  $BESubName1 = "Backend"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.11.0.0/16"
  $VNetPrefix12 = "10.12.0.0/16"
  $FESubPrefix1 = "10.11.0.0/24"
  $BESubPrefix1 = "10.12.0.0/24"
  $GWSubPrefix1 = "10.12.255.0/27"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconf1"
  $Connection14 = "VNet1toVNet4"
  $Connection15 = "VNet1toVNet5"
  ```

2. 连接到帐户。 使用下面的示例来帮助连接：

  ```powershell
  Connect-AzureRmAccount
  ```

  检查该帐户的订阅。

  ```powershell
  Get-AzureRmSubscription
  ```

  指定要使用的订阅。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub1
  ```
3. 创建新的资源组。

  ```powershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```
4. 创建 TestVNet1 的子网配置。 本示例创建一个名为 TestVNet1 的虚拟网络和三个子网，这三个子网分别名为 GatewaySubnet、FrontEnd 和 Backend。 替换值时，请务必始终将网关子网特意命名为 GatewaySubnet。 如果命名为其他名称，网关创建会失败。

  下面的示例使用先前设置的变量。 在本示例中，网关子网使用 /27。 尽管创建的网关子网最小可为 /29，但建议至少选择 /28 或 /27，创建包含更多地址的更大子网。 这样便可以留出足够多的地址，满足将来可能需要使用的其他配置。

  ```powershell
  $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
  $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
  $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
  ```
5. 创建 TestVNet1。

  ```powershell
  New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
  ```
6. 请求一个公共 IP 地址，以分配给要为 VNet 创建的网关。 注意，AllocationMethod 是动态的。 无法指定要使用的 IP 地址。 它动态分配到网关。 

  ```powershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
  ```
7. 创建网关配置。 网关配置定义要使用的子网和公共 IP 地址。 使用本示例创建网关配置。

  ```powershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```
8. 为 TestVNet1 创建网关。 在此步骤中，将为 TestVNet1 创建虚拟网络网关。 VNet 到 VNet 配置需要基于路由的 VPN 类型。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-3---create-and-configure-testvnet4"></a>步骤 3 - 创建并配置 TestVNet4

配置 TestVNet1 后，即可创建 TestVNet4。 遵循以下步骤，并根据需要替换为自己的值。 此步骤可在相同的 PowerShell 会话中完成，因为其位于相同的订阅中。

1. 声明变量。 请务必将值替换为用于配置的值。

  ```powershell
  $RG4 = "TestRG4"
  $Location4 = "West US"
  $VnetName4 = "TestVNet4"
  $FESubName4 = "FrontEnd"
  $BESubName4 = "Backend"
  $GWSubName4 = "GatewaySubnet"
  $VnetPrefix41 = "10.41.0.0/16"
  $VnetPrefix42 = "10.42.0.0/16"
  $FESubPrefix4 = "10.41.0.0/24"
  $BESubPrefix4 = "10.42.0.0/24"
  $GWSubPrefix4 = "10.42.255.0/27"
  $GWName4 = "VNet4GW"
  $GWIPName4 = "VNet4GWIP"
  $GWIPconfName4 = "gwipconf4"
  $Connection41 = "VNet4toVNet1"
  ```
2. 创建新的资源组。

  ```powershell
  New-AzureRmResourceGroup -Name $RG4 -Location $Location4
  ```
3. 创建 TestVNet4 的子网配置。

  ```powershell
  $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
  $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
  $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
  ```
4. 创建 TestVNet4。

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
  ```
5. 请求公共 IP 地址。

  ```powershell
  $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
  -Location $Location4 -AllocationMethod Dynamic
  ```
6. 创建网关配置。

  ```powershell
  $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
  $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
  $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
  ```
7. 创建 TestVNet4 网关。 创建网关通常需要 45 分钟或更长的时间，具体取决于所选的网关 SKU。

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
  -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-4---create-the-connections"></a>步骤 4 - 创建连接

1. 获取两个虚拟网关。 如果两个网关属于同一订阅（如示例所示），则可以在同一 PowerShell 会话中完成此步骤。

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
  ```
2. 创建 TestVNet1 到 TestVNet4 的连接。 在此步骤中，创建 TestVNet1 到 TestVNet4 的连接。 示例中引用了共享密钥。 可以对共享密钥使用自己的值。 共享密钥必须与两个连接匹配，这一点非常重要。 创建连接可能需要简短的一段时间才能完成。

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
3. 创建 TestVNet4 到 TestVNet1 的连接。 此步骤类似上面的步骤，只不过是创建 TestVNet4 到 TestVNet1 的连接。 确保共享密钥匹配。 几分钟后会建立连接。

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
  -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. 验证连接。 请参阅 [如何验证连接](#verify)部分。

## <a name="difsub"></a>如何连接不同订阅中的 VNet

在此方案中，连接 TestVNet1 和 TestVNet5。 TestVNet1 和 TestVNet5 驻留在不同订阅中。 订阅不需要与相同的 Active Directory 租户相关联。 这些步骤与上一组的差别在于，一些配置步骤需要在第二个订阅的环境的单独 PowerShell 会话中执行。 尤其是当两个订阅属于不同的组织时。

### <a name="step-5---create-and-configure-testvnet1"></a>步骤 5 - 创建并配置 TestVNet1

必须完成前述部分的[步骤 1](#Step1) 和[步骤 2](#Step2)，才能创建并配置 TestVNet1 及其 VPN 网关。 就此配置来说，不需创建前一部分的 TestVNet4，虽然创建后不会与这些步骤冲突。 完成步骤 1 和步骤 2 后，继续执行步骤 6，创建 TestVNet5。 

### <a name="step-6---verify-the-ip-address-ranges"></a>步骤 6 - 验证 IP 地址范围

必须确保新虚拟网络的 IP 地址空间 TestVNet5 不与任何 VNet 范围或局域网网关范围重叠。 在本示例中，虚拟网络可能属于不同的组织。 对于本练习，可以对 TestVNet5 使用以下值：

**TestVNet5 的值：**

* VNet 名称：TestVNet5
* 资源组：TestRG5
* 位置：日本东部
* TestVNet5：10.51.0.0/16 和 10.52.0.0/16
* FrontEnd：10.51.0.0/24
* BackEnd：10.52.0.0/24
* GatewaySubnet：10.52.255.0.0/27
* GatewayName：VNet5GW
* 公共 IP：VNet5GWIP
* VPNType：RouteBased
* 连接：VNet5toVNet1
* ConnectionType：VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>步骤 7 - 创建并配置 TestVNet5

必须在新订阅环境中完成此步骤。 此部分可能由拥有订阅的不同组织的管理员执行。

1. 声明变量。 请务必将值替换为用于配置的值。

  ```powershell
  $Sub5 = "Replace_With_the_New_Subcription_Name"
  $RG5 = "TestRG5"
  $Location5 = "Japan East"
  $VnetName5 = "TestVNet5"
  $FESubName5 = "FrontEnd"
  $BESubName5 = "Backend"
  $GWSubName5 = "GatewaySubnet"
  $VnetPrefix51 = "10.51.0.0/16"
  $VnetPrefix52 = "10.52.0.0/16"
  $FESubPrefix5 = "10.51.0.0/24"
  $BESubPrefix5 = "10.52.0.0/24"
  $GWSubPrefix5 = "10.52.255.0/27"
  $GWName5 = "VNet5GW"
  $GWIPName5 = "VNet5GWIP"
  $GWIPconfName5 = "gwipconf5"
  $Connection51 = "VNet5toVNet1"
  ```
2. 连接到订阅 5。 打开 PowerShell 控制台并连接到帐户。 使用下面的示例来帮助你连接：

  ```powershell
  Connect-AzureRmAccount
  ```

  检查该帐户的订阅。

  ```powershell
  Get-AzureRmSubscription
  ```

  指定要使用的订阅。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName $Sub5
  ```
3. 创建新的资源组。

  ```powershell
  New-AzureRmResourceGroup -Name $RG5 -Location $Location5
  ```
4. 创建 TestVNet5 的子网配置。

  ```powershell
  $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
  $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
  $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
  ```
5. 创建 TestVNet5。

  ```powershell
  New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
  -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
  ```
6. 请求公共 IP 地址。

  ```powershell
  $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
  -Location $Location5 -AllocationMethod Dynamic
  ```
7. 创建网关配置。

  ```powershell
  $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
  $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
  $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
  ```
8. 创建 TestVNet5 网关。

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
  -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
  ```

### <a name="step-8---create-the-connections"></a>步骤 8 - 创建连接

在本示例中，由于网关位于不同订阅中，因此将此步骤拆分为了两个 PowerShell 会话，分别标记为 [订阅 1] 和 [订阅 5]。

1. **[订阅 1]** 获取订阅 1 的虚拟网关。 登录并连接到订阅 1，并运行以下示例：

  ```powershell
  $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
  ```

  复制以下元素的输出，并通过电子邮件或其他方式将输出发送给订阅 5 的管理员。

  ```powershell
  $vnet1gw.Name
  $vnet1gw.Id
  ```

  这两个元素的值类似于以下示例输出：

  ```
  PS D:\> $vnet1gw.Name
  VNet1GW
  PS D:\> $vnet1gw.Id
  /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```
2. **[订阅 5]** 获取订阅 5 的虚拟网关。 登录并连接到订阅 5，运行以下示例：

  ```powershell
  $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
  ```

  复制以下元素的输出，并通过电子邮件或其他方式将输出发送给订阅 1 的管理员。

  ```powershell
  $vnet5gw.Name
  $vnet5gw.Id
  ```

  这两个元素的值类似于以下示例输出：

  ```
  PS C:\> $vnet5gw.Name
  VNet5GW
  PS C:\> $vnet5gw.Id
  /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```
3. **[订阅 1]** 创建 TestVNet1 到 TestVNet5 连接。 在此步骤中，创建 TestVNet1 到 TestVNet5 的连接。 此处的差别在于无法直接获取 $vnet5gw ，因为它位于不同订阅中。 需要使用上述步骤中从订阅 1 传递的值来创建新的 PowerShell 对象。 使用下面的示例。 将名称、ID 和共享密钥替换为自己的值。 共享密钥必须与两个连接匹配，这一点非常重要。 创建连接可能需要简短的一段时间才能完成。

  连接到订阅 1，并运行以下示例：

  ```powershell
  $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet5gw.Name = "VNet5GW"
  $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
  $Connection15 = "VNet1toVNet5"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
4. **[订阅 5]** 创建 TestVNet5 到 TestVNet1 连接。 此步骤类似上面的步骤，只不过是创建 TestVNet5 到 TestVNet1 的连接。 此处也适用基于从订阅 1 获取的值创建 PowerShell 对象这一相同过程。 在此步骤中，请确保共享密钥匹配。

  连接到订阅 5，并运行以下示例：

  ```powershell
  $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
  $vnet1gw.Name = "VNet1GW"
  $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
  $Connection51 = "VNet5toVNet1"
  New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

## <a name="verify"></a>如何验证连接

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="faq"></a>VNet 到 VNet 常见问题解答

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>后续步骤

* 连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机文档](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。
* 有关 BGP 的信息，请参阅 [BGP 概述](vpn-gateway-bgp-overview.md)和[如何配置 BGP](vpn-gateway-bgp-resource-manager-ps.md)。
