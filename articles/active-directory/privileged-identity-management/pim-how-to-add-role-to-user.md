---
title: 在 Privileged Identity Management Azure Active Directory 中分配 Azure AD 角色 |Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management （PIM）中分配 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809197"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中分配 Azure AD 角色

使用 Azure Active Directory （Azure AD），全局管理员可以**永久**地 Azure AD 管理员角色分配。 可以使用 [Azure 门户](../users-groups-roles/directory-assign-admin-roles.md)或 [PowerShell 命令](/powershell/module/azuread#directory_roles)创建这些角色分配。

Azure AD Privileged Identity Management （PIM）服务还允许特权角色管理员进行永久管理员角色分配。 此外，特权角色管理员还可以使用户有**资格**Azure AD 管理员角色。 符合条件的管理员可在需要时激活角色，在完成任务后，其权限随即失效。

## <a name="make-a-user-eligible-for-a-role"></a>使用户符合角色的条件

请按照以下步骤操作，使用户有资格使用 Azure AD 管理员角色。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成员用户的身份登录到 [Azure 门户](https://portal.azure.com/)。

    有关如何授予其他管理员访问权限以管理 Privileged Identity Management 的信息，请参阅向[其他管理员授予访问管理 Privileged Identity Management 的权限](pim-how-to-give-access-to-pim.md)。

1. 打开“Azure AD Privileged Identity Management”。

    如果尚未开始 Privileged Identity Management Azure 门户，请参阅[开始使用 Privileged Identity Management](pim-getting-started.md)。

1. 选择**Azure AD 角色**。

1. 选择 "**角色**" 或 "**成员**"。

    ![突出显示 "角色和成员" 菜单选项 Azure AD 角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 选择 "**添加成员**" 打开 "添加托管成员"。

1. 选择 "**选择角色**"，选择要管理的角色，然后选择 "**选择**"。

    ![选择 Azure AD 角色列出的角色窗格](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 选择 "**选择成员**"，选择要分配给角色的用户，然后选择 "**选择**"。

    ![选择用户可在其中选择用户的 "成员" 窗格](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在 "添加托管成员" 中，选择 **"确定"** 将用户添加到角色。

1. 在角色列表中，选择刚分配的角色以查看成员列表。

     分配角色后，选择的用户将显示在**符合**该角色条件的成员列表中。

    ![角色的成员连同其激活状态一起列出](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 现在用户符合角色条件，让他们知道，他们可以根据 Privileged Identity Management 中的["激活我的 Azure AD 角色](pim-how-to-activate-role.md)" 中的说明进行激活。

    符合条件的管理员在激活期间需要注册 Azure 多重身份验证 (MFA)。 如果用户无法注册 MFA 或使用 Microsoft 帐户（通常是 @outlook.com），则需要将其设置为永久充当其角色。

## <a name="make-a-role-assignment-permanent"></a>将角色分配设为永久

默认情况下，新用户仅适用于 Azure AD 管理员角色。 若要将某个角色分配设为永久，请执行以下步骤。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择“成员”。

    ![Azure AD 角色-显示角色和激活状态的成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 选择要使其成为永久角色的**合格**角色。

1. 选择 "**更多**"，然后选择 "**生成永久状态**"。

    ![一个窗格，其中列出了可用于打开更多菜单选项的角色的用户](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    该角色现在会列为**永久**角色。

    ![显示现在为永久角色和激活状态的成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>从角色中删除用户

可将用户从角色分配中删除，但始终必须至少保留一个永久的全局管理员用户。 如果不确定哪些用户仍然需要其角色分配，可以[启动角色的访问权限评审](pim-how-to-start-security-review.md)。

请按照以下步骤从 Azure AD 管理员角色中删除特定的用户。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择**Azure AD 角色**。

1. 选择“成员”。

    ![Azure AD 角色-显示角色和激活状态的成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 选择要删除的角色分配。

1. 选择 "**更多**"，然后**删除**。

    ![一个窗格，其中列出了具有 "更多" 菜单选项打开的永久角色的用户](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 当系统询问你确认操作时，请选择 **"是"** 。

    ![询问你是否要从角色中删除成员的消息](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    随即会删除该角色分配。

## <a name="authorization-error-when-assigning-roles"></a>分配角色时出现授权错误

方案：作为 Azure 资源的活动所有者或用户访问管理员，你可以查看 Privileged Identity Management 中的资源，但不能执行任何操作，例如，从资源概述页。 其中任何一个操作都会导致授权错误。

若要分配角色，必须在 azure 资源访问的基于 Azure 角色的访问控制中为 MS PIM 服务主体分配 "[用户访问管理员" 角色](../../role-based-access-control/built-in-roles.md#user-access-administrator)（与 Azure AD 管理角色相对）。 不需要等待 MS-PIM 被分配“用户访问管理员”角色，你可以手动分配该角色。

以下步骤将用户访问管理员角色分配给订阅的 MS PIM 服务主体。

1. 以 Azure AD 组织中的全局管理员身份登录到[Azure 门户](https://portal.azure.com)。

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

   ![显示 MS-PIM 的用户访问管理员角色分配的访问控制（IAM）边栏选项卡](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 管理员角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
