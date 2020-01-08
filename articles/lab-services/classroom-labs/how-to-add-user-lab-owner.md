---
title: 如何在 Azure 实验室服务中添加用户作为实验室所有者
description: 本文介绍管理员如何将用户作为实验室添加到实验室。
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
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480848"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>如何在 Azure 实验室服务中将用户添加为教室实验室的所有者
本文介绍如何在 Azure 实验室服务中添加用户作为实验室的所有者。

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>将用户添加到实验室帐户的 "读取者" 角色
1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”。 搜索 "**实验室服务**"，然后选择它。
3. 从列表中选择**实验室帐户**。 
2. 在 "**实验室帐户" 页**的左侧菜单中，选择 "**访问控制（IAM）** "。 
2. 在 "**访问控制（IAM）** " 页上，选择工具栏上的 "**添加**"，然后选择 "**添加角色分配**"。

    ![实验室帐户的角色分配 ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. 在 "**添加角色分配**" 页上，执行以下步骤： 
    1. 为**角色**选择 "**读取**者"。 
    2. 选择用户。 
    3. 选择“保存”。 

        ![将用户添加到实验室帐户的 "读取者" 角色 ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>将用户添加到实验室的所有者角色

1. 返回 "**实验室帐户**" 页，在左侧菜单中选择 "**所有实验室**"。
2. 选择要将用户添加为其所有者的**实验室**。 
    
    ![选择实验室 ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. 在 "**实验室**" 页的左侧菜单中，选择 "**访问控制（IAM）** "。
4. 在 "**访问控制（IAM）** " 页上，选择工具栏上的 "**添加**"，然后选择 "**添加角色分配**"。
5. 在 "**添加角色分配**" 页上，执行以下步骤： 
    1. 选择**角色**的 "**所有者**"。 
    2. 选择用户。 
    3. 选择“保存”。 

## <a name="next-steps"></a>后续步骤
确认用户在登录到[实验室服务门户](https://labs.azure.com)时看到了实验室。