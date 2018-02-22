---
title: "使用 Azure PowerShell 模块创建和管理 Windows VM | Microsoft Docs"
description: "教程 - 使用 Azure PowerShell 模块创建和管理 Windows VM"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4cf406dfbab40631c99da70085e99ba90f563411
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>使用 Azure PowerShell 模块创建和管理 Windows VM

Azure 虚拟机提供完全可配置的灵活计算环境。 本教程介绍 Azure 虚拟机的基本部署项目，例如选择 VM 大小、选择 VM 映像和部署 VM。 学习如何：

> [!div class="checklist"]
> * 创建并连接到 VM
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 大小
> * 调整 VM 的大小
> * 查看并了解 VM 状态


[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.3 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzureRmAccount` 以创建与 Azure 的连接。 

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建资源组。 

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机前创建资源组。 在以下示例中，在“EastUS”区域中创建了名为“myResourceGroupVM”的资源组：

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

创建或修改 VM 时指定资源组，本教程会对此进行演示。

## <a name="create-virtual-machine"></a>创建虚拟机

创建虚拟机时，可使用多个选项，例如操作系统映像、网络配置和管理凭据。 在此示例中，将创建名为“myVM”的虚拟机，它运行默认的 Windows Server 2016 Datacenter 最新版本。

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置虚拟机上管理员帐户所需的用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>连接到 VM

在部署完成后，创建到虚拟机的远程桌面连接。

运行以下命令，以返回虚拟机的公共 IP 地址。 需记下此 IP 地址，以便在后续步骤中使用浏览器连接到它测试 Web 连接。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

在本地计算机上使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为虚拟机的 publicIPAddress。 出现提示时，输入创建虚拟机时使用的凭据。

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>了解 VM 映像

Azure 应用商店包括许多可用于新建虚拟机的虚拟机映像。 在之前的步骤中，使用 Windows Server 2016 Datacenter 映像创建了虚拟机。 在此步骤中，使用 PowerShell 模块在应用商店搜索其他 Windows 映像，这些映像也可充当新 VM 的基础。 此过程包括查找发布者、产品/服务和映像名称 (Sku)。 

使用 [Get AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) 命令返回映像发布者的列表：

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

使用 [Get AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) 返回映像产品/服务的列表。 使用此命令，返回筛选了特定发布者的列表：

```azurepowershell-interactive
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```azurepowershell-interactive
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

然后，使用 [Get AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) 命令对发布者和产品/服务名称进行筛选，以返回映像名称的列表。

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```azurepowershell-interactive
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

此信息可用于部署具有特定映像的 VM。 此示例通过将 Windows Server 2016 与容器映像配合使用来部署虚拟机。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

`-AsJob` 参数以后台任务的方式创建 VM，因此 PowerShell 提示符会返回到你所在的位置。 可以通过 `Job` cmdlet 查看后台作业的详细信息。


## <a name="understand-vm-sizes"></a>了解 VM 大小

虚拟机大小决定虚拟机可用计算资源（如 CPU、GPU 和内存）的数量。 创建的虚拟机大小需适合预期的工作负荷。 如果工作负荷增加，可调整现有虚拟机的大小。

### <a name="vm-sizes"></a>VM 大小

下表将大小分类成了多个用例。  
| Type                     | 常见大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [常规用途](sizes-general.md)         |Dsv3、Dv3、DSv2、Dv2、DS、D、Av2、A0-7| CPU 与内存之比均衡。 适用于开发/测试、小到中型应用程序和数据解决方案。  |
| [计算优化](sizes-compute.md)   | Fs, F             | 高 CPU 与内存之比。 适用于中等流量的应用程序、网络设备和批处理。        |
| [内存优化](sizes-memory.md)    | Esv3、Ev3、M、GS、G、DSv2、DS、Dv2、D   | 较高的内存核心比。 适用于关系数据库、中到大型缓存和内存中分析。                 |
| [存储优化](sizes-storage.md)      | LS                | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | 专门针对大量图形绘制和视频编辑的 VM。       |
| [高性能](sizes-hpc.md) | H, A8-11          | 功能极其强大的 CPU VM 具有可选的高吞吐量网络接口 (RDMA)。 


### <a name="find-available-vm-sizes"></a>查找可用的 VM 大小

若要查看在特定区域可用的 VM 大小的列表，请使用 [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 命令。

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>调整 VM 的大小

部署 VM 后，可调整其大小以增加或减少资源分配。

调整 VM 大小之前，请检查所需的大小在当前 VM 群集上是否可用。 使用 [Get AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 命令返回大小的列表。 

```azurepowershell-interactive
Get-AzureRmVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

如果所需大小可用，则可从开机状态调整 VM 大小，但需在此操作期间重启 VM。

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
```

如果所需大小在当前群集上不可用，则需解除分配 VM，才能执行调整大小操作。 请注意，重新启动 VM 时会删除临时磁盘上的任何数据，并且如果不使用静态 IP 地址，则公共 IP 地址会发生更改。 

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroupVM"
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM"  -Name $vm.name
```

## <a name="vm-power-states"></a>VM 电源状态

Azure VM 可能会处于多种电源状态之一。 从虚拟机监控程序的角度来看，此状态表示 VM 的当前状态。 

### <a name="power-states"></a>电源状态

| 电源状态 | 说明
|----|----|
| 正在启动 | 指示正在启动虚拟机。 |
| 正在运行 | 指示虚拟机正在运行。 |
| 正在停止 | 指示正在停止虚拟机。 | 
| 已停止 | 指示虚拟机已停止。 请注意，虚拟机处于停止状态时仍会产生计算费用。  |
| 正在解除分配 | 指示正在解除分配虚拟机。 |
| 已解除分配 | 指示虚拟机已从虚拟机监控程序中完全删除，但仍可在控制面板中使用。 处于“已解除分配”状态的虚拟机不会产生计算费用。 |
| - | 指示虚拟机的电源状态未知。 |

### <a name="find-power-state"></a>查找电源状态

若要检索特定 VM 的状态，请使用 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 命令。 请确保为虚拟机和资源组指定有效的名称。 

```azurepowershell-interactive
Get-AzureRmVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

输出：

```azurepowershell-interactive
Status
------
PowerState/running
```

## <a name="management-tasks"></a>管理任务

在虚拟机生命周期中，可能需要运行管理任务，例如启动、停止或删除虚拟机。 此外，可能还需要创建脚本来自动执行重复或复杂的任务。 使用 Azure PowerShell，可从命令行或脚本运行许多常见的管理任务。

### <a name="stop-virtual-machine"></a>停止虚拟机

使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) 停止并解除分配虚拟机：

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM" -Force
```

若要让虚拟机保持已预配状态，请使用 -StayProvisioned 参数。

### <a name="start-virtual-machine"></a>启动虚拟机

```azurepowershell-interactive
Start-AzureRmVM -ResourceGroupName "myResourceGroupVM" -Name "myVM"
```

### <a name="delete-resource-group"></a>删除资源组

删除资源组会删除其包含的所有资源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroupVM" -Force
```

## <a name="next-steps"></a>后续步骤

在本教程中，已学习 VM 创建和管理的基本知识，例如如何：

> [!div class="checklist"]
> * 创建并连接到 VM
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 大小
> * 调整 VM 的大小
> * 查看并了解 VM 状态

请转到下一教程，了解 VM 磁盘。  

> [!div class="nextstepaction"]
> [创建和管理 VM 磁盘](./tutorial-manage-data-disk.md)
