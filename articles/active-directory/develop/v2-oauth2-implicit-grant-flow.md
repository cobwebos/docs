---
title: OAuth 2.0 隐式授权流-Microsoft 标识平台 |Microsoft
description: 使用 Microsoft 标识平台隐式流保护单页应用。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 42d315b44a76e79d6f1db48e5024094099564a98
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700475"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft 标识平台和隐式授权流

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

使用 Microsoft 标识平台终结点，你可以使用 Microsoft 的个人和工作或学校帐户，将用户登录到单页应用。 主要在浏览器上运行的单页面和其他 JavaScript 应用程序在身份验证时面临一些有趣的挑战：

* 这些应用的安全特征与传统的基于服务器的 Web 应用程序大不相同。
* 许多授权服务器与标识提供者不支持 CORS 请求。
* 重定向离开应用程序的完整网页浏览器变得对用户经验特别有侵略性。

对于这些应用程序（AngularJS、Ember、响应等），Microsoft 标识平台支持 OAuth 2.0 隐式授予流。 有关隐式流的说明，请参阅 [OAuth 2.0 规范](https://tools.ietf.org/html/rfc6749#section-4.2)。 它的主要优点在于，它允许应用程序从 Microsoft 标识平台获取令牌，而无需执行后端服务器凭据交换。 这可让应用登录用户、维护会话，并获取客户端 JavaScript 代码中所有其他 Web API 的令牌。 使用隐式流时有几个重要的安全注意事项，具体而言，是关于[客户端](https://tools.ietf.org/html/rfc6749#section-10.3)和[用户模拟](https://tools.ietf.org/html/rfc6749#section-10.3)的注意事项。

本文介绍如何在应用程序中直接对协议进行编程。  如果可能，我们建议你改为使用受支持的 Microsoft 身份验证库（MSAL）来[获取令牌并调用受保护的 Web api](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

但是，如果不想在单页面应用中使用库，而是要自行发送协议消息，请遵循下面的常规步骤。

> [!NOTE]
> Microsoft 标识平台终结点并不支持所有 Azure Active Directory （Azure AD）方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读[microsoft 标识平台限制](active-directory-v2-limitations.md)。

## <a name="protocol-diagram"></a>协议图

下图显示了整个隐式登录流的样子，后续各部分更详细地介绍了每个步骤。

![显示隐式登录流的关系图](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>发送登录请求

若要最初将用户登录到应用，可以发送[OpenID connect](v2-protocols-oidc.md)身份验证请求，并从 Microsoft 标识平台终结点获取 `id_token`。

> [!IMPORTANT]
> 若要成功请求 ID 令牌和/或访问令牌，必须在 " [Azure 门户应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)" 页中注册应用注册，方法是在 "**隐式授予**" 部分下选择 " **ID 令牌**" 和 "**访问令牌**"。 如果未启用，则将返回 `unsupported_response` 错误：**此客户端不允许为输入参数 "response_type" 提供的值。预期值为 "code"**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 若要使用隐式流测试登录，请单击 " <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. "。</a>登录后，应将浏览器重定向到地址栏中的 `id_token` `https://localhost/myapp/` 。
>

| 参数 |  | Description |
| --- | --- | --- |
| `tenant` | 必填 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 允许的值为 `common`、`organizations`、`consumers` 和租户标识符。 有关更多详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | 必填 | [Azure 门户应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给应用的应用程序（客户端） ID。 |
| `response_type` | 必填 |必须包含 OpenID Connect 登录的 `id_token`。 也可以包含 response_type `token`。 此处使用 `token`，让应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。 如果使用 `token` response_type，则 `scope` 参数必须包含一个作用域，指示要为其颁发令牌的资源（例如，用户在 Microsoft Graph 上进行读取）。  |
| `redirect_uri` | 建议 |应用程序的 redirect_uri，应用程序可在此发送及接收身份验证响应。 其必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 url。 |
| `scope` | 必填 |[范围](v2-permissions-and-consent.md)的空格分隔列表。 对于 OpenID Connect （id_tokens），它必须包括范围 `openid`，这将转换为同意 UI 中的 "登录" 权限。 你还可以选择包括 `email` 和 `profile` 范围，以获取对其他用户数据的访问权限。 如果请求访问令牌，则还可以在此请求中包含其他范围，以请求同意各种资源。 |
| `response_mode` | 可选 |指定将生成的令牌送回到应用程序所应该使用的方法。 默认情况下，仅查询访问令牌，但如果请求包含 id_token，则为片段。 |
| `state` | 建议 |同样随令牌响应返回的请求中所包含的值。 它可以是你想要的任何内容的字符串。 随机生成的唯一值通常用于[防止跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 该状态也用于在身份验证请求出现之前，于应用程序中编码用户的状态信息，例如之前所在的网页或视图。 |
| `nonce` | 必填 |由应用程序生成且包含在请求中的值，以声明方式包含在生成的 id_token 中。 应用程序接着便可确认此值，以减少令牌重新执行攻击。 此值通常是随机的唯一字符串，可用以识别请求的来源。 只有请求 id_token 时才是必需的。 |
| `prompt` | 可选 |表示需要的用户交互类型。 目前的有效值为“login”、“none”、“select_account”和“consent”。 `prompt=login` 将强制用户在该请求上输入其凭据，从而使单一登录无效。 `prompt=none` 相反，它会确保用户不会看到任何交互式提示。 如果请求无法通过单一登录静默完成，Microsoft 标识平台终结点将返回错误。 `prompt=select_account` 将用户发送到一个帐户选取器，其中将显示在会话中记住的所有帐户。 `prompt=consent` 在用户登录后将触发 OAuth 同意对话框，要求用户向应用授予权限。 |
| `login_hint`  |可选 |如果事先知道其用户名称，可用于预先填充用户登录页面的用户名称/电子邮件地址字段。 通常，应用会在重新身份验证期间使用此参数，并且已经使用 `preferred_username` 声明从前次登录提取用户名。|
| `domain_hint` | 可选 |如果包括，它将跳过用户在登录页面上经历的基于电子邮件的发现过程，从而使用户体验稍微更加简洁。 这通常用于在单个租户中运行的业务线应用，他们将在给定租户中提供域名。  这会将用户转发到该租户的联合身份验证提供程序。  请注意，这会阻止来宾登录到此应用程序。  |

此时，请求用户输入其凭据并完成身份验证。 Microsoft 标识平台终结点还将确保用户已同意 `scope` 查询参数中指示的权限。 如果用户未曾同意这些权限的任何一项，就请求用户同意请求的权限。 有关详细信息，请参阅[权限、同意和多租户应用](v2-permissions-and-consent.md)。

用户进行身份验证并授予许可后，Microsoft 标识平台终结点将使用 `response_mode` 参数中指定的方法，将响应返回到指定 `redirect_uri`的应用。

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=fragment` 和 `response_type=id_token+token` 的成功响应如下所示（为方便阅读，包含了换行符）：

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 参数 | Description |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token`，则包含该参数。 应用请求的访问令牌。 不应对访问令牌进行解码或检查，而应将其视为不透明的字符串。 |
| `token_type` |如果 `response_type` 包含 `token`，则包含该参数。 始终为 `Bearer`。 |
| `expires_in`|如果 `response_type` 包含 `token`，则包含该参数。 表示令牌有效的秒数（针对缓存目的）。 |
| `scope` |如果 `response_type` 包含 `token`，则包含该参数。 表示 access_token 的有效范围。 如果不适用于用户，则不能包括请求的所有范围（如果在使用个人帐户登录时请求仅限 Azure AD 作用域）。 |
| `id_token` | 有符号 JSON Web 令牌 (JWT)。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值来获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅 [`id_token reference`](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |
| `state` |如果请求中包含状态参数，响应中就应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |

#### <a name="error-response"></a>错误响应

错误响应可能也发送到 `redirect_uri`，让应用可以适当地处理：

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 参数 | Description |
| --- | --- |
| `error` |用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

## <a name="getting-access-tokens-silently-in-the-background"></a>在后台无提示地获取访问令牌

现在，你已将用户登录到单页面应用程序，你可以通过无提示方式获取用于调用由 Microsoft 标识平台（如[Microsoft Graph](https://developer.microsoft.com/graph)）保护的 web api 的访问令牌。 即使你已使用 `token` response_type 收到令牌，也仍可以使用此方法获取其他资源的令牌，而无需再次将用户重定向到登录页。

在正常的 OpenID Connect/OAuth 流中，可以通过向 Microsoft 标识平台 `/token` 终结点发出请求来实现此目的。 但是，Microsoft 标识平台终结点不支持 CORS 请求，因此进行 AJAX 调用以获取和刷新令牌就不是问题了。 相反，可以在隐藏的 iframe 中使用隐式流，以获取其他 Web API 的新令牌： 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

有关 URL 中的查询参数的详细信息，请参阅[发送登录请求](#send-the-sign-in-request)。

> [!TIP]
> 请尝试将以下请求复制并粘贴到浏览器选项卡中！ （不要忘记使用适用于用户的正确值替换 `login_hint` 值）
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

借助 `prompt=none` 参数，此请求将立即成功或立即失败，并返回应用程序。 成功的响应会通过 `response_mode` 参数中指定的方法，发送到位于所指示的 `redirect_uri` 的应用。

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=fragment` 的成功响应如下所示：

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| 参数 | Description |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token`，则包含该参数。 应用请求的访问令牌，在本例中为 Microsoft Graph 的访问令牌。 不应对访问令牌进行解码或检查，而应将其视为不透明的字符串。 |
| `token_type` | 始终为 `Bearer`。 |
| `expires_in` | 表示令牌有效的秒数（针对缓存目的）。 |
| `scope` | 表示 access_token 的有效范围。 如果不适用于用户，则不能包括请求的所有范围（如果在使用个人帐户登录时请求仅限 Azure AD 作用域）。 |
| `id_token` | 有符号 JSON Web 令牌 (JWT)。 如果 `response_type` 包含 `id_token`，则包含该参数。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值来获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅[`id_token`参考](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |
| `state` |如果请求中包含状态参数，响应中就应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |

#### <a name="error-response"></a>错误响应

错误响应还可能发送到 `redirect_uri`，因此应用可以适当地对其进行处理。 如果是 `prompt=none`，预期的错误为：

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 参数 | Description |
| --- | --- |
| `error` |用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

如果在 iframe 请求中收到此错误，用户必须再次以交互方式登录以检索新令牌。 可以选择对应用程序合理的任何方式处理这种情况。

## <a name="refreshing-tokens"></a>刷新令牌

隐式授权不提供刷新令牌。 `id_token` 和 `access_token` 很快就会过期，因此应用必须准备好定期刷新这些令牌。 若要刷新任一类型的令牌，可以使用 `prompt=none` 参数执行相同的隐藏 iframe 请求，以控制标识平台的行为。 如果要接收新 `id_token`，请确保使用 `response_type` 和 `scope=openid`中的 `id_token` 以及 `nonce` 参数。

## <a name="send-a-sign-out-request"></a>发送注销请求

OpenID Connect `end_session_endpoint` 允许你的应用程序将请求发送到 Microsoft 标识平台终结点，以结束用户会话并清除 Microsoft 标识平台终结点设置的 cookie。 要完全将用户从 Web 应用程序注销，应用应结束自己与用户的会话（通常通过清除令牌缓存或删除 Cookie 实现），然后将浏览器重定向到：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 参数 |  | Description |
| --- | --- | --- |
| `tenant` |必填 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 允许的值为 `common`、`organizations`、`consumers` 和租户标识符。 有关更多详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| `post_logout_redirect_uri` | 建议 | 注销完成后用户应返回到的 URL。 此值必须与为应用程序注册的重定向 URI 之一匹配。 如果不包含，则 Microsoft 标识平台终结点会向用户显示一条一般消息。 |

## <a name="next-steps"></a>后续步骤

* 重温 [MSAL JS 示例](sample-v2-code.md)以开始编码。
