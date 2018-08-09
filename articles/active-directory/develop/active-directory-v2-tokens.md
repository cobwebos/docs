---
title: Azure Active Directory v2.0 令牌引用 | Microsoft Docs
description: Azure AD v2.0 终结点发出的令牌和声明类型
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: dc58c282-9684-4b38-b151-f3e079f034fd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1b0db9ad6d72268ff6074ab2b21026cc04758fbd
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504186"
---
# <a name="azure-active-directory-v20-tokens-reference"></a>Azure Active Directory v2.0 令牌引用
Azure Active Directory (Azure AD) v2.0 在每个[身份验证流](active-directory-v2-flows.md)中发出多种安全令牌。 此引用说明每种令牌的格式、安全特征和内容。

> [!NOTE]
> v2.0 终结点并不支持所有 Azure Active Directory 方案和功能。 若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。
>
>

## <a name="types-of-tokens"></a>类型的令牌
v2.0 终结点支持 [OAuth 2.0 授权协议](active-directory-v2-protocols.md)，该协议使用访问令牌和刷新令牌。 V2.0 终结点还支持通过 [OpenID Connect](active-directory-v2-protocols.md) 进行身份验证和登录。 OpenID Connect 引入了第三种令牌，即 ID 令牌。 每个令牌表示为持有者令牌。

