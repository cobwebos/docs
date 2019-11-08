---
title: 向用户分配目录角色 - Azure Active Directory | Microsoft Docs
description: 有关如何为具有 Azure Active Directory 的用户分配管理员和非管理员角色的说明。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d071ff071c13637b15479d86a699d0b368119196
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742601"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>为具有 Azure Active Directory 的用户分配管理员和非管理员角色
如果组织中的用户需要管理 Azure Active Directory (Azure AD) 资源的权限，则必须根据用户需要权限才能执行的操作为 Azure AD 中的用户分配适当的角色。

有关可用角色的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](../users-groups-roles/directory-assign-admin-roles.md)。 有关添加用户的详细信息，请参阅[将新用户添加到 Azure Active Directory](add-users-azure-active-directory.md)。

## <a name="assign-roles"></a>分配角色
常用方法是在用户的“目录角色”页面上将 Azure AD 角色分配给用户。

还可以使用 Privileged Identity Management (PIM) 分配角色。 了解如何使用 PIM 的详细信息，请参阅 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)。

### <a name="to-assign-a-role-to-a-user"></a>向用户分配角色
1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“Azure Active Directory”，选择“用户”，然后搜索并选择要获得角色分配的用户。 例如，Alain Charon。

      !["所有用户" 页-选择用户](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

3. 在 " **Alain Charon-配置文件**" 页上，选择 "**分配的角色**"。

    将出现“Alain Charon - 目录角色”页面。

4. 选择 "**添加分配**"，选择要分配给 Alain 的角色（例如，_应用程序管理员_），然后选择 "**选择**"。

    !["分配的角色" 页-显示所选角色](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    应用程序管理员角色分配给 Alain Charon，显示在“Alain Charon - 目录角色”页面上。

## <a name="remove-a-role-assignment"></a>删除角色分配
如果需要从用户删除角色分配，也可从“Alain Charon - 目录角色”页面执行此操作。

### <a name="to-remove-a-role-assignment-from-a-user"></a>从用户删除角色分配

1. 选择“Azure Active Directory”，选择“用户”，然后搜索并选择删除分配了角色的用户。 例如，Alain Charon。

2. 选择 "**分配的角色**"，选择 "**应用程序管理员**"，然后选择 "**删除分配**"。

    !["分配的角色" 页，显示所选角色和删除选项](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    “应用程序管理员”角色将从 Alain Charon 中删除，并且不再显示在“Alain Charon - 目录角色”页面上。

## <a name="next-steps"></a>后续步骤
- [添加或删除用户](add-users-azure-active-directory.md)

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)

- [添加另一个目录中的来宾用户](../b2b/what-is-b2b.md)

也可以执行其他用户管理任务，例如分配委托、使用策略以及共享用户帐户。 有关其他可用操作的详细信息，请参阅 [Azure Active Directory 用户管理文档](../users-groups-roles/index.yml)。


