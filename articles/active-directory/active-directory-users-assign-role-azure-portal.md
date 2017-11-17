---
title: "在 Azure Active Directory 中向用户分配管理员角色 | Microsoft Docs"
description: "说明如何在 Azure Active Directory 中更改用户管理信息"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a1ca1a53-50d8-4bf0-ae8f-73fa1253e2d9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.openlocfilehash: 290f2e837cae9bdb95cf8acb486938632927ca8c
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2017
---
# <a name="assign-a-user-to-administrator-roles-in-azure-active-directory"></a>在 Azure Active Directory 中向用户分配管理员角色
本文介绍如何将管理角色分配给 Azure Active Directory (Azure AD) 中的用户。 有关添加组织中的新用户的信息，请参阅[将新用户添加到 Azure Active Directory](active-directory-users-create-azure-portal.md)。 默认情况下添加的用户没有管理员权限，但随时可以为其分配角色。

## <a name="assign-a-role-to-a-user"></a>向用户分配角色
1. 使用目录的全局管理员帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“用户和组”。

   ![打开“用户管理”](./media/active-directory-users-assign-role-azure-portal/create-users-user-management.png)
3. 选择“所有用户”。

   ![打开“所有用户”组](./media/active-directory-users-assign-role-azure-portal/create-users-open-users-blade.png)
1. 从列表中选择用户。
2. 对于所选用户，选择“目录角色”，并为用户分配“目录角色”列表中的一个角色。 有关用户和管理员角色的详细信息，请参阅[在 Azure AD 中分配管理员角色](active-directory-assign-admin-roles-azure-portal.md)。

      ![为用户分配角色](./media/active-directory-users-assign-role-azure-portal/create-users-assign-role.png)
6. 选择“保存”。

## <a name="next-steps"></a>后续步骤
* [添加用户](active-directory-users-create-azure-portal.md)
* [在新版 Azure 门户中重置用户的密码](active-directory-users-reset-password-azure-portal.md)
* [更改用户的工作信息](active-directory-users-work-info-azure-portal.md)
* [管理用户个人资料](active-directory-users-profile-azure-portal.md)
* [在 Azure AD 中删除用户](active-directory-users-delete-user-azure-portal.md)
