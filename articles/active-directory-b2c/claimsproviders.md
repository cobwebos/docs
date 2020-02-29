---
title: ClaimsProviders  - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 ClaimsProvider 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dfb34085181e0b759d1d77485ff21b5bc59e0de3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189763"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

一个声明提供程序可以包含一组[技术配置文件](technicalprofiles.md)。 每个声明提供程序必须包含一个或多个用于确定终结点的技术配置文件，以及与该声明提供程序通信所需的协议。 一个声明提供程序可以包含多个技术配置文件。 例如，由于声明提供程序需要支持多个协议和具有不同功能的各种终结点，或者需要在不同的保障级别发布不同的声明，因此可以定义多个技术配置文件。 可以接受在一个用户旅程中发布敏感声明，但不接受在另一个用户旅程中发布此类声明。

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

ClaimsProviders 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | 经认可的声明提供程序，可在各种用户旅程中使用。 |

## <a name="claimsprovider"></a>ClaimsProvider

ClaimsProvider 元素包含以下子元素：

| 元素 | 出现次数 | 说明 |
| ------- | ---------- | ----------- |
| 域 | 0:1 | 一个字符串，包含声明提供程序的域名。 例如，如果声明提供程序包含 Facebook 技术配置文件，则域名为 Facebook.com。 此域名用于声明提供程序中定义的所有技术配置文件，除非被技术配置文件覆盖。 域名也可以在 **domain_hint** 中引用。 有关详细信息，请参阅**使用 Azure Active Directory B2C 设置直接登录**的[将登录重定向到社交提供者](direct-signin.md)部分。 |
| DisplayName | 1:1 | 一个包含声明提供程序的名称的字符串。 |
| [技术配置文件](technicalprofiles.md) | 0:1 | 声明提供程序支持的一组技术配置文件 |

**ClaimsProvider**组织技术配置文件与声明提供程序之间的关系。 以下示例显示了使用 Azure Active Directory 技术配置文件的 Azure Active Directory 声明提供程序：

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

以下示例显示了使用 **Facebook-OAUTH** 技术配置文件的 Facebook 声明提供程序。

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
