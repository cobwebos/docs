---
title: 在自定义策略中为 SAML 颁发者定义技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure 活动目录 B2C 中的自定义策略中为安全断言标记语言令牌 （SAML） 颁发者定义技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967270"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure 活动目录 B2C 自定义策略中为 SAML 令牌颁发者定义技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 在处理每个身份验证流时颁发多种安全令牌。 SAML 令牌颁发者的技术配置文件发出 SAML 令牌，该令牌将返回到依赖方应用程序（服务提供商）。 通常，此技术配置文件是用户旅程中的最后一个业务流程步骤。

## <a name="protocol"></a>协议

"**Name****协议"** 元素的名称属性需要设置为`None`。 将 **OutputTokenFormat** 元素设置为 `SAML2`。

以下示例演示了 `Saml2AssertionIssuer` 的技术配置文件：

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>输入、输出和保存声明

**InputClaims**、**OutputClaims** 和 **PersistClaims** 元素为空或不存在。 **InutputClaimsTransformations** 和 **OutputClaimsTransformations** 元素也不存在。

## <a name="metadata"></a>元数据

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| IssuerUri | 否 | 出现在 SAML 响应中的颁发者名称。 该值应与依赖方应用程序中配置的名称相同。 |

## <a name="cryptographic-keys"></a>加密密钥

CryptographicKeys 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| MetadataSigning | 是 | X509 证书（RSA 密钥集），用于对 SAML 元数据进行签名。 Azure AD B2C 使用此密钥对元数据进行签名。 |
| SamlMessageSigning| 是| 指定 X509 证书（RSA 密钥集），用于对 SAML 消息进行签名。 Azure AD B2C 使用此密钥对发送到`<samlp:Response>`依赖方的响应进行签名。|

## <a name="session-management"></a>会话管理

要在依赖方应用程序之间配置 Azure AD B2C SAML 会话，`UseTechnicalProfileForSessionManagement`该元素的属性引用[SamlSSOSession 提供程序](custom-policy-reference-sso.md#samlssosessionprovider)SSO 会话。

## <a name="next-steps"></a>后续步骤

有关使用 SAML 颁发者技术配置文件的示例，请参阅以下文章：

- [在 Azure AD B2C 中注册 SAML 应用程序](connect-with-saml-service-providers.md)












