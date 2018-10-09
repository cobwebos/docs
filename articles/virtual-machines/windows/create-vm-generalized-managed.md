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
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 8acbb33b396aa617936eb0333bd68fea60532425
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404650"
---
# <a name="create-a-vm-from-a-managed-image"></a>从托管的映像创建 VM

可以使用 Azure 门户或 PowerShell 基于 Azure 托管 VM 映像创建多台虚拟机 (VM)。 托管 VM 映像包含创建 VM（包括 OS 和数据磁盘）所需的信息。 构成映像的虚拟硬盘 (VHD)（包括 OS 磁盘和任何数据磁盘）均作为托管磁盘存储。 

在创建新的 VM 之前，需要[创建托管 VM 映像](capture-image-resource.md)来用作源映像。 

## <a name="use-the-portal"></a>使用门户

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单上，选择“所有资源”。 可以按“类型”对资源进行排序以方便查找映像。
3. 从列表中选择需要使用的映像。 随即打开映像“概述”页。
4. 从菜单中选择“创建 VM”。
5. 输入虚拟机信息。 在此处输入的用户名和密码将用来登录到虚拟机。 完成后选择“确定”。 可以在现有资源组中创建新的 VM，或者选择“新建”创建一个新的资源组用于存储 VM。
6. 为 VM 选择大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 
7. 在“设置”下，根据需要进行更改并选择“确定”。 
8. 在摘要页上，应该可以看见你的映像名称作为“专用映像”列出。 选择“确定”以启动虚拟机部署。


## <a name="use-powershell"></a>使用 PowerShell

可以使用 PowerShell 通过使用 [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) cmdlet 的简化参数集来基于映像创建 VM。 该映像需要位于你要在其中创建 VM 的同一资源组中。

此示例需要 5.6.0 版本或更高版本的 AzureRM 模块。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

[New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) 的简化参数集仅要求提供名称、资源组和映像名称便可基于映像创建 VM。 New-AzureRmVm 将使用 **-Name** 参数的值作为它自动创建的所有资源的名称。 在此示例中，我们为每个资源提供更详细的名称，但让 cmdlet 自动创建这些资源。 你还可以提前创建资源（例如虚拟网络）并将名称传递给 cmdlet。 如果 New-AzureRmVm 可以通过名称找到资源，则它将使用现有资源。

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
[使用 Azure PowerShell 模块创建和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

