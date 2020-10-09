---
title: 定义自定义策略中的自断言技术配置文件
titleSuffix: Azure AD B2C
description: 定义采用 Azure Active Directory B2C 中自定义策略的自断言技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84e92cbac064106ca95277288eb773e311798930
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203446"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定义采用 Azure Active Directory B2C 中自定义策略的自断言技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

在 Azure Active Directory B2C (Azure AD B2C) 中用户需要提供输入的所有交互都属于自我断言技术配置文件。 例如，注册页面、登录页面或密码重置页面。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 “handler”  属性必须包含 Azure AD B2C 用来自断言的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的示例显示了电子邮件注册的自断言技术配置文件：

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>输入声明

在自断言技术配置文件中，你可以使用“InputClaims”和“InputClaimsTransformations”元素预填充自断言页面上出现的声明的值（显示声明）   。 例如，在编辑配置文件策略中，用户旅程首先从 Azure AD B2C 目录服务读取用户配置文件，然后自断言技术配置文件使用用户配置文件中存储的用户数据设置输入声明。 这些声明是从用户配置文件中收集的，然后呈现给可以编辑现有数据的用户。

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>显示声明

此显示声明功能目前以预览版提供  。

“DisplayClaims”元素包含要呈现在屏幕上用于从用户处收集数据的声明列表  。 若要预填充显示声明的值，请使用前面介绍的输入声明。 另外，此元素还可能包含默认值。

“DisplayClaims”中的声明顺序指定 Azure AD B2C 在屏幕上呈现声明的顺序  。 若要强制用户提供特定声明的值，请将“DisplayClaim”元素的“Required”属性设置为 `true`。

“DisplayClaims”集合中的“ClaimType”元素需要将“UserInputType”元素设置为 Azure AD B2C 支持的任意用户输入类型    。 例如，`TextBox` 或 `DropdownSingleSelect`。

### <a name="add-a-reference-to-a-displaycontrol"></a>添加对 DisplayControl 的引用

在显示声明集合中，可以包含对已创建的 [DisplayControl](display-controls.md) 的引用。 显示控件是一个具有特殊功能的用户界面元素，可以与 Azure AD B2C 后端服务进行交互。 它允许用户在页面上执行某些操作，这些操作在后端调用验证技术配置文件。 例如，验证电子邮件地址、电话号码或客户会员号。

以下示例 `TechnicalProfile` 阐释了如何配合使用显示声明和显示控件。

* 第一个显示声明引用收集和验证电子邮件地址的 `emailVerificationControl` 显示控件。
* 第五个显示声明引用收集和验证电话号码的 `phoneVerificationControl` 显示控件。
* 其他显示声明为 ClaimTypes，要从用户处收集。

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

如上所述，引用显示控件的显示声明可以自己运行验证，例如，验证电子邮件地址。 此外，自断言页面支持在执行下一个业务流程步骤之前使用验证技术配置文件来验证整个页面，包括任何用户输入（声明类型或显示控件）。

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>谨慎细心地配合使用显示声明和输出声明

如果在自断言技术配置文件中指定一个或多个“DisplayClaim”元素，则必须对要从用户处收集的、要在屏幕上显示的每个声明使用 DisplayClaim   。 至少包含一个显示声明的自断言技术配置文件中不显示任何输出声明。

请思考以下示例，其中 `age` 声明被定义为基本策略中的输出声明  。 在将任何显示声明添加到自断言技术配置文件之前，屏幕上会显示 `age` 声明，用于从用户处收集数据：

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

如果继承了该基本策略的叶策略随后指定 `officeNumber` 作为显示声明  ：

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

屏幕上不再向用户显示基本策略中的 `age` 声明 - 它实际上处于“隐藏”状态。 若要显示 `age` 声明并从用户处收集“年龄”值，需要将 `age` 添加到 DisplayClaim  。

## <a name="output-claims"></a>输出声明

“OutputClaims”元素包含要返回到下一个业务流程步骤的的声明列表  。 “DefaultValue”属性只有在从未设置过声明的情况下才会生效  。 如果在上一业务流程步骤中设置过，即使用户将值留空，默认值也不会生效。 若要强制使用默认值，请将“AlwaysUseDefaultValue”  属性设置为 `true`。

出于安全原因，密码声明值（`UserInputType` 设置为 `Password`）仅可用于自断言技术配置文件的验证技术配置文件。 在下一个业务流程步骤中，不能使用密码声明。 

> [!NOTE]
> 在以前的 Identity Experience Framework (IEF) 版本中，输出声明用于从用户处收集数据。 若要从用户处收集数据，请改用“DisplayClaims”集合  。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="when-you-should-use-output-claims"></a>何时应使用输出声明

在自断言技术配置文件中，输出声明集合将声明返回到下一个业务流程步骤。

在以下情况下使用输出声明：

