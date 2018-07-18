---
title: 在 Azure Active Directory B2C 中使用自定义策略管理 SSO 和令牌自定义 | Microsoft Docs
description: 了解如何使用自定义策略管理 SSO 和令牌自定义。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 811fb8b2de59c9d324ab4acb8b0f51b4cec80aee
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441791"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C：使用自定义策略管理 SSO 和令牌自定义
与使用内置策略相比，使用自定义策略能够以相同的力度控制令牌、会话和单一登录 (SSO) 配置。  若要了解每项设置的作用，请参阅[此文档](#active-directory-b2c-token-session-sso)。

## <a name="token-lifetimes-and-claims-configuration"></a>令牌生存期和声明配置
若要更改令牌生存期的设置，需要在想要影响的策略的信赖方文件中添加 `<ClaimsProviders>` 元素。  `<ClaimsProviders>` 元素是 `<TrustFrameworkPolicy>` 的子级。  在该元素中，需要放置影响令牌生存期的信息。  XML 如以下示例所示：

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

访问令牌生存期 - 可以通过修改包含 Key="token_lifetime_secs"（以秒为单位）的 `<Item>` 中的值，来更改访问令牌生存期。  内置策略中的默认值为 3600 秒（60 分钟）。

ID 令牌生存期 - 可以通过修改包含 Key="id_token_lifetime_secs"（以秒为单位）的 `<Item>` 中的值，来更改 ID 令牌生存期。  内置策略中的默认值为 3600 秒（60 分钟）。

刷新令牌生存期 - 可以通过修改包含 Key="refresh_token_lifetime_secs"（以秒为单位）的 `<Item>` 中的值，来更改刷新令牌生存期。  内置策略中的默认值为 1209600 秒（14 天）。

刷新令牌滑动窗口生存期 - 若要为刷新令牌设置滑动窗口生存期，请修改包含 Key="rolling_refresh_token_lifetime_secs"（以秒为单位）的 `<Item>` 中的值。  内置策略中的默认值为 7776000 秒（90 天）。  如果不想要强制滑动窗口生存期，请将此代码行替换为：
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

颁发者 (iss) 声明 - 若要更改颁发者 (iss) 声明，请修改包含 Key="IssuanceClaimPattern" 的 `<Item>` 中的值。  适用的值为 `AuthorityAndTenantGuid` 和 `AuthorityWithTfp`。

设置声明表示策略 ID - 用于设置此值的选项为 TFP（信任框架策略）和 ACR（身份验证上下文引用）。  
我们建议将其设置为 TFP，为此，请确保存在包含 Key="AuthenticationContextReferenceClaimPattern" 的 `<Item>` 并且其值为 `None`。
在 `<OutputClaims>` 项中添加以下元素：
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
对于 ACR，请删除包含 Key="AuthenticationContextReferenceClaimPattern" 的 `<Item>`。

使用者 (sub) 声明 - 此选项默认为 ObjectID，如果要切换为 `Not Supported`，请执行以下操作：

将以下代码行 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
替换为以下代码行：
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>会话行为和 SSO

若要更改会话行为和 SSO 配置，需要在 `<RelyingParty>` 元素内部添加 `<UserJourneyBehaviors>` 元素。  `<UserJourneyBehaviors>` 元素必须紧接在 `<DefaultUserJourney>` 后面。  `<UserJourneyBehavors>` 元素的内部应如下所示：

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
单一登录 (SSO) 配置 - 若要更改单一登录配置，需要修改 `<SingleSignOn>` 的值。  适用的值为 `Tenant`、`Application`、`Policy` 和 `Disabled`。 

Web 应用会话生存期(分钟) - 若要更改 Web 应用会话生存期，需要修改 `<SessionExpiryInSeconds>` 元素的值。  内置策略中的默认值为 86400 秒（1440 分钟）。

Web 应用会话超时 - 若要更改 Web 应用会话超时，需要修改 `<SessionExpiryType>` 的值。  适用的值为 `Absolute` 和 `Rolling`。
