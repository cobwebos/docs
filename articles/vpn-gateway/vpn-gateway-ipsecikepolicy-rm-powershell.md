---
title: "为 S2S VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略: Azure Resource Manager: PowerShell | Microsoft Docs"
description: "本文逐步讲解如何使用 Azure Resource Manager 和 PowerShell 为 S2S 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: yushwang
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 12502c91f7dff01651e3edcfd1dfa6a9d5ffe234
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017


---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>为 S2S VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略

本文逐步讲解如何使用 Resource Manager 部署模型和 PowerShell 为 S2S VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略。

## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a>关于 Azure VPN 网关的 IPsec 和 IKE 策略参数
IPsec 和 IKE 协议标准支持采用各种组合的各种加密算法。 请参阅[关于加密要求和 Azure VPN 网关](vpn-gateway-about-compliance-crypto.md)，了解此操作如何帮助确保跨站点的和 VNet 到 VNet 的连接满足符合性或安全性要求。

本文介绍如何创建和配置 IPsec/IKE 策略，并将其应用于新的或现有连接：

* [第 1 部分 - 创建和设置 IPsec/IKE 策略的工作工作流程](#workflow)
* [第 2 部分 - 支持的加密算法和密钥强度](#params)
* [第 3 部分 - 新建采用 IPsec/IKE 策略的 S2S VPN 连接](#crossprem)
* [第 4 部分 - 新建采用 IPsec/IKE 策略的 VNet 到 VNet 的连接](#vnet2vnet)
* [第 5 部分 - 管理（创建、添加、删除）连接的 IPsec/IKE 策略](#managepolicy)

> [!IMPORTANT]
> 1. 请注意，IPsec/IKE 策略仅适用于标准 VPN 网关和基于路由的高性能 VPN 网关。
> 2. 一个给定的连接只能指定一个策略组合。
> 3. 必须指定 IKE（主模式）和 IPsec（快速模式）的所有算法和参数。 不允许指定部分策略。
> 4. 请查阅 VPN 设备供应商规范，确保本地 VPN 设备支持该策略。 如果策略不兼容，则无法建立 S2S 或 VNet 到 VNet 的连接。

## <a name ="workflow"></a>第 1 部分 - 创建和设置 IPsec/IKE 策略的工作流程
本部分概述了对 S2S VPN 或 VNet 到 VNet 的连接创建和更新 IPsec/IKE 策略的工作流程：
1. 创建虚拟网络和 VPN 网关
2. 创建用于跨界连接的本地网关，或用于 VNet 到 VNet 的连接的另一虚拟网络和网关
3. 使用选定的算法和参数创建 IPsec/IKE 策略
4. 创建采用 IPsec/IKE 策略的连接（IPsec 或 VNet2VNet）
5. 为现有连接添加/更新/删除 IPsec/IKE 策略

本文中的说明将按下图所示设置和配置 IPsec/IKE 策略：

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name ="params"></a>第 2 部分 - 支持的加密算法和密钥强度

下表列出了支持的加密算法和密钥强度，可供客户配置。

| IPsec/IKEv2  | 选项                                                                 |
| ---              | ---                                                                         |
| IKEv2 加密 | AES256、AES192、AES128、DES3、DES                                           |
| IKEv2 完整性  | SHA384、SHA256、SHA1、MD5                                                   |
| DH 组         | ECP384、ECP256、DHGroup24、DHGroup14、DHGroup2048、DHGroup2、DHGroup1、无 |
| IPsec 加密 | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、无    |
| IPsec 完整性  | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                          |
| PFS 组        | ECP384、ECP256、PFS24、PFS2048、PFS14、PFS2、PFS1、无                     |
| QM SA 生存期*  | 秒数（整数）和 KB 数（整数）                                      |
| 流量选择器 | UsePolicyBasedTrafficSelectors** ($True/$False - default $False)            |
|                  |                                                                             |

> [!NOTE]
> * (*) 在 Azure VPN 网关上，IKEv2 主模式 SA 生存期固定为 28,800 秒
> * (**) 对于连接，将“UsePolicyBasedTrafficSelectors”设置为 $True，此时会配置 Azure VPN 网关，以连接到基于策略的本地 VPN 防火墙。 如果启用 PolicyBasedTrafficSelectors，则需确保对于本地网络（本地网关）前缀与 Azure 虚拟网络前缀的所有组合，VPN 设备都定义了与之匹配的流量选择器（而不是任意到任意）。 例如，如果本地网络前缀为 10.1.0.0/16 和 10.2.0.0/16，虚拟网络前缀为 192.168.0.0/16 和 172.16.0.0/16，则需指定以下流量选择器：
>   * 10.1.0.0/16 <====> 192.168.0.0/16
>   * 10.1.0.0/16 <====> 172.16.0.0/16
>   * 10.2.0.0/16 <====> 192.168.0.0/16
>   * 10.2.0.0/16 <====> 172.16.0.0/16

有关基于策略的流量选择器的详细信息，请参阅[连接多个基于策略的本地 VPN 设备](vpn-gateway-connect-multiple-policybased-rm-ps.md)。

## <a name ="crossprem"></a>第 3 部分 - 新建采用 IPsec/IKE 策略的 S2S VPN 连接

本部分将逐步介绍如何创建采用 IPsec/IKE 策略的 S2S VPN 连接。 下面的步骤将创建如图所示的连接：

![s2s-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

请参阅[创建 S2S VPN 连接](vpn-gateway-create-site-to-site-rm-powershell.md)，详细了解创建 S2S VPN 连接的分步介绍。

### <a name="before-you-begin"></a>开始之前
* 确保你拥有 Azure 订阅。 如果你还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 你需要安装 Azure Resource Manager PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [Azure PowerShell 概述](/powershell/azure/overview)。

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>步骤 1 - 创建虚拟网络、VPN 网关和本地网关

#### <a name="1-declare-your-variables"></a>1.声明变量
对于本练习，我们首先要声明变量。 请务必在配置生产环境时，使用自己的值来替换该值。

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.连接到订阅并创建新资源组
确保切换到 PowerShell 模式，以便使用Resource Manager cmdlet。 有关详细信息，请参阅 [将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

打开 PowerShell 控制台并连接到你的帐户。 使用下面的示例来帮助你连接：

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3.创建虚拟网络、VPN 网关和本地网关
下方示例创建了具有三个子网和 VPN 网关的虚拟网络 TestVNet1。 替换值时，请务必始终将网关子网特意命名为 GatewaySubnet。 如果命名为其他名称，网关创建将会失败。

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---creat-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>步骤 2 - 创建含 IPsec/IKE 策略的 S2S VPN 连接

#### <a name="1-create-an-ipsecike-policy"></a>1.创建 IPsec/IKE 策略
下方示例脚本使用以下算法和参数创建 IPsec/IKE 策略：
* IKEv2：AES256、SHA384、DHGroup24
* IPsec：AES256、SHA256、PFS24、SA Lifetime 7200 seconds & 2048KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 2048
```

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2.创建采用 IPsec/IKE 策略的 S2S VPN 连接
创建 S2S VPN 连接并应用之前创建的 IPsec/IKE 策略。

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

可选择将“-UsePolicyBasedTrafficSelectors $True”添加到 create connection cmdlet 中，使 Azure VPN 网关能够连接到基于策略的本地 VPN 设备，如上所述。

> [!IMPORTANT]
> 对连接指定 IPsec/IKE 策略后，Azure VPN 网关将仅发送或接收对特定连接采用指定的加密算法和密钥强度的 IPsec/IKE 提议。 必须确保连接的本地 VPN 设备使用或接受确切策略组合，否则无法建立 S2S VPN 隧道。


## <a name ="vnet2vnet"></a>第 4 部分 - 新建采用 IPsec/IKE 策略的 VNet 到 VNet 的连接
创建采用 IPsec/IKE 策略的 VNet 到 VNet 的连接的步骤与创建 S2S VPN 连接的类似。 下面的示例脚本将创建如图所示的连接：

![v2v-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

请参阅[创建 VNet 到 VNet 的连接](vpn-gateway-vnet-vnet-rm-ps.md)，详细了解创建 VNet 到 VNet 的连接的步骤。 必须完成[第 3 部分](#crossprem)，才能创建和配置 TestVNet1 与 VPN 网关。 

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a>步骤 1 - 创建第二虚拟网络和 VPN 网关

#### <a name="1-declare-your-variables"></a>1.声明变量
请务必将值替换为要用于配置的值。

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2.在新资源组中创建第二个虚拟网络和 VPN 网关
```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---creat-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>步骤 2 - 创建采用 IPsec/IKE 策略的 VNet 到 VNet 的连接
使用与创建 S2S VPN 连接类似的方法，创建 IPsec/IKE 策略，然后将其应用于新的连接。

#### <a name="1-create-an-ipsecike-policy"></a>1.创建 IPsec/IKE 策略
下方示例脚本使用以下算法和参数创建其他的 IPsec/IKE 策略：
* IKEv2：AES128、SHA1、DHGroup14
* IPsec：GCMAES128、GCMAES128、PFS14、SA Lifetime 7200 seconds & 4096KB

```powershell
$ipsecpolicy2 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 4096
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2.创建采用 IPsec/IKE 策略的 VNet 到 VNet 的连接
创建 VNet 到 VNet 的连接并应用之前创建的 IPsec/IKE 策略。 在本示例中，这两个网关位于同一订阅中。 因此，可在同一 PowerShell 会话中创建并配置采用相同 IPsec/IKE 策略的两个连接。

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> 对连接指定 IPsec/IKE 策略后，Azure VPN 网关将仅发送或接收对特定连接采用指定的加密算法和密钥强度的 IPsec/IKE 提议。 必须确保两个连接的 IPsec 策略相同，否则无法建立 VNet 到 VNet 的连接。

完成这些步骤后，将在几分钟内建立连接，你将拥有如开头所示的以下网络拓扑：

![ipsec-ike-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name ="managepolicy"></a>第 5 部分 - 更新连接的 IPsec/IKE 策略
最后一部分将介绍如何管理现有 S2S 或 VNet 到 VNet 的连接的 IPsec/IKE 策略。 下面的练习将逐步介绍如何对连接执行以下操作：

1. 显示连接的 IPsec/IKE 策略
2. 添加 IPsec/IKE 策略到连接或更新策略
3. 删除连接的 IPsec/IKE 策略

同样的步骤适用于 S2S 和 VNet 到 VNet 的连接。

> [!IMPORTANT]
> IPsec/IKE 策略仅受标准 VPN 网关和基于路由的高性能 VPN 网关支持。 它不适用于基本网关 SKU 或基于策略的 VPN 网关。

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1.显示连接的 IPsec/IKE 策略
以下示例展示如何对连接配置 IPsec/IKE 策略。 该脚本也源自上面的练习。

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最后一个命令将列出对连接配置的最新 IPsec/IKE 策略（如果有）。 以下为连接的示例输出：

    SALifeTimeSeconds   : 3600
    SADataSizeKilobytes : 2048
    IpsecEncryption     : AES256
    IpsecIntegrity      : SHA256
    IkeEncryption       : AES256
    IkeIntegrity        : SHA384
    DhGroup             : DHGroup24
    PfsGroup            : PFS24

如果没有配置 IPsec/IKE 策略，则命令 (PS> $connection6.policy) 返回的值为空。 这并不意味着未对连接配置 IPsec/IKE，而是表示没有自定义 IPsec/IKE 策略。 实际连接将使用本地 VPN 设备和 Azure VPN 网关之间协商的默认策略。

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2.为连接添加或更新 IPsec/IKE 策略
对连接添加新策略或更新现有策略的步骤相同，即：创建新策略，然后将新策略应用于连接。

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup None -SALifeTimeSeconds 3600 -SADataSizeKilobytes 2048

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

连接到基于策略的本地 VPN 设备时，若要启用“UsePolicyBasedTrafficSelectors”，请将“-UsePolicyBaseTrafficSelectors”参数添加到 cmdlet，或将其设置为 $False，禁用该选项：

```powershell
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

可再次获取连接，检查策略是否更新。

```powershell
$connection6  = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最后一行应会显示输出，如下所示：

    SALifeTimeSeconds   : 3600
    SADataSizeKilobytes : 2048
    IpsecEncryption     : GCMAES128
    IpsecIntegrity      : GCMAES128
    IkeEncryption       : AES128
    IkeIntegrity        : SHA1
    DhGroup             : DHGroup14--
    PfsGroup            : None

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3.删除连接的 IPsec/IKE 策略
从连接中删除自定义策略后，Azure VPN 网关会还原为[默认的 IPsec/IKE 提议列表](vpn-gateway-about-vpn-devices.md)，并再次与本地 VPN 设备重新协商。

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

同样，可使用相同的上述脚本来检查是否已删除连接的策略。

## <a name="next-steps"></a>后续步骤
有关基于策略的流量选择器的详细信息，请参阅[连接多个基于策略的本地 VPN 设备](vpn-gateway-connect-multiple-policybased-rm-ps.md)。

连接完成后，即可将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以获取相关步骤。

