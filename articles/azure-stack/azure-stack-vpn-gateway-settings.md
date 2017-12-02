---
title: "适用于 Azure 堆栈的 VPN 网关设置 |Microsoft 文档"
description: "了解有关使用 Azure 堆栈的 VPN 网关设置。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/01/2017
ms.author: brenduns
ms.openlocfilehash: ed4a84965c37f66bbc7734f6043ad6f8f1666c1f
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>适用于 Azure 堆栈的 VPN 网关配置设置

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

VPN 网关是一种发送加密的 Azure 堆栈中的虚拟网络和远程的 VPN 网关之间的流量的虚拟网络网关。 远程 VPN 网关可以在 Azure 中，你的数据中心中的设备或另一站点中的设备。  如果两个终结点之间没有网络连接，你可以建立两个网络之间安全的站点到站点 (S2S) VPN 连接。

VPN 网关连接依赖于多个资源配置，其中每个资源包含可配置的设置。 本文的各部分介绍了与在 Resource Manager 部署模型中创建的虚拟网络的 VPN 网关相关的资源和设置。 你可以找到说明和拓扑图中每个连接解决方案[有关 VPN 网关 Azure 堆栈](azure-stack-vpn-gateway-about-vpn-gateways.md)。

## <a name="vpn-gateway-settings"></a>VPN 网关设置

### <a name="gateway-types"></a>网关类型
每个 Azure 堆栈的虚拟网络支持单个虚拟网络网关，它必须是类型的**Vpn**。  这不同于 Azure，支持其他类型。  

创建虚拟网络网关时，必须确保用于配置的网关类型正确。 VPN 网关需要`-GatewayType Vpn`。

示例：
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>网关 SKU
创建虚拟网络网关时，需要指定要使用的网关 SKU。 根据工作负荷、吞吐量、功能和、SLA 的类型，选择满足需求的 SKU。

>[!NOTE]
> 经典虚拟网络应继续使用旧版 SKU。 有关旧版网关 SKU 的详细信息，请参阅[使用虚拟网关 SKU（旧版）](/azure/vpn-gateway/vpn-gateway-about-skus-legacy)。

Azure 堆栈提供了以下 VPN 网关 Sku:

|   | VPN 网关吞吐量 |VPN 网关最大 IPsec 隧道 |
|-------|-------|-------|
|**基本 SKU**  | 100 Mbps  | 10    |
|**标准 SKU**           | 100 Mbps  | 10    |
|**高性能 SKU** | 200 Mbps    | 30    |

### <a name="resizing-gateway-skus"></a>调整大小网关 Sku
Azure 堆栈不支持受支持的旧 Sku 之间的 Sku 大小的调整。

同样，Azure 堆栈不支持从支持的旧 Sku （基本、 标准和高性能），大小调整到 Azure （VpnGw1、 VpnGw2 和 VpnGw3） 支持的较新的 Sku。

### <a name="configure-the-gateway-sku"></a>配置网关 SKU
#### <a name="azure-stack-portal"></a>Azure 堆栈门户
如果你使用 Azure 堆栈门户创建资源管理器虚拟网络网关，你可以通过使用下拉列表中选择网关 SKU。 显示的选项对应于所选的网关类型和 VPN 类型。

#### <a name="powershell"></a>PowerShell
下面的 PowerShell 示例作为 VpnGw1 指定-GatewaySku。

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>连接类型
在 Resource Manager 部署模型中，每个配置都需要特定的虚拟网络网关连接类型。 -ConnectionType 的可用资源管理器 PowerShell 值是：

- IPsec

在下面的 PowerShell 示例中，被创建 S2S 连接需要 IPsec 的连接类型。  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>VPN 类型
为 VPN 网关配置创建虚拟网络网关时，必须指定 VPN 类型。 选择的 VPN 类型取决于要创建的连接拓扑。  VPN 类型还取决于使用的硬件。 S2S 配置需要 VPN 设备。 有些 VPN 设备仅支持特定的 VPN 类型。

> [!IMPORTANT]  
> 在此期间，Azure 堆栈仅支持路由基于 VPN 类型。  如果你的设备仅支持策略基于 Vpn，然后不支持从 Azure 堆栈连接到这些设备。

- **PolicyBased**: *（支持 azure，但不能通过 Azure 堆栈）*基于策略的 Vpn 加密和直接通过基于配置的之间的地址前缀的组合的 IPsec 策略的 IPsec 隧道数据包你的本地网络和 Azure 堆栈 VNet。 通常会在 VPN 设备配置中将策略（或流量选择器）定义为访问列表。

- **RouteBased**: RouteBased Vpn IP 转发或表到直接数据包路由到其对应的隧道接口中使用"路由"。 然后，隧道接口会加密或解密出入隧道的数据包。 RouteBased VPN 的策略（或流量选择器）配置为任意到任意（或通配符）。 RouteBased VPN 类型的值是 RouteBased。

