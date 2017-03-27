---
title: "使用 PowerShell 将 VHD 文件上载到 Azure 开发测试实验室 | Microsoft 文档"
description: "使用 PowerShell 将 VHD 文件上载到实验室的存储帐户"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: cc14f7747c4a24acea434f62b7615d10819bd619
ms.openlocfilehash: 379544e6592e933e510308bb0ae7193542c099f2


---

# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>使用 PowerShell 将 VHD 文件上载到实验室的存储帐户

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

在 Azure 开发测试实验室中，可以使用 VHD 文件创建用于预配虚拟机的自定义映像。 以下步骤将引导你完成使用 PowerShell 将 VHD 文件上载到实验室的存储帐户。 上载 VHD 文件后，[后续步骤部分](#next-steps)将列出一些文章说明如何基于已上载的 VHD 文件创建自定义映像。 有关 Azure 中的磁盘和 VHD 的详细信息，请参阅 [About disks and VHDs for virtual machines](../storage/storage-about-disks-and-vhds-linux.md)（关于虚拟机的磁盘和 VHD）

## <a name="step-by-step-instructions"></a>分步说明

以下步骤将引导你完成使用 PowerShell 将 VHD 文件上载到 Azure 开发测试实验室。 

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择“更多服务”，然后从列表中选择“开发测试实验室”。

1. 从实验室列表中，选择所需的实验室。  

1. 在实验室的边栏选项卡，选择“配置”。 

1. 在实验室的“配置”边栏选项卡上，选择“自定义映像(VHD)”。

1. 在“自定义映像”边栏选项卡上，选择“+添加”。 

1. 在“自定义映像”边栏选项卡上，选择“VHD”。

1. 在“VHD”边栏选项卡上，选择“使用 PowerShell 上传 VHD”。

    ![使用 PowerShell 上载 VHD](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. 在“使用 PowerShell 上载映像”边栏选项卡上，将生成的 PowerShell 脚本复制到文本编辑器。

1. 修改 **Add-AzureVhd** cmdlet 的 **LocalFilePath** 参数，使其指向要上载的 VHD 文件的位置。

1. 在 PowerShell 提示符处，运行 **Add-AzureVhd** cmdlet（使用已修改的 **LocalFilePath** 参数）。

> [!WARNING] 
> 
> 上载 VHD 文件的过程可能耗时较长，具体取决于 VHD 文件的大小和连接速度。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户，在 Azure 开发测试实验室中基于 VHD 文件创建自定义映像](devtest-lab-create-template.md)
- [通过 PowerShell，在 Azure 开发测试实验室中基于 VHD 文件创建自定义映像](devtest-lab-create-custom-image-from-vhd-using-powershell.md)



<!--HONumber=Feb17_HO3-->


