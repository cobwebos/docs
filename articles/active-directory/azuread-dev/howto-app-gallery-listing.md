---
title: 在 Azure AD 应用程序库中列出应用 |微软文档
description: 了解如何在 Azure Active Directory 应用库中列出支持单一登录的应用程序
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: fbdae8b8506731f13ff08e877ea8eedb6d57fa78
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666939"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>在 Azure Active Directory 应用程序库中列出你的应用程序

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

本文演示如何在 Azure 活动目录 （Azure AD） 应用程序库中列出应用程序、实现单一登录 （SSO） 以及管理列表。

## <a name="what-is-the-azure-ad-application-gallery"></a>什么是 Azure AD 应用程序库？

- 可以为客户提供尽可能最佳的单一登录体验。
- 简化并最小化了应用程序的配置。
- 在库中快速搜索应用程序。
- 免费、基本和高级 Azure AD 客户都可以使用此集成。
- 共同客户可以获得分步配置教程。
- 使用跨域标识管理系统[（SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)） 的客户可以使用同一应用的预配。

## <a name="prerequisites"></a>先决条件

- 对于联合应用程序（打开 ID 和 SAML/WS-Fed），应用程序必须支持软件即服务 （SaaS） 模型，以便在 Azure AD 应用库中列出。 企业库应用程序必须支持多个客户配置，而不是任何特定客户。
- 对于打开 ID 连接，应用程序必须多租户，并且必须为应用程序正确实现[Azure AD 同意框架](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)。 用户可以将登录请求发送到公共终结点，以便任何客户都可以同意应用程序。 你可以根据在令牌中收到的租户 ID 和用户 UPN 来控制用户访问。
- 对于 SAML 2.0/WS-Fed，您的应用程序必须能够在 SP 或 IDP 模式下执行 SAML/WS-Fed SSO 集成。 在提交请求之前，请确保此功能工作正常。
- 对于密码 SSO，请确保应用程序支持表单身份验证，以便可以执行密码保管，使单一登录按预期工作。
- 需要一个永久帐户来测试至少两个用户。

**如何为开发人员获取 Azure AD？**

您可以获得一个免费的测试帐户与所有高级 Azure AD 功能 - 90 天免费，并可以延长，只要你做开发工作：https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>在门户中提交请求

