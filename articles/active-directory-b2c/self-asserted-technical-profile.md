---
title: 在自定义策略中定义自断言技术配置文件
titleSuffix: Azure AD B2C
description: 定义采用 Azure Active Directory B2C 中自定义策略的自断言技术配置文件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 37d895e2514d01bdbe73f42e1ba5ea5e4c46bfa1
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2020
ms.locfileid: "77372857"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定义采用 Azure Active Directory B2C 中自定义策略的自断言技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

需要用户提供输入的 Azure Active Directory B2C （Azure AD B2C）中的所有交互均为自断言技术配置文件。 例如，注册页面、登录页面或密码重置页面。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 “handler”属性必须包含 Azure AD B2C 用来自断言的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的示例显示了电子邮件注册的自断言技术配置文件：

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>输入声明

在自断言技术配置文件中，可以使用**InputClaims**和**InputClaimsTransformations**元素来预填充出现在自断言页面（显示声明）上的声明的值。 例如，在编辑配置文件策略中，用户旅程首先从 Azure AD B2C 目录服务读取用户配置文件，然后自断言技术配置文件使用用户配置文件中存储的用户数据设置输入声明。 这些声明是从用户配置文件中收集的，然后呈现给可以编辑现有数据的用户。

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

## <a name="display-claims"></a>显示声明

显示声明功能目前处于**预览阶段**。

**DisplayClaims**元素包含用于从用户收集数据的声明的列表。 若要预填充输出声明的值，请使用前面介绍的输入声明。 另外，此元素还可能包含默认值。

**DisplayClaims**中声明的顺序指定 Azure AD B2C 在屏幕上呈现声明的顺序。 若要强制用户为特定声明提供值，请将**DisplayClaim**元素的**Required**特性设置为 `true`。

**DisplayClaims**集合中的**ClaimType**元素需要将**UserInputType**元素设置为 Azure AD B2C 支持的任何用户输入类型。 例如，`TextBox` 或 `DropdownSingleSelect`。

### <a name="add-a-reference-to-a-displaycontrol"></a>添加对控件的引用

在 "显示声明" 集合中，可以包含对已创建的显示[控件](display-controls.md)的引用。 显示控件是一个具有特殊功能并与 Azure AD B2C 后端服务进行交互的用户界面元素。 它允许用户在页面上执行在后端调用验证技术配置文件的操作。 例如，验证电子邮件地址、电话号码或客户会员号。

下面的示例 `TechnicalProfile` 阐释了如何将显示声明用于显示控件。

* 第一个显示声明将对收集和验证电子邮件地址的 `emailVerificationControl` 显示控件进行引用。
* 第五个显示声明将对收集和验证电话号码的 `phoneVerificationControl` 显示控件进行引用。
* 其他显示声明是从用户收集的 ClaimTypes。

```XML
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

如前所述，引用显示控件的显示声明可能会运行其自己的验证，例如，验证电子邮件地址。 此外，自断言页面支持使用验证技术配置文件来验证整个页面，包括任何用户输入（声明类型或显示控件），然后再继续执行下一个业务流程步骤。

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>仔细组合显示声明和输出声明的使用情况

如果在自断言技术配置文件中指定一个或多个**DisplayClaim**元素，则必须对要在屏幕上显示并从用户收集的*每个*声明使用 DisplayClaim。 自断言技术配置文件中不显示任何输出声明，其中至少包含一个显示声明。

请考虑以下示例，其中 `age` 声明在基本策略中定义为**输出**声明。 在将任何显示声明添加到自断言技术配置文件之前，将在屏幕上显示 `age` 声明，以便从用户收集数据：

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

如果继承该基准的叶策略随后将 `officeNumber` 指定为**显示**声明：

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

基本策略中的 `age` 声明在屏幕上不再向用户显示-它实际上是 "隐藏"。 若要显示 `age` 声明并从用户收集 age 值，你必须添加一个 `age` **DisplayClaim**。

## <a name="output-claims"></a>输出声明

**OutputClaims**元素包含要返回到下一个业务流程步骤的声明列表。 **DefaultValue**特性仅在从未设置该声明时才会生效。 如果在上一个业务流程步骤中设置了此值，则即使用户将值保留为空，默认值也不会生效。 若要强制使用默认值，请将“AlwaysUseDefaultValue”属性设置为 `true`。

> [!NOTE]
> 在以前版本的标识体验框架（IEF）中，输出声明用于从用户那里收集数据。 若要从用户那里收集数据，请改用**DisplayClaims**集合。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

### <a name="when-you-should-use-output-claims"></a>何时应使用输出声明

在自断言技术配置文件中，输出声明集合将声明返回到下一个业务流程步骤。

在以下情况时，应使用输出声明：

- **声明由输出声明转换输出**。
- **在输出声明中设置默认值**，而不从用户那里收集数据或返回验证技术配置文件中的数据。 `LocalAccountSignUpWithLogonEmail` 自断言技术配置文件将“executed-SelfAsserted-Input”声明设置为 `true`。
- **验证技术配置文件返回输出声明** - 你的技术配置文件可以调用返回某些声明的验证技术配置文件。 你需要发出声明并将其返回到用户旅程中的下一个业务流程步骤。 例如，当使用本地帐户登录时，名为 `SelfAsserted-LocalAccountSignin-Email` 的自断言技术配置文件会调用名为 `login-NonInteractive` 的验证技术配置文件。 此技术配置文件将验证用户凭据，并返回用户配置文件。 例如“userPrincipalName”、“displayName”、“givenName”和“surName”。
- **显示控件返回输出声明**-技术配置文件可能具有对[显示控件](display-controls.md)的引用。 显示控件返回一些声明，如已验证的电子邮件地址。 你需要发出声明并将其返回到用户旅程中的下一个业务流程步骤。 显示控件功能目前处于**预览阶段**。

下面的示例演示如何使用自断言技术配置文件，该配置文件使用显示声明和输出声明。

```XML
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

