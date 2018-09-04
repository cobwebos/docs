---
title: 在 PIM 中查看具有 Azure 资源角色的人员 | Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中查看具有 Azure 资源角色的人员。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: aee172bc6fc77aaac8d2d52037a481fdb976d308
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188962"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>在 PIM 中查看具有 Azure 资源角色的人员

使用 Azure Active Directory Privileged Identity Management (PIM) 可以管理、控制和监视组织中对 Azure 资源的访问。 这些资源包括订阅、资源组甚至虚拟机。 Azure 门户中利用 Azure 基于角色的访问控制 (RBAC) 功能的任何资源都可以利用 Azure AD PIM 中的安全和生命周期管理功能。 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>适用于 Azure 资源的 PIM 可为资源管理员提供帮助

- 查看为哪些用户和组分配了所管理的 Azure 资源的角色
- 启用按需的“实时”访问权限来管理订阅、资源组等资源
- 使用新的时限分配设置，自动使分配的用户/组资源访问权限失效
- 针对快速任务或钟点工计划分配临时资源访问权限
- 强制要求任何内置角色或自定义角色在访问资源时执行多重身份验证 
- 获取在用户活动会话期间与资源访问关联的资源活动报告
- 在为新用户或组分配资源访问权限以及激活有资格的分配时获取警报

## <a name="view-activation-and-azure-resource-activity"></a>查看激活和 Azure 资源活动

如果需要查看特定的用户针对各种资源执行了哪些操作，可以查看与给定激活时段关联的 Azure 资源活动（适用于有资格的用户）。 首先，从“成员”视图中或者从充当特定角色的成员列表中选择一个用户。 结果会按日期显示该用户对 Azure 资源执行的操作的图形视图，以及最近在同一时间段内执行的角色激活操作。

![](media/azure-pim-resource-rbac/user-details.png)

选择特定的角色激活操作会显示角色激活详细信息，以及在该用户处于活动状态时发生的相应 Azure 资源活动。

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>评审在订阅中拥有访问权限的用户

若要评审订阅中的角色分配，请在左侧导航菜单中选择“成员”选项卡，或选择角色，并选择特定的角色以评审成员。 

在操作栏中选择“评审”以查看现有访问评审，并选择“添加”创建新的评审。

![](media/azure-pim-resource-rbac/owner.png)

[详细了解访问评审](pim-how-to-perform-security-review.md)

>[!NOTE]
目前只有“订阅”资源类型支持评审。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
- [在 PIM 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
- [Azure 中的内置角色](../../role-based-access-control/built-in-roles.md)
