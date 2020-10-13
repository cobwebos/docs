---
title: 在 Azure 实验室服务中使用共享映像库 | Microsoft Docs
description: 了解如何将实验室帐户配置为使用共享映像库，以便用户可以与其他用户共享映像，其他用户可以使用该映像在实验室中创建模板 VM。
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 04e3764b095706d091bb72baaae77f5a4016fd28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052828"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 实验室服务中使用共享映像库
本文介绍教师/实验室管理员如何将模板虚拟机映像保存到[共享映像库](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)，以便其他人可以使用它来创建实验室。 

> [!IMPORTANT]
> 使用共享映像库时，Azure 实验室服务仅支持少于 128 GB 操作系统磁盘空间的映像。 在创建实验室的过程中，将不会在虚拟机映像列表中显示超过 128 GB 磁盘空间或多个磁盘的映像。

## <a name="scenarios"></a>方案
此功能支持以下两个场景： 

- 实验室帐户管理员将共享映像库附加到实验室帐户，并将映像上传到实验室上下文外部的共享映像库。 然后，实验室创建者可以使用共享映像库中的映像创建实验室。 
- 实验室帐户管理员将共享映像库附加到实验室帐户。 实验室创建者（讲师）将其实验室的自定义映像保存到共享映像库。 然后，其他实验室创建者可以从共享映像库中选择此映像，为其实验室创建模板。 

    将映像保存到共享映像库时，Azure 实验室服务会将已保存的映像复制到同一[地域](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他区域。 它确保映像可用于在同一地域中的其他区域中创建的实验室。 将映像保存到共享映像库会产生额外费用，其中包括所有复制映像的成本。 此成本不同于 Azure 实验室服务的使用成本。 有关共享映像库定价的详细信息，请参阅[共享映像库 – 计费]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。
    
## <a name="prerequisites"></a>先决条件
- 使用 [Azure PowerShell](../virtual-machines/windows/shared-images.md) 或 [Azure CLI](../virtual-machines/linux/shared-images.md) 创建共享映像库。
- 你已将共享映像库附加到实验室帐户。 有关分步说明，请参阅[如何附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>将映像保存到共享映像库
附加共享映像库后，实验室帐户管理员或教师可以将映像保存到共享映像库，以便其他教师可以重复使用。 

1. 在实验室的“模板”页上，选择工具栏上的“导出到共享映像库”。

    ![“保存映像”按钮](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 在“导出到共享映像库”对话框中，输入“映像名称”，然后选择“导出”。 

    ![“导出到共享映像库”对话框](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. 可以在“模板”页上查看此操作的进度。 此操作可能需要一段时间。 

    ![正在进行导出](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 如果导出操作成功，则会看到以下消息：

    ![导出已完成](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    将映像保存到共享映像库后，可以在创建其他实验室时从库中使用该映像。 还可以将映像上传到实验室上下文之外的共享映像库。 有关详细信息，请参阅[共享映像库概述](../virtual-machines/windows/shared-images.md)。 

    > [!IMPORTANT]
    > 将 Azure 实验室服务中的 [实验室模板映像保存](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) 到共享映像库时，会将该映像作为 **专用映像**上载到库。 [专用映像](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) 保留计算机特定的信息和用户配置文件。 你仍可以直接将通用化映像上传到 Azure 实验室服务之外的库。    

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共享映像库中的映像
在创建新的实验室时，教师可以在该模板的共享映像库中选择可用的自定义映像。

![使用库中的虚拟机映像](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> 可以基于 Azure 实验室服务中的 **通用** 映像和 **专用** 映像创建模板 VM。 


## <a name="next-steps"></a>后续步骤
有关共享映像库的详细信息，请参阅[共享映像库](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)。
