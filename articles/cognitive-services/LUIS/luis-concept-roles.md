---
title: 了解如何在基于模式的实体中使用角色 - Azure | Microsoft Docs
description: 了解如何在基于模式的实体中使用角色，为上下文的实体子类型提供名称。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35371056"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>“模式”中的实体角色是上下文子类型
角色是仅在[模式](luis-concept-patterns.md)中使用的实体的已命名上下文子类型。

例如，在表述 `buy a ticket from New York to London` 中，纽约和伦敦都是城市，但是两者在句中的含义不同。 纽约为出发城市，伦敦为目标城市。 

角色为这些差异命名：

|实体|角色|目的|
|--|--|--|
|位置|origin|飞机起飞地|
|位置|目标|飞机降落地|

## <a name="how-are-roles-used-in-patterns"></a>如何在模式中使用角色？
在模式的模板表述中，角色在表述内使用： 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>模式中的角色语法
实体和角色在括号 `{}` 内。 实体和角色由冒号分隔。 

## <a name="roles-versus-hierarchical-entities"></a>角色与分层实体
分层实体提供与角色相同的上下文信息，但是仅为“意向”中的表述提供。 同样，角色提供与分层实体相同的上下文信息，但仅在“模式”中提供。

|上下文学习|使用位置|
|--|--|
|分层实体|意向|
|角色|模式|

## <a name="next-steps"></a>后续步骤

* 了解如何添加[角色](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
