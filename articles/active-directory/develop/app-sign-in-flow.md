---
title: Microsoft 标识平台的应用登录流 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台（v2.0）中的 web、桌面和移动应用的登录流。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 7b326e17611b5f4b9520d8218a28a67afe9a851a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584345"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>Microsoft 标识平台的应用登录流

本主题介绍使用 Microsoft 标识平台的 web、桌面和移动应用的基本登录流。 若要了解 Microsoft 标识平台支持的登录方案，请参阅[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。

## <a name="web-app-sign-in-flow"></a>Web 应用登录流

当用户在浏览器中导航到某个 Web 应用时，将发生以下情况：

* Web 应用确定用户是否已完成身份验证。
* 如果用户未完成身份验证，Web 应用将委托 Azure AD 将用户登录。 登录符合组织的策略，这可能意味着，系统会要求用户输入其凭据、使用多重身份验证，或者完全禁止使用密码（例如使用 Windows Hello）。
* 系统要求用户许可客户端应用所需的访问权限。 这就是需要在 Azure AD 中注册客户端应用程序的原因，以便 Microsoft 标识平台可以提供令牌，这些令牌代表用户同意的访问权限。

用户成功完成身份验证后：

* Microsoft 标识平台会将令牌发送到 web 应用。
* 保存与 Azure AD 的域关联的 Cookie，其中包含浏览器 Cookie jar 中的用户标识。 下一次应用使用浏览器导航到 Microsoft 标识平台授权终结点时，浏览器会显示该 cookie，这样用户就无需再次登录。 这也是实现 SSO 的方式。 Cookie 由 Azure AD 生成，只有 Azure AD 能够识别它。
* 然后，Web 应用验证令牌。 如果验证成功，Web 应用将显示受保护的页，并在浏览器的 Cookie jar 中保存会话 Cookie。 当用户导航到另一页时，Web 应用知道用户已基于会话 Cookie 进行身份验证。

以下序列图汇总了这种交互：

![Web 应用身份验证过程](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Web 应用如何确定用户是否已完成身份验证

Web 应用开发人员可以指定是所有页还是只有特定的页需要身份验证。 例如，在 ASP.NET/ASP.NET Core 中，可以通过将 `[Authorize]` 属性添加到控制器操作来进行这种指定。

此属性会导致 ASP.NET 检查是否存在包含用户标识的会话 Cookie。 如果 Cookie 不存在，ASP.NET 会将身份验证重定向到指定的标识提供者。 如果标识提供者是 Azure AD，则 Web 应用会将身份验证重定向到 `https://login.microsoftonline.com`，这会显示登录对话框。

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Web 应用如何委托登录 Microsoft 标识平台和获取令牌

用户身份验证通过浏览器发生。 OpenID 协议使用标准 HTTP 协议消息。

* Web 应用将 HTTP 302 （重定向）发送到浏览器，以使用 Microsoft 标识平台。
* 对用户进行身份验证时，Microsoft 标识平台使用重定向通过浏览器将令牌发送到 web 应用。
* 重定向由 Web 应用以重定向 URI 的形式提供。 此重定向 URI 将注册到 Azure AD 应用程序对象。 由于应用程序可部署到多个 URL，可能存在多个重定向 URI。 因此，Web 应用还需要指定要使用的重定向 URI。
* Azure AD 验证 Web 应用发送的重定向 URI 是否为应用的已注册重定向 URI 之一。

## <a name="desktop-and-mobile-app-sign-in-flow"></a>桌面和移动应用登录流

上面所述的流适用于桌面和移动应用程序，只是存在少许差异。

桌面和移动应用程序可以使用嵌入式 Web 控件或系统浏览器进行身份验证。 下图演示了桌面或移动应用如何使用 Microsoft 身份验证库 (MSAL) 获取访问令牌和调用 Web API。

![桌面应用的身份验证方式](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL 使用浏览器来获取令牌。 对于 web 应用，身份验证将委托给 Microsoft 标识平台。

由于 Azure AD 与对 Web 应用一样在浏览器中保存相同的标识 Cookie，因此，如果本机应用或移动应用使用系统浏览器，则会立即在相应的 Web 应用上实现 SSO。

默认情况下，MSAL 使用系统浏览器。 但 .NET Framework 桌面应用程序除外，这种应用程序使用嵌入式控件来提供集成程度更高的用户体验。

## <a name="next-steps"></a>后续步骤

有关涉及身份验证和授权的其他主题，请执行以下操作：

* 若要了解 Microsoft 标识平台中身份验证和授权的基本概念，请参阅[身份验证与授权](authentication-vs-authorization.md)。
* 请参阅[安全令牌](security-tokens.md)，了解身份验证和授权中如何使用访问令牌、刷新令牌和 ID 令牌。
* 请参阅[应用程序模型](application-model.md)，了解注册应用程序的过程，使其能够与 Microsoft 标识平台集成。

若要了解有关应用登录流的详细信息，请参阅：

* 若要详细了解 Microsoft 标识平台支持的用户，请参阅[身份验证流和应用程序方案](authentication-flows-app-scenarios.md)。
* 请参阅[MSAL 库](msal-overview.md)，了解 microsoft 库，这些库可帮助你开发使用 microsoft 帐户、Azure AD 帐户和 Azure AD B2C 用户在单个简化编程模型中的应用程序。
