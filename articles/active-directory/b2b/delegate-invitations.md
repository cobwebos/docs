---
title: 启用 B2B 外部协作设置-Azure Active Directory |Microsoft Docs
description: 了解如何启用 Active Directory B2B 外部协作和管理谁可以邀请来宾用户。 使用来宾邀请者角色来委托邀请。
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
ms.openlocfilehash: 11dda7fc3760f468c094fb4cf4484a27895f83b9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812675"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>启用 B2B 外部协作和管理谁可以邀请来宾

本文介绍如何启用 Azure Active Directory (Azure AD) B2B 协作并确定谁可以邀请来宾。 默认情况下，所有用户和你的目录中的来宾可都邀请来宾即使它们未分配到管理员角色。 外部协作设置让你在组织中不同类型的用户的来宾邀请打开或关闭。 此外可以通过分配角色，以便邀请来宾委派给单个用户的邀请。

## <a name="configure-b2b-external-collaboration-settings"></a>配置 B2B 外部协作设置

使用 Azure AD B2B 协作，租户管理员可以设置以下邀请策略：

- 关闭邀请
- 只有管理员和具有“来宾邀请者”角色的用户可以邀请
- 管理员、“来宾邀请者”角色和成员可以邀请
- 所有用户（包括来宾）都可以邀请

默认情况下，所有用户，包括来宾，可以都邀请来宾用户。

### <a name="to-configure-external-collaboration-settings"></a>若要配置外部协作设置：

1. 登录到[Azure 门户](https://portal.azure.com)作为租户管理员。
2. 选择“Azure Active Directory” > “用户” > “用户设置”。
3. 在“外部用户”下，选择“管理外部协作设置”。
   > [!NOTE]
   > “外部协作设置”也可从“组织关系”页获得。 在 Azure Active Directory 中的“管理”下，转到“组织关系” > “设置”。
4. 上**外部协作设置**页上，选择你想要启用的策略。

   ![外部协作设置](./media/delegate-invitations/control-who-to-invite.png)

  - **来宾用户权限处于限制**:此策略将确定你的目录中的来宾权限。 选择**是**块来宾从某些目录任务，例如枚举用户、 组或其他目录资源。 选择**否**让来宾即目录中的常规用户相同的访问权限的目录数据。
   - **管理员和来宾邀请者角色中的用户可以邀请**:若要允许管理员和用户邀请来宾"来宾邀请者"角色中，请将此策略设置为**是**。
   - **成员可以邀请**:若要允许非管理员的目录的成员可以邀请来宾，请将此策略设置为**是**。
   - **来宾可邀请**:若要允许来宾邀请其他来宾，请将此策略设置为**是**。
   - **对于来宾 （预览版） 中启用电子邮件一次性密码**:有关一次性密码功能的详细信息，请参阅[电子邮件的一次性密码身份验证 （预览版）](one-time-passcode.md)。
   - **协作限制**:有关允许或阻止向特定的域发送邀请的详细信息，请参阅[允许或阻止向用户发送邀请 B2B 特定组织中的](allow-deny-list.md)。

## <a name="assign-the-guest-inviter-role-to-a-user"></a>将来宾邀请者角色分配给用户

使用来宾邀请者角色中，可以授予单个用户邀请来宾没有为其分配的全局管理员或其他管理员角色的功能。 将来宾邀请者角色分配给各位成员。 然后，请确保设置**管理员和来宾邀请者角色中的用户可以邀请**到**是**。

下面是一个示例，它展示了如何使用 PowerShell 将用户添加到“来宾邀请者”角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [在没有邀请的情况下添加 B2B 协作来宾用户](add-user-without-invite.md)
- [将 B2B 协作用户添加到角色](add-guest-to-role.md)


