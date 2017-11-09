---
title: "委托 Azure Active Directory B2B 协作邀请 | Microsoft 文档"
description: "Azure Active Directory B2B 协作用户属性是可配置的"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 78613cc978b585a98d235245194c02371f7f3849
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
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

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作的当前限制](active-directory-b2b-current-limitations.md)
