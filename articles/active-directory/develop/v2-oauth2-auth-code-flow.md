---
title: Microsoft 标识平台和 OAuth 2.0 授权代码流 |Microsoft
titleSuffix: Microsoft identity platform
description: 使用 OAuth 2.0 身份验证协议的 Microsoft 标识平台实现生成 Web 应用程序。
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
ms.openlocfilehash: 29720b338326a29e65af1b6564cb0b59a976c62c
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926436"
---
# <a name="microsoft-identity-platform-and-oauth-20-authorization-code-flow"></a>Microsoft 标识平台和 OAuth 2.0 授权代码流

OAuth 2.0 授权代码授予可用于设备上所安装的应用，以访问受保护的资源，例如 Web API。 使用 OAuth 2.0 的 Microsoft 标识平台实现，可将登录名及 API 访问添加到移动应用和桌面应用。 本指南与语言无关，介绍在不使用任何 [Azure 开放源代码身份验证库](reference-v2-libraries.md)的情况下，如何发送和接收 HTTP 消息。

本文介绍如何在应用程序中直接针对协议进行编程。  如果可能，建议你改用受支持的 Microsoft 身份验证库 (MSAL) 来[获取令牌并调用受保护的 Web API](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)。  另请参阅[使用 MSAL 的示例应用](sample-v2-code.md)。

