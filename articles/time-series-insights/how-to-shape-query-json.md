---
title: 在 Azure 时序见解查询中塑造 JSON 的最佳做法 | Microsoft Docs
description: 了解如何提高 Azure 时序见解查询的效率。
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244460"
---
# <a name="shape-json-to-maximize-query-performance"></a>若要最大化查询性能的 JSON 形状 

本文指导如何形状 JSON 以最大化你的 Azure 时序见解查询的效率。

## <a name="video"></a>视频

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>了解有关调整 JSON 以满足你的存储需求的最佳实践。</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>最佳做法
考虑一下如何将事件发送到时序见解。 也就是说，您始终：

1. 尽量高效地通过网络发送数据。
1. 请确保你的数据存储在一种方法，因此，你可以执行聚合适用于你的方案。
1. 请确保你未达到的时序见解最大属性限制：
   - 对于 S1 环境，属性（列）数目不能超过 600 个。
   - 对于 S2 环境，属性（列）数目不能超过 800 个。

以下指南可帮助确保获得最佳的可能查询性能：

1. 不要使用动态属性，如标记 ID，作为属性名称。 达到最大属性限制导致这种用法。
1. 不要发送不必要的属性。 如果查询属性并不是必需，则最好不要将其发送。 这种方式，避免存储限制。
1. 使用[引用数据](time-series-insights-add-reference-data-set.md)以避免通过网络发送的静态数据。
1. 共享以更有效地通过网络发送数据的多个事件之间的维度属性。
1. 不要使用深层数组嵌套。 时序见解支持最多两个级别的嵌套的数组，包含对象的对象。 时序见解并平展的消息中与属性值对的多个事件。
1. 如果所有或大多数事件只存在几个度量，最好是在同一个对象中将这些度量作为单独的属性发送。 单独发送它们可以减少事件数目，可能会提高查询性能，因为需要处理更少的事件。 当存在多个度量值时，将它们发送为中的单个属性的值降至最低的最大属性限制的可能性。

## <a name="example-overview"></a>示例概述

以下两个示例演示如何将发送事件，以突出显示前面的建议。 根据每个示例中，您可以看到如何应用建议。

这些示例基于多个设备发送度量值或信号的场景。 度量值或信号可以是流速率、 引擎机油压力、 温度和湿度。 在第一个示例中，所有设备提供了一些度量值。 第二个示例具有多个设备和每个设备发送多个唯一的度量值。

## <a name="scenario-one-only-a-few-measurements-exist"></a>方案一：仅几个度量值存在

> [!TIP]
> 我们建议你将每个度量值或信号发送作为单独的属性或列。

以下示例中，在没有外部数组包含共享的常见维度值一部分的其中一条 Azure IoT 中心消息。 该外部数组使用参考数据来提高消息的效率。 引用数据包含设备元数据，不会随每个事件，但它提供了用于数据分析的有用属性。 批处理常见维度值以及如何使用的引用数据将保存在通过网络发送的字节数，这将使消息更高效。

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
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* 具有键属性的引用数据表**deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | EU |
   | FYYY | LINE\_DATA | 美国 |

* 时间平展后的时序见解事件表：

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | 美国 | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

这两个表的说明：

- **deviceId** 列充当机群中各种设备的列标题。 生成的 deviceId 值其自己的属性名称限制到 595 （适用于 S1 环境） 或使用其他五列 795 （适用于 S2 环境） 的设备总数。
- 避免了不必要的属性，有关示例、 品牌和型号信息。 因为属性不会在将来进行查询，消除它们可以更好的网络和存储效率。
- 参考数据用于减少通过网络传输的字节数。 两个属性**messageId**并**deviceLocation**通过使用键属性联接**deviceId**。 此数据与遥测数据联接在入口时，然后将存储在时序见解用于查询。
- 使用两个嵌套层，这是支持的时序见解的嵌套最大数量。 必须避免深层嵌套的数组。
- 因为有几个度量值将作为相同对象内的单独属性发送的度量值。 此处的 **series.Flow Rate psi** 和 **series.Engine Oil Pressure ft3/s** 是唯一的列。

## <a name="scenario-two-several-measures-exist"></a>方案二：存在多个度量值

> [!TIP]
> 我们建议您将度量在"键入时，""单位"和"value"元组发送。

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

* 具有键属性的引用数据表**deviceId**并**series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | type | 单位 |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流速 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎油压 | psi |
   | FYYY | pumpRate | LINE\_DATA | 美国 | 流速 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | 美国 | 引擎油压 | psi |

* 时间平展后的时序见解事件表：

   | deviceId | series.tagId | messageId | deviceLocation | type | 单位 | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | EU | 流速 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎油压 | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | EU | 流速 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | EU | 引擎油压 | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | 美国 | 流速 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | 美国 | 引擎油压 | psi | 2018-01-17T01:18:00Z | 22.2 |

这两个表的说明：

- 列**deviceId**并**series.tagId**用作列标题的各种设备和一队中的标记。 使用每个作为其自己的属性将查询限制为 594 （适用于 S1 环境） 或 794 （适用于 S2 环境） 的总设备的其他六个列数。
- 避免了不必要的属性，第一个示例中提到的原因。
- 使用引用数据来减少通过引入通过网络传输的字节数**deviceId**，为唯一的对用于**messageId**和**deviceLocation**. 组合键**series.tagId**用于的唯一的对**类型**并**单元**。 可以使用复合密钥**deviceId**并**series.tagId**对要用来为四个值，请参阅： **messageId，deviceLocation，键入，** 和**单元**. 在引入时，如果与遥测数据联接此数据。 它然后是用于查询存储在时序见解。
- 对于第一个示例中提到的原因，使用两个嵌套层。

### <a name="for-both-scenarios"></a>对于两种场景

对于具有大量可能的值的属性，最好是以单个列而不是创建一个新列的每个值中的非重复值的形式发送。 对于前面的两个示例：

  - 在第一个示例中，几个属性具有多个值，因此它适合，使每一个单独的属性。
  - 在第二个示例中，度量值不被指定为单独的属性。 相反，它们的值或常见的系列属性下的度量值的数组。 新的密钥**tagId**发送，这将创建新的列**series.tagId**平展表中。 新属性**类型**并**单元**以便属性限制不会达到使用引用数据创建的。

## <a name="next-steps"></a>后续步骤

- 读取[Azure 时序见解查询语法](/rest/api/time-series-insights/ga-query-syntax)若要了解有关查询语法的详细信息，为时序见解数据访问 REST API。
- 了解[如何形状事件](./time-series-insights-send-events.md)。
