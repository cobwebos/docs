---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: ec178827a2f27a4566f509fe76b58e451329efd4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044119"
---
用于添加示例话语的 `response` 数组指示每个具有 `hasError` 属性的示例话语是成功还是失败。 以下 JSON 响应显示两个话语都已成功添加。 

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle today",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

以下 JSON 显示了训练请求成功的结果：

```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

以下 JSON 显示了成功请求训练状态的结果。 每个 modelID 都是一个意向。 每个意向都必须针对所有话语进行训练，以正确识别属于该意向的话语以及不属于该意向的话语。 

```JSON
[
    {
        "modelId": "0c694cf9-8c32-44b8-9ea0-3d30a7d901ca",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "10e53836-ade4-494e-9531-3bd6a944c510",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "21e48732-a512-4c33-b5ed-8ea629465269",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "edee15b1-9999-45c2-bbab-591d3a643033",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "aa78e06e-df81-4bb2-b2d9-a2fbb2f81c54",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "e39bb7bd-b417-41a9-a24f-caf4c47fc62c",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "3782eac7-db84-4d66-ba00-0598dffb48ee",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "a941d926-cb0f-47a8-ab7e-deba4378b96f",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "8137f40e-ce6d-40a5-881f-dfd46a05f7e0",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "dc08f95a-58b4-4064-a210-03fe34f75a3c",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    },
    {
        "modelId": "4fabdbed-5697-4562-8c7d-36e174efff2e",
        "details": {
            "statusId": 3,
            "status": "InProgress",
            "exampleCount": 48
        }
    }
]
```
