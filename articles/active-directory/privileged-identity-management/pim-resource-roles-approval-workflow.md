---
title: 在 Privileged Identity Management Azure Active Directory 中批准或拒绝 Azure 资源角色的请求 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中批准或拒绝 Azure 资源角色的请求。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c436a529ebaf15a3024f935c1b28327230da0ac0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895900"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求

通过 Azure Active Directory （Azure AD）中的 Privileged Identity Management （PIM），可以将角色配置为需要批准激活，并从 Azure AD 组织中选择用户或组作为委派的审批者。 建议为每个角色选择两个或多个审批者，以减少特权角色管理员的工作负荷。 委派的审批者有 24 小时可以审批请求。 如果请求未在 24 小时内获得审批，则符合条件的用户必须重新提交新请求。 24 小时审批时间范围不可配置。

按照本文中的步骤，审批或拒绝 Azure 资源角色的请求。

## <a name="view-pending-requests"></a>查看待处理请求

有 Azure 资源角色请求正在等待审批时，委派的审批者将收到电子邮件通知。 可以在 Privileged Identity Management 中查看这些挂起的请求。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择 "**批准请求**"。

    ![批准请求-显示请求评审的 Azure 资源页面](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    在“请求激活角色”部分，将看到等待审批的请求列表。

## <a name="approve-requests"></a>审批请求

1. 查找并选择要批准的请求。 此时将显示 "批准" 或 "拒绝" 页。

    !["批准请求-批准或拒绝" 窗格，其中包含详细信息和理由框](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在 "**对齐**" 框中，输入业务理由。

1. 选择 "**批准**"。 你将收到 Azure 审批通知。

    ![批准显示请求的批准通知](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>拒绝请求

1. 找到并选择要拒绝的请求。 此时将显示 "批准" 或 "拒绝" 页。

    !["批准请求-批准或拒绝" 窗格，其中包含详细信息和理由框](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在 "**对齐**" 框中，输入业务理由。

1. 选择 "**拒绝**"。 拒绝后会出现一个通知。

## <a name="workflow-notifications"></a>工作流通知

下面是一些有关工作流通知的信息：

- 当某个角色的请求等待其审阅时，审批者将收到电子邮件通知。 电子邮件通知包含请求的直接链接，审批者可通过此链接批准或拒绝请求。
- 请求由批准或拒绝的第一个审批者来解析。
- 当审批者响应请求时，会通知所有审批者该操作。
- 当批准的用户处于活动状态时，会通知资源管理员。

>[!Note]
>如果资源管理员认为已批准的用户不应该处于活动状态，则可以删除 Privileged Identity Management 中的活动角色分配。 尽管不会向资源管理员通知挂起的请求，除非这些请求是审批者，但他们可以通过查看 Privileged Identity Management 中的挂起请求来查看和取消所有用户的挂起的请求。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management 中的电子邮件通知](pim-email-notifications.md)
- [批准或拒绝 Privileged Identity Management 中 Azure AD 角色的请求](azure-ad-pim-approval-workflow.md)
