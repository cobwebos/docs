---
title: "Azure Active Directory B2B 协作的限制 | Microsoft 文档"
description: "Azure Active Directory B2B 协作预览版的当前限制"
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
ms.date: 04/12/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: cdc951d4e16e7f0df425dba7c33d86255276f526
ms.lasthandoff: 04/13/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure Active Directory B2B 协作的限制
Azure Active Directory (Azure AD) B2B 协作当前具有本文中描述的限制。

## <a name="possible-double-multi-factor-authentication"></a>可能需要进行两次多重身份验证
使用 Azure AD B2B，可在资源组织（邀请方组织）上进行多重身份验证。 [B2B 协作用户的条件访问](active-directory-b2b-mfa-instructions.md)中对使用此方法的原因进行了详细说明。 这意味着，如果合作伙伴已设置并实施了多重身份验证，则合作伙伴的用户可能必须在其本组织中执行一次身份验证，然后在你的组织中再次进行身份验证。

在将来的版本中，我们计划引入一个策略，使用该策略可以通过选择信任合作伙伴的多重身份验证来避免两次身份验证问题。


## <a name="instant-on"></a>即时启用
通过 B2B 协作流，我们将用户添加到目录，并在邀请兑换、应用分配等期间动态更新用户。 更新和写入通常发生在一个目录实例中，并且必须复制到所有实例中。 完成复制可能需要非零时间量。 有时，如果在目录的一个实例中编写或更新对象，但是对检索该对象的调用负载均衡到了另一个实例，则会导致授权问题。 我们已做了大量工作来消除或减少这些复制延迟，但是在某些少数情况下，它们仍然可能发生。 如果发生这种情况，刷新或重试可有所帮助。 如果正在使用 API 编写应用，则请重试后退操作，这是解决该问题的一个很好的防御措施。

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 协作用户属性](active-directory-b2b-user-properties.md)
* [向角色添加 B2B 协作用户](active-directory-b2b-add-guest-to-role.md)
* [委派 B2bB 协作邀请](active-directory-b2b-delegate-invitations.md)
* [动态组和 B2B 协作](active-directory-b2b-dynamic-groups.md)
* [B2B 协作代码和 PowerShell 示例](active-directory-b2b-code-samples.md)
* [为 B2B 协作配置 SaaS 应用](active-directory-b2b-configure-saas-apps.md)
* [B2B 协作用户令牌](active-directory-b2b-user-token.md)
* [B2B 协作用户声明映射](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共享](active-directory-b2b-o365-external-user.md)

