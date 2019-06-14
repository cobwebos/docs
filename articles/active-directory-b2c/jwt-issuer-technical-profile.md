---
title: 在 Azure Active Directory B2C 的自定义策略中定义 JWT 令牌颁发者的技术配置文件 | Microsoft Docs
description: 在 Azure Active Directory B2C 的自定义策略中定义 JWT 令牌颁发者的技术配置文件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 573463d91fc7a4119bd1bc30182588ff9dfdecb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510702"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 JWT 令牌颁发者的技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C 在每个身份验证流的处理过程中发出多种安全令牌。 JWT 令牌颁发者的技术配置文件发出返回给信赖方应用程序的 JWT 令牌。 通常，此技术配置文件是用户旅程中的最后一个业务流程步骤。

## <a name="protocol"></a>Protocol

“Protocol”  元素的“Name”  属性必须设置为 `None`。 将 **OutputTokenFormat** 元素设置为 `JWT`。

以下示例演示了 `JwtIssuer` 的技术配置文件：

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>输入、输出和保存声明

**InputClaims**、**OutputClaims** 和 **PersistClaims** 元素为空或不存在。 **InutputClaimsTransformations** 和 **OutputClaimsTransformations** 元素也不存在。

## <a name="metadata"></a>元数据

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | 是 | 应在 OAuth2 授权代码和刷新令牌中用作用户标识声明的声明。 默认情况下，除非指定了不同的 SubjectNamingInfo 声明类型，否则应将其设置为 `objectId`。 | 
| SendTokenResponseBodyWithJsonNumbers | 否 | 始终设置为 `true`。 对于以字符串而不是 JSON 数字形式指定数字值的旧格式，请将此属性设置为 `false`。 依赖于以字符串形式返回此类属性的早期实现的客户端需要此属性。 | 
| token_lifetime_secs | 否 | 访问令牌生存期。 用于获取受保护资源的访问权限的 OAuth 2.0 持有者令牌的生存期。 默认值为 3,600 秒（1 小时）。 最小值为 300 秒（5 分钟）（含）。 最大值为 86,400 秒（24 小时）（含）。 | 
| id_token_lifetime_secs | 否 | ID 令牌生存期。 默认值为 3,600 秒（1 小时）。 最小值为 300 秒（5 分钟）（含）。 最大值为 86,400 秒（24 小时）（含）。 | 
| refresh_token_lifetime_secs | 否 | 刷新令牌生存期。 在应用程序已获取 offline_access 范围的情况下，可以使用某个刷新令牌获取新访问令牌之前所要经过的最长时间段。 默认值为 120,9600 秒（14 天）。 最小值为 86,400 秒（24 小时）（含）。 最大值为 7,776,000 秒（90 天）（含）。 | 
| rolling_refresh_token_lifetime_secs | 否 | 刷新令牌滑动窗口生存期。 此时间段过后，会强制用户重新进行身份验证，不考虑该应用程序获取的最近刷新令牌的有效期。 如果不想要实施滑动窗口生存期，请将 allow_infinite_rolling_refresh_token 的值设置为 `true`。 默认值为 7,776,000 秒（90 天）。 最小值为 86,400 秒（24 小时）（含）。 最大值为 31,536,000 秒（365 天）（含）。 | 
| allow_infinite_rolling_refresh_token | 否 | 如果设置为 `true`，则刷新令牌滑动窗口生存期永不过期。 |
| IssuanceClaimPattern | 是 | 控制颁发者 (iss) 声明。 值为下列其中一项：<ul><li>AuthorityAndTenantGuid - iss 声明包含域名（例如 `login.microsoftonline` 或 `tenant-name.b2clogin.com`）和租户标识符 https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/</li><li>AuthorityWithTfp - iss 声明包含域名（例如 `login.microsoftonline` 或 `tenant-name.b2clogin.com`）、租户标识符和信赖方策略名称。 https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | 否 | 控制 `acr` 声明值。<ul><li>None - Azure AD B2C 不发出 acr 声明</li><li>PolicyId - `acr` 声明包含策略名称</li></ul>用于设置此值的选项为 TFP（信任框架策略）和 ACR（身份验证上下文引用）。 建议将此值设置为 TFP，若要设置值，请确保存在包含 `Key="AuthenticationContextReferenceClaimPattern"` 的 `<Item>`，且值为 `None`。 在信赖方策略中，添加 `<OutputClaims>` 项和此元素 `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`。 另请确保策略包含声明类型 `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>` | 

## <a name="cryptographic-keys"></a>加密密钥

CryptographicKeys 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| issuer_secret | 是 | 用于对 JWT 令牌进行签名的 X509 证书（RSA 密钥集）。 这是在[自定义策略入门](active-directory-b2c-get-started-custom.md)中配置的 `B2C_1A_TokenSigningKeyContainer` 密钥。 | 
| issuer_refresh_token_key | 是 | 用于加密刷新令牌的 X509 证书（RSA 密钥集）。 在[自定义策略入门](active-directory-b2c-get-started-custom.md)中已配置 `B2C_1A_TokenEncryptionKeyContainer` 密钥 |














