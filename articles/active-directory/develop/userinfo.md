---
title: Microsoft 标识平台 UserInfo 终结点 | Azure
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台上的 UserInfo 终结点。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8f3fd462a52b035cd5b5447560e5472b41f237fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91653224"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft 标识平台的 UserInfo 终结点

UserInfo 终结点是 [OpenID Connect 标准](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) (OIDC) 的一部分，用于返回有关已通过身份验证的用户的声明。  对于 Microsoft 标识平台，UserInfo 终结点承载在 Microsoft Graph (https://graph.microsoft.com/oidc/userinfo) 上。 

## <a name="find-the-well-known-configuration-endpoint"></a>查找 .well-known 配置终结点

你可以使用 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` 上的 OpenID Connect 发现文档以编程方式发现 UserInfo 终结点。 它在 `userinfo_endpoint` 字段中列出，该模式可以在各个云中用来指向正确的终结点。  我们不建议在应用中对用户信息终结点进行硬编码–使用 OIDC 发现文档可在运行时查找此终结点。

根据 OpenID Connect 规范，[符合 OIDC 规范的库](https://openid.net/developers/certified/)通常会自动调用 UserInfo 终结点以获取用户的信息。  如果不承载此类终结点，Microsoft 标识平台就不符合标准，某些库会失败。  我们根据 [OIDC 标准中标识的声明的列表](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims)生成名称声明、主题声明和电子邮件（如果这些项可用并已获得许可）。  

## <a name="consider-use-an-id-token-instead"></a>请考虑：改用 ID 令牌

应用可以接收的 ID 令牌中提供的信息是它可以从 UserInfo 终结点获取的信息的超集。  由于你可以在获取用于调用 UserInfo 终结点的令牌的同时获取 ID 令牌，因此建议你使用该 ID 令牌获取用户的信息，而不必调用 UserInfo 终结点。  使用 ID 令牌可以在应用程序启动时消除一到两个网络请求，从而降低应用程序中的延迟。

如果需要有关用户的更多详细信息，则应调用 [Microsoft Graph `/user` API](/graph/api/user-get) 来获取该信息，例如办公电话或职务。   你还可以使用[可选声明](active-directory-optional-claims.md)在 ID 和访问令牌中包括其他用户信息。

## <a name="calling-the-userinfo-endpoint"></a>调用 UserInfo 终结点

UserInfo 是一个标准的 OAuth 持有者令牌 API，与任何其他 Microsoft Graph API 一样，将使用为 Microsoft Graph 获取令牌时收到的访问令牌来调用它。 它返回一个 JSON 响应，其中包含有关用户的声明的。

### <a name="permissions"></a>权限

使用以下 [OIDC 权限](v2-permissions-and-consent.md#openid-connect-scopes)调用 UserInfo API。 `openid` 是必需的，而 `profile` 和 `email` 作用域则可确保在响应中提供其他信息。

|权限类型      | 权限    |
|:--------------------|:---------------------------------------------------------|
|委托的（工作或学校帐户） | openid（必需）、profile、email |
|委托 (个人 Microsoft 帐户)  | openid（必需）、profile、email |
|应用程序 | 不适用 |

> [!TIP]
> 将以下 URL 复制到你的浏览器中以获取 UserInfo 终结点的令牌以及 [ID 令牌](id-tokens.md)，并将客户端 ID 和重定向 URI 替换为你自己的值。 请注意，它只请求 OpenID 作用域或 Graph 作用域，而不请求其他内容。  这是必需的，因为无法在同一令牌请求中请求两个不同资源的权限。
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> 在下一部分中，你可以使用此访问令牌。

与任何其他 Microsoft Graph 令牌一样，你在此处收到的令牌可能不是 JWT。 如果登录了一个 Microsoft 帐户用户，它将是加密令牌格式。 这是因为 Microsoft Graph 具有特殊的令牌颁发模式。 这不会影响你使用访问令牌调用 UserInfo 终结点的能力。

### <a name="calling-the-api"></a>调用 API

根据 OIDC 规范，UserInfo API 同时支持 GET 和 POST。

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>UserInfo 响应

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

此处列出的声明是用户信息终结点可以返回的所有声明。  这些值与应用程序在颁发给应用程序的 [ID 令牌](id-tokens.md) 中看到的值相同。  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>有关 UserInfo 终结点的说明和注意事项

* 如果要调用此 UserInfo 终结点，则必须使用 v2.0 终结点。  如果你使用 v1.0 终结点，你将获得一个在 login.microsoftonline.com 上托管的1.0 用户信息终结点令牌。  建议符合 OIDC 的所有应用和库都使用 v2.0 终结点，以确保兼容性。
* 无法自定义来自 UserInfo 终结点的响应。  如果要自定义声明，请使用[声明映射]( active-directory-claims-mapping.md)编辑令牌中返回的信息。
* 无法添加来自 UserInfo 终结点的响应。  若要获取有关用户的其他声明，请使用[可选声明]( active-directory-optional-claims.md)将新声明添加到令牌中。

## <a name="next-steps"></a>后续步骤

* [查看 ID 令牌的内容](id-tokens.md)
* [使用可选声明自定义 ID 令牌的内容](active-directory-optional-claims.md)
* [使用 OAuth2 协议请求访问令牌和 ID 令牌](v2-protocols-oidc.md)
