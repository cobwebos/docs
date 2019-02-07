---
title: 配置 Azure Stack 站点到站点 VPN 连接 |Microsoft Docs
description: 了解如何为 Azure Stack 中的站点到站点 VPN 或 VNet 到 VNet 连接的 IPsec/IKE 策略
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: 96cb07ca4ede26e9bf0e5ceba28f1549061d0bf3
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815670"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>配置站点到站点 VPN 连接的 IPsec/IKE 策略

本文将指导完成为站点到站点 (S2S) VPN 配置 IPsec/IKE 策略的步骤在 Azure Stack 中的连接。

>[!NOTE]
> 您必须运行 Azure Stack 内部版本**1809年**或更高版本才能使用此功能。  如果当前正在生成 1809年之前，请更新你的 Azure Stack 系统到最新版本然后再尝试使用此功能，或按照这篇文章中的步骤。

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>VPN 网关的 IPsec 和 IKE 策略参数

IPsec 和 IKE 协议标准在各种组合中支持范围广泛的加密算法。 若要查看在 Azure Stack 中支持哪些参数，请参阅 [IPsec/IKE 参数](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)，这有助于满足符合性或安全性要求。

本文说明如何创建和配置 IPsec/IKE 策略并将应用于新的或现有连接。

## <a name="considerations"></a>注意事项

使用这些策略时，请注意以下重要注意事项：

- IPsec/IKE 策略仅适用于*标准* 并 *HighPerformance* （基于路由） 网关 Sku。

- 仅可以指定 **一个** 给定连接的策略组合。

- 必须指定 IKE（主模式）和 IPsec（快速模式）的所有算法和参数。 不允许指定部分策略。

- 请查阅 VPN 设备供应商规范，确保本地 VPN 设备支持该策略。 如果策略不兼容，无法建立站点到站点连接。

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a>第 1 部分-创建和设置 IPsec/IKE 策略的工作流

本部分概述了创建和更新在站点到站点 VPN 连接的 IPsec/IKE 策略所需的工作流：

1. 创建虚拟网络和 VPN 网关。

2. 创建跨界连接的本地网络网关。

3. 使用所选的算法和参数创建 IPsec/IKE 策略。

4. 创建采用 IPsec/IKE 策略的 IPSec 连接。

5. 添加/更新/删除现有连接的 IPsec/IKE 策略。

本文将帮助中的说明设置和配置 IPsec/IKE 策略下, 图中所示：

![设置和配置 IPsec/IKE 策略](media/azure-stack-vpn-s2s/site-to-site.png)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>第 2 部分-支持的加密算法和密钥强度

下表列出了支持的加密算法和密钥强度，可通过 Azure Stack 客户：

| IPsec/IKEv2                                          | 选项                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| IKEv2 加密                                     | AES256、AES192、AES128、DES3、DES                                        |
| IKEv2 完整性                                      | SHA384、SHA256、SHA1、MD5                                                |
| DH 组                                             | ECP384、 ECP256、 DHGroup14、 DHGroup2048、 DHGroup2、 DHGroup1、 无         |
| IPsec 加密                                     | GCMAES256、GCMAES192、GCMAES128、AES256、AES192、AES128、DES3、DES、无 |
| IPsec 完整性                                      | GCMASE256、GCMAES192、GCMAES128、SHA256、SHA1、MD5                       |
| PFS 组                                            | PFS24、ECP384、ECP256、PFS2048、PFS2、PFS1、无                         |
| QM SA 生存期                                       | (可选： 默认值是如果未指定使用)<br />                         秒 （整数; 最小 300/默认为 27000 秒）<br />                         千字节数 (整数; 最小 1024年/默认 102400000 kb 数) |                                                                          |
| 流量选择器                                     | Azure Stack 不支持策略基于流量选择器。         |

- 本地 VPN 设备配置必须匹配，或者必须包含可在 Azure IPsec/IKE 策略中指定的以下算法和参数：

  - IKE 加密算法（主模式 / 阶段 1）
  - IKE 完整性算法（主模式 / 阶段 1）
  - DH 组（主模式 / 阶段 1）
  - IPsec 加密算法（快速模式 / 阶段 2）
  - IPsec 完整性算法（快速模式 / 阶段 2）
  - PFS 组（快速模式 / 阶段 2）
  - SA 生存期是本地规范，不需匹配。

- 如果 GCMAES 用于 IPsec 加密算法，则必须选择相同的 GCMAES 算法和密钥长度为 IPsec 完整性;例如，这两者使用 GCMAES128。

- 在前面的表中：

  - IKEv2 对应于主模式或阶段 1
  - IPsec 对应于快速模式或阶段 2
  - DH 组指定在主模式或阶段 1 中使用的 Diffie-Hellmen 组
  - PFS 组指定在快速模式或阶段 2 中使用的 Diffie-Hellmen 组

- IKEv2 主模式 SA 生存期固定为 28,800 秒内对 Azure Stack 的 VPN 网关。

下表列出了自定义策略支持的相应 Diffie-Hellman 组：

