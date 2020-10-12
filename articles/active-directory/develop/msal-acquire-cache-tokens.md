---
title: 使用 Microsoft 身份验证库 (MSAL) 获取和缓存令牌 | Azure
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
ms.openlocfilehash: 47af4015fa5c6d9a73ee597146890a29b4b9ef9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119889"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>使用 Microsoft 身份验证库 (MSAL) 获取和缓存令牌

客户端可以使用[访问令牌](access-tokens.md)安全调用受 Azure 保护的 Web API。 可以使用 Microsoft 身份验证库 (MSAL) 通过多种方式获取令牌。 某些方式需要用户通过 Web 浏览器进行交互，而另一些方式则不需要用户交互。 一般情况下，用于获取令牌的方式取决于应用程序是公共客户端应用程序（例如桌面或移动应用）还是机密客户端应用程序（例如 Web 应用、Web API 或守护程序应用程序）。

MSAL 在获取令牌后会缓存令牌。 在尝试通过其他方式获取令牌之前，应用程序代码应该先尝试以无提示方式从缓存中获取令牌。

你也可以清除令牌缓存，这可以通过从缓存中删除帐户来实现。 不过，这不会删除浏览器中的会话 Cookie。

## <a name="scopes-when-acquiring-tokens"></a>获取令牌时的范围

[范围](v2-permissions-and-consent.md)是客户端应用程序可以请求访问的 Web API 公开的权限。 在发出身份验证请求以获取用于访问 Web API 的令牌时，客户端应用程序将请求用户许可这些范围。 使用 MSAL 可以获取令牌来访问面向开发人员的 Azure AD (v1.0) 和 Microsoft 标识平台 (v2.0) API。 v2.0 协议在请求中使用范围而不是资源。 有关详细信息，请阅读 [v1.0 与 v2.0 的比较](../azuread-dev/azure-ad-endpoint-comparison.md)。 根据 v2.0 终结点接受的令牌版本的 Web API 配置，该终结点会将访问令牌返回到 MSAL。

MSAL 的一些令牌获取方法需要使用 `scopes` 参数。 `scopes` 参数是一个字符串列表，这些字符串声明了所需的权限和所请求的资源。 广为人知的范围是 [Microsoft Graph 权限](/graph/permissions-reference)。

在 MSAL 中也可以访问 v1.0 资源。 有关详细信息，请参阅 [v1.0 应用程序的范围](msal-v1-app-scopes.md)。

### <a name="request-scopes-for-a-web-api"></a>请求 Web API 的范围

当应用程序需要请求对资源 API 具有特定权限的访问令牌时，请按 `<app ID URI>/<scope>` 格式传递包含 API 的应用 ID URI 的范围。

适用于不同资源的一些示例范围值：

- Microsoft Graph API：`https://graph.microsoft.com/User.Read`
- 自定义 Web API：`api://11111111-1111-1111-1111-111111111111/api.read`

范围值的格式因接收访问令牌的资源 (API) 和其接受的 `aud` 声明值而异。

单纯对于 Microsoft Graph 而言，`user.read` 范围映射到 `https://graph.microsoft.com/User.Read`，这两种范围格式可以互换使用。

