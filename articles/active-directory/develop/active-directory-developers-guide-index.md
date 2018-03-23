---
title: 针对开发人员的 Azure Active Directory | Microsoft Docs
description: Azure Active Directory 开发人员指南中的所有指南和参考文章列表。
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/08/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: ccad1f12e4f99074763f116596b4467e822d9f35
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="articles-in-the-azure-ad-developer-guide"></a>Azure AD 开发人员指南中的文章
下面是 Azure Active Directory 开发人员指南中涉及的所有主题的列表。

## <a name="guides"></a>指南
这些文章介绍 Azure Active Directory 的特定概念和功能。

|                                                                                                                                 |  |
| ------------------------------------------------------------------------------------------------------------------------------- | --- |
| [多租户应用](active-directory-devhowto-multi-tenant-overview.md)                                                         | 如何登录任何 Microsoft 工作帐户 |
| [OAuth 与 OpenID Connect](active-directory-protocols-openid-connect-code.md)                                                     | 如何使用我们的新式身份验证协议使用户登录并调用 Web API |
| [SAML 2.0](active-directory-saml-protocol-reference.md)                                                                         | 如何使用 SAML 身份验证协议使用户登录 |
| [应用注册](active-directory-integrating-applications.md)                                                                | 如何在 Azure AD 中注册应用 |
| [品牌准则](active-directory-branding-guidelines.md)                                                                  | 集成 Microsoft 登录时要使用的文本、样式和术语 |
| [移动 SSO 和桌面 SSO](active-directory-sso-android.md)                                                                         | 如何让单一登录在本机应用程序中正常工作 |
| [Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md)                                                                 | 如何邀请外部用户加入 Azure AD 租户 |
| [使用 SCIM 2.0 进行用户预配](../active-directory-scim-provisioning.md)                                                     | 如何使用 SCIM 协议从 Azure AD 租户同步用户 |
| [Azure AD Graph API](active-directory-graph-api.md)                                                                             | 如何使用 Microsoft REST API 查询用户、组和其他数据 |
| [AppSource 认证](active-directory-devhowto-appsource-certified.md)                                                     | 如何在 Microsoft AppSource 上发布应用程序 |
| [Azure AD 应用库](active-directory-app-gallery-listing.md)                                                                 |如何将应用程序发布到 Azure AD 应用库|
| [Office 365 卖家仪表板](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)               | 如何在 O365 卖家仪表板上发布应用程序 |
| [Visual Studio 连接服务](vs-active-directory-dotnet-getting-started.md)                                               | 如何在 Visual Studio 中使用 Azure AD 与 MVC 项目的集成 |
| [Windows Server ADFS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers) | 如何使用 ADFS 与本地 Active Directory 集成 |
| [ADAL 错误处理最佳做法](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-adal-error-handling) | 如何构建弹性错误处理和启用日志记录 |

## <a name="reference"></a>引用
以下文章提供了有关 REST 和身份验证库 API、协议、错误、代码示例和终结点的基础信息。

|                                                                                     | |
| ----------------------------------------------------------------------------------- | --- |
| [身份验证库 (ADAL)](active-directory-authentication-libraries.md)     | Azure AD 提供的库和 SDK 的概述 |
| [身份验证协议](active-directory-authentication-protocols.md)            | Azure AD 实现的 OAuth、OpenID Connect 和 SAML 的详细信息 |
| [术语表](active-directory-dev-glossary.md)                                        | 本文档通篇使用的术语和单词的定义 |
| [应用程序对象](active-directory-application-objects.md)                      | Azure AD 的应用程序模型的说明 |
| [应用程序清单](active-directory-application-manifest.md)                    | 构成 Azure AD 应用注册的元素的说明 |
| [令牌生存期](../active-directory-configurable-token-lifetimes.md)              | Azure AD 令牌和自定义指令的默认生存期 |
| [服务限制和局限性](../active-directory-service-limits-restrictions.md) | Azure AD 实体和对象的限制 |
| [代码示例](active-directory-code-samples.md)                                    | 所有 Azure AD 代码示例的列表 |