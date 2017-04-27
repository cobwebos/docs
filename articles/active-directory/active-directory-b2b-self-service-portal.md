---
title: "Azure Active Directory B2B 协作的自助注册门户 | Microsoft Docs"
description: "Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: f629eeb6f12c8785cab2585190f70e98b02fa5b4
ms.lasthandoff: 04/12/2017


---


# <a name="self-service-sign-up-portal-for-azure-ad-b2b-collaboration"></a>Azure AD B2B 协作的自助注册门户

客户可以对我们通过 [Azure 门户](https://portal.azure.com)和[应用程序访问面板](https://myapps.microsoft.com)（适用于非管理员）中的 IT 管理员体验公开的内置产品功能执行许多操作。 但我们也意识到，企业需要为 B2B 用户自定义载入工作流，以适应其组织的需求。 他们可以使用[我们的 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) 实现这一点。

在与许多客户讨论这个问题时，我们发现一个常见的需求超过所有其他需求。 这就是邀请组织可能事先不知道（或想知道）需要访问其资源的各个外部协作者是谁。 他们需要一种方式，通过这种方式，合作伙伴公司的用户可以自助注册一组由邀请组织控制的策略。 这通过我们的 API 可以实现，所以我们在 Github 上发布了一个项目：[示例 Github 项目](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)。

我们的 Github 项目演示组织可以如何使用我们的 API，并为其受信任的合作伙伴提供基于策略的自助注册功能，而且包含确定他们应访问哪些应用的规则。 这样一来，合作伙伴用户可以在需要时安全地访问正确的资源，而无需邀请组织中的任何人手动载入他们。 只需单击一下你所选的 Azure 订阅，就可以轻松部署项目。 试一试！

## <a name="as-is-code"></a>原样代码

请记住，此代码可作为一个示例，用于演示 Azure Active Directory B2B 邀请 API 的用法。 它应由开发团队或合作伙伴自定义，并且应在生产方案中部署之前对其进行评审。

## <a name="next-steps"></a>后续步骤

在 Azure AD B2B 协作网站上浏览我们的其他文章：
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
