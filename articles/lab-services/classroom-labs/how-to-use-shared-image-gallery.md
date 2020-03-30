---
title: 在 Azure 实验室服务中使用共享映像库 |微软文档
description: 了解如何将实验室帐户配置为使用共享图像库，以便用户可以与其他用户共享映像，而另一个用户可以使用该映像在实验室中创建模板 VM。
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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190443"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 实验室服务中使用共享映像库
本文介绍教师/实验室管理员如何保存模板虚拟机映像以供他人重用。 这些映像保存在 Azure[共享映像库中](../../virtual-machines/windows/shared-image-galleries.md)。 作为第一步，实验室管理员将现有的共享映像库附加到实验室帐户。 附加共享图像库后，在实验室帐户中创建的实验室可以将图像保存到共享图像库。 其他教师可以从共享图像库中选择此图像，为其班级创建模板。 

> [!NOTE]
> 目前，Azure 实验室服务仅支持基于共享映像库中**的通用**VM 映像（非专用映像）创建模板 VM。 

## <a name="prerequisites"></a>先决条件
- 使用[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或[Azure CLI](../../virtual-machines/linux/shared-images.md)创建共享映像库。
- 您已将共享图像库附加到实验室帐户。 有关分步说明，请参阅[如何附加或分离共享图像库](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>将图像保存到共享图像库
附加共享图像库后，实验室帐户管理员或教师可以将图像保存到共享图像库，以便其他教师可以重复使用该图像。 

1. 在实验室的 **"模板"** 页上，选择工具栏上的 **"导出到共享图像库**"。

    ![保存图像按钮](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. 在 **"导出到共享图像库**"对话框中，输入**图像的名称**，然后选择 **"导出**"。 

    ![导出到共享图像库对话框](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. 您可以在 **"模板"** 页上查看此操作的进度。 此操作可能需要一些时间。 

    ![正在出口](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. 导出操作成功后，您将看到以下消息：

    ![出口已完成](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

您还可以将图像上载到实验室上下文外的共享图像库。 有关详细信息，请参阅[共享图像库概述](../../virtual-machines/windows/shared-images.md)。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共享图像库中的图像
教师/教授可以在新实验室创建期间为模板选择共享图像库中可用的自定义图像。

![使用库中的虚拟机映像](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>后续步骤
有关共享图像库的详细信息，请参阅[共享图像库](../../virtual-machines/windows/shared-image-galleries.md)。