测试应用程序集成是否与 Azure AD 配合使用后，请在[Microsoft 应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交应用程序请求。

如果在登录后出现以下页面，请与 Azure [AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)联系。 提供要用于提交请求的电子邮件帐户。 首选企业电子邮件地址。 [name@yourbusiness.com](mailto:name@yourbusiness.com) Azure AD 团队将在 Microsoft 应用程序网络门户中添加帐户。

![访问 SharePoint 门户上的请求消息](./media/howto-app-gallery-listing/errorimage.png)

添加帐户后，您可以登录到 Microsoft 应用程序网络门户。

如果在登录后出现以下页面，请提供需要访问的文本框中的业务理由。 然后选择 **"请求访问**"。

  ![SharePoint 门户上的业务理由框](./media/howto-app-gallery-listing/accessrequest.png)

我们的团队将审核详细信息并相应地为你提供访问权限。 请求获得批准后，您可以通过在主页上选择 **"提交请求 （ISV）"** 磁贴登录门户并提交请求。

![在主页上提交请求 （ISV） 磁贴](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>登录门户时的问题

如果您在登录时看到此错误，则下面是有关该问题的详细信息，这是修复此问题的方式。

* 如果您的登录被阻止，如下所示：

  ![解决库中应用程序的问题](./media/howto-app-gallery-listing/blocked.png)

**发生了什么事情：**

来宾用户联合到主租户，该租户也是 Azure AD。 来宾用户处于高风险。 微软不允许高风险用户访问其资源。 所有高风险用户（员工或客人/供应商）必须修复/关闭其风险才能访问 Microsoft 资源。 对于来宾用户，此用户风险来自主租户，策略来自资源租户（本例中为 Microsoft）。
 
**安全解决方案：**

* MFA 注册来宾用户修复自己的用户风险。 这可以通过执行安全密码更改或重置的来宾用户（https://aka.ms/sspr)在其主租户处）完成（这需要家庭租户的 MFA 和 SSPR）。 必须在 Azure AD 上启动安全密码更改或重置，而不是打开前置。

* 来宾用户让管理员修复其风险。 在这种情况下，管理员将执行密码重置（临时密码生成）。 这不需要身份保护。 来宾用户的管理员可以转到https://aka.ms/RiskyUsers并单击"重置密码"。

* 来宾用户有他们的管理员关闭/消除他们的风险。 同样，这不需要身份保护。 管理员可以转到https://aka.ms/RiskyUsers并单击"消除用户风险"。 但是，管理员必须尽职尽责，以确保这是一个误报风险评估，然后再关闭用户风险。 否则，他们可以通过不进行调查就压制风险评估，从而将自身和微软的资源置于危险之中。

> [!NOTE]
> 如果访问有任何问题，请与 Azure [AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)联系。

## <a name="implement-sso-by-using-the-federation-protocol"></a>使用联合协议实现 SSO

若要将某个应用程序列在 Azure AD 应用库中，首先需要实现 Azure AD 支持的以下联合身份验证协议之一。 您还需要同意 Azure AD 应用程序库条款和条件。 阅读[本网站](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)上 Azure AD 应用程序库的条款和条件。

- **OpenID 连接**：要使用 Open ID 连接协议将应用程序与 Azure AD 集成，请按照[开发人员的说明](v1-authentication-scenarios.md)操作。

    ![在库中列出 OpenID 连接应用程序](./media/howto-app-gallery-listing/openid.png)

    * 如果要使用 OpenID 连接将应用程序添加到库中的列表，请选择**OpenID 连接& OAuth 2.0，** 如图所示。
    * 如果访问有任何问题，请与 Azure [AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)联系。

- **SAML 2.0**或**WS-Fed：** 如果应用支持 SAML 2.0，则可以按照[添加自定义应用程序的说明](../active-directory-saas-custom-apps.md)将其直接与 Azure AD 租户集成。

  ![在库中列出 SAML 2.0 或 WS-Fed 应用程序](./media/howto-app-gallery-listing/saml.png)

  * 如果要使用**SAML 2.0**或**WS-Fed**将应用程序添加到库中列表，请选择**SAML 2.0/WS-Fed，** 如下所示。

  * 如果访问有任何问题，请与 Azure [AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)联系。

## <a name="implement-sso-by-using-the-password-sso"></a>使用密码 SSO 实现 SSO

创建具有 HTML 登录页的 Web 应用程序来配置[基于密码的单一登录](../manage-apps/what-is-single-sign-on.md)。 基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 对于多个用户需要共享单个帐户（例如，到组织的社交媒体应用帐户）的方案，它非常有用。

![在库中列出密码 SSO 应用程序](./media/howto-app-gallery-listing/passwordsso.png)

* 如果要使用密码 SSO 将应用程序添加到库中的列表，请选择 **"密码 SSO"，** 如下所示。
* 如果访问有任何问题，请与 Azure [AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)联系。

## <a name="request-for-user-provisioning"></a>用户预配请求

按照下图所示的过程请求用户预配。

   ![用户预配请求](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>更新或删除现有列表

要更新或删除 Azure AD 应用库中的现有应用程序，首先需要在[应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交请求。 如果你有 Office 365 帐户，请使用该帐户登录到此门户。 否则，请使用 Microsoft 帐户（如 Outlook 或 Hotmail）登录。

- 选择如下图像所示的相应选项。

    ![在库中列出 SAML 应用程序](./media/howto-app-gallery-listing/updateorremove.png)

    * 要更新现有应用程序，请根据您的要求选择适当的选项。
    * 要从 Azure AD 应用库中删除现有应用程序，请选择**从库中删除我的应用程序列表**。
    * 如果访问有任何问题，请与 Azure [AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)联系。

## <a name="list-requests-by-customers"></a>列出客户的请求

客户可以通过选择客户 > 提交**新请求****的应用请求提交**申请以列出申请。

![显示客户请求的应用磁贴](./media/howto-app-gallery-listing/customer-submit-request.png)

以下是客户请求的应用程序流。

![显示客户请求的应用流](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>时间线

在库中列出 SAML 2.0 或 WS-Fed 应用程序的过程时间表为 7 到 10 个工作日。

  ![在库中列出 SAML 应用程序的时间表](./media/howto-app-gallery-listing/timeline.png)

在库中列出 OpenID Connect 应用程序的过程的时间线是 2 到 5 个工作日。

  ![在库中列出 OpenID 连接应用程序的时间表](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>升级

对于任何升级，请发送电子邮件至 Azure SaaSApplicationIntegrations@service.microsoft.com [AD SSO 集成团队](mailto:SaaSApplicationIntegrations@service.microsoft.com)，我们将尽快做出响应。