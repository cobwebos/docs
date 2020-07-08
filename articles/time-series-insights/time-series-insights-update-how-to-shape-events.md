---
title: 塑造事件 - Azure 时序见解 | Microsoft Docs
description: 了解最佳做法以及如何在 Azure 时序见解预览版中塑造用于查询的事件。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: fd2c58b07f3be5d5fa6d99d0c8c64906b81e7de4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86036978"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>使用 Azure 时序见解预览版塑造事件

本文介绍了塑造 JSON 有效负载以便在 Azure 时序见解中引入，以及最大程度地提高预览版查询的效率时可以采用的最佳做法。

## <a name="best-practices"></a>最佳实践

最好认真考虑如何向时序见解预览版环境发送事件。 

常规最佳做法包括：

* 尽量高效地通过网络发送数据。
* 以特定的方式存储数据，以便更好地根据方案来聚合数据。

为了获得最佳查询性能，请遵循以下经验法则：

* 不要发送不必要的属性。 时序见解预览版按使用量收费。 最好仅存储和处理你要查询的数据。
* 对于静态数据，请使用实例字段。 此做法有助于避免通过网络发送静态数据。 实例字段是时序模型的组件，其工作方式类似于时序见解正式版服务中的参考数据。 若要详细了解实例字段，请阅读[时序模型](./concepts-model-overview.md)。
* 在两个或多个事件中共享维度属性。 此做法可以更有效地通过网络发送数据。
* 不要使用深层数组嵌套。 时序见解预览版最多支持两个级别的包含对象的嵌套数组。 时序见解预览版会将消息中的数组平展成包含属性值对的多个事件。
* 如果所有或大多数事件只存在几个度量，最好是在同一个对象中将这些度量作为单独的属性发送。 单独发送度量可以减少事件数目，并可能会提高查询的效率，因为要处理的事件更少。

## <a name="column-flattening"></a>列平展

在引入过程中，会将包含嵌套对象的有效负载平展，这样，列名将是包含界定符的单个值。

* 例如，下面的嵌套 JSON：

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   在平展后将变为：`data_flow`。

> [!IMPORTANT]
> * Azure 时序见解预览版使用下划线 (`_`) 进行列界定。
> * 请注意，这与正式版不同，正式版将改用句点 (`.`)。

下面阐述了更复杂的方案。

#### <a name="example-1"></a>示例 1：

以下方案包含两个（或多个）设备，它们发送度量（信号）：“流速”、“引擎油压”、“温度”和“湿度”。    

发送了一条 Azure IoT 中心消息，其中的外层数组包含通用维度值的共享节（请注意消息中包含的两个设备条目）。

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

**要点：**

* 示例 JSON 有一个外层数组，它使用[时序实例](./concepts-model-overview.md#time-series-model-instances)数据来提高消息的效率。 尽管时序实例设备元数据不可能发生更改，但它通常可为数据分析提供有用的属性。

* JSON 将两条或更多消息（每个设备一条）组合成单个有效负载，从而随着时间的推移节省带宽。

* 每个设备的各个序列数据点被组合成单个**序列**属性，从而降低了对每个设备进行连续流式更新的需求。

> [!TIP]
> 若要减少发送数据所需的消息数并使遥测更有效，请考虑将公用维度值和时序实例元数据成批放到单个 JSON 有效负载中。

#### <a name="time-series-instance"></a>时序实例 

接下来，我们将详细介绍如何使用[时序实例](./concepts-model-overview.md#time-series-model-instances)来更好地塑造 JSON。 

> [!NOTE]
> 下面的[时序 ID](./time-series-insights-update-how-to-id.md) 是 *deviceId*。

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

时序见解预览版会在查询时联接一个表（平展后）。 此表包含其他列，例如“类型”。 

| deviceId  | 类型 | L1 | L2 | timestamp | series_Flow Rate ft3/s | series_Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | 电池系统 | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   电池系统 |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    电池系统 |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  上表表示[时序预览资源管理器](./time-series-insights-update-explorer.md)中的查询视图。

**结论**

* 在前面的示例中，静态属性存储在时序见解预览中，以优化通过网络发送的数据。
* 时序见解预览版数据在查询时通过在实例中定义的时序 ID 进行联接。
* 使用了两个嵌套层。 该数字是时序见解预览版支持的最大数字。 必须避免深层嵌套的数组。
* 由于度量很少，因此将其作为单独的属性在同一对象中发送。 在此示例中， **Series_Flow rate psi**， **series_Engine 石油压力 Psi**， **series_Flow rate ft3/s**为唯一列。

>[!IMPORTANT]
> 实例字段不与遥测数据一起存储。 它们与元数据一起存储在时序模型中。

#### <a name="example-2"></a>示例 2：

请考虑以下 JSON：

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```

在上面的示例中，平展的 `data["flow"]` 属性会与 `data_flow` 属性造成命名冲突。

在这种情况下，最新的属性值会覆盖以前的值。** 

> [!TIP]
> 若要获得更多帮助，请联系时序见解团队！

> [!WARNING] 
> * 如果由于平展或其他机制导致相同（单数）事件有效负载中存在重复属性，则会存储最新的 > 属性值，并覆盖以前的任何值。
> * 组合事件的序列不会相互覆盖。

## <a name="next-steps"></a>后续步骤

* 若要将这些指导原则付诸实践，请阅读 [Azure 时序见解预览版查询语法](./time-series-insights-query-data-csharp.md)。 你将了解有关用于数据访问的时序见解[预览 REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview)的查询语法的详细信息。

* 结合 JSON 最佳实践与[如何时序模型](./time-series-insights-update-how-to-tsm.md)。
