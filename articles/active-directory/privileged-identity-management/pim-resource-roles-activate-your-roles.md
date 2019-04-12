---
title: 激活我在 PIM 的 Azure Active Directory 中的 Azure 资源角色 |Microsoft Docs
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
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04e8615cc5534255308c35fa1f675ef3a85aa84e
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493546"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中激活 Azure 资源角色

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，Azure 资源的符合条件的角色成员可以计划在将来的日期和时间。 他们还可选择特定激活持续时间，但不能超过最长持续时间（由管理员配置）。

本文面向需要在 PIM 中激活其 Azure 资源角色的成员。

## <a name="activate-a-role"></a>激活角色

需要充当某个 Azure 资源角色时，可在 PIM 中使用“我的角色”导航选项请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 有关如何将 PIM 磁贴添加到仪表板的信息，请参阅[开始使用 PIM](pim-getting-started.md)。

1. 单击“我的角色”。

    ![Azure AD 角色和 Azure 资源角色-我的角色](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 单击**Azure 资源角色**若要查看符合条件的 Azure 资源角色的列表。

   ![Azure 资源角色](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

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

    如果角色不需要审批，则会激活该角色并将其添加到活动角色列表中。 如果你想要使用该角色，请按照下一节中的步骤。

    如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![请求等待审批通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>激活后立即使用角色

在激活后的任何延迟，请执行以下步骤后激活立即使用 Azure 资源角色。

1. 打开 Azure AD Privileged Identity Management。

1. 单击**我的角色**，列出你有资格接受 Azure AD 角色和 Azure 资源角色。

1. 单击**Azure 资源角色**。

1. 单击**活动角色**选项卡。

1. 激活角色后，门户中注销并重新登录。

    现在应可使用该角色。

## <a name="view-the-status-of-your-requests"></a>查看请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 单击**我的请求**，列出你的 Azure AD 角色和 Azure 资源角色的请求。

    ![Azure AD 角色和 Azure 资源角色-我的请求](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 向右滚动以查看“请求状态”列。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“我的请求”。

1. 针对想要取消的角色，单击“取消”链接。

    当您单击取消时，将取消该请求。 若要再次激活该角色，必须提交新的激活请求。

   ![取消等待中的请求](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>故障排除

### <a name="permissions-not-granted-after-activating-a-role"></a>激活角色后未授予权限

在 PIM 中激活某个角色时，需要等待至少 10 分钟，然后你才能访问所需的管理门户，或者执行特定管理工作负荷中的功能。 激活完成后，Azure 门户中注销并重新登录以开始使用新激活的角色。

有关其他故障排除步骤，请参阅[排查提升的权限问题](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx)。

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>由于资源锁而无法激活角色

如果尝试激活某个角色时收到一条消息，指出 Azure 资源被锁定，则可能是因为角色分配作用域内的某个资源具有资源锁。 锁可以防止资源被意外删除或意外更改。 锁还可以防止 PIM 在激活期间结束时从资源上删除角色分配。 由于 PIM 在应用了锁时无法正常工作，因此，PIM 会禁止用户在资源上激活角色。 有两种方法可以解决此问题：

- 如[锁定资源以防止意外更改](../../azure-resource-manager/resource-group-lock-resources.md)中所述删除锁。
- 如果你希望保留锁，请使角色分配成为永久的或者使用“不受限”帐户。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [激活我在 PIM 中的 Azure AD 角色](pim-how-to-activate-role.md)
