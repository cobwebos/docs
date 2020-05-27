---
title: 在 Azure 实验室服务中附加或分离共享映像库 | Microsoft Docs
description: 本文介绍了如何在 Azure 实验室服务中将共享映像库附加到课堂实验室。
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
ms.openlocfilehash: aef5cd13742c0265851f5ba2918d557b4e1026d0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592638"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>在 Azure 实验室服务中附加或分离共享映像库
本文介绍了如何将共享映像库附加到实验室帐户或与之分离。 

## <a name="scenarios"></a>方案
下面是此功能支持的两种方案： 

- 实验室帐户管理员将共享映像库附加到实验室帐户，并在实验室上下文之外将映像上传到共享映像库。 然后，实验室创建者可以使用共享映像库中的映像来创建实验室。 
- 实验室帐户管理员将共享映像库附加到实验室帐户。 实验室创建者（讲师）将其实验室的自定义映像保存到共享映像库。 然后，其他实验室创建者可以从共享映像库中选择此映像，为其实验室创建模板。 

    当映像保存到共享映像库时，Azure 实验室服务会将保存的映像复制到同一[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中可用的其他区域。 它确保在相同地理位置的其他区域创建的实验室可以使用此映像。 将映像保存到共享映像库会产生额外成本，其中包括所有复制的映像的成本。 此成本与 Azure 实验室服务的使用成本是分开的。 若要详细了解共享映像库定价，请参阅[共享映像库 - 计费]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)。

    > [!NOTE]
    > Azure 实验室服务支持根据共享映像库中的通用化和特殊化映像来创建模板 VM。 


## <a name="configure-at-the-time-of-lab-account-creation"></a>在创建实验室帐户时配置
在创建实验室帐户时，可以将共享映像库附加到实验室帐户。 可以从下拉列表中选择现有的共享映像库，也可以新建一个。 若要创建共享映像库，并将其附加到实验室帐户，请选择“新建”，输入库名称，然后按“确定”。 

![在创建实验室帐户时配置共享映像库](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>在创建实验室帐户后配置
在创建实验室帐户后，可以执行以下任务：

- 创建和附加共享映像库
- 将共享映像库附加到实验室帐户
- 将共享映像库与实验室帐户分离

## <a name="create-and-attach-a-shared-image-gallery"></a>创建和附加共享映像库
1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”。 在“DEVOPS”部分中，选择“实验室服务” 。 如果选择“实验室服务”旁边的星星 (`*`)，则会将其添加到左侧菜单上的“收藏夹”部分。  从下次开始，请在“收藏夹”下选择“实验室服务”。 

    ![“所有服务”->“实验室服务”](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 选择实验室帐户，以查看“实验室帐户”页。 
4. 依次选择左侧菜单中的“共享映像库”和工具栏上中的“+ 创建”。  

    ![“创建共享映像库”按钮](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. 在“创建共享映像库”窗口的“名称”中，输入库名称，然后按“确定”。 

    ![“创建共享映像库”窗口](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    此时，Azure 实验室服务创建共享映像库，并将其附加到实验室帐户。 在此实验室帐户中创建的所有实验室都有权访问附加的共享映像库。 

    ![附加的映像库](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    在底部窗格中，可以看到共享映像库中的映像。 此新库中没有任何映像。 当你将映像上传到此库后，就会在此页上看到它们。     

    默认情况下，附加的共享映像库中的所有映像都是启用的。 可以启用或禁用所选映像，具体方法为在列表中选择映像，然后使用“启用所选映像”或“禁用所选映像”按钮。

## <a name="attach-an-existing-shared-image-gallery"></a>附加现有共享映像库
下面的过程展示了如何将现有共享映像库附加到实验室帐户。 

1. 在“实验室帐户”页上，依次选择左侧菜单中的“共享映像库”和工具栏上的“附加”。 

    ![共享映像库 -“添加”按钮](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 在“附加现有共享映像库”页上，依次选择共享映像库和“确定”。

    ![选择现有库](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 此时，你会看到以下屏幕： 

    ![列表中的我的库](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    在此示例中，共享映像库中还没有任何映像。

    Azure 实验室服务标识作为参与者添加到附加到实验室的共享映像库。 这样，教师/IT 管理员可以将虚拟机映像保存到共享映像库。 在此实验室帐户中创建的所有实验室都有权访问附加的共享映像库。 

    默认情况下，附加的共享映像库中的所有映像都是启用的。 可以启用或禁用所选映像，具体方法为在列表中选择映像，然后使用“启用所选映像”或“禁用所选映像”按钮。 

## <a name="detach-a-shared-image-gallery"></a>分离共享映像库
只能将一个共享映像库附加到一个实验室。 若要附加另一个共享映像库，请先分离当前共享映像库，再附加新的共享映像库。 若要将共享映像库与实验室分离，请选择工具栏上的“分离”，然后确认分离操作。 

![将共享映像库与实验室帐户分离](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>后续步骤
若要了解如何将实验室映像保存到共享映像库，或使用共享映像库中的映像来创建 VM，请参阅[如何使用共享映像库](how-to-use-shared-image-gallery.md)。

若要详细了解共享映像库的常规概念，请参阅[共享映像库](../../virtual-machines/windows/shared-image-galleries.md)。
