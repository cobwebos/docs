---
title: 使用 PowerShell 将 VHD 文件上传到 Azure 开发测试实验室 | Microsoft 文档
description: 本文介绍如何使用 PowerShell 将 VHD 文件上传到 Azure 开发测试实验室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2b393b886a50f60a918690ee2a5583f9623dbe39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650751"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>使用 PowerShell 将 VHD 文件上传到实验室的存储帐户

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

在 Azure 开发测试实验室中，可使用 VHD 文件创建用于预配虚拟机的自定义映像。 以下步骤将引导完成使用 PowerShell 将 VHD 文件上传到实验室的存储帐户。 上传 VHD 文件后，[后续步骤部分](#next-steps)将列出一些文章说明如何基于已上传的 VHD 文件创建自定义映像。 有关 Azure 中的磁盘和 VHD 的详细信息，请参阅[托管磁盘简介](../virtual-machines/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>分步说明

以下步骤将引导完成使用 PowerShell 将 VHD 文件上传到 Azure 开发测试实验室。 

1. 登录 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 选择 " **所有服务**"，然后从列表中选择 " **开发测试实验室** "。

1. 从实验室列表，选择所需的实验室。  

1. 在实验室的边栏选项卡上，选择“配置”****。 

1. 在实验室的“配置”**** 边栏选项卡上，选择“自定义映像(VHD)”****。

1. 在“自定义映像”**** 边栏选项卡上，选择“+添加”****。 

1. 在“自定义映像”**** 边栏选项卡上，选择“VHD”****。

1. 在“VHD”**** 边栏选项卡上，选择“使用 PowerShell 上传 VHD”****。

    ![使用 PowerShell 上传 VHD](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. 在“使用 PowerShell 上传映像”**** 边栏选项卡上，将生成的 PowerShell 脚本复制到文本编辑器。

1. 修改 **Add-AzureVhd** cmdlet 的 **LocalFilePath** 参数，使其指向要上载的 VHD 文件的位置。

1. 在 PowerShell 提示符处，运行 **Add-AzureVhd** cmdlet（使用已修改的 **LocalFilePath** 参数）。

> [!WARNING] 
> 
> 上传 VHD 文件的过程可能耗时较长，具体取决于 VHD 文件的大小和连接速度。

## <a name="next-steps"></a>后续步骤

- [通过 Azure 门户，在 Azure 开发测试实验室中基于 VHD 文件创建自定义映像](devtest-lab-create-template.md)
- [通过 PowerShell，在 Azure 开发测试实验室中基于 VHD 文件创建自定义映像](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
