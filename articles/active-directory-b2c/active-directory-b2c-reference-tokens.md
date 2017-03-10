---
title: Azure Active Directory B2C | Microsoft Docs
description: "Azure Active Directory B2C 中颁发的令牌类型。"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 6df79878-65cb-4dfc-98bb-2b328055bc2e
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 0ae9ad40f2e32d56fd50c90b86339cbb458d7291
ms.openlocfilehash: a3276c764ebb6382594cf7002e7c7e8e328862ef
ms.lasthandoff: 03/09/2017


---
# <a name="azure-ad-b2c-token-reference"></a>Azure AD B2C：令牌参考
Azure Active Directory (Azure AD) 在每个[身份验证流](active-directory-b2c-apps.md)的处理过程中发出多种安全令牌。 本文档说明每种令牌的格式、安全特征和内容。

## <a name="types-of-tokens"></a>类型的令牌
Azure AD B2B 支持 [OAuth 2.0 身份验证协议](active-directory-b2c-reference-protocols.md)，该协议使用访问令牌和刷新令牌。 它还支持通过 [OpenID Connect](active-directory-b2c-reference-protocols.md) 进行身份验证和登录，其中引入了第三种类型的令牌：ID 令牌。 每个令牌表示为“持有者令牌”。

持有者令牌是一种轻型安全令牌，它授予对受保护资源的“持有者”访问权限。 持有者是可以提供令牌的任何一方。 参与方必须先经 Azure AD 验证才能接收持有者令牌。 但如果不采取必要的步骤在传输过程和存储中对令牌进行保护，令牌可能会被意外的某一方拦截并使用。 某些安全令牌具有防止未授权方使用令牌的内置机制，但持有者令牌并不具有这种机制。 它们必须在安全通道中传输，例如传输层安全性 (HTTPS)。

如果持有者令牌在安全通道外传输，则恶意方就可以利用中间人攻击来获得令牌，并使用它对受保护资源进行未经授权的访问。 存储或缓存持有者令牌以供以后使用时，也应遵循同样的安全原则。 请始终确保你的应用以安全的方式传输和存储持有者令牌。

