---
title: 针对虚拟网络对等互连配置 VPN 网关传输
description: 针对虚拟网络对等互连配置 VPN 网关传输。
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 03/25/2018
ms.author: yushwang
ms.openlocfilehash: c243c6ded4057c9e4ac63345f300f3b3b690b363
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798947"
---
# <a name="configure-vpn-gateway-transit-for-virtual-network-peering"></a>针对虚拟网络对等互连配置 VPN 网关传输

本文介绍如何针对虚拟网络对等互连配置网关传输。 [虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)可以无缝地连接两个 Azure 虚拟网络，为了连接目的将两个虚拟网络合并成一个。 [网关传输](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity)是一种对等互连属性，可以让一个虚拟网络利用对等互连的虚拟网络中的 VPN 网关进行跨界连接或 VNet 到 VNet 连接。 下图说明了在虚拟网络对等互连中使用网关传输的工作原理。

![网关传输](./media/vpn-gateway-peering-gateway-transit/gatewaytransit.png)

在图中，对等互连的虚拟网络通过网关传输来使用 Hub-RM 中的 Azure VPN 网关。 在 VPN 网关上提供的连接（包括 S2S 连接、P2S 连接和 VNet 到 VNet 连接）适用于所有三种虚拟网络。 传输选项适用于在相同的或不同的部署模型之间进行对等互连。 约束条件是，VPN 网关只能位于使用资源管理器部署模型的虚拟网络中，如图所示。

在中心辐射型网络体系结构中，辐射虚拟网络可以通过网关传输共享中心的 VPN 网关，不必在每个辐射虚拟网络中部署 VPN 网关。 通往网关连接的虚拟网络或本地网络的路由会通过网关传输传播到对等互连的虚拟网络的路由表。 可以禁用源自 VPN 网关的自动路由传播。 使用“禁用 BGP 路由传播”选项创建一个路由表，将路由表关联到子网，防止将路由分发到这些子网。 有关详细信息，请参阅[虚拟网络路由表](../virtual-network/manage-route-table.md)。

本文档介绍两个方案：

1. 两种虚拟网络都使用资源管理器部署模型
2. 辐射虚拟网络为经典部署模型，带网关的中心虚拟网络为资源管理器部署模型


>[!NOTE]
> 如果更改网络拓扑并且具有 VPN 客户端，必须再次下载和安装 Windows 客户端的 VPN 客户端包，以使更改应用于客户端。
>

## <a name="requirements"></a>要求



本文档中的示例要求创建以下资源：

1. 带 VPN 网关的 Hub-RM 虚拟网络
2. Spoke-RM 虚拟网络
3. 使用经典部署模型的 Spoke-Classic 虚拟网络
4. 所用帐户要求必需的角色和权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。

有关说明，请参阅以下文档：

