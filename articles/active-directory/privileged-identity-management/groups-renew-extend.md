---
title: 在 Privileged Identity Management-Azure AD | 中续订成员分配的过期组所有者Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中扩展或续订角色分配的组分配。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 06/30/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfbb0be22dee4550050d6af10314f3a3bb1f583
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505986"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中扩展或续订特权访问组分配（预览版）

Azure Active Directory （Azure AD） Privileged Identity Management （PIM）提供控件以管理特权访问组的访问和分配生命周期。 管理员可以使用开始和结束日期时间属性分配角色。 当分配结束时间即将到来时，Privileged Identity Management 会向受影响的用户或组发送电子邮件通知。 此外，它还向资源管理员发送电子邮件通知，确保能够保持相应的访问权限。 即使访问权限未延期，分配也可以续订，并在长达 30 天内以过期状态保持可见。

## <a name="who-can-extend-and-renew"></a>谁可以扩展和续订

只有资源的管理员才能扩展或续订特权访问组分配。 受影响的用户或组可以请求扩展即将过期的分配，并请求续订已过期的分配。

## <a name="when-notifications-are-sent"></a>发送通知时

Privileged Identity Management 会向管理员发送电子邮件通知，并将过期的特权访问组分配的受影响用户发送到：

- 到期前14天内
- 到期前一天
- 当分配过期时

当用户或组请求扩展或续订过期或过期分配时，管理员会收到通知。 当管理员解决请求时，会通知所有管理员和请求用户批准或拒绝。

## <a name="extend-group-assignments"></a>扩展组分配

以下步骤概述了请求、解析或管理组分配的扩展或续订过程。

### <a name="self-extend-expiring-assignments"></a>自我延期即将过期的分配

分配给特权访问组的用户可以直接从组的 "**分配**" 页上的 "**符合条件**" 或 "**活动**" 选项卡中展开 "过期组分配"。 用户或组可以请求扩展在接下来的14天内过期的符合条件的活动分配。

!["我的角色" 页列出了符合条件的 assgnments 和操作列](media/groups-renew-extend/self-extend-group-assignment.png)

如果分配结束日期时间在14天内，则**扩展**命令可用。 若要请求组分配的扩展，请选择 "**扩展**" 以打开请求窗体。

!["扩展组分配" 窗格，其中包含原因框和详细信息](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>我们建议详细说明为何有必要延期，以及要同意延期多久（如果知道此信息）。

在此期间，管理员会收到电子邮件通知，请求他们查看扩展请求。 如果已提交延期请求，门户中会显示一条 Azure 通知。

若要查看或取消请求的状态，请打开组分配的 "**挂起的请求**" 页。

!["特权访问组分配-挂起的请求" 页，显示要取消的链接](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>管理员批准的延期

当用户或组提交扩展组分配的请求时，管理员会收到电子邮件通知，其中包含原始分配的详细信息以及请求的原因。 此通知还包含一个直接链接，让管理员批准或拒绝该请求。

除了使用电子邮件中的链接以外，管理员还可以通过转到 Privileged Identity Management 管理门户，并从左窗格中选择“审批请求”来批准或拒绝请求。****

!["特权访问组分配-批准请求" 页列出请求和要批准或拒绝的链接](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

当管理员选择 "**批准**" 或 "**拒绝**" 时，将显示请求的详细信息，以及一个字段，用于为审核日志提供业务理由。

![批准具有请求者原因、分配类型、开始时间、结束时间和原因的组分配请求](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

当批准请求以扩展组分配时，资源管理员可以选择新的 "开始日期"、"结束日期" 和 "分配类型"。 如果管理员希望提供受限的访问权限来完成特定的任务（例如，一天的访问权限），则可能需要更改分配类型。 在此示例中，管理员可将分配从“符合条件”更改为“活动”。******** 这意味着，他们可为请求者提供访问权限，而无需让请求者激活。

### <a name="admin-initiated-extension"></a>管理员发起的延期

如果分配给某个组的用户未请求组分配的扩展，则管理员可以代表该用户扩展分配。 组分配的管理扩展不需要审批，但会在扩展分配后将通知发送给所有其他管理员。

若要扩展组分配，请浏览到 Privileged Identity Management 中的 "分配" 视图。 找到需要延期的分配。 在操作列中选择“延期”。****

![列出具有扩展链接的符合条件的组分配的 "分配" 页](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>续订组分配

虽然在概念上类似于请求扩展的过程，但续订过期组分配的过程不同。 使用以下步骤，分配和管理员可以在需要时续订过期的分配。

### <a name="self-renew"></a>自我续订

不再能够访问资源的用户可以访问最长 30 天的已过期分配历史记录。 要执行此操作，请在左窗格中浏览到 **"我的角色**"，然后选择 "**过期的分配**" 选项卡。

!["我的角色" 页-"过期分配" 选项卡](media/groups-renew-extend/groups-renew-from-my-roles.png)

显示的分配的列表默认为**符合条件的分配**。 使用下拉菜单在符合条件和活动分配之间切换。

若要请求续订列表中的任何组分配，请选择 "**续订**" 操作。 然后提供请求原因。 建议提供持续时间和任何附加的上下文或业务理由，以帮助资源管理员做出批准或拒绝的决定。

!["续订组分配" 窗格显示原因框](media/groups-renew-extend/groups-renew-request-form.png)

提交请求后，资源管理员会收到等待续订组分配的等待请求的通知。

### <a name="admin-approves"></a>管理员审批

资源管理员可以通过电子邮件通知中的链接，或者在 Azure 门户中访问 Privileged Identity Management 并从左窗格中选择“审批请求”，来访问续订请求。****

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的业务理由。********

当批准续订组分配的请求时，资源管理员必须输入新的开始日期、结束日期和分配类型。

## <a name="next-steps"></a>后续步骤

- [批准或拒绝 Privileged Identity Management 中的特权访问组分配的请求](groups-approval-workflow.md)
- [在 Privileged Identity Management 中配置特权访问组设置](groups-role-settings.md)
