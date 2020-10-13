---
title: 将应用发布到 Azure AD 应用库
description: 了解如何列出在 Azure Active Directory 应用库中支持单一登录的应用程序。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: dc271fa768bee66107e66a1b8d4f16c1188ce418
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89439738"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>将应用发布到 Azure AD 应用库

可以在 Azure AD 应用库中发布应用。 当你的应用程序发布时，它将显示为客户在将应用添加到其租户时的选项。 

将应用添加到 Azure AD 库的一些优点包括：

- 客户找到你的应用程序的最佳单一登录体验。
- 简化并最小化了应用程序的配置。
- 在库中快速搜索应用程序。
- 免费、基本和高级 Azure AD 客户都可以使用此集成。
- 共同客户可以获得分步配置教程。

此外，当客户使用 Azure AD 作为应用的标识提供者时，有许多好处。 其中包括：

- 为用户提供单一登录。 使用 SSO，可让客户更轻松地使用单一登录来降低支持成本。 如果启用一次单击 SSO，则客户的 IT 管理员无需了解如何配置应用程序以在其组织中使用。 若要详细了解单一登录，请参阅[什么是单一登录？](../manage-apps/what-is-single-sign-on.md)。
- 可以在 Microsoft 365 应用程序库、Microsoft 365 应用程序启动器和 Office.com 上的 Microsoft 搜索中发现你的应用程序。 
- 集成的应用管理。 若要详细了解 Azure AD 中的应用管理，请参阅 [什么是应用程序管理？](../manage-apps/what-is-application-management.md)。
- 应用可以使用 [图形 API](https://docs.microsoft.com/graph/) 来访问在 Microsoft 生态系统中推动用户工作效率的数据。
- 应用程序特定的文档与 Azure AD 团队共同生成，以便我们的共同客户更轻松地采用。
- 为客户提供完全管理其员工和来宾身份身份验证和授权的功能。
- 将所有帐户管理和合规性责任放入这些标识的客户所有者。
- 为特定的标识提供者、组或用户提供启用或禁用 SSO 以满足其业务需求的能力。
- 增加 marketability 和 adoptability。 许多大型组织要求 (或努力做到) 其员工在所有应用程序中都有无缝的 SSO 体验。 简化 SSO 非常重要。
- 减少最终用户的摩擦，这可能会增加最终用户的使用并增加收入。
- 使用系统进行跨域标识管理 ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) 的客户可以对同一应用使用预配。
- 当用户使用 Azure AD SSO 登录到应用程序并无需单独的凭据时，添加安全性和便利性。

> [!TIP]
> 当你通过购买或订阅向你的应用程序提供供其他公司使用的应用程序时，你可以让你的应用程序在其自己的 Azure 租户中提供给客户。 这称为创建多租户应用程序。 有关此概念的概述，请参阅 [Azure 中的多租户应用程序](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) 和 Azure Active Directory 中的 [租户](single-and-multi-tenant-apps.md)。

> [!IMPORTANT]
> 若要在 Azure AD 库中发布应用程序，你必须同意特定的条款和条件。 在开始之前，请确保阅读并同意 [条款和条件](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)。

在 Azure AD 应用库中发布应用的步骤如下：
1. 为应用选择正确的单一登录标准。
2. 在应用程序中实现单一登录。
3. 创建 Azure 租户并测试应用。
4. 创建和发布文档。
5. 提交你的应用。
6. 加入 Microsoft 合作伙伴网络。


## <a name="prerequisites"></a>必备条件

你需要一个永久帐户来测试至少注册了两个用户。


## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>步骤 1-为应用选择正确的单一登录标准

若要列出 Azure AD 应用库中的应用程序，需要至少实现一个受支持的单一登录选项。 若要了解单一登录选项以及客户如何在 Azure AD 中配置这些选项，请参阅 [SSO 选项](../manage-apps/sso-options.md)。

下表比较了主标准：打开身份验证 2.0 (OAuth 2.0) 与 OpenID Connect (OIDC) ，安全断言标记语言 (SAML) 和 Web Services 联合身份验证 (WS-ADDRESSING) 。

| 功能| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| 基于 Web 的单一登录| √| √ |
| 基于 Web 的单一注销| √| √ |
| 基于移动设备的单一登录| √| √* |
| 基于移动的单一登录| √| √* |
| 移动应用程序的条件性访问策略| √| X |
| 移动应用程序的无缝 MFA 体验| √| X |
| 访问 Microsoft Graph| √| X |

