---
title: "如何添加或删除用户角色 | Microsoft 文档"
description: "了解如何使用 Azure Active Directory Privileged Identity Management 应用程序将角色添加到特权标识。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 6a47ced8-cf34-4ce8-bea2-e4fc548cfe22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5dde6b98f72306d6fe78f3c12377d3ce2ec5ddea


---
# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure AD Privileged Identity Management：如何添加或删除用户角色
全局管理员（或公司管理员）可以使用 Azure Active Directory (AD) 来更新要将哪些用户**永久**分配到 Azure AD 中的角色。 为此，可以使用 `Add-MsolRoleMember` 和 `Remove-MsolRoleMember` 等 PowerShell cmdlet。 或者，可以根据[在 Azure Active Directory 中分配管理员角色](active-directory-assign-admin-roles.md)中所述使用 Azure 经典门户。

Azure AD Privileged Identity Management 应用程序还允许特权角色管理员分配永久的角色。 此外，特权角色管理员可将用户设置为管理员角色的**合格**用户。 合格管理员可在需要时激活角色，在完成任务后，其权限随即失效。

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>在 Azure 门户使用 PIM 管理角色
在组织中，可以将用户分配到 Azure AD、Office 365 及其他 Microsoft 服务和应用程序中不同的管理角色。  有关可用角色的详细信息，请参阅 [Azure AD PIM 中的角色](active-directory-privileged-identity-management-roles.md)。

若要使用 Privileged Identity Management 添加或删除充当某个角色的用户，请打开 PIM 仪表板。 然后单击“充当管理员角色的用户”按钮，或从角色表中选择特定的角色（例如“全局管理员”）。

> [!NOTE]
> 如果尚未在 Azure 门户中启用 PIM，请转到 [Azure AD PIM 入门](active-directory-privileged-identity-management-getting-started.md)了解详细信息。
> 
> 

如果想要授权其他用户访问 PIM 本身，请参阅 [how to give access to PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)（如何授予对 PIM 的访问权限），其中进一步说明了 PIM 要求该用户必须拥有哪些角色。

## <a name="add-a-user-to-a-role"></a>将用户添加到角色
1. 在 [Azure 门户](https://portal.azure.com/)中的仪表板上选择“Azure AD Privileged Identity Management”磁贴。
2. 选择“管理特权角色”。
3. 在“角色摘要”表格中，选择想要管理的角色。
4. 在“角色”边栏选项卡中，选择“添加”。
5. 单击“选择用户”，然后在“选择用户”边栏选项卡上搜索该用户。  
6. 从搜索结果列表中选择该用户，然后单击“完成”。
7. 单击“确定”保存选择内容。 选定的用户将在列表中显示为该角色的合格用户。

> [!NOTE]
> 充当某个角色的新用户默认仅为该角色的合格用户。 如果想要让该用户永久充当该角色，请在列表中单击该用户。 该用户的信息随即显示在新的边栏选项卡中。 在用户信息菜单中，选择“设为永久”。  
> 如果用户无法注册 Azure 多重身份验证 (MFA) 或使用 Microsoft 帐户（通常是 @outlook.com),，则需要将他们充当的所有角色设为永久。 系统将要求合格的管理员在激活期间注册 MFA。
> 
> 

使用户符合某个角色的资格后，请告诉他们，可以根据[如何激活或停用角色](active-directory-privileged-identity-management-how-to-activate-role.md)中的说明来激活该角色。

## <a name="remove-a-user-from-a-role"></a>从角色中删除用户
可以将用户从合格角色分配中删除，但始终必须至少保留一个永久的全局管理员用户。

遵循以下步骤从角色中删除特定的用户：

1. 在 Azure AD PIM 仪表板中选择一个角色，或单击“充当管理员角色的用户”按钮，导航到角色列表中的该角色。
2. 在用户列表中单击该用户。
3. 单击“删除”。 此时会出现一条请求确认的消息。
4. 单击“是”即可删除该用户的该角色。

如果不确定哪些用户仍然需要其角色分配，可以[启动角色的访问权限评审](active-directory-privileged-identity-management-how-to-start-security-review.md)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]




<!--HONumber=Nov16_HO3-->


