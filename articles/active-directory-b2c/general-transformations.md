---
title: 自定义策略的一般声明转换示例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的标识体验框架（IEF）架构的一般声明转换示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 639277177bf63e659e5b0ea804eca5e20f956831
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948822"
---
# <a name="general-claims-transformations"></a>常规声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了有关在 Azure Active Directory B2C （Azure AD B2C）中使用标识体验框架架构的一般声明转换的示例。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="doesclaimexist"></a>DoesClaimExist

检查 inputClaim 是否存在并将 outputClaim 相应地设置为 true 或 false。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |任意 | 需要验证是否存在的输入声明。 |
| OutputClaim | outputClaim | boolean | 调用此 ClaimsTransformation 后生成的 ClaimType。 |

使用此声明转换检查声明是否存在或是否包含任何值。 返回值是指示声明是否存在的布尔值。 以下示例检查电子邮件地址是否存在。

```XML
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
  - outputClaim: true

## <a name="hash"></a>哈希

使用加密盐和机密对提供的纯文本执行哈希。 使用的哈希算法是 SHA-256。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 纯文本 | 字符串 | 要加密的输入声明 |
| InputClaim | 加密盐 | 字符串 | 加密盐参数。 可以使用 `CreateRandomString` 声明转换创建随机值。 |
| InputParameter | randomizerSecret | 字符串 | 指向现有 Azure AD B2C**策略密钥**。 若要创建新的策略密钥：在 Azure AD B2C 租户中的 "**管理**" 下，选择 "**标识体验框架**"。 选择 "**策略密钥**"，查看租户中可用的密钥。 选择 **添加** 。 对于“选项”，请选择“手动”。 提供一个名称（可能会自动添加前缀*B2C_1A_* 。） 在 "**机密**" 文本框中，输入要使用的任何机密，如1234567890。 对于“密钥用法”，请选择“签名”。 选择**创建**。 |
| OutputClaim | hash | 字符串 | 调用此声明转换后生成的 ClaimType。 在 `plaintext` inputClaim 中配置的声明。 |

```XML
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
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- 输出声明：
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
