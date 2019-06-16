---
title: Academic Graph 实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可以与学术知识 API 中的 Academic Graph 结合使用的实体属性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340173"
---
# <a name="entity-attributes"></a>实体属性

Academic Graph 包含 7 类实体。 所有实体会都将一个实体 ID 和实体类型。

## <a name="common-entity-attributes"></a>常见实体属性
Name    |描述                |Type       | 操作
------- | ------------------------- | --------- | ----------------------------
Id      |实体 ID                  |Int64      |等于
Ty      |实体类型                |enum   |等于

## <a name="entity-type-enum"></a>实体类型枚举
Name                                                            |value
----------------------------------------------------------------|-----
[论文](PaperEntityAttributes.md)                               |0
[作者](AuthorEntityAttributes.md)                             |第
[期刊](JournalEntityAttributes.md)                           |2
[会议录](JournalEntityAttributes.md)                 |3
[会议实例](ConferenceInstanceEntityAttributes.md)    |4
[隶属关系](AffiliationEntityAttributes.md)                   |5
[研究领域](FieldsOfStudyEntityAttributes.md)                      |6

