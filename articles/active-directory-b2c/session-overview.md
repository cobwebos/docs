---
title: Azure Active Directory B2C 中的 SSO 会话 | Microsoft Docs
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82927031"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C 会话

当用户在 Azure Active Directory B2C (Azure AD B2C) 中登录到应用程序时，单一登录 (SSO) 可以提高安全性和便利性。 本文介绍 Azure AD B2C 中使用的单一登录方法，并在配置策略时帮助你选择最适合的 SSO 方法。

借助单一登录，用户可以使用单个帐户登录一次，然后即可访问多个应用程序。 应用程序可以是 Web、移动或单页应用程序，不管它们的平台或域名如何。

当用户最初登录到应用程序时，Azure AD B2C 会保留一个基于 Cookie 的会话。 收到后续的身份验证请求后，Azure AD B2C 会读取并验证该基于 Cookie 的会话，然后颁发访问令牌，且不提示用户重新登录。 如果基于 Cookie 的会话过期或失效，则系统会提示用户重新登录。  

## <a name="sso-session-types"></a>SSO 会话类型

与 Azure AD B2C 的集成涉及到三种类型的 SSO 会话：

- **Azure AD B2C** - 由 Azure AD B2C 管理的会话
- **联合标识提供程序**-由标识提供程序管理的会话，例如 Facebook、Salesforce 或 Microsoft 帐户
- **应用程序** - 由 Web、移动或单页应用程序管理的会话

