---
title: 启用 B2B 外部协作设置 - Azure AD
description: 了解如何启用 Active Directory B2B 外部协作，以及如何管理可以邀请来宾用户的人员。 使用“来宾邀请者”角色委托邀请。
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
ms.openlocfilehash: cf872991adbf1361a482f6618e57304e33ec1261
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591278"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>启用 B2B 外部协作并管理可以邀请来宾的人员

本文介绍如何启用 Azure Active Directory (Azure AD) B2B 协作并确定可以邀请来宾的人员。 默认情况下，目录中的所有用户和来宾都可以邀请来宾，即使未将他们分配给管理员角色也是如此。 通过外部协作设置，可以为组织中不同类型的用户打开或关闭来宾邀请。 你还可以通过分配允许用户邀请来宾的角色将邀请委托给各个用户。

## <a name="configure-b2b-external-collaboration-settings"></a>配置 B2B 外部协作设置

使用 Azure AD B2B 协作，租户管理员可以设置以下邀请策略：

- 关闭邀请
- 只有管理员和具有“来宾邀请者”角色的用户可以邀请
- 管理员、“来宾邀请者”角色和成员可以邀请
- 所有用户（包括来宾）都可以邀请

默认情况下，所有用户（包括来宾）都可以邀请来宾用户。

### <a name="to-configure-external-collaboration-settings"></a>若要配置外部协作设置，请执行以下操作：

1. 以租户管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“Azure Active Directory”。
3. 选择“外部标识” > “外部协作设置”。
6. 在“外部协作设置”页上，选择要启用的策略。

   ![外部协作设置](./media/delegate-invitations/control-who-to-invite.png)

  - 来宾用户权限处于限制状态：此策略确定目录中来宾的权限。 选择“是”可阻止来宾执行某些目录任务，如枚举用户、组或其他目录资源。 选择“否”可使来宾获得与目录中的常规用户相同的目录数据访问权限。
   - 管理员和具有“来宾邀请者”角色的用户可以邀请：若要允许管理员和具有“来宾邀请者”角色的用户邀请来宾，请将此策略设置为“是”。
   - 成员可邀请：若要允许目录的非管理员成员邀请来宾，请将此策略设置为“是”。
   - 来宾可邀请：若要允许来宾邀请其他来宾，请将此策略设置为“是”。
   - 为来宾启用电子邮件一次性密码(预览)：有关一次性密码功能的详细信息，请参阅[电子邮件一次性密码身份验证（预览）](one-time-passcode.md)。
   - 协作限制：有关允许或阻止向特定域发送邀请的详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](allow-deny-list.md)。

## <a name="assign-the-guest-inviter-role-to-a-user"></a>将“来宾邀请者”角色分配给用户

借助“来宾邀请者”角色，无需向各个用户分配全局管理员或其他管理员角色即可为他们提供邀请来宾的功能。 将“来宾邀请者”角色分配给个人。 然后确保将“管理员和具有“来宾邀请者”角色的用户可以邀请”设置为“是”。 

下面是一个示例，它展示了如何使用 PowerShell 将用户添加到“来宾邀请者”角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在没有邀请的情况下添加 B2B 协作来宾用户](add-user-without-invite.md)
- [将 B2B 协作用户添加到角色](add-guest-to-role.md)


