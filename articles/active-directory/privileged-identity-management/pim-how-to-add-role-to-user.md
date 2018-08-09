---
title: 使用 Azure AD PIM 将目录角色分配给用户 | Microsoft Docs
description: 了解如何使用 Azure Active Directory Privileged Identity Management 和 Azure 门户将目录角色分配给用户。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.openlocfilehash: 1aede38cabba7f9811f2b9320bc1e9a9da857f08
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621807"
---
# <a name="assign-directory-roles-to-users-using-azure-ad-pim"></a>使用 Azure AD PIM 将目录角色分配给用户

使用 Azure Active Directory (Azure AD)，全局管理员可以完成**永久性的**目录角色分配。 可以使用 [Azure 门户](../users-groups-roles/directory-assign-admin-roles.md)或 [PowerShell 命令](/powershell/module/azuread#directory_roles)创建这些角色分配。

Azure AD Privileged Identity Management (PIM) 服务还允许特权角色管理员完成永久性的目录角色分配。 此外，特权角色管理员能使用户**符合**目录角色的条件。 符合条件的管理员可在需要时激活角色，在完成任务后，其权限随即失效。 有关可以使用 PIM 管理的角色的信息，请参阅[可以使用 Azure AD PIM 管理的目录角色](pim-roles.md)。

## <a name="make-a-user-eligible-for-a-role"></a>使用户符合角色的条件

遵循以下步骤可使用户符合 Azure AD 目录角色的条件。

1. 使用[特权角色管理员](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色成员用户的身份登录到 [Azure 门户](https://portal.azure.com/)。

    有关如何向另一个用户授予 PIM 管理访问权限的信息，请参阅[如何授予 PIM 的访问权限](pim-how-to-give-access-to-pim.md)。

1. 打开“Azure AD Privileged Identity Management”。

    如果尚未在 Azure 门户中启用 PIM，请参阅 [Azure AD PIM 入门](pim-getting-started.md)。

1. 单击“Azure AD 目录角色”。

1. 单击“角色(预览版)”或“成员”。

    ![Azure AD 目录角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 单击“添加成员”打开“添加受管理成员”。

1. 依次单击“选择角色”、要管理的角色、“选择”。

    ![选择角色](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 单击“选择成员”，选择要向其分配角色的用户，然后单击“选择”。

    ![选择角色](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在“添加受管理成员”中，单击“确定”将该用户添加到角色。

     分配角色后，选择的用户将显示在**符合**该角色条件的成员列表中。

    ![符合角色条件的用户](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 使用户符合角色的条件后，请告诉他们，可以根据[如何激活或停用角色](pim-how-to-activate-role.md)中的说明来激活该角色。

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

## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
