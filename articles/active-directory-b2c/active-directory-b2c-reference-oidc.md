---
title: 使用 OpenID Connect 进行 Web 登录 - Azure Active Directory B2C | Microsoft Docs
description: 构建 web 应用程序在 Azure Active Directory B2C 中使用的 OpenID Connect 身份验证协议。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6285a90a9dca305f3a9cd909af6c084c747daf99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317116"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用 OpenID Connect 进行 Web 登录

OpenID Connect 是构建在 OAuth 2.0 基础之上的身份验证协议，可用于将用户安全登录到 Web 应用程序。 通过使用 OpenID Connect 的 Azure Active Directory B2C (Azure AD B2C) 实现，可以将 Web 应用程序中的注册、登录和其他标识管理体验转移到 Azure Active Directory (Azure AD) 中。 本指南演示如何使用与语言无关的方式执行此操作。 介绍在不使用我们的任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 扩展了 OAuth 2.0 *授权*协议，将其用作*身份验证*协议。 此身份验证协议，可执行单一登录。 它引入的概念*ID 令牌*，这允许客户端验证用户的标识，并获取有关用户的基本配置文件信息。

由于它扩展了 OAuth 2.0，它还使应用程序安全地获取*访问令牌*。 可以使用 access_token 访问由[授权服务器](active-directory-b2c-reference-protocols.md)保护的资源。 建议使用 OpenID Connect 如果您正在构建的具有托管的服务器上并通过浏览器访问的 web 应用程序。 如果你想要添加到您的移动设备标识管理或使用 Azure AD B2C 的桌面应用程序，应使用[OAuth 2.0](active-directory-b2c-reference-oauth-code.md)而不是 OpenID Connect。 有关令牌的详细信息，请参阅[的令牌在 Azure Active Directory B2C 概述](active-directory-b2c-reference-tokens.md)

Azure AD B2C 扩展了标准 OpenID Connect 协议，使其功能远远超出了简单的身份验证和授权。 它引入了[用户流参数](active-directory-b2c-reference-policies.md)，这样您就可以使用 OpenID Connect 将用户添加体验添加到您的应用程序，如注册、 登录和配置文件管理。

## <a name="send-authentication-requests"></a>发送身份验证请求

当 web 应用程序需要对用户进行身份验证并运行用户流时，它可以将用户定向到`/authorize`终结点。 用户可以采取操作，具体取决于用户流。

在此请求中，客户端指示必须获取从中的用户的权限`scope`参数，用户流运行`p`参数。 以下部分中提供了三个示例（带换行符以便阅读），每个示例使用不同的用户流。 要了解每个请求的工作原理，请尝试将请求粘贴到浏览器并运行它。 您可以替换`fabrikamb2c`与租户如果有一个并且已创建用户流的名称。

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

