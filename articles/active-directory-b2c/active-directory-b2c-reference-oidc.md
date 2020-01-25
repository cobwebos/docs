---
title: 通过 OpenID Connect 进行 Web 登录-Azure Active Directory B2C
description: 在 Azure Active Directory B2C 中使用 OpenID Connect 身份验证协议生成 web 应用程序。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 69b58b402b49e2346621bf473a0e897809f1c008
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712841"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 OpenID Connect로 웹 로그인

OpenID Connect는 웹 애플리케이션에 사용자를 안전하게 로그인하는 데 사용할 수 있도록 OAuth 2.0을 기반으로 하여 빌드된 인증 프로토콜입니다. OpenID Connect의 Azure AD B2C(Azure Active Directory B2C) 구현을 사용하여 웹 애플리케이션의 등록, 로그인 및 기타 ID 관리 환경을 Azure AD( Azure Active Directory)로 아웃소싱할 수 있습니다. 이 가이드에서는 언어에 관계 없이 이 작업을 수행하는 방법을 보여 줍니다. 오픈 소스 라이브러리를 사용하지 않고 HTTP 메시지를 보내고 받는 방법을 설명합니다.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)는 *인증* 프로토콜로 사용하기 위해 OAuth 2.0 *권한 부여* 프로토콜을 확장합니다. 此身份验证协议允许你执行单一登录。 它引入了*ID 令牌*的概念，该令牌允许客户端验证用户的身份，并获取有关用户的基本配置文件信息。

