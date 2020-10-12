---
title: 自定义策略中的电话号码声明转换
titleSuffix: Azure AD B2C
description: 针对 Azure AD B2C 中电话号码声明转换的自定义策略参考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e175a81efc1ab0950c1fda314efb206ff97a2b7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385376"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>定义 Azure AD B2C 中的电话号码声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C (Azure AD B2C) 中使用 Identity Experience Framework 架构的电话号码声明转换的参考和示例。 有关一般声明转换的详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

将 `phoneNumber` 数据类型转换为 `string` 数据类型。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  要转换为字符串的 ClaimType。 |
| OutputClaim | phoneNumberString | 字符串 | 调用此声明转换后生成的 ClaimType。 |

在此示例中，值类型为 `phoneNumber` 的 cellPhoneNumber 声明将转换为值类型为 `string` 的 cellPhone 声明。

```xml
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **phoneNumber**：+11234567890 (phoneNumber)
- 输出声明：
  - **phoneNumberString**：+11234567890 (string)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

此声明转换验证电话号码的格式。 如果它是有效的格式，请将其更改为 Azure AD B2C 使用的标准格式。 如果提供的电话号码的格式无效，则返回一条错误消息。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | 字符串 |  电话号码的字符串声明。 电话号码必须采用国际格式，以“+”和国家/地区代码开头。 如果提供了输入声明 `country`，则电话号码采用本地格式（无国家/地区代码）。 |
| InputClaim | country | 字符串 | [可选] 电话号码的国家/地区代码的字符串声明采用 ISO3166 格式（由两个字母组成的 ISO-3166 国家/地区代码）。 |
| OutputClaim | outputClaim | phoneNumber | 此声明转换的结果。 |

ConvertStringToPhoneNumberClaim 声明转换始终从[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)或[显示控件](display-controls.md)调用。 UserMessageIfClaimsTransformationInvalidPhoneNumber 自断言技术配置文件元数据控制向用户显示的错误消息。

![错误消息执行路径示意图](./media/phone-authentication/assert-execution.png)

可以使用此声明转换来确保所提供的字符串声明是有效的电话号码。 如果没有，则会引发错误消息。 下面的示例检查 phoneString ClaimType 是否确实是有效的电话号码，然后返回 Azure AD B2C 标准格式的电话号码。 否则会引发错误消息。

```xml
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

调用包含此声明转换的验证技术配置文件的自断言技术配置文件可以定义错误消息。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>示例 1

- 输入声明：
  - **phoneNumberString**：033 456-7890
  - **country**：DK
- 输出声明：
  - **outputClaim**：+450334567890

### <a name="example-2"></a>示例 2

- 输入声明：
  - **phoneNumberString**：+1 (123) 456-7890
- 输出声明：
  - **outputClaim**：+11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

这会提取输入声明中的国家/地区代码和国家/地区号码，如果提供的电话号码无效，则可根据需要引发异常。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | 字符串 | 电话号码的字符串声明。 电话号码必须采用国际格式，以“+”和国家/地区代码开头。 |
| InputParameter | throwExceptionOnFailure | boolean | [可选] 一个参数，指示电话号码无效时是否引发异常。 默认值为 false。 |
| InputParameter | countryCodeType | 字符串 | [可选] 一个参数，指示输出声明中国家/地区代码的类型。 可用值是 CallingCode（国家/地区的国际呼叫代码，例如 +1）或 ISO3166（由两个字母组成的 ISO-3166 国家/地区代码） 。 |
| OutputClaim | nationalNumber | 字符串 | 电话号码的国家/地区号码的字符串声明。 |
| OutputClaim | countryCode | 字符串 | 电话号码的国家/地区代码的字符串声明。 |


如果 GetNationalNumberAndCountryCodeFromPhoneNumberString 声明转换从[验证技术配置文件](validation-technical-profile.md)执行，该文件由[自断言技术配置文件](self-asserted-technical-profile.md)或[显示控件](display-controls.md#display-control-actions)调用，则 UserMessageIfPhoneNumberParseFailure 自断言技术配置文件元数据控制向用户显示的错误消息 。

![错误消息执行路径示意图](./media/phone-authentication/assert-execution.png)

可以使用此声明转换将完整的电话号码拆分为国家/地区代码和国家/地区号码。 如果提供的电话号码无效，则可以选择引发错误消息。

下面的示例尝试将电话号码拆分为国家/地区号码和国家/地区代码。 如果电话号码有效，则国家/地区号码将替换电话号码。 如果电话号码无效，则不会引发异常，并且电话号码仍具有其原始值。

```xml
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

调用包含此声明转换的验证技术配置文件的自断言技术配置文件可以定义错误消息。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>示例 1

- 输入声明：
  - **phoneNumber**：+49 (123) 456-7890
- 输入参数：
  - **throwExceptionOnFailure**：false
  - **countryCodeType**：ISO3166
- 输出声明：
  - **nationalNumber**：1234567890
  - **countryCode**：DE

### <a name="example-2"></a>示例 2

- 输入声明：
  - **phoneNumber**：+49 (123) 456-7890
- 输入参数
  - **throwExceptionOnFailure**：false
  - **countryCodeType**：CallingCode
- 输出声明：
  - **nationalNumber**：1234567890
  - **countryCode**：+49
