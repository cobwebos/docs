---
title: "Azure Active Directory B2C：使用隐式流的单页应用 | Microsoft Docs"
description: "了解如何直接通过使用 Azure Active Directory B2C 的 OAuth 2.0 隐式流生成单页应用。"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: a45cc74c-a37e-453f-b08b-af75855e0792
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: parakhj
ms.openlocfilehash: 44ff168599e9078506e1afdd0f1dc4657ef0964d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-single-page-app-sign-in-by-using-oauth-20-implicit-flow"></a>Azure AD B2C：使用 OAuth 2.0 隐式流的单页应用登录

> [!NOTE]
> 此功能为预览版。
> 

许多新式应用都有一个单页应用前端（主要以 JavaScript 编写）。 通常，该应用通过 AngularJS、Ember.js 或 Durandal 等框架编写。 主要在浏览器上运行的单页应用和其他 JavaScript 应用在身份验证时还面临一些其他挑战：

* 这些应用的安全特征与传统的基于服务器的 Web 应用程序大不相同。
* 许多授权服务器与标识提供者不支持跨源资源共享 (CORS) 请求。
* 重定向离开应用的完整网页浏览器可能会对用户体验具有较大侵略性。

为了支持这些应用程序，Azure Active Directory B2C (Azure AD B2C) 使用 OAuth 2.0 隐式流。 [OAuth 2.0 规范第 4.2 部分](http://tools.ietf.org/html/rfc6749)描述了 OAuth 2.0 授权隐式授权流。 在隐式流中，应用直接从 Azure Active Directory (Azure AD) 授权终结点接收令牌，无需任何服务器到服务器的交换。 所有身份验证逻辑和会话处理完全发生在 JavaScript 客户端中，无需进行额外的页面重定向。

Azure AD B2C 扩展了标准 OAuth 2.0 隐式流，使其功能远远超出了简单的身份验证和授权。 Azure AD B2C 引入了[策略参数](active-directory-b2c-reference-policies.md)。 通过该策略参数，你可以使用 OAuth 2.0 向应用添加用户体验，例如注册、登录和配置文件管理。 在本文中，我们将介绍如何使用隐式流和 Azure AD 在单页应用程序中实现上述每种体验。 为了帮助你开始操作，请查看我们的 [Node.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) 和 [Microsoft .NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) 示例。

在本文的示例 HTTP 请求中，我们使用示例 Azure AD B2C 目录 fabrikamb2c.onmicrosoft.com。此外，我们还使用自己的示例应用程序和策略。 你可以使用这些值亲自尝试这些请求，或者可以用自己的值替换它们。
了解如何[获取自己的 Azure AD B2C 目录、应用程序和策略](#use-your-own-b2c-tenant)。


## <a name="protocol-diagram"></a>协议图

隐式登录流看起来类似于下图。 本文后面将详细说明每个步骤。

![OpenID Connect swimlanes](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>发送身份验证请求
当 Web 应用需要对用户进行身份验证并执行策略时，它会将用户定向到 `/authorize` 终结点。 这是流程的交互部分，在此部分中，用户会根据策略执行一些操作。 用户会从 Azure AD 终结点获取 ID 令牌。

在此请求中，客户端指示在 `scope` 参数中需要从用户处获取的权限。 在 `p` 参数中，它指示要执行的策略。 下面有三个示例（为方便阅读，提供了换行符），每个示例都使用不同的策略。 要了解每个请求的工作原理，请尝试将请求粘贴到浏览器并运行它。

### <a name="use-a-sign-in-policy"></a>使用登录策略
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-policy"></a>使用注册策略
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-policy"></a>使用编辑配置文件策略
```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| client_id |必选 |在 [Azure 门户](https://portal.azure.com)中分配给应用的应用程序 ID。 |
| response_type |必选 |必须包含 OpenID Connect 登录的 `id_token`。 也可以包含响应类型 `token`。 如果使用 `token`，则应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。  如果使用 `token` 响应类型，`scope` 参数必须包含一个范围，以指出要对哪个资源发出令牌。 |
| redirect_uri |建议 |应用的重定向 URI，应用可通过此 URI 发送和接收身份验证响应。 它必须完全匹配在门户中注册的其中一个重定向 URI，但必须经 URL 编码。 |
| response_mode |建议 |指定用于将生成的令发送回应用的方法。  对于隐式流，使用 `fragment`。 |
| 作用域 |必选 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 `openid` 作用域表示允许使用 ID 令牌的形式使用户登录并获取有关用户的数据。 （我们将在本文的后面部分中详细讨论。）`offline_access` 作用域对 Web 应用是可选的。 它表示你的应用需要刷新令牌才能长期访问资源。 |
| state |建议 |同时随令牌响应返回的请求中所包含的值。 它可以是你想要使用的任何内容的字符串。 随机生成的唯一值通常用于防止跨站点请求伪造攻击。 该状态也用于在身份验证请求出现之前，在应用中编码用户的状态信息，例如用户之前所在的页面。 |
| nonce |必选 |由应用生成且包含在请求中的值，以声明方式包含在生成的 ID 令牌中。 应用程序接着便可确认此值，以减少令牌重新执行攻击。 此值通常是随机产生的唯一字符串，可用于识别请求的来源。 |
| p |必选 |要执行的策略。 它是在 Azure AD B2C 租户中创建的策略的名称。 策略名称值应以“b2c\_1\_”开头。 有关详细信息，请参阅 [Azure AD B2C 内置策略](active-directory-b2c-reference-policies.md)。 |
| prompt |可选 |需要的用户交互类型。 目前唯一有效的值是 `login`。 这会强制用户在该请求上输入其凭据。 单一登录不会生效。 |

此时，要求用户完成策略的工作流。 这可能涉及用户输入其用户名和密码、使用社交标识登录、注册目录，或任何其他步骤。 用户操作取决于策略是如何定义的。

用户完成策略后，Azure AD 会在你用于 `redirect_uri` 的值处将响应返回到应用。 它使用在 `response_mode` 参数中指定的方法。 对于每种用户操作情况，响应完全相同，与执行的策略无关。

### <a name="successful-response"></a>成功的响应
使用 `response_mode=fragment` 和 `response_type=id_token+token` 的成功响应如下所示（包含换行符以便阅读）：

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| 参数 | 说明 |
| --- | --- |
| access_token |应用请求的访问令牌。  访问令牌不得进行解码或检查， 可以被视为不透明的字符串。 |
| token_type |令牌类型值。 Azure AD 唯一支持的类型是 Bearer。 |
| expires_in |访问令牌有效的时间长度（以秒为单位）。 |
| 作用域 |令牌的有效范围。 还可使用范围来缓存令牌，以供以后使用。 |
| id_token |应用请求的 ID 令牌。 可以使用 ID 令牌验证用户的身份，并开始与用户的会话。 有关 ID 令牌及其内容的详细信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 |
| state |如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用需验证请求和响应中的 `state` 值是否相同。 |

### <a name="error-response"></a>错误响应
错误响应也可能发送到重定向 URI，使应用可以对其进行适当处理：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 说明 |
| --- | --- |
| error |错误代码字符串，用于对发生的错误类型进行分类。 还可以使用错误代码进行错误处理。 |
| error_description |可帮助用户识别身份验证错误根本原因的特定错误消息。 |
| state |请参阅上表中的完整说明。 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用需验证请求和响应中的 `state` 值是否相同。|

## <a name="validate-the-id-token"></a>验证 ID 令牌
收到 ID 令牌并不表示可以对用户进行身份验证。 还必须根据应用的要求，验证 ID 令牌的签名和令牌中的声明。 Azure AD B2C 使用 [JSON Web 令牌 (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。

许多开放源代码库都可用于验证 JWT，具体取决于首选使用的语言。 请考虑浏览可用的开放源代码库，而不是实现自己的验证逻辑。 可以使用本文中的信息了解如何正确使用这些库。

Azure AD B2C 具有 OpenID Connect 元数据终结点。 应用可以使用终结点在运行时提取 Azure AD B2C 的相关信息。 此信息包括终结点、令牌内容和令牌签名密钥。 Azure AD B2C 租户中的每个策略都有一个 JSON 元数据文档。 例如，fabrikamb2c.onmicrosoft.com 租户中的 b2c_1_sign_in 策略的元数据文档位于以下位置：

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此配置文档的其中一个属性是 `jwks_uri`。 相同策略的值是：

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

若要确定对 ID 令牌签名所用的策略（以及提取元数据的位置），共有两个选择。 首先，策略名称包含在 `id_token` 中的 `acr` 声明中。 有关如何从 ID 令牌中分析声明的信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 另一个方法是在发出请求时在 `state` 参数的值中对策略进行编码。 然后对 `state` 参数进行解码以确定所使用的策略。 任意一种方法均有效。

从 OpenID Connect 元数据终结点获取元数据文档后，可以使用 RSA-256 公钥（位于此终结点上）来验证 ID 令牌的签名。 在任何给定的时间，此终结点上可能列出多个密钥，每个密钥使用 `kid` 进行标识。 `id_token` 的标头还包含 `kid` 声明。 它指示这些键中的哪一个用于对 ID 令牌进行签名。 有关详细信息（包括了解[验证令牌](active-directory-b2c-reference-tokens.md)），请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md#token-validation)。
<!--TODO: Improve the information on this-->

在验证 ID 令牌的签名后，需要验证几个声明。 例如：

* 验证 `nonce` 声明以防止令牌重放攻击。 其值应为在登录请求中指定的内容。
* 验证 `aud` 声明以确保已为应用颁发 ID 令牌。 其值应为应用的应用程序 ID。
* 验证 `iat` 和 `exp` 声明以确保 ID 令牌未过期。

其他一些需要执行的验证在 [OpenID Connect 核心规范](http://openid.net/specs/openid-connect-core-1_0.html)中有详细说明。根据情况，可能还希望验证其他声明。 一些常见的验证包括：

* 确保用户或组织已注册应用。
* 确保用户拥有正确的授权和权限。
* 确保执行了一定强度的身份验证，例如使用 Azure 多重身份验证。

有关 ID 令牌中声明的详细信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。

完全验证 ID 令牌后，可以开始与用户的会话。 在应用中，使用 ID 令牌中的声明来获取用户的相关信息。 此信息可以用于显示、记录和授权等等。

## <a name="get-access-tokens"></a>获取访问令牌
如果 Web 应用唯一需要做的是执行策略，则可以跳过下面几节。 下面几节中的信息仅适用于需要对 Web API 进行验证调用，并且受 Azure AD B2C 保护的 Web 应用。

将用户登录到单页应用后，便可获取访问令牌以调用受 Azure AD 保护的 Web API。 即使已使用 `token` 响应类型收到令牌，也仍可以使用此方法获取其他资源的令牌，而无需再次将用户重定向到登录页。

在典型 Web 应用流中，可以通过对 `/token` 终结点进行请求来实现此目的。  但是，该终结点不支持 CORS 请求，因此进行 AJAX 调用以获取和刷新令牌并不可取。 相反，可以在隐藏的 HTML iframe 元素中使用隐式流，以获取其他 Web API 的新令牌。 下面是一个示例（带换行符以便阅读）：

```

https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| client_id |必选 |在 [Azure 门户](https://portal.azure.com)中分配给应用的应用程序 ID。 |
| response_type |必选 |必须包含 OpenID Connect 登录的 `id_token`。  也可能包含响应类型 `token`。 如果在此处使用 `token`，应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。 如果使用 `token` 响应类型，`scope` 参数必须包含一个范围，以指出要对哪个资源发出令牌。 |
| redirect_uri |建议 |应用的重定向 URI，应用可通过此 URI 发送和接收身份验证响应。 它必须与门户中注册的其中一个重定向 URI 完全匹配，否则必须经过 URL 编码。 |
| 作用域 |必选 |范围的空格分隔列表。  若要获取令牌，请包含相应资源所需的所有范围。 |
| response_mode |建议 |指定用于将生成的令牌送回到应用的方法。  可以是 `query`、`form_post` 或 `fragment`。 |
| state |建议 |随令牌响应返回的请求中所包含的值。  它可以是你想要使用的任何内容的字符串。  随机生成的唯一值通常用于防止跨站点请求伪造攻击。  它还可用于在身份验证请求发生前，对有关用户在应用中的状态信息进行编码。 例如，用户之前所在的页面或视图。 |
| nonce |必选 |由应用生成且包含在请求中的值，以声明方式包含在生成的 ID 令牌 中。  应用程序接着便可确认此值，以减少令牌重新执行攻击。 此值通常是随机产生的唯一字符串，可识别请求的来源。 |
| prompt |必选 |若要刷新并获取隐藏的 iframe 中的令牌，请使用 `prompt=none` 以确保 iframe 会立即返回，而不会停滞在登录页面上。 |
| login_hint |必选 |若要刷新并获取隐藏的 iframe 中的令牌，请在此提示中加入用户的用户名，以便区分用户在给定时间内可能具有的多个会话。 可以使用 `preferred_username` 声明从以前的登录中提取用户名。 |
| domain_hint |必选 |可以是 `consumers` 或 `organizations`。  若要刷新并获取隐藏的 iframe 中的令牌，必须在请求中包含 `domain_hint` 值。  从以前登录的 ID 令牌提取 `tid` 声明，以确定要使用哪个值。  如果 `tid` 声明值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，请使用 `domain_hint=consumers`。  否则使用 `domain_hint=organizations`。 |

通过设置 `prompt=none` 参数，此请求会立即成功或立即失败，并返回到应用程序。  成功的响应会通过 `response_mode` 参数中指定的方法，发送到位于所指示的重定向 URI 的应用。

### <a name="successful-response"></a>成功的响应
使用 `response_mode=fragment` 的成功响应如下所示：

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| 参数 | 说明 |
| --- | --- |
| access_token |应用请求的令牌。 |
| token_type |令牌类型始终是“持有者”。 |
| state |如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用需验证请求和响应中的 `state` 值是否相同。 |
| expires_in |访问令牌的有效期（以秒为单位）。 |
| 作用域 |访问令牌有效的范围。 |

### <a name="error-response"></a>错误响应
错误响应也可能发送到重定向 URI，使应用可以对其进行适当处理。  对于 `prompt=none`，错误应如下所示：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 参数 | 说明 |
| --- | --- |
| error |错误代码字符串，可用于对发生的错误类型进行分类。 还可使用该字符串对错误作出响应。 |
| error_description |可帮助用户识别身份验证错误根本原因的特定错误消息。 |

如果在 iframe 请求中收到此错误，用户必须再次以交互方式登录以检索新令牌。 可以按合理方式针对应用程序处理上述情况。

## <a name="refresh-tokens"></a>刷新令牌
ID 令牌和访问令牌在较短时间后都会过期。 应用必须准备好定期刷新这些令牌。  若要刷新任一类型的令牌，请使用 `prompt=none` 参数控制 Azure AD 步骤，执行我们在先前示例中使用的同一隐藏的 iframe 请求。  若要接收新的 `id_token` 值，请务必使用 `response_type=id_token` 和 `scope=openid`，以及 `nonce` 参数。

## <a name="send-a-sign-out-request"></a>发送注销请求
如果想要从应用中注销用户，请将用户重定向到 Azure AD 进行注销。否则，用户可能可以在应用中重新进行身份验证，且无需再次输入其凭据。 这是因为他们具有和 Azure AD 的有效单一登录会话。

只需将用户重定向到[验证 ID 令牌](#validate-the-id-token)中所述的相同 OpenID Connect 元数据文档中列出的 `end_session_endpoint`。 例如：

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| p |必选 |要用于从应用程序中注销用户的策略。 |
| post_logout_redirect_uri |建议 |用户在成功注销后应重定向到的 URL。如果未包含此参数，Azure AD B2C 会向用户显示一条常规消息。 |

> [!NOTE]
> 将用户定向到 `end_session_endpoint` 会清除用户的某些 Azure AD B2C 单一登录状态。 但是，不会从用户的社交标识提供者会话中注销该用户。 如果用户在后续登录中选择相同的标识提供者，用户将重新进行身份验证，且无需输入其凭据。 例如，如果用户要注销 Azure AD B2C 应用程序，并不表示他们想要完全注销其 Facebook 帐户。 但是，如果是本地帐户，则会以正确的方式结束用户的会话。
> 
> 

## <a name="use-your-own-azure-ad-b2c-tenant"></a>使用你自己的 Azure AD B2C 租户
若要自行尝试这些请求，请完成以下三个步骤。 将我们在本文中使用的示例值替换为你自己的值：

1. [创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)。 在请求中使用租户的名称。
2. [创建应用程序](active-directory-b2c-app-registration.md)以获取应用程序 ID 和 `redirect_uri` 值。 在应用中包含 Web 应用或 Web API。 或者，你可以选择创建应用程序密码。
3. [创建策略](active-directory-b2c-reference-policies.md)以获取策略名称。

## <a name="samples"></a>示例

* [使用 Node.js 创建单页应用](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [使用 .NET 创建单页应用](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)

