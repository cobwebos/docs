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
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332653"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure 活动目录 B2C 自定义策略中定义电话因子技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活动目录 B2C（Azure AD B2C）支持注册和验证电话号码。 此技术配置文件：

- 提供与用户交互的用户界面。
- 使用内容定义来控制外观。
- 支持电话和短信来验证电话号码。
- 支持多个电话号码。 用户可以选择要验证的一个电话号码。  
- 如果提供电话号码，则电话因子用户界面要求用户验证电话号码。 如果未提供，则要求用户注册新电话号码。
- 返回声明，指示用户是否提供了新电话号码。 可以使用此声明来决定是否应将电话号码保留到 Azure AD 用户配置文件。  

## <a name="protocol"></a>协议

"**Name****协议"** 元素的名称属性需要设置为`Proprietary`。 **处理程序**属性必须包含 Azure AD B2C 用于电话因子的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的示例显示了用于注册和验证的电话因子技术配置文件：

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>输入声明

InputClaims 元素必须包含以下声明。 您还可以将索赔的名称映射到电话因子技术配置文件中定义的名称。 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

下面的示例演示了使用多个电话号码。 有关详细信息，请参阅[示例策略](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

InputClaims转换元素可能包含一个输入声明转换元素的集合，这些元素用于修改输入声明或生成新声明，然后再将其呈现到电话因子页。

## <a name="output-claims"></a>输出声明

"输出索赔"元素包含电话因子技术配置文件返回的声明列表。

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

OutputClaimsTransformations 元素可能包含用于修改输出声明或生成新输出声明的 OutputClaimsTransformation 元素集合。

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”**** 元素。


## <a name="metadata"></a>元数据

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 是 | 与此技术配置文件关联的[内容定义](contentdefinitions.md)的标识符。 |
| 手动电话号码输入允许| 否 | 指定是否允许用户手动输入电话号码。 可能的值：`true`或`false`（默认值）。|

### <a name="ui-elements"></a>UI 元素

电话因子认证页面用户界面元素可以[本地化](localization-string-ids.md#azure-mfa-error-messages)。

## <a name="next-steps"></a>后续步骤

- 使用 MFA 初学者包检查[社交和本地帐户](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)。