下面的 PowerShell 示例作为 RouteBased 指定-VpnType。 在创建网关时，必须确保用于配置的 -VpnType 正确。

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>网关要求
下表列出 VPN 网关的要求。

| |PolicyBased Basic VPN 网关 | RouteBased Basic VPN 网关 | RouteBased 标准 VPN 网关 | RouteBased 高性能 VPN 网关|
|--|--|--|--|--|
| **站点到站点连接 (S2S)** | 不支持 | 基于路由的 VPN 配置 | 基于路由的 VPN 配置 | 基于路由的 VPN 配置 |
| **身份验证方法**  | 不支持 | S2S 连接的预共享的密钥  | S2S 连接的预共享的密钥  | S2S 连接的预共享的密钥  |   
| **S2S 连接的最大数目**  | 不支持 | 10 | 10| 30|
|**活动路由支持 (BGP)** | 不支持 | 不支持 | 支持 | 支持 |

### <a name="gateway-subnet"></a>网关子网
在创建 VPN 网关之前，必须创建一个网关子网。 网关子网包含虚拟网络网关 VM 和服务使用的 IP 地址。 在创建虚拟网络网关时，将网关 VM 部署到网关子网，并使用所需的 VPN 网关设置进行配置。 不会部署任何其他操作 （例如，更多虚拟机） 与网关子网。 网关子网必须命名为“GatewaySubnet”才能正常工作。 命名网关子网 GatewaySubnet 允许 Azure 堆栈，以便知道这是要部署的虚拟网络网关 Vm 和服务添加到的子网。

创建网关子网时，请指定子网包含的 IP 地址数。 将网关子网中的 IP 地址分配到网关 VM 和网关服务。 有些配置需要具有比其他配置更多的 IP 地址。 查看要创建的配置的说明，验证想要创建的网关子网是否会满足这些要求。 此外，可能需要确保网关子网包含足够多的 IP 地址，以便应对将来可能会添加的配置。 尽管网关子网最小可创建为 /29，但建议创建 /28 或更大（/28、/27 和 /26 等）的网关子网。 这样一来，如果你添加功能在将来，你无需关闭您的网关，然后删除并重新创建网关子网，以允许多个 IP 地址。

以下 Resource Manager PowerShell 示例显示名为 GatewaySubnet 的网关子网。 可以看到，CIDR 表示法指定了 /27，这可提供足够的 IP 地址供大多数现有配置使用。

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> 使用网关子网时，避免将网络安全组 (NSG) 与网关子网关联。 将网络安全组与此子网关联可能会导致 VPN 网关停止按预期方式工作。 关于网络安全组的详细信息，请参阅[什么是网络安全组？](/azure/virtual-network/virtual-networks-nsg)。

### <a name="local-network-gateways"></a>本地网关
当在 Azure 中创建 VPN 网关配置，本地网络网关通常表示你的本地位置。 Azure 堆栈中它表示位于 Azure 堆栈之外的任何远程 VPN 设备。  这可能是在你的数据中心、 远程数据中心或在 Azure 中的 VPN 网关的 VPN 设备。

你提供一个名称，VPN 设备的公共 IP 地址的本地网络网关，并指定都位于本地位置的地址前缀。 Azure 将查看网络流量的目标地址前缀、查阅针对本地网络网关指定的配置，并相应地路由数据包。

以下 PowerShell 示例创建新的本地网络网关：

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
有时需要修改本地网络网关设置。 例如，在添加或修改地址范围时，或 VPN 设备的 IP 地址发生变化时。 请参阅[使用 PowerShell 修改本地网络网关设置](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway)。

## <a name="ipsecike-parameters"></a>IPsec/IKE 参数
如果你设置了 Azure 堆栈中的 VPN 连接，你需要在两端配置连接。  如果你要配置 Azure 堆栈与硬件设备，如交换机或路由器，它充当 VPN 网关之间的 VPN 连接，则该设备可能要求你提供其他设置。

与 Azure，支持多个提供作为发起方和响应方，不同 Azure 堆栈支持一个产品。

###  <a name="ike-phase-1-main-mode-parameters"></a>IKE 阶段 1（主模式）参数
| 属性              | 值|
|-|-|
| SDK 版本           | IKEv2 |
|Diffie-Hellman 组   | 组 2（1024 位） |
| 身份验证方法 | 预共享密钥 |
|加密和哈希算法 | AES256、SHA256 |
|SA 生存期（时间）     | 28,800 秒|

### <a name="ike-phase-2-quick-mode-parameters"></a>IKE 阶段 2（快速模式）参数
| 属性| 值|
|-|-|
|SDK 版本 |IKEv2 |
|加密和哈希算法 （加密）     | GCMAES256|
|加密和哈希算法 （身份验证） | GCMAES256|
|SA 生存期（时间）  | 3,600 秒 |
|SA 生存期（字节数） | 819,200       |
|完全向前保密 (PFS) |PFS2048 |
|死对等体检测 | 支持|  