1. [在虚拟网络中创建 VPN 网关](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
2. [使用相同的部署模型创建虚拟网络对等互连](../virtual-network/tutorial-connect-virtual-networks-portal.md)
3. [使用不同的部署模型创建虚拟网络对等互连](../virtual-network/create-peering-different-deployment-models.md)

## <a name="permissions"></a><a name="permissions"></a>Permissions

用于创建虚拟网络对等互连的帐户必须具有所需的角色或权限。 在以下示例中，若要将两个名为 Hub-RM 和 Spoke-Classic 的虚拟网络进行对等互连，帐户必须具有适用于每个虚拟网络的以下角色或权限：
    
|虚拟网络|部署模型|角色|权限|
|---|---|---|---|
|Hub-RM|资源管理器|[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |经典|[经典网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|空值|
|Spoke-Classic|资源管理器|[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||经典|[经典网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

详细了解[内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及将特定的权限分配到[自定义角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（仅限资源管理器）。

## <a name="resource-manager-to-resource-manager-peering-with-gateway-transit"></a>通过网关传输进行的资源管理器到资源管理器对等互连

请按说明创建或更新虚拟网络对等互连，以便启用网关传输。

1. 在 Azure 门户中创建或更新从 Spoke-RM 到 Hub-RM 的虚拟网络对等互连。 导航到 Spoke-RM 虚拟网络资源，单击“对等互连”，然后单击“添加”：
    - 设置“资源管理器”选项
    - 在相应的订阅中选择 Hub-RM 虚拟网络
    - 确保“允许虚拟网络访问”为“已启用”
    - 设置“使用远程网关”选项
    - 单击“确定”

      ![spokerm-to-hubrm](./media/vpn-gateway-peering-gateway-transit/spokerm-hubrm-peering.png)

2. 如果已创建对等互连，请导航到对等互连资源，然后启用“使用远程网关”选项（类似于步骤 (1) 中所示的屏幕截图）

3. 在 Azure 门户中创建或更新从 Hub-RM 到 Spoke-RM 的虚拟网络对等互连。 导航到 Hub-RM 虚拟网络资源，单击“对等互连”，然后单击“添加”：
    - 设置“资源管理器”选项
    - 确保“允许虚拟网络访问”为“已启用”
    - 在相应的订阅中选择“Spoke-RM”虚拟网络
    - 设置“允许网关传输”选项
    - 单击“确定”

      ![hubrm-to-spokerm](./media/vpn-gateway-peering-gateway-transit/hubrm-spokerm-peering.png)

4. 如果已创建对等互连，请导航到对等互连资源，然后启用“允许网关传输”选项（类似于步骤 (3) 中所示的屏幕截图）

5. 验证两个虚拟网络上的对等互连状态是否为“已连接”

### <a name="powershell-sample"></a>PowerShell 示例

也可使用 PowerShell 来创建或更新以上示例的对等互连。 将变量替换为虚拟网络和资源组的名称。

```azurepowershell-interactive
$SpokeRG = "SpokeRG1"
$SpokeRM = "Spoke-RM"
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$spokermvnet = Get-AzVirtualNetwork -Name $SpokeRM -ResourceGroup $SpokeRG
$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name SpokeRMtoHubRM `
  -VirtualNetwork $spokermvnet `
  -RemoteVirtualNetworkId $hubrmvnet.Id `
  -UseRemoteGateways

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId $spokermvnet.Id `
  -AllowGatewayTransit
```

## <a name="classic-to-resource-manager-peering-with-gateway-transit"></a>通过网关传输进行的经典到资源管理器对等互连

步骤类似于资源管理器示例，区别是操作仅应用于 Hub-RM 虚拟网络。

1. 在 Azure 门户中创建或更新从 Hub-RM 到 Spoke-RM 的虚拟网络对等互连。 导航到 Hub-RM 虚拟网络资源，单击“对等互连”，然后单击“添加”：
   - 设置“经典”选项作为虚拟网络部署模型
   - 在相应的订阅中选择“Spoke-Classic”虚拟网络
   - 确保“允许虚拟网络访问”为“已启用”
   - 设置“允许网关传输”选项
   - 单击“确定”

     ![hubrm-to-spokeclassic](./media/vpn-gateway-peering-gateway-transit/hubrm-spokeclassic-peering.png)

2. 如果已创建对等互连，请导航到对等互连资源，然后启用“允许网关传输”选项（类似于步骤 (1) 中所示的屏幕截图）

3. 在 Spoke-Classic 虚拟网络上没有任何操作

4. 验证 Hub-RM 虚拟网络上的对等互连状态是否为“已连接”

状态显示“已连接”后，辐射虚拟网络即可通过中心虚拟网络中的 VPN 网关使用 VNet 到 VNet 连接或跨界连接。

### <a name="powershell-sample"></a>PowerShell 示例

也可使用 PowerShell 来创建或更新以上示例的对等互连。 将变量和订阅 ID 替换为虚拟网络和资源组以及订阅的值。 只需在中心虚拟网络上创建虚拟网络对等互连。

```azurepowershell-interactive
$HubRG   = "HubRG1"
$HubRM   = "Hub-RM"

$hubrmvnet   = Get-AzVirtualNetwork -Name $HubRM -ResourceGroup $HubRG

Add-AzVirtualNetworkPeering `
  -Name HubRMToSpokeRM `
  -VirtualNetwork $hubrmvnet `
  -RemoteVirtualNetworkId "/subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/Spoke-Classic" `
  -AllowGatewayTransit
```

## <a name="next-steps"></a>后续步骤

* 在为生产用途创建虚拟网络对等互连之前，请详细了解[虚拟网络对等互连约束和行为](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)和[虚拟网络对等互连设置](../virtual-network/virtual-network-manage-peering.md#create-a-peering)。
* 了解如何使用虚拟网络对等互连和网关传输[创建中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering)。
