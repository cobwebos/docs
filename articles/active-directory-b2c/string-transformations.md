---
title: Azure Active Directory B2C 标识体验框架架构的 字符串声明转换示例 | Microsoft Docs
description: Azure Active Directory B2C 标识体验框架架构的字符串声明转换示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83379cc194f23ebff977babc7124a7bc90f4bc60
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063442"
---
# <a name="string-claims-transformations"></a>字符串声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C （Azure AD B2C）中使用标识体验框架架构的字符串声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

比较两个声明，如果根据指定的比较 inputClaim1、inputClaim2 和 stringComparison 它们不相等，将引发异常。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | 要比较的第一个声明的类型。 |
| inputClaim | inputClaim2 | string | 要比较的第二个声明的类型。 |
| InputParameter | stringComparison | string | 字符串比较，值为下列其中一项：Ordinal、OrdinalIgnoreCase。 |

AssertStringClaimsAreEqual 声明转换始终从[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)调用。 UserMessageIfClaimsTransformationStringsAreNotEqual 自断言技术配置文件元数据控制向用户显示的错误消息。

![AssertStringClaimsAreEqual 执行](./media/string-transformations/assert-execution.png)

可以使用此声明转换来确保两个 ClaimTypes 具有相同的值。 如果没有，则会引发错误消息。 以下示例检查 strongAuthenticationEmailAddress ClaimType 是否等同于 email ClaimType， 否则会引发错误消息。

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


login-NonInteractive 验证技术配置文件调用 AssertEmailAndStrongAuthenticationEmailAddressAreEqual 声明转换。
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

自断言技术配置文件调用验证 login-NonInteractive 技术配置文件。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>示例

- 输入声明：
  - inputClaim1: someone@contoso.com
  - inputClaim2: someone@outlook.com
    - 输入参数：
  - stringComparison: ordinalIgnoreCase
- 结果：引发错误

## <a name="changecase"></a>ChangeCase

将所提供的声明更改为小写或大写，具体要取决于运算符。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | string | 要更改的 ClaimType。 |
| InputParameter | toCase | string | 以下值之一：`LOWER` 或 `UPPER`。 |
| OutputClaim | outputClaim | string | 调用此声明转换后生成的 ClaimType。 |

使用此声明转换将任何字符串 ClaimType 更改为小写或大写。

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - email: SomeOne@contoso.com
- 输入参数：
    - **toCase**：LOWER
- 输出声明：
  - email: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

基于策略中提供的输入参数创建字符串声明。

| 项 | TransformationClaimType | 数据类型 | 说明 |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | 要设置的字符串 |
| OutputClaim | createdClaim | string | 调用此声明转换后生成的 ClaimType，其值在输入参数中指定。 |

使用此声明转换设置一个字符串 ClaimType 值。

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入参数：
    - **value**：Contoso 服务条款...
- 输出声明：
    - **createdClaim**：TOS ClaimType 包含“Contoso 服务条款...”值。

## <a name="compareclaims"></a>CompareClaims

确定一个字符串声明是否等于另一个字符串声明。 结果是新布尔型 ClaimType，值为 `true` 或 `false`。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | 要比较的第一个声明类型。 |
| inputClaim | inputClaim2 | string | 要比较的第二个声明类型。 |
| InputParameter | 运算符 | string | 可能的值：`EQUAL` 或 `NOT EQUAL`。 |
| InputParameter | ignoreCase | boolean | 指定此比较是否应忽略所比较字符串的大小写。 |
| OutputClaim | outputClaim | boolean | 调用此声明转换后生成的 ClaimType。 |

使用此声明转换检查一个声明是否等于另一个声明。 例如，以下声明转换检查 email 声明的值是否等于 Verified.Email 声明的值。

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - inputClaim1: someone@contoso.com
  - inputClaim2: someone@outlook.com
- 输入参数：
    - **operator**：不等于
    - ignoreCase: true
- 输出声明：
    - outputClaim: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

确定声明值是否等于输入参数值。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | string | 要比较的声明类型。 |
| InputParameter | 运算符 | string | 可能的值：`EQUAL` 或 `NOT EQUAL`。 |
| InputParameter | compareTo | string | 字符串比较，值为下列其中一项：Ordinal、OrdinalIgnoreCase。 |
| InputParameter | ignoreCase | boolean | 指定此比较是否应忽略所比较字符串的大小写。 |
| OutputClaim | outputClaim | boolean | 调用此声明转换后生成的 ClaimType。 |

可以使用此声明转换检查一个声明是否等于指定的值。 例如，以下声明转换将检查 termsOfUseConsentVersion 声明的值是否等于 `v1`。

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例
- 输入声明：
    - inputClaim1: v1
