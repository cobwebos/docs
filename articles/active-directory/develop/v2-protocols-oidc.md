---
title: Microsoft 标识平台和 OpenID Connect 协议 |Microsoft
titleSuffix: Microsoft identity platform
description: 使用 OpenID Connect 身份验证协议的 Microsoft 标识平台实现生成 Web 应用程序。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 88f647bbb72c92db194407b677e533a867261ce4
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926487"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft 标识平台和 OpenID Connect 协议

OpenID Connect （OIDC）是一种在 OAuth 2.0 上构建的身份验证协议，可用于将用户安全登录到 web 应用程序。 使用 Microsoft 标识平台终结点的 OpenID Connect 实现时，可将登录功能和 API 访问权限添加到基于 Web 的应用中。 本文介绍如何独立于语言执行此操作，并介绍如何在不使用任何 Microsoft 开源库的情况下发送和接收 HTTP 消息。

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 扩展了 OAuth 2.0 *授权*协议，使其可用作*身份验证*协议，这样一来，用户可使用 OAuth 执行单一登录。 OpenID Connect 引入了 *ID 令牌*的概念，这是一种安全令牌，可让客户端验证用户的标识。 ID 令牌还可获取有关用户的基本配置文件信息。 由于 OpenID Connect 扩展了 OAuth 2.0，因此应用可安全获取访问令牌，访问令牌可用于访问[授权服务器](active-directory-v2-protocols.md#the-basics)保护的资源。  Microsoft 标识平台终结点还允许注册到 Azure AD 的第三方应用颁发受保护资源（例如 Web API）的访问令牌。 有关如何设置应用程序以颁发访问令牌的详细信息，请参阅[如何向 Microsoft 标识平台终结点注册应用](quickstart-register-app.md)。 如果要构建在服务器上托管并通过浏览器访问的 [Web 应用程序](v2-app-types.md#web-apps)，建议使用 OpenID Connect。

## <a name="protocol-diagram-sign-in"></a>协议图：登录

最基本的登录流包含下图中所示的步骤。 本文将详细介绍每个步骤。

![OpenID Connect 协议：登录](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>提取 OpenID Connect 元数据文档

OpenID Connect 描述了元数据文档，该文档包含了应用执行登录所需的大部分信息。 这些信息包括要使用的 URL 以及服务公共签名密钥的位置。 对于 Microsoft 标识平台终结点，应使用的 OpenID Connect 的元数据文档为：

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 试试看！ 单击 [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) 可查看 `common` 租户配置。

`{tenant}` 可取以下四个值之一：

| 值 | 说明 |
| --- | --- |
| `common` |同时拥有个人 Microsoft 帐户和工作或学校帐户的用户 Azure AD 可以登录到应用程序。 |
| `organizations` |只有在 Azure AD 中具有工作或学校帐户的用户才能登录到应用程序。 |
| `consumers` |仅拥有 Microsoft 个人帐户的用户可以登录到应用程序。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` | 只有特定 Azure AD 租户中的用户（无论是使用工作或学校帐户的目录中的成员还是具有个人 Microsoft 帐户的目录中的来宾），才能登录到应用程序。 可以使用 Azure AD 租户的友好域名或租户的 GUID 标识符。 也可以使用使用者租户 `9188040d-6c67-4c5b-b112-36a304b66dad` 来取代 `consumers` 租户。  |

元数据是简单的 JavaScript 对象表示法 (JSON) 文档。 有关示例，请参阅以下代码片段。 [OpenID Connect 规范](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)中完整介绍了该代码片段的内容。

```json
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/{tenant}\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/{tenant}\/discovery\/v2.0\/keys",

  ...

}
```

如果应用因使用[声明映射](active-directory-claims-mapping.md)功能而具有自定义签名密钥，则必须追加包含应用 ID 的 `appid` 查询参数，以获取指向应用的签名密钥信息的 `jwks_uri`。 例如：`https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含 `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 的 `jwks_uri`。

通常，使用此元数据文档来配置 OpenID Connect 库或 SDK；该库使用元数据来完成其工作。 但是，如果不使用预生成的 OpenID Connect 库，则可以按照本文剩余部分的步骤来使用 Microsoft 标识平台终结点执行 Web 应用中的登录。

## <a name="send-the-sign-in-request"></a>发送登录请求

当 Web 应用需要对用户进行身份验证时，可以将用户定向到 `/authorize` 终结点。 此请求类似于 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)的第一个阶段，但有以下几个重要区别：

* 该请求必须在 `scope` 参数中包含 `openid` 范围。
* `response_type` 参数必须包含 `id_token`。
* 请求必须在 `nonce` 。

> [!IMPORTANT]
> 若要从 /authorization 终结点成功请求 ID 令牌，[注册门户](https://portal.azure.com)中的应用注册必须在“身份验证”选项卡中启用 id_token 的隐式授予（将[应用程序清单](reference-app-manifest.md)中的 `oauth2AllowIdTokenImplicitFlow` 标志设置为 `true`）。 如果未启用，将返回 `unsupported_response` 错误：“为输入参数 'response_type' 提供的值不允许用于此客户端。 预期值为“code””

例如：

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> 单击以下链接执行此请求！ 登录后，浏览器将重定向到 `https://localhost/myapp/`，且地址栏中有一个 ID 令牌。 请注意，此请求使用 `response_mode=fragment`（仅用于演示）。 建议使用 `response_mode=form_post`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| `tenant` | 必须 | 可以在请求路径中使用 `{tenant}` 值来控制谁可以登录到应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参见[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | 必需 | Azure 门户的**应用程序（客户端） ID** [-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给应用程序的体验。 |
| `response_type` | 必需 | 必须包含 OpenID Connect 登录的 `id_token` 。 还可能包含其他 `response_type` 值，例如 `code`。 |
| `redirect_uri` | 建议 | 应用的重定向 URI，应用可在其中发送和接收身份验证响应。 必须完全符合在门户中注册的重定向 URI 之一，否则必须是编码的 URL。 如果不存在该 URL，终结点将随机选取一个已注册的 redirect_uri，以将用户发回到其中。 |
| `scope` | 必需 | 范围的空格分隔列表。 对于 OpenID Connect，它必须包含范围 `openid`，该范围在同意 UI 中会转换为“将你登录”权限。 也可以在此请求中包含其他范围来请求许可。 |
| `nonce` | 必需 | 由应用程序生成且包含在请求中的值，以声明方式包含在生成的 id_token 值中。 应用可以验证此值，以缓解令牌重放攻击。 该值通常是随机化的唯一字符串，可用于标识请求的来源。 |
| `response_mode` | 建议 | 指定将生成的授权代码发回给应用时应该使用的方法。 可以是 `form_post` 或 `fragment`。 对于 Web 应用程序，建议使用 `response_mode=form_post`，确保以最安全的方式将令牌传输到应用程序。 |
| `state` | 建议 | 同样随令牌响应返回的请求中所包含的值。 可以是所需的任何内容的字符串。 随机生成的唯一值通常用于 [防范跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 该状态还用于在身份验证请求出现之前，在应用中编码用户的状态信息，例如用户过去所在的页面或视图。 |
| `prompt` | 可选 | 表示需要的用户交互类型。 目前仅有的有效值为 `login`、`none` 和 `consent`。 `prompt=login` 声明强制用户在该请求上输入凭据，从而使单一登录无效。 而 `prompt=none` 声明完全相反。 此声明确保不会向用户显示任何交互提示。 如果请求无法通过单一登录以无提示方式完成，则 Microsoft 标识平台终结点将返回一个错误。 `prompt=consent` 声明将在用户登录后触发 OAuth 同意对话框。 该对话框要求用户向应用授予权限。 |
| `login_hint` | 可选 | 如果事先知道用户名，可以使用此参数预先填充用户登录页的用户名/电子邮件地址字段。 通常，应用在已经使用 `preferred_username` 声明从前次登录提取用户名后，会在重新身份验证时使用此参数。 |
| `domain_hint` | 可选 | 联合目录中的用户领域。  这会跳过用户在登录页面上经历的基于电子邮件的发现过程，从而实现更加流畅的用户体验。 对于通过本地目录（例如 AD FS）联合的租户，这通常会由于现有登录会话而导致无缝登录。 |

此时，系统会提示用户输入凭据并完成身份验证。 Microsoft 标识平台终结点将验证用户是否已许可 `scope` 查询参数中指定的权限。 如果用户未许可其中的任何权限，Microsoft 标识平台终结点会提示用户许可所需的权限。 阅读有关[权限、许可与多租户应用](v2-permissions-and-consent.md)的详细信息。

用户经过身份验证并许可后，Microsoft 标识平台终结点会使用 `response_mode` 参数中指定的方法，将响应返回到位于指定重定向 URI 处的应用。

### <a name="successful-response"></a>成功的响应

使用 `response_mode=form_post` 时的成功响应如下所示：

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 参数 | 说明 |
| --- | --- |
| `id_token` | 应用请求的 ID 令牌。 可以使用 `id_token` 参数验证用户的标识，开始与用户建立会话。 有关 ID 令牌及其内容的详细信息，请参阅 [`id_tokens` 参考](id-tokens.md)。 |
| `state` | 如果请求中包含 `state` 参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

### <a name="error-response"></a>错误响应

也可以将错误响应发送到重定向 URI，使应用能够处理这些响应。 错误响应如下所示：

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| --- | --- |
| `error` | 可用于对发生的错误分类以及对错误做出反应的错误代码字符串。 |
| `error_description` | 帮助识别身份验证错误根本原因的特定错误消息。 |

### <a name="error-codes-for-authorization-endpoint-errors"></a>授权终结点错误的错误代码

下表描述了可在错误响应的 `error` 参数中返回的错误代码：

| 错误代码 | 说明 | 客户端操作 |
| --- | --- | --- |
| `invalid_request` | 协议错误，例如，缺少必需的参数。 |修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 |
| `unauthorized_client` | 客户端应用程序无法请求授权代码。 |客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现这种情况。 应用程序可以提示用户，说明如何安装应用程序并将其添加到 Azure AD。 |
| `access_denied` | 资源所有者拒绝许可。 |客户端应用程序可以通知用户，除非用户许可，否则无法继续。 |
| `unsupported_response_type` |授权服务器不支持请求中的响应类型。 |修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 |
| `server_error` | 服务器遇到意外的错误。 |重试请求。 这些错误可能是临时状况导致的。 客户端应用程序可向用户说明，其响应由于临时错误而延迟。 |
| `temporarily_unavailable` | 服务器暂时繁忙，无法处理请求。 |重试请求。 客户端应用程序可向用户说明，其响应由于临时状况而延迟。 |
| `invalid_resource` | 目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 |这表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，说明如何安装应用程序并将其添加到 Azure AD。 |

## <a name="validate-the-id-token"></a>验证 ID 令牌

仅接收 id_token 不足以对用户进行身份验证；必须验证 id_token 的签名，并按照应用的要求验证令牌中的声明。 Microsoft 标识平台终结点使用 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。

可以选择验证客户端代码中的 `id_token`，但常见的做法是将 `id_token` 发送到后端服务器，并在那里执行验证。 验证 id_token 的签名后，需要验证一些声明。 有关详细信息，请参阅 [`id_token` 参考](id-tokens.md)，其中包括[验证令牌](id-tokens.md#validating-an-id_token)和[有关签名密钥滚动更新的重要信息](active-directory-signing-key-rollover.md)。 我们建议利用库来分析和验证令牌 - 对于大多数语言和平台至少有一个可用。

可能还希望根据自己的方案验证其他声明。 一些常见的验证包括：

* 确保用户/组织已注册应用。
* 确保用户拥有正确的授权/权限
* 确保身份验证具有一定的强度，例如多重身份验证。

验证 id_token 后，可以开始与用户的会话，并使用 id_token 中的声明来获取应用中的用户相关信息。 此信息可用于显示、记录和个性化等。

## <a name="send-a-sign-out-request"></a>发送注销请求

如果希望将用户从应用中注销，仅仅是清除应用的 Cookie 或结束用户会话并不足够。 还必须将用户重定向到 Microsoft 标识平台终结点才能注销。如果不这样做，用户不需要再次输入凭据就能重新通过应用的身份验证，因为他们与 Microsoft 标识平台终结点之间存在有效的单一登录会话。

可以将用户重定向到 OpenID Connect 元数据文档中所列的 `end_session_endpoint`：

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| 参数 | 条件 | 说明 |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | 建议 | 用户在成功注销后将重定向到的 URL。如果不包括参数，将向用户显示一条 Microsoft 标识平台终结点生成的常规消息。 此 URL 必须与在应用注册门户中为应用程序注册的重定向 URI 之一匹配。 |

## <a name="single-sign-out"></a>单一登录

将用户重定向到 `end_session_endpoint` 时，Microsoft 标识平台终结点将从浏览器中清除用户的会话。 但是，用户可能仍登录到其他使用 Microsoft 帐户进行身份验证的应用程序。 要使这些应用程序能够同时注销用户，Microsoft 标识平台终结点会将 HTTP GET 请求发送到用户当前登录到的所有应用程序的注册 `LogoutUrl`。 应用程序必须通过清除任何标识用户的会话并返回 `200` 响应来响应此请求。 如果要在应用程序中支持单一注销，必须在应用程序代码中实现此类 `LogoutUrl`。 可以从应用注册门户设置 `LogoutUrl`。

## <a name="protocol-diagram-access-token-acquisition"></a>协议图：访问令牌获取

许多 Web 应用不仅需要将用户登录，而且还要代表该用户使用 OAuth 来访问 Web 服务。 此方案合并了用于对用户进行身份验证的 OpenID Connect，同时会获取授权代码，用于通过 OAuth 授权代码流来获取访问令牌。

完整的 OpenID Connect 登录和令牌获取流如下图所示。 本文的后续部分详细介绍每个步骤。

![OpenID Connect 协议：令牌获取](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-access-tokens"></a>获取访问令牌
若要获取访问令牌，请修改登录请求：

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs
offline_access%20
https%3A%2F%2Fgraph.microsoft.com%2Fuser.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> 单击以下链接执行此请求！ 登录后，浏览器将重定向到 `https://localhost/myapp/`，且地址栏中有一个 ID 令牌和一个代码。 请注意，此请求使用 `response_mode=fragment`（仅用于演示）。 建议使用 `response_mode=form_post`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

通过使用 `response_type=id_token code` 在请求中包含权限范围，Microsoft 标识平台终结点可确保用户已经许可 `scope` 查询参数中指示的权限。 v2.0 终结点会将授权代码返回给应用，以交换访问令牌。

### <a name="successful-response"></a>成功的响应

使用 `response_mode=form_post` 后的成功响应如下所示：

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 参数 | 说明 |
| --- | --- |
| `id_token` | 应用请求的 ID 令牌。 可以使用 ID 令牌验证用户的标识，开始与用户建立会话。 有关 ID 令牌及其内容的详细信息，请参阅 [`id_tokens` 参考](id-tokens.md)。 |
| `code` | 应用请求的授权代码。 应用可以使用授权代码请求目标资源的访问令牌。 授权代码的生存期较短。 通常，授权代码在大约 10 分钟后即会过期。 |
| `state` | 如果请求中包含 state 参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

### <a name="error-response"></a>错误响应

也可以将错误响应发送到重定向 URI，使应用能够适当地处理这些响应。 错误响应如下所示：

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| --- | --- |
| `error` | 可用于对发生的错误分类以及对错误做出反应的错误代码字符串。 |
| `error_description` | 帮助识别身份验证错误根本原因的特定错误消息。 |

有关可能的错误代码和建议的客户端操作的说明，请参阅 [授权终结点错误的错误代码](#error-codes-for-authorization-endpoint-errors)。

获取授权代码和 ID 令牌之后，可将用户登录，并代表用户获取访问令牌。 要将用户登录，必须 [完全根据说明](id-tokens.md#validating-an-id_token)验证 ID 令牌。 若要获取访问令牌，请遵循 [OAuth 代码流文档](v2-oauth2-auth-code-flow.md#request-an-access-token)中所述的步骤。
