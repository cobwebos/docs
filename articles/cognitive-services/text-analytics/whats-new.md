---
title: 文本分析 API 中的新增功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文提供有关 Azure 认知服务文本分析的新版本和功能的信息。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77188803"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文本分析 API 中有哪些新功能？

文本分析 API 会持续更新。 为了了解最新动态，本文为您提供了有关新版本和功能的信息。

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>对文本分析 API v3 公共预览的 SDK 支持

作为[统一 Azure SDK 版本的](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)一部分，文本分析 API v3 SDK 现在可作为以下编程语言的公共预览版提供：
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [了解有关文本分析 API v3 SDK 的更多信息](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名实体识别 v3 公共预览

随着我们扩展对文本中常规和个人信息实体的检测，命名实体识别 （NER） v3 公共预览服务中现在提供了其他实体类型。 此更新引入了[模型版本](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)`2020-02-01`，其中包括：

* 识别以下一般实体类型（仅限英语）：
    * 人员类型
    * Products
    * 事件
    * 位置下的地缘政治实体 （GPE） 作为子类型
    * 技能

* 识别以下个人信息实体类型（仅限英语）：
    * 人员
    * 组织
    * 年龄作为"数量"下的子类型
    * 日期作为日期时间下的子类型
    * 电子邮件 
    * 电话号码（仅限美国）
    * 代码
    * IP 地址

> [!div class="nextstepaction"]
> [了解有关命名实体识别 v3 的更多](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019 年 10 月

#### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

* 用于识别个人信息实体类型[的新终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii)（仅限英语）

* [实体识别和](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)[实体链接](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)的单独终结点。

* [模型版本](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)`2019-10-01`， 包括：
    * 扩展了文本中找到的实体的检测和分类。 
    * 识别以下新实体类型：
        * 电话号码
        * IP 地址

实体链接支持英语和西班牙语。 NER 语言支持因实体类型而异。

#### <a name="sentiment-analysis-v3-public-preview"></a>情绪分析 v3 公共预览版

* 用于分析情绪[的新端点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)。
* [模型版本](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)`2019-10-01`， 包括：

    * API 文本分类和评分的准确性和准确性有了显著改善。
    * 文本中不同情绪的自动标记。
    * 在文档和句子级别上进行情绪分析和输出。 

It supports English (`en`), Japanese (`ja`), Chinese Simplified (`zh-Hans`), Chinese Traditional (`zh-Hant`), French (`fr`), Italian (`it`), Spanish (`es`), Dutch (`nl`), Portuguese (`pt`), and German (`de`), and is available in the following regions: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`, and `West US 2`. 

> [!div class="nextstepaction"]
> [了解有关情绪分析 v3 的更多](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>后续步骤

* [什么是文本分析 API？](overview.md)  
* [示例用户方案](text-analytics-user-scenarios.md)
* [情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [语言检测](how-tos/text-analytics-how-to-language-detection.md)
* [实体识别](how-tos/text-analytics-how-to-entity-linking.md)
* [关键短语提取](how-tos/text-analytics-how-to-keyword-extraction.md)
