---
title: 内置数据提取、自然语言、图像处理 - Azure 搜索
description: 数据提取、自然语言、图像处理认知技能将语义和结构添加到 Azure 搜索管道中的原始内容。
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bc1353ffb4514622ce0ef6e5c3ced76adc7f999f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314782"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>内容扩充的预定义技能（Azure 搜索）

本文介绍 Azure 搜索提供的认知技能。 认知技能是以某种方式转换内容的操作。 该组件通常用于提取数据或推断结构，因此增强了我们对输入数据的理解。 输出几乎总是基于文本。 技能组合是一组定义扩充管道的技能。 

> [!NOTE]
> 自 2018 年 12 月 21 日起，你可将认知服务资源与 Azure 搜索技能集进行关联。 这将使我们能够开始收取技能集执行的费用。 在此日期，我们还会开始将图像提取视为文档破解阶段的一部分进行计费。 我们将继续提供文档文本提取服务（不收取额外费用）。
>
> 内置技能的执行将按现有的[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)进行计费。 图像提取费用将按预览版定价进行计费，详见 [Azure 搜索定价页面](https://go.microsoft.com/fwlink/?linkid=2042400)。 了解[详细信息](cognitive-search-attach-cognitive-services.md)。

## <a name="predefined-skills"></a>预定义技能

多种技能可在其使用或生成的对象中灵活应用。 一般来说，大多数技能都基于预先定型的模型，这意味着你无法使用自己的定型数据来定型模型。 有关创建自定义技能的指导，请参阅[如何定义自定义界面](cognitive-search-custom-skill-interface.md)和[示例：创建自定义技能](cognitive-search-create-custom-skill-example.md)。 下表枚举并介绍了 Microsoft 提供的技能。 

| 技能 | Description |
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

+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [自定义技能接口定义](cognitive-search-custom-skill-interface.md)
+ [教程：使用认知搜索扩充索引编制](cognitive-search-tutorial-blob.md)