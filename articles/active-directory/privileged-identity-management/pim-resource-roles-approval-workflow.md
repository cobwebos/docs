---
title: 在 PIM 中批准对 Azure 资源角色的请求-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中批准或拒绝 Azure 资源角色的请求。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 096da0547806680f62b569f7c7809a7bbb04cb86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84742073"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求

利用 Azure Active Directory (Azure AD) 中的 Privileged Identity Management (PIM)，可以将角色配置为需要审批才能激活，并从 Azure AD 组织中选择用户或组作为委托的审批者。 我们建议为每个角色选择两个或更多审批者，以减少特权角色管理员的工作量。 委派的审批者有 24 小时可以审批请求。 如果请求未在 24 小时内获得审批，则符合条件的用户必须重新提交新请求。 24 小时的审批时间范围不可供配置。

按照本文中的步骤，审批或拒绝 Azure 资源角色的请求。

## <a name="view-pending-requests"></a>查看待处理请求

有 Azure 资源角色请求正在等待审批时，委派的审批者将收到电子邮件通知。 可以在 Privileged Identity Management 中查看这些挂起的请求。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 

1. 选择“审批请求”  。

    ![显示要评审的请求的“审批请求 - Azure 资源”页](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    在“请求激活角色”部分，将看到等待审批的请求列表  。

## <a name="approve-requests"></a>审批请求

1. 找到并选择要审批的请求。 此时将显示“批准或拒绝”页。

    ![“审批请求 - 批准或拒绝”窗格，其中包含详细信息和“理由”框](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在“理由”  框中，输入业务理由。

1. 选择“批准”  。 你将收到 Azure 批准通知。

    ![显示请求已批准的批准通知](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>拒绝请求

1. 找到并选择要拒绝的请求。 此时将显示“批准或拒绝”页。

    ![“审批请求 - 批准或拒绝”窗格，其中包含详细信息和“理由”框](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在“理由”  框中，输入业务理由。

1. 选择“拒绝”  。 拒绝后会出现一个通知。

## <a name="workflow-notifications"></a>工作流通知

下面是一些有关工作流通知的信息：

- 当某个角色的请求等待审阅时，审批者将收到电子邮件通知。 电子邮件通知包含请求的直接链接，审批者可通过此链接批准或拒绝请求。
- 请求由第一个批准或拒绝的审批者来解析。
- 当审批者响应请求时，会通知所有审批者该操作。
- 获批准的用户激活其角色后，资源管理员会收到通知。

>[!Note]
>如果资源管理员认为获批准的用户不应被激活，则可在 Privileged Identity Management 中删除已激活的角色分配。 尽管资源管理员不会收到待处理请求的通知（除非他们是审批者），但他们可通过在 Privileged Identity Management 中查看待处理请求，来查看和取消所有用户的待处理请求。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management 中的电子邮件通知](pim-email-notifications.md)
- [在 Privileged Identity Management 中批准或拒绝 Azure AD 角色的请求](azure-ad-pim-approval-workflow.md)
