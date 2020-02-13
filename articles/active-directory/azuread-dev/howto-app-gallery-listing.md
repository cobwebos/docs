---
title: 在 Azure AD 应用程序库中列出你的应用 |Microsoft Docs
description: 了解如何在 Azure Active Directory 应用库中列出支持单一登录的应用程序
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 99c7d7fccf674fe8cda9d1f64cdf303f1e7764fd
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164833"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>在 Azure Active Directory 应用程序库中列出你的应用程序

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

本文介绍如何在 Azure Active Directory （Azure AD）应用程序库中列出应用程序，如何实现单一登录（SSO）和管理列表。

## <a name="what-is-the-azure-ad-application-gallery"></a>什么是 Azure AD 应用程序库？

- 可以为客户提供尽可能最佳的单一登录体验。
- 简化并最小化了应用程序的配置。
- 在库中快速搜索应用程序。
- 免费、基本和高级 Azure AD 客户都可以使用此集成。
- 共同客户可以获得分步配置教程。
- 使用系统进行跨域标识管理（[SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)）的客户可以使用同一应用的预配。

## <a name="prerequisites"></a>必备条件

- 对于联合应用程序（开放 ID 和 SAML/WS 进纸），应用程序必须支持 "软件即服务" （SaaS）模型，才能在 Azure AD 应用库中列出。 企业库应用程序必须支持多个客户配置，而不是任何特定客户。
- 对于 Open ID Connect，应用程序必须是 multitenanted 的，并且必须正确地为应用程序实现[Azure AD 许可框架](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。 用户可以将登录请求发送到公共终结点，以便任何客户都可以向应用程序提供许可。 你可以根据在令牌中收到的租户 ID 和用户 UPN 来控制用户访问。
- 对于 SAML 2.0/WS 馈送，应用程序必须能够在 SP 或 IDP 模式下执行 SAML/WS-FEDERATION SSO 集成。 提交请求之前，请确保此功能正常工作。
- 对于密码 SSO，请确保你的应用程序支持窗体身份验证，以便可以进行密码保险存储以使单一登录能够按预期方式工作。
- 你需要一个永久帐户来测试至少注册了两个用户。

**如何为开发人员获取 Azure AD？**

你可以获取免费的测试帐户，其中包含所有高级 Azure AD 功能-90 天免费版，只要你使用它执行开发工作，就可以扩展： https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>在门户中提交请求

在测试应用程序集成是否适用于 Azure AD 后，请在[应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交你的访问请求。 如果你有 Office 365 帐户，请使用该帐户登录到此门户。 如果没有，请使用您的 Microsoft 帐户（例如 Outlook 或 Hotmail）登录。

如果在登录后出现以下页面，请联系[AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 提供要用于提交请求的电子邮件帐户。 Azure AD 团队将在 Microsoft 应用程序网络门户中添加该帐户。

![SharePoint 门户上的访问请求消息](./media/howto-app-gallery-listing/errorimage.png)

添加帐户后，你可以登录到 Microsoft 应用程序网络门户。

如果在登录后出现以下页，请在文本框中提供需要访问的业务理由。 然后选择 "**请求访问权限**"。

  ![SharePoint 门户上的业务理由框](./media/howto-app-gallery-listing/accessrequest.png)

我们的团队将审核详细信息并相应地为你提供访问权限。 批准请求后，可以通过在主页上选择 "**提交请求（ISV）** " 磁贴来登录到门户并提交请求。

![主页上的提交请求（ISV）磁贴](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>登录到门户的问题

如果在登录时看到此错误，请参阅此问题的详细信息，这是解决该问题的方法。

* 如果登录被阻止，如下所示：

  ![解决库中的应用程序问题](./media/howto-app-gallery-listing/blocked.png)

**发生了什么事情：**

来宾用户与家庭租户联合，这也是 Azure AD。 来宾用户处于高风险状态。 Microsoft 不允许高风险用户访问其资源。 所有高风险用户（员工或来宾/供应商）必须修正/关闭他们访问 Microsoft 资源的风险。 对于来宾用户，此用户面临的风险来自于 home 租户，而策略来自资源租户（在本例中为 Microsoft）。
 
**安全解决方案：**

* MFA 注册的来宾用户修正其自己的用户风险。 这可以由来宾用户执行安全的密码更改或重置（ https://aka.ms/sspr) 在其主租户上，这需要在家庭租户上进行 MFA 和 SSPR）。 必须在 Azure AD 而不是本地上启动安全密码更改或重置。

* 来宾用户的管理员可以纠正他们的风险。 在这种情况下，管理员将执行密码重置（临时密码生成）。 这不需要 Identity Protection。 来宾用户的管理员可以前往 https://aka.ms/RiskyUsers 并单击 "重置密码"。

* 来宾用户的管理员需要关闭/消除其风险。 同样，这不需要 Identity Protection。 管理员可以 https://aka.ms/RiskyUsers，然后单击 "消除用户风险"。 但是，管理员必须执行 "截止努力"，以确保在关闭用户风险之前，这是误报风险评估。 否则，他们会通过抑制风险评估而不进行调查，使其和 Microsoft 的资源面临风险。

> [!NOTE]
> 如果访问有任何问题，请联系[AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="implement-sso-by-using-the-federation-protocol"></a>使用联合身份验证协议实现 SSO

若要将某个应用程序列在 Azure AD 应用库中，首先需要实现 Azure AD 支持的以下联合身份验证协议之一。 还需要同意 Azure AD 应用程序库的条款和条件。 阅读[本网站](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)上 Azure AD 应用程序库的条款和条件。

- **OpenID connect**：若要通过使用 Open ID Connect 协议将应用程序与 Azure AD 进行集成，请按照[开发人员的说明](v1-authentication-scenarios.md)进行操作。

    ![在库中列出 OpenID Connect 应用程序](./media/howto-app-gallery-listing/openid.png)

    * 如果要使用 OpenID Connect 将应用程序添加到库中，请选择 " **Openid connect & OAuth 2.0** "，如下所示。
    * 如果访问有任何问题，请联系[AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

- **SAML 2.0**或**ws-addressing**：如果应用支持 SAML 2.0，则可以按照[说明添加自定义应用程序](../active-directory-saas-custom-apps.md)，将其与 Azure AD 租户直接集成。

  ![在库中列出 SAML 2.0 或 WS 进纸应用程序](./media/howto-app-gallery-listing/saml.png)

  * 如果要使用**SAML 2.0**或**ws-addressing**将应用程序添加到库中，请选择 " **saml 2.0/ws-** 已显示"。

  * 如果访问有任何问题，请联系[AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="implement-sso-by-using-the-password-sso"></a>使用密码 SSO 实现 SSO

创建具有 HTML 登录页的 Web 应用程序来配置[基于密码的单一登录](../manage-apps/what-is-single-sign-on.md)。 基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 在多个用户需要共享单个帐户（例如，组织的社交媒体应用帐户）的情况下，此方法也非常有用。

![在库中列出密码 SSO 应用程序](./media/howto-app-gallery-listing/passwordsso.png)

* 如果要使用密码 SSO 将应用程序添加到库中，请选择 "**密码 sso** "，如所示。
* 如果访问有任何问题，请联系[AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="request-for-user-provisioning"></a>用户预配请求

按照下图所示的过程请求用户预配。

   ![用户预配请求](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>更新或删除现有列表

若要更新或删除 Azure AD 应用库中的现有应用程序，首先需要在[应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交请求。 如果你有 Office 365 帐户，请使用该帐户登录到此门户。 如果没有，请使用您的 Microsoft 帐户（例如 Outlook 或 Hotmail）登录。

- 选择相应的选项，如下图所示。

    ![在库中列出 SAML 应用程序](./media/howto-app-gallery-listing/updateorremove.png)

    * 若要更新现有应用程序，请根据您的要求选择适当的选项。
    * 若要从 Azure AD 应用库中删除现有应用程序，请从库中选择 "**删除我的应用程序列表**"。
    * 如果访问有任何问题，请联系[AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

## <a name="list-requests-by-customers"></a>列出客户的请求

客户可以通过选择**客户的应用请求** > **提交新请求**来提交列出应用程序的请求。

![显示客户请求的应用磁贴](./media/howto-app-gallery-listing/customer-submit-request.png)

下面是客户请求的应用程序的流程。

![显示客户请求的应用流](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>时间线

在库中列出 SAML 2.0 或 WS 进纸应用程序的过程的时间线是7到10个工作日内。

  ![用于在库中列出 SAML 应用程序的时间线](./media/howto-app-gallery-listing/timeline.png)

在库中列出 OpenID Connect 应用程序的过程的时间线是2到5个工作日。

  ![用于在库中列出 OpenID Connect 应用程序的时间线](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>升级

对于任何升级，请将电子邮件发送到 SaaSApplicationIntegrations@service.microsoft.com[AZURE AD SSO 集成团队](mailto:SaaSApplicationIntegrations@service.microsoft.com)，并尽快做出回复。
