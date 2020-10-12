---
title: 在 Privileged Identity Management (PIM) - Azure AD 中查看 Azure 资源角色的审核历史记录 | Microsoft Docs
description: 在 Azure AD Privileged Identity Management (PIM) 中查看 Azure 资源角色的活动和审核历史记录。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84743144"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中查看 Azure 资源角色的活动和审核历史记录

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可以查看组织中的 Azure 资源角色的活动、激活和审核历史记录。 这些资源包括订阅、资源组甚至虚拟机。 Azure 门户中利用 Azure 基于角色的访问控制功能的任何资源都可以利用 Privileged Identity Management 中的安全和生命周期管理功能。

> [!NOTE]
> 如果你的组织对使用 [Azure 委托资源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)的服务提供商具有外包管理功能，则此处将不会显示该服务提供商授权的角色分配。

## <a name="view-activity-and-activations"></a>查看活动和激活

若要查看特定用户在各种资源中执行的操作，可以查看与给定激活时段关联的 Azure 资源活动。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 选择要查看其活动和激活的资源。

1. 选择“角色”**** 或“成员”****。

1. 选择用户。

    你将在 Azure 资源中按日期查看用户操作的摘要。 其中还显示了同一时间段内的最近角色激活。

    ![包含资源活动摘要和角色激活的用户详细信息](media/azure-pim-resource-rbac/rbac-user-details.png)

1. 选择特定角色激活，以查看该用户处于活动状态时出现的详细信息和相应的 Azure 资源活动。

    [![已选择角色激活和活动详细信息](media/azure-pim-resource-rbac/export-membership.png "已选择角色激活和活动详细信息")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>导出具有子级的角色分配

你可能具有合规性要求，必须向审核者提供角色分配的完整列表。 可以使用 Privileged Identity Management 查询特定资源上的角色分配，这包括针对所有子资源的角色分配。 以前，管理员很难获取某个订阅的角色分配完整列表，他们必须导出每个特定资源的角色分配。 使用 Privileged Identity Management，可以查询某个订阅中所有处于活动状态和符合条件的角色分配，包括针对所有资源组和资源的角色分配。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 选择要为其导出角色分配的资源，例如订阅。

1. 选择“**成员**”。

1. 选择 " **导出** " 以打开 "导出成员身份" 窗格。

    [![用于导出所有成员的“导出成员身份”窗格](media/azure-pim-resource-rbac/export-membership.png "导出成员身份页以导出所有成员")](media/azure-pim-resource-rbac/export-membership.png)

1. 选择 " **导出所有成员** " 以导出 CSV 文件中的所有角色分配。

    ![CSV 文件中导出的角色分配在 Excel 中显示](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>查看资源审核历史记录

资源审核提供资源的所有角色活动的视图。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 选择要查看其审核历史记录的资源。

1. 选择“资源审核”****。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    [![带筛选器的资源审核列表](media/azure-pim-resource-rbac/rbac-resource-audit.png "带筛选器的资源审核列表")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. 对于“审核类型”****，选择“激活(已分配 + 已激活)”****。

    [![按激活审核类型筛选的资源审核列表](media/azure-pim-resource-rbac/rbac-audit-activity.png "按激活筛选的资源审核列表")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![按激活审核类型筛选的资源审核列表](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. 在“操作”**** 下，单击某个用户的 **（活动）** 可查看该用户在 Azure 资源中的活动详细信息。

    ![特定操作的用户活动详细信息](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>查看我的审核

使用“我的审核”，可以查看你的个人角色活动。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure 资源” 。

1. 选择要查看其审核历史记录的资源。

1. 选择“我的审核”****。

1. 可以使用预定义的日期或自定义范围筛选历史记录。

    [![当前用户的审核列表](media/azure-pim-resource-rbac/my-audit-time.png "当前用户的审核列表")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> 访问审核历史记录需要“全局管理员”或“特权角色管理员”角色。

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>获取批准事件的原因、审批者和票证编号

1. 以具有特权的角色管理员角色权限登录到 [Azure 门户](https://aad.portal.azure.com) ，并打开 Azure AD。
1. 选择“审核日志”。
1. 使用 **服务** 筛选器仅显示特权标识管理服务的审核事件。 在 " **审核日志** " 页上，您可以：

    - 请参阅 **状态原因** 列中的审核事件的原因。
    - 若要查看 "将成员添加到角色请求已批准" 事件，请参阅 " **发起方 (** 执行组件") 中的审批者。

    [![筛选 PIM 服务的审核日志](media/azure-pim-resource-rbac/filter-audit-logs.png "筛选 PIM 服务的审核日志")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. 选择 "审核日志" 事件，以在 "**详细信息**" 窗格中的 "**活动**" 选项卡上查看票证编号。
  
    [![检查审核事件的票证号](media/azure-pim-resource-rbac/audit-event-ticket-number.png "检查审核事件的票证编号")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. 可以在审核事件的 "**详细信息**" 窗格的 "**目标**" 选项卡上查看 (人员激活角色) 的请求者。 有三种适用于 Azure 资源角色的目标类型：

    - Role (**Type** = role) 
    - 请求方 (**类型** = 其他) 
    - 审批者 (**Type** = User) 

    [![检查目标类型](media/azure-pim-resource-rbac/audit-event-target-type.png "检查目标类型")](media/azure-pim-resource-rbac/audit-event-target-type.png)

通常，在审批事件上立即发生的日志事件是 "将成员添加到角色已完成" 的事件，在此事件中， (执行组件) 为请求 **方 ** 。 在大多数情况下，你无需从审核角度查找审批请求中的请求者。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
- [在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
- [在 Privileged Identity Management 中查看 Azure AD 角色的审核历史记录](pim-how-to-use-audit-log.md)
