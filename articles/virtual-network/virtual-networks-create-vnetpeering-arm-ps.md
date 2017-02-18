---
title: "Azure 虚拟网络对等互连 - PowerShell | Microsoft 文档"
description: "了解如何使用 Azure PowerShell 创建虚拟网络对等互连。"
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: 5240bfc66ce15f845a511b7f09a5cd6209c8d539
ms.openlocfilehash: 34cc0fbddadb3860320ae730c2bc9951c735c7f9


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>使用 Azure PowerShell 创建虚拟网络对等互连
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

若要使用 PowerShell 创建 VNet 对等互连，请执行以下步骤：

1. 如果你从未使用过 Azure PowerShell，请参阅 [How to Install and Configure Azure PowerShell](/powershell/azureps-cmdlets-docs) （如何安装和配置 Azure PowerShell），并始终按照说明进行操作，以登录到 Azure 并选择你的订阅。

    > [!NOTE]
    > 用于管理 VNet 对等互连的 PowerShell cmdlet 随附于 [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. 读取虚拟网络对象：

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. 若要建立 VNet 对等互连，需要创建两个链接，每个方向各一个。 下一步将首先创建从 VNet1 到 VNet2 的 VNet 对等互连链接：

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    返回的输出：
        
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
            UseRemoteGateways    : False
            RemoteGateways        : null
            RemoteVirtualNetworkAddressSpace : null

4. 此步骤将创建 VNet2 到 VNet1 的 VNet 对等互连链接：

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    返回的输出：

        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
5. 创建 VNet 对等互连链接后，即可输入以下命令查看链接状态：

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    返回的输出：
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    每个 VNet 对等互连有几个可配置属性：
   
   | 选项 | 说明 | 默认 |
   |:--- |:--- |:--- |
   | 允许虚拟网络访问 |是否将对等 VNet 的地址空间包括为 Virtual_network 标记的一部分 |是 |
   | 允许转发的流量 |是否接受或丢弃不是源自对等 VNet 的流量 |否 |
   | 允许网关传输 |允许对等 VNet 使用你的 VNet 网关 |否 |
   | 使用远程网关 |使用对等方的 VNet 网关。 对等 VNet 必须已配置网关并且已选中“允许网关传输”。 如果你已配置了网关，则无法使用此选项 |否 |

    VNet 对等互连中的每个链接都有上述属性集。 例如，对于 VNet1 到 VNet2 的 VNet 对等互连链接，可以将 `AllowVirtualNetworkAccess` 设置为 *True*；而对于其他方向的 VNet 对等互连链接，可以将其设置为 *False*。

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    更改后，可以运行 `Get-AzureRmVirtualNetworkPeering` 仔细检查属性值。 运行上述 cmdlet 后，可以在输出中看到 `AllowForwardedTraffic` 设置已更改为 *True*。

        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null

    建立对等互连后，VM 应能够跨这两个 VNet 相互通信。 默认情况下，`AllowVirtualNetworkAccess` 为 *True*，且 VNet 对等互连将预配正确的 ACL 以允许 VNet 之间的通信。 仍可应用网络安全组 (NSG) 规则来阻止特定子网或虚拟机之间的连接，从而实现两个 VNet 之间访问权限的细粒度控制。 阅读 [网络安全组](virtual-networks-create-nsg-arm-ps.md) 一文，了解有关 NSG 的详细信息。

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

若要使用 PowerShell 跨订阅创建 VNet 对等互连，请完成以下步骤：

1. 使用订阅 A 的特权用户 A 帐户登录到 Azure，并运行以下 cmdlet：

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    这不是必须的。 只要请求匹配，即使用户分别提出针对各个 VNet 的对等互连请求，也可建立对等互连。 添加其他 VNet 的特权用户作为本地 VNet 中的用户可以更轻松地进行设置。
2. 使用订阅 B 的特权用户 B 帐户登录到 Azure，并运行以下 cmdlet：

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > 如果要在通过 Azure Resource Manager 部署模型创建的两个 VNet 之间创建对等互连，请继续执行本部分中的剩余步骤。 如果两个 VNet 是通过不同部署模型创建的，请跳过本部分的剩余步骤并完成本文的[将通过不同部署模型创建的虚拟网络对等互连](#x-model)部分中所述的步骤。

3. 在用户 A 的登录会话中，运行以下命令：

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. 在用户 B 的登录会话中，运行以下 cmdlet：

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. 建立对等互连后，VNet3 中的任意 VM 应该能够与 VNet5 中的任意 VM 进行通信。

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. 在此方案中，可以运行以下 PowerShell cmdlet 建立 VNet 对等互连。 需要将 `AllowForwardedTraffic` 属性设置为 *True*，并将 VNET1 链接到 HubVNet，这能允许来自对等互连 VNet 以外地址空间的入站流量。

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. 对等互连建立后，可以参考此 [文章](virtual-network-create-udr-arm-ps.md) ，定义用户定义的路由 (UDR)，以便通过虚拟设备重定向 VNet1 流量以使用其功能。 在路由中指定下一个跃点地址时，可以在对等 VNet HubVNet 中将其设置为虚拟装置的 IP 地址。 下面是一个示例：

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. 如果要在*同一*订阅中创建通过不同部署模型部署的 VNet 之间的对等互连，请跳到步骤 2。 在**预览**版中，能够在*不同*订阅中创建通过不同部署模型部署的 VNet 之间的对等互连。 预览版功能的可靠性和服务级协议与正式版功能不是同一级别。 如果要在不同订阅中创建通过不同部署模型部署的 VNet 之间的对等互连，必须先完成以下任务：
    - 通过从 PowerShell 输入以下命令在 Azure 订阅中注册预览版功能：`Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network`
    - 完成本文的[跨订阅对等互连](#x-sub)部分中的步骤 1-2。
2. 输入以下命令，读取 **VNET1**（Azure Resource Manager 虚拟网络）的虚拟网络对象：

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. 若要在此方案中建立 VNet 对等互连，只需要一个链接，具体而言，是从 **VNET1** 到 **VNET2** 的链接。 执行此步骤需要先了解经典 VNet 的资源 ID。 资源组 ID 格式如以下示例所示：

        subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    请务必将 SubscriptionID、ResourceGroupName 和 VirtualNetworkName 替换为相应的名称。

    这可以通过输入以下命令实现：

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. 创建 VNet 对等互连链接后，可以看到链接状态，如以下输出中所示：
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-a-vnet-peering"></a>删除 VNet 对等互连
1. 若要删除 VNet 对等互连，需要运行以下 cmdlet：

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    若要删除这两个链接，请输入以下命令：

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. 删除 VNET 对等互连中的一个链接后，该对等链接状态将为“断开”。 在此状态下，在对等链接状态更改为“已启动”之前无法重新创建链接。 建议先删除这两个链接，然后再重新创建 VNet 对等互连。




<!--HONumber=Feb17_HO1-->


