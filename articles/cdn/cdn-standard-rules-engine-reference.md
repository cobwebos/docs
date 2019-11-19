---
title: Azure CDN 的标准规则引擎参考 |Microsoft Docs
description: Azure 内容分发网络（Azure CDN）的标准规则引擎中匹配条件和操作的参考文档。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171566"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN 的标准规则引擎参考

在 Azure 内容分发网络（Azure CDN）的[标准规则引擎](cdn-standard-rules-engine.md)中，规则由一个或多个匹配条件和操作组成。 本文详细说明了适用于 Azure CDN 的标准规则引擎中的匹配条件和功能。

规则引擎旨在作为 Azure CDN 标准请求处理特定类型的请求的最终颁发机构。

**规则的常见用途**：

- 重写或定义自定义缓存策略。
- 将请求重定向。
- 修改 HTTP 请求和响应标头。

## <a name="terminology"></a>术语

若要在规则引擎中定义规则，请设置[match 条件](cdn-standard-rules-engine-match-conditions.md)和[操作](cdn-standard-rules-engine-actions.md)：

 ![Azure CDN 规则结构](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每个规则最多可以有四个匹配条件和三个操作。 每个 Azure CDN 端点最多可以有五个规则。 

Azure CDN 终结点的当前五规则限制中包含默认*全局规则*。 全局规则不具有匹配条件，并且全局规则中定义的操作始终触发。

## <a name="syntax"></a>语法

规则中特殊字符的处理方式取决于不同的匹配条件和操作处理文本值的方式。 匹配条件或操作可以通过以下方式之一来解释文本：

- [文本值](#literal-values)
- [通配符值](#wildcard-values)


### <a name="literal-values"></a>文本值

解释为文本值的文本会*将除% 符号之外*的所有特殊字符视为必须在规则中匹配的值的一部分。 例如，仅当找到准确的值 `'*'` 时，才满足设置为 `'*'` 的文本匹配条件。

百分比符号用于指示 URL 编码（例如 `%20`）。

### <a name="wildcard-values"></a>通配符值

解释为通配符值的文本会为特殊字符赋予其他意义。 下表说明了标准规则引擎中特定特殊字符的解释方式：

Character | 说明
----------|------------
\ | 反斜杠用于对此表中指定的任何字符进行转义操作。 必须直接在应该进行转义的特殊字符之前指定一个反斜杠。 例如，以下语法会对星号进行转义：`\*`
% | 百分比符号用于指示 URL 编码（例如 `%20`）。
\* | 星号是通配符，代表一个或多个字符。
空间 | 空格字符指示可以通过任一指定的值或模式满足匹配条件。
单引号 | 单引号没有特殊含义。 不过，一组单引号指示应将值视为文本值。 可以通过以下方式使用单引号：<ul><li>如果指定的值与比较值的任何部分匹配，则允许满足匹配条件。  例如，`'ma'` 与以下任何字符串都匹配： <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>允许将特殊字符指定为文本字符。 例如，可以通过将空格字符括在一组单引号（`' '` 或 `'<sample value>'`）来指定文本空格字符。</li><li>如果允许指定空白值，则为。 通过指定一组单引号（ **' '** ）来指定空值。</li></ul>**重要说明：**<br /><ul><li>如果指定的值不包含通配符，则该值将被自动视为文本值。 不需要为文本值指定一组单引号。</li><li>如果不使用反斜杠来对此表中的其他字符进行转义，则当在一组单引号中指定反斜杠时，将忽略它。</li><li>将特殊字符指定为文本字符的另一种方法是使用反斜杠（`\`）对其进行转义。</li></ul>

## <a name="next-steps"></a>后续步骤

- [标准规则引擎中的匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [标准规则引擎中的操作](cdn-standard-rules-engine-actions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)
