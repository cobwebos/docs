---
title: 自定义策略的字符串声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的标识体验框架（IEF）架构的字符串声明转换示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4a5d0908842c20e15fdf7b336b9e244c4bafb345
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78397790"
---
# <a name="string-claims-transformations"></a>字符串声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C （Azure AD B2C）中使用标识体验框架架构的字符串声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

比较两个声明，如果根据指定的比较 inputClaim1、inputClaim2 和 stringComparison 它们不相等，将引发异常。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字符串 | 要比较的第一个声明的类型。 |
| InputClaim | inputClaim2 | 字符串 | 要比较的第二个声明的类型。 |
| InputParameter | stringComparison | 字符串 | 字符串比较，其中一个值：Ordinal、OrdinalIgnoreCase。 |

**AssertStringClaimsAreEqual**声明转换始终从由[自断言技术配置文件](self-asserted-technical-profile.md)或[DisplayConrtol](display-controls.md)调用的[验证技术配置文件](validation-technical-profile.md)执行。 自断言技术配置文件的 `UserMessageIfClaimsTransformationStringsAreNotEqual` 元数据控制向用户显示的错误消息。


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

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字符串 | 要更改的 ClaimType。 |
| InputParameter | toCase | 字符串 | 以下值之一：`LOWER` 或 `UPPER`。 |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后生成的 ClaimType。 |

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
    - toCase: LOWER
- 输出声明：
  - email: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

从转换中提供的输入参数创建一个字符串声明。

