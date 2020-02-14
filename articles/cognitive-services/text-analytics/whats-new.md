---
title: 文本分析 API 中的新增功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文提供了有关 Azure 认知服务文本分析的新版本和功能的信息。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 162e60ac8d33dc5d1951a58b0a9643b668608d7b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188803"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文本分析 API 中有哪些新功能？

文本分析 API 会持续更新。 为了及时了解最新的发展，本文提供了有关新版本和功能的信息。

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK 对文本分析 API v3 公共预览版的支持

作为[统一的 AZURE SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文本分析 API v3 SDK 现已作为以下编程语言的公共预览版提供：
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript （node.js）](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)

> [!div class="nextstepaction"]
> [详细了解文本分析 API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名实体识别 v3 公共预览版

其他实体类型现已在命名实体识别（NER） v3 公共预览服务中提供，因为我们展开了在文本中找到的常规和个人信息实体的检测。 此更新引入 `2020-02-01`[模型版本](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)，其中包括：

* 识别以下常规实体类型（仅限英语）：
    * PersonType
    * Products
    * 事件
    * 地缘政治实体（GPE）作为位置下的子类型
    * 技能

* 识别以下个人信息实体类型（仅限英语）：
    * Person
    * 单位
    * 在数量下作为子类型的年龄
    * 日期作为 DateTime 下的子类型
    * 电子邮件 
    * 电话号码（仅限美国）
    * URL
    * IP 地址

> [!div class="nextstepaction"]
> [了解有关命名实体识别 v3 的详细信息](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

### <a name="october-2019"></a>2019 年 10 月

#### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

* 用于识别个人信息实体类型的[新终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionPii)（仅限英语）

* 用于[实体识别](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)和[实体链接](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesLinking)的不同终结点。

* [模型版本](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)`2019-10-01`，其中包括：
    * 扩展了文本中实体的检测和分类。 
    * 以下新实体类型的识别：
        * 电话号码
        * IP 地址

实体链接支持英语和西班牙语。 NER 语言支持因实体类型而异。

#### <a name="sentiment-analysis-v3-public-preview"></a>情绪分析 v3 公共预览版

* 用于分析情绪的[新终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)。
* [模型版本](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)`2019-10-01`，其中包括：

    * API 文本分类和评分的准确性和详细信息的显著改进。
    * 为文本中的不同情绪自动添加标签。
    * 在文档和句子级别上情绪分析和输出。 

它支持英语（`en`）、日语（`ja`）、简体中文（`zh-Hans`）、繁体中文（`zh-Hant`）、法语（`fr`）、意大利语（`it`）、西班牙语（`es`）、荷兰语（`nl`）、葡萄牙语（`pt`）和德语（`de`），并在以下区域中提供： `Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe`和 `West US 2`。 

> [!div class="nextstepaction"]
> [详细了解情绪分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>后续步骤

* [什么是文本分析 API？](overview.md)  
* [示例用户方案](text-analytics-user-scenarios.md)
* [情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [语言检测](how-tos/text-analytics-how-to-language-detection.md)
* [实体识别](how-tos/text-analytics-how-to-entity-linking.md)
* [关键短语提取](how-tos/text-analytics-how-to-keyword-extraction.md)