![SSO 会话](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

当用户使用本地帐户或社交帐户成功完成身份验证时，Azure AD B2C 会在用户的浏览器中存储一个基于 Cookie 的会话。 Cookie 存储在 Azure AD B2C 租户域名（例如 `https://contoso.b2clogin.com`）下。

如果用户最初使用联合帐户登录，然后在会话时间窗口（生存时间，简称 TTL）内登录到相同或不同的应用，则 Azure AD B2C 会尝试从联合标识提供者获取新的访问令牌。 如果联合标识提供者会话已过期或失效，则联合标识提供者会提示用户输入其凭据。 如果会话仍处于活动状态（或者用户已使用本地帐户而不是联合帐户登录），则 Azure AD B2C 将为用户授权并消除进一步的提示。

可以配置会话行为，包括会话 TTL，以及 Azure AD B2C 如何在策略和应用程序之间共享会话。

### <a name="federated-identity-provider-sso"></a>联合标识提供者 SSO

社交或企业标识提供者需管理其自己的会话。 Cookie 存储在标识提供者的域名（例如 `https://login.salesforce.com`）下。 Azure AD B2C 不会控制联合标识提供者会话。 会话行为由联合标识提供者确定。 

请参考以下方案：

1. 用户登录 Facebook 以检查其源。
2. 稍后，用户将打开你的应用程序并启动登录过程。 应用程序将用户重定向到 Azure AD B2C 以完成登录过程。
3. 在 Azure AD B2C 注册或登录页上，用户选择通过其 Facebook 帐户进行登录。 用户被重定向到 Facebook。 如果 Facebook 上存在活动会话，则不会提示用户提供其凭据，并立即重定向到使用 Facebook 令牌 Azure AD B2C。

### <a name="application-sso"></a>应用程序 SSO

可以通过 OAuth 访问、ID 令牌或 SAML 令牌来保护 Web、移动或单页应用程序。 当用户尝试访问应用中某个受保护的资源时，应用会检查应用程序端是否存在活动的会话。 如果不存在应用会话或者会话已过期，则应用会将用户转到 Azure AD B2C 登录页。

应用程序会话可以是存储在应用程序域名（例如 `https://contoso.com`）下的基于 Cookie 的会话。 移动应用程序可能会通过一种不同的方式（但使用类似的方法）存储会话。

## <a name="azure-ad-b2c-session-configuration"></a>Azure AD B2C 会话配置

### <a name="session-scope"></a>会话范围

可为 Azure AD B2C 会话配置以下范围：

- **租户** - 这是默认设置。 使用此设置允许 B2C 租户中的多个应用和用户流共享相同的用户会话。 例如，一旦用户登录到某个应用程序，就还可以在访问该应用程序时无缝登录到另一个应用程序。
- **应用程序** - 此设置允许为某个应用程序维持独占式用户会话（独立于其他应用程序）。 例如，如果你希望无论用户是否已登录到 Contoso Groceries，他们都能够登录到 Contoso Pharmacy，则可以使用此设置。
- **策略** - 此设置为某个用户流维持独占式用户会话（独立于使用它的应用程序）。 例如，如果用户已登录并完成多重身份验证 (MFA) 步骤，那么只要绑定到用户流的会话未过期，该用户就可以访问多个应用程序的具有较高安全性的部分。
- **已禁用** - 此设置强制用户在每次执行策略时都要运行完整的用户流。

### <a name="session-life-time"></a>会话生存时间

**会话生存时间**是指成功完成身份验证后，将 Azure AD B2C 会话 Cookie 存储在用户浏览器中的时间量。 可将会话生存时间设置为 15 到 720 分钟的值。

### <a name="keep-me-signed-in"></a>使我保持登录状态

"[使我保持登录](custom-policy-keep-me-signed-in.md)" 功能通过使用永久性 cookie 延长会话生存期。 在用户关闭并重新打开浏览器后，会话将保持活动状态。 仅当用户注销时，才会撤销会话。“使我保持登录状态”功能仅适用于使用本地帐户进行的登录。

“使我保持登录状态”功能优先于会话生存时间的设置。 如果启用了“使我保持登录状态”功能，并且用户选择了此功能，则此功能将决定会话何时会过期。 

### <a name="session-expiry-type"></a>会话过期类型

**会话过期类型**指示如何通过会话生存时间设置或“使我保持登录状态”设置来使会话延期。

- **滚动** - 指示每当用户执行基于 Cookie 的身份验证时都延长会话（默认值）。
- **绝对** - 指示在指定的时间段后强制用户重新进行身份验证。

## <a name="sign-out"></a>注销

如果想要从应用程序中注销用户，只是清除应用程序的 Cookie 或者结束与用户的会话是不够的。 必须将用户重定向到 Azure AD B2C 进行注销。否则，用户可能可以在应用程序中重新进行身份验证，且无需再次输入其凭据。

收到注销请求后，Azure AD B2C 将会：

1. 使 Azure AD B2C 基于 Cookie 的会话失效。
1. 尝试从联合标识提供者注销：
   - OpenId Connect - 如果标识提供者的已知配置终结点指定了 `end_session_endpoint` 位置。
   - SAML - 如果标识提供者元数据包含 `SingleLogoutService` 位置。
1. 选择性地从其他应用程序注销。 有关详细信息，请参阅[单一注销](#single-sign-out)部分。

注销会清除用户在 Azure AD B2C 中的单一登录状态，但可能不会将用户从其社交标识提供者会话中注销。 如果用户在后续登录期间选择相同的标识提供者，那么他们可以重新进行身份验证，且无需输入其凭据。 如果用户想要从应用程序中注销，则不一定意味着他们要注销其 Facebook 帐户。 但是，如果使用了本地帐户，则用户的会话将正常结束。

### <a name="single-sign-out"></a>单一登录 


> [!NOTE]
> 此功能仅限于[自定义策略](custom-policy-overview.md)。

将用户重定向到 Azure AD B2C 注销终结点（适用于 OAuth2 和 SAML 协议）时，Azure AD B2C 将从浏览器中清除该用户的会话。 但是，用户可能在其他使用 Azure AD B2C 进行身份验证的应用程序中仍处于已登录状态。 要使这些应用程序能够同时注销用户，Azure AD B2C 会将 HTTP GET 请求发送到用户当前登录到的所有应用程序的已注册 `LogoutUrl`。


应用程序必须通过清除任何标识用户的会话并返回 `200` 响应来响应此请求。 若要在应用程序中支持单一注销，必须在应用程序代码中实现 `LogoutUrl`。 

## <a name="next-steps"></a>后续步骤

- 了解如何[在用户流中配置会话行为](session-behavior.md)。
- 了解如何[在自定义策略中配置会话行为](session-behavior-custom-policy.md)。