---
title: 使用 Azure 实验室服务中共享的映像库 |Microsoft Docs
description: 了解如何配置要使用共享的映像库，以便用户可以与其他共享映像和另一个用户可以使用图像来在实验室中创建模板 VM 的实验室帐户。
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695143"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>使用 Azure 实验室服务中共享的映像库
本文介绍如何教师/实验室管理员可以将保存模板的虚拟机映像，以便其他人重复使用。 这些映像保存在 Azure 中[共享的映像库](../../virtual-machines/windows/shared-image-galleries.md)。 第一步，实验室管理员将一个现有的共享的映像库附加到实验室帐户。 一旦将附加的共享的映像库，实验室的实验室帐户中创建可以将图像保存到共享的映像库。 其他教师可以从共享的映像库创建它们的类模板中选择此映像。 

## <a name="prerequisites"></a>必备组件
通过使用创建共享的映像库[Azure PowerShell](../../virtual-machines/windows/shared-images.md)或[Azure CLI](../../virtual-machines/linux/shared-images.md)。

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>将共享的映像库连接到实验室帐户
以下过程演示如何将共享的映像库连接到实验室帐户。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”。 在“DEVOPS”部分中，选择“实验室服务”。 如果选择“实验室服务”旁边的星星 (`*`)，则会将其添加到左侧菜单上的“收藏夹”部分。 从下次开始，请在“收藏夹”下选择“实验室服务”。

    ![“所有服务”->“实验室服务”](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 选择实验室帐户，请参阅**实验室帐户**页。 
4. 选择**共享映像库**左侧的菜单中，然后选择**附加**工具栏上。 

    ![共享映像库-添加按钮](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. 上**附加现有的共享映像库**页上，选择共享的映像库，然后选择**确定**。

    ![选择现有的库](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. 请参阅以下屏幕： 

    ![我的库列表中](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    在此示例中，有尚没有图像在共享的映像库中。

Azure 实验室服务标识作为参与者添加到共享的映像库中的附加到实验室。 它允许教师 / 共享的映像库的 IT 管理员要保存虚拟机映像。 在此实验室帐户中创建的所有实验室都具有访问权限的附加共享的映像库。 

默认情况下启用的附加共享的映像库中的所有映像。 您可以通过启用或禁用所选的图像列表中选择它们并使用**启用所选的映像**或**禁用所选的图像**按钮。 

## <a name="detach-a-shared-image-gallery"></a>分离共享的映像库
只有一个共享的映像库可以附加到实验室。 如果你想要将附加另一个共享的映像库，分离之前附加新的当前一个。 若要从你的实验室共享的映像库中分离，请选择**分离**的工具栏上，并确认分离操作。 

## <a name="save-an-image-to-the-shared-image-gallery"></a>将图像保存到共享的映像库
附加共享的映像库后，教师可以为共享的图片库保存的模板映像，以便其他教师可以重用它。

![在库中保存虚拟机映像](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>使用共享的映像库中的图像
教师/教授可以在创建新实验室过程选取了模板的共享的映像库中的可用的自定义映像。

![从库中使用虚拟机映像](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>后续步骤
有关共享的映像库的详细信息，请参阅[共享的映像库](../../virtual-machines/windows/shared-image-galleries.md)。
