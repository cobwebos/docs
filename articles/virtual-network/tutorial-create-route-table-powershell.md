---
title: "路由网络流量 - PowerShell | Microsoft Docs"
description: "了解如何使用 PowerShell 通过路由表路由网络流量。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>使用 PowerShell 通过路由表路由网络流量

默认情况下，Azure 自动在虚拟网络中的所有子网之间路由流量。 可以创建自己的路由来覆盖 Azure 的默认路由。 创建自定义路由的功能非常有用，例如，可以通过防火墙在子网之间路由流量。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建路由表
> * 创建路由
> * 将路由表关联到虚拟网络子网
> * 测试路由
> * 排查路由问题

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。 

## <a name="create-a-route-table"></a>创建路由表

默认情况下，Azure 在虚拟网络中的所有子网之间路由流量。 若要详细了解 Azure 的默认路由，请参阅[系统路由](virtual-networks-udr-overview.md)。 若要覆盖 Azure 的默认路由，可以创建包含路由的路由表，并将该路由表关联到虚拟网络子网。

创建路由表之前，需使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建资源组。 以下示例为本文中创建的所有资源创建名为 *myResourceGroup* 的资源组。 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

使用 [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable) 创建路由表。 以下示例创建名为 *myRouteTablePublic* 的路由表。

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>创建路由

一个路由表包含零个或多个路由。 使用 [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable) 检索路由表对象以创建路由，使用 [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig) 创建路由，然后使用 [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable) 将路由配置写入路由表。 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

