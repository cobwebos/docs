---
title: 续订“特权身份管理”中“成员分配”的过期组所有者 - Azure AD | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中延期或续订可分配角色的组分配。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87505986"
---
# <a name="extend-or-renew-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>在 Privileged Identity Management 中延期或续订特权访问组分配（预览版）

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 提供相应的控件用于管理特权访问组的访问和分配生命周期。 管理员可以使用开始和结束日期时间属性分配角色。 当分配结束时间即将到来时，Privileged Identity Management 会向受影响的用户或组发送电子邮件通知。 此外，它还向资源管理员发送电子邮件通知，确保能够保持相应的访问权限。 即使访问权限未延期，分配也可以续订，并在长达 30 天内以过期状态保持可见。

## <a name="who-can-extend-and-renew"></a>谁可以延期和续订

只有资源管理员可以延期或续订特权访问组分配。 受影响的用户或组可以请求延期即将过期的分配，以及请求续订已过期的分配。

## <a name="when-notifications-are-sent"></a>何时发送通知

Privileged Identity Management 会向管理员以及过期的特权访问组分配中的受影响用户发送电子邮件通知：

- 到期前 14 天内
- 到期前一天
- 当分配过期时

当用户或组请求延期或续订即将过期或已过期的分配时，管理员会收到通知。 当管理员解决该请求时，所有管理员和发出请求的用户都会收到批准或拒绝的通知。

## <a name="extend-group-assignments"></a>延期组分配

以下步骤概述了请求、解决或管理组分配延期或续订的过程。

### <a name="self-extend-expiring-assignments"></a>自我延期即将过期的分配

分配给特权访问组的用户可以直接在该组的“分配”页面上通过“符合条件”或“活动”选项卡，延长到期的组分配的有效期  。 用户或组可以请求延期在后续 14 天过期的符合条件且处于活动状态的分配。

![“我的角色”页中列出了带有“操作”列的符合条件的分配](media/groups-renew-extend/self-extend-group-assignment.png)

如果分配结束日期-时间在 14 天以内，可使用“延期”命令。 若要请求延期组分配，请选择“延期”以打开请求窗体。

![包含“原因”框和详细信息的“延期组分配”窗格](media/groups-renew-extend/extend-request-details-group-assignment.png)

>[!NOTE]
>我们建议详细说明为何有必要延期，以及要同意延期多久（如果知道此信息）。

片刻之后，管理员会收到一封电子邮件通知，要求他们审阅延期请求。 如果已提交延期请求，门户中会显示一条 Azure 通知。

要查看请求的状态或取消请求，请打开组分配的“待处理请求”页面。

![特权访问组分配 -“待处理请求”页面，显示“取消”链接](media/groups-renew-extend/group-assignment-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>管理员批准的延期

当用户或组提交延期组分配的请求时，管理员会收到一封电子邮件通知，其中包含原始分配的详细信息，以及请求的原因。 此通知还包含一个直接链接，让管理员批准或拒绝该请求。

除了使用电子邮件中的链接以外，管理员还可以通过转到 Privileged Identity Management 管理门户，并从左窗格中选择“审批请求”来批准或拒绝请求。****

![“特权访问组分配 - 审批请求”页列出了请求和“批准”或“拒绝”链接](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的业务理由。 

![使用请求者原因、分配类型、开始时间、结束时间和原因审批组分配请求](media/groups-renew-extend/group-assignment-extend-admin-approve-reason.png)

批准延期组分配的请求时，资源管理员可以选择新的开始日期、结束日期和分配类型。 如果管理员希望提供受限的访问权限来完成特定的任务（例如，一天的访问权限），则可能需要更改分配类型。 在此示例中，管理员可将分配从“符合条件”更改为“活动”。******** 这意味着，他们可为请求者提供访问权限，而无需让请求者激活。

### <a name="admin-initiated-extension"></a>管理员发起的延期

如果分配到某个组的用户未请求组分配延期，管理员可以代表该用户延期分配。 组分配的管理延期不需要审批，但在完成分配延期后，系统会向其他所有管理员发送通知。

若要延期组分配，请浏览 Privileged Identity Management 中的分配视图。 找到需要延期的分配。 在操作列中选择“延期”。****

![“分配”页列出了带有“延期”链接的符合条件的组分配](media/groups-renew-extend/group-assignment-extend-admin-approve.png)

## <a name="renew-group-assignments"></a>续订组分配

续订已过期组分配的过程虽然在概念上与请求延期的过程类似，但两者确实存在差异。 分配和管理员可根据需要，使用以下步骤来续订对已过期分配的访问权限。

### <a name="self-renew"></a>自我续订

不再能够访问资源的用户可以访问最长 30 天的已过期分配历史记录。 为此，他们可以在左窗格中浏览“我的角色”，并选择“已过期的分配”选项卡。 

![“我的角色”页 -“过期的分配”选项卡](media/groups-renew-extend/groups-renew-from-my-roles.png)

显示的分配列表默认为“符合条件”的分配。 使用下拉菜单在“符合条件”与“活动”分配之间切换。

若要请求续订列表中的任何组分配，请选择“续订”操作。 然后提供请求原因。 建议提供持续时间和任何附加的上下文或业务理由，以帮助资源管理员做出批准或拒绝的决定。

![“续订组分配”窗格显示了“原因”框](media/groups-renew-extend/groups-renew-request-form.png)

提交请求后，资源管理员会收到一个续订组分配的待定请求的通知。

### <a name="admin-approves"></a>管理员审批

资源管理员可以通过电子邮件通知中的链接，或者在 Azure 门户中访问 Privileged Identity Management 并从左窗格中选择“审批请求”，来访问续订请求。****

当管理员选择“批准”或“拒绝”时，将显示请求的详细信息，同时会显示一个字段，让管理员提供审核日志的业务理由。********

批准续订组分配的请求时，资源管理员必须输入新的开始日期、结束日期和分配类型。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中批准或拒绝特权访问组分配的请求](groups-approval-workflow.md)
- [在 Privileged Identity Management 中配置特权访问组设置](groups-role-settings.md)
