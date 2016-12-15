---
title: "Azure Active Directory B2C：令牌、会话和单一登录配置 | Microsoft Docs"
description: "Azure Active Directory B2C 中的令牌、会话和单一登录配置"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: e78e6344-0089-49bf-8c7b-5f634326f58c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b37d419f799e5a56c67344ca634bdecec2f3c1f2


---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C：令牌、会话和单一登录配置
通过此功能，可以对以下各方面进行[按策略](active-directory-b2c-reference-policies.md)的精细控制：

1. Azure Active Directory (Azure AD) B2C 发出的安全令牌的生存期。
2. 由 Azure AD B2C 管理的 Web 应用程序会话的生存期。
3. B2C 租户中跨多个应用和策略的单一登录 (SSO) 行为。

你可以按如下方式在 B2C 租户中使用此功能：

1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“登录策略”。 *注意：可以对任何策略类型使用此功能，不只是**签入策略***。
3. 通过单击策略以打开它。 例如，单击“B2C_1_SiIn”。
4. 单击边栏选项卡顶部的“编辑”。
5. 单击“令牌、会话和单一登录配置”。
6. 进行所需的更改。 在后续章节中了解可用的属性。
7. 单击“确定”。
8. 单击边栏选项卡顶部的“保存”。

![令牌、会话和单一登录配置的屏幕截图](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>令牌生存期配置
Azure AD B2C 支持 [OAuth 2.0 授权协议](active-directory-b2c-reference-protocols.md)以启用对受保护资源的安全访问。 若要实现此支持，Azure AD B2C 需发出各种[安全令牌](active-directory-b2c-reference-tokens.md)。 以下是可用于管理 Azure AD B2C 发出的安全令牌的生存期的属性：

* **访问令牌和 ID 令牌生存期（分钟）**：用于获取受保护资源的访问权限的 OAuth 2.0 持有者令牌的生存期。 当前，Azure AD B2C 仅发出 ID 令牌。 如果添加了对访问令牌的支持，该值也应用于访问令牌。
  * 默认值 = 60 分钟。
  * 最小值（含）= 5 分钟。
  * 最大值（含）= 1440 分钟。
* **刷新令牌生存期（天）**：可以使用刷新令牌获取新的访问令牌或 ID 令牌（或者（可选）新刷新令牌，如果应用程序被授予了 `offline_access` 作用域）的最大期限。
  * 默认值 = 14 天。
  * 最小值（含）= 1 天。
  * 最大值（含）= 90 天。
* **刷新令牌的滑动窗口生存期（天）**：此时间段过后，强制用户重新进行身份验证，不考虑应用获取的最新刷新令牌的有效期。 仅在此开关设为“有限”时该属性才可用。 它的值必须大于或等于**刷新令牌生存期（天）**的值。 如果此开关设置为“无限”，则无法提供特定值。
  * 默认值 = 90 天。
  * 最小值（含）= 1 天。
  * 最大值（含）= 365 天。

以下是可以使用上述属性的两个用例：

* 只要用户在移动应用上持续保持活动状态，允许该用户无限期地保持登录此应用。 可以通过将登录策略中的**刷新令牌的滑动窗口生存期（天）**开关设为**无限**来实现此场景。
* 通过设置合适的访问令牌生存期来满足行业的安全性和合规性要求。

## <a name="session-configuration"></a>会话配置
Azure AD B2C 支持 [OpenID Connect 身份验证协议](active-directory-b2c-reference-oidc.md)以启用对 Web 应用的安全登录。 以下是可用于管理 Web 应用会话的属性：

* **Web 应用会话生存期（分钟）**：身份验证成功后，存储在用户浏览器上的 Azure AD B2C 会话 Cookie 的生存期。
  * 默认值 = 1440 分钟。
  * 最小值（含）= 15 分钟。
  * 最大值（含）= 1440 分钟。
* **Web 应用会话超时**：如果此开关设置为“绝对”，则在 **Web 应用会话生存期（分钟）**指定的时间之后强制用户重新进行身份验证。 如果此开关设置为“滚动”（默认设置），那么只要用户持续在 Web 应用中保持活动状态，该用户就可以保持登录状态。

以下是可以使用上述属性的两个用例：

* 通过设置合适的 Web 应用会话生存期来满足行业的安全性和合规性要求。
* 在用户与 Web 应用的安全性较高的部分进行交互时，在设定的时间段之后强制重新进行身份验证。 

## <a name="single-sign-on-sso-configuration"></a>单一登录 (SSO) 配置
如果在 B2C 租户中有多个应用和策略，可以使用**单一登录配置**属性管理它们之间的用户交互。 可以将该属性设为以下设置之一：

* **租户**：这是默认设置。 使用此设置允许 B2C 租户中的多个应用和策略共享相同的用户会话。 例如，一旦用户登录到一个应用“Contoso 购物”之后，他或她就可以在访问另一个应用“Contoso 药店”时无缝登录到该应用。
* **应用程序**：允许为某个应用程序维持独占式用户会话（独立于其他应用程序）。 例如，如果想要用户登录 Contoso 药店（具有相同的凭据），即使他或她已登录 Contoso 购物（相同 B2C 租户中的另一个应用程序），也要输入凭据。 
* **策略**：允许为某个策略维持独占式用户会话（独立于使用它的应用程序）。 例如，如果用户已登录并完成多重身份验证 (MFA) 步骤，那么只要绑定到策略的会话未过期，他或她就可以访问多个应用程序的具有较高安全性的部分。
* **已禁用**：强制用户在每次执行策略时都要进行完整的登录验证过程。 例如，即使单个用户在整个过程中保持已登录状态，也允许多个用户注册应用程序（在共享桌面方案中）。




<!--HONumber=Nov16_HO3-->


