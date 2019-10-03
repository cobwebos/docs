---
title: 在 Azure Active Directory B2C 中使用自定义策略管理 SSO 和令牌自定义 | Microsoft Docs
description: 了解在 Azure Active Directory B2C 中使用自定义策略管理 SSO 和令牌自定义。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 966386bfed5f94556f145afab1c665eb3c90546a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065560"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略管理 SSO 和令牌自定义

本文提供了有关如何在 Azure Active Directory B2C （Azure AD B2C）中使用[自定义策略](active-directory-b2c-overview-custom.md)管理令牌、会话和单一登录（SSO）配置的信息。

## <a name="token-lifetimes-and-claims-configuration"></a>令牌生存期和声明配置

若要更改令牌生存期的设置，需要在想要影响的策略的信赖方文件中添加 [ClaimsProviders](claimsproviders.md) 元素。  **ClaimsProviders** 元素是 [TrustFrameworkPolicy](trustframeworkpolicy.md) 元素的子代。

在信赖方文件的 BasePolicy 元素和 RelyingParty 元素之间插入 ClaimsProviders 元素。

在该元素中，需要放置影响令牌生存期的信息。 XML 如以下示例所示：

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

上一示例中设置了以下值：

- **访问令牌生存期** - 访问令牌生存期值是通过 **token_lifetime_secs** 元数据项设置的。 默认值为 3600 秒（60 分钟）。
- **ID 令牌生存期** - ID 令牌生存期值是通过 **id_token_lifetime_secs** 元数据项设置的。 默认值为 3600 秒（60 分钟）。
- **刷新令牌生存期** - 刷新令牌生存期值是通过 **refresh_token_lifetime_secs** 元数据项设置的。 默认值为 1209600 秒（14 天）。
- **刷新令牌滑动窗口生存期** - 若要为刷新令牌设置滑动窗口生存期，请设置 **rolling_refresh_token_lifetime_secs** 元数据项的值。 默认值为 7776000（90 天）。 如果不想强制实施滑动窗口生存期，请将此项替换为 `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`。
- **颁发者 (iss) 声明** - 颁发者 (iss) 声明是通过 **IssuanceClaimPattern** 元数据项设置的。 适用的值为 `AuthorityAndTenantGuid` 和 `AuthorityWithTfp`。
- **设置声明表示策略 ID** - 用于设置此值的选项为 `TFP`（信任框架策略）和 `ACR`（身份验证上下文引用）。 `TFP` 是建议使用的值。 将 **AuthenticationContextReferenceClaimPattern** 设置为值 `None`。

    在 ClaimsSchema 元素中，添加此元素：

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    在 OutputClaims 元素中，添加此元素：

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    对于 ACR，请删除 **AuthenticationContextReferenceClaimPattern** 项。

- **使用者 (sub) 声明** - 此选项默认为 ObjectID，如果要将此设置切换为 `Not Supported`，请替换以下行：

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    替换为以下代码行：

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>会话行为和 SSO

若要更改会话行为和 SSO 配置，需要在 [RelyingParty](relyingparty.md) 元素内添加 **UserJourneyBehaviors** 元素。  **UserJourneyBehaviors** 元素必须紧跟在 **DefaultUserJourney** 之后。 **UserJourneyBehavors** 元素的内部看起来应当如以下示例所示：

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

上一示例中配置了以下值：

- **单一登录 (SSO)** - 单一登录是通过 **SingleSignOn** 配置的。 适用的值为 `Tenant`、`Application`、`Policy` 和 `Suppressed`。
- **Web 应用会话生存期（分钟）** - Web 应用会话生存期是通过 **SessionExpiryInSeconds** 元素设置的。 默认值为 86400 秒（1440 分钟）。
- **Web 应用会话超时** - Web 应用会话超时是通过 **SessionExpiryType** 元素设置的。 适用的值为 `Absolute` 和 `Rolling`。
