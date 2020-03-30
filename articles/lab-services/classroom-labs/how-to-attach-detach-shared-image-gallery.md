---
title: 在 Azure 实验室服务中连接或分离共享映像库 |微软文档
description: 本文介绍如何将共享映像库附加到 Azure 实验室服务中的教室实验室。
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
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284312"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 实验室服务中连接或分离共享映像库
教师/实验室管理员可以将模板 VM 映像保存在 Azure[共享映像库中](../../virtual-machines/windows/shared-image-galleries.md)，以供其他人重复使用。 作为第一步，实验室管理员将现有的共享映像库附加到实验室帐户。 附加共享图像库后，在实验室帐户中创建的实验室可以将图像保存到共享图像库。 其他教师可以从共享图像库中选择此图像，为其班级创建模板。 

将映像保存到共享映像库时，Azure 实验室服务会将保存的图像复制到同一[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他区域。 它可确保图像可用于在同一地理位置的其他地区创建的实验室。 将图像保存到共享图像库会产生额外的成本，其中包括所有复制图像的成本。 此成本与 Azure 实验室服务使用成本分开。 有关共享图像库定价的详细信息，请参阅[共享图像库和计费]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。

本文介绍如何将共享图像库附加到实验室帐户。 

> [!NOTE]
> 目前，Azure 实验室服务仅支持基于共享映像库中**的通用**VM 映像（非专用映像）创建模板 VM。 


## <a name="configure-at-the-time-of-lab-account-creation"></a>在创建实验室帐户时进行配置
创建实验室帐户时，可以将共享图像库附加到实验室帐户。 您可以从下拉列表中选择现有的共享图像库，也可以创建新的共享图像库。 要创建共享图像库并将其附加到实验室帐户，请选择"**创建新**"，输入库的名称，然后输入 **"确定**"。 

![在创建实验室帐户时配置共享映像库](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>创建实验室帐户后配置
创建实验室帐户后，您可以执行以下任务：

- 创建并附加共享图像库
- 将共享图像库附加到实验室帐户
- 从实验室帐户分离共享图像库

## <a name="create-and-attach-a-shared-image-gallery"></a>创建并附加共享图像库
1. 登录到 Azure[门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”****。 在“DEVOPS”部分中，选择“实验室服务”********。 如果选择“实验室服务”旁边的星星 (`*`)，则会将其添加到左侧菜单上的“收藏夹”部分。******** 从下次开始，请在“收藏夹”下选择“实验室服务”。********

    ![“所有服务”->“实验室服务”](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 选择您的实验室帐户以查看 **"实验室帐户"** 页面。 
4. 选择左侧菜单上的 **"共享图像库**"，然后选择"在工具栏上**创建**"。  

    ![创建共享图像库按钮](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. 在 **"创建共享图像库**"窗口中，输入库**的名称**，然后输入 **"确定**"。 

    ![创建共享图像库窗口](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure 实验室服务将创建共享映像库并将其附加到实验室帐户。 在此实验室帐户中创建的所有实验室都有权访问附加的共享映像库。 

    ![附加的图像库](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    在底部窗格中，您可以在共享图像库中看到图像。 在这个新库中，没有图像。 将图像上载到库时，您会在此页面上看到它们。     

    默认情况下，连接的共享图像库中的所有图像都已启用。 您可以通过在列表中选择选定的**图像并使用"启用所选图像**"或禁用所选图像按钮来启用或**禁用选定的图像**。

## <a name="attach-an-existing-shared-image-gallery"></a>附加现有的共享映像库
以下过程演示如何将现有共享映像库附加到实验室帐户。 

1. 在 **"实验室帐户"** 页上，选择左侧菜单上的 **"共享图像库**"，然后选择工具栏上的 **"附加**"。 

    ![共享图像库 - 添加按钮](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 在 **"连接现有共享图像库"** 页上，选择共享图像库，然后选择 **"确定**"。

    ![选择现有库](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 您将看到以下屏幕： 

    ![列表中的我的库](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    在此示例中，共享图像库中尚未存在图像。

    Azure 实验室服务标识将作为参与者添加到附加到实验室的共享映像库。 它允许教师/IT 管理员将虚拟机映像保存到共享映像库。 在此实验室帐户中创建的所有实验室都有权访问附加的共享映像库。 

    默认情况下，连接的共享图像库中的所有图像都已启用。 您可以通过在列表中选择选定的**图像并使用"启用所选图像**"或禁用所选图像按钮来启用或**禁用选定的图像**。 

## <a name="detach-a-shared-image-gallery"></a>分离共享图像库
实验室只能将一个共享图像库附加到实验室。 如果要附加另一个共享映像库，请先分离当前映像库，然后再附加新映像库。 要从实验室分离共享图像库，请在工具栏上选择 **"分离"，** 并确认分离操作。 

![从实验室帐户分离共享图像库](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>后续步骤
要了解如何将实验室图像保存到共享图像库或使用共享图像库中的图像创建 VM，请参阅[如何使用共享图像库](how-to-use-shared-image-gallery.md)。

有关共享图像库的详细信息，请参阅[共享图像库](../../virtual-machines/windows/shared-image-galleries.md)。
