---
title: "令牌、会话和单一登录配置 - Azure AD B2C | Microsoft 文档"
description: "Azure Active Directory B2C 中的令牌、会话和单一登录配置"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: e78e6344-0089-49bf-8c7b-5f634326f58c
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: parakhj
ms.openlocfilehash: 76feb172ce9b08d4feef9c86e74a592553a3c7f4
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C：令牌、会话和单一登录配置

通过此功能，可以对以下各方面进行[按策略](active-directory-b2c-reference-policies.md)的精细控制：

1. Azure Active Directory (Azure AD) B2C 发出的安全令牌的生存期。
2. 由 Azure AD B2C 管理的 Web 应用程序会话的生存期。
3. Azure AD B2C 发出的安全令牌中的重要声明格式。
4. B2C 租户中跨多个应用和策略的单一登录 (SSO) 行为。

对于内置策略，你可以在 Azure AD B2C 目录中使用此功能，如下所示：

1. 请按照以下步骤在 Azure 门户上[导航到 B2C 功能菜单](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)。
2. 单击“注册或登录策略”。 注意：可以对任何策略类型使用此功能，不局限于注册或登录策略**。
3. 通过单击策略以打开它。 例如，单击“B2C_1_SiUpIn”。
4. 单击菜单顶部的“编辑”。
5. 单击“令牌、会话和单一登录配置”。
6. 进行所需的更改。 在后续章节中了解可用的属性。
7. 单击 **“确定”**。
8. 单击菜单顶部的“保存”。

## <a name="token-lifetimes-configuration"></a>令牌生存期配置

Azure AD B2C 支持 [OAuth 2.0 授权协议](active-directory-b2c-reference-protocols.md)以启用对受保护资源的安全访问。 若要实现此支持，Azure AD B2C 需发出各种[安全令牌](active-directory-b2c-reference-tokens.md)。 以下是可用于管理 Azure AD B2C 发出的安全令牌的生存期的属性：

* **访问令牌和 ID 令牌生存期（分钟）**：用于获取受保护资源的访问权限的 OAuth 2.0 持有者令牌的生存期。
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

    > [!NOTE]
    > 这些设置不适用于密码重置策略。
    > 
    > 

## <a name="token-compatibility-settings"></a>令牌兼容性设置

我们对 Azure AD B2C 发出的安全令牌中的重要声明做出了格式更改。 这样做是为了改善标准协议支持以及与第三方标识库的互操作性。 但是，为了避免破坏现有应用，我们创建了以下属性，使客户能够根据需要选择格式：

* **颁发者 (iss) 声明**：标识颁发令牌的 Azure AD B2C 租户。
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`：默认值。
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`：此值包含令牌请求中使用的 B2C 租户和策略的 ID。 如果应用或库需要 Azure AD B2C 才能符合 [OpenID Connect Discovery 1.0 规范](http://openid.net/specs/openid-connect-discovery-1_0.html)，请使用此值。
* **使用者 (sub) 声明**：标识实体，即令牌断言其信息的用户。
  * **ObjectID**：默认值。 将在令牌的 `sub` 声明中填充目录中用户的对象 ID。
  * **不支持**：此项只是为了向后兼容而提供的，我们建议尽快改用 **ObjectID**。
* **表示策略 ID 的声明**：标识要在其中填充令牌请求中使用的策略 ID 的声明类型。
  * **tfp**：默认值。
  * **acr**：此项只是为了向后兼容而提供的，我们建议尽快改用 `tfp`。

## <a name="session-behavior"></a>会话行为

Azure AD B2C 支持 [OpenID Connect 身份验证协议](active-directory-b2c-reference-oidc.md)以启用对 Web 应用的安全登录。 以下是可用于管理 Web 应用会话的属性：

* **Web 应用会话生存期（分钟）**：身份验证成功后，存储在用户浏览器上的 Azure AD B2C 会话 Cookie 的生存期。
  * 默认值 = 1440 分钟。
  * 最小值（含）= 15 分钟。
  * 最大值（含）= 1440 分钟。
* **Web 应用会话超时**：如果此开关设置为“绝对”，则在 **Web 应用会话生存期（分钟）**指定的时间之后强制用户重新进行身份验证。 如果此开关设置为“滚动”（默认设置），那么只要用户持续在 Web 应用中保持活动状态，该用户就可以保持登录状态。

以下是可以使用上述属性的两个用例：

* 通过设置合适的 Web 应用会话生存期来满足行业的安全性和合规性要求。
* 在用户与 Web 应用的安全性较高的部分进行交互时，在设定的时间段之后强制重新进行身份验证。 

    > [!NOTE]
    > 这些设置不适用于密码重置策略。
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>单一登录 (SSO) 配置
如果在 B2C 租户中有多个应用和策略，可以使用**单一登录配置**属性管理它们之间的用户交互。 可以将该属性设为以下设置之一：

* **租户**：这是默认设置。 使用此设置允许 B2C 租户中的多个应用和策略共享相同的用户会话。 例如，一旦用户登录到一个应用“Contoso 购物”之后，他或她就可以在访问另一个应用“Contoso 药店”时无缝登录到该应用。
* **应用程序**：允许为某个应用程序维持独占式用户会话（独立于其他应用程序）。 例如，如果想要用户登录 Contoso 药店（具有相同的凭据），即使他或她已登录 Contoso 购物（相同 B2C 租户中的另一个应用程序），也要输入凭据。 
* **策略**：允许为某个策略维持独占式用户会话（独立于使用它的应用程序）。 例如，如果用户已登录并完成多重身份验证 (MFA) 步骤，那么只要绑定到策略的会话未过期，他或她就可以访问多个应用程序的具有较高安全性的部分。
* **已禁用**：强制用户在每次执行策略时都要进行完整的登录验证过程。 例如，即使单个用户在整个过程中保持已登录状态，也允许多个用户注册应用程序（在共享桌面方案中）。

    > [!NOTE]
    > 这些设置不适用于密码重置策略。
    > 
    > 

