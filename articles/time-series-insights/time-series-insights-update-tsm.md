---
title: Azure 时序见解预览中的时序模型 | Microsoft Docs
description: 了解 Azure 时序见解时序模型。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 3c9bface359df020cea0bfff8f82e25e25efbc47
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585246"
---
# <a name="time-series-model"></a>时序模型

本文介绍时序模型、功能，以及如何在 Azure 时序见解预览版环境中开始构建和更新自己的模型。

> [!TIP]
>  * 有关实时时序模型示例，请参阅 [Contoso 风场演示](https://insights.timeseries.azure.com/preview/samples)环境。
> * 阅读有关[Azure 时序见解预览资源管理器](time-series-insights-update-explorer.md)的信息，了解如何导航时序模型 UI。

## <a name="summary"></a>摘要

一直以来，从 IoT 设备收集的数据缺少上下文信息，以致难以快速查找和分析传感器。 时序模型的主要动机是简化 IoT 或时序数据的查找和分析。 它通过启用时序数据的特选、维护和扩充来实现这一目标，帮助为分析准备使用者就绪的数据集。

## <a name="scenario-contosos-new-smart-oven"></a>方案： Contoso 的新智能烤箱

**假设 Contoso 智能烤箱的虚拟应用场景。** 在此方案中，假设每个 Contoso smart 烤箱有五个温度传感器，每四个最多一个刻录机，另一个用于烤箱本身。 直到最近，每个 Contoso 温度传感器分别发送、存储和可视化其数据。 对于其厨房设备监视，Contoso 依赖于基本图表，每个传感器各有一个。

虽然 Contoso 对其最初的数据和可视化解决方案都满意，但有几个限制会变得显而易见：

* 客户希望知道，当大部分顶部刻录机处于打开状态时，整体烤箱将获得的热。 Contoso 更难分析和呈现有关整个烤箱的条件的统一答案。
* Contoso 工程师希望验证同时运行的顶部刻录机是否不会导致能源消耗低下。 出现交叉引用时，哪些温度和电压传感器彼此关联，以及如何在应用商店中查找它们。
* Contoso 质量保证团队想要审核和比较两个传感器版本之间的历史记录。 确定哪些数据属于哪个传感器版本非常困难。

由于不能构建、组织和定义全面的智能烤箱时序模型，每个温度传感器都维护了 dislocated、隔离和不太丰富的数据点。 将这些数据点转换为可操作的见解更难，因为每个数据集的生存期都与其他数据集无关。

这些限制表明了智能数据聚合和可视化工具在 Contoso 的新烤箱中的重要性：

* 当能够将数据关联并合并到方便的视图中时，数据可视化会证明非常有用。 例如，显示电压传感器以及温度传感器。
* 管理多个实体的多维数据以及比较、缩放和时间范围功能可能很难完成。

时序模型为在此虚构的示例中遇到的许多方案**提供了一个方便的解决方案**：

[![时序模型图表](media/v2-update-tsm/tsi-charting.png)](media/v2-update-tsm/tsi-charting.png#lightbox)

* 时序模型在查询和导航中起着至关重要的作用，因为它通过允许跨时间范围以及传感器与设备类型绘制比较来 tsi 数据。
* 数据会进一步过程，因为在时序模型中保留的数据将时序查询计算作为变量保留，并在查询时使用这些计算。
* 时序模型对数据进行组织和聚合，以改进可视化和管理功能。

### <a name="key-capabilities"></a>关键功能

时序模型的目标是让用户毫不费力地管理时序上下文化，它在时序见解预览版中可以实现以下功能。 它可以帮助你：

* 创作和管理利用标量函数、聚合操作等的计算或公式。
* 定义父-子关系以启用导航、搜索和引用。
* 定义与实例关联的属性（定义为*实例字段*），并使用它们来创建层次结构。

### <a name="components"></a>组件

时序模型包含三个核心组件：

* [时序模型实例](#time-series-model-instances)
* [时序模型层次结构](#time-series-model-hierarchies)
* [时序模型类型](#time-series-model-types)

这些组件组合在一起以指定时序模型，并用于组织 Azure 时序见解数据。

[![时序模型类型](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

可以通过[时序见解预览](time-series-insights-update-how-to-tsm.md)界面来创建和管理时序模型。 时序模型设置可以通过[模型设置 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)进行管理。

## <a name="time-series-model-instances"></a>时序模型实例

时序模型*实例*本身就是时间序列的虚拟表示。

在大多数情况下，实例由**deviceId**或**assetId**唯一标识，它们保存为时序 id。

实例具有与它们关联的描述性信息称为*实例属性*，如时间序列 ID、类型、名称、说明、层次结构和实例字段。 实例属性最起码包括层次结构信息。

*实例字段*是描述性信息的集合，可包含层次结构级别的值以及制造商、运算符等。

为时序见解环境配置事件源后，会在时序模型中自动发现和创建实例。 可以通过时序见解资源管理器使用时序模型查询来创建或更新实例。

[Contoso 风场演示](https://insights.timeseries.azure.com/preview/samples)提供多个实时实例示例。

[![时序模型实例](media/v2-update-tsm/instance.png)](media/v2-update-tsm/instance.png#lightbox)

### <a name="instance-properties"></a>实例属性

实例由**timeSeriesId**、 **typeId**、 **name**、 **description**、 **hierarchyIds**和**instanceFields**定义。 每个实例仅映射到一个*类型*和一个或多个*层次结构*。

| 属性 | 说明 |
| --- | ---|
| timeSeriesId | 与实例关联的时序的 UUID。 |
| typeId | 实例与之关联的时序模型类型的 UUID。 默认情况下，所有发现的新实例都关联到默认类型。
| name | **name** 属性可选，并且区分大小写。 如果 "**名称**" 不可用，则默认为 " **timeSeriesId**"。 如果提供了名称， **timeSeriesId** [仍将可用。](time-series-insights-update-explorer.md#preview-well) |
| 说明 | 实例的文本说明。 |
| hierarchyIds | 定义实例所属的层次结构。 |
| instanceFields | **instanceFields** 是实例的属性，并且是定义实例的任何静态数据。 它们定义层次结构或非层次结构属性的值，同时还支持索引，以便能够执行搜索操作。 |

> [!NOTE]
> 层次结构是使用实例字段生成的。 可以为进一步的实例属性定义添加其他**instanceFields** 。

实例具有以下 JSON 表示形式：

```JSON
{
    "timeSeriesId": ["PU2"],
    "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
    "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
    "description": "Pump #2",
    "instanceFields": {
        "Location": "Redmond",
        "Fleet": "Fleet 5",
        "Unit": "Pump Unit 3",
        "Manufacturer": "Contoso",
        "ScalePres": "0.54",
        "scaleTemp": "0.54"
    }
}
```

> [!TIP]
> 有关时序见解实例 API 以及创建、读取、更新和删除（CRUD）支持，请参阅[数据查询](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)一文和[实例 API REST 文档](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)。

## <a name="time-series-model-hierarchies"></a>时序模型层次结构

时序模型*层次结构*通过指定属性名称及其关系对实例进行组织。

可以在给定的时序见解环境中配置多个层次结构。 时序模型实例可以映射到单个层次结构或多个层次结构（多对多关系）。

[Contoso 风场演示](https://insights.timeseries.azure.com/preview/samples)客户端界面显示标准实例和类型层次结构。

[![时序模型层次结构](media/v2-update-tsm/hierarchy.png)](media/v2-update-tsm/hierarchy.png#lightbox)

### <a name="hierarchy-definition"></a>层次结构定义

层次结构由层次结构**id**、**名称**和**源**定义。

| 属性 | 说明 |
| ---| ---|
| id | 层次结构的唯一标识符，例如在定义实例时使用。 |
| name | 用于提供层次结构的名称的字符串。 |
| 源 | 指定组织层次结构或路径，它是用户要创建的层次结构的自顶向下父子顺序。 父子属性映射实例字段。 |

层次结构以 JSON 表示，如下所示：

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

在前面的 JSON 示例中：

* `Location` 定义具有父 `states` 和子 `cities`的层次结构。 每个 `location` 可以有多个 `states`，而后者又可以有多个 `cities`。
* `ManufactureDate` 定义具有父 `year` 和子 `month`的层次结构。 每个 `ManufactureDate` 可以有多个 `years`，而后者又可以有多个 `months`。

> [!TIP]
> 有关时序见解实例 API 和 CRUD 支持，请参阅[数据查询](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)一文和[层次结构 API REST 文档](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)。

### <a name="hierarchy-example"></a>层次结构示例

请考虑一个示例，其中层次结构**H1**将 `building`、`floor`和 `room` 作为其**instanceFieldNames**定义的一部分：

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

给定前面定义中使用的实例字段和多个时间序列，层次结构属性和值如下表中所示：

| 时序 ID | 实例字段 |
| --- | --- |
| ID1 | “building” = “1000”, “floor” = “10”, “room” = “55”  |
| ID2 | “building” = “1000”, “room” = “55” |
| ID3 | “floor” = “10” |
| ID4 | “building” = “1000”, “floor” = “10”  |
| ID5 | 未设置 "生成"、"楼层" 或 "房间"。 |

时序**ID1**和**ID4**在[Azure 时序见解资源管理器](time-series-insights-update-explorer.md)中作为层次结构**H1**的一部分显示，因为它们具有完全定义且正确排序的*生成*、*楼层*和*房间*parameters.

其他在*Unparented 实例*中分类，因为它们不符合指定的数据层次结构。

## <a name="time-series-model-types"></a>时序模型类型

时序模型类型可帮助你定义用于执行计算的变量或公式。 类型与特定的时序见解实例相关联。

一种类型可以具有一个或多个变量。 例如，时序模型实例的类型可能为*温度传感器*，其中包括变量*平均温度*、*最小温度*和*最大温度*。

[Contoso 风场演示](https://insights.timeseries.azure.com/preview/samples)直观显示了与其各自的实例关联的几个时序模型类型。

[![时序模型类型](media/v2-update-tsm/types.png)](media/v2-update-tsm/types.png#lightbox)

> [!TIP]
> 有关时序见解实例 API 和 CRUD 支持，请参阅[数据查询](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)一文和[类型 API REST 文档](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)。

### <a name="type-properties"></a>Type 属性

时序模型类型定义方式如下： **id**、**名称**、**说明**和**变量**。

| 属性 | 说明 |
| ---| ---|
| id | 类型的 UUID。 |
| name | 用于为类型提供名称的字符串。 |
| 说明 | 类型的字符串说明。 |
| variables | 指定与类型关联的变量。 |

类型符合以下 JSON 示例：

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>变量

时序见解类型可能有多个变量，这些变量指定了事件的公式和计算规则。

每个变量可以是以下三*种类型*之一： *numeric*、*分类*和*聚合*。

* **数值**类型适用于连续值。 
* **分类**类型适用于一组定义的离散值。
* **聚合**值组合了单个类型的多个变量（全部数值或所有分类）。

下表显示了每种变量类型相关的属性。

[![时序模型类型](media/v2-update-tsm/variable-table.png)](media/v2-update-tsm/variable-table.png#lightbox)

#### <a name="numeric-variables"></a>数值变量

| 变量属性 | 说明 |
| --- | ---|
| 变量筛选器 | 筛选器是可选的条件子句，用于限制被视为计算的行数。 |
| 变量值 | 用于计算的遥测值来自设备或传感器，或使用时序表达式进行转换。 数值类型变量的类型必须为*Double*。|
| 变量内插 | 内插指定如何使用现有数据重新构造信号。 *单步执行*和*线性*内插选项适用于数值变量。 |
| 变量聚合 | 支持通过*Avg*、 *Min*、 *max*、 *Sum*、 *Count*、 *First*、 *Last*和 time 加权（*Avg*、 *Min*、 *Max*、 *Sum*、 *Left*）运算符进行计算。 |

变量符合以下 JSON 示例：

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>分类变量

| 变量属性 | 说明 |
| --- | ---|
| 变量筛选器 | 筛选器是可选的条件子句，用于限制被视为计算的行数。 |
| 变量值 | 用于计算的遥测值来自设备或传感器。 分类类型变量必须是*Long*或*String*。 |
| 变量内插 | 内插指定如何使用现有数据重新构造信号。 "*步骤*内插" 选项可用于分类变量。 |
| 变量类别 | 类别在来自设备的值与标签之间创建映射。 |
| 变量默认类别 | 默认类别为 "类别" 属性中未映射的所有值。 |

变量符合以下 JSON 示例：

```JSON
"Status": {
  "kind": "categorical",
  "value": "toLong($event.[Status].Double)",
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>聚合变量

| 变量属性 | 说明 |
| --- | ---|
| 变量筛选器 | 筛选器是可选的条件子句，用于限制被视为计算的行数。 |
| 变量聚合 | 支持通过*Avg*、 *Min*、 *Max*、 *Sum*、 *Count*、 *First*、 *Last*进行计算。 |

变量符合以下 JSON 示例：

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

变量存储在时序模型的类型定义中，可以通过[查询 api](time-series-insights-update-tsq.md)以内联方式提供，以替代存储的定义。

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 时序见解预览存储和入口](./time-series-insights-update-storage-ingress.md)
- 了解[Azure 时序见解预览版中的数据建模](./time-series-insights-update-how-to-tsm.md)中的常见时序模型操作
- 阅读新的[时序模型](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)参考文档。
