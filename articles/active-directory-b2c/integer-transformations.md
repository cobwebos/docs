---
title: 自定义策略的整数声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的标识体验框架（IEF）架构的整数声明转换示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187672"
---
# <a name="integer-claims-transformations"></a>整数声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C （Azure AD B2C）中使用标识体验框架架构的整数声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

将 long 数据类型转换为字符串数据类型。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 要转换为字符串的 ClaimType。 |
| OutputClaim | outputClaim | 字符串 | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

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
    - **inputClaim**: 12334 (long)
- 输出声明：
    - **outputClaim**: "12334" (string)

