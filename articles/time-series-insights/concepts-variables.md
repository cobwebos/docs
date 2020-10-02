---
title: 模型变量- Azure 时序见解第 2 代 | Microsoft Docs
description: 模型变量
author: shreyasharmamsft
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: dc03a74bfad5b1417eb9cc20b5f211718cc08646
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653700"
---
# <a name="time-series-model-variables"></a>时序模型变量

本文介绍了时序见解变量，这些变量指定事件的公式和计算规则。

每个变量可以是以下三个种类之一：数字、分类和聚合。  

* “数字”种类适用于连续数值。
* “分类”种类适用于一组定义的离散值。
* “聚合”种类组合单个种类（要么全为数字，要么全为分类）的多个变量。

下表显示了每个变量种类的相关属性。

[![时序模型变量表](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

## <a name="numeric-variables"></a>数字变量

| 变量属性 | 说明 |
| --- | ---|
| 变量筛选器 | 筛选器是可选的条件子句，用于限制可在计算中考虑的行数。 |
| 变量值 | 来自设备或传感器的，或使用时序表达式进行转换的用于计算的遥测值。 数字种类变量的类型必须为 *Double*。|
| 变量内插 | 内插指定如何使用现有数据重构信号。 *Step* 和 *Linear* 内插选项适用于数字变量。 |
| 变量聚合 | 通过[“数字”变量种类支持的聚合函数](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#numeric-variable-kind)执行计算。 |

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

## <a name="categorical-variables"></a>分类变量

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
      "values": [4],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

## <a name="aggregate-variables"></a>聚合变量

| 变量属性 | 说明 |
| --- | ---|
| 变量筛选器 | 筛选器是可选的条件子句，用于限制可在计算中考虑的行数。 |
| 变量聚合 | 通过[“聚合”变量种类支持的聚合函数](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#aggregate-variable-kind)执行计算。 |

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

变量存储在时序模型的类型定义中，可以通过 API以内联方式提供，以重写或补充已存储的定义。

## <a name="next-steps"></a>后续步骤

* 详细了解[时序模型](./concepts-model-overview.md)。

* 阅读有关如何使用[查询 API](./concepts-query-overview.md) 以内联方式定义变量的详细信息。
