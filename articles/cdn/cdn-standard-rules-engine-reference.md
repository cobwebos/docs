---
title: Azure CDN 标准规则引擎参考 |Microsoft Docs
description: Azure CDN 标准规则引擎匹配条件和操作的参考文档。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615931"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Microsoft 规则引擎参考中的 Azure CDN

本文列出了 Azure 内容分发网络（CDN）[标准规则引擎](cdn-standard-rules-engine.md)的可用匹配条件和功能的详细说明。

设计规则引擎的目的，是让其充当一个最终授权机构，控制 CDN 处理特定类型请求的方式。

**常见用途**：

- 重写或定义自定义缓存策略。
- 将请求重定向。
- 修改 HTTP 请求和响应标头

## <a name="terminology"></a>术语

通过使用[**匹配条件**](cdn-standard-rules-engine-match-conditions.md)和[**操作**](cdn-standard-rules-engine-actions.md)来定义规则。 下图突出显示了这些元素：

 ![CDN 规则结构](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每个规则最多可以有4个匹配条件和三个操作。 每个 CDN 终结点最多有5个规则。 此外，默认情况下有一个称为**全局规则**的规则。 这是一个没有匹配条件的规则，其中定义的操作将始终触发。 此规则包含在当前的5个规则限制中。

## <a name="syntax"></a>语法

处理特殊字符的方式因 match 条件或 actopm 处理文本值的方式而异。 匹配条件或功能可能会以下述某种方式解释文本：

1. [**文本值**](#literal-values)
2. [**通配符值**](#wildcard-values)


### <a name="literal-values"></a>文本值

可解释为文本值的文本会将所有特殊字符（% 字符除外）视为必须匹配的值的一部分。 换言之，仅当找到精确值（即 `\'*'\`）时才满足设置为 `\'*'\` 的文本匹配条件。

百分比符号用于指示 URL 编码（例如 `%20`）。

### <a name="wildcard-values"></a>通配符值

可以解释为通配符值的文本会为特殊字符赋予其他意义。 下表说明了如何解释以下字符集：

Character | 说明
----------|------------
\ | 反斜杠用于对此表中指定的任何字符进行转义操作。 必须直接在应该进行转义的特殊字符之前指定一个反斜杠。<br/>例如，以下语法会对星号进行转义：`\*`
% | 百分比符号用于指示 URL 编码（例如 `%20`）。
\* | 星号是通配符，代表一个或多个字符。
空格 | 空格字符表示某个匹配条件可以由指定的值或模式满足。
'值' | 一个单引号没有特殊含义， 但是，可以使用一组单引号来指示应将其中的值视为文本值。 可以通过以下方式使用单引号：<br><br/>- 使用单引号时，只要指定的值与比较值的任何部分匹配，即表示满足匹配条件。  例如，`'ma'` 与以下任何字符串都匹配： <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 使用单引号时，可以将特殊字符指定为文本字符。 例如，可以通过将空格字符括在一组单引号中（即 `' '` 或 `'sample value'`）来指定文本空格字符。<br/>- 使用单引号时，可以指定空值。 通过指定一组单引号（即 ''）来指定空值。<br /><br/>**重要提示：**<br/>- 如果指定的值不包含通配符，则会自动将其视为文本值，这意味着不需要指定一组单引号。<br/>- 如果反斜杠不对此表中的其他字符进行转义操作，则会忽略在一组单引号中指定的反斜杠。<br/>- 要将特殊字符指定为文本字符，另一种方式是使用反斜杠（即 `\`）对其进行转义操作。

## <a name="next-steps"></a>后续步骤

- [标准规则引擎匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [标准规则引擎操作](cdn-standard-rules-engine-actions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)
