---
title: 实体角色
titleSuffix: Azure Cognitive Services
description: 角色是仅在模式中使用的实体的已命名上下文子类型。 例如，在表述 `buy a ticket from New York to London` 中，纽约和伦敦都是城市，但是两者在句中的含义不同。 纽约为出发城市，伦敦为目标城市。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: cb155486365ffa1beb4657e2d9cc56fcf143b624
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547739"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>模式中的实体角色是上下文子类型
角色是仅在[模式](luis-concept-patterns.md)中使用的实体的已命名上下文子类型。

例如，在表述 `buy a ticket from New York to London` 中，纽约和伦敦都是城市，但是两者在句中的含义不同。 纽约为出发城市，伦敦为目标城市。 

角色为这些差异命名：

|实体|角色|目的|
|--|--|--|
|位置|origin|飞机起飞地|
|位置|目标|飞机降落地|
|预生成的 datetimeV2|to|结束日期|
|预生成的 datetimeV2|from|开始日期|

## <a name="how-are-roles-used-in-patterns"></a>如何在模式中使用角色？
在模式的模板表述中，角色在表述内使用： 

|模式与实体角色|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>模式中的角色语法
实体和角色在括号 `{}` 内。 实体和角色由冒号分隔。 


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>实体的示例角色

角色只是一个实体在话语中的上下文学习位置。 当话语具有多个实体类型时，它最有效。 对于任何实体类型，最简单的示例是区分 to 和 from 位置。 位置可以用许多不同的实体类型表示。 

一个示例用例是将员工从一个部门调到另一个部门，其中每个部门都是列表中的项目。 例如： 

`Move [PersonName] from [Department:from] to [Department:to]`。 

在返回的预测中，两个部门实体将在 JSON 响应中返回，每个部门实体都将包含角色名称。 

## <a name="roles-with-prebuilt-entities"></a>具有预构建实体的角色

使用具有预生成实体的角色，对某话语中预生成实体的不同实例赋予含义。 

### <a name="roles-with-datetimev2"></a>具有 datetimeV2 的角色

预生成实体 datetimeV2 可以很好地理解话语中日期和时间的各种变化。 你可能希望以不同于预生成实体的默认理解方式来指定日期和日期范围。 

## <a name="next-steps"></a>后续步骤

* 了解如何添加[角色](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