- 声明由输出声明转换输出  。
- 在输出声明中设置默认值无需从用户处收集数据或从验证技术配置文件返回数据  。 `LocalAccountSignUpWithLogonEmail` 自断言技术配置文件将“executed-SelfAsserted-Input”  声明设置为 `true`。
- **验证技术配置文件返回输出声明** - 你的技术配置文件可以调用返回某些声明的验证技术配置文件。 你需要发出声明并将其返回到用户旅程中的下一个业务流程步骤。 例如，当使用本地帐户登录时，名为 `SelfAsserted-LocalAccountSignin-Email` 的自断言技术配置文件会调用名为 `login-NonInteractive` 的验证技术配置文件。 此技术配置文件将验证用户凭据，并返回用户配置文件。 例如“userPrincipalName”、“displayName”、“givenName”和“surName”。
- **显示控件返回输出声明** - 技术配置文件可能引用[显示控件](display-controls.md)。 显示控件返回某些声明，如已验证的电子邮件地址。 你需要发出声明并将其返回到用户旅程中的下一个业务流程步骤。 此显示控件功能目前以预览版提供  。

以下示例演示如何使用同时包含显示声明和输出声明的自断言技术配置文件。

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>保存声明

不使用 PersistedClaims 元素。 自断言技术配置文件不会将数据持久保存到 Azure AD B2C。 而是改为调用负责保留数据的验证技术配置文件。 例如，注册策略使用 `LocalAccountSignUpWithLogonEmail` 自断言技术配置文件来收集新用户配置文件。 `LocalAccountSignUpWithLogonEmail` 技术配置文件调用验证技术配置文件来在 Azure AD B2C 中创建帐户。

## <a name="validation-technical-profiles"></a>验证技术配置文件

验证技术配置文件用于验证部分或所有引用技术配置文件的输出声明。 验证技术配置文件的输入声明必须出现在自断言技术配置文件的输出声明中。 验证技术配置文件将验证用户输入，并可以向用户返回错误。

验证技术配置文件可以是策略中的任何技术配置文件，例如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md) 技术配置文件。 在上一示例中，`LocalAccountSignUpWithLogonEmail` 技术配置文件会验证 signinName 是否存在于目录中。 如果不存在，验证技术配置文件会创建一个本地帐户，并返回 objectId、authenticationSource、newUser。 `SelfAsserted-LocalAccountSignin-Email` 技术配置文件调用 `login-NonInteractive` 验证技术配置文件来验证用户凭据。

此外，你也可以使用你的业务逻辑调用 REST API 技术配置文件，覆盖输入声明或通过进一步与企业业务线应用程序集成来丰富用户数据。 有关详细信息，请参阅[验证技术配置文件](validation-technical-profile.md)

## <a name="metadata"></a>元数据

| Attribute | 必选 | 说明 |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| 否 | 对于登录页面，此属性可控制用户名字段的行为，如输入验证和错误消息。 预期的值为 `Username` 或 `Email`。  |
| AllowGenerationOfClaimsWithNullValues| 否| 允许生成值为 NULL 的声明。 例如，在用户未选中复选框的情况下。|
| ContentDefinitionReferenceId | 是 | 与此技术配置文件关联的[内容定义](contentdefinitions.md)的标识符。 |
| EnforceEmailVerification | 否 | 对于注册或配置文件编辑，强制实施电子邮件验证。 可能的值为 `true`（默认）或 `false`。 |
| setting.retryLimit | 否 | 控制用户可以尝试提供数据的次数，所提供数据将根据验证技术配置文件进行检查。 例如，用户尝试注册已经存在的帐户，而且一直尝试，直到达到限制。
| SignUpTarget <sup>1</sup>| 否 | 注册目标交换标识符。 当用户单击“注册”按钮时，Azure AD B2C 将执行指定的交换标识符。 |
| setting.showCancelButton | 否 | 显示“取消”按钮。 可能的值为 `true`（默认）或 `false` |
| setting.showContinueButton | 否 | 显示“继续”按钮。 可能的值为 `true`（默认）或 `false` |
| setting.showSignupLink <sup>2</sup>| 否 | 显示“注册”按钮。 可能的值为 `true`（默认）或 `false` |
| setting.forgotPasswordLinkLocation <sup>2</sup>| 否| 显示“忘记密码”链接。 可能的值：`AfterInput`（默认值）链接显示在页面底部，或者 `None`（删除“忘记密码”链接）。|
| setting.enableRememberMe <sup>2</sup>| 否| 显示 " [使我保持登录](custom-policy-keep-me-signed-in.md) " 复选框。 可能的值：`true` 或 `false`（默认值）。 |
| IncludeClaimResolvingInClaimsHandling  | 否 | 对于输入和输出声明，指定[声明解析](claim-resolver-overview.md)是否包含在技术配置文件中。 可能的值：`true` 或 `false` （默认值）。 若要使用技术配置文件中的声明解析程序，请将此项设为 `true`。 |

说明：
1. 可用于内容定义 [DataUri](contentdefinitions.md#datauri) 类型 `unifiedssp` 或 `unifiedssd`。
1. 可用于内容定义 [DataUri](contentdefinitions.md#datauri) 类型 `unifiedssp` 或 `unifiedssd`。 [页面布局版本](page-layout.md) 1.1.0 及更高版本。

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”  元素。
