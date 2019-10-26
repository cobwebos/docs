---
title: 通过 OpenID Connect 进行 Web 登录-Azure Active Directory B2C
description: 在 Azure Active Directory B2C 中使用 OpenID Connect 身份验证协议生成 web 应用程序。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b3f3727fe3705d686f25faedf1871e5aacb74352
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893268"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 OpenID Connect 进行 Web 登录

OpenID Connect 是构建在 OAuth 2.0 基础之上的身份验证协议，可用于将用户安全登录到 Web 应用程序。 通过使用 OpenID Connect 的 Azure Active Directory B2C (Azure AD B2C) 实现，可以将 Web 应用程序中的注册、登录和其他标识管理体验转移到 Azure Active Directory (Azure AD) 中。 本指南演示如何使用与语言无关的方式执行此操作。 介绍在不使用我们的任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 扩展了 OAuth 2.0 *授权*协议，将其用作*身份验证*协议。 此身份验证协议允许你执行单一登录。 它引入了*ID 令牌*的概念，该令牌允许客户端验证用户的身份，并获取有关用户的基本配置文件信息。

由于它扩展了 OAuth 2.0，因此它还允许应用程序安全获取*访问令牌*。 可以使用 access_token 访问由[授权服务器](active-directory-b2c-reference-protocols.md)保护的资源。 如果要构建在服务器上托管并通过浏览器访问的 web 应用程序，则建议使用 OpenID Connect。 如果要使用 Azure AD B2C 向移动或桌面应用程序添加标识管理，则应使用[OAuth 2.0](active-directory-b2c-reference-oauth-code.md) ，而不是 OpenID connect。 有关令牌的详细信息，请参阅[中的令牌概述 Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C 扩展了标准 OpenID Connect 协议，使其功能远远超出了简单的身份验证和授权。 它引入了[用户流参数](active-directory-b2c-reference-policies.md)，使你可以使用 OpenID connect 向应用程序添加用户体验，例如注册、登录和配置文件管理。

## <a name="send-authentication-requests"></a>发送身份验证请求

当 web 应用程序需要对用户进行身份验证并运行用户流时，可以将用户定向到 `/authorize` 终结点。 用户根据用户流执行操作。

在此请求中，客户端指示需要在 `scope` 参数中从用户获取的权限，并指定要运行的用户流。 若要查看请求的工作方式，请尝试将请求粘贴到浏览器并运行该请求。 将 `{tenant}` 替换为租户的名称。 将 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` 替换为之前在租户中注册的应用程序的应用程序 ID。 还将策略名称（`{policy}`）更改为你在租户中拥有的策略名称，例如 `b2c_1_sign_in`。

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 参数 | 需要 | 描述 |
| --------- | -------- | ----------- |
| 组织 | 是 | Azure AD B2C 租户的名称 |
| 政策 | 是 | 要运行的用户流。 指定在 Azure AD B2C 租户中创建的用户流的名称。 例如： `b2c_1_sign_in`、`b2c_1_sign_up`或 `b2c_1_edit_profile`。 |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| nonce | 是 | 包含在请求中的值（由应用程序生成），在生成的 ID 令牌中包含为声明。 然后，应用程序可以验证此值，以减少令牌重放攻击。 此值通常是随机的唯一字符串，可用以识别请求的来源。 |
| response_type | 是 | 必须包括用于 OpenID Connect 的 ID 令牌。 如果 web 应用程序还需要标记来调用 web API，则可以使用 `code+id_token`。 |
| scope | 是 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 对于 web 应用程序，`offline_access` 范围是可选的。 它表示你的应用程序将需要一个*刷新令牌*来扩展对资源的访问。 |
| prompt | No | 需要的用户交互类型。 此时唯一有效的值为 `login`，这会强制用户在该请求上输入其凭据。 |
| redirect_uri | No | 应用程序的 `redirect_uri` 参数，应用程序可在其中发送和接收身份验证响应。 它必须与你在 Azure 门户中注册的某个 `redirect_uri` 参数完全匹配，但必须对其进行 URL 编码。 |
| response_mode | No | 用于将生成的授权代码发送回应用程序的方法。 这可以是 `query`、`form_post` 或 `fragment`。  建议使用 `form_post` 响应模式以获得最佳安全性。 |
| state | No | 请求中包含的值，也会在令牌响应中返回。 它可以是用户想要的任何内容的字符串。 随机生成的唯一值通常用于防止跨站点请求伪造攻击。 状态还用于在身份验证请求出现之前，在应用程序中编码有关用户状态的信息，例如它们所在的页。 |

此时，系统会要求用户完成工作流。 用户可能必须输入其用户名和密码、使用社交标识登录或注册目录。 可能有任何其他数量的步骤，具体取决于如何定义用户流。

用户完成用户流后，将使用 `response_mode` 参数中指定的方法，将响应返回到应用程序中的指定 `redirect_uri` 参数。 对于上述每种情况，响应都是相同的，与用户流无关。

使用 `response_mode=fragment` 的成功的响应如下所示：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 描述 |
| --------- | ----------- |
| id_token | 应用程序请求的 ID 令牌。 可以使用 ID 令牌验证用户的身份，并开始与用户的会话。 |
| 代码 | 如果使用 `response_type=code+id_token`，则为应用程序请求的授权代码。 应用程序可以使用授权代码请求目标资源的访问令牌。 授权代码通常在大约10分钟后过期。 |
| state | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序应该验证请求和响应中的 `state` 值是否相同。 |

也可以将错误响应发送到 `redirect_uri` 参数，使应用程序可以适当地处理它们：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 描述 |
| --------- | ----------- |
| error | 可用于分类发生的错误类型的代码。 |
| error_description | 可帮助识别身份验证错误根本原因的特定错误消息。 |
| state | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序应该验证请求和响应中的 `state` 值是否相同。 |

## <a name="validate-the-id-token"></a>验证 ID 令牌

仅收到一个 ID 令牌并不表示可以对用户进行身份验证。 验证 ID 令牌的签名，并根据应用程序的要求验证令牌中的声明。 Azure AD B2C 使用 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。 有许多开放源代码库可用于验证 JWT，具体取决于首选语言。 我们建议使用这些库，而不是实施自己的验证逻辑。

Azure AD B2C 具有 OpenID Connect 元数据终结点，该终结点允许应用程序在运行时获取有关 Azure AD B2C 的信息。 此信息包括终结点、令牌内容和令牌签名密钥。 B2C 租户中的每个用户流都有一个 JSON 元数据文档。 例如，`fabrikamb2c.onmicrosoft.com` 中 `b2c_1_sign_in` 用户流的元数据文档位于：

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

此配置文档的一个属性为 `jwks_uri`，对于相同用户流，该属性的值为：

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

若要确定用于对 ID 令牌进行签名的用户流（以及从何处获取元数据），可以使用两个选项。 第一种方法，用户流名称包含在 ID 令牌的 `acr` 声明中。 另一个方法是在发出请求时在 `state` 参数的值中对用户流进行编码，然后对其进行解码以确定使用的用户流。 任意一种方法均有效。

从 OpenID Connect 元数据终结点获取元数据文档后，可以使用 RSA 256 公钥验证 ID 令牌的签名。 此终结点上可能列出了多个密钥，每个密钥都由 `kid` 声明标识。 ID 令牌的标头还包含 `kid` 声明，该声明指示哪个密钥用于对 ID 令牌进行签名。

若要验证令牌 Azure AD B2C，需要使用指数（e）和取模（n）生成公钥。 你需要根据相应的编程语言来确定如何执行此操作。 可在此处找到有关通过 RSA 协议生成公钥的官方文档： https://tools.ietf.org/html/rfc3447#section-3.1

验证 ID 令牌的签名后，还有几项声明需要验证。 对于实例：

- 验证 `nonce` 声明以防止令牌重放攻击。 其值应为在登录请求中指定的内容。
- 验证 `aud` 声明，以确保为你的应用程序颁发了 ID 令牌。 其值应为应用程序的应用程序 ID。
- 验证 `iat` 和 `exp` 声明，确保 ID 令牌未过期。

此外，还需要执行更多的一些验证。 [OpenID Connect Core 规范](https://openid.net/specs/openid-connect-core-1_0.html)中详细介绍了验证。你可能还需要根据你的方案验证其他声明。 一些常见的验证包括：

- 确保用户/组织已注册了应用程序。
- 确保用户拥有正确的授权/权限。
- 确保执行了一定强度的身份验证，例如 Azure 多重身份验证。

验证 ID 令牌后，可以开始与用户的会话。 你可以使用 ID 令牌中的声明来获取有关应用程序中的用户的信息。 此信息的用途包括显示、记录和授权。

## <a name="get-a-token"></a>获取令牌

如果你需要 web 应用程序才能运行用户流，则可以跳过下面几节。 这些部分仅适用于需要对 web API 进行经过身份验证的调用的 web 应用程序，并且还受 Azure AD B2C 保护。

通过将 `POST` 请求发送到 `/token` 终结点，可以将获取的授权代码（通过 `response_type=code+id_token` 获取）兑换为所需资源的令牌。 在 Azure AD B2C 中，可以通过在请求中指定其作用域，照常[请求其他 api 的访问令牌](active-directory-b2c-access-tokens.md#request-a-token)。

你还可以通过使用应用的客户端 ID 作为请求的作用域来为你的应用程序的后端 Web API 请求访问令牌（这会导致使用该客户端 ID 的访问令牌为 "受众"）：

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 参数 | 需要 | 描述 |
| --------- | -------- | ----------- |
| 组织 | 是 | Azure AD B2C 租户的名称 |
| 政策 | 是 | 用于获取授权代码的用户流。 不能在此请求中使用其他用户流。 将此参数添加到查询字符串中，而不是添加到 POST 正文。 |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| client_secret | 是，在 Web 应用中 | 在[Azure 门户](https://portal.azure.com/)中生成的应用程序机密。 在此流中，客户端密码用于 Web 应用方案，在这些方案中，客户端可以安全地存储客户端机密。 对于本机应用（公共客户端）方案，不能安全地存储客户端机密，threfore 不会在此流中使用。 如果使用客户端机密，请定期更改。 |
| 代码 | 是 | 用户流开始时获取的授权代码。 |
| grant_type | 是 | 授予类型，该类型必须是授权代码流的 `authorization_code`。 |
| redirect_uri | 是 | 在其中收到授权代码的应用程序的 `redirect_uri` 参数。 |
| scope | No | 范围的空格分隔列表。 `openid` 作用域表示允许使用 id_token 参数的形式使用户登录并获取有关用户的数据。 它可用于获取应用程序自己的后端 web API 的标记，它由与客户端相同的应用程序 ID 表示。 `offline_access` 作用域表示应用程序需要刷新令牌才能对资源进行扩展访问。 |

成功的令牌响应如下所示：

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 参数 | 描述 |
| --------- | ----------- |
| not_before | epoch 时间中令牌被视为有效的时间。 |
| token_type | 令牌类型值。 `Bearer` 是唯一受支持的类型。 |
| access_token | 请求的已签名的 JWT 令牌。 |
| scope | 令牌的有效范围。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌在当前令牌过期后获取其他令牌。 刷新令牌可用于长时间保留对资源的访问权限。 必须在授权和令牌请求中都使用了范围 `offline_access`，才能接收刷新令牌。 |

错误响应如下所示：

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 描述 |
| --------- | ----------- |
| error | 可用于对发生的错误类型进行分类的代码。 |
| error_description | 可帮助识别身份验证错误根本原因的消息。 |

## <a name="use-the-token"></a>使用令牌

现在，已成功获取访问令牌，可通过在 `Authorization` 标头中包含令牌，在后端 Web API 请求中使用令牌：

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>刷新令牌

ID 令牌会在短时间内过期。 在令牌过期后刷新，以继续访问资源。 可以通过向 `/token` 终结点提交另一个 `POST` 请求来刷新令牌。 此时，提供 `refresh_token` 参数而不是 `code` 参数：

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 参数 | 需要 | 描述 |
| --------- | -------- | ----------- |
| 组织 | 是 | Azure AD B2C 租户的名称 |
| 政策 | 是 | 用于获取原始刷新令牌的用户流。 不能在此请求中使用其他用户流。 将此参数添加到查询字符串中，而不是添加到 POST 正文。 |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| client_secret | 是，在 Web 应用中 | 在[Azure 门户](https://portal.azure.com/)中生成的应用程序机密。 在此流中，客户端密码用于 Web 应用方案，在这些方案中，客户端可以安全地存储客户端机密。 对于本机应用（公共客户端）方案，不能安全地存储客户端机密，threfore 不能用于此调用。 如果使用客户端机密，请定期更改。 |
| grant_type | 是 | 授权的类型，该类型必须是授权代码流的此部分的刷新令牌。 |
| refresh_token | 是 | 流的第二部分中获取的原始刷新令牌。 必须在授权和令牌请求中都使用 `offline_access` 作用域，才能接收刷新令牌。 |
| redirect_uri | No | 在其中收到授权代码的应用程序的 `redirect_uri` 参数。 |
| scope | No | 范围的空格分隔列表。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 它可用于将令牌发送到应用程序的后端 web API，该 API 由与客户端相同的应用程序 ID 表示。 `offline_access` 作用域表示应用程序需要刷新令牌才能对资源进行扩展访问。 |

成功的令牌响应如下所示：

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 参数 | 描述 |
| --------- | ----------- |
| not_before | epoch 时间中令牌被视为有效的时间。 |
| token_type | 令牌类型值。 `Bearer` 是唯一受支持的类型。 |
| access_token | 请求的已签名 JWT 标记。 |
| scope | 令牌的有效范围。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌在当前令牌过期后获取其他令牌。 刷新令牌可用于长时间保留对资源的访问权限。 |

错误响应如下所示：

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 描述 |
| --------- | ----------- |
| error | 可用于对发生的错误类型进行分类的代码。 |
| error_description | 可帮助识别身份验证错误根本原因的消息。 |

## <a name="send-a-sign-out-request"></a>发送注销请求

如果要从应用程序中注销用户，则不能清除应用程序的 cookie 或结束与用户的会话。 将用户重定向到 Azure AD B2C 以注销。如果无法执行此操作，用户可以重新向应用程序进行身份验证，而无需再次输入其凭据。

若要注销用户，请将用户重定向到前面所述的 OpenID Connect 元数据文档中列出的 `end_session` 终结点：

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 参数 | 需要 | 描述 |
| --------- | -------- | ----------- |
| 组织 | 是 | Azure AD B2C 租户的名称 |
| 政策 | 是 | 想要用于从应用程序中注销用户的用户流。 |
| id_token_hint| No | 以前颁发的 ID 令牌，作为有关最终用户当前通过身份验证的会话与客户端的提示传递到注销终结点。 `id_token_hint` 确保 `post_logout_redirect_uri` 是 Azure AD B2C 应用程序设置中的已注册答复 URL。 |
| post_logout_redirect_uri | No | 用户在成功注销后应重定向到的 URL。如果未包含，Azure AD B2C 向用户显示一般消息。 除非提供 `id_token_hint`，否则不应将此 URL 注册为 Azure AD B2C 应用程序设置中的答复 URL。 |
| state | No | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序应该验证请求和响应中的 `state` 值是否相同。 |

### <a name="secure-your-logout-redirect"></a>保护注销重定向

注销后，用户将被重定向到 `post_logout_redirect_uri` 参数中指定的 URI，而不考虑已为应用程序指定的回复 Url。 但是，如果传递了有效的 `id_token_hint`，Azure AD B2C 将在执行重定向之前，验证 `post_logout_redirect_uri` 的值是否与应用程序的配置重定向 Uri 之一匹配。 如果没有为应用程序配置匹配的答复 URL，将显示一条错误消息，并且不会重定向用户。

### <a name="external-identity-provider-sign-out"></a>外部标识提供者注销

将用户定向到 `end_session` 终结点会清除用户 Azure AD B2C 的部分用户单一登录状态，但不会将用户从其社交标识提供者（IDP）会话中注销。 如果用户在后续登录中选择相同的 IDP，则会重新进行身份验证，而无需输入其凭据。 如果用户想要从应用程序中注销，则不一定意味着他们要注销其 Facebook 帐户。 但是，如果使用了本地帐户，用户的会话便会正确结束。
