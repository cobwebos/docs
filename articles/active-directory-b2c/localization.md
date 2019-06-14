---
title: Localization - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 Localization 元素。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a02983c5019870e8b17db48184b2f238a82f8a40
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510582"
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

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| Enabled | 否 | 可能的值：`true` 或 `false`。 |

**Localization** 元素包含以下 XML 元素

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | 支持的语言列表。 | 
| LocalizedResources | 0:n | 本地化资源列表。 |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| DefaultLanguage | 是 | 用作本地化资源默认值的语言。 |
| MergeBehavior | 否 | 与父策略中具有相同标识符的任何 ClaimType 合并在一起的值的枚举值。 覆盖基本策略中指定的声明时，请使用此属性。 可能的值：`Append`、`Prepend` 或 `ReplaceAll`。 `Append` 值指定应将现有数据集合追加到父策略中指定的集合的末尾。 `Prepend` 值指定应将现有数据集合添加到父策略中指定的集合的前面。 `ReplaceAll` 值指定应忽略父策略中定义的数据集合，改用当前策略中定义的数据。 |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | 显示符合 RFC 5646“用于标识语言的标记”中所述语言标记的内容。 | 

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 用于唯一标识本地化资源的标识符。 |

**LocalizedResources** 元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | 在各种区域性中定义整个集合。 一个集合可以包含不同的项数，以及适用于各种区域性的不同字符串。 集合的示例包括声明类型中显示的枚举。 例如，在下拉列表中向用户显示国家/地区列表。 |
| LocalizedStrings | 0:n | 在各种区域性中定义所有字符串，但集合中出现的字符串除外。 |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** 元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | 支持的语言列表。 |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** 元素包含以下属性：

| 特性 | 需要 | 描述 |
| --------- | -------- | ----------- |
| ElementType | 是 | 引用策略文件中的 ClaimType 元素或用户界面元素。 |
| ElementId | 是 | 一个字符串，包含当 **ElementType** 设置为 ClaimType 时使用的 ClaimsSchema 节中已定义的声明类型的引用。 |
| TargetCollection | 是 | 目标集合。 |

**LocalizedCollection** 元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| Item | 0:n | 定义可让用户在用户界面中为声明选择的可用选项，例如下拉列表中的值。 |

**Item** 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| Text | 是 | 应在用户界面中向用户显示的此选项的用户友好字符串。 |
| 值 | 是 | 与此选项关联的字符串声明值。 |

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

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | 一个本地化字符串。 |

**LocalizedString** 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| ElementType | 是 | 对策略中声明类型元素或用户界面元素的引用。 可能的值：`ClaimType`、`UxElement`、`ErrorMessage`、`Predicate` 或 `ClaimType` 值用于本地化 StringId 中指定的某个声明属性。 `UxElement` 值用于本地化 StringId 中指定的某个用户界面元素。 `ErrorMessage` 值用于本地化 StringId 中指定的某个系统错误消息。 `Predicate` 值用于本地化 StringId 中指定的某个 [Predicate](predicates.md) 错误消息。 `InputValidation` 值用于本地化 StringId 中指定的某个 [PredicateValidation](predicates.md) 组错误消息。 |
| ElementId | 是 | 如果 **ElementType** 设置为 `ClaimType`、`Predicate` 或 `InputValidation`，此元素包含对 ClaimsSchema 节中已定义的声明类型的引用。 | 
| StringId | 是 | 如果 **ElementType** 设置为 `ClaimType`，此元素包含对声明类型的属性的引用。 可能的值：`DisplayName`、`AdminHelpText` 或 `PatternHelpText`。 `DisplayName` 值用于设置声明显示名称。 `AdminHelpText` 值用于设置声明用户的帮助文本名称。 `PatternHelpText` 值用于设置声明模式帮助文本。 如果 **ElementType** 设置为 `UxElement`，此元素包含对用户界面元素的属性的引用。 如果 **ElementType** 设置为 `ErrorMessage`，此元素指定错误消息的标识符。 有关 `UxElement` 标识符的完整列表，请参阅[本地化字符串 ID](localization-string-ids.md)。|


以下示例演示了一个本地化的注册页。 前三个 **LocalizedString** 值设置声明属性。 第三个值更改继续按钮的值。 最后一个值更改错误消息。

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

