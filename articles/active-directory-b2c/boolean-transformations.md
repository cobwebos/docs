---
title: Azure Active Directory B2C 标识体验框架架构的布尔型声明转换示例 | Microsoft Docs
description: Azure Active Directory B2C 标识体验框架架构的布尔型声明转换示例。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
origin.date: 09/10/2018
ms.date: 04/03/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: fea0f8c2c2bcab94202916594e66514f2ec87396
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60314612"
---
# <a name="boolean-claims-transformations"></a>布尔型声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示了在 Azure Active Directory (Azure AD) B2C 中使用标识体验框架架构的布尔型声明转换的过程。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="andclaims"></a>AndClaims

执行两个布尔型 inputClaims 的 And 运算并使用运算结果设置 outputClaim。

| Item  | TransformationClaimType  | 数据类型  | 说明 |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | 第一个要评估的 ClaimType。 |
| InputClaim | inputClaim2  | boolean | 第二个要评估的 ClaimType。 |
|OutputClaim | outputClaim | boolean | 调用此声明转换后将生成的 ClaimTypes（true 或 false）。 |

以下声明转换演示如何执行两个布尔型 ClaimTypes 的 And 运算：`isEmailNotExist` 和 `isSocialAccount`。 如果这两个输入声明的值为 `true`，则输出声明 `presentEmailSelfAsserted` 设置为 `true`。 在业务流程步骤中，只有在社交帐户电子邮件为空的情况下，才可以使用前置条件来预设自断言页。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim1**: true
    - **inputClaim2**: false
- 输出声明：
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

检查两个声明的布尔值是否相等，如果不等，将引发异常。

| Item | TransformationClaimType  | 数据类型  | 说明 |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | 要断言的 ClaimType。 |
| InputParameter |valueToCompareTo | boolean | 要比较的值（true 或 false）。 |

AssertBooleanClaimIsEqualToValue 声明转换始终从[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)调用。 UserMessageIfClaimsTransformationBooleanValueIsNotEqual 自断言技术配置文件元数据控制向用户显示的技术配置文件。

![AssertStringClaimsAreEqual 执行](./media/boolean-transformations/assert-execution.png)

以下声明转换演示如何检查带 `true` 值的布尔型 ClaimType 的值。 如果 `accountEnabled` ClaimType 的值为 false，则会引发错误消息。

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


`login-NonInteractive` 验证技术配置文件会调用 `AssertAccountEnabledIsTrue` 声明转换。
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

自断言技术配置文件调用验证 login-NonInteractive 技术配置文件。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**: false
    - **valueToCompareTo**: true
- 结果：引发错误

## <a name="notclaims"></a>NotClaims

执行布尔型 inputClaim 的 Not 运算并使用运算结果设置 outputClaim。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | 要运算的声明。 |
| OutputClaim | outputClaim | boolean | 调用此 ClaimsTransformation 后生成的 ClaimType（true 或 false）。 |

使用此声明转换对声明执行逻辑非运算。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**: false
- 输出声明：
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims 

执行两个布尔型 inputClaims 的 Or 运算并使用运算结果设置 outputClaim。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | 第一个要评估的 ClaimType。 |
| InputClaim | inputClaim2 | boolean | 第二个要评估的 ClaimType。 |
| OutputClaim | outputClaim | boolean | 调用此 ClaimsTransformation 后将生成的 ClaimTypes（true 或 false）。 |

以下声明转换演示如何执行两个布尔型 ClaimTypes 的 `Or` 运算。 在业务流程步骤中，如果其中一个声明的值为 `true`，则可以使用前置条件来预设自断言页。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim1**: true
    - **inputClaim2**: false
- 输出声明：
    - **outputClaim**: true


