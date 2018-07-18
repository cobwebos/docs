---
title: 有关在 Azure 时序见解查询中塑造 JSON 的最佳做法。
description: 了解如何提高时序见解查询的效率。
services: time-series-insights
author: ashannon7
manager: timlt
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: bryanla
ms.openlocfilehash: 29919a3ce8c18982c88f7f0e6bbd774c612e9c44
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659273"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>如何塑造 JSON 以最大化查询性能 

本文提供有关塑造 JSON 以最大化 Azure 时序见解 (TSI) 查询效率的指导。

## <a name="best-practices"></a>最佳实践

必须考虑如何将事件发送到时序见解。 也就是说，始终应该：

1. 尽量高效地通过网络发送数据。
2. 确保数据的存储方式可让你执行适合方案的聚合。
3. 确保不会达到 TSI 的最大属性数限制：
   - 对于 S1 环境，属性（列）数目不能超过 600 个。
   - 对于 S2 环境，属性（列）数目不能超过 800 个。

以下指导可帮助实现最佳查询性能：

1. 不要使用动态属性（例如标记 ID）作为属性名称，因为此类属性容易导致达到最大属性数限制。
2. 不要发送不必要的属性。 如果不需要查询属性，最好不要发送它，以避免达到存储限制。
3. 使用[参考数据](time-series-insights-add-reference-data-set.md)来避免通过网络发送静态数据。
4. 在多个事件之间共享维度属性，以更有效地通过网络发送数据。
5. 不要使用深层数组嵌套。 TSI 最多支持两个级别的包含对象的嵌套数组。 TSI 会将消息中的数组平展成包含属性值对的多个事件。
6. 如果所有或大多数事件只存在几个度量，最好是在同一个对象中将这些度量作为单独的属性发送。 单独发送度量可以减少事件数目，并提高查询的效率，因为要处理的事件更少。 如果存在多个度量，在单个属性中将它们作为值发送可以最大程度地减少达到最大属性数限制的可能性。

## <a name="examples"></a>示例

以下两个示例演示如何发送事件，并强调前面所述的建议。 遵循每个示例可以了解建议的应用方式。

这些示例基于多个设备发送度量值或信号的场景。 度量值或信号可能包括“流速”、“引擎油压”、“温度”和“湿度”。 在第一个示例中，所有设备提供了一些度量值。 在第二个示例提到许多的设备，每个设备发送多个唯一的度量值。

### <a name="scenario-only-a-few-measurementssignals-exist"></a>场景：只存在少量的度量值/信号

**建议：** 将每个度量值作为单独的属性/列发送。

以下示例提到单个 IoT 中心消息，其中的外部数组包含通用维度值的共享节。 该外部数组使用参考数据来提高消息的效率。 参考数据包含设备元数据，这些数据不会根据每个事件变化，但提供有用的属性用于数据分析。 批处理通用维度值和采用参考数据都能减少通过网络发送的字节数，从而提高消息的效率。

示例 JSON 有效负载：

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

参考数据表（键属性为 deviceId）：

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | LINE\_DATA | EU |
| FYYY | LINE\_DATA | 美国 |

时序见解事件表（平展后）：

| deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| FYYY | LINE\_DATA | 美国 | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

请注意前面示例中的以下内容：

- **deviceId** 列充当机群中各种设备的列标题。 对于其他五个列，尝试使 deviceId 值成为其自身的属性名称会将设备总数限制为 595（S1 环境）或 795（S2 环境）。

- 避免不必要的属性，例如制造商和型号信息等。由于将来不会查询这些信息，消除它们对网络和存储效率有利。

- 参考数据用于减少通过网络传输的字节数。 已使用键属性 **deviceId** 联接 **messageId** 和 **deviceLocation** 这两个特性。 此数据在传入时联接到遥测数据，然后存储在 TSI 中以供查询。

- 使用了两个嵌套层，这是 TSI 支持的最大嵌套数量。 必须避免深层嵌套的数组。

- 由于度量很少，度量作为单独的属性在同一个对象中发送。 此处的 **series.Flow Rate psi** 和 **series.Engine Oil Pressure ft3/s** 是唯一的列。

### <a name="scenario-several-measures-exist"></a>场景：存在多个度量

**建议：** 将度量值作为“type”、“unit”、“value”元组发送。

示例 JSON 有效负载：

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

参考数据（键属性为 deviceId 和 series.tagId）：

| deviceId | series.tagId | messageId | deviceLocation | type | 单位 |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | 流速 | ft3/s |
| FXXX | oilPressure | LINE\_DATA | EU | 引擎油压 | psi |
| FYYY | pumpRate | LINE\_DATA | 美国 | 流速 | ft3/s |
| FYYY | oilPressure | LINE\_DATA | 美国 | 引擎油压 | psi |

时序见解事件表（平展后）：

| deviceId | series.tagId | messageId | deviceLocation | type | 单位 | timestamp | series.value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | LINE\_DATA | EU | 流速 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | LINE\_DATA | EU | 引擎油压 | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | LINE\_DATA | EU | 流速 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | LINE\_DATA | EU | 引擎油压 | Psi | 2018-01-17T01:17:00Z | 49.2 |
| FYYY | pumpRate | LINE\_DATA | 美国 | 流速 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| FYYY | oilPressure | LINE\_DATA | 美国 | 引擎油压 | psi | 2018-01-17T01:18:00Z | 22.2 |

注意前面示例中的以下内容与第一个示例类似：

- 列 **deviceId** 和 **series.tagId** 充当机群中各个设备和标记的列标题。 对于其他六个列，将每个值用作其自身的特性会将设备总数查询限制为 594（S1 环境）或 794（S2 环境）。

- 出于第一个示例中提到的原因，请避免不必要的属性。

- 参考数据用于减少通过网络传输的字节数，因为针对 **messageId** 和 **deviceLocation** 的唯一对引入了 **deviceId**。 针对 **type** 和 **unit** 的唯一对使用了组合键 **series.tagId**。 该组合键允许使用 **deviceId** 和 **series.tagId** 对来引用四个值：**messageId、deviceLocation、type** 和 **unit**。 此数据在传入时联接到遥测数据，然后存储在 TSI 中以供查询。

- 出于第一个示例中提到的原因，使用了两个嵌套层。

### <a name="for-both-scenarios"></a>对于两种场景

如果某个属性包含大量的可能值，则最好是在单个列中将这些值作为非重复值发送，而不要为每个值创建一个新列。 对于前面的两个示例：
  - 在第一个示例中，少量的属性包含多个值，因此，适合将每个属性设为单独的属性。 
  - 但是，在第二个示例中可以看到，度量并未指定为单独的属性，而是在一系列通用属性下使用了值/度量数组。 发送了新键 **tagId**，该键在平展表中创建新列 **series.tagId**。 使用参考数据创建了新属性 **type** and **unit**，从而防止达到属性限制。

## <a name="next-steps"></a>后续步骤

若要将这些指导原则付诸实践，请参阅 [Azure 时序见解查询语法](/rest/api/time-series-insights/time-series-insights-reference-query-syntax)，以详细了解 TSI 数据访问 REST API 的查询语法。