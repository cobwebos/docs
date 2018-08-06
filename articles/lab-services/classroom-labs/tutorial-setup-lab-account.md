---
title: 使用 Azure 实验室服务设置实验室帐户 | Microsoft Docs
description: 本教程介绍如何使用 Azure 实验室服务设置实验室帐户。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 07/17/2018
ms.author: spelluru
ms.openlocfilehash: b60c1e84eb5b62bfce0eb2ba96129deeee2fc3c3
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345302"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>教程：使用 Azure 实验室服务设置实验室帐户
在 Azure 实验室服务中，实验室帐户充当管理组织实验室的中心帐户。 在你的实验室帐户中，授予他人创建实验室的权限，并设置适用于实验室帐户下所有实验室的策略。 在本教程中，了解如何以实验室管理员身份创建实验室帐户。 

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建实验室帐户
> * 将用户添加为“实验室创建者”角色
> * 指定可供实验室所有者使用的市场映像

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-lab-account"></a>创建实验室帐户
以下步骤演示了如何使用 Azure 门户通过 Azure 实验室服务创建实验室。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧的主菜单中，选择“创建资源”。
3. 在 Azure 市场中搜索“实验室服务”，然后从下拉列表中选择“实验室服务”。 
4. 在筛选出的服务列表中，选择“实验室服务(预览版)”。 
1. 在“创建实验室帐户”窗口，选择“创建”。
2. 在“实验室帐户”窗口，执行以下操作： 
    1. 在“实验室帐户名称”中输入名称。 
    2. 选择要在其中创建实验室帐户的“Azure 订阅”。
    3. 在“资源组”中选择“新建”，然后输入资源组的名称。
    4. 为“位置”选择要在其中创建实验室帐户的位置/区域。 
    5. 选择**创建**。 

        ![创建实验室帐户窗口](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. 如果没有看到实验室帐户页面，请选择“通知”按钮，然后单击通知中的“转到资源”按钮。 

    ![创建实验室帐户窗口](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. 会看到以下“实验室帐户”页面：

    ![“实验室帐户”页面](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>将用户添加为“实验室创建者”角色
若要在实验室帐户中设置课堂实验室，用户必须是实验室帐户中“实验室创建者”角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 如果打算使用同一用户帐户创建课堂实验室，可以跳过此步骤。 若要使用其他用户帐户创建教室实验室，请执行以下步骤： 

为教师提供为课程创建实验室的权限，并将他们添加为**实验室创建者**角色：

1. 在“实验室帐户”页上，选择“访问控制(IAM)”，然后单击工具栏中的“+ 添加”。 

    ![“实验室帐户”页面](../media/tutorial-setup-lab-account/access-control.png)
2. 在“添加权限”页，为“角色”选择“实验室创建者”，选择想要添加为实验室创建者角色的用户，然后选择“保存”。 

    ![将用户添加为“实验室创建者”角色](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>指定可供实验室所有者使用的市场映像
作为实验室帐户所有者，你可以指定可供实验室创建者用来在实验室帐户中创建实验室的市场映像。 

1. 在左侧的菜单上选择“市场映像”。 默认情况下，可以看到映像的完整列表（包括启用的和禁用的）。 可以通过从顶部的下拉列表中选择“仅启用的/仅禁用的”选项对列表进行筛选来仅查看启用的/禁用的映像。 
    
    ![“市场映像”页](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    列表中显示的市场映像只是满足以下条件的映像：
        
    - 创建单个 VM。
    - 使用 Azure 资源管理器预配 VM
    - 不需要购买额外的许可计划
2. 若要**禁用**已启用的市场映像，请执行下列操作之一： 
    1. 选择最后一列中的“...”（省略号）并选择“禁用映像”。 

        ![禁用一个映像](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 通过选中列表中映像名称前面的复选框从列表中选择一个或多个映像，然后选择“禁用所选映像”。 

        ![禁用多个映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 类似地，若要**启用**市场映像，请执行下列操作之一： 
    1. 选择最后一列中的“...”（省略号）并选择“启用映像”。 
    2. 通过选中列表中映像名称前面的复选框从列表中选择一个或多个映像，然后选择“启用所选映像”。 

## <a name="next-steps"></a>后续步骤
在本教程中，已创建实验室帐户。 要了解如何专业地创建教室实验室，请继续下一教程：

> [!div class="nextstepaction"]
> [设置课堂实验室](tutorial-setup-classroom-lab.md)

