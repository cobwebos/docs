---
title: Azure Active Directory B2C 中的 SSO 会话 |Microsoft Docs
description: 在 Azure Active Directory B2C 中配置会话行为。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea8c40faad4ee709ae98f868e36fd42e46501bea
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927031"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C 会话

单一登录（SSO）在用户跨 Azure Active Directory B2C （Azure AD B2C）中的应用程序登录时增加了安全性和便利性。 本文介绍 Azure AD B2C 中使用的单一登录方法，并可帮助你在配置策略时选择最适合的 SSO 方法。

使用单一登录，用户可以使用单个帐户登录一次，并可以访问多个应用程序。 无论平台或域名如何，应用程序都可以是 web 应用、移动应用或单页应用程序。

用户首次登录应用程序时，Azure AD B2C 会保留基于 cookie 的会话。 在后续身份验证请求中，Azure AD B2C 读取和验证基于 cookie 的会话，并颁发访问令牌，而不提示用户重新登录。 如果基于 cookie 的会话过期或变为无效，则系统会提示用户重新登录。  

## <a name="sso-session-types"></a>SSO 会话类型

与 Azure AD B2C 的集成涉及三种类型的 SSO 会话：

- **Azure AD B2C**由 Azure AD B2C 管理
- **联合标识提供程序**-由标识提供程序管理的会话，例如 Facebook、Salesforce 或 Microsoft 帐户
- 由 web 应用程序、移动应用程序或单页应用程序管理的**应用程序**会话

