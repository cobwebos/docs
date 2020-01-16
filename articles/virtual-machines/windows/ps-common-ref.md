---
title: 适用于 Azure 虚拟机的常见 PowerShell 命令
description: 帮助你开始在 Azure 中创建和管理 Windows VM 的常用 PowerShell 命令。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 1d66908d956f60ec894af50c45fd64387639addf
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981283"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>用于创建和管理 Azure 虚拟机的常用 PowerShell 命令

本文介绍一些可用于在 Azure 订阅中创建和管理虚拟机的 Azure PowerShell 命令。  有关特定命令行开关和选项的详细帮助，可以使用**get-help** *命令*。

 

如果运行本文中的多个命令，以下变量可能对你有用：

- $location - 虚拟机的位置。 可以使用 [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) 查找适合你的[地理区域](https://azure.microsoft.com/regions/)。
- $myResourceGroup - 包含虚拟机的资源组的名称。
- $myVM - 虚拟机的名称。

## <a name="create-a-vm---simplified"></a>创建 VM - 简化

| 任务 | 命令 |
| ---- | ------- |
| 创建简单的 VM | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM 有一组*简化的*参数，其中只有名称是必需的。 -Name 的值将用作创建新 VM 所需的所有资源的名称。 你可以指定更多，但只需要这个。|
| 从自定义映像创建 VM | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>需要已创建自己的[托管映像](capture-image-resource.md)。 可以使用一个映像生成多个相同的 VM。 |



## <a name="create-a-vm-configuration"></a>创建 VM 配置

| 任务 | 命令 |
| ---- | ------- |
| 创建 VM 配置 |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM 配置用于定义或更新 VM 的设置。 使用 VM 的名称及其[大小](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)对配置进行初始化。 |
| 添加配置设置 |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>向以前使用 New-AzVMConfig 创建的配置对象添加包括[凭据](https://technet.microsoft.com/library/hh849815.aspx)在内的操作系统设置。 |
| 添加网络接口 |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>VM 必须使用[网络接口](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)在虚拟网络中通信。 还可使用 [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) 检索现有网络接口对象。 |
| 指定平台映像 |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>向以前使用 New-AzVMConfig 创建的配置对象添加[映像信息](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 仅当将操作系统磁盘设置为使用平台映像时，才使用此命令返回的对象。 |
| 创建 VM |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>所有资源在[资源组](../../azure-resource-manager/management/manage-resource-groups-powershell.md)中创建。 运行此命令之前，请运行 New-AzVMConfig、Set-AzVMOperatingSystem、Set-AzVMSourceImage、Add-AzVMNetworkInterface 和 Set-AzVMOSDisk。 |
| 更新 VM |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>使用 Get-AzVM 获取当前 VM 配置，更改 VM 对象上的配置设置，并运行此命令。 |

## <a name="get-information-about-vms"></a>获取有关 VM 的信息

| 任务 | 命令 |
| ---- | ------- |
| 列出订阅中的 VM |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| 列出资源组中的 VM |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>若要获取订阅中的资源组列表，请使用 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup)。 |
| 获取有关 VM 的信息 |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>管理 VM
| 任务 | 命令 |
| --- | --- |
| 启动 VM |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 停止 VM |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 重启正在运行的 VM |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 删除 VM |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>后续步骤
* 请参阅[使用 Resource Manager 和 PowerShell 创建 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 中有关创建虚拟机的基本步骤。

