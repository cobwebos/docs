---
title: Azure PIM 资源 RBAC 概述 | Microsoft Docs
description: 获取 PIM 中 RBAC 功能的概述，包括术语和通知
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 87d349933a8daeaf8b7a4487612ad62dd2b9efc2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="pim-for-azure-resources"></a>适用于 Azure 资源的 PIM

使用 Azure Active Directory Privileged Identity Management (PIM) 可以管理、控制和监视组织中对 Azure 资源的访问。 这些资源包括订阅、资源组甚至虚拟机。 Azure 门户中利用 Azure 基于角色的访问控制 (RBAC) 功能的任何资源都可以利用 Azure AD PIM 提供的所有突出安全和生命周期管理功能，我们计划在不久后为 Azure AD 角色推出一些极佳的新功能。 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>适用于 Azure 资源的 PIM 可为资源管理员提供帮助

- 查看为哪些用户和组分配了所管理的 Azure 资源的角色
- 启用按需的“实时”访问权限来管理订阅、资源组等资源
- 使用新的时限分配设置，自动使分配的用户/组资源访问权限失效
- 针对快速任务或钟点工计划分配临时资源访问权限
- 强制要求任何内置角色或自定义角色在访问资源时执行多重身份验证 
- 获取在用户活动会话期间与资源访问关联的资源活动报告
- 在为新用户或组分配资源访问权限以及激活有资格的分配时获取警报

Azure AD PIM 可以管理内置的 Azure 资源角色以及自定义 (RBAC) 角色，包括但不限于：

- 所有者
- 用户访问管理员
- 参与者
- 安全管理员
- 安全管理器等等

>[!NOTE]
已分配到“所有者”或“用户访问管理员”角色的用户或组成员，以及在 Azure AD 中启用订阅管理的全局管理员都属于资源管理员。 这些管理员可以使用适用于 Azure 资源的 PIM 来分配角色、配置角色设置，以及审查访问权限。 查看 [Azure 资源的内置角色](../role-based-access-built-in-roles.md)列表。

## <a name="tasks"></a>任务

PIM 提供便捷的访问方式，让我们通过左侧导航菜单中的“任务”部分激活角色，以及查看等待做出答复的激活/请求、审批（适用于 [Azure AD 目录角色](azure-ad-pim-approval-workflow.md)）和评审。

从“概述”入口点访问任何“任务”菜单项时，生成的视图包含 Azure AD 目录角色和 Azure 资源角色的结果。 

![](media/azure-pim-resource-rbac/role-settings-details.png)

“我的角色”包含你的活动角色分配，以及 Azure AD 目录角色和 Azure 资源角色的有资格角色分配。

## <a name="activate-roles"></a>激活角色

Azure 资源的激活角色功能引入了新的体验，可让有资格的角色成员计划在将来的某个日期/时间进行激活，并在最大时间范围（由管理员配置）内选择特定的激活持续时间。 在此处了解[如何激活 Azure AD 角色](../active-directory-privileged-identity-management-how-to-activate-role.md)。

![](media/azure-pim-resource-rbac/contributor.png)

通过“激活”菜单输入所需的开始日期和时间以激活角色。 （可选）减小激活持续时间（角色保持活动状态的时间长短），根据需要输入理由；单击“激活”。

如果未修改开始日期和时间，角色在几秒内即会激活。 “我的角色”页面上会出现一条横幅消息，指出某个角色已排队等待激活。 单击刷新按钮可清除此消息。

![](media/azure-pim-resource-rbac/my-roles.png)

如果计划在将来的某个日期时间激活，挂起的请求会显示在左侧导航菜单的“挂起的请求”选项卡中。 如果不再需要激活角色，用户可以通过单击页面右侧的“取消”按钮取消请求。

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>发现和管理 Azure 资源

若要查找和管理 Azure 资源的角色，请在左侧导航菜单中的“管理”选项卡下面选择“Azure 资源”。 使用页面顶部的筛选器或搜索栏查找资源。

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>资源仪表板

“管理员视图”仪表板包含四个主要组件。 过去七天执行的资源角色激活操作的图形表示形式。 此数据对应于选定的资源，显示最常见角色（所有者、参与者、用户访问管理员）以及所有角色的激活情况。

在激活图形的右侧，有两个图表按分配类型显示了用户和组的角色分配分布情况。 选择图表中的切片会将值更改为百分比（或相反）。

![](media/azure-pim-resource-rbac/admin-view.png)

在图表下方，可以看到在过去 30 天获得了新角色分配的用户和组数（左侧），以及按分配总数排序的角色列表（降序）。

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>管理角色分配

