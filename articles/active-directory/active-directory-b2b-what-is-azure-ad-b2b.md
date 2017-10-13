---
title: "什么是 Azure Active Directory B2B 协作？ | Microsoft Docs"
description: "Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持。"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.custom: aaddev
ms.reviewer: sasubram
ms.openlocfilehash: fbc12a52555b190d43b5e953fd4d19923a25b0ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>什么是 Azure AD B2B 协作？

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

借助 Azure AD 企业到企业 (B2B) 协作功能，任何使用 Azure AD 的组织都能安全可靠地与来自任何其他组织的用户协同工作（无论这些组织的规模大小）。 这些组织可使用/不使用 Azure AD，甚至可具有/不具有 IT 部门。 

使用 Azure AD 的组织可以向其合作伙伴提供文档、资源和应用程序的访问权限，同时保持对自己企业数据的完全控制。 开发人员可使用 Azure AD 企业到企业 API 编写应用程序，以更安全的方式将两个组织凝聚在一起。 此外，最终用户可非常轻松地进行导航。

我方 97% 的客户都表示 Azure AD B2B 协作对他们非常重要。

![饼图](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

截至 2017 年 4 月初，已有约 300 万用户在使用 Azure AD B2B 协作功能。 在超过 23% 的 Azure AD 组织中，每个组织已有 10 个以上的用户从这些功能中获益。

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Azure AD B2B 协作对组织的主要益处

### <a name="work-with-any-user-from-any-partner"></a>与来自任意合作伙伴的任意用户合作

* 合作伙伴使用其自己的凭据

* 合作伙伴无需使用 Azure AD

* 无需任何外部目录或进行任何复杂设置

### <a name="simple-and-secure-collaboration"></a>协作简单安全

* 提供对任何企业应用或数据的访问权限，同时应用 Azure AD 支持的高级授权策略

* 方便用户

* 应用和数据的企业级安全性

### <a name="no-management-overhead"></a>无管理开销

* 无需外部帐户或密码管理

* 无需同步或手动的帐户生命周期管理

* 无外部管理开销

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>可轻松向组织添加 B2B 协作用户

管理员可在 [Azure 门户](https://portal.azure.com)添加 B2B 协作（来宾）用户。

![添加来宾用户](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>允许协作者自带标识

B2B 协作者可以使用自己选择的标识登录。 如果用户没有 Microsoft 帐户或和 Azure AD 帐户，则会在提供兑换时为其无缝创建一个帐户。

![登录标识选择](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>委托给应用程序和组所有者 
应用程序和组所有者可以直接将 B2B 用户添加到所关心的任何应用程序，无论是否为 Microsoft 应用程序。 管理员可向非管理员委托添加 B2B 用户的权限。 非管理员可以使用 [Azure AD 应用程序访问面板](https://myapps.microsoft.com)将 B2B 协作用户添加到应用程序或组。

![访问面板](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![添加成员](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>授权策略保护企业内容

可通过以下级别强制执行多重身份验证等条件访问策略：
- 租户级别
- 应用程序级别
- 针对特定用户，保护企业应用和数据

![添加成员](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>使用 API 和示例代码轻松生成要载入的应用程序
使用按组织需求自定义的方法引入外部合作伙伴。

使用 [B2B 协作邀请 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)，可自定义载入体验，包括创建自助服务注册门户。 我们在 [Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) 上提供自助服务门户的示例代码。

![注册门户](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

通过 Azure AD B2B 协作，可以获得 Azure AD 的全部功能，以最终用户认为简单且直观的方式来保护合作伙伴关系。 不要犹豫，加入使用 Azure AD B2B 进行外部协作的成千上万个组织的行列中吧！

## <a name="next-steps"></a>后续步骤

* 管理员经验位于 [Azure 门户](https://portal.azure.com)。

* 信息工作者经验位于[访问面板](https://myapps.microsoft.com)。

* 此外，还可与往常一样通过 [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)（Microsoft 技术社区）与产品团队联系，获取任何反馈、讨论和建议。

在 Azure AD B2B 协作网站上浏览我们的其他文章：

* [Azure Active Directory 管理员如何添加 B2B 协作用户？](active-directory-b2b-admin-add-users.md)
* [信息工作者如何添加 B2B 协作用户？](active-directory-b2b-iw-add-users.md)
* [B2B 协作邀请电子邮件的元素](active-directory-b2b-invitation-email.md)
* [B2B 协作邀请兑换](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B 协作授权](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B 协作疑难解答](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B 协作常见问题 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B 协作 API 和自定义](active-directory-b2b-api.md)
* [适用于 B2B 协作用户的多重身份验证](active-directory-b2b-mfa-instructions.md)
* [在没有邀请的情况下添加 B2B 协作用户](active-directory-b2b-add-user-without-invite.md)
* [B2B 协作用户审核和报告](active-directory-b2b-auditing-and-reporting.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
