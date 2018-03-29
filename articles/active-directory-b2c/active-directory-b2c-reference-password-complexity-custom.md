---
title: 自定义策略中的密码复杂性 - Azure AD B2C | Microsoft 文档
description: 如何在自定义策略中配置密码复杂性要求
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 4dabcdef32d337c5063d2b0503f32b06d5d0fdeb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="configure-password-complexity-in-custom-policies"></a>在自定义策略中配置密码复杂性

> [!NOTE]
> **此功能为预览版。**  联系 [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) 为测试租户启用此功能。  不要对生产租户测试此功能。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文对密码复杂性的工作原理进行了深入描述，并介绍了如何使用 Azure AD B2C 自定义策略来启用它。

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C：配置密码复杂性要求

Azure Active Directory B2C (Azure AD B2C) 支持更改由最终用户在创建帐户时提供的密码复杂性要求。  默认情况下，Azure AD B2C 使用“强”密码。  此外，Azure AD B2C 还支持用于控制客户可以使用的密码复杂性的配置选项。  本文介绍如何在自定义策略中配置密码复杂性。  另外，还有可能要使用[在内置策略中配置密码复杂性](active-directory-b2c-reference-password-complexity.md)。

## <a name="prerequisites"></a>先决条件

根据[入门](active-directory-b2c-get-started-custom.md)中所述配置一个 Azure AD B2C 租户，以完成本地帐户注册/登录。

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>如何在自定义策略中配置密码复杂性

若要在自定义策略中配置密码复杂性，自定义策略的整体结构必须包括 `BuildingBlocks` 内的 `ClaimsSchema`、`Predicates` 和 `InputValidations` 元素。

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

这些元素的用途如下所示：

- 每个 `Predicate` 元素定义基本字符串验证检查，返回 true 或 false。
- `InputValidations` 元素具有一个或多个 `InputValidation` 元素。  通过使用一系列 `Predicate` 元素构建每个 `InputValidation`。 此元素允许你执行布尔聚合（类似于 `and` 和 `or`）。
- `ClaimsSchema` 定义正在验证的声明。  然后，它定义用于验证该声明的 `InputValidation` 规则。

### <a name="defining-a-predicate-element"></a>定义谓词元素

谓词具有两种方法类型：IsLengthRange 或 MatchesRegex。 让我们查看每个示例。  首先，我们有一个用于匹配正则表达式的 MatchesRegex 示例。  在此示例中，它可匹配包含数字的字符串。

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

接下来，让我们回顾一下 IsLengthRange 的示例。  此方法采用最小和最大字符串长度。

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

如果检查失败，则使用 `HelpText` 属性为最终用户提供错误消息。  可以使用[语言自定义功能](active-directory-b2c-reference-language-customization.md)本地化此字符串。

### <a name="defining-an-inputvalidation-element"></a>定义 InputValidation 元素

`InputValidation` 是 `PredicateReferences` 的聚合。 每个 `PredicateReferences` 必须为 true `InputValidation` 才能成功。  但是，在 `PredicateReferences` 元素内，请使用名为 `MatchAtLeast` 的属性指定多少个 `PredicateReference` 检查必须返回 true。  （可选）定义 `HelpText` 属性来覆盖它所引用的 `Predicate` 元素中定义的错误消息。

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>定义 ClaimsSchema 元素

声明类型 `newPassword` 和 `reenterPassword` 被视为特殊类型，因此请不要更改名称。  UI 基于这些 `ClaimType` 元素验证用户在创建帐户期间是否正确重新输入其密码。  若要查找相同的 `ClaimType` 元素，请在初学者包的 TrustFrameworkBase.xml 中查找。  此示例中的新增内容是我们将重写这些元素来定义 `InputValidationReference`。 此新元素的 `ID` 属性指向我们定义的 `InputValidation` 元素。

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>汇总

此示例演示所有部分如何组合在一起以形成工作策略。  使用以下示例：

1. 按照先决条件[入门](active-directory-b2c-get-started-custom.md)中的说明下载、配置并上传 TrustFrameworkBase.xml 和 TrustFrameworkExtensions.xml
1. 使用本部分中的示例内容创建 SignUporSignIn.xml 文件。
1. 更新 SignUporSignIn.xml，将 `yourtenant` 替换为你的 Azure AD B2C 租户名称。
1. 最后上传 SignUporSignIn.xml 策略文件。

此示例包含对 pin 密码的验证和对强密码的验证：

- 查找 `PINpassword`。 此 `InputValidation` 元素验证任意长度的 pin。  由于未在 `ClaimType` 内的 `InputValidationReference` 元素中引用，因此目前不会使用。 
- 查找 `PasswordValidation`。 此 `InputValidation` 元素验证密码是否为 8 到 16 个字符，以及是否包含 4 个数字、大写字母、小写字母或符号中的 3 个。  在 `ClaimType` 中引用。  因此，此规则在此策略中强制实施。

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <Restriction>
          <Pattern RegularExpression="^.*$" HelpText="" />
        </Restriction>
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[a-z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">^[A-Z]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">^[!@#$%^*()]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
