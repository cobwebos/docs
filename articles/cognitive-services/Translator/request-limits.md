---
title: 请求限制 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 本文列出了文本翻译 API 的请求限制。 费用是根据字符数而不是请求频率产生的，每个请求限制为 5,000 个字符。 字符限制是基于订阅的，F0 限制为每小时 200 万个字符。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: erhopf
ms.openlocfilehash: c19e39918aa64730a35a27fcdadd70800f47f4fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514644"
---
# <a name="request-limits-for-translator-text"></a>文本翻译的请求限制

本文提供了文本翻译 API 的限制。 服务包括翻译、音译、句子长度检测、语言检测和备用翻译。

## <a name="character-and-array-limits-per-request"></a>每个请求的字符和数组的限制

每个转换请求被限制为 5,000 个字符。 按字符收费，而不是按请求数收费。 建议将较短的请求发送。

下表列出了数组元素和字符限制，文本翻译 API 的每个操作。

| Operation | 数组元素的最大大小 |   数组中元素的最大数目 |  最大请求大小 （字符） |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 5,0000 |
| 字典查找| 100 |  10  | 1,000 |
| 字典示例 | 100 的文本和 100 之间的转换 (总 200)| 10|   2,000 |

## <a name="character-limits-per-hour"></a>每小时的字符限制

每小时的字符限制取决于文本翻译订阅层。 应在一小时内平均消费每小时配额。 如果达到或超过这些限制，或在短时间内发送过大的配额一部分，你可能会收到配额响应不足。

| 层 | 字符限制 |
|------|-----------------|
| F0 | 每小时 200 万个字符 |
| S1 | 每小时 4000 万个字符 |
| S2 / C2 | 每小时 4000 万个字符 |
| S3 / C3 | 每小时 1.2 亿个字符 |
| S4 / C4 | 每小时 2 亿个字符 |

限制[多服务订阅](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)S1 层相同。

这些限制仅限于 Microsoft 的标准转换模型。 使用自定义转换器的自定义转换模型仅限于每秒 1,800 字符。

## <a name="latency"></a>Latency

文本翻译 API 在使用标准模型时的最大延迟为 15 秒。 使用自定义模型时，翻译的最大延迟为 25 秒。 此时你将收到结果或超时响应。 通常，将在 150 毫秒至 300 毫秒内返回响应。 响应时间因请求大小和语言对而异。 如果没有在该时间范围内收到翻译或[错误响应](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)，则应检查网络连接并重试。

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
