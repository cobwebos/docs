---
title: 在 Azure Active Directory B2C 中使用自定义策略配置密码复杂性 | Microsoft Docs
description: 如何在 Azure Active Directory B2C 中使用自定义策略配置密码复杂性要求。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7fb90720b8ee3834a0451cc70b026099dadaf369
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682479"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略配置密码复杂性

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory (Azure AD) B2C 中，可配置由用户在创建帐户时提供的密码的复杂性要求。 默认情况下，Azure AD B2C 使用“强”密码。 本文介绍如何在[自定义策略](active-directory-b2c-overview-custom.md)中配置密码复杂性。 还有可能在[用户流](active-directory-b2c-reference-password-complexity.md)中配置密码复杂性。

## <a name="prerequisites"></a>必备组件

完成 [Active Directory B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。

## <a name="add-the-elements"></a>添加元素

1. 复制随初学者包下载的 *SignUpOrSignIn.xml* 文件，并将其命名为 *SingUpOrSignInPasswordComplexity.xml*。
2. 打开 *SingUpOrSignInPasswordComplexity.xml* 文件，并将 **PolicyId** 和 **PublicPolicyUri** 更改为新的策略名称。 例如，*B2C_1A_signup_signin_password_complexity*。
3. 添加标识符为 `newPassword` 和 `reenterPassword` 的以下 **ClaimType** 元素：

    ```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    ```

4. [谓词](predicates.md)具有 `IsLengthRange` 或 `MatchesRegex` 方法类型。 `MatchesRegex` 类型用于匹配正则表达式。 `IsLengthRange` 类型采用最小和最大字符串长度。 如果 **BuildingBlocks** 元素不具有以下 **Predicate** 元素，则向其添加 **Predicates** 元素：

    ```XML
    <Predicates>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

5. 每个 **InputValidation** 元素使用定义的 **Predicate** 元素进行构造。 此元素允许执行布尔聚合（类似于 `and` 和 `or`）。 如果 **BuildingBlocks** 元素不具有以下 **InputValidation** 元素，则向其添加 **InputValidations** 元素：

    ```XML
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
    </InputValidations>
    ```

6. 确保 **PolicyProfile** 技术配置文件包含下列元素：

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn"/>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName"/>
          <OutputClaim ClaimTypeReferenceId="givenName"/>
          <OutputClaim ClaimTypeReferenceId="surname"/>
          <OutputClaim ClaimTypeReferenceId="email"/>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub"/>
      </TechnicalProfile>
    </RelyingParty>
    ```

7. 保存策略文件。

## <a name="test-your-policy"></a>测试策略

在 Azure AD B2C 中测试应用程序时，可以使 Azure AD B2C 令牌返回到 `https://jwt.ms` 以便能够在其中查看声明，这可能很有用处。

### <a name="upload-the-files"></a>上传文件

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择“标识体验框架”。
5. 在“自定义策略”页上，单击“上传策略”。
6. 选择“覆盖策略(若存在)”，然后搜索并选择 *SingUpOrSignInPasswordComplexity.xml* 文件。
7. 单击“上传” 。

### <a name="run-the-policy"></a>运行策略

1. 打开你更改的策略。 例如，*B2C_1A_signup_signin_password_complexity*。
2. 对于“应用程序”，选择你之前注册的应用程序。 若要查看令牌，“回复 URL”应当显示 `https://jwt.ms`。
3. 单击“立即运行”。
4. 选择“立即注册”，输入电子邮件地址，并输入新密码。 密码限制中会显示相关指导。 完成输入用户信息，然后单击“创建”。 应看到返回的令牌的内容。

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure Active Directory B2C 中使用自定义策略配置密码更改](active-directory-b2c-reference-password-change-custom.md)。


