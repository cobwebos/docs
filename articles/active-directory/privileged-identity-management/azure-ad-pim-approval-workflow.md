---
title: 在 PIM 中批准或拒绝 Azure AD 角色的请求-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中批准或拒绝 Azure AD 角色的请求。
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3245f7343a48d3e54795c14dcb23b836c8d9d988
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756427"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>批准或拒绝 Privileged Identity Management 中 Azure AD 角色的请求

通过 Azure Active Directory （Azure AD） Privileged Identity Management （PIM），可以将角色配置为需要批准激活，并选择一个或多个用户或组作为委托的审批者。 委派的审批者有 24 小时可以审批请求。 如果请求未在 24 小时内获得审批，则符合条件的用户必须重新提交新请求。 24 小时审批时间范围不可配置。

按照本文中的步骤批准或拒绝 Azure AD 角色的请求。

## <a name="view-pending-requests"></a>查看待处理请求

作为委托的审批者，当 Azure AD 的角色请求等待批准时，你将收到电子邮件通知。 可以在 Privileged Identity Management 中查看这些挂起的请求。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 角色”。

1. 单击“审批请求”。

    ![Azure AD 角色-批准请求](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    将看到待审批的请求列表。

## <a name="approve-requests"></a>审批请求

1. 选中要审批的请求，然后单击“审批”以打开审批所选请求窗格。

    ![突出显示 "批准" 选项的 "批准请求" 列表](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在“审批原因”框中，键入原因。

    ![批准所选请求窗格并提供批准原因](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 单击“批准”。

    状态符号将随审批操作更新。

    ![单击 "批准" 按钮后批准选定请求窗格](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒绝请求

1. 选中要拒绝的请求，然后单击“拒绝”以打开拒绝所选请求窗格。

    ![突出显示拒绝选项的批准请求列表](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在“拒绝原因”框中，键入原因。

    ![拒绝所选请求窗格，拒绝原因](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 单击“拒绝”。

    状态符号将随拒绝操作更新。

## <a name="next-steps"></a>后续步骤

- [Privileged Identity Management 中的电子邮件通知](pim-email-notifications.md)
- [在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
