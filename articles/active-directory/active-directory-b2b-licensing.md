---
title: "Azure Active Directory B2B 协作授权指南 | Microsoft 文档"
description: "Azure Active Directory B2B 协作无需付费的 Azure AD 许可证，但你仍然可以为 B2B 来宾用户获取付费功能"
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
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.openlocfilehash: dfef32c05af157ae8d3a5434016f87f488a35051
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 协作授权指南

可以使用 Azure AD B2B 协作功能，将来宾用户邀请至自己的 Azure AD 租户，允许他们访问你组织中的 Azure AD 服务和其他资源。 如果要提供对 Azure AD 付费功能的访问权限，B2B 协作来宾用户必须使用相应的 Azure AD 许可证获得授权。 

具体而言：
* 来宾用户可使用 Azure AD Free 功能，无需额外授权。
* 如果想向 B2B 用户提供对 Azure AD 付费功能的访问权限，则必须有足够的许可证来支持这些 B2B 来宾用户。
* 一个具有 Azure AD 付费许可证的邀请方租户具有其他五个受邀加入租户的 B2B 来宾用户的 B2B 协作使用权限。
* 拥有邀请方租户的客户必须确定有多少个 B2B 协作用户需要 Azure AD 付费功能。 根据需要为来宾用户提供的 Azure AD 付费功能，必须购买足够数量的 Azure AD 付费许可证才能以相同的 5:1 的比例涵盖 B2B 协作用户。

B2B 协作来宾用户作为来自合作伙伴公司的用户添加，而不是作为你组织的员工或集团组织中不同业务部门的员工添加。 B2B 来宾用户可以使用本文中所述的外部凭据或组织所有的凭据登录。 

换而言之，B2B 授权不是由用户认证的方式而是由用户与你组织的关系而定。 如果这些用户不是合作伙伴，则他们在许可条款中的对待方式会有所不同。 即使他们的 UserType 被标记为“来宾”，也不会将他们视为用于授权目的的 B2B 协作用户。 他们应按正常方式获得许可，每个用户一个许可证。 这些用户包括：
* 你的员工
* 使用外部标识登录的员工
* 你的集团组织中不同业务部门的员工


## <a name="licensing-examples"></a>授权示例
- 某个客户想要邀请 100 个 B2B 协作用户加入其 Azure AD 租户。 该客户为所有用户分配访问管理和预配，但其中的 50 个用户还需要 MFA 和条件访问。 该客户必须购买 10 个 Azure AD Basic 许可证和 10 个 Azure AD Premium P1 许可证，才能正常涵盖这些 B2B 用户。 如果客户打算对 B2B 用户使用 Identity Protection 功能，则必须购买 Azure AD Premium P2 许可证才能以同样的 5:1 的比例涵盖受邀用户。
- 某个客户雇用了 10 名员工，这些员工目前都已获得 Azure AD 高级 P1 授权。 现在，他们想要邀请 60 个 B2B 用户，这些用户都需要多重身份验证 (MFA)。 根据 5:1 授权规则，该客户必须至少购买 12 个 Azure AD Premium P1 许可证才能涵盖这 60 个 B2B 协作用户。 由于该客户已经为其 10 名员工购买了 10 个高级 P1 许可证，因此有权使用 MFA 等高级 P1 功能邀请 50 个 B2B 用户。 在本示例中，他们必须购买 2 个额外的高级 P1 许可证才能涵盖剩余的 10 个 B2B 协作用户。

> [!NOTE]
> 目前无法直接向 B2B 用户分配许可证来启用这些 B2B 协作用户权限。

拥有邀请方租户的客户必须确定有多少个 B2B 协作用户需要 Azure AD 付费功能。 根据需要为来宾用户提供的 Azure AD 付费功能，必须购买足够数量的 Azure AD 付费许可证才能以 5:1 的比例涵盖 B2B 协作用户。 

## <a name="additional-licensing-details"></a>其他授权详细信息
- 不需要将许可证实际分配给 B2B 用户帐户。 系统根据 5:1 的比例自动计算和报告授权。
- 如果租户中没有 Azure AD 付费许可证，每个受邀用户将获得 Azure AD Free 提供的权限。
- 如果某个 B2B 协作用户从其组织获得了 Azure AD 付费许可证，则不会消耗邀请方租户的一个 B2B 协作许可证。

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>深入讨论：将企业集团中的用户添加为“成员”，并允许其使用 API 时有哪些授权注意事项？
B2B 来宾用户属于合作伙伴组织，并且被邀请与主机组织进行合作。 通常，其他任何情况都不符合 B2B 的条件，即使使用 B2B 功能也如此。 我们来特别了解以下两种情况：

1. 如果主机邀请员工使用使用者地址
  * 这种情况不符合我们的授权策略，不建议这样做。

2. 如果主机组织从其他企业组织中添加用户
  1. 这种情况下，用户受邀使用 B2B API，但这不是传统的 B2B。 理想情况下，应该让这些组织邀请其他组织的用户作为成员（API 允许这样操作）。 在这种情况下，必须向这些成员分配许可证，这样他们才能访问邀请组织中的资源。

  2. 一些组织可能希望通过策略的方式添加其他组织中即将被添加为“来宾”用户。 这时存在两种情况：
      * 该集团组织已经在使用 Azure AD，并且受邀用户已经在对方组织中获得授权：在这种情况下，受邀用户不需要遵循本文档前面提到的 1:5 公式。 

      * 该集团组织未使用 Azure AD 或没有足够的许可证：在这种情况下，请遵循本文档前面提到的 1:5 公式。

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure Active Directory B2B 协作疑难解答](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [在没有邀请的情况下添加 B2B 协作用户](active-directory-b2b-add-user-without-invite.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
