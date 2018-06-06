---
title: 路由网络流量 - Azure PowerShell | Microsoft Docs
description: 本文介绍了如何使用 PowerShell 通过路由表来路由网络流量。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 2aca1de567dbd4d37daf7f9dd7c407b669396a47
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "31603702"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>使用 PowerShell 通过路由表路由网络流量

默认情况下，Azure 自动在虚拟网络中的所有子网之间路由流量。 可以创建自己的路由来覆盖 Azure 的默认路由。 创建自定义路由的功能非常有用，例如，可以通过网络虚拟设备 (NVA) 在子网之间路由流量。 在本文中，学习如何：

* 创建路由表
* 创建路由
* 创建包含多个子网的虚拟网络
* 将路由表关联到子网
* 创建用于流量路由的 NVA
* 将虚拟机 (VM) 部署到不同子网
* 通过 NVA 将从一个子网的流量路由到另一个子网

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。 

## <a name="create-a-route-table"></a>创建路由表

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

使用 [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable) 检索路由表对象以创建路由，使用 [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig) 创建路由，然后使用 [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable) 将路由配置写入路由表。 

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

使用 [Set-azurermvirtualnetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 将子网配置写入虚拟网络，以便在虚拟网络中创建子网：

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

使用 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 将 *myRouteTablePublic* 路由表关联到公共子网，然后使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) 将子网配置写入虚拟网络。

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

## <a name="create-an-nva"></a>创建 NVA

NVA 是执行网络功能（如路由、防火墙或 WAN 优化）的 VM。

在创建 VM 之前，请创建网络接口。

### <a name="create-a-network-interface"></a>创建网络接口

在创建网络接口之前，必须使用 [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) 检索虚拟网络 ID，然后使用 [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) 检索子网 ID。 使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 在已启用 IP 转发的外围网络子网中创建网络接口。

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

### <a name="create-a-vm"></a>创建 VM

若要创建 VM 并在其上附加现有的网络接口，必须先使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 创建 VM 配置。 该配置包含上一步骤中创建的网络接口。 系统提示输入用户名和密码时，请选择用来登录 VM 的用户名和密码。 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
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

运行 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 使用 VM 配置创建 VM。 以下示例创建名为 *myVmNva* 的 VM。 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

`-AsJob` 选项会在后台创建 VM，因此可继续执行下一步。

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM，以便可以在后续步骤中验证来自公共子网的流量是否通过网络虚拟设备路由到专用子网。 

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 在公共子网中创建一个 VM。 以下示例在 *myVirtualNetwork* 虚拟网络的公共子网中创建名为 *myVmPublic* 的 VM。 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

在专用子网中创建一个 VM。

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

创建 VM 需要几分钟时间。 请等到 VM 创建完毕且 Azure 向 PowerShell 返回了输出之后再继续下一步。

## <a name="route-traffic-through-an-nva"></a>通过 NVA 路由流量

使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 返回 *myVmPrivate* VM 的公共 IP 地址。 以下示例返回 *myVmPrivate* VM 的公共 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

从本地计算机使用以下命令创建与 *myVmPrivate* VM 的远程桌面会话。 将 `<publicIpAddress>` 替换为上一命令返回的 IP 地址。

```
mstsc /v:<publicIpAddress>
```

打开下载的 RDP 文件。 出现提示时，选择“连接”。

输入在创建 VM 时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建 VM 时输入的凭据），然后选择“确定”。 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。 

在稍后的步骤中，tracert.exe 命令用于测试路由。 Tracert 使用 Internet 控制消息协议 (ICMP)，而 Windows 防火墙会拒绝该协议。 在 *myVmPrivate* VM 上，通过 PowerShell 输入以下命令来允许 ICMP 通过 Windows 防火墙：

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

虽然本文中使用跟踪路由来测试路由，但在生产部署中，不建议允许 ICMP 通过 Windows 防火墙。

在[启用 IP 转发](#enable-ip-forwarding)中已经在 Azure 中为 VM 的网络接口启用了 IP 转发。 在 VM 中，VM 中运行的操作系统或应用程序也必须能够转发网络流量。 在 *myVmNva* 的操作系统中启用 IP 转发。

在 *myVmPrivate* VM 中的命令提示符下，通过远程桌面连接到 *myVmNva*：

``` 
mstsc /v:myvmnva
```
    
若要在操作系统中启用 IP 转发，请通过 *myVmNva* VM 在 PowerShell 中输入以下命令：

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
重启 *myVmNva* VM，这也会断开远程桌面会话的连接。

在仍与 *myVmPrivate* VM 保持连接的情况下重启 *myVmNva* VM 后，与 *myVmPublic* VM 建立远程桌面会话：

``` 
mstsc /v:myVmPublic
```
    
在 *myVmPublic* VM 上，通过 PowerShell 输入以下命令来允许 ICMP 通过 Windows 防火墙：

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

若要测试从 *myVmPublic* VM 发往 *myVmPrivate* VM 的网络流量的路由，请在 *myVmPublic* VM 上通过 PowerShell 输入以下命令：

```
tracert myVmPrivate
```

响应类似于以下示例：
    
```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```
      
可以看到，第一个跃点为 10.0.2.4，即 NVA 的专用 IP 地址。 第二个跃点为 10.0.1.4，即 *myVmPrivate* VM 的专用 IP 地址。 添加到 *myRouteTablePublic* 路由表并关联到公共子网的路由导致 Azure 通过 NVA 路由流量，而不是直接将流量路由到专用子网。

关闭与 *myVmPublic* VM 建立的远程桌面会话，这样，就会与 *myVmPrivate* VM 保持连接。

若要测试从 *myVmPrivate* VM 发往 *myVmPublic* VM 的网络流量的路由，请在 *myVmPrivate* VM 上通过命令提示符下输入以下命令：

```
tracert myVmPublic
```

响应类似于以下示例：

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
   
Trace complete.
```

可以看到流量从 *myVmPrivate* VM 直接路由到 *myVmPublic* VM。 默认情况下，Azure 直接在子网之间路由流量。

关闭与 *myVmPrivate* VM 建立的远程桌面会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

在本文中，我们创建了一个路由表并将其关联到了某个子网。 我们还创建了一个简单网络虚拟设备，用于将流量从公共子网路由到专用子网。 从 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) 部署各种执行网络功能（例如防火墙和 WAN 优化）的预配置网络虚拟设备。 若要了解有关路由的详细信息，请参阅[路由概述](virtual-networks-udr-overview.md)和[管理路由表](manage-route-table.md)。

尽管可以在一个虚拟网络中部署多个 Azure 资源，但无法将某些 Azure PaaS 服务的资源部署到虚拟网络。 不过，仍可以限制为只允许来自某个虚拟网络子网的流量访问某些 Azure PaaS 服务的资源。 若要了解如何操作，请参阅[限制对 PaaS 资源的网络访问](tutorial-restrict-network-access-to-resources-powershell.md)。
