---
title: 请求限制 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 本文列出了文本翻译 API 的请求限制。 费用是根据字符数而不是请求频率产生的，每个请求限制为 5,000 个字符。 字符限制是基于订阅的，F0 限制为每小时 200 万个字符。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: swmachan
ms.openlocfilehash: 3694c8cb34b2a050c9e18265c8cc0a0198456076
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533706"
---
# <a name="request-limits-for-translator-text"></a>文本翻译的请求限制

本文提供了文本翻译 API 的限制。 服务包括翻译、音译、句子长度检测、语言检测和备用翻译。

## <a name="character-and-array-limits-per-request"></a>每个请求的字符和数组限制

每个翻译请求限制为 5,000 个字符。 按字符收费，而不是按请求数收费。 建议发送较短的请求。

下表列出了文本翻译 API 的每个操作的数组元素和字符限制。

| Operation | 数组元素的最大大小 |   最大数组元素数 |  最大请求大小（字符数） |
|:----|:----|:----|:----|
| 翻译 | 5,000 | 100   | 5,000 |
| 直译 | 5,000 | 10    | 5,000 |
| 检测 | 10,000 | 100 |   50,000 |
| 断句 | 10,000    | 100 | 50,000 |
| 字典查找| 100 |  10  | 1,000 |
| 字典示例 | 100 个用于文本，100 个用于翻译（总共 200 个）| 10|   2,000 |

## <a name="character-limits-per-hour"></a>每小时的字符限制

每小时的字符限制取决于文本翻译订阅层。 

每小时配额应在一小时内均匀使用。 例如，在 F0 层限制为每小时 200 万字符时，使用字符的速度不应超过大约 33,300 个字符/分钟滑动窗口（200 万个字符除以 60 分钟）。

如果你达到或超过这些限制，或在短时间内发送过大部分配额，则可能会收到超出配额响应。 对并发请求没有限制。

| 层 | 字符限制 |
|------|-----------------|
| F0 | 每小时 200 万个字符 |
| S1 | 每小时 4000 万个字符 |
| S2/C2 | 每小时 4000 万个字符 |
| S3/C3 | 每小时 1.2 亿个字符 |
| S4/C4 | 每小时 2 亿个字符 |

[多服务订阅](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)的限制与 S1 层相同。

这些限制仅限于 Microsoft 的标准翻译模型。 使用自定义转换器的自定义翻译模型的每秒限制为1800个字符。

## <a name="latency"></a>Latency

使用标准模型时，文本翻译 API 的最大延迟为15秒，使用自定义模型时为120秒。 通常， *100 字符内的文本*响应会在150毫秒后返回到300毫秒。 自定义转换器模型对于持续的请求速率具有相似的延迟特征，在请求速率为间歇性时可能会有较高的延迟。 响应时间因请求大小和语言对而异。 如果在该时间范围内未收到翻译或[错误响应](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)，请检查你的代码和你的网络连接，然后重试。 

## <a name="sentence-length-limits"></a>句子长度限制

使用 [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) 函数时，句子长度限制为 275 个字符。 这些语言存在例外情况：

| 语言 | 代码 | 字符限制 |
|----------|------|-----------------|
| 中文 | zh | 132 |
| 德语 | de | 290 |
| 意大利语 | it | 280 |
| 日语 | ja | 150 |
| 葡萄牙语 | pt | 290 |
| 西班牙语 | es | 280 |
| 意大利语 | it | 280 |
| 泰语 | th | 258 |

> [!NOTE]
> 此限制不适用于翻译。

## <a name="next-steps"></a>后续步骤

* [定价](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [区域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 文本翻译 API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
