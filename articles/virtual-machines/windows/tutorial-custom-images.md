---
title: 教程 - 使用 Azure PowerShell 创建自定义 VM 映像
description: 本教程介绍如何使用 Azure PowerShell 在 Azure 中创建自定义虚拟机映像
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aca1f8ce2c125cd90f2e61a7829be09bc81c3938
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464961"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>教程：使用 Azure PowerShell 创建 Azure VM 的自定义映像

自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动部署并确保多个 VM 的一致性。 在本教程中，需使用 PowerShell 创建自己的 Azure 虚拟机自定义映像。 学习如何：

> [!div class="checklist"]
> * 使用 Sysprep 通用化 VM
> * 创建自定义映像
> * 从自定义映像创建 VM
> * 列出订阅中的所有映像
> * 删除映像

在公共预览版中，我们提供了 [Azure VM 映像生成器](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview)服务。 只需在模板中描述自定义设置，它将处理本文中的映像创建步骤。 [试用 Azure 映像生成器（预览版）](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder)。

## <a name="before-you-begin"></a>开始之前

下列步骤详细说明如何将现有 VM 转换为可重用自定义映像，以便用于创建新 VM 实例。

若要完成本教程中的示例，必须具备现有虚拟机。 必要时，此[脚本示例](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)可为你创建一个。 通过教程操作时，根据需要替换资源组和 VM 名称。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。 

## <a name="prepare-vm"></a>准备 VM

若要创建虚拟机的映像，需通过以下方式准备源 VM：通用化该 VM、解除分配，然后在 Azure 中将其标记为通用化。

### <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 通用化 Windows VM

Sysprep 将删除所有个人帐户信息及其他某些数据，并准备好要用作映像的计算机。 有关 Sysprep 的详细信息，请参阅[如何使用 Sysprep：简介](https://technet.microsoft.com/library/bb457073.aspx)。


1. 连接到虚拟机。
2. 以管理员身份打开“命令提示符”窗口。 将目录切换到 *%windir%\system32\sysprep*，然后运行 `sysprep.exe`。
3. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”复选框。   
4. 在“关机选项”  中选择“关机”  ，并单击“确定”  。
5. Sysprep 在完成运行后会关闭虚拟机。 请勿重启 VM  。

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>解除分配并将 VM 标记为通用化

要创建映像，需解除分配 VM，并在 Azure 中将其标记为通用化。

使用 [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) 对 VM 解除分配。

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Force
```

使用 [Set-AzVm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm) 将虚拟机的状态设置为 `-Generalized`。 
   
```azurepowershell-interactive
Set-AzVM `
   -ResourceGroupName myResourceGroup `
   -Name myVM -Generalized
```


## <a name="create-the-image"></a>创建映像

现在，可以使用 [New-AzImageConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azimageconfig) 和 [New-AzImage](https://docs.microsoft.com/powershell/module/az.compute/new-azimage) 来创建 VM 的映像。 以下示例从名为“myVM”  的 VM 创建名为“myImage”  的映像。

获取虚拟机。 

```azurepowershell-interactive
$vm = Get-AzVM `
   -Name myVM `
   -ResourceGroupName myResourceGroup
```

创建映像配置。

```azurepowershell-interactive
$image = New-AzImageConfig `
   -Location EastUS `
   -SourceVirtualMachineId $vm.ID 
```

创建映像。

```azurepowershell-interactive
New-AzImage `
   -Image $image `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>从映像创建 VM

在已有映像之后，可以从该映像创建一个或多个新 VM。 从自定义映像创建 VM 与使用市场映像创建 VM 很相似。 如果使用市场映像，需提供有关映像、映像提供程序、产品/服务、SKU 和版本的信息。 使用为 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet 设置的简化参数时，如果自定义映像位于同一资源组中，则只需提供该映像的名称。 

本示例从“myResourceGroup”  中的“myImage”  映像创建名为“myVMfromImage”  的 VM。


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

我们建议你将单个映像的并发部署数量限制为 20 个 VM。 如果计划从同一自定义映像中同时大规模部署 20 多个 VM，则应将[共享映像库](shared-image-galleries.md)与多个映像副本一起使用。 


## <a name="image-management"></a>映像管理 

下面是一些常见的托管映像任务示例，并说明了如何使用 PowerShell 完成这些任务。

按名称列出所有映像。

```azurepowershell-interactive
$images = Get-AzResource -ResourceType Microsoft.Compute/images 
$images.name
```

删除映像。 此示例将从 *myResourceGroup* 中删除名为 *myImage* 的映像。

```azurepowershell-interactive
Remove-AzImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，已创建自定义 VM 映像。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Sysprep 通用化 VM
> * 创建自定义映像
> * 从自定义映像创建 VM
> * 列出订阅中的所有映像
> * 删除映像

请转到下一教程，了解如何创建高度可用的虚拟机。

> [!div class="nextstepaction"]
> [创建高度可用的 VM](tutorial-availability-sets.md)



