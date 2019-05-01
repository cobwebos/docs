---
title: 令牌-Azure Active Directory B2C 概述 |Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中使用的令牌。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ac3c2132fc28d9813a9322898f79c7cdfffa12d7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64681895"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的令牌的概述

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD) B2C 发出多种安全令牌，在处理每个[身份验证流](active-directory-b2c-apps.md)。 本文档说明每种令牌的格式、安全特征和内容。

## <a name="token-types"></a>令牌类型

Azure AD B2C 支持[OAuth 2.0 和 OpenID Connect 协议](active-directory-b2c-reference-protocols.md)，该协议使用的令牌进行身份验证和安全地访问资源。 使用 Azure AD B2C 中的所有标记都都[JSON web 令牌 (Jwt)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)包含断言有关持有者的信息和对令牌使用者。

与 Azure AD B2C 的通信中使用了以下标记：

- *ID 令牌*-JWT，其中包含的声明可用于标识你的应用程序中的用户。 此令牌安全地发送相同的应用程序或服务的两个组件之间进行通信的 HTTP 请求中。 可以根据需要使用 ID 令牌中的声明。 它们常用于显示帐户信息或者要在应用程序进行访问控制决策。 ID 令牌已签名，但未加密。 当你的应用程序或 API 收到 ID 令牌时，它必须验证签名，证明令牌可信。 你的应用程序或 API 还必须验证令牌中的几个声明来证明其有效。 根据方案要求，验证应用程序的声明可能会有所不同，但是您的应用程序必须在每个方案中执行一些常见声明验证。
- *访问令牌*-JWT，其中包含的声明可用于识别你的 api 授予的权限。 访问令牌已签名，但它们不会加密。 访问令牌用于提供对 Api 和资源的服务器的访问。  当 API 收到访问令牌时，它必须验证签名，证明令牌可信。 API 还必须验证令牌中的一些声明，证明令牌有效。 根据方案要求，验证应用程序的声明可能会有所不同，但是您的应用程序必须在每个方案中执行一些常见声明验证。
- *刷新令牌*-刷新令牌用于获取新 ID 令牌和访问令牌在 OAuth 2.0 流中的。 它们而无需交互的那些用户提供长期访问资源代表的用户应用程序。 刷新令牌是不透明的应用程序。 它们由 Azure AD B2C 颁发并可检查和解释只能由 Azure AD B2C。 它们属于长效令牌，但不应使用刷新令牌将持续一段特定时间的假定条件下编写你的应用程序。 出于各种原因，可随时验证刷新令牌。 您知道刷新令牌是否有效的应用程序的唯一方法是以尝试兑换通过向 Azure AD B2C 发出的令牌请求。 在兑换刷新令牌获取新的令牌，令牌响应中收到新的刷新令牌。 保存新的刷新令牌。 它取代了以前在请求中使用的刷新令牌。 此操作有助于保证刷新令牌保持有效的尽可能长时间。 

## <a name="endpoints"></a>终结点

一个[注册应用程序](tutorial-register-applications.md)接收令牌，并将请求发送到这些终结点与 Azure AD B2C 进行通信：

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

从 Azure AD B2C 接收你的应用程序的安全令牌可以来自`/authorize`或`/token`终结点。 从获取 ID 令牌时`/authorize`终结点，它们会完成使用[隐式流](active-directory-b2c-reference-spa.md)，这通常用于登录到基于 javascript 的 web 应用程序的用户。 从 `/token` 终结点获取 ID 令牌时，将通过使用[机密代码流](active-directory-b2c-reference-oidc.md)完成此操作，从而使令牌隐藏在浏览器中。

## <a name="claims"></a>声明

使用 Azure AD B2C 时，必须对令牌内容有精细的控制。 你可以配置[用户流](active-directory-b2c-reference-policies.md)并[自定义策略](active-directory-b2c-overview-custom.md)所需的应用程序的声明中发送的某些用户数据集。 这些声明可以包括标准属性如下所述**displayName**并**emailAddress**。 应用程序可以使用这些声明来安全地对用户和请求进行身份验证。 

ID 令牌中的声明不按任何特定顺序返回。 新的声明可以在任何时候引入 ID 令牌中。 你的应用程序不应中断，因为引入新声明。 此外可以包括[自定义用户属性](active-directory-b2c-reference-custom-attr.md)在声明中。

下表列出了可期望在 ID 令牌并访问由 Azure AD B2C 颁发的令牌的声明。

