---
title: "使用 Azure PowerShell 创建自定义 VM 映像 | Microsoft Docs"
description: "教程 - 使用 Azure PowerShell 创建自定义 VM 映像。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 773b37ec8f775d68f1faca0d252f3064c7de0317
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>使用 PowerShell 创建 Azure VM 的自定义映像

自定义映像类似于应用商店映像，不同的是自定义映像的创建者是你自己。 自定义映像可用于启动配置，例如预加载应用程序、应用程序配置和其他 OS 配置。 在本教程中，将创建自己的 Azure 虚拟机自定义映像。 你将学习如何：

> [!div class="checklist"]
> * 使用 Sysprep 通用化 VM
> * 创建自定义映像
> * 从自定义映像创建 VM
> * 列出订阅中的所有映像
> * 删除映像

本教程需要 Azure PowerShell 模块 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="before-you-begin"></a>开始之前

下列步骤详细说明如何将现有 VM 转换为可重用自定义映像，以便用于创建新 VM 实例。

若要完成本教程中的示例，必须具备现有虚拟机。 如果需要，此[脚本示例](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)可替你创建一个虚拟机。 通过教程操作时，根据需要替换资源组和 VM 名称。

## <a name="prepare-vm"></a>准备 VM

若要创建虚拟机的映像，需通过以下方式准备 VM：通用化 VM、解除分配，然后在 Azure 中将源 VM 标记为通用化。

### <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 通用化 Windows VM

Sysprep 将删除所有个人帐户信息及其他某些数据，并准备好要用作映像的计算机。 有关 Sysprep 的详细信息，请参阅[如何使用 Sysprep：简介](http://technet.microsoft.com/library/bb457073.aspx)。


1. 连接到虚拟机。
2. 以管理员身份打开“命令提示符”窗口。 将目录切换到 %windir%\system32\sysprep，然后运行 sysprep.exe。
3. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。
4. 在“关机选项”中选择“关机”，然后单击“确定”。
5. Sysprep 在完成运行后会关闭虚拟机。 请勿重启 VM。

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>解除分配并将 VM 标记为通用化

要创建映像，需解除分配 VM，并在 Azure 中将其标记为通用化。

使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) 对 VM 解除分配。

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Force
```

使用 [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) 将虚拟机的状态设置为 `-Generalized`。 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Generalized
```


## <a name="create-the-image"></a>创建映像

现在，可以使用 [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) 和 [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) 来创建 VM 的映像。 以下示例从名为“myVM”的 VM 创建名为“myImage”的映像。

获取虚拟机。 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroupImages
```

创建映像配置。

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

创建映像。

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroupImages
```    

 
## <a name="create-vms-from-the-image"></a>从映像创建 VM

在已有映像之后，可以从该映像创建一个或多个新 VM。 从自定义映像创建 VM 与使用应用商店映像创建 VM 非常相似。 如果使用应用商店映像，需提供有关映像、映像提供程序、产品/服务、SKU 和版本的信息。 如果使用自定义映像，则仅需提供自定义映像资源的 ID。 

在下列脚本中，我们创建了一个 $image 变量，用于存储使用 [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) 的自定义映像的信息，然后使用 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage)，并通过刚创建的 $image 变量指定 ID。 

脚本从自定义映像中创建名为“myVMfromImage”的 VM，该自定义映像位于“美国西部”位置名为“myResourceGroupFromImage”的新资源组内。


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroupImages

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>映像管理 

下面是一些常见的管理映像任务示例，并说明如何使用 PowerShell 完成这些任务。

按名称列出所有映像。

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

删除映像。 此示例将从 *myResourceGroup* 中删除名为 *myOldImage* 的映像。

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，已创建自定义 VM 映像。 你已了解如何：

> [!div class="checklist"]
> * 使用 Sysprep 通用化 VM
> * 创建自定义映像
> * 从自定义映像创建 VM
> * 列出订阅中的所有映像
> * 删除映像

请转到下一教程，了解如何创建高度可用的虚拟机。

> [!div class="nextstepaction"]
> [创建高度可用的 VM](tutorial-availability-sets.md)




