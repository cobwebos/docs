---
title: 自定义策略中的电话号码声明转换
titleSuffix: Azure AD B2C
description: Azure AD B2C 中电话号码声明转换的自定义策略参考。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 34a6d15090cd13a775ad3faa694718ec58738471
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620631"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>在 Azure AD B2C 中定义电话号码声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C （Azure AD B2C）中使用标识体验框架架构的电话号码声明转换的参考和示例。 有关一般声明转换的详细信息，请参阅[ClaimsTransformations](claimstransformations.md)。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

将 `phoneNumber` 的数据类型转换为 `string` 的数据类型。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  要转换为字符串的 ClaimType。 |
| OutputClaim | phoneNumberString | 字符串 | 调用此声明转换后生成的 ClaimType。 |

在此示例中，值类型为 `phoneNumber` 的 cellPhoneNumber 声明转换为值类型为 `string`的手机声明。

```XML
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
  - **phoneNumber**： + 11234567890 （phoneNumber）
- 输出声明：
  - **phoneNumberString**： + 11234567890 （字符串）


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

此声明转换验证电话号码的格式。 如果它是有效的格式，请将其更改为 Azure AD B2C 使用的标准格式。 如果提供的电话号码格式无效，则返回一条错误消息。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | 字符串 |  电话号码的字符串声明。 电话号码必须为国际格式，并以 "+" 和国家/地区代码开头。 如果提供了输入声明 `country`，则电话号码为本地格式（无国家/地区代码）。 |
| InputClaim | country | 字符串 | 可有可无ISO3166 格式的电话号码的国家/地区代码的字符串声明（由两个字母组成的 ISO-3166 国家/地区代码）。 |
| OutputClaim | outputClaim | phoneNumber | 此声明转换的结果。 |

**ConvertStringToPhoneNumberClaim**声明转换始终从由[自断言技术配置文件](self-asserted-technical-profile.md)或[显示控件](display-controls.md)调用的[验证技术配置文件](validation-technical-profile.md)执行。 **UserMessageIfClaimsTransformationInvalidPhoneNumber**自断言技术配置文件元数据控制向用户显示的错误消息。

![错误消息执行路径示意图](./media/phone-authentication/assert-execution.png)

您可以使用此声明转换来确保所提供的字符串声明是有效的电话号码。 如果没有，则会引发错误消息。 以下示例检查**phoneString** ClaimType 是否确实是有效的电话号码，然后以标准 Azure AD B2C 格式返回电话号码。 否则，将引发错误消息。

```XML
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

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>示例 1

- 输入声明：
  - **phoneNumberString**： 033 456-7890
  - **国家/地区**：深色
- 输出声明：
  - **outputClaim**： + 450334567890

### <a name="example-2"></a>示例 2

- 输入声明：
  - **phoneNumberString**： + 1 （123）456-7890
- 输出声明： 
  - **outputClaim**： + 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

这将提取国家/地区代码和输入声明中的国家/地区号，如果提供的电话号码无效，则可以选择引发异常。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | 字符串 | 电话号码的字符串声明。 电话号码必须为国际格式，并以 "+" 和国家/地区代码开头。 |
| InputParameter | throwExceptionOnFailure | boolean | 可有可无一个参数，该参数指示当电话号码无效时是否引发异常。 默认值为 false。 |
| InputParameter | countryCodeType | 字符串 | 可有可无一个参数，指示输出声明中的国家/地区代码类型。 可用值为**CallingCode** （国家/地区的国际电话号码，例如 + 1）或**ISO3166** （两字母 ISO-3166 国家/地区代码）。 |
| OutputClaim | nationalNumber | 字符串 | 电话号码的国家/地区号码的字符串声明。 |
| OutputClaim | countryCode | 字符串 | 电话号码的国家/地区代码的字符串声明。 |


如果从由[自断言技术配置文件](self-asserted-technical-profile.md)或[显示控制操作](display-controls.md#display-control-actions)调用的[验证技术配置文件](validation-technical-profile.md)执行**GetNationalNumberAndCountryCodeFromPhoneNumberString**声明转换，则**UserMessageIfPhoneNumberParseFailure**的自断言技术配置文件元数据将控制向用户显示的错误消息。

![错误消息执行路径示意图](./media/phone-authentication/assert-execution.png)

您可以使用此声明转换将完整的电话号码拆分为国家/地区代码和国家/地区。 如果提供的电话号码无效，您可以选择引发错误消息。

下面的示例尝试将电话号码拆分为国家/地区代码。 如果电话号码有效，则电话号码将被国家/地区重写。 如果电话号码无效，则不会引发异常，并且电话号码仍具有其原始值。

```XML
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

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>示例 1

- 输入声明：
  - **phoneNumber**： + 49 （123）456-7890
- 输入参数：
  - **throwExceptionOnFailure**： false
  - **countryCodeType**： ISO3166
- 输出声明：
  - **nationalNumber**：1234567890
  - **countryCode**： DE

### <a name="example-2"></a>示例 2

- 输入声明：
  - **phoneNumber**： + 49 （123）456-7890
- 输入参数
  - **throwExceptionOnFailure**： false
  - **countryCodeType**： CallingCode
- 输出声明：
  - **nationalNumber**：1234567890
  - **countryCode**： + 49
