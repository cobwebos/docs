---
title: AI 扩充功能的文档链接
titleSuffix: Azure Cognitive Search
description: 与 Azure 认知搜索中的 AI 扩充工作负荷相关的文章、教程、示例和博客文章的批注列表。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935341"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Azure 认知搜索中的 AI 扩充文档资源

AI 扩充是基于索引器的索引的加载项，用于查找非文本源和无差别文本中的潜在信息，并将其转换为 Azure 认知搜索中的全文可搜索内容。 

进行内置处理时，系统会在内部调用认知服务中预先训练的 AI 模型来执行分析。 你也可以使用 Azure 机器学习、Azure Functions 或其他方法来集成自定义模型。

下面是 AI 扩充文档的综合列表。

## <a name="concepts"></a>概念

+ [AI 扩充](cognitive-search-concept-intro.md)
+ [技能组](cognitive-search-working-with-skillsets.md)
+ [调试会话](cognitive-search-debug-session.md)
+ [知识存储](knowledge-store-concept-intro.md)
+ [投影](knowledge-store-projection-overview.md)
+ [增量扩充（重复使用缓存的扩充文档）](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>动手演练

+ [快速入门：在 Azure 门户中创建认知技能集](cognitive-search-quickstart-blob.md)
+ [教程：使用 AI 扩充的索引](cognitive-search-tutorial-blob.md)
+ [教程：通过调试会话诊断、修复并提交对技能组合的更改](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>知识存储

+ [快速入门：在 Azure 门户中创建知识存储](knowledge-store-create-portal.md)
+ [使用 REST 和 Postman 创建知识存储](knowledge-store-create-rest.md)
+ [使用存储资源管理器查看知识存储](knowledge-store-view-storage-explorer.md)
+ [使用 Power BI 连接知识存储](knowledge-store-connect-power-bi.md)
+ [投影示例（如何整形和导出扩充内容）](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>自定义技能（高级）

+ [如何定义自定义技能接口](cognitive-search-custom-skill-interface.md)
+ [示例：使用 Azure Functions（和必应实体搜索 API）创建自定义技能](cognitive-search-create-custom-skill-example.md)
+ [示例：使用 Python 创建自定义技能](cognitive-search-custom-skill-python.md)
+ [示例：使用窗体识别器创建自定义技能](cognitive-search-custom-skill-form.md) 
+ [示例：使用 Azure 机器学习创建自定义技能](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>操作说明指南

+ [附加认知服务资源](cognitive-search-attach-cognitive-services.md)
+ [定义技能集](cognitive-search-defining-skillset.md)
+ [在技能组中引用注释](cognitive-search-concept-annotations-syntax.md)
+ [将字段映射到索引](cognitive-search-output-field-mapping.md)
+ [处理和提取图像中的信息](cognitive-search-concept-image-scenarios.md)
+ [为增量扩充配置缓存](search-howto-incremental-index.md)
+ [如何重新生成 Azure 认知搜索索引](search-howto-reindex.md)
+ [设计提示](cognitive-search-concept-troubleshooting.md)
+ [常见错误和警告](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>技能参考

+ [内置技能](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ 自定义技能
  + [Microsoft AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [弃用的技能](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>API

+ [REST API](/rest/api/searchservice/)
  + [创建技能组 (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
  + [创建索引器 (api-version=2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>另请参阅

+ [Azure 认知搜索 REST API](/rest/api/searchservice/)
+ [Azure 认知搜索中的索引器](search-indexer-overview.md)
+ [什么是 Azure 认知搜索？](search-what-is-azure-search.md)