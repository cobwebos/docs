---
title: 使用自定义策略管理单一登录会话
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure AD B2C 中的自定义策略管理 SSO 会话。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4824b64236270c422f22809e9eeb191ee3be27fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85202562"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的单一登录会话管理

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[单一登录 (SSO) 会话](session-overview.md)管理使用的语义与自定义策略中其他任何技术配置文件使用的语义相同。 执行某个业务流程步骤时，会在与该步骤关联的技术配置文件中查询 `UseTechnicalProfileForSessionManagement` 引用。 如果存在该引用，则会检查引用的 SSO 会话提供程序，确定用户是否为会话参与者。 如果是，则使用 SSO 会话提供程序来重新填充会话。 同样，在完成执行某个业务流程步骤后，如果已指定 SSO 会话提供程序，则使用该提供程序将信息存储在会话中。

Azure AD B2C 定义了大量可用的 SSO 会话提供程序：

|会话提供程序  |作用域  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  无       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C 内部会话管理器。      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | 在 Azure AD B2C 和 OAuth1、OAuth2 或 OpenId 连接标识提供者之间。        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | 在 OAuth2 或 OpenId 连接依赖方应用程序和 Azure AD B2C 之间。        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | 在 Azure AD B2C 和 SAML 标识提供者之间。 以及在 SAML 服务提供商（信赖方应用）和 Azure AD B2C 之间。  |        




SSO 管理类是使用技术配置文件的 `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` 元素指定的。

## <a name="input-claims"></a>输入声明

`InputClaims` 元素为空或不存在。

## <a name="persisted-claims"></a>持久化声明

需要返回到应用程序或在后续步骤中由前置条件使用的声明，应该存储在会话中或通过从目录中用户配置文件读取来进行扩充。 使用持久化声明可确保身份验证旅程不会在缺少声明时失败。 若要在会话中添加声明，可使用技术配置文件的 `<PersistedClaims>` 元素。 使用提供程序重新填充会话时，持久保存的声明会添加到声明包。

## <a name="output-claims"></a>输出声明

`<OutputClaims>` 用于从会话中检索声明。

## <a name="session-providers"></a>会话提供程序

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

顾名思义，此提供程序不执行任何操作。 此提供程序可用于抑制特定技术配置文件的 SSO 行为。 `SM-Noop`[自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack)中包含以下技术配置文件。

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

此提供程序可以用于在会话中存储声明。 此提供程序通常在用于管理本地和联合帐户的技术配置文件中引用。 `SM-AAD`[自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack)中包含以下技术配置文件。

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


`SM-MFA`[自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack)中包含以下技术配置文件 `SocialAndLocalAccountsWithMfa` 。 此技术配置文件管理多重身份验证会话。

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

此提供程序用于禁止“选择标识提供者”屏幕并从联合标识提供者注销。 通常在为联合标识提供者（如 Facebook 或 Azure Active Directory）配置的技术配置文件中引用它。 `SM-SocialLogin`[自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack)中包含以下技术配置文件。

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="AlwaysFetchClaimsFromProvider">true</Item>
  </Metadata>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>元数据

| Attribute | 必需 | 描述|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | 否 | 当前未使用，可以忽略。 |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

此提供程序用于管理 OAuth2 或 OpenId 连接信赖方与 Azure AD B2C 之间的 Azure AD B2C 会话。

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

此提供程序用于管理信赖方应用程序或联合 SAML 标识提供者之间的 Azure AD B2C SAML 会话。 使用 SSO 提供程序存储 SAML 标识提供者会话时，`RegisterServiceProviders` 必须设为 `false`。 以下 `SM-Saml-idp` 技术配置文件由 [SAML 标识提供者技术配置文件](saml-identity-provider-technical-profile.md)使用。

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

使用提供程序存储 B2C SAML 会话时，`RegisterServiceProviders` 必须设为 `true`。 需要 `SessionIndex` 和 `NameID` 才能完成 SAML 会话注销。

以下 `SM-Saml-issuer` 技术配置文件由 [SAML 颁发者技术配置文件](saml-issuer-technical-profile.md)使用

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadata

| Attribute | 必需 | 说明|
| --- | --- | --- |
| IncludeSessionIndex | 否 | 当前未使用，可以忽略。|
| RegisterServiceProviders | 否 | 指示提供程序应注册已颁发断言的所有 SAML 服务提供程序。 可能的值为 `true`（默认）或 `false`。|


## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure AD B2C 会话](session-overview.md)。
- 了解如何[在自定义策略中配置会话行为](session-behavior-custom-policy.md)。
