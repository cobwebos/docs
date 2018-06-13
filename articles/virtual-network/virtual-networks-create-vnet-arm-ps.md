---
title: 创建包含多个子网的 Azure 虚拟网络 - PowerShell | Microsoft Docs
description: 了解如何使用 PowerShell 创建包含多个子网的虚拟网络。
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: c90bdc9381bf98e5c1457e8e28f74105227d8f8d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2018
ms.locfileid: "29880487"
---
# <a name="create-a-virtual-network-with-multiple-subnets-using-powershell"></a>使用 PowerShell 创建包含多个子网的虚拟网络

虚拟网络能让几种类型的 Azure 资源与 Internet 进行通信以及彼此之间私下通信。 在虚拟网络中创建多个子网即可对网络进行划分，以便筛选或控制子网之间的通信流。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建子网
> * 测试虚拟机之间的网络通信

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。 

## <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建资源组。 以下示例在“EastUS”位置创建名为“myResourceGroup”的资源组：

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 创建虚拟网络。 以下示例使用地址前缀 *10.0.0.0/16* 创建一个名为 *myVirtualNetwork* 的虚拟网络。

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

**AddressPrefix** 以 CIDR 表示法指定。 指定的地址前缀包括 IP 地址 10.0.0.0-10.0.255.254。

## <a name="create-a-subnet"></a>创建子网

创建子网时，先创建子网配置，然后使用子网配置更新虚拟网络。 使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 创建子网配置。 以下示例针对公用子网和专用子网创建两种子网配置：

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork
```

指定的子网 **AddressPrefix** 必须在为虚拟网络定义的地址前缀内。 Azure DHCP 将子网地址前缀中的 IP 地址分配给子网中部署的资源。 Azure 只将地址 10.0.0.4-10.0.0.254 分配给**公用**子网中部署的资源，因为 Azure 会保留每个子网中的前四个地址（10.0.0.0-10.0.0.3，用于此示例中的子网）和最后一个地址（10.0.0.255，用于此示例中的子网）。

使用 [Set-azurermvirtualnetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 将子网配置写入虚拟网络，以便在虚拟网络中创建子网：

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

在部署 Azure 虚拟网络和子网供生产用之前，建议你全面了解地址空间[注意事项](manage-virtual-network.md#create-a-virtual-network)和[虚拟网络限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 将资源部署到子网后，如果对虚拟网络和子网进行某些更改（例如更改地址范围），则可能需要重新部署子网中部署的现有 Azure 资源。

## <a name="test-network-communication"></a>测试网络通信

虚拟网络能让几种类型的 Azure 资源与 Internet 进行通信以及彼此之间私下通信。 虚拟机是一种能部署到虚拟网络的资源类型。 在虚拟网络中创建两个虚拟机，以便在稍后的步骤中测试其与 Internet 的通信。 

### <a name="create-virtual-machines"></a>创建虚拟机

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。 以下示例在 *myVirtualNetwork* 虚拟网络的公用子网中创建一个名为“myVmWeb”的虚拟机。 `-AsJob` 选项会在后台创建虚拟机，因此可继续执行下一步。 系统提示时，请输入需要用来登录到虚拟机的用户名和密码。

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

Azure 会自动分配 10.0.0.4 作为虚拟机的专用 IP 地址，因为 10.0.0.4 是公用子网中的第一个可用 IP 地址。 

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

创建虚拟机需花费几分钟的时间。 Azure 已分配 10.0.1.4（尚未在返回的输出中）作为虚拟机的专用 IP 地址，因为 10.0.1.4 是 *myVirtualNetwork* 的专用子网中的第一个可用 IP 地址。 

请等到虚拟机创建完毕且 PowerShell 返回输出之后再继续剩余的步骤。

本文中创建的虚拟机有一个[网络接口](virtual-network-network-interface.md)，该网络接口有一个动态分配给它的 IP 地址。 部署 VM 后，可以[添加多个公用 IP 地址和专用 IP 地址，或将 IP 地址分配方法更改为“静态”](virtual-network-network-interface-addresses.md#add-ip-addresses)。 可以[添加网络接口](virtual-network-network-interface-vm.md#vm-add-nic)，但其数目不能超过创建虚拟机时选择的 [VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)所允许的限制。 还可以为 VM [启用单根 I/O 虚拟化 (SR-IOV)](create-vm-accelerated-networking-powershell.md)，但前提是创建的 VM 的 VM 大小支持该功能。

### <a name="communicate-between-virtual-machines-and-with-the-internet"></a>在虚拟机之间通信以及与 Internet 通信

可以从 Internet 连接到虚拟机的公用 IP 地址。 Azure 创建 *myVmMgmt* 虚拟机时，也会创建名为 *myVmMgmt* 的公用 IP 地址并将其分配给该虚拟机。 尽管虚拟机不需要分配有公用 IP 地址，但默认情况下 Azure 仍会为你创建的每个虚拟机分配一个公用 IP 地址。 若要通过 Internet 与虚拟机进行通信，必须为虚拟机分配公用 IP 地址。 所有虚拟机都可以与 Internet 进行出站通信，无论是否为虚拟机分配了公用 IP 地址。 若要详细了解 Azure 中的出站 Internet 连接，请参阅 [Azure 中的出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 返回虚拟机的公用 IP 地址。 以下示例返回了 myVmMgmt 虚拟机的公用 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

从本地计算机使用以下命令创建与 myVmMgmt 虚拟机的远程桌面会话。 将 `<publicIpAddress>` 替换为上一命令返回的 IP 地址。

```
mstsc /v:<publicIpAddress>
```

此时会创建远程桌面协议 (.rdp) 文件，并下载到计算机，同时打开该文件。 输入在创建虚拟机时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建虚拟机时输入过的凭据），然后单击“确定”。 你可能会在登录过程中收到证书警告。 单击“是”或“继续”继续进行连接。 

在后面的步骤中，将使用 ping 从 *myVmWeb* 虚拟机与 *myVmMgmt* 虚拟机进行通信。 Ping 使用默认情况下无法通过 Windows 防火墙的 ICMP。 在命令提示符处输入以下命令，允许 ICMP 通过 Windows 防火墙：

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

虽然本文中使用 ping，但在进行生产部署时，不建议允许 ICMP 通过 Windows 防火墙。

出于安全原因，通常会限制虚拟网络中可远程连接到的虚拟机数。 在本教程中，*myVmMgmt* 虚拟机用于管理虚拟网络中的 *myVmWeb* 虚拟机。 使用以下命令从 *myVmMgmt* 虚拟机通过远程桌面连接到 *myVmWeb* 虚拟机：

``` 
mstsc /v:myVmWeb
```

若要从 *myVmWeb* 虚拟机与 *myVmMgmt* 虚拟机通信，请在命令提示符处输入以下命令：

```
ping myvmmgmt
```

收到的输出类似于以下示例输出：
    
```
Pinging myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net [10.0.1.4] with 32 bytes of data:
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
Reply from 10.0.1.4: bytes=32 time<1ms TTL=128
    
