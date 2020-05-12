---
title: 在 Azure 实验室服务中附加或分离共享映像库 |Microsoft Docs
description: 本文介绍如何在 Azure 实验室服务中将共享映像库附加到教室实验室。
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
ms.openlocfilehash: ce79674462f82e05cc07e9e470cb82ff8e47f672
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118475"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 实验室服务中附加或分离共享映像库
本文介绍如何将共享映像库附加到实验室帐户或将其分离。 

## <a name="scenarios"></a>方案
此功能支持以下两种方案： 

- 实验室帐户管理员将共享映像库附加到实验室帐户，并将图像上传到实验室上下文外部的共享映像库。 然后，实验室创建者可以使用共享映像库中的映像创建实验室。 
- 实验室帐户管理员将共享的映像库附加到实验室帐户。 实验室创建者（讲师）将其实验室的自定义映像保存到共享映像库。 然后，其他实验室创建者可以从共享图像库中选择此映像，为其实验室创建模板。 

    将映像保存到共享映像库时，Azure 实验室服务会将已保存的映像复制到相同[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他区域。 它确保映像可用于在同一地理区域中的其他区域创建的实验室。 将映像保存到共享映像库会产生额外的费用，其中包括所有复制映像的成本。 此成本不同于 Azure 实验室服务的使用成本。 有关共享映像库定价的详细信息，请参阅[共享图像库–计费]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。

    > [!NOTE]
    > Azure 实验室服务支持基于共享映像库中的**通用**映像和**专用**映像创建模板 vm。 


## <a name="configure-at-the-time-of-lab-account-creation"></a>创建实验室帐户时进行配置
创建实验室帐户时，可以将共享映像库附加到实验室帐户。 可以从下拉列表中选择现有的共享映像库，也可以创建一个新的。 若要创建共享映像库并将其附加到实验室帐户，请选择 "**新建**"，输入库的名称，然后输入 **"确定"**。 

![创建实验室帐户时配置共享映像库](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>创建实验室帐户后进行配置
创建实验室帐户后，你可以执行以下任务：

- 创建和附加共享映像库
- 将共享映像库附加到实验室帐户
- 从实验室帐户分离共享映像库

## <a name="create-and-attach-a-shared-image-gallery"></a>创建和附加共享映像库
1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中选择 "**所有服务**"。 在“DEVOPS”部分中，选择“实验室服务”********。 如果选择“实验室服务”旁边的星星 (`*`)，则会将其添加到左侧菜单上的“收藏夹”部分。******** 从下次开始，请在“收藏夹”下选择“实验室服务”。********

    ![“所有服务”->“实验室服务”](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 选择实验室帐户以查看 "**实验室帐户**" 页。 
4. 选择左侧菜单上的 "**共享图像库**"，然后在工具栏上选择 " **+ 创建**"。  

    !["创建共享图像库" 按钮](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. 在 "**创建共享图像库**" 窗口中，输入库的**名称**，然后输入 **"确定"**。 

    ![创建共享图像库窗口](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure 实验室服务创建共享映像库，并将其附加到实验室帐户。 此实验室帐户中创建的所有实验室都有权访问附加的共享映像库。 

    ![附加的图像库](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    在底部窗格中，可以看到共享图像库中的图像。 在这个新库中没有图像。 将图像上传到库时，会在此页上看到它们。     

    默认情况下，将启用附加的共享映像库中的所有映像。 您可以通过在列表中选择并使用 "**启用所选的映像**" 或 "**禁用所选**图像" 按钮来启用或禁用所选图像。

## <a name="attach-an-existing-shared-image-gallery"></a>附加现有的共享映像库
以下过程说明如何将现有的共享映像库附加到实验室帐户。 

1. 在 "**实验室帐户**" 页上，选择左侧菜单上的 "**共享图像库**"，然后在工具栏上选择 "**附加**"。 

    ![共享图像库-添加按钮](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 在 "**附加现有共享映像库**" 页上，选择共享映像库，然后选择 **"确定"**。

    ![选择现有库](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 你会看到以下屏幕： 

    ![列表中的库](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    在此示例中，共享映像库中尚未包含任何映像。

    Azure 实验室服务标识作为参与者添加到附加到实验室的共享映像库。 它允许教师/IT 管理员将虚拟机映像保存到共享映像库。 此实验室帐户中创建的所有实验室都有权访问附加的共享映像库。 

    默认情况下，将启用附加的共享映像库中的所有映像。 您可以通过在列表中选择并使用 "**启用所选的映像**" 或 "**禁用所选**图像" 按钮来启用或禁用所选图像。 

## <a name="detach-a-shared-image-gallery"></a>分离共享映像库
只能将一个共享的映像库附加到实验室。 如果要附加另一个共享图像库，请在附加新的共享映像库之前将其分离。 若要从实验室分离共享图像库，请在工具栏上选择 "**分离**"，然后确认分离操作。 

![从实验室帐户分离共享映像库](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>后续步骤
若要了解如何将实验室映像保存到共享映像库，或使用共享映像库中的映像创建 VM，请参阅[如何使用共享映像库](how-to-use-shared-image-gallery.md)。

有关共享映像库的一般详细信息，请参阅[共享映像库](../../virtual-machines/windows/shared-image-galleries.md)。
