---
title: JSON 平展和转义规则 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解 Azure 时序见解第 2 代中的 JSON 平展、转义和数组处理。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/28/2020
ms.openlocfilehash: a1f633548ed36320f40e485f540923c8e3045a99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460860"
---
# <a name="json-flattening-escaping-and-array-handling"></a>JSON 平展、转义和数组处理

Azure 时序见解第 2 代环境将按照一组特定的命名约定动态创建暖存储和冷存储的列。 引入事件时，会将一组规则应用于 JSON 有效负载和属性名称。 这包括对某些特殊字符进行转义以及平展嵌套的 JSON 对象。 你必须了解这些规则，以便理解 JSON 的形状如何影响事件的存储和查询方式。 有关规则的完整列表，请参阅下表。 示例 A 和 B 还演示了如何在数组中高效地对多个时序进行批处理。

> [!IMPORTANT]
>
> * 在选择[时序 ID 属性](time-series-insights-update-how-to-id.md)和/或事件源[时间戳属性](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)之前，请查看以下规则。 如果你的 TS ID 或时间戳位于嵌套对象内或具有下述一个或多个特殊字符，请务必确保你提供的属性名称与应用引入规则后的列名称相匹配。 请参阅下面的示例 [B](concepts-json-flattening-escaping-rules.md#example-b)。

| 规则 | 示例 JSON | [时序表达式语法](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) | Parquet 中的属性列名称
|---|---|---|---|
| Azure 时序见解第 2 代数据类型将以“_\<dataType\>”形式追加到列名称的末尾 | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| 在 Azure 时序见解第 2 代中，事件源[时间戳属性](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)将作为“时间戳”保存在存储中，并且值以 UTC 格式存储。 你可以自定义事件源时间戳属性来满足你的解决方案的需求，但暖存储和冷存储中的列名称为“时间戳”。 不是事件源时间戳的其他日期/时间 JSON 属性在保存时列名称中将带有“_datetime”，如上面的规则中所述。  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| 包含特殊字符的 JSON 属性名称。 [ 、\、和 '，将使用 [' 和 '] 对属性名称进行转义  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| 在 [' 和 '] 中，额外转义单引号和反斜杠。 单引号将写为 \’，反斜杠将写为 \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| 嵌套的 JSON 对象将以句点作为分隔符进行平展。 最多支持嵌套 10 层。 |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`、`$event['series']['value'].Long` 或 `$event.series['value'].Long` |  `series.value_long` |
| 基元类型的数组存储为 Dynamic 类型 |  ```"values": [154, 149, 147]``` | 动态类型只能通过 [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API 检索 | `values_dynamic` |
| 包含对象的数组有两个行为，具体取决于对象内容：如果 TS ID (s) 或 timestamp 属性 (的) 位于数组中的对象内，则数组将 unrolled，以便初始 JSON 负载产生多个事件。 这使你能够将多个事件成批转换为一个 JSON 结构。 与数组对等的所有顶级属性都会随每个展开的对象一起保存。 如果数组中没有 TS ID 和时间戳，则它会整体另存为 Dynamic 类型。 | 请参阅下面[的示例 A](concepts-json-flattening-escaping-rules.md#example-a)、 [B](concepts-json-flattening-escaping-rules.md#example-b)和[C](concepts-json-flattening-escaping-rules.md#example-c)
| 包含混合元素的数组不会平展。 |  ```"values": ["foo", {"bar" : 149}, 147]``` | 动态类型只能通过 [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) API 检索 | `values_dynamic` |
| 512 个字符是 JSON 属性名称的长度上限。 如果名称超过 512 个字符，则会将其截断为 512 个字符，并追加“_<'hashCode'>”。 **注意**，这也适用于从平展的对象连接的属性名称（表示嵌套的对象路径）。 |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>了解数组的双重行为

对象的数组将存储为一个整体或拆分为多个事件，具体取决于数据建模方式。 这允许你使用数组来成批处理事件，避免在根对象级别定义重复的遥测属性。 批处理可能很有利，因为它会导致发送较少的事件中心或 IoT 中心消息。

但在某些情况下，包含对象的数组仅在其他值的上下文中有意义。 创建多个事件会导致数据无意义。 若要确保对象的数组按原样存储为动态类型，请遵循下面的数据建模指导，并参阅[示例 C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>如何知道我的对象数组是否将产生多个事件

如果你的一个或多个时序 ID 属性嵌套在数组中的对象内，或者事件源时间戳属性是嵌套的，则引入引擎会将其拆分，以创建多个事件。 你为 TS ID 和/或时间戳提供的属性名称应遵循上述平展规则，因此会指示你的 JSON 的形状。 请参阅下面的示例，并查看有关如何[选择时序 ID 属性](time-series-insights-update-how-to-id.md)的指南。

### <a name="example-a"></a>示例 A

对象根和时间戳嵌套的时序 ID \
**环境时序 ID：**`"id"`\
**事件源时间戳：**`"values.time"`\
**JSON 有效负载：**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Parquet 文件中的结果：**\
上述配置和有效负载将产生三个列和四个事件

| timestamp  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>示例 B

一个嵌套了属性为 \
**环境时序 ID：** `"plantId"` 与 `"telemetry.tagId"`\
**事件源时间戳：**`"timestamp"`\
**JSON 有效负载：**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Parquet 文件中的结果：**\
上述配置和有效负载将产生四个列和六个事件

| timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30.9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19.960796 |

### <a name="example-c"></a>示例 C

时序 ID 和时间戳位于对象根 \
**环境时序 ID：**`"id"`\
**事件源时间戳：**`"timestamp"`\
**JSON 有效负载：**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Parquet 文件中的结果：**\
上述配置和有效负载将产生三个列和一个事件

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>后续步骤

* 了解你的环境的[吞吐量限制](./concepts-streaming-ingress-throughput-limits.md)
