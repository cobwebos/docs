---
title: Azure Active Directory B2C | Microsoft Docs
description: "通过使用 OpenID Connect 身份验证协议的 Azure Active Directory 实现构建 Web 应用程序。"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: c371aaab-813a-4317-97df-b62e2f53d865
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 51061199e4929406c3ac77a26e2f972686236bd4


---
# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory B2C：OAuth 2.0 授权代码流
OAuth 2.0 授权代码授予可用于设备上所安装的应用中，以访问受保护的资源，例如 Web API。 通过使用 OAuth 2.0 的 Azure Active Directory (Azure AD) B2C 实现，你可以向移动应用和桌面应用添加注册、登录和其他标识管理任务。 本指南与语言无关。 介绍在不使用我们的任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

<!-- TODO: Need link to libraries -->

[OAuth 2.0 规范第 4.1 部分](http://tools.ietf.org/html/rfc6749)描述了 OAuth 2.0 授权代码流。 你可以将它用于在大部分的应用类型（包括 [Web 应用](active-directory-b2c-apps.md#web-apps)和[本机安装的应用](active-directory-b2c-apps.md#mobile-and-native-apps)）中执行身份验证与授权。 它可让应用程序安全地获取 **access_tokens**，而这些令牌可用于访问[授权服务器](active-directory-b2c-reference-protocols.md#the-basics)保护的资源。

本指南将重点介绍 OAuth 2.0 授权代码流（**公共客户端**）的特定风格。 公共客户端是那些不能被信任以安全维护机密密码完整性的任何客户端应用程序。 这包括移动应用、桌面应用，以及几乎任何在设备上运行并需要获取 access_tokens 的应用程序。 如果要使用 Azure AD B2C 向 Web 应用添加标识管理，则应使用 [OpenID Connect](active-directory-b2c-reference-oidc.md)，而不是 OAuth 2.0。

Azure AD B2C 扩展了标准 OAuth 2.0 流，使其功能远远超出了简单的身份验证和授权。 它引入了[ **策略参数**](active-directory-b2c-reference-policies.md)，使你可以使用 OAuth 2.0 向应用添加用户体验，例如注册、登录和配置文件管理。 在这里，我们将演示如何使用 OAuth 2.0 和策略在你的本机应用程序中实现每个体验。 我们还将展示如何获取访问 Web API 的 access_tokens。

以下示例 HTTP 请求将使用我们的示例 B2C 目录 **fabrikamb2c.onmicrosoft.com** 以及示例应用程序和策略。 你可以使用这些值自由尝试请求，或者可以用自己的值替换它们。
了解如何[获取自己的 B2C 目录、应用程序和策略](#use-your-own-b2c-directory)。

## <a name="1-get-an-authorization-code"></a>1.获取授权代码
授权代码流始于客户端将用户定向到的 `/authorize` 终结点。 这是用户会实际执行操作的流的交互部分。 在此请求中，客户端指示要在 `scope` 参数中需要从用户获取的权限以及要在 `p` 参数中执行的策略。 下面提供了三个示例（带换行符以便阅读），每个示例使用不同的策略。

#### <a name="use-a-sign-in-policy"></a>使用登录策略
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

#### <a name="use-a-sign-up-policy"></a>使用注册策略
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

#### <a name="use-an-edit-profile-policy"></a>使用编辑配置文件策略
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
| client_id |必选 |Azure 门户分配给应用的[应用程序 ID](https://portal.azure.com) 。 |
| response_type |必选 |响应类型，其中必须包括 `code` 的授权待码流。 |
| redirect_uri |必选 |应用程序的 redirect_uri，应用程序可在此发送及接收身份验证响应。 其必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 URL。 |
| 作用域 |必选 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 使用客户端 ID 作为范围，该范围指示你的应用需要可以针对自己的服务或 Web API（由同一客户端 ID 表示）使用的**访问令牌**。  `offline_access` 范围表示应用需要 **refresh_token** 才能获得访问资源的长生存期。  还可以使用 `openid` 范围从 Azure AD B2C 请求 **id_token**。 |
| response_mode |建议 |将生成的 authorization_code 送回到应用程序所应该使用的方法。 该方法可以是“query”、“form_post”或“fragment”。 |
| state |建议 |同样随令牌响应返回的请求中所包含的值。 其可以是你想要的任何内容的字符串。 随机生成的唯一值通常用于防止跨站点请求伪造攻击。 该状态也用于在身份验证请求出现之前，于应用程序中编码用户的状态信息，例如之前所在的网页或正在执行的策略。 |
| p |必选 |将执行的策略。 它是在 B2C 目录中创建的策略的名称。 策略名称值应以“b2c\_1\_”开头。 了解[可扩展策略框架](active-directory-b2c-reference-policies.md)中的策略的详细信息。 |
| prompt |可选 |需要的用户交互类型。 此时唯一有效的值为“login”，这会强制用户在该请求上输入其凭据。 单一登录不会生效。 |

此时，将要求用户完成策略的工作流。 这可能涉及用户输入其用户名和密码、用社交标识登录、注册目录，或任何其他若干步骤，这取决于如何定义策略。

用户完成策略后，Azure AD 将使用 `response_mode` 参数中指定的方法，将响应返回到位于所指示的 `redirect_uri` 的应用。 对于上述每种情况，响应将完全相同，与执行的策略无关。

使用 `response_mode=query` 的成功响应如下所示：

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| 参数 | 说明 |
| --- | --- |
| 代码 |应用程序请求的 authorization_code。 应用可以使用授权代码请求目标资源的 access_token。 Authorization_code 的生存期短。 通常，它们在约 10 分钟后过期。 |
| state |请参阅上表中的完整说明。 如果请求中包含状态参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的状态值是否完全相同。 |

错误响应可能也发送到 `redirect_uri`，让应用可以适当地处理：

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 说明 |
| --- | --- |
| error |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |
| state |请参阅本节第一个表中的完整说明。 如果请求中包含状态参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的状态值是否完全相同。 |

## <a name="2-get-a-token"></a>2.获取令牌
你已获取 authorization_code，现在可以将 `POST` 请求发送到 `/token` 终结点，兑换 `code` 以获取所需资源的令牌。 在 Azure AD B2C 中，你可以请求令牌的唯一资源是应用自己的后端 Web API。 用于向自己请求令牌的约定是使用应用的客户端 ID 作为范围：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| p |必选 |用于获取授权代码的策略。 你无法在此请求中使用不同的策略。 请注意，将此参数添加到*查询字符串*中，而不是添加到 POST 正文中。 |
| client_id |必选 |Azure 门户分配给应用的[应用程序 ID](https://portal.azure.com) 。 |
| grant_type |必选 |授予类型，该类型必须是授权代码流的 `authorization_code`。 |
| 作用域 |推荐 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 使用客户端 ID 作为范围，该范围指示你的应用需要可以针对自己的服务或 Web API（由同一客户端 ID 表示）使用的**访问令牌**。  `offline_access` 范围表示应用需要 **refresh_token** 才能获得访问资源的长生存期。  还可以使用 `openid` 范围从 Azure AD B2C 请求 **id_token**。 |
| 代码 |必选 |在流的第一个阶段中获取的 authorization_code。 |
| redirect_uri |必选 |收到 authorization_code 的应用程序的 redirect_uri。 |

成功的令牌响应如下：

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
| access_token |你请求的已签名的 JSON Web 令牌 (JWT) 令牌。 |
| 作用域 |令牌有效的范围，可用于缓存令牌以供以后使用。 |
| expires_in |令牌有效的时间长度（以秒为单位）。 |
| refresh_token |OAuth 2.0 refresh_token。 应用可以使用此令牌，在当前令牌过期之后获取其他令牌。 Refresh_tokens 的生存期很长，而且可以用于延长保留资源访问权限的时间。 有关更多详细信息，请参阅 [B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 说明 |
| --- | --- |
| error |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

## <a name="3-use-the-token"></a>3.使用令牌
你已经成功获取 `access_token`，现在可以通过在 `Authorization` 标头中包含令牌，在你的后端 Web API 的请求中使用令牌：

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4.刷新令牌
访问令牌和 ID 令牌的生存期短。 你必须在它们过期后将其刷新才能继续访问资源。 为此，你可以向 `/token` 终结点提交另一个 `POST` 请求。 但这次要提供 `refresh_token` 而不是 `code`：

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| p |必选 |用于获取原始 refresh_token 的策略。 你无法在此请求中使用不同的策略。 请注意，将此参数添加到*查询字符串*中，而不是添加到 POST 正文中。 |
| client_id |建议 |Azure 门户分配给应用的[应用程序 ID](https://portal.azure.com) 。 |
| grant_type |必选 |授予类型，该类型必须是这一段授权代码流的 `refresh_token`。 |
| 作用域 |建议 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 使用客户端 ID 作为范围，该范围指示你的应用需要可以针对自己的服务或 Web API（由同一客户端 ID 表示）使用的**访问令牌**。  `offline_access` 范围表示应用需要 **refresh_token** 才能获得访问资源的长生存期。  还可以使用 `openid` 范围从 Azure AD B2C 请求 **id_token**。 |
| redirect_uri |可选 |收到 authorization_code 的应用程序的 redirect_uri。 |
| refresh_token |必选 |在流的第二个阶段获取的原始 refresh_token。 |

成功的令牌响应如下：

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
| access_token |你请求的已签名的 JSON Web 令牌 (JWT) 令牌。 |
| 作用域 |令牌有效的范围，可用于缓存令牌以供以后使用。 |
| expires_in |令牌有效的时间长度（以秒为单位）。 |
| refresh_token |OAuth 2.0 refresh_token。 应用可以使用此令牌，在当前令牌过期之后获取其他令牌。 Refresh_tokens 的生存期很长，而且可以用于延长保留资源访问权限的时间。 有关更多详细信息，请参阅 [B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 |

错误响应如下所示：

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 参数 | 说明 |
| --- | --- |
| error |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

## <a name="use-your-own-b2c-directory"></a>使用你自己 B2C 目录
如果你想自己尝试这些请求，必须首先执行这三个步骤，然后用自己的值替换上面的示例值：

* [创建一个 B2C 目录](active-directory-b2c-get-started.md)，并在请求中使用你目录的名称。
* [创建应用程序](active-directory-b2c-app-registration.md)以获取应用程序 ID 和 redirect_uri。 建议你在应用中添加**本机客户端**。
* [创建策略](active-directory-b2c-reference-policies.md)以获取策略名称。




<!--HONumber=Nov16_HO3-->


