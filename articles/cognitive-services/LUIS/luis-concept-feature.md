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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531892"
---
# <a name="machine-learned-features"></a>机器学习功能

在机器学习中，_功能_是系统观察&学习的数据的特征或属性。 在语言理解 （LUIS） 中，一项功能描述并解释了您的意图和实体的重要意义。

在[预览 LUIS 门户](https://preview.luis.ai)中，要素是_描述符，_ 因为它们用于_描述_意图或实体。

## <a name="features-_descriptors_-in-language-understanding"></a>语言理解中的功能 （_描述符_）

功能（也称为描述符）描述帮助语言理解识别示例陈述的线索。 功能包括：

* 短语列表作为意图或实体的功能
* 实体作为意图或实体的特征

要素应被视为模型分解架构的必要部分。

## <a name="what-is-a-phrase-list"></a>什么是短语列表

短语列表是单词、短语、数字或其他字符的列表，可帮助确定您尝试识别的概念。 该列表不区分大小写。

## <a name="when-to-use-a-phrase-list"></a>何时使用短语列表

使用短语列表，LUIS 会考虑上下文并进行一般性化，以标识与文本匹配（但不是确切文本匹配）的项目。 如果您需要 LUIS 应用能够概括和标识新项目，请使用短语列表。

当希望能够识别新实例（如应识别新联系人名称的会议计划程序）或应识别新产品的清单应用时，请从机器学习实体开始。 然后创建一个短语列表，帮助 LUIS 查找具有类似含义的单词。 此短语列表通过为这些单词的值添加其他重要性来指导 LUIS 识别示例。

短语列表就像特定域的词汇表，有助于提高意向和实体的理解质量。

## <a name="considerations-when-using-a-phrase-list"></a>使用短语列表时的注意事项

默认情况下，短语列表将应用于应用中的所有模型。 这将适用于可以跨所有意图和实体的短语列表。 对于可分解性，应仅将短语列表应用于与它相关的模型。

如果创建短语列表（默认情况下创建全局），则稍后将其作为描述符（功能）应用于特定模型，则会将其从其他模型中删除。 此删除为应用于的模型的短语列表添加了相关性，有助于提高它在模型中提供的准确性。

标志`enabledForAllModels`在 API 中控制此模型作用域。

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>如何使用短语列表

当您的意图或实体具有重要的单词或短语时，[创建短语列表](luis-how-to-add-features.md)，例如：

* 行业术语
* 俚语
* 缩写
* 公司专用语言
* 虽来自另一种语言，但在应用中经常使用的语言
* 示例言语中的关键字和短语

**不要**添加所有可能的单词或短语。 相反，一次添加几个单词或短语，然后重新训练和发布。 随着列表随时间的增长，您可能会发现某些术语具有许多窗体（同义词）。 将这些列表分解为另一个列表。

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>何时将实体用作特征

可以在意图或实体级别添加实体作为要素。

### <a name="entity-as-a-feature-to-an-intent"></a>实体作为意图的要素

当检测到该实体对意图很重要时，将实体作为描述符（功能）添加到意图中。

例如，如果意图是预订航班，而实体是机票信息（如座位数、始发地和目的地），则查找机票信息实体应为预订航班意向的预测增加权重。

### <a name="entity-as-a-feature-to-another-entity"></a>实体作为另一个实体的要素

当该实体 （A） 的检测对于预测实体 （B） 非常重要时，应将实体 （A） 作为特征添加到另一个实体 （B）。

例如，如果检测到街道地址实体 （A），则查找街道地址 （A） 会增加送货地址实体 （B） 的预测权重。

## <a name="best-practices"></a>最佳做法
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

* [在](schema-change-prediction-runtime.md)预测运行时扩展应用模型
* 请参阅[添加功能](luis-how-to-add-features.md)，了解有关如何将特征添加到 LUIS 应用的详细信息。
