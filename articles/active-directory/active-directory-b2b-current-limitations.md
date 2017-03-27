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
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0e71a840d4f503779131ee4a21fe6063d33185f1
ms.openlocfilehash: cbefca2d45a332cd57cfea49dfeaa300426d5502
ms.lasthandoff: 02/24/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure Active Directory B2B 协作的限制
Azure Active Directory (Azure AD) B2B 协作当前具有本文中描述的限制。

## <a name="invitation-apis-are-in-preview"></a>邀请 API 为预览版
API 面是我们预期的前进方向。 不过，与所有预发行版本一样，该 API 受制于预览版命名空间协定。 我们会将该 API 与正式版 (GA) 一起迁移到带编号的版本。

## <a name="possible-double-multi-factor-authentication"></a>可能需要进行两次多重身份验证
如果你的合作伙伴已实施了 Azure 多重身份验证策略，则可能会出现此冗余。 B2B 协作多重身份验证是在邀请方组织中执行和管理的。 这样的身份验证是理想的，因为它涵盖所有身份并使你能够控制 B2B 协作受邀者的身份验证强度。

但是，如果合作伙伴已设置并实施了多重身份验证，则合作伙伴的用户可能必须在其主组织中执行一次身份验证，然后在你的组织中再次进行身份验证。

在将来的版本中，我们计划引入一个策略，你可以使用该策略通过选择信任合作伙伴的多重身份验证来避免两次身份验证问题。

## <a name="instant-on"></a>即时启用
通过 B2B 协作流，我们将用户添加到目录，并在邀请兑换、应用分配等期间动态更新用户。 更新和写入通常发生在一个目录实例中，并且必须复制到所有实例中。 我们观察到，由于可以用来完成复制的时间有限，有时可能会发生授权问题。 在正式版发布之前，我们正在努力减少或消除这些问题。 在这期间，你未必会遇到这些问题，但如果遇到，刷新或重试应当有助于解决它们。

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

