---
title: 创建虚拟网络 - 快速入门 - Azure PowerShell | Microsoft Docs
description: 本快速入门介绍如何使用 Azure 门户创建虚拟网络。 虚拟网络能让 Azure 资源（例如虚拟机）彼此之间私下通信以及与 Internet 进行通信。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 3d4c8e130f96c1b89247fe0c092363c33032ec3d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>快速入门：使用 PowerShell 创建虚拟网络

虚拟网络能让 Azure 资源（例如虚拟机 (VM)）彼此之间私下通信以及与 Internet 进行通信。 本快速入门介绍如何创建虚拟网络。 创建虚拟网络后，将两个 VM 部署到该虚拟网络中。 然后从 Internet 连接到其中一个 VM，并在两个 VM 之间进行私下通信。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本快速入门需要 AzureRM PowerShell 模块 5.4.1 或更高版本。 要查找已安装的版本，请运行 ` Get-Module -ListAvailable AzureRM`。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。

## <a name="create-a-virtual-network"></a>创建虚拟网络

在创建虚拟网络之前，必须创建一个资源组用于包含该虚拟网络。 使用 [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) 创建资源组。 以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 创建虚拟网络。 以下示例在“EastUS”位置创建名为“myVirtualNetwork”的默认虚拟网络

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Azure 资源将部署到虚拟网络中的子网，因此需要创建一个子网。 使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 创建子网配置。 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) 将子网配置写入虚拟网络，以便在虚拟网络中创建子网：

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM：

### <a name="create-the-first-vm"></a>创建第一个 VM

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM。 运行以下命令时，会提示输入凭据。 输入的值将配置为用于 VM 的用户名和密码。 `-AsJob` 选项会在后台创建 VM，因此可继续执行下一步。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

返回与以下示例输出类似的输出，并且 Azure 开始在后台创建 VM。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>创建第二个 VM 

输入以下命令：

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

创建 VM 需要几分钟时间。 在前一个命令执行并且输出返回到 PowerShell 之前，不会继续执行下一步。

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 返回 VM 的公共 IP 地址。 以下示例返回 myVm1 VM 的公共 IP 地址：

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

在以下命令中将 `<publicIpAddress>` 替换为前一个命令返回的公共 IP 地址，然后输入以下命令： 

```
mstsc /v:<publicIpAddress>
```

此时会创建远程桌面协议 (.rdp) 文件，并下载到计算机。 打开下载的 rdp 文件。 出现提示时，选择“连接”。 输入在创建 VM 时指定的用户名和密码。 可能需要选择“更多选择”，然后选择“使用其他帐户”，以指定在创建 VM 时输入的凭据。 选择“确定”。 你可能会在登录过程中收到证书警告。 如果收到警告，请选择“是”或“继续”以继续连接。

## <a name="communicate-between-vms"></a>VM 之间进行通信

从 *myVm1* VM 上的 PowerShell，输入 `ping myvm2`。 Ping 会失败，因为 ping 使用 Internet 控制消息协议 (ICMP)，而默认情况下不允许 ICMP 通过 Windows 防火墙。

若要在稍后的步骤中允许 *myVm2* ping 通 *myVm1*，请从 PowerShell 输入以下命令，以允许 ICMP 入站流量通过 Windows 防火墙：

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

关闭与 *myVm1* 的远程桌面连接。 

再次完成[从 Internet 连接到 VM](#connect-to-a-vm-from-the-internet) 中的步骤，但这次连接到 *myVm2*。 

在 *myVm2* VM上的命令提示符处，输入 `ping myvm1`。

将从 *myVm1* 收到答复，因为在上一步中已经允许 ICMP 通过 *myVm1* VM 上的 Windows 防火墙。

关闭与 *myVm2* 的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 将其删除：

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了默认的虚拟网络和两个 VM。 你从 Internet 连接到了其中一个 VM，然后该 VM 与另一个 VM 进行了私下通信。 若要了解有关虚拟网络设置的详细信息，请参阅[管理虚拟网络](manage-virtual-network.md)。 

默认情况下，Azure 允许虚拟机之间进行不受限制的私下通信，但仅允许从 Internet 到 Windows VM 的入站远程桌面连接。 若要了解如何允许或限制进出 VM 的不同类型的网络通信，请转到[筛选网络流量](tutorial-filter-network-traffic.md)教程。
