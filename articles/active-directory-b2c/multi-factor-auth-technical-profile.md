---
title: 自定义策略中的 Azure MFA 技术配置文件
titleSuffix: Azure AD B2C
description: Azure AD B2C 中的 Azure 多重身份验证（MFA）技术配置文件的自定义策略参考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05851dba9de06b5dfba2da4f455fbaf5e9376d08
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184275"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中定义 Azure MFA 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）支持使用 Azure 多重身份验证（MFA）来验证电话号码。 使用此技术配置文件生成代码并将其发送到电话号码，然后验证代码。

Azure MFA 技术配置文件可能还会返回一条错误消息。 可以通过使用**验证技术配置文件**来设计与 Azure MFA 的集成。 验证技术配置文件调用 Azure MFA 服务。 在用户旅程继续执行之前，验证技术配置文件将验证用户提供的数据。 使用验证技术配置文件，自断言页面上将显示一条错误消息。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 **处理程序**特性必须包含 Azure AD B2C 所使用的协议处理程序程序集的完全限定名称：

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

以下示例演示了 Azure MFA 技术配置文件：

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>发送短信

此技术配置文件的第一种模式是生成代码并发送。 可以为此模式配置以下选项。

### <a name="input-claims"></a>输入声明

**InputClaims**元素包含要发送到 Azure MFA 的声明列表。 你还可以将你的声明名称映射到在 MFA 技术配置文件中定义的名称。

| ClaimReferenceId | 必选 | 说明 |
| --------- | -------- | ----------- |
| userPrincipalName | 是 | 拥有电话号码的用户的标识符。 |
| phoneNumber | 是 | 要向其发送短信代码的电话号码。 |
| companyName | 否 |SMS 中的公司名称。 如果未提供，则使用应用程序的名称。 |
| 区域设置 | 否 | 短信的区域设置。 如果未提供，则使用用户的浏览器区域设置。 |

**InputClaimsTransformations**元素可包含**InputClaimsTransformation**元素的集合，这些元素用于在发送到 Azure MFA 服务之前修改输入声明或生成新声明。

### <a name="output-claims"></a>输出声明

Azure MFA 协议提供程序不返回任何**OutputClaims**，因此无需指定输出声明。 但是，只要设置了 `DefaultValue` 属性，就可以包含 Azure MFA 标识提供程序未返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="metadata"></a>元数据

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| Operation | 是 | 必须为**OneWaySMS**。  |
| UserMessageIfInvalidFormat | 否 | 自定义错误消息（如果提供的电话号码不是有效的电话号码） |
| UserMessageIfCouldntSendSms | 否 | 自定义错误消息（如果提供的电话号码不接受短信） |
| UserMessageIfServerError | 否 | 如果服务器遇到内部错误，则为自定义错误消息 |

### <a name="return-an-error-message"></a>返回错误消息

如[元数据](#metadata)中所述，你可以针对不同的错误情况自定义向用户显示的错误消息。 可以通过对区域设置进行前缀来进一步本地化这些消息。 例如：

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>示例：发送短信

以下示例显示了用于通过 SMS 发送代码的 Azure MFA 技术配置文件。

```XML
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

此技术配置文件的第二种模式是验证代码。 可以为此模式配置以下选项。

### <a name="input-claims"></a>输入声明

**InputClaims**元素包含要发送到 Azure MFA 的声明列表。 你还可以将你的声明名称映射到在 MFA 技术配置文件中定义的名称。

| ClaimReferenceId | 必选 | 说明 |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| 是 | 与以前用于发送代码的电话号码相同。 它还用于查找电话验证会话。 |
| verificationCode  | 是 | 要验证的用户提供的验证码 |

**InputClaimsTransformations**元素可包含**InputClaimsTransformation**元素的集合，这些元素用于修改输入声明，或在调用 Azure MFA 服务之前生成新输入声明。

### <a name="output-claims"></a>输出声明

Azure MFA 协议提供程序不返回任何**OutputClaims**，因此无需指定输出声明。 但是，只要设置了 `DefaultValue` 属性，就可以包含 Azure MFA 标识提供程序未返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

## <a name="metadata"></a>元数据

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| Operation | 是 | 必须**验证** |
| UserMessageIfInvalidFormat | 否 | 自定义错误消息（如果提供的电话号码不是有效的电话号码） |
| UserMessageIfWrongCodeEntered | 否 | 如果为验证输入的代码错误，则为自定义错误消息 |
| UserMessageIfMaxAllowedCodeRetryReached | 否 | 如果用户尝试验证代码的次数过多，则为自定义错误消息 |
| UserMessageIfThrottled | 否 | 如果用户被限制，则为自定义错误消息 |
| UserMessageIfServerError | 否 | 如果服务器遇到内部错误，则为自定义错误消息 |

### <a name="return-an-error-message"></a>返回错误消息

如[元数据](#metadata)中所述，你可以针对不同的错误情况自定义向用户显示的错误消息。 可以通过对区域设置进行前缀来进一步本地化这些消息。 例如：

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>示例：验证代码

下面的示例演示用于验证代码的 Azure MFA 技术配置文件。

```XML
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
