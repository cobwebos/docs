---
title: 文本分析 API 支持的语言 - Azure 认知服务 | Microsoft Docs
description: 列出了文本分析 API 操作的正式版和预览版语言支持。 适用于情绪分析、关键短语提取和语言检测。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: ashmaka
ms.openlocfilehash: 2d341cfaf261bea6367bb55dd5d322f419e22d34
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366662"
---
# <a name="supported-languages-in-the-text-analytics-api"></a>文本分析 API 支持的语言

本文介绍了以下每项操作支持哪些语言：情绪分析、关键短语提取和语言检测。

## <a name="language-detection"></a>语言检测

文本分析 API 可以检测多达 120 种不同的语言。 语言检测返回一种语言的“脚本”。 例如，对于短语“I have a dog”，它将返回 `en` 而非 `en-US`。 唯一的特例是中文，对于中文，如果语言检测功能可以确定所提供的文本的脚本，则它将返回 `zh_CHS` 或 `zh_CHT`。 当无法识别中文文档的具体脚本时，它将简单地返回 `zh`。

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>情绪分析、关键短语提取和实体链接

对于情绪分析、关键短语提取和实体链接，支持的语言的列表更具选择性，因为分析器已优化为适应更多语言的语言规则。

## <a name="language-list-and-status"></a>语言列表和状态

语言支持最初在预览版中推广，渐变到正式版 (GA) 状态，且各种语言彼此独立并且总体上独立于文本分析服务。 即使文本分析 API 转变为正式版时，有些语言也可能保持在预览版。

| 语言    | 语言代码 | 情绪 | 关键短语 | 实体链接 |   说明  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| 丹麦语      | `da`          | ✔ \*     | ✔           |             |     |
| 荷兰语       | `nl`          | ✔ \*     | ✔          |             |     |
| 英语     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| 芬兰语     | `fi`          | ✔ \*     | ✔           |             |     |
| 法语      | `fr`          | ✔        | ✔           |             |     |
| 德语      | `de`          | ✔ \*     | ✔           |            |     |
| 希腊语       | `el`          | ✔ \*     |             |            |     |
| 意大利语     | `it`          | ✔ \*     | ✔           |             |     |
| 日语    | `ja`          |          | ✔           |            |     |
| 韩语      | `ko`          |          | ✔           |            |     |
| 挪威语(博克马尔语) | `no`          | ✔ \*     |  ✔          |             |     |
| 波兰语      | `pl`          | ✔ \*     |  ✔          |             |     |
| 葡萄牙语(葡萄牙) | `pt-PT`| ✔        |  ✔          |       |`pt` 也接受|
| 葡萄牙语(巴西)   | `pt-BR`|          |  ✔   |         |     |
| 俄语     | `ru`          | ✔ \*     | ✔           |             |     |
| 西班牙语     | `es`          | ✔        | ✔           |     |     |
| 瑞典语     | `sv`          | ✔ \*     | ✔           |             |     |
| 土耳其语     | `tr`          | ✔ \*     |             |             |     |

\* 表示预览版中的语言支持

## <a name="see-also"></a>另请参阅

[认知服务文档页面](https://docs.microsoft.com/azure/cognitive-services/)   
[认知服务产品页面](https://azure.microsoft.com/services/cognitive-services/)
