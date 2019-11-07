---
title: AI 扩充功能的文档链接
titleSuffix: Azure Cognitive Search
description: 与 Azure 认知搜索中的 AI 扩充工作负荷相关的文章、教程、示例和博客文章的批注列表。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: cf0332909e053a0875d41a00d15f196b193ba15e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466715"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Azure 认知搜索中的 AI 扩充文档资源

AI 扩充是 Azure 认知搜索索引的一项功能，用于查找非文本源和无差别文本中的潜在信息，并将其转换为 Azure 认知搜索中的全文可搜索内容。

下文是 AI 扩充的完整文档。

## <a name="getting-started"></a>入门
+ [认知搜索中的 AI 简介](cognitive-search-concept-intro.md)
+ [快速入门：在 Azure 门户中创建认知技能集](cognitive-search-quickstart-blob.md)
+ [教程：使用 AI 扩充的索引](cognitive-search-tutorial-blob.md)
+ 示例：[创建 AI 扩充的自定义技能](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>操作说明指南
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何在技能集中引用注释](cognitive-search-concept-annotations-syntax.md)
+ [如何将字段映射到索引](cognitive-search-output-field-mapping.md)
+ [处理和提取图像中的信息](cognitive-search-concept-image-scenarios.md)
+ [如何重新生成 Azure 认知搜索索引](search-howto-reindex.md)
+ [如何定义自定义技能接口](cognitive-search-custom-skill-interface.md)
+ [故障排除提示](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>引用

+ [内置技能](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ 自定义技能
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [弃用的技能](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [创建技能集 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [创建索引器 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Azure 认知搜索中的索引器](search-indexer-overview.md)
+ [什么是 Azure 认知搜索？](search-what-is-azure-search.md)
