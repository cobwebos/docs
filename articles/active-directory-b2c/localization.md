---
title: Localization - Azure Active Directory B2C
description: 在 Azure Active Directory B2C 中指定自定义策略的 Localization 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681421"
---
# <a name="localization"></a>本地化

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

使用 **Localization** 元素可在用户旅程的策略中支持多个区域设置或语言。 使用策略中的本地化支持可以：

- 在策略中设置支持的语言的显式列表和选择默认语言。
- 提供特定于语言的字符串和集合。

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Localization** 元素包含以下属性：

| 特性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| 已启用 | 否 | 可能的值：`true` 或 `false`。 |

**Localization** 元素包含以下 XML 元素

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | 支持的语言列表。 |
| LocalizedResources | 0:n | 本地化资源列表。 |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 元素包含以下属性：

| 特性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| DefaultLanguage | 是 | 用作本地化资源默认值的语言。 |
| MergeBehavior | 否 | 与父策略中具有相同标识符的任何 ClaimType 合并在一起的值的枚举值。 覆盖基本策略中指定的声明时，请使用此属性。 可能的值：`Append`、`Prepend` 或 `ReplaceAll`。 `Append` 值指定应将现有数据集合追加到父策略中指定的集合的末尾。 `Prepend` 值指定应将现有数据集合添加到父策略中指定的集合的前面。 `ReplaceAll` 值指定应忽略父策略中定义的数据集合，改用当前策略中定义的数据。 |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | 显示符合 RFC 5646“用于标识语言的标记”中所述语言标记的内容。 |

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** 元素包含以下属性：

| 特性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ID | 是 | 用于唯一标识本地化资源的标识符。 |

**LocalizedResources** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | 在各种区域性中定义整个集合。 一个集合可以包含不同的项数，以及适用于各种区域性的不同字符串。 集合的示例包括声明类型中显示的枚举。 例如，在下拉列表中向用户显示国家/地区列表。 |
| LocalizedStrings | 0:n | 在各种区域性中定义所有字符串，但集合中出现的字符串除外。 |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | 支持的语言列表。 |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** 元素包含以下属性：

| 特性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ElementType | 是 | 引用策略文件中的 ClaimType 元素或用户界面元素。 |
| ElementId | 是 | 一个字符串，包含当 **ElementType** 设置为 ClaimType 时使用的 ClaimsSchema 节中已定义的声明类型的引用。 |
| TargetCollection | 是 | 目标集合。 |

**LocalizedCollection** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| 项 | 0:n | 定义可让用户在用户界面中为声明选择的可用选项，例如下拉列表中的值。 |

**Item** 元素包含以下属性：

| 特性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| Text | 是 | 应在用户界面中向用户显示的此选项的用户友好字符串。 |
| 值 | 是 | 与此选项关联的字符串声明值。 |
| SelectByDefault | 否 | 指示默认情况下是否应在 UI 中选择此选项。 可能的值：True 或 False。 |

以下示例演示了 **LocalizedCollections** 元素的用法。 其中包含两个 **LocalizedCollection** 元素，一个元素适用于英语区域设置，另一个元素适用于西班牙语区域设置。 这两个元素都设置了声明 `Gender` 的 **Restriction** 集合，以及适用于英语和西班牙语的项列表。

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

**LocalizedStrings** 元素包含以下元素：

| 元素 | 出现次数 | 说明 |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | 一个本地化字符串。 |

**LocalizedString** 元素包含以下属性：

| 特性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ElementType | 是 | 可能的值： [ClaimsProvider](#claimsprovider)、 [ClaimType](#claimtype)、 [ErrorMessage](#errormessage)、 [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype)、[谓词](#predicate)、 [InputValidation](#inputvalidation)或[UxElement](#uxelement)。   | 
| ElementId | 是 | 如果**ElementType**设置为`ClaimType`、 `Predicate`或`InputValidation`，则此元素包含对已在 ClaimsSchema 部分中定义的声明类型的引用。 |
| StringId | 是 | 如果 **ElementType** 设置为 `ClaimType`，此元素包含对声明类型的属性的引用。 可能的值：`DisplayName`、`AdminHelpText` 或 `PatternHelpText`。 `DisplayName` 值用于设置声明显示名称。 `AdminHelpText` 值用于设置声明用户的帮助文本名称。 `PatternHelpText` 值用于设置声明模式帮助文本。 如果 **ElementType** 设置为 `UxElement`，此元素包含对用户界面元素的属性的引用。 如果 **ElementType** 设置为 `ErrorMessage`，此元素指定错误消息的标识符。 有关 `UxElement` 标识符的完整列表，请参阅[本地化字符串 ID](localization-string-ids.md)。|

## <a name="elementtype"></a>ElementType

对声明类型、声明转换或策略中要本地化的用户界面元素的 ElementType 引用。

| 要本地化的元素 | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| 标识提供者名称 |`ClaimsProvider`| | ClaimsExchange 元素的 ID|
| 声明类型特性|`ClaimType`|声明类型的名称| 要本地化的声明的属性。 可能的值`AdminHelpText`： `DisplayName`、 `PatternHelpText`、和`UserHelpText`。|
|错误消息|`ErrorMessage`||错误消息的 ID |
|将本地化的字符串复制到声明中|`GetLocalizedStringsTra nsformationClaimType`||输出声明的名称|
|谓词用户消息|`Predicate`|谓词的名称| 要本地化的谓词的特性。 可能的值`HelpText`：。|
|谓词组用户消息|`InputValidation`|PredicateValidation 元素的 ID。|PredicateGroup 元素的 ID。 谓词组必须是 ElementId 中定义的谓词验证元素的子元素。|
|用户界面元素 |`UxElement` | | 要本地化的用户界面元素的 ID。|

## <a name="examples"></a>示例

### <a name="claimsprovider"></a>ClaimsProvider

ClaimsProvider 值用于本地化某个声明提供程序的显示名称。 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

下面的示例演示如何本地化声明提供程序的显示名称。

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

ClaimType 值用于本地化某个声明特性。 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

下面的示例演示如何本地化电子邮件声明类型的 DisplayName、UserHelpText 和 PatternHelpText 属性。

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

ErrorMessage 值用于本地化系统错误消息之一。 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

下面的示例演示如何本地化 UserMessageIfClaimsPrincipalAlreadyExists 错误消息。


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

GetLocalizedStringsTransformationClaimType 值用于将本地化的字符串复制到声明。 有关详细信息，请参阅[GetLocalizedStringsTransformation 声明转换](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

下面的示例演示如何本地化 GetLocalizedStringsTransformation 声明转换的输出声明。

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicate

谓词值用于本地化[谓词](predicates.md)错误消息之一。 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

下面的示例演示如何本地化谓词帮助文本。

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

InputValidation 值用于本地化某个[PredicateValidation](predicates.md)组错误消息。 

```xml
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

下面的示例演示如何本地化谓词验证组帮助文本。

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

UxElement 值用于本地化其中一个用户界面元素。 下面的示例演示如何本地化 "继续" 和 "取消" 按钮。

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>后续步骤

有关本地化示例，请参阅以下文章：

- [Azure Active Directory B2C 中的自定义策略的语言自定义](custom-policy-localization.md)
- [Azure Active Directory B2C 中的用户流的语言自定义](user-flow-language-customization.md)
