---
title: 通过 OpenID Connect 进行 Web 登录-Azure Active Directory B2C
description: 使用 Azure Active Directory B2C 中的 OpenID Connect 身份验证协议生成 Web 应用程序。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: f6188f5c5bdd256ee84c5e7dc8632e5c067ceca5
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541720"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 OpenID Connect 进行 Web 登录

OpenID Connect 是构建在 OAuth 2.0 基础之上的身份验证协议，可用于将用户安全登录到 Web 应用程序。 通过使用 OpenID Connect 的 Azure Active Directory B2C (Azure AD B2C) 实现，可以将 Web 应用程序中的注册、登录和其他标识管理体验转移到 Azure Active Directory (Azure AD) 中。 本指南演示如何使用与语言无关的方式执行此操作。 介绍在不使用我们的任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 扩展了 OAuth 2.0 *授权*协议，将其用作*身份验证*协议。 使用此身份验证协议可以执行单一登录。 它引入了 ID 令牌的概念，可让客户端验证用户的标识，并获取有关用户的基本配置文件信息。

因为 OpenID Connect 扩展了 OAuth 2.0，因此，它还能使应用程序安全地获取访问令牌。 可以使用 access_token 访问由[授权服务器](active-directory-b2c-reference-protocols.md)保护的资源。 如果要生成的 Web 应用程序托管在服务器中并通过浏览器访问，我们建议使用 OpenID Connect。 如果要使用 Azure AD B2C 向移动或桌面应用程序添加标识管理，则应使用 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md)，而不是 OpenID Connect。 有关令牌的详细信息，请参阅 [Azure Active Directory B2C 中的令牌概述](active-directory-b2c-reference-tokens.md)

Azure AD B2C 扩展了标准 OpenID Connect 协议，使其功能远远超出了简单的身份验证和授权。 它引入了[用户流参数](active-directory-b2c-reference-policies.md)，可让你使用 OpenID Connect 向应用程序添加注册、登录和配置文件管理等用户体验。

## <a name="send-authentication-requests"></a>发送身份验证请求

当 Web 应用程序需要对用户进行身份验证并运行用户流时，它可以将用户定向到 `/authorize` 终结点。 用户可以根据用户流执行操作。

在此请求中，客户端指示在 `scope` 参数中需要从用户获取的权限以及要在 `p` 参数中运行的用户流。 以下部分中提供了三个示例（带换行符以便阅读），每个示例使用不同的用户流。 要了解每个请求的工作原理，请尝试将请求粘贴到浏览器并运行它。 如果你有一个租户并已创建了用户流，则可将 `fabrikamb2c` 替换为该租户的名称。 还需要替换`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`。 将此客户端 ID 替换为你创建的应用程序注册的应用程序 ID。 还将策略名称`b2c_1_sign_in`更改为你在租户中拥有的策略名称。

#### <a name="use-a-sign-in-user-flow"></a>使用登录用户流
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>使用注册用户流
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>使用编辑配置文件用户流
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 参数 | 必填 | 描述 |
| --------- | -------- | ----------- |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| nonce | 是 | 由应用程序生成且包含在请求中的值，以声明方式包含在生成的 ID 令牌中。 应用程序接着便可确认此值，以减少令牌重新执行攻击。 此值通常是随机的唯一字符串，可用以识别请求的来源。 |
| p | 是 | 运行的用户流。 它是在 Azure AD B2C 租户中创建的用户流的名称。 用户流的名称应以 `b2c\_1\_` 开头。 |
| response_type | 是 | 必须包含 OpenID Connect 的 ID 令牌。 如果 Web 应用程序还需要使用令牌来调用 Web API，则可以使用 `code+id_token`。 |
| 范围 | 是 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 `offline_access` 范围对 Web 应用程序是可选的。 它表示应用程序需要使用刷新令牌来长期访问资源。 |
| prompt | 否 | 需要的用户交互类型。 此时唯一有效的值为 `login`，这会强制用户在该请求上输入其凭据。 |
| redirect_uri | 否 | 应用程序的 `redirect_uri` 参数，应用程序可在此发送及接收身份验证响应。 它必须完全匹配在 Azure 门户中注册的其中一个 `redirect_uri` 参数，但必须经过 URL 编码。 |
| response_mode | 否 | 将生成的授权代码发回到应用程序所用的方法。 这可以是 `query`、`form_post` 或 `fragment`。  建议使用 `form_post` 响应模式以获得最佳安全性。 |
| 省/自治区/直辖市 | 否 | 同时随令牌响应返回的请求中所包含的值。 它可以是用户想要的任何内容的字符串。 随机生成的唯一值通常用于防止跨站点请求伪造攻击。 该状态也用于在身份验证请求出现之前，在应用程序中编码用户的状态信息，例如用户之前所在的页面。 |

