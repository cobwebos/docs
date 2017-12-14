---
title: "向 Azure Active Directory 添加 B2B 协作用户（无邀请）| Microsoft Docs"
description: "可允许来宾用户将其他来宾用户添加到 Azure AD，而无需在 Azure Active Directory B2B 协作中兑换邀请。"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: aa0d8dc9bed0a56d998e782283c84e7d2571b1f1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>在没有邀请的情况下添加 B2B 协作来宾用户

可允许用户（例如合作伙伴代表）将用户从合作伙伴添加到组织，而无需兑换邀请。 而你要做的只是在用于合作伙伴组织的目录中授予该用户枚举特权。 

在以下情况下授予这些特权：

1. 主办组织（如 WoodGrove）中的用户邀请合作伙伴组织中的一名用户（如 Sam@litware.com）作为来宾。
2. 主办组织的管理员设置相关策略，允许 Sam 标识和添加合作伙伴组织 (Litware) 中的其他用户。
3. 现在，Sam 可将 Litware 中的其他用户添加到 WoodGrove 目录、组或应用程序而无需兑换邀请。 如果 Sam 在 Litware 中具有相应的枚举特权，则会自动执行此操作。

### <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作疑难解答](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)