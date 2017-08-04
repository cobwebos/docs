---
title: "创建、更改或删除 Azure 虚拟网络对等互连 | Microsoft Docs"
description: "了解如何创建、更改或删除虚拟网络对等互连。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 57ab8c0504ed8f7071a949e941f749025f9042e4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>创建、更改或删除虚拟网络对等互连

了解如何创建、更改或删除虚拟网络对等互连。 虚拟网络对等互连可以通过 Azure 主干网络在同一 Azure 位置连接两个虚拟网络。 对等互联成功后，这两个虚拟网络仍将作为单独的资源进行管理。 连接到任一虚拟网络的资源将以相同的延迟和带宽进行通信，就如同资源是连接到同一个虚拟网络一样。 如果不熟悉虚拟网络对等互连，我们建议阅读[虚拟网络对等互连概述](virtual-network-peering-overview.md)一文，并在完成本文中的任务之前先完成[创建虚拟网络对等互连教程](virtual-network-create-peering.md)。

## <a name="before"></a>准备工作

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果不熟悉 Azure 中的虚拟网络对等互连，建议在阅读本文之前，先完成[创建虚拟网络对等互连](virtual-network-create-peering.md)教程。
- 查看 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)一文，了解对等互连的限制。
- 使用 Azure 帐户登录到 Azure 门户、Azure 命令行接口 (CLI) 或 Azure PowerShell。 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用 PowerShell 命令来完成本文中的任务，请[安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保安装最新版本的 Azure PowerShell cmdlet。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请[安装和配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 确保安装最新版本的 Azure CLI。 若要获取 CLI 命令的帮助，请键入 `az <command> --help`。

## <a name="about-peering"></a>要求和限制 

- 进行对等互连的虚拟网络的 IP 地址空间不得重叠。
- 虚拟网络和另一个虚拟网络对等互连后，将不能向虚拟网络添加地址空间或从中删除地址空间。 若要添加或删除地址空间，请删除对等互连，添加或删除地址空间，然后重新创建对等互连。 要向虚拟网络添加地址空间或从中删除地址空间，请参阅[创建、更改或删除虚拟网络](virtual-network-manage-network.md#add-address-spaces)一文。 
- 可以对等互连两个通过 Resource Manager 部署的虚拟网络，或对等互连一个通过 Resource Manager 部署的虚拟网络与一个通过经典部署模型部署的虚拟网络。 不能对等互连两个通过经典部署模型创建的虚拟网络。 如果不熟悉 Azure 部署模型，请阅读[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 可以使用 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)来连接两个通过经典部署模型创建的虚拟网络。
- 对等互连两个通过 Resource Manager 创建的虚拟网络时，必须为对等互连中的每个虚拟网络都配置对等互连。 当从第一个虚拟网络将对等互连创建至第二个虚拟网络时，对等互连状态为“已启动”。  当从第二个虚拟网络将对等互连创建至第一个虚拟网络时，对等互连状态为“已连接”。 如果查看第一个虚拟网络的对等互连状态，将看到其状态从“已启动”更改为“已连接”。 直到两个虚拟网络对等互连的对等互连状态均为“已连接”时，对等互连才成功建立。 
- 当对等互连一个通过 Resource Manager 创建的虚拟网络与一个通过经典部署模型创建的虚拟网络时，只需为通过 Resource Manager 部署的虚拟网络配置对等互连。 不能为虚拟网络（经典）配置对等互连，或在两个通过经典部署模型部署的虚拟网络之间配置对等互连。 在从虚拟网络 (Resource Manager) 将对等互连创建至虚拟网络（经典）时，对等互连状态为“正在更新”，随后将更改为“已连接”。   
- 对等互连在两个虚拟网络之间创建。 对等互连是不可传递的。 如果在以下虚拟网络之间创建对等互连：
    - VirtualNetwork1 和 VirtualNetwork2
    - VirtualNetwork2 和 VirtualNetwork3

  不会通过 VirtualNetwork2 在 VirtualNetwork1 和 VirtualNetwork3 之间形成对等互连。 如果要在 VirtualNetwork1 和 VirtualNetwork3 之间创建虚拟网络对等互连，必须在 VirtualNetwork1 和 VirtualNetwork3 之间创建对等互连。
- 无法使用默认 Azure 名称解析来解析已对等互连的虚拟网络中的名称。 若要解析其他虚拟网络中的名称，必须使用自定义 DNS 服务器。 若要了解如何设置自己的 DNS 服务器，请阅读[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)一文。
- 处于对等互连中的两个虚拟网络中的资源可以互相之间以相同的带宽和延迟进行通信，就如同资源是位于同一个虚拟网络中一样。 但是，每种虚拟机大小都有其自己的最大网络带宽。 若要深入了解不同虚拟机大小的最大网络带宽，请参阅有关 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机大小的文章。
- 可以对等互连在同一个或者不同订阅中通过 Resource Manager 部署的虚拟网络。
- 可以对等互连在同一或不同订阅（预览）中通过不同部署模型部署的虚拟网络。 
- 两个虚拟网络都存在于的订阅必须关联到同一 Azure Active Directory 租户。 如果还没有 AD 租户，可以快速[创建一个](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch)。 可以使用 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)连接关联到不同 Active Directory 租户的不同订阅中的两个虚拟网络。
- <a name="roles-permissions"></a>帐户必须有必要的角色或权限才能创建对等互连。 例如，如果对等互连两个名为 *Vnet1* 和 *VNet2* 的虚拟网络，则对于每个虚拟网络，帐户必须分配有以下最低角色或权限：
    
    |虚拟网络|部署模型|角色|权限|
    |---|---|---|---|
    |VNet1|Resource Manager|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
    | |经典|[经典网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|不适用|
    |VNet2|Resource Manager|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
    ||经典|[经典网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

  详细了解[内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及如何向[自定义角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)分配特定权限（仅 Resource Manager）。
- 一个虚拟网络可以对等互连到另一个虚拟网络，也可以通过 Azure 虚拟网络网关连接到另一个虚拟网络。 当虚拟网络同时通过对等互连和网关连接时，虚拟网络的流量会根据对等互连配置流动，而不是网关。
- 对于利用虚拟网络对等互连的入口和出口流量，有少许收费。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="create-peering"></a>创建对等互连

>[!NOTE]
>成功创建对等互连有几项要求、约束和注意事项。 创建对等互连之前，请确保已熟悉本文中[关于对等互连](#about-peering)部分的注意事项列表。
>

1. 使用具有必要的[角色和权限](#roles-permissions)的帐户登录到[门户](https://portal.azure.com)。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请单击它。 如果“虚拟网络（经典）”出现在列表中，请不要选择它，因为无法从通过经典部署模型部署的虚拟网络创建对等互连。
3. 在显示的“虚拟网络”边栏选项卡中，单击想要为其创建对等互连的虚拟网络。
4. 在针对所选虚拟网络显示的窗格中，单击“设置”部分中的“对等互连”。
5. 单击“+ 添加”。 
6. <a name="add-peering"></a>在“添加对等互连”边栏选项卡中，为以下设置输入或选择值：
    - 名称：对等互连的名称在虚拟网络中必须唯一。
    - 虚拟网络部署模型：选择要对等互连的虚拟网络是通过哪种部署模型来进行部署的。
    - 我知道我的资源 ID：如果对要进行对等互连的虚拟网络拥有读取访问权限，请保留取消选中此复选框。 如果对要进行对等互连的虚拟网络或订阅没有读取访问权限，则选中此框。 在选中此框时显示的“资源 ID”复选框中输入要进行对等互连的虚拟网络的完整资源 ID。 输入的虚拟网络资源 ID 必须与此虚拟网络位于同一 Azure [位置](https://azure.microsoft.com/regions)。 完整资源 ID 类似于 /subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>。 可以通过查看虚拟网络的属性，获取虚拟网络的资源 ID。 若要了解如何查看虚拟网络的属性，请阅读[创建、更改或删除虚拟网络](virtual-network-manage-network.md#view-vnet)一文。
    - 订阅：选择要进行对等互连的虚拟网络的[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 将列出一个或多个订阅，具体取决于帐户对多少个订阅具有读取访问权限。 如果选中“资源 ID”复选框，则此设置不可用。 只要两个虚拟网络都是通过 Resource Manager 创建的，就可以对等互连不同订阅中的虚拟网络。 在预览版中，可以跨通过不同部署模型创建的订阅进行对等互连。 在不同订阅中通过不同部署模型部署的虚拟网络之间创建对等互连之前，请先注册预览版。 详细了解如何注册预览版，以及如何[对等互连不同订阅中通过不同部署模型创建的虚拟网络](create-peering-different-deployment-models-subscriptions.md)。
    - 虚拟网络：选择要进行对等互连的虚拟网络。 可以选择通过 Azure 部署模型创建的虚拟网络，但该虚拟网络必须与从中启动对等互连的虚拟网络位于同一位置。 必须具有对虚拟网络的读取访问权限，才能使其出现在列表中。 如果列出了某个虚拟网络，但显示为灰色，则可能是因为虚拟网络的地址空间与此虚拟网络的地址空间重叠。 如果虚拟网络地址空间重叠，则它们无法进行对等互连。 如果选中“资源 ID”复选框，则此设置不可用。
    - 允许虚拟网络访问：如果要启用两个虚拟网络之间的通信，请选择“启用”（默认）。 启用虚拟网络之间的通信可允许资源连接到任意虚拟网络，并以相同的带宽和延迟互相之间进行通信，就如同它们是连接到同一个虚拟网络一样。 这两个虚拟网络中的资源之间的所有通信都在 Azure 专用网络上进行。 网络安全组的默认标记 **VirtualNetwork** 会包围虚拟网络和已对等互连的虚拟网络。 若要了解网络安全组默认标记的详细信息，请阅读[网络安全组概述](virtual-networks-nsg.md#default-tags)一文。  如果不希望流量流到已对等互连的虚拟网络，请选择“禁用”。 如果已将一个虚拟网络与另一个虚拟网络对等互连，但有时想要禁用这两个虚拟网络之间的流量流动，则可以选择“禁用”。 可发现启用/禁用比删除并重新创建对等互连更加方便。 当禁用此设置时，流量不会在已对等互连的虚拟网络间流动。
    - 允许转发的流量：选中此框以允许转发到已对等互连的虚拟网络的流量（流量不是源自已对等互连的虚拟网络）流动到此虚拟网络。 如果已在进行对等互连的虚拟网络中部署网络虚拟设备，并创建了用户定义的路由以在整个网络虚拟设备中转发流量，则流量转发会变得非常常见。 如果保留此框的未选中状态（默认），从已对等互连的虚拟网络转发的流量将无法流动到此虚拟网络。 虽然启动此功能可允许通过对等互连转发流量，但它并不会创建任何用户定义的路由或网络虚拟设备。 用户自定义的路由和网络虚拟设备是单独创建的。 了解[用户定义的路由](virtual-networks-udr-overview.md)。
    - 允许网关传输：如果有附加到此虚拟网络的虚拟网络网关并且想要允许来自已对等互连的虚拟网络的流量流经网关，请选中此框。 例如，此虚拟网络有可能通过虚拟网络网关附加到本地网络。 选中此框将允许来自已对等互连的虚拟网络的流量流经附加到此虚拟网络的网关。 如果选中此框，则已对等连接的虚拟网络不能有已配置的网关。 将对等互连从另一个虚拟网络设置至此虚拟网络时，已对等互连的虚拟网络必须已选中复选框“使用远程网关”。 如果保留此框的未选中状态（默认），则来自已对等互连的虚拟网络的流量仍可流动到此虚拟网络，但无法流经附加到此虚拟网络的虚拟网络网关。 了解有关[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#site-to-site-and-multi-site-ipsecike-vpn-tunnel)的详细信息。 
    
    如果正在对等互连一个虚拟网络 (Resource Manager) 与另一个虚拟网络（经典），则无法启用此选项。 虽然流量在这两个虚拟网络之间流动，但虚拟网络（经典）的流量无法流经附加到虚拟网络 (Resource Manager) 的网络网关。
    - 使用远程网关：选中此框可允许来自此虚拟网络的流量流经附加到正与之对等互连的虚拟网络的虚拟网络网关。 例如，正与之对等互连的虚拟网络附加了一个 VPN 网关，可实现与本地网络的通信。  选中此框可允许来自此虚拟网络的流量流经附加到已对等互连的虚拟网络的 VPN 网关。 如果选中此框，已对等互连的虚拟网络必须附加有虚拟网络网关，并且必须已选中“允许网关传输”复选框。 如果保留此框的未选中状态（默认），则来自已对等互连的虚拟网络的流量仍将流动到此虚拟网络，但无法流经附加到此虚拟网络的虚拟网络网关。 
    
    如果正在对等互连一个虚拟网络 (Resource Manager) 与另一个虚拟网络（经典），则无法启用此选项。 虽然流量在这两个虚拟网络之间流动，但虚拟网络 (Resource Manager) 的流量无法流经附加到虚拟网络（经典）的网络网关。
7. 单击“确定”按钮，将子网添加到所选的虚拟网络。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet peering create](/cli/azure/network/vnet/peering#create?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>查看或更改对等互连设置

1. 使用具有必要的[角色和权限](#roles-permissions)的帐户登录到[门户](https://portal.azure.com)。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请单击它。
3. 在显示的“虚拟网络”边栏选项卡中，单击想要为其创建对等互连的虚拟网络。
4. 在针对所选虚拟网络显示的窗格中，单击“设置”部分中的“对等互连”。
5. 单击要查看或更改其设置的对等互连。
6. 更改相应的设置。 针对每个设置的相关选项，请阅读本文“创建对等互连”部分的[步骤 6](#add-peering)。 

    >[!NOTE]
    >成功创建对等互连有几项要求、约束和注意事项。 创建对等互连之前，请确保已熟悉本文的[要求和限制](#about-peering)部分中的注意事项列表。
    >

7. 单击“保存” 。

**命令**

|工具|命令|
|---|---|
|CLI|[az 网络 vnet 对等互连列表](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#list)可列出虚拟网络的对等互连，[az 网络 vnet 对等互连显示](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#show)可显示特定对等互连的设置，[az 网络 vnet 对等互连更新](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#update)可更改对等互连设置。|
|PowerShell|[Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) 可检索视图对等互连设置，[Set-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json) 可更改设置。|

## <a name="delete-subnet"></a>删除对等互连
当对等互连删除后，来自虚拟网络的流量将不再流动到已对等互连的虚拟网络中。 将通过 Resource Manager 部署的虚拟网络对等互连后，每个虚拟网络都具与另一个虚拟网络的对等互连。 虽然从一个虚拟网络删除对等互连会禁用虚拟网络之间的通信，但这不会删除另一个虚拟网络的对等互连。 存在于另一个虚拟网络中的对等互连的对等互连状态为“已断开连接”。 必须在第一个虚拟网络中重新创建对等互连，并且这两个虚拟网络的对等互连状态均更改为“已连接”后，才能重新创建对等互连。 

如果希望虚拟网络偶尔进行通信，而非始终通信，与其删除对等互连，更好的方法是改为将“允许虚拟网络访问”设置设为“禁用”。 若要了解如何操作，请阅读本文[创建对等互连](#create-peering)部分的步骤 6。 可发现禁用和启用网络访问比删除并重新创建对等互连更加容易。

1. 使用具有必要的[角色和权限](#roles-permissions)的帐户登录到[门户](https://portal.azure.com)。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请单击它。
3. 在显示的“虚拟网络”边栏选项卡中，单击想要从中删除对等互连的虚拟网络。
4. 在针对所选虚拟网络显示的边栏选项卡中，单击“设置”下的“对等互连”。
5. 在对等互连边栏选项卡中显示的对等互连列表中，右键单击要删除的对等互连，单击“删除”，然后单击“是”即可从第一个虚拟网络中删除对等互连。
6. 完成先前的步骤，以从对等互连中的另一个虚拟网络中删除对等互连。

**命令**

|工具|命令|
|---|---|
|CLI|[az network vnet peering delete](/cli/azure/network/vnet/peering?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>后续步骤

* 完成虚拟网络对等互连教程。 可在通过相同或不同订阅中的相同或不同部署模型创建的虚拟网络之间创建虚拟网络对等互连。 完成适用于以下方案之一的教程：
 
    |Azure 部署模型  | 订阅  |
    |---------|---------|
    |都是 Resource Manager 模型 |[相同](virtual-network-create-peering.md)|
    | |[不同](create-peering-different-subscriptions.md)|
    |一个是 Resource Manager 模型，一个是经典模型     |[相同](create-peering-different-deployment-models.md)|
    | |[不同](create-peering-different-deployment-models-subscriptions.md)|

* 了解如何创建[中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) 
* 了解所有[虚拟网络对等互连设置以及如何对其进行更改](virtual-network-manage-peering.md)
