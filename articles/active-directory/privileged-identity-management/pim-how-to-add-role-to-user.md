---
title: 在 PIM 中分配 Azure AD 角色-Azure Active Directory |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中分配 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/07/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048cefaae10cd55091dd72f0b73a3cf9d731a35
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498860"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中分配 Azure AD 角色

使用 Azure Active Directory （Azure AD），全局管理员可以**永久**地 Azure AD 管理员角色分配。 可以使用 [Azure 门户](../users-groups-roles/directory-assign-admin-roles.md)或 [PowerShell 命令](/powershell/module/azuread#directory_roles)创建这些角色分配。

Azure AD Privileged Identity Management （PIM）服务还允许特权角色管理员进行永久管理员角色分配。 此外，特权角色管理员还可以使用户有**资格**Azure AD 管理员角色。 符合条件的管理员可在需要时激活角色，在完成任务后，其权限随即失效。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从2019年11月开始，Privileged Identity Management 的 Azure AD 角色部分更新为与 Azure 资源角色体验相匹配的新版本。 这将创建其他功能以及对[现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中所遵循的过程取决于当前所用 Privileged Identity Management 的版本。 按照本部分中的步骤确定 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本后，可以在本文中选择与该版本匹配的过程。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 角色的用户登录到[Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。 如果在 "概述" 页顶部有一个横幅，请按照本文的 "**新版本**" 选项卡中的说明进行操作。 否则，请按照 "以前的**版本**" 选项卡中的说明进行操作。

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="assign-a-role"></a>分配角色

请按照以下步骤操作，使用户有资格使用 Azure AD 管理员角色。

1. 使用 "[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)" 角色的成员用户身份登录到[Azure 门户](https://portal.azure.com/)。

    有关如何授予其他管理员访问权限以管理 Privileged Identity Management 的信息，请参阅向[其他管理员授予访问管理 Privileged Identity Management 的权限](pim-how-to-give-access-to-pim.md)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择 "**角色**" 以查看 Azure AD 权限的角色列表。

    ![Azure AD 角色](./media/pim-how-to-add-role-to-user/roles-list.png)

1. 选择 "**添加成员**"，打开 "**新建分配**" 页。

1. 选择 "**选择角色**"，打开 "选择角色" 页。

    ![“新建分配”窗格](./media/pim-how-to-add-role-to-user/select-role.png)

1. 选择要分配的角色，然后单击“选择”。

1. 选择要分配给该角色的成员，然后选择 "**选择**"。

1. 在 "**成员资格设置**" 窗格的 "**分配类型**" 列表中，选择 "**符合条件**" 或 "**活动**"。

    - “合格”分配要求该角色的成员执行某个操作才能使用该角色。 操作可能包括执行多重身份验证 (MFA) 检查、提供业务理由或请求获得指定审批者的批准。

    - “活动”分配不要求成员执行任何操作便可使用该角色。 分配为“活动”的成员始终具有分配给该角色的权限。

1. 如果分配应为永久性的（永久符合或永久分配），请选择 "**永久**" 复选框。

    根据角色设置，复选框可能不会显示或可能无法修改。

1. 若要指定特定分配持续时间，请取消选中该复选框，并修改开始和/或结束日期与时间框。 完成后，选择 "**完成**"。

    ![成员身份设置 - 日期和时间](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 若要创建新的角色分配，请选择 "**添加**"。 显示状态通知。

    ![新建分配 - 通知](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或删除现有的角色分配

按照以下步骤更新或删除现有的角色分配。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择 "**角色**" 以查看 Azure AD 的角色列表。

1. 选择要更新或删除的角色。

1. 在“合格角色”或“活动角色”选项卡上查找角色分配。

    ![更新或删除角色分配](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. 选择“更新”或“删除”以更新或删除角色分配。

# <a name="previous-version"></a>[先前版本](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>使用户符合角色的条件

请按照以下步骤操作，使用户有资格使用 Azure AD 管理员角色。

1. 选择 "**角色**" 或 "**成员**"。

    ![Azure AD 角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 选择 "**添加成员**" 打开 "**添加托管成员**"。

1. 选择 "**选择角色**"，选择要管理的角色，然后选择 "**选择**"。

    ![选择角色](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 选择 "**选择成员**"，选择要分配给角色的用户，然后选择 "**选择**"。

    ![选择角色](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在 "**添加托管成员**" 中，选择 **"确定"** 将用户添加到角色。

1. 在角色列表中，选择刚分配的角色以查看成员列表。

     分配角色后，选择的用户将显示在**符合**该角色条件的成员列表中。

    ![符合角色条件的用户](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 现在用户符合角色条件，让他们知道，他们可以根据 Privileged Identity Management 中的["激活我的 Azure AD 角色](pim-how-to-activate-role.md)" 中的说明进行激活。

    符合条件的管理员在激活期间需要注册 Azure 多重身份验证 (MFA)。 如果用户无法注册 MFA，或使用 Microsoft 帐户（如 @outlook.com），则需要在其所有角色中使其成为永久角色。

## <a name="make-a-role-assignment-permanent"></a>将角色分配设为永久

默认情况下，新*用户仅适用*于 Azure AD 管理员角色。 若要将某个角色分配设为永久，请执行以下步骤。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择“成员”。

    ![成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 选择要使其成为永久角色的**合格**角色。

1. 选择 "**更多**"，然后选择 "**生成永久状态**"。

    ![将角色分配设为永久](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    该角色现在会列为**永久**角色。

    ![已做出永久性更改的成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>从角色中删除用户

你可以从角色分配中删除用户，但请确保始终至少有一个是永久全局管理员的用户。 如果不确定哪些用户仍然需要其角色分配，可以[启动角色的访问权限评审](pim-how-to-start-security-review.md)。

请按照以下步骤从 Azure AD 管理员角色中删除特定的用户。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择“成员”。

    ![成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 选择要删除的角色分配。

1. 选择 "**更多**"，然后选择 "**删除**"。

    ![删除角色](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 在要求确认的消息中，选择 **"是"** 。

    ![删除角色](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    随即会删除该角色分配。

## <a name="authorization-error-when-assigning-roles"></a>分配角色时出现授权错误

如果你最近为某个订阅启用了 Privileged Identity Management，并且在你尝试使某个用户符合 Azure AD 管理员角色时收到授权错误，则可能是因为 MS PIM 服务主体还没有适当的权限。 MS PIM 服务主体必须拥有[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色才能向其他人分配角色。 不需要等待 MS-PIM 被分配“用户访问管理员”角色，你可以手动分配该角色。

请按照以下步骤向订阅的 MS-PIM 服务主体分配“用户访问管理员”角色。

1. 以全局管理员身份登录到 Azure 门户。

1. 选择“所有服务”，然后选择“订阅”。

1. 选择订阅。

1. 选择“访问控制(IAM)”。

1. 选择“角色分配”，以在订阅范围查看角色分配的当前列表。

   ![订阅的“访问控制(IAM)”边栏选项卡](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. 检查 **MS-PIM** 服务主体是否已分配有“用户访问管理员”角色。

1. 如果不是，则选择“添加角色分配”以打开“添加角色分配”窗格。

1. 在“角色”下拉列表中，选择“用户访问管理员”角色。

1. 在“选择”列表中，找到并选择“MS-PIM”服务主体。

   ![添加角色分配窗格-为 MS PIM 服务主体添加权限](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. 选择“保存”以分配角色。

   过一会后，MS-PIM 服务主体将分配有在订阅范围内的“用户访问管理员”角色。

   ![显示 MS PIM 服务主体的用户访问管理员角色分配的访问控制页](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 管理员角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)