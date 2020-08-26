---
title: JSON 平展和转义规则-Azure 时序见解 Gen2 |Microsoft Docs
description: 了解 Azure 时序见解 Gen2 中的 JSON 平展、转义和数组处理。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d33b9b4cb50c1be7b316aad2a736bfd6fb074833
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075676"
---
# <a name="ingestion-rules"></a>引入规则
### <a name="json-flattening-escaping-and-array-handling"></a>JSON 平展、转义和数组处理

Azure 时序见解 Gen2 环境将按照一组特定的命名约定，动态创建热和冷存储的列。 引入事件时，会将一组规则应用于 JSON 有效负载和属性名称。 其中包括转义某些特殊字符和平展嵌套的 JSON 对象。 必须了解这些规则，以便了解 JSON 的形状如何影响如何存储和查询事件。 有关规则的完整列表，请参阅下表。 示例 A & B 还演示了如何在数组中有效地批处理多个时序。

> [!IMPORTANT]
>
> * 在选择[时序 ID 属性](time-series-insights-update-how-to-id.md)和/或事件源[时间戳属性（源）](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)之前，请查看下面的规则。 如果 TS ID 或时间戳在嵌套对象内或具有以下一个或多个特殊字符，请务必确保在应用引入规则*后*提供的属性名称与列名称相匹配。 请参阅下面的示例[B](concepts-json-flattening-escaping-rules.md#example-b) 。

| 规则 | 示例 JSON |存储中的列名称 |
|---|---|---|
| 将 Azure 时序见解 Gen2 数据类型追加到列名的末尾作为 "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | type_string |
| 在存储中将 "事件源[时间戳" 属性](concepts-streaming-ingestion-event-sources.md#event-source-timestamp)保存在 Azure 时序见解 Gen2 中，将其保存为 UTC 格式的值。 您可以自定义 "事件源" 时间戳属性以满足您的解决方案的需要，但 "热" 和 "冷" 存储中的列名称为 "时间戳"。 其他不是事件源时间戳的 datetime JSON 属性将与上述规则中提到的 "_datetime" 一起保存。  | ```"ts": "2020-03-19 14:40:38.318"``` | timestamp |
| 包含特殊字符的 JSON 属性名称。 [\ 和 "使用 [' 和 ' 进行转义]  |  ```"id.wasp": "6A3090FD337DE6B"``` | [' wasp '] _string |
| 在 ["和"] 中，有对单引号和反斜杠的额外转义。 单引号将以 \ 符号形式写入，并将反斜杠写入\\\ | ```"Foo's Law Value": "17.139999389648"``` | ["Foo \' s 定律值"] _double |
| 嵌套的 JSON 对象以句点作为分隔符进行平展。 支持最多10个级别的嵌套。 |  ```"series": {"value" : 316 }``` | series value_long |
| 基元类型的数组存储为动态类型 |  ```"values": [154, 149, 147]``` | values_dynamic |
| 包含对象的数组有两个行为，具体取决于对象内容：如果 TS ID 或时间戳属性在数组中的对象内，则数组将 unrolled，以便初始 JSON 负载产生多个事件。 这使你能够将多个事件成批转换为一个 JSON 结构。 与数组对等的所有顶级属性都将随每个 unrolled 对象一起保存。 如果 TS ID 和时间戳*不*在数组中，则会将其作为动态类型保存。 | 请参阅下面[的示例 A](concepts-json-flattening-escaping-rules.md#example-a)、 [B](concepts-json-flattening-escaping-rules.md#example-b)和[C](concepts-json-flattening-escaping-rules.md#example-c)
| 不平展包含混合元素的数组。 |  ```"values": ["foo", {"bar" : 149}, 147]``` | values_dynamic |
| 512个字符是 JSON 属性的名称限制。 如果该名称超过512个字符，则将其截断为512，并追加 "_<" > "的哈希代码。 **请注意**，这也适用于从平展对象连接的属性名称，表示嵌套的对象路径。 |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` | 数据点<，则为 .。。继续到512个字符>_912ec803b2ce49e4a541068d495ab570_double |

## <a name="understanding-the-dual-behavior-for-arrays"></a>了解阵列的双重行为

对象的数组将全部存储或拆分为多个事件，具体取决于您对数据建模的方式。 这允许你使用数组来批处理事件，并避免在根对象级别定义的重复遥测属性。 批处理可能更有利，因为它会导致发送的事件中心或 IoT 中心消息更少。 

但是，在某些情况下，包含对象的数组仅在其他值的上下文中有意义。 创建多个事件将导致数据无意义。 若要确保对象的数组按原样存储为动态类型，请遵循下面的数据建模指导，并查看[示例 C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-do-i-know-if-my-array-of-objects-will-produce-multiple-events"></a>如何实现知道我的对象数组是否将产生多个事件？

如果一个或多个时序 ID 属性嵌套在数组中的对象中，*或者*如果事件源时间戳属性是嵌套的，则引入引擎会将其拆分，以创建多个事件。 你为 TS ID 和/或时间戳提供的属性名称应遵循上述平展规则，因此将指示你的 JSON 的形状。 请参阅下面的示例，并查看有关如何[选择时序 ID 属性](time-series-insights-update-how-to-id.md)的指南。

### <a name="example-a"></a>示例 A：
对象根和时间戳嵌套的时序 ID<br/>
**环境时序 ID：**`"id"`<br/>
**事件源时间戳：**`"values.time"`<br/>
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

**Parquet 文件中的结果：**
<br/>
上述配置和负载将产生三列和四个事件

| timestamp  | id_string | 值。 value_double 
| ---- | ---- | ---- | 
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` | 
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` | 
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` | 
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` | 

### <a name="example-b"></a>示例 B：
嵌套了一个属性的复合时序 ID<br/> 
**环境时序 ID：** `"plantId"`与`"telemetry.tagId"`<br/>
**事件源时间戳：**`"timestamp"`<br/>
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

**Parquet 文件中的结果：**
<br/>
上述配置和负载将生成四列和六个事件

| timestamp  | plantId_string | 遥测. tagId_string | 遥测. value_double 
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31.149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20.560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336972` | ``100231-A-A7`` | -30.9918 |  
| `2020-01-22T16:42:14Z` | `9336972` | ``100231-A-A4`` | 19.960796 | 

### <a name="example-c"></a>示例 C：
时序 ID 和时间戳位于对象根<br/> 
**环境时序 ID：**`"id"`<br/>
**事件源时间戳：**`"timestamp"`<br/>
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

**Parquet 文件中的结果：**
<br/>
上述配置和负载将产生三列和一个事件

| timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- | 
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>后续步骤

* 了解环境的[吞吐量限制](./concepts-streaming-ingress-throughput-limits.md)
