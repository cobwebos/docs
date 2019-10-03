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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 65cc2d9ac2b96822f2c1b740f3180ba1d9eaf98c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389974"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 实验室服务中使用共享映像库
本文介绍了教师/实验室管理员如何保存模板虚拟机映像，使其可供其他人重复使用。 这些映像保存在 Azure[共享映像库](../../virtual-machines/windows/shared-image-galleries.md)中。 作为第一步，实验室管理员将现有的共享映像库附加到实验室帐户。 附加共享映像库后，在实验室帐户中创建的实验室可以将映像保存到共享映像库。 其他教师可以从共享图像库中选择此映像，以创建其类的模板。 

## <a name="prerequisites"></a>先决条件
- 使用[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或[Azure CLI](../../virtual-machines/linux/shared-images.md)创建共享映像库。
- 已将共享映像库附加到实验室帐户。 有关分步说明，请参阅[如何附加或分离共享映像库](how-to-attach-detach-shared-image-gallery.md)。


## <a name="save-an-image-to-the-shared-image-gallery"></a>将图像保存到共享映像库
附加共享映像库后，实验室帐户管理员或教师可以将图像保存到共享映像库，以便其他教师可以重复使用。 

1. 在实验室的主页上，选择 "**模板**" 部分中的磁贴上的 "**保存图像**"。

    ![保存图像按钮](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  在 "**保存虚拟机映像**" 窗口中，输入映像的名称，然后选择 "**保存**"。 

    !["保存虚拟机映像" 窗口](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. 检查 "实验室" 磁贴上的状态。 

    ![保存映像操作的状态](../media/how-to-use-shared-image-gallery/save-image-status.png)
4. 确认操作是否成功。

    ![已成功保存图像操作](../media/how-to-use-shared-image-gallery/save-image-successful.png)

您还可以将图像上传到实验室上下文之外的共享映像库。 有关详细信息，请参阅[共享映像库概述](../../virtual-machines/windows/shared-images.md)。 

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共享映像库中的映像
在创建新的实验室期间，教师/教授可以在模板的共享映像库中选择可用的自定义映像。

![使用库中的虚拟机映像](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>后续步骤
有关共享映像库的详细信息，请参阅[共享映像库](../../virtual-machines/windows/shared-image-galleries.md)。