持有者令牌是一种轻型安全令牌，它授予对受保护资源的持有者访问权限。 持有者是可以提供令牌的任何一方。 虽然某一方必须通过 Azure AD 的身份验证才能收到持有者令牌，但如果在传输和存储过程中不采取措施对令牌进行保护，令牌可能会被非预期方截获并使用。 某些安全令牌具有防止未授权方使用令牌的内置机制，但持有者令牌不具有这种机制。 必须在安全通道中传输持有者令牌，例如传输层安全性 (HTTPS)。 如果未在这种安全条件下传输持有者令牌，恶意方便可利用“中间人攻击”来获得令牌，并使用它对受保护资源进行未经授权的访问。 当存储或缓存持有者令牌供以后使用时，也应遵循同样的安全原则。 请始终确保应用以安全方式传输和存储持有者令牌。 有关持有者令牌的更多安全注意事项，请参阅 [RFC 6750 第 5 部分](http://tools.ietf.org/html/rfc6750)。

v2.0 终结点颁发的许多令牌都实现为 JSON Web 令牌 (JWT)。 JWT 是一种精简的 URL 安全方法，用于在两方之间传输信息。 JWT 中的信息称为声明。 声明是令牌持有者和令牌主题的相关信息的断言。 JWT 中的声明是为了传输而编码和序列化的 JavaScript 对象表示法 (JSON) 对象。 由于 v2.0 终结点所颁发的 JWT 已签名但未加密，因此可以轻松检查 JWT 的内容以进行调试。 有关 JWT 的详细信息，请参阅 [JWT 规范](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)。

### <a name="id-tokens"></a>ID 令牌
ID 令牌是应用使用 [OpenID Connect](active-directory-v2-protocols.md) 执行身份验证时收到的一种登录安全令牌形式。 ID 令牌以 [JWT](#types-of-tokens) 表示，包含可让用户登录应用的声明。 可采用多种方式在 ID 令牌中使用声明。 通常，管理员使用 ID 令牌来显示帐户信息或者在应用中作出访问控制决策。 v2.0 终结点只颁发一种 ID 令牌，无论登录用户的类型为何，该令牌都具有一组一致的声明。 ID 令牌的格式和内容与个人 Microsoft 帐户用户及工作或学校帐户的格式和内容相同。

目前，ID 令牌已签名但未加密。 应用收到 ID 令牌时，必须[验证签名](#validating-tokens)以证明令牌的真实性，并验证令牌中的几个声明来证明其有效性。 应用验证的声明因方案要求而异，但应用必须在每个方案中执行某些[常见声明验证](#validating-tokens)。

下面部分除了提供示例 ID 令牌，还提供有关 ID 令牌声明的完整详细信息。 请注意，ID 令牌中的声明不按特定顺序返回。 此外，可在任何时候将新声明引入 ID 令牌。 引入新声明时，切勿中断应用。 以下列表包含应用当前能够可靠解读的声明。 可在 [OpenID Connect 规范](http://openid.net/specs/openid-connect-core-1_0.html)中找到更多详细信息。

#### <a name="sample-id-token"></a>示例 ID 令牌
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [!TIP]
> 练习时，要检查示例 ID 令牌中的声明，请将示例 ID 令牌粘贴到 [jwt.ms](http://jwt.ms/)。
>
>

#### <a name="claims-in-id-tokens"></a>ID 令牌中的声明
| 名称 | 声明 | 示例值 | Description |
| --- | --- | --- | --- |
| audience |`aud` |`6731de76-14a6-49ae-97bc-6eba6914391e` |标识令牌的目标接收方。 在 ID 令牌中，受众是在 Microsoft 应用程序注册门户中分配给应用的应用程序 ID。 应用应该验证此值并拒绝其值不匹配的令牌。 |
| 颁发者 |`iss` |`https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` |标识构造并返回令牌的安全令牌服务 (STS)，以及对用户进行身份验证的 Azure AD 租户。 应用应该验证颁发者声明，以确保令牌来自 v2.0 终结点。 也可使用声明的 GUID 部分限制可登录应用的租户集。 表示用户是来自 Microsoft 帐户的使用者用户的 GUID 为 `9188040d-6c67-4c5b-b112-36a304b66dad`。 |
| 颁发时间 |`iat` |`1452285331` |颁发令牌的时间，以纪元时间表示。 |
| 过期时间 |`exp` |`1452289231` |令牌失效的时间，以纪元时间表示。 应用应该使用此声明来验证令牌生存期的有效性。 |
| 生效时间 |`nbf` |`1452285331` |令牌生效的时间，以纪元时间表示。 通常与颁发时间相同。 应用应该使用此声明来验证令牌生存期的有效性。 |
| 版本 |`ver` |`2.0` |Azure AD 定义的 ID 令牌版本。 对于 v2.0 终结点，该值为 `2.0`。 |
| 租户 ID |`tid` |`b9419818-09af-49c2-b0c3-653adc1f376e` |表示用户所属 Azure AD 租户的 GUID。 对于工作和学校帐户，此 GUID 就是用户所属组织的不可变租户 ID。 对于个人帐户，该值为 `9188040d-6c67-4c5b-b112-36a304b66dad`。 需要 `profile` 范围才能接收此声明。 |
| 代码哈希 |`c_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |仅在 ID 令牌随 OAuth 2.0 授权代码一起颁发时，代码哈希才包含在 ID 令牌中。 它可用于验证授权代码的真实性。 有关执行此验证的详细信息，请参阅 [OpenID Connect 规范](http://openid.net/specs/openid-connect-core-1_0.html)。 |
| 访问令牌哈希 |`at_hash` |`SGCPtt01wxwfgnYZy2VJtQ` |仅在 ID 令牌随 OAuth 2.0 访问令牌一起颁发时，访问令牌哈希才包含在 ID 令牌中。 它可用于验证访问令牌的真实性。 有关执行此验证的详细信息，请参阅 [OpenID Connect 规范](http://openid.net/specs/openid-connect-core-1_0.html)。 |
| nonce |`nonce` |`12345` |Nonce 是缓和令牌重放攻击的策略。 应用可通过使用 `nonce` 查询参数，在授权请求中指定 nonce。 在请求中提供的值会在 ID 令牌的 `nonce` 声明中发出（未经修改）。 应用可根据在请求上指定的值验证此值，使应用会话与特定 ID 令牌相关联。 应用可在 ID 令牌验证过程中执行这项验证。 |
| 名称 |`name` |`Babe Ruth` |此名称声明提供了标识令牌使用者的用户可读值。 此值不一定唯一，它是可变的，旨在仅用于显示目的。 需要 `profile` 范围才能接收此声明。 |
| 电子邮件 |`email` |`thegreatbambino@nyy.onmicrosoft.com` |与用户帐户关联的主要电子邮件地址（如果有）。 其值可变，并可能随时间而不断改变。 需要 `email` 范围才能接收此声明。 |
| 首选用户名 |`preferred_username` |`thegreatbambino@nyy.onmicrosoft.com` |表示 v2.0 终结点中用户的主用户名。 它可以是电子邮件地址、电话号码或未指定格式的一般用户名。 其值可变，并可能随时间而不断改变。 由于此值是可变的，因此它不能用于做出授权决定。 需要 `profile` 范围才能接收此声明。 |
| subject |`sub` |`MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | 令牌针对其断言信息的主体，例如应用的用户。 此值是固定不变的，无法重新分配或重复使用。 可使用它安全地执行授权检查（例如，使用令牌访问资源时），并可将它用作数据库表中的键。 由于使用者始终存在于 Azure AD 颁发的令牌中，因此建议在通用授权系统中使用此值。 但是，使用者是成对标识符 - 它对特定应用程序 ID 是唯一的。 因此，如果单个用户使用两个不同的客户端 ID 登录到两个不同的应用，这些应用将收到两个不同的使用者声明值。 这不一定是所需的，具体取决于体系结构和隐私要求。 |
| 对象 ID |`oid` |`a1dbdde8-e4f9-4571-ad93-3059e3750d23` | 在 Microsoft 标识系统中，对象的不可变标识符在这种情况下是用户帐户。 还可以使用它安全地执行授权检查，并将它用作数据库表中的键。 此 ID 唯一标识应用程序中的用户 - 同一个用户登录两个不同的应用程序会在 `oid` 声明中收到相同值。 这意味着，对 Microsoft Online Services（如 Microsoft Graph）进行查询时可以使用它。 Microsoft Graph 将返回此 ID 作为给定用户帐户的 `id` 属性。 因为 `oid` 允许多个应用关联用户，需要 `profile` 作用域才能收到此声明。 请注意，如果单个用户存在于多个租户中，该用户将包含每个租户中的不同对象 ID - 它们将视为不同帐户，即使用户使用相同的凭据登录到每个帐户，也是如此。 |

### <a name="access-tokens"></a>访问令牌

v2.0 终结点允许向 Azure AD 进行了注册的第三方应用为受保护的资源（例如 Web API）颁发访问令牌。 有关设置应用程序来颁发访问令牌的详细信息，请参阅[如何向 v2.0 终结点注册应用](quickstart-v2-register-an-app.md)。 在向 v2.0 终结点注册应用程序时，开发者可以指定可以为其颁发访问令牌的访问级别（称为**作用域**）。 例如，在 Microsoft Graph API 中定义的 **calendars.read** 作用域授权读取用户的日历。 当应用程序从 v2.0 终结点收到访问令牌时，你必须根据方案对令牌的签名、颁发者、受众、过期时间和任何其他声明进行验证。 

向 v2.0 终结点请求访问令牌时，v2.0 终结点还会返回有关访问令牌的元数据，以供应用使用。 此信息包括访问令牌的到期时间及其有效范围。 应用使用此元数据执行访问令牌的智能缓存，无需打开和分析访问令牌本身。

### <a name="refresh-tokens"></a>刷新令牌
刷新令牌是应用可用于在 OAuth 2.0 流中获取新访问令牌的安全令牌。 应用可使用刷新令牌以用户身份长期访问资源，而无需与用户交互。

刷新令牌属于多资源令牌。 在一个资源的令牌请求期间收到的刷新令牌可兑换为完全不同资源的访问令牌。

若要在令牌响应中接收刷新，应用必须请求并获得 `offline_access` 范围。 若要了解有关 `offline_access` 范围的详细信息，请参阅[许可和范围](active-directory-v2-scopes.md)一文。

刷新令牌永远对应用程序完全不透明。 它们由 Azure AD v2.0 终结点颁发，并只能由 v2.0 终结点检查和解译。 它们属于长效令牌，但你不应将应用编写成预期刷新令牌将持续任何一段时间。 刷新令牌可能由于各种原因而随时失效 - 有关详细信息，请参阅[令牌吊销](active-directory-token-and-claims.md#token-revocation)。 让应用知道刷新令牌是否有效的唯一方式就是对 v2.0 终结点发出令牌请求以尝试兑换。

使用刷新令牌兑换新的访问令牌（而且应用已获得 `offline_access` 范围）时，会在令牌响应中收到新的刷新令牌。 保存新颁发的刷新令牌，以替换请求中使用的刷新令牌。 这可保证刷新令牌尽可能长期保持有效。

## <a name="validating-tokens"></a>验证令牌
目前，应用需执行的唯一令牌验证就是验证 ID 令牌。 若要验证 ID 令牌，应用需验证 ID 令牌签名和 ID 令牌中的声明。

<!-- TODO: Link --> Microsoft 提供库和代码示例以演示如何轻松处理令牌验证。 在后续部分，我们介绍基本流程。 多个第三方开源库也可用于 JWT 验证。 几乎所有平台和语言都有至少一个库选项。

### <a name="validate-the-signature"></a>验证签名
JWT 包含三个段（以 `.` 字符分隔）。 第一个段称为标头，第二个段称为主体，第三个段称为签名。 签名段可用于验证 ID 令牌的真实性，使应用信任它。

ID 令牌使用行业标准非对称加密算法（例如 RSA 256）进行签名。 ID 令牌标头具有用于签名令牌的密钥和加密方法的相关信息。 例如：

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

`alg` 声明指示用于对令牌签名的算法。 `kid` 声明指示用于对令牌签名的公钥。

v2.0 终结点使用一组特定公钥-私钥对中的任何一对来对 ID 和访问令牌进行签名。 v2.0 终结点定期换用一组可能的密钥，因此应将应用编写成自动处理这些密钥更改。 检查 v2.0 终结点所用公钥的更新合理频率为每 24 小时一次。

可以使用位于以下位置的 OpenID Connect 元数据文档来获取验证签名所需的签名密钥数据：

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 在浏览器中尝试打开此 URL！

此元数据文档是一个 JSON 对象，其中包含一些有用信息，例如执行 OpenID Connect 身份验证所需各种终结点的位置。 文档还包含 *jwks_uri*，其提供用于对令牌签名的公钥集位置。 位于 jwks_uri 中的 JSON 文档包含当前正使用的所有公钥信息。 应用可使用 JWT 标头中的 `kid` 声明选择本文档中已用于对令牌签名的公钥。 然后可以使用正确的公钥和指定的算法来执行签名验证。

> [!NOTE]
> `x5t` 声明在 v2.0 终结点中已弃用。 我们建议使用 `kid` 声明来验证令牌。

本文档未说明如何执行签名验证。 许多开源代码库均提供了此说明。

### <a name="validate-the-claims"></a>验证声明
若应用在用户登录时收到 ID 令牌，则还应对 ID 令牌中的声明执行一些检查。 这些检查包括但不限于：

* **受众**声明：验证是否计划向应用提供 ID 令牌
* **生效时间**和**过期时间**声明：验证 ID 令牌是否未过期
* **颁发者**声明：验证令牌是否由 v2.0 终结点颁发给应用
* **nonce**：缓和令牌重播攻击

有关应用需执行的声明验证完整列表，请参阅 [OpenID Connect 规范](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)。

[ID 令牌](# ID tokens)部分介绍了有关这些声明预期值的详细信息。

## <a name="token-lifetimes"></a>令牌生存期
下方提供的令牌生存期仅供参考。 此信息可能在开发和调试应用时起到帮助。 不应将应用编写成预期任何一个生存期维持不变。 令牌生存期可能并且会随时改变。

| 令牌 | 生存期 | Description |
| --- | --- | --- |
| ID 令牌（工作或学校帐户） |1 小时	 |ID 令牌有效期通常为 1 小时。 Web 应用可使用此生存期来维护自身与用户的会话（建议），或选择完全不同的会话生存期。 如果应用需要获取新的 ID 令牌，对 v2.0 授权终结点发出新的登录请求即可。 如果用户与 v2.0 终结点存在有效的浏览器会话，可能无需用户再次输入凭据。 |
| ID 令牌（个人帐户） |24 小时 |个人帐户的 ID 令牌有效期通常为 24 小时。 Web 应用可使用此生存期来维护自身与用户的会话（建议），或选择完全不同的会话生存期。 如果应用需要获取新的 ID 令牌，对 v2.0 授权终结点发出新的登录请求即可。 如果用户与 v2.0 终结点存在有效的浏览器会话，可能无需用户再次输入凭据。 |
| 访问令牌（工作或学校帐户） |1 小时	 |在令牌响应中指定为令牌元数据的一部分。 |
| 访问令牌（个人帐户） |1 小时	 |在令牌响应中指定为令牌元数据的一部分。 可对代表个人账户颁发的访问令牌配置其他生存期，但通常为 1 小时。 |
| 刷新令牌（工作或学校帐户） |最长 14 天 |单个刷新令牌的有效期最长为 14 天。 但是，刷新令牌可能由于任何原因而随时失效，因此应用需继续尝试使用刷新令牌，直至失败，或直到应用将其替换为新的刷新令牌。 如果用户输入凭据已达 90 天，则刷新令牌也会失效。 |
| 刷新令牌（个人帐户） |最长 1 年 |单个刷新令牌的有效期最长为 1 年。 但是，刷新令牌可能由于任何原因而随时失效，因此应用需继续尝试使用刷新令牌，直至失败。 |
| 授权代码（工作或学校帐户） |10 分钟 |授权代码的生存期有意缩短，应在收到此令牌时立即将其兑换为访问令牌和刷新令牌。 |
| 授权代码（个人帐户） |5 分钟 |授权代码的生存期有意缩短，应在收到此令牌时立即将其兑换为访问令牌和刷新令牌。 代表个人帐户颁发的授权代码只能使用一次。 |
