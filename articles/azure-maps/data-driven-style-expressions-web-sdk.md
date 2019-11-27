---
title: Azure Maps Web SDK 中的数据驱动样式表达式 |Microsoft Docs
description: 如何在 Azure Maps Web SDK 中使用数据驱动样式表达式。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 6cd69ba8abe243daadf5d517ab7c5a224953cc99
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480636"
---
# <a name="data-driven-style-expressions-web-sdk"></a>数据驱动样式表达式（Web SDK）

使用表达式可以将业务逻辑应用到样式选项，这些选项可观察数据源中每个形状中定义的属性。 表达式还可用于筛选数据源或层中的数据。 表达式可以包含条件逻辑（如 if 语句），并且还可用于操作的数据;string、logical 和数学运算符。 

数据驱动的样式可减少在样式设置之间实现业务逻辑所需的代码量。 与层一起使用时，表达式在呈现时在单独的线程上进行计算，在与在 UI 线程上评估业务逻辑相比，此线程可提供更高的性能。

以下视频概述了 Azure Maps Web SDK 中的数据驱动样式。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

表达式表示为 JSON 数组。 数组中表达式的第一个元素是一个字符串，该字符串指定表达式运算符的名称。 例如，"+" 或 "case"。 接下来的元素（如果有）是表达式的参数。 每个参数都是一个文本值（字符串、数字、布尔值或 `null`）或另一个表达式数组。 以下伪代码定义表达式的基本结构。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK 支持多种类型，这些类型可在其自身或与其他表达式结合使用。

