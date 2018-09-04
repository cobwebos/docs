---
title: 在 PIM 中分配 Azure 资源角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中分配 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7019a6f97a9590d3b652584015f3077f4ed075af
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188914"
---
# <a name="assign-azure-resource-roles-in-pim"></a>在 PIM 中分配 Azure 资源角色

Azure AD PIM 可以管理内置的 Azure 资源角色以及自定义角色，包括但不限于：

- 所有者
- 用户访问管理员
- 参与者
- 安全管理员
- 安全管理器等等

>[!NOTE]
已分配到“所有者”或“用户访问管理员”角色的用户或组成员，以及在 Azure AD 中启用订阅管理的全局管理员都属于资源管理员。 这些管理员可以使用适用于 Azure 资源的 PIM 来分配角色、配置角色设置，以及审查访问权限。 查看 [Azure 资源的内置角色](../../role-based-access-control/built-in-roles.md)列表。

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

## <a name="next-steps"></a>后续步骤

- [在 PIM 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
- [在 PIM 中分配 Azure AD 目录角色](pim-how-to-add-role-to-user.md)
