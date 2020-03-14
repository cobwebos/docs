---
title: 功能-LUIS
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
ms.openlocfilehash: b151043babd6c67a17f704bb671a415fcc3ba7b2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220992"
---
# <a name="machine-learned-features"></a>计算机学习的功能 

在机器学习中，_功能_是您的系统在学习 & 的情况下所观察到的数据的特征或属性。 在语言理解（LUIS）中，一项功能描述和说明了对你的意图和实体的重要程度。

在[预览版 LUIS 门户](https://preview.luis.ai)中，功能是_描述符_，因为它们用于_描述_意向或实体。  

## <a name="features-_descriptors_-in-language-understanding"></a>语言理解中的功能（_描述符_）

功能（也称为描述符）描述了有助于语言理解确定示例最谈话的线索。 功能包括： 

* 短语列表作为意向或实体的功能
* 作为意向或实体功能的实体

应将功能视为模型分解的架构的必需部分。 

## <a name="what-is-a-phrase-list"></a>什么是短语列表

短语列表是可帮助标识您尝试识别的概念的单词、短语、数字或其他字符的列表。 列表不区分大小写。 

## <a name="when-to-use-a-phrase-list"></a>何时使用短语列表

使用短语列表，LUIS 将上下文和通用化视为类似于的项，而不是精确的文本匹配项。 如果需要 LUIS 应用才能通用化和标识新项目，请使用短语列表。 

如果希望能够识别新的实例（如应识别新联系人姓名的会议计划程序或应识别新产品的清单应用程序），请从机器学习的实体开始。 然后创建一个短语列表，帮助 LUIS 查找具有类似含义的单词。 此短语列表指导 LUIS 通过向这些单词的值添加其他意义来识别示例。 

短语列表就像特定域的词汇表，有助于提高意向和实体的理解质量。 

## <a name="considerations-when-using-a-phrase-list"></a>使用短语列表时的注意事项

默认情况下，将对应用程序中的所有模型应用短语列表。 这适用于可跨所有意图和实体的短语列表。 对于 decomposability，只应将短语列表应用于与其相关的模型。 

如果创建了短语列表（默认情况下在全局范围内创建），然后将其作为描述符（功能）应用于特定模型，则会将其从其他模型中删除。 此删除操作会为其所应用到的模型的短语列表添加相关性，有助于提高它在模型中提供的准确性。 

`enabledForAllModels` 标志控制 API 中的此模型作用域。 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>如何使用短语列表

当意向或实体包含重要的词或短语时，[创建短语列表](luis-how-to-add-features.md)，如：

* 行业术语
* 俚语
* 缩写
* 公司专用语言
* 虽来自另一种语言，但在应用中经常使用的语言
* 示例言语中的关键字和短语

不要**添加每**个可能的单词或短语。 相反，请一次添加几个字词或短语，然后重新训练并发布。 随着时间的推移，你可能会发现一些术语具有多种形式（同义词）。 将这些问题分解为另一个列表。 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>何时使用实体作为功能 

实体可以作为功能添加到目的或实体级别。 

### <a name="entity-as-a-feature-to-an-intent"></a>实体即功能到目的

当该实体的检测对于意向非常重要时，将实体作为描述符（功能）添加到意向。

例如，如果此目的是为了预订航班，而实体是票据信息（如座位数量、来源和目标），则查找 "票证信息" 实体应将 "权重" 添加到 "书籍航班意向预测"。 

### <a name="entity-as-a-feature-to-another-entity"></a>作为另一个实体的功能的实体

当实体（A）的检测对于实体（B）的预测很重要时，应将实体（A）作为功能添加到另一个实体（B）。

例如，如果检测到街道地址实体（A），则查找街道地址（A）会将权重添加到寄送地址实体的预测（B）。 

## <a name="best-practices"></a>最佳做法
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

请参阅[添加功能](luis-how-to-add-features.md)，了解有关如何将特征添加到 LUIS 应用的详细信息。