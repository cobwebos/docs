---
title: Azure 地图 Web SDK 中的数据驱动样式表达式 |微软 Azure 地图
description: 在本文中，您将了解如何在 Microsoft Azure 地图 Web SDK 中使用数据驱动的样式表达式。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d6009a655adcc26ebef31588eff2332a05f3a001
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804718"
---
# <a name="data-driven-style-expressions-web-sdk"></a>数据驱动的样式表达式（Web SDK）

表达式使您能够将业务逻辑应用于样式选项，这些选项观察数据源中每个形状中定义的属性。 表达式可以筛选数据源或图层中的数据。 表达式可能由条件逻辑组成，如 if 语句。 并且，它们可用于使用字符串运算符、逻辑运算符和数学运算符来操作数据。

数据驱动的样式减少了围绕样式实现业务逻辑所需的代码量。 当与图层一起使用时，表达式将在单独的线程的渲染时计算。 与在 UI 线程上评估业务逻辑相比，此功能提供了更高的性能。

此视频概述了 Azure 地图 Web SDK 中的数据驱动的样式。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

表达式表示为 JSON 数组。 数组中表达式的第一个元素是指定表达式运算符名称的字符串。 例如，"+"或"案例"。 下一个元素（如果有）是表达式的参数。 每个参数都是文本值（字符串、数字、布尔或 ）或其他`null`表达式数组。 以下伪代码定义表达式的基本结构。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure 映射 Web SDK 支持多种类型的表达式。 表达式可以自行使用，也可以与其他表达式结合使用。

