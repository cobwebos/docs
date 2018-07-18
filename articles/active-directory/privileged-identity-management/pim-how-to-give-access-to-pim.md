---
title: 如何授予对 Privileged Identity Management 的访问权限 - Azure | Microsoft 文档
description: 了解如何使用 Azure Active Directory Privileged Identity Management 扩展将角色添加到用户，使其能够管理 PIM。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: protection
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d7f5e5aeba522ca75bba7c74d62147a3ff5edd95
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723309"
---
# <a name="giving-access-to-manage-azure-ad-privileged-identity-management"></a>授予 Azure AD Privileged Identity Management 的管理访问权限
为组织启用 Azure AD Privileged Identity Management (PIM) 的全局管理员会自动获得角色分配和 PIM 的访问权限。 但是，默认情况下，没有任何其他人会获得写入访问权限，包括其他全局管理员。 其他全局管理员、安全管理员和安全读者拥有 Azure AD PIM 的只读访问权限。 要授予对 PIM 的访问权限，第一位用户可以将其他用户分配到“特权角色管理员”角色。

> [!NOTE]
> 管理 Azure AD PIM 需要 Azure MFA。 由于 Microsoft 帐户无法注册 Azure MFA，因此使用 Microsoft 帐户登录的用户无法访问 Azure AD PIM。
> 
> 

请确保特权角色管理员角色中始终至少有两位用户，以防其中一位用户被锁定或帐户被删除。

## <a name="give-another-user-access-to-manage-pim"></a>向另一位用户授予 PIM 的管理访问权限
1. 登录到 [Azure 门户](https://portal.azure.com/)，并在仪表板上选择 **Azure AD Privileged Identity Management** 应用。
2. 选择“管理特权角色” > “特权角色管理员” > “添加”。
   
    ![添加特权角色管理员 - 屏幕截图](./media/pim-how-to-give-access-to-pim/PIM_add_PRA.png)
3. 在“添加托管用户”边栏选项卡上，步骤 1 已完成。 请选择步骤 2“选择用户”，并搜索要添加的用户。
   
    ![选择用户 - 屏幕截图](./media/pim-how-to-give-access-to-pim/PIM_select_users.png)
4. 从搜索结果中选择该用户，并单击“完成”。
5. 单击“确定”保存选择内容。 选定的用户将出现在“特权角色管理员”列表中。
   
   * 每当向某位用户分配新角色时，系统会自动将其设置有资格激活该角色。 如果想要让这些用户永久充当该角色，请在列表中单击该用户。 在用户信息菜单中，选择“设为永久”。
6. 将 [Azure AD Privileged Identity Management 入门](pim-getting-started.md) 链接发送给用户。

## <a name="remove-another-users-access-rights-for-managing-pim"></a>删除另一位用户的 PIM 管理访问权限
从特权角色管理员角色中删除某人之前，请确保仍有两位用户分配有该角色。

1. 在 PIM 仪表板中，单击“特权角色管理员”角色。  此时会显示当前充当该角色的用户列表。
2. 在用户列表中单击该用户。
3. 单击“删除”。  此时会显示一条确认消息。
4. 单击“是”即可从角色中删除该用户。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]
