---
title: "关于 Windows 虚拟机的映像 | Microsoft Docs"
description: "了解如何对 Azure 中的 Windows 虚拟机使用映像。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: c118f9ebb8da496ad33b6e24e80d16ccf6557928


---
# <a name="about-images-for-windows-virtual-machines"></a>关于 Windows 虚拟机的映像
> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 有关在 Resource Manager 模型中查找和使用映像的信息，请参阅[此处](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>使用映像
可以使用 Azure PowerShell 模块管理 Azure 订阅可用的映像。 也可以使用 Azure 经典门户完成某些映像任务，但命令行可提供更多选项。

* **获取所有映像**：`Get-AzureVMImage`返回当前订阅中可用的所有映像的列表：你的映像以及 Azure 或合作伙伴提供的映像。 这意味着你可能会收到一个大列表。 下一个示例演示如何获取较短的列表。
* **获取映像系列**：`Get-AzureVMImage | select ImageFamily` 通过显示 **ImageFamily** 字符串属性获取映像系列的列表。
* **获取特定系列中的所有映像**：`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **查找 VM 映像**：`Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` 这通过筛选 DataDiskConfiguration 属性来完成，仅适用于 VM 映像。 此示例还仅根据标签和映像名称来筛选输出。
* **保存通用映像**：`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **保存专用映像**：`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
  >[Azure.Tip] 如果要创建包含数据磁盘和操作系统磁盘的 VM 映像，OSState 参数是必需的。 如果未使用此参数，该 cmdlet 将创建 OS 映像。 该参数的值根据操作系统磁盘是否已准备好重用来指示映像是通用的还是专用的。
* **删除映像**：`Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>后续步骤
还可以[使用经典门户创建 Windows 计算机](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)




<!--HONumber=Dec16_HO1-->


