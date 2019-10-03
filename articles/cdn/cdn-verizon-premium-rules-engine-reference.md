---
title: Azure CDN 规则引擎参考 | Microsoft 文档
description: Azure CDN 规则引擎匹配条件和功能的参考文档。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996755"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>从 Verizon 高级规则引擎引用 Azure CDN

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
% | 百分比符号的含义取决于其使用情况。<br/><br/> `%{HTTPVariable}`：此语法标识 HTTP 变量。<br/>`%{HTTPVariable%Pattern}`：此语法使用百分号符号标识 HTTP 变量和作为分隔符。<br />`\%`：对百分比符号进行转义可将其用作文本值, 或者用于指示 URL 编码 (例如`\%20`)。
\* | 使用星号时，其前面的字符可以匹配零次或更多次。
空格 | 通常将空格字符视为文本字符。
'值' | 可将单引号视为文本字符。 一组单引号没有特殊含义。

支持正则表达式的匹配条件和功能接受由 Perl 兼容的正则表达式 (PCRE) 定义的模式。

## <a name="next-steps"></a>后续步骤

- [规则引擎匹配条件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用规则引擎重写 HTTP 行为](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)
