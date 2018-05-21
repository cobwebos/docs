---
title: 使用 Azure AD 共享帐户 | Microsoft Docs
description: 介绍组织如何使用 Azure Active Directory 来安全共享本地应用和使用者云服务的帐户。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/13/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: b97ec4ffacead7630c267284f79f954ef03eff61
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="sharing-accounts-with-azure-ad"></a>使用 Azure AD 共享帐户
## <a name="overview"></a>概述
组织有时需要针对多人使用单个用户名和密码，这通常发生在两种情况下：

* 每个用户必须使用唯一的登录名和密码访问应用程序时（无论是本地应用还是使用者云服务，例如公司的社交媒体帐户）。
* 创建多用户环境时。 可能使用了单个具有提升特权的本地帐户，并且该帐户用于执行核心安装、管理和恢复活动。 例如，Office 365 的本地“全局管理员”帐户或 Salesforce 中的 root 帐户。

传统上，这些帐户的共享方式是通过将凭据（用户名/密码）分发给适当的人员，或者将凭据存储在多个受信任代理可以访问的共享位置。

传统的共享模式有几个缺点：

* 要使所有人都能访问新的应用程序，需要向他们分发凭据。
* 每个共享的应用程序可能都需要唯一的一组共享凭据，而用户必须记住多组凭据。 在用户必须记住许多凭据时，他们会依靠有风险的做法，风险就会随之增加。 （例如写下密码）。
* 不知道谁有权访问应用程序。
* 不知道谁访问了应用程序。
* 需要删除某个应用程序的访问权限时，必须更新凭据，并将凭据重新分发给需要访问该应用程序的所有人。

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory 帐户共享
Azure AD 提供使用共享帐户的新方法，从而可以消除这些缺点。

通过使用访问面板并选择最适合该应用程序的单一登录类型，Azure AD 管理员可以配置用户可访问的应用程序。 在这些类型中，基于密码的单一登录可在登录该应用的过程中，让 Azure AD 充当某种“代理”。

用户使用他们的组织帐户登录一次即可。 此帐户与他们平时用来访问桌面或电子邮件的帐户相同。 他们只能发现和访问分配给他们的那些应用程序。 使用共享帐户时，此应用程序列表可以包含任意数目的共享凭据。 最终用户不需要记住或写下他们可能要使用的多个帐户。

共享帐户不仅提高了监管力度和可用性，也增强了安全性。 有权使用凭据的用户看不到共享密码，而是通过协调的身份验证流程获取密码的使用权限。 此外，使用某些密码 SSO 应用程序可以选择让 Azure AD 定期滚动更新（更新）密码。 系统使用复杂的长密码来提高帐户安全性。 管理员可以轻松授予或吊销对应用程序的访问权限，还知道谁有权访问帐户以及谁曾经访问了帐户。

Azure AD 支持任何 Enterprise Mobility Suite (EMS)、高级或基本许可用户的共享帐户，包括所有类型的密码单一登录应用程序。 可以共享应用库中数千个预先集成的应用程序的帐户，并可使用[自定义 SSO 应用](active-directory-enterprise-apps-manage-sso.md)自己的密码身份验证应用程序。

支持帐户共享的 Azure AD 功能包括：

* [密码单一登录](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* 密码单一登录代理
* [组分配](active-directory-accessmanagement-self-service-group-management.md)
* 自定义密码应用
* [应用使用情况仪表板/报告](active-directory-passwords-get-insights.md)
* 最终用户访问门户
* [应用代理](manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>共享帐户
要使用 Azure AD 来共享帐户，需要：

* 添加应用程序[应用库](https://azure.microsoft.com/marketplace/active-directory/)或[自定义应用程序](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
* 将应用程序配置为使用密码单一登录 (SSO)
* 使用[基于组的分配](active-directory-accessmanagement-group-saasapps.md)，并选择输入共享凭据的选项
* 可选：在某些应用程序（例如 Facebook、Twitter 或 LinkedIn）中，可启用 [Azure AD 自动轮换密码](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)选项

还可使用多重身份验证 (MFA) 提高共享帐户的安全性（深入了解[使用 Azure AD 保护应用程序](authentication/concept-mfa-whichversion.md)），并可使用 [Azure AD 自助服务](active-directory-accessmanagement-self-service-group-management.md)“组管理”来委派有关谁有权访问应用程序的管理权。

## <a name="related-articles"></a>相关文章
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [使用条件性访问保护应用](active-directory-conditional-access-azure-portal.md)
* [自助服务组管理/SSAA](active-directory-accessmanagement-self-service-group-management.md)

