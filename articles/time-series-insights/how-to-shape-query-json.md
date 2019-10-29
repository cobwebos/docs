---
title: 在 Azure 时序见解查询中塑造 JSON 的最佳做法 | Microsoft Docs
description: 了解如何提高 Azure 时序见解查询的效率。
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 09090354012d2cd3ba050ff9c94593947f27b006
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990284"
---
# <a name="shape-json-to-maximize-query-performance"></a>最大化查询性能的形状 JSON 

本文提供了有关如何将 JSON 整形以最大程度地提高 Azure 时序见解查询效率的指导。

## <a name="video"></a>视频

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>了解整形 JSON 以满足你的存储需求的最佳实践。</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>最佳做法

考虑如何向时序见解发送事件。 也就是说，您始终可以：

1. 尽量高效地通过网络发送数据。
1. 请确保以某种方式存储数据，以便能够执行适用于方案的聚合。
1. 请确保不会达到时序见解的最大属性限制：
   - 对于 S1 环境，属性（列）数目不能超过 600 个。
   - 对于 S2 环境，属性（列）数目不能超过 800 个。

> [!TIP]
> 查看 Azure 时序见解预览版中的[限制和计划](time-series-insights-update-plan.md)。

以下指南可帮助确保最佳查询性能：

1. 不要使用动态属性（如标记 ID）作为属性名称。 这种使用有助于达到最大属性限制。
1. 不要发送不必要的属性。 如果不需要查询属性，最好不要发送它。 这样就避免了存储限制。
1. 使用[引用数据](time-series-insights-add-reference-data-set.md)来避免通过网络发送静态数据。
1. 在多个事件之间共享维度属性，以便更有效地通过网络发送数据。
1. 不要使用深层数组嵌套。 时序见解最多支持两个级别的嵌套数组，其中包含对象。 时序见解将消息中的数组平展为多个具有属性值对的事件。
1. 如果所有或大多数事件只存在几个度量，最好是在同一个对象中将这些度量作为单独的属性发送。 单独发送这些事件可以减少事件的数量，并可能提高查询性能，因为需要处理的事件更少。 如果有多个度量值，则将它们作为值发送到单个属性可以最大程度地减少达到最大属性限制的可能性。

## <a name="example-overview"></a>示例概述

下面两个示例演示了如何发送事件来突出显示前面的建议。 在每个示例中，可以看到建议的应用方式。

这些示例基于多个设备发送度量值或信号的场景。 度量值或信号可以是流动率、引擎石油压力、温度和湿度。 在第一个示例中，所有设备提供了一些度量值。 第二个示例包含多个设备，每个设备发送多个独特的度量。

## <a name="scenario-one-only-a-few-measurements-exist"></a>方案一：仅存在少数度量值

> [!TIP]
> 建议将每个度量值或信号作为单独的属性或列发送。

在下面的示例中，有一个 Azure IoT 中心消息，其中外部数组包含公共维度值的共享节。 该外部数组使用参考数据来提高消息的效率。 引用数据包含每个事件都不会改变的设备元数据，但它为数据分析提供了有用的属性。 批处理公共维度值和采用引用数据会节省通过线路发送的字节数，这使消息更有效。

使用在发送到 Azure 云时序列化为 JSON 的[IoT 设备消息对象](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet)，将以下 JSON 负载发送到你的时序见解 GA 环境：


```JSON
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

* 引用包含**key 属性的**数据表：

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | 欧洲 |
   | FYYY | LINE\_DATA | 美国 |

* 平展后的时序见解事件表：

   | deviceId | messageId | deviceLocation | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | 欧洲 | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | 欧洲 | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | 美国 | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - **deviceId** 列充当机群中各种设备的列标题。 如果将**deviceId**值作为其自身的属性名称，则会将总设备数限制为595（适用于 S1 环境）或795（适用于 S2 环境），其中包含其他五个列。
> - 避免了不必要的属性（例如，品牌和型号的信息）。 由于以后不会查询这些属性，因此，消除这些属性可以更好地利用网络和存储效率。
> - 参考数据用于减少通过网络传输的字节数。 使用 key 属性**deviceId**联接两个特性**messageId**和**msds-devicelocation** 。 此数据在入口时与遥测数据联接，然后存储在时序见解中用于查询。
> - 使用两层嵌套，这是时序见解所支持的最大嵌套量。 必须避免深层嵌套的数组。
> - 度量值作为不同的属性发送到相同的对象中，因为有很少的度量值。 此处的 **series.Flow Rate psi** 和 **series.Engine Oil Pressure ft3/s** 是唯一的列。

## <a name="scenario-two-several-measures-exist"></a>方案二：存在多个度量值

> [!TIP]
> 建议将度量值作为 "类型"、"单位" 和 "值" 元组发送。

示例 JSON 有效负载：

```JSON
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

* 具有键属性**deviceId**和**tagId**的引用数据表：

   | deviceId | series.tagId | messageId | deviceLocation | type | 单位 |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | 欧洲 | 流速 | ft3/s |
   | FXXX | oilPressure | LINE\_DATA | 欧洲 | 引擎油压 | psi |
   | FYYY | pumpRate | LINE\_DATA | 美国 | 流速 | ft3/s |
   | FYYY | oilPressure | LINE\_DATA | 美国 | 引擎油压 | psi |

* 平展后的时序见解事件表：

   | deviceId | series.tagId | messageId | deviceLocation | type | 单位 | timestamp | series.value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | LINE\_DATA | 欧洲 | 流速 | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | LINE\_DATA | 欧洲 | 引擎油压 | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | LINE\_DATA | 欧洲 | 流速 | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | LINE\_DATA | 欧洲 | 引擎油压 | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | LINE\_DATA | 美国 | 流速 | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | LINE\_DATA | 美国 | 引擎油压 | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - 列**deviceId**和**tagId**充当汽油中各种设备和标记的列标题。 将每个作为其自身的属性，可将查询限制为594（适用于 S1 环境）或794（适用于 S2 环境）包含其他六个列的设备总数。
> - 出于第一个示例中提到的原因，已避免了不必要的属性。
> - 引用数据用于通过引入**deviceId**来减少通过网络传输的字节数，该 id 用于唯一的**messageId**和**msds-devicelocation**。 复合键**序列 tagId**用于**类型**和**单元**的唯一对。 组合键允许使用**deviceId**和**tagId**对来表示四个值： **messageId、msds-devicelocation、type**和**unit**。 此数据将与传入时的遥测数据联接。 然后，将其存储在时序见解中用于查询。
> - 出于第一个示例中提到的原因，使用了两层嵌套。

### <a name="for-both-scenarios"></a>对于两种场景

对于具有大量可能值的属性，最好将作为单个列中的不同值发送，而不是为每个值创建新列。 对于前面的两个示例：

  - 在第一个示例中，有几个属性具有多个值，因此，每个属性都是一个单独的属性。
  - 在第二个示例中，不将度量值指定为单独的属性。 相反，它们是标准系列属性下的值或度量值的数组。 发送新的密钥**tagId** ，这会在平展表中创建新的**tagId**列。 新的属性**类型**和**单元**是通过使用引用数据创建的，因此不会达到属性限制。

## <a name="next-steps"></a>后续步骤

- 详细了解[如何向云发送 IoT 中心设备消息](../iot-hub/iot-hub-devguide-messages-construct.md)。

- 阅读[Azure 时序见解查询语法](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax)，详细了解时序见解数据访问 REST API 的查询语法。

- 了解[如何绘制事件](./time-series-insights-send-events.md)。
