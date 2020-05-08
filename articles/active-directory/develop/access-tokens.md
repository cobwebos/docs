---
title: Microsoft 标识平台访问令牌 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解 Azure AD v1.0 和 Microsoft 标识平台 (v2.0) 终结点发出的访问令牌。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: dedaf5214305003bf302c7c74466adb84c42b2f4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926793"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft 标识平台访问令牌

访问令牌可让客户端安全调用受保护的 Api。 Microsoft 标识平台访问令牌是[jwt](https://tools.ietf.org/html/rfc7519)的，由 Microsoft 标识平台签名的 Base64 编码的 JSON 对象。 客户端应将访问令牌视为不透明的字符串，作为令牌的内容仅适用于资源。 对于验证和调试，开发人员可以使用类似于[jwt.ms](https://jwt.ms)的站点解码 JWT （JSON Web 令牌）。 客户端可以使用各种协议从 v1.0 终结点或 v2.0 终结点获取访问令牌。

当客户端请求访问令牌时，Microsoft 标识平台还会返回有关应用使用情况的访问令牌的某些元数据。 此信息包含访问令牌的过期时间及其有效范围。 此数据可让应用执行访问令牌的智能缓存，而无需分析访问令牌本身。

如果应用程序是客户端可以请求访问的资源 (Web API)，则访问令牌会提供可在身份验证和授权中使用的有用信息，例如用户、客户端、颁发者、权限，等等。

请参阅以下部分，了解资源如何验证和使用访问令牌中的声明。

> [!IMPORTANT]
> 访问令牌是根据令牌的受众（即，拥有令牌中范围的应用程序）创建的。   如果在[应用清单](reference-app-manifest.md#manifest-reference)中将资源设置 `accessTokenAcceptedVersion` 指定为 `2`，则允许客户端调用 v1.0 终结点来接收 v2.0 访问令牌。  同样，这就是为什么更改客户端的访问令牌[可选声明](active-directory-optional-claims.md)不会更改为 `user.read` 请求令牌时收到的访问令牌的原因，该令牌归资源所有。
> 出于同一原因，当使用个人帐户（如 hotmail.com 或 outlook.com）测试客户端应用程序时，可能会发现客户端收到的访问令牌是不透明的字符串。 这是因为要访问的资源请求了旧 MSA （Microsoft 帐户）票证，它们已加密，并且客户端无法理解。

## <a name="sample-tokens"></a>示例令牌

v1.0 和 v2.0 令牌很相似，都包含许多相同的声明。 此处提供了每种令牌的示例。

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

在 [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd) 中查看此 v1.0 令牌。

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

在 [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt) 中查看此 v2.0 令牌。

## <a name="claims-in-access-tokens"></a>访问令牌中的声明

Jwt （JSON Web 令牌）拆分为三个部分：

* **标头** - 提供有关如何[验证令牌](#validating-tokens)的信息，包括有关令牌的类型及其签名方式的信息。
* **有效负载** - 包含有关正在尝试调用你的服务的用户或应用的所有重要数据。
* **签名** - 用于验证令牌的原始材料。

每个部分由句点 (`.`) 分隔，并分别采用 Base64 编码。

仅当存在可以填充声明的值时，才提供声明。 因此，应用不应该依赖于所要提供的声明。 示例包括 `pwd_exp`（并非每个租户都要求密码过期）或 `family_name`（客户端凭据（[v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)、[v2.0](v2-oauth2-client-creds-grant-flow.md)）流代表没有名称的应用程序）。 始终会提供用于验证访问令牌的声明。

> [!NOTE]
> 某些声明用于帮助 Azure AD 保护重复使用的令牌。 在说明中，这些声明标记为“不透明”，表示不可公开使用。 这些声明不一定会显示在令牌中，我们可能在不发出通告的情况下添加新的声明。

### <a name="header-claims"></a>标头声明

|声明 | 格式 | 说明 |
|--------|--------|-------------|
| `typ` | 字符串 - 始终为“JWT” | 指示令牌是 JWT。|
| `nonce` | String | 用于防范令牌重放攻击的唯一标识符。 资源可以记录此值以防范重放攻击。 |
| `alg` | String | 指示用于对令牌进行签名的算法，例如“RS256” |
| `kid` | String | 指定用于对此令牌进行签名的公钥的指纹。 在 v1.0 和 v2.0 访问令牌中已发出。 |
| `x5t` | String | 功能与 `kid` 相同（在用法和值方面）。 `x5t` 是在 v1.0 访问令牌中仅出于兼容目的而发出的旧式声明。 |

### <a name="payload-claims"></a>有效负载声明

| 声明 | 格式 | 说明 |
|-----|--------|-------------|
| `aud` | 字符串，应用 ID URI | 标识令牌的目标接收方。 在 ID 令牌中，受众是在 Azure 门户中分配给应用的应用程序 ID。 应用应该验证此值并拒绝其值不匹配的令牌。 |
| `iss` | 字符串，STS URI | 标识构造并返回令牌的安全令牌服务 (STS)，以及对用户进行身份验证的 Azure AD 租户。 如果颁发的令牌是 v2.0 令牌（请参阅 `ver` 声明），则 URI 将以 `/v2.0` 结尾。 表示用户是来自 Microsoft 帐户的使用者用户的 GUID 为 `9188040d-6c67-4c5b-b112-36a304b66dad`。 应用应该使用声明的 GUID 部分限制可登录应用的租户集（如果适用）。 |
|`idp`| 字符串，通常是 STS URI | 记录对令牌使用者进行身份验证的标识提供者。 除非用户帐户与颁发者不在同一租户中，否则此值与颁发者声明的值相同 - 例如，来宾。 如果声明不存在，则意味着可以改用 `iss` 的值。  对于在组织上下文中使用的个人帐户（例如，受邀加入 Azure AD 租户的个人帐户），`idp` 声明可能是“live.com”或包含 Microsoft 帐户租户 `9188040d-6c67-4c5b-b112-36a304b66dad` 的 STS URI。 |
| `iat` | int，UNIX 时间戳 | “Issued At”表示针对此令牌进行身份验证的时间。 |
| `nbf` | int，UNIX 时间戳 | “nbf”（不早于）声明指定只能在哪个时间之后接受 JWT 的处理。 |
| `exp` | int，UNIX 时间戳 | “exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 请务必注意，资源也可以在此时间之前拒绝令牌，例如，需要对身份验证进行更改，或者检测到令牌吊销时。 |
| `aio` | 不透明字符串 | 一个内部声明，Azure AD 用它来记录有关重复使用令牌的数据。 资源不应使用此声明。 |
| `acr` | 字符串，“0”或“1” | 仅在 v1.0 令牌中提供。 “身份验证上下文类”声明。 值为“0”表示最终用户身份验证不符合 ISO/IEC 29115 要求。 |
| `amr` | 字符串的 JSON 数组 | 仅在 v1.0 令牌中提供。 标识对令牌使用者进行身份验证的方式。 有关更多详细信息，请参阅 [amr 声明部分](#the-amr-claim)。 |
| `appid` | 字符串，GUID | 仅在 v1.0 令牌中提供。 使用令牌的客户端的应用程序 ID。 该应用程序可以自身名义或者代表用户进行操作。 应用程序 ID 通常表示应用程序对象，但它还可以表示 Azure AD 中的服务主体对象。 |
| `appidacr` | “0”、“1”或“2” | 仅在 v1.0 令牌中提供。 表示对客户端进行身份验证的方式。 对于公共客户端，值为“0”。 如果使用客户端 ID 和客户端机密，则值为“1”。 如果使用客户端证书进行身份验证，则值为“2”。 |
| `azp` | 字符串，GUID | 仅在 v2.0 令牌中存在，取代了 `appid`。 使用令牌的客户端的应用程序 ID。 该应用程序可以自身名义或者代表用户进行操作。 应用程序 ID 通常表示应用程序对象，但它还可以表示 Azure AD 中的服务主体对象。 |
| `azpacr` | “0”、“1”或“2” | 仅在 v2.0 令牌中存在，取代了 `appidacr`。 表示对客户端进行身份验证的方式。 对于公共客户端，值为“0”。 如果使用客户端 ID 和客户端机密，则值为“1”。 如果使用客户端证书进行身份验证，则值为“2”。 |
| `preferred_username` | String | 表示用户的主用户名。 可以是电子邮件地址、电话号码或未指定格式的一般用户名。 其值是可变的，可能随时改变。 由于此值是可变的，因此它不能用于做出授权决定。  但它可以用于用户名提示。 需要 `profile` 范围才能接收此声明。 |
| `name` | String | 提供一个用户可读值，用于标识令牌使用者。 该值不一定唯一，而且可变，只能用于显示目的。 需要 `profile` 范围才能接收此声明。 |
| `scp` | 字符串，范围的空格分隔列表 | 应用程序公开的、客户端应用程序已请求（和接收）其许可的范围集。 应用应该验证这些范围是否为应用公开的有效范围，并根据这些范围的值做出授权决策。 仅为[用户令牌](#user-and-application-tokens)包含此值。 |
| `roles` | 字符串数组，权限列表 | 应用程序公开的、请求方应用程序或用户有权调用的权限集。 对于[应用程序令牌](#user-and-application-tokens)，在执行客户端凭据流（[v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)、[v2.0](v2-oauth2-client-creds-grant-flow.md)）期间使用此声明代替用户范围。  对于[用户令牌](#user-and-application-tokens)，此值将填充为在目标应用程序上分配给用户的角色。 |
| `wids` | [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) GUID 的数组 | 表示在[管理员角色页](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids)中的角色部分分配给此用户的租户级角色。  此声明是通过[应用程序清单](reference-app-manifest.md)的 `groupMembershipClaims` 属性按应用程序定义的。  必须将其设置为“All”或“DirectoryRole”。  由于令牌长度方面的原因，它在通过隐式流获取的令牌中可能不存在。 |
| `groups` | GUID 的 JSON 数组 | 指定表示使用者的组成员身份的对象 ID。 这些值具有唯一性（请参阅对象 ID），可安全地用于管理访问，例如强制要求授权访问资源。 组声明中包含的组通过[应用程序清单](reference-app-manifest.md)的 `groupMembershipClaims` 属性基于每个应用程序进行配置。 值为 null 将排除所有组；值为“SecurityGroup”将只包括 Active Directory 安全组成员身份；值为“All”将包括安全组和 Office 365 通讯组列表。 <br><br>有关将 `groups` 声明与隐式授权一起使用的详细信息，请参阅下文中的 `hasgroups` 声明。 <br>对于其他流，如果用户所在的组数超出了某个限制（对于 SAML，为 150，对于 JWT，为 200），则会将超额声明添加到指向包含该用户的组列表的 Microsoft Graph 终结点的声明源。 |
| `hasgroups` | 布尔 | 如果存在，始终为 `true`，表示用户至少在一个组中。 如果完整组声明将导致 URI 片段超出 URL 长度限制（当前为 6 个或更多组），则在隐式授权流中用来替代 JWT 的 `groups` 声明。 指示客户端应当使用 Microsoft Graph API 来确定用户的组 (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`)。 |
| `groups:src1` | JSON 对象 | 对于长度不受限制（参阅上文中的 `hasgroups`）但对于令牌而言仍然太大的令牌请求，将包括指向用户的完整组列表的链接。 对于 JWT，作为分布式声明；对于 SAML，作为新声明替代 `groups` 声明。 <br><br>JWT 值示例：  <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }` |
| `sub` | 字符串，GUID | 令牌针对其断言信息的主体，例如应用的用户。 此值固定不变，无法重新分配或重复使用。 可使用它安全地执行授权检查（例如，使用令牌访问资源时），并可将它用作数据库表中的键。 由于使用者始终会在 Azure AD 颁发的令牌中存在，我们建议在通用授权系统中使用此值。 但是，使用者是成对标识符 - 它对特定应用程序 ID 是唯一的。 因此，如果单个用户使用两个不同的客户端 ID 登录到两个不同的应用，这些应用将收到两个不同的使用者声明值。 这不一定是所需的，具体取决于体系结构和隐私要求。 另请参阅 `oid` 声明（在租户中的应用之间确实会保持相同）。 |
| `oid` | 字符串，GUID | 在 Microsoft 标识平台中，对象的不可变标识符在这种情况下是用户帐户。 还可以使用它安全地执行授权检查，并将它用作数据库表中的键。 此 ID 唯一标识应用程序中的用户 - 同一个用户登录两个不同的应用程序会在 `oid` 声明中收到相同值。 因此，对 Microsoft 联机服务（例如 Microsoft Graph）发出查询时可以使用 `oid`。 Microsoft Graph 将返回此 ID 作为给定`id` [用户帐户](/graph/api/resources/user)的属性。 因为 `oid` 允许多个应用关联用户，需要 `profile` 作用域才能收到此声明。 请注意，如果单个用户存在于多个租户中，该用户将包含每个租户中的不同对象 ID - 它们将视为不同帐户，即使用户使用相同的凭据登录到每个帐户，也是如此。 |
| `tid` | 字符串，GUID | 表示用户所在的 Azure AD 租户。 对于工作和学校帐户，该 GUID 就是用户所属组织的不可变租户 ID。 对于个人帐户，该值为 `9188040d-6c67-4c5b-b112-36a304b66dad`。 需要 `profile` 范围才能接收此声明。 |
| `unique_name` | String | 仅在 v1.0 令牌中提供。 提供一个用户可读值，用于标识令牌使用者。 不保证此值在租户中唯一，只应该用于显示目的。 |
| `uti` | 不透明字符串 | Azure 用来重新验证令牌的内部声明。 资源不应使用此声明。 |
| `rh` | 不透明字符串 | Azure 用来重新验证令牌的内部声明。 资源不应使用此声明。 |
| `ver` | 字符串，`1.0` 或 `2.0` | 指示访问令牌的版本。 |

**组超额声明**

为了确保标记大小不超过 HTTP 标头大小限制，Azure AD 会限制组声明中包含的对象 Id 的数量。 如果用户所属的组数超过超额限制（SAML 令牌为 150 个，JWT 令牌为 200 个），则 Azure AD 不会在令牌中发出组声明。 但是，它会在令牌中包含超额声明，该声明指示应用程序查询 Microsoft Graph API 以检索用户的组成员身份。

```JSON
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
 }
 ```

可以使用 [App Creation Scripts](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-2-Groups/AppCreationScripts) 文件夹中提供的 `BulkCreateGroups.ps1` 来帮助测试超额方案。

#### <a name="v10-basic-claims"></a>v1.0 基本声明

以下声明将包含在 v1.0 令牌中（如果适用），默认不会包含在 v2.0 令牌中。 如果使用 v2.0 并需要其中的某个声明，请使用[可选声明](active-directory-optional-claims.md)来请求它们。

| 声明 | 格式 | 说明 |
|-----|--------|-------------|
| `ipaddr`| String | 进行身份验证的用户的来源 IP 地址。 |
| `onprem_sid`| 字符串，采用 [SID 格式](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | 如果用户使用了本地身份验证，则此声明会提供其 SID。 可在旧版应用程序中将 `onprem_sid` 用于授权。|
| `pwd_exp`| int，UNIX 时间戳 | 指示用户的密码何时过期。 |
| `pwd_url`| String | 可向用户发送的，以重置其密码的 URL。 |
| `in_corp`| boolean | 表示客户端是否从企业网络登录。 如果不是，则不包括该声明。 |
| `nickname`| String | 用户的附加名称，不同于名字或姓氏。|
| `family_name` | String | 根据用户对象中的定义提供用户的姓氏。 |
| `given_name` | String | 根据用户对象中的设置提供用户的名字。 |
| `upn` | String | 用户的用户名。 可以是电话号码、电子邮件地址或无格式的字符串。 只应该用于显示目的，在重新身份验证方案中提供用户名提示。 |

#### <a name="the-amr-claim"></a>`amr` 声明

Microsoft 标识可以通过与应用程序相关的不同方式进行身份验证。 `amr` 声明是可以包含多个项（例如 `["mfa", "rsa", "pwd"]`）的数组，适用于使用密码和 Authenticator 应用的身份验证。

| 值 | 说明 |
|-----|-------------|
| `pwd` | 密码身份验证，用户的 Microsoft 密码或应用的客户端机密。 |
| `rsa` | 身份验证基于 RSA 密钥的证明，例如，使用[Microsoft Authenticator 应用](https://aka.ms/AA2kvvu)。 这包括，身份验证是否是使用服务拥有的 X509 证书通过自签名的 JWT 执行的。 |
| `otp` | 使用电子邮件或短信的一次性密码。 |
| `fed` | 使用了联合身份验证断言（例如 JWT 或 SAML）。 |
| `wia` | Windows 集成身份验证 |
| `mfa` | 使用了多重身份验证。 如果存在这种情况，则也会包含其他身份验证方法。 |
| `ngcmfa` | 等效于 `mfa`，用于预配某些高级凭据类型。 |
| `wiaormfa`| 用户已使用 Windows 或 MFA 凭据进行身份验证。 |
| `none` | 未执行任何身份验证。 |

## <a name="validating-tokens"></a>验证令牌

若要验证 id_token 或 access_token，应用应该验证该令牌的签名和声明。 若要验证访问令牌，应用还需验证颁发者、受众和签名令牌。 这些需要根据 OpenID 发现文档中的值进行验证。 例如，文档的租户独立版本位于 [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)。

Azure AD 中间件具有验证访问令牌的内置功能，可以浏览我们的[示例](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples)，以所选语言进行查找。

我们提供了库和代码示例，用以演示如何轻松处理令牌验证。 我们还提供了以下信息以帮助用户了解基础过程。 另外还有多个第三方开源库可用于 JWT 验证 - 几乎每个平台和语言都至少有一个选项。 有关 Azure AD 身份验证库和代码示例的详细信息，请参阅 [v1.0 身份验证库](../azuread-dev/active-directory-authentication-libraries.md)和 [v2.0 身份验证库](reference-v2-libraries.md)。

### <a name="validating-the-signature"></a>验证签名

JWT 包含三个段（以 `.` 字符分隔）。 第一个段称为标头，第二个称为主体，第三个称为签名。    签名段可用于验证令牌的真实性，使令牌可获得应用的信任。

Azure AD 颁发的令牌已使用行业标准非对称式加密算法（例如 RS256）进行签名。 JWT 的标头包含用于签名令牌的密钥和加密方法的相关信息：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` 声明表示用于对令牌进行签名的算法，而 `kid` 声明表示用于验证令牌的特定公钥。

在任何给定时间点，Azure AD 可以使用特定公钥 - 私钥对中的任何一组对 id_token 进行签名。 Azure AD 会定期滚动更新一组可能的密钥，因此应将应用编写成自动处理这些密钥更改。 对 Azure AD 所用公钥的更新进行检查的合理频率为每 24 小时一次。

可以使用位于以下位置的 [OpenID Connect 元数据文档](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)来获取验证签名所需的签名密钥数据：

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 在浏览器中尝试打开此 [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration)！

此元数据文档：

* 是一个 JSON 对象，其中包含一些有用的信息，例如执行 OpenID Connect 身份验证所需的各种终结点的位置。
* 包含 `jwks_uri`，提供用于对令牌进行签名的公钥集的位置。 位于 `jwks_uri` 的 JSON Web 密钥 (JWK) 包含在该特定时间点使用的所有公钥信息。  [RFC 7517](https://tools.ietf.org/html/rfc7517) 中对 JWK 格式进行了说明。  应用可以使用 JWT 标头中的 `kid` 声明选择本文档中已用于对特定令牌进行签名的公钥。 然后可以使用正确的公钥和指定的算法来执行签名验证。

> [!NOTE]
> V1.0 终结点返回 `x5t` 和 `kid` 声明，尽管 v2.0 终结点仅使用 `kid` 声明进行响应。 从目前开始，我们建议使用 `kid` 声明来验证令牌。

执行签名验证超出了本文档的范围 - 有许多开源库可帮助你执行验证（如有必要）。  但是，Microsoft 标识平台具有标准的一个令牌签名扩展 - 自定义签名密钥。

如果应用因使用[声明映射](active-directory-claims-mapping.md)功能而具有自定义签名密钥，则必须追加包含应用 ID 的 `appid` 查询参数，以获取指向应用的签名密钥信息的 `jwks_uri`，该信息应该用于验证。 例如：`https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含 `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 的 `jwks_uri`。

### <a name="claims-based-authorization"></a>基于声明的授权

应用程序的业务逻辑将会强制规定此步骤，下面列出了一些常见的授权方法。

* 检查 `scp` 或 `roles` 声明，以验证所有现有的范围是否与 API 公开的范围相匹配，并允许客户端执行请求的操作。
* 确保允许调用方客户端使用 `appid` 声明调用你的 API。
* 使用 `appidacr` 验证调用方客户端的身份验证状态 - 如果不允许公共客户端调用你的 API，则值不应该是 0。
* 根据以往的 `nonce` 声明列表进行检查，以验证令牌是否未重放。
* 检查 `tid` 是否与允许调用该 API 的租户相匹配。
* 使用 `acr` 声明验证已执行 MFA 的用户。 应使用[条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)强制实施此步骤。
* 如果在访问令牌中请求了 `roles` 或 `groups` 声明，请验证用户是否在允许执行此操作的组中。
  * 对于使用隐式流检索的令牌，可能需要在 [Microsoft Graph](https://developer.microsoft.com/graph/) 中查询此数据，因为该数据通常很庞大，无法放到令牌中。

## <a name="user-and-application-tokens"></a>用户和应用程序令牌

应用程序可以代表用户（通常的流）或直接从应用程序（通过客户端凭据流（[v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)、[v2.0](v2-oauth2-client-creds-grant-flow.md)）接收令牌。 这些仅限应用的令牌表示这种调用来自应用程序，而没有支持它的用户。 这些令牌的处理方式大致相同，但存在一些差别：

* 仅限应用的令牌不包含 `scp` 声明，而是包含 `roles` 声明。 这是记录应用程序权限的位置（与委托的权限相反）。 有关委托的权限和应用程序权限的详细信息，请参阅权限和同意（[v1.0](../azuread-dev/v1-permissions-consent.md)、[v2.0](v2-permissions-and-consent.md)）。
* 许多特定于用户的声明将会缺失，例如 `name` 或 `upn`。
* `sub` 和 `oid` 声明相同。

## <a name="token-revocation"></a>令牌吊销

刷新令牌可能由于不同的原因而随时失效或吊销。 这些原因主要分为两个类别：超时和吊销。

### <a name="token-timeouts"></a>令牌超时

使用[令牌生存期配置](active-directory-configurable-token-lifetimes.md)，可以更改刷新令牌的生存期。  这是正常的，应在不使用的情况下（例如，用户在3个月内未打开应用程序），并因此过期。  应用程序将遇到这样的情况：登录服务器拒绝刷新令牌，因为其期限。 

* MaxInactiveTime：如果在 MaxInactiveTime 指定的时间内未使用刷新令牌，刷新令牌将不再有效。
* MaxSessionAge：如果 MaxAgeSessionMultiFactor 或 MaxAgeSessionSingleFactor 已设置为其默认值（“直到吊销”）以外的值，则在经过 MaxAgeSession* 中设置的时间后，需要重新进行身份验证。
* 示例:
  * 租户的 MaxInactiveTime 为 5 天，用户去度假一周，因此 Azure AD 在 7 天内未看到用户发出的新令牌请求。 下次用户请求新令牌时，他们将看到其刷新令牌已被吊销，他们必须重新输入其凭据。
  * 敏感应用程序的 MaxAgeSessionSingleFactor 为 1 天。 如果用户在星期一登录，则在星期二（25 个小时后），他们需要重新进行身份验证。

### <a name="revocation"></a>撤销

服务器可能会由于以下原因而撤销刷新令牌：凭据发生更改，或者用户或管理员执行了相关操作。  刷新令牌分为两类：颁发给机密客户端的刷新令牌（最右边的列），和颁发给公共客户端的刷新令牌（所有其他列）。   

|   | 基于密码的 Cookie | 基于密码的令牌 | 不基于密码的 Cookie | 不基于密码的令牌 | 机密客户端令牌 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 密码过期 | 一直有效 | 一直有效 | 一直有效 | 一直有效 | 一直有效 |
| 用户更改了密码 | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 用户执行 SSPR | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 管理员重置密码 | 已撤销 | 已撤销 | 一直有效 | 一直有效 | 一直有效 |
| 用户[通过 PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) 撤销刷新令牌 | 已撤销 | 已撤销 | 已撤销 | 已撤销 | 已撤销 |
| 管理员[通过 PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) 撤销用户的所有刷新令牌 | 已撤销 | 已撤销 |已撤销 | 已撤销 | 已撤销 |
| Web 上的单一注销（[v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out)、[v2.0](v2-protocols-oidc.md#single-sign-out)） | 已撤销 | 一直有效 | 已撤销 | 一直有效 | 一直有效 |

> [!NOTE]
> “不基于密码”登录是指用户在未键入密码的情况下登录。 例如，在 Windows Hello 中使用人脸登录、使用 FIDO2 密钥或 PIN 登录。
>
> Windows 10 上的主刷新令牌 (PRT) 基于凭据进行隔离。 例如，Windows Hello 和密码有各自的 PRT，彼此隔离。 当用户使用 Hello 凭据（PIN 或生物识别）登录，然后更改密码时，先前获得的基于密码的 PRT 将被撤销。 使用密码重新登录会使旧的 PRT 无效，并请求一个新的 PRT。
>
> 在用于提取新访问令牌和刷新令牌时，刷新令牌不会失效或撤销。  但是，你的应用应该在使用旧令牌后立即丢弃它，并将其替换为新令牌，因为新令牌中有一个新的过期时间。 

## <a name="next-steps"></a>后续步骤

* 了解 [Azure AD 中的 `id_tokens`](id-tokens.md)。
* 了解权限和同意（[v1.0](../azuread-dev/v1-permissions-consent.md)、[v2.0](v2-permissions-and-consent.md)）。
