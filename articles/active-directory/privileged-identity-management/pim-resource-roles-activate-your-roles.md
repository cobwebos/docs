---
title: 在 PIM 中激活 Azure 资源角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中激活 Azure 资源角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 59bce2c61db5838bb21a29757d4e354311ecffd5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666241"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中激活 Azure 资源角色

使用 Azure AD Privileged Identity Management (PIM)，Azure 资源的合格角色成员可以计划在将来的日期和时间激活。 他们还可选择特定激活持续时间，但不能超过最长持续时间（由管理员配置）。

本文面向需要在 PIM 中激活其 Azure 资源角色的成员。

## <a name="activate-a-role"></a>激活角色

需要充当某个 Azure 资源角色时，可在 PIM 中使用“我的角色”导航选项请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 有关如何将 PIM 磁贴添加到仪表板的信息，请参阅[开始使用 PIM](pim-getting-started.md)。

1. 单击“我的角色”，查看你有资格获取的 Azure AD 目录角色和 Azure 资源角色列表。

    ![Azure AD 目录角色和 Azure 资源角色 - 我的角色](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 在“Azure 资源角色”列表中，找到要激活的角色。

    ![Azure 资源角色 - 我的角色列表](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 单击“激活”打开“激活”窗格。

1. 如果角色需要多重身份验证 (MFA)，请单击“验证你的身份，然后继续”。 只需在每个会话中执行身份验证一次。

    ![在激活角色之前先执行 MFA 身份验证](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 单击“验证我的身份”，并遵照说明提供其他安全验证。

    ![其他安全验证](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果要指定缩小的范围，请单击“范围”以打开“资源筛选器”窗格。

    它是仅请求访问所需资源的最佳做法。 在“资源筛选器”窗格中，可以指定需要访问的资源组或资源。

    ![激活 - 资源筛选器](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 根据需要指定自定义的激活开始时间。 成员将在选定时间后激活。

1. 在“原因”框中，输入该激活请求的原因。

    ![完成操作后的“激活”窗格](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 单击“激活”。

    如果角色不需要审批，而且已经激活，则此角色将出现在活动角色列表中。 如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![请求等待审批通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>查看请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“我的请求”，查看你的 Azure AD 目录角色和 Azure 资源角色请求列表。

    ![Azure AD 目录角色和 Azure 资源角色 - 我的请求](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 向右滚动以查看“请求状态”列。

## <a name="use-a-role-immediately-after-activation"></a>激活后立即使用角色

由于缓存的原因，在未刷新的情况下，激活过程不会在 Azure 门户中立即发生。 如果需要在激活角色后减少延迟的可能性，可以使用门户中的“应用程序访问”页。 从此页访问的应用程序会立即检查新的角色分配。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“应用程序访问”页。

    ![PIM 应用程序访问 - 屏幕截图](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. 单击“Azure 资源”以在“所有资源”页上重新打开门户。

    单击此链接时，将强制刷新，并检查是否有新的 Azure 资源角色分配。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“我的请求”。

1. 针对想要取消的角色，单击“取消”链接。

    单击“取消”会取消该请求。 若要再次激活该角色，必须提交新的激活请求。

   ![取消等待中的请求](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="next-steps"></a>后续步骤

- [在 PIM 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [在 PIM 中激活 Azure AD 目录角色](pim-how-to-activate-role.md)