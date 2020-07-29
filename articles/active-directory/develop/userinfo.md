---
title: Microsoft 标识平台用户信息终结点 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解 Microsoft 标识平台上的用户信息终结点。
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
ms.custom: aaddev
ms.openlocfilehash: 08386e2c54a45fe4a6e35ce6d7ebb51145827a6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84268435"
---
# <a name="microsoft-identity-platform-userinfo-endpoint"></a>Microsoft 标识平台用户信息终结点

用户信息终结点是[OpenID connect 标准](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo)（OIDC）的一部分，旨在返回有关已进行身份验证的用户的声明。  对于 Microsoft 标识平台，用户信息终结点承载在 Microsoft Graph （ https://graph.microsoft.com/oidc/userinfo) 。 

## <a name="find-the-well-known-configuration-endpoint"></a>查找众所周知的配置终结点

您可以使用 OpenID Connect 发现文档以编程方式发现用户信息终结点，网址为 `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration` 。 它在字段中列出 `userinfo_endpoint` ，可跨云使用此模式，以帮助指向正确的终结点。  我们不建议在应用中对用户信息终结点进行硬编码–使用 OIDC 发现文档可在运行时查找此终结点。

作为 OpenID Connect 规范的一部分， [OIDC 兼容库](https://openid.net/developers/certified/)通常会自动调用用户信息终结点，以获取有关用户的信息。  如果不托管此类终结点，Microsoft 标识平台将不符合标准，并且某些库将会失败。  在[OIDC 标准中标识的声明列表](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims)中，我们将生成名称声明、主题声明和电子邮件（如果有），并获得许可。  

## <a name="consider-use-an-id-token-instead"></a>请考虑：改为使用 ID 令牌

您的应用程序可以接收的 ID 令牌中提供的信息是它可以从用户信息终结点获取的信息的超集。  由于可以在获取用于调用用户信息终结点的令牌的同时获取 ID 令牌，因此建议使用该 ID 令牌获取有关用户的信息，而不是调用用户信息终结点。  使用 ID 令牌将从应用程序启动中消除一到两个网络请求，从而减少应用程序中的延迟。

如果需要更多有关用户的详细信息，应调用[Microsoft Graph `/user` API](https://docs.microsoft.com/graph/api/user-get)来获取信息，例如办公室号码或职务。   你还可以使用[可选声明](active-directory-optional-claims.md)将其他用户信息包含在你的 ID 和访问令牌中。

## <a name="calling-the-userinfo-endpoint"></a>调用用户信息终结点

用户信息是标准的 OAuth 持有者令牌 API，与任何其他 Microsoft Graph API 一样，使用获取 Microsoft Graph 的令牌时收到的访问令牌。 它将返回包含有关用户的声明的 JSON 响应。

### <a name="permissions"></a>权限

使用以下[OIDC 权限](v2-permissions-and-consent.md#openid-connect-scopes)调用用户信息 API。 `openid`是必需的， `profile` 并且和 `email` 范围确保响应中提供了其他信息。

|权限类型      | 权限    |
|:--------------------|:---------------------------------------------------------|
|已委派（工作或学校帐户） | openid （必需）、配置文件、电子邮件 |
|已委派（个人 Microsoft 帐户） | openid （必需）、配置文件、电子邮件 |
|应用程序 | 不适用 |

> [!TIP]
> 在浏览器中复制此 URL，获取用户信息终结点的令牌以及[ID 令牌](id-tokens.md)，并将客户端 ID 和重定向 URI 替换为你自己的 url。 请注意，它只请求 OpenID 或 Graph 范围的作用域，而不请求其他内容。  这是必需的，因为无法在同一令牌请求中请求两个不同资源的权限。
>
> `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=<yourClientID>&response_type=token+id_token&redirect_uri=<YourRedirectUri>&scope=user.read+openid+profile+email&response_mode=fragment&state=12345&nonce=678910`
>
> 在下一部分中，你可以使用此访问令牌。

与任何其他 Microsoft Graph 令牌一样，此处收到的令牌可能不是 JWT。 如果你在 Microsoft 帐户用户登录，则它将是加密令牌格式。 这是因为 Microsoft Graph 具有特殊的令牌颁发模式。 这不会影响你使用访问令牌调用用户信息终结点的能力。

### <a name="calling-the-api"></a>调用 API

用户信息 API 根据 OIDC 规范支持 GET 和 POST。

```http
GET or POST /oidc/userinfo HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6Il…
```

### <a name="userinfo-response"></a>用户信息响应

```jsonc
{
    "sub": "OLu859SGc2Sr9ZsqbkG-QbeLgJlb41KcdiPoLYNpSFA",
    "name": "Mikah Ollenburg", // names all require the “profile” scope.
    "family_name": " Ollenburg",
    "given_name": "Mikah",
    "email": "mikoll@contoso.com" //requires the “email” scope.
}
```

此处列出的声明（包括 `sub` ）与应用程序在颁发给应用程序的[ID 令牌](id-tokens.md)中看到的声明相同。  

## <a name="notes-and-caveats-on-the-userinfo-endpoint"></a>用户信息终结点上的说明和警告

* 如果要调用此用户信息终结点，则必须使用 v2.0 终结点。  如果你使用 v1.0 终结点，你将获得一个在 login.microsoftonline.com 上托管的1.0 用户信息终结点令牌。  建议所有 OIDC 兼容的应用和库使用 v2.0 终结点，以确保兼容性。
* 无法自定义来自用户的终结点的响应。  如果要自定义声明，请使用[声明映射]( active-directory-claims-mapping.md)编辑令牌中返回的信息。
* 无法将来自用户的终结点的响应添加到。  如果你想要获取有关用户的其他声明，请使用[可选声明]( active-directory-optional-claims.md)向令牌中添加新声明。

## <a name="next-steps"></a>后续步骤

* [查看 ID 令牌的内容](id-tokens.md)
* [使用可选声明自定义 ID 令牌的内容](active-directory-optional-claims.md)
* [使用 OAuth2 协议请求访问令牌和 ID 令牌](v2-protocols-oidc.md)