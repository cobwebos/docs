---
title: 利用 Microsoft 身份验证库（MSAL）获取 & 缓存令牌 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何使用 MSAL 获取和缓存令牌。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: bdf9c1ce36921076ab79b2ca501bf008eddfe375
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194052"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>使用 Microsoft 身份验证库（MSAL）获取和缓存令牌

客户端可以使用[访问令牌](access-tokens.md)安全调用受 Azure 保护的 Web API。 可以通过多种方式使用 Microsoft 身份验证库（MSAL）获取令牌。 某些用户需要用户通过 web 浏览器进行交互，而其他人则不需要用户交互。 通常，用于获取令牌的方法取决于应用程序是否是公共客户端应用程序（如桌面或移动应用程序）或机密客户端应用程序（如 web 应用程序、web API 或后台程序应用程序）。

MSAL 在获取令牌后对其进行缓存。 应用程序代码应首先尝试从缓存中获取令牌，然后再尝试通过其他方式获取令牌。

你也可以清除令牌缓存，这可以通过从缓存中删除帐户来实现。 但这并不会删除浏览器中的会话 cookie。

## <a name="scopes-when-acquiring-tokens"></a>获取令牌时的范围

[作用域](v2-permissions-and-consent.md)是 web API 公开的权限，客户端应用程序可以请求对其进行访问。 在发出身份验证请求以获取用于访问 Web API 的令牌时，客户端应用程序将请求用户许可这些范围。 使用 MSAL 可以获取令牌来访问面向开发人员的 Azure AD (v1.0) 和 Microsoft 标识平台 (v2.0) API。 v2.0 协议在请求中使用范围而不是资源。 有关详细信息，请阅读 [v1.0 与 v2.0 的比较](active-directory-v2-compare.md)。 根据 v2.0 终结点接受的令牌版本的 Web API 配置，该终结点会将访问令牌返回到 MSAL。

MSAL 的一些令牌采集方法需要一个 `scopes` 参数。 `scopes`参数是一个字符串列表，这些字符串声明所需的权限和请求的资源。 众所周知的作用域是[Microsoft Graph 的权限](/graph/permissions-reference)。

在 MSAL 中也可以访问 v1.0 资源。 有关详细信息，请参阅 v2.0 [1.0 应用程序的作用域](msal-v1-app-scopes.md)。

### <a name="request-scopes-for-a-web-api"></a>Web API 的请求范围

当应用程序需要为资源 API 请求具有特定权限的访问令牌时，请以格式传递包含 API 的应用 ID URI 的作用域 `<app ID URI>/<scope>` 。

不同资源的一些示例范围值：

- Microsoft Graph API：`https://graph.microsoft.com/User.Read`
- 自定义 web API：`api://11111111-1111-1111-1111-111111111111/api.read`

作用域值的格式因接收访问令牌的资源（API）和 `aud` 它接受的声明值而异。

对于仅 Microsoft Graph， `user.read` 范围将映射到 `https://graph.microsoft.com/User.Read` ，并且这两个范围格式可互换使用。

某些 web Api （如 Azure 资源管理器 API）（ https://management.core.windows.net/) 需要访问令牌的受众声明（）中的尾随正斜杠（"/"） `aud` 。 在这种情况下，将作用域传递为 `https://management.core.windows.net//user_impersonation` ，包括双正斜杠（"//"）。

其他 Api 可能要求范围值中*不包含方案或主机*，并且仅预期应用 ID （GUID）和作用域名称，例如：

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> 如果下游资源不受控制，则如果在将 `401` 访问令牌传递给资源时收到或其他错误，则可能需要尝试不同的作用域值格式（例如，具有/不含方案和主机）。

### <a name="request-dynamic-scopes-for-incremental-consent"></a>请求增量许可的动态范围

当应用程序提供的功能或其要求发生变化时，你可以根据需要使用 scope 参数请求其他权限。 此类*动态范围*允许用户为范围提供增量许可。

例如，你可能会登录用户，但最初拒绝他们访问任何资源。 稍后，你可以通过请求获取令牌方法中的日历范围并获取用户同意来查看其日历。 例如，通过请求 `https://graph.microsoft.com/User.Read` 和 `https://graph.microsoft.com/Calendar.Read` 范围。

## <a name="acquiring-tokens-silently-from-the-cache"></a>以无提示方式（从缓存中）获取令牌

MSAL 维护一个令牌缓存（或机密客户端应用程序的两个缓存）并在获取令牌后缓存该令牌。 在许多情况下，尝试以无提示方式获取令牌会根据缓存中的令牌获取具有更多范围的另一个令牌。 此外，当某个令牌即将过期时可以刷新该令牌（因为令牌缓存中也包含一个刷新令牌）。

### <a name="recommended-call-pattern-for-public-client-applications"></a>公共客户端应用程序的建议调用模式

