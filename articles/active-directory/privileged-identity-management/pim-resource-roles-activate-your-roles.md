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
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501663"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>在 PIM 中激活 Azure 资源角色

使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)，Azure 资源的符合条件的角色成员可以计划在将来的日期和时间。 他们还可选择特定激活持续时间，但不能超过最长持续时间（由管理员配置）。

本文面向需要在 PIM 中激活其 Azure 资源角色的成员。

## <a name="activate-a-role"></a>激活角色

需要充当某个 Azure 资源角色时，可在 PIM 中使用“我的角色”导航选项请求激活。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。  有关如何将 PIM 磁贴添加到仪表板的信息，请参阅[开始使用 PIM](pim-getting-started.md)。

1. 单击“我的角色”。 

    ![显示的角色可以激活我角色页](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 单击**Azure 资源角色**若要查看符合条件的 Azure 资源角色的列表。

   ![我的角色-Azure 资源角色页](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. 在“Azure 资源角色”列表中，找到要激活的角色。 

    ![Azure 资源角色-我的符合条件的角色列表](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 单击“激活”打开“激活”窗格。 

1. 如果角色需要多重身份验证 (MFA)，请单击“验证你的身份，然后继续”。  只需在每个会话中执行身份验证一次。

    ![验证我的身份使用 MFA 前激活角色](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 单击“验证我的身份”，并遵照说明提供其他安全验证。 

    ![屏幕来提供如 PIN 代码安全验证](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 如果要指定缩小的范围，请单击“范围”  以打开“资源筛选器”窗格。

    它是仅请求访问所需资源的最佳做法。 在“资源筛选器”窗格中，可以指定需要访问的资源组或资源。

    ![激活-资源筛选器窗格来指定作用域](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 根据需要指定自定义的激活开始时间。 成员将在选定时间后激活。

1. 在“原因”框中，输入该激活请求的原因。 

    ![已完成的激活窗格，其中显示作用域、 开始时间、 持续时间和原因](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 单击“激活”  。

    如果角色不需要审批，则会激活该角色并将其添加到活动角色列表中。 如果你想要使用该角色，请按照下一节中的步骤。

    如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![激活请求正在挂起审批通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

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

    ![我的请求-显示挂起的请求的 Azure 资源页](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 向右滚动以查看“请求状态”  列。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“我的请求”。 

1. 针对想要取消的角色，单击“取消”链接。 

    当您单击取消时，将取消该请求。 若要再次激活该角色，必须提交新的激活请求。

   ![我使用突出显示的取消操作的请求列表](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>故障排除

### <a name="permissions-are-not-granted-after-activating-a-role"></a>激活角色后未授予权限

在 PIM 中的角色激活时，激活可能不立即传播到需要特权的角色的所有门户。 有时，即使传播更改，web 门户中缓存可能会导致不并且立即生效的更改。 如果您的激活延迟，下面是你应执行的操作。

1. Azure 门户中注销并重新登录。

    Azure 资源角色激活时，你将看到您的激活的阶段。 所有阶段完成后，您将看到**注销**链接。 可以使用此链接进行注销。这将解决大多数的情况下，激活延迟。

1. 在 PIM 中，验证列出为角色的成员。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [激活我在 PIM 中的 Azure AD 角色](pim-how-to-activate-role.md)
