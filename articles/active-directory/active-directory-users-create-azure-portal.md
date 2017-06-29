---

title: "向 Azure Active Directory 添加新用户 | Microsoft Docs"
description: "说明如何在 Azure Active Directory 中添加新用户或更改用户信息。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: curtand;jeffsta
ms.reviewer: jeffsta
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 5f1f28b6e0135fbd76fa5f6664f457f259dd9dd4
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017



---
# <a name="add-new-users-to-azure-active-directory"></a>向 Azure Active Directory 添加新用户
> [!div class="op_single_selector"]
> * [Azure 门户](active-directory-users-create-azure-portal.md)
> * [Azure 经典门户](active-directory-create-users.md)
>
>

本文介绍如何在 Azure Active Directory (Azure AD) 中添加组织中的新用户。 

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“更多服务”，在文本框中输入“用户和组”，然后选择 **Enter**。

   ![打开用户和组](./media/active-directory-users-create-azure-portal/create-users-user-management.png)
3. 在“用户和组”边栏选项卡中，选择“所有用户”，然后选择“添加”。

   ![选择“添加”命令](./media/active-directory-users-create-azure-portal/create-users-add-command.png)
4. 输入用户的详细信息，如**名称**和**用户名**。 用户名的域名部分必须是初始默认域名“oo.onmicrosoft.com”或已验证的非联合域名（例如“contoso.com”）。
5. 复制或以其他方式记下生成的用户密码，以便在此过程完成后可以提供给用户。
6. （可选）可以打开“个人资料”边栏选项卡、“组”边栏选项卡或“目录角色”边栏选项卡并在其中填写用户信息。 有关用户和管理员角色的详细信息，请参阅[在 Azure AD 中分配管理员角色](active-directory-assign-admin-roles.md)。
7. 在“用户”边栏选项卡中，选择“创建”。
8. 以安全方式将生成的密码分发给新用户，以便用户可以登录。

### <a name="next-steps"></a>后续步骤
* [添加外部用户](active-directory-users-create-external-azure-portal.md)
* [在新版 Azure 门户中重置用户的密码](active-directory-users-reset-password-azure-portal.md)
* [更改用户的工作信息](active-directory-users-work-info-azure-portal.md)
* [管理用户个人资料](active-directory-users-profile-azure-portal.md)
* [在 Azure AD 中删除用户](active-directory-users-delete-user-azure-portal.md)
* [在 Azure AD 中为用户分配角色](active-directory-users-assign-role-azure-portal.md)