此时，要求用户完成工作流。 用户可能需要输入其用户名和密码、用社交标识登录，或注册目录。 可能还可任何其他若干步骤，具体取决于如何定义用户流。

用户完成用户流后，会使用 `response_mode` 参数中指定的方法，将响应返回到 `redirect_uri` 参数中指定的应用程序。 对于上述每种情况，响应均相同，而与用户流无关。

使用 `response_mode=fragment` 的成功的响应如下所示：

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 描述 |
| --------- | ----------- |
| id_token | 应用程序请求的 ID 令牌。 可以使用 ID 令牌验证用户的身份，并开始与用户的会话。 |
| code | 如果使用了 `response_type=code+id_token`，则为应用程序请求的授权代码。 应用程序可以使用该授权代码请求目标资源的访问令牌。 授权代码通常在约 10 分钟后即会过期。 |
| 省/自治区/直辖市 | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序需验证请求和响应中的 `state` 值是否相同。 |

错误响应也可能会被发送到 `redirect_uri` 参数，以便应用程序对它们进行恰当的处理：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 描述 |
| --------- | ----------- |
| 错误 | 一个代码，可用于对发生的错误类型进行分类。 |
| error_description | 帮助识别身份验证错误根本原因的特定错误消息。 |
| 省/自治区/直辖市 | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序需验证请求和响应中的 `state` 值是否相同。 |

## <a name="validate-the-id-token"></a>验证 ID 令牌

仅收到一个 ID 令牌并不表示可以对用户进行身份验证。 根据应用程序的要求验证 ID 令牌的签名和令牌中的声明。 Azure AD B2C 使用 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。 有许多开放源代码库可用于验证 JWT，具体取决于首选语言。 我们建议使用这些库，而不是实施自己的验证逻辑。