[OAuth 2.0 规范第 4.1 部分](https://tools.ietf.org/html/rfc6749)描述了 OAuth 2.0 授权代码流。 它用于在大多数应用程序类型（包括[web 应用](v2-app-types.md#web-apps)和[本机安装的应用](v2-app-types.md#mobile-and-native-apps)）中执行身份验证和授权。 此 OAuth 流使应用程序可以安全地获取 access_tokens，这些应用程序可用于访问由 Microsoft 标识平台终结点保护的资源。

## <a name="protocol-diagram"></a>协议图

在高级别上，本机/移动应用程序的整个 OAuth2 身份验证流看起来有点类似于：

![OAuth 授权代码流](./media/v2-oauth2-auth-code-flow/convergence-scenarios-native.svg)

## <a name="request-an-authorization-code"></a>请求授权代码

授权代码流始于客户端将用户定向到 `/authorize` 终结点。 在此请求中，客户端请求`openid`用户`offline_access`的、 `https://graph.microsoft.com/mail.read `和权限。  某些权限受管理员限制，例如使用 `Directory.ReadWrite.All` 将数据写入组织的目录。 如果应用程序向组织用户请求访问以下权限之一，用户会收到错误消息，指出他们未经授权，无法许可应用的权限。 若要请求访问受管理员限制的范围，应该直接向公司管理员请求。  有关详细信息，请参阅[管理员限制的权限](v2-permissions-and-consent.md#admin-restricted-permissions)。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> 单击下面的链接以执行此请求！ 登录之后，浏览器应重定向至地址栏中具有 `code` 的 `https://localhost/myapp/`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| 参数    | 必需/可选 | 说明 |
|--------------|-------------|--------------|
| `tenant`    | 必填    | 请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。  |
| `client_id`   | 必填    | Azure 门户的**应用程序（客户端） ID** [-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给应用程序的体验。  |
| `response_type` | 必需    | 必须包括授权代码流的 `code` 。       |
| `redirect_uri`  | 必需 | 应用的 redirect_uri，应用可向其发送及从其接收身份验证响应。 它必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 url。 对于本机和移动应用，应使用默认值 `https://login.microsoftonline.com/common/oauth2/nativeclient`。   |
| `scope`  | 必需    | 希望用户同意的[范围](v2-permissions-and-consent.md)的空格分隔列表。  对于请求的 `/authorize` 段，这可以涵盖多个资源，从而允许应用获得你要调用的多个 Web API 的同意。 |
| `response_mode`   | 建议 | 指定将生成的令牌送回到应用程序时应该使用的方法。 可以是以下值之一：<br/><br/>- `query`<br/>- `fragment`<br/>- `form_post`<br/><br/>`query` 在重定向 URI 上提供代码作为查询字符串参数。 如果要使用隐式流请求 ID 令牌，则不能使用 [OpenID 规范](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations)中指定的 `query`。如果只是请求代码，则可以使用 `query`、`fragment` 或 `form_post`。 `form_post` 对重定向 URI 执行包含代码的 POST。 有关详细信息，请参阅 [OpenID Connect 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)。  |
| `state`                 | 建议 | 同样随令牌响应返回的请求中所包含的值。 可以是想要的任何内容的字符串。 随机生成的唯一值通常用于 [防止跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 在发出身份验证请求出现之前，此值对有关用户在应用中的状态的信息（例如前面所在的页面或视图）进行编码。 |
| `prompt`  | 可选    | 表示需要的用户交互类型。 目前仅有的有效值为 `login`、`none` 和 `consent`。<br/><br/>- `prompt=login` 将强制用户在该请求上输入其凭据，从而使单一登录无效。<br/>- `prompt=none` 则相反 - 它确保不对用户显示任何交互式提示。 如果请求无法通过单一登录静默完成，则 Microsoft 标识平台终结点将返回 `interaction_required` 错误。<br/>- `prompt=consent` 在用户登录后将触发 OAuth 同意对话框，要求用户向应用授予权限。<br/>- `prompt=select_account` 将中断单一登录，提供帐户选择体验（列出所有帐户（会话中的帐户或任何记住的帐户），或提供用于选择使用其他帐户的选项）。<br/> |
| `login_hint`  | 可选    | 如果事先知道用户名，可用于预先填充用户登录页的用户名/电子邮件地址字段。 通常，应用会在重新身份验证期间使用此参数，并且已经使用 `preferred_username` 声明从前次登录提取用户名。   |
| `domain_hint`  | 可选    | 可以是 `consumers` 或 `organizations` 之一。<br/><br/>如果包含，它跳过用户在登录页上经历的基于电子邮件的发现过程，导致稍微更加流畅的用户体验。 通常，应用将在重新身份验证期间使用此参数，方法是从上次登录提取 `tid`。 如果 `tid` 声明值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，应该使用 `domain_hint=consumers`。 否则使用 `domain_hint=organizations`。  |
| `code_challenge_method` | 可选    | 用于为 `code_challenge` 参数编码 `code_verifier` 的方法。 可以是以下值之一：<br/><br/>- `plain` <br/>- `S256`<br/><br/>如果已排除在外，且包含了 `code_challenge`，则假定 `code_challenge` 为纯文本。 Microsoft 标识平台支持 `plain` 和 `S256`。 有关详细信息，请参阅 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |
| `code_challenge`  | 可选 | 用于通过本地客户端的 Proof Key for Code Exchange (PKCE) 保护授权代码授权。 如果包含 `code_challenge_method`，则需要。 有关详细信息，请参阅 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |

此时，将请求用户输入凭据并完成身份验证。 Microsoft 标识平台终结点还会确保用户已许可 `scope` 查询参数中指定的权限。 如果用户未曾同意这些权限的任何一项，则请求用户同意请求的权限。 [此处提供了权限、许可与多租户应用](v2-permissions-and-consent.md)的详细信息。

用户经过身份验证并授予许可后，Microsoft 标识平台终结点将使用 `response_mode` 参数中指定的方法，将响应返回到位于所指示的 `redirect_uri` 的应用。

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=query` 的成功响应如下所示：

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 参数 | 说明  |
|-----------|--------------|
| `code` | 应用请求的 authorization_code。 应用可以使用授权代码请求目标资源的访问令牌。 Authorization_codes 的生存期较短，通常在约 10 分钟后即过期。 |
| `state` | 如果请求中包含 state 参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

#### <a name="error-response"></a>错误响应

错误响应可能也发送到 `redirect_uri` ，让应用可以适当地处理：

```HTTP
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 参数 | 描述  |
|----------|------------------|
| `error`  | 可用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` | 帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>授权终结点错误的错误代码

下表描述了可在错误响应的 `error` 参数中返回的各个错误代码。

| 错误代码  | 说明    | 客户端操作   |
|-------------|----------------|-----------------|
| `invalid_request` | 协议错误，例如，缺少必需的参数。 | 修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 |
| `unauthorized_client` | 不允许客户端应用程序请求授权代码。 | 客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现此错误。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| `access_denied`  | 资源所有者拒绝了许可  | 客户端应用程序可以通知用户，除非用户许可，否则无法继续。 |
| `unsupported_response_type` | 授权服务器不支持请求中的响应类型。 | 修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。  |
| `server_error`  | 服务器遇到意外的错误。| 重试请求。 这些错误可能是临时状况导致的。 客户端应用程序可能向用户说明，其响应由于临时错误而延迟。 |
| `temporarily_unavailable`   | 服务器暂时繁忙，无法处理请求。 | 重试请求。 客户端应用程序可向用户说明，其响应由于临时状况而延迟。 |
| `invalid_resource`  | 目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 | 此错误表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| `login_required` | 找到的用户太多或未找到任何用户 | 客户端请求了静默身份验证 (`prompt=none`)，但找不到单个用户。 这可能表示会话中有多个处于活动状态的用户或无用户。 此时会考虑所选的租户（例如，如果有两个处于活动状态的 Azure AD 帐户和一个 Microsoft 帐户，并且已选择 `consumers`，则会执行静默身份验证）。 |
| `interaction_required` | 请求需要用户交互。 | 需要额外的身份验证步骤或许可。 请在没有 `prompt=none` 的情况下重试请求。 |

## <a name="request-an-access-token"></a>请求访问令牌

现在已获取 authorization_code 并获得用户授权，可兑换 `code` 以获取所需资源的 `access_token`。 通过向 `/token` 终结点发送 `POST` 请求来完成此操作：

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps. This secret needs to be URL-Encoded.
```

> [!TIP]
> 尝试在 Postman 中执行此请求！ （请不要忘记替换 `code`）[![尝试在 Postman 中运行此请求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

| 参数  | 必需/可选 | 说明     |
|------------|-------------------|----------------|
| `tenant`   | 必填   | 请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。  |
| `client_id` | 必填  | [Azure 门户–应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给应用的应用程序（客户端） ID。 |
| `grant_type` | 必需   | 必须是授权代码流的 `authorization_code` 。   |
| `scope`      | 必需   | 范围的空格分隔列表。 在此阶段请求的范围必须相当于或为第一个阶段中所请求的范围子集。 范围必须全部来自单个资源，以及 OIDC范围（`profile`、`openid`、`email`）。 有关范围更加详细的说明，请参阅[权限、许可和范围](v2-permissions-and-consent.md)。 |
| `code`          | 必需  | 在流的第一个阶段中获取的 authorization_code。 |
| `redirect_uri`  | 必需  | 用于获取 authorization_code 的相同 redirect_uri 值。 |
| `client_secret` | 必需（对于 Web 应用） | 在应用注册门户中为应用创建的应用程序机密。 不应在本机应用中使用应用程序机密，因为 client_secrets 不能可靠地存储在设备上。 Web 应用和 Web API 都需要应用程序机密，能够将 client_secret 安全地存储在服务器端。  在发送客户端密码之前必须对其进行 URL 编码。 有关详细信息，请参阅[URI 一般语法规范](https://tools.ietf.org/html/rfc3986#page-12)。 |
| `code_verifier` | 可选  | 即用于获取 authorization_code 的 code_verifier。 如果在授权码授权请求中使用 PKCE，则需要。 有关详细信息，请参阅 [PKCE RFC](https://tools.ietf.org/html/rfc7636)。 |

### <a name="successful-response"></a>成功的响应

成功的令牌响应如下：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 参数     | 说明   |
|---------------|------------------------------|
| `access_token`  | 请求的访问令牌。 应用可以使用此令牌验证受保护的资源，例如 Web API。  |
| `token_type`    | 指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer |
| `expires_in`    | 访问令牌的有效期（以秒为单位）。 |
| `scope`         | access_token 的有效范围。 |
| `refresh_token` | OAuth 2.0 刷新令牌。 应用可以使用此令牌，在当前的访问令牌过期之后获取其他访问令牌。 Refresh_tokens 的生存期很长，而且可以用于延长保留资源访问权限的时间。 有关刷新访问令牌的详细信息，请参阅[以下部分](#refresh-the-access-token)。 <br> **注意：** 仅当已请求 `offline_access` 作用域时提供。 |
| `id_token`      | JSON Web 令牌 (JWT)。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅 [`id_token reference`](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |

### <a name="error-response"></a>错误响应

错误响应如下所示：

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数         | 描述    |
|-------------------|----------------|
| `error`       | 可用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` | 帮助开发人员识别身份验证错误根本原因的特定错误消息。 |
| `error_codes` | 可帮助诊断的 STS 特定错误代码列表。  |
| `timestamp`   | 发生错误的时间。 |
| `trace_id`    | 可帮助诊断的请求唯一标识符。 |
| `correlation_id` | 可帮助跨组件诊断的请求唯一标识符。 |

### <a name="error-codes-for-token-endpoint-errors"></a>令牌终结点错误的错误代码

| 错误代码         | 说明        | 客户端操作    |
|--------------------|--------------------|------------------|
| `invalid_request`  | 协议错误，例如，缺少必需的参数。 | 修复并重新提交请求。   |
| `invalid_grant`    | 授权代码或 PKCE 代码验证程序无效或已过期。 | 尝试向 `/authorize` 终结点发送新请求，并验证 code_verifier 参数是否正确。  |
| `unauthorized_client` | 经过身份验证的客户端无权使用此权限授予类型。 | 客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现这种情况。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| `invalid_client` | 客户端身份验证失败。  | 客户端凭据无效。 若要修复，应用程序管理员应更新凭据。   |
| `unsupported_grant_type` | 授权服务器不支持权限授予类型。 | 更改请求中的授权类型。 这种类型的错误应该只在开发过程中发生，并且应该在初始测试过程中检测到。 |
| `invalid_resource` | 目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 | 这表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。  |
| `interaction_required` | 请求需要用户交互。 例如，需要额外的身份验证步骤。 | 使用同一资源重试请求。  |
| `temporarily_unavailable` | 服务器暂时繁忙，无法处理请求。 | 重试请求。 客户端应用程序可向用户说明，其响应由于临时状况而延迟。 |

## <a name="use-the-access-token"></a>使用访问令牌

现已成功获取 `access_token`，可以通过在 `Authorization` 标头中包含令牌，在 Web API 的请求中使用令牌：

> [!TIP]
> 在 Postman 中执行此请求！ （先替换 `Authorization` 标头）[![尝试在 Postman 中运行此请求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>刷新访问令牌

Access_token 生存期很短，必须在其过期后刷新，才能继续访问资源。 为此，可以向 `/token` 终结点提交另一个 `POST` 请求，但这次要提供 `refresh_token` 而不是 `code`。  刷新令牌对客户端已获得同意的所有权限有效 - 因此，对 `scope=mail.read` 请求发出的刷新令牌可用于请求 `scope=api://contoso.com/api/UseResource` 的新访问令牌。

刷新令牌没有指定的生存期。 通常，刷新令牌的生存期相对较长。 但是，在某些情况下，刷新令牌会过期、被吊销，或缺少执行所需操作的足够权限。 应用程序需要正确预期和处理[令牌颁发终结点返回的错误](#error-codes-for-token-endpoint-errors)。

尽管刷新令牌在用于获取新访问令牌时不会被吊销，但预期你会丢弃旧的刷新令牌。 [OAuth 2.0 规范](https://tools.ietf.org/html/rfc6749#section-6)指出：“授权服务器可能会发出新的刷新令牌，在这种情况下，客户端必须丢弃旧的刷新令牌，并将其替换为新的刷新令牌。 授权服务器在向客户端颁发新的刷新令牌后，可能会吊销旧的刷新令牌。”

```HTTP
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps. This secret needs to be URL-Encoded
```

> [!TIP]
> 尝试在 Postman 中执行此请求！ （请不要忘记替换 `refresh_token`）[![尝试在 Postman 中运行此请求](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)
>

| 参数     |                | 说明        |
|---------------|----------------|--------------------|
| `tenant`        | 必填     | 请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。   |
| `client_id`     | 必填    | Azure 门户的**应用程序（客户端） ID** [-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)分配给应用程序的体验。 |
| `grant_type`    | 必需    | 必须是授权代码流的此阶段的 `refresh_token` 。 |
| `scope`         | 必需    | 范围的空格分隔列表。 在此阶段请求的范围必须等效于原始 authorization_code 请求阶段中所请求的范围，或者为该范围的子集。 如果这个请求中指定的范围遍及多个资源服务器，Microsoft 标识平台终结点将返回第一个范围内所指定资源的令牌。 有关范围更加详细的说明，请参阅[权限、许可和范围](v2-permissions-and-consent.md)。 |
| `refresh_token` | 必需    | 在流的第二个阶段获取的 refresh_token。 |
| `client_secret` | 必需（对于 Web 应用） | 在应用注册门户中为应用创建的应用程序机密。 它不应用于本机应用，因为设备无法可靠地存储 client_secrets。 Web 应用和 Web API 都需要应用程序机密，能够将 client_secret 安全地存储在服务器端。 此密钥需要 URL 编码。 有关详细信息，请参阅[URI 一般语法规范](https://tools.ietf.org/html/rfc3986#page-12)。 |

#### <a name="successful-response"></a>成功的响应

成功的令牌响应如下：

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```

| 参数     | 说明         |
|---------------|-------------------------------------------------------------|
| `access_token`  | 请求的访问令牌。 应用可以使用此令牌验证受保护的资源，例如 Web API。 |
| `token_type`    | 指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer |
| `expires_in`    | 访问令牌的有效期（以秒为单位）。   |
| `scope`         | access_token 的有效范围。    |
| `refresh_token` | 新的 OAuth 2.0 刷新令牌。 应该将旧刷新令牌替换为新获取的这个刷新令牌，以确保刷新令牌的有效期尽可能地长。 <br> **注意：** 仅当已请求 `offline_access` 作用域时提供。|
| `id_token`      | 无符号 JSON Web 令牌 (JWT)。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅 [`id_token reference`](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |

#### <a name="error-response"></a>错误响应

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数         | 说明                                                                                        |
|-------------------|----------------------------------------------------------------------------------------------------|
| `error`           | 可用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` | 帮助开发人员识别身份验证错误根本原因的特定错误消息。           |
| `error_codes` |可帮助诊断的 STS 特定错误代码列表。 |
| `timestamp` | 发生错误的时间。 |
| `trace_id` | 可帮助诊断的请求唯一标识符。 |
| `correlation_id` | 可帮助跨组件诊断的请求唯一标识符。 |

有关错误代码的描述和建议的客户端操作，请参阅 [令牌终结点错误的错误代码](#error-codes-for-token-endpoint-errors)。
