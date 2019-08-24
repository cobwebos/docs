---
title: 令牌概述-Azure Active Directory B2C
description: 了解 Azure Active Directory B2C 中使用的令牌。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c347a5740a13d071d4bb06daf43463f974198e5d
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980805"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的令牌概述

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 在处理每个[身份验证流](active-directory-b2c-apps.md)时颁发多种安全令牌。 本文档说明每种令牌的格式、安全特征和内容。

## <a name="token-types"></a>令牌类型

Azure AD B2C 支持 [OAuth 2.0 和 OpenID Connect 协议](active-directory-b2c-reference-protocols.md)，使用令牌进行身份验证和保护对资源的访问。 Azure AD B2C 中使用的所有令牌都是 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)，其中包含有关令牌持有者和使用者的信息断言。

与 Azure AD B2C 通信时使用以下令牌：

- ID 令牌 - 一个 JWT，其中包含用于验证应用程序中用户的身份的声明。 在相同应用程序或服务的两个组件之间通信时，会在 HTTP 请求中安全发送此令牌。 可以根据需要使用 ID 令牌中的声明。 它们常用于显示帐户信息或者在应用程序中进行访问控制决策。 ID 令牌已签名，但未加密。 当应用程序或 API 收到 ID 令牌时，必须验证签名，证明令牌可信。 应用程序或 API 还必须验证令牌中的一些声明，证明令牌有效。 根据具体的方案要求，应用程序验证的声明有所不同，但应用程序在每个方案中均须执行某些常见声明验证。
- 访问令牌 - 一个 JWT，其中包含用于识别已授予的对 API 的权限的声明。 访问令牌已签名，但未加密。 访问令牌用于提供对 API 和资源服务器的访问权限。  当 API 收到访问令牌时，必须验证签名，证明令牌可信。 API 还必须验证令牌中的一些声明，证明令牌有效。 根据具体的方案要求，应用程序验证的声明有所不同，但应用程序在每个方案中均须执行某些常见声明验证。
- 刷新令牌 - 刷新令牌用于在 OAuth 2.0 流中获取新的 ID 令牌和访问令牌。 它们向应用程序提供代表用户长期访问资源的权限，而无需与这些用户进行交互。 刷新令牌对于应用程序而言是不透明的。 它们由 Azure AD B2C 颁发，且只能由 Azure AD B2C 检查和解释。 它们属于长效令牌，但编写应用程序时，不应期望刷新令牌将持续一段特定时间。 出于各种原因，可随时验证刷新令牌。 让应用程序知道刷新令牌是否有效的唯一方式就是对 Azure AD B2C 发出令牌请求以尝试兑换刷新令牌。 使用刷新令牌兑换新的令牌时，会在令牌响应中收到新的刷新令牌。 保存新的刷新令牌。 它会替代以前在请求中使用的刷新令牌。 此操作有助于保证刷新令牌尽可能长期保持有效。

## <a name="endpoints"></a>终结点

[已注册的应用程序](tutorial-register-applications.md)接收令牌，并通过向这些终结点发送请求来与 Azure AD B2C 通信：

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

应用程序从 Azure AD B2C 收到的安全令牌可能来自于 `/authorize` 或 `/token` 终结点。 从 `/authorize` 终结点获取 ID 令牌时，将通过使用[隐式流](active-directory-b2c-reference-spa.md)完成此操作，该令牌通常供用户用于登录到基于 Javascript 的 Web 应用程序。 从 `/token` 终结点获取 ID 令牌时，将通过使用[机密代码流](active-directory-b2c-reference-oidc.md)完成此操作，从而使令牌隐藏在浏览器中。

## <a name="claims"></a>声明

使用 Azure AD B2C 时，必须对令牌内容有精细的控制。 可以配置[用户流](active-directory-b2c-reference-policies.md)和[自定义策略](active-directory-b2c-overview-custom.md)，在声明中发送应用程序所需的特定用户数据集。 这些声明可以包含标准属性，例如 **displayName** 和 **emailAddress**。 应用程序可以使用这些声明来安全地对用户和请求进行身份验证。

ID 令牌中的声明不按任何特定顺序返回。 新的声明可以在任何时候引入 ID 令牌。 引入新的声明时，应用程序不应中断。 还可以在声明中包含[自定义用户属性](active-directory-b2c-reference-custom-attr.md)。

下表列出了 Azure AD B2C 颁发的 ID 令牌和访问令牌中预期包含的声明。

