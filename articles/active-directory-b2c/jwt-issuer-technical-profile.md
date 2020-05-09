---
title: 在自定义策略中定义 JWT 颁发者的技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义 JSON Web 令牌 (JWT) 颁发者的技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fbadfb63b9f575053feca87bda2c3ad2e64e91eb
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926028"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 JWT 令牌颁发者的技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 在处理每个身份验证流时颁发多种安全令牌。 JWT 令牌颁发者的技术配置文件发出返回给信赖方应用程序的 JWT 令牌。 通常，此技术配置文件是用户旅程中的最后一个业务流程步骤。

## <a name="protocol"></a>协议

“Protocol”  元素的“Name”  属性必须设置为 `None`。 将 **OutputTokenFormat** 元素设置为 `JWT`。

以下示例演示了 `JwtIssuer` 的技术配置文件：

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  <Metadata>
    <Item Key="client_id">{service:te}</Item>
    <Item Key="issuer_refresh_token_user_identity_claim_type">objectId</Item>
    <Item Key="SendTokenResponseBodyWithJsonNumbers">true</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
    <Key Id="issuer_refresh_token_key" StorageReferenceId="B2C_1A_TokenEncryptionKeyContainer" />
  </CryptographicKeys>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-jwt-issuer" />
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>输入、输出和保存声明

**InputClaims**、**OutputClaims** 和 **PersistClaims** 元素为空或不存在。 **InutputClaimsTransformations** 和 **OutputClaimsTransformations** 元素也不存在。

## <a name="metadata"></a>Metadata

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | 是 | 应在 OAuth2 授权代码和刷新令牌中用作用户标识声明的声明。 默认情况下，除非指定了不同的 SubjectNamingInfo 声明类型，否则应将其设置为 `objectId`。 |
| SendTokenResponseBodyWithJsonNumbers | 否 | 始终设置为 `true`。 对于以字符串而不是 JSON 数字形式指定数字值的旧格式，请将此属性设置为 `false`。 依赖于以字符串形式返回此类属性的早期实现的客户端需要此属性。 |
| token_lifetime_secs | 否 | 访问令牌生存期。 用于获取受保护资源的访问权限的 OAuth 2.0 持有者令牌的生存期。 默认值为 3,600 秒（1 小时）。 最小值为 300 秒（5 分钟）（含）。 最大值为 86,400 秒（24 小时）（含）。 |
| id_token_lifetime_secs | 否 | ID 令牌生存期。 默认值为 3,600 秒（1 小时）。 最小值为 300 秒（5 分钟）（含）。 最大值为 86,400 秒（24 小时）（含）。 |
| refresh_token_lifetime_secs | 否 | 刷新令牌生存期。 在应用程序已获取 offline_access 范围的情况下，可以使用某个刷新令牌获取新访问令牌之前所要经过的最长时间段。 默认值为 120,9600 秒（14 天）。 最小值为 86,400 秒（24 小时）（含）。 最大值为 7,776,000 秒（90 天）（含）。 |
| rolling_refresh_token_lifetime_secs | 否 | 刷新令牌滑动窗口生存期。 此时间段过后，会强制用户重新进行身份验证，不考虑该应用程序获取的最近刷新令牌的有效期。 如果不想要实施滑动窗口生存期，请将 allow_infinite_rolling_refresh_token 的值设置为 `true`。 默认值为 7,776,000 秒（90 天）。 最小值为 86,400 秒（24 小时）（含）。 最大值为 31,536,000 秒（365 天）（含）。 |
| allow_infinite_rolling_refresh_token | 否 | 如果设置为 `true`，则刷新令牌滑动窗口生存期永不过期。 |
| IssuanceClaimPattern | 否 | 控制颁发者 (iss) 声明。 值为下列其中一项：<ul><li>AuthorityAndTenantGuid-iss 声明包括域名（如`login.microsoftonline`或`tenant-name.b2clogin.com`）和租户标识符 https：\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - iss 声明包含域名（例如 `login.microsoftonline` 或 `tenant-name.b2clogin.com`）、租户标识符和信赖方策略名称。 https：\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> 默认值：AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | 否 | 控制 `acr` 声明值。<ul><li>None - Azure AD B2C 不发出 acr 声明</li><li>PolicyId - `acr` 声明包含策略名称</li></ul>用于设置此值的选项为 TFP（信任框架策略）和 ACR（身份验证上下文引用）。 建议将此值设置为 TFP，若要设置值，请确保存在包含 `Key="AuthenticationContextReferenceClaimPattern"` 的 `<Item>`，且值为 `None`。 在信赖方策略中，添加 `<OutputClaims>` 项和此元素 `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`。 另请确保策略包含声明类型 `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` |
|RefreshTokenUserJourneyId| 否 | 向 `/token` 终结点发送[刷新访问令牌](authorization-code-flow.md#4-refresh-the-token) POST 请求期间应执行的用户旅程的标识符。 |

## <a name="cryptographic-keys"></a>加密密钥

CryptographicKeys 元素包含以下属性：

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| issuer_secret | 是 | 用于对 JWT 令牌进行签名的 X509 证书（RSA 密钥集）。 这是你`B2C_1A_TokenSigningKeyContainer`在[自定义策略入门](custom-policy-get-started.md)中配置的密钥。 |
| issuer_refresh_token_key | 是 | 用于加密刷新令牌的 X509 证书（RSA 密钥集）。 在[自定义策略入门](custom-policy-get-started.md)中已配置 `B2C_1A_TokenEncryptionKeyContainer` 密钥 |

## <a name="session-management"></a>会话管理

若要配置 Azure AD B2C 与信赖方应用程序之间的 Azure AD B2C 会话，请在`UseTechnicalProfileForSessionManagement`元素的属性中添加对[OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider) SSO 会话的引用。














