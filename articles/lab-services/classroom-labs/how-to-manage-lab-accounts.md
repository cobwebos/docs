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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 6039ea482b0968d48fc21ff3dfec82a2ff0db43d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715320"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>管理 Azure 实验室服务中的实验室帐户 
在 Azure 实验室服务中，实验室帐户是托管实验室（如教室实验室）的容器。 管理员可以设置一个具有 Azure 实验室服务的实验室帐户，并为能够在帐户中创建实验室的实验室所有者提供访问权限。 本文介绍如何创建实验室帐户、查看所有实验室帐户，或者删除实验室帐户。

## <a name="create-a-lab-account"></a>创建实验室帐户
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧的主菜单中，选择“创建资源”。
3. 在 Azure 市场中搜索“实验室服务”，然后从下拉列表中选择“实验室服务”。 
4. 在筛选出的服务列表中，选择“实验室服务(预览版)”。 
5. 在“创建实验室帐户”窗口，选择“创建”。
7. 在“实验室帐户”窗口，执行以下操作： 
    1. 在“实验室帐户名称”中输入名称。 
    2. 选择要在其中创建实验室帐户的“Azure 订阅”。
    3. 在“资源组”中选择“新建”，然后输入资源组的名称。
    4. 为“位置”选择要在其中创建实验室帐户的位置/区域。 
    5. 选择**创建**。 

        ![创建实验室帐户窗口](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. 如果没有看到实验室帐户页面，请选择“通知”按钮，然后单击通知中的“转到资源”按钮。 

    ![创建实验室帐户窗口](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. 会看到以下“实验室帐户”页面：

    ![“实验室帐户”页面](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>将用户添加为“实验室创建者”角色
为教师提供为课程创建实验室的权限，并将他们添加为实验室创建者角色：

1. 在“实验室帐户”页上，选择“访问控制(IAM)”，然后单击工具栏中的“+ 添加”。 

    ![“实验室帐户”页面](../media/tutorial-setup-lab-account/access-control.png)
2. 在“添加权限”页，为“角色”选择“实验室创建者”，选择想要添加为实验室创建者角色的用户，然后选择“保存”。 

    ![将用户添加为“实验室创建者”角色](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


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
开始使用 Azure 实验室服务设置实验室：

- [设置课堂实验室](tutorial-setup-classroom-lab.md)
- [设置实验室](../tutorial-create-custom-lab.md)
