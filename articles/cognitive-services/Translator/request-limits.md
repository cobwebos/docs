---
title: 请求限制 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 本文列出了文本翻译 API 的请求限制。 费用是根据字符数而不是请求频率产生的，每个请求限制为 5,000 个字符。 字符限制是基于订阅的，F0 限制为每小时 200 万个字符。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: erhopf
ms.openlocfilehash: 29ef4cc594a3335d37bd813c0b682b248f96cf22
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2018
ms.locfileid: "51858328"
---
# <a name="request-limits-for-translator-text"></a>文本翻译的请求限制

本文提供了文本翻译 API 的限制。 服务包括翻译、音译、句子长度检测、语言检测和备用翻译。

## <a name="character-limits-per-request"></a>每个请求的字符限制

每个请求限制为 5,000 个字符。 按字符收费，而不是按请求数收费。 建议发送更短的请求，并在任何给定时间提出一些未完成的请求。

对文本翻译 API 的未完成请求数量没有限制。

## <a name="character-limits-per-hour"></a>每小时的字符限制

每小时的字符限制取决于文本翻译订阅层。 如果达到或超过这些限制，则可能会收到超出配额响应：

| 层 | 字符限制 |
|------|-----------------|
| F0 | 每小时 200 万个字符 |
| S1 | 每小时 4000 万个字符 |
| S2 | 每小时 4000 万个字符 |
| S3 | 每小时 1.2 亿个字符 |
| S4 | 每小时 2 亿个字符 |

这些限制仅限于 Microsoft 的泛型系统。 使用 Microsoft Translator Hub 的自定义翻译系统每秒限制为 1,800 个字符。

## <a name="latency"></a>Latency

文本翻译的最大延迟为 13 秒。 此时你将收到结果或超时响应。 通常，将在 150 毫秒至 300 毫秒内返回响应。 响应时间将根据大小或请求和语言对而有所不同。

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
