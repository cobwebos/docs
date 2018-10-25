---
title: 了解如何在基于模式的实体中使用角色
titleSuffix: Azure Cognitive Services
description: 角色是仅在模式中使用的实体的已命名上下文子类型。 例如，在购买从纽约到伦敦的车票的话语中，纽约和伦敦都是城市，但是两者在句中的含义不同。 纽约为出发城市，伦敦为目标城市。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5fda0ac590e5faeaa8b6ec44a7d649d2c0122eeb
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352979"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>“模式”中的实体角色是上下文子类型
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

## <a name="roles-versus-hierarchical-entities"></a>角色与分层实体
分层实体提供与角色相同的上下文信息，但是仅为“意向”中的表述提供。 同样，角色提供与分层实体相同的上下文信息，但仅在“模式”中提供。

|上下文学习|使用位置|
|--|--|
|分层实体|意向|
|角色|模式|

## <a name="roles-with-prebuilt-entities"></a>具有预生成实体的角色

使用具有预生成实体的角色，对某话语中预生成实体的不同实例赋予含义。 

### <a name="roles-with-datetimev2"></a>具有 datetimeV2 的角色

预生成实体 datetimeV2 可以很好地理解话语中日期和时间的各种变化。 你可能希望以不同于预生成实体的默认理解方式来指定日期和日期范围。 

## <a name="next-steps"></a>后续步骤

* 了解如何添加[角色](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
