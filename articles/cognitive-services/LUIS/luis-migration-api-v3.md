---
title: API V2 到 V3 的 迁移
titleSuffix: Azure Cognitive Services
description: 终结点 API 版本 3 已发生更改。 请使用本指南了解如何迁移到终结点 API 版本 3。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/22/2019
ms.author: diberry
ms.openlocfilehash: edaa36cf22e63d42eb347aea3da1816e2c93b45e
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479222"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>预览版：迁移到 API 版本 3.x, 适用于 LUIS 应用

查询预测终结点 API 已发生更改。 请使用本指南了解如何迁移到终结点 API 版本 3。 

此 V3 API 提供以下新功能，其中包括重大的 JSON 请求和/或响应更改： 

* [外部实体](#external-entities-passed-in-at-prediction-time)
* [动态列表](#dynamic-lists-passed-in-at-prediction-time)
* [预生成实体 JSON 更改](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

查询预测终结点[请求](#request-changes)和[响应](#response-changes)已发生重大更改，可以支持上面列出的新功能。这些更改包括：

* [响应对象更改](#top-level-json-changes)
* [使用实体角色名称引用而不是实体名称](#entity-role-name-instead-of-entity-name)
* [用于标记言语中的实体的属性](#marking-placement-of-entities-in-utterances)

V3 API **不支持**以下 LUIS 功能：

* 必应拼写检查 V7

为 V3 提供了[参考文档](https://aka.ms/luis-api-v3)。

## <a name="endpoint-url-changes-by-slot-name"></a>终结点 URL 按槽名称更改

V3 终结点 HTTP 调用的格式已更改。

|方法|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|发布|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

槽的有效值:

* `production`
* `staging`

## <a name="endpoint-url-changes-by-version-id"></a>终结点 URL 按版本 ID 更改

如果希望按版本查询，首先需要使用 `"directVersionPublish":true` [通过 API 发布](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)。 查询引用版本 ID 而不是槽名称的终结点。


|方法|URL|
|--|--|
|GET|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|发布|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>使用新 JSON 的预生成实体

V3 响应对象更改包括[预生成实体](luis-reference-prebuilt-entities.md)。 

## <a name="request-changes"></a>请求更改 

### <a name="query-string-parameters"></a>查询字符串参数

V3 API 包含不同的查询字符串参数。

|参数名称|类型|Version|默认|用途|
|--|--|--|--|--|
|`log`|boolean|V2 和 V3|假|将查询存储在日志文件中。| 
|`query`|string|仅 V3|无默认值 - 在 GET 请求中是必需的|**在 V2 中**，要预测的言语位于 `q` 参数中。 <br><br>**在 V3 中**，该功能在 `query` 参数中传递。|
|`show-all-intents`|boolean|仅 V3|假|在 **prediction.intents** 对象中返回包含相应评分的所有意向。 意向将在父 `intents` 对象中作为对象返回。 这样，便可以通过编程方式进行访问，而无需在数组中查找意向：`prediction.intents.give`。 在 V2 中，这些意向在数组中返回。 |
|`verbose`|boolean|V2 和 V3|假|**在 V2 中**，如果设置为 true，则返回所有预测意向。 如果需要所有预测的意向，请使用 V3 参数 `show-all-intents`。<br><br>**在 V3 中**，此参数仅提供实体预测的实体元数据详细信息。  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>`POST` 请求的查询预测 JSON 正文

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|属性|type|Version|默认|用途|
|--|--|--|--|--|
|`dynamicLists`|array|仅 V3|非必需。|使用[动态列表](#dynamic-lists-passed-in-at-prediction-time)可以扩展已在 LUIS 应用中的已训练且已发布的现有列表实体。|
|`externalEntities`|array|仅 V3|非必需。|[外部实体](#external-entities-passed-in-at-prediction-time)可让 LUIS 应用在运行时识别和标记实体，这些实体可用作现有实体的特征。 |
|`options.datetimeReference`|string|仅 V3|无默认值|用于确定 [datetimeV2 偏移量](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 DatetimeReference 的格式为[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)。|
|`options.overridePredictions`|boolean|仅 V3|假|指定是使用用户的[外部实体（与现有实体具有相同名称）](#override-existing-model-predictions)，还是使用模型中的现有实体进行预测。 |
|`query`|string|仅 V3|必需。|**在 V2 中**，要预测的言语位于 `q` 参数中。 <br><br>**在 V3 中**，该功能在 `query` 参数中传递。|



## <a name="response-changes"></a>响应更改

查询响应 JSON 已发生更改，允许以更合理的编程方式访问最常用的数据。 

### <a name="top-level-json-changes"></a>顶级 JSON 更改

当 `verbose` 设置为 true 时（在 `intents` 属性中返回所有意向及其评分），V2 的顶级 JSON 属性为：

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 的顶级 JSON 属性为：

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

`normalizedQuery`包含拼写更正。 这对应于 V2 API 属性`alteredQuery`。  

`intents` 对象是未排序的列表。 不要假设 `intents` 中的第一个子级对应于 `topIntent`。 请改用 `topIntent` 值来查找评分：

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

响应 JSON 架构已发生更改，允许：

* 消除原始言语 `query` 与返回的预测 `prediction` 之间的差别。
* 更轻松地以编程方式访问预测的数据。 可以按意向和实体的 **named** 访问值，而无需枚举 V2 中的数组。 对于预测的实体角色，将返回角色名称，因为它在整个应用中是唯一的。
* 将遵循数据类型（如果已确定）。 数字不再作为字符串返回。
* 第一个优先级预测信息与其他元数据之间的差别在 `$instance` 对象中返回。 

### <a name="access-instance-for-entity-metadata"></a>访问实体元数据的 `$instance`

如果你需要实体元数据，则查询字符串需要使用 `verbose=true` 标志，并且响应在 `$instance` 对象中包含元数据。 以下部分的 JSON 响应中提供了示例。

### <a name="each-predicted-entity-is-represented-as-an-array"></a>预测的每个实体以数组的形式表示

`prediction.entities.<entity-name>` 对象包含一个数组，因为可以在言语中多次预测每个实体。 

### <a name="list-entity-prediction-changes"></a>列表实体预测更改

列表实体预测的 JSON 已更改为数组的数组：

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
每个内部数组对应于言语中的文本。 内部对象是一个数组，因为相同的文本可以出现在列表实体的多个子列表中。 

在 `entities` 对象与 `$instance` 对象之间映射时，将为列表实体预测保留对象顺序。

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>使用实体角色名称而不是实体名称 

在 V2 中，`entities` 数组返回具有实体名称（唯一标识符）的所有预测实体。 在 V3 中，如果实体使用角色，并且预测针对实体角色，则主标识符是角色名称。 之所以能够这样做，是因为实体角色名称必须在整个应用中唯一，这包括其他模型（意向、实体）名称。

在以下示例中，假设某段言语包含文本 `Yellow Bird Lane`。 此文本将作为自定义 `Location` 实体的 `Destination` 角色进行预测。

|言语文本|实体名称|角色名|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

在 V2 中，实体由实体名称以及用作对象属性的角色进行标识：

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

在 V3 中，如果预测针对角色，则实体由实体角色引用：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

在 V3 中，可以使用用于返回实体元数据的 `verbose` 标志来实现相同的结果：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>预测时传入的外部实体

外部实体可让 LUIS 应用在运行时识别和标记实体，这些实体可用作现有实体的特征。 这样，在将查询发送到预测终结点之前，便可以使用自己的独立自定义实体提取器。 此操作是在查询预测终结点上执行的，因此不需要重新训练并发布模型。

客户端应用程序将提供其自身的实体提取器，它会管理实体匹配操作并确定匹配实体的言语中的位置，然后连同请求一起发送该信息。 

外部实体是用于扩展任何实体类型，同时仍可用作其他模型（例如角色、复合等等）的信号的机制。

这对于仅在查询预测运行时才提供数据的实体而言非常有用。 此类数据的示例包括不断变化的数据，或者每个用户的具体数据。 可以使用用户联系人列表中的外部信息扩展 LUIS 联系人实体。 

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

上面的言语使用 `him` 作为对 `Hazem` 的引用。 在 POST 正文中，聊天机器人可将 `him` 映射到从第一段言语 `Hazem` 提取的实体值。

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

`overridePredictions` 选项属性指定如果用户发送与同名的预测实体重叠的外部实体，LUIS 将选择传入的实体还是模型中存在的实体。 

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

如果 `overridePredictions` 设置为 `false`，则 LUIS 将返回响应就像未发送外部实体一样。 

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

如果 `overridePredictions` 设置为 `true`，则 LUIS 将返回包括以下内容的响应：

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解决

可选的 `resolution` 属性将在预测响应中返回，可让你传入与外部实体关联的元数据，然后在响应中接收该元数据。 

主要目的是扩展预生成实体，但并不局限于该实体类型。 

`resolution` 属性可以是数字、字符串、对象或数组：

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>预测时传入的动态列表

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
            "listEntityName":"ProductList",
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

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset 已重命名为 datetimeReference

**在 V2 中**，`timezoneOffset` [参数](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)将在预测请求中作为查询字符串参数发送，无论该请求是作为 GET 还是 POST 请求发送。 

**在 V3 中**，可以使用 POST 正文参数 `datetimeReference` 提供相同的功能。 

## <a name="marking-placement-of-entities-in-utterances"></a>在言语中标记实体的位置

**在 V2 中**，使用 `startIndex` 和 `endIndex` 在言语中标记实体。 

**在 V3 中**，使用 `startIndex` 和 `entityLength` 标记实体。

## <a name="deprecation"></a>弃用 

在 V3 预览后至少 9 个月内不会弃用 V2 API。 

## <a name="next-steps"></a>后续步骤

使用 V3 API 文档更新对 LUIS [终结点](https://aka.ms/luis-api-v3) API 的现有 REST 调用。 