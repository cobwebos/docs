---
title: 在自定义策略中 Azure AD SSPR 技术配置文件
titleSuffix: Azure AD B2C
description: Azure AD B2C 中 Azure AD SSPR 技术配置文件的自定义策略参考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85383591"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中定义 Azure AD SSPR 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）为验证自助服务密码重置（SSPR）的电子邮件地址提供支持。 使用 Azure AD SSPR 技术配置文件生成代码并将其发送到电子邮件地址，然后验证代码。 Azure AD SSPR 技术配置文件可能还会返回一条错误消息。 在用户旅程继续执行之前，验证技术配置文件将验证用户提供的数据。 使用验证技术配置文件，自断言页面上将显示一条错误消息。

此技术配置文件：

- 不提供与用户交互的接口。 相反，用户界面是从[自断言](self-asserted-technical-profile.md)技术配置文件或[显示控件](display-controls.md)作为[验证技术配置文件](validation-technical-profile.md)调用的。
- 使用 Azure AD SSPR 服务生成代码并将其发送到电子邮件地址，然后验证代码。  
- 通过验证代码来验证电子邮件地址。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 handler 属性必须包含 Azure AD B2C 使用的协议处理程序程序集的完全限定名称：

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

以下示例演示了一个 Azure AD SSPR 技术配置文件：

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>发送电子邮件

此技术配置文件的第一种模式是生成并发送代码。 可以为该模式配置以下选项。

### <a name="input-claims"></a>输入声明

**InputClaims**元素包含要发送到 Azure AD SSPR 的声明的列表。 你还可以将声明名称映射到 SSPR 技术配置文件中定义的名称。

| ClaimReferenceId | 必须 | 描述 |
| --------- | -------- | ----------- |
| emailAddress | 是 | 拥有电子邮件地址的用户的标识符。 `PartnerClaimType`输入声明的属性必须设置为 `emailAddress` 。 |


**InputClaimsTransformations**元素可包含**InputClaimsTransformation**元素的集合，这些元素用于修改输入声明，或在发送到 Azure AD SSPR 服务之前生成新声明。

### <a name="output-claims"></a>输出声明

Azure AD SSPR 协议提供程序不返回任何**OutputClaims**，因此无需指定输出声明。 但是，只要设置了属性，就可以包括 Azure AD SSPR 协议提供程序未返回的声明 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="metadata"></a>Metadata

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| 操作 | 是 | 必须为**SendCode**。  |

#### <a name="ui-elements"></a>UI 元素

以下元数据可用于配置发送 SMS 失败时显示的错误消息。 元数据应该在[自断言](self-asserted-technical-profile.md)技术配置文件中进行配置。 可以将错误消息[本地化](localization-string-ids.md#azure-ad-sspr)。

| 属性 | 必需 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | 否 | 如果服务器遇到内部错误，则为用户错误消息。 |
| UserMessageIfThrottled| 否 | 如果已限制请求，则为用户错误消息。|


### <a name="example-send-an-email"></a>示例：发送电子邮件

下面的示例演示一个用于通过电子邮件发送代码 Azure AD SSPR 技术配置文件。

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>验证验证码

此技术配置文件的第二种模式是验证代码。 可以为该模式配置以下选项。

### <a name="input-claims"></a>输入声明

**InputClaims**元素包含要发送到 Azure AD SSPR 的声明的列表。 你还可以将声明名称映射到 SSPR 技术配置文件中定义的名称。

| ClaimReferenceId | 必须 | 描述 |
| --------- | -------- | ----------- | ----------- |
| emailAddress| 是 | 与以前用于发送代码的电子邮件地址相同。 它还用于查找电子邮件验证会话。 `PartnerClaimType`输入声明的属性必须设置为 `emailAddress` 。|
| verificationCode  | 是 | 要验证的用户提供的验证码。 `PartnerClaimType`输入声明的属性必须设置为 `verificationCode` 。 |

**InputClaimsTransformations**元素可包含**InputClaimsTransformation**元素的集合，这些元素用于在调用 Azure AD SSPR 服务之前修改输入声明或生成新的输入声明。

### <a name="output-claims"></a>输出声明

Azure AD SSPR 协议提供程序不返回任何**OutputClaims**，因此无需指定输出声明。 但是，只要设置了属性，就可以包括 Azure AD SSPR 协议提供程序未返回的声明 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="metadata"></a>Metadata

| Attribute | 必需 | 说明 |
| --------- | -------- | ----------- |
| 操作 | 是 | 必须是**VerifyCode** |

#### <a name="ui-elements"></a>UI 元素

以下元数据可用于配置在代码验证失败时显示的错误消息。 元数据应该在[自断言](self-asserted-technical-profile.md)技术配置文件中进行配置。 可以将错误消息[本地化](localization-string-ids.md#azure-ad-sspr)。

| 属性 | 必需 | 描述 |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | 代码验证会话过期后向用户显示的消息。 代码已过期，或者从未为给定标识符生成了代码。|
|UserMessageIfInternalError | 如果服务器遇到内部错误，则为用户错误消息。|
|UserMessageIfThrottled | 如果已限制请求，则为用户错误消息。|
|UserMessageIfVerificationFailedNoRetry | 向用户显示的消息（如果用户提供了无效的代码），并且不允许用户提供正确的代码。|
|UserMessageIfVerificationFailedRetryAllowed | 向用户显示的消息（如果用户提供了无效的代码），并且允许用户提供正确的代码。|

### <a name="example-verify-a-code"></a>示例：验证代码

下面的示例演示用于验证代码的 Azure AD SSPR 技术配置文件。

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```