---
title: Azure Active Directory B2C 标识体验框架架构的整数声明转换示例 | Microsoft Docs
description: Azure Active Directory B2C 标识体验框架架构的整数声明转换示例。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
origin.date: 09/10/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 358ee07b8fd32edded084d406e490cae9f557fdd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397135"
---
# <a name="integer-claims-transformations"></a>整数声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory (Azure AD) B2C 中使用标识体验框架架构的整数声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

将 long 数据类型转换为字符串数据类型。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 要转换为字符串的 ClaimType。 |
| OutputClaim | outputClaim | string | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

在此示例中，值类型为 long 的 `numericUserId` 声明将转换为值类型为字符串的 `UserId` 声明。

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**：12334 (long)
- 输出声明： 
    - **outputClaim**："12334" (string)


