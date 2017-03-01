---
title: "Azure Active Directory B2B 协作的当前限制 | Microsoft 文档"
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
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 4dae74740d8fd00d36492e5a9fad8848b5d03952
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-current-limitations"></a>Azure Active Directory B2B 协作的当前限制

1. Azure Active Directory (Azure AD) B2B 协作邀请 API 尚处于 Beta 测试版。该 API 面有待进一步完善，但如所有 Beta 版一样，它受 Beta 版命名空间协定的约束。 我们会将该 API 与正式版一起迁移到带编号的版本。
2. 可能执行两次多重身份验证 (MFA)。如果你的合作伙伴已设置 MFA 策略，将在邀请方组织中执行和管理 B2B 协作 MFA。 这是理想情况，因为它涵盖所有标识并使你能够控制 B2B 协作受邀者的身份验证强度。 但是，如果你的合作伙伴已设置 MFA 并强制执行它，则其用户可能需要在其本组织中一次执行 MFA，然后再在你的组织中执行一次。 在将来版本中，我们将引入策略使你可以选择信任某些合作伙伴的 MFA，以避免两次 MFA 问题。
3. Instant ON：通过 B2B 协作流，我们将用户添加到目录，并在邀请兑换、应用分配等期间动态更新用户。 更新和写入通常发生在一个目录实例中，但必须复制到所有实例中。 我们观察到，在某些情况下，由于完成复制所需的时间有限，可能会导致发生问题，而这些问题表现为授权问题。 我们正在努力在正式发布前使此组问题最小化或将其消除。 在此期间，这不应是你遇到的问题，但如果你遇到了，进行刷新或重试通常会有帮助。

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