| Item | TransformationClaimType | 数据类型 | 说明 |
|----- | ----------------------- | --------- | ----- |
| InputParameter | 值 | 字符串 | 要设置的字符串。 此输入参数支持[字符串声明转换表达式](string-transformations.md#string-claim-transformations-expressions)。 |
| OutputClaim | createdClaim | 字符串 | 调用此声明转换后生成的 ClaimType，其值在输入参数中指定。 |

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
    - value：Contoso 服务条款...
- 输出声明：
    - createdClaim：TOS ClaimType 包含“Contoso 服务条款...”值。

## <a name="compareclaims"></a>CompareClaims

确定一个字符串声明是否等于另一个字符串声明。 结果是新布尔型 ClaimType，值为 `true` 或 `false`。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字符串 | 要比较的第一个声明类型。 |
| InputClaim | inputClaim2 | 字符串 | 要比较的第二个声明类型。 |
| InputParameter | 运算符后的表达式 | 字符串 | 可能的值：`EQUAL` 或 `NOT EQUAL`。 |
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
    - operator:  NOT EQUAL
    - ignoreCase: true
- 输出声明：
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

确定声明值是否等于输入参数值。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字符串 | 要比较的声明类型。 |
| InputParameter | 运算符后的表达式 | 字符串 | 可能的值：`EQUAL` 或 `NOT EQUAL`。 |
| InputParameter | compareTo | 字符串 | 字符串比较，其中一个值：Ordinal、OrdinalIgnoreCase。 |
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
    - compareTo: V1
    - operator: EQUAL
    - ignoreCase:  true
- 输出声明：
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

使用随机数生成器创建随机字符串。 如果随机数生成器是 `integer` 类型，则可以选择提供种子参数和最大数。 可选字符串格式参数允许使用它来格式化输出，可选的 base64 参数指定输出是否为 base64 编码的 encoded randomGeneratorType [guid, integer] outputClaim（字符串）。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | 字符串 | 指定要生成的随机值，`GUID`（全局唯一 ID）或 `INTEGER`（数字）。 |
| InputParameter | stringFormat | 字符串 | [可选]格式化随机值。 |
| InputParameter | base64 | boolean | [可选]将随机值转换为 base64。 如果应用字符串格式，则字符串格式之后的值将被编码为 base64。 |
| InputParameter | maximumNumber | int | [可选]仅限 `INTEGER` randomGeneratorType。 指定最大数。 |
| InputParameter | seed  | int | [可选]仅限 `INTEGER` randomGeneratorType。 指定随机值的种子。 注意：同一个种子生成相同的随机数字序列。 |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后将生成的 ClaimTypes。 随机值。 |

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
    - randomGeneratorType: GUID
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
    - randomGeneratorType: INTEGER
    - maximumNumber: 1000
    - stringFormat: OTP_{0}
    - base64: false
- 输出声明：
    - outputClaim: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

根据提供的格式字符串格式化声明。 此转换将使用 C# `String.Format` 方法。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |字符串 |作为字符串格式 {0} 参数的 ClaimType。 |
| InputParameter | stringFormat | 字符串 | 字符串格式，包括 {0} 参数。 此输入参数支持[字符串声明转换表达式](string-transformations.md#string-claim-transformations-expressions)。  |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后生成的 ClaimType。 |

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
    - inputClaim: 5164db16-3eee-4629-bfda-dcc3326790e9
- 输入参数：
    - stringFormat:  cpim_{0}@{RelyingPartyTenantId}
- 输出声明：
  - outputClaim: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

根据提供的格式字符串格式化两个声明。 此转换将使用 C# `String.Format` 方法。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |字符串 | 作为字符串格式 {0} 参数的 ClaimType。 |
| InputClaim | inputClaim | 字符串 | 作为字符串格式 {1} 参数的 ClaimType。 |
| InputParameter | stringFormat | 字符串 | 字符串格式，包括 {0} 和 {1} 参数。 此输入参数支持[字符串声明转换表达式](string-transformations.md#string-claim-transformations-expressions)。   |
| OutputClaim | outputClaim | 字符串 | 调用此声明转换后生成的 ClaimType。 |

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
    - inputClaim1: Joe
    - inputClaim2: Fernando
- 输入参数：
    - **stringFormat**： {0} {1}
- 输出声明：
    - outputClaim: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

将本地化的字符串复制到声明。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | 本地化字符串的名称 | 字符串 | 在调用此声明转换之后生成的声明类型的列表。 |

使用 GetLocalizedStringsTransformation 声明转换：

1. 定义一个[本地化字符串](localization.md)，并将其与[自断言技术配置文件](self-asserted-technical-profile.md)相关联。
1. `LocalizedString` 元素的 `ElementType` 必须设置为 `GetLocalizedStringsTransformationClaimType`。
1. `StringId` 是你定义的唯一标识符，稍后在声明转换中使用它。
1. 在声明转换中，指定要用本地化字符串设置的声明的列表。 `ClaimTypeReferenceId` 是对已在策略的 ClaimsSchema 部分中定义的 ClaimType 的引用。 `TransformationClaimType` 是在 `LocalizedString` 元素的 `StringId` 中定义的本地化字符串的名称。
1. 在[自断言技术配置文件](self-asserted-technical-profile.md)或[显示控件](display-controls.md)输入或输出声明转换中，对声明转换进行引用。

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

下面的示例从本地化的字符串查找电子邮件主题、正文、代码消息和电子邮件的签名。 以后自定义电子邮件验证模板使用这些声明。

定义英语（默认）和西班牙语的本地化字符串。

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

声明转换使用*email_subject*的 `StringId` 值设置*声明类型的*值。

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输出声明：
  - **subject**： Contoso 帐户电子邮件验证码
  - **消息**：感谢验证你的帐户！
  - **codeIntro**：你的代码是
  - **签名**：此致


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

从声明 Restriction 集合中查找项。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | 字符串 | 该声明包含要在带 Restriction 集合的 restrictionValueClaim 声明中查找的文本。  |
| OutputClaim | restrictionValueClaim | 字符串 | 包含 Restriction 集合的声明。 在调用声明转换后，此声明的值将包含选定项的值。 |

下面的示例基于错误密钥查找错误消息描述。 ResponseMsg 声明包含一系列要显示给最终用户或发送给信赖方的错误消息。

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
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
    - mapFromClaim: B2C_V1_90001
- 输出声明：
    - **restrictionValueClaim**：你无法登录，因为你是个小版本。

## <a name="lookupvalue"></a>LookupValue

基于另一个声明的值从值列表中查找声明值。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | 字符串 | 包含查找值的声明 |
| InputParameter | |字符串 | inputParameters 集合。 |
| InputParameter | errorOnFailedLookup | boolean | 控制在没有任何匹配查找时是否返回错误。 |
| OutputClaim | inputParameterId | 字符串 | 调用此声明转换后将生成的 ClaimTypes。 匹配 `Id`的值。 |

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
    - contoso.com: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - microsoft.com: 0213308f-17cb-4398-b97e-01da7bd4804e
    - test.com: c7026f88-4299-4cdb-965d-3f166464b8a9
    - errorOnFailedLookup: false
- 输出声明：
    - outputClaim:  c7026f88-4299-4cdb-965d-3f166464b8a9

当 `errorOnFailedLookup` 输入参数设置为 `true`时，始终从由[自断言技术配置文件](self-asserted-technical-profile.md)或[DisplayConrtol](display-controls.md)调用的[验证技术配置文件](validation-technical-profile.md)执行**LookupValue**声明转换。 自断言技术配置文件的 `LookupNotFound` 元数据控制向用户显示的错误消息。

![AssertStringClaimsAreEqual 执行](./media/string-transformations/assert-execution.png)

下面的示例在某一个 inputParameters 集合中查找域名。 声明转换查找标识符中的域名，并返回其值（应用程序 ID），或引发错误消息。

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputParameterId**： live.com
- 输入参数：
    - contoso.com: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - microsoft.com: 0213308f-17cb-4398-b97e-01da7bd4804e
    - test.com: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**： true
- 错误：
    - 在输入参数 id 列表中找不到输入声明值的匹配项，errorOnFailedLookup 为 true。


## <a name="nullclaim"></a>NullClaim

清除给定声明的值。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | 字符串 | 声明的值设置为 NULL。 |

使用此声明转换从声明属性包中删除不必要的数据，以便会话 cookie 更小。 以下示例将删除 `TermsOfService` 声明类型的值。

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- 输入声明：
    - outputClaim：欢迎使用 Contoso 应用。 如果继续浏览和使用本网站，表示你同意遵守并受下列条款和条件的约束...
- 输出声明：
    - outputClaim: NULL

## <a name="parsedomain"></a>ParseDomain

获取电子邮件地址的域部分。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | 字符串 | 包含电子邮件地址的 ClaimType。 |
| OutputClaim | 域 | 字符串 | 调用此声明转换后生成的 ClaimType - 域。 |

使用此声明转换分析用户 @ 符号之后的域名。 以下声明转换演示如何分析 email 声明中的域名。

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

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

检查 `claimToMatch` 和 `matchTo` 输入参数的字符串声明是否相等，并使用 `outputClaimIfMatched` input 参数中存在的值来设置输出声明，并使用比较结果输出声明，该声明根据比较结果设置为 `true` 或 `false`。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | 字符串 | 要比较的声明类型。 |
| InputParameter | matchTo | 字符串 | 要匹配的正则表达式。 |
| InputParameter | outputClaimIfMatched | 字符串 | 字符串相等情况下要设置的值。 |
| OutputClaim | outputClaim | 字符串 | 如果正则表达式匹配，此输出声明将包含 `outputClaimIfMatched` 输入参数的值。 如果没有匹配项，则为 null。 |
| OutputClaim | regexCompareResultClaim | boolean | 正则表达式匹配结果输出声明类型，该类型根据匹配结果设置为 `true` 或 `false`。 |

例如，根据电话号码正则表达式模式检查提供的电话号码是否有效。

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="setClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **claimToMatch**： "64854114520"
- 输入参数：
    - **matchTo**： "^ [0-9]{4,16}$"
    - **outputClaimIfMatched**： "isPhone"
- 输出声明：
    - **outputClaim**： "isPhone"
    - **regexCompareResultClaim**： true

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

检查字符串声明和 `matchTo` 输入参数是否相等，并使用 `stringMatchMsg` 和 `stringMatchMsgCode` 输入参数中提供的值设置输出声明，以及比较结果输出声明，将基于比较结果将此声明设置为 `true` 或 `false`。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 要比较的声明类型。 |
| InputParameter | matchTo | 字符串 | 要与 `inputClaim` 进行比较的字符串。 |
| InputParameter | stringComparison | 字符串 | 可能的值：`Ordinal` 或 `OrdinalIgnoreCase`。 |
| InputParameter | stringMatchMsg | 字符串 | 字符串相等情况下第一个要设置的值。 |
| InputParameter | stringMatchMsgCode | 字符串 | 字符串相等情况下第二个要设置的值。 |
| OutputClaim | outputClaim1 | 字符串 | 如果字符串相等，则此输出声明包含 `stringMatchMsg` 输入参数的值。 |
| OutputClaim | outputClaim2 | 字符串 | 如果字符串相等，则此输出声明包含 `stringMatchMsgCode` 输入参数的值。 |
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
    - matchTo: V1
    - stringComparison: ordinalIgnoreCase
    - stringMatchMsg:  B2C_V1_90005
    - stringMatchMsgCode：TOS 升级到 v2
- 输出声明：
    - outputClaim1: B2C_V1_90005
    - outputClaim2：TOS 升级到 v2
    - stringCompareResultClaim: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

检查字符串声明和 `matchTo` 输入参数是否相等，并使用 `outputClaimIfMatched` 输入参数中提供的值设置输出声明，以及比较结果输出声明，将基于比较结果将此声明设置为 `true` 或 `false`。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | 字符串 | 要比较的声明类型。 |
| InputParameter | matchTo | 字符串 | 要与 inputClaim 进行比较的字符串。 |
| InputParameter | stringComparison | 字符串 | 可能的值：`Ordinal` 或 `OrdinalIgnoreCase`。 |
| InputParameter | outputClaimIfMatched | 字符串 | 字符串相等情况下要设置的值。 |
| OutputClaim | outputClaim | 字符串 | 如果字符串相等，则此输出声明包含 `outputClaimIfMatched` 输入参数的值。 或者如果字符串不匹配，则为 NULL。 |
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
    - claimToMatch: Minor
- 输入参数：
    - matchTo: Minor
    - stringComparison: ordinalIgnoreCase
    - outputClaimIfMatched:  B2C_V1_90001
- 输出声明：
    - isMinorResponseCode: B2C_V1_90001
    - isMinor: true


## <a name="stringcontains"></a>StringContains

确定指定的子字符串是否出现在输入声明中。 结果是新布尔型 ClaimType，值为 `true` 或 `false`。 如果值参数出现在此字符串中，则 `true`; 否则 `false`。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 要搜索的声明类型。 |
|InputParameter|contains|字符串|要搜索的值。|
|InputParameter|ignoreCase|字符串|指定此比较是否应忽略所比较的字符串的大小写。|
| OutputClaim | outputClaim | 字符串 | 调用此 ClaimsTransformation 后生成的 ClaimType。 如果子字符串出现在输入声明中，则为布尔型指示器。 |

使用此声明转换来检查字符串声明类型是否包含子字符串。 下面的示例检查 `roles` 字符串声明类型是否包含**admin**的值。

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**： "管理员、审批者、编辑器"
- 输入参数：
    - **包含**： "admin"
    - ignoreCase: true
- 输出声明：
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

从位于指定位置处的字符开始，提取字符串声明类型的各个部分，并返回指定数目的字符。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 声明类型，它包含字符串。 |
| InputParameter | startIndex | int | 此实例中子字符串的从零开始的起始字符位置。 |
| InputParameter | 长度 | int | 子字符串中的字符数。 |
| OutputClaim | outputClaim | boolean | 一个字符串，该字符串等效于此实例中以 startIndex 开始的长度的子字符串; 如果 startIndex 等于此实例的长度并且 length 为零，则为空。 |

例如，获取电话号码的国家/地区前缀。


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**： "+ 1644114520"
- 输入参数：
    - **startIndex**：0
    - **长度**：2
- 输出声明：
    - **outputClaim**： "+ 1"

## <a name="stringreplace"></a>StringReplace

在声明类型字符串中搜索指定的值，并返回一个新的声明类型字符串，其中，当前字符串中出现的所有指定字符串都替换为另一个指定的字符串。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 声明类型，它包含字符串。 |
| InputParameter | oldValue | 字符串 | 要搜索的字符串。 |
| InputParameter | newValue | 字符串 | 用于替换所有出现的 `oldValue` 的字符串 |
| OutputClaim | outputClaim | boolean | 一个字符串，该字符串等效于当前字符串，只不过 oldValue 的所有实例都将替换为 newValue。 如果在当前实例中找不到 oldValue，则方法返回未更改的当前实例。 |

例如，通过删除 `-` 字符来标准化电话号码


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**： "+ 164-411-452-054"
- 输入参数：
    - **oldValue**： "-"
    - **长度**： ""
- 输出声明：
    - **outputClaim**： "+ 164411452054"

## <a name="stringjoin"></a>StringJoin

通过在每个元素或成员之间使用指定的分隔符，连接指定字符串集合声明类型的元素。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | 一个包含要串联的字符串的集合。 |
| InputParameter | delimiter | 字符串 | 要用作分隔符的字符串，如逗号 `,`。 |
| OutputClaim | outputClaim | 字符串 | 由 `inputClaim` 字符串集合的成员组成的字符串，由 `delimiter` 输入参数分隔。 |

下面的示例使用用户角色的字符串集合，并将其转换为逗号分隔符字符串。 您可以使用此方法在 Azure AD 用户帐户中存储字符串集合。 稍后，当你从目录中读取帐户时，使用 `StringSplit` 将逗号分隔符字符串转换回字符串集合。

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **inputClaim**： ["Admin"、"Author"、"Reader"]
- 输入参数：
  - **分隔符**： "，"
- 输出声明：
  - **outputClaim**： "Admin，Author，Reader"


## <a name="stringsplit"></a>StringSplit

返回一个字符串数组，该字符串数组包含此实例中的子字符串（由指定字符串的元素分隔）。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字符串 | 一个字符串声明类型，其中包含要拆分的子字符串。 |
| InputParameter | delimiter | 字符串 | 要用作分隔符的字符串，如逗号 `,`。 |
| OutputClaim | outputClaim | stringCollection | 一个字符串集合，其元素包含此字符串中的子字符串，这些子字符串由 `delimiter` 输入参数分隔。 |

下面的示例使用用户角色的逗号分隔符字符串，并将其转换为字符串集合。

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **inputClaim**： "Admin，Author，Reader"
- 输入参数：
  - **分隔符**： "，"
- 输出声明：
  - **outputClaim**： ["Admin"、"Author"、"Reader"]

## <a name="string-claim-transformations-expressions"></a>字符串声明转换表达式
Azure AD B2C 自定义策略中的声明转换表达式提供了有关租户 ID 和技术配置文件 ID 的上下文信息。

  | 表达式 | 说明 | 示例 |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | 技术配置文件 id 名称。 | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | 信赖方策略的租户 ID。 | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | 信任框架的租户 ID。 | your-tenant.onmicrosoft.com |
