---
title: 请求访问令牌-Azure Active Directory B2C |Microsoft Docs
description: 了解如何从 Azure Active Directory B2C 请求访问令牌。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d1b4bb58d777d2dc5bd79b66e128a0edd7a88adc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702961"
---
# <a name="request-an-access-token-in-azure-active-directory-b2c"></a>请求 Azure Active Directory B2C 中的访问令牌

*访问令牌*包含声明，您可以使用 Azure Active Directory (Azure AD) B2C 中你的 Api 向标识授予的权限。 在调用资源服务器时，访问令牌必须在 HTTP 请求中存在。 访问令牌表示为**access_token**从 Azure AD B2C 的响应中。 

本文介绍了如何请求 web 应用程序和 web API 的访问令牌。 有关在 Azure AD B2C 令牌的详细信息，请参阅[令牌在 Azure Active Directory B2C 概述](active-directory-b2c-reference-tokens.md)。

> [!NOTE]
> **Web API 链（代理）不受 Azure AD B2C 支持。** -许多体系结构包含的 web API 需要调用另一个下游 web API，这两者都受 Azure AD B2C。 此方案常见于具有 web API 后端，后者又调用另一个服务的客户端。 可以使用 OAuth 2.0 JWT 持有者凭据授权（也称为代理流）来支持这种链接的 Web API 方案。 但是，Azure AD B2C 中目前尚未实现代理流。

## <a name="prerequisites"></a>必备组件

- [创建用户流](tutorial-create-user-flows.md)，以便用户能够注册并登录应用程序。
- 如果尚未这样，做[添加 web API 应用程序到 Azure Active Directory B2C 租户](add-web-application.md)。

## <a name="scopes"></a>作用域

范围提供某种方式来管理对受保护资源的权限。 客户端应用程序请求访问令牌时，需要指定在所需的权限**作用域**请求的参数。 例如，若要指定**作用域值**的`read`api 具有**应用程序 ID URI**的`https://contoso.onmicrosoft.com/api`，范围应是`https://contoso.onmicrosoft.com/api/read`。

Web API 使用作用域实施基于作用域的访问控制。 例如，可以让 Web API 用户拥有读取和写入访问权限，或者只拥有读取访问权限。 若要获取在同一请求中的多个权限，可以添加多个条目中单个**作用域**请求，由空格分隔的参数。

下面的示例演示在 URL 中解码的作用域：

```
scope=https://contoso.onmicrosoft.com/api/read openid offline_access
```

下面的示例演示在 URL 中编码的作用域：

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fapi%2Fread%20openid%20offline_access
```

如果请求超过授予客户端应用程序的数目更多作用域，则调用将成功，如果授予了至少一个权限。 **Scp**中生成的访问令牌的声明填充了仅成功授予的权限。 OpenID Connect 标准指定多个特殊的作用域值。 下列作用域表示访问用户的配置文件的权限：

- **openid** -请求的 ID 令牌。
- **offline_access** -请求刷新令牌 using[授权代码流](active-directory-b2c-reference-oauth-code.md)。

如果**response_type**中的参数`/authorize`请求包括`token`，则**作用域**参数必须包含至少一个资源作用域以外`openid`和`offline_access`将被授予。 否则为`/authorize`请求失败。

## <a name="request-a-token"></a>请求令牌

若要请求访问令牌，需要一个授权代码。 下面是对的请求的一个示例`/authorize`授权代码的终结点。 不支持与访问令牌一起使用的自定义域。 使用在请求 URL 中的租户 name.onmicrosoft.com 域。

在以下示例中，会替换这些值：

- `<tenant-name>` - Azure AD B2C 租户的名称。
- `<policy-name>` - 自定义策略或用户流的名称。
- `<application-ID>` 的注册以支持用户流的 web 应用程序应用程序标识符。
- `<redirect-uri>` - 注册客户端应用程序时输入的重定向 URI。

```
GET https://<tenant-name>.b2clogin.com/tfp/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize?
client_id=<application-ID>
&nonce=anyRandomValue
&redirect_uri=https://jwt.ms
&scope=https://tenant-name>.onmicrosoft.com/api/read
&response_type=code 
```

使用授权代码响应应类似于以下示例：

```
https://jwt.ms/?code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
```

已成功收到授权代码后, 你可以使用它来请求访问令牌：

```
POST <tenant-name>.onmicrosoft.com/oauth2/v2.0/token?p=<policy-name> HTTP/1.1
Host: https://<tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code
&client_id=<application-ID>
&scope=https://<tenant-name>.onmicrosoft.com/api/read
&code=eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMC...
&redirect_uri=https://jwt.ms
&client_secret=2hMG2-_:y12n10vwH...
```

应看到类似于以下响应：

```
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

当使用 https://jwt.ms若要检查返回的访问令牌，应看到类似于下面的示例：

```
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

- 了解有关如何对[配置 Azure AD B2C 中的令牌](configure-tokens.md)
