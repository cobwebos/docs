---
title: 批准 Privileged Identity Management 中的组成员和所有者的激活请求 - Azure AD
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中批准或拒绝可分配角色的组的请求。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536981"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>批准特权访问组成员和所有者的激活请求（预览）

利用 Azure Active Directory (Azure AD) 中的 Privileged Identity Management (PIM)，可以将特权访问组成员和所有者配置为需要审批才能激活，并从 Azure AD 组织中选择用户或组作为委托的审批者。 我们建议为每个组选择两个或更多审批者，以减少特权角色管理员的工作量。 委派的审批者有 24 小时可以审批请求。 如果请求未在 24 小时内获得审批，则符合条件的用户必须重新提交新请求。 24 小时的审批时间范围不可供配置。

按照本文中的步骤，审批或拒绝 Azure 资源角色的请求。

## <a name="view-pending-requests"></a>查看待处理请求

有 Azure 资源角色请求正在等待审批时，委派的审批者将收到电子邮件通知。 可以在 Privileged Identity Management 中查看挂起的请求。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“审批请求”。

    ![显示要评审的请求的“审批请求 - Azure 资源”页](./media/groups-approval-workflow/groups-select-request.png)

    在“请求激活角色”部分，将看到等待审批的请求列表****。

## <a name="approve-requests"></a>审批请求

1. 找到并选择要审批的请求，然后选择“批准”。

    ![显示 "批准请求" 页的屏幕截图，其中突出显示了 "批准" 和 "确认" 按钮。](./media/groups-approval-workflow/groups-confirm-approval.png)

1. 在“理由”**** 框中，输入业务理由。

1. 选择“确认”。 批准将生成 Azure 通知。

## <a name="deny-requests"></a>拒绝请求

1. 找到并选择要拒绝的请求，然后选择“拒绝”。

    ![“审批请求 - 批准或拒绝”窗格，其中包含详细信息和“理由”框](./media/groups-approval-workflow/groups-confirm-denial.png)

1. 在“理由”**** 框中，输入业务理由。

1. 选择“确认”。 拒绝将生成 Azure 通知。

## <a name="workflow-notifications"></a>工作流通知

下面是一些有关工作流通知的信息：

- 当组分配的请求等待审批者审阅时，审批者将收到电子邮件通知。 电子邮件通知包含请求的直接链接，审批者可通过此链接批准或拒绝请求。
- 请求由第一个批准或拒绝的审批者来解析。
- 当审批者响应请求时，会通知所有审批者该操作。

>[!Note]
>如果管理员认为获批准的用户不应被激活，则可在 Privileged Identity Management 中删除已激活的组分配。 尽管资源管理员不会收到待处理请求的通知（除非他们是审批者），但他们可通过在 Privileged Identity Management 中查看待处理请求，来查看和取消所有用户的待处理请求。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中延期或续订组分配](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management 中的电子邮件通知](pim-email-notifications.md)
- [在 Privileged Identity Management 中批准或拒绝组分配的请求](azure-ad-pim-approval-workflow.md)
