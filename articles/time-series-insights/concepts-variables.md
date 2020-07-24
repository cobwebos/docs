---
title: 模型变量-Azure 时序见解 Gen2 |Microsoft Docs
description: 模型变量
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 86df87af44f5594fe09445f2cc85d2a2bc823200
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096520"
---
# <a name="time-series-model-variables"></a>时序模型变量

本文介绍了用于指定事件的公式和计算规则的时序模型变量。

每个变量可以是以下三种类型之一： *numeric*、*分类*和*聚合*。

* **数值**类型适用于连续数值。
* “分类”种类适用于一组定义的离散值。****
* **聚合**种类合并了一种类型的多个变量（全部数值或全部分类）。

下表显示了每个变量种类的相关属性。

[![时序模型变量表](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>数字变量

| 变量属性 | 描述 |
| --- | ---|
| 变量筛选器 | 筛选器是可选的条件子句，用于限制可在计算中考虑的行数。 |
| 变量值 | 来自设备或传感器的，或使用时序表达式进行转换的用于计算的遥测值。 数字种类变量的类型必须为 *Double*。|
| 变量内插 | 内插指定如何使用现有数据重构信号。 *Step* 和 *Linear* 内插选项适用于数字变量。 |
| 变量聚合 | 通过支持的[数值变量类型的聚合函数](https://docs.microsoft.com/rest/api/time-series-insights/preview#numeric-variable-kind-1)执行计算。 |

变量符合以下 JSON 示例：

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event['Speed-Sensor'].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "right($value)"
  }
}
```

#### <a name="categorical-variables"></a>分类变量

| 变量属性 | 描述 |
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
     "tsx": "$event.Status.Long" 
},
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

| 变量属性 | 描述 |
| --- | ---|
| 变量筛选器 | 筛选器是可选的条件子句，用于限制可在计算中考虑的行数。 |
| 变量聚合 | 通过支持聚合[变量类型的聚合函数](https://docs.microsoft.com/rest/api/time-series-insights/preview#aggregate-variable-kind-1)执行计算。 |

变量符合以下 JSON 示例：

```JSON
"Speed Range": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "max($event.Speed.Double) - min($event.Speed.Double)"
  }
}
```

变量存储在时序模型的类型定义中，可以通过 Api 以内联方式提供，以替代或补充存储的定义。

## <a name="next-steps"></a>后续步骤

* 了解有关[时序模型](./concepts-model-overview.md)的详细信息。

* 详细了解如何使用[查询 api](./concepts-query-overview.md)以内联方式定义变量。

