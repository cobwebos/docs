---
title: "关于 Azure Active Directory B2B 协作预览版 | Microsoft 文档"
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
ms.date: 03/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4bfd76a00ba694096ef0b045b14beb757ba0809f
ms.lasthandoff: 03/21/2017


---

# <a name="about-the-azure-ad-b2b-collaboration-preview"></a>关于 Azure AD B2B 协作预览版
本文介绍全新 Azure Active Directory (Azure AD) B2B 协作公共预览版的目标、功能和优势。

不管你是 IT 专业人员还是信息工作者，都可以利用许多的 Azure AD B2B 协作功能。 你可以与世界各地其他组织中的合作伙伴密切合作，不管他们的规模、行业或者法规遵从与政府监管要求如何。 可以向他们提供文档、资源和应用程序的访问权限，同时对其内部数据保持完全的控制。

如果你是开发人员，可以使用 Azure AD B2B API 编写应用程序，以安全的方式将各组织凝聚在一起。 对于信息工作者用户而言，整个流程和导航都相当直接。

## <a name="how-b2b-collaboration-works"></a>B2B 协作的工作原理

在当前的预览版中，IT 专业人员和信息工作者可以通过 Azure 门户或邀请管理器 API 一次添加一个或多个用户，以此来与合作伙伴组织建立关系。

管理员可以使用 Azure 门户中的新门户体验 (https://portal.azure.com) 和 PowerShell 来建立关系。

信息工作者可以使用 http://myapps.microsoft.com 中的访问面板体验。

开发人员可以使用 Azure AD B2B 邀请管理器 API 创建应用程序，添加 B2B 协作用户以及自定义邀请和登记工作流。

B2B 协作用户通常是通过邀请与兑换过程添加的。 工作原理如下：

1. WoodGrove 公司的 John Doe 想要使用 Sam Oogle 的 gmail 地址 (gsamoogle@gmail.com) 来添加 Sam Oogle 用户。

2. John Doe 转到 WoodGrove 门户 (portal.azure.com) 或访问面板 (myapps.microsoft.com)，登录，然后将用户 Sam Oogle 添加到 WoodGrove 目录、组或应用程序。

3. John Doe 指定一封要发送到 Sam Oogle 的自定义邀请电子邮件。

4. John Doe 发送邀请后，将在 WoodGrove 的 Azure AD 中创建用户 Sam Oogle，如下所示：

  ![portal.azure.com 中的管理员用户界面](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. 创建该用户后，Azure AD 将向 Sam Oogle 发送一封邀请电子邮件：

  ![发给 Sam Oogle 的邀请邮件](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. 在邀请中，Sam Oogle 选择“开始”。  
此时将打开 Azure 门户。

7. Sam Oogle 登录到 Azure 门户。

8. Azure AD 将使用 Sam Oogle 的令牌中的信息在 Azure AD 中更新 Sam Oogle 用户对象，如下所示：

  ![Azure 门户中的 Sam Oogle 用户配置文件](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

9. 现在，Sam Oogle 已兑换邀请，可以访问 WoodGrove 资源。 管理员可以管理用户 Sam Oogle，就像 Azure AD 中的其他任何用户一样。 用户列表如下所示：

  ![Azure AD 用户列表](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>公共预览版功能
根据用户的反馈，B2B 协作公共预览全新版提供以下主要功能：

* 对于管理员：[Azure 门户](https://portal.azure.com)中的用户界面已得到增强。 例如，管理员可以邀请 B2B 用户加入目录、任何组或应用程序。  

* 对于信息工作者：[访问面板](https://myapps.microsoft.com)中提供 B2B 协作自助邀请功能。 信息工作者可以邀请 B2B 协作用户加入他们管理的任何自助服务组或应用程序。

* 允许受邀用户使用任何电子邮件地址。 无论使用的是 Office 365、本地 Microsoft Exchange、outlook.com 还是任何社交电子邮件地址（Gmail、Yahoo 等等），用户都可以创建一个 Azure AD 帐户或 Microsoft 帐户来访问受邀请组织。

* 创建带有租户品牌的专业邀请电子邮件。

* 使用邀请 API 自定义用户定位。

* 为邀请方组织中的 B2B 协作用户设置多重身份验证。

* 向非管理员委派邀请。

* 为 B2B 协作提供 PowerShell 支持。

* 提供审核和报告功能。

## <a name="help-us-shape-your-features"></a>帮助塑造你的功能
 
我们将在改进 B2B 协作的过程中不断听取你的反馈。 诚邀你在 [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)加入讨论、分享用户方案和最佳实践并分享 Azure AD B2B 协作中让你钟意的方面
 
还力邀你在 [B2B 协作意见](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas)网站提交意见并为未来的功能投票。

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
* [B2B 协作用户审核和报告](active-directory-b2b-auditing-and-reporting.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)

