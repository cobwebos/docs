---
title: 在运行时扩展应用 - LUIS
description: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 69e2608fb01ece81f555aae2f3d4a2e4a05cfc90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322795"
---
# <a name="extend-app-at-prediction-runtime"></a>在预测运行时扩展应用

应用的架构（模型和功能）经过训练后将发布到预测终结点。 此发布的模型用于预测运行时。 可以将新信息以及用户的言语传递到预测运行时以增加预测。

预测运行时架构的两个更改包括：
* [外部实体](#external-entities)
* [动态列表](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>外部实体

外部实体可让 LUIS 应用在运行时识别和标记实体，这些实体可用作现有实体的特征。 这样，在将查询发送到预测终结点之前，便可以使用自己的独立自定义实体提取器。 此操作是在查询预测终结点上执行的，因此不需要重新训练并发布模型。

客户端应用程序将提供其自身的实体提取器，它会管理实体匹配操作并确定匹配实体的言语中的位置，然后连同请求一起发送该信息。

外部实体是用于扩展任何实体类型，同时仍可用作其他模型的信号的机制。

这对于仅在查询预测运行时才提供数据的实体而言非常有用。 此类数据的示例包括不断变化的数据，或者每个用户的具体数据。 可以使用用户联系人列表中的外部信息扩展 LUIS 联系人实体。

外部实体是 V3 创作 API 的一部分。 详细了解如何[迁移](luis-migration-api-v3.md)到此版本。

### <a name="entity-already-exists-in-app"></a>实体已在应用中存在

发出请求时，在终结点请求 POST 正文中传递的外部实体 `entityName` 值必须事先在已训练且发布的应用中存在。 实体的类型无关紧要，因为支持所有类型。

### <a name="first-turn-in-conversation"></a>聊天中的第一个轮次

假设在聊天机器人的第一段聊天言语中，用户输入了以下不完整的信息：

`Send Hazem a new message`

聊天机器人发送给 LUIS 的请求可以在 POST 正文中传入有关 `Hazem` 的信息，以便将此信息直接匹配为用户的联系人之一。

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

预测响应包含该外部实体以及其他所有预测实体，因为该实体已在请求中定义。

### <a name="second-turn-in-conversation"></a>聊天中的第二个轮次

在聊天机器人中输入的下一段用户言语使用较模糊的字词：

`Send him a calendar reminder for the party.`

在这一轮会话中，言语使用 `him` 作为对 `Hazem` 的引用。 在 POST 正文中，聊天机器人可将 `him` 映射到从第一段言语 `Hazem` 提取的实体值。

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

预测响应包含该外部实体以及其他所有预测实体，因为该实体已在请求中定义。

### <a name="override-existing-model-predictions"></a>重写现有模型预测

`preferExternalEntities` 选项属性指定如果用户发送与同名的预测实体重叠的外部实体，LUIS 将选择传入的实体还是模型中存在的实体。

例如，考虑查询 `today I'm free`。 LUIS 检测到 `today` 为 datetimeV2，响应如下：

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

如果用户发送外部实体：

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

如果 `preferExternalEntities` 设置为 `false`，则 LUIS 将返回响应就像未发送外部实体一样。

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

如果 `preferExternalEntities` 设置为 `true`，则 LUIS 将返回包括以下内容的响应：

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解决方法

可选的 `resolution` 属性将在预测响应中返回，可让你传入与外部实体关联的元数据，然后在响应中接收该元数据。__

主要目的是扩展预生成实体，但并不局限于该实体类型。

`resolution` 属性可以是数字、字符串、对象或数组：

* "Dallas"
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>动态列表

使用动态列表可以扩展已包含在 LUIS 应用中的已训练并发布的现有列表实体。

需要定期更改列表实体值时，请使用此功能。 使用此功能可以扩展已训练并发布的列表实体：

* 发出查询预测终结点请求时。
* 对于单个请求。

列表实体在 LUIS 应用中可为空，但必须存在。 LUIS 应用中的列表实体不会更改，但终结点上的预测功能将会扩展，以最多包含 2 个列表，其中有大约 1,000 个项。

### <a name="dynamic-list-json-request-body"></a>动态列表 JSON 请求正文

发送以下 JSON 正文，以向列表发送包含同义词的新子列表，并使用 `POST` 查询预测请求预测文本 `LUIS` 的列表实体：

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

预测响应包含该列表实体以及其他所有预测实体，因为该实体已在请求中定义。

## <a name="next-steps"></a>后续步骤

* [预测分数](luis-concept-prediction-score.md)
* [创作 API V3 更改](luis-migration-api-v3.md)
