---
title: 在 Privileged Identity Management Azure Active Directory 中扩展或续订 Azure 资源角色分配 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中扩展或续订 Azure 资源角色分配。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: edfe6a545aa9acae8045e9c9756fc2711504d75d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895561"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>在 Privileged Identity Management 中扩展或续订 Azure 资源角色分配

Azure Active Directory （Azure AD） Privileged Identity Management （PIM）提供控件来管理 Azure 资源的访问和分配生命周期。 管理员可以使用开始和结束日期时间属性分配角色。 分配结束方法时，Privileged Identity Management 会向受影响的用户或组发送电子邮件通知。 此外，它还向资源管理员发送电子邮件通知，确保能够保持相应的访问权限。 即使访问权限未延期，分配也可以续订，并在长达 30 天内以过期状态保持可见。

## <a name="who-can-extend-and-renew"></a>谁可以延期和续订？

只有资源管理员可以延期或续订角色分配。 受影响的用户或组可以请求扩展即将过期的角色，并请求续订已过期的角色。

## <a name="when-are-notifications-sent"></a>何时发送通知？

Privileged Identity Management 会将电子邮件通知发送给管理员、受影响的用户或在14天内过期的角色组，以及在过期之前的一天。 分配正式过期后，会额外发送一封电子邮件。

当向用户或组分配过期或过期的角色请求来扩展或续订时，管理员会收到通知。 当特定管理员解决请求时，做出决定（批准或拒绝）的其他所有管理员会收到通知。 然后，将通知请求的用户或组。

## <a name="extend-role-assignments"></a>延期角色分配

以下步骤概述了请求、解决或管理角色分配延期或续订的过程。

### <a name="self-extend-expiring-assignments"></a>自行扩展过期分配

分配到角色的用户或组可以直接从资源的 "**我的角色**" 页上的 "**符合条件**" 或 "**活动**" 选项卡，或在 Privileged Identity Management 门户的 "**我的角色**" 页上扩展过期的角色分配.用户或组可以请求扩展在接下来的14天内过期的符合条件和活动（已分配）的角色。

![Azure 资源-"我的角色" 页，列出具有操作列的符合条件的角色](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

如果分配结束日期时间在 14 天内，则“延期”按钮将在用户界面中变为有效链接。 以下示例假设当前日期为 3 月 27 日。

![带有要激活或扩展的链接的操作列](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

若要请求延期此角色分配，请选择“延期”打开请求窗体。

!["扩展角色分配" 窗格，其中包含原因框](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

若要查看有关原始分配的信息，请展开“分配详细信息”。 输入延期请求的原因，然后选择“延期”。

>[!NOTE]
>我们建议详细说明为何有必要延期，以及要同意延期多久（如果知道此信息）。

![展开分配详细信息的 "扩展角色分配" 窗格](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

在一段时间内，资源管理员会收到电子邮件通知，请求他们查看扩展请求。 如果已提交扩展请求，则会在门户中显示 Azure 通知。

![通知，说明已存在挂起的角色分配扩展](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

请在 "**挂起的请求**" 页上，查看请求的状态或取消请求。

!["Azure 资源-挂起的请求" 页，其中列出了任何待请求的和取消的链接](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>管理员批准的扩展

当用户或组提交扩展角色分配的请求时，资源管理员会收到电子邮件通知，其中包含原始分配的详细信息和请求的原因。 此通知还包含一个直接链接，让管理员批准或拒绝该请求。

除了使用来自电子邮件的链接，管理员还可以通过转到 Privileged Identity Management 管理门户，并在左窗格中选择 "**批准请求**" 来批准或拒绝请求。

![Azure 资源-批准请求页面列出请求和要批准或拒绝的链接](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

当管理员选择 "**批准**" 或 "**拒绝**" 时，将显示请求的详细信息，以及一个字段，用于为审核日志提供业务理由。

![用请求者原因、分配类型、开始时间、结束时间和原因批准角色分配请求](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

批准延期角色分配的请求时，资源管理员可以选择新的开始日期和结束日期以及分配类型。 如果管理员希望提供受限的访问权限来完成特定的任务（例如，一天的访问权限），则可能需要更改分配类型。 在此示例中，管理员可将分配从“符合条件”更改为“活动”。 这意味着，他们可为请求者提供访问权限，而无需让请求者激活。

### <a name="admin-initiated-extension"></a>管理员启动的扩展

如果分配给角色的用户未请求角色分配的扩展，则管理员可以代表用户扩展分配。 角色分配的管理扩展不需要审批，但在扩展角色后，通知将发送给所有其他管理员。

若要扩展角色分配，请浏览到 Privileged Identity Management 中的 "资源" 角色或 "分配" 视图。 查找需要扩展的赋值。 在操作列中选择“延期”。

![Azure 资源-"分配" 页，其中列出了具有要扩展的链接的合格角色](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>续订角色分配

续订已过期角色分配的过程虽然在概念上与请求延期的过程类似，但两者确实存在差异。 使用以下步骤，分配和管理员可以在必要时续订过期角色的访问权限。

### <a name="self-renew"></a>自我续订

不能再访问资源的用户可以访问最多30天的过期分配历史记录。 为此，他们可以浏览到左窗格中的“我的角色”，并在 Azure 资源角色部分选择“已过期的角色”选项卡。

![我的角色页-过期角色选项卡](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

显示的角色列表默认为“符合条件的角色”。 使用下拉菜单在“符合条件”与“活动”分配角色之间切换。

若要请求续订列表中的任何角色分配，请选择“续订”操作。 然后提供请求原因。 除了可以帮助资源管理员决定批准或拒绝的任何其他上下文或业务理由外，还可以提供持续时间。

!["续订角色分配" 窗格显示原因框](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

提交请求后，系统会通知资源管理员收到了一个续订角色分配的待定请求。

### <a name="admin-approves"></a>管理员审批

资源管理员可以从电子邮件通知中的链接访问续订请求，或者通过从 Azure 门户访问 Privileged Identity Management 并从左窗格中选择 "**批准请求**" 来访问续订请求。

![Azure 资源-批准请求页面列出请求和要批准或拒绝的链接](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

当管理员选择 "**批准**" 或 "**拒绝**" 时，将显示请求的详细信息以及一个字段，以便为审核日志提供业务理由。

![用请求者原因、分配类型、开始时间、结束时间和原因批准角色分配请求](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

批准续订角色分配的请求时，资源管理员必须输入新的开始日期和结束日期以及分配类型。

### <a name="admin-renew"></a>管理员续订

资源管理员可以通过资源左侧导航菜单中的“成员”选项卡，续订已过期的角色分配。 他们也可以在资源角色的“已过期角色”选项卡中续订已过期的角色分配。

若要查看所有已过期角色分配的列表，请在“成员”屏幕中选择“已过期角色”。

![Azure 资源-列出过期角色并带有要续订的链接的成员页](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
