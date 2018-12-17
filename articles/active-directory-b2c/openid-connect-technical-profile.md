---
title: 在 Azure Active Directory B2C 的自定义策略中定义 OpenId Connect 技术配置文件 | Microsoft Docs
description: 在 Azure Active Directory B2C 的自定义策略中定义 OpenId Connect 技术配置文件。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a20b8df1f0d9cddbde4c4886e11967bc8c04160
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842401"
---
# <a name="define-a-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 OpenId Connect 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C 为 [OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) 协议标识提供者提供支持。 OpenID Connect 1.0 在 OAuth 2.0 基础上提供一个标识层，代表现代身份验证协议的最新技术。  使用 OpenId Connect 技术配置文件，可以与基于 OpenId Connect 的标识提供者（例如 Azure AD）联合，方便用户使用其现有的社交或企业标识登录。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `OpenIdConnect`。 例如，**MSA-OIDC** 技术配置文件的协议为 `OpenIdConnect`：

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...    
```

## <a name="input-claims"></a>输入声明

**InputClaims** 和 **InputClaimsTransformations** 元素不是必需的。 但是，你可能需要向标识提供者发送更多参数。 以下示例将值为 `contoso.com` 的 **domain_hint** 查询字符串参数添加到授权请求。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>输出声明

**OutputClaims** 元素包含 OpenId Connect 标识提供者返回的声明列表。 可能需要将策略中定义的声明名称映射到标识提供者中定义的名称。 如果设置了 `DefaultValue` 属性，则还可以包含标识提供者不会返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

以下示例演示 Microsoft Account 标识提供者返回的声明：

- 映射到 **socialIdpUserId** 声明的 **sub** 声明。
- 映射到 **displayName** 声明的 **name** 声明。
- 没有名称映射的 **email**。

技术配置文件还会返回标识提供者不返回的声明：

- **identityProvider** 声明，其中包含标识提供者的名称。
- **authenticationSource** 声明，其默认值为 **socialIdpAuthentication**。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>元数据

| 属性 | 必选 | Description |
| --------- | -------- | ----------- |
| client_id | 是 | 标识提供者的应用程序标识符。 |
| IdTokenAudience | 否 | id_token 的受众。 在指定此项的情况下，Azure AD B2C 会检查令牌是否位于标识提供者返回的声明中，以及是否与指定的令牌相同。 |
| METADATA | 是 | 一个 URL，其指向的 JSON 配置文档的格式符合 OpenID Connect Discovery 规范，该规范也称已知的 openid 配置终结点。 |
| ProviderName | 否 | 标识提供者的名称。 |
| response_types | 否 | 符合 OpenID Connect Core 1.0 规范的响应类型。 可能的值：`id_token`、`code` 或 `token`。 |
| response_mode | 否 | 标识提供者在将结果发送回 Azure AD B2C 时使用的方法。 可能的值：`query`、`form_post`（默认值）或 `fragment`。 |
| 作用域 | 否 | 根据 OpenID Connect Core 1.0 规范定义的访问请求的范围。 例如 `openid`、`profile` 和 `email`。 |
| HttpBinding | 否 | 预期的 HTTP 绑定，绑定到访问令牌和声明令牌终结点。 可能的值：`GET` 或 `POST`。  |
| ValidTokenIssuerPrefixes | 否 | 一个密钥，可以在使用多租户标识提供者（例如 Azure Active Directory）时用于登录到每个租户。 |
| UsePolicyInRedirectUri | 否 | 指示在构造重定向 URI 时是否要使用策略。 在标识提供者中配置应用程序时，需指定重定向 URI。 重定向 URI 指向 Azure AD B2C `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`（login.microsoftonline.com 可能会变为 your-tenant-name.b2clogin.com）。  如果指定 `false`，需为每个使用的策略添加重定向 URI。 例如：`https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`。 |
| MarkAsFailureOnStatusCode5xx | 否 | 指示在 Http 状态代码处于 5xx 范围内的情况下，是否应将对外部服务的请求标记为失败。 默认为 `false`。 |
| DiscoverMetadataByTokenIssuer | 否 | 指示是否应使用 JWT 令牌中的颁发者来发现 OIDC 元数据。 |

## <a name="cryptographic-keys"></a>加密密钥

**CryptographicKeys** 元素包含以下属性：

| 属性 | 必选 | Description |
| --------- | -------- | ----------- |
| client_secret | 是 | 标识提供者应用程序的客户端机密。 只有在将 **response_types** 元数据设置为 `code` 的情况下，才需要加密密钥。 在这种情况下，Azure AD B2C 会再次进行调用，以便用授权代码来交换访问令牌。 如果元数据已设置为 `id_token`，则可省略加密密钥。  |  

## <a name="redirect-uri"></a>重定向 URI
 
配置标识提供者的重定向 URI 时，请输入 `https://login.microsoftonline.com/te/tenant/oauth2/authresp`。 确保将 **tenant** 替换为租户名称（例如 contosob2c.onmicrosoft.com）或租户 ID。重定向 URI 需要采用全小写形式。

如果使用 **b2clogin.com** 域而不是 **login.microsoftonline.com**，请确保使用 b2clogin.com 而不是 login.microsoftonline.com。

示例：

- [使用自定义策略添加 Microsoft Account (MSA) 作为标识提供者](active-directory-b2c-custom-setup-msa-idp.md)
- [使用 Azure AD 帐户登录](active-directory-b2c-setup-aad-custom.md)
- [让用户使用自定义策略登录到多租户 Azure AD 标识提供者](active-directory-b2c-setup-commonaad-custom.md)

 














