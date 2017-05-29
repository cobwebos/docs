---
title: "使用 Azure PowerShell 模块创建和管理 Windows VM | Microsoft Docs"
description: "教程 - 使用 Azure PowerShell 模块创建和管理 Windows VM"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: a26f33247710431d433f3309ecdaca20e605c75a
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017

---

# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>使用 Azure PowerShell 模块创建和管理 Windows VM

Azure 虚拟机提供完全可配置的灵活计算环境。 本教程介绍 Azure 虚拟机的基本部署项目，例如选择 VM 大小、选择 VM 映像和部署 VM。 你将学习如何：

> [!div class="checklist"]
> * 创建并连接到 VM
> * 选择并使用 VM 映像
> * 查看和使用特定 VM 大小
> * 调整 VM 的大小
> * 查看并了解 VM 状态

本教程需要 Azure PowerShell 模块 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建资源组。 

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 必须在创建虚拟机前创建资源组。 在此示例中，在“EastUS”区域中创建了名为“myResourceGroupVM”的资源组。 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location EastUS
```

创建或修改 VM 时指定资源组，本教程会对此进行演示。

## <a name="create-virtual-machine"></a>创建虚拟机

虚拟机必须连接到虚拟网络。 可以通过网络接口卡使用公共 IP 地址与虚拟机通信。

### <a name="create-virtual-network"></a>创建虚拟网络

使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 创建子网：

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
```

使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 创建虚拟网络：

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 ` 
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>创建公共 IP 地址

使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 创建一个公共 IP 地址：

```powershell
$pip = New-AzureRmPublicIpAddress ` 
  -ResourceGroupName myResourceGroupVM `
  -Location EastUS ` 
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>创建网络接口卡

使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 创建网络接口卡：

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location EastUS `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>创建网络安全组

Azure [网络安全组](../../virtual-network/virtual-networks-nsg.md) (NSG) 控制一个或多个虚拟机的入站和出站流量。 网络安全组规则允许或拒绝特定端口上或端口范围内的网络流量。 这些规则还可包括源地址前缀，这样只有源自预定义源的流量才可与虚拟机进行通信。 若要访问正安装的 IIS web 服务器，必须添加入站 NSG 规则。

若要创建入站 NSG 规则，请使用 [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig)。 以下示例创建名为“myNSGRule”的 NSG 规则，这将为虚拟机打开端口 3389：

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

将 myNSGRule 与 New-AzureRmNetworkSecurityGroup[](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) 结合使用，创建 NSG：

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupVM `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRule
```

使用 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 将 NSG 添加到虚拟网络中的子网：

```powershell
Set-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -VirtualNetwork $vnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24
```

使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) 更新虚拟网络：

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>创建虚拟机

创建虚拟机时，可使用多个选项，例如操作系统映像、磁盘大小调整和管理凭据。 在此示例中，将创建名为“myVM”的虚拟机，它运行最新版本的 Windows Server 2016 Datacenter。

使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置虚拟机上管理员帐户所需的用户名和密码：

```powershell
$cred = Get-Credential
```

使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 为虚拟机创建初始配置：

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

使用 [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) 向虚拟机配置添加操作系统信息：

```powershell
$vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM `
    -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

使用 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) 向虚拟机配置添加映像信息：

```powershell
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
```

使用 [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) 向虚拟机配置添加操作系统磁盘设置：

```powershell
$vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
```

