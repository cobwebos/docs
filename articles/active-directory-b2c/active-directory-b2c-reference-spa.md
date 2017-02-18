---
title: Azure Active Directory B2C | Microsoft Docs
description: "如何直接使用隐式流构建单页应用。"
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
translationtype: Human Translation
ms.sourcegitcommit: 96c9425d8f58d3b617330615573a479429f12b11
ms.openlocfilehash: 44a7cf18afdf6a523c5f7fb03ee49f72cdd22564


---
# <a name="azure-active-directory-b2c-sign-in-for-single-page-apps-using-auth-20-implicit-flow"></a>Azure Active Directory B2C：使用身份验证 2.0 隐式流登录单页应用
许多新式应用都有一个单页应用前端（主要以 JavaScript 编写）。 通常，这通过 AngularJS、Ember.js 或 Durandal.js 等框架编写。 主要在浏览器上运行的单一页面和其他 JavaScript 应用程序在身份验证时面临一些有趣的挑战：

* 这些应用的安全特征与传统的基于服务器的 Web 应用程序大不相同。
* 许多授权服务器与标识提供者不支持 CORS 请求。
* 重定向离开应用程序的完整网页浏览器变得对用户经验特别有侵略性。

为了支持这些应用程序，Azure AD B2C 使用 OAuth 2.0 隐式流。  [OAuth 2.0 规范第 4.2 部分](http://tools.ietf.org/html/rfc6749)描述了 OAuth 2.0 授权隐式授权流。  在此流中，应用直接从 Azure AD 授权终结点接收令牌，无需任何服务器到服务器的交换。 所有身份验证逻辑和会话处理完全在 JavaScript 客户端中发生，无需进行额外的页面重定向。

Azure AD B2C 扩展了标准 OAuth 2.0 隐式流，使其功能远远超出了简单的身份验证和授权。 它引入了[ **策略参数**](active-directory-b2c-reference-policies.md)，使你可以使用 OAuth 2.0 向应用添加用户体验，例如注册、登录和配置文件管理。 下面我们将介绍如何使用隐式流和 Azure AD 在单页应用程序中实现上述每种体验。  你还可以查看我们的 [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi) 或 [.NET](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi) 示例来帮助你开始使用。

以下示例 HTTP 请求将使用我们的示例 B2C 目录 **fabrikamb2c.onmicrosoft.com** 以及示例应用程序和策略。 你可以随意使用这些值亲自尝试这些请求，或者可以用自己的值替换它们。
了解如何[获取自己的 B2C 目录、应用程序和策略](#use-your-own-b2c-tenant)。

## <a name="protocol-diagram"></a>协议图
整个隐式登录流如下所示 - 下面将详细描述每个步骤。

![OpenId Connect Swimlanes](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>发送身份验证请求
当 Web 应用需要对用户进行身份验证并执行策略时，它会将用户定向到 `/authorize` 终结点。 这是身份验证流程的交互部分，在此部分中用户将根据策略实际执行一些操作，并从 Azure AD 终结点获取 `id_token`。

在此请求中，客户端指示要在 `scope` 参数中需要从用户获取的权限以及要在 `p` 参数中执行的策略。 下面提供了三个示例（带换行符以便阅读），每个示例使用不同的策略。 若要了解每个请求的工作原理，请尝试将请求粘贴到浏览器并运行它。

#### <a name="use-a-sign-in-policy"></a>使用登录策略
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

#### <a name="use-a-sign-up-policy"></a>使用注册策略
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

#### <a name="use-an-edit-profile-policy"></a>使用编辑配置文件策略
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

| 参数 |  | 说明 |
| --- | --- | --- |
| client_id |必选 |Azure 门户分配给应用的[应用程序 ID](https://portal.azure.com/) 。 |
| response_type |必填 |必须包含 OpenID Connect 登录的 `id_token`。  也可以包含 response_type `token`。 此处使用 `token`，让应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。  如果使用 `token` response_type，`scope` 参数必须包含范围，以指出要对哪个资源发出令牌。 |
| redirect_uri |建议 |应用程序的 redirect_uri，应用程序可在此发送及接收身份验证响应。 其必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 URL。 |
| response_mode |建议 |指定将生成的令牌送回到应用程序所应该使用的方法。  对于隐式流应该是 `fragment`。 |
| 作用域 |必选 |范围的空格分隔列表。 一个范围值，该值向 Azure AD 指示正在请求的两个权限。 `openid` 作用域表示允许使用 **id_tokens** 的形式使用户登录并获取关于用户的数据（本文稍后对此进行详述）。 `offline_access` 作用域对 Web 应用是可选的。 该作用域表示应用需要 **refresh_token** 才能长期访问资源。 |
| state |建议 |同样随令牌响应返回的请求中所包含的值。 其可以是你想要的任何内容的字符串。 随机生成的唯一值通常用于防止跨站点请求伪造攻击。 该状态也用于在身份验证请求出现之前，在应用程序中编码用户的状态信息，例如用户之前所在的页面。 |
| nonce |必选 |由应用程序生成且包含在请求中的值，以声明方式包含在生成的 id_token 中。 应用程序接着便可确认此值，以减少令牌重新执行攻击。 此值通常是随机的唯一字符串，可用以识别请求的来源。 |
| p |必选 |将执行的策略。 它是在 B2C 租户中创建的策略的名称。 策略名称值应以“b2c\_1\_”开头。 了解[可扩展策略框架](active-directory-b2c-reference-policies.md)中的策略的详细信息。 |
| prompt |可选 |需要的用户交互类型。 此时唯一有效的值为“login”，这会强制用户在该请求上输入其凭据。 单一登录不会生效。 |

此时，将要求用户完成策略的工作流。 这可能涉及用户输入其用户名和密码、用社交标识登录、注册目录，或任何其他若干步骤，这取决于如何定义策略。

用户完成策略后，Azure AD 使用 `response_mode` 参数中指定的方法，将响应返回到位于所指示的 `redirect_uri` 的应用。 对于上述每种情况，响应将完全相同，与执行的策略无关。

#### <a name="successful-response"></a>成功的响应
使用 `response_mode=fragment` 和 `response_type=id_token+token` 的成功响应如下所示（包含换行符以方便阅读）：

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
| access_token |应用请求的访问令牌。  访问令牌不得进行解码或检查，可被视为不透明的字符串。 |
| token_type |令牌类型值。 Azure AD 唯一支持的类型是 Bearer。 |
| expires_in |access_token 有效的时间长度（以秒为单位）。 |
| 作用域 |令牌有效的范围，可用于缓存令牌以供以后使用。 |
| id_token |应用程序请求的 id_token。 可以使用 id_token 验证用户的标识，并以用户身份开始会话。 有关 id_token 及其内容的更多详细信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 |
| state |如果请求中包含状态参数，响应中就应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |

#### <a name="error-response"></a>错误响应
错误响应可能也发送到 `redirect_uri`，让应用可以适当地处理：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 参数 | 说明 |
| --- | --- |
| error |错误代码字符串，用于对发生的错误类型进行分类。 错误代码可用于错误处理。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |
| state |请参阅上表中的完整说明。 如果请求中包含状态参数，响应中就应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。|

## <a name="validate-the-idtoken"></a>验证 id_token
仅接收 id_token 不足以对用户进行身份验证 — 必须验证 id_token 的签名，并按照应用的要求验证令牌中的声明。 Azure AD B2C 使用 [JSON Web 令牌 (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。

有许多开放源代码库可用于验证 JWT，具体取决于你的首选语言。 请考虑浏览这些选项，而不是实现自己的验证逻辑。 此处的信息有助于弄清如何正确使用这些库。

Azure AD B2C 具有 OpenID Connect 元数据终结点，允许应用程序在运行时提取有关 Azure AD B2C 的信息。 此信息包括终结点、令牌内容和令牌签名密钥。 B2C 租户中的每个策略都有一个 JSON 元数据文档。 例如，`fabrikamb2c.onmicrosoft.com` 中的 `b2c_1_sign_in` 策略的元数据文档位于：

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

此配置文档的一个属性为 `jwks_uri`，相同策略的该属性的值为：

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`。

若要确定对 id_token 进行签名所使用的策略（以及从何处获取元数据），你可以有两个方法。 首先，策略名称包含在 id_token 中的 `acr` 声明中。 有关如何从 id_token 中解析声明的信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。 另一个方法是在发出请求时在 `state` 参数的值中对策略进行编码，然后对其进行解码以确定使用的策略。 任何一种方法都是完全有效的。

从 OpenID Connect 元数据终结点获取元数据文档后，可以使用 RSA 256 公钥（位于此终结点上）来验证 id_token 的签名。 在任何指定的时间点，此终结点上可能列出多个密钥，每个密钥使用 `kid` 进行标识。 Id_token 的标头还包含 `kid` 声明，该声明指示哪个密钥用于对 id_token 进行签名。 有关详细信息（包括[验证令牌](active-directory-b2c-reference-tokens.md#token-validation)），请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。
<!--TODO: Improve the information on this-->

在验证 id_token 的签名后，几个声明还需要验证，例如：

* 验证 `nonce` 声明以防止令牌重放攻击。 其值应为在登录请求中指定的内容。
* 验证 `aud` 声明以确保已为应用颁发 id_token。 其值应为应用的应用程序 ID。
* 验证 `iat` 和 `exp` 声明以确保 id_token 未过期。

还有其他一些验证需要执行，有关详细信息请参阅 [OpenID Connect 核心规范](http://openid.net/specs/openid-connect-core-1_0.html)。  根据你的情况，你可能还希望验证其他声明。 一些常见的验证包括：

* 确保用户/组织已注册应用。
* 确保用户拥有正确的授权/权限。
* 确保执行了一定强度的身份验证，例如 Azure 多重身份验证。

有关 id_token 中声明的详细信息，请参阅 [Azure AD B2C 令牌参考](active-directory-b2c-reference-tokens.md)。

完全验证 id_token 后，即可开始与用户的会话，并使用 id_token 中的声明来获取应用中的用户相关信息。 此信息可以用于显示、记录和授权等等。

## <a name="get-access-tokens"></a>获取访问令牌
如果 Web 应用需要做的只是执行策略，则可以跳过下面几节。 这些章节仅适用于需要对 Web API 进行验证调用，还受 Azure AD B2C 保护的 Web 应用。

将用户登录到单页应用后，便可获取访问令牌以调用受 Azure AD 保护的 Web API。  即使你已使用 `token` response_type 收到令牌，也仍可以使用此方法获取其他资源的令牌，而无需再次将用户重定向到登录页。

在标准 Web 应用流中，可以通过对 `/token` 终结点进行请求来实现此目的。  但是，该终结点不支持 CORS 请求，因此进行 AJAX 调用以获取和刷新令牌是不可能的。  相反，你可以在隐藏的 iframe 中使用隐式流，以获取其他 Web API 的新令牌：

```
// Line breaks for legibility only

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

| 参数 |  | 说明 |
| --- | --- | --- |
| client_id |必选 |Azure 门户分配给应用的[应用程序 ID](https://portal.azure.com/) 。 |
| response_type |必填 |必须包含 OpenID Connect 登录的 `id_token`。  也可以包含 response_type `token`。 此处使用 `token`，让应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。  如果使用 `token` response_type，`scope` 参数必须包含范围，以指出要对哪个资源发出令牌。 |
| redirect_uri |建议 |应用程序的 redirect_uri，应用程序可在此发送及接收身份验证响应。  其必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 url。 |
| 作用域 |必填 |范围的空格分隔列表。  若要获取令牌，请包含相应资源请求的所有范围。 |
| response_mode |建议 |指定用于将生成的令牌送回到应用的方法。  可以是 `query`、`form_post` 或 `fragment` 之一。 |
| state |建议 |随令牌响应返回的请求中所包含的值。  其可以是你想要的任何内容的字符串。  随机生成的唯一值通常用于防止跨站点请求伪造攻击。  该状态也用于在身份验证请求出现之前，于应用程序中编码用户的状态信息，例如之前所在的网页或视图。 |
| nonce |必填 |由应用生成且包含在请求中的值，以声明方式包含在生成的 id_token 中。  应用程序接着便可确认此值，以减少令牌重新执行攻击。  此值通常是随机的唯一字符串，可用于识别请求的来源。 |
| prompt |必填 |若要刷新并获取隐藏的 iframe 中的令牌，请使用 `prompt=none` 以确保 iframe 会立即返回，而不会在登录页面上挂起。 |
| login_hint |必填 |若要刷新并获取隐藏 iframe 中的令牌，必须在此提示中包含用户的用户名，以便区分用户在特定时间点可能具有的多个会话。 可以使用 `preferred_username` 声明从前次登录提取用户名。 |
| domain_hint |必填 |可以是 `consumers` 或 `organizations` 之一。  若要刷新并获取隐藏 iframe 中的令牌，必须在请求中包含 domain_hint。  从前次登录的 id_token 提取 `tid` 声明，以确定要使用哪个值。  如果 `tid` 声明值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，应该使用 `domain_hint=consumers`。  否则使用 `domain_hint=organizations`。 |

通过设置 `prompt=none` 参数，此请求将立即成功或立即失败，并返回应用程序。  成功的响应会通过 `response_mode` 参数中指定的方法，发送到位于所指示的 `redirect_uri` 的应用。

#### <a name="successful-response"></a>成功的响应
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
| token_type |始终为 `Bearer`。 |
| state |如果请求中包含状态参数，响应中就应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |
| expires_in |访问令牌的有效期（以秒为单位）。 |
| 作用域 |访问令牌有效的范围。 |

#### <a name="error-response"></a>错误响应
错误响应还可能发送到 `redirect_uri`，因此应用可以适当地对其进行处理。  如果是 `prompt=none`，预期的错误为：

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 参数 | 说明 |
| --- | --- |
| error |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

如果在 iframe 请求中收到此错误，用户必须再次以交互方式登录以检索新令牌。  可以选择对应用程序合理的任何方式处理这种情况。

## <a name="refreshing-tokens"></a>刷新令牌
`id_token` 和 `access_token` 很快就会过期，因此应用必须准备好定期刷新这些令牌。  若要刷新任一类型的令牌，请通过使用 `prompt=none` 参数控制 Azure AD 的行为，来执行上述同一隐藏的 iframe 请求。  若要接收新的 `id_token`，请务必使用 `response_type=id_token` 和 `scope=openid`，以及 `nonce` 参数。

## <a name="send-a-sign-out-request"></a>发送注销请求
如果想要从应用中注销用户，请将用户重定向到 Azure AD 进行注销。 如果没有这么做，那么用户可能可以在应用程序中重新进行身份验证，且无需再次输入其凭据。 这是因为他们具有和 Azure AD 的有效单一登录会话。

只需将用户重定向到前面的章节“验证 id_token”中所述的相同 OpenID Connect 元数据文档中列出的 `end_session_endpoint`：

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| 参数 | 必需？ | 说明 |
| --- | --- | --- |
| p |必选 |要用于从应用程序中注销用户的策略。 |
| post_logout_redirect_uri |建议 |用户在成功注销后应重定向到的 URL。 如果未包含此参数，Azure AD B2C 会向用户显示一条常规消息。 |

> [!NOTE]
> 尽管将用户定向到 `end_session_endpoint` 将清除用户的某些 Azure AD B2C 的单一登录状态，但是不会将用户从其社交标识提供者 (IDP) 会话中注销。 如果用户在后续登录中选择相同的 IDP，他们将重新进行身份验证，且无需输入其凭据。 如果用户想要注销 B2C 应用程序，并不表示他们想要完全注销其 Facebook 帐户。 但是，如果是本地帐户，则将以正确的方式结束用户的会话。
> 
> 

## <a name="use-your-own-b2c-tenant"></a>使用自己的 B2C 租户
如果你想自己尝试这些请求，必须首先执行这三个步骤，然后用自己的值替换上面的示例值：

* [创建一个 B2C 租户](active-directory-b2c-get-started.md)，并在请求中使用该租户的名称。
* [创建应用程序](active-directory-b2c-app-registration.md)以获取应用程序 ID 和 redirect_uri。 你会想要在应用程序中包含 **Web 应用/Web API**，并可以选择创建**应用程序密码**。
* [创建策略](active-directory-b2c-reference-policies.md)以获取策略名称。

## <a name="samples"></a>示例

* [使用 Node.JS 创建单页应用](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-nodejs-webapi)
* [使用 .NET 创建单页应用](https://github.com/Azure-Samples/active-directory-b2c-javascript-singlepageapp-dotnet-webapi)




<!--HONumber=Feb17_HO1-->


