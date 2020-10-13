---
title: 在自定义策略中定义电话因素技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义电话因素技术配置文件
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 322e4b78fbfb38f1822fb7a7cdcdbfcc0738b303
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950390"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义电话因素技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 支持注册和验证电话号码。 此技术配置文件：

- 提供一个用户界面，用于与用户进行交互以验证或注册电话号码。
- 支持使用电话呼叫和短信来验证电话号码。
- 支持多个电话号码。 用户可以选择一个要验证的电话号码。  
- 返回一个声明，指示用户是否提供了新电话号码。 可以使用此声明来确定是否要将电话号码保存在 Azure AD B2C 用户配置文件中。  
- 使用[内容定义](contentdefinitions.md)来控制外观。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 handler 属性必须包含 Azure AD B2C 对电话因素使用的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

以下示例演示了用于注册和验证的电话因素技术配置文件：

```xml
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>输入声明转换

InputClaimsTransformations 元素可以包含一组输入声明转换，这些转换用于修改输入声明或生成新的输入声明。 以下输入声明转换将生成一个 `UserId` 声明，稍后在输入声明集合中将使用该声明。

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>输入声明

InputClaims 元素必须包含以下声明。 还可将声明名称映射到电话因素技术配置文件中定义的名称。 

|  数据类型| 必须 | 说明 |
| --------- | -------- | ----------- | 
| string| 是 | 用户的唯一标识符。 声明名称或 PartnerClaimType 必须设置为 `UserId`。 此声明不应包含个人身份信息。|
| string| 是 | 声明类型的列表。 每个声明都包含一个电话号码。 如果有任何输入声明不包含电话号码，则会要求用户注册并验证新的电话号码。 已验证的电话号码将作为输出声明返回。 如果其中一个输入声明包含电话号码，则要求用户对其进行验证。 如果有多个输入声明包含电话号码，则会要求用户选择并验证其中一个电话号码。 |

以下示例演示如何使用多个电话号码。 有关详细信息，请参阅[示例策略](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>输出声明

OutputClaims 元素包含电话因素技术配置文件返回的声明列表。

|  数据类型| 必须 | 说明 |
|  -------- | ----------- |----------- |
| boolean | 是 | 指明用户是否已输入新的电话号码。 声明名称或 PartnerClaimType 必须设置为 `newPhoneNumberEntered`|
| string| 是 | 已验证的电话号码。 声明名称或 PartnerClaimType 必须设置为 `Verified.OfficePhone`。|

OutputClaimsTransformations 元素可以包含用于修改输出声明或生成新输出声明的 OutputClaimsTransformation 元素集合。

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”元素。


## <a name="metadata"></a>Metadata

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 是 | 与此技术配置文件关联的[内容定义](contentdefinitions.md)的标识符。 |
| ManualPhoneNumberEntryAllowed| 否 | 指定是否允许用户手动输入电话号码。 可能的值：`true` 或 `false`（默认值）。|
| setting.authenticationMode | 否 | 用于验证电话号码的方法。 可能的值：`sms`、`phone` 或 `mixed`（默认值）。|
| setting.autodial| 否| 指定技术配置文件是否应自动拨号或自动发送短信。 可能的值：`true` 或 `false`（默认值）。 自动拨号要求将 `setting.authenticationMode` 元数据设置为 `sms` 或 `phone`。 输入声明集合必须包含单个电话号码。 |

### <a name="ui-elements"></a>UI 元素

可以[本地化](localization-string-ids.md#phone-factor-authentication-page-user-interface-elements)电话因素身份验证页的用户界面元素。

## <a name="next-steps"></a>后续步骤

- 查看[使用 MFA 的社交和本地帐户](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)初学者包。
