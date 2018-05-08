---
title: 在 Azure Active Directory 中向用户分配管理员角色 | Microsoft Docs
description: 说明如何在 Azure Active Directory 中更改用户管理信息
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 61662acb10a6f2085d297eae473e1330c619d48d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中向用户分配管理员角色
本文介绍如何将管理角色分配给 Azure Active Directory (Azure AD) 中的用户。 有关添加组织中的新用户的信息，请参阅[将新用户添加到 Azure Active Directory](active-directory-users-create-azure-portal.md)。 默认情况下添加的用户没有管理员权限，但随时可以为其分配角色。

## <a name="assign-a-role-to-a-user"></a>向用户分配角色
1. 使用目录的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“用户和组”。

   ![打开“用户管理”](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. 选择“所有用户”。
  
  ![打开“所有用户”组](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
4. 从列表中选择用户。
5. 对于所选用户，选择“目录角色”，并为用户分配“目录角色”列表中的一个角色。 有关用户和管理员角色的详细信息，请参阅[在 Azure AD 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md)。

      ![为用户分配角色](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. 选择“保存”。

## <a name="next-steps"></a>后续步骤
* [快速入门：在 Azure Active Directory 中添加或删除用户](add-users-azure-active-directory.md)
* [管理用户个人资料](active-directory-users-profile-azure-portal.md)
* [添加另一个目录中的来宾用户](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [在 Azure AD 中为用户分配角色](active-directory-users-assign-role-azure-portal.md)
* [还原已删除的用户](active-directory-users-restore.md)