Ping statistics for 10.0.1.4:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```
      
可以看到 *myVmMgmt* 虚拟机的地址是 10.0.1.4。 在之前的步骤中，已将 *myVmMgmt* 虚拟机部署到一个专用子网，而 10.0.1.4 则是该专用子网的地址范围中的第一个可用 IP 地址。  可以看到，该虚拟机的完全限定域名是 *myvmmgmt.dar5p44cif3ulfq00wxznl3i3f.bx.internal.cloudapp.net*。 尽管该域名的 *dar5p44cif3ulfq00wxznl3i3f* 部分不同于你的虚拟机，但其余部分是相同的。 默认情况下，所有 Azure 虚拟机均使用默认的 Azure DNS 服务。 虚拟网络中的所有虚拟机均可使用 Azure 的默认 DNS 服务解析同一虚拟网络中所有其他虚拟机的名称。 可以不使用 Azure 的默认 DNS 服务，而使用自己的 DNS 服务器或 Azure DNS 服务的专用域功能。 有关详细信息，请参阅[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)或[对专用域使用 Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

若要在 *myVmWeb* 虚拟机上安装用于 Windows Server 的 Internet Information Services (IIS)，请在 PowerShell 会话中输入以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

安装完 IIS 以后，请断开 *myVmWeb* 远程桌面会话的连接，留在 *myVmMgmt* 远程桌面会话中。 打开 Web 浏览器并浏览到 http://myvmweb。 此时会看到 IIS 欢迎页。

断开 *myVmMgmt* 远程桌面会话的连接。

获取 Azure 分配给 *myVmWeb* 虚拟机的公用地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

在自己的计算机上浏览到 *myVmWeb* 虚拟机的公用 IP 地址。 尝试从自己的计算机查看 IIS 欢迎页失败。 尝试之所以失败，是因为在部署虚拟机时，Azure 已默认为每个虚拟机创建网络安全组。 

网络安全组包含安全规则，这些规则可根据端口和 IP 地址允许或拒绝入站和出站网络流量。 Azure 创建的默认网络安全组允许通过同一虚拟网络中资源之间的所有端口进行通信。 对于 Windows 虚拟机，默认网络安全组拒绝所有端口中来自 Internet 的所有入站流量，TCP 端口 3389 (RDP) 除外。 因此，默认情况下，还可以通过 RDP 直接从 Internet 连接到 *myVmWeb* 虚拟机。当然，你可能不希望向 Web 服务器开放端口 3389。 由于在进行 Web 浏览时是通过端口 80 通信，因此如果从 Internet 通信失败，则是因为默认网络安全组中没有允许流量通过端口 80 的规则。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何部署具有多个子网的虚拟网络。 你还了解了创建 Windows 虚拟机时，Azure 将创建一个网络接口并将它附加到该虚拟机，并且还创建一个只允许通过端口 3389 来自 Internet 的流量的网络安全组。 学习下一个教程，了解如何筛选流向子网（而不是流向单个虚拟机）的网络流量。

> [!div class="nextstepaction"]
> [筛选流向子网的网络流量](./virtual-networks-create-nsg-arm-ps.md)
