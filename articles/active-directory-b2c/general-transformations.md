---
title: 自定义策略的常规声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的 Identity Experience Framework (IEF) 架构的常规声明转换示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 52831a1907d5ca8d13b0477c909d0d0358873973
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202214"
---
# <a name="general-claims-transformations"></a>常规声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C (Azure AD B2C) 中使用 Identity Experience Framework 架构的常规声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="copyclaim"></a>CopyClaim

将声明的值复制到另一个声明。 这两个声明的类型必须相同。

| 项目 | TransformationClaimType | 数据类型 | 注释 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string, int | 要复制的声明类型。 |
| OutputClaim | outputClaim | string, int | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

使用此声明转换可以将字符串或数值声明中的值复制到另一个声明。 以下示例将 externalEmail 声明值复制到电子邮件声明。

```xml
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **inputClaim**: bob@contoso.com
- 输出声明：
    -  outputClaim: bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

检查 inputClaim  是否存在并将 outputClaim  相应地设置为 true 或 false。

| 项目 | TransformationClaimType | 数据类型 | 注释 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |任意 | 需要验证是否存在的输入声明。 |
| OutputClaim | outputClaim | boolean | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

使用此声明转换检查声明是否存在或是否包含任何值。 返回值是指示声明是否存在的布尔值。 以下示例检查电子邮件地址是否存在。

```xml
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **inputClaim**: someone@contoso.com
- 输出声明：
  - **outputClaim**: true

## <a name="hash"></a>哈希

使用加密盐和机密对提供的纯文本执行哈希。 使用的哈希算法是 SHA-256。

| 项目 | TransformationClaimType | 数据类型 | 注释 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 明文 | string | 要加密的输入声明 |
| InputClaim | 加密盐 | string | 加密盐参数。 可以使用 `CreateRandomString` 声明转换创建随机值。 |
| InputParameter | randomizerSecret | string | 指向现有的 Azure AD B2C **策略密钥**。 若要创建新策略密钥，请执行以下操作：在 Azure AD B2C 租户的**管理**下，选择 **Identity Experience Framework**。 选择“策略密钥”，以查看租户中的可用密钥。  选择“添加”   。 对于“选项”，请选择“手动”   。 提供名称（可能会自动添加前缀 B2C_1A_  ）。 在“机密”  文本框中，输入要使用的任何机密，如 1234567890。 对于“密钥用法”，请选择“签名”   。 选择“创建”  。 |
| OutputClaim | hash | string | 调用此声明转换后生成的 ClaimType。 在 `plaintext` inputClaim 中配置的声明。 |

```xml
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
  - **plaintext**: MyPass@word1
  - **加密盐**：487624568
  - **randomizerSecret**：B2C_1A_AccountTransformSecret
- 输出声明：
  - **outputClaim**：CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
