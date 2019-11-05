---
title: 内置的数据提取、自然语言、索引过程中的图像处理技巧。
titleSuffix: Azure Cognitive Search
description: 数据提取、自然语言、图像处理认知技能将语义和结构添加到 Azure 认知搜索管道中的原始内容。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2f03752c5fda366fc16b681d71ba68486527a30f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466796"
---
# <a name="built-in-cognitive-skills-for-content-enrichment-azure-cognitive-search"></a>内容扩充的内置认知技能（Azure 认知搜索）

本文介绍 Azure 认知搜索提供的认知技能。 认知技能是以某种方式转换内容的操作。 该组件通常用于提取数据或推断结构，因此增强了我们对输入数据的理解。 输出几乎总是基于文本。 技能组合是一组定义扩充管道的技能。 

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 在认知服务中调用 Api 时，将会产生费用，并将其作为 Azure 认知搜索中文档解密阶段的一部分进行图像提取。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 [Azure 认知搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)上介绍了图像提取定价。


## <a name="built-in-skills"></a>内置技能

多种技能可在其使用或生成的对象中灵活应用。 一般来说，大多数技能都基于预先定型的模型，这意味着你无法使用自己的定型数据来定型模型。 下表枚举并介绍了 Microsoft 提供的技能。 

| 技能 | 说明 |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | 此技能使用预定型模型来检测基于术语放置、语言规则、与其他术语的接近度以及该术语在源数据内的异常程度的重要短语。 |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | 该技能使用预定型模型来检测使用语言的语言类型（每个文档一个语言 ID）。 在同一文本段中使用多种语言时，输出是主要使用的语言的 LCID。|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | 将字段集合中的文本合并到单个字段中。  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | 此技能使用预训练模型为固定的一组类别建立实体：人员、位置、组织、电子邮件、URL、日期时间字段。 |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | 此技能使用预定型模型根据记录对记录中的正面或负面情绪进行评分。 得分介于 0 和 1 之间。 当无法检测到情绪以及文本被认为是中性时，这两个 null 事例都会出现中性分数。  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | 将文本拆分为多页，以便以增量方式扩充或增加内容。 |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | 此技能使用预先训练的模型将输入文本转换为各种语言，以用于规范化或本地化用例。 |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | 此技能使用图像检测算法来识别图像的内容并生成文本说明。 |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光学字符识别。 |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | 允许根据条件进行筛选、分配默认值和合并数据。|
| [Util. DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | 从扩充管道内的文件中提取内容。 |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 将输出映射到复杂类型（多部分数据类型，可用于全名、多行地址或姓氏和个人标识符的组合）。 |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | 允许通过对自定义 Web API 进行 HTTP 调用来实现 AI 扩充管道的可扩展性 |


有关创建[自定义技能](cognitive-search-custom-skill-web-api.md)的指南，请参阅[如何定义自定义界面](cognitive-search-custom-skill-interface.md)和[示例：为 AI 扩充创建自定义技能](cognitive-search-create-custom-skill-example.md)。

## <a name="see-also"></a>另请参阅

+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [自定义技能接口定义](cognitive-search-custom-skill-interface.md)
+ [教程：通过 AI 扩充索引](cognitive-search-tutorial-blob.md)
