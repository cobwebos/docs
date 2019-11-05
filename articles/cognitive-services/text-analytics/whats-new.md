---
title: 文本分析 API 中的新增功能
titlesuffix: Text Analytics - Azure Cognitive Services
description: 了解文本分析服务的新开发
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 44ef6fb118be4d1110a693faded6c57bc8b4e2fd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499958"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文本分析 API 中有哪些新功能？

文本分析 API 会持续更新。 为了及时了解最新的发展，本文提供了有关新版本和功能的信息。

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>命名实体识别 v3 公共预览版-10 月2019

下一版本的命名实体识别（NER）现在可用于公共预览版，并为在文本中找到的实体提供扩展的检测和分类。 它提供：

* 以下新实体类型的识别：
    * 电话号码
    * IP 地址

* 用于识别个人信息实体类型的新终结点（仅限英语）
* 用于实体识别和实体链接的不同终结点。

实体链接支持英语和西班牙语。 NER 语言支持因实体类型而异。 有关详细信息，请参阅下面的链接。 

> [!div class="nextstepaction"]
> [了解有关命名实体识别 v3 的详细信息](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>情绪分析 v3 公共预览版-2019 年10月

下一版本的情绪分析现在可用于公共预览版，并在 API 的文本分类和评分方面提供了显著的改进。 它还提供：

* 为文本中的不同情绪自动添加标签。
* 在文档和句子级别上情绪分析和输出。 

它支持英语（`en`）、日语（`ja`）、简体中文（`zh-Hans`）、繁体中文（`zh-Hant`）、法语（`fr`）、意大利语（`it`）、西班牙语（`es`）、荷兰语（`nl`）、葡萄牙语（`pt`）和德语（`de`）。以下区域提供和： `Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe`和 `West US 2`。

> [!div class="nextstepaction"]
> [详细了解情绪分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>后续步骤

* [什么是文本分析 API？](overview.md)  
* [示例用户方案](text-analytics-user-scenarios.md)
* [情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [语言检测](how-tos/text-analytics-how-to-language-detection.md)
* [实体识别](how-tos/text-analytics-how-to-entity-linking.md)
* [关键短语提取](how-tos/text-analytics-how-to-keyword-extraction.md)
