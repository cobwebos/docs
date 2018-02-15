---
title: "在 Azure 中创建虚拟网络 - PowerShell | Microsoft Docs"
description: "快速了解如何使用 PowerShell 创建虚拟网络 虚拟网络能让多种不同类型的 Azure 资源互相私下通信。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 091e7e6cabf325cdd9d4289e7d22e71c583d91db
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-powershell"></a>使用 PowerShell 创建虚拟网络

本文将介绍如何创建虚拟网络。 创建虚拟网络后，向虚拟网络中部署两个虚拟机，并让它们互相私下通信。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.1.1 或更高版本。 要查找已安装的版本，请运行 ` Get-Module -ListAvailable AzureRM`。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。 所有 Azure 资源均在 Azure 位置（或区域）中创建。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 创建虚拟网络。 以下示例在“EastUS”位置创建名为“myVirtualNetwork”的默认虚拟网络

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/24
```

所有虚拟网络均分配有一个或多个地址前缀。 CIDR 表示法中指定了此地址空间。 地址空间 10.0.0.0/24 包含 10.0.0.0-10.0.0.254。 虚拟网络中包含零个或多个子网。 资源部署在虚拟网络的子网中。 

使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 创建子网配置。 所有子网都有一个地址前缀，该地址前缀存在于虚拟网络的地址前缀中。 此示例中创建了地址前缀与虚拟网络的地址前缀相同的的子网配置：

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

虽然子网地址前缀包含 10.0.0.0-10.0.0.254，但可用地址仅限 10.0.0.4-10.0.0.254，因为 Azure 保留了每个子网中的前四个地址 (0-3) 和最后一个地址。 由于子网地址前缀与虚拟网络地址前缀相同，因此该虚拟网络中仅可存在一个子网。

使用 [Set-azurermvirtualnetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 将子网配置写入虚拟网络，从而创建子网：

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>创建虚拟机

虚拟网络能让几种类型的 Azure 资源互相私下通信。 虚拟机是一种能部署到虚拟网络的资源类型。 在虚拟网络中创建两个虚拟机，以便在稍后的步骤中验证和了解虚拟机在虚拟网络中互相通信的原理。

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。 运行此步骤时，会提示输入凭据。 输入的值将配置为用于虚拟机的用户名和密码。 虚拟机创建的位置必须与虚拟网络在同一位置。 虽然在本文中虚拟机在同一资源组，但实际上并无此要求。 `-AsJob` 参数允许命令在后台运行，因此可以继续下一项任务。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

返回与以下示例输出类似的输出，并且 Azure 开始在后台创建虚拟机。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

创建期间，Azure DHCP 会将分配 10.0.0.4 自动到虚拟机，因为它是“default”子网中的第一个可用地址。

创建第二个虚拟机。 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```
创建虚拟机需花费几分钟的时间。 创建后，Azure 会返回有关已创建虚拟机的输出。 尽管不在返回的输出中，Azure 还是将 10.0.0.5 分配给了 myVm2 虚拟机，因为它是子网中下一个可用地址。

## <a name="connect-to-a-virtual-machine"></a>连接到虚拟机

使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 命令返回虚拟机的公共 IP 地址。 默认情况下，Azure 将向各虚拟机分配公共的、通过 Internet 进行连接的 IP 地址。 从[分配给各 Azure 区域的地址池](https://www.microsoft.com/download/details.aspx?id=41653)将公共 IP 地址分配给虚拟机。 虽然 Azure 知晓虚拟机分配的公共 IP 地址，但在虚拟机上运行的操作系统却不知道分配给它的任何公共 IP 地址。 以下示例返回了 myVm1 虚拟机的公共 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name myVm1 -ResourceGroupName myResourceGroup | Select IpAddress
```

从本地计算机使用以下命令创建与 myVm1 虚拟机的远程桌面会话。 将 `<publicIpAddress>` 替换为上一命令返回的 IP 地址。

```
mstsc /v:<publicIpAddress>
```

此时会创建远程桌面协议 (.rdp) 文件，并下载到计算机，同时打开该文件。 输入在创建虚拟机时指定的用户名和密码，然后单击“确定”。 你可能会在登录过程中收到证书警告。 单击“是”或“继续”继续进行连接。

## <a name="validate-communication"></a>验证通信

尝试 ping Windows 虚拟机失败，因为默认情况下 Windows 防火墙不允许 ping。 要允许 ping myVm1，请从命令提示符中输入以下命令：

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

要验证与 myVm2 的通信，请在虚拟机 myVm1 的命令提示符中输入以下命令. 提供创建虚拟机时使用的凭据，然后完成连接：

```
mstsc /v:myVm2
```

由于两个虚拟机都分配有来自“default”子网的私有 IP 地址，且远程桌面通过 Windows 防火墙开启，因此远程桌面连接成功。 可以通过主机名连接至 myVm2，因为 Azure 将自动为虚拟网络中的所有主机提供 DNS 名称解析。 在命令提示符中，从 myVm2 ping myVm1。

```
ping myvm1
```

ping 操作成功，因为在上一步中已经允许该命令通过虚拟机 myVm1 上的 Windows 防火墙。 要确认到 Internet 的出站通信，请输入以下命令：

```
ping bing.com
```

从 bing.com 接收了四个回复。默认情况下，虚拟网络中的任意虚拟机都可以与 Internet 进行出站通信。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令将其删除。 退出远程登录会话，然后从计算机运行以下命令以删除资源组：

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

本文将使用一个子网和两个虚拟机来部署默认虚拟网络。 要了解如何使用多个子网创建自定义虚拟网络并执行基本虚拟网络管理任务，请继续参阅教程的创建和管理自定义虚拟网络部分。


> [!div class="nextstepaction"]
> [创建和管理自定义虚拟网络](virtual-networks-create-vnet-arm-pportal.md#powershell)
