---
title: 启用 B2B 外部协作设置-Azure AD
description: 了解如何启用 Active Directory B2B 外部协作，以及如何管理可以邀请来宾用户的用户。 使用来宾邀请者角色委托邀请。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fb10863334392b207c7cfd2172dc9260cf15e2d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74272902"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>启用 B2B 外部协作并管理谁可以邀请来宾

本文介绍如何启用 Azure Active Directory （Azure AD） B2B 协作并确定谁可以邀请来宾。 默认情况下，目录中的所有用户和来宾都可以邀请来宾，即使这些用户未分配到管理员角色也是如此。 外部协作设置使你可以为组织中的不同类型的用户打开或关闭来宾邀请。 你还可以通过分配允许用户邀请来宾的角色将邀请委托给各个用户。

## <a name="configure-b2b-external-collaboration-settings"></a>配置 B2B 外部协作设置

使用 Azure AD B2B 协作，租户管理员可以设置以下邀请策略：

- 关闭邀请
- 只有管理员和具有“来宾邀请者”角色的用户可以邀请
- 管理员、“来宾邀请者”角色和成员可以邀请
- 所有用户（包括来宾）都可以邀请

默认情况下，所有用户（包括来宾）都可以邀请来宾用户。

### <a name="to-configure-external-collaboration-settings"></a>配置外部协作设置的步骤：

1. 以租户管理员身份登录到[Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory” **“用户”** “用户设置”。 >  > 
3. 在“外部用户”下，选择“管理外部协作设置”。
   > [!NOTE]
   > “外部协作设置”也可从“组织关系”页获得。 在 Azure Active Directory 中的“管理”下，转到“组织关系” > “设置”。
4. 在 "**外部协作设置**" 页上，选择要启用的策略。

   ![外部协作设置](./media/delegate-invitations/control-who-to-invite.png)

  - **Guest 用户权限受到限制**：此策略确定目录中来宾的权限。 选择 **"是"** 以阻止来自某些目录任务的来宾，如枚举用户、组或其他目录资源。 选择 "**否**" 可为来宾授予目录中的普通用户访问目录数据的相同权限。
   - **管理员和来宾邀请者角色中的用户可以邀请**：若要允许 "来宾邀请者" 角色中的管理员和用户邀请来宾，请将此策略设置为 **"是"** 。
   - **成员可以邀请**：若要允许非管理员的目录成员邀请来宾，请将此策略设置为 **"是"** 。
   - **来宾可以邀请**：若要允许来宾邀请其他来宾，请将此策略设置为 **"是"** 。
   - 为**来宾启用电子邮件一次性密码（预览版）** ：有关一次性密码功能的详细信息，请参阅[电子邮件一次性密码身份验证（预览版）](one-time-passcode.md)。
   - **协作限制**：有关允许或阻止特定域的邀请的详细信息，请参阅[允许或阻止来自特定组织的 B2B 用户的邀请](allow-deny-list.md)。

## <a name="assign-the-guest-inviter-role-to-a-user"></a>将来宾邀请者角色分配给用户

使用来宾邀请者角色，你可以为单个用户提供邀请来宾的功能，而无需向他们分配全局管理员或其他管理员角色。 将来宾邀请者角色分配给个人。 然后，请确保设置**管理员和来宾邀请者角色中的用户可以邀请**为 **"是"** 。

下面是一个示例，它展示了如何使用 PowerShell 将用户添加到“来宾邀请者”角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在没有邀请的情况下添加 B2B 协作来宾用户](add-user-without-invite.md)
- [向角色添加 B2B 协作用户](add-guest-to-role.md)


