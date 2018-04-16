---
title: Azure 资源的 Privileged Identity Management - 分配角色 | Microsoft Docs
description: 介绍如何在 PIM 中分配角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - 资源角色 - 分配

## <a name="assign-roles"></a>分配角色

若要将用户或组分配到角色，请选择角色（如果查看角色）， 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

或单击操作栏中的“添加”（如果在“成员”视图中）。

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


如果从“成员”选项卡添加用户或组，将需要： 

1. 从“添加”菜单中选择角色，然后才能选择用户或组。

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. 从目录中选择用户或组。

3. 从下拉菜单中选择相应的分配类型。 

    - **实时分配：**将拥有访问资格但没有永久访问权限的用户或组成员分配到角色，时效为指定的时段或无限期（如果已在角色设置中进行配置）。 
    - **直接分配：**不需要用户或组成员激活角色分配（称为永久性访问）。 Microsoft 建议针对在完成任务后即不需要访问权限的短期用途（例如钟点工班次或时间敏感型活动）使用直接分配。

使用分配类型下拉列表下面的复选框可以指定分配是否为永久性的（永远有资格激活实时分配/直接分配永久有效）。

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>如果其他管理员在角色设置中为每个分配类型指定了最长分配持续时间，则该复选框不可修改。

 若要指定特定的分配持续时间，请取消选中该复选框，并修改开始和/或结束日期与时间字段。

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>管理角色分配

管理员可以从左侧导航菜单中选择“角色”或“成员”来管理角色分配。 选择“角色”可让管理员将其管理员对应到特定的角色，而选择“成员”会显示资源的所有用户和组角色分配。

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
如果有等待激活的角色，在查看成员身份时，页面顶部会显示通知横幅。


## <a name="modify-existing-assignments"></a>修改现有分配

若要通过用户/组详细信息视图修改现有分配，请在页面顶部的操作栏中选择“更改设置”。 将分配类型更改为“实时分配”或“直接分配”。

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
