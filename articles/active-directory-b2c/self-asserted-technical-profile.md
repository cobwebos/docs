---
title: 定义采用 Azure Active Directory B2C 中自定义策略的自断言技术配置文件 | Microsoft Docs
description: 定义采用 Azure Active Directory B2C 中自定义策略的自断言技术配置文件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3c728660f1a77c02f1e4b5fdeb467a7dbba4e36a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512657"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定义采用 Azure Active Directory B2C 中自定义策略的自断言技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory (Azure AD) B2C 中用户需要提供输入的所有交互都属于自我断言技术配置文件。 例如，注册页面、登录页面或密码重置页面。

## <a name="protocol"></a>Protocol

“Protocol”  元素的“Name”  属性必须设置为 `Proprietary`。 “handler”  属性必须包含 Azure AD B2C 用来自断言的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的示例显示了电子邮件注册的自断言技术配置文件：

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>输入声明

在自断言技术配置文件中，你可以使用“InputClaims”  和“InputClaimsTransformations”  元素预填充自断言页面上出现的声明的值（输出声明）。 例如，在编辑配置文件策略中，用户旅程首先从 Azure AD B2C 目录服务读取用户配置文件，然后自断言技术配置文件使用用户配置文件中存储的用户数据设置输入声明。 这些声明是从用户配置文件中收集的，然后呈现给可以编辑现有数据的用户。

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>输出声明

“OutputClaims”  元素包含要呈现以从用户处收集数据的声明列表。 若要使用某些值预填充输出声明，请使用前面描述的输入声明。 另外，此元素还可能包含默认值。 “OutputClaims”  中的声明顺序将控制 Azure AD B2C 将声明呈现在屏幕上的顺序。 “DefaultValue”  属性只有在从未设置过声明时才会生效。 但是，如果之前在上一业务流程步骤中设置过，即使用户将值留空，默认值也不会生效。 若要强制使用默认值，请将“AlwaysUseDefaultValue”  属性设置为 `true`。 若要强制用户提供特定输出声明的值，请将“OutputClaims”  元素的“Required”  属性设置为 `true`。

“OutputClaims”  集合中的“ClaimType”  元素需要将“UserInputType”  元素设置为 Azure AD B2C 支持的任意用户输入类型，例如 `TextBox` 或 `DropdownSingleSelect`。 或者“OutputClaim”  元素必须设置“DefaultValue”  。  

“OutputClaimsTransformations”  元素可能包含用于修改输出声明或生成新声明的“OutputClaimsTransformation”  元素的一个集合。

下面的输出声明始终将设置为 `live.com`：

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>使用案例

关于输出声明，有四种场景：

- **从用户处收集输出声明** - 当你需要从用户处收集信息（如出生日期）时，应将声明添加到“OutputClaims”  集合。 呈现给用户的声明必须指定“UserInputType”  ，如 `TextBox` 或 `DropdownSingleSelect`。 如果自断言技术配置文件包含输出相同声明的验证技术配置文件，则 Azure AD B2C 不会将声明呈现给用户。 如果没有任何要呈现给用户的输出声明，Azure AD B2C 将跳过技术配置文件。
- **在输出声明中设置默认值** - 无需从用户处收集数据或从验证技术配置文件返回数据。 `LocalAccountSignUpWithLogonEmail` 自断言技术配置文件将“executed-SelfAsserted-Input”  声明设置为 `true`。
- **验证技术配置文件返回输出声明** - 你的技术配置文件可以调用返回某些声明的验证技术配置文件。 你需要发出声明并将其返回到用户旅程中的下一个业务流程步骤。 例如，当使用本地帐户登录时，名为 `SelfAsserted-LocalAccountSignin-Email` 的自断言技术配置文件会调用名为 `login-NonInteractive` 的验证技术配置文件。 此技术配置文件将验证用户凭据，并返回用户配置文件。 例如“userPrincipalName”、“displayName”、“givenName”和“surName”。
- **通过输出声明转换输出声明**

在以下示例中，`LocalAccountSignUpWithLogonEmail` 自断言技术配置文件演示了如何使用输出声明并将“executed-SelfAsserted-Input”  设置为 `true`。 `objectId`、`authenticationSource`、`newUser` 声明是 `AAD-UserWriteUsingLogonEmail` 验证技术配置文件的输出，不会显示给用户。

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>保存声明

如果“PersistedClaims”  元素不存在，则自断言技术配置文件不会将数据保存到 Azure AD B2C。 而是改为调用负责保留数据的验证技术配置文件。 例如，注册策略使用 `LocalAccountSignUpWithLogonEmail` 自断言技术配置文件来收集新用户配置文件。 `LocalAccountSignUpWithLogonEmail` 技术配置文件调用验证技术配置文件来在 Azure AD B2C 中创建帐户。

## <a name="validation-technical-profiles"></a>验证技术配置文件

验证技术配置文件用于验证部分或所有引用技术配置文件的输出声明。 验证技术配置文件的输入声明必须出现在自断言技术配置文件的输出声明中。 验证技术配置文件将验证用户输入，并可以向用户返回错误。 

验证技术配置文件可以是策略中的任何技术配置文件，例如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md) 技术配置文件。 在上一示例中，`LocalAccountSignUpWithLogonEmail` 技术配置文件会验证 signinName 是否存在于目录中。 如果不存在，验证技术配置文件会创建一个本地帐户，并返回 objectId、authenticationSource、newUser。 `SelfAsserted-LocalAccountSignin-Email` 技术配置文件调用 `login-NonInteractive` 验证技术配置文件来验证用户凭据。

此外，你也可以使用你的业务逻辑调用 REST API 技术配置文件，覆盖输入声明或通过进一步与企业业务线应用程序集成来丰富用户数据。 有关详细信息，请参阅[验证技术配置文件](validation-technical-profile.md)

## <a name="metadata"></a>元数据

| 特性 | 需要 | 描述 |
| --------- | -------- | ----------- |
| setting.showContinueButton | 否 | 显示“继续”按钮。 可能的值为 `true`（默认）或 `false` |
| setting.showCancelButton | 否 | 显示“取消”按钮。 可能的值为 `true`（默认）或 `false` |
| setting.operatingMode | 否 | 对于登录页面，此属性可控制用户名字段的行为，如输入验证和错误消息。 预期的值为 `Username` 或 `Email`。 |
| ContentDefinitionReferenceId | 是 | 与此技术配置文件关联的[内容定义](contentdefinitions.md)的标识符。 |
| EnforceEmailVerification | 否 | 对于注册或配置文件编辑，强制实施电子邮件验证。 可能的值为 `true`（默认）或 `false`。 | 
| setting.showSignupLink | 否 | 显示“注册”按钮。 可能的值为 `true`（默认）或 `false` |
| setting.retryLimit | 否 | 控制用户可以尝试提供数据的次数，所提供数据将根据验证技术配置文件进行检查。 例如，用户尝试注册已经存在的帐户，而且一直尝试，直到达到限制。
| SignUpTarget | 否 | 注册目标交换标识符。 当用户单击“注册”按钮时，Azure AD B2C 将执行指定的交换标识符。 |

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”  元素。













