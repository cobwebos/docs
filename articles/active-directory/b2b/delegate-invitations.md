---
title: 启用 B2B 外部协作设置 - Azure AD
description: 了解如何启用 Active Directory B2B 外部协作，并管理谁可以邀请来宾用户。 使用“来宾邀请者”角色来委托邀请。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f69069b0c62f1e96c16441e99fbadac45728743
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199429"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>启用 B2B 外部协作并管理谁可以邀请来宾

本文介绍如何启用 Azure Active Directory (Azure AD) B2B 协作并确定谁可以邀请来宾。 默认情况下，目录中的所有用户和来宾都可以邀请来宾，即使未为他们分配管理员角色。 使用外部协作设置可为组织中不同类型的用户启用或禁用来宾邀请功能。 还可以将邀请委托给个人用户，只需向他们分配有权邀请来宾的角色即可。

## <a name="configure-b2b-external-collaboration-settings"></a>配置 B2B 外部协作设置

使用 Azure AD B2B 协作，租户管理员可以设置以下邀请策略：

- 关闭邀请
- 只有管理员和具有“来宾邀请者”角色的用户可以邀请
- 管理员、“来宾邀请者”角色和成员可以邀请
- 所有用户（包括来宾）都可以邀请

默认情况下，所有用户（包括来宾）都可以邀请来宾用户。

### <a name="to-configure-external-collaboration-settings"></a>若要配置外部协作设置，请执行以下操作：

1. 以租户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory”  。
3. 选择**组织关系**  >  **设置**（或选择**外部标识**  >  **外部协作设置**）。
6. 在“外部协作设置”页上，选择要启用的策略  。

   ![外部协作设置](./media/delegate-invitations/control-who-to-invite.png)

  - **来宾用户权限处于限制状态**：此策略确定目录中来宾的权限。 选择“是”会阻止来宾执行某些目录任务，例如枚举用户、组或其他目录资源  。 选择“否”会向来宾授予与目录中普通用户相同的目录数据访问权限  。
   - **管理员和具有“来宾邀请者”角色的用户可以邀请**：若要允许充当“来宾邀请者”角色的管理员和用户邀请来宾，请将此策略设置为“是”  。
   - **成员可以邀请**：若要允许目录的非管理员成员邀请来宾，请将此策略设置为“是”  。
   - **来宾可以邀请**：若要允许来宾邀请其他来宾，请将此策略设置为“是”  。
   - 为**来宾启用电子邮件一次性密码（预览版）**：有关一次性密码功能的详细信息，请参阅[电子邮件一次性密码身份验证（预览版）](one-time-passcode.md)。
   - **协作限制**：若要详细了解如何允许或阻止向特定的域发送邀请，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](allow-deny-list.md)。

## <a name="assign-the-guest-inviter-role-to-a-user"></a>将“来宾邀请者”角色分配给用户

“来宾邀请者”角色可让个人用户邀请来宾，无需向他们分配全局管理员角色或其他管理员角色。 将“来宾邀请者”角色分配给个人。 然后，确保将“管理员和具有‘来宾邀请者’角色的用户可以邀请”设置为“是”   。

下面是一个示例，它展示了如何使用 PowerShell 将用户添加到“来宾邀请者”角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在没有邀请的情况下添加 B2B 协作来宾用户](add-user-without-invite.md)
- [将 B2B 协作用户添加到角色](add-guest-to-role.md)