使用 [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 向虚拟机配置添加以前创建的网络接口卡：

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroupVM -Location EastUS -VM $vm
```

## <a name="connect-to-vm"></a>连接到 VM

在部署完成后，创建到虚拟机的远程桌面连接。

运行以下命令，以返回虚拟机的公共 IP 地址。 需记下此 IP 地址，以便在后续步骤中使用浏览器连接到它测试 Web 连接。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

使用以下命令创建与虚拟机的远程桌面会话。 将 IP 地址替换为你的虚拟机的 publicIPAddress。 出现提示时，输入创建虚拟机时使用的凭据。

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>了解 VM 映像

Azure 应用商店包括许多可用于新建虚拟机的虚拟机映像。 在之前的步骤中，使用 Windows Server 2016 Datacenter 映像创建了虚拟机。 在此步骤中，使用 PowerShell 模块在应用商店搜索其他 Windows 映像，这些映像也可充当新 VM 的基础。 此过程包括查找发布者、产品/服务和映像名称 (Sku)。 

使用 [Get AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) 命令返回映像发布者的列表。  

```powersehll
Get-AzureRmVMImagePublisher -Location "EastUS"
```

使用 [Get AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) 返回映像产品/服务的列表。 使用此命令，返回筛选了特定发布者的列表。 

```powershell
Get-AzureRmVMImageOffer -Location "EastUS" -PublisherName "MicrosoftWindowsServer"
```

```powershell
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer EastUS 
WindowsServer     MicrosoftWindowsServer EastUS   
WindowsServer-HUB MicrosoftWindowsServer EastUS   
```

然后，使用 [Get AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) 命令对发布者和产品/服务名称进行筛选，以返回映像名称的列表。

```powershell
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```powershell
Skus                            Offer         PublisherName          Location
----                            -----         -------------          --------
2008-R2-SP1                     WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-BYOL                WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                 WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-BYOL            WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter              WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-BYOL         WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                 WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core     WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-with-Containers WindowsServer MicrosoftWindowsServer EastUS  
2016-Nano-Server                WindowsServer MicrosoftWindowsServer EastUS
```

此信息可用于部署具有特定映像的 VM。 此示例为 VM 对象设置映像名称。 请参阅本教程前面的示例，了解完整的部署步骤。

```powershell
$vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter-with-Containers `
    -Version latest
```

## <a name="understand-vm-sizes"></a>了解 VM 大小

虚拟机大小决定虚拟机可用计算资源（如 CPU、GPU 和内存）的数量。 创建的虚拟机大小需适合预期的工作负荷。 如果工作负荷增加，可调整现有虚拟机的大小。

### <a name="vm-sizes"></a>VM 大小

下表将大小分类成了多个用例。  

| 类型                     | 大小           |    说明       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| 常规用途         |DSv2、Dv2、DS、D、Av2、A0-7| CPU 与内存之比均衡。 适用于开发/测试、小到中型应用程序和数据解决方案。  |
| 计算优化      | Fs, F             | 高 CPU 与内存之比。 适用于中等流量的应用程序、网络设备和批处理。        |
| 内存优化       | GS、G、DSv2、DS、Dv2、D   | 较高的内存核心比。 适用于关系数据库、中到大型缓存和内存中分析。                 |
| 存储优化       | LS                | 高磁盘吞吐量和 IO。 适用于大数据、SQL 和 NoSQL 数据库。                                                         |
| GPU           | NV, NC            | 专门针对大量图形绘制和视频编辑的 VM。       |
| 高性能 | H, A8-11          | 功能极其强大的 CPU VM 具有可选的高吞吐量网络接口 (RDMA)。 


### <a name="find-available-vm-sizes"></a>查找可用的 VM 大小

若要查看在特定区域可用的 VM 大小的列表，请使用 [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 命令。

```powershell
Get-AzureRmVMSize -Location EastUS
```

## <a name="resize-a-vm"></a>调整 VM 的大小

部署 VM 后，可调整其大小以增加或减少资源分配。

调整 VM 大小之前，请检查所需的大小在当前 VM 群集上是否可用。 使用 [Get AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 命令返回大小的列表。 

```powershell
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

如果所需大小可用，则可从开机状态调整 VM 大小，但需在此操作期间重启 VM。

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

如果所需大小在当前群集上不可用，则需解除分配 VM，然后才能执行调整大小操作。 请注意，重新启动 VM 时会删除临时磁盘上的任何数据，并且如果不使用静态 IP 地址，则公共 IP 地址会发生更改。 

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
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

```powershell
Get-AzureRmVM `
    -ResourceGroupName myResourceGroup `
    -Name myVM `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

输出：

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>管理任务

在虚拟机生命周期中，你可能需要运行管理任务，例如启动、停止或删除虚拟机。 此外，可能还需要创建脚本来自动执行重复或复杂的任务。 使用 Azure PowerShell，可从命令行或脚本运行许多常见的管理任务。

### <a name="stop-virtual-machine"></a>停止虚拟机

使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) 停止并解除分配虚拟机：

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

若要让虚拟机保持已预配状态，请使用 -StayProvisioned 参数。

### <a name="start-virtual-machine"></a>启动虚拟机

```powershell
Start-AzureRmVM -ResourceGroupName myResourceGroupVM -Name myVM
```

### <a name="delete-resource-group"></a>删除资源组

删除资源组会删除其包含的所有资源。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupVM -Force
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

