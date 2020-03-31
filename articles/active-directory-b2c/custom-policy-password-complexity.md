---
title: 使用自定义策略配置密码复杂性
titleSuffix: Azure AD B2C
description: 如何在 Azure Active Directory B2C 中使用自定义策略配置密码复杂性要求。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b16790e288f6569f08ce14e5a7c751bbd8083faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138428"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略配置密码复杂性

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure 活动目录 B2C（Azure AD B2C）中，您可以配置用户在创建帐户时提供的密码的复杂性要求。 默认情况下，Azure AD B2C 使用“强”**** 密码。 本文介绍如何在[自定义策略](custom-policy-overview.md)中配置密码复杂性。 还有可能在[用户流](user-flow-password-complexity.md)中配置密码复杂性。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](custom-policy-get-started.md)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。


## <a name="add-the-elements"></a>添加元素

要配置密码复杂性，请用引用[谓词验证](predicates.md#predicatevalidations)来覆盖`newPassword`和`reenterPassword`[声明类型](claimsschema.md)。 谓词验证元素对一组谓词进行分组，以形成可应用于声明类型的用户输入验证。 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>.

1. 搜索 BuildingBlocks[](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到[声明架构](claimsschema.md)元素。 如果该元素不存在，请添加该元素。
1. 将`newPassword`和`reenterPassword`声明添加到**声明架构**元素。

    ```XML
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [谓词](predicates.md)定义基本验证以检查声明类型的值并返回 true 或 false。 验证通过使用指定的方法元素和一组与方法相关的参数来完成。 在`</ClaimsSchema>`元素关闭后立即将以下谓词添加到**构建块**元素：

    ```XML
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. 在`</Predicates>`元素关闭后立即将以下谓词验证添加到**构建块**元素：

    ```XML
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

1. 以下技术配置文件是[活动目录技术配置文件](active-directory-technical-profile.md)，用于读取数据并将其写入 Azure 活动目录。 覆盖扩展文件中的这些技术配置文件。 用于`PersistedClaims`禁用强密码策略。 找到 **ClaimsProviders** 元素。  添加以下声明提供程序，如下所示：

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
          <PersistedClaims>
            <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
          </PersistedClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. 保存策略文件。

## <a name="test-your-policy"></a>测试策略

### <a name="upload-the-files"></a>上传文件

1. 登录到 Azure[门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录****。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
4. 选择**身份体验框架**。
5. 在“自定义策略”页上，单击“上传策略”****。
6. 选择 **"覆盖策略（如果存在），** 然后搜索并选择*TrustFramework 扩展.xml*文件。
7. 单击 **“上载”**。

### <a name="run-the-policy"></a>运行策略

1. 打开注册或登录策略。 例如，*B2C_1A_signup_signin*。
2. 对于“应用程序”****，选择你之前注册的应用程序。 若要查看令牌，“回复 URL”应当显示 `https://jwt.ms`****。
3. 单击"**立即运行**"。
4. 选择“立即注册”，输入电子邮件地址，并输入新密码****。 密码限制中会显示相关指导。 完成输入用户信息，然后单击“创建”****。 应看到返回的令牌的内容。

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure Active Directory B2C 中使用自定义策略配置密码更改](custom-policy-password-change.md)。
- 详细了解 IEF 引用中的[谓词](predicates.md)和[谓词验证](predicates.md#predicatevalidations)元素。
