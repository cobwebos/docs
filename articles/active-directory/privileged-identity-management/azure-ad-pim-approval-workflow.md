---
title: 在 PIM 中批准或拒绝 Azure AD 角色的请求-Azure AD |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中批准或拒绝 Azure AD 角色的请求。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b39336591e9939d0e5200304cbeced2d9831979
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498768"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>批准或拒绝 Privileged Identity Management 中 Azure AD 角色的请求

通过 Azure Active Directory （Azure AD） Privileged Identity Management （PIM），可以将角色配置为需要批准激活，并选择一个或多个用户或组作为委托的审批者。 委派的审批者有 24 小时可以审批请求。 如果请求未在 24 小时内获得审批，则符合条件的用户必须重新提交新请求。 24 小时的审批时间范围不可供配置。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从2019年11月开始，Privileged Identity Management 的 Azure AD 角色部分更新为符合 Azure 角色体验的新版本。 这将创建其他功能以及对[现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中所遵循的过程取决于当前所用 Privileged Identity Management 的版本。 按照本部分中的步骤确定 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本后，可以在本文中选择与该版本匹配的过程。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 角色的用户登录到[Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 如果在 "概述" 页顶部有一个横幅，请按照本文的 "**新版本**" 选项卡中的说明进行操作。 否则，请按照 "以前的**版本**" 选项卡中的说明进行操作。

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

按照本文中的步骤批准或拒绝 Azure AD 角色的请求。

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="view-pending-requests"></a>查看待处理请求

作为委托的审批者，当 Azure AD 的角色请求等待批准时，你将收到电子邮件通知。 可以在 Privileged Identity Management 中查看这些挂起的请求。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择 "**批准请求**"。

    !["批准请求"-显示 Azure AD 角色评审请求的页面](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    在“请求激活角色”部分，将看到等待审批的请求列表。

## <a name="approve-requests"></a>审批请求

1. 查找并选择要批准的请求。 此时将显示 "批准" 或 "拒绝" 页。

    !["批准请求-批准或拒绝" 窗格，其中包含详细信息和理由框](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. 在 "**对齐**" 框中，输入业务理由。

1. 选择 "**批准**"。 你将收到 Azure 审批通知。

    ![批准显示请求的批准通知](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

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
- 当批准的用户处于活动状态时，将通知全局管理员和特权角色管理员。

>[!NOTE]
>如果 "全局管理员" 或 "特权角色管理员" 认为批准的用户不应处于活动状态，则可以在 Privileged Identity Management 中删除活动角色分配。 尽管不会向管理员通知挂起的请求，除非他们是审批者，但他们可以通过查看 Privileged Identity Management 中的挂起请求来查看和取消所有用户的任何挂起的请求。

# <a name="previous-version"></a>[先前版本](#tab/previous)

## <a name="view-pending-requests"></a>查看待处理请求

作为委托的审批者，当 Azure AD 的角色请求等待批准时，你将收到电子邮件通知。 可以在 Privileged Identity Management 中查看这些挂起的请求。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 角色”。

1. 单击“审批请求”。

    ![Azure AD 角色-批准请求](./media/azure-ad-pim-approval-workflow/approve-requests.png)

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

1. 选择 "**拒绝**"。

    状态符号将随拒绝操作更新。

---

## <a name="next-steps"></a>后续步骤

- [Privileged Identity Management 中的电子邮件通知](pim-email-notifications.md)
- [在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
