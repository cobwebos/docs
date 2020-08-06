---
title: Azure CDN 的标准规则引擎参考 |Microsoft Docs
description: 适用于 Azure 内容传送网络的标准规则引擎中的匹配条件和操作的参考文档 (Azure CDN) 。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: f729176d3f79c2a1f6fabb5631d49747219db48f
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760084"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN 的标准规则引擎参考

在 Azure 内容分发网络 (Azure CDN) 的[标准规则引擎](cdn-standard-rules-engine.md)中，规则由一个或多个匹配条件和操作组成。 本文详细说明了适用于 Azure CDN 的标准规则引擎中的匹配条件和功能。

规则引擎旨在作为 Azure CDN 标准请求处理特定类型的请求的最终颁发机构。

**规则的常见用途**：

- 重写或定义自定义缓存策略。
- 将请求重定向。
- 修改 HTTP 请求和响应标头。

## <a name="terminology"></a>术语

若要在规则引擎中定义规则，请设置[match 条件](cdn-standard-rules-engine-match-conditions.md)和[操作](cdn-standard-rules-engine-actions.md)：

 ![Azure CDN 规则结构](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每个规则最多可以有10个匹配条件和五个操作。 每个 Azure CDN 端点最多可以有25个规则。 

此限制中包含默认的*全局规则*。 全局规则没有匹配条件;始终触发全局规则中定义的操作。

## <a name="limits-and-pricing"></a>限制和定价 

每个 Azure CDN 端点最多可以有25个规则。 每个规则最多可以有10个匹配条件和五个操作。 规则引擎的定价遵循以下尺寸： 
- 规则：每个规则每月 $1 
- 处理的请求数： $0.60/百万 requets
- 前5个规则将继续可用

## <a name="syntax"></a>语法

规则中特殊字符的处理方式取决于不同的匹配条件和操作处理文本值的方式。 匹配条件或操作可以通过以下方式之一来解释文本：

- [文本值](#literal-values)
- [通配符值](#wildcard-values)


### <a name="literal-values"></a>文本值

解释为文本值的文本会*将除% 符号之外*的所有特殊字符视为必须在规则中匹配的值的一部分。 例如， `'*'` 仅当找到准确值时，才会满足将的文本匹配条件设置为 `'*'` 。

百分比符号用于指示 URL 编码 (例如 `%20`) 。

### <a name="wildcard-values"></a>通配符值

目前，我们支持标准规则引擎的**UrlPath Match 条件**中的通配符。 \*字符是表示一个或多个字符的星号。 

## <a name="next-steps"></a>后续步骤

- [标准规则引擎中的匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [标准规则引擎中的操作](cdn-standard-rules-engine-actions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概述](cdn-overview.md)
