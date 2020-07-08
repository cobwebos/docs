---
title: Microsoft 标识平台和 OpenID Connect 协议 | Azure
titleSuffix: Microsoft identity platform
description: 使用 OpenID Connect 身份验证协议的 Microsoft 标识平台实现来生成 Web 应用。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 741e7a13513d571fbaabd17016b2282a860271cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84263272"
---
# <a name="microsoft-identity-platform-and-openid-connect-protocol"></a>Microsoft 标识平台和 OpenID Connect 协议

OpenID Connect （OIDC）是一种在 OAuth 2.0 上构建的身份验证协议，可用于将用户安全登录到应用程序。 当你使用 Microsoft 标识平台终结点的 OpenID Connect 实现时，你可以将登录和 API 访问权限添加到你的应用。 本文介绍如何独立于语言执行此操作，并说明如何在不使用任何[Microsoft 开源库](reference-v2-libraries.md)的情况下发送和接收 HTTP 消息。

[OpenID connect](https://openid.net/specs/openid-connect-core-1_0.html)扩展了 oauth 2.0*授权*协议以用作*身份验证*协议，因此你可以使用 OAuth 执行单一登录。 OpenID Connect 引入了 ID 令牌的概念，ID 令牌是一种可让客户端验证用户标识的安全令牌。 ID 令牌还可获取用户的基本个人资料信息。 它还引入了用户信息[终结点，该终结点](userinfo.md)将返回有关用户的信息。 


## <a name="protocol-diagram-sign-in"></a>协议图：登录

最基本的登录流程步骤如下图所示。 本文将详细介绍每个步骤。

![OpenID Connect 协议：登录](./media/v2-protocols-oidc/convergence-scenarios-webapp.svg)

## <a name="fetch-the-openid-connect-metadata-document"></a>提取 OpenID Connect 元数据文档

OpenID Connect 描述了元数据文档[（RFC）](https://openid.net/specs/openid-connect-discovery-1_0.html) ，其中包含应用进行登录所需的大多数信息。 这包括要使用的 URL 和服务的公共签名密钥的位置等信息。 可以通过将发现文档路径追加到颁发机构 URL 来找到此文档：

发现文档路径：`/.well-known/openid-configuration`

无权`https://login.microsoftonline.com/{tenant}/v2.0`

`{tenant}` 可采用四个值的其中之一：

| 值 | 描述 |
| --- | --- |
| `common` |同时拥有 Microsoft 个人帐户和 Azure AD 中的工作或学校帐户的用户可以登录应用。 |
| `organizations` |仅拥有工作/学校帐户的用户可以从 Azure AD 登录到应用程序。 |
| `consumers` |仅拥有 Microsoft 个人帐户的用户可以登录到应用程序。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` | 只有特定 Azure AD 租户中的用户（无论是目录中拥有工作或学校帐户的成员，还是目录中拥有 Microsoft 个人帐户的来宾），才能登录应用。 可以使用 Azure AD 租户的友好域名或租户的 GUID 标识符。 还可以使用使用者租户 `9188040d-6c67-4c5b-b112-36a304b66dad` 来代替 `consumers` 租户。  |

此机构不同于全国云-例如， `https://login.microsoftonline.de` 对于 Azure AD 德国的实例。 如果不使用公有云，请查看[国家/地区云终结点](authentication-national-cloud.md#azure-ad-authentication-endpoints)，为你查找合适的云终结点。 确保 `/v2.0/` 你的请求中存在租户和，以便你可以使用该终结点的 v2.0 版本。

> [!TIP]
> 试试看！ 单击 [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) 以查看 `common` 配置。

### <a name="sample-request"></a>示例请求

若要为公有云上的公共机构调用用户信息终结点，请使用以下内容：

```http
GET /common/v2.0/.well-known/openid-configuration
Host: login.microsoftonline.com
```

### <a name="sample-response"></a>示例响应

元数据是简单的 JavaScript 对象表示法 (JSON) 文档。 有关示例，请参阅下面的代码段。 [OpenID connect 规范](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)中完全介绍了内容。

```json
{
  "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize",
  "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys",
  "userinfo_endpoint": "https://graph.microsoft.com/oidc/userinfo",
  "subject_types_supported": [
      "pairwise"
  ],
  ...

}
```

如果应用因为使用[声明映射](active-directory-claims-mapping.md)功能而具有自定义签名密钥，你必须追加包含应用 ID 的 `appid` 查询参数，以便获取指向应用的签名密钥信息的 `jwks_uri`。 例如：`https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含 `https://login.microsoftonline.com/{tenant}/discovery/v2.0/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 的 `jwks_uri`。

通常，使用此元数据文档来配置 OpenID Connect 库或 SDK；该库使用元数据来完成其工作。 不过，如果没有使用预生成的 OpenID Connect 库，则可以按照本文剩余部分的步骤操作，通过使用 Microsoft 标识平台终结点在 Web 应用中执行登录。

## <a name="send-the-sign-in-request"></a>发送登录请求

当 Web 应用需要对用户进行身份验证时，可以将用户定向到 `/authorize` 终结点。 此请求类似于 [OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)的第一个阶段，但有以下几个重要区别：

* 该请求必须在 `scope` 参数中包含 `openid` 范围。
* `response_type` 参数必须包含 `id_token`。
* 请求必须包含 `nonce` 参数。

> [!IMPORTANT]
> 为了成功地从 /authorization 终结点请求获取 ID 令牌，[注册门户](https://portal.azure.com)中的应用注册必须在“身份验证”选项卡中启用隐式授予 id_token（将[应用清单](reference-app-manifest.md)中的 `oauth2AllowIdTokenImplicitFlow` 标志设置为 `true`）。 如果没有启用，就会返回 `unsupported_response` 错误：“为输入参数 'response_type' 提供的值不允许用于此客户端。 预期值为“code””

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

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| `tenant` | 必选 | 可以使用请求路径中的 `{tenant}` 值来控制哪些用户可以登录到该应用程序。 允许的值为 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参见[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | 必选 | [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)体验分配给应用的应用（客户端）ID。 |
| `response_type` | 必选 | 必须包含 OpenID Connect 登录的 `id_token`。 它可能还包括其他 `response_type` 值，例如 `code`。 |
| `redirect_uri` | 建议 | 应用的重定向 URI，应用可通过此 URI 发送和接收身份验证响应。 其必须与门户中注册的其中一个重定向 URI 完全匹配，否则必须经过 URL 编码。 如果不存在，终结点会随机选取一个已注册的 redirect_uri，以将用户重定向回此 URI。 |
| `scope` | 必选 | 范围的空格分隔列表。 针对 OpenID Connect，即必须包含范围 `openid`，其在同意 UI 中转换为“你将登录”权限。 也可以在此请求中包含其他范围，以请求同意。 |
| `nonce` | 必选 | 应用生成并包含在请求中的值，以声明方式包含在生成的 id_token 值中。 应用可验证此值，以减少令牌重放攻击。 此值通常是随机的唯一字符串，可用于识别请求的来源。 |
| `response_mode` | 建议 | 指定应用于将生成的授权代码发送回应用的方法。 可以是 `form_post` 或 `fragment`。 对于 Web 应用程序，建议使用 `response_mode=form_post`，确保以最安全的方式将令牌传输到应用程序。 |
| `state` | 建议 | 同样随令牌响应返回的请求中所包含的值。 其可以是关于想要的任何内容的字符串。 随机生成的唯一值通常用于[防止跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 该状态也用于身份验证请求出现前（例如用户所在页面或视图），对有关用户在应用中状态的信息进行编码。 |
| `prompt` | 可选 | 表示需要的用户交互类型。 此时唯一有效值为 `login``none` 和 `consent`。 `prompt=login` 声明将强制用户在该请求上输入凭据，从而取消单一登录。 而 `prompt=none` 声明截然相反。 此声明确保用户不会看到任何交互式提示。 如果请求无法通过单一登录静默完成，Microsoft 标识平台终结点就会返回错误。 `prompt=consent` 声明会在用户登录后触发 OAuth 同意对话框。 该对话框要求用户向应用授予权限。 |
| `login_hint` | 可选 | 如果事先知道用户名，可使用此参数预先填充用户登录页面的用户名和电子邮件地址字段。 通常，应用在已经使用 `preferred_username` 声明从前次登录提取用户名后，会在重新身份验证时使用此参数。 |
| `domain_hint` | 可选 | 用户在联合目录中的领域。  这跳过用户在登录页面上经历的基于电子邮件的发现过程，从而实现更加流畅的用户体验。 对于通过本地目录（如 AD FS）联合的租户，由于现有的登录会话，这通常会带来无缝登录。 |

此时，系统会提示用户输入凭据并完成身份验证。 Microsoft 标识平台终结点验证用户是否已同意 `scope` 查询参数中指明的权限。 如果用户尚未同意这些权限中的任何一项，Microsoft 标识平台终结点会提示用户同意所需权限。 你可以深入了解[权限、同意和多租户应用](v2-permissions-and-consent.md)。

在用户进行身份验证并授予同意后，Microsoft 标识平台终结点会使用 `response_mode` 参数中指定的方法，在指定的重定向 URI 处向应用返回响应。

### <a name="successful-response"></a>成功的响应

使用 `response_mode=form_post` 时，成功的响应如下所示：

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 参数 | 说明 |
| --- | --- |
| `id_token` | 应用请求的 ID 令牌。 可以使用 `id_token` 参数验证用户身份，并开始与该用户的会话。 有关 ID 令牌及其内容的详细信息，请参阅 [`id_tokens` 参考](id-tokens.md)。 |
| `state` | 如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |

### <a name="error-response"></a>错误响应

错误响应也可能发送到重定向 URI，使应用可对其进行处理。 错误响应如下所示：

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| --- | --- |
| `error` | 可用于分类发生的错误类型和响应错误的错误代码字符串。 |
| `error_description` | 可帮助用户识别身份验证错误根本原因的特定错误消息。 |

### <a name="error-codes-for-authorization-endpoint-errors"></a>授权终结点错误的错误代码

下表描述了可在错误响应的 `error` 参数中返回的错误代码：

| 错误代码 | 说明 | 客户端操作 |
| --- | --- | --- |
| `invalid_request` | 协议错误，例如缺少必需的参数。 |修复并重新提交请求。 这是通常在初始测试期间捕获的开发错误。 |
| `unauthorized_client` | 客户端应用无法请求获取授权代码。 |如果客户端应用没有在 Azure AD 中注册，或没有添加到用户的 Azure AD 租户，通常会出现这种情况。 应用程序可以提示用户，说明如何安装该应用程序并将其添加到 Azure AD。 |
| `access_denied` | 资源所有者拒绝了许可。 |客户端应用可以通知用户，除非用户同意，否则它无法继续。 |
| `unsupported_response_type` |授权服务器不支持请求中的响应类型。 |修复并重新提交请求。 这是通常在初始测试期间捕获的开发错误。 |
| `server_error` | 服务器遇到意外的错误。 |重试请求。 这些错误可能是临时状态导致的。 客户端应用可能会向用户解释，它的响应由于临时错误而延迟。 |
| `temporarily_unavailable` | 服务器暂时繁忙，无法处理请求。 |重试请求。 客户端应用可能会向用户解释，它的响应由于临时状况而延迟。 |
| `invalid_resource` | 目标资源无效，因为它不存在、Azure AD 找不到它，或者它没有正确配置。 |这指明资源（如果存在）尚未在租户中配置。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |

## <a name="validate-the-id-token"></a>验证 ID 令牌

只是接收到 id_token 对用户进行身份验证并不总是足够的;你可能还需要验证 id_token 的签名，并按照应用的要求验证令牌中的声明。 与所有 OIDC 平台一样，Microsoft 标识平台终结点使用[JSON Web 令牌（jwt）](https://tools.ietf.org/html/rfc7519)和公钥加密对 ID 令牌进行签名，并验证它们是否有效。

并非所有应用都受益于验证 ID 令牌-本机应用和单页应用，例如，验证 ID 令牌不会带来好处。  具有对设备的物理访问权限的用户（或浏览器）可以通过多种方式绕过验证-从编辑到设备的 web 流量到提供虚设的令牌和密钥，只需对应用程序进行调试，即可跳过验证逻辑。  另一方面，使用 ID 令牌进行身份验证的 web 应用和 Api 必须仔细验证 ID 令牌，因为它们会对数据进行访问。

验证 id_token 的签名后，就需要验证几项声明。 有关详细信息，请参阅 [`id_token` 参考](id-tokens.md)，其中包括[验证令牌](id-tokens.md#validating-an-id_token)和[有关签名密钥滚动更新的重要信息](active-directory-signing-key-rollover.md)。 我们建议利用库来分析和验证令牌 - 对于大多数语言和平台至少有一个可用。

可能还希望根据自己的方案验证其他声明。 一些常见的验证包括：

* 确保用户/组织已注册应用。
* 确保用户拥有正确的授权/权限
* 确保进行了一定强度的身份验证，如[多重身份验证](../authentication/concept-mfa-howitworks.md)。

验证 id_token 后，就可以开始与用户的会话，并使用 id_token 中的声明来获取应用中用户的信息。 此信息可用于显示、记录和个性化等。

## <a name="protocol-diagram-access-token-acquisition"></a>协议图：访问令牌获取

许多 Web 应用不仅需要登录用户，还需要代表该用户使用 OAuth 访问 Web 服务。 如果要使用 OAuth 授权代码流，此方案合并了用于对用户进行身份验证的 OpenID Connect，同时将获取授权代码，用户可以使用该代码获取访问令牌。

完整的 OpenID Connect 登录和令牌获取流与下图类似。 本文以下各节将详细介绍各步骤。

![OpenID Connect 协议：令牌获取](./media/v2-protocols-oidc/convergence-scenarios-webapp-webapi.svg)

## <a name="get-an-access-token-to-call-userinfo"></a>获取用于调用用户信息的访问令牌

若要为 OIDC 用户的终结点获取令牌，请修改登录请求：

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20token                       // this will return both an id_token and an access token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid+profile+email                           // `openid` is required.  `profile` and `email` provide additional information in the UserInfo endpoint the same way they do in an ID token. 
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

你还可以使用[授权代码流](v2-oauth2-auth-code-flow.md)、[设备代码流](v2-oauth2-device-code.md)或[刷新令牌](v2-oauth2-auth-code-flow.md#refresh-the-access-token)代替 `response_type=token` 来获取应用的令牌。

> [!TIP]
> 单击下面的链接可执行此请求。 登录后，浏览器将重定向到 `https://localhost/myapp/` ，且地址栏中有一个 ID 令牌和一个令牌。 请注意，此请求 `response_mode=fragment` 仅用于演示目的-对于 webapp，我们建议 `form_post` 尽可能使用以实现额外的安全性。 
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid+profile+email&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

### <a name="successful-token-response"></a>成功的令牌响应

使用 `response_mode=form_post` 的成功响应如下所示：

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
 access_token=eyJ0eXAiOiJKV1QiLCJub25jZSI6I....
 &token_type=Bearer
 &expires_in=3598
 &scope=email+openid+profile
 &id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI....
 &state=12345
```

无论用于获取它们的流如何，响应参数都是一样的。

| 参数 | 说明 |
| --- | --- |
| `token` | 将用于调用用户信息终结点的标记。|
| `token_type` | 始终为 "持有者" |
| `expires_in`| 访问令牌过期前等待多长时间（以秒为单位）。 |
| `scope` | 授予访问令牌的权限。  请注意，由于用户在 MS Graph 上托管了用户信息终结点，因此，如果以前已将其授予应用，则可能会在此处列出其他图形范围（如 "用户"）。  这是因为给定资源的令牌始终包含当前授予客户端的每个权限。  |
| `id_token` | 应用请求的 ID 令牌。 可以使用 ID 令牌验证用户的身份，并开始与用户的会话。 有关 ID 令牌及其内容的详细信息，请参阅 [`id_tokens` 参考](id-tokens.md)。 |
| `state` | 如果请求中包含状态参数，响应中就应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |

### <a name="error-response"></a>错误响应

错误响应也可能发送到重定向 URI，使应用可以对其进行适当处理。 错误响应如下所示：

```HTTP
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| --- | --- |
| `error` | 可用于分类发生的错误类型和响应错误的错误代码字符串。 |
| `error_description` | 可帮助用户识别身份验证错误根本原因的特定错误消息。 |

有关可能的错误代码的描述及建议的客户端响应，请参阅[授权终结点错误的错误代码](#error-codes-for-authorization-endpoint-errors)。

如果拥有授权代码和 ID 令牌，可以登录用户并代表他们获取访问令牌。 若要登录用户，必须[完全按照上面所述](id-tokens.md#validating-an-id_token)验证 ID 令牌。 若要获取访问令牌，请遵循 [OAuth 代码流文档](v2-oauth2-auth-code-flow.md#request-an-access-token)中所述的步骤。

### <a name="calling-the-userinfo-endpoint"></a>调用用户信息终结点

查看[用户信息文档](userinfo.md#calling-the-api)以了解如何通过此令牌调用用户信息终结点。

## <a name="send-a-sign-out-request"></a>发送注销请求

如果希望将用户从应用中注销，仅仅是清除应用的 Cookie 或结束用户会话并不足够。 还必须将用户重定向到 Microsoft 标识平台终结点才能注销。如果不这样做，用户无需再次输入凭据，就可以重新向应用进行身份验证，因为他们使用 Microsoft 标识平台终结点进行有效的单一登录会话。

可以将用户重定向到 OpenID Connect 元数据文档中所列的 `end_session_endpoint`：

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| 参数 | 条件 | 说明 |
| ----------------------- | ------------------------------- | ------------ |
| `post_logout_redirect_uri` | 建议 | 用户在成功注销后将重定向到的 URL。如果不包括此参数，则会向用户显示由 Microsoft 标识平台终结点生成的常规消息。 此 URL 必须与在应用注册门户中为应用程序注册的重定向 URI 之一匹配。 |

## <a name="single-sign-out"></a>单一登录

当你将用户重定向到 `end_session_endpoint` 时，Microsoft 标识平台终结点从浏览器中清除用户会话。 但是，用户可能仍登录到其他使用 Microsoft 帐户进行身份验证的应用程序。 为了让这些应用能够同时注销用户，Microsoft 标识平台终结点会将 HTTP GET 请求发送到用户当前登录的所有应用的已注册 `LogoutUrl`。 应用程序必须通过清除任何标识用户的会话并返回 `200` 响应来响应此请求。 如果要在应用程序中支持单一注销，必须在应用程序代码中实现此类 `LogoutUrl`。 可以从应用注册门户设置 `LogoutUrl`。

## <a name="next-steps"></a>后续步骤

* 查看[用户信息文档](userinfo.md)
* 了解如何使用本地系统中的数据[自定义令牌中的值](active-directory-claims-mapping.md)。 
* 了解如何[在令牌中包含其他标准声明](active-directory-optional-claims.md)。  