有关持有者令牌的其他安全注意事项，请参阅 [RFC 6750 第 5 部分](http://tools.ietf.org/html/rfc6750)。

Azure AD B2C 颁发的许多令牌都实现为 JSON Web 令牌 (JWT)。 JWT 是一种精简的 URL 安全方法，可在两方之间传输信息。 JWT 包含称为“声明”的信息。 这些信息是令牌持有人和令牌主题的相关断言信息。 JWT 中的声明是为了传输而编码和序列化的 JSON 对象。 由于 Azure AD 所颁发的 JWT 已签名但未加密，因此可以轻松检查 JWT 的内容以进行调试。 有多个工具可执行此操作，其中包括 [calebb.net](http://calebb.net)。 有关 JWT 的详细信息，请参阅 [JWT 规范](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

### <a name="id-tokens"></a>ID 令牌
ID 令牌是应用从 Azure AD B2C `authorize` 和 `token` 终结点接收的一种安全令牌。 ID 令牌表示为 [JWT](#types-of-tokens)，并且它们包含可用于标识应用中的用户的声明。 从 `authorize` 终结点获取 ID 令牌时，它们通常用来使用户登录 Web 应用程序。 从 `token` 终结点获取 ID 令牌时，它们可在同一应用程序或服务的两个组件之间进行通信时，在 HTTP 请求中发送。 可以根据需要使用 ID 令牌中的声明。 它们常用于显示帐户信息或者在应用中进行访问控制决策。  

ID 令牌已签名，但目前不会加密。 当应用或 API 收到 ID 令牌时，必须[验证签名](#token-validation)，证明令牌可信。 应用或 API 还必须验证令牌中的一些声明，证明令牌有效。 根据具体的方案要求，应用验证的声明有所不同，但应用在每个方案中均须执行某些[常见声明验证](#token-validation)。

#### <a name="sample-id-token"></a>示例 ID 令牌
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>访问令牌
访问令牌也是应用从 Azure AD B2C `authorize` 和 `token` 终结点接收的一种安全令牌。 访问令牌也表示为 [JWT](#types-of-tokens)，并且它们包含声明，可用于标识 Web 服务和 API 中的用户。

访问令牌已签名，但目前不会加密 - 与 ID 令牌十分相似。  访问令牌应用来提供对 Web 服务和 API 的访问，并标识这些服务中的用户和进行身份验证。  但是，它们不提供任何有关这些服务的授权断言。  也就是说，访问令牌中的 `scp` 声明不会限制或表示已授予令牌使用者的访问权限。

当 API 收到访问令牌时，必须[验证签名](#token-validation)，证明令牌可信。 API 还必须验证令牌中的一些声明，证明令牌有效。 根据具体的方案要求，应用验证的声明有所不同，但应用在每个方案中均须执行某些[常见声明验证](#token-validation)。

### <a name="claims-in-id--access-tokens"></a>ID 令牌和访问令牌中的声明
使用 Azure AD B2C 时，必须对令牌内容有精细的控制。 可以配置[策略](active-directory-b2c-reference-policies.md)，在声明中发送应用操作所需的几组特定的用户数据。 这些声明可以包括标准属性，比如用户的 `displayName` 和 `emailAddress` 属性。 还可以包括可在 B2C 目录中定义的[自定义用户属性](active-directory-b2c-reference-custom-attr.md)。 收到的每个 ID 令牌和访问令牌都会包含一组特定的安全相关声明。 应用程序可以使用这些声明来安全地对用户和请求进行身份验证。

请注意，ID 令牌中的声明不按任何特定顺序返回。 此外，新的声明可以在任何时候引入 ID 令牌。 引入新的声明时，不得中断应用。 以下是期望在 Azure AD B2C 颁发的 ID 令牌和访问令牌中存在的声明。 任何其他声明将由策略确定。 练习时，请尝试将示例 ID 令牌中的声明粘贴到 [calebb.net](http://calebb.net) 中进行检查。 可以在 [OpenID Connect 规范](http://openid.net/specs/openid-connect-core-1_0.html)中找到进一步的详细信息。

| Name | 声明 | 示例值 | 说明 |
| --- | --- | --- | --- |
| 目标受众 |`aud` |`90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` |受众声明标识令牌的目标接收方。 对于 Azure AD B2C，受众是在应用注册门户中分配给应用的应用程序 ID。 应用应该验证此值并拒绝不匹配的令牌。 |
| 颁发者 |`iss` |`https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` |此声明标识构造并返回令牌的安全令牌服务 (STS)。 它还标识在其中进行用户身份验证的 Azure AD 目录。 应用应该验证颁发者声明，以确保令牌来自 v2.0 终结点。 |
| 颁发时间 |`iat` |`1438535543` |此声明表示颁发令牌的时间，以新纪元时间表示。 |
| 过期时间 |`exp` |`1438539443` |此过期时间声明表示令牌失效的时间，以纪元时间表示。 应用应该使用此声明来验证令牌生存期的有效性。 |
| 生效时间 |`nbf` |`1438535543` |此声明表示令牌生效的时间，以新纪元时间表示。 这通常与颁发令牌的时间相同。 应用应该使用此声明来验证令牌生存期的有效性。 |
| 版本 |`ver` |`1.0` |这是 Azure AD 定义的 ID 令牌版本。 |
| 代码哈希 |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |仅当令牌随 OAuth 2.0 授权代码一起颁发时，代码哈希才包含在 ID 令牌中。 代码哈希可用于验证授权代码的真实性。 有关如何执行此验证的详细信息，请参阅 [OpenID Connect 规范](http://openid.net/specs/openid-connect-core-1_0.html)。 |
| 访问令牌哈希 |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |仅当令牌随 OAuth 2.0 访问令牌一起颁发时，访问令牌才包含在 ID 令牌中。 访问令牌哈希可用于验证访问令牌的真实性。 有关如何执行此验证的详细信息，请参阅 [OpenID Connect 规范](http://openid.net/specs/openid-connect-core-1_0.html)。 |
| Nonce |`nonce` |`12345` |Nonce 是缓和令牌重放攻击的策略。 应用可通过使用 `nonce` 查询参数，在授权请求中指定 nonce。 在请求中提供的值将仅在 ID 令牌的 `nonce` 声明中发出（未经修改）。 这可让应用根据在请求上指定的值验证此值，使应用的会话与给定的 ID 令牌相关联。 应用可在 ID 令牌验证过程中执行这项验证。 |
| 使用者 |`sub` |`884408e1-2918-4cz0-b12d-3aa027d7563b` |这是令牌针对其断言信息的主体，例如应用的用户。 此值是固定不变的，无法重新分配或重复使用。 可使用它安全地执行授权检查，例如，使用令牌访问资源时。 默认情况下，将使用目录中用户的对象 ID 填充使用者声明。 请阅读[此文](active-directory-b2c-token-session-sso.md)了解详细信息。 |
| 身份验证上下文类引用 |`acr` |不适用 |除非是在旧策略中，否则目前不使用。 请阅读[此文](active-directory-b2c-token-session-sso.md)了解详细信息。 |
| 信任框架策略 |`tfp` |`b2c_1_sign_in` |这是用于获取 ID 令牌的策略名称。 |
| 身份验证时间 |`auth_time` |`1438535543` |此声明是用户最后一次输入凭据的时间，以新纪元时间表示。 |

### <a name="refresh-tokens"></a>刷新令牌
刷新令牌是应用可用于在 OAuth 2.0 流中获取新 ID 令牌和访问令牌的安全令牌。 它们向应用提供代表用户长期访问资源的权限，而无需与这些用户进行交互。

若要在令牌响应中接收刷新令牌，应用必须请求 `offline_acesss` 范围。 若要了解有关 `offline_access` 范围的详细信息，请参阅 [Azure AD B2C 协议参考](active-directory-b2c-reference-protocols.md)。

刷新令牌永远对应用程序完全不透明。 它们由 Azure AD 颁发，且只能由 Azure AD 检查和解释。 它们属于长效令牌，但编写应用时，不应期望刷新令牌将持续一段特定时间。 出于各种原因，可随时验证刷新令牌。 让应用知道刷新令牌是否有效的唯一方式就是对 Azure AD 发出令牌请求以尝试兑换刷新令牌。

使用刷新令牌兑换新的访问令牌（而且应用已获得 `offline_access` 范围）时，将在令牌响应中收到新的刷新令牌。 应该保存新颁发的刷新令牌。 它应替代以前在请求中使用的刷新令牌。 这有助于保证刷新令牌尽可能长期保持有效。

## <a name="token-validation"></a>令牌验证
若要验证令牌，应用应检查令牌的签名和声明。

许多开放源代码库都可用于验证 JWT，具体取决于你的首选语言。 建议浏览这些选项，而不是实施自己的验证逻辑。 本指南中的信息可帮助了解如何正确使用这些库。

### <a name="validate-the-signature"></a>验证签名
JWT 包含三段，以 `.` 字符分隔。 第一个段称为**标头**，第二个称为**主体**，第三个称为**签名**。 签名段可用于验证令牌的真实性，使应用信任它。

Azure AD B2C 令牌使用行业标准非对称式加密算法（例如 RSA 256）进行签名。 令牌标头包含用于签名令牌的密钥和加密方法的相关信息：

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` 声明指示用于对令牌签名的算法。 `kid` 声明指示用于对令牌签名的特殊公钥。

在任何给定时间，Azure AD 都可能使用一组特定公钥-私钥对中的任意一个对令牌签名。 Azure AD 定期换用一组可能的密钥，因此应将应用编写成自动处理这些密钥更改。 检查 Azure AD 所用公钥的更新的合理频率大约为每 24 小时一次。

Azure AD B2C 具有 OpenID Connect 元数据终结点。 这允许应用在运行时提取 Azure AD B2C 的相关信息。 此信息包括终结点、令牌内容和令牌签名密钥。 B2C 目录针对每个策略都有一个 JSON 元数据文档。 例如，`fabrikamb2c.onmicrosoft.com` 中的 `b2c_1_sign_in` 策略的元数据文档位于：

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` 是用于对用户进行身份验证的 B2C 目录，`b2c_1_sign_in` 是用来获取令牌的策略。 若要确定对令牌签名所用的策略（以及提取元数据的位置），你有两个选择。 首先，策略名称包含在令牌的 `acr` 声明中。 可以通过对主体进行 Base-64 解码，并反序列化生成的 JSON 字符串，从而分析 JWT 主体中的声明。 `acr` 声明将成为用于颁发令牌的策略名称。  另一个方法是在发出请求时在 `state` 参数的值中对策略进行编码，然后对其进行解码以确定使用的策略。 任意一种方法均有效。

元数据文档是包含多条有用信息的 JSON 对象。 其中包括执行 OpenID Connect 身份验证所需的终结点的位置。 还包括 `jwks_uri`，它提供用于对令牌签名的公钥集位置。 在此处提供该位置，但最好使用元数据文档并分析 `jwks_uri` 来动态提取位置：

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

位于此 URL 的 JSON 文档包含将在特定时间点使用的所有公钥信息。 应用可以使用 JWT 标头中的 `kid` 声明，选择用于签名特定令牌的 JSON 文档中的公钥。 然后可以使用正确的公钥和指定的算法来执行签名验证。

关于如何执行签名验证的说明已超出了本文档的讨论范围。 如果需要，许多开放源代码库都可用于对此进行帮助。

### <a name="validate-the-claims"></a>验证声明
应用或 API 收到 ID 令牌时，还应对 ID 令牌中的声明执行几项检查。 这些检查包括但不限于：

* **受众**声明：验证是否计划向应用提供 ID 令牌。
* **生效时间**和**过期时间**声明：验证 ID 令牌是否未过期。
* **颁发者**声明：验证令牌是否确实由 Azure AD 向应用颁发。
* **Nonce**：这是缓和令牌重放攻击的策略。

有关应用应该执行的验证的完整列表，请参阅 [OpenID Connect 规范](https://openid.net)。 这些声明的预期值详细信息包含在前面的[令牌部分](#types-of-tokens)中。  

## <a name="token-lifetimes"></a>令牌生存期
提供以下令牌生存期，以拓展你的知识。 它们在开发和调试应用时有所帮助。 请注意：编写应用时，不应期望任何这些生存期维持不变。 它们可以并且将会改变。  可以在[此处](active-directory-b2c-token-session-sso.md)阅读有关在 Azure AD B2C 自定义令牌生存期的详细信息。

| 令牌 | 生存期 | 说明 |
| --- | --- | --- |
| ID 令牌 |一小时 |ID 令牌的有效期通常为&1; 小时。 Web 应用可使用此生存期维护其与用户的会话（推荐）。 也可以选择不同的会话生存期。 如果应用需要获取新的 ID 令牌，只需要对 Azure AD 发出新的登录请求即可。 如果用户与 Azure AD 具有有效的浏览器会话，则可能不会要求该用户再次输入凭据。 |
| 刷新令牌 |最长 14 天 |单个刷新令牌的有效期最长为 14 天。 但是，刷新令牌可能随时会因为很多原因失效。 应用应继续尝试使用刷新令牌，直到该请求失败，或直到应用使用新的刷新令牌替换该刷新令牌。  如果自用户上次输入凭据已过 90 天，刷新令牌也可能失效。 |
| 授权代码 |五分钟 |授权代码均有意设定为短期有效。 在收到授权代码时，应立即兑换访问令牌、ID 令牌或刷新令牌。 |


