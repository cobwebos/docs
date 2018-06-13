---
title: 在 Azure 中从托管映像创建 VM | Microsoft Docs
description: 在资源管理器部署模型中，使用 Azure PowerShell 或 Azure 门户从通用托管映像创建 Windows 虚拟机。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
ms.locfileid: "30239049"
---
# <a name="create-a-vm-from-a-managed-image"></a>从托管的映像创建 VM

可以使用 PowerShell 或 Azure 门户从托管 VM 映像创建多个 VM。 托管 VM 映像包含创建 VM（包括 OS 和数据磁盘）所需的信息。 构成映像的 VHD（包括 OS 磁盘和任何数据磁盘）均作为托管磁盘存储。 

必须已[创建托管 VM 映像](capture-image-resource.md)以用于创建新 VM。 

## <a name="use-the-portal"></a>使用门户

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有资源”。 可以按“类型”对资源进行排序以方便查找映像。
3. 从列表中选择需要使用的映像。 随即打开映像“概述”页。
4. 从菜单中单击“+ 创建 VM”。
5. 输入虚拟机信息。 在此处输入的用户名和密码用于登录到虚拟机。 完成后，单击“确定”。 可以在现有资源组中创建新的 VM，或者选择“新建”创建一个新的资源组用于存储 VM。
6. 为 VM 选择大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 
7. 在“设置”下，按需做出更改并单击“确定”。 
8. 在摘要页上，应该可以看见为“专用映像”列出的映像名称。 单击“确定”启动虚拟机部署。


## <a name="use-powershell"></a>使用 PowerShell

可以使用 PowerShell 通过 [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 的简化参数集从映像创建 VM。 该映像需要位于要在其中创建 VM 的同一资源组。

此示例需要 5.6.0 版本或更高版本的 AzureRM 模块。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

New-AzureRmVm 的简化参数集只需要你提供名称、资源组和映像名称就能从映像创建 VM，但是它会将 -Name 参数的值作为所有自动创建的资源的名称。 在此示例中，我们为每个资源提供更详细的名称，但让 cmdlet 自动创建这些资源。 你还可以提前创建资源（例如虚拟网络）并将名称输进 cmdlet。 如果 cmdlet 可以通过名称找到资源，就会使用现有资源。

以下示例在“myResourceGroup”资源组中从名为“myImage”的映像创建名为“myVMFromImage”的 VM。 


```azurepowershell-interactive
New-AzureRmVm `
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



## <a name="next-steps"></a>后续步骤
若要使用 Azure PowerShell 管理新虚拟机，请参阅[使用 Azure PowerShell 模块创建和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

