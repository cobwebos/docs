---
title: 语言支持 - 文本分析 API
titleSuffix: Azure Cognitive Services
description: 文本分析 API 支持的自然语言列表。 本文介绍了以下每项操作支持哪些语言：情绪分析、关键短语提取、语言检测和实体识别。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 69c14c6b98b572bc413f5a35696269e13344387e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417313"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>文本分析 API 的语言和区域支持

本文介绍了以下每项操作支持哪些语言：情绪分析、关键短语提取、语言检测和命名的实体识别。

## <a name="language-detection"></a>语言检测

文本分析 API 可以检测到各种语言、 变体、 方言和某些区域和文化的语言。  语言检测返回一种语言的“脚本”。 例如，对于短语“I have a dog”，它将返回 `en` 而非 `en-US`。 唯一的特例是中文，对于中文，如果语言检测功能可以确定所提供的文本的脚本，则它将返回 `zh_CHS` 或 `zh_CHT`。 当无法识别中文文档的具体脚本时，它将简单地返回 `zh`。

我们未发布此功能的语言的确切列表，但它可以检测各种语言、 变体、 方言和某些区域和文化的语言。 

如果有较少使用的语言所表示的内容，则可以尝试语言检测，以查看是否返回代码。 无法检测的语言的响应为 `unknown`。

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>情绪分析、关键短语提取和命名的实体识别

对于情绪分析、关键短语提取和实体识别，所支持语言的列表更具选择性，因为分析器已优化为适应更多语言的语言规则。

## <a name="language-list-and-status"></a>语言列表和状态

语言支持最初在预览版中推广，渐变到正式版 (GA) 状态，且各种语言彼此独立并且总体上独立于文本分析服务。 即使文本分析 API 转变为正式版时，有些语言也可能保持在预览版。

| 语言    | 语言代码 | 情绪 | 关键短语 | 命名实体识别 |   说明  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| 阿拉伯语      | `ar`          |           |             | ✔ \*                     | |
| 捷克语       | `cs`          |           |             | ✔ \*                     | |
| 简体中文 | `zh-CN`|           |             | ✔ \*        |    |
| 丹麦语      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| 荷兰语       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| 英语     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| 芬兰语     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| 法语      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| 德语      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| 希腊语       | `el`          | ✔ \*     |             |            |     |
| 匈牙利语   | `hu`          |           |             |  ✔ \*          |     | 
| 意大利语     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| 日语    | `ja`          |          | ✔           |  ✔ \*          |     |
| 韩语      | `ko`          |          | ✔           |  ✔ \*          |     |
| 挪威语(博克马尔语) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| 波兰语      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| 葡萄牙语(葡萄牙) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` 也接受|
| 葡萄牙语(巴西)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| 俄语     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| 西班牙语     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| 瑞典语     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| 土耳其语     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* 语言支持为预览版

\*\* 命名的实体识别和[实体链接](how-tos/text-analytics-how-to-entity-linking.md)均适用于此语言。    

## <a name="see-also"></a>另请参阅

[认知服务文档页面](https://docs.microsoft.com/azure/cognitive-services/)   
[认知服务产品页面](https://azure.microsoft.com/services/cognitive-services/)
