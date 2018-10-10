---
title: Geography V2 预构建实体 - LUIS 参考
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的 geographyV2 预构建实体信息。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 3559bc02944f88f486104d4d9553f0c45a1f1754
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983406"
---
# <a name="geographyv2-entity"></a>GeographyV2 实体
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

```JSON
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