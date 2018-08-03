---
title: 了解 Azure 中 LUIS 应用的特征 | Microsoft Docs
description: 了解有助于提升 LUIS 应用性能的特征。 特征包括用于识别正则表达式的短语列表和模式。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: diberry
ms.openlocfilehash: 8d3f006f27d1d728f89458deba27e1c1a63b6de5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224922"
---
# <a name="phrase-list-features-in-luis"></a>LUIS 中的短语列表特征

在机器学习中，特征是系统观察到的数据的不同特征或属性。 

将特征添加到语言模型，提供有关如何识别需要标记或分类的输入的提示。 特征帮助 LUIS 识别意向和实体，但特征本身不是意向或实体。 相反，特征可能提供相关术语的示例。  

## <a name="what-is-a-phrase-list-feature"></a>短语列表特征是什么？
短语列表包括一组值（词或短语），它们属于同一个类，并且必须同样对待（例如城市或产品名称）。 LUIS 对其中一个值的了解也自动应用到其他值。 此列表不是匹配字词的封闭[列表实体](luis-concept-entity-types.md#types-of-entities)（完全文本匹配）。

短语列表添加到应用域的词汇中，作为这些字词的第二个 LUIS 信号。

## <a name="how-to-use-phrase-lists"></a>如何使用短语列表
在人力资源应用的[简单实体教程](luis-quickstart-primary-and-secondary-data.md)中，应用使用作业类型的“工作”短语列表，例如程序员、屋顶工和秘书。 如果将其中一个值标记为机器学习实体，则 LUIS 会学习识别其他值。 

短语列表可能是可互换的也可能是不可互换的。 可互换短语列表适用于同义的值，而不可互换短语列表适用于不同义但在应用中仍需要其他信号的值。 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>
## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>短语列表有助于识别简单的可互换实体
可互换短语列表是优化 LUIS 应用性能的一种好方法。 如果应用在预测正确意向的表述或识别实体时存在困难，请思考表述是否包含异常字词，或者包含含义不明的字词。 这些词是列入短语列表的优秀候选词。

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>短语列表有助于通过更好地理解上下文来识别意向
短语列表并非指示 LUIS 执行严格匹配或始终将短语列表中的所有术语标记为完全相同的指令。 它只是一个提示。 例如，短语列表可以将“Patti”和“Selma”指示为姓名，但 LUIS 仍然可以使用上下文信息识别它们在“Make a reservation for 2 at Patti's Diner for dinner”和“Find me driving directions to Selma, Georgia”中意义不同。 

添加短语列表是将更多示例表述添加到意向的替代方法。 

## <a name="an-interchangeable-phrase-list"></a>可互换短语列表
字词或短语列表创建类或组时，使用可互换短语列表。 例如“January”、“February”、“March”等月份列表；或者像“John”、“Mary”、“Frank”这样的名字列表。  这些列表是可互换的，如果使用短语列表中的不同字词，话语将被标记为具有相同的意向或实体。 例如，如果“show the calendar for January”和“show the calendar for February”具有相同的意向，那么字词应位于可互换列表中。 

## <a name="a-non-interchangeable-phrase-list"></a>不可互换短语列表
对于可在域中分组的非同义字词或短语，请使用不可互换短语列表。 

例如，对于罕见词、专有词和外来词使用不可互换短语列表。 LUIS 可能无法识别罕见词、专有词以及外来词（在应用区域性以外）。 不可互换设置指示罕见字词集组成 LUIS 应学会识别的类，但它们不是同义词，也不能彼此互换。

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>何时使用短语列表与列表实体
尽管短语列表和列表实体都可以影响所有意向中的表述，但各自实现的方式不同。 短语列表用于影响意向预测评分。 列表实体用于影响完全文本匹配的实体提取。 

### <a name="use-a-phrase-list"></a>使用短语列表
有了短语列表，LUIS 仍可以考虑上下文并进行归纳，从而标识与列表项相似但并非完全匹配的项。 如果需要 LUIS 应用能够归纳和识别分类中的新项，请使用短语列表。 

如果想要能够识别实体的新实例（例如：应识别新联系人姓名的会议计划程序、应识别新产品的库存应用），请使用另一类型的机器学习实体，例如简单或分层实体。 然后，创建字词和短语的短语列表，有助于 LUIS 查找与实体相似的其他字词。 此列表通过增加这些字词的价值来指导 LUIS 识别实体的示例。 

短语列表就像特定域的词汇表，有助于提高意向和实体的理解质量。 短语列表的常见用法是专有名词，例如城市名。 城市名称可以是包括连字符或撇号的多个字词。
 
### <a name="dont-use-a-phrase-list"></a>请勿使用短语列表 
列表实体显式定义实体可以采用的每个值，并仅标识完全匹配的值。 列表实体可能适用于其中某实体的所有实例已知且不常更改的应用。 例如餐厅菜单上不常更改的菜品项。 如果需要实体的完全文本匹配，请勿使用短语列表。 

## <a name="best-practices"></a>最佳实践
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

请参阅[添加功能](luis-how-to-add-features.md)，了解有关如何将特征添加到 LUIS 应用的详细信息。
