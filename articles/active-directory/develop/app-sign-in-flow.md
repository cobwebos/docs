---
title: 使用 Microsoft 标识平台的应用登录流 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台 (v2.0) 中 Web、桌面和移动应用的登录流。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: af5b27dc85a276c731a61135ab59ab81f5aaf3c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83772193"
---
# <a name="app-sign-in-flow-with-microsoft-identity-platform"></a>使用 Microsoft 标识平台的应用登录流

本主题介绍使用 Microsoft 标识平台的 Web、桌面和移动应用的基本登录流。 请参阅[身份验证流和应用方案](authentication-flows-app-scenarios.md)，了解 Microsoft 标识平台支持的登录方案。

## <a name="web-app-sign-in-flow"></a>Web 应用登录流

当用户在浏览器中导航到 Web 应用时，会发生以下情况：

* Web 应用确定用户是否进行了身份验证。
* 如果用户未进行身份验证，则 Web 应用将委托 Azure AD 登录该用户。 该登录将符合组织的策略，这可能意味着要求用户输入其凭据，使用[多重身份验证](../authentication/concept-mfa-howitworks.md)（有时称为双因素身份验证或 2FA），或者根本不使用密码（例如使用 Windows Hello）。
* 要求用户同意该客户端应用所需的访问。 这就是为什么需要使用 Azure AD 注册客户端应用，以便 Microsoft 标识平台可以传递表示用户已同意访问的令牌。

成功对用户进行身份验证以后，请执行以下操作：

* Microsoft 标识平台向 web 应用发送令牌。
* 将保存与 Azure AD 的域相关联的 cookie，其中包含浏览器 cookie jar 中用户的标识。 下次应用使用浏览器导航到 Microsoft 标识平台授权终结点时，浏览器将显示 cookie，这样用户就无需再次登录。 这也是实现 SSO 的方式。 Cookie 由 Azure AD 生成，只能通过 Azure AD 理解。
* 然后，Web 应用对令牌进行验证。 如果验证成功，Web 应用将显示受保护的页面并将会话 cookie 保存在浏览器的 cookie jar 中。 当用户导航到另一个页面时，Web 应用知道用户是基于会话 cookie 进行身份验证。

下面的序列图概述了这种交互：

![Web 应用身份验证过程](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>web 应用如何确定用户是否进行了身份验证

Web 应用开发人员可以指示是否所有或仅某些页面需要身份验证。 例如，在 ASP.NET/ASP.NET Core 中，则将 `[Authorize]` 属性添加到控制器操作来完成此操作。

此属性可让 ASP.NET 检查是否存在包含用户标识的会话 cookie。 如果 cookie 不存在，ASP.NET 将身份验证重定向到指定的标识提供者。 如果标识提供者是 Azure AD，则 Web 应用将身份验证重定向到 `https://login.microsoftonline.com`，后者将显示登录对话框。

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Web 应用如何委托登录到 Microsoft 标识平台并获取令牌

通过浏览器进行用户身份验证。 OpenID 协议使用标准 HTTP 协议消息。

* Web 应用将 HTTP 302（重定向）发送到浏览器以使用 Microsoft 标识平台。
* 当用户进行了身份验证时，Microsoft 标识平台通过浏览器使用重定向将令牌发送到 Web 应用。
* Web 应用以重定向 URI 的形式提供重定向。 此重定向 URI 已注册到 Azure AD 应用程序对象。 可能有多个重定向 URI，因为应用程序可能部署在多个 URL 上。 因此，Web 应用还需要指定要使用的重定向 URI。
* Azure AD 验证 Web 应用发送的重定向 URI 是否是该应用的某个注册重定向 URI。

## <a name="desktop-and-mobile-app-sign-in-flow"></a>桌面和移动应用登录流

上面描述的流仅适用于桌面和移动应用程序，但略有不同。

桌面和移动应用程序可以使用嵌入式 Web 控件或系统浏览器进行身份验证。 下图显示了桌面或移动应用程序如何使用 Microsoft 身份验证库 (MSAL) 获取访问令牌并调用 Web API。

![桌面应用的外观](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL 使用浏览器获取令牌。 与 Web 应用一样，身份验证委托给 Microsoft 标识平台。

由于 Azure AD 在浏览器中保存的标识 cookie 与在 Web 应用中保存的标识 cookie 相同，因此如果本机或移动应用使用系统浏览器，它将立即使用相应的 Web 应用获取 SSO。

默认情况下，MSAL 使用系统浏览器。 例外情况是 .NET Framework 桌面应用程序，其中使用嵌入式控件提供更集成的用户体验。

## <a name="next-steps"></a>后续步骤

有关其他涉及身份验证和授权基础知识的主题：

* 请参阅[身份验证和授权](authentication-vs-authorization.md)，了解 Microsoft 标识平台中身份验证和授权的基本概念。
* 请参阅[安全令牌](security-tokens.md)，了解如何在身份验证和授权中使用访问令牌、刷新令牌和 ID 令牌。
* 请参阅[应用程序模型](application-model.md)，了解注册应用程序的过程，以便它可以与 Microsoft 标识平台集成。

若要了解有关应用登录流的详细信息：

* 请参阅[身份验证流和应用方案](authentication-flows-app-scenarios.md)，了解有关验证 Microsoft 标识平台支持的用户的其他方案的详细信息。
* 请参阅 [MSAL 库](msal-overview.md)，以了解帮助你开发与简化的单个编程模型中 Microsoft 帐户、Azure AD 帐户和 Azure AD B2C 用户一起使用的应用程序的 Microsoft 库。
