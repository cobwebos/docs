---
title: 使用 Azure 实验室服务设置实验室帐户 | Microsoft Docs
description: 了解如何使用 Azure 实验室服务设置实验室帐户、添加实验室创建者，并指定实验室帐户中实验室使用的市场映像。
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 1823d9df8824c9a0bf1ee30a9900ca118625fd9e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589425"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>教程：使用 Azure 实验室服务设置实验室帐户
在 Azure 实验室服务中，实验室帐户充当管理组织实验室的中心帐户。 在你的实验室帐户中，授予他人创建实验室的权限，并设置适用于实验室帐户下所有实验室的策略。 在本教程中，你将了解如何创建实验室帐户。 

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建实验室帐户
> * 将用户添加为“实验室创建者”角色

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-lab-account"></a>创建实验室帐户
以下步骤演示了如何使用 Azure 门户通过 Azure 实验室服务创建实验室。 

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”。 从“类别”中选择“DevOps” 。 然后，选择“实验室服务”。 如果选择“实验室服务”旁边的星星 (`*`)，则会将其添加到左侧菜单上的“收藏夹”部分。  从下次开始，请在“收藏夹”下选择“实验室服务”。 

    ![“所有服务”->“实验室服务”](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 在“实验室服务”页面上，在工具栏上选择“添加”或者在页面上选择“创建实验室帐户”按钮。 

    ![在“实验室帐户”页上选择“添加”](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. 在“创建实验室帐户”页面的“基本信息” 选项卡上，执行以下操作： 
    1. 在“实验室帐户名称”中输入名称。 
    2. 选择要在其中创建实验室帐户的“Azure 订阅”。
    3. 对于“资源组”，选择一个现有资源组，或者选择“新建”并输入资源组的名称。 
    4. 对于“位置”，选择要在其中创建实验室帐户的位置/区域。 

        ![实验室帐户 - “基本信息”页](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. 选择“查看 + 创建”。
    6. 查看“摘要”页，并选择“创建”。 

        ![“查看 + 创建”->“创建”](../media/tutorial-setup-lab-account/create-button.png)    
5. 当部署完成时，展开“后续步骤”，然后选择“转到资源”。  

    ![“转到实验室帐户”页面](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. 确认你看到了“实验室帐户”页面。 

    ![“实验室帐户”页面](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>将用户添加为“实验室创建者”角色
若要在实验室帐户中设置课堂实验室，用户必须是实验室帐户中“实验室创建者”角色的成员。 为教师提供为课程创建实验室的权限，并将他们添加为**实验室创建者**角色：

> [!NOTE]
> 用来创建实验室帐户的帐户会自动添加到此角色。 如果在本教程中打算使用同一用户帐户创建课堂实验室，可以跳过此步骤。 

1. 在“实验室帐户”页上，选择“访问控制(IAM)”，选择工具栏上的“添加”，然后选择工具栏上的“+ 添加角色分配”   。 

    ![“访问控制”->“添加角色分配”按钮](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在“添加角色分配”页上，选择“实验室创建者”作为“角色”，选择想要添加到“实验室创建者”角色的用户，然后选择“保存”   。 

    ![添加实验室创建者](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>后续步骤
在本教程中，已创建实验室帐户。 若要了解如何以教师身份创建课堂实验室，请继续学习下一教程：

> [!div class="nextstepaction"]
> [设置课堂实验室](tutorial-setup-classroom-lab.md)

