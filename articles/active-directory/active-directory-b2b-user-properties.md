---
title: "Azure Active Directory B2B 协作用户的属性 | Microsoft 文档"
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
ms.date: 05/25/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 6e49cb202ed03bf50fb9ca34d34924cda434829c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/27/2017

---

# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 协作用户的属性

Azure Active Directory (Azure AD) 企业对企业 (B2B) 协作用户是 UserType = Guest 的用户。 此来宾用户通常来自某个合作伙伴组织，默认情况下，对邀请方的目录拥有有限的特权。

根据邀请方组织的需要，Azure AD B2B 协作用户可以处于以下帐户状态之一：

- 状态 1：驻留在 Azure AD 的外部实例中，代表邀请方组织中的来宾用户。 在这种情况下，B2B 用户需使用属于受邀方租户的 Azure AD 帐户进行登录。 如果合作伙伴组织不使用 Azure AD，仍会在 Azure AD 中创建来宾用户。 相应要求是，他们兑换自己的邀请，并由 Azure AD 验证其电子邮件地址。 此安排也称为实时 (JIT) 租户或“促销型”租户。

- 状态 2：驻留在 Microsoft 帐户中，代表宿主组织中的来宾用户。 在这种情况下，来宾用户需使用 Microsoft 帐户登录。 受邀用户的非 Microsoft 帐户社交标识（google.com 或类似项）在兑换产品期间将创建为 Microsoft 帐户。

- 状态 3：驻留在组织的本地 Active Directory 中，并且与宿主组织的 Azure AD 同步。 在此版本中，必须使用 PowerShell 手动更改云中的此类用户的 UserType。

- 状态 4：驻留在宿主组织的 Azure AD 中并且 UserType = Guest，其凭据由宿主组织管理。

  ![显示邀请方的姓名首字母缩写](media/active-directory-b2b-user-properties/redemption-diagram.png)


现在，让我们看看处于状态 1 的 Azure AD B2B 协作用户在 Azure AD 中的大致情况。

### <a name="before-invitation-redemption"></a>兑换邀请之前

![兑换产品之前](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>兑换邀请之后

![兑换产品之后](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B 协作用户的关键属性
### <a name="usertype"></a>UserType
此属性表示用户与宿主租户之间的关系。 此属性可以具有两个值：
- 成员：此值表示宿主组织的某位员工，即组织的工资单中的某个用户。 例如，此用户应当对仅限内部站点具有访问权限。 此用户不被视为外部协作者。

- 来宾：此值指示不视为公司内部用户的用户，例如外部协作者、合作伙伴、客户或类似的用户。 此类用户不需要接收 CEO 的内部备注，或享受（比如）公司效益。

  > [!NOTE]
  > UserType 与用户的登录方式、用户的目录角色等等之间没有关系。 此属性只是指明该用户与宿主组织之间的关系，使该组织能够实施依赖于此属性的策略。

### <a name="source"></a>Source
此属性指示用户如何登录。

- 已邀请用户：此用户已受邀但尚未兑换其邀请。

- 外部 Active Directory：此用户驻留在外部组织中，使用属于另一组织的 Azure AD 帐户进行身份验证。 此登录类型对应于状态 1。

- Microsoft 帐户：此用户驻留在某个 Microsoft 帐户中，使用 Microsoft 帐户进行身份验证。 此登录类型对应于状态 2。

- Windows Server Active Directory：此用户从属于此组织的本地 Active Directory 进行登录。 此登录类型对应于状态 3。

- Azure Active Directory：此用户使用属于此组织的 Azure AD 帐户进行身份验证。 此登录类型对应于状态 4。
  > [!NOTE]
  > Source 和 UserType 是独立的属性。 Source 的值并不暗示特定的 UserType 值。

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>是否可将 Azure AD B2B 用户添加为成员而非来宾？
通常，Azure AD B2B 用户和来宾用户是同义词。 因此，默认情况下，Azure AD B2B 协作用户将添加为 UserType = Guest 的用户。 但在某些情况下，合作伙伴组织又是一家更大型上级组织的成员，而宿主组织也属于该大型组织。 如果是这样，宿主组织可能希望将合作伙伴组织中的用户视为成员而非来宾。 可以使用 Azure AD B2B 邀请管理器 API 将合作伙伴组织中的用户作为成员添加或邀请到宿主组织。

## <a name="filter-for-guest-users-in-the-directory"></a>对目录中的来宾用户进行筛选

![筛选来宾用户](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>转换 UserType
目前，用户可使用 PowerShell 将 UserType 从 Member 转换为 Guest，反之亦然。 但是，UserType 属性应该表示用户与组织之间的关系。 因此，只有当用户与组织之间的关系发生更改时，才应当更改此属性的值。 如果用户的关系发生更改，是否应当解决诸如用户主体名称 (UPN) 是否应更改之类的问题？ 用户是否应该继续有权访问同样的资源？ 是否应该分配邮箱？ 因此，我们不建议使用 PowerShell 以原子活动的形式更改 UserType。 此外，为防止使用 PowerShell 导致此属性成为不可变的，我们也不建议对此值产生依赖关系。

## <a name="remove-guest-user-limitations"></a>删除来宾用户限制
在某些情况下，你可能想要为来宾用户提供更高的特权。 可将来宾用户添加到任何角色，甚至可在目录中删除默认的来宾用户限制，向用户提供与成员相同的特权。

可以禁用默认的来宾用户限制，便于为公司目录中的来宾用户提供与成员用户相同的权限。

![删除来宾用户限制](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委托 B2B 协作邀请](active-directory-b2b-delegate-invitations.md)
* [B2B 协作用户审核和报告](active-directory-b2b-auditing-and-reporting.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作的当前限制](active-directory-b2b-current-limitations.md)

