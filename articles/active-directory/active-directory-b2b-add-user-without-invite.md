---
title: 向 Azure Active Directory 添加 B2B 协作用户（无邀请）| Microsoft Docs
description: 可允许来宾用户将其他来宾用户添加到 Azure AD，而无需在 Azure Active Directory B2B 协作中兑换邀请。
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075617"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>在没有邀请的情况下添加 B2B 协作来宾用户

> [!NOTE]
> 现在，除非在某些特殊情况下，否则来宾用户不再需要邀请电子邮件。 有关详细信息，请参阅 [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)。  

可允许用户（例如合作伙伴代表）将用户从合作伙伴添加到组织，而无需兑换邀请。 而你要做的只是在用于合作伙伴组织的目录中授予该用户枚举特权。 

在以下情况下授予这些特权：

1. 主办组织（如 WoodGrove）中的用户邀请合作伙伴组织中的一名用户（如 Sam@litware.com）作为来宾。
2. 主办组织的管理员[设置相关策略](active-directory-b2b-delegate-invitations.md)，允许 Sam 标识和添加合作伙伴组织 (Litware) 中的其他用户。
3. 现在，Sam 可将 Litware 中的其他用户添加到 WoodGrove 目录、组或应用程序而无需兑换邀请。 如果 Sam 在 Litware 中具有相应的枚举特权，则会自动执行此操作。

### <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
- [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
- [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
- [委托 Azure Active Directory B2B 协作邀请](active-directory-b2b-delegate-invitations.md)

