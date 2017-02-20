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
ms.date: 02/03/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: ae154e09e3b9ef2182e74b1dc5a0d8da3922b0df


---

# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 协作用户的属性

## <a name="defining-a-b2b-collaboration-user"></a>定义 B2B 协作用户
B2B 协作用户是 UserType = Guest 的用户，即来宾用户。 此用户通常代表合作伙伴组织中的用户，默认情况下，对邀请方的目录拥有有限的特权。 根据邀请方组织的需要，B2B 协作用户可以处于以下帐户状态之一：
1. 驻留在 Azure Active Directory (Azure AD) 的外部实例中，代表宿主组织中的来宾用户。 在这种情况下，B2B 用户需使用属于其宿主租户的 Azure AD 帐户登录。 如果该用户所属的外部组织在邀请时未使用 Azure AD，将在用户验证其电子邮件地址后兑换邀请时“适时”在 Azure AD 中创建来宾用户。 这也称为适时 (JIT) 租户，有时称为促销型租户。
2. 驻留在 Microsoft 帐户中，代表宿主组织中的来宾用户。 在这种情况下，来宾用户需使用 Microsoft 帐户登录。 在 B2B 协作的 Azure AD 公共预览全新版中，受邀用户的非 MSA 社交标识（google.com 或类似）在兑换产品期间适时创建为 Microsoft 帐户。
3. 驻留在组织的本地 Active Directory 中，与宿主组织的 Azure AD 同步。 在预览版发布期间，必须使用 PowerShell 将此类用户在云中的 UserType 手动更改为 guest。 以后的版本将支持通过 Azure AD Connect 自动完成此项更改。
4. 驻留在宿主组织的 AzureAD 中并且 UserType = Guest，其凭据由宿主组织管理。

  ![显示邀请方的姓名首字母缩写](media/active-directory-b2b-user-properties/redemption-diagram.png)


现在，让我们看看处于状态 1 的 B2B 协作用户在 Azure AD 中的大致情况。

### <a name="before-invitation-redemption"></a>兑换邀请之前

![兑换产品之前](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>兑换邀请之后

![兑换产品之后](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-b2b-collaboration-user"></a>B2B 协作用户的关键属性

### <a name="usertype"></a>UserType
此属性表示用户与宿主租户之间的关系。 它可以使用两个值：
- Member：宿主组织的某位员工，即组织工资单中的用户。 例如，此用户预期能够访问仅限内部访问的站点。 此用户不被视为外部协作者。
- Guest：表示不被视为公司内部人员的用户。 它们可能是外部协作者、合作伙伴、客户或类似用户。举例来说，他们没有打算进入 CEO 内部备忘录或享受公司福利。

  > ![NOTE] UserType 与用户的登录方式或者用户所属的目录角色等之间没有关系。 此属性只是指明该用户与宿主组织之间的关系，使该组织能够实施依赖于此属性的策略。

### <a name="source"></a>源
用户如何登录。

- 受邀用户：此用户已受邀但尚未兑换其邀请。

- 外部 Active Directory：此用户驻留在外部组织中，使用属于另一组织的 Azure AD 帐户进行身份验证。 这对应于上面的状态 1。

- Microsoft 帐户：此用户驻留在 MSA 中，使用 Microsoft 帐户进行身份验证。 这对应于上面的状态 2。

- Windows Server AD：通过属于此组织的本地 Active Directory 同步此用户。 这对应于上面的状态 3。

- Azure Active Directory：此用户使用属于此组织的 Azure AD 帐户进行身份验证。 这对应于上面的状态 4。

  > ![NOTE] Source 和 UserType 是独立的属性。 source 的值并不暗指特定的 UserType。

## <a name="can-b2b-users-be-added-as-members-instead-of-guests"></a>是否可将 B2B 用户添加为成员而不是来宾？
通常，B2B 用户和来宾用户是同义词。 因此，默认情况下，B2B 协作用户将添加为 UserType = Guest 的用户。 但在某些情况下，合作伙伴组织实际上又是一家更大型上级组织的成员，而宿主组织也属于该大型组织。 如果是这样，宿主组织可能希望将合作伙伴组织中的用户视为成员而不是来宾。 在这种情况下，可以使用 B2B 邀请管理器 API 将合作伙伴组织中的用户添加或邀请到宿主组织作为成员。

## <a name="filtering-for-guest-users-in-the-directory"></a>筛选目录中的来宾用户

![筛选来宾用户](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="converting-usertype"></a>转换 UserType
目前，用户可在 PowerShell 中将 UserType 从 Member 转换为 Guest，反之亦然。 但是，UserType 属性应该表示用户与组织之间的关系。 因此，仅当用户与组织的关系发生更改时，才应更改此属性。 如果用户关系发生更改，其他应该回答的问题包括，是否应该更改 UPN？ 该用户是否应该继续有权访问他（她）过去有权访问的资源？ 是否应该分配邮箱？ 因此，我们不建议在 PowerShell 中以原子活动的形式更改 UserType。 此外，在以后的版本中，我们会将此属性设计为不可通过 PowerShell 改变，因此，我们不建议对此值产生依赖。

## <a name="removing-guest-user-limitations"></a>删除来宾用户的限制
在某些情况下，你可能想要为来宾用户提供更高的特权。 为此，可将来宾用户添加到任何角色，甚至可在目录中删除默认的来宾用户限制，向他们提供与成员相同的特权。 请继续阅读了解更多信息。

可以禁用默认的来宾用户限制，以便为公司目录中的来宾用户提供与常规用户（成员）相同的目录权限。

![删除来宾用户限制](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [将 B2B 协作用户添加到角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2bB 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)
* [B2B 协作的当前限制](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


