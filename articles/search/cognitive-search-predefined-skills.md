---
title: 预定义数据提取、自然语言、图像处理技能（Azure 搜索）| Microsoft Docs
description: 数据提取、自然语言、图像处理认知技能将语义和结构添加到 Azure 搜索管道中的原始内容。
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 870cf9629c7af8faee0ce5709199b64910b27ffb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786696"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>内容扩充的预定义技能（Azure 搜索）

本文介绍认知搜索提供的认知技能。 认知技能是以某种方式转换内容的操作。 该组件通常用于提取数据或推断结构，因此增强了我们对输入数据的理解。 输出几乎总是基于文本。 技能组合是一组定义扩充管道的技能。 

## <a name="predefined-skills"></a>预定义技能

多种技能可在其使用或生成的对象中灵活应用。 一般来说，大多数技能都基于预先定型的模型，这意味着你无法使用自己的定型数据来定型模型。 有关创建自定义技能的指导，请参阅[如何定义自定义界面](cognitive-search-custom-skill-interface.md)和[示例：创建自定义技能](cognitive-search-create-custom-skill-example.md)。 下表枚举并介绍了 Microsoft 提供的技能。 

| 技能 | 说明 |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | 此技能使用预定型模型来检测基于术语放置、语言规则、与其他术语的接近度以及该术语在源数据内的异常程度的重要短语。 |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | 该技能使用预定型模型来检测使用语言的语言类型（每个文档一个语言 ID）。 在同一文本段中使用多种语言时，输出是主要使用的语言的 LCID。|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | 将字段集合中的文本合并到单个字段中。  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | 此技能使用预定型模型为一组固定的类别构建实体：人员、位置、组织。 |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | 此技能使用预定型模型根据记录对记录中的正面或负面情绪进行评分。 得分介于 0 和 1 之间。 当无法检测到情绪以及文本被认为是中性时，这两个 null 事例都会出现中性分数。  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | 将文本拆分为多页，以便以增量方式扩充或增加内容。 |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | 此技能使用图像检测算法来识别图像的内容并生成文本说明。 |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | 光学字符识别。 |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | 将输出映射到复杂类型（多部分数据类型，可用于全名、多行地址或姓氏和个人标识符的组合）。 |

## <a name="see-also"></a>另请参阅

+ [如何定义技能组合](cognitive-search-defining-skillset.md)
+ [自定义技能接口定义](cognitive-search-custom-skill-interface.md)
+ [教程：具有认知搜索的已扩充索引](cognitive-search-tutorial-blob.md)