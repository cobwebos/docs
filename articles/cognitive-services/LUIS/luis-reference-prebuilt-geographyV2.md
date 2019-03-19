---
title: Geography V2 预生成实体
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的 geographyV2 预构建实体信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 17f612f2ee6c7d27dcec9f72ed3df1ed418eb3d2
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961596"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的 GeographyV2 预生成实体
预构建的 geographyV2 可以检测地点。 由于此实体已经过训练，因此不需要将包含 GeographyV2 的陈述示例添加到应用程序意向中。 GeographyV2 实体支持英语[区域性](luis-reference-prebuilt-entities.md)。

## <a name="subtypes"></a>子类型
地理位置具有以下子类型：

|子类型|目的|
|--|--|
|`poi`|兴趣点|
|`city`|城市名称|
|`countryRegion`|国家/地区或区域名称|
|`continent`|洲名称|
|`state`|省/自治区/直辖市名称|


## <a name="resolution-for-geographyv2-entity"></a>GeographyV2 实体的解析
以下示例显示了 builtin.geographyV2 实体的解析。

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

## <a name="next-steps"></a>后续步骤

了解[电子邮件](luis-reference-prebuilt-email.md)、[数字](luis-reference-prebuilt-number.md)和[序号](luis-reference-prebuilt-ordinal.md)实体。 
