---
title: 自定义策略的 StringCollection 声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的标识体验框架（IEF）架构的 StringCollection 声明转换示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e3ce7ff633f41ccfe6faa3cc1dba1020e74459aa
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656086"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection 声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C （Azure AD B2C）中使用标识体验框架架构的字符串集合声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="additemtostringcollection"></a>AddItemToStringCollection

将字符串声明添加到新的 stringCollection 声明的唯一值。 

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | 字符串 | 要添加到输出声明的 ClaimType。 |
| InputClaim | collection | stringCollection | [可选] 如果已指定，则声明转换会复制此集合中的项，并将该项添加到输出集合声明的末尾。 |
| OutputClaim | collection | stringCollection | 在调用此声明转换之后生成的 ClaimType，并在输入声明中指定了值。 |

使用此声明转换将字符串添加到新的或现有的 stringCollection。 它通常用于 AAD-UserWriteUsingAlternativeSecurityId 技术配置文件。 在创建新的社交帐户之前，CreateOtherMailsFromEmail 声明转换会读取 ClaimType，并将值添加到 otherMails ClaimType。

以下声明转换会将 email ClaimType 添加到 otherMails ClaimType。

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

将字符串参数添加到新的 stringCollection 声明的唯一值。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [可选] 如果已指定，则声明转换会复制此集合中的项，并将该项添加到输出集合声明的末尾。 |
| InputParameter | item | 字符串 | 要添加到输出声明的值。 |
| OutputClaim | collection | stringCollection | 调用此声明转换后生成的 ClaimType，其值在输入参数中指定。 |

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
| InputClaim | collection | stringCollection | 由声明转换用于获取项的 ClaimTypes。 |
| OutputClaim | extractedItem | 字符串 | 调用此 ClaimsTransformation 后生成的 ClaimType。 集合中的第一项。 |

以下示例读取 otherMails 声明，并将第一项返回到 email 声明中。

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


## <a name="stringcollectioncontains"></a>StringCollectionContains

检查 StringCollection 声明类型是否包含元素

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | 要搜索的声明类型。 |
|InputParameter|item|字符串|要搜索的值。|
|InputParameter|ignoreCase|字符串|指定此比较是否应忽略所比较字符串的大小写。|
| OutputClaim | outputClaim | boolean | 调用此 ClaimsTransformation 后生成的 ClaimType。 如果集合包含这样的字符串，则为布尔型指示器 |

下面的示例检查 `roles` stringCollection 声明类型是否包含**admin**的值。

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

- 输入声明：
    - **inputClaim**： ["reader"、"author"、"admin"]
- 输入参数：
    - **项**： "Admin"
    - **regexoptions.ignorecase**： "true"
- 输出声明：
    - **outputClaim**： "true"


