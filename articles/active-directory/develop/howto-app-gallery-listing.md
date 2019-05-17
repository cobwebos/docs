---
title: 在 Azure Active Directory 应用程序库中列出应用程序 | Microsoft 文档
description: 了解如何在 Azure Active Directory 应用库中列出支持单一登录的应用程序
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 005e5c92a16760d8eec5dc37526f4b1f2dbd751c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540536"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>如何：在 Azure Active Directory 应用程序库中列出你的应用程序

本文演示如何列出 Azure AD 应用程序库中的应用程序、 单一登录 (SSO)、 实现和管理列表。

## <a name="what-is-the-azure-ad-application-gallery"></a>什么是 Azure AD 应用程序库？

- 可以为客户提供尽可能最佳的单一登录体验。
- 简化并最小化了应用程序的配置。
- 在库中快速搜索应用程序。
- 免费、基本和高级 Azure AD 客户都可以使用此集成。
- 共同客户可以获得分步配置教程。
- 使用 SCIM 的客户可以将预配用于同一应用。

## <a name="prerequisites"></a>必备组件

- 对于联合应用程序（Open ID 和 SAML/WS-Fed），应用程序必须支持 SaaS 模型才能在 Azure AD 库中列出。 企业库应用程序应支持多个客户配置，而不是任何特定客户。

- 对于 Open ID Connect，应用程序应该是多租户的，并且应为应用程序正确实现 [Azure AD 许可框架](consent-framework.md)。 用户可以将登录请求发送到公用终结点，以便任何客户都可以向应用程序提供许可。 你可以根据在令牌中收到的租户 ID 和用户 UPN 来控制用户访问。

- 对于 SAML 2.0/WS-Fed，应用程序需要能够以 SP 或 IDP 模式执行 SAML/WS-Fed SSO 集成。 请确保此功能在提交请求之前工作正常。

- 对于密码 SSO，请确保你的应用程序支持窗体身份验证，以便可以进行密码保管，从而让单一登录按预期工作。

- 对于自动用户预配请求，应在库中列出应用程序，并使用 SAML 2.0/WS-Fed 启用单一登录功能。 如果尚未列出，可以在门户上一起请求 SSO 和用户预配。

>[!NOTE]
>我们正在运行与大量的 SCIM 连接器的请求，因此我们已停止在我们的门户上执行新请求。 请保存你的请求上进一步通知之前。 我们这种延迟，这可能会造成任何不便的道歉。

## <a name="submit-the-request-in-the-portal"></a>在门户中提交请求

在测试你的应用程序集成可以使用 Azure AD 正常工作后，在[应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)上提交你的访问请求。 如果你有 Office 365 帐户，请使用该帐户登录到此门户。 如果没有，请使用 Microsoft 帐户（例如 Outlook 或 Hotmail）进行登录。

如果登录后显示以下页面，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)并提供要用于提交请求的电子邮件帐户。 然后 Azure AD 团队将在 Microsoft Application Network Portal 中添加该帐户。

![SharePoint 门户上的访问请求](./media/howto-app-gallery-listing/errorimage.png)

添加帐户后，你可以登录到 Microsoft Application Network Portal。

如果登录后出现以下页面，请在文本框中提供需要进行访问的业务理由，然后选择“请求访问”。

  ![SharePoint 门户上的访问请求](./media/howto-app-gallery-listing/accessrequest.png)

我们的团队将审核详细信息并相应地为你提供访问权限。 请求获得批准后，单击主页上的“提交请求(ISV)”磁贴即可登录门户并提交请求。

![SharePoint 门户主页](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> 如果你有关于访问的任何问题，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="implementing-sso-using-federation-protocol"></a>使用联合身份验证协议实现 SSO

若要将某个应用程序列在 Azure AD 应用库中，首先需要实现 Azure AD 支持的以下联合身份验证协议之一并同意 Azure AD 应用程序库条款和条件。 请在[此处](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)阅读 Azure AD 应用程序库的条款和条件。

- **OpenID Connect**：若要使用 Open ID Connect 协议将应用程序与 Azure AD 集成，请遵循[开发人员的说明](authentication-scenarios.md)。

    ![将 OpenID Connect 应用程序列到库中的时间线](./media/howto-app-gallery-listing/openid.png)

    * 如果想要使用 OpenID Connect 将你的应用程序添加到库中的列表，请如上所述选择“OpenID Connect & OAuth 2.0”。
    * 如果你有关于访问的任何问题，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 

- **SAML 2.0** 或 **WS 联合身份验证**：如果你的应用支持 SAML 2.0，则可以根据[用来添加自定义应用程序的说明](../active-directory-saas-custom-apps.md)将其直接与 Azure AD 租户集成。

  ![将 SAML 2.0 或 WS-Fed 应用程序列到库中的时间线](./media/howto-app-gallery-listing/saml.png)

  * 如果想要使用 SAML 2.0 或 WS-Fed 将你的应用程序添加到库中的列表，请如上所述选择“SAMl 2.0/WS-Fed”。
  * 如果你有关于访问的任何问题，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="implementing-sso-using-password-sso"></a>使用密码 SSO 实现 SSO

创建具有 HTML 登录页的 Web 应用程序来配置[基于密码的单一登录](../manage-apps/what-is-single-sign-on.md)。 基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 如果有多个用户需要共享单个帐户（例如共享组织的社交媒体应用帐户），此功能也很有用。

![将 Password SSO 应用程序列到库中的时间线](./media/howto-app-gallery-listing/passwordsso.png)

* 如果想要使用 Password SSO 将你的应用程序添加到库中的列表，请如上所述选择“Password SSO”。
* 如果你有关于访问的任何问题，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="updateremove-existing-listing"></a>更新/删除现有列表

若要更新或删除 Azure AD 应用库中的现有应用程序，首先需要在[应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交请求。 如果你有 Office 365 帐户，请使用该帐户登录到此门户。 如果没有，请使用 Microsoft 帐户（例如 Outlook 或 Hotmail）进行登录。

- 选择适当的选项，如下图所示：

    ![将 SAML 应用程序列到库中的时间线](./media/howto-app-gallery-listing/updateorremove.png)

    * 如果要更新现有应用程序，请选择“更新现有应用程序列表”。
    * 如果要从 Azure AD 库中删除现有应用程序，请选择“删除现有应用程序列表”。
    * 如果你有关于访问的任何问题，请联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 

## <a name="listing-requests-by-customers"></a>列出由客户的请求

客户可以提交的通过单击列出的应用程序请求**应用程序请求的客户** -> **提交新请求**。

![客户请求的应用磁贴](./media/howto-app-gallery-listing/customer-submit-request.png)

下面是客户的流请求的应用程序-

![客户要求的应用程序流](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>时间线

在库中列出 SAML 2.0 或 WS-Fed 应用程序这一过程的时间线是 7-10 个工作日。

   ![将 SAML 应用程序列到库中的时间线](./media/howto-app-gallery-listing/timeline.png)

在库中列出 OpenID Connect 应用程序这一过程的时间线是 2-5 个工作日。

   ![将 SAML 应用程序列到库中的时间线](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>升级

若要进行升级，请向 [Azure AD SSO 集成团队](mailto:SaaSApplicationIntegrations@service.microsoft.com)发送电子邮件，电子邮件地址为 SaaSApplicationIntegrations@service.microsoft.com，我们会尽快进行回复。
