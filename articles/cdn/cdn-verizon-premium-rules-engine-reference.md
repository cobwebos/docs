---
title: Azure CDN 规则引擎参考 | Microsoft 文档
description: Azure CDN 规则引擎匹配条件和功能的参考文档。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 5fc611af75a7f733576f9343a4375fb56cacc030
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593149"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>来自 Verizon 的高级规则引擎参考的 azure CDN

本文列出了 Azure 内容分发网络 (CDN) [规则引擎](cdn-verizon-premium-rules-engine.md)的可用匹配条件和功能的详细说明。

设计规则引擎的目的，是让其充当一个最终授权机构，控制 CDN 处理特定类型请求的方式。

**常见用途**：

- 重写或定义自定义缓存策略。
- 保护或拒绝对敏感内容的请求。
- 将请求重定向。
- 存储自定义日志数据。

## <a name="terminology"></a>术语

通过使用[**条件表达式**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)、[**匹配条件**](cdn-verizon-premium-rules-engine-reference-match-conditions.md)和[**功能**](cdn-verizon-premium-rules-engine-reference-features.md)来定义规则。 下图突出显示了这些元素：

 ![CDN 匹配条件](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>语法

特殊字符的处理方式取决于匹配条件或功能处理文本值的方式。 匹配条件或功能可能会以下述某种方式解释文本：

1. [**文本值**](#literal-values)
2. [**通配符值**](#wildcard-values)
3. [**正则表达式**](#regular-expressions)

### <a name="literal-values"></a>文本值

可解释为文本值的文本会将所有特殊字符（% 字符除外）视为必须匹配的值的一部分。 换言之，仅当找到精确值（即 `\'*'\`）时才满足设置为 `\'*'\` 的文本匹配条件。

百分比符号用于指示 URL 编码（例如 `%20`）。

### <a name="wildcard-values"></a>通配符值

可以解释为通配符值的文本会为特殊字符赋予其他意义。 下表说明了如何解释以下字符集：

Character | 描述
----------|------------
\ | 反斜杠用于对此表中指定的任何字符进行转义操作。 必须直接在应该进行转义的特殊字符之前指定一个反斜杠。<br/>例如，以下语法会对星号进行转义：`\*`
% | 百分比符号用于指示 URL 编码（例如 `%20`）。
\* | 星号是通配符，代表一个或多个字符。
空格 | 空格字符表示某个匹配条件可以由指定的值或模式满足。
'值' | 一个单引号没有特殊含义， 但是，可以使用一组单引号来指示应将其中的值视为文本值。 可以通过以下方式使用单引号：<br><br/>- 使用单引号时，只要指定的值与比较值的任何部分匹配，即表示满足匹配条件。  例如，`'ma'` 与以下任何字符串都匹配： <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 使用单引号时，可以将特殊字符指定为文本字符。 例如，可以通过将空格字符括在一组单引号中（即 `' '` 或 `'sample value'`）来指定文本空格字符。<br/>- 使用单引号时，可以指定空值。 通过指定一组单引号（即 ''）来指定空值。<br /><br/>**重要提示：**<br/>- 如果指定的值不包含通配符，则会自动将其视为文本值，这意味着不需要指定一组单引号。<br/>- 如果反斜杠不对此表中的其他字符进行转义操作，则会忽略在一组单引号中指定的反斜杠。<br/>- 要将特殊字符指定为文本字符，另一种方式是使用反斜杠（即 `\`）对其进行转义操作。

### <a name="regular-expressions"></a>正则表达式

正则表达式定义要在文本值中对其进行搜索的模式。 正则表达式表示法定义各种符号的具体含义。 下表说明了支持正则表达式的匹配条件和功能如何处理特殊字符。

特殊字符 | 描述
------------------|------------
\ | 反斜杠会对其后的字符进行转义操作，这会导致系统将该字符视为文本值，而不会采用其正则表达式含义。 例如，以下语法会对星号进行转义：`\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | An asterisk allows the preceding character to be matched zero or more times.
Space | A space character is typically treated as a literal character.
'value' | Single quotes are treated as literal characters. A set of single quotes does not have special meaning.

## Next steps

- [Rules engine match conditions](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)
- [Azure CDN overview](cdn-overview.md百分比符号的含义取决于其使用情况。 Docs
descr`%{HTTPVariable}`：此语法标识 HTTP 变量。match`%{HTTPVariable%Pattern}`：此语法使用百分比符号来标识 HTTP 变量并将作为分隔符。1/2019`\``: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\`20`).f the available match conditions and features for the Azure Content Delivery Network (CDN) [rules engine](cdn-verizon-premium-rules-engine.md).

The rules engine is designed to be the final authority on how specific types of requests are processed by the CDN.

**Common uses**:

- Override or define a custom cache policy.
- Secure or deny requests for sensitive content.
- Redirect requests.
- Store custom log data.

## Terminology

A rule is defined through the use of [**conditional expressions**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**match conditions**](cdn-verizon-premium-rules-engine-reference-match-conditions.md), and [**features**](cdn-verizon-premium-rules-engine-reference-features.md). These elements are highlighted in the following illustration:

 ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## Syntax

The manner in which special characters are treated varies according to how a match condition or feature handles text values. A match condition or feature may interpret text in one of the following ways:

1. [**Literal values**](#literal-values)
2. [**Wildcard values**](#wildcard-values)
3. [**Regular expressions**](#regular-expressions)

### Literal values

Text that is interpreted as a literal value treats all special characters, with the exception of the % symbol, as a part of the value that must be matched. In other words, a literal match condition set to `\'*'\` is only satisfied when that exact value (that is, `\'*'\`) is found.

A percentage symbol is used to indicate URL encoding (for example, `%20`).

### Wildcard values

Text that is interpreted as a wildcard value assigns additional meaning to special characters. The following table describes how the following set of characters is interpreted:

Character | Description
----------|------------
\ | A backslash is used to escape any of the characters specified in this table. A backslash must be specified directly before the special character that should be escaped.<br/>For example, the following syntax escapes an asterisk: `\*`
% | A percentage symbol is used to indicate URL encoding (for example, `%20`).
\* | An asterisk is a wildcard that represents one or more characters.
Space | A space character indicates that a match condition may be satisfied by either of the specified values or patterns.
'value' | A single quote does not have special meaning. However, a set of single quotes is used to indicate that a value should be treated as a literal value. It can be used in the following ways:<br><br/>- It allows a match condition to be satisfied whenever the specified value matches any portion of the comparison value.  For example, `'ma'` would match any of the following strings: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- It allows a special character to be specified as a literal character. For example, you may specify a literal space character by enclosing a space character within a set of single quotes (that is, `' '` or `'sample value'`).<br/>- It allows a blank value to be specified. Specify a blank value by specifying a set of single quotes (that is, '').<br /><br/>**Important:**<br/>- If the specified value does not contain a wildcard, then it is automatically considered a literal value, which means that it is not necessary to specify a set of single quotes.<br/>- If a backslash does not escape another character in this table, it is ignored when it is specified within a set of single quotes.<br/>- Another way to specify a special character as a literal character is to escape it using a backslash (that is, `\`).

### Regular expressions

Regular expressions define a pattern that is searched for within a text value. Regular expression notation defines specific meanings to a variety of symbols. The following table indicates how special characters are treated by match conditions and features that support regular expressions.

Special Character | Description
------------------|------------
\ | A backslash escapes the character the follows it, which causes that character to be treated as a literal value instead of taking on its regular expression meaning. For example, the following syntax escapes an asterisk: `\*`
% | The meaning of a percentage symbol depends on its usage.<br/><br/> `%{HTTPVariable}`: This syntax identifies an HTTP variable.<br/>`%{HTTPVariable%Pattern}`: This syntax uses a percentage symbol to identify an HTTP variable and as a delimiter.<br />`\%`: Escaping a percentage symbol allows it to be used as a literal value or to indicate URL encoding (for example, `\%20`).
\* | 使用星号时，其前面的字符可以匹配零次或更多次。
空格 | 通常将空格字符视为文本字符。
'值' | 可将单引号视为文本字符。 一组单引号没有特殊含义。

## <a name="next-steps"></a>后续步骤

- [规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用规则引擎重写 HTTP 行为](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)