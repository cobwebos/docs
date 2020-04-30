---
title: 特征 - LUIS
titleSuffix: Azure Cognitive Services
description: 将特征添加到语言模型，提供有关如何识别需要标记或分类的输入的提示。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 5b8257e24cf52d01be8065d97db17fd685aa316d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531892"
---
# <a name="machine-learned-features"></a>机器习得的特征

在机器学习中，特征是系统观察到的以及习得的数据的判别特征或属性。  在语言理解 (LUIS) 中，特征描述并解释意向和实体的要素。

在[预览版 LUIS 门户](https://preview.luis.ai)中，特征是描述符，因为它们用于描述意向或实体。  

## <a name="features-_descriptors_-in-language-understanding"></a>语言理解中的特征（描述符） 

特征（也称为描述符）描述可帮助语言理解识别示例言语的线索。 功能包括：

* 用作意向或实体特征的短语列表
* 用作意向或实体特征的实体

应将特征视为模型分解架构的必要组成部分。

## <a name="what-is-a-phrase-list"></a>什么是短语列表

短语列表是单词、短语、数字或其他字符的列表，可帮助识别你要尝试识别的概念。 列表不区分大小写。

## <a name="when-to-use-a-phrase-list"></a>何时使用短语列表

使用短语列表时，LUIS 会考虑上下文并归纳内容，以识别类似的但不完全匹配文本的项。 如果需要 LUIS 应用能够归纳和识别新项，请使用短语列表。

如果想要能够识别新实例，例如，应识别新联系人姓名的会议计划程序，或应识别新产品的库存应用，请从机器习得的实体开始。 然后创建一个短语列表，以帮助 LUIS 查找具有类似含义的单词。 此短语列表通过将其他重要性添加到这些单词的价值来指导 LUIS 识别示例。

短语列表就像特定域的词汇表，有助于提高意向和实体的理解质量。

## <a name="considerations-when-using-a-phrase-list"></a>使用短语列表时的注意事项

默认情况下，将对应用中的所有模型应用短语列表。 这适用于可跨所有意向和实体的短语列表。 对于可分解性，应该只将短语列表应用到与其相关的模型。

如果创建短语列表（默认会全局创建），然后将其作为描述符（特征）应用到特定的模型，则会从其他模型中删除此短语列表。 此删除操作会将相关性添加到相应模型的短语列表，帮助改善它在模型中提供的准确度。

`enabledForAllModels` 标志控制 API 中的此模型范围。

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>如何使用短语列表

如果意向或实体包含重要的单词或短语，请[创建短语列表](luis-how-to-add-features.md)，例如：

* 行业术语
* 俚语
* 缩写
* 公司专用语言
* 虽来自另一种语言，但在应用中经常使用的语言
* 示例言语中的关键字和短语

**不要**添加每个可能的单词或短语。 应该每次添加几个单词或短语，然后重新训练并发布。 随着列表不断扩大，你可能发现一些字词有多种形式（同义词）。 请将这些形式划分到另一个列表。

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>何时将实体用作特征

可以在意向或实体级别将实体添加为特征。

### <a name="entity-as-a-feature-to-an-intent"></a>实体用作意向特征

如果该实体的检测对于意向非常重要，请将实体作为描述符（特征）添加到意向。

例如，如果意向是预订航班，实体是机票信息（例如座位数量、出发地和目的地），则查找机票信息实体应会将权重添加到航班预订意向的预测。

### <a name="entity-as-a-feature-to-another-entity"></a>实体用作另一个实体的特征

如果实体 (A) 的检测对于另一个实体 (B) 的预测非常重要，则应将实体 (A) 作为特征添加到实体 (B)。

例如，如果检测到街道地址实体 (A)，则查找街道地址 (A) 会将权重添加到发货地址实体 (B) 的预测。

## <a name="best-practices"></a>最佳做法
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

* 在预测运行时[扩展](schema-change-prediction-runtime.md)应用模型
* 请参阅[添加功能](luis-how-to-add-features.md)，了解有关如何将特征添加到 LUIS 应用的详细信息。
