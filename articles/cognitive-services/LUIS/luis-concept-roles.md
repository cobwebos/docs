---
title: 实体角色-LUIS
titleSuffix: Azure Cognitive Services
description: 角色是仅在模式中使用的实体的已命名上下文子类型。 例如，在表述 `buy a ticket from New York to London` 中，纽约和伦敦都是城市，但是两者在句中的含义不同。 纽约为出发城市，伦敦为目标城市。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b4bd61ea74055a04718d8a9d8d5ccd42671af2ac
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638350"
---
# <a name="entity-roles-for-contextual-subtypes"></a>上下文子类型的实体角色

角色允许实体使用命名子类型。 角色可与任何预生成的或自定义的实体类型配合使用，并可在示例言语和模式中使用。 

<a name="example-role-for-entities"></a>
<a name="roles-with-prebuilt-entities"></a>

## <a name="machine-learned-entity-example-of-roles"></a>角色的机器学习实体示例

在购买从**纽约**到**伦敦**的车票的言语中，纽约和伦敦都是城市，但是两者在句中的含义不同。 纽约为出发城市，伦敦为目标城市。 

```
buy a ticket from New York to London
```

角色为这些差异命名：

|实体类型|实体名称|Role|用途|
|--|--|--|--|
|简单|Location|原点|飞机起飞地|
|简单|Location|destination|飞机降落地|

## <a name="non-machine-learned-entity-example-of-roles"></a>角色的非机器学习实体示例

在言语“安排从 8 点到 9 点的会议”中，两个数字表示时间，但每个时间在言语中具有不同的含义。 角色提供差异的名称。 

```
Schedule the meeting from 8 to 9
```

|实体类型|角色名|ReplTest1|
|--|--|--|
|预生成的 datetimeV2|Starttime|8|
|预生成的 datetimeV2|Endtime|9|

## <a name="are-multiple-entities-in-an-utterance-the-same-thing-as-roles"></a>言语中的多个实体是否与角色相同？ 

多个实体可以存在于一个言语中，无需使用角色即可提取这些实体。 如果句子的上下文指示实体的哪个版本具有值, 则应使用角色。 

### <a name="dont-use-roles-for-duplicates-without-meaning"></a>不要对没有含义的重复项使用角色

如果言语 `I want to travel to Seattle, Cairo, and London.` 包含位置列表，该列表中的每个项没有其他含义。 

### <a name="use-roles-if-duplicates-indicate-meaning"></a>如果重复项指示了含义，请使用角色

如果言语 `I want to travel from Seattle, with a layover in Londen, landing in Cairo.` 包含有含义的位置列表，应使用角色捕获原点、中途停留地和目的地的此含义。

### <a name="roles-can-indicate-order"></a>角色可以指示顺序

如果言语 `I want to first start with Seattle, second London, then third Cairo` 发生更改，指示你要提取顺序，可通过两种方法提取顺序。 可以标识指示角色、`first start with`、`second` 和 `third` 的标记。 也可以在复合实体中使用预生成实体 **Ordinal** 和 **GeographyV2** 捕获顺序和位置的概念。 

## <a name="how-are-roles-used-in-example-utterances"></a>如何在示例言语中使用角色？

如果实体包含角色，并且已在示例言语中标记该实体，则只能选择实体，或者选择实体和角色。 

以下示例言语使用实体和角色：

|标记视图|实体视图|
|--|--|
|我想要更多地了解**西雅图**|我想要更多地了解 {Location}|
|购买从西雅图到纽约的票|购买从 {Location:Origin} 到 {Location:Destination} 的票|

## <a name="how-are-roles-used-in-patterns"></a>如何在模式中使用角色？
在模式的模板表述中，角色在表述内使用： 

|模式与实体角色|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>模式中的角色语法
实体和角色在括号 `{}` 内。 实体和角色由冒号分隔。 

## <a name="entity-roles-versus-collaborator-roles"></a>实体角色与协作者角色

实体角色适用于 LUIS 应用的数据模型。 [协作者](luis-concept-collaborator.md)角色适用于创作访问级别。 

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>后续步骤

* 使用具有非计算机学习实体的实体角色的[动手教程](tutorial-entity-roles.md)
* 了解如何添加[角色](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
