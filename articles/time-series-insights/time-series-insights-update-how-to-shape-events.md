---
title: 形状事件-Azure 时序见解 |Microsoft Docs
description: 了解最佳做法，以及如何在 Azure 时间见解预览版中对查询进行查询。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650917"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>使用 Azure 时序见解预览版塑造事件

本文定义了在 Azure 时序见解中确定用于引入的 JSON 有效负载的最佳实践，并最大限度地提高了预览查询的效率。

## <a name="best-practices"></a>最佳做法

最好认真考虑如何向时序见解预览环境发送事件。 

一般最佳实践包括：

* 尽量高效地通过网络发送数据。
* 以特定的方式存储数据，以便更好地根据方案来聚合数据。

为了获得最佳查询性能，请遵循以下经验法则：

* 不要发送不必要的属性。 时序见解按使用情况预览帐单。 最好只存储和处理所要查询的数据。
* 对于静态数据，请使用实例字段。 这种做法有助于避免通过网络发送静态数据。 实例字段是时序模型的一个组件，它类似于时序见解服务中公开发布的引用数据。 若要了解有关实例字段的详细信息，请参阅[时序模型](./time-series-insights-update-tsm.md)。
* 在两个或多个事件中共享维度属性。 此做法可以更有效地通过网络发送数据。
* 不要使用深层数组嵌套。 时序见解预览最多支持包含对象的两个级别的嵌套数组。 时序见解预览版会将消息中的数组平展成包含属性值对的多个事件。
* 如果所有或大多数事件只存在几个度量，最好是在同一个对象中将这些度量作为单独的属性发送。 单独发送这些事件可以减少事件的数量，并可能提高查询性能，因为需要处理的事件更少。

## <a name="column-flattening"></a>列平展

在引入过程中，将对包含嵌套对象的负载进行平展，使列名称是具有 delineator 的单个值。

* 例如，下面的嵌套 JSON：

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   变为：平展后 `data_flow`。

> [!IMPORTANT]
> * Azure 时序见解预览版对略图列使用下划线（`_`）。
> * 请注意，一般可用性的不同之处在于使用句点（`.`）。

更复杂的方案如下所示。

#### <a name="example-1"></a>示例 1：

以下方案包含两个（或多个）设备，这些设备用于发送测量（信号）： *Flow 率*、*引擎石油压力*、*温度*和*湿度*。

发送了单个 Azure IoT 中心消息，其中外部数组包含公共维度值的共享部分（请注意消息中包含的两个设备条目）。

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

**结论**

* 示例 JSON 有一个外部数组，该数组使用[时序实例](./time-series-insights-update-tsm.md#time-series-model-instances)数据提高消息的效率。 尽管时序实例设备元数据不可能发生更改，但它通常为数据分析提供有用的属性。

* JSON 将两个或多个消息（每个设备一个）合并为在一段时间内随带宽节省的单一负载。

* 每个设备的单个序列数据点合并为一个**序列**属性，从而减少了每个设备不断流式传输更新的需要。

> [!TIP]
> 若要减少发送数据所需的消息数并使遥测更有效，请考虑将公共维度值和时序实例元数据批处理为单个 JSON 有效负载。

#### <a name="time-series-instance"></a>时序实例 

接下来，我们将详细介绍如何使用[时序实例](./time-series-insights-update-tsm.md#time-series-model-instances)来更好地绘制 JSON。 

> [!NOTE]
> 以下时序[id](./time-series-insights-update-how-to-id.md)为*deviceIds*。

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

| deviceId  | 类型 | L1 | L2 | timestamp | series_Flow 速率 ft3/秒 | series_Engine 石油压力 psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | 电池系统 | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   电池系统 |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    电池系统 |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  上表表示[时序预览资源管理器](./time-series-insights-update-explorer.md)中的查询视图。

**结论**

* 在前面的示例中，静态属性存储在时序见解预览中，以优化通过网络发送的数据。
* 时序见解预览数据在查询时通过实例中定义的时序 ID 联接。
* 使用两层嵌套。 此数字是时序见解预览版支持的最大数量。 必须避免深层嵌套的数组。
* 由于度量很少，因此将其作为单独的属性在同一对象中发送。 在此示例中， **Series_Flow rate psi**， **series_Engine 石油压力 Psi**， **series_Flow rate ft3/s**为唯一列。

>[!IMPORTANT]
> 实例字段不存储遥测数据。 它们随元数据一起存储在时序模型中。

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

在上面的示例中，平展 `data["flow"]` 属性会出现与 `data_flow` 属性的命名冲突。

在这种情况下，*最新*的属性值将覆盖以前的属性值。 

> [!TIP]
> 若要获得更多帮助，请联系时序见解团队！

> [!WARNING] 
> * 如果由于平展或其他机制导致相同（单数）事件有效负载中存在重复属性，则会存储最新的 > 属性值，并覆盖以前的任何值。
> * 组合事件的序列不会相互覆盖。

## <a name="next-steps"></a>后续步骤

* 若要将这些准则引入实践，请阅读[Azure 时序见解预览查询语法](./time-series-insights-query-data-csharp.md)。 你将了解有关用于数据访问的时序见解[预览 REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview)的查询语法的详细信息。

* 结合 JSON 最佳实践与[如何时序模型](./time-series-insights-update-how-to-tsm.md)。