- 输入参数：
    - **compareTo**:V1
    - **operator**：EQUAL
    - ignoreCase:  true
- 输出声明：
    - outputClaim: true

## <a name="createrandomstring"></a>CreateRandomString

使用随机数生成器创建随机字符串。 如果随机数生成器是 `integer` 类型，则可以选择提供种子参数和最大数。 可选字符串格式参数允许使用它来格式化输出，可选的 base64 参数指定输出是否为 base64 编码的 encoded randomGeneratorType [guid, integer] outputClaim（字符串）。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | 指定要生成的随机值，`GUID`（全局唯一 ID）或 `INTEGER`（数字）。 |
| InputParameter | stringFormat | string | [可选]格式化随机值。 |
| InputParameter | base64 | boolean | [可选]将随机值转换为 base64。 如果应用字符串格式，则字符串格式之后的值将被编码为 base64。 |
| InputParameter | maximumNumber | int | [可选]仅限 `INTEGER` randomGeneratorType。 指定最大数。 |
| InputParameter | 种子  | int | [可选]仅限 `INTEGER` randomGeneratorType。 指定随机值的种子。 注意：同一个种子生成相同的随机数字序列。 |
| OutputClaim | outputClaim | string | 调用此声明转换后将生成的 ClaimTypes。 随机值。 |

下面的示例将生成全局唯一 ID。 此声明转换用于创建随机 UPN（用户主体名称）。

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>示例

- 输入参数：
    - **randomGeneratorType**：GUID
- 输出声明：
    - outputClaim: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

下面的示例生成 0 到 1000 之间的随机整数值。 值被格式化为 OTP_{random value}。

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入参数：
    - **randomGeneratorType**：INTEGER
    - **maximumNumber**：1000
    - **stringFormat**：OTP_{0}
    - base64: false
- 输出声明：
    - **outputClaim**：OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

根据提供的格式字符串格式化声明。 此转换将使用 C# `String.Format` 方法。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string |作为字符串格式 {0} 参数的 ClaimType。 |
| InputParameter | stringFormat | string | 字符串格式，包括 {0} 参数。 |
| OutputClaim | outputClaim | string | 调用此声明转换后生成的 ClaimType。 |

使用此声明转换格式化任何带一个参数 {0} 的字符串。 以下示例创建一个 userPrincipalName。 所有社交标识提供者技术配置文件（如 `Facebook-OAUTH`）都调用 CreateUserPrincipalName 来生成 userPrincipalName。

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**：5164db16-3eee-4629-bfda-dcc3326790e9
- 输入参数：
    - stringFormat:  cpim_{0}@{RelyingPartyTenantId}
- 输出声明：
  - outputClaim: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

根据提供的格式字符串格式化两个声明。 此转换将使用 C# String.Format 方法。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |string | 作为字符串格式 {0} 参数的 ClaimType。 |
| InputClaim | inputClaim | string | 作为字符串格式 {1} 参数的 ClaimType。 |
| InputParameter | stringFormat | string | 字符串格式，包括 {0} 和 {1} 参数。 |
| OutputClaim | outputClaim | string | 调用此声明转换后生成的 ClaimType。 |

使用此声明转换格式化任何带两个参数 {0} 和 {1} 的字符串。 下面的示例创建带指定格式的 displayName：

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim1**：Joe
    - **inputClaim2**：Fernando
- 输入参数：
    - stringFormat: {0} {1}
- 输出声明：
    - **outputClaim**：Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

从声明 Restriction 集合中查找项。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | string | 该声明包含要在带 Restriction 集合的 restrictionValueClaim 声明中查找的文本。  |
| OutputClaim | restrictionValueClaim | string | 包含 Restriction 集合的声明。 在调用声明转换后，此声明的值将包含选定项的值。 |

下面的示例基于错误密钥查找错误消息描述。 ResponseMsg 声明包含一系列要显示给最终用户或发送给信赖方的错误消息。

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
声明转换将查找项文本，并返回其值。 如果使用 `<LocalizedCollection>` 本地化限制，则声明转换返回本地化的值。

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **mapFromClaim**：B2C_V1_90001
- 输出声明：
    - **restrictionValueClaim**：无法登录，因为你未成年。

## <a name="lookupvalue"></a>LookupValue

基于另一个声明的值从值列表中查找声明值。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | string | 包含查找值的声明 |
| InputParameter | |string | inputParameters 集合。 |
| InputParameter | errorOnFailedLookup | boolean | 控制在没有任何匹配查找时是否返回错误。 |
| OutputClaim | inputParameterId | string | 调用此声明转换后将生成的 ClaimTypes。 匹配 ID 的值。 |

