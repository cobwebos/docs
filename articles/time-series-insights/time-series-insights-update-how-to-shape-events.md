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
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650917"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>使用 Azure 时序见解预览版塑造事件

本文定义了用于在 Azure 时间序列见解中引入 JSON 有效负载并最大限度地提高预览查询效率的最佳做法。

## <a name="best-practices"></a>最佳做法

最好仔细考虑如何向时序见解预览环境发送事件。 

一般最佳做法包括：

* 尽量高效地通过网络发送数据。
* 以特定的方式存储数据，以便更好地根据方案来聚合数据。

为了获得最佳的查询性能，请遵循以下经验法则：

* 不要发送不必要的属性。 时间序列见解 按使用情况预览帐单。 最好只存储和处理要查询的数据。
* 对于静态数据，请使用实例字段。 这种做法有助于避免通过网络发送静态数据。 实例字段是时序模型的组件，其工作方式类似于时序见解正式版服务中的参考数据。 若要详细了解实例字段，请阅读[时序模型](./time-series-insights-update-tsm.md)。
* 在两个或多个事件中共享维度属性。 此做法可以更有效地通过网络发送数据。
* 不要使用深层数组嵌套。 时序见解预览版最多支持两个级别的包含对象的嵌套数组。 时序见解预览版会将消息中的数组平展成包含属性值对的多个事件。
* 如果所有或大多数事件只存在几个度量，最好是在同一个对象中将这些度量作为单独的属性发送。 单独发送度量可以减少事件数目，并可能会提高查询的效率，因为要处理的事件更少。

## <a name="column-flattening"></a>列拼平

在引入期间，包含嵌套对象的有效负载将被拼合，以便列名称是具有划线器的单个值。

* 例如，以下嵌套 JSON：

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   变为：`data_flow`拼合时。

> [!IMPORTANT]
> * Azure 时间序列见解预览使用下划`_`线 （ ） 进行列表示。
> * 请注意与使用期间 （`.`） 的"常规可用性"的区别。

更复杂的场景如下图所示。

#### <a name="example-1"></a>示例 1：

以下方案有两个（或更多）设备发送测量（信号）：*流速*、*发动机机油压力*、*温度*和*湿度*。

发送单个 Azure IoT 中心消息，其中外部数组包含公共维度值的共享部分（请注意消息中包含的两个设备条目）。

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

**外卖：**

* 示例 JSON 具有一个外部数组，该数组使用[时间序列实例](./time-series-insights-update-tsm.md#time-series-model-instances)数据来提高消息的效率。 即使时间序列实例设备元数据不太可能更改，它通常为数据分析提供有用的属性。

* JSON 将两个或多个消息（每个设备中的一条消息）合并到单个有效负载中，从而随着时间的推移节省带宽。

* 每个设备的单个系列数据点合并到单个**系列**属性中，减少了对每台设备持续流式传输更新的需求。

> [!TIP]
> 为了减少发送数据所需的消息数，提高遥测效率，请考虑将通用维度值和时间序列实例元数据批处理到单个 JSON 负载中。

#### <a name="time-series-instance"></a>时序实例 

让我们仔细看看如何使用[时间序列实例](./time-series-insights-update-tsm.md#time-series-model-instances)更优化地塑造 JSON。 

> [!NOTE]
> 下面的[时间序列 ID](./time-series-insights-update-how-to-id.md)是*设备 ID*。

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

时序见解预览版会在查询时联接一个表（平展后）。 此表包含其他列，例如“类型”。****

| deviceId  | 类型 | L1 | L2 | timestamp | series_Flow Rate ft3/s | series_Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | 电池系统 | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   电池系统 |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    电池系统 |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  前面的表表示[时间序列预览资源管理器](./time-series-insights-update-explorer.md)中的查询视图。

**外卖：**

* 在前面的示例中，静态属性存储在时间序列见解预览中，以优化通过网络发送的数据。
* 时序见解预览版数据在查询时通过在实例中定义的时序 ID 进行联接。
* 使用了两个嵌套层。 该数字是时序见解预览版支持的最大数字。 必须避免深层嵌套的数组。
* 由于度量很少，因此将其作为单独的属性在同一对象中发送。 在此示例中 **，series_Flow速率 psi、series_Engine****油压 psi**和**series_Flow速率 ft3/s**是唯一的列。

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
> 请联系时序洞察团队以获得更多帮助！

> [!WARNING] 
> * 如果由于拼合或其他机制而在同一（单数）事件负载中存在重复属性，则存储最新的>属性值，从而过度写入任何以前的值。
> * 一系列合并事件不会相互覆盖。

## <a name="next-steps"></a>后续步骤

* 若要将这些指导原则付诸实践，请阅读 [Azure 时序见解预览版查询语法](./time-series-insights-query-data-csharp.md)。 您将了解有关时间序列见解[预览 REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview)用于数据访问的查询语法的更多信息。

* 将 JSON 最佳实践与["如何时间序列模型"](./time-series-insights-update-how-to-tsm.md)相结合。