| 姓名 | 声明 | 示例值 | 描述 |
| ---- | ----- | ------------- | ----------- |
| 受众 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 标识令牌的目标接收方。 对于 Azure AD B2C，受众是在应用程序 ID。 应用程序应该验证此值并拒绝不匹配的令牌。 受众是资源的同义词。 |
| 颁发者 | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | 标识构造并返回令牌的安全令牌服务 (STS)。 它还标识在其中进行用户身份验证的目录。 应用程序应该验证颁发者声明，以确保令牌来自适当的终结点。 |
| 颁发时间 | `iat` | `1438535543` | 颁发令牌的时间，以纪元时间表示。 |
| 过期时间 | `exp` | `1438539443` | 令牌失效的时间，以纪元时间表示。 应用程序应该使用此声明来验证令牌生存期的有效性。 |
| 生效时间 | `nbf` | `1438535543` | 令牌生效的时间，以纪元时间表示。 此时间通常与颁发令牌的时间相同。 应用程序应该使用此声明来验证令牌生存期的有效性。 |
| Version | `ver` | `1.0` | Azure AD B2C 定义的 ID 令牌版本。 |
| 代码哈希 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 仅当令牌随 OAuth 2.0 授权代码一起颁发时，代码哈希才包含在 ID 令牌中。 代码哈希可用于验证授权代码的真实性。 有关如何执行此验证的详细信息，请参阅 [OpenID Connect 规范](https://openid.net/specs/openid-connect-core-1_0.html)。  |
| 访问令牌哈希 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 仅当令牌随 OAuth 2.0 访问令牌一起颁发时，访问令牌哈希才包含在 ID 令牌中。 访问令牌哈希可用于验证访问令牌的真实性。 有关如何执行此验证的详细信息，请参阅 [OpenID Connect 规范](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Nonce 是缓和令牌重放攻击的策略。 应用程序可通过使用 `nonce` 查询参数，在授权请求中指定 nonce。 在请求中提供的值将仅在 ID 令牌的 `nonce` 声明中发出（未经修改）。 此声明可让应用程序根据请求中指定的值验证该值。 应用程序应该在 ID 令牌验证过程中执行这项验证。 |
| Subject | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | 令牌针对其断言信息的主体，例如应用程序的用户。 此值是固定不变的，无法重新分配或重复使用。 可使用它安全地执行授权检查，例如，使用令牌访问资源时。 默认情况下，将使用目录中用户的对象 ID 填充使用者声明。 |
| 身份验证上下文类引用 | `acr` | 不适用 | 仅与旧策略配合使用。 |
| 信任框架策略 | `tfp` | `b2c_1_signupsignin1` | 用于获取 ID 令牌的策略名称。 |
| 身份验证时间 | `auth_time` | `1438535543` | 用户最后一次输入凭据的时间，以纪元时间表示。 该身份验证与全新登录、单一登录 (SSO) 会话或其他登录类型之间没有任何区别。 `auth_time`是应用程序 (或用户) 在 Azure AD B2C 上启动身份验证尝试的最后时间。 用于进行身份验证的方法没有区别。 |
| 范围 | `scp` | `Read`| 授予访问令牌对资源的权限。 多个授予的权限以空格分隔。 |
| 授权方 | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | 发起请求的客户端应用程序的**应用程序 ID**。 |

## <a name="configuration"></a>配置

以下是用于管理 Azure AD B2C 发出的[安全令牌的生存期](configure-tokens.md)的属性：

- **访问令牌和 ID 令牌生存期（分钟）** - 用于获取受保护资源的访问权限的 OAuth 2.0 持有者令牌的生存期。 默认值为 60 分钟。 最小值为 5 分钟（含）。 最大值为 1440 分钟（含）。

- **刷新令牌生存期（天）** - 可以使用刷新令牌获取新的访问令牌或 ID 令牌之前的最大时限。 该时限还包括为应用程序授予了 `offline_access` 范围时获取新刷新令牌的最大时限。 默认值为 14 天。 最小值为 1 天（含）。 最大值为 90 天（含）。

- **刷新令牌的滑动窗口生存期（天）** - 此时限过后，强制用户重新进行身份验证，不考虑应用程序获取的最新刷新令牌的有效期。 仅在此开关设为“有限”时该属性才可用。 它的值必须大于或等于**刷新令牌生存期（天）** 的值。 如果此开关设置为“无限”，则无法提供特定值。 默认值为 90 天。 最小值为 1 天（含）。 最大值为 365 天（含）。

以下用例是使用这些属性实现的：

- 只要用户在移动应用程序上持续保持活动状态，允许该用户无限期地保持登录此应用程序。 可将登录用户流中的“刷新令牌的滑动窗口生存期(天)”开关设为“无限”。
- 通过设置合适的访问令牌生存期来满足行业的安全性和合规性要求。

这些设置不适用于密码重置用户流。

## <a name="compatibility"></a>兼容性

以下属性用于[管理令牌兼容性](configure-tokens.md)：

- **颁发者 (iss) 声明** - 此属性标识颁发令牌的 Azure AD B2C 租户。 默认值为 `https://<domain>/{B2C tenant GUID}/v2.0/`。 `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` 值包含令牌请求中使用的 Azure AD B2C 租户和用户流的 ID。 如果应用程序或库需要 Azure AD B2C 才能符合 [OpenID Connect Discovery 1.0 规范](https://openid.net/specs/openid-connect-discovery-1_0.html)，请使用此值。

- **使用者 (sub) 声明** - 此属性标识令牌断言其信息的实体。 默认值为 **ObjectID**，即在令牌的 `sub` 声明中填充用户的对象 ID。 **Not supported** 值仅供用于实现后向兼容。 建议尽快改用 **ObjectID**。

- **表示策略 ID 的声明** - 此属性标识要在其中填充令牌请求中使用的策略名称的声明类型。 默认值为 `tfp`。 `acr` 值仅供用于实现后向兼容。

## <a name="pass-through"></a>直通

用户旅程开始时，Azure AD B2C 会从标识提供者处收到一个访问令牌。 Azure AD B2C 使用该令牌来检索有关用户的信息。 [在用户流中启用声明](idp-pass-through-user-flow.md)或[在自定义策略中定义声明](idp-pass-through-custom.md)即可将该令牌传递给你在 Azure AD B2C 中注册的应用程序。 应用程序必须使用 [v2 用户流](user-flow-versions.md)才能利用将令牌作为声明传递的优势。

Azure AD B2C 当前仅支持传递 OAuth 2.0 标识提供程序 (包括 Facebook 和 Google) 的访问令牌。 对于所有其他标识提供者，声明将返回空白。

## <a name="validation"></a>验证

若要验证令牌，应用程序应检查令牌的签名和声明。 许多开放源代码库都可用于验证 JWT，具体取决于首选语言。 建议浏览这些选项，而不是实施自己的验证逻辑。

### <a name="validate-signature"></a>验证签名

JWT 包含三段：标头、主体和签名。 签名段可用于验证令牌的真实性，使令牌可获得应用程序的信任。 Azure AD B2C 令牌使用行业标准非对称式加密算法（例如 RSA 256）进行签名。

令牌标头包含用于签名令牌的密钥和加密方法的相关信息：

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

**alg** 声明的值是用来为令牌签名的算法。 **kid** 声明的值是用来为令牌签名的公钥。 在任何给定时间，Azure AD B2C 都可使用一组公钥-私钥对中的任意一个对令牌签名。 Azure AD B2C 定期轮换可能一组的密钥。 应将应用程序编写成自动处理这些密钥更改。 对 Azure AD B2C 所用公钥的更新进行检查的合理频率为每 24 小时一次。

Azure AD B2C 具有 OpenID Connect 元数据终结点。 使用此终结点，应用程序可以在运行时请求有关 Azure AD B2C 的信息。 此信息包括终结点、令牌内容和令牌签名密钥。 Azure AD B2C 租户针对策略包含一个 JSON 元数据文档。 元数据文档是包含多条有用信息的 JSON 对象。 元数据包含 **jwks_uri**，它提供用于对令牌签名的公钥集位置。 在此处提供该位置，但最好使用元数据文档并分析 **jwks_uri** 来动态提取位置：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
位于此 URL 的 JSON 文档包含将在特定时间点使用的所有公钥信息。 应用可以使用 JWT 标头中的 `kid` 声明，选择用于签名特定令牌的 JSON 文档中的公钥。 然后，可以使用正确的公钥和指定的算法来执行签名验证。

`contoso.onmicrosoft.com` 租户中的 `B2C_1_signupsignin1` 策略的元数据文档位于：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

若要确定对令牌签名所用的策略（以及请求元数据的位置），有两个选择。 首先，策略名称包含在令牌的 `acr` 声明中。 可以通过对主体进行 Base-64 解码，并反序列化生成的 JSON 字符串，从而分析 JWT 主体中的声明。 `acr` 声明是用于颁发令牌的策略名称。 另一个方法是在发出请求时在 `state` 参数的值中对策略进行编码，并对其进行解码以确定使用的策略。 任意一种方法均有效。

关于如何执行签名验证的说明已超出了本文档的讨论范围。 有许多开源库可帮助验证令牌。

### <a name="validate-claims"></a>验证声明

应用程序或 API 收到 ID 令牌时，还应对 ID 令牌中的声明执行几项检查。 应检查以下声明：

- **audience** - 验证是否计划向应用程序提供 ID 令牌。
- **not before** 和 **expiration time** - 验证 ID 令牌是否未过期。
- **issuer** - 验证令牌是否确实由 Azure AD B2C 向应用程序颁发。
- **nonce** - 缓解令牌重放攻击的策略。

有关应用程序应该执行的验证的完整列表，请参阅 [OpenID Connect 规范](https://openid.net)。

## <a name="next-steps"></a>后续步骤

详细了解如何[使用访问令牌](active-directory-b2c-access-tokens.md)。