![SSO 会话](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

当用户使用本地帐户或社交帐户成功进行身份验证时，Azure AD B2C 会在用户的浏览器上存储基于 cookie 的会话。 Cookie 存储在 Azure AD B2C 租户域名下，如`https://contoso.b2clogin.com`。

如果用户使用联合帐户首次登录，然后在会话时间窗口（生存时间或 TTL）中登录到相同的应用程序或其他应用程序，则 Azure AD B2C 尝试从联合标识提供程序获取新的访问令牌。 如果联合标识提供程序会话已过期或无效，则联合标识提供程序会提示用户输入其凭据。 如果会话仍处于活动状态（或者如果用户使用本地帐户而不是联合帐户登录），Azure AD B2C 会授权用户并消除进一步的提示。

你可以配置会话行为，包括会话 TTL 以及 Azure AD B2C 如何跨策略和应用程序共享会话。

### <a name="federated-identity-provider-sso"></a>联合标识提供程序 SSO

社交或企业标识提供者管理自己的会话。 Cookie 存储在标识提供程序的域名下，如`https://login.salesforce.com`。 Azure AD B2C 不会控制联合标识提供程序会话。 会话行为取决于联合标识提供者。 

请考虑下列方案：

1. 用户登录 Facebook 以检查其源。
2. 稍后，用户将打开你的应用程序并启动登录过程。 应用程序将用户重定向到 Azure AD B2C 以完成登录过程。
3. 在 Azure AD B2C 注册或登录页上，用户选择通过其 Facebook 帐户进行登录。 用户被重定向到 Facebook。 如果 Facebook 上存在活动会话，则不会提示用户提供其凭据，并立即重定向到使用 Facebook 令牌 Azure AD B2C。

### <a name="application-sso"></a>应用程序 SSO

Web、移动或单页应用程序可通过 OAuth 访问、ID 令牌或 SAML 令牌进行保护。 当用户尝试访问应用中的受保护资源时，应用会检查应用程序端是否存在活动会话。 如果没有应用会话或会话已过期，应用将使用户 Azure AD B2C 登录页。

应用程序会话可以是存储在应用程序域名下的基于 cookie 的会话，例如`https://contoso.com`。 移动应用程序可以采用不同的方式存储会话，但使用类似的方法。

## <a name="azure-ad-b2c-session-configuration"></a>Azure AD B2C 会话配置

### <a name="session-scope"></a>会话作用域

Azure AD B2C 会话可以配置为以下作用域：

- **租户** - 这是默认设置。 使用此设置允许 B2C 租户中的多个应用和用户流共享相同的用户会话。 例如，当用户登录到应用程序后，用户还可以在访问应用程序时无缝登录到另一个应用程序。
- **应用程序** - 此设置允许为某个应用程序维持独占式用户会话（独立于其他应用程序）。 例如，如果希望用户登录到 Contoso 药房，无论用户是否已登录到 Contoso 杂货，都可以使用此设置。
- **策略** - 此设置为某个用户流维持独占式用户会话（独立于使用它的应用程序）。 例如，如果用户已登录并完成多重身份验证（MFA）步骤，则只要与用户流关联的会话未过期，用户就可以访问多个应用程序的更高安全性部分。
- **已禁用**-此设置将强制用户在每次执行策略时都运行整个用户流。

### <a name="session-life-time"></a>会话生存期

**会话生存期**是在身份验证成功后，将 Azure AD B2C 会话 cookie 存储在用户浏览器上的时间量。 可以将会话生存期设置为介于15到720分钟之间的值。

### <a name="keep-me-signed-in"></a>使我保持登录状态

"[使我保持登录](custom-policy-keep-me-signed-in.md)" 功能通过使用永久性 cookie 延长会话生存期。 在用户关闭并重新打开浏览器后，会话将保持活动状态。 仅当用户注销时才会撤消会话。"使我保持登录" 功能仅适用于登录本地帐户。

"使我保持登录" 功能优先于会话生存期。 如果启用了 "使我保持登录状态" 功能，并且用户选择了该功能，则此功能将指示会话过期的时间。 

### <a name="session-expiry-type"></a>会话过期类型

**会话过期类型**指示会话如何由会话生命时间设置或 "使我保持登录" 设置进行扩展。

- "**滚动**"-指示每次用户执行基于 cookie 的身份验证（默认值）时会话都将扩展。
- **绝对**-表示用户在指定的时间段后被迫重新进行身份验证。

## <a name="sign-out"></a>注销

如果想要从应用程序中注销用户，只是清除应用程序的 Cookie 或者结束与用户的会话是不够的。 必须将用户重定向到 Azure AD B2C 以注销。否则，用户可以重新对应用程序进行身份验证，而无需再次输入其凭据。

注销请求时，Azure AD B2C：

1. 使基于 Azure AD B2C cookie 的会话失效。
1. 尝试从联合标识提供程序注销：
   - OpenId Connect-如果标识提供者众所周知的配置终结点指定`end_session_endpoint`位置，则为。
   - SAML-如果标识提供程序元数据包含`SingleLogoutService`位置。
1. 还可以从其他应用程序中注销。 有关详细信息，请参阅[单一登录](#single-sign-out)部分。

注销会清除用户与 Azure AD B2C 的单一登录状态，但它可能不会将用户从其社交标识提供者会话中注销。 如果用户在后续登录期间选择同一标识提供者，则他们可能会在不输入其凭据的情况下进行身份验证。 如果用户想要从应用程序中注销，则不一定意味着他们要注销其 Facebook 帐户。 但是，如果使用了本地帐户，则用户的会话将正常结束。

### <a name="single-sign-out"></a>单一登录 


> [!NOTE]
> 此功能仅限[自定义策略](custom-policy-overview.md)。

将用户重定向到 Azure AD B2C 注销终结点（适用于 OAuth2 和 SAML 协议）时，Azure AD B2C 从浏览器中清除用户的会话。 但是，用户可能仍登录到其他使用 Azure AD B2C 进行身份验证的应用程序。 若要使这些应用程序能够同时注销用户，Azure AD B2C 会将 HTTP GET 请求发送到用户`LogoutUrl`当前登录到的所有应用程序的注册。


应用程序必须通过清除任何标识用户的会话并返回 `200` 响应来响应此请求。 如果要在应用程序中支持单一注销，则必须`LogoutUrl`在应用程序的代码中实现。 

## <a name="next-steps"></a>后续步骤

- 了解如何[在用户流中配置会话行为](session-behavior.md)。
- 了解如何[在自定义策略中配置会话行为](session-behavior-custom-policy.md)。