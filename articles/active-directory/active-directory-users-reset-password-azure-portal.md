---
title: "Azure Active Directory 中的重置密码 | Microsoft 文档"
description: "管理员在 Azure Active Directory 中为用户重置密码"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: bea082081e3f3f52ba78188903a9536fe9de9392
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>在 Azure Active Directory 中重置用户的密码

在用户忘记密码或用户被锁定等情况下，管理员可能需要重置用户的密码。 可通过以下步骤完成用户密码重置。

## <a name="how-to-reset-the-password-for-a-user"></a>如何重置用户的密码

1. 使用具有目录权限的帐户登录 [Azure AD 管理员中心](https://aad.portal.azure.com)以重置用户密码。
2. 选择“Azure Active Directory” > “用户和组” > “所有用户”。
3. 选择要为其重置密码的用户。
2. 对于所选用户，选择“重置密码”。

    ![在 Azure AD 中，通过用户配置文件为用户重置密码](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. 在“重置密码”上，选择“重置密码”。
7. 会显示一个临时密码，可将其提供给用户。 下一次登录时将要求用户更改密码。 

   > [!NOTE]
   > 此临时密码不具有过期时间，因此在用于登录之前将始终有效，登录后将强制用户更改密码。 

## <a name="next-steps"></a>后续步骤
* [添加用户](active-directory-users-create-azure-portal.md)
* [向用户分配管理员角色](active-directory-users-assign-role-azure-portal.md)
* [管理用户个人资料](active-directory-users-profile-azure-portal.md)
* [在 Azure AD 中删除用户](active-directory-users-delete-user-azure-portal.md)