应用程序代码应首先尝试从缓存中无提示地获取令牌。 如果方法调用返回“需要 UI”错误或异常，请尝试通过其他方式获取令牌。

但有两个流，**不应**尝试以无提示方式获取令牌：

- [客户端凭据流](msal-authentication-flows.md#client-credentials)，不使用用户令牌缓存，而使用应用程序令牌缓存。 此方法负责在向 security token service （STS）发送请求之前验证应用程序令牌缓存。
- Web 应用中的[授权代码流](msal-authentication-flows.md#authorization-code)，因为它兑换了应用程序通过登录用户并同意更多作用域获得的代码。 由于代码而不是帐户作为参数传递，因此在兑换代码之前，该方法将无法在缓存中查找，这将调用对服务的调用。

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>使用授权代码流的 web 应用中的建议调用模式

对于使用 [OpenID Connect 授权代码流](v2-protocols-oidc.md)的 Web 应用程序，控制器中的建议模式为：

- 使用已经过自定义序列化的令牌缓存实例化某个机密客户端应用程序。
- 使用授权代码流获取令牌

## <a name="acquiring-tokens"></a>获取令牌

一般情况下，获取令牌的方法取决于应用程序是公共客户端还是机密客户端应用程序。

### <a name="public-client-applications"></a>公共客户端应用程序

对于公共客户端应用程序（桌面或移动应用）：

- 通常是让用户通过 UI 或弹出窗口登录，以交互方式获取令牌。
- 如果桌面应用程序在已加入域或 Azure 的 Windows 计算机上运行，则可以使用 Windows 集成身份验证 (IWA/Kerberos) [以无提示方式获取已登录用户的令牌](msal-authentication-flows.md#integrated-windows-authentication)。
- 可以在 .NET framework 桌面客户端应用程序中[使用用户名和密码获取令牌](msal-authentication-flows.md#usernamepassword)（不推荐）。 在机密客户端应用程序中不要使用用户名/密码。
- 可以通过[设备代码流](msal-authentication-flows.md#device-code)在没有 web 浏览器的设备上运行的应用程序中获取令牌。 系统将为用户提供 URL 和代码，然后，他们可以在另一台设备上打开 Web 浏览器、输入代码并登录。 然后，Azure AD 会将令牌发回到没有浏览器的设备。

### <a name="confidential-client-applications"></a>机密客户端应用程序

对于机密客户端应用程序（web 应用、web API 或 Windows 服务等后台程序应用程序），你需要：

- 使用[客户端凭据流](msal-authentication-flows.md#client-credentials)获取**应用程序本身**而不是用户的令牌。 此方法可用于同步工具或处理一般用户的工具，而不是特定用户。
- 为 web API 使用[代表流](msal-authentication-flows.md#on-behalf-of)，以代表用户调用 API。 应用程序使用客户端凭据进行标识，以便基于用户断言（例如 SAML，或 JWT 令牌）获取令牌。 需要在服务到服务调用中访问特定用户的资源的应用程序使用此流。
- 在用户通过授权请求 URL 登录后，在 Web 应用中使用[授权代码流](msal-authentication-flows.md#authorization-code)获取令牌。 OpenID Connect 应用程序通常使用此机制，可让用户使用 Open ID Connect 登录，然后代表用户访问 Web API。

## <a name="authentication-results"></a>身份验证结果

当客户端请求访问令牌时，Azure AD 还会返回包含有关访问令牌的元数据的身份验证结果。 此信息包含访问令牌的过期时间及其有效范围。 此数据可让应用执行访问令牌的智能缓存，而无需分析访问令牌本身。 身份验证结果公开：

- 由 Web API 用来访问资源的[访问令牌](access-tokens.md)。 此字符串通常是 Base64 编码的 JWT，但客户端不应在访问令牌中查看。 不保证格式稳定，并且可以为资源加密令牌。 根据客户端的访问令牌内容编写代码的人是错误和客户端逻辑破损的最常见原因之一。
- 用户的[ID 令牌](id-tokens.md)（JWT）。
- 令牌过期时间，告知令牌的过期日期/时间。
- 租户 ID 包含用户所在的租户。 对于来宾用户（Azure AD B2B 方案），租户 ID 是来宾租户，而不是唯一的租户。 以用户的名义传送令牌时，身份验证结果还包含有关此用户的信息。 对于未使用用户帐户请求令牌的机密客户端流（适用于应用程序），此用户信息为 null。
- 令牌的颁发范围。
- 用户的唯一 ID。

## <a name="next-steps"></a>后续步骤

如果使用的是 MSAL for Java，请参阅[MSAL For java 中的自定义令牌缓存序列化](msal-java-token-cache-serialization.md)。

了解如何[处理错误和异常](msal-handling-exceptions.md)。
