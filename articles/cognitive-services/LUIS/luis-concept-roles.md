---
title: 了解如何在基于模式的实体中使用角色
titleSuffix: Azure Cognitive Services
description: 角色是仅在模式中使用的实体的已命名上下文子类型。 例如，在购买从纽约到伦敦的车票的话语中，纽约和伦敦都是城市，但是两者在句中的含义不同。 纽约为出发城市，伦敦为目标城市。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: fea70e2b1912bde94e23dbd98fa933784d2a69ac
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140309"
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


[!include[H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="roles-with-prebuilt-entities"></a>具有预构建实体的角色

使用具有预生成实体的角色，对某话语中预生成实体的不同实例赋予含义。 

### <a name="roles-with-datetimev2"></a>具有 datetimeV2 的角色

预生成实体 datetimeV2 可以很好地理解话语中日期和时间的各种变化。 你可能希望以不同于预生成实体的默认理解方式来指定日期和日期范围。 

## <a name="next-steps"></a>后续步骤

* 了解如何添加[角色](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
