---
title: Predicates 和 PredicateValidations - Azure Active Directory B2C | Microsoft Docs
description: 针对 Azure Active Directory B2C 标识体验框架架构的社交帐户声明转换示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 06879164c6f72891b734da077c667c6f90448fe4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512971"
---
# <a name="predicates-and-predicatevalidations"></a>Predicates 和 PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Predicates  和 PredicateValidations  元素可用于执行验证过程，以确保向 Azure Active Directory (Azure AD) B2C 租户仅输入正确格式的数据。  

以下图示显示了元素之间的关系：  

![谓词](./media/predicates/predicates.png)

## <a name="predicates"></a>谓词  

Predicate  元素定义基本验证，用以检查声明类型的值并返回 `true` 或 `false`。 可通过使用指定的 Method  元素和一组与该方法相关的 Parameter  元素来完成验证。 例如，谓词可以检查字符串声明值的长度是否在指定的最小和最大参数范围内，或者字符串声明值是否包含字符集。 如果检查失败，UserHelpText  元素将为用户提供一条错误消息。 UserHelpText  元素的值可以使用[语言自定义](localization.md)进行本地化。

Predicates  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| Predicate | 1:n | 谓词列表。 | 

Predicate  元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 用于谓词的标识符。 其他元素可以在策略中使用此标识符。 |
| 方法 | 是 | 用于验证的方法类型。 可能的值：IsLengthRange、MatchesRegex、IncludesCharacters 或 IsDateRange     。 IsLengthRange  值检查字符串声明值的长度是否在指定的最小和最大参数的范围内。 MatchesRegex  值检查字符串声明值是否与正则表达式相匹配。 IncludesCharacters  值检查字符串声明值是否包含字符集。 IsDateRange  值检查日期声明值是否在指定的最小和最大参数范围内。 |

Predicate  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | 检查失败时向用户发送的错误消息。 此字符串可以使用[语言自定义](localization.md)进行本地化 |
| parameters | 1:1 | 用于字符串验证的方法类型参数。 | 

Parameters  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| 参数 | 1:n | 用于字符串验证的方法类型参数。 | 

Parameter  元素包含以下属性：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| Id | 1:1 | 该参数的标识符。 |

下面的示例说明 `IsLengthRange` 方法使用参数 `Minimum` 和 `Maximum` 指定字符串的长度范围：

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

下面的示例说明 `MatchesRegex` 方法使用参数 `RegularExpression` 指定正则表达式：

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

下面的示例说明 `IncludesCharacters` 方法使用参数 `CharacterSet` 指定字符集：

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

下面的示例说明 `IsDateRange` 方法使用参数 `Minimum` 和 `Maximum` 指定格式为 `yyyy-MM-dd` 和 `Today` 的日期范围。

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations 

尽管谓词定义了根据声明类型进行检查的验证，但是 PredicateValidations  会对一组谓词进行分组，以构成可应用于声明类型的用户输入验证。 每个 PredicateValidation  元素均包含一组 PredicateGroup  元素，其中包含一组指向 Predicate  的 PredicateReference  元素。 为了通过验证，声明的值应通过所有 PredicateGroup  下的任何谓词的全部测试，及其包含的一组 PredicateReference  元素。

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

PredicateValidations  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | 谓词验证列表。 | 

PredicateValidation  元素包含以下属性：

| 特性 | 需要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 用于谓词验证的标识符。 ClaimType  元素可以在策略中使用此标识符。 |

PredicateValidation  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | 谓词组列表。 | 

PredicateGroups  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | 谓词列表。 | 

PredicateGroup  元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 用于谓词组的标识符。  |

PredicateGroup  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  谓词的说明，可以帮助用户了解应键入哪些值。 | 
| PredicateReferences | 1:n | 谓词引用的列表。 | 

PredicateReferences  元素包含以下属性：

| 特性 | 需要 | 描述 |
| --------- | -------- | ----------- |
| MatchAtLeast | 否 | 指定该值针对要接受的输入必须至少匹配多个谓词定义。 |

PredicateReferences  元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | 对谓词的引用。 | 

PredicateReference  元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 用于谓词验证的标识符。  |


## <a name="configure-password-complexity"></a>配置密码复杂性

使用 Predicates  和 PredicateValidationsInput  ，可以在创建帐户时控制用户提供的密码的复杂性要求。 默认情况下，Azure AD B2C 使用强密码。 此外，Azure AD B2C 还支持用于控制客户可以使用的密码复杂性的配置选项。 你可以使用这些谓词元素来定义密码的复杂性： 

- IsLengthBetween8And64  使用 `IsLengthRange` 方法，验证密码必须介于 8 到 64 个字符之间。
- Lowercase  使用 `IncludesCharacters` 方法，验证密码包含一个小写字母。
- Uppercase  使用 `IncludesCharacters` 方法，验证密码包含一个大写字母。
- Number  使用 `IncludesCharacters` 方法，验证密码包含一个数字。
- Symbol  使用 `IncludesCharacters` 方法，验证密码包含以下符号之一 `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- PIN  使用 `MatchesRegex` 方法，验证密码仅包含数字。
- AllowedAADCharacters  使用 `MatchesRegex` 方法，验证提供了仅限密码的无效字符。
- DisallowedWhitespace  使用 `MatchesRegex` 方法，验证密码不以空格字符开头或结尾。

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
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
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

定义基本验证后，你可以将它们组合在一起，并创建一组可以在策略中使用的密码策略：

- SimplePassword  验证 DisallowedWhitespace、AllowedAADCharacters 和 IsLengthBetween8And64
- StrongPassword  验证 DisallowedWhitespace、AllowedAADCharacters、IsLengthBetween8And64。 最后一组 `CharacterClasses` 运行一组额外的谓词，其中 `MatchAtLeast` 设置为 3。 用户密码长度必须为 8 到 16 个字符，且必须包含下列其中的三类字符：小写、大写、数字或符号。
- CustomPassword  仅验证 DisallowedWhitespace 和 AllowedAADCharacters。 因此，只要字符有效，用户可以提供任意长度的任何密码。

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
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

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

在声明类型中，添加 PredicateValidationReference  元素，并将标识符指定为 SimplePassword、StrongPassword 或 CustomPassword 等谓词验证中的一个。

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

下面显示了当 Azure AD B2C 显示错误消息时元素的组织方式：

![Predicate 进程](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>配置日期范围

借助 Predicates  和 PredicateValidations  元素，你可以通过使用 `DateTimeDropdown` 来控制 UserInputType  的最小和最大日期值。 如下例所示，可使用 `IsDateRange` 方法创建 Predicate  并提供最小和最大参数。

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

添加引用 `DateRange` 谓词的 PredicateValidation  。

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

在声明类型中，添加 PredicateValidationReference  元素，并将标识符指定为 `CustomDateRange`。 
    
```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
