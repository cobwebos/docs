---
title: 动态 Azure Maps 的 StylesObject
description: 用于在动态 Azure Maps 中创建的 StylesObject 的 JSON 架构和语法的参考指南。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4b085fbc6e330d38b59fce0c494f672b00c712b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85120476"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>动态映射的 StylesObject 架构参考指南

本文是对的 JSON 架构和语法的参考指南 `StylesObject` 。 `StylesObject`是 `StyleObject` 表示 stateset 样式的数组。 使用 Azure Maps 创建者 [功能状态服务](https://docs.microsoft.com/rest/api/maps/featurestate) 将 stateset 样式应用到室内地图数据功能。 一旦创建了 stateset 样式，并将其与室内地图功能关联，就可以使用它们来创建动态室内地图。 有关创建动态室内地图的详细信息，请参阅 [实现 Creator 室内地图的动态样式](indoor-map-dynamic-styling.md)。

## <a name="styleobject"></a>StyleObject

`StyleObject`可以是 [`BooleanTypeStyleRule`](#booleantypestylerule) 或 [`NumericTypeStyleRule`](#numerictypestylerule) 。

下面的 JSON 显示了一个名为的 `BooleanTypeStyleRule` `occupied` 和名为的 `NumericTypeStyleRule` `temperature` 。

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 `NumericTypeStyleRule`是一个 [`StyleObject`](#styleobject) 并且包含以下属性：

| properties | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `keyName` | string | *状态*或动态属性名称。 在 `keyName` 数组中应是唯一的 `StyleObject` 。| 是 |
| `type` | 字符串 | 值为 "数值"。 | 是 |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| 具有关联颜色的数字样式范围的数组。 每个范围都定义一个颜色，当 *状态值* 满足此范围时，将使用该颜色。| 是 |

### <a name="numberruleobject"></a>NumberRuleObject

`NumberRuleObject`由 [`RangeObject`](#rangeobject) 和 `color` 属性组成。 如果 *状态* 值落在范围内，则其显示颜色将是在属性中指定的颜色 `color` 。

如果定义多个重叠范围，选择的颜色将是在满足的第一个范围中定义的颜色。

在下面的 JSON 示例中，当 *状态值* 介于50-60 之间时，两个范围都将为 true。 但是，将使用的颜色是 `#343deb` 因为它是列表中已满足的第一个范围。

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| properties | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject)定义一组逻辑范围条件，如果为 `true` ，则将*状态*的显示颜色更改为属性中指定的颜色 `color` 。 如果未 `range` 指定， `color` 将始终使用在属性中定义的颜色。   | 否 |
| `color` | 字符串 | 状态值落到范围中时要使用的颜色。 `color`属性是采用以下任意一种格式的 JSON 字符串： <ul><li> HTML 样式的十六进制值 </li><li> RGB ( "#ff0"、"#ffff00"、"rgb (255、255、0) " ) </li><li> RGBA ( "rgba (255、255、0、1) " ) </li><li> HSL ( "hsl (100、50%、50% ) " ) </li><li> HSLA ( "HSLA (100，50%，50%，1) " ) </li><li> 预定义的 HTML 颜色名称，如黄色和蓝色。</li></ul> | 是 |

### <a name="rangeobject"></a>RangeObject

`RangeObject`定义的数值范围值 [`NumberRuleObject`](#numberruleobject) 。 要使 *状态值* 进入范围，所有定义的条件都必须为 true。 

| properties | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `minimum` | double | X ≥的所有数字 x `minimum` 。| 否 |
| `maximum` | double | X ≤的所有数字 x `maximum` 。 | 否 |
| `exclusiveMinimum` | double | X > 的所有数字 x `exclusiveMinimum` 。| 否 |
| `exclusiveMaximum` | double | X < 的所有数字 x `exclusiveMaximum` 。| 否 |

### <a name="example-of-numerictypestylerule"></a>NumericTypeStyleRule 的示例

下面的 JSON 阐释了一个 `NumericTypeStyleRule` 名为的 *状态* `temperature` 。 在此示例中， [`NumberRuleObject`](#numberruleobject) 包含两个定义的温度范围及其关联的颜色样式。 如果温度范围为50-69，则显示应该使用颜色 `#343deb` 。  如果温度范围为31-70，则显示应该使用颜色 `#eba834` 。

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

`BooleanTypeStyleRule`是一个 [`StyleObject`](#styleobject) 并且包含以下属性：

| properties | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `keyName` | string |  *状态*或动态属性名称。  在 `keyName` style 数组中应该是唯一的。| 是 |
| `type` | 字符串 |值为 "boolean"。 | 是 |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)2| 具有和状态值的颜色的布尔对 `true` `false` *state* 。| 是 |

### <a name="booleanruleobject"></a>BooleanRuleObject

`BooleanRuleObject`定义 `true` 和值的颜色 `false` 。

| properties | 类型 | 说明 | 必须 |
|-----------|----------|-------------|-------------|
| `true` | string | *状态*值为时要使用的颜色 `true` 。 `color`属性是采用以下任意一种格式的 JSON 字符串： <ul><li> HTML 样式的十六进制值 </li><li> RGB ( "#ff0"、"#ffff00"、"rgb (255、255、0) " ) </li><li> RGBA ( "rgba (255、255、0、1) " ) </li><li> HSL ( "hsl (100、50%、50% ) " ) </li><li> HSLA ( "HSLA (100，50%，50%，1) " ) </li><li> 预定义的 HTML 颜色名称，如黄色和蓝色。</li></ul>| 是 |
| `false` | 字符串 | *状态*值为时要使用的颜色 `false` 。 | 是 |

### <a name="example-of-booleantypestylerule"></a>BooleanTypeStyleRule 的示例

下面的 JSON 阐释了一个 `BooleanTypeStyleRule` 名为的 *状态* `occupied` 。 [`BooleanRuleObject`](#booleanruleobject)定义 `true` 和值的颜色 `false` 。

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```
