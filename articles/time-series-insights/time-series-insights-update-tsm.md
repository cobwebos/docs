---
title: 时序模型 - Azure 时序见解 | Microsoft Docs
description: 了解 Azure 时序见解预览版中的时序模型
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/29/2020
ms.custom: seodec18
ms.openlocfilehash: 1487cbb7885711beca969604316fd151defb114a
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580605"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Azure 时序见解预览版中的时序模型

本文介绍时序模型及其功能，以及如何在 Azure 时序见解预览版环境中开始生成和更新自己的模型。

> [!TIP]
>
> * 有关实时时序模型的示例，请转到  [Contoso 风力发电厂演示](https://insights.timeseries.azure.com/preview/samples)环境。
> * 若要了解如何在时序模型 UI 中导航，请阅读有关 [Azure 时序见解预览版资源管理器](time-series-insights-update-explorer.md)的文章。
> * 通过时序见解 Web 资源管理器了解[如何使用时序模型](time-series-insights-update-how-to-tsm.md)。

## <a name="summary"></a>总结

一直以来，从 IoT 设备收集的数据缺少上下文信息，以致难以快速查找和分析传感器。 时序模型的主要动机是简化 IoT 或时序数据的查找和分析。 为了实现此目标，它会启用时序数据的策划、维护和扩充，以帮助准备随时可供使用的分析数据集。

## <a name="scenario-contosos-new-smart-oven"></a>方案： Contoso 的新智能烤箱

**下面虚构了一个 Contoso 智能烤箱的场景。** 在此场景中，假设每部 Contoso 智能烤箱有五个温度传感器，顶部的四个电热丝各配有一个传感器，烤箱本体配有一个传感器。 每个 Contoso 温度传感器单独发送、存储和可视化到目前为止的数据。 Contoso 依赖使用基本图表来监视其厨房电器，每个图表对应于一个传感器。

尽管 Contoso 对其最初的数据和可视化解决方案感到满意，但有几处限制也不容忽视：

* 客户想要知道，当顶部的大部分电热丝都打开时，整个烤箱的温度会有多高。 在整个烤箱的工作状况方面，Contoso 很难进行分析并提供统一的答案。
* Contoso 工程师希望验证在顶部的电热丝同时工作时，会不会导致用电效率下降。 很难通过交叉参考来确定哪些温度和电压传感器彼此关联，以及如何在存储中定位这些传感器。
* Contoso 质量保证团队希望审核并比较两个传感器版本之间的历史记录。 难以确定哪些数据属于哪个传感器版本。

由于不能构建、组织和定义统括式的智能烤箱时序模型，每个温度传感器都会保留失位、隔离且参考价值不大的数据点。 由于每个数据集相互独立，更难以将这些数据点转化为可行的见解。

这些限制揭示了随 Contoso 的新烤箱附送智能数据聚合与可视化工具的重要性：

* 如果能够将数据关联且合并一个可方便查看的视图，则可以证明数据可视化的作用。 例如，将电压传感器与温度传感器一起显示。
* 既要管理多个实体的多维数据，同时又要提供比较、缩放和时间范围功能，可能很难实现。

时序模型为此虚构示例中出现的许多场景提供一个**便利的解决方案**：

[![时序模型智能烤箱图表示例](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* 时序模型在查询和导航中起着至关重要的作用，因为它允许跨时间范围以及在传感器与设备类型之间绘制比较比较，因此可将数据上下文化。 （**A**）
* 数据之所以能够进一步上下文化，是因为时序模型中保存的数据将时序查询计算结果作为变量保留，并在查询时重新使用这些结果。
* 时序模型对数据进行组织与聚合，以改进可视化和管理功能。 （**B**）

### <a name="key-capabilities"></a>关键功能

时序模型的目标是让用户毫不费力地管理时序上下文化，它在时序见解预览版中可以实现以下功能。 它可以帮助你：

* 利用标量函数、聚合操作等创作和管理计算或公式。
* 定义父子关系以实现导航、搜索和引用。
* 定义与实例（定义为实例字段）关联的属性，并使用这些属性来创建层次结构。**

### <a name="components"></a>组件

时序模型包括三个核心组件：

* [时序模型实例](#time-series-model-instances)
* [时序模型层次结构](#time-series-model-hierarchies)
* [时序模型类型](#time-series-model-types)

组合这些组件可以指定时序模型以及组织 Azure 时序见解数据。

[![时序模型概述图表](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

可以通过[时序见解预览版](time-series-insights-update-how-to-tsm.md)界面来创建和管理时序模型。 可以通过[模型设置 API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api) 来管理时序模型设置。

## <a name="time-series-model-instances"></a>时序模型实例

时序模型实例是时序本身的虚拟表示形式。**

在大多数情况下，实例由保存为时序 ID 的 **deviceId** 或 **assetId** 唯一标识。

实例具有与自身关联的、称作“实例属性”的描述性信息，例如时序 ID、类型、名称、说明、层次结构和实例字段。** 实例属性最起码包括层次结构信息。

实例字段是描述性信息的集合，可包含层次结构级别的值，以及制造商、运营商等信息。**

为时序见解环境配置事件源后，可在时序模型中自动发现和创建实例。 可以使用时序模型查询通过时序见解资源管理器来创建或更新实例。

[Contoso 风力发电厂演示](https://insights.timeseries.azure.com/preview/samples)提供了多个实时实例示例。

[![时序模型实例示例](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>实例属性

实例由 **timeSeriesId**、**typeId**、**name**、**description**、**hierarchyIds** 和 **instanceFields** 定义。 每个实例仅映射到一个*类型*和一个或多个*层次结构*。

| 属性 | 说明 |
| --- | ---|
| timeSeriesId | 与实例关联的时序的 UUID。 |
| typeId | 与实例关联的时序模型类型的 UUID。 默认情况下，所有已发现的新实例都会关联到默认类型。
| name | **name** 属性可选，区分大小写。 如果未提供 **name**，则默认值为 **timeSeriesId**。 如果提供了名称，也仍会在 [well](time-series-insights-update-explorer.md#4-time-series-well) 中提供 **timeSeriesId**。 |
| description | 实例的文本说明。 |
| hierarchyIds | 定义实例所属的层次结构。 |
| instanceFields | 实例的属性，并且是定义实例的任何静态数据。 它们定义层次结构或非层次结构属性的值，同时还支持索引，以便能够执行搜索操作。 |

> [!NOTE]
> 层次结构是使用实例字段生成的。 可为其他实例属性定义添加更多的 **instanceFields**。

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
> 有关时序见解实例 API 以及创建、读取、更新和删除 (CRUD) 支持，请阅读[数据查询](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)一文和[实例 API REST 文档](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)。

## <a name="time-series-model-hierarchies"></a>时序模型层次结构

时序模型层次结构通过指定属性名称及其关系来组织实例。**

可以在给定的时序见解环境中配置多个层次结构。 一个时序模型实例可以映射到一个或多个层次结构（多对多的关系）。

[Contoso 风力发电厂演示](https://insights.timeseries.azure.com/preview/samples)客户端界面显示了标准实例和类型层次结构。

[![时序模型层次结构示例](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>层次结构定义

层次结构由层次结构**id**、**名称**和**源**定义。

| 属性 | 说明 |
| ---| ---|
| id | 层次结构的唯一标识符，例如，在定义实例时使用。 |
| name | 用于提供层次结构名称的字符串。 |
| source | 指定组织的层次结构或路径，即，用户想要创建的层次结构的自上而下的父子顺序。 父子属性映射实例字段。 |

层次结构的 JSON 表示形式如下：

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

在上面的 JSON 示例中：

* `Location` 定义包含父级 `states` 和子级 `cities` 的层次结构。 每个 `location` 可以包含多个 `states`，而后者又可以包含多个 `cities`。
* `ManufactureDate` 定义包含父级 `year` 和子级 `month` 的层次结构。 每个 `ManufactureDate` 可以包含多个 `years`，而后者又可以包含多个 `months`。

> [!TIP]
> 有关时序见解实例 API 和 CRUD 支持，请阅读[数据查询](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)一文和[层次结构 API REST 文档](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)。

### <a name="hierarchy-example"></a>层次结构示例

请考虑一个示例`building`，其中层次结构`floor` **H1**将`room` 、和作为其**instanceFieldNames**定义的一部分：

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

根据上述定义和多个时序中使用的实例字段，层次结构属性和值将如下表中所示：

| 时序 ID | 实例字段 |
| --- | --- |
| ID1 | "生成" = "1000"，"地板" = "10"，"房间" = "55"  |
| ID2 | "生成" = "1000"，"房间" = "55" |
| ID3 | "楼层" = "10" |
| ID4 | "生成" = "1000"，"地板" = "10"  |
| ID5 | 未设置 "生成"、"楼层" 或 "房间"。 |

时序 **ID1** 和 **ID4** 在 [Azure 时序见解资源管理器](time-series-insights-update-explorer.md)中显示为层次结构 **H1** 的一部分，因为它们包含已完全定义且正确排序的 *building*、*floor* 和 *room* 参数。

其他 ID 分类到“无父实例”下，因为它们不符合指定的数据层次结构。**

## <a name="time-series-model-types"></a>时序模型类型

时序模型类型可帮助你定义用于执行计算的变量或公式。** 类型与特定的时序见解实例相关联。

一个类型可以包含一个或多个变量。 例如，某个时序模型实例的类型为“温度传感器”，其中包括变量“平均温度”、“最小温度”和“最大温度”。********

[Contoso 风力发电厂演示](https://insights.timeseries.azure.com/preview/samples)可视化了与相应实例关联的多个时序模型类型。

[![时序模型类型示例](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> 有关时序见解实例 API 和 CRUD 支持，请阅读[数据查询](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis)一文和[类型 API REST 文档](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)。

### <a name="type-properties"></a>Type 属性

时序模型类型由 **id**、**name**、**description** 和 **variables** 定义。

| 属性 | 说明 |
| ---| ---|
| id | 类型的 UUID。 |
| name | 用于提供类型名称的字符串。 |
| description | 类型的字符串说明。 |
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

时序见解类型可能有多个变量，这些变量指定事件的公式和计算规则。

每个变量可以是以下三个种类之一：数字、分类和聚合。********

* “数字”种类适用于连续值。****
* “分类”种类适用于一组定义的离散值。****
* “聚合”值组合单个种类（要么全为数字，要么全为分类）的多个变量。****

下表显示了每个变量种类的相关属性。

[![时序模型变量表](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>数字变量

| 变量属性 | 说明 |
| --- | ---|
| 变量筛选器 | 筛选器是可选的条件子句，用于限制可在计算中考虑的行数。 |
| 变量值 | 来自设备或传感器的，或使用时序表达式进行转换的用于计算的遥测值。 数字种类变量的类型必须为 *Double*。|
| 变量内插 | 内插指定如何使用现有数据重构信号。 *Step* 和 *Linear* 内插选项适用于数字变量。 |
| 变量聚合 | 支持通过 *Avg*、*Min*、*Max*、*Sum*、*Count*、*First*、*Last* 和时间加权（*Avg*、*Min*、*Max*、*Sum*、*Left*）运算符进行计算。 |

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
| 变量筛选器 | 筛选器是可选的条件子句，用于限制可在计算中考虑的行数。 |
| 变量值 | 来自设备或传感器的用于计算的遥测值。 分类种类变量的类型必须是 *Long* 或 *String*。 |
| 变量内插 | 内插指定如何使用现有数据重构信号。 *Step* 内插选项适用于分类变量。 |
| 变量类别 | 类别在来自设备或传感器的值与某个标签之间创建映射。 |
| 变量的默认类别 | 默认类别适用于“categories”属性中未映射的所有值。 |

变量符合以下 JSON 示例：

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
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
| 变量筛选器 | 筛选器是可选的条件子句，用于限制可在计算中考虑的行数。 |
| 变量聚合 | 支持通过 *Avg*、*Min*、*Max*、*Sum*、*Count*、*First* 和 *Last* 进行计算。 |

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

变量存储在时序模型的类型定义中，可以通过[查询 API](time-series-insights-update-tsq.md) 以内联方式提供，以重写存储的定义。

## <a name="next-steps"></a>后续步骤

- 阅读 [Azure 时序见解预览版存储和引入](./time-series-insights-update-storage-ingress.md)。

- 在 [Azure 时序见解预览版中的数据建模](./time-series-insights-update-how-to-tsm.md)中了解常用的时序模型操作

- 阅读新[时序模型](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)的参考文档。
