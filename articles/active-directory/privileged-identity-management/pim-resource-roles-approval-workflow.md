---
title: 在 PIM 中批准或拒绝 Azure 资源角色的请求 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中批准或拒绝 Azure 资源角色的请求。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 02/08/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a458f62ec38503e401b3fb810dc7edc8059a7715
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245569"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>在 PIM 中批准或拒绝 Azure 资源角色的请求

利用 Azure AD Privileged Identity Management (PIM)，可以将角色配置为需要审批才可激活，还可选择一个或多个用户或组作为委派的审批者。 委托的审批者有 24 小时可以审批请求。 如果请求未在 24 小时内获得审批，则符合条件的用户必须重新提交新请求。 24 小时的审批时间范围不可供配置。

按照本文中的步骤，审批或拒绝 Azure 资源角色的请求。

## <a name="view-pending-requests"></a>查看待处理请求

有 Azure 资源角色请求正在等待审批时，委派的审批者将收到电子邮件通知。 可在 PIM 中查看这些待处理的请求。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“审批请求”。

    ![Azure 资源 - 审批请求](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    在“请求激活角色”部分，将看到等待审批的请求列表。

## <a name="approve-requests"></a>审批请求

1. 找到并单击要审批的请求。 此时显示审批窗格。

    ![“审批请求”窗格](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在“理由”框中，键入一个原因。

1. 单击“批准”。

    审批后会出现一个通知。

    ![批准通知](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>拒绝请求

1. 找到并单击要拒绝的请求。 此时显示审批窗格。

    ![“审批请求”窗格](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在“理由”框中，键入一个原因。

1. 单击“拒绝”。

    拒绝后会出现一个通知。

## <a name="workflow-notifications"></a>工作流通知

下面是一些有关工作流通知的信息：

- 当角色请求正在等待其审查时，审批者列表的所有成员会收到电子邮件通知。 电子邮件通知包含请求的直接链接，审批者可通过此链接批准或拒绝请求。
- 请求将由列表中第一个做出批准或拒绝决定的成员来解决。
- 当审批者响应请求时，审批者列表的所有成员都会收到操作通知。
- 获批准的成员激活其角色后，资源管理员会收到通知。

>[!Note]
>如果资源管理员认为获批准的成员不应被激活，则可在 PIM 中删除已激活的角色分配。 尽管资源管理员不会收到等待处理请求的通知（除非他们是审批者列表的成员），但他们可通过在 PIM 中查看等待处理的请求，来查看和取消所有用户等待处理的请求。 

## <a name="next-steps"></a>后续步骤

- [在 PIM 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [PIM 中的电子邮件通知](pim-email-notifications.md)
- [在 PIM 中批准或拒绝 Azure AD 目录角色的请求](azure-ad-pim-approval-workflow.md)
