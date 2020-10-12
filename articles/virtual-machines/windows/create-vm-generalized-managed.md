---
title: 在 Azure 中从托管映像创建 VM
description: 使用 Azure PowerShell 或门户从通用托管映像创建 Windows 虚拟机。
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 2be48025bcf2be8cec63e2a251f034e72c803e88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289654"
---
# <a name="create-a-vm-from-a-managed-image"></a>从托管映像创建 VM

可以使用 Azure 门户或 PowerShell 基于 Azure 托管 VM 映像创建多台虚拟机 (VM)。 托管 VM 映像包含创建 VM 所需的信息，包括 OS 和数据磁盘。 构成映像的虚拟硬盘 (VHD)（包括 OS 磁盘和任何数据磁盘）均作为托管磁盘存储。 

在创建新的 VM 之前，需要[创建托管 VM 映像](capture-image-resource.md)以用作源映像，并向应有权访问该映像的任何用户授予对该映像的读取权限。 

一个托管映像最多支持 20 个同时部署。 如果尝试从同一托管映像同时创建超过 20 个 VM，则可能会由于单个 VHD 的存储性能限制而导致预配超时。 若要同时创建 20 个以上的 VM，请使用为每 20 个并发 VM 部署配置 1 个副本的[共享映像库](shared-image-galleries.md)映像。

## <a name="use-the-portal"></a>使用门户

1. 转到 [Azure 门户](https://portal.azure.com)查找托管映像。 搜索并选择“映像”。
3. 从列表中选择需要使用的映像。 随即打开映像“概述”页。
4. 从菜单中选择“创建 VM”。
5. 输入虚拟机信息。 在此处输入的用户名和密码将用来登录到虚拟机。 完成后选择“确定”。 可以在现有资源组中创建新的 VM，或者选择“新建”创建一个新的资源组用于存储 VM。
6. 为 VM 选择大小。 若要查看更多的大小，请选择“全部查看”或更改“支持的磁盘类型”筛选器。 
7. 在“设置”下，根据需要进行更改并选择“确定” 。 
8. 在摘要页上，应该可以看见你的映像名称作为“专用映像”列出。 选择“确定”以启动虚拟机部署。


## <a name="use-powershell"></a>使用 PowerShell

可以使用 PowerShell 通过使用 [New-AzVm](/powershell/module/az.compute/new-azvm) cmdlet 的简化参数集来基于映像创建 VM。 该映像需要位于你要在其中创建 VM 的同一资源组中。

 

[New-AzVm](/powershell/module/az.compute/new-azvm) 的简化参数集仅要求提供名称、资源组和映像名称便可基于映像创建 VM。 New-AzVm 将使用 -Name 参数的值作为它自动创建的所有资源的名称。 在此示例中，我们为每个资源提供更详细的名称，但让 cmdlet 自动创建这些资源。 你还可以提前创建资源（例如虚拟网络）并将名称传递给 cmdlet。 如果 New-AzVm 可以通过名称找到现有资源，就会使用现有资源。

以下示例在“myResourceGroup”资源组中从名为“myImage”的映像创建名为“myVMFromImage”的 VM。 


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



## <a name="next-steps"></a>后续步骤
[使用 Azure PowerShell 模块创建和管理 Windows VM](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
