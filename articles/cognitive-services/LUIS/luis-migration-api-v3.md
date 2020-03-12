---
title: V3 API 中的预测终结点更改
description: 查询预测终结点 V3 Api 已更改。 使用本指南了解如何迁移到版本3终结点 Api。
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 9a8e8cb331dd11eebaddbcbf8f603c1148415aef
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117386"
---
# <a name="prediction-endpoint-changes-for-v3"></a>V3 的预测终结点更改

查询预测终结点 V3 Api 已更改。 使用本指南了解如何迁移到版本3终结点 Api。

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**公开提供状态**-此 V3 api 包含来自 V2 api 的重要 JSON 请求和响应更改。

V3 API 提供以下新增功能：

* [外部实体](#external-entities-passed-in-at-prediction-time)
* [动态列表](#dynamic-lists-passed-in-at-prediction-time)
* [预生成的实体 JSON 更改](#prebuilt-entity-changes)

预测终结点[请求](#request-changes)和[响应](#response-changes)对支持上面列出的新功能进行了重大更改，包括以下各项：

* [响应对象更改](#top-level-json-changes)
* [实体角色名称引用而不是实体名称](#entity-role-name-instead-of-entity-name)
* [用于标记最谈话中的实体的属性](#marking-placement-of-entities-in-utterances)

[参考文档](https://aka.ms/luis-api-v3)适用于 V3。

## <a name="v3-changes-from-preview-to-ga"></a>V3 从预览版更改为 GA

V3 进行了以下更改，作为搬迁到 GA 的一部分：

* 以下预先生成的实体具有不同的 JSON 响应：
    * [OrdinalV1](luis-reference-prebuilt-ordinal.md)
    * [GeographyV2](luis-reference-prebuilt-geographyv2.md)
    * [DatetimeV2](luis-reference-prebuilt-datetimev2.md)
    * 从 `units` 到 `unit` 的可度量单位密钥名称

* 请求正文 JSON 更改：
    * 从 `preferExternalEntities` 到 `preferExternalEntities`
    * 外部实体的可选 `score` 参数

* 响应正文 JSON 更改：
    * `normalizedQuery` 已删除

## <a name="suggested-adoption-strategy"></a>建议采用策略

如果使用 Bot Framework、必应拼写检查 V7 或只想迁移 LUIS 应用创作，请继续使用 V2 终结点。

如果你不知道你的客户端应用程序或集成（Bot Framework 和必应拼写检查 V7）不会受到影响，并且你可以同时迁移 LUIS 应用创作和预测终结点，请开始使用 V3 预测终结点。 V2 预测终结点仍将可用，并且是一种良好的回退策略。


## <a name="not-supported"></a>不支持

### <a name="bing-spell-check"></a>必应拼写检查

V3 预测终结点不支持此 API-继续使用 V2 API 预测终结点进行拼写更正。 如果在使用 V3 API 时需要拼写更正，请让客户端应用程序调用[必应拼写检查](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/overview)api，并将文本发送到 LUIS api 之前，将文本更改为正确的拼写。

## <a name="bot-framework-and-azure-bot-service-client-applications"></a>机器人框架和 Azure 机器人服务客户端应用程序

继续使用 V2 API 预测终结点，直到 Bot Framework 的4.7 版被释放。

## <a name="v2-api-deprecation"></a>V2 API 弃用

在 V3 预览版第8月 2020 8 日之后的至少9个月后，不会弃用 V2 预测 API。

## <a name="endpoint-url-changes"></a>终结点 URL 更改

### <a name="changes-by-slot-name-and-version-name"></a>按槽名称和版本名称进行的更改

V3 终结点 HTTP 调用的格式已更改。

如果希望按版本进行查询，首先需要使用 `"directVersionPublish":true`[通过 API 发布](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b)。 查询引用版本 ID 的终结点，而不是槽名称。

|预测 API 版本|METHOD|代码|
|--|--|--|
|V3|GET|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>预测</b>/<b>3.0</b>/apps/<b>{应用 ID}</b>/slots/<b>{槽名称}</b>/predict？ query =<b>{query}</b>|
|V3|POST|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>预测</b>/<b>3.0</b>/apps/<b>{APP-ID}</b>/slots/<b>{槽-NAME}</b>/predict|
|V2|GET|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>预测</b>/<b>3.0</b>/apps/<b>{应用 ID}</b>/versions/<b>{版本 ID}</b>/predict？ query =<b>{query}</b>|
|V2|POST|https://<b>{REGION}</b>. api.cognitive.microsoft.com/luis/<b>预测</b>/<b>3.0</b>/Apps/<b>{</b>}/versions/<b>{VERSION-id}</b>/predict|

|`SLOT-NAME` 的有效值|
|--|
|`production`|
|`staging`|

## <a name="request-changes"></a>请求更改

### <a name="query-string-changes"></a>查询字符串更改

V3 API 具有不同的查询字符串参数。

|参数名称|类型|版本|默认|目的|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|将查询存储在日志文件中。 默认值为 false。|
|`query`|字符串|仅 V3|无默认值-在 GET 请求中是必需的|**在 V2**中，要预测的查询文本在 `q` 参数中。 <br><br>**在 V3**中，在 `query` 参数中传递功能。|
|`show-all-intents`|boolean|仅 V3|false|返回在**联想**对象中具有相应分数的所有意向。 意向作为对象返回 `intents` 父对象。 这允许编程访问，无需在数组中查找意向： `prediction.intents.give`。 在 V2 中，它们在数组中返回。 |
|`verbose`|boolean|V2 & V3|false|**在 V2 中**，当设置为 true 时，将返回所有预测意向。 如果需要所有预测意向，请使用 `show-all-intents`的 V3 参数。<br><br>**在 V3 中**，此参数仅提供实体预测的实体元数据详细信息。  |
|`timezoneOffset`|字符串|V2|-|应用于 datetimeV2 实体的时区。|
|`datetimeReference`|字符串|V3|-|应用于 datetimeV2 实体的[时区](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 将 `timezoneOffset` 替换为 V2。|


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
|`dynamicLists`|array|仅 V3|非必需。|利用[动态列表](#dynamic-lists-passed-in-at-prediction-time)，你可以扩展现有的已训练和已发布的列表实体（已在 LUIS 应用中）。|
|`externalEntities`|array|仅 V3|非必需。|[外部实体](#external-entities-passed-in-at-prediction-time)使你的 LUIS 应用能够在运行时标识并标记实体，这可用作现有实体的功能。 |
|`options.datetimeReference`|字符串|仅 V3|无默认值|用于确定[datetimeV2 偏移量](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity)。 DatetimeReference 的格式为[ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)。|
|`options.preferExternalEntities`|boolean|仅 V3|false|指定是否使用用户的[外部实体（与现有实体具有相同的名称）](#override-existing-model-predictions)或模型中的现有实体是否用于预测。 |
|`query`|字符串|仅 V3|必需。|**在 V2**中，要预测的查询文本在 `q` 参数中。 <br><br>**在 V3**中，在 `query` 参数中传递功能。|



## <a name="response-changes"></a>响应更改

查询响应 JSON 发生了更改，以允许对最常使用的数据进行更多的编程访问。

### <a name="top-level-json-changes"></a>顶层 JSON 更改



将 `verbose` 设置为 true 时，V2 的 top JSON 属性为 true，这将在 `intents` 属性中返回所有意向及其分数：

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

V3 的顶层 JSON 属性包括：

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

`intents` 对象是一个无序列表。 不要假定 `intents` 中的第一个子级对应于 `topIntent`。 请改用 `topIntent` 值来查找分数：

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

响应 JSON 架构更改允许：

* 清除原始查询文本、`query`和返回的预测 `prediction`之间的区别。
* 更方便地以编程方式访问预测的数据。 您可以通过**名称**为意向和实体访问值，而不是在 V2 中枚举数组。 对于预测实体角色，将返回角色名称，因为它在整个应用中是唯一的。
* 考虑数据类型（如果确定）。 数值型不再作为字符串返回。
* 在 `$instance` 对象中返回的第一个优先级预测信息和其他元数据之间的区别。

### <a name="entity-response-changes"></a>实体响应更改

#### <a name="marking-placement-of-entities-in-utterances"></a>标记最谈话中实体的位置

**在 V2**中，实体在查询文本中标记为 `startIndex` 和 `endIndex`。

**在 V3 中**，实体标记有 `startIndex` 和 `entityLength`。

#### <a name="access-instance-for-entity-metadata"></a>实体元数据的访问 `$instance`

如果需要实体元数据，则查询字符串需要使用 `verbose=true` 标志，并且响应包含 `$instance` 对象中的元数据。 以下部分中的 JSON 响应中显示了示例。

#### <a name="each-predicted-entity-is-represented-as-an-array"></a>每个预测实体都表示为数组

`prediction.entities.<entity-name>` 对象包含一个数组，因为每个实体可在查询文本中预测多次。

<a name="prebuilt-entities-with-new-json"></a>

#### <a name="prebuilt-entity-changes"></a>预生成实体更改

V3 响应对象包括对预先生成的实体的更改。 查看[特定的预生成实体](luis-reference-prebuilt-entities.md)以了解详细信息。

#### <a name="list-entity-prediction-changes"></a>列出实体预测更改

列表实体预测的 JSON 已改为数组的数组：

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
每个内部数组对应于查询文本中的文本。 内部对象是一个数组，因为同一文本可以出现在一个列表实体的多个子列表中。

将 `entities` 对象映射到 `$instance` 对象时，将为列表实体预测保留对象顺序。

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

#### <a name="entity-role-name-instead-of-entity-name"></a>实体角色名称，而不是实体名称

在 V2 中，`entities` 数组返回所有具有实体名称的预测实体作为唯一标识符。 在 V3 中，如果实体使用角色并且预测用于实体角色，则主标识符是角色名称。 这是可能的，因为实体角色名称在整个应用中必须是唯一的，包括其他模型（意向、实体）名称。

在下面的示例中：考虑包含 `Yellow Bird Lane`文本的查询文本。 此文本将预测为自定义 `Location` 实体的 `Destination`的角色。

|查询文本文本|实体名称|角色名称|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

在 V2 中，实体由_实体名称_标识，该实体具有作为对象的属性的角色：

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

在 V3 中，如果针对角色的预测为，实体_角色_将引用实体：

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

在 V3 中，与返回实体元数据的 `verbose` 标志相同的结果：

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

外部实体使你的 LUIS 应用能够在运行时标识并标记实体，这可用作现有实体的功能。 这样，便可以在将查询发送到预测终结点之前，使用自己的单独的和自定义实体提取器。 由于这是在查询预测终结点上完成的，因此您无需重新训练和发布模型。

客户端应用程序通过管理实体匹配并确定该匹配实体的查询文本中的位置，然后使用请求发送该信息，来提供自己的实体提取程序。

外部实体是一种机制，用于扩展任何实体类型，同时仍用作其他模型（如角色、复合等）的信号。

这适用于仅在查询预测运行时中提供数据的实体。 此类数据的示例包括不断变化的数据或每个用户的特定数据。 您可以使用用户联系人列表中的外部信息来扩展 LUIS contact 实体。

### <a name="entity-already-exists-in-app"></a>应用中已存在实体

对于在终结点请求 POST 正文中传递的外部实体，`entityName` 的值必须已经存在于发出请求时经过训练和发布的应用中。 实体的类型并不重要，所有类型都受支持。

### <a name="first-turn-in-conversation"></a>首先打开对话

请考虑聊天 bot 对话中的第一个查询文本，其中用户输入以下不完整信息：

`Send Hazem a new message`

从聊天机器人到 LUIS 的请求可以传入有关 `Hazem` 的 POST 正文中的信息，因此它直接与用户的联系人之一匹配。

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

预测响应包括该外部实体和所有其他预测实体，因为它是在请求中定义的。

### <a name="second-turn-in-conversation"></a>第二个轮流对话

接下来，查询文本到聊天机器人的用户使用更含糊的术语：

`Send him a calendar reminder for the party.`

在前面的查询文本中，查询文本使用 `him` 作为对 `Hazem`的引用。 POST 正文中的会话聊天机器人可以将 `him` 映射到从第一个查询文本 `Hazem`中提取的实体值。

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

预测响应包括该外部实体和所有其他预测实体，因为它是在请求中定义的。

### <a name="override-existing-model-predictions"></a>重写现有模型预测

`preferExternalEntities` options 属性指定，如果用户发送与具有相同名称的预测实体重叠的外部实体，则 LUIS 将选择传入的实体或模型中存在的实体。

例如，请考虑 `today I'm free` 查询。 LUIS 检测到 `today` 为 datetimeV2，并提供以下响应：

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

如果 `preferExternalEntities` 设置为 `false`，则 LUIS 将返回一个响应，就好像未发送外部实体一样。

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

如果 `preferExternalEntities` 设置为 `true`，则 LUIS 将返回一个响应，其中包括：

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>解决方法

_可选_`resolution` 属性在预测响应中返回，使您可以传入与外部实体关联的元数据，然后在响应中返回。

主要目的是扩展预生成的实体，但不限于该实体类型。

`resolution` 属性可以是数字、字符串、对象或数组：

* 州
* {"text": "value"}
* 12345
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>预测时传入的动态列表

利用动态列表，你可以扩展现有的已训练和已发布的列表实体（已在 LUIS 应用中）。

当你的列表实体值需要定期更改时使用此功能。 利用此功能，您可以扩展已训练和已发布的列表实体：

* 查询预测终结点请求时。
* 用于单个请求。

列表实体在 LUIS 应用程序中可以为空，但该实体必须存在。 LUIS 应用中的列表实体不会更改，但端点的预测能力将扩展到最多包含2个列表，其中包含大约1000个项目。

### <a name="dynamic-list-json-request-body"></a>动态列表 JSON 请求正文

发送以下 JSON 正文，将包含同义词的新子列表添加到列表，并使用 `POST` 查询预测请求来预测文本的列表实体 `LUIS`：

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

预测响应包含该列表实体以及所有其他预测实体，因为它是在请求中定义的。

## <a name="deprecation"></a>弃用

在 V3 预览后的至少9个月内不会弃用 V2 API。

## <a name="next-steps"></a>后续步骤

使用 V3 API 文档更新对 LUIS[终结点](https://aka.ms/luis-api-v3)api 的现有 REST 调用。
