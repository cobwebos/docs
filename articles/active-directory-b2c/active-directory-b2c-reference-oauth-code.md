---
title: 授权代码流 - Azure AD B2C | Microsoft Docs
description: 了解如何使用 Azure AD B2C 和 OpenID Connect 身份验证协议生成 Web 应用。
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: d8ed5747f29f969535bbafc1624d9d02e54c8418
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C：OAuth 2.0 授权代码流
可使用 OAuth 2.0 授权代码向设备上安装的应用授权，获取访问受保护资源（例如 Web API）的权限。 通过使用 OAuth 2.0 的 Azure Active Directory B2C (Azure AD B2C) 实现，可向移动应用和桌面应用添加注册、登录和其他标识管理任务。 本文与语言无关。 本文介绍在不使用任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

<!-- TODO: Need link to libraries -->

[OAuth 2.0 规范第 4.1 部分](http://tools.ietf.org/html/rfc6749)描述了 OAuth 2.0 授权代码流。 可使用它在大部分应用类型（包括 [Web 应用](active-directory-b2c-apps.md#web-apps)和[本机安装应用](active-directory-b2c-apps.md#mobile-and-native-apps)）中执行身份验证与授权。 可使用 OAuth 2.0 授权代码流安全地获取应用的*访问令牌*，访问令牌可用于访问受到[授权服务器](active-directory-b2c-reference-protocols.md#the-basics)保护的资源。

本文重点介绍**公共客户端** OAuth 2.0 授权代码流。 公共客户端是那些不能被信任以安全维护机密密码完整性的任何客户端应用程序。 这包括移动应用、桌面应用，以及在设备上运行并需要获取访问令牌的几乎所有应用程序。 

> [!NOTE]
> 若要使用 Azure AD B2C 向 Web 应用添加标识管理，请使用 [OpenID Connect](active-directory-b2c-reference-oidc.md)，而不要使用 OAuth 2.0。

Azure AD B2C 扩展了标准 OAuth 2.0 流，使其功能远远超出了简单的身份验证和授权。 它引入了[策略参数](active-directory-b2c-reference-policies.md)。 借助内置策略，可使用 OAuth 2.0 向应用添加用户体验，例如注册、登录和配置文件管理。 本文演示如何使用 OAuth 2.0 和策略在本机应用程序中实现每个体验。 我们还将演示如何获取用于访问 Web API 的访问令牌。

在本文的示例 HTTP 请求中，我们使用示例 Azure AD B2C 目录 **fabrikamb2c.onmicrosoft.com**。我们还使用示例应用程序和策略。 可使用这些值自行尝试这些请求，或使用自己的值替换它们。
了解如何[获取自己的 Azure AD B2C 目录、应用程序和策略](#use-your-own-azure-ad-b2c-directory)。

## <a name="1-get-an-authorization-code"></a>1.获取授权代码
授权代码流始于客户端将用户定向到的 `/authorize` 终结点。 这是授权代码流中用户会执行操作的交互部分。 在此请求中，客户端在 `scope` 参数中指示其需要从用户处获取的权限。 在 `p` 参数中，它指示要执行的策略。 下面有三个示例（为了方便阅读而提供换行符），每个示例都使用不同的策略。

### <a name="use-a-sign-in-policy"></a>使用登录策略
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>使用注册策略
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>使用编辑配置文件策略
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| client_id |必选 |在 [Azure 门户](https://portal.azure.com)中分配给应用的应用程序 ID。 |
| response_type |必选 |响应类型，其中必须包括 `code` 的授权待码流。 |
| redirect_uri |必选 |应用的重定向 URI，应用可通过此 URI 发送和接收身份验证响应。 它必须完全匹配在门户中注册的其中一个重定向 URI，但必须经 URL 编码。 |
| 作用域 |必选 |范围的空格分隔列表。 一个范围值可向 Azure Active Directory (Azure AD) 指示正在请求的两个权限。 使用客户端 ID 作为范围表示，应用需要可对自己的服务或 Web API（由同一客户端 ID 表示）使用的访问令牌。  `offline_access` 范围表示应用需要刷新令牌才能获取对资源的长生存期访问权限。 还可使用 `openid` 范围从 Azure AD B2C 请求 ID 令牌。 |
| response_mode |建议 |用于将生成的授权代码发回应用的方法。 可以是 `query`、`form_post` 或 `fragment`。 |
| state |建议 |随令牌响应返回的请求中所包含的值。 它可以是想要使用的任何内容的字符串。 随机生成的唯一值通常用于防止跨网站请求伪造攻击。 它还可用于在身份验证请求发生前，对有关用户在应用中的状态的信息编码。 例如，用户所处的页面或要执行的策略。 |
| p |必选 |执行的策略。 它是在 Azure AD B2C 目录中创建的策略的名称。 策略名称值应以“**b2c\_1\_**”开头。 若要详细了解策略，请参阅 [Azure AD B2C 内置策略](active-directory-b2c-reference-policies.md)。 |
| prompt |可选 |需要的用户交互类型。 目前，唯一有效的值为 `login`，这会强制用户在该请求中输入其凭据。 单一登录不会生效。 |

此时，要求用户完成策略的工作流。 这可能涉及用户输入其用户名和密码、使用社交标识登录、注册目录，或任何其他步骤。 用户操作取决于策略是如何定义的。

用户完成策略后，Azure AD 会在你用于 `redirect_uri` 的值处将响应返回到应用。 它使用在 `response_mode` 参数中指定的方法。 对于每种用户操作情况，响应完全相同，与执行的策略无关。

使用 `response_mode=query` 的成功响应如下所示：

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 参数 | 说明 |
| --- | --- |
| 代码 |应用程序请求的授权代码。 应用可使用授权代码请求目标资源的访问令牌。 授权代码的生存期非常短。 通常，它们在约 10 分钟后过期。 |
| state |请参阅上一部分的表中的完整说明。 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用需验证请求和响应中的 `state` 值是否相同。 |

错误响应也可能发送到重定向 URI，让应用能够对其进行适当处理：

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 说明 |
| --- | --- |
| error |可用于分类发生的错误类型的错误代码字符串。 还可使用该字符串对错误作出响应。 |
| error_description |可帮助用户识别身份验证错误根本原因的特定错误消息。 |
| state |请参阅上表中的完整说明。 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用应该验证请求和响应中的 `state` 值是否相同。 |

## <a name="2-get-a-token"></a>2.获取令牌
现在，已获取授权代码，可将 POST 请求发送到 `/token` 终结点，兑换 `code` 来获取所需资源的令牌。 在 Azure AD B2C 中，可请求令牌的唯一资源是应用自己的后端 Web API。 用于向自己请求令牌的约定为，使用应用的客户端 ID 作为范围：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| p |必选 |用于获取授权代码的策略。 无法在此请求中使用不同的策略。 请注意，将此参数添加到*查询字符串*中，而不是添加到 POST 正文中。 |
| client_id |必选 |在 [Azure 门户](https://portal.azure.com)中分配给应用的应用程序 ID。 |
| grant_type |必选 |授权的类型。 对于授权代码流，授权类型必须为 `authorization_code`。 |
| 作用域 |建议 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 使用客户端 ID 作为范围表示，应用需要可对自己的服务或 Web API（由同一客户端 ID 表示）使用的访问令牌。  `offline_access` 范围表示应用需要刷新令牌才能获取对资源的长生存期访问权限。  还可使用 `openid` 范围从 Azure AD B2C 请求 ID 令牌。 |
| 代码 |必选 |在流的第一个阶段获取的授权代码。 |
| redirect_uri |必选 |在其中收到授权代码的应用程序的重定向 URI。 |

成功令牌响应如下所示：

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
| 参数 | 说明 |
| --- | --- |
| not_before |epoch 时间中令牌被视为有效的时间。 |
| token_type |令牌类型值。 Azure AD 唯一支持的类型是 Bearer。 |
| access_token |所请求的已签名 JSON Web 令牌 (JWT)。 |
| 作用域 |令牌的有效范围。 还可使用范围缓存令牌以备后用。 |
| expires_in |令牌有效的时间长度（以秒为单位）。 |
| refresh_token |OAuth 2.0 刷新令牌。 应用可以使用此令牌，在当前令牌过期之后获取其他令牌。 刷新令牌的生存期较长。 可使用它们长期保留对资源的访问权限。 有关详细信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 说明 |
| --- | --- |
| error |可用于分类发生的错误类型的错误代码字符串。 还可使用该字符串对错误作出响应。 |
| error_description |可帮助用户识别身份验证错误根本原因的特定错误消息。 |

## <a name="3-use-the-token"></a>3.使用令牌
现在，已成功获取访问令牌，可通过在 `Authorization` 标头中包含令牌，在后端 Web API 请求中使用令牌：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4.刷新令牌
访问令牌和 ID 令牌的生存期较短。 过期后，必须将其刷新才能继续访问资源。 若要执行此操作，请向 `/token` 终结点提交另一个 POST 请求。 这次提供的是 `refresh_token` 而不是 `code`：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| p |必选 |用于获取原始刷新令牌的策略。 无法在此请求中使用不同的策略。 请注意，将此参数添加到*查询字符串*中，而不是添加到 POST 正文中。 |
| client_id |必选 |在 [Azure 门户](https://portal.azure.com)中分配给应用的应用程序 ID。 |
| client_secret |必选 |在 [Azure 门户](https://portal.azure.com)中关联到 client_id 的 client_secret。 |
| grant_type |必选 |授权的类型。 对于授权代码流的此阶段，授权类型必须为 `refresh_token`。 |
| 作用域 |建议 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 使用客户端 ID 作为范围表示，应用需要可对自己的服务或 Web API（由同一客户端 ID 表示）使用的访问令牌。  `offline_access` 范围表示应用需要刷新令牌才能获取对资源的长生存期访问权限。  还可使用 `openid` 范围从 Azure AD B2C 请求 ID 令牌。 |
| redirect_uri |可选 |在其中收到授权代码的应用程序的重定向 URI。 |
| refresh_token |必选 |在授权代码流的第二个阶段获取的原始刷新令牌。 |

成功令牌响应如下所示：

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
| 参数 | 说明 |
| --- | --- |
| not_before |epoch 时间中令牌被视为有效的时间。 |
| token_type |令牌类型值。 Azure AD 唯一支持的类型是 Bearer。 |
| access_token |所请求的已签名 JWT。 |
| 作用域 |令牌的有效范围。 还可使用范围缓存令牌以备后用。 |
| expires_in |令牌有效的时间长度（以秒为单位）。 |
| refresh_token |OAuth 2.0 刷新令牌。 应用可以使用此令牌，在当前令牌过期之后获取其他令牌。 刷新令牌的生存期很长，而且可以用于延长保留资源访问权限的时间。 有关详细信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 说明 |
| --- | --- |
| error |可用于分类发生的错误类型的错误代码字符串。 还可使用该字符串对错误作出响应。 |
| error_description |可帮助用户识别身份验证错误根本原因的特定错误消息。 |

## <a name="use-your-own-azure-ad-b2c-directory"></a>使用自己的 Azure AD B2C 目录
若要自行尝试这些请求，请完成以下步骤。 使用自己的值替换本文中使用的示例值。

1. [创建 Azure AD B2C 目录](active-directory-b2c-get-started.md)。 在请求中使用目录的名称。
2. [创建应用程序](active-directory-b2c-app-registration.md)，获取应用程序 ID 和重定向 URI。 在应用中包含本机客户端。
3. [创建策略](active-directory-b2c-reference-policies.md)以获取策略名称。

