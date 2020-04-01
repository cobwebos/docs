---
title: 在自定义策略中定义电话因子技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure 活动目录 B2C 中的自定义策略中定义电话因子技术配置文件。
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
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437456"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure 活动目录 B2C 自定义策略中定义电话因子技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活动目录 B2C（Azure AD B2C）支持注册和验证电话号码。 此技术配置文件：

- 提供用户界面，以便与用户进行交互以验证或注册电话号码。
- 支持电话呼叫和短信以验证电话号码。
- 支持多个电话号码。 用户可以选择要验证的一个电话号码。  
- 返回声明，指示用户是否提供了新电话号码。 可以使用此声明来决定是否应将电话号码保留到 Azure AD B2C 用户配置文件。  
- 使用[内容定义](contentdefinitions.md)来控制外观。

## <a name="protocol"></a>协议

"**Name****协议"** 元素的名称属性需要设置为`Proprietary`。 **处理程序**属性必须包含 Azure AD B2C 用于电话因子的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的示例显示了用于注册和验证的电话因子技术配置文件：

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>输入声明转换

InputClaims 转换元素可能包含用于修改输入声明或生成新声明的输入声明转换的集合。 以下输入声明转换生成一个`UserId`声明，该声明稍后用于输入声明集合。

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>输入声明

InputClaims 元素必须包含以下声明。 您还可以将索赔的名称映射到电话因子技术配置文件中定义的名称。 

|  数据类型| 必选 | 说明 |
| --------- | -------- | ----------- | 
| 字符串| 是 | 用户的唯一标识符。 声明名称或合作伙伴索赔类型必须设置为`UserId`。 此声明不应包含个人可识别信息。|
| 字符串| 是 | 声明类型的列表。 每个声明包含一个电话号码。 如果任何输入声明不包含电话号码，将询问用户注册并验证新电话号码。 已验证的电话号码将作为输出声明返回。 如果其中一个输入声明包含电话号码，则要求用户验证它。 如果多个输入声明包含电话号码，则要求用户选择并验证其中一个电话号码。 |

下面的示例演示了使用多个电话号码。 有关详细信息，请参阅[示例策略](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>输出声明

"输出索赔"元素包含电话因子技术配置文件返回的声明列表。

|  数据类型| 必选 | 说明 |
|  -------- | ----------- |----------- |
| boolean | 是 | 指示用户是否已输入新电话号码。 声明名称或合作伙伴索赔类型必须设置为`newPhoneNumberEntered`|
| 字符串| 是 | 已验证的电话号码。 声明名称或合作伙伴索赔类型必须设置为`Verified.OfficePhone`。|

OutputClaims 转换元素可能包含用于修改输出声明或生成新声明的输出声明转换元素的集合。

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”**** 元素。


## <a name="metadata"></a>元数据

| 特性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 是 | 与此技术配置文件关联的[内容定义](contentdefinitions.md)的标识符。 |
| 手动电话号码输入允许| 否 | 指定是否允许用户手动输入电话号码。 可能的值：`true` 或 `false`（默认值）。|
| 设置.身份验证模式 | 否 | 验证电话号码的方法。 可能的值： `sms` `phone`、`mixed`或 （默认值）。|
| 设置.自动拨号| 否| 指定技术配置文件是自动拨号还是自动发送 SMS。 可能的值：`true` 或 `false`（默认值）。 自动拨号要求将`setting.authenticationMode`元数据设置为`sms`或 。 `phone` 输入声明集合必须具有单个电话号码。 |

### <a name="ui-elements"></a>UI 元素

电话因子认证页面用户界面元素可以[本地化](localization-string-ids.md#azure-mfa-error-messages)。

## <a name="next-steps"></a>后续步骤

- 使用 MFA 初学者包检查[社交和本地帐户](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)。
