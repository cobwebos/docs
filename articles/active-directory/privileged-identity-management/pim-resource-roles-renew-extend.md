---
title: Azure 资源的 Privileged Identity Management - 延期和续订角色 | Microsoft Docs
description: 本文档介绍如何延期和续订 PIM 资源的 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Privileged Identity Management - 资源角色 - 延期或续订

Azure 资源的 PIM 引入了新控件用于管理 Azure 资源的访问和分配生命周期。 管理员可以使用开始和结束日期时间属性分配成员身份。 当分配结束时间即将到来时，PIM 会向受影响成员（可能是用户或组）和资源管理器发送电子邮件通知，以确保能够保持相应的访问权限。 如果由于未采取措施而导致访问权限未延期，分配可以续订，并在长达 30 天内以过期状态保持可见。

## <a name="who-can-extend-and-renew"></a>谁可以延期和续订？

只有资源管理员可以延期或续订角色分配。 受影响成员可以请求延期即将过期的角色，以及请求续订已过期的角色。

## <a name="when-are-notifications-sent"></a>何时发送通知？

对于在 14 天内过期的角色，PIM 会在过期前的一天向管理员和受影响成员发送电子邮件通知。 分配正式过期后，会额外发送一封电子邮件。 

当即将过期或已过期的角色的成员请求延期或续订时，管理员会收到通知。 当特定管理员解决请求时，做出决定（批准或拒绝）的其他所有管理员会收到通知，并且发出请求的成员会收到决定结果。 

## <a name="extend-role-assignments"></a>延期角色分配

下面概述了请求、解决或管理角色分配延期或续订所涉及的步骤和用户界面。 

### <a name="member-extend"></a>成员延期

角色分配的成员可以直接通过资源的“我的角色”页上的“符合条件”或“活动”选项卡，或者通过 PIM 门户的“我的角色”顶级菜单请求延期即将过期的角色分配。 成员可以请求延期在后续 14 天过期的符合条件且处于活动状态（已分配）的角色。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

如果分配结束日期时间在 14 天内，则“延期”按钮将在用户界面中变为有效链接。 以下示例假设当前日期为 3 月 27 日。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

若要请求延期此角色分配，请单击“延期”打开请求窗体。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

展开“分配详细信息”查看有关原始分配的信息。 输入延期请求的原因，然后单击“延期”。

>[!Note]
>我们建议详细说明为何有必要延期，以及要延期多久（如果知道）。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

片刻之后，资源管理员会收到一封电子邮件通知，要求他们审阅延期请求。 如果已提交延期请求，Azure 门户的顶部会显示一条 toast 通知，其中解释了错误。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

访问左侧导航菜单中的“待定的请求”选项卡查看请求状态或取消请求。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>管理员审批

当成员提交延期角色分配的请求时，资源管理员会收到一封电子邮件通知，其中包含原始分配的详细信息，以及请求者提供的原因。 此通知还包含一个直接链接，让管理员批准或拒绝该请求。 

除了使用电子邮件中的链接以外，管理员还可以通过导航到 PIM 管理门户，并从左侧导航菜单中选择“审批请求”来批准或拒绝请求。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的理由。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

批准延期角色分配的请求时，资源管理员可以选择新的开始和结束日期时间以及分配类型。 如果管理员希望提供受限的访问权限来完成特定的任务（例如，一天的访问权限），则可能需要更改分配类型。 在此示例中，管理员可将分配从“符合条件”更改为“活动”，从而为请求者提供访问权限，而无需让请求者激活。

### <a name="admin-extend"></a>管理员延期

如果角色成员忘记或者无法请求角色成员身份延期，管理员可以代表该成员延期分配。 角色成员身份的管理延期不需要审批，但在完成角色延期后，系统会向其他所有管理员发送通知。

若要延期角色成员身份，请导航到 PIM 中的资源角色或成员视图。 找到需要延期的成员，并在操作列中单击“延期”。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>续订角色分配

续订已过期角色分配的过程虽然在概念上与延期类似，但成员和管理员执行的续订步骤却有所不同。 成员和管理员可根据需要，使用以下步骤来续订对已过期角色的访问权限。

### <a name="member-renew"></a>成员续订

不再能够访问资源的成员可以通过导航到 PIM 左侧导航菜单中的“我的角色”，并在 Azure 资源角色部分中选择“已过期的角色”选项卡，来访问最长 30 天的已过期分配历史记录。

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

显示的角色列表默认为“符合条件”的分配。 使用下拉列表在“符合条件”与“活动”分配角色之间切换。

若要请求续订列表中的任何角色分配，请选择“续订”操作，并提供请求原因。 建议提供持续时间和任何附加的上下文，以帮助资源管理员做出批准或拒绝的决定。

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

提交请求后，系统会通知资源管理员收到了一个续订角色分配的待定请求。

### <a name="admin-approves"></a>管理员审批

资源管理员可以通过电子邮件通知中的链接，或者在 Auzre 门户中访问 PIM 并从左侧导航菜单中选择“审批请求”，来访问续订请求。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的理由。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

批准续订角色分配的请求时，资源管理员必须输入新的开始和结束日期时间以及分配类型。 

### <a name="admin-renew"></a>管理员续订

资源管理员可以通过资源左侧导航菜单中的“成员”选项卡，或者在资源角色的“已过期角色”选项卡中，续订已过期的角色分配。

在“成员”屏幕中，选择“已过期角色”查看所有已过期角色分配的列表。

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>后续步骤

[需要批准才能激活](pim-resource-roles-approval-workflow.md)

[激活角色](pim-resource-roles-use-the-audit-log.md)


