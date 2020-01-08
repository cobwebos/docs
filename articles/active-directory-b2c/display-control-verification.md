---
title: 用显示控件验证声明
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure AD B2C 显示控件验证自定义策略提供的用户旅程中的声明。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4d7deb02e28734eb2b4c42bad566fdfd0d380682
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479080"
---
# <a name="verification-display-control"></a>验证显示控件

使用验证[显示控件](display-controls.md)验证声明（例如电子邮件地址或电话号码），并将验证代码发送给用户。

## <a name="verificationcontrol-actions"></a>VerificationControl 操作

验证显示控制包含两个步骤（操作）：

1. 请求用户的目标，如电子邮件地址或电话号码，验证代码应发送到该目标。 当用户选择 "**发送代码**" 按钮时，将执行验证显示控件的**SendCode 操作**。 **SendCode 操作**生成代码，构造要发送的内容，然后将其发送给用户。 可以预先填充地址的值并将其用作第二重身份验证。

    ![发送代码操作的示例页](media/display-control-verification/display-control-verification-email-action-01.png)

1. 发送代码后，用户将读取该消息，将验证代码输入到显示控件提供的控件中，然后选择 "**验证代码**"。 通过选择 "**验证代码**"，可执行**VerifyCode 操作**来验证与该地址关联的代码。 如果用户选择 "**发送新代码**"，则会再次执行第一个操作。

    ![验证代码操作的示例页](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificationControl 必需的元素

**VerificationControl**必须包含以下元素：

- `DisplayControl` 的类型 `VerificationControl`。
- `DisplayClaims`
  - **发送到**-一个或多个声明，指定将验证代码发送到的位置。 例如，*电子邮件*或*国家/地区代码*和*电话号码*。
  - **验证码**-发送代码后用户提供的验证码声明。 此声明必须设置为必需，并且 `ControlClaimType` 必须设置为 `VerificationCode`。
- 用户完成验证过程后要返回到自断言页面的输出声明（可选）。 例如，*电子邮件*或*国家/地区代码*和*电话号码*。 自断言技术配置文件使用声明来持久保存数据，或将输出声明向上冒泡到下一个业务流程步骤。
- 具有以下名称的两个 `Action`：
  - **SendCode** -向用户发送代码。 此操作通常包含两个验证技术配置文件，用于生成代码并发送代码。
  - **VerifyCode** -验证代码。 此操作通常包含单一验证技术配置文件。

在下面的示例中，页面上将显示**电子邮件**文本框。 用户输入其电子邮件地址并选择**SendCode**时，会在 Azure AD B2C 后端触发**SendCode**操作。

然后，用户输入**verificationCode** ，并选择**VerifyCode** ，以在后端触发**VerifyCode**操作。 如果所有验证均通过，则**VerificationControl**被视为已完成，用户可以继续下一步。

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
