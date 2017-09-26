---
title: "动态组和 Azure Active Directory B2B 协作 | Microsoft 文档"
description: "Azure Active Directory B2B 协作可与 Azure AD 动态组一起使用"
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
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 5818c41610c8c5df89abcb0dcd058bcbe9579ce7
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>动态组和 Azure Active Directory B2B 协作

## <a name="what-are-dynamic-groups"></a>什么是动态组？
Azure Active Directory (Azure AD) 的安全组成员身份动态配置在 [Azure 门户](https://portal.azure.com)中提供。 管理员可以设置规则以填充在 Azure Active Directory 中基于用户属性（如 userType、部门或国家/地区）创建的组。 可基于成员属性自动在安全组中添加或删除成员。 这些组可以为成员提供对应用程序或云资源（SharePoint 站点、文档）的访问权限并分配许可证。 在 [Azure Active Directory 中的专用组](active-directory-accessmanagement-dedicated-groups.md)中阅读有关动态组的详细信息。

创建和使用动态组需要相应的 [Azure AD Premium P1 或 P2 授权](https://azure.microsoft.com/pricing/details/active-directory/)。 有关详细信息，请参阅[在 Azure Active Directory 中为动态组成员身份创建基于属性的规则](active-directory-groups-dynamic-membership-azure-portal.md)一文。

## <a name="what-are-the-built-in-dynamic-groups"></a>什么是内置动态组？
“所有用户”动态组使租户管理员只需一次单击即可创建包含租户中所有用户的组。 默认情况下，“所有用户”组包含目录中的所有用户，其中包括成员和来宾。
在新的 Azure Active Directory 管理门户中，可以在“组设置”视图中选择启用“所有用户”组。

![内置组](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

## <a name="hardening-the-all-users-dynamic-group"></a>强化“所有用户”动态组
默认情况下，“所有用户”组也包含 B2B 协作（来宾）用户。 可以使用规则删除来宾用户，进一步保护“所有用户”组。 下图显示修改后不包括来宾的“所有用户”组。

![启用“所有用户”组](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

你可能还会发现创建仅包含来宾用户的新动态组很有用，这样就可以向来宾用户应用策略（例如 Azure AD 条件访问策略）。
此类组的外观可能如下所示：

![排除来宾用户](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委托 B2B 协作邀请](active-directory-b2b-delegate-invitations.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作的当前限制](active-directory-b2b-current-limitations.md)

