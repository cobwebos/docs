---
title: Azure 澳大利亚的 azure VPN 网关
description: 在 Azure 澳大利亚实现 VPN 网关以符合 ISM 要求并有效地保护澳大利亚政府机构
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571843"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>Azure 澳大利亚的 azure VPN 网关

任何公有云的关键服务是将云资源和服务与现有本地系统安全连接。  在 Azure 中提供此功能的服务是 Azure VPN 网关 (VPN 网关)。 本文概述了将 VPN 网关配置为符合澳大利亚信号国防局 (.ASD)[信息安全手册控制](https://acsc.gov.au/infosec/ism/)(ISM) 的关键注意事项。

VPN 网关用于在 Azure 中的虚拟网络和另一个网络之间发送加密流量。  VPN 网关解决了三个方案:

- **站点到站点**S2S
- **点到站点**P2S
- **VNet 到 VNet**

本文重点介绍 S2S VPN 网关。 图1显示了一个站点到站点 VPN 网关配置示例。

![具有多站点连接的 VPN 网关](media/vpngateway-multisite-connection-diagram.png)

*图1– Azure 站点到站点 VPN 网关*

## <a name="key-design-considerations"></a>关键设计注意事项

有三个网络选项可用于将 Azure 连接到澳大利亚政府客户:

- **按钮**
- **ExpressRoute**
- **公共 internet**

适用于 Azure 的澳大利亚网络安全中心[使用者指南](https://servicetrust.microsoft.com/viewpage/Australia)建议使用 VPN 网关 (或等效的受认证的第三方服务) 与三个网络选项结合使用, 以确保连接符合ISM 控制加密和完整性。

### <a name="encryption-and-integrity"></a>加密和完整性

默认情况下, 在连接建立期间, VPN 将协商加密和完整性算法和参数, 作为 IKE 握手的一部分。  在 IKE 握手期间, 配置和优先顺序将取决于 VPN 网关是发起方还是响应方 (NB: 这是通过 VPN 设备控制的)。  连接的最终配置由 VPN 设备的配置控制。  有关经验证的 VPN 设备及其配置的详细信息, 请参阅:[关于 VPN 设备](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

VPN 网关可以通过在连接上配置自定义 IPsec/IKE 策略来控制加密和完整性。

### <a name="resource-operations"></a>资源操作

VPN 网关通过公共 internet 在 Azure 和非 Azure 环境之间建立连接。  ISM 包含与连接的显式授权相关的控件。  默认情况下, 可以使用 VPN 网关在安全环境中创建未经授权的隧道。  因此, 组织应使用基于 Azure 角色的访问控制 (RBAC) 来控制谁可以创建和修改 VPN 网关及其连接, 这一点非常重要。  Azure 没有 "内置" 角色来管理 VPN 网关, 因此, 这将需要自定义角色。

严格控制对 "所有者"、"参与者" 和 "网络参与者" 角色的访问。  还建议将 Azure AD Privileged Identity Management 用于更精细的访问控制。

### <a name="high-availability"></a>高可用性

Azure VPN 网关可以有多个连接 (请参阅图 1), 并支持将多个本地 VPN 设备连接到相同的本地环境。  

Azure 中的虚拟网络可以有多个 VPN 网关, 可在独立的、主动-被动或主动-主动配置中进行部署。

建议将所有 VPN 网关部署在[高可用配置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)中: 例如, 两个本地 vpn 设备连接到主动-被动或主动-主动模式下的两个 VPN 网关 (请参阅图 2)。

![VPN 网关冗余连接](media/dual-redundancy.png)

*图 2-主动-主动 VPN 网关和两个 VPN 设备*

### <a name="forced-tunneling"></a>强制隧道

强制隧道通过 VPN 网关将所有 Internet 绑定流量重定向或 "强制" 返回到本地环境, 以便进行检查和审核。 如果没有强制隧道, 来自 Azure 中 Vm 的 Internet 绑定流量会将 Azure 网络基础结构直接发送到公共 Internet, 而不会有用于检查或审核流量的选项。  如果组织需要为环境使用安全的 Internet 网关 (SIG), 这一点非常重要。

## <a name="detailed-configuration"></a>详细配置

### <a name="service-attributes"></a>服务属性

为澳大利亚政府配置的 S2S 连接的 VPN 网关需要具有以下属性:

|特性 | 一定|
|--- | --- |
|gatewayType | UPN|
|

符合受保护的 ISM 控件所需的属性设置为:

|特性 | 一定|
|--- |---|
|vpnType |RouteBased|
|Vpnclientconfiguration.zip/vpnClientProtocols | IkeV2|
|

Azure VPN 网关支持 IPsec 和 IKE 协议标准中的一系列加密算法。  默认策略设置与各种第三方 VPN 设备的最大化互操作性。  因此, 在 IKE 握手期间, 协商不合规的配置是可能的。  因此, 强烈建议将[自定义 IPsec/IKE 策略](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell)参数应用到 VPN 网关上的 vpnclientconfiguration.zip, 以确保连接满足 ISM 控件的本地环境与 Azure 的连接。  关键特性如下:

|特性|决不|一定|
|---|---|---|
|saLifeTimeSeconds|< 14400 秒|> 300 秒|
|saDataSizeKilobytes| |> 1024 KB|
|ipsecEncryption| |AES256-GCMAES256|
|ipsecIntegrity| |SHA256-GCMAES256|
|ikeEncryption| |AES256-GCMAES256|
|ikeIntegrity| |SHA256-GCMAES256|
|dhGroup|DHGroup14、DHGroup24、ECP256、ECP384|DHGroup2|
|pfsGroup|PFS2048、PFS24、ECP256、ECP384||
|

*对于上表中的 dhGroup 和 pfsGroup, 即使可以使用其他设置, 也最好使用 ECP256 和 ECP384*

### <a name="related-services"></a>相关服务

设计和配置 Azure VPN 网关时, 还必须存在和配置一些相关服务:

|服务 | 所需操作|
|--- | ---|
|虚拟网络 | VPN 网关连接到虚拟网络。  在创建新的 VPN 网关之前, 需要创建一个虚拟网络。|
|公用 IP 地址 | S2S VPN 网关需要一个公共 IP 地址, 以便在本地 VPN 设备和 VPN 网关之间建立连接。  需要先创建公共 IP 地址, 然后才能创建 S2S VPN 网关。|
|Subnet | 需要为 VPN 网关创建虚拟网络的子网。|
|

## <a name="implementation-steps-using-powershell"></a>使用 PowerShell 执行步骤

### <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

1. 创建自定义角色 (例如, virtualNetworkGateway 参与者)。  创建要分配给将被允许创建和修改 VPN 网关的用户的角色。 自定义角色应允许下列操作:

   VirtualNetworkGateways/*  
   Microsoft. 网络/连接/*  
   Localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   PublicIPAddresses/*  
   PublicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. 将自定义角色添加到允许创建和管理 VPN 网关和连接到本地环境的用户。

### <a name="create-vpn-gateway"></a>创建 VPN 网关

*这些步骤假定已创建了虚拟网络*

1. 创建新的公共 IP 地址
2. 创建 VPN 网关子网
3. 创建 VPN 网关 IP 配置
4. 创建 VPN 网关
5. 为本地 VPN 设备创建本地网络网关
6. 创建 IPsec 策略 (假定使用自定义 IPsec/IKE 策略)
7. 使用 IPsec 策略在 VPN 网关和本地网关之间创建连接

### <a name="enforce-tunneling"></a>强制隧道

如果需要强制隧道, 则在创建 VPN 网关之前:

1. 创建路由表和路由规则
2. 将路由表与相应的子网相关联

创建 VPN 网关后:

1. 将 GatewayDefaultSite 设置为 VPN 网关上的本地环境

### <a name="example-powershell-script"></a>PowerShell 脚本示例

用于创建自定义 IPSEC/IKE 策略的示例 PowerShell 脚本, 该策略符合用于澳大利亚受保护安全分类的 ISM 控件。

它假定存在虚拟网络、VPN 网关和本地网关。

#### <a name="create-an-ipsecike-policy"></a>创建 IPsec/IKE 策略

下方示例脚本使用以下算法和参数创建 IPsec/IKE 策略：

- IKEv2：AES256、SHA256、DHGroup ECP256
- IPsec：AES256、SHA256、PFS ECP256、SA 生存期14400秒 & 102400000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>使用自定义 IPsec/IKE 策略创建 S2S VPN 连接

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>后续步骤

本文介绍 VPN 网关的特定配置, 以满足在传输过程中保护澳大利亚政府保护数据的信息安全手册 (ISM) 中指定的要求。 有关配置 VPN 网关的详细步骤:

- [Azure 虚拟网络网关概述](https://docs.microsoft.com/azure/vpn-gateway/)  
- [什么是 VPN 网关？](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [使用 PowerShell 创建具有站点到站点 VPN 连接的 VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [创建和管理 VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)