如果“PersistedClaims”元素不存在，则自断言技术配置文件不会将数据保存到 Azure AD B2C。 而是改为调用负责保留数据的验证技术配置文件。 例如，注册策略使用 `LocalAccountSignUpWithLogonEmail` 自断言技术配置文件来收集新用户配置文件。 `LocalAccountSignUpWithLogonEmail` 技术配置文件调用验证技术配置文件来在 Azure AD B2C 中创建帐户。

## <a name="validation-technical-profiles"></a>验证技术配置文件

验证技术配置文件用于验证部分或所有引用技术配置文件的输出声明。 验证技术配置文件的输入声明必须出现在自断言技术配置文件的输出声明中。 验证技术配置文件将验证用户输入，并可以向用户返回错误。

验证技术配置文件可以是策略中的任何技术配置文件，例如 [Azure Active Directory](active-directory-technical-profile.md) 或 [REST API](restful-technical-profile.md) 技术配置文件。 在上一示例中，`LocalAccountSignUpWithLogonEmail` 技术配置文件会验证 signinName 是否存在于目录中。 如果不存在，验证技术配置文件会创建一个本地帐户，并返回 objectId、authenticationSource、newUser。 `SelfAsserted-LocalAccountSignin-Email` 技术配置文件调用 `login-NonInteractive` 验证技术配置文件来验证用户凭据。

此外，你也可以使用你的业务逻辑调用 REST API 技术配置文件，覆盖输入声明或通过进一步与企业业务线应用程序集成来丰富用户数据。 有关详细信息，请参阅[验证技术配置文件](validation-technical-profile.md)

## <a name="metadata"></a>元数据

| 属性 | 必需 | 说明 |
| --------- | -------- | ----------- |
| 设置。 operatingMode <sup>1</sup>| 是 | 对于登录页面，此属性可控制用户名字段的行为，如输入验证和错误消息。 预期的值为 `Username` 或 `Email`。  |
| AllowGenerationOfClaimsWithNullValues| 是| 允许生成 null 值声明。 例如，如果用户未选中复选框，则为。|
| ContentDefinitionReferenceId | 是 | 与此技术配置文件关联的[内容定义](contentdefinitions.md)的标识符。 |
| EnforceEmailVerification | 是 | 对于注册或配置文件编辑，强制实施电子邮件验证。 可能的值为 `true`（默认）或 `false`。 |
| setting.retryLimit | 是 | 控制用户可以尝试提供数据的次数，所提供数据将根据验证技术配置文件进行检查。 例如，用户尝试注册已经存在的帐户，而且一直尝试，直到达到限制。
| SignUpTarget <sup>1</sup>| 是 | 注册目标交换标识符。 当用户单击“注册”按钮时，Azure AD B2C 将执行指定的交换标识符。 |
| setting.showCancelButton | 是 | 显示“取消”按钮。 可能的值为 `true`（默认）或 `false` |
| setting.showContinueButton | 是 | 显示“继续”按钮。 可能的值为 `true`（默认）或 `false` |
| 设置。 showSignupLink <sup>2</sup>| 是 | 显示“注册”按钮。 可能的值为 `true`（默认）或 `false` |
| 设置。 forgotPasswordLinkLocation <sup>2</sup>| 是| 显示 "忘记密码" 链接。 可能的值： `AfterInput` （默认值）该链接显示在页面底部，或者 `None` 删除 "忘记密码" 链接。| 
| IncludeClaimResolvingInClaimsHandling  | 是 | 对于输入和输出声明，指定技术配置文件中是否包含[声明解析](claim-resolver-overview.md)。 可能的值： `true`或 `false` （默认值）。 如果要使用技术配置文件中的声明解析程序，请将此项设置为 `true`。 |

注：
1. 可用于内容定义[DataUri](contentdefinitions.md#datauri)类型的 `unifiedssp`或 `unifiedssd`。
1. 可用于内容定义[DataUri](contentdefinitions.md#datauri)类型的 `unifiedssp`或 `unifiedssd`。 [页面布局版本](page-layout.md)1.1.0 及更高版本。

## <a name="cryptographic-keys"></a>加密密钥

不使用“CryptographicKeys”元素。
