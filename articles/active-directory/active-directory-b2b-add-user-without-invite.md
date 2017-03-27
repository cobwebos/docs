---

title: "向 Azure Active Directory 添加 B2B 协作用户（无邀请）| Microsoft Docs"
description: "通过 Azure Active Directory B2B 协作，信息工作者可将组织中的用户添加到 Azure AD，以便其访问你的公司应用程序"
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
ms.date: 02/10/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0eef684115f9c21ea61502a10576f74cac0ace8e
ms.openlocfilehash: 8fa8ba169ca85c33e52eee4a7cd5b84aa4012673


---

# <a name="add-b2b-collaboration-users-without-an-invitation"></a>在没有邀请的情况下添加 B2B 协作用户

如果邀请方所属的角色在要从中添加用户的合作伙伴组织的目录中具有枚举权限，则受邀用户无需邀请即可添加到邀请方组织。

下面是其最有用的情况：

1. 主办组织（如 WoodGrove）中的用户邀请合作伙伴组织中的一名用户（如 Sam@litware.com)）作为来宾。
2. 主办组织的管理员设置相关策略，允许 Sam 标识和添加合作伙伴组织 (Litware) 中的其他用户。
3. 现在，Sam 可将 Litware 中的其他用户添加到 WoodGrove 目录、组或应用程序而无需兑换邀请。 如果 Sam 在 Litware 中具有相应的枚举权限，则会自动执行此操作。

### <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作疑难解答](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作常见问题解答 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


