---
title: 自定义策略的 JSON 声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的标识体验框架（IEF）架构的 JSON 声明转换示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad8fcf578ae1c89856a9d7929af0aec813cb4082
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187587"
---
# <a name="json-claims-transformations"></a>JSON 声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C （Azure AD B2C）中使用标识体验框架架构的 JSON 声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="generatejson"></a>GenerateJson

使用声明值或常量生成 JSON 字符串。 后随点表示法的路径字符串用于指示将数据插入 JSON 字符串的位置。 按点拆分后，任何整数被解释为 JSON 数组的索引，而非整数被解释为 JSON 对象的索引。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 后随点表示法的任何字符串 | 字符串 | JSON 的 JsonPath，声明值将插入到其中。 |
| InputParameter | 后随点表示法的任何字符串 | 字符串 | 要将常量字符串值插入到其中的 JSON 的 JsonPath。 |
| OutputClaim | outputClaim | 字符串 | 生成的 JSON 字符串。 |

下面的示例基于 "email" 和 "otp" 的声明值以及常量字符串生成 JSON 字符串。

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

下面的声明转换输出将作为发送到 SendGrid （第三方电子邮件提供程序）的请求正文的 JSON 字符串声明。 JSON 对象的结构由 InputClaims 的输入参数和 TransformationClaimTypes 的点表示法中的 Id 定义。 点表示法中的数字表示数组。 值来自 "InputClaims" 值和 "输入参数" "值" 属性。

- 输入声明：
  - **电子邮件**，转换声明类型**个性化. 0. 发. 0. email**： "someone@example.com"
  - **otp**，转换声明类型**个性化. 0. dynamic_template_data** "346349"
- 输入参数：
  - **template_id**： "4c56ffb40fa648b1aa6822283df94f60"
  - **自. email**： "service@contoso.com"
  - **个性化。 0. 使用者**"Contoso 帐户电子邮件验证码"
- 输出声明：
  - **requestBody**： JSON 值

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

从 JSON 数据中获取指定的元素。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 字符串 | 由声明转换用于获取项的 ClaimTypes。 |
| InputParameter | claimToExtract | 字符串 | 要提取的 JSON 元素的名称。 |
| OutputClaim | extractedClaim | 字符串 | 调用此声明转换后生成的 ClaimType，即 claimToExtract 输入参数中指定的元素值。 |

在以下示例中，声明转换提取 JSON 数据中的 `emailAddress` 元素：`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- 输入参数：
    - claimToExtract: emailAddress
- 输出声明：
  - extractedClaim: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

从 Json 数据中获取指定元素列表。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | 字符串 | 由声明转换用于获取声明的 ClaimTypes。 |
| InputParameter | errorOnMissingClaims | boolean | 指定如果缺少一个声明是否引发错误。 |
| InputParameter | includeEmptyClaims | 字符串 | 指定是否包含空声明。 |
| InputParameter | jsonSourceKeyName | 字符串 | 元素键名称 |
| InputParameter | jsonSourceValueName | 字符串 | 元素值名称 |
| OutputClaim | 集合 | 字符串、int、布尔值，和日期时间 |要提取的声明列表。 声明名称应等于 jsonSourceClaim 输入声明中指定的名称。 |

在以下示例中，声明转换从 JSON 数据中提取以下声明：email（字符串）、displayName（字符串）、membershipNum (int)、active（布尔值）和 birthdate（日期时间）。

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- 输入声明：
  - jsonSourceClaim: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- 输入参数：
    - errorOnMissingClaims: false
    - includeEmptyClaims: false
    - jsonSourceKeyName: key
    - jsonSourceValueName: value
- 输出声明：
  - email: "someone@example.com"
  - displayName: "Someone"
  - membershipNum: 6353399
  - active: true
  - birthdate: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

从 JSON 数据中获取指定的数值 (long) 元素。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 字符串 | 由声明转换用于获取声明的 ClaimTypes。 |
| InputParameter | claimToExtract | 字符串 | 要提取的 JSON 元素的名称。 |
| OutputClaim | extractedClaim | long | 调用此 ClaimsTransformation 后生成的 ClaimType，即 claimToExtract 输入参数中指定的元素值。 |

在以下示例中，声明转换提取 JSON 数据中的 `id` 元素。

```JSON
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - inputJson: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- 输入参数
    - claimToExtract:  id
- 输出声明：
    - extractedClaim: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

从 JSON 数据数组中获取第一个元素。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | 字符串 | 由声明转换用于从 JSON 数组中获取项的 ClaimTypes。 |
| OutputClaim | extractedClaim | 字符串 | 调用此 ClaimsTransformation 后生成的 ClaimType，即 JSON 数组中的第一个元素。 |

在以下示例中，声明转换提取 JSON 数组 `["someone@example.com", "Someone", 6353399]` 中的第一个元素（电子邮件地址）。

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - inputJsonClaim: ["someone@example.com", "Someone", 6353399]
- 输出声明：
  - extractedClaim: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

将 XML 数据转换为 JSON 格式。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | 字符串 | 由声明转换用于将数据从 XML 转换为 JSON 格式的 ClaimTypes。 |
| OutputClaim | json | 字符串 | 调用此 ClaimsTransformation 后生成的 ClaimType，即采用 JSON 格式的数据。 |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

在以下示例中，声明转换将以下 XML 数据转换为 JSON 格式。

#### <a name="example"></a>示例
输入声明：

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

输出声明：

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```
