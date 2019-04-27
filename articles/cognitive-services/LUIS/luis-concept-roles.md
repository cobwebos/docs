---
title: 实体角色
titleSuffix: Azure Cognitive Services
description: 角色是仅在模式中使用的实体的已命名上下文子类型。 例如，在表述 `buy a ticket from New York to London` 中，纽约和伦敦都是城市，但是两者在句中的含义不同。 纽约为出发城市，伦敦为目标城市。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 5fa922cb91d34483256faf4dcf70569aa2f17b97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813831"
---
# <a name="entity-roles-for-contextual-subtypes"></a>上下文的子类型的实体角色

角色允许实体具有名为的子类型。 角色可与任何预生成的或自定义的实体类型配合使用，并可在示例言语和模式中使用。 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>机器学习的内容的实体的角色的示例

在查询文本"购买从票证**纽约**到**伦敦**、 New York 和伦敦城市但每个句子中具有不同的含义。 纽约为出发城市，伦敦为目标城市。 

```
buy a ticket from New York to London
```

角色为这些差异命名：

|实体类型|实体名称|角色|目的|
|--|--|--|--|
|简单|Location|origin|飞机起飞地|
|简单|Location|目标|飞机降落地|

## <a name="non-machine-learned-entity-example-of-roles"></a>非机器学习了实体的角色的示例

在查询文本"计划到 9 8 会议"，这两个数字表示一次，但每次在查询文本中具有不同的含义。 角色提供名称的差异。 

```
Schedule the meeting from 8 to 9
```

|实体类型|角色名称|值|
|--|--|--|
|预生成的 datetimeV2|开始时间|8|
|预生成的 datetimeV2|结束时间|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>在查询文本中的多个实体与角色相同的功能？ 

多个实体可以存在于查询文本，并且可以提取不使用角色。 如果句子的上下文具有指示版本的实体具有一个值，则应使用角色。 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>不要为重复项的含义不使用角色

如果查询文本中包含的位置，列表`I want to travel to Seattle, Cairo, and London.`，这是一个列表的每个项没有其他意义。 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>如果重复项表示的含义，请使用角色

如果查询文本中包含有意义的位置的列表`I want to travel from Seattle, with a layover in Londen, landing in Cairo.`，应使用角色捕获此原点、 里，以及目标的含义。

### <a name="roles-can-indicate-order"></a>角色可以指示订单

如果查询文本更改，以指示你想要提取，顺序`I want to first start with Seattle, second London, then third Cairo`，可以在两种方法中提取。 你可以标记指示该角色，标记`first start with`， `second`， `third`。 您还可以使用预生成的实体**序号**并**GeographyV2**在复合实体中捕获的顺序和位置概念的预建的实体。 

## <a name="how-are-roles-used-in-example-utterances"></a>如何在示例查询文本中使用角色？

当实体具有一个角色，并且该实体标记中的示例查询文本时，可以选择只是实体，或选择的实体和角色的选择。 

以下示例查询文本使用实体和角色：

|标记视图|实体视图|
|--|--|
|我所感学习更多有关**西雅图**|我感兴趣学习有关 {Location} 的详细信息|
|从西雅图到纽约购买票证|购买票证来源 {位置:} 到 {位置： 目标}|

## <a name="how-are-roles-related-to-hierarchical-entities"></a>如何与层次结构的实体相关角色？

角色现可用于示例查询文本，以及上一使用模式中的所有实体。 因为这些信息可无处不在它们会取代分层实体的需求。 使用角色，而不是使用分层实体，应创建新的实体。 

层次结构的实体最终将被弃用。

## <a name="how-are-roles-used-in-patterns"></a>如何在模式中使用角色？
在模式的模板表述中，角色在表述内使用： 

|模式与实体角色|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>模式中的角色语法
实体和角色在括号 `{}` 内。 实体和角色由冒号分隔。 

## <a name="entity-roles-versus-collaborator-roles"></a>实体与协作者角色的角色

实体的角色适用于 LUIS 应用的数据模型。 [协作者](luis-concept-collaborator.md)角色适用于创作访问的级别。 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>后续步骤

* 使用[动手教程](tutorial-entity-roles.md)实体角色使用机器学习实体
* 了解如何添加[角色](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