| 名称 | 声明 | 示例值 | 描述 |
| ---- | ----- | ------------- | ----------- |
| 目标受众 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | 标识令牌的目标接收方。 对于 Azure AD B2C，受众是应用程序 id。 你的应用程序应验证此值，并拒绝该令牌，如果不匹配。 受众是资源的同义词。 |
| 颁发者 | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | 标识构造并返回令牌的安全令牌服务 (STS)。 它还标识用户进行身份验证的目录。 你的应用程序应该验证颁发者声明，以确保令牌来自相应的终结点。 |
| 颁发时间 | `iat` | `1438535543` | 颁发令牌的时间，以纪元时间表示。 |
| 过期时间 | `exp` | `1438539443` | 令牌失效的时间，以纪元时间表示。 应用程序应使用此声明来验证令牌的生存期的有效性。 |
| 生效时间 | `nbf` | `1438535543` | 令牌生效的时间，以纪元时间表示。 此时间通常是颁发令牌的时间相同。 应用程序应使用此声明来验证令牌的生存期的有效性。 |
| Version | `ver` | `1.0` | ID 令牌，由 Azure AD B2C 定义的版本。 |
| 代码哈希 | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 仅当令牌随 OAuth 2.0 授权代码一起颁发时包含在 ID 令牌代码哈希。 代码哈希可用于验证授权代码的真实性。 有关如何执行此验证的详细信息，请参阅[OpenID Connect 规范](https://openid.net/specs/openid-connect-core-1_0.html)。  |
| 访问令牌哈希 | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | 访问令牌哈希仅当令牌随 OAuth 2.0 访问令牌一起颁发时包含在 ID 令牌。 访问令牌哈希可用于验证访问令牌的真实性。 有关如何执行此验证的详细信息，请参阅[OpenID Connect 规范](https://openid.net/specs/openid-connect-core-1_0.html)  |
| Nonce | `nonce` | `12345` | Nonce 是缓和令牌重放攻击的策略。 你的应用程序可以通过使用授权请求中指定 nonce`nonce`查询参数。 在请求中提供的值，将发出未修改的形式在`nonce`仅在 ID 令牌的声明。 此声明允许应用程序以根据在请求上指定的值验证此值。 你的应用程序应执行此验证 ID 令牌验证过程。 |
| Subject | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | 有关哪些令牌断言信息，例如应用程序的用户的主体。 此值是固定不变的，无法重新分配或重复使用。 可使用它安全地执行授权检查，例如，使用令牌访问资源时。 默认情况下，将使用目录中用户的对象 ID 填充使用者声明。 |
| 身份验证上下文类引用 | `acr` | 不适用 | 仅与较旧的策略一起使用。 |
| 信任框架策略 | `tfp` | `b2c_1_signupsignin1` | 用于获取 ID 令牌策略的名称。 |
| 身份验证时间 | `auth_time` | `1438535543` | 用户上次输入凭据，以纪元时间表示。 |
| 范围 | `scp` | `Read`| 访问令牌的资源授予的权限。 由空格分隔多个授予的权限。 |
| 授权方 | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | 发起请求的客户端应用程序的**应用程序 ID**。 |

## <a name="configuration"></a>配置

以下属性可用于[管理的安全令牌的生存期](configure-tokens.md)Azure AD B2C 发出的：

- **访问令牌和 ID 令牌生存期（分钟）**- 用于获取受保护资源的访问权限的 OAuth 2.0 持有者令牌的生存期。 默认值为 60 分钟。 最小值 （含） 为 5 分钟。 （含） 的最大值为 1440 分钟。

- **刷新令牌生存期 （天）** -在其之前可以使用刷新令牌获取新的访问或 ID 令牌的最大时间段。 时间段内还介绍了获取新的刷新令牌，如果你的应用程序已被授予`offline_access`作用域。 默认值为 14 天。 最小值 （含） 为一天。 （含） 的最大值为 90 天。

- **刷新令牌滑动窗口生存期 （天）** -后强制用户重新进行身份验证，不考虑的最新刷新有效期由应用程序获取令牌此时间段结束。 仅在此开关设为“有限”时该属性才可用。 它的值必须大于或等于**刷新令牌生存期（天）** 的值。 如果此开关设置为“无限”，则无法提供特定值。 默认值为 90 天。 最小值 （含） 为一天。 （含） 的最大值为 365 天。

以下用例是使用这些属性实现的：

- 只要用户在移动应用程序上持续保持活动状态，允许该用户无限期地保持登录此应用程序。 可将登录用户流中的“刷新令牌的滑动窗口生存期(天)”开关设为“无限”。
- 通过设置合适的访问令牌生存期来满足行业的安全性和合规性要求。

这些设置不适用于密码重置用户流。 

## <a name="compatibility"></a>兼容性

以下属性可用于[管理令牌兼容性](configure-tokens.md):

- **颁发者 (iss) 声明** - 此属性标识颁发令牌的 Azure AD B2C 租户。 默认值为 `https://<domain>/{B2C tenant GUID}/v2.0/`。 值`https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`包括适用于 Azure AD B2C 租户和令牌请求中使用的用户流的 Id。 如果你的应用程序或库需要 Azure AD B2C 才能符合[OpenID Connect Discovery 1.0 规范](https://openid.net/specs/openid-connect-discovery-1_0.html)，使用此值。

- **使用者 (sub) 声明** - 此属性标识令牌断言其信息的实体。 默认值是**ObjectID**，该组件填充`sub`声明中使用的用户的对象 ID 令牌。 值**不支持**仅用于向后兼容性。 建议改用**ObjectID**就立即可以。

- **声明表示策略 ID** -此属性标识在其中填充令牌请求中使用的策略名称的声明类型。 默认值为 `tfp`。 值`acr`仅用于向后兼容性。

## <a name="pass-through"></a>直通

用户旅程在启动时，Azure AD B2C 从标识提供程序接收访问令牌。 Azure AD B2C 使用该令牌来检索有关用户的信息。 您[启用用户流中的声明](idp-pass-through-user-flow.md)或[自定义策略中定义声明](idp-pass-through-custom.md)以通过令牌传递给在 Azure AD B2C 中注册的应用程序。 必须使用你的应用程序[v2 用户流](user-flow-versions.md)才能利用以声明方式传递令牌。

Azure AD B2C 目前仅支持传递的 OAuth 2.0 标识提供者，其中包括 Facebook 和 Google 访问令牌。 对于所有其他标识提供者，声明将返回空白。 

## <a name="validation"></a>验证

若要验证令牌，应用程序应检查的签名和令牌的声明。 许多开放源代码库都可用于验证 Jwt，具体取决于您的首选语言。 建议您浏览这些选项而不是实现您自己的验证逻辑。

### <a name="validate-signature"></a>验证签名

JWT 包含三个段*标头*即*正文*，和一个*签名*。 签名段可用于验证令牌的真实性，使它可以信任的应用程序。 Azure AD B2C 令牌使用行业标准非对称式加密算法（例如 RSA 256）进行签名。 

令牌标头包含用于签名令牌的密钥和加密方法的相关信息：

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

值**alg**声明是用于对令牌进行签名的算法。 值**kid**声明是用于对令牌进行签名的公钥。 在任何给定时间，Azure AD B2C 可以使用任意一种一组公共 / 专用密钥对令牌签名。 Azure AD B2C 将定期旋转一组可能的密钥。 你的应用程序应编写成自动处理这些密钥更改。 若要检查的 Azure AD B2C 使用的公钥的更新的合理频率为每 24 小时。

Azure AD B2C 具有 OpenID Connect 元数据终结点。 使用此终结点，应用程序可以请求在运行时 Azure AD B2C 的相关信息。 此信息包括终结点、令牌内容和令牌签名密钥。 在 Azure AD B2C 租户包含每个策略的 JSON 元数据文档。 元数据文档是包含多条有用信息的 JSON 对象。 元数据包含**jwks_uri**，其中提供的公钥集位置的用来签名令牌。 但它在这里，提供位置的最好通过使用元数据文档并分析动态提取位置**jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
位于此 URL 的 JSON 文档包含将在特定时间点使用的所有公钥信息。 应用可以使用 JWT 标头中的 `kid` 声明，选择用于签名特定令牌的 JSON 文档中的公钥。 然后，可以使用正确的公钥和指定的算法来执行签名验证。

元数据文档`B2C_1_signupsignin1`中的策略`contoso.onmicrosoft.com`租户位于：

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

若要确定哪个策略用于对令牌进行签名 （以及到何处去请求元数据），有两个选项。 首先，策略名称包含在令牌的 `acr` 声明中。 可以通过对主体进行 Base-64 解码，并反序列化生成的 JSON 字符串，从而分析 JWT 主体中的声明。 `acr`声明是用来颁发令牌的策略的名称。 另一个选项是进行编码的值中的策略`state`参数时发出请求，然后将其解码以确定使用哪条策略了。 任意一种方法均有效。

关于如何执行签名验证的说明已超出了本文档的讨论范围。 许多开放源代码库都可用于帮助你验证令牌。

### <a name="validate-claims"></a>验证声明

当你的应用程序或 API 收到 ID 令牌时，还应对 ID 令牌中执行的声明的多个检查。 应检查以下声明：

- **受众**-验证 ID 令牌旨在提供给应用程序。
- **不早**并**过期时间**-验证 ID 令牌未过期。
- **颁发者**-验证由 Azure AD B2C 令牌已颁发给你的应用程序。
- **nonce** -令牌重放攻击缓解的策略。

有关你的应用程序应该执行的验证的完整列表，请参阅[OpenID Connect 规范](https://openid.net)。  

## <a name="next-steps"></a>后续步骤

详细了解如何[使用访问令牌](active-directory-b2c-access-tokens.md)。

