---
title: 认知搜索文档资源（Azure 搜索）| Microsoft 文档
description: 与 Azure 搜索中的认知搜索工作负载相关的文章、教程、示例和博客文章的批注列表。
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: a96b66f61b19d218c5708a0ce967e0033ba26627
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786676"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>认知搜索工作负载的文档资源

认知搜索（现在在公共预览版中）是 Azure 搜索索引中的新扩充层，用于查找非文本源和无差别文本中的潜在信息，将其转换为 Azure 搜索中的可全文搜索内容。

下文是认知搜索的完整文档。

## <a name="getting-started"></a>入门
+ [什么是认知搜索？](cognitive-search-concept-intro.md)
+ [快速入门：在门户中尝试认知搜索](cognitive-search-quickstart-blob.md)
+ [教程：了解认知搜索 API](cognitive-search-tutorial-blob.md)
+ [示例：创建自定义技能](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>操作说明指南
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何在技能集中引用注释](cognitive-search-concept-annotations-syntax.md)
+ [如何将字段映射到索引](cognitive-search-output-field-mapping.md)
+ [处理和提取图像中的信息](cognitive-search-concept-image-scenarios.md)
+ [如何重新生成 Azure 搜索索引](search-howto-reindex.md)
+ [如何定义自定义技能接口](cognitive-search-custom-skill-interface.md)
+ [故障排除提示](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>引用

+ [预定义的技能](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [预览 REST API](search-api-2017-11-11-preview.md)
  + [创建技能集 (api-version=2017-11-11-Preview)](ref-create-skillset.md)
  + [创建索引器 (api-version=2017-11-11-Preview)](ref-create-indexer.md)

## <a name="see-also"></a>另请参阅

+ [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Azure 搜索中的索引器](search-indexer-overview.md)
+ [什么是 Azure 搜索？](search-what-is-azure-search.md)