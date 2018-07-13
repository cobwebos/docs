---
title: 学术知识 API 的 Academic Graph 实体属性 | Microsoft Docs
description: 了解可以与学术知识 API 中的 Academic Graph 结合使用的实体属性。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365383"
---
# <a name="entity-attributes"></a>实体属性

Academic Graph 包含 7 类实体。 所有实体都有实体 ID 和实体类型。

## <a name="common-entity-attributes"></a>常见实体属性
名称    |说明                |Type       | 操作
------- | ------------------------- | --------- | ----------------------------
ID      |实体 ID                  |Int64      |等于
Ty      |实体类型                |枚举   |等于

## <a name="entity-type-enum"></a>实体类型枚举
名称                                                            |值
----------------------------------------------------------------|-----
[论文](PaperEntityAttributes.md)                               |0
[作者](AuthorEntityAttributes.md)                             |1
[期刊](JournalEntityAttributes.md)                           |2
[会议录](JournalEntityAttributes.md)                 |3
[会议实例](ConferenceInstanceEntityAttributes.md)    |4
[隶属关系](AffiliationEntityAttributes.md)                   |5
[研究领域](FieldsOfStudyEntityAttributes.md)                      |6