* 但 Microsoft 不提供示例或指导。

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 和 OpenID Connect
OAuth 2.0 是一种 [行业标准](https://oauth.net/2/) 的授权协议。 OpenID Connect (OIDC) 是在 OAuth 2.0 协议基础之上构建的 [行业标准](https://openid.net/connect/) 标识身份验证层。 

**选择 OAuth/OIDC 的原因**
- 利用这些协议中的固有授权，应用程序可以通过 Microsoft Graph API 访问丰富的用户和组织数据并与之集成。
- 简化客户对应用程序采用 SSO 时的最终用户体验。 您可以轻松地定义必要的权限集，然后这些权限集会自动代表管理员或最终用户同意。
- 使用这些协议，客户可以使用条件性访问和多重身份验证 (MFA) 策略来控制对应用程序的访问。 
- Microsoft [跨多个技术平台](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) 提供库和代码示例，以帮助你进行开发。  

**需要考虑的事项**
- 如果你已经为应用程序实现了基于 SAML 的单一登录，则可能不希望实现新的标准以使你的应用程序在库中进行。

### <a name="saml-20-or-ws-fed"></a>SAML 2.0 或 WS-Fed

SAML 是适用于 web 应用程序的成熟且广泛采用的 [单一登录标准](https://www.oasis-open.org/standards#samlv2.0) 。 若要了解有关 Azure 如何使用 SAML 的详细信息，请参阅 [Azure 如何使用 saml 协议](active-directory-saml-protocol-reference.md)。 

Web Services 联合身份验证 (的 WS) 是一个 [行业标准](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) ，通常用于使用 .net 平台开发的 web 应用程序。

**选择 SAML 的原因**
- SAML 2.0 是一种成熟的标准，大多数技术平台支持 SAML 2.0 的开源库。 
- 可以为客户提供管理界面来配置 SAML SSO。 它们可以配置 SAML SSO for Microsoft Azure AD 和任何其他支持 SAML 的标识提供者。

**需要考虑的事项**
- 将 SAML 2.0 或 WSFed 协议用于移动应用程序时，某些条件访问策略（包括多重身份验证 (MFA) ）将具有降级的体验。
- 如果要访问 Microsoft Graph，则需要通过 OAuth 2.0 实现授权才能生成必要的令牌。 

### <a name="password-based"></a>基于密码
基于密码的 SSO 也称为密码保管，可用于管理不支持标识联合的 Web 应用程序中的用户访问权限和密码。 在多个用户需要共享单个帐户（例如，组织的社交媒体应用帐户）的情况下，此方法也非常有用。


## <a name="step-2---implement-single-sign-on-in-your-app"></a>步骤 2-在应用中实现单一登录
库中的每个应用都必须实现某个受支持的单一登录选项。 若要了解有关支持的选项的详细信息，请参阅 [SSO 选项](../manage-apps/sso-options.md)。

有关 OAuth 和 OIDC 的详细说明，请参阅 [身份验证模式](v2-app-types.md) 和 [Azure active Directory 代码示例](sample-v2-code.md)的指导。

对于 SAML 和 WS-FEDERATION，你的应用程序必须能够在 SP 或 IDP 模式下进行 SSO 集成。 提交请求之前，请确保此功能正常工作。

若要了解有关身份验证的详细信息，请参阅 [什么是身份验证？](../azuread-dev/v1-authentication-scenarios.md)。

> [!IMPORTANT]
> 对于 (OpenID 和 SAML/WS 进) 的联合应用程序，该应用程序必须支持软件即服务 (SaaS) 模型。 Azure AD 库应用程序必须支持多个客户配置，且不应特定于任何单个客户。

### <a name="implement-oauth-20-and-openid-connect"></a>实现 OAuth 2.0 和 OpenID Connect

对于 OpenID Connect，应用程序必须是多租户的，并且必须正确地为应用程序实现 [Azure AD 许可框架](consent-framework.md) 。 用户可以将登录请求发送到公共终结点，以便任何客户都可以向应用程序提供许可。 你可以根据在令牌中收到的租户 ID 和用户 UPN 来控制用户访问。

若要查看具体示例，请参阅 [Microsoft 标识平台代码示例](sample-v2-code.md)。 

若要查看移动特定的示例，请参阅： 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [通用 Windows 平台](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>实现 SAML 2。0

如果你的应用支持 SAML 2.0，你可以将其与 Azure AD 租户直接集成。 若要详细了解 Azure AD 的 SAML 配置，请参阅 [配置基于 SAML 的单一登录](../manage-apps/configure-saml-single-sign-on.md)。

Microsoft 不为 SAML 实现提供或建议库。 有许多开源库可用。

### <a name="implement-ws-fed"></a>实现 WS-Fed
若要了解有关 ASP.NET Core 中 WS-Fed 的详细信息，请参阅 [在 ASP.NET Core 中用 WS-Federation 对用户进行身份验证](https://docs.microsoft.com/aspnet/core/security/authentication/ws-federation)。

### <a name="implement-password-vaulting"></a>实现密码保管

创建具有 HTML 登录页的 web 应用程序。 确保你的应用程序支持窗体身份验证，以便可以进行密码保险存储以使单一登录能够按预期方式工作。


## <a name="step-3---create-your-azure-tenant-and-test-your-app"></a>步骤 3-创建 Azure 租户并测试应用程序

需要 Azure AD 租户才能测试应用程序。 若要设置开发环境，请参阅 [快速入门：设置租户](quickstart-create-new-tenant.md)。

或者，Azure AD 租户附带每个 Microsoft 365 订阅。 若要设置免费 Microsoft 365 开发环境，请参阅 [加入 Microsoft 365 开发人员计划](https://docs.microsoft.com/office/developer-program/microsoft-365-developer-program)。

拥有租户后，需要启用和测试单一登录访问。 

**对于 OIDC 或 Oath 应用**程序，请将 [应用程序注册](quickstart-register-app.md) 为多租户应用程序。 在 "受支持的帐户类型" 中选择 "任何组织目录和个人 Microsoft 帐户中的帐户" 选项。

**对于基于 saml 和 ws-federation 的应用程序**，可以使用 Azure AD 中的通用 SAML 模板来 [配置基于 saml 的单一登录](../manage-apps/configure-saml-single-sign-on.md) 应用程序。

如果需要，还可以 [将单租户应用程序转换为多租户应用程序](howto-convert-app-to-be-multi-tenant.md) 。


## <a name="step-4---create-and-publish-documentation"></a>步骤 4-创建和发布文档

### <a name="documentation-on-your-site"></a>网站上的文档

易用性是企业软件决策中的一个重要因素。 明确的易于理解的文档在采用旅程中支持客户并降低支持成本。 Microsoft 使用成千上万个软件供应商。

建议你至少在网站上包含以下各项。

* SSO 功能简介
  * 支持的协议
  * 版本和 SKU
  * 支持的标识提供者列表和文档链接
* 应用程序的授权信息
* 用于配置 SSO 的基于角色的访问控制
* SSO 配置步骤
  * 具有提供程序中的预期值的 SAML 的 UI 配置元素
  * 要传递给标识提供程序的服务提供程序信息
* If OIDC/OAuth
  * 同意业务理由所需的权限列表
* 试验用户的测试步骤
* 疑难解答信息，包括错误代码和消息
* 客户的支持机制

### <a name="documentation-on-the-microsoft-site"></a>Microsoft 网站上的文档

当你使用 Azure Active Directory 应用程序库列出你的应用程序（该应用程序也会在 Azure Marketplace 中发布应用程序）时，Microsoft 将为我们的共同客户生成文档，其中介绍了分步过程。 可在 [此处](https://aka.ms/appstutorial)查看示例。 此文档是基于你提交到库的创建的，如果你使用 GitHub 帐户对应用程序进行更改，则可以轻松地对其进行更新。


## <a name="step-5---submit-your-app"></a>步骤 5-提交应用

在测试应用程序集成是否可用于 Azure AD 后，请在 [Microsoft 应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中提交应用程序请求。

首次登录到门户时，将显示以下两个屏幕之一。 

如果收到 "无法正常工作" 的消息，则需要联系 [AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 提供要用于提交请求的电子邮件帐户。 诸如这样的业务电子邮件地址 `name@yourbusiness.com` 是首选的。 Azure AD 团队将在 Microsoft 应用程序网络门户中添加该帐户。

如果你看到 "请求访问权限" 页，请填写业务理由，然后选择 " **请求访问权限**"。

添加帐户后，你可以登录到 Microsoft 应用程序网络门户，并通过在主页上选择 " **提交请求 (ISV) ** " 磁贴来提交请求。

![在主页上提交请求 (ISV) 磁贴](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>登录到门户的问题

如果在登录时看到此错误，请参阅此问题的详细信息，这是解决该问题的方法。

* 如果登录被阻止，如下所示：

  ![解决库中的应用程序问题](./media/howto-app-gallery-listing/blocked.png)

**发生了什么事情：**

来宾用户与家庭租户联合，这也是 Azure AD。 来宾用户处于高风险状态。 Microsoft 不允许高风险用户访问其资源。 所有高风险用户 (员工或来宾/供应商) 必须修正/关闭他们访问 Microsoft 资源的风险。 对于来宾用户，此用户面临的风险来自于 home 租户，策略来自资源租户 (Microsoft 在此案例中) 。
 
**安全解决方案：**

* MFA 注册的来宾用户修正其自己的用户风险。 这可以由来宾用户在其主租户 (执行安全密码更改或重置 https://aka.ms/sspr) ， (这需要在 home 租户) 进行 MFA 和 SSPR。 必须在 Azure AD 而不是本地上启动安全密码更改或重置。

* 来宾用户的管理员可以纠正他们的风险。 在这种情况下，管理员将 (临时密码生成) 执行密码重置。 这不需要 Identity Protection。 来宾用户的管理员可以前往 https://aka.ms/RiskyUsers ，并单击 "重置密码"。

* 来宾用户的管理员需要关闭/消除其风险。 同样，这不需要 Identity Protection。 管理员可以前往 https://aka.ms/RiskyUsers ，并单击 "消除用户风险"。 但是，管理员必须执行 "截止努力"，以确保在关闭用户风险之前，这是误报风险评估。 否则，他们会通过抑制风险评估而不进行调查，使其和 Microsoft 的资源面临风险。

> [!NOTE]
> 如果访问有任何问题，请联系 [AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。

### <a name="implementation-specific-options"></a>特定于实现的选项
如果要使用 OpenID Connect 将应用程序添加到库中，请选择 " **Openid connect & OAuth 2.0** "，如下所示。

![在库中列出 OpenID Connect 应用程序](./media/howto-app-gallery-listing/openid.png)

如果要使用 **SAML 2.0** 或 **ws-addressing**将应用程序添加到库中，请选择 " **saml 2.0/ws-** 已显示"。

![在库中列出 SAML 2.0 或 WS-Fed 应用程序](./media/howto-app-gallery-listing/saml.png)

如果要使用密码 SSO 将应用程序添加到库中，请选择 " **密码 sso** "，如所示。

![在库中列出密码 SSO 应用程序](./media/howto-app-gallery-listing/passwordsso.png)

如果要实现用户预配的 SCIM 2.0 终结点，请选择相应的选项。 

   ![用户预配请求](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>更新或删除现有列表

你可以在 [Microsoft 应用程序网络门户](https://microsoft.sharepoint.com/teams/apponboarding/Apps)中更新或删除现有的库应用。

![在库中列出 SAML 应用程序](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> 如果访问有任何问题，请参阅上一节 "创建帐户"。 如果这不起作用，请联系 [AZURE AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。


### <a name="timelines"></a>时间线

在库中列出 SAML 2.0 或 WS-Fed 应用程序的过程的时间线是7到10个工作日内。

![用于在库中列出 SAML 应用程序的时间线](./media/howto-app-gallery-listing/timeline.png)

在库中列出 OpenID Connect 应用程序的过程的时间线是2到5个工作日。

![用于在库中列出 OpenID Connect 应用程序的时间线](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>升级

对于任何升级，请将电子邮件发送到 [AZURE AD SSO 集成团队](mailto:SaaSApplicationIntegrations@service.microsoft.com)，我们将尽快回复。


## <a name="step-6---join-the-microsoft-partner-network"></a>步骤 6-加入 Microsoft 合作伙伴网络
Microsoft 合作伙伴网络提供对独占资源、程序、工具和连接的即时访问。 若要加入网络并创建转到市场计划，请参阅 [联系商业客户](https://partner.microsoft.com/explore/commercial#gtm)。


## <a name="next-steps"></a>后续步骤
* [生成 SCIM 终结点并配置预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Azure AD 的身份验证方案](authentication-flows-app-scenarios.md)