以下示例演示了 ID 为 `IsLengthBetween8And64` 的 **Predicate** 的本地化 **UserHelpText**。 另外还演示了 ID 为 `CharacterClasses` 的 **PredicateGroup**，以及 ID 为 `StrongPassword` 的 **PredicateValidation** 的本地化 **UserHelpText**。

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>              
```

## <a name="set-up-localization"></a>设置本地化

本文将介绍如何在用户旅程的策略中支持多个区域设置或语言。 本地化需要执行三个步骤：设置受支持语言的显式列表，提供特定于语言的字符串和集合，以及编辑页面的 ContentDefinition。

### <a name="set-up-the-explicit-list-of-supported-languages"></a>设置受支持语言的显式列表

在 **BuildingBlocks** 元素下，添加包含受支持语言列表的 **Localization** 元素。 以下示例演示如何为英语（默认）和西班牙语定义本地化支持：

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

### <a name="provide-language-specific-strings-and-collections"></a>提供特定于语言的字符串和集合 

在 **Localization** 元素中 **SupportedLanguages** 元素的结束标记后面添加 **LocalizedResources** 元素。 为每个页面（内容定义）和要支持的任何语言添加 **LocalizedResources** 元素。 若要为英语、西班牙语和法语自定义统一注册或登录页面、注册和多重身份验证 (MFA) 页面，请添加以下 **LocalizedResources** 元素。  
- 英语统一注册或登录页面：`<LocalizedResources Id="api.signuporsignin.en">`
- 西班牙语统一注册或登录页面：`<LocalizedResources Id="api.signuporsignin.es">`
- 法语统一注册或登录页面：`<LocalizedResources Id="api.signuporsignin.fr">` 
- 英语注册：`<LocalizedResources Id="api.localaccountsignup.en">`
- 西班牙语注册：`<LocalizedResources Id="api.localaccountsignup.es">`
- 法语注册：`<LocalizedResources Id="api.localaccountsignup.fr">`
- 英语 MFA：`<LocalizedResources Id="api.phonefactor.en">`
- 西班牙语 MFA：`<LocalizedResources Id="api.phonefactor.es">`
- 法语 MFA：`<LocalizedResources Id="api.phonefactor.fr">`

每个 **LocalizedResources** 元素包含所有必需的 **LocalizedStrings** 元素（其中包含多个 **LocalizedString** 元素）和 **LocalizedCollections** 元素（其中包含多个 **LocalizedCollection** 元素）。  以下示例添加注册页面英语本地化： 

请注意:此示例引用 `Gender` 和 `City` 声明类型。 若要使用此示例，请务必定义这些声明。 有关详细信息，请参阅 [ClaimsSchema](claimsschema.md)。

```XML
<LocalizedResources Id="api.localaccountsignup.en">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="New York" Value="NY" />
      <Item Text="Paris" Value="Paris" />
      <Item Text="London" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

西班牙语注册页面本地化。

```XML
<LocalizedResources Id="api.localaccountsignup.es">

 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
   <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
      <Item Text="Nueva York" Value="NY" />
      <Item Text="París" Value="Paris" />
      <Item Text="Londres" Value="London" />
    </LocalizedCollection>
  </LocalizedCollections>

  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

### <a name="edit-the-contentdefinition-for-the-page"></a>编辑页面的 ContentDefinition 

对于要本地化的每个页面，请在 **ContentDefinition** 中指定要查找的语言代码。

在以下示例中，英语 (en) 和西班牙语 (es) 自定义字符串已添加到注册页面。 每个 **LocalizedResourcesReference** 的 **LocalizedResourcesReferenceId** 与其区域设置相同，但可将任何字符串用作标识符。 对于每个语言和页面组合，请指向前面创建的相应 **LocalizedResources**。

```XML
<ContentDefinition Id="api.localaccountsignup">
...
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
  </LocalizedResourcesReferences>
</ContentDefinition>
```

以下示例演示了最终的 XML：

```XML
<BuildingBlocks>
  <ContentDefinitions>
    <ContentDefinition Id="api.localaccountsignup">
      <!-- Other content definitions elements... -->
      <LocalizedResourcesReferences MergeBehavior="Prepend">
         <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
         <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
      </LocalizedResourcesReferences>
    </ContentDefinition>
    <!-- More content definitions... -->
  </ContentDefinitions>

  <Localization Enabled="true">

    <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
      <!-- More supported language... -->
    </SupportedLanguages>

    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Female" Value="F" />
          <Item Text="Male" Value="M" />
          <!-- More items... -->
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="New York" Value="NY" />
          <Item Text="Paris" Value="Paris" />
          <Item Text="London" Value="London" />
        </LocalizedCollection>
        <!-- More localized collections... -->
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
       <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
        <!-- More localized strings... -->
      </LocalizedStrings>
    </LocalizedResources>

    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
       <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
          <Item Text="Femenino" Value="F" />
          <Item Text="Masculino" Value="M" />
        </LocalizedCollection>
        <LocalizedCollection ElementType="ClaimType" ElementId="City" TargetCollection="Restriction">
          <Item Text="Nueva York" Value="NY" />
          <Item Text="París" Value="Paris" />
          <Item Text="Londres" Value="London" />
        </LocalizedCollection>
      </LocalizedCollections>
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Dirección de correo electrónico</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Introduzca una dirección de correo electrónico.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_continue">Crear</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
    <!-- More localized resources... -->
  </Localization>
</BuildingBlocks>
```