Azure AD B2C 具有 OpenID Connect 元数据终结点，允许应用程序在运行时获取有关 Azure AD B2C 的信息。 此信息包括终结点、令牌内容和令牌签名密钥。 B2C 租户中的每个用户流都有一个 JSON 元数据文档。 例如，`fabrikamb2c.onmicrosoft.com` 中 `b2c_1_sign_in` 用户流的元数据文档位于：

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此配置文档的一个属性为 `jwks_uri`，对于相同用户流，该属性的值为：

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`。

若要确定对 ID 令牌进行签名所使用的用户流（以及从何处获取元数据），可以使用两种方法。 第一种方法，用户流名称包含在 ID 令牌的 `acr` 声明中。 另一个方法是在发出请求时在 `state` 参数的值中对用户流进行编码，然后对其进行解码以确定使用的用户流。 任意一种方法均有效。

从 OpenID Connect 元数据终结点获取元数据文档后，可以使用 RSA-256 公钥来验证 ID 令牌的签名。 此终结点上可能列出多个密钥，每个密钥使用 `kid` 声明进行标识。 ID 令牌的标头还包含 `kid` 声明，该声明指示哪个密钥用于对 ID 令牌进行签名。

若要验证令牌 Azure AD B2C, 需要使用指数 (e) 和取模 (n) 生成公钥。 你需要根据相应的编程语言来确定如何执行此操作。 可在此处找到有关通过 RSA 协议生成公钥的官方文档: https://tools.ietf.org/html/rfc3447#section-3.1

验证 ID 令牌的签名后，还有几项声明需要验证。 例如：

- 验证 `nonce` 声明以防止令牌重放攻击。 其值应为在登录请求中指定的内容。
- 验证 `aud` 声明以确保已为应用程序颁发 ID 令牌。 其值应为应用程序的 ID。
- 验证 `iat` 和 `exp` 声明以确保 ID 令牌未过期。

此外，还需要执行更多的一些验证。 [OpenID Connect 核心规范](https://openid.net/specs/openid-connect-core-1_0.html)中详细介绍了验证。根据情况，可能还希望验证其他声明。 一些常见的验证包括：

- 确保用户/组织已注册应用程序。
- 确保用户拥有正确的授权/权限。
- 确保执行了一定强度的身份验证，例如 Azure 多重身份验证。

验证 ID 令牌后，可以开始与用户的会话。 在应用程序中，可以使用 ID 令牌中的声明来获取用户的相关信息。 此信息的用途包括显示、记录和授权。

## <a name="get-a-token"></a>获取令牌

如果仅需要 Web 应用程序运行用户流，则可以跳过下面几个部分。 这些部分仅适用于需要对 Web API 进行验证的调用，以及受到 Azure AD B2C 保护的 Web 应用程序。

通过将 `POST` 请求发送到 `/token` 终结点，可以将获取的授权代码（通过 `response_type=code+id_token` 获取）兑换为所需资源的令牌。 在 Azure AD B2C 中, 可以通过在请求中指定其作用域, 照常[请求其他 api 的访问令牌](active-directory-b2c-access-tokens.md#request-a-token)。

还可以按照将应用的客户端 ID 用作所请求范围（这将导致具有该客户端 ID 的访问令牌作为“受众”）的约定，为应用自己的后端 Web API 请求访问令牌：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| 参数 | 必填 | 描述 |
| --------- | -------- | ----------- |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| client_secret | 是 | 在 [Azure 门户](https://portal.azure.com/)中生成的应用程序机密。 此应用程序密码是重要的安全项目。 应将其安全地存储在服务器上。 请定期更新此客户端机密。 |
| code | 是 | 在用户流的开头获取的授权代码。 |
| grant_type | 是 | 授予类型，该类型必须是授权代码流的 `authorization_code`。 |
| p | 是 | 用于获取授权代码的用户流。 无法在此请求中使用不同的用户流。 将此参数添加到查询字符串中，而不是添加到 POST 正文中。 |
| redirect_uri | 是 | 在其中收到授权代码的应用程序的 `redirect_uri` 参数。 |
| 范围 | 否 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 id_token 参数的形式使用户登录并获取有关用户的数据。 它可以用于为应用程序的后端 Web API 获取令牌，该令牌使用和客户端相同的应用程序 ID 表示。 `offline_access` 范围表示应用程序需要使用刷新令牌来长期访问资源。 |

成功的令牌响应如下所示：

```
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
| token_type | 令牌类型值。 `Bearer` 是唯一支持的类型。 |
| access_token | 请求的已签名的 JWT 令牌。 |
| 范围 | 令牌的有效范围。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌，在当前令牌过期之后获取其他令牌。 刷新令牌可用于延长保留资源访问权限的时间。 必须在授权和令牌请求中使用范围 `offline_access`，才能接收刷新令牌。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 描述 |
| --------- | ----------- |
| 错误 | 一个代码，可用于对发生的错误类型进行分类。 |
| error_description | 帮助识别身份验证错误根本原因的消息。 |

## <a name="use-the-token"></a>使用令牌

现在你已成功获取访问令牌，可通过在 `Authorization` 标头中加入令牌的方式，在后端 Web API 请求中使用该令牌：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>刷新令牌

