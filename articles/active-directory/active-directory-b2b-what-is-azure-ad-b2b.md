---
title: "什么是 Azure Active Directory B2B 协作预览版？ | Microsoft Docs"
description: "Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持"
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
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: 5e55afe6eb88a558ea4eb147860ac5e2ebc97dbc


---

# <a name="what-is-azure-ad-b2b-collaboration-preview"></a>什么是 Azure AD B2B 协作预览版？

Azure AD B2B 协作功能使 IT 专业人员和信息工作者能够与世界各地的其他任何组织中的合作伙伴密切合作。 他们可以提供文档、资源和应用程序的访问权限，同时对其内部数据保持完全的控制。 开发人员可以使用 Azure AD 企业到企业 API 编写应用程序，以安全的方式将两个组织凝聚在一起，让信息工作者感觉不到任何差异，可以直观地浏览资源。

Azure AD B2B 协作功能可让各种规模、各个行业的组织轻松安全地与世界各地的合作伙伴展开协作，不管它们的法规遵从与政府监管要求如何。 这就是 B2B 协作公共预览全新版的目标。

## <a name="how-does-it-work"></a>工作原理

在当前预览版本中，若要与某家组织建立关系，IT 专业人员和信息工作者可以通过门户或邀请管理器 API，一次添加另一家组织中的一个或多个用户。 管理员可以使用 Azure 门户中的新门户体验 (https://portal.azure.com) 和 PowerShell 实现此目的。 信息工作者可以使用 http://myapps.microsoft.com 中的访问面板体验。 开发人员可以使用 Azure AD B2B 邀请管理器 API 创建应用程序，添加 B2B 协作用户以及自定义邀请和登记工作流。

B2B 协作用户通常是通过邀请与兑换过程登记的。 工作原理如下。

1. WoodGrove 公司的 John Doe 想要使用 Sam Oogle 的 gmail 地址 (gsamoogle@gmail.com) 来添加他

2. John 转到 WoodGrove 门户 (portal.azure.com) 或访问面板 (myapps.microsoft.com)，登录，然后将该用户添加到 WoodGrove 目录、组或应用程序。

3. John 指定一封要发送到 Sam 的自定义邀请电子邮件。

4. 完成该过程后，将在 WoodGrove AD 中创建以下用户（屏幕截图取自 portal.azure.com 中的管理员用户界面）：

  ![已添加用户](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. 添加完此用户后，Azure AD 会向 Sam 发出邀请电子邮件：

  ![发送给 Sam 的邀请邮件](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. 接下来，Sam 选择“开始”并登录。 此时，Azure AD 将使用 Sam 的令牌中的信息更新他在目录中的用户对象（屏幕截图取自 [Azure 门户](https://portal.azure.com)中的管理员用户界面）：

  ![已填充用户配置文件](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

7. 现在，Sam 的邀请已兑换，他可以访问 WoodGrove 资源，当然，管理员也可以像管理目录中的其他任何用户一样对他进行管理（屏幕截图取自 [Azure 门户](https://portal.azure.com)中的管理员用户界面）：

  ![Sam 现在是 Azure AD 中的用户](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>公共预览版功能
在 B2B 协作公共预览版功能推出后，用户为我们提供了大量的宝贵意见。 我们一直都在倾听！ 我们会将此公共预览全新版所做的改进全部打包。 下面是 B2B 协作公共预览全新版的重要功能：

1. B2B 体验的管理员用户界面增强
  - 即将在 https://portal.azure.com 上发布
  - 管理员可以邀请 B2B 用户加入目录、任何组或应用程序

2. 在访问面板中为信息工作者提供了 B2B 协作自助邀请功能：https://myapps.microsoft.com。 信息工作者可以邀请 B2B 协作用户加入他们管理的任何自助服务组或应用程序。

3. 现在，可以使用任何电子邮件地址邀请用户。 无论用户使用的是 Office365、本地 Microsoft Exchange、outlook.com 还是任何社交电子邮件地址（Gmail、Yahoo 等等），他们都可以轻松就地创建一个 Azure AD 帐户或 Microsoft 帐户，访问受邀请的组织。

4. 带有租户品牌的专业邀请电子邮件提供的好处。

5. 可以使用邀请 API 对登记过程进行广泛的自定义。

6. 针对邀请方组织中的 B2B 协作用户执行多重身份验证。

7. 可向非管理员委托邀请。

8. B2B 协作支持 PowerShell。

9. 审核和报告功能。

## <a name="next-steps"></a>后续步骤

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
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


