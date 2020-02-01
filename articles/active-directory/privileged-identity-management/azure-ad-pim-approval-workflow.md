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
ms.date: 11/12/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bc4b518cd2400cf3826b31a590d499c1ad031e7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896530"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>批准或拒绝 Privileged Identity Management 中 Azure AD 角色的请求

通过 Azure Active Directory （Azure AD） Privileged Identity Management （PIM），可以将角色配置为需要批准激活，并选择一个或多个用户或组作为委托的审批者。 委派的审批者有24小时批准请求。 如果在24小时内未批准请求，则合格用户必须重新提交新的请求。 "24 小时批准时间" 窗口不可配置。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从2019年11月开始，Privileged Identity Management 的 Azure AD 角色部分更新为与 Azure 资源角色体验相匹配的新版本。 这将创建其他功能以及对[现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中所遵循的过程取决于当前所用 Privileged Identity Management 的版本。 按照本部分中的步骤确定 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本后，可以在本文中选择与该版本匹配的过程。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 角色的用户登录到[Azure 门户](https://portal.azure.com/)。
1. 打开**Azure AD Privileged Identity Management**。 如果在 "概述" 页顶部有一个横幅，请按照本文的 "**新版本**" 选项卡中的说明进行操作。 否则，请按照 "以前的**版本**" 选项卡中的说明进行操作。

    ![Azure AD 角色新版本](./media/pim-how-to-add-role-to-user/pim-new-version.png)

按照本文中的步骤批准或拒绝 Azure AD 角色的请求。

# <a name="previous-versiontabprevious"></a>[先前版本](#tab/previous)

## <a name="view-pending-requests"></a>查看挂起的请求

作为委托的审批者，当 Azure AD 的角色请求等待批准时，你将收到电子邮件通知。 可以在 Privileged Identity Management 中查看这些挂起的请求。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开**Azure AD Privileged Identity Management**。

1. 单击 " **Azure AD 角色**"。

1. 单击 "**批准请求**"。

    ![Azure AD 角色-批准请求](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    你将看到等待你审批的请求的列表。

## <a name="approve-requests"></a>批准请求

1. 选择要批准的请求，然后单击 "**批准**" 以打开 "批准所选请求" 窗格。

    ![突出显示 "批准" 选项的 "批准请求" 列表](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. 在 "**批准原因**" 框中，键入一个原因。

    ![批准所选请求窗格并提供批准原因](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. 单击 "**批准**"。

    状态符号将更新为你的审批。

    ![单击 "批准" 按钮后批准选定请求窗格](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>拒绝请求

1. 选择要拒绝的请求，然后单击 "**拒绝**" 以打开 "拒绝所选请求" 窗格。

    ![突出显示拒绝选项的批准请求列表](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. 在 "**拒绝原因**" 框中，键入一个原因。

    ![拒绝所选请求窗格，拒绝原因](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. 单击 "**拒绝**"。

    状态符号将用拒绝更新。

# <a name="new-versiontabnew"></a>[新版本](#tab/new)

## <a name="view-pending-requests"></a>查看挂起的请求

作为委托的审批者，你将在 Azure 资源角色请求等待你的批准时收到电子邮件通知。 可以在 Privileged Identity Management 中查看这些挂起的请求。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开**Azure AD Privileged Identity Management**。

1. 选择 "**批准请求**"。

    ![批准请求-显示请求评审的 Azure 资源页面](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    在 "**对角色的请求激活**" 部分中，你将看到等待你审批的请求的列表。

## <a name="approve-requests"></a>批准请求

1. 查找并选择要批准的请求。 此时将显示 "批准" 或 "拒绝" 页。

    !["批准请求-批准或拒绝" 窗格，其中包含详细信息和理由框](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. 在 "**对齐**" 框中，输入业务理由。

1. 选择 "**批准**"。 你将收到 Azure 审批通知。

    ![批准显示请求的批准通知](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>拒绝请求

1. 找到并选择要拒绝的请求。 此时将显示 "批准" 或 "拒绝" 页。

    !["批准请求-批准或拒绝" 窗格，其中包含详细信息和理由框](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. 在 "**对齐**" 框中，输入业务理由。

1. 选择 "**拒绝**"。 此时会出现一个通知，告知你拒绝。

## <a name="workflow-notifications"></a>工作流通知

以下是有关工作流通知的一些信息：

- 当某个角色的请求等待其审阅时，审批者将收到电子邮件通知。 电子邮件通知包括指向请求的直接链接，审批者可以在其中批准或拒绝。
- 请求由批准或拒绝的第一个审批者来解析。
- 当审批者响应请求时，会通知所有审批者该操作。
- 当批准的用户处于活动状态时，会通知资源管理员。

>[!NOTE]
>如果资源管理员认为已批准的用户不应该处于活动状态，则可以删除 Privileged Identity Management 中的活动角色分配。 尽管不会向资源管理员通知挂起的请求，除非这些请求是审批者，但他们可以通过查看 Privileged Identity Management 中的挂起请求来查看和取消所有用户的挂起的请求。

---

## <a name="next-steps"></a>后续步骤

- [Privileged Identity Management 中的电子邮件通知](pim-email-notifications.md)
- [在 Privileged Identity Management 中批准或拒绝 Azure 资源角色的请求](pim-resource-roles-approval-workflow.md)
