---
title: 在 Azure 实验室服务中使用共享映像库 |Microsoft Docs
description: 了解如何将实验室帐户配置为使用共享映像库，以便用户可以与其他用户共享图像，其他用户可以使用该映像在实验室中创建模板 VM。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: 294a270107033590e340db49b85e8a67acbbb701
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116860"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 实验室服务中使用共享映像库
本文介绍了教师/实验室管理员如何将模板虚拟机映像保存到[共享映像库](../../virtual-machines/windows/shared-image-galleries.md)，以便其他人可以使用它创建实验室。 

## <a name="scenarios"></a>方案
此功能支持以下两种方案： 

- 实验室帐户管理员将共享映像库附加到实验室帐户，并将图像上传到实验室上下文外部的共享映像库。 然后，实验室创建者可以使用共享映像库中的映像创建实验室。 
- 实验室帐户管理员将共享的映像库附加到实验室帐户。 实验室创建者（讲师）将其实验室的自定义映像保存到共享映像库。 然后，其他实验室创建者可以从共享图像库中选择此映像，为其实验室创建模板。 

    将映像保存到共享映像库时，Azure 实验室服务会将已保存的映像复制到相同[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他区域。 它确保映像可用于在同一地理区域中的其他区域创建的实验室。 将映像保存到共享映像库会产生额外的费用，其中包括所有复制映像的成本。 此成本不同于 Azure 实验室服务的使用成本。 有关共享映像库定价的详细信息，请参阅[共享图像库–计费]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。

    > [!NOTE]
    > Azure 实验室服务支持基于共享映像库中的**通用**映像和**专用**映像创建模板 vm。 


## <a name="prerequisites"></a>必备条件
- 使用[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或[Azure CLI](../../virtual-machines/linux/shared-images.md)创建共享映像库。
- 已将共享映像库附加到实验室帐户。 有关分步说明，请参阅[如何附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>将图像保存到共享映像库
附加共享映像库后，实验室帐户管理员或教师可以将图像保存到共享映像库，以便其他教师可以重复使用。 

1. 在实验室的 "**模板**" 页上，选择工具栏上的 "**导出到共享图像库**"。

    ![保存图像按钮](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 在 "**导出到共享图像库**" 对话框中，输入**图像的名称**，然后选择 "**导出**"。 

    !["导出到共享图像库" 对话框](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. 可以在 "**模板**" 页上查看此操作的进度。 此操作可能需要一段时间。 

    ![正在导出](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 如果导出操作成功，则会看到以下消息：

    ![导出已完成](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    > [!NOTE]
    > 将图像保存到共享图像库后，可以在创建其他实验室时从库中使用该图像。 
    > 
    > 您还可以将图像上传到实验室上下文之外的共享映像库。 有关详细信息，请参阅[共享映像库概述](../../virtual-machines/windows/shared-images.md)。 

    

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共享映像库中的映像
在创建新的实验室期间，教师/教授可以在模板的共享映像库中选择可用的自定义映像。

![使用库中的虚拟机映像](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>后续步骤
有关共享映像库的详细信息，请参阅[共享映像库](../../virtual-machines/windows/shared-image-galleries.md)。
