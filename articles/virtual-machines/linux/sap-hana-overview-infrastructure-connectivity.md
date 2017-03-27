---
title: "Azure 上的 SAP HANA（大型实例）的基础结构与连接 | Microsoft 文档"
description: "配置所需的连接基础结构，以使用 Azure 上的 SAP HANA（大型实例）。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 4b01305ea1fc033de64ed2e4fb7e4c9f3d16d394
ms.lasthandoff: 03/01/2017


---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Azure 上的 SAP HANA（大型实例）的基础结构与连接 

在你与 Microsoft 企业帐户团队之间达成“Azure 上的 SAP HANA（大型实例）”的采购协议后，需要提供以下信息：

- 客户名称
- 业务联系人信息（包括电子邮件地址和电话号码）
- 技术联系人信息（包括电子邮件地址和电话号码）
- 技术网络联系人信息（包括电子邮件地址和电话号码）
- Azure 部署区域（截止 2016 年 9 月，包括“美国西部”或“美国东部”）
- 确认“Azure 上的 SAP HANA 配置（大型实例）”的 SKU（配置）
- 对于要部署该解决方案的每个 Azure 区域，需要提供：
  - P2P 连接的 /29 IP 地址范围
  - CIDR 块（用于 HANA 大型实例 NAT 池；建议使用 /24）
- 对于连接到 HANA 大型实例的每个 Azure VNet，需要提供（与 Azure 区域无关）：
  - 一个或多个 /28 或 /27 IP 地址范围（用于客户 VNet 网关子网）
  - 一个或多个 CIDR 块（用于客户 VNet 租户子网；建议使用 /24）
- 对于每个 HANA 大型实例系统的数据，需要提供：
  - 所需的主机名
  - NAT 池中所需的 IP 地址
- Azure 上的 SAP HANA 大型实例将直接连接到的 Azure 订阅的 Azure 订阅编号
- SAP HANA 实例的 SAP HANA SID 名称（创建所需的 SAP HANA 相关磁盘卷时需要用到）

提供这些信息后，Microsoft 将预配 Azure 上的 SAP HANA（大型实例）。

然后，Microsoft 将提供网络设置信息，以便：

- 将 Azure VNet 连接到 ExpressRoute 线路，后者将 Azure VNet 连接到 HANA 大型实例
  - 对于 Azure Resource Manager，将提供：
     - 授权密钥
     - ExpressRoute 对等互连 ID
- 使用建立的 ExpressRoute 线路和 Azure VNet 访问 HANA 大型实例

## <a name="creating-an-azure-vnet"></a>创建 Azure VNet

应使用 Azure Resource Manager 部署模型创建此 Azure VNet。 此解决方案不支持旧的 Azure 部署模型（通常称为 ASM）。

创建的 Azure VNet 应至少有一个租户子网和一个网关子网。 应该为这些子网分配指定的 IP 地址范围，并将其提交给 Microsoft。

> [!IMPORTANT] 
> 只能将租户和网关地址块分配到 Azure 订阅中的 VNet。 P2P 和 NAT 池地址块必须独立于 VNet 与子网地址空间，因为它们位于 Azure 订阅的外部。

可以使用多个租户子网（甚至可以利用不连续的地址范围），但如前所述，必须事先将这些地址范围提交给 Microsoft。

对于这些租户子网，可以使用偏好的任何命名标准。 但是，与 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路连接的**每个 VNet 必须有且仅有一个网关子网**，**此网关子网必须始终命名为 &quot;GatewaySubnet&quot;**，确保正确放置 ExpressRoute 网关。

> [!WARNING] 
> 网关子网必须始终命名为 &quot;GatewaySubnet&quot;，这一点至关重要。

可以使用 Azure 门户、PowerShell、Azure 模板或 Azure CLI 创建 VNet（请参阅[使用 Azure 门户创建虚拟网络](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)）。

## <a name="creating-a-gateway-subnet"></a>创建网关子网

创建 Azure VNet 后，必须在 VNet 中创建 ExpressRoute 网关，以便将 VNet 链接到与大型实例戳记上的客户租户相连接的 ExpressRoute 线路。

> [!NOTE] 
> 此步骤可能需要长达 30 分钟才能完成，因为需要在指定的 Azure 订阅中创建新的网关，然后将其连接到指定的 Azure VNet。

如果网关已存在，请检查它是否为 ExpressRoute 网关。 如果不是，则必须删除该网关，然后重新创建 ExpressRoute 网关。 如果已建立 ExpressRoute 网关，请转到下面的“链接 VNet”部分，因为 Azure VNet 已连接到用于建立本地连接的 ExpressRoute 线路。

- 使用（新的）[Azure 门户](https://portal.azure.com/)或 PowerShell 创建与 VNet 连接的 ExpressRoute VPN 网关。
  - 如果使用 Azure 门户预览版，请添加新的**虚拟网络网关**，然后选择 **ExpressRoute** 作为网关类型。
  - 如果选择的是 PowerShell，请先下载并使用最新的 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)，确保获得最佳体验。 以下命令创建 ExpressRoute 网关。 前面带有 _$_ 的文本是用户定义的变量，需要使用具体的信息更新。

