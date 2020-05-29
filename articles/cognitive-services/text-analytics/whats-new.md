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
ms.date: 05/19/2020
ms.author: aahi
ms.openlocfilehash: 32dc7e86d5cd737533a4a6c8b3d9ce7d00795c65
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140493"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文本分析 API 中有哪些新功能？

文本分析 API 会持续更新。 为了及时了解最新的发展，本文提供了有关新版本和功能的信息。

## <a name="may-2020"></a>2020 年 5 月

### <a name="text-analytics-api-v3-general-availability"></a>文本分析 API v3 公开上市

文本分析 API v3 现已正式发布，具有以下更新：

* 模型版本`2020-04-01`
* 每个功能的新[数据限制](concepts/data-limits.md)
* [情绪分析（SA） v3](how-tos/text-analytics-how-to-sentiment-analysis.md)的更新[语言支持](language-support.md)
* 用于实体链接的单独终结点 
* [命名实体识别（NER） v3](how-tos/text-analytics-how-to-entity-linking.md)中的新 "Address" 实体类别。
* NER v3 中的新子类别：
   * 位置-地理
   * 位置-结构
   * 组织-股票
   * 组织-医疗
   * 组织-体育
   * 事件-文化
   * 事件-自然
   * 活动-体育

已添加 JSON 响应中的以下属性：
   * `SentenceText`在情绪分析
   * `Warnings`对于每个文档 

JSON 响应中以下属性的名称已更改，如果适用：

* `score` 已重名为 `confidenceScore`
    * `confidenceScore`具有两个小数点精度点。 
* `type` 已重名为 `category`
* `subtype` 已重名为 `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [详细了解文本分析 API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>文本分析 API 3.1 公共预览版
   * 新情绪分析功能-[观点挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * 用于受保护的运行状况信息的新[个人（ `PII` ）域筛选器](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)（ `PHI` ）。
   * 新的个人（ `PII` ）类别：
      * 疾病的国际分类（ICD-9-CM）
      * 疾病的国际分类（ICD-10-CM）

> [!div class="nextstepaction"]
> [详细了解文本分析 API 3.1 Preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK 对文本分析 API v3 公共预览版的支持

作为[统一的 AZURE SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文本分析 API v3 SDK 现已作为以下编程语言的公共预览版提供：
   * [C#](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-csharp)
   * [Python](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-python)
   * [JavaScript (Node.js)](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-javascript)
   * [Java](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3&pivots=programming-language-java)
   
   > [!div class="nextstepaction"]
> [详细了解文本分析 API v3 SDK](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/text-analytics-sdk?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名实体识别 v3 公共预览版

其他实体类型现已在命名实体识别（NER） v3 公共预览服务中提供，因为我们展开了在文本中找到的常规和个人信息实体的检测。 此更新引入了[模型版本](concepts/model-versioning.md) `2020-02-01` ，其中包括：

* 识别以下常规实体类型（仅限英语）：
    * PersonType
    * 产品
    * 事件
    * 地缘政治实体（GPE）作为位置下的子类型
    * 技能

* 识别以下个人信息实体类型（仅限英语）：
    * 人员
    * 组织
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

* [模型版本](concepts/model-versioning.md) `2019-10-01` ，其中包括：
    * 扩展了文本中实体的检测和分类。 
    * 以下新实体类型的识别：
        * 电话号码
        * IP 地址

实体链接支持英语和西班牙语。 NER 语言支持因实体类型而异。

#### <a name="sentiment-analysis-v3-public-preview"></a>情绪分析 v3 公共预览版

* 用于分析情绪的[新终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)。
* [模型版本](concepts/model-versioning.md) `2019-10-01` ，其中包括：

    * API 文本分类和评分的准确性和详细信息的显著改进。
    * 为文本中的不同情绪自动添加标签。
    * 在文档和句子级别上情绪分析和输出。 

它支持英语（ `en` ）、日语（ `ja` ）、简体中文（ `zh-Hans` ）、繁体中文（）、 `zh-Hant` 法语（ `fr` ）、意大利语（ `it` ）、西班牙语（ `es` ）、荷兰语（ `nl` ）、葡萄牙语（）和 `pt` 德语（ `de` ），并在以下区域中提供： `Australia East` 、、 `Central Canada` 、 `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` 、、、、、、、和。 

> [!div class="nextstepaction"]
> [详细了解情绪分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>后续步骤

* [什么是文本分析 API？](overview.md)  
* [示例用户方案](text-analytics-user-scenarios.md)
* [情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [语言检测](how-tos/text-analytics-how-to-language-detection.md)
* [实体识别](how-tos/text-analytics-how-to-entity-linking.md)
* [关键短语提取](how-tos/text-analytics-how-to-keyword-extraction.md)
