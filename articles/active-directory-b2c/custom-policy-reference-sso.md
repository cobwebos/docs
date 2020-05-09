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
ms.openlocfilehash: 4aa9f4839c8bfc04cee4bb03ea0eac98cb8b25c0
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926113"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的单一登录会话管理

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[单一登录（SSO）会话](session-overview.md)管理在自定义策略中使用与任何其他技术配置文件相同的语义。 执行某个业务流程步骤时，会在与该步骤关联的技术配置文件中查询 `UseTechnicalProfileForSessionManagement` 引用。 如果存在该引用，则会检查引用的 SSO 会话提供程序，确定用户是否为会话参与者。 如果是，则使用 SSO 会话提供程序来重新填充会话。 同样，在完成执行某个业务流程步骤后，如果已指定 SSO 会话提供程序，则使用该提供程序将信息存储在会话中。

Azure AD B2C 定义了大量可用的 SSO 会话提供程序：

|会话提供程序  |范围  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  None       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C 内部会话管理器。      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | 介于 Azure AD B2C 和 OAuth1、OAuth2 或 OpenId Connect 标识提供程序之间。        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | OAuth2 或 OpenId connect 信赖方应用程序与 Azure AD B2C。        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Azure AD B2C 与 SAML 标识提供程序之间。 SAML 服务提供商（信赖方应用程序）和 Azure AD B2C 之间。  |        




SSO 管理类是使用技术配置文件的 `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` 元素指定的。

## <a name="input-claims"></a>输入声明

`InputClaims`元素为空或不存在。

## <a name="persisted-claims"></a>持久化声明

需要返回到应用程序或后续步骤中的前置条件所使用的声明应存储在会话中，或通过从目录中用户的配置文件的读取进行扩展。 使用持久化声明可确保身份验证旅程不会对缺少的声明失败。 若要在会话中添加声明，可使用技术配置文件的 `<PersistedClaims>` 元素。 使用提供程序重新填充会话时，持久保存的声明会添加到声明包。

## <a name="output-claims"></a>输出声明

`<OutputClaims>`用于从会话中检索声明。

## <a name="session-providers"></a>会话提供程序

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

顾名思义，此提供程序不执行任何操作。 此提供程序可用于抑制特定技术配置文件的 SSO 行为。 `SM-Noop` [自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack)中包含以下技术配置文件。

```XML
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

此提供程序可以用于在会话中存储声明。 通常在用于管理本地和联合帐户的技术配置文件中引用此提供程序。 `SM-AAD` [自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack)中包含以下技术配置文件。

```XML
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


`SM-MFA` [自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`中包含以下技术配置文件。 此技术配置文件管理多重身份验证会话。

```XML
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

此提供程序用于禁止 "选择标识提供者" 屏幕和从联合标识提供程序注销。 通常在为联合标识提供者（如 Facebook 或 Azure Active Directory）配置的技术配置文件中引用它。 `SM-SocialLogin` [自定义策略初学者包](custom-policy-get-started.md#custom-policy-starter-pack)中包含以下技术配置文件。

```XML
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

| 特性 | 必需 | 说明|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | 否 | 当前未使用，可以忽略。 |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

此提供程序用于管理 OAuth2 或 OpenId Connect 信赖方和 Azure AD B2C 之间的 Azure AD B2C 会话。

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

此提供程序用于管理信赖方应用程序或联合 SAML 标识提供程序之间的 Azure AD B2C SAML 会话。 使用 SSO 提供程序存储 SAML 标识提供者会话时， `RegisterServiceProviders`必须将设置为。 `false` 以下`SM-Saml-idp`技术配置文件由[SAML 标识提供者技术配置文件](saml-identity-provider-technical-profile.md)使用。

```XML
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

使用提供程序存储 B2C SAML 会话时， `RegisterServiceProviders`必须将设置为。 `true` 需要 `SessionIndex` 和 `NameID` 才能完成 SAML 会话注销。

以下`SM-Saml-issuer`技术配置文件由[SAML 颁发者技术配置文件](saml-issuer-technical-profile.md)使用

```XML
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>元数据

| 特性 | 必需 | 说明|
| --- | --- | --- |
| IncludeSessionIndex | 否 | 当前未使用，可以忽略。|
| RegisterServiceProviders | 否 | 指示提供程序应注册已颁发断言的所有 SAML 服务提供程序。 可能的值为 `true`（默认）或 `false`。|


## <a name="next-steps"></a>后续步骤

- 了解[Azure AD B2C 会话](session-overview.md)的详细信息。
- 了解如何[在自定义策略中配置会话行为](session-behavior-custom-policy.md)。
