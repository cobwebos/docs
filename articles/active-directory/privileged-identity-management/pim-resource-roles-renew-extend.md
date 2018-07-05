---
title: 使用 Privileged Identity Management 延期和评审 Azure 资源中的角色 | Microsoft Docs
description: 本文档介绍如何延期和续订 PIM 资源的 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 0f4919fb11d50269f5d4f5956e956bae31c96dc7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442298"
---
# <a name="extend-and-review-roles-in-azure-resources-by-using-privileged-identity-management"></a>使用 Privileged Identity Management 延期和评审 Azure 资源中的角色

Azure 资源的 Privileged Identity Management (PIM) 引入了新控件用于管理 Azure 资源的访问和分配生命周期。 管理员可以使用开始和结束日期时间属性分配成员身份。 当分配结束时间即将到来时，PIM 会向受影响的用户或组发送电子邮件通知。 此外，它还向资源管理员发送电子邮件通知，确保能够保持相应的访问权限。 即使访问权限未延期，分配也可以续订，并在长达 30 天内以过期状态保持可见。

## <a name="who-can-extend-and-renew"></a>谁可以延期和续订？

只有资源管理员可以延期或续订角色分配。 受影响成员可以请求延期即将过期的角色，以及请求续订已过期的角色。

## <a name="when-are-notifications-sent"></a>何时发送通知？

对于在 14 天内过期的角色，PIM 会在过期前的一天向管理员和受影响成员发送电子邮件通知。 分配正式过期后，会额外发送一封电子邮件。 

当即将过期或已过期的角色的成员请求延期或续订时，管理员会收到通知。 当特定管理员解决请求时，做出决定（批准或拒绝）的其他所有管理员会收到通知。 然后，发出请求的成员会收到决定结果。 

## <a name="extend-role-assignments"></a>延期角色分配

以下步骤概述了请求、解决或管理角色分配延期或续订的过程。 

### <a name="member-extend"></a>成员延期

角色分配的成员可以直接通过资源的“我的角色”页上的“符合条件”或“活动”选项卡，或者通过 PIM 门户的“我的角色”顶级页延期即将过期的角色分配。 成员可以请求延期在后续 14 天过期的符合条件且处于活动状态（已分配）的角色。

![延期角色](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

如果分配结束日期时间在 14 天内，则“延期”按钮将在用户界面中变为有效链接。 以下示例假设当前日期为 3 月 27 日。

![“延期”按钮](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

若要请求延期此角色分配，请选择“延期”打开请求窗体。

![打开请求窗体](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

若要查看有关原始分配的信息，请展开“分配详细信息”。 输入延期请求的原因，然后选择“延期”。

>[!Note]
>我们建议详细说明为何有必要延期，以及要同意延期多久（如果知道此信息）。

![延期角色分配](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

片刻之后，资源管理员会收到一封电子邮件通知，要求他们审阅延期请求。 如果已提交延期请求，Azure 门户的顶部会显示一条 toast 通知，其中解释了错误。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

转到左窗格中的“待定的请求”选项卡查看请求状态或取消请求。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>管理员审批

当成员提交延期角色分配的请求时，资源管理员会收到一封电子邮件通知，其中包含原始分配的详细信息，以及请求的原因。 此通知还包含一个直接链接，让管理员批准或拒绝该请求。 

除了使用电子邮件中的链接以外，管理员还可以通过转到 PIM 管理门户，并从左窗格中选择“审批请求”来批准或拒绝请求。

![错误的屏幕截图](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的理由。

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

批准延期角色分配的请求时，资源管理员可以选择新的开始日期和结束日期以及分配类型。 如果管理员希望提供受限的访问权限来完成特定的任务（例如，一天的访问权限），则可能需要更改分配类型。 在此示例中，管理员可将分配从“符合条件”更改为“活动”。 这意味着，他们可为请求者提供访问权限，而无需让请求者激活。

### <a name="admin-extend"></a>管理员延期

如果角色成员忘记或者无法请求角色成员身份延期，管理员可以代表该成员延期分配。 角色成员身份的管理延期不需要审批，但在完成角色延期后，系统会向其他所有管理员发送通知。

若要延期角色成员身份，请浏览到 PIM 中的资源角色或成员视图。 找到需要延期的成员。 在操作列中选择“延期”。

![延期角色成员资格](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>续订角色分配

续订已过期角色分配的过程虽然在概念上与请求延期的过程类似，但两者确实存在差异。 成员和管理员可根据需要，使用以下步骤来续订对已过期角色的访问权限。

### <a name="member-renew"></a>成员续订

不再能够访问资源的成员可以访问最长 30 天的已过期分配历史记录。 为此，他们可以浏览到左窗格中的“我的角色”，并在 Azure 资源角色部分选择“已过期的角色”选项卡。

![“已过期的角色”选项卡](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

显示的角色列表默认为“符合条件的角色”。 使用下拉菜单在“符合条件”与“活动”分配角色之间切换。

若要请求续订列表中的任何角色分配，请选择“续订”操作。 然后提供请求原因。 建议提供持续时间和任何附加的上下文，以帮助资源管理员做出批准或拒绝的决定。

![续订角色分配](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

提交请求后，系统会通知资源管理员收到了一个续订角色分配的待定请求。

### <a name="admin-approves"></a>管理员审批

资源管理员可以通过电子邮件通知中的链接，或者在 Azure 门户中访问 PIM 并从左窗格中选择“审批请求”，来访问续订请求。

![审批请求](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的理由。

![批准角色分配](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

批准续订角色分配的请求时，资源管理员必须输入新的开始日期和结束日期以及分配类型。 

### <a name="admin-renew"></a>管理员续订

资源管理员可以通过资源左侧导航菜单中的“成员”选项卡，续订已过期的角色分配。 他们也可以在资源角色的“已过期角色”选项卡中续订已过期的角色分配。

若要查看所有已过期角色分配的列表，请在“成员”屏幕中选择“已过期角色”。

![已过期角色](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>后续步骤

[需要批准才能激活](pim-resource-roles-approval-workflow.md)

[激活角色](pim-resource-roles-use-the-audit-log.md)


