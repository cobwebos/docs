---
title: Azure Active Directory v2.0 和 OpenID Connect 协议 | Microsoft Docs
description: 通过使用 OpenID Connect 身份验证协议的 Azure AD v2.0 实现，构建 Web 应用程序。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a4875997-3aac-4e4c-b7fe-2b4b829151ce
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a0cd077b1c6530c5794c92f131dffb814f5b341d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157711"
---
# <a name="azure-active-directory-v20-and-the-openid-connect-protocol"></a>Azure Active Directory v2.0 和 OpenID Connect 协议
OpenID Connect 是在 OAuth 2.0 基础上构建的身份验证协议，可用于将用户安全登录到 Web 应用程序。 使用 OpenID Connect 的 v2.0 终结点的实现时，可以将登录和 API 访问权限添加到基于 Web 的应用中。 本文将演示执行此操作的方法（无论何种语言）。 本文介绍在不使用任何 Microsoft 开放源代码库的情况下，如何发送和接收 HTTP 消息。

> [!NOTE]
> v2.0 终结点并不支持所有 Azure Active Directory 方案和功能。 若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) 扩展了 OAuth 2.0 *授权*协议，使其可用作*身份验证*协议，这样一来，用户可使用 OAuth 执行单一登录。 OpenID Connect 引入了 ID 令牌的概念，ID 令牌是一种可让客户端验证用户标识的安全令牌。 ID 令牌还可获取用户的基本个人资料信息。 由于 OpenID Connect 扩展了 OAuth 2.0，因此应用可安全获取访问令牌，访问令牌可用于访问[授权服务器](active-directory-v2-protocols.md#the-basics)保护的资源。 v2.0 终结点还允许向 Azure AD 进行了注册的第三方应用为受保护的资源（例如 Web API）颁发访问令牌。 有关如何设置应用程序来颁发访问令牌的详细信息，请参阅[如何向 v2.0 终结点注册应用](active-directory-v2-app-registration.md)。 如果要构建在服务器上托管并通过浏览器访问的 [web 应用程序](active-directory-v2-flows.md#web-apps)，建议使用 OpenID Connect。

## <a name="protocol-diagram-sign-in"></a>协议图：登录
最基本的登录流程步骤如下图所示。 本文将详细介绍每个步骤。

![OpenID Connect 协议：登录](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>提取 OpenID Connect 元数据文档
OpenID Connect 描述了元数据文档，该文档包含了应用执行登录所需的大部分信息。 这包括要使用的 URL 和服务的公共签名密钥的位置等信息。 对于 v2.0 终结点，应使用的 OpenID Connect 元数据文档为：

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```
> [!TIP] 
> 试试看！ 单击 [https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration) 可查看 `common` 租户配置。 
>

`{tenant}` 可采用四个值的其中之一：

| 值 | 说明 |
| --- | --- |
| `common` |同时拥有 Microsoft 个人帐户和工作/学校帐户的用户可以从 Azure Active Directory (Azure AD) 登录到应用程序。 |
| `organizations` |仅拥有工作/学校帐户的用户可以从 Azure AD 登录到应用程序。 |
| `consumers` |仅拥有 Microsoft 个人帐户的用户可以登录到应用程序。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 或 `contoso.onmicrosoft.com` |仅拥有工作/学校帐户的用户可以从特定 Azure AD 租户登录到应用程序。 可以使用 Azure AD 租户的友好域名或租户的 GUID 标识符。 |

元数据是简单的 JavaScript 对象表示法 (JSON) 文档。 有关示例，请参阅下面的代码段。 [OpenID Connect 规范](https://openid.net/specs/openid-connect-discovery-1_0.html#rfc.section.4.2)对该代码段的内容进行了完整描述。

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

通常，使用此元数据文档来配置 OpenID Connect 库或 SDK；该库使用元数据来完成其工作。 但是，如果不使用预生成的 OpenID Connect 库，则可以按照本文剩余部分的步骤，通过使用 v2.0 终结点在 Web 应用中执行登录。

## <a name="send-the-sign-in-request"></a>发送登录请求
当 Web 应用需要对用户进行身份验证时，可以将用户定向到 `/authorize` 终结点。 此请求类似于 [OAuth 2.0 授权代码流](active-directory-v2-protocols-oauth-code.md)的第一个阶段，但有以下几个重要区别：

* 该请求必须在 `scope` 参数中包含 `openid` 范围。
* `response_type` 参数必须包含 `id_token`。
* 请求必须包含 `nonce` 参数。

> [!IMPORTANT]
> 为了成功请求 ID 令牌，[注册门户](https://apps.dev.microsoft.com)中的应用注册必须已为 Web 客户端启用**[隐式授权](active-directory-v2-protocols-implicit.md)**。 如果未启用，将返回 `unsupported_response` 错误：“输入参数“response_type”提供的值不允许用于此客户端。 预期值为“code””

例如：

```
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
> 单击下面的链接可执行此请求。 登录后，浏览器将重定向到 https://localhost/myapp/，且地址栏中有一个 ID 令牌。 请注意，此请求使用 `response_mode=fragment`（仅用于演示）。 建议使用 `response_mode=form_post`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| 参数 | 条件 | 说明 |
| --- | --- | --- |
| tenant |必选 |可以使用请求路径中的 `{tenant}` 值来控制哪些用户可以登录到该应用程序。 允许的值为 `common`、`organizations`、`consumers` 和租户标识符。 有关详细信息，请参见[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| client_id |必选 |[应用程序注册门户](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)分配给该应用的应用程序 ID。 |
| response_type |必选 |必须包含 OpenID Connect 登录的 `id_token`。 它可能还包括其他 `response_types` 值，例如 `code`。 |
| redirect_uri |建议 |应用的重定向 URI，应用可通过此 URI 发送和接收身份验证响应。 其必须与门户中注册的其中一个重定向 URI 完全匹配，否则必须经过 URL 编码。 |
| 作用域 |必选 |范围的空格分隔列表。 针对 OpenID Connect，即必须包含范围 `openid`，其在同意 UI 中转换为“你将登录”权限。 也可以在此请求中包含其他范围，以请求同意。 |
| nonce |必选 |应用生成并包含在请求中的值，以声明方式包含在生成的 id_token 值中。 应用可验证此值，以减少令牌重放攻击。 此值通常是随机的唯一字符串，可用于识别请求的来源。 |
| response_mode |建议 |指定应用于将生成的授权代码发送回应用的方法。 可以是 `form_post` 或 `fragment`。 对于 Web 应用程序，建议使用 `response_mode=form_post`，确保以最安全的方式将令牌传输到应用程序。 |
| state |建议 |同样随令牌响应返回的请求中所包含的值。 其可以是关于想要的任何内容的字符串。 随机生成的唯一值通常用于[防止跨站点请求伪造攻击](http://tools.ietf.org/html/rfc6749#section-10.12)。 该状态也用于身份验证请求出现前（例如用户所在页面或视图），对有关用户在应用中状态的信息进行编码。 |
| prompt |可选 |表示需要的用户交互类型。 此时唯一有效值为 `login``none` 和 `consent`。 `prompt=login` 声明将强制用户在该请求上输入凭据，从而取消单一登录。 而 `prompt=none` 声明截然相反。 此声明将确保无论如何都不会向用户显示任何交互提示。 如果请求无法通过单一登录静默完成，则 v2.0 终结点将返回一个错误。 `prompt=consent` 声明会在用户登录后触发 OAuth 同意对话框。 该对话框要求用户向应用授予权限。 |
| login_hint |可选 |如果事先知道用户名，可使用此参数预先填充用户登录页面的用户名和电子邮件地址字段。 通常，应用在已经使用 `preferred_username` 声明从前次登录提取用户名后，会在重新身份验证时使用此参数。 |
| domain_hint |可选 |此值可为 `consumers` 或 `organizations`。 如果已包含在内，它将跳过用户在 v2.0 登录页面上经历的基于电子邮件的发现过程，从而实现更加流畅的用户体验。 通常，应用在重新身份验证时使用此参数，方法是从 ID 令牌提取 `tid` 声明。 如果 `tid` 声明值为 `9188040d-6c67-4c5b-b112-36a304b66dad`（Microsoft 帐户使用者租户），则使用 `domain_hint=consumers`。 否则使用 `domain_hint=organizations`。 |

此时，系统会提示用户输入凭据并完成身份验证。 v2.0 终结点会验证用户是否已经同意 `scope` 查询参数中指示的权限。 如果用户尚未同意这些权限中的任何一项，v2.0 终结点会提示用户同意所需权限。 可以深入了解[权限、许可和多租户应用](active-directory-v2-scopes.md)。

用户经过身份验证并授予许可后，v2.0 终结点会使用 `response_mode` 参数中指定的方法，将响应返回位于所指示的重定向 URI 的应用。

### <a name="successful-response"></a>成功的响应
使用 `response_mode=form_post` 时，成功的响应如下所示：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 参数 | 说明 |
| --- | --- |
| id_token |应用请求的 ID 令牌。 可以使用 `id_token` 参数验证用户身份，并开始与该用户的会话。 有关 ID 令牌及其内容的详细信息，请参阅 [v2.0 终结点令牌参考](active-directory-v2-tokens.md)。 |
| state |如果请求中包含 `state` 参数，响应中应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |

### <a name="error-response"></a>错误响应
错误响应也可能发送到重定向 URI，使应用可对其进行处理。 错误响应如下所示：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| --- | --- |
| error |可用于分类发生的错误类型和响应错误的错误代码字符串。 |
| error_description |可帮助用户识别身份验证错误根本原因的特定错误消息。 |

### <a name="error-codes-for-authorization-endpoint-errors"></a>授权终结点错误的错误代码
下表描述了可在错误响应的 `error` 参数中返回的错误代码：

| 错误代码 | 说明 | 客户端操作 |
| --- | --- | --- |
| invalid_request |协议错误，例如缺少必需的参数。 |修复并重新提交请求。 这是通常在初始测试期间捕获的开发错误。 |
| unauthorized_client |客户端应用程序无法请求授权代码。 |客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现这种情况。 应用程序可以提示用户，说明如何安装该应用程序并将其添加到 Azure AD。 |
| access_denied |资源所有者拒绝了许可。 |客户端应用程序可以通知用户除非用户许可，否则无法继续。 |
| unsupported_response_type |授权服务器不支持请求中的响应类型。 |修复并重新提交请求。 这是通常在初始测试期间捕获的开发错误。 |
| server_error |服务器遇到意外的错误。 |重试请求。 这些错误可能是临时状态导致的。 客户端应用程序可能向用户说明，其响应由于临时错误而延迟。 |
| temporarily_unavailable |服务器暂时繁忙，无法处理请求。 |重试请求。 客户端应用程序可能向用户说明，其响应由于临时状况而延迟。 |
| invalid_resource |目标资源无效的原因可能是以下之一：目标资源不存在，Azure AD 找不到目标资源，或者未正确配置目标资源。 |这表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |

## <a name="validate-the-id-token"></a>验证 ID 令牌
收到一个 ID 令牌并不表示可以对用户进行身份验证。 还必须根据应用要求，验证 ID 令牌的签名和令牌中的声明。 v2.0 终结点使用 [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)（JSON Web 令牌 (JWT)）和公钥加密对令牌进行签名并验证其是否有效。

可以选择验证客户端代码中的 ID 令牌，但是常见的做法是将 ID 令牌发送到后端服务器，并在那里执行验证。 验证 ID 令牌签名后，需要验证几个声明。 有关详细信息（包括[验证令牌](active-directory-v2-tokens.md#validating-tokens)的详细信息和[有关签名密钥滚动更新的重要信息](active-directory-v2-tokens.md#validating-tokens)），请参阅 [v2.0 令牌参考](active-directory-v2-tokens.md)。 建议使用库来分析和验证令牌。 这些库中至少有一个库可用于大多数语言和平台。
<!--TODO: Improve the information on this-->

根据具体情形，可能还需要验证其他声明。 一些常见的验证包括：

* 确保用户/组织已注册该应用。
* 确保用户具有必需的授权或特权。
* 确保身份验证具有一定的强度，例如多重身份验证。

有关 ID 令牌中声明的详细信息，请参阅 [v2.0 终结点令牌参考](active-directory-v2-tokens.md)。

完全验证 ID 令牌后，可以开始与用户的会话。 使用 ID 令牌中的声明来获取应用中用户的相关信息。 可以将此信息用于显示、记录、授权等。

## <a name="send-a-sign-out-request"></a>发送注销请求
如果希望将用户从应用中注销，仅仅是清除应用的 Cookie 或结束用户会话并不足够。 还必须将用户重定向到 v2.0 终结点才能注销。如果不这样做，用户不需要再次输入凭据就能重新通过应用的身份验证，因为他们与 v2.0 终结点之间仍然存在有效的单一登录会话。

可以将用户重定向到 OpenID Connect 元数据文档中所列的 `end_session_endpoint`：

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| 参数 | 条件 | 说明 |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | 建议 | 用户在成功注销后将重定向到的 URL。如果不包括参数，将向用户显示一条 v2.0 终结点生成的常规消息。 此 URL 必须与在应用注册门户中为应用程序注册的重定向 URI 之一匹配。 |

## <a name="single-sign-out"></a>单一登录
将用户重定向到 `end_session_endpoint` 时，v2.0 终结点将从浏览器中清除用户的会话。 但是，用户可能仍登录到其他使用 Microsoft 帐户进行身份验证的应用程序。 要使这些应用程序能够同时注销用户，v2.0 终结点会将 HTTP GET 请求发送到用户当前登录到的所有应用程序的注册 `LogoutUrl`。 应用程序必须通过清除任何标识用户的会话并返回 `200` 响应来响应此请求。 如果要在应用程序中支持单一注销，必须在应用程序代码中实现此类 `LogoutUrl`。 可以从应用注册门户设置 `LogoutUrl`。

## <a name="protocol-diagram-access-token-acquisition"></a>协议图：访问令牌获取
许多 Web 应用不仅需要登录用户，还需要代表该用户使用 OAuth 访问 Web 服务。 如果要使用 OAuth 授权代码流，此方案合并了用于对用户进行身份验证的 OpenID Connect，同时将获取授权代码，用户可以使用该代码获取访问令牌。

完整的 OpenID Connect 登录和令牌获取流与下图类似。 本文以下各节将详细介绍各步骤。

![OpenID Connect 协议：令牌获取](../../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>获取访问令牌
若要获取访问令牌，请修改登录请求：

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered redirect URI, URL encoded
&response_mode=form_post                              // 'form_post' or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes that your app needs  
offline_access%20                                         
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

> [!TIP]
> 单击下面的链接可执行此请求。 登录后，浏览器将重定向到 https://localhost/myapp/，且地址栏中有一个 ID 令牌和一个代码。 请注意，此请求使用 `response_mode=fragment`（仅用于演示）。 建议使用 `response_mode=form_post`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=fragment&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

通过在请求中包含权限范围并使用 `response_type=id_token code`，v2.0 终结点可确保用户已经同意 `scope` 查询参数中指示的权限。 它将授权代码返回应用，以交换访问令牌。

### <a name="successful-response"></a>成功的响应
使用 `response_mode=form_post` 的成功响应如下所示：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 参数 | 说明 |
| --- | --- |
| id_token |应用请求的 ID 令牌。 可以使用 ID 令牌验证用户的身份，并开始与用户的会话。 有关 ID 令牌及其内容的详细信息，请参阅 [v2.0 终结点令牌参考](active-directory-v2-tokens.md)。 |
| 代码 |应用程序请求的授权代码。 应用程序可以使用授权代码请求目标资源的访问令牌。 授权代码生存期非常短。 通常情况下，授权代码会在约 10 分钟后过期。 |
| state |如果请求中包含状态参数，响应中就应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |

### <a name="error-response"></a>错误响应
错误响应也可能发送到重定向 URI，使应用可以对其进行适当处理。 错误响应如下所示：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| --- | --- |
| error |可用于分类发生的错误类型和响应错误的错误代码字符串。 |
| error_description |可帮助用户识别身份验证错误根本原因的特定错误消息。 |

有关可能的错误代码的描述及建议的客户端响应，请参阅[授权终结点错误的错误代码](#error-codes-for-authorization-endpoint-errors)。

如果拥有授权代码和 ID 令牌，可以登录用户并代表他们获取访问令牌。 若要登录用户，必须[完全按照上面所述](#validate-the-id-token)验证 ID 令牌。 若要获取访问令牌，请遵循 [OAuth 协议文档](active-directory-v2-protocols-oauth-code.md#request-an-access-token)中所述的步骤。