某些 Web API（例如 Azure 资源管理器 API (https://management.core.windows.net/) ）要求在访问令牌的受众声明 (`aud` ) 中使用尾随正斜杠“/”。 在这种情况下，请将范围作为 `https://management.core.windows.net//user_impersonation` 传递，其中包括双正斜杠 ('//')。

其他 API 可能要求不在范围值中包括方案或主机，并且仅需要应用 ID (GUID) 和范围名称，例如：

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> 如果下游资源不受你的控制，而你在将访问令牌传递给资源时收到 `401` 或其他错误，你可能需要尝试不同的范围值格式（例如，包含/不包含方案和主机）。

### <a name="request-dynamic-scopes-for-incremental-consent"></a>请求增量许可的动态范围

当应用程序提供的功能或其要求发生变化时，你可以根据需要使用范围参数请求其他权限。 这样的动态范围允许用户针对范围提供增量许可。

例如，你可以让用户登录，但最初拒绝他们访问任何资源。 然后，你可以在令牌获取方法中请求日历范围并获得用户的同意，从而让用户能够查看其日历。 例如，可以请求 `https://graph.microsoft.com/User.Read` 和 `https://graph.microsoft.com/Calendar.Read` 范围。

## <a name="acquiring-tokens-silently-from-the-cache"></a>以无提示方式（从缓存中）获取令牌

MSAL 维护一个令牌缓存（对于机密客户端应用程序，则会维护两个），获取令牌后会缓存令牌。 在许多情况下，尝试以无提示方式获取令牌会根据缓存中的令牌获取具有更多范围的另一个令牌。 此外，当某个令牌即将过期时可以刷新该令牌（因为令牌缓存中也包含一个刷新令牌）。

### <a name="recommended-call-pattern-for-public-client-applications"></a>公共客户端应用程序的建议调用模式

应用程序代码应该先尝试以无提示方式从缓存中获取令牌。 如果方法调用返回“需要 UI”错误或异常，请尝试通过其他方式获取令牌。

但是，在下面的两个流中，**不应**尝试以无提示方式获取令牌：

- [客户端凭据流](msal-authentication-flows.md#client-credentials)：不使用用户令牌缓存，而是使用应用程序令牌缓存。 在将请求发送到安全令牌服务 (STS) 之前，此方法负责验证此应用程序令牌缓存。
- Web 应用中的[授权代码流](msal-authentication-flows.md#authorization-code)：因为它兑换应用程序通过将用户登录并让他们许可更多范围而获得的代码。 由于是将代码而不是将帐户作为参数传递，该方法在兑换代码之前无法查看缓存，而兑换代码需要调用服务。

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>使用授权代码流的 Web 应用中的建议调用模式

对于使用 [OpenID Connect 授权代码流](v2-protocols-oidc.md)的 Web 应用程序，控制器中的建议模式为：

- 使用已经过自定义序列化的令牌缓存实例化某个机密客户端应用程序。
- 使用授权代码流获取令牌

## <a name="acquiring-tokens"></a>获取令牌

一般情况下，获取令牌的方法取决于应用程序是公共客户端还是机密客户端应用程序。

### <a name="public-client-applications"></a>公共客户端应用程序

对于公共客户端应用程序（桌面或移动应用）：

- 通常是让用户通过 UI 或弹出窗口登录，以交互方式获取令牌。
- 如果桌面应用程序在已加入域或 Azure 的 Windows 计算机上运行，则可以使用 Windows 集成身份验证 (IWA/Kerberos) [以无提示方式获取已登录用户的令牌](msal-authentication-flows.md#integrated-windows-authentication)。
- 可以在 .NET Framework 桌面客户端应用程序中[使用用户名和密码获取令牌](msal-authentication-flows.md#usernamepassword)（不建议这样做）。 在机密客户端应用程序中不要使用用户名/密码。
- 如果应用程序在没有 Web 浏览器的设备上运行，则可在应用程序中通过[设备代码流](msal-authentication-flows.md#device-code)获取令牌。 系统将为用户提供 URL 和代码，然后，他们可以在另一台设备上打开 Web 浏览器、输入代码并登录。 然后，Azure AD 会将令牌发回到没有浏览器的设备。

### <a name="confidential-client-applications"></a>机密客户端应用程序

对于机密客户端应用程序（Web 应用、Web API 或类似于 Windows 服务的守护程序应用程序），请执行以下操作：

- 使用[客户端凭据流](msal-authentication-flows.md#client-credentials)获取**应用程序本身**而不是用户的令牌。 此方法可用于同步工具或者那些处理普通用户而不是特定用户的工具。
- 使用适用于 Web API 的[代表流](msal-authentication-flows.md#on-behalf-of)代表用户调用 API。 系统会使用客户端凭据标识应用程序，以根据用户断言（例如 SAML 或 JWT 令牌）获取令牌。 需要在服务到服务调用中访问特定用户的资源的应用程序使用此流。
- 在用户通过授权请求 URL 登录后，在 Web 应用中使用[授权代码流](msal-authentication-flows.md#authorization-code)获取令牌。 OpenID Connect 应用程序通常使用此机制，可让用户使用 Open ID Connect 登录，然后代表用户访问 Web API。

## <a name="authentication-results"></a>身份验证结果

当客户端请求访问令牌时，Azure AD 还会返回一条身份验证结果，其中包含有关访问令牌的元数据。 此信息包含访问令牌的过期时间及其有效范围。 此数据可让应用执行访问令牌的智能缓存，而无需分析访问令牌本身。 身份验证结果公开：

- 由 Web API 用来访问资源的[访问令牌](access-tokens.md)。 此字符串通常是一个 Base64 编码的 JWT，但客户端不得查看访问令牌的内部信息。 不保证格式稳定，并且可以为资源加密令牌。 在客户端上依赖访问令牌内容编写代码是最常见的错误来源之一，并且会违反客户端逻辑。
- 用户的 [ID 令牌](id-tokens.md) (JWT)。
- 令牌过期时间，告知令牌的过期日期/时间。
- 租户 ID 包含用户所在的租户。 对于来宾用户（Azure AD B2B 方案），租户 ID 是来宾租户，而不是唯一的租户。 以用户的名义传送令牌时，身份验证结果还包含有关此用户的信息。 对于未使用用户帐户请求令牌的机密客户端流（适用于应用程序），此用户信息为 null。
- 令牌的颁发范围。
- 用户的唯一 ID。

## <a name="next-steps"></a>后续步骤

如果使用的是适用于 Java 的 MSAL，请了解[适用于 Java 的 MSAL 中的自定义令牌缓存序列化](msal-java-token-cache-serialization.md)。

了解如何[处理错误和异常](msal-handling-exceptions.md)。