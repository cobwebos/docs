---
title: 使用 Azure 实验室服务设置实验室帐户 | Microsoft Docs
description: 本教程介绍了如何使用 Azure 实验室服务（以前称为开发测试实验室）设置实验室帐户。
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
ms.date: 04/26/2018
ms.author: spelluru
ms.openlocfilehash: 347b7d183839868f3b52adbdfd00b38cee3f3fbc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services-formerly-azure-devtest-labs"></a>教程：使用 Azure 实验室服务设置一个实验室帐户（以前称为 Azure 开发测试实验室）
在本教程中，你将作为实验室管理员使用 Azure 实验室服务创建实验室帐户。 然后，为教师提供使用此实验室帐户为课程创建实验室的权限。 教师可通过使用 [Azure 实验室服务网站](https://labs.azure.com)为课程设置实验室。   

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建实验室帐户
> * 将用户添加为“实验室创建者”角色

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-a-lab-account"></a>创建实验室帐户
以下步骤演示了如何使用 Azure 门户通过 Azure 实验室服务创建实验室。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧的主菜单中，选择“创建资源”。
3. 在 Azure Marketplace 中搜索“实验室服务”，然后从下拉列表中选择“实验室服务”。 
4. 在筛选出的服务列表中，选择“实验室服务(预览版)”。 
1. 在“创建实验室帐户”窗口，选择“创建”。
2. 在“实验室帐户”窗口，执行以下操作： 
    1. 在“实验室帐户名称”中输入名称。 
    2. 选择要在其中创建实验室帐户的“Azure 订阅”。
    3. 在“资源组”中选择“新建”，然后输入资源组的名称。
    4. 为“位置”选择要在其中创建实验室帐户的位置/区域。 
    5. 选择**创建**。 

        ![创建实验室帐户窗口](./media/tutorial-setup-lab-account/lab-account-settings.png)
5. 如果没有看到实验室帐户页面，请选择“通知”按钮，然后单击通知中的“转到资源”按钮。 

    ![创建实验室帐户窗口](./media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. 会看到以下“实验室帐户”页面：

    ![“实验室帐户”页面](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>将用户添加为“实验室创建者”角色
为教师提供为课程创建实验室的权限，并将他们添加为实验室创建者角色：

1. 在“实验室帐户”页上，选择“访问控制(IAM)”，然后单击工具栏中的“+ 添加”。 

    ![“实验室帐户”页面](./media/tutorial-setup-lab-account/access-control.png)
2. 在“添加权限”页，为“角色”选择“实验室创建者”，选择想要添加为实验室创建者角色的用户，然后选择“保存”。 

    ![将用户添加为“实验室创建者”角色](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>后续步骤
在本教程中，已创建实验室帐户。 要了解如何专业地创建教室实验室，请继续下一教程：

> [!div class="nextstepaction"]
> [设置课堂实验室](tutorial-setup-classroom-lab.md)