由于它扩展了 OAuth 2.0，因此它还允许应用程序安全获取*访问令牌*。 액세스 토큰을 사용하여 [권한 부여 서버](active-directory-b2c-reference-protocols.md)로 보안이 유지되는 리소스에 액세스할 수 있습니다. 如果要构建在服务器上托管并通过浏览器访问的 web 应用程序，则建议使用 OpenID Connect。 有关令牌的详细信息，请参阅[中的令牌概述 Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C는 단순한 인증 및 권한 부여 보다 더 많은 작업으로 표준 OpenID Connect 프로토콜을 확장합니다. 它引入了[用户流参数](active-directory-b2c-reference-policies.md)，使你可以使用 OpenID connect 向应用程序添加用户体验，例如注册、登录和配置文件管理。

## <a name="send-authentication-requests"></a>인증 요청 보내기

当 web 应用程序需要对用户进行身份验证并运行用户流时，可以将用户定向到 `/authorize` 终结点。 用户根据用户流执行操作。

在此请求中，客户端指示它需要在 `scope` 参数中从用户获取的权限，并指定要运行的用户流。 若要查看请求的工作方式，请尝试将请求粘贴到浏览器并运行该请求。 `{tenant}`을 테넌트 이름으로 바꿉니다. 将 `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` 替换为之前在租户中注册的应用程序的应用程序 ID。 还将策略名称（`{policy}`）更改为你在租户中拥有的策略名称，例如 `b2c_1_sign_in`。

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| 组织 | 예 | Azure AD B2C 租户的名称 |
| 政策 | 예 | 要运行的用户流。 指定在 Azure AD B2C 租户中创建的用户流的名称。 例如： `b2c_1_sign_in`、`b2c_1_sign_up`或 `b2c_1_edit_profile`。 |
| client_id | 예 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| nonce | 예 | 包含在请求中的值（由应用程序生成），在生成的 ID 令牌中包含为声明。 然后，应用程序可以验证此值，以减少令牌重放攻击。 값은 일반적으로 요청의 출처를 식별하는 데 사용할 수 있는 임의의 고유 문자열입니다. |
| response_type | 예 | 必须包括用于 OpenID Connect 的 ID 令牌。 如果 web 应用程序还需要标记来调用 web API，则可以使用 `code+id_token`。 |
| scope | 예 | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 对于 web 应用程序，`offline_access` 范围是可选的。 它表示你的应用程序将需要一个*刷新令牌*来扩展对资源的访问。 |
| prompt | 아닙니다. | 필요한 사용자 상호 작용의 형식입니다. 현재 유효한 값은 `login`뿐이며, 이는 사용자가 해당 요청에 대한 자격 증명을 입력하도록 합니다. |
| redirect_uri | 아닙니다. | 应用程序的 `redirect_uri` 参数，应用程序可在其中发送和接收身份验证响应。 它必须与你在 Azure 门户中注册的某个 `redirect_uri` 参数完全匹配，但必须对其进行 URL 编码。 |
| response_mode | 아닙니다. | 用于将生成的授权代码发送回应用程序的方法。 `query`, `form_post` 또는 `fragment` 중 하나일 수 있습니다.  최상의 보안을 위해 `form_post` 응답 모드를 사용하는 것이 좋습니다. |
| state | 아닙니다. | 请求中包含的值，也会在令牌响应中返回。 원하는 모든 콘텐츠의 문자열일 수 있습니다. 일반적으로 교차 사이트 요청 위조 공격을 방지하기 위해 임의로 생성된 고유 값이 사용됩니다. 状态还用于在身份验证请求出现之前，在应用程序中编码有关用户状态的信息，例如它们所在的页。 |

此时，系统会要求用户完成工作流。 用户可能必须输入其用户名和密码、使用社交标识登录或注册目录。 可能有任何其他数量的步骤，具体取决于如何定义用户流。

用户完成用户流后，将使用 `response_mode` 参数中指定的方法，将响应返回到应用程序中的指定 `redirect_uri` 参数。 对于上述每种情况，响应都是相同的，与用户流无关。

`response_mode=fragment` 를 사용한 성공적인 응답은 다음과 같습니다.

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | Description |
| --------- | ----------- |
| id_token | 应用程序请求的 ID 令牌。 ID 토큰을 사용하여 사용자 ID를 확인하고 사용자와 세션을 시작할 수 있습니다. |
| 코드 | 如果使用 `response_type=code+id_token`，则为应用程序请求的授权代码。 应用程序可以使用授权代码请求目标资源的访问令牌。 授权代码通常在大约10分钟后过期。 |
| state | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 应用程序应该验证请求和响应中的 `state` 值是否相同。 |

也可以将错误响应发送到 `redirect_uri` 参数，使应用程序可以适当地处理它们：

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| 매개 변수 | Description |
| --------- | ----------- |
| error | 可用于分类发生的错误类型的代码。 |
| error_description | 可帮助识别身份验证错误根本原因的特定错误消息。 |
| state | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 应用程序应该验证请求和响应中的 `state` 值是否相同。 |

## <a name="validate-the-id-token"></a>ID 토큰 유효성 검사

ID 토큰을 받는 것만으로는 사용자를 인증할 수 없습니다. 验证 ID 令牌的签名，并根据应用程序的要求验证令牌中的声明。 Azure AD B2C는 [JWT(JSON 웹 토큰)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 및 공개 키 암호화를 사용하여 토큰에 서명하고 토큰이 유효한지 확인합니다. 기본 설정의 언어에 따라 JWT의 유효성 검사에 사용할 수 있는 다양한 공개 소스 라이브러리가 있습니다. 고유한 유효성 검사 논리를 구현하는 것보다 이러한 옵션을 탐색하는 것이 좋습니다.

Azure AD B2C 具有 OpenID Connect 元数据终结点，该终结点允许应用程序在运行时获取有关 Azure AD B2C 的信息。 이 정보에는 엔드포인트, 토큰 콘텐츠 및 토큰 서명 키가 포함됩니다. B2C 테넌트에서 각 사용자 흐름에 대한 JSON 메타데이터 문서가 있습니다. 예를 들어 `fabrikamb2c.onmicrosoft.com`의 `b2c_1_sign_in` 사용자 흐름에 대한 메타데이터 문서는 다음 위치에 있습니다.

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

이 구성 문서의 속성 중 하나가 `jwks_uri`이며, 동일한 사용자 흐름에 대한 값은 다음과 같습니다.

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

若要确定用于对 ID 令牌进行签名的用户流（以及从何处获取元数据），可以使用两个选项。 먼저 사용자 흐름 이름이 ID 토큰의 `acr` 클레임에 포함됩니다. 다른 옵션은 요청을 실행할 때 `state` 매개 변수의 값에 사용자 흐름을 인코딩한 다음 이를 디코딩하여 어떤 사용자 흐름을 사용할지 결정하는 것입니다. 두 방법 중 하나는 유효합니다.

从 OpenID Connect 元数据终结点获取元数据文档后，可以使用 RSA 256 公钥验证 ID 令牌的签名。 此终结点上可能列出了多个密钥，每个密钥都由 `kid` 声明标识。 ID 토큰의 헤더에는 `kid` 클레임도 포함되어 있으며, 이는 이러한 키 중에서 ID 토큰 서명에 사용된 키를 나타냅니다.

若要验证令牌 Azure AD B2C，需要使用指数（e）和取模（n）生成公钥。 你需要根据相应的编程语言来确定如何执行此操作。 可在此处找到有关通过 RSA 协议生成公钥的官方文档： https://tools.ietf.org/html/rfc3447#section-3.1

ID 토큰의 서명에 대한 유효성을 검사한 후에는 확인해야 할 몇 가지 클레임이 있습니다. 예를 들면 다음과 같습니다.

- `nonce` 클레임의 유효성을 검사하여 토큰 재생 공격을 방지합니다. 해당 값이 로그인 요청에 지정된 값이어야 합니다.
- 验证 `aud` 声明，以确保为你的应用程序颁发了 ID 令牌。 其值应为应用程序的应用程序 ID。
- 验证 `iat` 和 `exp` 声明，确保 ID 令牌未过期。

또한 수행해야 하는 몇 가지 추가 유효성 검사가 있습니다. [OpenID Connect Core 规范](https://openid.net/specs/openid-connect-core-1_0.html)中详细介绍了验证。你可能还需要根据你的方案验证其他声明。 몇 가지 일반적인 유효성 검사는 다음과 같습니다.

- 确保用户/组织已注册了应用程序。
- 사용자에게 적절한 권한 부여/권한이 있는지 확인
- Azure Multi-Factor Authentication과 같은 특정 강도의 인증이 발생했는지 확인

验证 ID 令牌后，可以开始与用户的会话。 你可以使用 ID 令牌中的声明来获取有关应用程序中的用户的信息。 이 정보의 용도로 표시, 레코드 및 권한 부여가 있습니다.

## <a name="get-a-token"></a>토큰 가져오기

如果你需要 web 应用程序才能运行用户流，则可以跳过下面几节。 这些部分仅适用于需要对 web API 进行经过身份验证的调用的 web 应用程序，并且还受 Azure AD B2C 保护。

`response_type=code+id_token`을 사용하여 `POST` 요청을 `/token` 엔드포인트로 보내 원하는 리소스에 대한 토큰에 대해 얻은 권한 부여 코드를 사용할 수 있습니다. 在 Azure AD B2C 中，可以通过在请求中指定其作用域，照常[请求其他 api 的访问令牌](active-directory-b2c-access-tokens.md#request-a-token)。

你还可以通过使用应用的客户端 ID 作为请求的作用域来为你的应用程序的后端 Web API 请求访问令牌（这会导致使用该客户端 ID 的访问令牌为 "受众"）：

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| 组织 | 예 | Azure AD B2C 租户的名称 |
| 政策 | 예 | 권한 부여 코드를 획득하는 데 사용된 사용자 흐름입니다. 不能在此请求中使用其他用户流。 将此参数添加到查询字符串中，而不是添加到 POST 正文。 |
| client_id | 예 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| client_secret | 是，在 Web 应用中 | 在[Azure 门户](https://portal.azure.com/)中生成的应用程序机密。 在此流中，客户端密码用于 Web 应用方案，在这些方案中，客户端可以安全地存储客户端机密。 对于本机应用（公共客户端）方案，不能安全地存储客户端机密，threfore 不会在此流中使用。 如果使用客户端机密，请定期更改。 |
| 코드 | 예 | 用户流开始时获取的授权代码。 |
| grant_type | 예 | 권한 유형입니다. 인증 코드 흐름의 경우 `authorization_code`이어야 합니다. |
| redirect_uri | 예 | 권한 부여 코드를 받은 애플리케이션의 `redirect_uri` 매개 변수입니다. |
| scope | 아닙니다. | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 id_token 매개 변수의 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 它可用于获取应用程序自己的后端 web API 的标记，它由与客户端相同的应用程序 ID 表示。 `offline_access` 作用域表示应用程序需要刷新令牌才能对资源进行扩展访问。 |

성공적인 토큰 응답은 다음과 같습니다.

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 매개 변수 | Description |
| --------- | ----------- |
| not_before | epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type | 토큰 형식 값입니다. `Bearer` 是唯一受支持的类型。 |
| access_token | 사용자가 요청한 서명된 JWT 토큰입니다. |
| scope | 토큰이 유효한 범위입니다. |
| expires_in | 액세스 토큰이 유효한 시간(초)입니다. |
| refresh_token | OAuth 2.0 새로 고침 토큰입니다. 应用程序可以使用此令牌在当前令牌过期后获取其他令牌。 刷新令牌可用于长时间保留对资源的访问权限。 必须在授权和令牌请求中都使用了范围 `offline_access`，才能接收刷新令牌。 |

오류 응답은 다음과 같습니다.

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | Description |
| --------- | ----------- |
| error | 可用于对发生的错误类型进行分类的代码。 |
| error_description | 可帮助识别身份验证错误根本原因的消息。 |

## <a name="use-the-token"></a>토큰 사용

액세스 토큰을 성공적으로 얻었으므로 이제 `Authorization` 헤더에 포함하여 백 엔드 웹 API에 대한 요청에서 토큰을 사용할 수 있습니다.

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>토큰 새로 고침

ID 令牌会在短时间内过期。 在令牌过期后刷新，以继续访问资源。 可以通过向 `/token` 终结点提交另一个 `POST` 请求来刷新令牌。 이번에는 `code` 매개 변수 대신 `refresh_token` 매개 변수를 제공합니다.

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| 组织 | 예 | Azure AD B2C 租户的名称 |
| 政策 | 예 | 원래의 새로 고침 토큰을 얻는 데 사용된 사용자 흐름입니다. 不能在此请求中使用其他用户流。 将此参数添加到查询字符串中，而不是添加到 POST 正文。 |
| client_id | 예 | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。 |
| client_secret | 是，在 Web 应用中 | 在[Azure 门户](https://portal.azure.com/)中生成的应用程序机密。 在此流中，客户端密码用于 Web 应用方案，在这些方案中，客户端可以安全地存储客户端机密。 对于本机应用（公共客户端）方案，不能安全地存储客户端机密，threfore 不能用于此调用。 如果使用客户端机密，请定期更改。 |
| grant_type | 예 | 授权的类型，该类型必须是授权代码流的此部分的刷新令牌。 |
| refresh_token | 예 | 流的第二部分中获取的原始刷新令牌。 必须在授权和令牌请求中都使用 `offline_access` 作用域，才能接收刷新令牌。 |
| redirect_uri | 아닙니다. | 권한 부여 코드를 받은 애플리케이션의 `redirect_uri` 매개 변수입니다. |
| scope | 아닙니다. | 공백으로 구분된 범위 목록입니다. `openid` 범위는 사용자에게 로그인하고 ID 토큰 형식으로 사용자에 대한 데이터를 가져올 권한을 나타냅니다. 它可用于将令牌发送到应用程序的后端 web API，该 API 由与客户端相同的应用程序 ID 表示。 `offline_access` 作用域表示应用程序需要刷新令牌才能对资源进行扩展访问。 |

성공적인 토큰 응답은 다음과 같습니다.

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| 매개 변수 | Description |
| --------- | ----------- |
| not_before | epoch 시간에서 토큰은 유효한 것으로 간주되는 시간입니다. |
| token_type | 토큰 형식 값입니다. `Bearer` 是唯一受支持的类型。 |
| access_token | 请求的已签名 JWT 标记。 |
| scope | 令牌的有效范围。 |
| expires_in | 액세스 토큰이 유효한 시간(초)입니다. |
| refresh_token | OAuth 2.0 새로 고침 토큰입니다. 应用程序可以使用此令牌在当前令牌过期后获取其他令牌。 刷新令牌可用于长时间保留对资源的访问权限。 |

오류 응답은 다음과 같습니다.

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| 매개 변수 | Description |
| --------- | ----------- |
| error | 可用于对发生的错误类型进行分类的代码。 |
| error_description | 可帮助识别身份验证错误根本原因的消息。 |

## <a name="send-a-sign-out-request"></a>로그아웃 요청 보내기

如果要从应用程序中注销用户，则不能清除应用程序的 cookie 或结束与用户的会话。 将用户重定向到 Azure AD B2C 以注销。如果无法执行此操作，用户可以重新向应用程序进行身份验证，而无需再次输入其凭据。

若要注销用户，请将用户重定向到前面所述的 OpenID Connect 元数据文档中列出的 `end_session` 终结点：

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| 매개 변수 | 필수 | Description |
| --------- | -------- | ----------- |
| 组织 | 예 | Azure AD B2C 租户的名称 |
| 政策 | 예 | 애플리케이션에서 사용자를 로그아웃하는 데 사용하려는 사용자 흐름입니다. |
| id_token_hint| 아닙니다. | 以前颁发的 ID 令牌，作为有关最终用户当前通过身份验证的会话与客户端的提示传递到注销终结点。 `id_token_hint` 确保 `post_logout_redirect_uri` 是 Azure AD B2C 应用程序设置中的已注册答复 URL。 |
| client_id | 아니요* | [Azure 门户](https://portal.azure.com/)分配给应用程序的应用程序 ID。<br><br>\**如果使用 `Application` 隔离 SSO 配置，并要求注销请求中的_ID 令牌_设置为 `No`，则此项是必需的。* |
| post_logout_redirect_uri | 아닙니다. | 用户在成功注销后应重定向到的 URL。如果未包含，Azure AD B2C 向用户显示一般消息。 除非提供 `id_token_hint`，否则不应将此 URL 注册为 Azure AD B2C 应用程序设置中的答复 URL。 |
| state | 아닙니다. | 요청에 `state` 매개 변수가 포함되어 있으면 동일한 값이 응답에도 나타나야 합니다. 应用程序应该验证请求和响应中的 `state` 值是否相同。 |

### <a name="secure-your-logout-redirect"></a>保护注销重定向

注销后，用户将被重定向到 `post_logout_redirect_uri` 参数中指定的 URI，而不考虑已为应用程序指定的回复 Url。 但是，如果传递了有效的 `id_token_hint`，Azure AD B2C 将在执行重定向之前，验证 `post_logout_redirect_uri` 的值是否与应用程序的配置重定向 Uri 之一匹配。 如果没有为应用程序配置匹配的答复 URL，将显示一条错误消息，并且不会重定向用户。

### <a name="external-identity-provider-sign-out"></a>外部标识提供者注销

将用户定向到 `end_session` 终结点会清除用户 Azure AD B2C 的部分用户单一登录状态，但不会将用户从其社交标识提供者（IDP）会话中注销。 如果用户在后续登录中选择相同的 IDP，则会重新进行身份验证，而无需输入其凭据。 如果用户想要从应用程序中注销，则不一定意味着他们要注销其 Facebook 帐户。 但是，如果使用了本地帐户，用户的会话便会正确结束。
