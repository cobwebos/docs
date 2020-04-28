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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437456"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义电话因素技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 支持注册和验证电话号码。 此技术配置文件：

- 提供一个用户界面，用于与用户进行交互以验证或注册电话号码。
- 支持电话呼叫和短信以验证电话号码。
- 支持多个电话号码。 用户可以选择一个电话号码进行验证。  
- 返回一个声明，该声明指示用户是否提供了新的电话号码。 您可以使用此声明来决定是否应将电话号码保存到 Azure AD B2C 用户配置文件。  
- 使用[内容定义](contentdefinitions.md)来控制外观。

## <a name="protocol"></a>协议

**协议**元素的`Proprietary` **Name**属性需要设置为。 **处理程序**属性必须包含协议处理程序程序集的完全限定名称，该程序集由 Azure AD B2C 用于电话系数：`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

以下示例演示了用于注册和验证的电话系数技术配置文件：

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>输入声明转换

InputClaimsTransformations 元素可能包含一组输入声明转换，这些转换用于修改输入声明或生成新声明。 以下输入声明转换将生成一个`UserId`声明，该声明稍后用于输入声明集合。

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>输入声明

InputClaims 元素必须包含以下声明。 你还可以将声明名称映射到在电话系数技术配置文件中定义的名称。 

|  数据类型| 必需 | 说明 |
| --------- | -------- | ----------- | 
| 字符串| 是 | 用户的唯一标识符。 声明名称或 PartnerClaimType 必须设置为`UserId`。 此声明不应包含个人身份信息。|
| 字符串| 是 | 声明类型的列表。 每个声明都包含一个电话号码。 如果任何输入声明不包含电话号码，则会要求用户注册并验证新的电话号码。 验证的电话号码将以输出声明的形式返回。 如果其中一个输入声明包含电话号码，则要求用户对其进行验证。 如果多个输入声明包含一个电话号码，则要求用户选择并验证电话号码之一。 |

下面的示例演示如何使用多个电话号码。 有关详细信息，请参阅[示例策略](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>输出声明

OutputClaims 元素包含由电话系数技术配置文件返回的声明列表。

|  数据类型| 必需 | 说明 |
|  -------- | ----------- |----------- |
| boolean | 是 | 指示是否已由用户输入新的电话号码。 声明名称或 PartnerClaimType 必须设置为`newPhoneNumberEntered`|
| 字符串| 是 | 经验证的电话号码。 声明名称或 PartnerClaimType 必须设置为`Verified.OfficePhone`。|

OutputClaimsTransformations 元素可包含 OutputClaimsTransformation 元素的集合，这些元素用于修改输出声明或生成新声明。

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”**** 元素。


## <a name="metadata"></a>元数据

| 特性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 是 | 与此技术配置文件关联的[内容定义](contentdefinitions.md)的标识符。 |
| ManualPhoneNumberEntryAllowed| 否 | 指定是否允许用户手动输入电话号码。 可能的值：`true` 或 `false`（默认值）。|
| 设置。 authenticationMode | 否 | 用于验证电话号码的方法。 可能的值`sms`： `phone`、或`mixed` （默认值）。|
| 设置。自动拨号| 否| 指定技术配置文件是否应自动拨号或自动发送短信。 可能的值：`true` 或 `false`（默认值）。 自动拨号要求`setting.authenticationMode`元数据设置为`sms`或。 `phone` 输入声明集合必须有一个电话号码。 |

### <a name="ui-elements"></a>UI 元素

可以[本地化](localization-string-ids.md#azure-mfa-error-messages)"电话系数身份验证" 页的用户界面元素。

## <a name="next-steps"></a>后续步骤

- 检查[社会和本地帐户是否有 MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) starter pack。
