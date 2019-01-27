---
title: 在 PIM 中分配 Azure AD 目录角色 | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中分配 Azure AD 目录角色。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 10/30/2018
ms.author: rolyon
ms.openlocfilehash: 69bc2bb5fa06f4edc0283961063d2302c7905b4f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438994"
---
# <a name="assign-azure-ad-directory-roles-in-pim"></a>在 PIM 中分配 Azure AD 目录角色

使用 Azure Active Directory (Azure AD)，全局管理员可以完成**永久性的**目录角色分配。 可以使用 [Azure 门户](../users-groups-roles/directory-assign-admin-roles.md)或 [PowerShell 命令](/powershell/module/azuread#directory_roles)创建这些角色分配。

Azure AD Privileged Identity Management (PIM) 服务还允许特权角色管理员完成永久性的目录角色分配。 此外，特权角色管理员能使用户**符合**目录角色的条件。 符合条件的管理员可在需要时激活角色，在完成任务后，其权限随即失效。

## <a name="make-a-user-eligible-for-a-role"></a>使用户符合角色的条件

遵循以下步骤可使用户符合 Azure AD 目录角色的条件。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成员用户的身份登录到 [Azure 门户](https://portal.azure.com/)。

    有关如何授予其他管理员访问权限以管理 PIM 的信息，请参阅[授予其他管理员访问权限以管理 PIM](pim-how-to-give-access-to-pim.md)。

1. 打开“Azure AD Privileged Identity Management”。

    如果尚未在 Azure 门户中启动 PIM，请转到[开始使用 PIM](pim-getting-started.md)。

1. 单击“Azure AD 目录角色”。

1. 单击“角色”或“成员”。

    ![Azure AD 目录角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 单击“添加成员”打开“添加受管理成员”。

1. 依次单击“选择角色”、要管理的角色、“选择”。

    ![选择角色](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 单击“选择成员”，选择要向其分配角色的用户，然后单击“选择”。

    ![选择角色](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在“添加受管理成员”中，单击“确定”将该用户添加到角色。

1. 在角色列表中，单击刚刚分配的角色以查看成员列表。

     分配角色后，选择的用户将显示在**符合**该角色条件的成员列表中。

    ![符合角色条件的用户](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 用户符合角色的条件后，请告诉他们，可以根据[在 PIM 中激活 Azure AD 目录角色](pim-how-to-activate-role.md)中的说明来激活该角色。

    符合条件的管理员在激活期间需要注册 Azure 多重身份验证 (MFA)。 如果用户无法注册 MFA 或使用 Microsoft 帐户（通常是 @outlook.com），则需要将其设置为永久充当其角色。

## <a name="make-a-role-assignment-permanent"></a>将角色分配设为永久

默认情况下，新用户只符合目录角色的条件。 若要将某个角色分配设为永久，请执行以下步骤。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 目录角色”。

1. 单击“成员”。

    ![成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 单击要设为永久的**符合条件**的角色。

1. 依次单击“更多”、“设为永久”。

    ![将角色分配设为永久](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    该角色现在会列为**永久**角色。

    ![已做出永久性更改的成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>从角色中删除用户

可将用户从角色分配中删除，但始终必须至少保留一个永久的全局管理员用户。 如果不确定哪些用户仍然需要其角色分配，可以[启动角色的访问权限评审](pim-how-to-start-security-review.md)。

遵循以下步骤从目录角色中删除特定的用户。

1. 打开“Azure AD Privileged Identity Management”。

1. 单击“Azure AD 目录角色”。

1. 单击“成员”。

    ![成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 单击要删除的角色分配。

1. 依次单击“更多”、“删除”。

    ![删除角色](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 在要求确认的消息中，单击“是”。

    ![删除角色](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    随即会删除该角色分配。

## <a name="authorization-error-when-assigning-roles"></a>分配角色时出现授权错误

如果你最近为某个订阅启用了 PIM 并且在尝试使某个用户获得某个目录角色时出现授权错误，则可能是因为 MS-PIM 服务主体尚未具有合适的权限。 MS-PIM 服务主体必须具有[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色才能向其他人分配角色。 不需要等待 MS-PIM 被分配“用户访问管理员”角色，你可以手动分配该角色。

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

   ![为 MS PIM 添加权限](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. 选择“保存”以分配角色。

   过一会后，MS-PIM 服务主体将分配有在订阅范围内的“用户访问管理员”角色。

   ![MS-PIM 的“用户访问管理员”角色](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>后续步骤

- [在 PIM 中配置 Azure AD 目录角色设置](pim-how-to-change-default-settings.md)
- [在 PIM 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)
