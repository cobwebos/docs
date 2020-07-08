---
title: 请求访问令牌 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何从 Azure Active Directory B2C 请求访问令牌。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5c478ac6641fe5b1e342c063d134f70084b2ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201440"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中请求访问令牌

访问令牌包含可在 Azure Active Directory B2C (Azure AD B2C) 中使用的声明，用于标识已授予的对 API 的权限。 调用资源服务器时，必须在 HTTP 请求中提供访问令牌。 访问令牌在 Azure AD B2C 的响应中表示为 access_token。

本文介绍如何请求 Web 应用和 Web API 的访问令牌。 有关 Azure AD B2C 中的令牌的详细信息，请参阅 [Azure Active Directory B2C 中的令牌概述](tokens-overview.md)。

> [!NOTE]
> **Web API 链（代理）不受 Azure AD B2C 支持。** - 许多体系结构包含需要调用另一个下游 Web API 的 Web API，这两者都受 Azure AD B2C 的保护。 此方案常见于包含 Web API 后端的客户端，该后端调用另一个服务。 可以使用 OAuth 2.0 JWT 持有者凭据授权（也称为代理流）来支持这种链接的 Web API 方案。 但是，Azure AD B2C 中目前尚未实现代理流。

## <a name="prerequisites"></a>先决条件

- [创建用户流](tutorial-create-user-flows.md)，以便用户能够注册并登录应用程序。
- 如果尚未执行此操作，请[向 Azure Active Directory B2C 租户添加 Web API 应用程序](add-web-api-application.md)。

## <a name="scopes"></a>作用域

可通过作用域管理对受保护资源的权限。 在请求访问令牌时，客户端应用程序需要在请求的 scope 参数中指定所需的权限。 例如，若要指定具有 `https://contoso.onmicrosoft.com/api`“应用 ID URI”的 API“作用域值”`read`，该作用域将是 `https://contoso.onmicrosoft.com/api/read` 。

Web API 使用作用域实施基于作用域的访问控制。 例如，可以让 Web API 用户拥有读取和写入访问权限，或者只拥有读取访问权限。 若要在同一请求中获取多个权限，可在请求的单个 scope 参数中添加多个条目并用空格分隔。

以下示例显示了已在 URL 中解码的作用域：

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

以下示例显示了已在 URL 中编码的作用域：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

如果请求的作用域数超过为客户端应用程序授予的数目，则在至少授予一个权限时调用将成功。 生成的访问令牌中的 scp 声明将仅使用已成功授予的权限进行填充。 OpenID Connect 标准指定了多个特殊的 scope 值。 以下作用域表示访问用户配置文件的权限：

- **openid** - 请求 ID 令牌。
- **offline_access** - 使用[授权代码流](authorization-code-flow.md)请求刷新令牌。

如果 `/authorize` 请求中的 response_type 参数包含 `token`，那么 scope 参数必须包含至少一个将被授予的资源作用域（`openid` 和 `offline_access` 除外） 。 否则，`/authorize` 请求将失败。

## <a name="request-a-token"></a>请求令牌

若要请求访问令牌，需要一个授权代码。 下面是对 `/authorize` 终结点发出的授权代码请求的示例。 不支持将自定义域与访问令牌一起使用。 请使用请求 URL 中的 tenant-name.onmicrosoft.com 域。

在以下示例中，会替换这些值：

- `<tenant-name>` - Azure AD B2C 租户的名称。
- `<policy-name>` - 自定义策略或用户流的名称。
- `<application-ID>` - 为支持用户流而注册的 Web 应用的应用程序标识符。
- `<redirect-uri>` - 注册客户端应用程序时输入的重定向 URI。

```http
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&response_type=code
```

授权代码的响应类似于以下示例：

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

成功收到授权码后，可以使用它来请求访问令牌：

```http
POST <tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

应会看到类似于下面的响应：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrN...",
    "token_type": "Bearer",
    "not_before": 1549647431,
    "expires_in": 3600,
    "expires_on": 1549651031,
    "resource": "f2a76e08-93f2-4350-833c-965c02483b11",
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiJjNjRhNGY3ZC0zMDkxLTRjNzMtYTcyMi1hM2YwNjk0Z..."
}
```

使用 https://jwt.ms 检查返回的访问令牌时，应会看到类似于以下示例的内容：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dl..."
}.{
  "iss": "https://contoso0926tenant.b2clogin.com/c64a4f7d-3091-4c73-a7.../v2.0/",
  "exp": 1549651031,
  "nbf": 1549647431,
  "aud": "f2a76e08-93f2-4350-833c-965...",
  "oid": "1558f87f-452b-4757-bcd1-883...",
  "sub": "1558f87f-452b-4757-bcd1-883...",
  "name": "David",
  "tfp": "B2C_1_signupsignin1",
  "nonce": "anyRandomValue",
  "scp": "read",
  "azp": "38307aee-303c-4fff-8087-d8d2...",
  "ver": "1.0",
  "iat": 1549647431
}.[Signature]
```

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure AD B2C 中配置令牌](configure-tokens.md)
