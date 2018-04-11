---
title: 在 Azure 门户中添加 B2B 协作用户 - Azure Active Directory | Microsoft Docs
description: 介绍如何使用 Azure Active Directory (Azure AD) B2B 协作将来宾用户从合作伙伴组织添加到其目录。
services: active-directory
documentationcenter: ''
author: twooley
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/02/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 34bd5b51089045c4cd20f29d179bb230e5e3fac2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>在 Azure 门户中添加 Azure Active Directory B2B 协作用户

全局管理员或者获得了任何有限管理员目录角色的用户可以使用 Azure 门户邀请 B2B 协作用户。 可将来宾用户邀请到目录、组或应用程序。 通过上述任一方法邀请用户后，受邀用户的帐户将添加到 Azure Active Directory (Azure AD)，其用户类型为“来宾”。 然后，来宾用户必须兑换其邀请才能访问资源。

## <a name="add-guest-users-to-the-directory"></a>将来宾用户添加到目录

若要将 B2B 协作用户添加到目录，请执行以下步骤：

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在导航窗格中选择“Azure Active Directory”。
3. 在“管理”下，选择“用户和组” > “所有用户”。
4. 选择“新来宾用户”。

   ![显示“新来宾用户”在 UI 中的位置](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. 在“邀请来宾”下，输入外部用户的电子邮件地址。 （可选）包含一条欢迎消息。 例如：

   ![显示“新来宾用户”在 UI 中的位置](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. 选择“邀请”，以自动向来宾用户发送邀请。 在“通知”区域中，找到“已成功邀请用户”消息。 
 
发送邀请后，该用户帐户将以来宾的形式自动添加到目录。


![显示“来宾”用户类型的 B2B 用户](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>将来宾用户添加到组
如果需要以 Azure AD 管理员的身份手动将 B2B 协作用户添加到组，请执行以下步骤：

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在导航窗格中选择“Azure Active Directory”。
3. 在“管理”下，选择“用户和组” > “所有组”。
4. 选择一个组（或单击“新建组”创建一个新组）。 最好是输入组说明，指出该组包含 B2B 来宾用户。
5. 选择“成员” > “添加成员”。 
6. 执行下列操作之一：
   - 如果目录中已存在该来宾用户，请搜索 B2B 用户。 选择该用户，单击“选择”将该用户添加到组中。
   - 如果目录中不存在该来宾用户，请选择“邀请”。
   ![添加邀请按钮以添加来宾成员](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      在“邀请来宾”下输入电子邮件地址和可选的个人消息，然后选择“邀请”。 单击“选择”将该用户添加到组中。

      邀请会自动发送到受邀用户。 在“通知”区域中，找到“已成功邀请用户”消息。 

还可以结合 Azure AD B2B 协作使用动态组。 有关详细信息，请参阅[动态组和 Azure Active Directory B2B 协作](active-directory-b2b-dynamic-groups.md)。

## <a name="add-guest-users-to-an-application"></a>将来宾用户添加到应用程序

若要以 Azure AD 管理员的身份将 B2B 协作用户添加到应用程序，请执行以下步骤：

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在导航窗格中选择“Azure Active Directory”。
3. 在“管理”下，选择“企业应用程序” > “所有应用程序”。
4. 选择要将来宾用户添加到的应用程序。
5. 在“管理”下，选择“用户和组”。
6. 选择“添加用户”。
7. 在“添加分配”下，选择“用户和组”。
8. 执行下列操作之一：
   - 如果目录中已存在该来宾用户，请搜索 B2B 用户。 选择该用户，单击“选择”将该用户添加到应用中。
   - 如果目录中不存在该来宾用户，请选择“邀请”。
   ![添加邀请按钮以添加来宾成员](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      在“邀请来宾”下输入电子邮件地址和可选的个人消息，然后选择“邀请”。 单击“选择”将该用户添加到应用中。

      邀请会自动发送到受邀用户。 在“通知”区域中，找到“已成功邀请用户”消息。

9. 在“添加分配”下，单击“选择角色”，选择要将选定用户应用到的角色（如果适用），然后选择“确定”。
10. 单击“分配”。
 
## <a name="resend-invitations-to-guest-users"></a>向来宾用户重新发送邀请

如果来宾用户尚未兑换其邀请，你可以重新发送邀请。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在导航窗格中选择“Azure Active Directory”。
3. 在“管理”下，选择“用户和组”。
4. 选择“所有用户”。
5. 选择用户帐户。
6. 在“管理”下，选择“配置文件”。
7. 如果该用户尚未接受邀请，则会显示“重新发送邀请”选项。 选择此按钮可以重新发送邀请。

   ![用户配置文件中的“重新发送邀请”选项](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> 请注意，如果重新发送的邀请最初将用户定向到了特定的应用，新邀请中的链接会使该用户转到顶级访问面板。

## <a name="next-steps"></a>后续步骤

- 若要了解非 Azure AD 管理员如何添加 B2B 来宾用户，请参阅[信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
- 有关邀请电子邮件的信息，请参阅 [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)。
- 有关邀请兑换过程的信息，请参阅 [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)。


