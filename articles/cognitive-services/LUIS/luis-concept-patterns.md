---
title: 了解模式如何提高预测的准确性 | Microsoft Docs
titleSuffix: Azure
description: 了解设计模式如何提高意向预测得分和找到实体。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: c08419e3fb5b25284121a0eac30c38c8ba7570f1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225211"
---
# <a name="patterns-improve-prediction-accuracy"></a>模式可提高预测的准确性
模式旨在多条话语非常类似的情况下提升准确性。 通过提供话语模式，LUIS 可具有极高的预测置信度。 

## <a name="patterns-solve-low-intent-confidence"></a>模式解决了意向置信度低的问题
设想一个人力资源应用，它在组织结构图上报告某员工的相关信息。 如果具有员工的姓名和组织关系，LUIS 就能反馈所涉及的员工。 设想员工叫做孔西明，经理叫做柏隼，下属员工有翁捷生、康霓和尹锋。

![组织结构图](./media/luis-concept-patterns/org-chart.png)

|陈述|预测的意向|意向得分|
|--|--|--|
|孔西明的下属员工有谁？|获取组织结构组|.30|
|谁归孔西明管辖？|获取组织结构组|.30|

如果应用具有 10 到 20 条句子长度不同、字词顺序不同，甚至用词不同（“下属员工”、“经理”和“报告”的近义词）的话语，则 LUIS 返回的置信度得分可能较低。 为了帮助 LUIS 理解字词顺序的重要性，需创建一个模式。 

模式可处理以下情况： 

* 当意向得分较低时
* 当正确的意向得分不是最高但太接近最高得分。 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>模式不保证提取意向
模式使用多种预测技术。 在模式中设置模板话语的意向并不保证得出意向预测，但表示很可能得出。 

## <a name="patterns-do-not-improve-entity-detection"></a>模式不提升检测到实体的可能性
当模式需要实体时，它不帮助检测实体。 模式仅表示使用意向和角色帮助作出预测。  

## <a name="patterns-use-entity-roles"></a>模式使用实体角色
如果模式中的两个或多个实体具有相关的上下文，模式会使用实体[角色](luis-concept-roles.md)来提取关于实体的上下文信息。 这相当于分层实体子对象，但仅可在模式中使用。 

## <a name="prediction-scores-with-and-without-patterns"></a>使用和不使用模式的情况下的预测得分
假定有足够多的实例话语，则 LUIS 不使用模式即能提高预测的置信度。 无需提供尽量多的话语，模式就能增加置信度得分。  

## <a name="pattern-matching"></a>模式匹配
模式的匹配顺序是先检测模式内的实体，再验证模式的其余字词和字词顺序。 要匹配模式，需要在模式中包含实体。 

## <a name="pattern-syntax"></a>模式语法
模式语法是一个话语模板。 该模板应包含要匹配的字词和实体，还要包含希望忽略的字词和标点。 它不是一个正则表达式。 

模式中的实体用花括号 `{}` 括起。 模式可包含实体和带角色的实体。 Pattern.any 是一种只在模式中使用的实体。 以下部分介绍了语法。

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>用于向模式模板添加实体的语法
要向模式模板添加实体，需用花括号将实体名称括起，比如 `Who does {Employee} manage?`。 

```
Who does {Employee} manage?
```

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>用于向模式模板添加实体和角色的语法
实体角色表示为 `{entity:role}`其中实体名称后接一个冒号，再接角色名称。 要向模式模板添加带角色的实体，需用花括号将实体名称和角色名称括起，比如 `Book a ticket from {Location:Origin} to {Location:Destination}`。 

```
Book a ticket from {Location:Origin} to {Location:Destination}
```

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>用于向模式模板添加 pattern.any 的语法
Pattern.any 实体可用于向模式添加不同长度的实体。 只要按照模式模板操作，pattern.any 即可为任意长度。 

要向模式模板添加 Pattern.any 实体，需用花括号将 Pattern.any 实体括起，比如 `How much does {Booktitle} cost and what format is it available in?`。  

```
How much does {Booktitle} cost and what format is it available in?
```

|模式中的书名|
|--|
|《偷书》的价格是多少？它适合哪种格式？|
|《询问》的价格是多少？它适合哪种格式？|
|《深夜小狗神秘事件》的价格是多少？它适合哪种格式？| 

在这些书名示例中，书名的上下文字词不会让 LUIS 混乱。 LUIS 知道哪一截是书名，因为书名位于模式中且用 Pattern.any 实体标记。

### <a name="explicit-lists"></a>显式列表
如果模式包含 Pattern.any，且模式语法可能基于话语提取出错误的实体，需通过创作 API 创建一个[显式列表](https://aka.ms/ExplicitList)，允许出现此异常。 

例如，假设你的模式中包含可选语法 `[]` 和实体语法 `{}`，两个语法的组合方式导致数据提取错误。

请考虑使用模式“[find] email about {subject} [from {person}]”。 在下面的话语中，主题和人员实体一个提取正确，一个提取错误：

|话语|实体|提取是否正确|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

在上表中，话语 `email about the man from La Mancha` 的主题应该是 `the man from La Mancha`（书名），但因为主题包含可选字词 `from`书名被预测错误。 

要避免模式出现此异常，需使用[显式列表的创作 API](https://aka.ms/ExplicitList) 添加 `the man from la mancha` 作为 {subject} 实体的显式列表匹配。

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>用于在模板话语中标记可选文本的语法
使用正则表达式方括号语法 `[]` 标出话语中的可选文本。 最多能对两个括号使用方括号嵌套可选文本。

```
[find] email about {subject} [from {person}]
```

可使用方括号忽略 `.``!` 和 `?` 等标点符号。 要忽略这些标记，必须将每个标记包含在单独的模式中。 可选语法目前不支持在多个项目的列表中忽略一个项目。

## <a name="patterns-only"></a>仅限模式
即使意向中没有任何实例话语，应用也可通过 LUIS 运行。 这种用法仅在使用模式的情况下使用。 模式要求每个模式至少有一个实体。 对于只有模式的应用，模式不能包含通过机器学习到的实体，因为这些实体要求具有示例话语。 

## <a name="best-practices"></a>最佳实践
了解[最佳实践](luis-concept-best-practices.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在本教程中学习如何实现模式](luis-tutorial-pattern.md)