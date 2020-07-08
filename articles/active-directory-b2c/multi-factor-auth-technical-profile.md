---
title: 自定义策略中的 Azure MFA 技术配置文件
titleSuffix: Azure AD B2C
description: Azure AD B2C 中的 Azure 多重身份验证 (MFA) 技术配置文件的自定义策略参考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 71040f831ed7a64f2bc7be7f3a75218976fc2559
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385937"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中定义 Azure MFA 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 使用 Azure 多重身份验证 (MFA) 为验证电话号码提供支持。 使用此技术配置文件生成代码并将其发送到一定的手机号，然后验证该代码。 Azure MFA 技术配置文件也可能返回错误消息。  在用户旅程继续执行之前，验证技术配置文件将验证用户提供的数据。 使用验证技术配置文件时，将在自断言页面上显示错误消息。

此技术配置文件：

- 不提供与用户交互的接口， 而是从[自断言](self-asserted-technical-profile.md)技术配置文件或充当[验证技术配置文件](validation-technical-profile.md)的[显示控件](display-controls.md)中调用用户界面。
- 使用 Azure MFA 服务生成代码并将其发送到某个手机号，然后验证该代码。  
- 通过文本消息验证电话号码。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 handler 属性必须包含 Azure AD B2C 使用的协议处理程序程序集的完全限定名称  ：

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

以下示例演示了 Azure MFA 的技术配置文件：

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>发送短信

此技术配置文件的第一种模式是生成并发送代码。 可以为该模式配置以下选项。

### <a name="input-claims"></a>输入声明

InputClaims 元素包含要发送到 Azure MFA 的声明的列表  。 还可将声明名称映射到 MFA 技术配置文件中定义的名称。

| ClaimReferenceId | 必选 | 说明 |
| --------- | -------- | ----------- |
| userPrincipalName | 是 | 拥有此电话号码的用户的标识符。 |
| phoneNumber | 是 | 要将短信代码发送到的电话号码。 |
| companyName | 否 |短信中的公司名称。 如果未提供，则使用应用程序的名称。 |
| 区域设置 | 否 | 短信的区域设置。 如果未提供，则使用用户的浏览器区域设置。 |

InputClaimsTransformations 元素可能包含一系列 InputClaimsTransformation 元素，这些元素用于修改输入声明，或者生成新的声明并将其发送到 Azure MFA 服务。  

### <a name="output-claims"></a>输出声明

Azure MFA 协议提供程序未返回任何 OutputClaims，因此无需指定输出声明  。 但是，只要设置了 `DefaultValue` 属性，就可以包含 Azure MFA 标识提供者不会返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="metadata"></a>元数据

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| Operation | 是 | 必须是 OneWaySMS  。  |

#### <a name="ui-elements"></a>UI 元素

以下元数据可用于配置在发送短信失败时显示的错误消息。 元数据应该在[自断言](self-asserted-technical-profile.md)技术配置文件中进行配置。 可以将错误消息[本地化](localization-string-ids.md#azure-mfa-error-messages)。

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | 否 | 提供的电话号码不可接收短信时显示的用户错误消息。 |
| UserMessageIfInvalidFormat | 否 | 提供的电话号码不是有效电话号码时显示的用户错误消息。 |
| UserMessageIfServerError | 否 | 服务器遇到内部错误时显示的用户错误消息。 |
| UserMessageIfThrottled| 否 | 请求被限制时显示的用户错误消息。|

### <a name="example-send-an-sms"></a>示例：发送短信

以下示例显示了一个 Azure MFA 技术配置文件（用于通过短信发送代码）。

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>验证验证码

该技术配置文件的第二种模式是验证代码。 可以为该模式配置以下选项。

### <a name="input-claims"></a>输入声明

InputClaims 元素包含要发送到 Azure MFA 的声明的列表  。 还可将声明名称映射到 MFA 技术配置文件中定义的名称。

| ClaimReferenceId | 必选 | 说明 |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| 是 | 与之前用于发送代码的手机号相同。 它也用来定位电话验证会话。 |
| verificationCode  | 是 | 待验证用户提供的验证码 |

InputClaimsTransformations 元素可能包含一系列 InputClaimsTransformation 元素，这些元素用于修改输入声明，或者生成新的声明并调用 Azure MFA 服务。  

### <a name="output-claims"></a>输出声明

Azure MFA 协议提供程序未返回任何 OutputClaims，因此无需指定输出声明  。 但是，只要设置了 `DefaultValue` 属性，就可以包含 Azure MFA 标识提供者不会返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="metadata"></a>元数据

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| Operation | 是 | 必须是“验证”  |

#### <a name="ui-elements"></a>UI 元素

以下元数据可用于配置在代码验证失败时显示的错误消息。 元数据应该在[自断言](self-asserted-technical-profile.md)技术配置文件中进行配置。 可以将错误消息[本地化](localization-string-ids.md#azure-mfa-error-messages)。

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| 否 | 用户多次尝试提交某验证码时显示的用户错误消息。 |
| UserMessageIfServerError | 否 | 服务器遇到内部错误时显示的用户错误消息。 |
| UserMessageIfThrottled| 否 | 请求受限制时显示的用户错误消息。|
| UserMessageIfWrongCodeEntered| 否| 输入的验证码错误时显示的用户错误消息。|

### <a name="example-verify-a-code"></a>示例：验证代码

以下示例显示了用来验证代码的 Azure MFA 技术配置文件。

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
