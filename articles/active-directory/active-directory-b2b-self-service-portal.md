---
title: "Azure Active Directory B2B 协作的自助注册门户 | Microsoft Docs"
description: "Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/24/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: bb63a3b23bdcaac5c94d43bb8e7294a82b0c3fa0
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B 协作注册的自助服务门户

客户可借助通过 IT 管理员 [Azure 门户](https://portal.azure.com)和面向最终用户的[应用程序访问面板](https://myapps.microsoft.com)公开的内置功能执行许多操作。 但我们也意识到，企业需要为 B2B 用户自定义载入工作流，以适应其组织的需求。 他们可以使用[邀请 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) 执行该操作。

在与客户讨论时，我们发现一个常见需求超过所有其他需求。 邀请方组织可能事先不知道需要访问其资源的各个外部协作者是谁。 他们需要为合作伙伴公司的用户提供一种方式，让他们自行注册一套由邀请方组织控制的策略。 此方案可通过 API 得以实现，因此我们在 Github 上发布了一个只实现该目的的项目：[示例 Github 项目](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)。

此 Github 项目演示组织可以如何使用 API，并为其受信任的合作伙伴提供基于策略的自助注册功能，而且包含确定他们可访问哪些应用的规则。 合作伙伴用户可以在需要时安全地访问资源，而无需邀请方组织手动载入他们。 可以轻松将项目部署到所选的 Azure 订阅中。

## <a name="as-is-code"></a>原样代码

请记住，此代码可作为一个示例，用于演示 Azure Active Directory B2B 邀请 API 的用法。 它应由开发团队或合作伙伴自定义，并且应在生产方案中部署之前对其进行评审。

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览其他文章：
* [什么是 Azure AD B2B 协作？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作疑难解答](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](active-directory-b2b-faq.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [在没有邀请的情况下添加 B2B 协作用户](active-directory-b2b-add-user-without-invite.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)