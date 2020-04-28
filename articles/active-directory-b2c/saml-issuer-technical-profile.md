---
title: 在自定义策略中定义 SAML 颁发者的技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义安全断言标记语言令牌 (SAML) 颁发者的技术配置文件。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78967270"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 SAML 令牌颁发者的技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 在处理每个身份验证流时颁发多种安全令牌。 SAML 令牌颁发者的技术配置文件会发出返回给信赖方应用程序（服务提供程序）的 SAML 令牌。 通常，此技术配置文件是用户旅程中的最后一个业务流程步骤。

## <a name="protocol"></a>协议

“Protocol”  元素的“Name”  属性必须设置为 `None`。 将 **OutputTokenFormat** 元素设置为 `SAML2`。

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

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| IssuerUri | 否 | SAML 响应中出现的颁发者名称。 该值应与信赖方应用程序中配置的名称相同。 |

## <a name="cryptographic-keys"></a>加密密钥

CryptographicKeys 元素包含以下属性：

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| MetadataSigning | 是 | X509 证书（RSA 密钥集），用于对 SAML 元数据进行签名。 Azure AD B2C 使用此密钥对元数据进行签名。 |
| SamlMessageSigning| 是| 指定 X509 证书（RSA 密钥集），用于对 SAML 消息进行签名。 Azure AD B2C 使用此密钥对发送给信赖方的响应 `<samlp:Response>` 进行签名。|

## <a name="session-management"></a>会话管理

在信赖方应用程序之间配置 Azure AD B2C SAML 会话、`UseTechnicalProfileForSessionManagement` 元素的属性、对 [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO 会话的引用。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关使用 SAML 颁发者技术配置文件的示例：

- [在 Azure AD B2C 中注册 SAML 应用程序](connect-with-saml-service-providers.md)












