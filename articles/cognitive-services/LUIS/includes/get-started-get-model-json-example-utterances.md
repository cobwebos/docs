---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: e879afdbd4c34e9d74405644de86421fb2cbab46
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279505"
---
示例言语采用特定格式。

`text` 字段包含示例话语的文本。 `intentName` 字段必须对应于 LUIS 应用中的现有意向名称。 `entityLabels` 字段是必填的。 如果不想标记任何实体，请提供一个空数组。

如果 entityLabels 数组不为空，则 `startCharIndex` 和 `endCharIndex` 需要标记 `entityName` 字段中引用的实体。 索引从零开始，这意味着顶部示例中的 6 表示西雅图的“S”而不是大写字母 S 之前的空格。如果你在文本中的空格处开始或结束标签，则用于添加话语的 API 调用将失败。

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
