---
title: Azure Active Directory B2C 标识体验框架架构的社交帐户声明转换示例 | Microsoft Docs
description: Azure Active Directory B2C 标识体验框架架构的社交帐户声明转换示例。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 52ec7c83b4070a4c38963b3ab12f58f923fa889d
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562618"
---
# <a name="social-accounts-claims-transformations"></a>社交帐户声明转换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory (Azure AD) B2C 中，社交帐户标识存储在 **alternativeSecurityIdCollection** 声明类型的 `userIdentities` 属性中。 **alternativeSecurityIdCollection** 中的每个项指定颁发者（标识提供者名称，例如 facebook.com）和 `issuerUserId`（颁发者的唯一用户标识符）。

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

本文通过示例演示如何使用 Azure Active Directory (Azure AD) B2C 中标识体验框架架构的社交帐户声明转换。 有关详细信息，请参阅 [ClaimsTransformations](claimstransformations.md)。

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

创建可在 Azure Active Directory 调用中使用的用户 alternativeSecurityId 属性的 JSON 表示形式。 有关详细信息，请参阅 [AlternativeSecurityId 的架构](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#AlternativeSecurityIdType)。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | 字符串 | 用于指定社交标识提供者所用唯一用户标识符的 ClaimType。 |
| InputClaim | identityProvider | 字符串 | 用于指定社交帐户标识提供者名称（例如 facebook.com）的 ClaimType。 |
| OutputClaim | alternativeSecurityId | 字符串 | 调用 ClaimsTransformation 后生成的 ClaimType。 包含社交帐户用户的标识信息。 **issuer** 是 `identityProvider` 声明的值。 **issuerUserId** 是 `key` 声明的值，采用 base64 格式。 |

使用此声明转换可以生成 `alternativeSecurityId` ClaimType。 此 ClaimType 由所有社交标识提供者技术配置文件（例如 `Facebook-OAUTH`）使用。 以下声明转换接收用户社交帐户 ID 和标识提供者名称。 此技术配置文件的输出是可在 Azure AD 目录服务中使用的 JSON 字符串格式。

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="socialIdpUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **key**：12334
    - **identityProvider**：Facebook.com
- 输出声明：
    - **alternativeSecurityId**：{ "issuer": "facebook.com", "issuerUserId":"MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

将 `AlternativeSecurityId` 添加到 `alternativeSecurityIdCollection` 声明。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | 字符串 | 要添加到输出声明的 ClaimType。 |
| InputClaim | collection | alternativeSecurityIdCollection | 声明转换使用的 ClaimTypes（如果在策略中可用）。 如果已提供，则声明转换将在集合末尾添加 `item`。 |
| OutputClaim | collection | alternativeSecurityIdCollection | 调用此 ClaimsTransformation 后生成的 ClaimType。 包含 `collection` 和 `item` 输入中的项的集合。 |

以下示例将新的社交标识与现有帐户相链接。 链接新的社交标识：
1. 在 **AAD-UserReadUsingAlternativeSecurityId** 和 **AAD-UserReadUsingObjectId** 技术配置文件中，输出用户的 **alternativeSecurityIds** 声明。
1. 要求用户使用某个不与此用户关联的标识提供者登录。
1. 使用 **CreateAlternativeSecurityId** 声明转换创建名为 `AlternativeSecurityId2` 的新 **alternativeSecurityId** 声明类型
1. 调用 **AddItemToAlternativeSecurityIdCollection** 声明转换，将 **AlternativeSecurityId2** 声明添加到现有的 **AlternativeSecurityIds** 声明。
1. 将 **alternativeSecurityIds** 声明保存到用户帐户

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>示例

- 输入声明：
    - **item**：{ "issuer": "facebook.com", "issuerUserId":"MTIzNDU=" }
    - **collection**：[ { "issuer": "live.com", "issuerUserId":"MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- 输出声明：
    - **collection**：[ { "issuer": "live.com", "issuerUserId":"MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId":"MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

在新的 **stringCollection** 声明中返回 **alternativeSecurityIdCollection** 声明中的颁发者列表。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | 用于获取标识提供者（颁发者）列表的 ClaimType。 |
| OutputClaim | identityProvidersCollection | stringCollection | 调用此 ClaimsTransformation 后生成的 ClaimType。 与 alternativeSecurityIdCollection 输入声明关联的标识提供者列表 |

以下声明转换读取用户 **alternativeSecurityIds** 声明，并提取与该帐户关联的标识提供者名称列表。 使用 **identityProvidersCollection** 输出向用户显示与该帐户关联的标识提供者列表。 或者，在标识提供者选项页上，基于输出 **identityProvidersCollection** 声明筛选标识提供者列表。 因此，用户可以选择链接尚未与该帐户关联的新社交标识。

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- 输入声明：
    - **alternativeSecurityIdCollection**：[ { "issuer": "google.com", "issuerUserId":"MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId":"MTIzNDU=" } ]
- 输出声明：
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

从 **alternativeSecurityIdCollection** 声明中删除 **AlternativeSecurityId**。

| Item | TransformationClaimType | 数据类型 | 说明 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | 字符串 | 包含要从集合中删除的标识提供者名称的 ClaimType。 |
| InputClaim | collection | alternativeSecurityIdCollection | 声明转换使用的 ClaimTypes。 声明转换从集合中删除 identityProvider。 |
| OutputClaim | collection | alternativeSecurityIdCollection | 调用此 ClaimsTransformation 后生成的 ClaimType。 从集合中删除 identityProvider 后的新集合。 |

以下示例取消某个社交标识与现有帐户的链接。 取消链接社交标识：
1. 在 **AAD-UserReadUsingAlternativeSecurityId** 和 **AAD-UserReadUsingObjectId** 技术配置文件中，输出用户的 **alternativeSecurityIds** 声明。
2. 要求用户选择要从此用户关联的标识提供者列表中删除哪个社交帐户。
3. 调用一个声明转换技术配置文件，该技术配置文件调用使用标识提供者名称删除了所选社交标识的 **RemoveAlternativeSecurityIdByIdentityProvider** 声明转换。
4. 将 **alternativeSecurityIds** 声明保存到用户帐户。

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>示例

- 输入声明：
    - **identityProvider**: facebook.com
    - **collection**：[ { "issuer": "live.com", "issuerUserId":"MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId":"MTIzNDU=" } ]
- 输出声明：
    - **collection**：[ { "issuer": "live.com", "issuerUserId":"MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
