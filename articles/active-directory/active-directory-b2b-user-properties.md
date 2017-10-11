---
title: "Azure Active Directory B2B 协作用户的属性 |Microsoft 文档"
description: "Azure Active Directory B2B 协作用户属性是可配置"
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
ms.date: 05/25/2017
ms.author: sasubram
ms.openlocfilehash: 6e49cb202ed03bf50fb9ca34d34924cda434829c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 协作用户的属性

Azure Active Directory (Azure AD) 企业到企业 (B2B) 协作用户是具有 UserType 的用户 = 来宾。 此来宾用户通常是从伙伴组织，并具有有限权限在邀请的目录中，默认情况下。

具体取决于邀请组织的需求，Azure AD B2B 协作用户可以采用以下的帐户状态之一：

- 状态 1： 在 Azure AD 的外部实例宿主的并且表示为邀请组织中的来宾用户。 在这种情况下，B2B 用户在使用属于受邀租户的 Azure AD 帐户登录。 如果合作伙伴组织不使用 Azure AD，仍会创建 Azure AD 中的来宾用户。 要求是它们兑换其邀请和 Azure AD 验证其电子邮件地址。 在实时 (JIT) 租户或"病毒"租户，也被称为这种安排。

- 状态 2： 驻留在 Microsoft 帐户和表示为主机组织中的来宾用户。 在这种情况下，guest 用户使用 Microsoft 帐户登录。 受邀的用户的社交标识 (google.com 或类似)，这不 Microsoft 帐户，在优惠兑换期间创建为 Microsoft 帐户。

- 状态 3： 驻留在主机组织的本地 Active Directory 和同步与主机组织的 Azure AD。 在此版本中，必须使用 PowerShell 以手动更改此类用户在云中 UserType。

- 状态 4： 驻留在主机组织的 Azure AD 与 UserType = 来宾和主机组织管理的凭据。

  ![显示邀请者的姓名缩写](media/active-directory-b2b-user-properties/redemption-diagram.png)


现在，让我们查看状态 1 中的 Azure AD B2B 协作用户在 Azure AD 中的显示效果。

### <a name="before-invitation-redemption"></a>邀请兑换之前

![优惠兑换之前](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>邀请兑换后

![优惠兑换后](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B 协作用户的关键属性
### <a name="usertype"></a>UserType
此属性指示主机租户对用户的关系。 此属性可以具有两个值：
- 成员： 此值指示主机组织和组织的工资单中的用户的一名员工。 例如，此用户需要具有访问权限仅限内部使用的站点。 此用户将不被视为外部协作者。

- 来宾： 此值指示不会被视为公司，比如外部协作者、 合作伙伴、 客户或类似的用户的内部的用户。 这样的用户不需要接收 CEO 的内部的备注，或接收公司的好处，例如。

  > [!NOTE]
  > UserType 具有用户如何登录、 目录角色的用户和等等的任何关系。 此属性只需指示主机的组织的用户的关系，并允许组织强制执行依赖于此属性的策略。

### <a name="source"></a>源
此属性指示用户如何登录。

- 受邀用户： 此用户已受邀但未兑换邀请。

- 外部的 Active Directory： 此用户主客户端的外部组织中，并使用属于另一组织的 Azure AD 帐户进行身份验证。 这种类型的登录对应于状态 1。

- Microsoft 帐户： 此用户驻留在 Microsoft 帐户和使用 Microsoft 帐户进行身份验证。 这种类型的登录对应于状态 2。

- Windows Server Active Directory： 此用户已登录从属于此组织的本地 Active Directory。 这种类型的登录对应于状态 3。

- Azure Active Directory： 此用户进行身份验证使用属于此组织的 Azure AD 帐户。 这种类型的登录对应于状态 4。
  > [!NOTE]
  > 源和 UserType 是独立的属性。 源的值不为 UserType 意味着特定值。

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>可以将 Azure AD B2B 用户添加为成员而不是来宾？
通常情况下，Azure AD B2B 用户和来宾用户是同义词。 因此，作为具有 UserType 的用户添加 Azure AD B2B 协作用户 = 默认的来宾。 但是，在某些情况下，合作伙伴组织是主机组织还所属的较大组织的成员。 如果是这样，主机组织可能想要将用户作为成员而不是来宾合作伙伴组织中。 使用 Azure AD B2B 邀请管理器 Api 添加或邀请到为成员主机组织从伙伴组织用户。

## <a name="filter-for-guest-users-in-the-directory"></a>在目录中的来宾用户的筛选器

![筛选器来宾用户](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>转换 UserType
目前，用户可以将 UserType 从成员转换为来宾，反之亦然，使用 PowerShell。 但是，应该 UserType 属性表示对组织的用户的关系。 因此，仅当对组织的用户的关系发生更改，则应该将更改此属性的值。 如果用户的关系发生更改，需要解决问题，例如是否应更改的用户主体名称 (UPN)，类似？ 应用户仍拥有访问相同的资源的访问权限？ 应分配邮箱？ 因此，我们不建议更改 UserType 作为原子活动中使用 PowerShell。 此外，如果使用 PowerShell，此属性将变得不可变，我们不建议产生此值的依赖关系。

## <a name="remove-guest-user-limitations"></a>删除来宾用户的限制
可能情况下你想要为来宾用户提供更高特权。 可以将来宾用户添加到任何角色，并甚至可以向用户授予成员相同的权限的目录中删除默认来宾用户限制。

它是可以禁用默认来宾用户的限制，因此公司目录中的来宾用户给定成员用户相同的权限。

![删除来宾用户的限制](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>后续步骤

浏览我们在 Azure AD B2B 协作的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委托 B2B 协作邀请](active-directory-b2b-delegate-invitations.md)
* [B2B 协作用户审核和报告](active-directory-b2b-auditing-and-reporting.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [配置 B2B 协作的 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作的用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作当前限制](active-directory-b2b-current-limitations.md)
