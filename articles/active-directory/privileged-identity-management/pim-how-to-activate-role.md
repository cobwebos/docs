---
title: 在 PIM 中激活我的 Azure AD 角色-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中激活 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/28/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f95a1a08189668e5b6f88941069566b00a73bce
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77499208"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>在 PIM 中激活我的 Azure AD 角色

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 简化了企业管理以特权身份访问 Azure AD 中的资源和其他 Microsoft 联机服务（如 Office 365 或 Microsoft Intune）的方式。  

如果符合管理角色的资格，即表示可以在需要执行特权操作时激活该角色。 例如，如果偶尔管理 Office 365 功能，则组织的特权角色管理员可能不会让你成为永久全局管理员，因为该角色也影响其他服务。 他们会让你符合 Azure AD 角色（例如“Exchange Online 管理员”）的资格。 可以在需要权限时，请求暂时分配该角色，并将在预定的时段内拥有管理员控制权。

本文适用于需要在 Privileged Identity Management 中激活其 Azure AD 角色的管理员。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从2019年11月开始，Privileged Identity Management 的 Azure AD 角色部分更新为与 Azure 资源角色体验相匹配的新版本。 这将创建其他功能以及对[现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中所遵循的过程取决于当前所用 Privileged Identity Management 的版本。 按照本部分中的步骤确定 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本后，可以在本文中选择与该版本匹配的过程。

1. 以[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色登录到[Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 如果在 "概述" 页顶部有一个横幅，请按照本文的 "**新版本**" 选项卡中的说明进行操作。 否则，请按照 "以前的**版本**" 选项卡中的说明进行操作。

    [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="activate-a-role"></a>激活角色

当你需要担任 Azure AD 角色时，你可以通过使用 Privileged Identity Management 中的 **"我的角色**" 导航选项来请求激活。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 有关如何将 Privileged Identity Management 磁贴添加到仪表板的信息，请参阅[开始使用 Privileged Identity Management](pim-getting-started.md)。

1. 选择 **"我的角色**"，然后选择 " **Azure AD 角色**" 查看符合条件的 Azure AD 角色的列表。

    ![显示您可以激活的角色的 "我的角色" 页](./media/pim-how-to-activate-role/my-roles.png)

1. 在 " **Azure AD 角色**" 列表中，找到要激活的角色。

    ![Azure AD 角色-我的合格角色列表](./media/pim-how-to-activate-role/activate-link.png)

1. 选择 "**激活**" 以打开激活窗格。

    ![Azure AD 角色-激活页面包含持续时间和范围](./media/pim-how-to-activate-role/activate-page.png)

1. 如果角色需要多重身份验证，请选择“验证你的身份，然后继续”。 只需在每个会话中执行身份验证一次。

    ![在激活角色之前，通过 MFA 验证我的身份](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. 选择 **"验证我的身份**"，并按照说明进行操作以提供额外的安全验证。

    ![用于提供安全验证的屏幕，例如 PIN 代码](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. 若要指定缩减的作用域，请选择 "**作用域**" 以打开 "筛选器" 窗格。 在 "筛选器" 窗格中，可以指定需要访问的 Azure AD 资源。 最佳做法是只请求访问所需资源。

1. 根据需要指定自定义的激活开始时间。 Azure AD 角色将在选定时间之后激活。

1. 在“原因”框中，输入该激活请求的原因。

1. 选择“激活”。

    如果角色不需要审批，则会激活该角色并将其添加到活动角色列表中。 如果要使用角色，请按照下一部分中的步骤进行操作。

    ![已完成激活窗格，其中包含作用域、开始时间、持续时间和原因](./media/pim-how-to-activate-role/azure-ad-activation-status.png)

    如果[角色需要审批](pim-resource-roles-approval-workflow.md)才能激活，则浏览器右上角会显示一条通知，告知你请求正在等待审批。

    ![激活请求正在等待审批通知](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>激活后立即使用角色

如果激活后发生延迟，请在激活后按以下步骤立即使用 Azure AD 角色。

1. 打开 Azure AD Privileged Identity Management。

1. 选择 **"我的角色"** ，查看符合条件的 Azure AD 角色和 Azure 资源角色的列表。

1. 选择**Azure AD 角色**。

1. 选择 "**活动角色**" 选项卡。

1. 角色激活后，请从门户注销并重新登录。

    该角色现在应可供使用。

## <a name="view-the-status-of-your-requests"></a>查看请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 选择 **"我的请求"** 以查看 Azure AD 角色和 Azure 资源角色请求的列表。

    !["我的请求-Azure AD" 页显示挂起的请求](./media/pim-how-to-activate-role/my-requests-page.png)

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

    激活 Azure AD 角色时，你将看到激活的各个阶段。 在所有阶段都完成后，**注销**链接将会显示。 你可以使用此链接来注销。这将解决激活延迟的大多数情况。

1. 在 Privileged Identity Management 中，验证是否作为角色成员列出。

# <a name="previous-version"></a>[先前版本](#tab/previous)

## <a name="activate-a-role"></a>激活角色

需要采取 Azure AD 角色时，可以使用 Privileged Identity Management 中的 "**我的角色**" 导航选项来请求激活。

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开“Azure AD Privileged Identity Management”。 有关如何将 Privileged Identity Management 磁贴添加到仪表板的信息，请参阅[开始使用 Privileged Identity Management](pim-getting-started.md)。

1. 单击“Azure AD 角色”。

1. 单击 **"我的角色**" 查看符合条件的 Azure AD 角色的列表。

    ![Azure AD 角色-我的角色，用于显示合格或活动的角色列表](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. 找到想要激活的角色。

    ![Azure AD 角色-显示 "激活" 链接的合格角色列表](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. 单击“激活”打开“角色激活详细信息”窗格。

1. 如果角色需要多重身份验证 (MFA)，请单击“验证你的身份，然后继续”。 只需在每个会话中执行身份验证一次。

    ![在激活角色之前，验证我的标识窗格是否包含 MFA](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. 单击“验证我的身份”，并遵照说明提供其他安全验证。

    ![询问如何与你联系的其他安全验证页面](./media/pim-how-to-activate-role/additional-security-verification.png)

1. 单击“激活”打开“激活”窗格。

    ![用于指定开始时间、持续时间、票证和原因的激活窗格](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. 根据需要指定自定义的激活开始时间。

1. 指定激活持续时间。

1. 在“激活原因”框中，输入该激活请求的原因。 某些角色要求提供问题票证编号。

    ![已完成的激活窗格，其中包含自定义的开始时间、持续时间、票证和原因](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. 单击“激活”。

    如果角色不需要审批，将显示 "**激活状态**" 窗格，其中显示激活的状态。

    ![显示三个激活阶段的 "激活状态" 页](./media/pim-how-to-activate-role/activation-status.png)

    所有阶段完成后，单击 "**注销**" 链接以注销 Azure 门户。 当你登录到门户时，你现在可以使用角色。

    如果[角色需要审批](./azure-ad-pim-approval-workflow.md)才能激活，则会在浏览器的右上角显示 Azure 通知，通知你请求正在等待审批。

## <a name="view-the-status-of-your-requests"></a>查看请求的状态

可以查看等待激活的请求的状态。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“Azure AD 角色”。

1. 单击“我的请求”查看请求列表。

    ![Azure AD 角色-我的请求列表](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>停用角色

角色激活后，在达到时间限制（符合条件的时间段）时会自动停用。

如果提前完成了管理员任务，也可以在 Azure AD Privileged Identity Management 中手动停用角色。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“Azure AD 角色”。

1. 单击“我的角色”。

1. 单击“活动角色”，查看活动角色的列表。

1. 找到已用完的角色，然后单击“停用”。

## <a name="cancel-a-pending-request"></a>取消挂起的请求

如果不需要激活需要审批的角色，随时可以取消等待中的请求。

1. 打开 Azure AD Privileged Identity Management。

1. 单击“Azure AD 角色”。

1. 单击“我的请求”。

1. 针对想要取消的角色，单击“取消”按钮。

    单击 "取消" 时，请求将被取消。 若要再次激活该角色，必须提交新的激活请求。

   !["取消" 按钮突出显示的 "我的请求" 列表](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>故障排除

### <a name="permissions-are-not-granted-after-activating-a-role"></a>激活角色后，权限未被授予

在 Privileged Identity Management 中激活角色时，激活可能不会立即传播到需要特权角色的所有门户。 有时，即使更改已传播，门户中的 Web 缓存也可能会导致更改不能立即生效。 如果激活延迟，应执行以下操作。

1. 注销 Azure 门户，然后重新登录。

    激活 Azure AD 角色时，你将看到激活的各个阶段。 在所有阶段都完成后，**注销**链接将会显示。 你可以使用此链接来注销。这将解决激活延迟的大多数情况。

1. 在 Privileged Identity Management 中，验证是否作为角色成员列出。

 ---

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中激活我的 Azure AD 角色](pim-how-to-activate-role.md)