管理员可以从左侧导航菜单中选择“角色”或“成员”来管理角色分配。 选择“角色”可让管理员将其管理员对应到特定的角色，而选择“成员”会显示资源的所有用户和组角色分配。

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
如果有等待激活的角色，在查看成员身份时，页面顶部会显示通知横幅。

## <a name="assign-roles"></a>分配角色

若要将某个用户或组分配到某个角色，请选择该角色（查看“角色”时），或者在操作栏中单击“添加”（在“成员”视图中时）。

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
如果从“成员”选项卡添加用户或组，需要先从“添加”菜单中选择一个角色，然后才能选择用户或组。

![](media/azure-pim-resource-rbac/select-role.png)

从目录中选择用户或组。

![](media/azure-pim-resource-rbac/choose.png)

从下拉菜单中选择相应的分配类型。 

**实时分配：**将拥有访问资格但没有永久访问权限的用户或组成员分配到角色，时效为指定的时段或无限期（如果已在角色设置中进行配置）。 

**直接分配：**不需要用户或组成员激活角色分配（称为永久性访问）。 Microsoft 建议针对在完成任务后即不需要访问权限的短期用途（例如钟点工班次或时间敏感型活动）使用直接分配。

![](media/azure-pim-resource-rbac/membership-settings.png)

使用分配类型下拉列表下面的复选框可以指定分配是否为永久性的（永远有资格激活实时分配/直接分配永久有效）。 若要指定特定的分配持续时间，请取消选中该复选框，并修改开始和/或结束日期与时间字段。

>[!NOTE]
如果其他管理员在角色设置中为每个分配类型指定了最长分配持续时间，则该复选框不可修改。

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>查看激活和 Azure 资源活动

如果需要查看特定的用户针对各种资源执行了哪些操作，可以查看与给定激活时段关联的 Azure 资源活动（适用于有资格的用户）。 首先，从“成员”视图中或者从充当特定角色的成员列表中选择一个用户。 结果会按日期显示该用户对 Azure 资源执行的操作的图形视图，以及最近在同一时间段内执行的角色激活操作。

![](media/azure-pim-resource-rbac/user-details.png)

选择特定的角色激活操作会显示角色激活详细信息，以及在该用户处于活动状态时发生的相应 Azure 资源活动。

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>修改现有分配

若要通过用户/组详细信息视图修改现有分配，请在页面顶部的操作栏中选择“更改设置”。 将分配类型更改为“实时分配”或“直接分配”。

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>评审在订阅中拥有访问权限的用户

若要评审订阅中的角色分配，请在左侧导航菜单中选择“成员”选项卡，或选择角色，并选择特定的角色以评审成员。 

在操作栏中选择“评审”以查看现有访问评审，并选择“添加”创建新的评审。

![](media/azure-pim-resource-rbac/owner.png)

[详细了解访问评审](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
目前只有“订阅”资源类型支持评审。

## <a name="configure-role-settings"></a>配置角色设置

配置角色设置可以定义要应用到 PIM 环境中的分配的默认值。 若要为资源定义这些设置，请在左侧导航菜单中选择“角色设置”选项卡，或者在任何角色的操作栏中选择“角色设置”按钮来查看当前选项。

单击页面顶部操作栏中的“编辑”可以修改每项设置。

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

对设置所做的更改会记录在角色设置页面中，包括上次更新日期时间，以及更改设置的管理员。

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>资源审核

资源审核提供资源的所有角色活动的视图。 可以使用预定义日期或自定义范围筛选信息。
![](media/azure-pim-resource-rbac/last-day.png)资源审核还提供快速访问方式来查看用户的活动详细信息。 在视图中，所有“激活角色”操作都以特定请求者的资源活动的链接形式提供。
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Just Enough Administration

在适用于 Azure 资源的 PIM 中，可以轻松针对资源角色分配使用 Just Enough Administration (JEA) 最佳做法。 Azure 订阅或资源组中获得分配的用户和组成员可以在更小的范围内激活其现有角色分配。 

在搜索页面中，找到需要管理的附属资源。

![](media/azure-pim-resource-rbac/azure-resources-02.png)

在左侧导航菜单中选择“我的角色”，并选择要激活的相应角色。 请注意分配类型为“继承”，因为角色是在订阅级别而不是资源组级别分配的，如下所示。

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>后续步骤

- [Azure 资源的内置角色](../role-based-access-built-in-roles.md)
- [在此处了解如何激活 Azure AD 角色](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [PIM 审批工作流](azure-ad-pim-approval-workflow.md)
