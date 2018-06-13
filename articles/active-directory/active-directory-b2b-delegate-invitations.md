---
title: 委托 Azure Active Directory B2B 协作邀请 | Microsoft 文档
description: Azure Active Directory B2B 协作用户属性是可配置的
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a4a1303c3992b8c576650859eea65591392b9b20
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929257"
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>委托 Azure Active Directory B2B 协作邀请

使用 Azure Active Directory (Azure AD) 企业对企业 (B2B) 协作，不必成为全局管理员便可发送邀请。 相反，可以使用策略并将邀请委托给其角色允许他们发送邀请的用户。 委托来宾用户邀请的重要新方法是通过“来宾邀请者”角色邀请。

## <a name="guest-inviter-role"></a>“来宾邀请者”角色
我们可以将用户分配给可发送邀请的“来宾邀请者”角色。 不必成为全局管理员角色的成员便可发送邀请。 默认情况下，常规用户还可以调用邀请 API，除非全局管理员已对常规用户禁用邀请。 用户也可以使用 Azure 门户或 PowerShell 调用 API。

下面是一个示例，它展示了如何使用 PowerShell 将用户添加到“来宾邀请者”角色：

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>控制谁可以邀请

![控制如何邀请](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

使用 Azure AD B2B 协作，租户管理员可以设置以下邀请策略：

- 关闭邀请
- 只有管理员和具有“来宾邀请者”角色的用户可以邀请
- 管理员、“来宾邀请者”角色和成员可以邀请
- 所有用户（包括来宾）都可以邀请

默认情况下，租户设置为 #4。 （所有用户（包括来宾）都可以邀请 B2B 用户。）

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [在没有邀请的情况下添加 B2B 协作来宾用户](active-directory-b2b-add-user-without-invite.md)
- [将 B2B 协作用户添加到角色](active-directory-b2b-add-guest-to-role.md)


