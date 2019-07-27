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
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 1aed6f9a0ceec18ca800e5030ec09bbb8d98cb76
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560726"
---
# <a name="phrase-list-features-in-your-luis-app"></a>LUIS 应用中的短语列表特征

在机器学习中，*特征*是系统观察到的数据的特征或特有属性。 

将特征添加到语言模型，提供有关如何识别需要标记或分类的输入的提示。 特征帮助 LUIS 识别意向和实体，但特征本身不是意向或实体。 相反，特征可能提供相关术语的示例。  

## <a name="what-is-a-phrase-list-feature"></a>短语列表特征是什么？
短语列表是比言语中其他字词对应用更重要的字词或短语的列表。 短语列表作为这些字词的附加 LUIS 信号，添加到应用域的词汇中。 LUIS 对其中一个值的了解也自动应用到其他值。 此列表不是完全文本匹配的封闭式[列表实体](luis-concept-entity-types.md#types-of-entities)。

短语列表没有词干分解作用，因此需要添加对任何重要词汇字词和短语使用各种词干分解的言语示例。

## <a name="phrase-lists-help-all-models"></a>短语列表可以帮助所有模型

短语列表虽未与特定意向或实体关联，但却作为重要增强项添加到所有意向和实体。 其目的是改进意向检测和实体分类。

## <a name="how-to-use-phrase-lists"></a>如何使用短语列表

创建短语列表的情况为，应用有对应用重要的字词或短语，如：

* 行业术语
* 俚语
* 缩写
* 公司专用语言
* 虽来自另一种语言，但在应用中经常使用的语言
* 示例言语中的关键字和短语

输入几个字词或短语后，立即使用“建议”功能来查找相关值。 先检查相关值，再添加到短语列表值。

|列表类型|用途|
|--|--|
|可互换|将其更换为列表中的其他字词后具有相同意向和实体提取的同义词或字词。|
|不可互换|相对于该语言中的其他通用字词，更特定于应用的应用词汇。|

### <a name="interchangeable-lists"></a>可交换列表

可交换短语列表适用于作为同义词的值。 例如，如果希望找到所有水体，且有示例言语，如： 

* 什么城市靠近五大湖？ 
* 什么道路沿着哈瓦苏湖城走？
* 尼罗河的起点和终点在哪里？ 

应确定每个言语的意向和实体，而不考虑水体： 

* 什么城市靠近 [bodyOfWater]？
* 什么道路沿着 [bodyOfWater] 走？
* [bodyOfWater] 的起点和终点在哪里？ 

由于水体的字词或短语是同义词，并能在言语中交换使用，因此对短语列表使用“可交换”设置。 

### <a name="non-interchangeable-lists"></a>不可交换列表

不可交换短语列表是增强 LUIS 检测的信号。 短语列表代表比其他字词更重要的字词或短语。 这有助于确定意向和实体检测。 例如，假设有全局主题域（即跨区域性，但仍为一种语言），如旅行。 虽有对应用重要的字词和短语，但它们不是同义词。 

再比如，对罕见词、专有词和外来词使用不可交换短语列表。 LUIS 可能无法识别罕见词、专有词以及外来词（在应用区域性以外）。 不可互换设置指示罕见字词集组成 LUIS 应学会识别的类，但它们不是同义词，也不能彼此互换。

不要将每个可能的字词或短语都添加到短语列表，一次添加几个字词或短语，再重新训练和发布。 

随着短语列表随时间越来越长，可能会发现一些术语有许多种形式（同义词）。 将它们分入另一个可互换的短语列表。 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>短语列表有助于识别简单的可互换实体
可互换短语列表是优化 LUIS 应用性能的一种好方法。 如果应用在预测正确意向的表述或识别实体时存在困难，请思考表述是否包含异常字词，或者包含含义不明的字词。 这些词是列入短语列表的优秀候选词。

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>短语列表有助于通过更好地理解上下文来识别意向
短语列表并非指示 LUIS 执行严格匹配或始终将短语列表中的所有术语标记为完全相同的指令。 它只是一个提示。 例如，短语列表可以将“Patti”和“Selma”指示为姓名，但 LUIS 仍然可以使用上下文信息识别它们在“Make a reservation for 2 at Patti's Diner for dinner”和“Find me driving directions to Selma, Georgia”中意义不同。 

添加短语列表是将更多示例表述添加到意向的替代方法。 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>何时使用短语列表与列表实体
尽管短语列表和列表实体都可以影响所有意向中的表述，但各自实现的方式不同。 短语列表用于影响意向预测评分。 列表实体用于影响完全文本匹配的实体提取。 

### <a name="use-a-phrase-list"></a>使用短语列表
有了短语列表，LUIS 仍可以考虑上下文并进行归纳，从而标识与列表项相似但并非完全匹配的项。 如果需要 LUIS 应用能够归纳和识别分类中的新项，请使用短语列表。 

如果想要能够识别实体的新实例（例如：应识别新联系人姓名的会议计划程序、应识别新产品的库存应用），请使用另一类型的机器学习到的实体，例如简单实体。 然后，创建字词和短语的短语列表，有助于 LUIS 查找与实体相似的其他字词。 此列表通过增加这些字词的价值来指导 LUIS 识别实体的示例。 

短语列表就像特定域的词汇表，有助于提高意向和实体的理解质量。 短语列表的常见用法是专有名词，例如城市名。 城市名称可以是包括连字符或撇号的多个字词。
 
### <a name="dont-use-a-phrase-list"></a>请勿使用短语列表 
列表实体显式定义实体可以采用的每个值，并仅标识完全匹配的值。 列表实体可能适用于其中某实体的所有实例已知且不常更改的应用。 例如餐厅菜单上不常更改的菜品项。 如果需要实体的完全文本匹配，请勿使用短语列表。 

## <a name="best-practices"></a>最佳实践
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

请参阅[添加功能](luis-how-to-add-features.md)，了解有关如何将特征添加到 LUIS 应用的详细信息。
