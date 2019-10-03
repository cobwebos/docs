---
title: 在 PIM 中激活 Azure 资源角色 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中激活 Azure 资源角色。
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
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e2bf1253f1c2f53b839a73034fcdb33f3aab463
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804335"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中激活 Azure 资源角色

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，Azure 资源的合格角色成员可以计划在将来的日期和时间激活。 他们还可选择特定激活持续时间，但不能超过最长持续时间（由管理员配置）。

本文面向需要在 PIM 中激活其 Azure 资源角色的成员。

## <a name="activate-a-role"></a>激活角色

需要充当某个 Azure 资源角色时，可在 PIM 中使用“我的角色”导航选项请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 有关如何将 PIM 磁贴添加到仪表板的信息，请参阅[开始使用 PIM](pim-getting-started.md)。

1. 单击“我的角色”。

    ![显示可以激活的角色的“我的角色”页](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 单击“Azure 资源角色”查看符合条件的 Azure 资源角色列表。

   ![“我的角色 - Azure 资源角色”页](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. 在“Azure 资源角色”列表中，找到要激活的角色。

    ![Azure 资源角色 - 我的合格角色列表](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 单击“激活”打开“激活”窗格。

1. 如果角色需要多重身份验证 (MFA)，请单击“验证你的身份，然后继续”。 只需在每个会话中执行身份验证一次。

    ![在激活角色之前使用 MFA 验证身份](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 单击“验证我的身份”，并遵照说明提供其他安全验证。

    ![用于提供安全验证（例如 PIN 码）的屏幕](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果要指定缩小的范围，请单击“范围”以打开“资源筛选器”窗格。

    它是仅请求访问所需资源的最佳做法。 在“资源筛选器”窗格中，可以指定需要访问的资源组或资源。

    ![用于指定范围的“激活 - 资源筛选器”窗格](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 根据需要指定自定义的激活开始时间。 成员将在选定时间后激活。

1. 在“原因”框中，输入该激活请求的原因。

    ![“已完成激活”窗格，其中包含范围、开始时间、持续时间和原因](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 单击“激活”。

    如果角色不需要审批，则会激活该角色并将其添加到活动角色列表中。 若要使用该角色，请按照下一部分中的步骤操作。

    如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![“激活请求正在等待审批”通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>激活后立即使用角色

如果激活后出现任何延迟，请在激活后按照以下步骤立即使用 Azure 资源角色。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“我的角色”，查看符合条件的 Azure AD 角色和 Azure 资源角色列表。

1. 单击“Azure 资源角色”。

1. 单击“活动角色”选项卡。

1. 角色处于活动状态后，请退出门户并重新登录。

    该角色现在应可供使用。

## <a name="view-the-status-of-your-requests"></a>查看请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“我的请求”，查看你的 Azure AD 角色和 Azure 资源角色请求列表。

    ![显示挂起的请求的“我的请求 - Azure 资源”页](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 向右滚动以查看“请求状态”列。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“我的请求”。

1. 针对想要取消的角色，单击“取消”链接。

    单击“取消”会取消该请求。 若要再次激活该角色，必须提交新的激活请求。

   ![突出显示“取消”操作的“我的请求”列表](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>故障排除

### <a name="permissions-are-not-granted-after-activating-a-role"></a>激活角色后，权限未被授予

在 PIM 中激活角色时，激活可能不会立即传播到需要特权角色的所有门户。 有时，即使更改已传播，门户中的 Web 缓存也可能会导致更改不能立即生效。 如果激活已延迟，应当按照以下步骤操作。

1. 注销 Azure 门户，然后重新登录。

    激活 Azure 资源角色时，将会看见激活的各阶段。 在所有阶段都完成后，**注销**链接将会显示。 可以使用此链接进行注销。这将解决大多数情况下激活延迟的问题。

1. 在 PIM 中，验证是否已将你列为角色的成员。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [在 PIM 中激活我的 Azure AD 角色](pim-how-to-activate-role.md)
