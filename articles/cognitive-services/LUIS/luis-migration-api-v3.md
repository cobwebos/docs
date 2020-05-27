---
title: V3 API 中的预测终结点更改
description: 查询预测终结点 V3 API 已更改。 请使用本指南了解如何迁移到终结点 API 版本 3。
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: diberry
ms.openlocfilehash: 84afcbcd348c3fd91014096877de2315722b53a0
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849325"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3 的预测终结点更改

查询预测终结点 V3 API 已更改。 请使用本指南了解如何迁移到终结点 API 版本 3。

**公开提供的状态** - 此 V3 API 包括 V2 API 的重大 JSON 请求和响应更改。

V3 API 提供以下新功能：

* [外部实体](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)
* [动态列表](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [预生成实体 JSON 更改](#prebuilt-entity-changes)

预测终结点[请求](#request-changes)和[响应](#response-changes)已发生重大更改，可以支持上面列出的新功能。这些更改包括：

* [响应对象更改](#top-level-json-changes)
* [使用实体角色名称引用而不是实体名称](#entity-role-name-instead-of-entity-name)
* [用于标记言语中的实体的属性](#marking-placement-of-entities-in-utterances)

为 V3 提供了[参考文档](https://aka.ms/luis-api-v3)。

## <a name="v3-changes-from-preview-to-ga"></a>从预览版到正式版的 V3 更改

V3 在从预览版过渡到正式版的过程中进行了以下更改：

* 以下预构建的实体具有不同的 JSON 响应：
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * 可度量单位密钥名称从 `units` 更改为 `unit`

* 请求正文 JSON 更改：
    * 从 `preferExternalEntities` 更改为 `preferExternalEntities`
    * 外部实体的可选 `score` 参数

* 响应正文 JSON 更改：
    * 删除了 `normalizedQuery`

## <a name="suggested-adoption-strategy"></a>建议的采用策略

如果使用 Bot Framework、必应拼写检查 V7 或者只要迁移 LUIS 应用创作，请继续使用 V2 终结点。

如果你知道自己的客户端应用程序或集成（Bot Framework 和必应拼写检查 V7）不受影响，并且愿意同时迁移 LUIS 应用创作和预测终结点，则可开始使用 V3 预测终结点。 V2 预测终结点仍将可用，是一项良好的回退策略。


## <a name="not-supported"></a>不支持

### <a name="bing-spell-check"></a>必应拼写检查

V3 预测终结点不支持此 API-继续使用 V2 API 预测终结点进行拼写更正。 如果在使用 V3 API 时需要拼写更正，请让客户端应用程序调用[必应拼写检查](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview)api，并将文本发送到 LUIS api 之前，将文本更改为正确的拼写。

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>Bot Framework 和 Azure 机器人服务客户端应用程序

请继续使用 V2 API 预测终结点，直到 Bot Framework V4.7 发布。

## <a name="v2-api-deprecation"></a>弃用 V2 API

在 2020 年 6 月 8 日发布 V3 预览版后至少 9 个月内不会弃用 V2 预测 API。

## <a name="endpoint-url-changes"></a>终结点 URL 更改

### <a name="changes-by-slot-name-and-version-name"></a>按槽名称和版本名称进行的更改

[V3 终结点 HTTP](developer-reference-resource.md#rest-endpoints)调用的格式已更改。

如果希望按版本查询，首先需要使用 `"directVersionPublish":true`[通过 API 进行发布](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)。 查询引用版本 ID 而不是槽名称的终结点。

|`SLOT-NAME` 的有效值|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>请求更改

### <a name="query-string-changes"></a>查询字符串更改

V3 API 包含不同的查询字符串参数。

|参数名称|类型|版本|默认|目的|
|--|--|--|--|--|
|`log`|boolean|V2 和 V3|false|将查询存储在日志文件中。 默认值为 false。|
|`query`|字符串|仅 V3|无默认值 - 在 GET 请求中是必需的|**在 V2 中**，要预测的言语位于 `q` 参数中。 <br><br>**在 V3 中**，该功能在 `query` 参数中传递。|
|`show-all-intents`|boolean|仅 V3|false|在 **prediction.intents** 对象中返回包含相应评分的所有意向。 意向将在父 `intents` 对象中作为对象返回。 这样，便可以通过编程方式进行访问，而无需在数组中查找意向：`prediction.intents.give`。 在 V2 中，这些意向在数组中返回。 |
|`verbose`|boolean|V2 和 V3|false|**在 V2 中**，如果设置为 true，则返回所有预测意向。 如果需要所有预测的意向，请使用 V3 参数 `show-all-intents`。<br><br>**在 V3 中**，此参数仅提供实体预测的实体元数据详细信息。  |
|`timezoneOffset`|字符串|V2|-|应用于 datetimeV2 实体的时区。|
|`datetimeReference`|字符串|V3|-|应用于 datetimeV2 实体的[时区](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 替换 V2 中的 `timezoneOffset`。|


### <a name="v3-post-body"></a>V3 POST 正文

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "preferExternalEntities": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|properties|类型|版本|默认|目的|
|--|--|--|--|--|
|`dynamicLists`|数组|仅 V3|不要求。|使用[动态列表](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)可以扩展已在 LUIS 应用中的已训练且已发布的现有列表实体。|
|`externalEntities`|数组|仅 V3|不要求。|[外部实体](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)可让 LUIS 应用在运行时识别和标记实体，这些实体可用作现有实体的特征。 |
|`options.datetimeReference`|字符串|仅 V3|无默认值|用于确定 [datetimeV2 偏移量](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 datetimeReference 的格式是 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)。|
|`options.preferExternalEntities`|boolean|仅 V3|false|指定是使用用户的[外部实体（与现有实体具有相同名称）](schema-change-prediction-runtime.md#override-existing-model-predictions)，还是使用模型中的现有实体进行预测。 |
|`query`|字符串|仅 V3|必需。|**在 V2 中**，要预测的言语位于 `q` 参数中。 <br><br>**在 V3 中**，该功能在 `query` 参数中传递。|

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
        "topIntent": "intent-name-1",
        "intents": {},
        "entities":{}
    }
}
```

`intents` 对象是未排序的列表。 不要假设 `intents` 中的第一个子级对应于 `topIntent`。 请改用 `topIntent` 值来查找评分：

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

响应 JSON 架构已发生更改，允许：

* 消除原始言语 `query` 与返回的预测 `prediction` 之间的差别。
* 更轻松地以编程方式访问预测的数据。 可以按意向和实体的 **name** 访问值，而无需枚举 V2 中的数组。 对于预测的实体角色，将返回角色名称，因为它在整个应用中是唯一的。
* 将遵循数据类型（如果已确定）。 数字不再作为字符串返回。
* 第一个优先级预测信息与其他元数据之间的差别在 `$instance` 对象中返回。

### <a name="entity-response-changes"></a>实体响应更改

#### <a name="marking-placement-of-entities-in-utterances"></a>在言语中标记实体的位置

**在 V2 中**，使用 `startIndex` 和 `endIndex` 在言语中标记实体。

**在 V3 中**，使用 `startIndex` 和 `entityLength` 标记实体。

#### <a name="access-instance-for-entity-metadata"></a>访问实体元数据的 `$instance`

如果你需要实体元数据，则查询字符串需要使用 `verbose=true` 标志，并且响应在 `$instance` 对象中包含元数据。 以下部分的 JSON 响应中提供了示例。

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>预测的每个实体以数组的形式表示

`prediction.entities.<entity-name>` 对象包含一个数组，因为可以在言语中多次预测每个实体。

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>预构建实体更改

V3 响应对象包含对预构建实体的更改。 查看[特定的预构建实体](luis-reference-prebuilt-entities.md)以了解详细信息。

#### <a name="list-entity-prediction-changes"></a>列表实体预测更改

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

#### <a name="entity-role-name-instead-of-entity-name"></a>使用实体角色名称而不是实体名称

在 V2 中，`entities` 数组返回具有实体名称（唯一标识符）的所有预测实体。 在 V3 中，如果实体使用角色，并且预测针对实体角色，则主标识符是角色名称。 之所以能够这样做，是因为实体角色名称必须在整个应用中唯一，这包括其他模型（意向、实体）名称。

在以下示例中，假设某段言语包含文本 `Yellow Bird Lane`。 此文本将作为自定义 `Location` 实体的 `Destination` 角色进行预测。

|言语文本|实体名称|角色名称|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

在 V2 中，实体由实体名称以及用作对象属性的角色进行标识：__

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

在 V3 中，如果预测针对角色，则实体由实体角色引用：__

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

<a name="external-entities-passed-in-at-prediction-time"></a>
<a name="override-existing-model-predictions"></a>

## <a name="extend-the-app-at-prediction-time"></a>在预测时间扩展应用

了解有关如何在预测运行时扩展应用程序的[概念](schema-change-prediction-runtime.md)。

## <a name="deprecation"></a>弃用

在 V3 预览后至少 9 个月内不会弃用 V2 API。

## <a name="next-steps"></a>后续步骤

使用 V3 API 文档更新对 LUIS [终结点](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/operations/5cb0a9459a1fe8fa44c28dd8) API 的现有 REST 调用。
