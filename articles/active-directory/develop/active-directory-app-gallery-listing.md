---
title: "列出 Azure Active Directory 应用程序库中的应用程序"
description: "如何列出 Azure Active Directory 库中支持单一登录的应用程序 | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>列出 Azure Active Directory 应用程序库中的应用程序


##  <a name="what-is-azure-ad-app-gallery"></a>什么是 Azure AD 应用库？

Azure AD 是一种基于云的标识服务。 [Azure AD 应用库](https://azure.microsoft.com/marketplace/active-directory/all/)是一个公用存储，所有应用程序连接器都发布在其中以用于实现单一登录和用户预配。 我们的使用 Azure AD 作为标识提供者的共同客户将查找其中发布的各种 SaaS 应用程序连接器。 IT 管理员从应用库中添加连接器，对其进行配置并将其用于单一登录和预配。 Azure AD 支持使用所有主要的联合身份验证协议（例如 SAML 2.0、OpenID Connect、OAuth 和 WS-Fed）进行单一登录。 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>将应用程序列在该库中有什么好处？

*  可以为客户提供尽可能最佳的单一登录体验。

*  简化并最大程度地减少了应用程序的配置。

*  客户可以搜索应用程序，并在库中找到它。 

*  任何客户都可以使用此集成，无论是 Azure AD SKU 免费版、基本版还是高级版。

*  可以为共同客户提供分步配置教程。

*  如果在使用 SCIM，能够为同一应用实现用户预配。


##  <a name="what-are-the-pre-requisites"></a>有哪些先决条件？

若要将某个应用程序列在 Azure AD 库中，该应用程序首先需要实现 Azure AD 支持的联合身份验证协议之一。 请从这里阅读 Azure AD 应用程序库的条款和条件。 如果使用： 

*   **OpenID Connect** - 在 Azure AD 中创建多租户应用程序并为应用程序实现 [Azure AD 许可框架](active-directory-integrating-applications.md#overview-of-the-consent-framework)。 将登录请求发送到公用终结点，以便任何客户都可以向应用程序提供许可。 你可以根据在令牌中收到的租户 ID 和用户 UPN 控制客户用户访问。 若要将应用程序与 Azure AD 进行集成，可以根据[开发人员说明](active-directory-authentication-scenarios.md)进行操作。

*   **SAML 2.0 或 WS-Fed** – 应用程序应当能够以 SP 或 IDP 模式执行 SAML/WS-Fed SSO 集成。 可以根据[用来添加自定义应用程序的说明](../active-directory-saas-custom-apps.md)将支持 SAML 2.0 的任何应用程序直接与 Azure AD 租户集成。

*   **密码 SSO** – 创建具有 HTML 登录页面的 Web 应用程序来配置[基于密码的单一登录](../active-directory-appssoaccess-whatis.md)。 基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 如果有多个用户需要共享单个帐户（例如共享组织的社交媒体应用帐户），此功能也很有用。 

## <a name="process-for-submitting-the-request-in-the-portal"></a>在门户中提交请求的流程

在测试你的应用程序集成可以使用 Azure AD 正常工作后，需要在[应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)上提交你的访问请求。 如果拥有 Office 365 帐户，则可以使用该帐户登录到此门户，否则，请使用你的 Microsoft ID（Live ID、Outlook、Hotmail，等等）进行登录。 可以看到用来请求访问的以下页面。 在文本框中提供业务理由并单击“请求访问”。 我们的团队将查看所有详细信息并相应地向你授予访问权限。 之后，你可以登录到该门户并为应用程序提交详细请求。

如果遇到与访问相关的任何问题，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

![SharePoint 门户上的访问请求](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>时间线
    
*   将 SAML 2.0 或 WS-Fed 应用程序列到库中的流程 - **7-10 个工作日**

   ![将 SAML 应用程序列到库中的时间线](./media/active-directory-app-gallery-listing/timeline.png)

*   将 OpenID Connect 应用程序列到库中的流程 - **2-5 个工作日**

   ![将 SAML 应用程序列到库中的时间线](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>升级

若要进行升级，请向 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)发送电子邮件，我们会尽快回复。

