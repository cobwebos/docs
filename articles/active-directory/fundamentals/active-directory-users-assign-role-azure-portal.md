---
title: 向用户分配 Azure AD 角色-Azure Active Directory |Microsoft Docs
description: 有关如何为具有 Azure Active Directory 的用户分配管理员和非管理员角色的说明。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/15/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a468b5f43e8b024dd1da6e382049f8cce6de295
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797359"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>为具有 Azure Active Directory 的用户分配管理员和非管理员角色

在 Azure Active Directory (Azure AD) 中，如果某个用户需要管理 Azure AD 资源的权限，则必须将其分配给提供所需权限的角色。 有关哪些角色管理 Azure 资源以及哪些角色管理 Azure AD 资源的信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 角色](../../role-based-access-control/rbac-and-directory-admin-roles.md)。

有关可用 Azure AD 角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)。 若要添加用户，请参阅[将新用户添加到 Azure Active Directory](add-users-azure-active-directory.md)。

## <a name="assign-roles"></a>分配角色

向用户分配 Azure AD 角色的常见方法是在用户的 "**分配的角色**" 页上。 你还可以使用 Privileged Identity Management (PIM) 向角色提升实时提升的资格。 有关如何使用 PIM 的详细信息，请参阅[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)。

> [!Note]
> 如果有 Azure AD Premium P2 许可计划并已使用 PIM，则所有角色管理任务都将在[Privileged Identity Management 体验](../users-groups-roles/directory-manage-roles-portal.md)中执行。
>
> ![为已使用 PIM 并具有高级 P2 许可证的用户在 PIM 中管理 Azure AD 角色](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>向用户分配角色

1. 中转到[Azure 门户](https://portal.azure.com/)，并使用目录的全局管理员帐户登录。

2. 搜索并选择“Azure Active Directory”。

      ![在 Azure 门户中搜索 Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. 选择“用户”。

4. 搜索并选择获得角色分配的用户。 例如，_Alain Charon_。

      ![“所有用户”页 - 选择用户](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. 在“Alain Charon - 个人资料”**** 页上，选择“分配的角色”。****

    此时将显示 " **Alain Charon-管理角色**" 页。

6. 选择 "**添加分配**"，选择要分配给 Alain 的角色 (例如，"_应用程序管理员_") ，然后选择 "**选择**"。

    ![“分配的角色”页 - 显示所选角色](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    "应用程序管理员" 角色分配给 Alain Charon，它显示在 " **Alain Charon-管理角色**" 页上。

## <a name="remove-a-role-assignment"></a>删除角色分配

如果需要从用户中删除角色分配，也可以从**Alain Charon-管理角色**页面执行此操作。

### <a name="to-remove-a-role-assignment-from-a-user"></a>从用户删除角色分配

1. 选择“Azure Active Directory”，选择“用户”，然后搜索并选择删除分配了角色的用户********。 例如，_Alain Charon_。

2. 选择“分配的角色”，选择“应用程序管理员”，然后选择“删除分配”。************

    ![“分配的角色”页，其中显示了所选角色和删除选项](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    将从 Alain Charon 中删除应用程序管理员角色，该角色将不再显示在 " **Alain Charon-管理角色**" 页上。

## <a name="next-steps"></a>后续步骤

- [添加或删除用户](add-users-azure-active-directory.md)

- [添加或更改个人资料信息](active-directory-users-profile-azure-portal.md)

- [添加另一个目录中的来宾用户](../b2b/what-is-b2b.md)

可以查看的其他用户管理任务在[Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)中提供。
