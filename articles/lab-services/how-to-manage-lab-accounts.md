---
title: 管理 Azure 实验室服务中的实验室帐户 | Microsoft Docs
description: 了解如何在 Azure 订阅中创建实验室帐户、查看所有实验室帐户，或者删除实验室帐户。
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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 09303a4d4fc730aae6fc7c04148d64b0f57e69ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="manage-lab-accounts-in-azure-lab-services-formerly-azure-devtest-labs"></a>管理 Azure 实验室服务（以前称为 Azure 开发测试实验室）中的实验室帐户
本文介绍如何创建实验室帐户、查看所有实验室帐户，或者删除实验室帐户。

## <a name="prerequisites"></a>先决条件
Azure 实验室服务当前为门控的预览版。 若要创建实验室帐户，请[注册预览版](https://aka.ms/azlabspreviewsignup)。

## <a name="create-a-lab-account"></a>创建实验室帐户
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从左侧的主菜单中，选择“创建资源”（列表顶部），指向“开发人员工具”，然后单击“实验室服务(预览)”。
1. 在“创建实验室帐户”窗口，选择“创建”。
2. 在“实验室帐户”窗口，执行以下操作： 
    1. 输入一个名称作为“实验室帐户名称”。 
    2. 选择要在其中创建实验室帐户的“Azure 订阅”。
    3. 对于“资源组”，请选择“新建”，然后为资源组输入一个名称。
    4. 对于“位置”，请选择要在其中创建实验室帐户的位置/区域。 
    5. 选择**创建**。 

        ![创建实验室帐户窗口](./media/how-to-manage-lab-accounts/lab-account-settings.png)
5. 如果没有看到实验室帐户页面，请选择“通知”按钮，然后单击通知中的“转到资源”按钮。 

    ![创建实验室帐户窗口](./media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. 会看到以下“实验室帐户”页面：

    ![“实验室帐户”页面](./media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>将用户添加为“实验室创建者”角色
若要向教程讲解人提供为课程创建实验室的权限，请将他们添加到“实验室创建者”角色：

1. 在“实验室帐户”页面，选择“访问控制(IAM)”，然后单击工具栏中的“+ 添加”。 

    ![“实验室帐户”页面](./media/tutorial-setup-lab-account/access-control.png)
2. 在“添加权限”页选择“实验室创建者”作为“角色”，再选择要添加到“实验室创建者”角色的用户，然后选择“保存”。 

    ![将用户添加为“实验室创建者”角色](./media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>查看实验室帐户
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从菜单中选择“所有资源”。 
3. 选择“实验室服务”作为“类型”。 
    也可按订阅、资源组、位置和标记进行筛选。 

## <a name="delete-a-lab-account"></a>删除实验室帐户
按照上一节中的说明来显示列表中的实验室帐户。 根据以下说明删除实验室帐户： 

1. 选择要删除的**实验室帐户**。 
2. 从工具栏选择“删除”。 
3. 键入“是”进行确认。
4. 选择“删除”。 

## <a name="next-steps"></a>后续步骤
使用 Azure 实验室服务设置实验室入门：

- [设置教室实验室](tutorial-setup-classroom-lab.md)
- [设置自定义实验室](tutorial-create-custom-lab.md)