| 表达式的类型 | 说明 |
|---------------------|-------------|
| [聚合表达式](#aggregate-expression) | 定义计算的表达式，该计算通过一组数据进行处理，并可与 `DataSource`的 `clusterProperties` 选项一起使用。 |
| [布尔表达式](#boolean-expressions) | 布尔表达式提供一组用于计算布尔值比较的布尔运算符表达式。 |
| [颜色表达式](#color-expressions) | 颜色表达式使您可以更轻松地创建和操作颜色值。 |
| [条件表达式](#conditional-expressions) | 条件表达式提供类似于 if 语句的逻辑运算。 |
| [数据表达式](#data-expressions) | 提供对功能中的属性数据的访问。 |
| [内插和单步表达式](#interpolate-and-step-expressions) | 内插和单步表达式可用于沿内插曲线或单步执行函数计算值。 |
| [层特定的表达式](#layer-specific-expressions) | 仅适用于单个层的特殊表达式。 |
| [数学表达式](#math-expressions) | 提供数学运算符，用于在 expression framework 内执行数据驱动的计算。 |
| [字符串运算符表达式](#string-operator-expressions) | 字符串运算符表达式对字符串执行转换操作，例如连接和转换大小写。 |
| [类型表达式](#type-expressions) | 类型表达式提供了用于测试和转换不同数据类型（如字符串、数字和布尔值）的工具。 |
| [变量绑定表达式](#variable-binding-expressions) | 可变绑定表达式使计算结果存储在变量中，并多次在表达式中的其他位置引用，而无需重新计算存储的值。 |
| [Zoom 表达式](#zoom-expression) | 在呈现时检索地图的当前缩放级别。 |

本文档中的所有示例都将使用以下功能演示不同类型的表达式的不同使用方式。 

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

数据表达式提供对功能中的属性数据的访问。 

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['at', number, array]` | 对象 | 从数组中检索项。 |
| `['geometry-type']` | 字符串 | 获取功能的几何图形类型： Point、MultiPoint、LineString、MultiLineString、多边形、MultiPolygon。 |
| `['get', string]` | 值 | 从当前功能的属性获取属性值。 如果缺少请求的属性，则返回 null。 |
| `['get', string, object]` | 值 | 从提供的对象的属性获取属性值。 如果缺少请求的属性，则返回 null。 |
| `['has', string]` | boolean | 确定功能的属性是否具有指定的属性。 |
| `['has', string, object]` | boolean | 确定对象的属性是否具有指定的属性。 |
| `['id']` | 值 | 获取功能的 ID （如果有）。 |
| `['length', string | array]` | number | 获取字符串或数组的长度。 |

**示例**

通过使用 `get` 表达式，可以在表达式中直接访问功能的属性。 下面的示例使用功能的 "zoneColor" 值指定气泡图层的颜色属性。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

如果所有点特征都具有 `zoneColor` 属性，则上述示例将正常运行，但如果不存在，则颜色可能会回退到 "黑色"。 若要修改回退颜色，`case` 表达式可与 `has` 表达式结合使用来检查属性是否存在，如果不返回回退颜色，则为。

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

默认情况下，气泡图和符号层将呈现数据源中所有形状的坐标。 这样可以突出显示多边形或线条的顶点。 该层的 `filter` 选项可用于限制通过使用布尔表达式中的 `['geometry-type']` 表达式呈现的功能的几何类型。 下面的示例将限制气泡图层，以便仅呈现 `Point` 的功能。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

下面的示例将允许呈现 `Point` 和 `MultiPoint` 功能。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同样，多边形的轮廓将以线条层呈现。 若要在线条层中禁用此行为，请添加仅允许 `LineString` 和 `MultiLineString` 功能的筛选器。  

## <a name="math-expressions"></a>数学表达式

数学表达式提供数学运算符，用于在 expression framework 内执行数据驱动的计算。

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 计算指定数字的和。 |
| `['-', number]` | number | 将0减去指定数字。 |
| `['-', number, number]` | number | 用第二个数字减去第一个数字。 |
| `['*', number, number, …]` | number | 将指定的数字相乘。 |
| `['/', number, number]` | number | 将第一个数字除以第二个数字。 |
| `['%', number, number]` | number | 计算第一个数字除以第二个数字时的余数。 |
| `['^', number, number]` | number | 计算第一个值的值，该值为第二个数字的幂。 |
| `['abs', number]` | number | 计算指定数字的绝对值。 |
| `['acos', number]` | number | 计算指定数字的反余弦值。 |
| `['asin', number]` | number | 计算指定数字的反正弦值。 |
| `['atan', number]` | number | 计算指定数字的反正切值。 |
| `['ceil', number]` | number | 将数字向上舍入到下一个整数。 |
| `['cos', number]` | number | 计算指定数字的 cos。 |
| `['e']` | number | 返回数学常量 `e`。 |
| `['floor', number]` | number | 将数字向下舍入到上一个整数。 |
| `['ln', number]` | number | 计算指定数字的自然对数。 |
| `['ln2']` | number | 返回数学常量 `ln(2)`。 |
| `['log10', number]` | number | 计算指定数字的以10为底的对数。 |
| `['log2', number]` | number | 计算指定数字的以2为底的对数。 |
| `['max', number, number, …]` | number | 计算指定的一组数字中的最大数目。 |
| `['min', number, number, …]` | number | 计算指定的一组数字中的最小数目。 |
| `['pi']` | number | 返回数学常量 `PI`。 |
| `['round', number]` | number | 将数字舍入到最接近的整数。 将值从零向外舍入。 例如，`['round', -1.5]` 的计算结果为-2。 |
| `['sin', number]` | number | 计算指定数字的正弦值。 |
| `['sqrt', number]` | number | 计算指定数字的平方根。 |
| `['tan', number]` | number | 计算指定数值的正切值。 |

## <a name="aggregate-expression"></a>聚合表达式

聚合表达式定义对一组数据进行处理并可与 `DataSource`的 `clusterProperties` 选项一起使用的计算。 这些表达式的输出必须是数字或布尔值。 

聚合表达式采用三个值;运算符值和初始值，以及用于从数据中的每个功能检索属性以便对其应用聚合运算的表达式。 此表达式的格式如下：

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 运算符：然后应用于每个 `mapExpression` 为分类中的每个点计算的所有值的表达式函数。 支持的运算符; 
    - 对于数字： `+`、`*`、`max``min`
    - 对于布尔值： `all`，`any`
- initialValue：用于聚合第一个计算值的初始值。
- mapExpression：应用于数据集中的每个点的表达式。

**示例**

如果数据集中的所有功能都有一个 `revenue` 属性，该属性是一个数字。 可以使用以下聚合表达式来计算从数据集中创建的群集中所有点的总收入： `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>布尔表达式

布尔表达式提供一组用于计算布尔值比较的布尔运算符表达式。

比较值时，将严格类型化比较。 不同类型的值始终被视为不相等。 在分析时已知不同类型的情况被视为无效，并将生成分析错误。 

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | 逻辑求反。 如果输入 `false`，则返回 `true`; 如果 `true`，则返回 `false`。 |
| `['!= ', value, value]` | boolean | 返回 `true` 如果输入值不相等，则返回 `false` 否则为。 |
| `['<', value, value]` | boolean | 如果第一个输入严格小于第二个输入 `false`，则返回 `true`; 否则返回。 参数需要同时为字符串和/或数字。 |
| `['<=', value, value]` | boolean | 如果第一个输入小于等于第二个输入 `false`，则返回 `true`; 否则返回。 参数需要同时为字符串和/或数字。 |
| `['==', value, value]` | boolean | 返回 `true` 如果输入值相等，则 `false` 否则返回。 参数需要同时为字符串和/或数字。 |
| `['>', value, value]` | boolean | 如果第一个输入严格大于第二个输入，则返回 `true`; 否则，`false`。 参数需要同时为字符串和/或数字。 |
| `['>=' value, value]` | boolean | 返回 `true` 如果第一个输入大于或等于第二个输入，则 `false` 否则返回。 参数需要同时为字符串和/或数字。 |
| `['all', boolean, boolean, …]` | boolean | 如果所有输入均 `true`，则返回 `true` `false` 否则返回。 |
| `['any', boolean, boolean, …]` | boolean | 如果 `true`任何输入，则返回 `true`; 否则返回 `false`。 |

## <a name="conditional-expressions"></a>条件表达式

条件表达式提供类似于 if 语句的逻辑运算。

以下表达式对输入数据执行条件逻辑操作。 例如，`case` 表达式提供 "if/then/else" 逻辑，而 `match` 表达式与 "switch 语句" 类似。 

### <a name="case-expression"></a>Case 表达式

`case` 表达式是一种提供 if 语句（if/then/else）的条件表达式。 这种类型的表达式逐句通过一个布尔条件列表，并返回第一个 true 布尔条件的输出值。

以下伪代码定义 `case` 表达式的结构。 

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

下面的示例单步执行不同的布尔条件，直到找到计算结果为 `true`的值，然后返回该关联值。 如果没有布尔条件计算结果为 `true`，将返回回退值。 

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

### <a name="match-expression"></a>Match 表达式

`match` 表达式是一种提供类似于逻辑的 switch 语句的条件表达式。 输入可以是任何表达式，如 `['get', 'entityType']` 返回字符串或数字。 每个标签都必须是单个文本值或文本值的数组，其值必须是所有字符串或所有数字。 如果数组中的任何值匹配，则输入匹配。 每个标签都必须是唯一的。 如果输入类型与标签类型不匹配，则结果将为回退值。

以下伪代码定义 `match` 表达式的结构。 

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

下面的示例将查看气泡图层中某个点功能的 `entityType` 属性，搜索匹配项。 如果找到匹配项，则返回指定的值或返回回退值。

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

下面的示例使用数组列出一组应返回相同值的标签。 这比单独列出每个标签要高效得多。 在这种情况下，如果 `entityType` 属性为 "餐馆" 或 "grocery_store"，则将返回颜色 "red"。

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

下面的示例使用 match 表达式来执行 "in array" 或 "array contains" 类型筛选器，在这种情况下，筛选 ID 值在允许 Id 列表中的数据。 使用带有筛选器的表达式时，结果必须是布尔值。

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

### <a name="coalesce-expression"></a>合并表达式

`coalesce` 表达式逐句通过一组表达式，直到获取第一个非 null 值并返回该值。 

以下伪代码定义 `coalesce` 表达式的结构。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**示例**

下面的示例使用 `coalesce` 表达式来设置符号层的 `textField` 选项。 如果特性缺少 `title` 属性或将其设置为 `null`，则表达式将尝试查找 `subtitle` 属性（如果缺少或 `null`，它将回退到空字符串）。 

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

下面的示例使用 `coalesce` 表达式从指定的图像名称列表中检索 "映射" 子画面中可用的第一个可用图像图标。

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
| `['literal', array]`<br/><br/>`['literal', object]` | 数组 \| 对象 | 返回文本数组或对象值。 使用此表达式可防止将数组或对象作为表达式进行计算。 当表达式需要返回数组或对象时，这是必需的。 |
| `['image', string]` | 字符串 | 检查是否已将指定的映像 ID 加载到 maps 图像 sprite。 如果为，则返回 ID，否则返回 null。 |
| `['to-boolean', value]` | boolean | 将输入值转换为布尔值。 如果输入为空字符串、`0`、`false`、`null`或 `NaN`，则结果 `false`;否则，其 `true`。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 将输入值转换为颜色。 如果提供了多个值，则将按顺序对每个值进行计算，直到获取第一个成功的转换。 如果没有任何输入可转换，则表达式为错误。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 如果可能，将输入值转换为数字。 如果输入是 `null` 或 `false`，则结果为0。 如果输入 `true`，则结果为1。 如果输入是字符串，则使用 ECMAScript 语言规范的[ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type)字符串函数将其转换为数字。 如果提供了多个值，则将按顺序对每个值进行计算，直到获取第一个成功的转换。 如果没有任何输入可转换，则表达式为错误。 |
| `['to-string', value]` | 字符串 | 将输入值转换为字符串。 如果输入 `null`，则结果为 `""`。 如果输入为布尔值，则结果为 `"true"` 或 `"false"`。 如果输入是一个数字，则使用 ECMAScript 语言规范的[ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) number 函数将其转换为字符串。 如果输入是一种颜色，则会将其转换为 CSS RGBA 颜色字符串 `"rgba(r,g,b,a)"`。 否则，使用 ECMAScript 语言规范的[json.stringify](https://tc39.github.io/ecma262/#sec-json.stringify)函数将输入转换为字符串。 |
| `['typeof', value]` | 字符串 | 返回一个字符串，该字符串描述给定值的类型。 |

> [!TIP]
> 如果浏览器控制台中出现类似于 `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` 的错误消息，则表示代码中的某个位置有一个数组，该表达式的第一个值没有字符串。 如果希望表达式返回数组，请使用 `literal` 表达式包装数组。 下面的示例通过使用 `match` 表达式根据点功能的 `entityType` 属性的值在两个偏移值之间进行选择，将符号层的图标 `offset` 选项设置为一个包含两个数字的数组。
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

颜色表达式使您可以更轻松地创建和操作颜色值。

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | 从*红色*、*绿色*和*蓝色*分量创建颜色值，该颜色值必须介于 `0` 和 `255`之间，并且是 `1`的 alpha 分量。 如果任何组件超出范围，则表达式为错误。 |
| `['rgba', number, number, number, number]` | color | 根据必须介于 `0` 和 `255`之间的*红色*、*绿色*和*蓝色*分量创建颜色值，并从 `0` 和 `1`范围内的 alpha 分量创建一个颜色值。 如果任何组件超出范围，则表达式为错误。 |
| `['to-rgba']` | \[number、number、number、number\] | 返回一个由四个元素组成的数组，其中包含输入颜色的*红色*、*绿色*、*蓝色*和*alpha*分量，按顺序排列。 |

**示例**

下面的示例创建和 RGB 颜色值，其*红色*值为 `255`，*绿色*和*蓝色*值由 `temperature` 属性的值相乘 `2.5` 计算得出。 当温度改变时，颜色将变为不同的*红色*阴影。

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

字符串运算符表达式对字符串执行转换操作，例如连接和转换大小写。 

| 表达式 | 返回类型 | 说明 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 字符串 | 将多个字符串连接在一起。 每个值必须是字符串。 如果需要，请使用 `to-string` 类型表达式将其他值类型转换为字符串。 |
| `['downcase', string]` | 字符串 | 将指定的字符串转换为小写。 |
| `['upcase', string]` | 字符串 | 将指定的字符串转换为大写。 |

**示例**

下面的示例将 point 功能的 `temperature` 属性转换为字符串，然后将 "° F" 连接到该字符串的末尾。

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

上面的表达式在地图上呈现文本 "64 ° F" 的 pin，如下图所示。

<center>

![字符串运算符表达式示例](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>内插和单步表达式

内插和单步表达式可用于沿内插曲线或单步执行函数计算值。 这些表达式采用返回数值作为输入的表达式，例如 `['get',  'temperature']`。 根据输入和输出值对（称为 "停止"）对输入值进行求值，以确定最适合于内插曲线或单步执行函数的值。 每个停止的输入值必须是数字，并且必须是升序。 输出值必须是数字、数字数组或颜色。

### <a name="interpolate-expression"></a>内插表达式

`interpolate` 表达式可用于通过在 stop 值之间进行插值计算来计算连续平滑的值集。 返回颜色值的 `interpolate` 表达式将产生从中选择结果值的颜色渐变。

`interpolate` 表达式中可以使用三种类型的内插方法：
 
* `['linear']`-在一对停止之间以线性方式内插。
* `['exponential', base]` 在停止之间以指数方式内插。 `base` 值控制输出的增加速度。 值越大，输出就会增加到范围的高端。 接近1的 `base` 值将生成输出，该输出将增加线性增长。
* 使用由给定控制点定义的[三次方贝塞尔曲线](https://developer.mozilla.org/docs/Web/CSS/timing-function)`['cubic-bezier', x1, y1, x2, y2]`。

下面是这些不同类型的内插的示例。 

| 线性  | 指数 | 三次方贝塞尔 |
|---------|-------------|--------------|
| ![线性内插图](media/how-to-expressions/linear-interpolation.png) | ![指数内插关系图](media/how-to-expressions/exponential-interpolation.png) | ![三次方贝塞尔插值图](media/how-to-expressions/bezier-curve-interpolation.png) |

以下伪代码定义 `interpolate` 表达式的结构。 

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

下面的示例使用 `linear interpolate` 表达式基于点功能的 `temperature` 属性设置气泡图层的 `color` 属性。 如果 `temperature` 值小于60，则将返回 "blue"，如果介于60和低于70之间，则将返回黄色，如果在70和低于80之间，则将返回 "橙色"，如果为80或更高，则返回 "red"。

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

下图演示了如何为上面的表达式选择颜色。
 
<center>

![的内插表达式示例](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>步骤表达式

`step` 表达式可用于通过计算由停止定义的[分段函数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)来计算离散的阶数结果值。 

以下伪代码定义 `step` 表达式的结构。 

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

步骤表达式返回的输出值刚好早于输入值，或第一个输入值（如果输入小于第一次停止）。 

**示例**

下面的示例使用 `step` 表达式基于点功能的 `temperature` 属性设置气泡图层的 `color` 属性。 如果 `temperature` 值小于60，则将返回 "blue"，如果介于60和低于70之间，则将返回 "黄色"，如果在70和低于80之间，将返回 "橙色"，如果80或更高，则返回 "red"。

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

下图演示了如何为上面的表达式选择颜色。
 
<center>

![步骤表达式示例](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>层特定的表达式

仅适用于特定层的特殊表达式。

### <a name="heat-map-density-expression"></a>热度地图密度表达式

热度地图密度表达式检索热度地图层中每个像素的热度地图密度值，并将其定义为 `['heatmap-density']`。 此值是 `0` 和 `1` 之间的一个数字，并与 `interpolation` 或 `step` 表达式结合使用来定义用于着色热度地图的颜色渐变。 此表达式只能用于热度地图层的 "[颜色" 选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color)。

> [!TIP]
> 内插表达式中索引0处的颜色或步骤颜色的默认颜色，定义了没有数据并可用于定义背景色的区域的颜色。 很多用户喜欢将此值设置为透明或半透明黑色。 

**示例**

此示例使用 "内插内插" 表达式为呈现热度地图创建平滑颜色渐变。 

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

除了使用平滑渐变来着色热度地图外，还可以使用 `step` 表达式在一组范围内指定颜色。 使用用于着色的 `step` 表达式，热度地图会将密度直观地分解为多个范围，更类似于等高线或雷达样式地图。  

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

有关详细信息，请参阅[添加热度地图层](map-add-heat-map-layer.md)文档。

### <a name="line-progress-expression"></a>行进度表达式

行进度表达式按直线层中的渐变线检索进度，并将其定义为 `['line-progress']`。 此值是一个介于0到1之间的数字，与 `interpolation` 或 `step` 表达式结合使用。 此表达式只能与线条层的[strokeGradient 选项]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient)一起使用。 

> [!NOTE]
> 线条层的 `strokeGradient` 选项要求将数据源的 `lineMetrics` 选项设置为 "`true`"。

**示例**

下面的示例使用 `['line-progress']` 表达式将颜色渐变应用于线条的笔画。

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

文本字段格式表达式可以与符号层 `textOptions` 属性的 `textField` 选项一起使用，以提供混合文本格式。 此表达式允许指定一组输入字符串和格式设置选项。 可以为此表达式中的每个输入字符串指定以下选项。

 * `'font-scale'`-指定字体大小的缩放系数。 如果指定，此值将覆盖单个字符串的 `textOptions` 的 `size` 属性。
 * `'text-font'`-指定应对此字符串使用的一个或多个字体系列。 如果指定，此值将覆盖单个字符串的 `textOptions` 的 `font` 属性。
 * `'text-color'`-指定呈现时要应用于文本的颜色。 

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

下面的示例通过添加粗体字体并放大功能的 `title` 属性的字号来设置文本字段的格式。 此示例还在新行上添加了该功能的 `subtitle` 属性，并缩小了字体大小和红色。

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

此层将呈现点功能，如下图所示：
 
<center>

带格式的文本字段的 ![点功能图像](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>数字格式表达式

`number-format` 表达式只能与符号层的 `textField` 选项一起使用。 此表达式将提供的数字转换为带格式的字符串。 此表达式包装 JavaScript 的[toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函数，并支持以下选项集。

 * `locale`-指定此选项，以便将数字转换为字符串，使其与指定语言对齐。 将[BCP 47 语言标记](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)传递到此选项。
 * `currency`-将数字转换为表示货币的字符串。 可能的值为[ISO 4217 币种代码](https://en.wikipedia.org/wiki/ISO_4217)，如美元的美元 "USD"、欧元的 "EUR" 或中文 RMB 的 "CNY"。
 * `'min-fraction-digits'`-指定要包含在数字的字符串版本中的最小小数位数。
 * `'max-fraction-digits'`-指定要包含在数字字符串版本中的小数位数的最大位数。

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

下面的示例使用 `number-format` 表达式来修改 "点" 功能的 `revenue` 属性在符号层的 `textField` 选项中的呈现方式，使其显示为美元值。

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

此层将呈现点功能，如下图所示：

<center>

![数字格式表达式示例](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>Zoom 表达式

`zoom` 表达式用于检索在呈现时的地图的当前缩放级别，并定义为 `['zoom']`。 此表达式在地图的最小和最大缩放级别范围之间返回一个数字。 使用此表达式可在更改地图的缩放级别时动态修改样式。 `zoom` 表达式只能与 `interpolate` 和 `step` 表达式一起使用。

**示例**

默认情况下，热度地图层中呈现的数据点的半径对于所有缩放级别都具有固定像素半径。 当地图放大数据聚合时，热度地图层看起来会有所不同。 `zoom` 表达式可用于缩放每个缩放级别的半径，使每个数据点都涵盖地图的同一物理区域。 这会使热度地图层的外观更具静态和一致性。 地图的每个缩放级别都具有两倍于与上一个缩放级别垂直和水平的像素。 缩放半径以使每个缩放级别翻倍，将创建一个在所有缩放级别上都保持一致的热度地图。 这可以通过将 `zoom` 表达式与 `base 2 exponential interpolation` 表达式结合使用来完成，如下所示。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[查看实时示例](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>变量绑定表达式

变量绑定表达式将计算结果存储在变量中，以便可以多次引用表达式中的其他位置，而无需重新计算该值。 这是涉及许多计算的表达式的有用优化

| 表达式 | 返回类型 | 说明 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"let"，<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1： string，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1： any，<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2： string，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2： any、<br/>&nbsp;&nbsp;&nbsp;&nbsp;。<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 将一个或多个值作为变量存储，以供返回结果的子表达式中的 `var` 表达式使用。 |
| `['var', name: string]` | 任意 | 引用使用 `let` 表达式创建的变量。 |

**示例**

此示例使用一个表达式，该表达式相对于温度比计算收入，然后使用 `case` 表达式来计算对该值的不同布尔运算。 `let` 表达式用于存储相对于温度比率的收入，因此它只需计算一次，并且 `var` 表达式会根据需要经常引用此变量，而不必重新计算该值。

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
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"] 
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"] 
> [添加热度地图层](map-add-heat-map-layer.md)

详细了解支持表达式的层选项：

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
