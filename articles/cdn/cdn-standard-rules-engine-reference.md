---
title: Azure CDN 的标准规则引擎引用 |微软文档
description: Azure 内容交付网络 （Azure CDN） 的标准规则引擎中匹配条件和操作的参考文档。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171566"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN 的标准规则引擎引用

在 Azure 内容交付网络 （Azure CDN）[的标准规则引擎](cdn-standard-rules-engine.md)中，规则由一个或多个匹配条件和操作组成。 本文详细介绍了 Azure CDN 的标准规则引擎中可用的匹配条件和功能。

规则引擎旨在成为标准 Azure CDN 处理特定类型请求的最终权限。

**规则的常见用途**：

- 重写或定义自定义缓存策略。
- 将请求重定向。
- 修改 HTTP 请求和响应标头。

## <a name="terminology"></a>术语

要在规则引擎中定义规则，请设置[匹配条件和](cdn-standard-rules-engine-match-conditions.md)[操作](cdn-standard-rules-engine-actions.md)：

 ![Azure CDN 规则结构](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每个规则最多可以有四个匹配条件和三个操作。 每个 Azure CDN 终结点最多只能有五个规则。 

Azure CDN 终结点的当前五个规则限制中包括默认*全局规则*。 全局规则没有匹配条件，并且在全局规则中定义的操作始终触发。

## <a name="syntax"></a>语法

规则中特殊字符的处理方式因匹配条件和操作处理文本值的不同而不同。 匹配条件或操作可以通过以下方式之一解释文本：

- [文字值](#literal-values)
- [通配符值](#wildcard-values)


### <a name="literal-values"></a>文本值

解释为文本值的文本将*除 % 符号之外*的所有特殊字符视为规则中必须匹配的值的一部分。 例如，仅当找到确切值`'*'``'*'`时，才满足设置为 的文本匹配条件。

百分比符号用于指示 URL 编码（例如， `%20`。

### <a name="wildcard-values"></a>通配符值

被解释为通配符值的文本为特殊字符分配了其他含义。 下表描述了在标准规则引擎中如何解释特定特殊字符：

字符 | 描述
----------|------------
\ | 反斜杠用于对此表中指定的任何字符进行转义操作。 必须直接在应该进行转义的特殊字符之前指定一个反斜杠。 例如，以下语法会对星号进行转义：`\*`
% | 百分比符号用于指示 URL 编码（例如， `%20`。
\* | 星号是通配符，代表一个或多个字符。
space | 空格字符指示匹配条件可以通过指定的值或模式之一满足。
单引号 | 单个引号没有特殊含义。 但是，一组单引号指示值应视为文本值。 单引号可通过以下方式使用：<ul><li>允许在指定值匹配比较值的任何部分时满足匹配条件。  例如，`'ma'` 与以下任何字符串都匹配： <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>允许将特殊字符指定为文本字符。 例如，可以通过将空格字符括在一组单引号 （`' '`或`'<sample value>'`） 中来指定文本空格字符。</li><li>允许指定空白值。 通过指定一组单引号 **（''**） 指定空值。</li></ul>**重要提示**：<br /><ul><li>如果指定的值不包含通配符，则该值将自动视为文本值。 不需要为文本值指定一组单引号。</li><li>如果反斜杠不用于转义此表中的另一个字符，则在一组单引号中指定反斜杠时将忽略它。</li><li>将特殊字符指定为文本字符的另一种方法是使用反斜杠 （）`\`来转义它。</li></ul>

## <a name="next-steps"></a>后续步骤

- [标准规则引擎中的匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [标准规则引擎中的操作](cdn-standard-rules-engine-actions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)
