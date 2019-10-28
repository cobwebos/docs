---
title: 在 PIM 中激活我的 Azure 资源角色-Azure Active Directory |Microsoft Docs
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
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e2062ece84676e0af37c79cf25d8662f186ab0a
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808979"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中激活我的 Azure 资源角色

使用 Privileged Identity Management （PIM）允许 Azure 资源的合格角色成员计划在将来的日期和时间激活。 他们还可选择特定激活持续时间，但不能超过最长持续时间（由管理员配置）。

本文适用于需要在 Privileged Identity Management 中激活其 Azure 资源角色的成员。

## <a name="activate-a-role"></a>激活角色

需要采用 Azure 资源角色时，可以使用 Privileged Identity Management 中的 "**我的角色**" 导航选项来请求激活。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 有关如何将 Privileged Identity Management 磁贴添加到仪表板的信息，请参阅[开始使用 Privileged Identity Management](pim-getting-started.md)。

1. 选择 **"我的角色"** 。

    ![显示您可以激活的角色的 "我的角色" 页](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. 选择 " **azure 资源角色**" 可查看符合条件的 azure 资源角色的列表。

   ![我的角色-Azure 资源角色页](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. 在“Azure 资源角色”列表中，找到要激活的角色。

    ![Azure 资源角色-我的合格角色列表](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. 选择 "**激活**" 以打开激活窗格。

1. 如果角色需要多重身份验证，请选择“验证你的身份，然后继续”。 只需在每个会话中执行身份验证一次。

    ![在激活角色之前，通过 MFA 验证我的身份](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 选择 **"验证我的身份**"，并按照说明进行操作以提供额外的安全验证。

    ![用于提供安全验证的屏幕，例如 PIN 代码](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 若要指定缩减的作用域，请选择 "**作用域**" 以打开 "资源筛选器" 窗格。

    它是仅请求访问所需资源的最佳做法。 在“资源筛选器”窗格中，可以指定需要访问的资源组或资源。

    ![激活-"资源筛选器" 窗格以指定作用域](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. 根据需要指定自定义的激活开始时间。 成员将在选定时间后激活。

1. 在“原因”框中，输入该激活请求的原因。

    ![已完成激活窗格，其中包含作用域、开始时间、持续时间和原因](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. 选择“激活”。

    如果角色不需要审批，则会激活该角色并将其添加到活动角色列表中。 如果要使用角色，请按照下一部分中的步骤进行操作。

    如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![激活请求正在等待审批通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>激活后立即使用角色

如果激活后发生延迟，请在激活后执行以下步骤，立即使用 Azure 资源角色。

1. 打开 Azure AD Privileged Identity Management。

1. 选择 **"我的角色"** ，查看符合条件的 Azure AD 角色和 Azure 资源角色的列表。

1. 选择 " **Azure 资源角色**"。

1. 选择 "**活动角色**" 选项卡。

1. 角色激活后，请从门户注销并重新登录。

    该角色现在应可供使用。

## <a name="view-the-status-of-your-requests"></a>查看请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 选择 **"我的请求"** 以查看 Azure AD 角色和 Azure 资源角色请求的列表。

    !["我的请求"-显示挂起的请求的 Azure 资源页](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. 向右滚动以查看“请求状态”列。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 选择 **"我的请求"** 。

1. 对于要取消的角色，请选择 "**取消**" 链接。

    选择 "取消" 时，请求将被取消。 若要再次激活该角色，必须提交新的激活请求。

   ![突出显示取消操作的请求列表](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>故障排除

### <a name="permissions-are-not-granted-after-activating-a-role"></a>激活角色后，权限未被授予

在 Privileged Identity Management 中激活角色时，激活可能不会立即传播到需要特权角色的所有门户。 有时，即使更改已传播，门户中的 Web 缓存也可能会导致更改不能立即生效。 如果激活延迟，应执行以下操作。

1. 注销 Azure 门户，然后重新登录。

    激活 Azure 资源角色时，你将看到激活的各个阶段。 在所有阶段都完成后，**注销**链接将会显示。 你可以使用此链接来注销。这将解决激活延迟的大多数情况。

1. 在 Privileged Identity Management 中，验证是否作为角色成员列出。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中扩展或续订 Azure 资源角色](pim-resource-roles-renew-extend.md)
- [在 Privileged Identity Management 中激活我的 Azure AD 角色](pim-how-to-activate-role.md)
