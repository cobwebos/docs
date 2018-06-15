---
title: 使用 Privileged Identity Management 为 Azure 资源分配角色 | Microsoft Docs
description: 介绍如何在 PIM 中分配角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 246467d5eeebd43b8d89d98a30fdfc1c5ca0909a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233649"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 为 Azure 资源分配角色

## <a name="assign-roles"></a>分配角色

若要在查看“角色”窗格时为用户或组分配角色，请选择该角色，然后选择“添加用户”。 

![显示“添加用户”按钮的“角色”窗格](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

也可从“成员”窗格选择“添加用户”。

![显示“添加用户”按钮的“成员”窗格](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


如果从“成员”窗格添加用户或组，则需要： 

1. 从“选择角色”窗格中选择某个角色，然后才能选择用户或组。

   ![“选择角色”窗格](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. 从目录中选择用户或组。

3. 从下拉菜单中选择相应的分配类型： 

   - **实时**：将拥有访问资格但没有永久访问权限的用户或组成员分配到角色，时效为指定的时段或无限期（如果已在角色设置中进行配置）。 
   - **直接**：不需要用户或组成员激活角色分配（称为永久性访问）。 对于短期使用，建议采用直接分配，因为在任务完成时不需要访问权限。 例如值班交班和具有时效性的活动。

4. 如果为永久性分配（永远有资格接受实时分配，或直接分配永久有效），请选择“分配类型”框下的复选框。

   ![显示“分配类型”框以及相关复选框的“成员资格设置”窗格](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >如果其他管理员在角色设置中为每个分配类型指定了最长分配持续时间，则该复选框不可修改。

   若要指定特定分配持续时间，请取消选中该复选框，并修改开始和/或结束日期与时间框。

   ![显示开始日期、开始时间、结束日期和结束时间的“成员资格设置”窗格](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>管理角色分配

管理员可从左侧窗格选择“角色”或“成员”来管理角色分配。 选择“角色”，管理员可将其管理任务限定为针对特定角色。 选择“成员”即显示资源的所有用户和组角色分配。

![“角色”窗格](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![“成员”窗格](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
如果有等待激活的角色，在查看成员身份时，窗格顶部会显示通知横幅。


## <a name="modify-existing-assignments"></a>修改现有分配

若要通过用户/组详细信息视图修改现有分配，请选择操作栏中的“更改设置”。 将分配类型更改为“实时”或“直接”。

![显示“更改设置”按钮的“用户详细信息”窗格](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
