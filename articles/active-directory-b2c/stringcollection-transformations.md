---
title: Azure Active Directory B2C 标识体验框架架构的 StringCollection 声明转换示例 | Microsoft Docs
description: Azure Active Directory B2C 标识体验框架架构的 StringCollection 声明转换示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98453daeb34d093b49cdcc636f68c3d7ae017126
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512437"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection 声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示了在 Azure Active Directory (Azure AD) B2C 中使用标识体验框架架构的字符串集合声明转换的过程。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="additemtostringcollection"></a>AddItemToStringCollection

将字符串声明添加到新的 stringCollection 声明。 

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | 字符串 | 要添加到输出声明的 ClaimType。 |
| InputClaim | 集合 | stringCollection | [可选] 如果已指定，则声明转换会复制此集合中的项，并将该项添加到输出集合声明的末尾。 |
| OutputClaim | 集合 | stringCollection | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

使用此声明转换将字符串添加到新的或现有的 stringCollection。 它通常用于 AAD-UserWriteUsingAlternativeSecurityId  技术配置文件。 在创建新的社交帐户之前，CreateOtherMailsFromEmail  声明转换会读取 ClaimType，并将值添加到 otherMails  ClaimType。 

以下声明转换会将 email  ClaimType 添加到 otherMails  ClaimType。

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **collection**: ["someone@outlook.com"]
  - **item**: "admin@contoso.com"
- 输出声明： 
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

将字符串参数添加到新的 stringCollection 声明。 

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 集合 | stringCollection | [可选] 如果已指定，则声明转换会复制此集合中的项，并将该项添加到输出集合声明的末尾。 |
| InputParameter | item | 字符串 | 要添加到输出声明的值。 |
| OutputClaim | 集合 | stringCollection | 调用此 ClaimsTransformation 后将生成的 ClaimTypes。 |

使用此声明转换将字符串值添加到新的或现有的 stringCollection。 以下示例将常量电子邮件地址 (admin@contoso.com) 添加到 **otherMails** 声明。 

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **collection**: ["someone@outlook.com"]
- 输入参数 
  - **item**: "admin@contoso.com"
- 输出声明：
  - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

从提供的字符串集合中获取第一项。 

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 集合 | stringCollection | 由声明转换用于获取项的 ClaimTypes。 |
| OutputClaim | extractedItem | 字符串 | 调用此 ClaimsTransformation 后生成的 ClaimType。 集合中的第一项。 |

以下示例读取 otherMails  声明，并将第一项返回到 email  声明中。 

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **collection**: ["someone@outlook.com", "someone@contoso.com"]
- 输出声明： 
  - **extractedItem**: "someone@outlook.com"

