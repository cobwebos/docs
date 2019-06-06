---
title: Azure Active Directory B2C 标识体验框架架构的日期声明转换示例 | Microsoft Docs
description: Azure Active Directory B2C 标识体验框架架构的日期声明转换示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ad4c6c78556f98e2905b3583910e498055257c36
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511130"
---
# <a name="date-claims-transformations"></a>日期声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示了在 Azure Active Directory (Azure AD) B2C 中使用标识体验框架架构的日期声明转换的过程。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

检查一个日期和时间声明（字符串数据类型）是否晚于另一个日期和时间声明（字符串数据类型），然后引发异常。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | leftOperand | string | 第一个声明的类型，应晚于第二个声明。 |
| inputClaim | rightOperand | 字符串 | 第二个声明的类型，应早于第一个声明。 |
| InputParameter | AssertIfEqualTo | boolean | 指定如果左操作数等于右操作数，是否应传递此断言。 |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | 指定如果缺少右操作数，是否应传递此断言。 |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | 指定将两个日期时间视为相等时允许两者之间相差的毫秒数（例如，用于说明时钟偏差）。 |

AssertDateTimeIsGreaterThan  声明转换始终从[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)调用。 DateTimeGreaterThan  自断言技术配置文件元数据控制技术配置文件向用户呈现的错误消息。

![AssertStringClaimsAreEqual 执行](./media/date-transformations/assert-execution.png)

下面的示例比较 `currentDateTime` 声明与 `approvedDateTime` 声明。 如果 `currentDateTime` 晚于 `approvedDateTime`，则引发错误。 如果它们的差异在 5 分钟（30000 毫秒）内，则转换将值视为相等。

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

`login-NonInteractive` 验证技术配置文件会调用 `AssertApprovedDateTimeLaterThanCurrentDateTime` 声明转换。
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

自断言技术配置文件调用验证 login-NonInteractive  技术配置文件。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>示例

- 输入声明：
    - leftOperand  ：2018-10-01T15:00:00.0000000Z
    - rightOperand  ：2018-10-01T14:00:00.0000000Z
- 结果：引发错误

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

将 Date  ClaimType 转换为 DateTime  ClaimType。 该声明转换会转换时间格式并向日期添加 12:00:00 AM。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | 要转换的 ClaimType。 |
| OutputClaim | outputClaim | dateTime | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

下面的示例演示声明 `dateOfBirth`（date 数据类型）到另一个声明 `dateOfBirthWithTime`（dateTime 数据类型）的转换。

```XML
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**：2019-06-01
- 输出声明：
    - **outputClaim**：1559347200（2019 年 1 月 1 日上午 12:00:00）

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

获取当前 UTC 日期和时间，并将值添加到 ClaimType。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

* 输出声明：
    * currentDateTime  ：1534418820（2018 年 8 月 16 日上午 11:27:00）

## <a name="datetimecomparison"></a>DateTimeComparison

确定一个日期/时间是晚于、早于还是等于另一个日期/时间。 结果是值为 `true` 或 `false` 的新布尔 ClaimType 布尔值。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | 第一个日期/时间，用于比较它是在第二个日期/时间之前还是之后。 Null 值会引发异常。 |
| InputClaim | secondDateTime | dateTime | 第二个日期/时间，用于比较它是在第一个日期/时间之前还是之后。 NULL 值被视为当前日期/时间。 |
| InputParameter | operator | string | 以下值之一：same、later than 或 earlier than。 |
| InputParameter | timeSpanInSeconds | int | 向第一个日期/时间添加的时间范围。 |
| OutputClaim | 结果 | boolean | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

使用此声明转换可确定两个 ClaimType 之间是相等、晚于还是早于。 例如，可能会存储用户接受服务条款 (TOS) 的上次时间。 3 个月后，可以要求用户再次访问 TOS。
若要运行声明转换，首先需要获取当前日期/时间，以及用户接受 TOS 的上次时间。

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - firstDateTime  ：2018-01-01T00:00:00.100000Z
    - secondDateTime  ：2018-04-01T00:00:00.100000Z
- 输入参数：
    - operator: later than 
    - timeSpanInSeconds  ：7776000（90 天）
- 输出声明：
    - 结果  ：true
