---
title: "从 Windows VM 中分离数据磁盘 - Azure | Microsoft Docs"
description: "了解如何从使用 Resource Manager 部署模型的 Azure 中的虚拟机分离磁盘。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 7013e7ff3cb14dcad8e3e9a926bcee771180259d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>如何从 Windows 虚拟机分离数据磁盘
不再需要附加到虚拟机的数据磁盘时，可以轻松地分离它。 这会从虚拟机中删除磁盘，但不会从存储中删除它。

> [!WARNING]
> 如果分离磁盘，它将不会自动删除。 如果订阅了高级存储，则将继续承担该磁盘的存储费用。 有关详细信息，请参阅[使用高级存储时的定价和计费方式](premium-storage.md#pricing-and-billing)。
>
>

如果希望再次使用磁盘上的现有数据，可以将其重新附加到相同的虚拟机或另一个虚拟机。

## <a name="detach-a-data-disk-using-the-portal"></a>使用门户分离数据磁盘

1. 在左侧菜单中，选择“虚拟机”。
2. 选择具有要分离的数据磁盘的虚拟机，并单击“停止”以解除分配 VM。
3. 在虚拟机窗格中，选择“磁盘”。
4. 在“磁盘”窗格的顶部，选择“编辑”。
5. 在“磁盘”窗格中，转到要分离的数据磁盘最右侧，并单击![分离按钮图像](./media/detach-disk/detach.png)分离按钮。
5. 删除磁盘后，单击窗格顶部的“保存”。
6. 在虚拟机窗格中，单击“概述”，并单击窗格顶部的“开始”按钮重启 VM。



磁盘保留在存储中，但不再附加到虚拟机。

## <a name="detach-a-data-disk-using-powershell"></a>使用 PowerShell 分离数据磁盘
在此示例中，第一个命令将使用 [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) 获取 RG11 资源组中名为 MyVM07 的虚拟机，并将其存储在 $VirtualMachine 变量中。

第二行将使用 [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk) cmdlet 从虚拟机中删除名为 DataDisk3 的数据磁盘。

第三行将使用 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet 更新虚拟机的状态以完成数据磁盘删除过程。

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

有关详细信息，请参阅 [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk)。

## <a name="next-steps"></a>后续步骤
如果想要重新使用数据磁盘，只需将它[附加到另一个 VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

