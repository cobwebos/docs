---
title: Azure Active Directory 中的 Web API 应用
description: 介绍什么是 Web API 应用，以及有关此应用类型的协议流、注册和令牌到期的基础知识。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b477171be0f306431b0f7c5965ebede4f4680c22
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201901"
---
# <a name="web-api"></a>Web API

Web API 应用是需要通过 Web API 获取资源的 Web 应用。 在此方案中，Web 应用可以使用两种标识类型进行身份验证并调用 Web API：

- **应用程序标识** - 此方案使用 OAuth 2.0 客户端凭据授予作为应用程序进行身份验证并访问 Web API。 当使用应用程序标识时，Web API 只能检测到 Web 应用程序在调用它，因为 Web API 不会收到关于用户的任何信息。 如果应用程序收到关于用户的信息，则该信息将通过应用程序协议发送，并且 Azure AD 不会对其进行签名。 Web API 相信 Web 应用程序已对用户进行了身份验证。 因此，此模式称为受信任的子系统。
- **委托用户标识** - 此方案可以通过两种方式完成：OpenID Connect 和 OAuth 2.0 授权代码使用机密客户端进行授权。 Web 应用程序为用户获取访问令牌，该令牌将向 Web API 证明用户已成功通过了 Web 应用程序的身份验证并且 Web 应用程序能够获取委托用户标识来调用 Web API。 然后会在请求中将此访问令牌发送到 Web API，后者对用户进行授权并返回所需的资源。

下面的流对应用程序标识类型和委托用户标识类型都进行了讨论。 它们之间的主要区别是，委托用户标识必须先获取一个授权代码，用户才能登录并访问 Web API。

## <a name="diagram"></a>图表