下面的示例在某一个 inputParameters 集合中查找域名。 声明转换查找标识符中的域名，并返回其值（应用程序 ID）。

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - inputParameterId: test.com
- 输入参数：
    - **contoso.com**：13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**：0213308f-17cb-4398-b97e-01da7bd4804e
    - test.com: c7026f88-4299-4cdb-965d-3f166464b8a9
    - errorOnFailedLookup: false
- 输出声明：
    - outputClaim:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

清除给定声明的值。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | string | 其值为 NULL 的声明。 |

使用此声明转换可从声明属性包中删除不必要的数据。 因此，会话 cookie 会比较小。 以下示例将删除 `TermsOfService` 声明类型的值。

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- 输入声明：
    - **outputClaim**：欢迎使用 Contoso 应用。 如果继续浏览和使用本网站，表示你同意遵守并受下列条款和条件的约束...
- 输出声明：
    - **outputClaim**：NULL

## <a name="parsedomain"></a>ParseDomain

获取电子邮件地址的域部分。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | string | 包含电子邮件地址的 ClaimType。 |
| OutputClaim | 域 | string | 调用此声明转换后生成的 ClaimType - 域。 |

使用此声明转换分析用户 @ 符号之后的域名。 这可以帮助从审核数据中删除个人身份信息 (PII)。 以下声明转换演示如何分析 email 声明中的域名。

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - emailAddress: joe@outlook.com
- 输出声明：
    - domain: outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

检查字符串声明和 `matchTo` 输入参数是否相等，并使用 `stringMatchMsg` 和 `stringMatchMsgCode` 输入参数中提供的值设置输出声明，以及比较结果输出声明，将基于比较结果将此声明设置为 `true` 或 `false`。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | string | 要比较的声明类型。 |
| InputParameter | matchTo | string | 要与 `inputClaim` 进行比较的字符串。 |
| InputParameter | stringComparison | string | 可能的值：`Ordinal` 或 `OrdinalIgnoreCase`。 |
| InputParameter | stringMatchMsg | string | 字符串相等情况下第一个要设置的值。 |
| InputParameter | stringMatchMsgCode | string | 字符串相等情况下第二个要设置的值。 |
| OutputClaim | outputClaim1 | string | 如果字符串相等，则此输出声明包含 `stringMatchMsg` 输入参数的值。 |
| OutputClaim | outputClaim2 | string | 如果字符串相等，则此输出声明包含 `stringMatchMsgCode` 输入参数的值。 |
| OutputClaim | stringCompareResultClaim | boolean | 比较结果输出声明类型，将基于比较结果设置为 `true` 或 `false`。 |

可以使用此声明转换检查一个声明是否等于指定的值。 例如，以下声明转换将检查 termsOfUseConsentVersion 声明的值是否等于 `v1`。 如果是，则将值更改为 `v2`。

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>示例

- 输入声明：
    - inputClaim: v1
- 输入参数：
    - **matchTo**：V1
    - stringComparison: ordinalIgnoreCase
    - **stringMatchMsg**：B2C_V1_90005
    - **stringMatchMsgCode**：TOS 升级到 v2
- 输出声明：
    - **outputClaim1**：B2C_V1_90005
    - **outputClaim2**：TOS 升级到 v2
    - stringCompareResultClaim: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

检查字符串声明和 `matchTo` 输入参数是否相等，并使用 `outputClaimIfMatched` 输入参数中提供的值设置输出声明，以及比较结果输出声明，将基于比较结果将此声明设置为 `true` 或 `false`。

| 项 | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | string | 要比较的声明类型。 |
| InputParameter | matchTo | string | 要与 inputClaim 进行比较的字符串。 |
| InputParameter | stringComparison | string | 可能的值：`Ordinal` 或 `OrdinalIgnoreCase`。 |
| InputParameter | outputClaimIfMatched | string | 字符串相等情况下要设置的值。 |
| OutputClaim | outputClaim | string | 如果字符串相等，则此输出声明包含 `outputClaimIfMatched` 输入参数的值。 或者如果字符串不匹配，则为 NULL。 |
| OutputClaim | stringCompareResultClaim | boolean | 比较结果输出声明类型，将基于比较结果设置为 `true` 或 `false`。 |

例如，以下声明转换检查 ageGroup 声明的值是否等于 `Minor`。 如果是，则返回 `B2C_V1_90001` 值。

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **claimToMatch**：Minor
- 输入参数：
    - **matchTo**：Minor
    - stringComparison: ordinalIgnoreCase
    - **outputClaimIfMatched**：B2C_V1_90001
- 输出声明：
    - **isMinorResponseCode**：B2C_V1_90001
    - isMinor: true

