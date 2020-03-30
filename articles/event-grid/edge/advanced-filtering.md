---
title: 高级筛选 - Azure 事件网格 IoT 边缘 |微软文档
description: IoT 边缘事件网格中的高级筛选。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992556"
---
# <a name="advanced-filtering"></a>高级筛选
事件网格允许在 json 负载中的任何属性上指定筛选器。 这些筛选器建模为条件集`AND`，每个外部条件具有可选的内部`OR`条件。 对于每个`AND`条件，指定以下值：

* `OperatorType`- 比较的类型。
* `Key`- 要应用筛选器的属性的 json 路径。
* `Value`- 运行筛选器的引用值（或） `Values` - 运行筛选器的引用值集。

## <a name="json-syntax"></a>JSON 语法

高级筛选器的 JSON 语法如下所示：

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>筛选数组值

事件网格今天不支持对值数组进行筛选。 如果传入事件具有高级筛选器键的数组值，则匹配操作将失败。 传入事件最终与事件订阅不匹配。

## <a name="and-or-not-semantics"></a>AND-OR-NOT 语义

请注意，在前面给出的 json 示例中`AdvancedFilters`，是一个数组。 将每个`AdvancedFilter`数组元素视为条件`AND`。

对于支持`NumberIn`多个值（如 、`NumberNotIn`等`StringIn`）的运算符，每个值都被视为条件。 `OR` 因此，将`StringBeginsWith("a", "b", "c")`匹配以 或`a`或`b``c`开头的任何字符串值。

> [!CAUTION]
> NOT 运算符`NumberNotIn`-`StringNotIn`并在现场给出`Values`的每个值上作为 AND 条件执行。
>
> 不这样做将使筛选器成为"接受-所有"筛选器，并破坏筛选的目的。

## <a name="floating-point-rounding-behavior"></a>浮点舍入行为

事件网格使用`decimal`.NET 类型来处理所有数值。 事件订阅 JSON 中指定的编号值不受浮点舍入行为的约束。

## <a name="case-sensitivity-of-string-filters"></a>字符串筛选器的区分大小写

所有字符串比较都是不区分大小写的。 今天没有办法改变这种行为。

## <a name="allowed-advanced-filter-keys"></a>允许的高级筛选器键

该`Key`属性可以是已知的顶级属性，也可以是具有多个点的 json 路径，其中每个点表示踏入嵌套的 json 对象。

事件网格对密钥中的`$`字符没有任何特殊含义，与 JSONPath 规范不同。

### <a name="event-grid-schema"></a>事件网格架构

对于事件网格架构中的事件：

* ID
* 主题
* 主题
* EventType
* DataVersion
* 数据.Prop1
* 数据.Prop_Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>自定义事件架构

自定义事件架构没有限制，`Key`因为事件网格不会在负载上强制实施任何包络架构。

## <a name="numeric-single-value-filter-examples"></a>数字单值筛选器示例

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>数值范围值筛选器示例

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>字符串范围值筛选器示例

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>布尔单值筛选器示例

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