| 表达式类型 | 说明 |
|---------------------|-------------|
| [聚合表达式](#aggregate-expression) | 定义通过一组数据处理的计算的表达式，`clusterProperties`可以与 选项一`DataSource`起使用。 |
| [布尔表达式](#boolean-expressions) | 布尔表达式提供了一组用于评估布尔比较的布尔运算符表达式。 |
| [颜色表达式](#color-expressions) | 颜色表达式使创建和操作颜色值变得更加容易。 |
| [条件表达式](#conditional-expressions) | 条件表达式提供的逻辑操作类似于 if 语句。 |
| [数据表达式](#data-expressions) | 提供对要素中属性数据的访问。 |
| [插值和步进表达式](#interpolate-and-step-expressions) | 插值和步进表达式可用于计算沿插值曲线或步进函数的值。 |
| [特定于图层的表达式](#layer-specific-expressions) | 仅适用于单个图层的特殊表达式。 |
| [数学表达式](#math-expressions) | 提供数学运算符，用于在表达式框架中执行数据驱动的计算。 |
| [字符串运算符表达式](#string-operator-expressions) | 字符串运算符表达式对字符串执行转换操作，例如串联和转换大小写。 |
| [类型表达式](#type-expressions) | 类型表达式提供了用于测试和转换不同数据类型（如字符串、数字和布尔值）的工具。 |
| [变量绑定表达式](#variable-binding-expressions) | 变量绑定表达式将计算结果存储在变量中，并在表达式的其他位置多次引用，而无需重新计算存储的值。 |
| [缩放表达式](#zoom-expression) | 在渲染时检索地图的当前缩放级别。 |

本文档中的所有示例都使用以下功能来演示使用不同类型的表达式的不同方式。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>数据表达式

数据表达式提供对要素中属性数据的访问。 

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['at', number, array]` | 对象 (object) | 从数组中检索项。 |
| `['geometry-type']` | 字符串 | 获取要素的几何类型：点、多点、线串、多行串、多边形、多面形。 |
| `['get', string]` | 值 | 从当前要素的属性获取属性值。 如果请求的属性丢失，则返回 null。 |
| `['get', string, object]` | 值 | 从提供的对象的属性获取属性值。 如果请求的属性丢失，则返回 null。 |
| `['has', string]` | boolean | 确定要素的属性是否具有指定的属性。 |
| `['has', string, object]` | boolean | 确定对象的属性是否具有指定的属性。 |
| `['id']` | 值 | 如果要素有该功能的 ID，则获取该功能的 ID。 |
| `['length', string | array]` | 数字 | 获取字符串或数组的长度。 |
| `['in', boolean | string | number, array]` | boolean | 确定数组中是否存在项 |
| `['in', substring, string]` | boolean | 确定子字符串是否存在于字符串中 |

**示例**

可以使用`get`表达式直接在表达式中访问要素的属性。 此示例使用要素的"区域颜色"值指定气泡图层的颜色属性。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

如果所有点要素都具有该`zoneColor`属性，则上述示例将工作正常。 如果他们不这样做，颜色可能会回落到"黑色"。 要修改回退颜色，请使用`case`表达式与`has`表达式结合使用，以检查该属性是否存在。 如果属性不存在，则返回回退颜色。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

默认情况下，气泡和符号图层将呈现数据源中所有形状的坐标。 此行为可以突出显示多边形或线的顶点。 图层`filter`的选项可用于通过使用布尔表达式中的`['geometry-type']`表达式来限制其呈现的要素的几何类型。 下面的示例限制气泡图层，以便仅`Point`呈现要素。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

以下示例允许呈现和`Point``MultiPoint`特征。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同样，多边形的轮廓将在线图层中呈现。 要在行图层中禁用此行为，请添加只允许`LineString`和`MultiLineString`功能的筛选器。  

## <a name="math-expressions"></a>数学表达式

数学表达式提供数学运算符，用于在表达式框架中执行数据驱动的计算。

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['+', number, number, …]` | 数字 | 计算指定数字的总和。 |
| `['-', number]` | 数字 | 按指定数字减去 0。 |
| `['-', number, number]` | 数字 | 将第一个数字减去第二个数字。 |
| `['*', number, number, …]` | 数字 | 将指定的数字相乘在一起。 |
| `['/', number, number]` | 数字 | 将第一个数字除以第二个数字。 |
| `['%', number, number]` | 数字 | 将第一个数字除以第二个数字时计算余数。 |
| `['^', number, number]` | 数字 | 计算第一个值的值，以第二个数字的功率提升。 |
| `['abs', number]` | 数字 | 计算指定数字的绝对值。 |
| `['acos', number]` | 数字 | 计算指定数字的弧形。 |
| `['asin', number]` | 数字 | 计算指定数字的弧线。 |
| `['atan', number]` | 数字 | 计算指定数字的弧形。 |
| `['ceil', number]` | 数字 | 将数字向上舍入到下一个整数。 |
| `['cos', number]` | 数字 | 计算指定数字的 cos。 |
| `['e']` | 数字 | 返回数学常量`e`。 |
| `['floor', number]` | 数字 | 将数字向下舍入到以前的整数。 |
| `['ln', number]` | 数字 | 计算指定数字的自然对数。 |
| `['ln2']` | 数字 | 返回数学常量`ln(2)`。 |
| `['log10', number]` | 数字 | 计算指定数字的基数 10 对数。 |
| `['log2', number]` | 数字 | 计算指定数字的基数-二对数。 |
| `['max', number, number, …]` | 数字 | 计算指定数字集中的最大数字。 |
| `['min', number, number, …]` | 数字 | 计算指定数字集中的最小数字。 |
| `['pi']` | 数字 | 返回数学常量`PI`。 |
| `['round', number]` | 数字 | 将数字舍入到最接近的整数。 中间值从零四舍五入。 例如，`['round', -1.5]`计算为 -2。 |
| `['sin', number]` | 数字 | 计算指定数字的子值。 |
| `['sqrt', number]` | 数字 | 计算指定数字的平方根。 |
| `['tan', number]` | 数字 | 计算指定数字的切线。 |

## <a name="aggregate-expression"></a>聚合表达式

聚合表达式定义通过一组数据处理的计算，`clusterProperties`并可与 选项一`DataSource`起使用。 这些表达式的输出必须是数字或布尔。 

聚合表达式采用三个值：运算符值和初始值，以及用于从数据中的每个要素检索属性以应用聚合操作的表达式。 此表达式具有以下格式：

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 运算符：表达式函数，然后应用于针对群集`mapExpression`中每个点计算的所有值。 支持的运算符： 
    - 对于数字： `+` `*`， `max`， ， ，`min`
    - 对于布尔斯： `all`，`any`
- 初始值：聚合第一个计算值的初始值。
- mapExpression：应用于数据集中每个点的表达式。

**示例**

如果数据集中的所有要素都有一个`revenue`属性，这是一个数字。 然后，可以计算从数据集创建的群集中所有点的总收入。 此计算使用以下聚合表达式完成：`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>布尔表达式

布尔表达式提供了一组用于评估布尔比较的布尔运算符表达式。

比较值时，将严格键入比较。 不同类型的值始终被视为不相等。 已知类型在分析时不同的情况被视为无效，并将生成分析错误。 

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | 逻辑否定。 如果`true`输入为`false`，则返回`false`，如果输入为`true`。 |
| `['!= ', value, value]` | boolean | 如果`true`输入值不相等，则返回，`false`否则。 |
| `['<', value, value]` | boolean | 如果`true`第一个输入严格小于第二个输入，`false`则返回，否则。 参数必须是字符串或两个数字。 |
| `['<=', value, value]` | boolean | 如果`true`第一个输入小于或等于第二个输入，则`false`返回，否则。 参数必须是字符串或两个数字。 |
| `['==', value, value]` | boolean | 如果`true`输入值相等，则返回，`false`否则。 参数必须是字符串或两个数字。 |
| `['>', value, value]` | boolean | 如果`true`第一个输入严格大于第二个输入，`false`则返回，否则。 参数必须是字符串或两个数字。 |
| `['>=' value, value]` | boolean | 如果`true`第一个输入大于或等于第二个输入，则`false`返回，否则。 参数必须是字符串或两个数字。 |
| `['all', boolean, boolean, …]` | boolean | 如果`true`所有输入都是`true`，则`false`返回，否则。 |
| `['any', boolean, boolean, …]` | boolean | 如果`true`任何输入为`true`，则返回`false`，否则。 |

## <a name="conditional-expressions"></a>条件表达式

条件表达式提供的逻辑操作类似于 if 语句。

以下表达式对输入数据执行条件逻辑操作。 例如，`case`表达式提供"if/then/else"逻辑，`match`而表达式类似于"开关语句"。 

### <a name="case-expression"></a>案例表达式

表达式`case`是提供"if/然后/else"逻辑的条件表达式的类型。 这种类型的表达式步步通过布尔条件列表。 它将第一个布尔条件的输出值返回以进行 true。

以下伪代码定义表达式的结构`case`。 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**示例**

下面的示例步步遍了不同的布尔条件，直到它找到一个计算到`true`的布尔条件，然后返回该关联值。 如果没有布尔条件计算到`true`，将返回回退值。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>匹配表达式

表达式`match`是提供开关语句（如逻辑）的条件表达式的类型。 输入可以是任何表达式，例如`['get', 'entityType']`返回字符串或数字的表达式。 每个标签必须是单个文本值或文本值数组，其值必须是所有字符串或所有数字。 如果数组中的任何值匹配，则输入匹配。 每个标签必须是唯一的。 如果输入类型与标签的类型不匹配，则结果将是回退值。

以下伪代码定义表达式的结构`match`。 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**示例**

下面的示例查看气泡图层中`entityType`搜索匹配项的 Point 要素的属性。 如果找到匹配项，则返回该指定值或返回回退值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

下面的示例使用数组列出一组标签，这些标签应全部返回相同的值。 此方法比单独列出每个标签更有效。 在这种情况下，`entityType`如果属性是"餐厅"或"grocery_store"，则返回颜色"红色"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

下面的示例使用匹配表达式执行"在数组"或"数组包含"类型筛选器。 在这种情况下，表达式筛选具有允许 ID 列表中的 ID 值的数据。 将表达式与筛选器一起使用时，结果需要为布尔值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>凝聚表达式

表达式`coalesce`步步遍一组表达式，直到获得第一个非空值并返回该值。 

以下伪代码定义表达式的结构`coalesce`。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**示例**

下面的示例使用`coalesce`表达式设置符号图层`textField`的选项。 如果`title`属性从要素中丢失或设置为`null`，则表达式将尝试查找`subtitle`该属性（如果缺少该属性或`null`，则该属性将回退回空字符串）。 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

下面的示例使用`coalesce`表达式从指定图像名称列表中检索地图子画面中可用的第一个可用图像图标。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>类型表达式

类型表达式提供了用于测试和转换不同数据类型（如字符串、数字和布尔值）的工具。

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | 数组\|对象 | 返回文本数组或对象值。 使用此表达式可防止将数组或对象计算为表达式。 当数组或对象需要由表达式返回时，这是必需的。 |
| `['image', string]` | 字符串 | 检查是否将指定的图像 ID 加载到地图图像精灵中。 如果是，则返回 ID，否则返回 null。 |
| `['to-boolean', value]` | boolean | 将输入值转换为布尔。 `false`结果是当输入为空字符串`0`时， 、 `false`、 `null`、 `NaN`否则其`true`。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 将输入值转换为颜色。 如果提供了多个值，则按顺序计算每个值，直到获得第一个成功转换。 如果无法转换任何输入，则表达式是错误。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | 数字 | 如果可能，将输入值转换为数字。 如果输入为`null`或`false`，则结果为 0。 如果输入为`true`，则结果为 1。 如果输入是字符串，则使用 ECMAScript 语言规范的[ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type)字符串函数将其转换为数字。 如果提供了多个值，则按顺序计算每个值，直到获得第一个成功转换。 如果无法转换任何输入，则表达式是错误。 |
| `['to-string', value]` | 字符串 | 将输入值转换为字符串。 如果输入为`null`，则结果为`""`。 如果输入是布尔，则结果是`"true"`或`"false"`。 如果输入是数字，则使用 ECMAScript 语言规范的[ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type)编号函数将其转换为字符串。 如果输入是一种颜色，则将其转换为 CSS RGBA 颜色字符串`"rgba(r,g,b,a)"`。 否则，使用 ECMAScript 语言规范的[JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify)函数将输入转换为字符串。 |
| `['typeof', value]` | 字符串 | 返回描述给定值类型的字符串。 |

> [!TIP]
> 如果浏览器控制台中出现类似于`Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`的错误消息，则意味着代码中有一个表达式，该表达式具有第一个值没有字符串的数组。 如果希望表达式返回数组，则用`literal`表达式换行数组。 下面的示例设置符号图层的`offset`图标选项，该图标必须是包含两个数字的数组，通过使用表达式根据点要素`match``entityType`的属性的值在两个偏移值之间进行选择。
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>颜色表达式

颜色表达式使创建和操作颜色值变得更加容易。

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | 从*红色*、*绿色*和*蓝色*分量创建颜色值，这些分量必须在`0``255`和 和 之间范围`1`，以及 的 alpha 分量。 如果任何组件的范围不大，则表达式是错误。 |
| `['rgba', number, number, number, number]` | color | 从*红色*、*绿色*、*蓝色*分量创建颜色值，这些分量`0`必须在`255`和 之间范围，并在`0`和`1`的范围内创建 alpha 分量。 如果任何组件的范围不大，则表达式是错误。 |
| `['to-rgba']` | \[数字、数字、数字、数字\] | 按该顺序返回包含输入颜色*的红色*、*绿色*、*蓝色*和*alpha*分量的四元素数组。 |

**示例**

下面的示例创建一个 RGB 颜色值，*red*该值的红色`255`值为 ，*绿色*和*蓝色*值通过乘以`2.5``temperature`属性的值来计算。 随着温度的变化，颜色将变为不同的*红色*色调。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>字符串运算符表达式

字符串运算符表达式对字符串执行转换操作，例如串联和转换大小写。 

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 字符串 | 将多个字符串串联在一起。 每个值都必须是一个字符串。 如果需要，`to-string`请使用类型表达式将其他值类型转换为字符串。 |
| `['downcase', string]` | 字符串 | 将指定的字符串转换为小写。 |
| `['upcase', string]` | 字符串 | 将指定的字符串转换为大写。 |

**示例**

下面的示例将`temperature`点要素的属性转换为字符串，然后将"+F"串联到它的末尾。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

上面的表达式在地图上渲染一个图钉，上面覆盖着文本"64°F"，如下图所示。

<center>

![字符串运算符表达式示例](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>插值和步进表达式

插值和步进表达式可用于计算沿插值曲线或步进函数的值。 这些表达式采用返回数值作为输入的表达式，例如`['get',  'temperature']`。 根据输入和输出值对计算输入值，以确定最适合插值曲线或步进函数的值。 输出值称为"停止"。 每个停靠站的输入值必须为数字，并且按升序排列。 输出值必须是数字、数字数组或颜色。

### <a name="interpolate-expression"></a>插值表达式

表达式`interpolate`可用于通过在停止值之间插值来计算连续、平滑的值集。 返回`interpolate`颜色值的表达式将生成颜色渐变，其中从中选择结果值。

有三种类型的插值方法可用于`interpolate`表达式：
 
* `['linear']`- 插值在两个停止之间线性进行。
* `['exponential', base]`- 插值在停靠点之间呈指数级。 该`base`值控制输出增加的速率。 值越高，输出会更多地向范围的高端增长。 接近`base`1 的值会产生线性增长较多的输出。
* `['cubic-bezier', x1, y1, x2, y2]`- 使用由给定控制点定义的[立方贝塞尔曲线](https://developer.mozilla.org/docs/Web/CSS/timing-function)进行插值。

下面是这些不同类型的插值示例。 

| 线性  | 指数 | 三次方贝塞尔 |
|---------|-------------|--------------|
| ![线性插值图](media/how-to-expressions/linear-interpolation.png) | ![指数插值图](media/how-to-expressions/exponential-interpolation.png) | ![立方贝塞尔插值图](media/how-to-expressions/bezier-curve-interpolation.png) |

以下伪代码定义表达式的结构`interpolate`。 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**示例**

下面的示例使用`linear interpolate`表达式根据点要素`color``temperature`的属性设置气泡图层的属性。 如果`temperature`值小于 60，则返回"蓝色"。 如果介于 60 和小于 70 之间，则黄色将返回。 如果介于 70 和小于 80 之间，则返回"橙色"。 如果为 80 或更高，则返回"红色"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

下图演示了如何为上述表达式选择颜色。
 
<center>

![插值表达式示例](media/how-to-expressions/interpolate-expression-example.png)</center>

### <a name="step-expression"></a>步骤表达式

表达式`step`可用于通过计算由停靠点定义的[分段常数函数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)来计算离散的阶梯式结果值。 

以下伪代码定义表达式的结构`step`。 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

步长表达式返回在输入值之前的止损的输出值，或者如果输入小于第一个停止，则返回第一个输入值。 

**示例**

下面的示例使用`step`表达式根据点要素`color``temperature`的属性设置气泡图层的属性。 如果`temperature`值小于 60，则返回"蓝色"。 如果介于 60 和小于 70 之间，则返回"黄色"。 如果介于 70 和小于 80 之间，则返回"橙色"。 如果为 80 或更高，则返回"红色"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

下图演示了如何为上述表达式选择颜色。
 
<center>

![步骤表达式示例](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>特定于图层的表达式

仅适用于特定图层的特殊表达式。

### <a name="heat-map-density-expression"></a>热图密度表达式

热图密度表达式检索热图图层中每个像素的热图密度值，并定义为`['heatmap-density']`。 此值是 和`0``1`之间的数字。 它与 或`interpolation``step`表达式结合使用，用于定义用于着色热图的颜色渐变。 此表达式只能在热图图层[的颜色选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color)中使用。

> [!TIP]
> 索引 0 处的颜色（插值表达式中）或单步颜色的默认颜色定义没有数据的区域的颜色。 索引 0 处的颜色可用于定义背景颜色。 很多用户喜欢将此值设置为透明或半透明黑色。

**示例**

本示例使用衬线插值表达式创建平滑的颜色渐变以渲染热图。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

除了使用平滑渐变对热图着色外，还可以使用`step`表达式在一组范围内指定颜色。 使用`step`表达式对热图进行着色，直观地将密度分解为类似于轮廓或雷达样式图的范围。  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

有关详细信息，请参阅[添加热图图层](map-add-heat-map-layer.md)文档。

### <a name="line-progress-expression"></a>行进度表达式

行进度表达式检索沿线图层中的渐变线的进度，并定义为`['line-progress']`。 此值是介于 0 和 1 之间的数字。 它与 或`interpolation``step`表达式结合使用。 此表达式只能与线图层的[描边渐变选项]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient)一起使用。 

> [!NOTE]
> 线`strokeGradient`图层的选项需要将数据源`lineMetrics`设置为`true`的选项。

**示例**

此示例使用`['line-progress']`表达式将颜色渐变应用于线条的描边。

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[查看实时示例](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文本字段格式表达式

文本字段格式表达式可与符号图层`textField``textOptions`属性的选项一起使用，以提供混合文本格式。 此表达式允许指定一组输入字符串和格式选项。 可以为此表达式中的每个输入字符串指定以下选项。

 * `'font-scale'`- 指定字体大小的缩放因子。 如果指定，此值将覆盖`size`各个字符串的属性。 `textOptions`
 * `'text-font'`- 指定应用于此字符串的一个或多个字体系列。 如果指定，此值将覆盖`font`各个字符串的属性。 `textOptions`
 * `'text-color'`- 指定在渲染时要应用于文本的颜色。 

以下伪代码定义文本字段格式表达式的结构。 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**示例**

下面的示例通过添加粗体字体并缩小该功能`title`属性的字体大小来设置文本字段的格式。 此示例还会在`subtitle`newline 上添加该功能的属性，其字体大小缩小为红色。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

此图层将呈现点特征，如下图所示：
 
<center>

![具有格式化文本字段](media/how-to-expressions/text-field-format-expression.png)的点要素图像</center>

### <a name="number-format-expression"></a>数字格式表达式

表达式`number-format`只能与符号图层`textField`的选项一起使用。 此表达式将提供的号码转换为格式化字符串。 此表达式包装 JavaScript 的[编号.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函数，并支持以下选项集。

 * `locale`- 指定此选项，用于以与指定语言对齐的方式将数字转换为字符串。 将[BCP 47 语言标记](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)传递到此选项。
 * `currency`- 将数字转换为表示货币的字符串。 可能的值是[ISO 4217 货币代码](https://en.wikipedia.org/wiki/ISO_4217)，如美元的"美元"、欧元的"欧元"或人民币的"CNY"。
 * `'min-fraction-digits'`- 指定要包含在数字字符串版本中的小数位数的最小数。
 * `'max-fraction-digits'`- 指定要包含在数字字符串版本中的十进制位的最大数数。

以下伪代码定义文本字段格式表达式的结构。 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**示例**

下面的示例使用`number-format`表达式修改点要素的属性在符号`revenue`图层`textField`选项中呈现的方式，使其显示为美元值。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

此图层将呈现点特征，如下图所示：

<center>

![数字格式表达式示例](media/how-to-expressions/number-format-expression.png)</center>

### <a name="image-expression"></a>图像表达式

图像表达式可与符号图层 和`image`多边`textField`形图层`fillPattern`的选项一起使用。 此表达式检查请求的图像是否存在样式，并将返回已解析的图像名称或`null`，具体取决于图像当前是否位于样式中。 此验证过程是同步的，要求在映像参数中请求映像之前将映像添加到样式中。

**示例**

下面的示例使用`image`表达式添加与符号图层中文本的内联图标。 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

此图层将在符号图层中渲染文本字段，如下图所示：

<center>

![图像表达式示例](media/how-to-expressions/image-expression.png)</center>

## <a name="zoom-expression"></a>缩放表达式

表达式`zoom`用于在渲染时检索地图的当前缩放级别，并定义为`['zoom']`。 此表达式在地图的最小缩放级别和最大缩放级别范围之间返回一个数字。 Azure 地图 Web 和 Android 的交互式地图控件支持 25 个缩放级别，编号为 0 到 24。 使用表达式`zoom`允许在更改地图的缩放级别时动态修改样式。 表达式`zoom`只能与 和`interpolate``step`表达式一起使用。

**示例**

默认情况下，热贴图图层中呈现的数据点的半径为所有缩放级别的固定像素半径。 放大地图时，数据聚合在一起，热图图层看起来不同。 表达式`zoom`可用于缩放每个缩放级别的半径，以便每个数据点覆盖地图的相同物理区域。 这将使热图图层看起来更静态且一致。 地图的每个缩放级别垂直和水平的像素数是上一个缩放级别的两倍。 缩放半径（使其与每个缩放级别加倍）将创建在所有缩放级别上看起来一致的热图。 可以使用具有`zoom``base 2 exponential interpolation`表达式的表达式完成，为最小缩放级别设置像素半径，并针对最大缩放级别的缩放半径（如下所示）`2 * Math.pow(2, minZoom - maxZoom)`计算。

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[查看实时示例](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>变量绑定表达式

变量绑定表达式将计算结果存储在变量中。 因此，计算结果可以在表达式的其他地方多次引用。 对于涉及许多计算的表达式，它是一种有用的优化。

| 表达式 | 返回类型 | 说明 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"让我们"，<br/>&nbsp;&nbsp;&nbsp;&nbsp;名称1：字符串，<br/>&nbsp;&nbsp;&nbsp;&nbsp;值1：任何，<br/>&nbsp;&nbsp;&nbsp;&nbsp;名称2：字符串，<br/>&nbsp;&nbsp;&nbsp;&nbsp;值2：任何，<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;子表达式<br/>\] | | 将一个或多个值存储为变量，`var`供返回结果的子表达式中的表达式使用。 |
| `['var', name: string]` | any | 引用使用`let`表达式创建的变量。 |

**示例**

此示例使用一个表达式，该表达式计算收入相对于温度比，然后使用`case`表达式计算此值上的不同布尔操作。 该`let`表达式用于存储相对于温度比的收入，因此只需计算一次。 表达式`var`根据需要经常引用此变量，而无需重新计算它。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>后续步骤

有关实现表达式的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"] 
> [添加符号图层](map-add-pin.md)

> [!div class="nextstepaction"] 
> [添加气泡图层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"] 
> [添加热度地图层](map-add-heat-map-layer.md)

详细了解支持表达式的图层选项：

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [线图层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [多边形图层选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