```
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

此示例使用了 HighPerformance 网关 SKU。 选项包括 HighPerformance 或 UltraPerformance - Azure 上的 SAP HANA（大型实例）仅支持这两个网关 SKU。

## <a name="linking-vnets"></a>链接 VNet

在 Azure VNet 中创建 ExpressRoute 网关后，可以使用 Microsoft 提供的授权信息，将 ExpressRoute 网关连接到为此连接创建的“Azure 上的 SAP HANA（大型实例）”ExpressRoute 线路。 只能使用 PowerShell 执行此操作（目前不支持通过 Azure 门户执行此操作）。

- 请使用每个连接的不同 AuthGUID 针对每个 VNet 网关执行以下操作。 下面显示的前两个条目来自 Microsoft 提供的信息。 此外，AuthGUID 特定于每个 VNet 及其网关。

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

如果想要将网关连接到与订阅关联的多个不同的 ExpressRoute 线路，可能需要多次执行此步骤。

## <a name="adding-more-ip-addresses-or-subnets"></a>添加更多 IP 地址或子网

可以使用 Azure 门户、PowerShell 或 CLI 添加多个 IP 地址或子网。

如果尚未向 Azure 上的 SAP HANA 服务管理部门声明其他 IP 地址空间范围，请提出 Azure 支持请求，以便添加该范围。 收到确认后，请执行后续步骤。

若要通过 Azure 门户预览版创建其他子网，请参阅[使用 Azure 门户预览版创建虚拟网络](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文；若要使用 PowerShell，请参阅[使用 PowerShell 创建虚拟网络](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="adding-vnets"></a>添加 VNet

首次连接一个或多个 Azure VNet 后，可能需要添加其他用于访问 Azure 上的 SAP HANA（大型实例）的 VNet。 首先，请提交 Azure 支持请求，在该请求中，请包含指明特定 Azure 部署的具体信息，以及其他 Azure VNet 的租户子网和网关子网的 IP 地址空间范围。 然后，Azure 上的 SAP HANA 服务管理部门将提供所需的信息用于连接其他 VNet 和 ExpressRoute。

添加新 Azure VNet 的步骤：

1. 完成登记过程的第一个步骤，具体请参阅前面的_创建 Azure VNet_ 部分。
2. 完成登记过程的第二个步骤，具体请参阅前面的_创建网关子网_部分。
3. 提出 Azure 支持请求并在其中包含有关新 VNet 的信息，并请求提供新的授权密钥，以便将其他 VNet 连接到 HANA 大型实例 ExpressRoute 线路。
4. 收到已完成授权的通知后，请使用 Microsoft 提供的授权信息完成登记过程的第三个步骤，具体请参阅上面的_链接 VNet_ 部分。

## <a name="increasing-expressroute-circuit-bandwidth"></a>提高 ExpressRoute 线路带宽

咨询 Azure 上的 SAP HANA 服务管理部门。 如果有通知指出需要提高 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路的带宽，请创建 Azure 支持请求。 （可以请求将单条线路的带宽提高到最大 10 Gbps）。完成该操作后，你将会收到通知；不需要采取其他措施即可在 Azure 中提高速度。

## <a name="adding-an-additional-expressroute-circuit"></a>添加其他 ExpressRoute 线路

如果有通知指出需要更多的 ExpressRoute 线路，请咨询 Azure 上的 SAP HANA 服务管理部门，提出 Azure 支持请求来创建新的 ExpressRoute 线路（并获取用于连接该线路的授权信息）。 要在 VNet 上使用的地址空间必须在提出请求之前定义，以便 Azure 上的 SAP HANA 服务管理部门提供授权信息。

创建新线路并且 Azure 上的 SAP HANA 服务管理部门完成配置后，你将会收到通知，其中包含继续下一步所需的信息。 请遵循上面提供的步骤创建新的 VNet 并将其连接到此附加线路。 如果 Azure VNet 已连接到另一条 ExpressRoute 线路，则无法将其连接到此附加线路。

## <a name="deleting-a-subnet"></a>删除子网

若要删除 VNet 子网，可以使用 Azure 门户、PowerShell 或 CLI。 如果删除了某个地址空间，应该告知 Azure 上的 SAP HANA 服务管理部门发生了地址空间变化，以便从 Azure 上的 SAP HANA（大型实例）可以通信的范围中删除该地址空间。

Azure.com 上有关删除子网的专门指南中所述的过程，就是添加子网的相反过程，不过，尚未提供具体的步骤。 有关创建子网的详细信息，请参阅[使用 Azure 门户创建虚拟网络](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文。

## <a name="deleting-a-vnet"></a>删除 VNet

可以使用 Azure 门户、PowerShell 或 CLI 删除 VNet。 Azure 上的 SAP HANA 服务管理部门将删除 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路中的现有授权，并删除用来与 HANA 大型实例通信的 IP 地址范围（租户和网关范围）。

删除 VNet 后，请提出 Azure 支持请求并提供要删除的 IP 地址空间范围。

Azure.com 上有关删除 VNet 的专门指南中所述的过程，就是添加 VNet（如前所述）的相反过程，不过，尚未提供具体的步骤。 有关创建 VNet 的详细信息，请参阅文章[使用 Azure 门户创建虚拟网络](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)和[使用 PowerShell 创建虚拟网络](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

为了确保删除所有组件，请删除以下各项：

- **对于 Azure Resource Manager：**ExpressRoute 连接、VNet 网关、VNet 网关公共 IP 和 VNet
- **对于经典 VM：**VNet 网关和 VNet

## <a name="deleting-an-expressroute-circuit"></a>删除 ExpressRoute 线路

若要删除某个附加的 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路，请向 Azure 上的 SAP HANA 服务管理部门提出删除该线路的 Azure 支持请求。 在 Azure 订阅中，可以根据需要删除或保留 VNet。 但是，必须删除连接（对于 Azure Resource Manager），或者断开 HANA 大型实例 ExpressRoute 线路与链接的 VNet 网关之间的连接（对于经典 VM）。

如果还想要删除某个 VNet，请遵循上一部分中有关删除 VNet 的指导。