| Diffie-Hellman 组 | DHGroup   | PFSGroup      | 密钥长度    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768 位 MODP  |
| 2                    | DHGroup2  | PFS2          | 1024 位 MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048 位 MODP |
| 19                   | ECP256    | ECP256        | 256 位 ECP   |
| 20                   | ECP384    | ECP384        | 384 位 ECP   |
| 24                   | DHGroup24 | PFS24         | 2048 位 MODP |

有关详细信息，请参阅 [RFC3526](https://tools.ietf.org/html/rfc3526) 并 [RFC5114](https://tools.ietf.org/html/rfc5114)。

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>第 3 部分-采用 IPsec/IKE 策略创建新的站点到站点 VPN 连接

本部分将指导你完成创建站点到站点 VPN 连接采用 IPsec/IKE 策略的步骤。 以下步骤创建连接，如下图中所示：

![site-to-site-policy](media/azure-stack-vpn-s2s/site-to-site.png)

有关更多详细创建站点到站点 VPN 连接的分步说明，请参阅 [创建站点到站点 VPN 连接](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)。

### <a name="prerequisites"></a>系统必备

在开始之前，请确保具备以下先决条件：

- Azure 订阅。 如果还没有 Azure 订阅，则可以激活您 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，或注册 [免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

- Azure 资源管理器 PowerShell cmdlet。 请参阅 [安装适用于 Azure Stack 的 PowerShell](../azure-stack-powershell-install.md) 有关安装 PowerShell cmdlet 的详细信息。

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>步骤 1 - 创建虚拟网络、VPN 网关和本地网关

#### <a name="1-declare-variables"></a>1.声明变量

对于此练习，开始通过声明以下变量。 请务必配置生产环境时的占位符替换为自己的值：

```powershell
$Sub1 = "\<YourSubscriptionName\>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.连接到订阅并创建新资源组

确保切换到 PowerShell 模式，以便使用Resource Manager cmdlet。 有关详细信息，请参阅 [连接到 Azure Stack 使用 PowerShell 以用户身份](azure-stack-powershell-configure-user.md)。

打开 PowerShell 控制台并连接到帐户。 使用下面的示例来帮助连接：

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3.创建虚拟网络、VPN 网关和本地网关

下面的示例创建的虚拟网络**TestVNet1**，带三个子网，VPN 网关。 替换值时, 非常重要，您始终命名网关子网专门**GatewaySubnet**。 如果命名为其他名称，网关创建会失败。

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet"
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>步骤 2-创建采用 IPsec/IKE 策略的站点到站点 VPN 连接

#### <a name="1-create-an-ipsecike-policy"></a>1.创建 IPsec/IKE 策略

此示例脚本使用以下算法和参数创建 IPsec/IKE 策略：

- IKEv2：AES128, SHA1, DHGroup14
- IPsec：AES256、 SHA256，无、 SA 生存期 14400 秒和 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

如果将 GCMAES 用于 IPsec，必须为 IPsec 加密和完整性使用相同的 GCMAES 算法和密钥长度。

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2.创建采用 IPsec/IKE 策略的站点到站点 VPN 连接

创建站点到站点 VPN 连接并应用前面创建的 IPsec/IKE 策略。

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> 对连接指定 IPsec/IKE 策略后，Azure VPN 网关将仅发送或接受对该特定连接采用指定加密算法和密钥强度的 IPsec/IKE 方案。 请确保连接你的本地 VPN 设备使用或接受确切策略组合，否则为将不会建立站点到站点 VPN 隧道。

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>第 4-部分连接的更新 IPsec/IKE 策略

上一部分介绍了如何管理现有的站点到站点连接的 IPsec/IKE 策略。 以下部分将指导完成对连接执行以下操作：

1. 显示连接的 IPsec/IKE 策略
2. 为连接添加或更新 IPsec/IKE 策略
3. 删除连接的 IPsec/IKE 策略

> [!NOTE]
> 支持 IPsec/IKE 策略 *标准* 并 *HighPerformance* 基于路由的 VPN 网关仅。 它不适用于*基本*网关 SKU。

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1.显示连接的 IPsec/IKE 策略

以下示例演示如何对连接配置 IPsec/IKE 策略。 脚本也源自于上一练习：

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最后一个命令将列出当前配置的连接上，如果有的 IPsec/IKE 策略。 下面的示例是连接的示例输出：

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

如果没有任何 IPsec/IKE 策略配置，则命令`$connection6.policy`获取空返回。 它并不意味着未在连接; 上配置 IPsec/IKE这还意味着，任何自定义 IPsec/IKE 策略。 实际连接使用本地 VPN 设备和 Azure VPN 网关之间协商的默认策略。

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2.为连接添加或更新 IPsec/IKE 策略

若要添加新策略或更新现有策略的连接上的步骤都相同： 创建新的策略，然后将新策略应用到连接。

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

就可以再次检查策略已更新的连接：

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

最后一行应会显示输出，如以下示例所示：

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3.删除连接的 IPsec/IKE 策略

一旦从连接中删除自定义策略，Azure VPN 网关将恢复为 [默认 IPsec/IKE 方案](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)，并与你的本地 VPN 设备重新进行协商。

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name
$Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = “AzS123”
$currentpolicy = $connection6.IpsecPolicies\[0\]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

可使用相同脚本来检查是否已删除连接的策略。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 的 VPN 网关配置设置](azure-stack-vpn-gateway-settings.md)