![Web 应用程序到 Web API 图示](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>协议流

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>带有 OAuth 2.0 客户端凭据授权的应用程序标识

1. 用户在 Web 应用中登录到 Azure AD（有关详细信息，请参阅 **Web 应用**部分）。
1. Web 应用程序需要获取访问令牌，以便通过 Web API 进行身份验证并检索所需的资源。 它向 Azure AD 的令牌终结点发出一个请求，在其中提供凭据、应用程序 ID 以及 Web API 的应用程序 ID URI。
1. Azure AD 对应用程序进行身份验证并返回用来调用 Web API 的 JWT 访问令牌。
1. 通过 HTTPS，Web 应用程序使用返回的 JWT 访问令牌在发往 Web API 的请求的 Authorization 标头中添加一个具有“Bearer”限定符的 JWT 字符串。 然后，Web API 对 JWT 令牌进行验证，并且如果验证成功，则返回所需的资源。

### <a name="delegated-user-identity-with-openid-connect"></a>采用 OpenID Connect 的委托用户标识

1. 用户使用 Azure AD 登录到 Web 应用程序（请参阅前面的“Web 浏览器到 Web 应用程序”部分）。 如果 Web 应用程序的用户尚未许可允许 Web 应用程序代表自己调用 Web API，则需要用户表示许可。 应用程序会显示它要求的权限，并且如果这些权限中有任何一个是管理员级权限，则目录中的普通用户将无法表示许可。 此许可过程仅适用于多租户应用程序，不适用于单租户应用程序，因为单租户应用程序那时已经具有了必需的权限。 当用户登录后，Web 应用程序将收到一个 ID 令牌，其中包含关于用户的信息以及授权代码。
1. 使用由 Azure AD 颁发的授权代码，Web 应用程序向 Azure AD 的令牌终结点发送请求，请求中包括授权代码、关于客户端应用程序的详细信息（应用程序 ID 和重定向 URI）以及所需的资源（Web API 的应用程序 ID URI）。
1. Azure AD 对授权代码和关于 Web 应用程序和 Web API 的信息进行验证。 当验证成功时，Azure AD 返回两个令牌：一个 JWT 访问令牌和一个 JWT 刷新令牌。
1. 通过 HTTPS，Web 应用程序使用返回的 JWT 访问令牌在发往 Web API 的请求的 Authorization 标头中添加一个具有“Bearer”限定符的 JWT 字符串。 然后，Web API 对 JWT 令牌进行验证，并且如果验证成功，则返回所需的资源。

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>采用 OAuth 2.0 授权代码授权的委托用户标识

1. 用户已登录到 Web 应用程序，该应用程序的身份验证机制独立于 Azure AD。
1. Web 应用程序需要一个授权代码来获取访问令牌，因此，在成功进行身份验证后，它通过浏览器向 Azure AD 的授权终结点发出一个请求，其中提供了应用程序 ID 和 Web 应用程序的重定向 URI。 用户登录到 Azure AD。
1. 如果 Web 应用程序的用户尚未许可允许 Web 应用程序代表自己调用 Web API，则需要用户表示许可。 应用程序会显示它要求的权限，并且如果这些权限中有任何一个是管理员级权限，则目录中的普通用户将无法表示许可。 此许可适用于单租户和多租户应用程序。 在单租户情况下，管理员可以代表其用户对许可执行管理员同意。 可使用 [Azure 门户](https://portal.azure.com)中的 `Grant Permissions` 按钮完成此操作。 
1. 在用户表示许可后，Web 应用程序将收到它获取访问令牌所需的授权代码。
1. 使用由 Azure AD 颁发的授权代码，Web 应用程序向 Azure AD 的令牌终结点发送请求，请求中包括授权代码、关于客户端应用程序的详细信息（应用程序 ID 和重定向 URI）以及所需的资源（Web API 的应用程序 ID URI）。
1. Azure AD 对授权代码和关于 Web 应用程序和 Web API 的信息进行验证。 当验证成功时，Azure AD 返回两个令牌：一个 JWT 访问令牌和一个 JWT 刷新令牌。
1. 通过 HTTPS，Web 应用程序使用返回的 JWT 访问令牌在发往 Web API 的请求的 Authorization 标头中添加一个具有“Bearer”限定符的 JWT 字符串。 然后，Web API 对 JWT 令牌进行验证，并且如果验证成功，则返回所需的资源。

## <a name="code-samples"></a>代码示例

请参阅 Web 应用程序到 Web API 方案的代码示例。 另外，请经常回来查看 - 我们会经常添加新示例。 Web [应用程序到 Web API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity)。

## <a name="app-registration"></a>应用注册

若要向 Azure AD v1.0 终结点注册应用程序，请参阅[向 Azure AD v1.0 终结点注册应用](quickstart-v1-add-azure-ad-app.md)。

* 单租户 - 对于应用程序标识和委托用户标识这两种情况，Web 应用和 Web API 都必须在 Azure AD 的同一个目录中进行注册。 可以对 Web API 进行配置以公开一组权限，并使用这些权限来限制 Web 应用程序对其资源的访问。 如果使用的是委托用户标识类型，则 Web 应用需要从 Azure 门户的“对其他应用程序的权限”下拉菜单中选择所需的权限。 如果使用的是应用程序标识类型，则不需要此步骤。
* 多租户 - 首先，Web 应用在配置后会指示它在正常运行时所需的权限。 当目标目录中的用户或管理员表示许可应用程序的要求时（这会使应用程序可供其组织使用），此必需权限列表会显示在一个对话框中。 某些应用程序只需要用户级权限，组织中的任何用户都可以表示许可。 另外一些应用程序需要管理员级权限，组织中的用户无法表示许可。 只有目录管理员可以对需要此级别的权限的应用程序表示许可。 当用户或管理员表示许可后，会在其目录中注册 Web 应用程序和 Web API。

## <a name="token-expiration"></a>令牌过期

当 Web 应用程序使用其授权代码来获取 JWT 访问令牌时，它还会收到一个 JWT 刷新令牌。 当访问令牌过期时，可以使用刷新令牌来重新对用户进行身份验证，而不需要他们重新登录。 然后将使用此刷新令牌对用户进行身份验证，这会生成新的访问令牌和刷新令牌。

## <a name="next-steps"></a>后续步骤

- 详细了解其他[应用程序类型和方案](app-types.md)
- 了解 Azure AD [身份验证基础知识](authentication-scenarios.md)
