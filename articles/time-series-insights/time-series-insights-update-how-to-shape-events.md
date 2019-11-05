---
title: 使用 Azure 时序见解预览版塑造事件 | Microsoft Docs
description: 了解如何在 Azure 时序见解预览版中绘制事件。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: f8a50e062d2dac1f30f8b745f351570262daac53
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990900"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>使用 Azure 时序见解预览版塑造事件

本文介绍如何塑造 JSON 文件，以最大化 Azure 时序见解预览版的查询效率。

## <a name="best-practices"></a>最佳实践

考虑如何将事件发送到时序见解预览版。 也就是说，始终应该：

* 尽量高效地通过网络发送数据。
* 以特定的方式存储数据，以便更好地根据方案来聚合数据。

为了获得最佳查询性能，请执行以下操作：

* 不要发送不必要的属性。 时序见解预览版按使用情况收费。 最好是存储后再处理要查询的数据。
* 对于静态数据，请使用实例字段。 此做法有助于避免通过网络发送静态数据。 实例字段是时序模型的一个组件，它类似于时序见解服务中公开发布的引用数据。 若要了解有关实例字段的详细信息，请参阅[时序模型](./time-series-insights-update-tsm.md)。
* 在两个或多个事件中共享维度属性。 此做法可以更有效地通过网络发送数据。
* 不要使用深层数组嵌套。 时序见解预览版最多支持两个级别的包含对象的嵌套数组。 时序见解预览版会将消息中的数组平展成包含属性值对的多个事件。
* 如果所有或大多数事件只存在几个度量，最好是在同一个对象中将这些度量作为单独的属性发送。 单独发送度量可以减少事件数目，并可能会提高查询的效率，因为要处理的事件更少。

## <a name="example"></a>示例

以下示例基于至少两个设备发送度量值或信号的场景。 度量值或信号可能包括“流速”、“引擎油压”、“温度”和“湿度”。

在此示例中，有一个 Azure IoT 中心消息，其中外部数组包含公共维度值的共享节。 该外部数组使用时序实例数据来提高消息的效率。 

时序实例包含设备元数据。 对于每个事件，此元数据不会更改，但它确实为数据分析提供了有用的属性。 若要节省通过线路发送的字节并使消息更有效，请考虑批处理公共维度值和使用时序实例元数据。

### <a name="example-json-payload"></a>示例 JSON 有效负载

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

### <a name="time-series-instance"></a>时序实例 
> [!NOTE]
> 时序 ID 为 *deviceId*。

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

时序见解预览版会在查询时联接一个表（平展后）。 此表包含其他列，例如“类型”。 以下示例演示如何才能[塑造](./time-series-insights-send-events.md#supported-json-shapes)遥测数据。

| deviceId  | 类型 | L1 | L2 | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | 电池系统 | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   电池系统 |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    电池系统 |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

在上一示例中，请注意以下几点：

* 静态属性存储在时序见解预览版中，这样可以优化通过网络发送的数据。
* 时序见解预览数据在查询时通过实例中定义的时序 ID 联接。
* 使用两层嵌套。 此数字是时序见解预览版支持的最大数量。 必须避免深层嵌套的数组。
* 由于度量很少，因此将其作为单独的属性在同一对象中发送。 在此示例中，**series.Flow Rate psi**、**series.Engine Oil Pressure psi** 和 **series.Flow Rate ft3/s** 是唯一的列。

>[!IMPORTANT]
> 实例字段不与遥测数据一起存储。 它们随元数据一起存储在时序模型中。
> 上表表示查询视图。

## <a name="next-steps"></a>后续步骤

- 若要将这些指导原则付诸实践，请参阅 [Azure 时序见解预览版查询语法](./time-series-insights-query-data-csharp.md)。 你将了解有关用于数据访问的时序见解预览 REST API 的查询语法的详细信息。
- 若要了解支持的 JSON 形状，请参与[支持的 JSON 形状](./time-series-insights-send-events.md#supported-json-shapes)。
