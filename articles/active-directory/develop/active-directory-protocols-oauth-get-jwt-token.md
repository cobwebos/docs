---
title: 使用 OAuth 2.0 通过 Azure AD 进行身份验证和获取 JWT 令牌
description: 示例代码，演示如何使用 OAuth 2.0 向 Azure Active Directory 进行身份验证以访问组织中受保护的 Web 应用程序和 Web API。
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: eb26101229ad60abae7a8a84f8dfa496488e84ba
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578997"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>使用 OAuth 2.0 请求访问令牌，以访问受 Azure Active Directory 保护的 Web API 和应用程序

本文介绍如何获取 JSON Web 令牌 (JWT) 以访问受 Azure AD 保护的资源。 本文假设你具有来自用户授予权限或通过[服务主体](/azure/active-directory/develop/active-directory-application-objects)获得的[授权令牌](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)。

## <a name="build-the-request"></a>生成请求

使用以下 `POST` HTTP 请求获取 JWT，以访问受 Azure AD 保护的特定应用程序或 API。

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>请求标头

以下标头是必需的：

|请求标头|Description|  
|--------------------|-----------------|  
| Host: | https://login.microsoftonline.com |
| Content-Type：| application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>URI 参数

| 参数     |                       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant        | 必填              | 请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 允许的值为 `common`、`organizations`、`consumers` 和租户标识符。 有关更多详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。                                                                                                                                                   |
| client_id     | 必填              | 注册门户 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 分配给应用的应用程序 ID。                                                                                                                                                                                                                             |
| grant_type    | 必填              | 必须是授权代码流的 `authorization_code`。                                                                                                                                                                                                                                                                                                                                                                            |
| 作用域         | 必填              | 范围的空格分隔列表。 在此阶段中请求的范围必须相当于或为调用 `/authorize` 时的范围的子集。 如果这个请求中指定的范围遍及多个资源服务器，v2.0 终结点将返回第一个范围内所指定资源的令牌。 有关范围的更加详细的说明，请参阅[权限、许可和范围](v2-permissions-and-consent.md)。 |
| 代码          | 必填              | 调用 `/authorize` 时获取的 authorization_code                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | 必填              | 用于获取 authorization_code 的相同 redirect_uri 值。                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | Web 应用所需 | 在应用程序注册门户中为应用程序创建的应用程序机密。 请勿用于本机应用，因为设备无法可靠地存储 client_secrets。 Web 应用和 Web API 都需要应用程序机密，能够将 client_secret 安全地存储在服务器端。  发送客户端机密之前必须对其进行 URL 编码。                                                                                 |
| code_verifier | 可选              | 即用于获取 authorization_code 的 code_verifier。 如果在授权码授权请求中使用 PKCE，则需要。 有关详细信息，请参阅 [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>处理响应

成功的令牌响应包含一个 JWT 令牌，类似如下所示：

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
| 参数     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | 请求的访问令牌。 应用可以使用此令牌验证受保护的资源，例如 Web API。                                                                                                                                                                                                                                                                                                                                    |
| token_type    | 指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | 访问令牌的有效期（以秒为单位）。                                                                                                                                                                                                                                                                                                                                                                                                       |
| 作用域         | access_token 有效的范围。                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | OAuth 2.0 刷新令牌。 应用程序可以使用此令牌，在当前的访问令牌过期之后获取其他访问令牌。 Refresh_tokens 的生存期很长，而且可以用于延长保留资源访问权限的时间。 有关更多详细信息，请参阅 [v2.0 令牌参考](v2-id-and-access-tokens.md)。 <br> **注意：** 仅当已请求 `offline_access` 作用域时提供。                                               |
| id_token      | 无符号 JSON Web 令牌 (JWT)。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅 [v2.0 终结点令牌参考](v2-id-and-access-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |



