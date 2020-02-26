---
title: 在自定义策略中定义 OAuth2 技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中的自定义策略中定义 OAuth2 技术配置文件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f9db8fd0865b7dba869795526cf6b68f4ad3ffb9
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585775"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 OAuth2 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）提供对 OAuth2 协议标识提供者的支持。 OAuth2 是授权和委托身份验证的主要协议。 有关详细信息，请参阅 [RFC 6749 - OAuth 2.0 授权框架](https://tools.ietf.org/html/rfc6749)。 使用 OAuth2 技术配置文件，可以与基于 OAuth2 的标识提供者（如 Facebook）联合。 使用标识提供者进行联合允许用户使用现有的社交或企业标识登录。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `OAuth2`。 例如，**Facebook-OAUTH** 技术配置文件的协议为 `OAuth2`：

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...
```

## <a name="input-claims"></a>输入声明

**InputClaims** 和 **InputClaimsTransformations** 元素不是必需的。 但是，你可能需要向标识提供者发送更多参数。 以下示例将值为 **的**domain_hint`contoso.com` 查询字符串参数添加到授权请求。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>输出声明

**OutputClaims** 元素包含 OAuth2 标识提供者返回的声明列表。 可能需要将策略中定义的声明名称映射到标识提供者中定义的名称。 只要设置了 `DefaultValue` 属性，就还可以包含标识提供者不会返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

以下示例演示 Facebook 标识提供者返回的声明：

- **first_name** 声明已映射到 **givenName** 声明。
- **last_name** 声明已映射到 **surname** 声明。
- 不带名称映射的**displayName**声明。
- 没有名称映射的 **email** 声明。

技术配置文件还会返回标识提供者不返回的声明：

- **identityProvider** 声明，其中包含标识提供者的名称。
- **authenticationSource** 声明，其默认值为 **socialIdpAuthentication**。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>元数据

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| client_id | 是 | 标识提供者的应用程序标识符。 |
| IdTokenAudience | 否 | id_token 的受众。 在指定此项的情况下，Azure AD B2C 会检查令牌是否位于标识提供者返回的声明中，以及是否与指定的令牌相同。 |
| authorization_endpoint | 是 | 符合 RFC 6749 规范的授权终结点的 URL。 |
| AccessTokenEndpoint | 是 | 符合 RFC 6749 规范的令牌终结点的 URL。 |
| ClaimsEndpoint | 是 | 符合 RFC 6749 规范的用户信息终结点的 URL。 |
| AccessTokenResponseFormat | 否 | 访问令牌终结点调用的格式。 例如，Facebook 需要 HTTP GET 方法，但访问令牌响应采用 JSON 格式。 |
| AdditionalRequestQueryParameters | 否 | 附加的请求查询参数。 例如，你可能需要向标识提供者发送更多参数。 可以使用逗号分隔符包含多个参数。 |
| ClaimsEndpointAccessTokenName | 否 | 访问令牌查询字符串参数的名称。 某些标识提供者的声明终结点支持 GET HTTP 请求。 在这种情况下，将使用查询字符串参数而不是授权标头发送持有者令牌。 |
| ClaimsEndpointFormatName | 否 | 格式查询字符串参数的名称。 例如，可在此 LinkedIn 声明终结点 `format` 中将名称设置为 `https://api.linkedin.com/v1/people/~?format=json`。 |
| ClaimsEndpointFormat | 否 | 格式查询字符串参数的值。 例如，可在此 LinkedIn 声明终结点 `json` 中将值设置为 `https://api.linkedin.com/v1/people/~?format=json`。 |
| ProviderName | 否 | 标识提供者的名称。 |
| response_mode | 否 | 标识提供者在将结果发送回 Azure AD B2C 时使用的方法。 可能的值：`query`、`form_post`（默认值）或 `fragment`。 |
| scope | 否 | 根据 OAuth2 标识提供程序规范定义的请求的范围。 例如 `openid`、`profile` 和 `email`。 |
| HttpBinding | 否 | 预期的 HTTP 绑定，绑定到访问令牌和声明令牌终结点。 可能的值：`GET` 或 `POST`。  |
| ResponseErrorCodeParamName | 否 | 包含连同 HTTP 200 (Ok) 一起返回的错误消息的参数的名称。 |
| ExtraParamsInAccessTokenEndpointResponse | 否 | 包含可在某些标识提供者的 **AccessTokenEndpoint** 响应中返回的附加参数。 例如，**AccessTokenEndpoint** 的响应包含 `openid` 等附加参数，在 **ClaimsEndpoint** 请求查询字符串中，除 access_token 以外，此参数也是必需的参数。 多个参数名称应该转义，并以逗号“,”分隔符分隔。 |
| ExtraParamsInClaimsEndpointRequest | 否 | 包含可在某些标识提供者的 **ClaimsEndpoint** 请求中返回的附加参数。 多个参数名称应该转义，并以逗号“,”分隔符分隔。 |
| IncludeClaimResolvingInClaimsHandling  | 否 | 对于输入和输出声明，指定技术配置文件中是否包含[声明解析](claim-resolver-overview.md)。 可能的值： `true`或 `false` （默认值）。 如果要使用技术配置文件中的声明解析程序，请将此项设置为 `true`。 |
| ResolveJsonPathsInJsonTokens  | 否 | 指示技术配置文件是否解析 JSON 路径。 可能的值： `true`或 `false` （默认值）。 使用此元数据从嵌套的 JSON 元素中读取数据。 在[OutputClaim](technicalprofiles.md#outputclaims)中，将 `PartnerClaimType` 设置为要输出的 JSON 路径元素。 例如： `firstName.localized`或 `data.0.to.0.email`。|

## <a name="cryptographic-keys"></a>加密密钥

**CryptographicKeys** 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| client_secret | 是 | 标识提供者应用程序的客户端机密。 只有在将 **response_types** 元数据设置为 `code` 的情况下，才需要加密密钥。 在这种情况下，Azure AD B2C 会再次进行调用，以便用授权代码来交换访问令牌。 如果将元数据设置为 `id_token`，则可以省略加密密钥。 |

## <a name="redirect-uri"></a>重定向 URI

配置标识提供者的重定向 URL 时，请输入 `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`。 确保将 **tenant** 替换为租户名称（例如 contosob2c.onmicrosoft.com），将 **policyId** 替换为策略的标识符（例如 b2c_1a_policy）。 重定向 URI 需要采用全小写形式。

如果使用 **b2clogin.com** 域而不是 **login.microsoftonline.com**，请确保使用 b2clogin.com 而不是 login.microsoftonline.com。

示例：

- [使用自定义策略添加 Google+ 作为 OAuth2 标识提供者](identity-provider-google-custom.md)













