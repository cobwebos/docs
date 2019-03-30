---
title: ClaimsTransformations - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C 标识体验框架架构中 ClaimsTransformations 元素的定义。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: bc6cc7b07d3dce43a666b3e5b0a958b41cdd3131
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651502"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

ClaimsTransformations 元素包含一个可在用户旅程中用作[自定义策略](active-directory-b2c-overview-custom.md)的一部分的声明转换函数列表。 声明转换可以将给定声明转换为另一个声明。 在声明转换中，指定转换方法，例如将项添加到一个字符串集合或更改字符串的大小写。

若要包含可在用户旅程中使用的声明转换函数的列表，必须在策略的 BuildingBlocks 部分下声明 ClaimsTransformations XML 元素。

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**ClaimsTransformation**元素包含以下属性：

| 属性 |需要 | 描述 |
| --------- |-------- | ----------- |
| ID |是 | 用于唯一标识声明转换的标识符。 从策略中的其他 XML 元素引用此标识符。 |
| TransformationMethod | 是 | 用于声明转换的转换方法。 每个声明转换都有其自己的值。 有关可用值的完整列表，请参阅[声明转换引用](#claims-transformations-reference)。 |

## <a name="claimstransformation"></a>ClaimsTransformation

ClaimsTransformation 元素包含以下元素：

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>                
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| 元素 | 出现次数 | 描述 |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | InputClaim 元素列表，这些元素用于指定被看作声明转换输入的声明类型。 每个元素都包含对在策略的 ClaimsSchema 部分定义的 ClaimType 的引用。 |
| InputParameters | 0:1 | InputParameter 元素列表，这些元素作为声明转换输入提供。  
| OutputClaims | 0:1 | OutputClaim 元素列表，这些元素用于指定在调用 ClaimsTransformation 后生成的声明类型。 每个元素都包含对在 ClaimsSchema 部分定义的 ClaimType 的引用。 |

### <a name="inputclaims"></a>InputClaims

InputClaims 元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 预期的输入声明类型。 |

#### <a name="inputclaim"></a>InputClaim

InputClaim 元素包含以下属性：

| 属性 |需要 | 描述 |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |是 | 对在策略的 ClaimsSchema 部分定义的 ClaimType 的引用。 |
| TransformationClaimType |是 | 用于引用转换声明类型的标识符。 每个声明转换都有其自己的值。 有关可用值的完整列表，请参阅[声明转换引用](#claims-transformations-reference)。 |

### <a name="inputparameters"></a>InputParameters

InputParameters 元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | 预期的输入参数。 |

#### <a name="inputparameter"></a>InputParameter

| 属性 | 需要 |描述 |
| --------- | ----------- |----------- |
| ID | 是 | 是对声明转换方法的参数的引用的标识符。 每个声明转换方法都有其自己的值。 有关可用值的完整列表，请参阅声明转换表。 |
| 数据类型 | 是 | 参数的数据类型，例如根据自定义策略 XML 架构中的 DataType 枚举的字符串、布尔值、Int 或 DateTime。 此类型用于正确执行算术操作。 每个声明转换都有其自己的值。 有关可用值的完整列表，请参阅[声明转换引用](#claims-transformations-reference)。 |
| 值 | 是 | 将逐字字符串传递到传递的一个值。 某些值是任意的，某些值是从声明转换方法中选择的。 |

### <a name="outputclaims"></a>OutputClaims

OutputClaims 元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | 预期的输出声明类型。 |

#### <a name="outputclaim"></a>OutputClaim 

OutputClaim 元素包含以下属性：

| 属性 |需要 | 描述 |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | 是 | 对在策略的 ClaimsSchema 部分定义的 ClaimType 的引用。
| TransformationClaimType | 是 | 用于引用转换声明类型的标识符。 每个声明转换都有其自己的值。 有关可用值的完整列表，请参阅[声明转换引用](#claims-transformations-reference)。 |
 
如果输入声明和输出声明具有相同的类型（字符串或布尔值），可以将相同的输入声明用作输出声明。 在这种情况下，声明转换更改具有输出值的输入声明。

## <a name="example"></a>示例

例如，可能会存储用户接受的上一版本的服务条款。 更新服务条款时，可以请求用户接受新版本。 在以下示例中，HasTOSVersionChanged 声明将 TOSVersion 声明的值与 LastTOSAcceptedVersion 声明的值进行比较，然后返回布尔 TOSVersionChanged 声明。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>声明转换引用

有关声明转换的示例，请参阅以下引用页：

- [布尔值](boolean-transformations.md)
- [日期](date-transformations.md)
- [整数](integer-transformations.md)
- [JSON](json-transformations.md)
- [常规](general-transformations.md)
- [社交帐户](social-transformations.md)
- [字符串](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

