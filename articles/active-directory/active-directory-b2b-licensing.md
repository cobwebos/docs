---
title: "Azure Active Directory B2B 协作授权指南 | Microsoft 文档"
description: "Azure Active Directory B2B 协作要求根据你对 B2B 协作用户使用的功能购买 Azure AD 许可证"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 4e620f3d76caa25ac0e5afb134f37ffe263935f0
ms.contentlocale: zh-cn
ms.lasthandoff: 04/13/2017


---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B 协作授权指南

Azure Active Directory (Azure AD) B2B 协作将一组选定的现有 Azure AD 功能扩展到了受邀加入 Azure AD 租户的来宾用户。 因此，Azure AD B2B 协作来宾用户将通过 Azure AD 许可证获得授权，并与此处所述的现有的免费、基本和高级 P1/P2 许可证层保持一致：https://azure.microsoft.com/pricing/details/active-directory/。

邀请 B2B 用户并将其分配到 Azure AD 中的应用程序不会产生费用。 此外，B2B 用户的每个来宾用户最多可免费创建 10 个应用，B2B 用户可免费创建 3 份基本报告，因为他们属于 Azure AD“免费”层。
通过 B2B 协作功能扩展到 B2B 用户的任何付费 Azure AD 功能需要使用 Azure AD 付费许可证（基本、高级 P1 或高级 P2，具体取决于使用的功能）授权。 邀请方租户将会凭借每个 Azure AD 付费许可证获得 5 个 B2B 用户权限。 也就是说，向某个租户中的一个员工用户提供对 Azure AD 付费功能的权限的每个 Azure AD 付费许可证现在还向被邀请到该租户的另外 5 个 B2B 用户提供对那些相同 Azure AD 付费功能的权限。

## <a name="licensing-examples"></a>授权示例
- 某个客户想要邀请 100 个 B2B 用户加入其 Azure AD 租户，并针对所有用户使用基于组的访问管理和预配，但其中的 50 个用户还需要 MFA 和条件访问。 在这种情况下，该客户必须购买 10 个 Azure AD 基本许可证和 10 个 Azure AD 高级 P1 许可证，才能正常涵盖其所有 B2B 用户。 同样，如果邀请方租户打算对 B2B 用户使用 Identity Protection 功能，则必须购买足够的 Azure AD 高级 P2 许可证才能以 5:1 的比例涵盖所有这些 B2B 用户。
- 某个客户雇用了 10 名员工，这些员工目前都已获得 Azure AD 高级 P1 授权。 现在，他们想要邀请 60 个 B2B 用户，这些用户需要多重身份验证 (MFA)。 根据 5:1 授权规则，该客户必须至少购买 12 个 Azure AD 高级 P1 许可证才能涵盖这 60 个 B2B 协作用户。 由于该客户已经为其 10 名员工购买了 10 个高级 P1 许可证，因此有权使用 MFA 等高级 P1 功能邀请 50 个 B2B 用户。 在本示例中，他们需要购买 2 个额外的高级 P1 许可证才能涵盖剩余的 10 个 B2B 协作用户。

> [!NOTE]
> 没有相应的工具，也不需要向 B2B 用户分配许可证，即可启用这些 B2B 协作用户权限。

拥有邀请方租户的客户必须确定多少个 B2B 协作用户需要付费 Azure AD 功能，根据使用的是基本、高级 P1 还是高级 P2 级别的功能，客户必须购买足够数量的相应 Azure AD 付费许可证才能以 5:1 的比例涵盖其 B2B 协作用户。 如果公司需要其他 B2B 协作用户权限，必须购买所需的 Azure AD 付费许可证。

## <a name="additional-licensing-details"></a>其他授权详细信息
- B2B 协作可根据 Azure AD 版本的现有模型，向不同的 B2B 协作用户提供不同的功能。
- 每个 Azure AD 付费许可证包含 5 个 B2B 协作用户的权限（5:1 模型）。
- 不需要将许可证实际分配给 B2B 用户帐户。 系统会自动计算和报告。
- 如果租户中没有付费 Azure AD 许可证，每个受邀用户将获得 Azure AD 免费版提供的权限。
- 如果某个 B2B 协作用户以员工身份从其组织获得了付费 Azure AD 许可证，则不会消耗邀请方租户中的一个 B2B 协作许可证。

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>深入讨论：将企业集团中的用户添加为“成员”，并允许其使用 API 时有哪些授权注意事项？
B2B 来宾用户属于合作伙伴组织，并且被邀请与主机组织进行合作。 通常，除此之外的任何其他情况都不符合 B2B 的条件，即使使用了 B2B 功能。 我们来特别了解以下两种情况：

1. 如果主机邀请员工使用使用者地址
  1. 这不符合我们的授权策略，并且当前不建议这样做。

2. 如果主机组织从其他企业组织中添加用户
  1. 这种情况下，用户受邀使用 B2B API，但这不是传统的 B2B。 理想情况下，应该让这些组织邀请其他组织的用户作为成员（API 允许这样操作）。 在这种情况下，必须向这些成员分配许可证，这样他们才能访问邀请组织中的资源。

  2. 一些组织可能希望通过策略的方式添加其他组织中即将被添加为“来宾”用户。 这时存在两种情况：
      * 该集团组织已经在使用 Azure AD，并且受邀用户已经在对方组织中获得许可：在这种情况下，受邀用户不需要遵循本文档前面提到的 1:5 公式。 

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

