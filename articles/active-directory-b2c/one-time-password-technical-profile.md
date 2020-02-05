---
title: 启用一次性密码（OTP）验证
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure AD B2C 自定义策略设置一次性密码（OTP）方案。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dab35fbcd221af9f4eb587b8c98a8ff85aeef59f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982783"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中定义一次性密码技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）支持管理一次性密码的生成和验证。 使用技术配置文件生成代码，并稍后验证代码。

一次性密码技术配置文件也可以在代码验证过程中返回错误消息。 使用**验证技术配置文件**设计与一次性密码的集成。 验证技术配置文件调用一次性密码技术配置文件来验证代码。 在用户旅程继续执行之前，验证技术配置文件将验证用户提供的数据。 使用验证技术配置文件，自断言页面上将显示一条错误消息。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 **处理程序**特性必须包含 Azure AD B2C 所使用的协议处理程序程序集的完全限定名称：

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

以下示例显示了一次性密码技术配置文件：

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>生成代码

此技术配置文件的第一种模式是生成代码。 下面是可为此模式配置的选项。

### <a name="input-claims"></a>输入声明

**InputClaims**元素包含发送到一次性密码协议提供程序所需的声明的列表。 你还可以将声明名称映射到下面定义的名称。

| ClaimReferenceId | 需要 | Description |
| --------- | -------- | ----------- |
| 标识符 (identifier) | 是 | 标识稍后需要验证代码的用户的标识符。 它通常用作代码传递到的目标的标识符，例如电子邮件地址或电话号码。 |

**InputClaimsTransformations**元素可包含**InputClaimsTransformation**元素的集合，这些元素用于修改输入声明，或在发送到一次性密码协议提供程序之前生成新声明。

### <a name="output-claims"></a>输出声明

**OutputClaims**元素包含由一次性密码协议提供程序生成的声明列表。 你还可以将声明名称映射到下面定义的名称。

| ClaimReferenceId | 需要 | Description |
| --------- | -------- | ----------- |
| otpGenerated | 是 | Azure AD B2C 管理其会话的生成代码。 |

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="metadata"></a>元数据

以下设置可用于配置代码生成和维护：

| Attribute | 需要 | Description |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | 否 | 代码过期之前的时间（秒）。 最小值： `60`;最大值： `1200`;默认值： `600`。 |
| CodeLength | 否 | 代码的长度。 默认值是 `6`。 |
| CharacterSet | 否 | 代码的字符集，格式设置为在正则表达式中使用。 例如，`a-z0-9A-Z` 。 默认值是 `0-9`。 字符集必须在指定的集中至少包含10个不同的字符。 |
| NumRetryAttempts | 否 | 代码被视为无效之前的验证尝试次数。 默认值是 `5`。 |
| 操作 | 是 | 要执行的操作。 可能的值： `GenerateCode`或 `VerifyCode`。 |
| ReuseSameCode | 否 | 当给定代码未过期且仍然有效时，是否应提供重复的代码，而不是生成新代码。 默认值是 `false`。 |

### <a name="returning-error-message"></a>返回错误消息

没有为代码生成模式返回错误消息。

### <a name="example"></a>示例

下面的示例 `TechnicalProfile` 用于生成代码：

```XML
<TechnicalProfile Id="GenerateCode">
    <DisplayName>Generate Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">600</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="NumRetryAttempts">5</Item>
        <Item Key="ReuseSameCode">false</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
    </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>验证验证码

此技术配置文件的第二种模式是验证代码。 下面是可为此模式配置的选项。

### <a name="input-claims"></a>输入声明

**InputClaims**元素包含发送到一次性密码协议提供程序所需的声明的列表。 你还可以将声明名称映射到下面定义的名称。

| ClaimReferenceId | 需要 | Description |
| --------- | -------- | ----------- |
| 标识符 (identifier) | 是 | 标识之前已生成代码的用户的标识符。 它通常用作代码传递到的目标的标识符，例如电子邮件地址或电话号码。 |
| otpToVerify | 是 | 用户提供的验证码。 |

**InputClaimsTransformations**元素可包含**InputClaimsTransformation**元素的集合，这些元素用于修改输入声明，或在发送到一次性密码协议提供程序之前生成新声明。

### <a name="output-claims"></a>输出声明

在此协议提供程序的代码验证过程中未提供任何输出声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="metadata"></a>元数据

以下设置可用于配置在代码验证失败时显示的错误消息：

| Attribute | 需要 | Description |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | 否 | 在代码验证会话过期时向用户显示的消息。 代码已过期，或者从未为给定标识符生成了代码。 |
| UserMessageIfMaxRetryAttempted | 否 | 当用户超过允许的最大验证尝试次数时向用户显示的消息。 |
| UserMessageIfInvalidCode | 否 | 如果用户提供的代码无效，则向用户显示消息。 |

### <a name="returning-error-message"></a>返回错误消息

如[元数据](#metadata)中所述，你可以针对不同的错误情况自定义向用户显示的错误消息。 可以通过对区域设置进行前缀来进一步本地化这些消息，例如：

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>示例

下面的示例 `TechnicalProfile` 用于验证代码：

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```