ID 令牌在短时间内即会过期。 在它们过期后，请刷新令牌以便能够继续访问资源。 可以通过向 `/token` 终结点提交另一个 `POST` 请求来刷新令牌。 此时，提供 `refresh_token` 参数而不是 `code` 参数：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| 参数 | 必填 | 描述 |
| --------- | -------- | ----------- |
| client_id | 是 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| client_secret | 是 | 在 [Azure 门户](https://portal.azure.com/)中生成的应用程序机密。 此应用程序密码是重要的安全项目。 应将其安全地存储在服务器上。 请定期更新此客户端机密。 |
| grant_type | 是 | 授予类型，必须是此授权代码流部分的刷新令牌。 |
| refresh_token | 是 | 在流的第二部分获取的原始刷新令牌。 必须在授权和令牌请求中使用范围 `offline_access`，才能接收刷新令牌。 |
| p | 是 | 用于获取原始刷新令牌的用户流。 无法在此请求中使用不同的用户流。 将此参数添加到查询字符串中，而不是添加到 POST 正文中。 |
| redirect_uri | 否 | 在其中收到授权代码的应用程序的 `redirect_uri` 参数。 |
| 范围 | 否 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 它可以用于向应用程序的后端 Web API 发送令牌，该令牌使用和客户端相同的应用程序 ID 表示。 `offline_access` 范围表示应用程序需要使用刷新令牌来长期访问资源。 |

成功的令牌响应如下所示：

```
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
| token_type | 令牌类型值。 `Bearer` 是唯一支持的类型。 |
| access_token | 请求的已签名 JWT 令牌。 |
| 范围 | 令牌的有效范围。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌，在当前令牌过期之后获取其他令牌。 刷新令牌可用于延长保留资源访问权限的时间。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 描述 |
| --------- | ----------- |
| 错误 | 一个代码，可用于对发生的错误类型进行分类。 |
| error_description | 帮助识别身份验证错误根本原因的消息。 |

## <a name="send-a-sign-out-request"></a>发送注销请求

如果想要从应用程序中注销用户，只是清除应用程序的 Cookie 或者结束与用户的会话是不够的。 需将用户重定向到 Azure AD B2C 进行注销。如果没有这么做，那么用户可能可以在应用程序中重新进行身份验证，且无需再次输入其凭据。

若要注销用户, 请将用户重定向到`end_session`前面所述的 OpenID connect 元数据文档中列出的终结点:

```
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 参数 | 必填 | 描述 |
| --------- | -------- | ----------- |
| 组织 | 是 | Azure AD B2C 租户的名称 |
| 政策 | 是 | 想要用于从应用程序中注销用户的用户流。 |
| id_token_hint| 否 | 以前颁发的 ID 令牌, 作为有关最终用户当前通过身份验证的会话与客户端的提示传递到注销终结点。 |
| post_logout_redirect_uri | 否 | 用户在成功注销后应重定向到的 URL。如果未包含此参数，Azure AD B2C 会向用户显示一条常规消息。 |
| 省/自治区/直辖市 | 否 | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序需验证请求和响应中的 `state` 值是否相同。 |

### <a name="require-id-token-hint-in-logout-request"></a>注销请求中需要 ID 令牌提示

注销后, 用户将被重定向到`post_logout_redirect_uri`参数中指定的 URI, 而不考虑已为应用程序指定的回复 url。 但是, 如果传递了`id_token_hint`有效的, 则 Azure AD B2C 在执行重定向`post_logout_redirect_uri`之前, 验证的值是否与应用程序的一个已配置重定向 uri 相匹配。 如果没有为应用程序配置匹配的答复 URL, 将显示一条错误消息, 并且不会重定向用户。

### <a name="external-identity-provider-session"></a>外部标识提供者会话

将用户定向到 `end_session` 终结点会清除用户的某些 Azure AD B2C 的单一登录状态，但是不会将用户从其社交标识提供者 (IDP) 会话中注销。 如果用户在后续登录中选择相同的 IDP, 则会重新进行身份验证, 而无需输入其凭据。 如果用户想要从应用程序中注销, 则不一定意味着他们要注销其 Facebook 帐户。 但是, 如果使用了本地帐户, 用户的会话便会正确结束。
