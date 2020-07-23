---
title: 文本分析 API 中的新增功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文介绍了 Azure 认知服务文本分析的新版本和新功能。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 3d9fb7f3fa01c06cc778e4062dd1d136e9c6e342
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103392"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文本分析 API 中有哪些新功能？

文本分析 API 会持续更新。 为了让大家随时了解最新的开发成果，本文介绍了新版本和新功能。

## <a name="july-2020"></a>2020 年 7 月

### <a name="text-analytics-for-health-container-public-gated-preview"></a>用于运行状况容器公共封闭预览的文本分析

运行状况容器的文本分析现在处于公共封闭预览版中，可让你从临床文档中的非结构化英语文本中提取信息，例如：患者进气窗体、医生说明、研究论文和解雇汇总。 目前，你不会对运行状况容器使用情况的文本分析计费。 

容器提供以下功能：

* 命名实体识别
* 关系提取
* 实体链接
* 否定


> [!div class="nextstepaction"]
> [了解有关运行状况容器文本分析的详细信息](how-tos/text-analytics-for-health.md)

## <a name="may-2020"></a>2020 年 5 月

### <a name="text-analytics-api-v3-general-availability"></a>文本分析 API v3 正式发布

文本分析 API v3 现已正式发布，具有以下更新：

* 模型版本 `2020-04-01`
* 每个功能的新[数据限制](concepts/data-limits.md)
* 更新了[情绪分析 (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) 的[语言支持](language-support.md)
* 用于实体链接的单独终结点 
* [命名实体识别 (NER) v3](how-tos/text-analytics-how-to-entity-linking.md) 中的新“Address”实体类别。
* NER v3 中的新子类别：
   * 位置 - 地理
   * 位置 - 结构
   * 组织 - 证券交易所
   * 组织 - 医疗
   * 组织 - 体育
   * 事件 - 文化
   * 事件 - 自然
   * 事件 - 体育

已在 JSON 响应中添加了以下属性：
   * 情绪分析中的 `SentenceText`
   * 每个文档的 `Warnings` 

JSON 响应中以下属性的名称已更改（如果适用）：

* `score` 已重名为 `confidenceScore`
    * `confidenceScore` 的精度为小数点后两位。 
* `type` 已重名为 `category`
* `subtype` 已重名为 `subcategory`

[!INCLUDE [v3 region availability](includes/v3-region-availability.md)]

> [!div class="nextstepaction"]
> [详细了解文本分析 API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>文本分析 API 3.1 公共预览版
   * 新情绪分析功能-[观点挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * 用于受保护的运行状况信息的新[个人（ `PII` ）域筛选器](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)（ `PHI` ）。

> [!div class="nextstepaction"]
> [详细了解文本分析 API v3.1 预览版](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK 对文本分析 API v3 公共预览版的支持

作为[统一 Azure SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文本分析 API v3 SDK 现已作为以下编程语言的公共预览版提供：
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