该路由通过 IP 地址为 10.0.2.4 的网络虚拟设备来定向所有发往 10.0.1.0/24 地址前缀的流量。 后续步骤将会创建该网络虚拟设备以及具有指定地址前缀的子网。 该路由覆盖 Azure 的默认路由。默认路由在子网之间直接路由流量。 每个路由指定下一跃点类型。 下一跃点类型指示 Azure 如何路由流量。 在此示例中，下一跃点类型为 *VirtualAppliance*。 若要详细了解 Azure 中的所有可用下一跃点类型及其使用时机，请参阅[下一跃点类型](virtual-networks-udr-overview.md#custom-routes)。

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

将路由表关联到子网之前，必须先创建虚拟网络和子网。 使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 创建虚拟网络。 以下示例使用地址前缀 *10.0.0.0/16* 创建一个名为 *myVirtualNetwork* 的虚拟网络。

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 创建三个子网配置，以创建三个子网。 以下示例针对公共、专用和外围网络子网创建三个子网配置：

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

地址前缀必须在针对虚拟网络定义的地址前缀中。 子网地址前缀不能相互重叠。

使用 [Set-azurermvirtualnetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 将子网配置写入虚拟网络，以便在虚拟网络中创建子网：

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

可将一个路由表关联到零个或多个子网。 子网可以没有或有一个与其相关联的路由表。 来自子网的出站流量将会根据 Azure 的默认路由，以及已添加到与子网关联的路由表中的任何自定义路由进行路由。 使用 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 将 *myRouteTablePublic* 路由表关联到公共子网，然后使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) 将子网配置写入虚拟网络。

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

在部署用于生产的路由表之前，我们建议全面了解 [Azure 中的路由](virtual-networks-udr-overview.md)和 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="test-routing"></a>测试路由

若要测试路由，需要创建一个虚拟机，充当用于路由上一步骤中创建的路由的网络虚拟设备。 创建网络虚拟设备后，可虚拟机部署到公共和专用子网。 然后通过网络虚拟设备将流量从公共子网路由到专用子网。

### <a name="create-a-network-virtual-appliance"></a>创建网络虚拟设备

运行网络应用程序的虚拟机通常称为网络虚拟设备。 网络虚拟设备通常会接收网络流量、执行某项操作，然后根据网络应用程序中配置的逻辑转发或丢弃网络流量。 

#### <a name="create-a-network-interface"></a>创建网络接口

在上一步骤中，我们创建了将网络虚拟设备指定为下一跃点类型的路由。 运行网络应用程序的虚拟机通常称为网络虚拟设备。 在生产环境中，部署的网络虚拟设备通常是预配置的虚拟机。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 中提供了多个网络虚拟设备。 本文将会创建一个基本的虚拟机。

虚拟机上附加了一个或多个网络接口，使该虚拟机能够与网络通信。 要使网络接口能够转发发送给它的、而不是发往其自身 IP 地址的网络流量，必须为该网络接口启用 IP 转发。 在创建网络接口之前，必须使用 [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) 检索虚拟网络 ID，然后使用 [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) 检索子网 ID。 使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 在已启用 IP 转发的外围网络子网中创建网络接口。

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

#### <a name="create-a-virtual-machine"></a>创建虚拟机

若要创建虚拟机并在其上附加现有的网络接口，必须先使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 创建虚拟机配置。 该配置包含上一步骤中创建的网络接口。 系统提示输入用户名和密码时，请选择用来登录虚拟机的用户名和密码。 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

运行 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 使用虚拟机配置创建虚拟机。 以下示例创建名为 *myVmNva* 的虚拟机。 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` 选项会在后台创建虚拟机，因此可继续执行下一步。 系统提示时，请输入需要用来登录到虚拟机的用户名和密码。 在生产环境中，部署的网络虚拟设备通常是预配置的虚拟机。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1) 中提供了多个网络虚拟设备。

Azure 分配了 10.0.2.4 作为虚拟机的专用 IP 地址，因为 10.0.2.4 是 *myVirtualNetwork* 的外围网络子网中第一个可用的 IP 地址。

### <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个虚拟机，以便可以在后续步骤中验证来自公共子网的流量是否通过网络虚拟设备路由到专用子网。 

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 在公共子网中创建虚拟机。 以下示例在 *myVirtualNetwork* 虚拟网络的公用子网中创建一个名为“myVmWeb”的虚拟机。 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure 分配了 10.0.0.4 作为虚拟机的专用 IP 地址，因为 10.0.1.4 是 *myVirtualNetwork* 的公共子网中第一个可用的 IP 地址。

在专用子网中创建一个虚拟机。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

创建虚拟机需花费几分钟的时间。 Azure 分配了 10.0.1.4 作为虚拟机的专用 IP 地址，因为 10.0.1.4 是 *myVirtualNetwork* 的专用子网中第一个可用的 IP 地址。 

请等到虚拟机创建完毕且 Azure 向 PowerShell 返回了输出之后再继续下一步。

### <a name="route-traffic-through-a-network-virtual-appliance"></a>通过网络虚拟设备路由流量

使用 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 使 *myVmWeb* 和 *myVmMgmt* 的入站 ICMP 能够通过 Windows 防火墙，以便在后续步骤中可以使用 tracert 来测试虚拟机之间的通信：

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

上述命令可能需要几分钟才能完成。 请在上述命令已完成并且向 PowerShell 返回了输出之后，才继续下一步。 虽然本文中使用 tracert 测试路由，但在进行生产部署时，不建议允许 ICMP 通过 Windows 防火墙。

从 Internet 连接到虚拟机的公共 IP 地址。 使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 返回虚拟机的公用 IP 地址。 以下示例返回了 myVmMgmt 虚拟机的公用 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

从本地计算机使用以下命令创建与 myVmMgmt 虚拟机的远程桌面会话。 将 `<publicIpAddress>` 替换为上一命令返回的 IP 地址。

```
mstsc /v:<publicIpAddress>
```

此时会创建远程桌面协议 (.rdp) 文件，并下载到计算机，同时打开该文件。 输入在创建虚拟机时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建虚拟机时输入过的凭据），然后选择“确定”。 你可能会在登录过程中收到证书警告。 单击“是”或“继续”继续进行连接。 

在[启用 IP 转发](#enable-ip-forwarding)中已经在 Azure 中为虚拟机的网络接口启用了 IP 转发。 在虚拟机中，操作系统或虚拟机中运行的应用程序也必须能够转发网络流量。 在生产环境中部署网络虚拟设备时，该设备通常会筛选、记录数据或执行其他某种功能，然后再转发流量。 但在本文中，操作系统只会转发它收到的所有流量。 在 *myVmNva* 的操作系统中启用 IP 转发：

在 *myVmMgmt* 虚拟机中的命令提示符下，通过远程桌面连接到 *myVmNva* 虚拟机：

``` 
mstsc /v:myVmNva
```
    
若要在 *myVmNva* 虚拟机的操作系统中启用 IP 转发，请在 *myVmNva* 虚拟机上的 PowerShell 中输入以下命令：

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
重启 *myVmNva* 虚拟机，这也会断开远程桌面会话，使你保持与 *myVmMgmt* 虚拟机建立远程桌面会话。

重启 *myVmNva* 虚拟机后，使用以下命令测试从 *myVmMgmt* 虚拟机发往 *myVmWeb* 虚拟机的网络流量的路由。

```
tracert myvmweb
```

响应类似于以下示例：

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

可以看到，流量直接从 *myVmMgmt* 虚拟机路由到了 *myVmWeb* 虚拟机。 Azure 的默认路由，直接在子网之间路由流量。

使用以下命令从 *myVmMgmt* 虚拟机通过远程桌面连接到 *myVmWeb* 虚拟机：

``` 
mstsc /v:myVmWeb
```

若要测试从 *myVmWeb* 虚拟机到 *myVmMgmt* 虚拟机的网络流量路由，请在命令提示符下输入以下命令：

```
tracert myvmmgmt
```

响应类似于以下示例：

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

可以看到，第一个跃点为 10.0.2.4，即网络虚拟设备的专用 IP 地址。 第二个跃点为 10.0.1.4，即 *myVmMgmt* 虚拟机的专用 IP 地址。 添加到 *myRouteTablePublic* 路由表并关联到公共子网的路由导致 Azure 通过 NVA 路由流量，而不是直接将流量路由到专用子网。

关闭与 *myVmWeb* 和 *myVmMgmt* 虚拟机建立的远程桌面会话。

## <a name="troubleshoot-routing"></a>排查路由问题

如前面的步骤所述，Azure 会应用默认路由，而你可以选择性地使用自己的路由覆盖默认路由。 有时，无法按预期方式路由流量。 使用 [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) 在美国东部区域启用网络观察程序（如果尚未在该区域启用网络观察程序）：

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

使用 [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) 确定两个虚拟机之间的流量路由方式。 例如，以下命令测试从 *myVmWeb* (10.0.0.4) 虚拟机到 *myVmMgmt* (10.0.1.4) 虚拟机的流量路由：

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
等待片刻后，会返回以下输出：

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

输出中指出，从 *myVmWeb* 到 *myVmMgmt* 的流量的下一跃点 IP 地址为 10.0.2.4（*myVmNva* 虚拟机），下一跃点类型为 *VirtualAppliance*，导致路由的路由表为 *myRouteTablePublic*。

每个网络接口的有效路由是 Azure 默认路由与定义的任何路由的组合。 使用 [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable) 查看虚拟机中网络接口的所有有效路由。 例如，若要显示 *myVmWeb* 虚拟机中 *myVmWeb* 网络接口的有效路由，请输入以下命令：

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

随后将返回所有默认路由，以及在上一步骤中添加的路由。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

在本文中，我们创建了一个路由表并将其关联到了某个子网。 我们还创建了一个网络虚拟设备，用于将流量从公共子网路由到专用子网。 尽管可以在一个虚拟网络中部署多个 Azure 资源，但无法将某些 Azure PaaS 服务的资源部署到虚拟网络。 不过，仍可以限制为只允许来自某个虚拟网络子网的流量访问某些 Azure PaaS 服务的资源。 请继续学习下一篇文章，了解如何限制 Azure PaaS 资源的网络访问。

> [!div class="nextstepaction"]
> [限制 PaaS 资源的网络访问](virtual-network-service-endpoints-configure.md#azure-powershell)