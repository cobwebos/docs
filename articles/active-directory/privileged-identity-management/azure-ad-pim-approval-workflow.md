---
title: 在 PIM 中批准或拒绝 Azure AD 角色的请求-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中批准或拒绝 Azure AD 角色的请求。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f663f7daad19e77dcc1cc95a6a324e881d92b28
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847132"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>批准或拒绝 Privileged Identity Management 中 Azure AD 角色的请求

利用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，可以将角色配置为需要审批才可激活，还可选择一个或多个用户或组作为委托的审批者。 委派的审批者有 24 小时可以审批请求。 如果请求未在 24 小时内获得审批，则符合条件的用户必须重新提交新请求。 24 小时审批时间范围不可配置。

按照本文中的步骤，批准或拒绝 Azure AD 角色的请求。

## <a name="view-pending-requests"></a>查看待处理请求

有 Azure AD 角色请求正在等待审批时，委托的审批者将收到电子邮件通知。 可以在 Privileged Identity Management 中查看这些挂起的请求。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 角色”。

1. 单击“审批请求”。

    ![Azure AD 角色 - 审批请求](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    将看到待审批的请求列表。

## <a name="approve-requests"></a>审批请求

1. 选中要审批的请求，然后单击“审批”以打开审批所选请求窗格。

    ![突出显示了“批准”选项的“审批请求”列表](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在“审批原因”框中，键入原因。

    ![包含“批准原因”的“批准所选请求”窗格](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 单击“批准”。

    状态符号将随审批操作更新。

    ![单击“批准”按钮后的“批准所选请求”窗格](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒绝请求

1. 选中要拒绝的请求，然后单击“拒绝”以打开拒绝所选请求窗格。

    ![突出显示了“拒绝”选项的“审批请求”列表](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在“拒绝原因”框中，键入原因。

    ![包含“拒绝原因”的“拒绝所选请求”窗格](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 单击“拒绝”。

    状态符号将随拒绝操作更新。

## <a name="next-steps"></a>后续步骤

- [Privileged Identity Management 中的电子邮件通知](pim-email-notifications.md)
- [在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