| 参数 | 需要 | 描述 |
| --------- | -------- | ----------- |
| client_id | 是 | 应用程序 ID [Azure 门户](https://portal.azure.com/)分配给你的应用程序。 |
| response_type | 是 | 必须包括用于 OpenID Connect ID 令牌。 如果 web 应用程序还需要使用令牌来调用 web API，则可以使用`code+id_token`。 |
| redirect_uri | 否 | `redirect_uri`参数的应用程序中，可以发送和接收的应用程序身份验证响应。 它必须完全匹配的一个`redirect_uri`，但它必须编码的 URL 在 Azure 门户中注册的参数。 |
| 作用域 | 是 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 `offline_access`作用域取决于您的 web 应用程序。 它表示您的应用程序需要*刷新令牌*扩展访问资源。 |
| response_mode | 否 | 用于将生成的授权代码发送回您的应用程序的方法。 这可以是 `query`、`form_post` 或 `fragment`。  建议使用 `form_post` 响应模式以获得最佳安全性。 |
| state | 否 | 也会在令牌响应中返回在请求中包含的值。 它可以是用户想要的任何内容的字符串。 随机生成的唯一值通常用于防止跨站点请求伪造攻击。 状态也用于之前发生身份验证请求，例如，它们之前所在的页面编码应用程序中的用户的状态信息。 |
| nonce | 是 | （由应用程序生成） 中生成的 ID 令牌以声明方式包含在请求中包含的值。 然后，应用程序可以验证此值以缓解令牌重放攻击。 此值通常是随机的唯一字符串，可用以识别请求的来源。 |
| p | 是 | 用户流运行时。 它是在 Azure AD B2C 租户中创建用户流的名称。 用户流的名称应以开头`b2c\_1\_`。 |
| prompt | 否 | 需要的用户交互类型。 此时唯一有效的值为 `login`，这会强制用户在该请求上输入其凭据。 |

此时，要求用户完成该工作流。 用户可能必须输入其用户名和密码，注册使用社交标识或登录的目录。 可能有任何其他若干步骤，具体取决于如何定义用户流。

用户完成用户流后，将响应返回到位于所指示的应用程序`redirect_uri`参数，通过使用中指定的方法`response_mode`参数。 响应是为每个前面的情况下，独立于用户流相同。

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
| 代码 | 应用程序请求，如果您使用的授权代码`response_type=code+id_token`。 应用程序可以使用授权代码请求目标资源的访问令牌。 授权代码通常在约 10 分钟后过期。 |
| state | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序应验证`state`请求和响应中的值相等。 |

错误响应还可以发送到`redirect_uri`参数，以便应用程序可以适当地处理：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 描述 |
| --------- | ----------- |
| error | 可用于分类发生的错误的类型代码。 |
| error_description | 特定错误消息，可以帮助标识身份验证错误的根本原因。 |
| state | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序应验证`state`请求和响应中的值相等。 |

## <a name="validate-the-id-token"></a>验证 ID 令牌

仅收到一个 ID 令牌并不表示可以对用户进行身份验证。 验证 ID 令牌的签名并验证每个应用程序的要求在令牌中的声明。 Azure AD B2C 使用 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。 有许多开放源代码库可用于验证 JWT，具体取决于首选语言。 我们建议使用这些库，而不是实施自己的验证逻辑。 

Azure AD B2C 具有 OpenID Connect 元数据终结点，它允许应用程序以获取有关 Azure AD B2C 在运行时的信息。 此信息包括终结点、令牌内容和令牌签名密钥。 B2C 租户中的每个用户流都有一个 JSON 元数据文档。 例如，`fabrikamb2c.onmicrosoft.com` 中 `b2c_1_sign_in` 用户流的元数据文档位于：

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此配置文档的一个属性为 `jwks_uri`，对于相同用户流，该属性的值为：

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`。

若要确定哪些用户流签名所使用的 ID 令牌 （以及从何处获取元数据），您具有两个选项。 第一种方法，用户流名称包含在 ID 令牌的 `acr` 声明中。 另一个方法是在发出请求时在 `state` 参数的值中对用户流进行编码，然后对其进行解码以确定使用的用户流。 任意一种方法均有效。

获取从 OpenID Connect 元数据终结点的元数据文档后，可以使用 RSA 256 公钥来验证 ID 令牌的签名。 可能有多个键列在此终结点，每个由标识`kid`声明。 ID 令牌的标头还包含 `kid` 声明，该声明指示哪个密钥用于对 ID 令牌进行签名。

验证 ID 令牌的签名后，还有几项声明需要验证。 例如：

- 验证 `nonce` 声明以防止令牌重放攻击。 其值应为在登录请求中指定的内容。
- 验证`aud`声明以确保 ID 令牌已为你的应用程序发出。 其值应为你的应用程序的应用程序 ID。
- 验证`iat`和`exp`声明以确保 ID 令牌未过期。

此外，还需要执行更多的一些验证。 中详细描述了验证[OpenID Connect 核心规范](https://openid.net/specs/openid-connect-core-1_0.html)。根据情况，可能还希望验证其他声明。 一些常见的验证包括：

- 确保用户/组织已注册的应用程序。
- 确保用户拥有正确的授权/权限。
- 确保执行了一定强度的身份验证，例如 Azure 多重身份验证。

验证 ID 令牌后，可以开始与用户会话。 可以使用 ID 令牌中的声明来在应用程序中获取有关用户的信息。 此信息的用途包括显示、记录和授权。

## <a name="get-a-token"></a>获取令牌

如果你需要将 web 应用程序仅运行用户流，则可以跳过下面几节。 它们是仅适用于 web 的应用程序需要进行身份验证对 web API 的调用，并还受 Azure AD B2C。

通过将 `POST` 请求发送到 `/token` 终结点，可以将获取的授权代码（通过 `response_type=code+id_token` 获取）兑换为所需资源的令牌。 目前，你可以为其请求令牌的唯一资源是应用程序自身的后端 web API。 向自己请求令牌的约定是使用应用程序的客户端 ID 作为范围：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| 参数 | 需要 | 描述 |
| --------- | -------- | ----------- |
| p | 是 | 用于获取授权代码的用户流。 在此请求中，不能使用不同的用户流。 添加此参数的查询字符串，而不 POST 正文。 |
| client_id | 是 | 应用程序 ID [Azure 门户](https://portal.azure.com/)分配给你的应用程序。 |
| grant_type | 是 | 授予类型，该类型必须是授权代码流的 `authorization_code`。 |
| 作用域 | 否 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 id_token 参数的形式使用户登录并获取有关用户的数据。 它可以用于获取应用程序自身的后端 web API，由客户端相同的应用程序 ID 表示的令牌。 `offline_access`作用域表示应用程序需要扩展访问资源刷新令牌。 |
| 代码 | 是 | 在用户流的开头获取授权代码。 |
| redirect_uri | 是 | 在其中收到授权代码的应用程序的 `redirect_uri` 参数。 |
| client_secret | 是 | 在生成的应用程序机密[Azure 门户](https://portal.azure.com/)。 此应用程序密码是重要的安全项目。 应将其安全地存储在服务器上。 更改定期更新此客户端密码。 |

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
| 作用域 | 令牌的有效范围。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌在当前令牌过期之后获取其他令牌。 刷新令牌可用于更长时间保留资源访问权限。 作用域`offline_access`必须已使用授权和令牌请求中才能接收刷新令牌。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 描述 |
| --------- | ----------- |
| error | 可用于分类发生的错误的类型代码。 |
| error_description | 一条消息，可帮助识别身份验证错误的根本原因。 |

## <a name="use-the-token"></a>使用令牌

现在你已成功获取访问令牌，可通过在 `Authorization` 标头中加入令牌的方式，在后端 Web API 请求中使用该令牌：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>刷新令牌

ID 令牌在一小段时间后过期。 若要继续能够访问的资源其过期后刷新令牌。 可以通过提交另一个刷新令牌`POST`请求到`/token`终结点。 此时，提供 `refresh_token` 参数而不是 `code` 参数：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| 参数 | 需要 | 描述 |
| --------- | -------- | ----------- |
| p | 是 | 用于获取原始刷新令牌的用户流。 在此请求中，不能使用不同的用户流。 添加此参数的查询字符串，而不 POST 正文。 |
| client_id | 是 | 应用程序 ID [Azure 门户](https://portal.azure.com/)分配给你的应用程序。 |
| grant_type | 是 | 授予，它必须是授权代码流的此部分的刷新令牌的类型。 |
| 作用域 | 否 | 范围的空格分隔列表。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 它可以用于将令牌发送到应用程序自身的后端 web API，由客户端相同的应用程序 ID 表示。 `offline_access`作用域表示应用程序需要扩展访问资源刷新令牌。 |
| redirect_uri | 否 | 在其中收到授权代码的应用程序的 `redirect_uri` 参数。 |
| refresh_token | 是 | 流的第二部分中获取原始刷新令牌。 `offline_access`作用域必须使用授权和令牌请求中才能接收刷新令牌。 |
| client_secret | 是 | 在生成的应用程序机密[Azure 门户](https://portal.azure.com/)。 此应用程序密码是重要的安全项目。 应将其安全地存储在服务器上。 更改定期更新此客户端密码。 |

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
| access_token | 请求已签名的 JWT 令牌。 |
| 作用域 | 令牌的有效范围。 |
| expires_in | 访问令牌有效的时间长度（以秒为单位）。 |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌在当前令牌过期之后获取其他令牌。 刷新令牌可用于更长时间保留资源访问权限。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 描述 |
| --------- | ----------- |
| error | 可用于分类发生的错误的类型代码。 |
| error_description | 一条消息，可帮助识别身份验证错误的根本原因。 |

## <a name="send-a-sign-out-request"></a>发送注销请求

当你想要从应用程序中注销用户时，它并不足以清除应用程序的 cookie 或结束与用户会话。 将用户重定向到 Azure AD B2C 进行注销。如果无法实现此目的，用户可能可以进行身份验证，你的应用程序而无需再次输入其凭据。

您可以只需将用户重定向到`end_session`前面所述的 OpenID Connect 元数据文档中列出的终结点：

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 参数 | 需要 | 描述 |
| --------- | -------- | ----------- |
| p | 是 | 想要用于从应用程序中注销用户的用户流。 |
| post_logout_redirect_uri | 否 | 用户成功注销后应会重定向到 URL。如果它不包含，Azure AD B2C 向用户显示一条常规消息。 |

将定向到用户`end_session`终结点中清除的某些用户的单一登录状态与 Azure AD B2C，但它不会将用户从其社交标识提供者 (IDP) 会话注销。 如果用户在后续登录中选择的 IDP，它们将重新进行身份验证，而无需输入其凭据。 如果用户想要注销应用程序，并不一定意味着他们想要注销其 Facebook 帐户。 但是，如果使用本地帐户，则用户的会话将正常结束。

