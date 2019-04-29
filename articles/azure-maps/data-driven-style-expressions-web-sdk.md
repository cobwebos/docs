---
title: 数据驱动样式表达式中 Azure Maps Web SDK |Microsoft Docs
description: 如何在 Azure Maps Web SDK 中使用数据驱动样式表达式。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60904885"
---
# <a name="data-driven-style-expressions-web-sdk"></a>数据驱动样式表达式 (Web SDK)

表达式可以将业务逻辑应用样式选项，以观察数据源中的每个形状中定义的属性。 表达式还可以使用数据源或图层中的数据进行筛选。 表达式可以包含的条件逻辑，如 if 语句和还可用于处理数据;字符串，逻辑运算符和数学运算符。 

数据驱动样式可以减少实现业务逻辑，围绕样式设置所需的代码量。 当用于层，在提供更高的性能相比评估在 UI 线程上的业务逻辑时在单独线程上的呈现时间进行计算表达式。

以下视频概述了 Azure Maps Web SDK 中的数据驱动样式设置。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

表达式表示为 JSON 数组。 数组中的第一个元素是表达式的一个字符串，指定的表达式运算符的名称。 例如，"+"或"用例"。 接下来的元素 （如果有） 是为表达式的参数。 每个自变量是文本值 (字符串、 数字、 布尔值，或`null`)，或另一个表达式数组。 下面的伪代码定义的基本结构的表达式。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps Web SDK 支持许多类型的表达式，可自行或与其他表达式结合使用。

| 表达式的类型 | 描述 |
|---------------------|-------------|
| [布尔表达式](#boolean-expressions) | 布尔表达式提供了一组布尔运算符表达式用于计算布尔比较。 |
| [颜色表达式](#color-expressions) | 颜色表达式更加轻松地创建和操作颜色值。 |
| [条件表达式](#conditional-expressions) | 条件表达式提供了类似于 if 语句的逻辑操作。 |
| [数据表达式](#data-expressions) | 提供了一项功能中的属性数据的访问权限。 |
| [内插并单步表达式](#interpolate-and-step-expressions) | 执行内插和步长表达式可用于计算值的内插的曲线或步骤函数。 |
| [层特定表达式](#layer-specific-expressions) | 都只适用于单个层的特殊表达式。 |
| [数学表达式](#math-expressions) | 提供了数学运算符来执行数据驱动的计算表达式框架中。 |
| [字符串运算符表达式](#string-operator-expressions) | 字符串运算符表达式执行转换操作，如串联和转换大小写的字符串。 |
| [类型表达式](#type-expressions) | 类型表达式提供用于测试和转换不同的数据类型，如字符串、 数字和布尔值的工具。 |
| [变量绑定表达式](#variable-binding-expressions) | 变量绑定表达式让存储在变量中的计算结果和其他位置引用的表达式中多次而无需重新计算存储的值。 |
| [缩放表达式](#zoom-expression) | 检索在呈现时地图的当前缩放级别。 |

本文档中的所有示例将都使用以下功能来演示不同的方式，可以使用不同类型的表达式。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
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

数据表达式提供一项功能中的属性数据的访问权限。 

| 表达式 | 返回类型 | 描述 |
|------------|-------------|-------------|
| `['at', number, array]` | 对象 | 从数组中检索项。 |
| `['geometry-type']` | string | 获取该功能的 geometry 类型：多点、 LineString、 MultiLineString、 Polygon、 MultiPolygon 点。 |
| `['get', string]` | value | 从当前功能的属性获取的属性值。 如果缺少请求的属性，则，返回 null。 |
| `['get', string, object]` | value | 获取与所提供的对象的属性的属性值。 如果缺少请求的属性，则，返回 null。 |
| `['has', string]` | boolean | 确定是否一项功能的属性具有指定的属性。 |
| `['has', string, object]` | boolean | 确定是否该对象的属性具有指定的属性。 |
| `['id']` | value | 如果有，则获取该功能的 ID。 |
| `['length', string | array]` | 数字 | 获取字符串或数组的长度。 |

**示例**

可以通过在表达式中直接访问属性的一项功能`get`表达式。 以下示例使用功能"zoneColor"的值来指定气泡图层的颜色属性。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

如果所有点功能，则上面的示例将正常工作`zoneColor`属性，但如果不提供，颜色将很可能会返回"black"。 若要修改的回退颜色`case`可以在与组合中使用表达式`has`表达式来检查是否存在该属性，并它不会改为返回回退的颜色。

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

默认情况下，气泡图和符号的层将呈现的数据源中的所有形状的坐标。 完成此操作以突出显示多边形或行的顶点。 `filter`层的选项可用于限制其使用的呈现的功能的 geometry 类型`['geometry-type']`布尔表达式中的表达式。 以下示例将限制气泡图层以便仅`Point`呈现功能。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

下面的示例将允许同时`Point`和`MultiPoint`要呈现的功能。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同样，轮廓的多边形将呈现线条层中。 若要禁用此行为在线条层中的，添加筛选器，只允许`LineString`和`MultiLineString`功能。  

## <a name="math-expressions"></a>数学表达式

数学表达式提供了数学运算符来执行数据驱动的计算表达式框架中。

| 表达式 | 返回类型 | 描述 |
|------------|-------------|-------------|
| `['+', number, number, …]` | 数字 | 计算指定数字的总和。 |
| `['-', number]` | 数字 | 中减去指定的数字 0。 |
| `['-', number, number]` | 数字 | 第一个数字的第二个数字相减。 |
| `['*', number, number, …]` | 数字 | 将指定的数字一起相乘。 |
| `['/', number, number]` | 数字 | 将第二个数字的第一个数值。 |
| `['%', number, number]` | 数字 | 除第一个数字，通过第二个数字时计算的余数。 |
| `['^', number, number]` | 数字 | 计算的第一个值，第二个数字的幂值。 |
| `['abs', number]` | 数字 | 计算指定数字的绝对值。 |
| `['acos', number]` | 数字 | 计算指定数字的反余弦值。 |
| `['asin', number]` | 数字 | 计算指定数字的反正弦值。 |
| `['atan', number]` | 数字 | 计算指定数字的反正切值。 |
| `['ceil', number]` | 数字 | 将舍入到下一步的整数数目。 |
| `['cos', number]` | 数字 | 计算指定数字的余弦值。 |
| `['e']` | 数字 | 返回数学常量`e`。 |
| `['floor', number]` | 数字 | 数值舍入到上一个整数。 |
| `['ln', number]` | 数字 | 计算指定数字的自然对数。 |
| `['ln2']` | 数字 | 返回数学常量`ln(2)`。 |
| `['log10', number]` | 数字 | 计算指定数字的 base 10 对数。 |
| `['log2', number]` | 数字 | 计算指定数目的两个基本的对数。 |
| `['max', number, number, …]` | 数字 | 计算指定的一组数值的最大数目。 |
| `['min', number, number, …]` | 数字 | 计算指定的一组数值的最小数目。 |
| `['pi']` | 数字 | 返回数学常量`PI`。 |
| `['round', number]` | 数字 | 舍入为最接近的整数。 中间值远离零方向舍入。 例如，`['round', -1.5]`的计算结果为-2。 |
| `['sin', number]` | 数字 | 计算指定数字的正弦值。 |
| `['sqrt', number]` | 数字 | 计算指定数字的平方根。 |
| `['tan', number]` | 数字 | 计算指定数字的正切值。 |
## <a name="boolean-expressions"></a>布尔表达式

布尔表达式提供了一组布尔运算符表达式用于计算布尔比较。

在比较值时，比较进行严格类型化。 不同类型的值始终被视为不相等。 了解类型要在分析时不同情况下将被视为无效，将产生分析错误。 

| 表达式 | 返回类型 | 描述 |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | 逻辑求反运算。 返回`true`如果输入是`false`，并`false`如果输入是`true`。 |
| `['!= ', value, value]` | boolean | 返回`true`输入的值是否不相等，如果`false`否则为。 |
| `['<', value, value]` | boolean | 返回`true`如果第一个输入是严格小于第二个，`false`否则为。 需要为同时字符串或这两个数字参数。 |
| `['<=', value, value]` | boolean | 返回`true`的第一个输入是否小于或等于第二个，`false`否则为。 需要为同时字符串或这两个数字参数。 |
| `['==', value, value]` | boolean | 返回`true`输入的值是否相等，如果`false`否则为。 需要为同时字符串或这两个数字参数。 |
| `['>', value, value]` | boolean | 返回`true`严格大于第二个，第一个输入是否`false`否则为。 需要为同时字符串或这两个数字参数。 |
| `['>=' value, value]` | boolean | 返回`true`第一个输入是大于或等于第二，如果`false`否则为。 需要为同时字符串或这两个数字参数。 |
| `['all', boolean, boolean, …]` | boolean | 返回`true`如果所有输入都是否`true`，`false`否则为。 |
| `['any', boolean, boolean, …]` | boolean | 返回`true`如果任何输入都`true`，`false`否则为。 |

## <a name="conditional-expressions"></a>条件表达式

条件表达式提供了类似于 if 语句的逻辑操作。

以下表达式执行条件逻辑操作的输入数据。 例如，`case`表达式提供时的"if/then/else"逻辑`match`表达式就像"开关-语句"。 

### <a name="case-expression"></a>Case 表达式

一个`case`表达式是提供 if 语句等逻辑 (if/then/else) 的条件表达式的类型。 此类型的表达式包含布尔条件的列表的步骤，并返回第一个为 true 的布尔条件的输出值。

下面的伪代码定义的结构`case`表达式。 

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

下面的示例步骤通过不同的布尔条件，直到它找到一个计算结果为`true`，，然后返回该关联值。 如果任何布尔条件的计算不结果为`true`，回退值将返回。 

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

一个`match`表达式是提供了类似逻辑的 switch 语句的条件表达式的类型。 输入可以是任何表达式如`['get', 'entityType']`，它返回一个字符串或数字。 每个标签必须是单个文本值或文本值，其值必须是所有字符串或所有数字的数组。 输入与匹配任何中数组匹配的值。 每个标签必须是唯一的。 如果输入的类型不匹配的标签的类型，结果将是回退值。

下面的伪代码定义的结构`match`表达式。 

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

以下示例查看`entityType`的气泡图层中的点功能的属性搜索匹配项。 如果找到匹配项，则返回值或返回回退值指定。

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

以下示例使用一个数组，若要列出一组的所有应返回相同的值的标签。 这是不是分别列出每个标签的高效得多。 在这种情况下，如果`entityType`属性为"餐馆"或"grocery_store"，就将返回"red"的颜色。

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

### <a name="coalesce-expression"></a>Coalesce 表达式

一个`coalesce`表达式步骤通过一组表达式，直到第一个非 null 值获取和返回该值。 

下面的伪代码定义的结构`coalesce`表达式。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**示例**

下面的示例使用`coalesce`表达式来设置`textField`符号层的选项。 如果`title`属性中的功能或设置为缺少`null`，该表达式将尝试寻找`subtitle`属性，如果其缺少或`null`，它将会回退到一个空字符串。 

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

## <a name="type-expressions"></a>类型表达式

类型表达式提供用于测试和转换不同的数据类型，如字符串、 数字和布尔值的工具。

| 表达式 | 返回类型 | 描述 |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | 数组\|对象 | 返回数组或对象的文本值。 使用此表达式来防止将被评估为表达式的数组或对象。 当需要由表达式返回数组或对象时，这是必需的。 |
| `['to-boolean', value]` | boolean | 将输入的值转换为布尔值。 结果是`false`输入是空字符串，当`0`， `false`， `null`，或`NaN`; 否则为其`true`。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 输入的值转换为一种颜色。 如果提供了多个值，直到获得第一次成功转换按顺序评估每个。 如果可以转换没有任何输入，该表达式是错误的。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | 数字 | 将输入的值转换为一个数字，如有可能。 如果输入的内容`null`或`false`，则结果为 0。 如果输入是`true`，则结果为 1。 如果输入是一个字符串，它将转换为数字 using [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) string ECMAScript 语言规范的函数。 如果提供了多个值，直到获得第一次成功转换按顺序评估每个。 如果可以转换没有任何输入，该表达式是错误的。 |
| `['to-string', value]` | string | 将输入的值转换为字符串。 如果输入的内容`null`，结果是`""`。 如果输入是一个布尔值，则结果是`"true"`或`"false"`。 如果输入是一个数字，它将转换为字符串使用[ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type)编号 ECMAScript 语言规范的函数。 如果输入是一种颜色，它将转换为 CSS RGBA 颜色字符串`"rgba(r,g,b,a)"`。 否则，将输入转换为字符串使用[JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) ECMAScript 语言规范的函数。 |
| `['typeof', value]` | string | 返回描述类型的给定值的字符串。 |

> [!TIP]
> 如果错误消息类似于`Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`将显示在浏览器控制台中它意味着是有一个数组，其中不包含其第一个值的字符串在代码中的某个位置的表达式。 如果你想要返回的数组的表达式，包装数组，该数组`literal`表达式。 下面的示例设置图标`offset`符号层，需要使用包含两个数字的数组的选项`match`两个偏移量值之间进行选择的表达式值的基础`entityType`点属性功能。
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
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>颜色表达式

颜色表达式更加轻松地创建和操作颜色值。

| 表达式 | 返回类型 | 描述 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | 创建从一个颜色值*红色*，*绿色*，并*蓝色*组件之间的范围必须`0`和`255`，和的 alpha 分量的`1`. 如果任何组件不在范围内，该表达式是错误的。 |
| `['rgba', number, number, number, number]` | color | 创建从一个颜色值*红色*，*绿色*，*蓝色*组件之间的范围必须`0`和`255`，并在某个范围内的 alpha 分量`0`和`1`。 如果任何组件不在范围内，该表达式是错误的。 |
| `['to-rgba']` | \[编号、 数字、 数量、 编号\] | 返回包含输入的颜色的四个元素数组*红色*，*绿色*，*蓝色*，以及*alpha*组件，请在该顺序。 |

**示例**

下面的示例创建并具有 RGB 颜色值*红色*的值`255`，和*绿色*并*蓝色*乘以来计算的值`2.5`的值`temperature`属性。 为温度变化颜色将更改为不同的色调*红色*。

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

字符串运算符表达式执行转换操作，如串联和转换大小写的字符串。 

| 表达式 | 返回类型 | 描述 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | string | 将多个字符串连接在一起。 每个值必须是字符串。 使用`to-string`键入表达式，如果需要将其他值类型转换为字符串。 |
| `['downcase', string]` | string | 将指定的字符串转换为小写。 |
| `['upcase', string]` | string | 将指定的字符串转换为大写。 |

**示例**

以下示例将转换`temperature`点的属性功能转换为字符串，然后再连接到它的末尾的"° F"。

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

上面的表达式将呈现包含文本"64 ° F"— — 在其下图中所示上叠加在地图上的 pin。

<center>

![字符串运算符的表达式示例](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>内插并单步表达式

执行内插和步长表达式可用于计算值的内插的曲线或步骤函数。 这些表达式需要一个表达式，返回的数字值作为其输入，例如中`['get',  'temperature']`。 针对输入和输出值，名为"停止"，以确定最适合的内插的曲线或步骤函数的值对计算输入的值。 每个停止的输入的值必须是一个数字并按升序排序。 输出值必须是数字和数字，数组或一种颜色。

### <a name="interpolate-expression"></a>内插表达式

`interpolate`表达式可用于属性停止值之间插入值来计算连续、 顺利地的一组值。 `interpolate`返回颜色值的表达式将生成从选择的结果值的颜色渐变。

有三种类型的内插方法，可在`interpolate`表达式：
 
* `['linear']` -内插以线性方式停止对之间。
* `['exponential', base]` -之间进行内插指数级增长则将停止。 `base`值控制输出位于增长的速率。 较高的值使输出更增加高范围的末尾。 一个`base`接近 1 的值产生更呈线性增加的输出。
* `['cubic-bezier', x1, y1, x2, y2]` -内插使用[三次方贝塞尔曲线](https://developer.mozilla.org/docs/Web/CSS/timing-function)由给定的控件点定义。

下面是这些不同类型的内插的外观示例。 

| 线性  | 指数 | 三次方贝塞尔 |
|---------|-------------|--------------|
| ![线性内插关系图](media/how-to-expressions/linear-interpolation.png) | ![指数的内插关系图](media/how-to-expressions/exponential-interpolation.png) | ![三次方贝塞尔曲线内插关系图](media/how-to-expressions/bezier-curve-interpolation.png) |

下面的伪代码定义的结构`interpolate`表达式。 

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

下面的示例使用`linear interpolate`表达式，设置`color`气泡图层属性基于`temperature`点功能的属性。 如果`temperature`值小于 60，"blue"将返回，如果达到 60 到小于 70，黄色将返回，如果 70，小于 80，"橙色"将返回，如果将返回 80 或更高版本，"red"。

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

![内插表达式示例](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>步骤表达式

一个`step`表达式可用于通过评估来评估计算离散、 递阶结果值[分段常量函数](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)定义的停止点。 

下面的伪代码定义的结构`step`表达式。 

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

步骤表达式返回停止之前输入的值或如果输入的内容的第一个输入的值不会早于第一个位的输出值。 

**示例**

下面的示例使用`step`表达式，设置`color`气泡图层属性基于`temperature`点功能的属性。 如果`temperature`值小于 60，"blue"将返回，如果达到 60 到小于 70，"黄色"将返回，如果 70，小于 80，"橙色"将返回，如果将返回 80 或更高版本，"red"。

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

## <a name="layer-specific-expressions"></a>层特定表达式

仅适用于特定层的特殊表达式。

### <a name="heat-map-density-expression"></a>热度地图密度表达式

热度地图密度表达式检索热度地图层中每个像素的热度地图密度值，并定义为`['heatmap-density']`。 此值是之间的数字`0`并`1`和与结合使用`interpolation`或`step`表达式定义用于为着色热度地图的颜色渐变。 此表达式仅可在[颜色选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color)的热度地图层。

> [!TIP]
> 在索引内插表达式中的 0 处的颜色或步骤颜色的默认颜色定义区域的颜色其中没有任何数据，用于定义背景色。 很多用户喜欢将此值设置为透明或半透明黑色。 

**示例**

此示例使用单行内插表达式来创建用于呈现热度地图平滑颜色渐变。 

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

除了使用流畅的渐变着色，让热度地图，可以将指定的颜色的一组范围内使用`step`表达式。 使用`step`着色热度地图的表达式分解，类似于轮廓或雷达图样式映射到范围以可视方式密度。  

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

行进度表达式检索沿渐变线线条层中的进度，并定义为`['line-progress']`。 此值是介于 0 和 1 之间的数字，结合使用`interpolation`或`step`表达式。 此表达式仅可用于[strokeGradient 选项]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient)的线条层。 

> [!NOTE]
> `strokeGradient`线条层的选项需要`lineMetrics`数据源的选项设置为`true`。

**示例**

下面的示例使用`['line-progress']`要应用于行的笔画的颜色渐变的表达式。

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

[实时示例，请参阅](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文本字段格式表达式

文本字段格式表达式可以用于`textField`符号层选项`textOptions`属性以提供混合的文本格式设置。 此表达式允许一组输入的字符串和格式设置选项来指定。 可以为此表达式中的每个输入字符串中指定以下选项。

 * `'font-scale'` -指定字体大小的缩放系数。 如果指定，此值将覆盖`size`属性的`textOptions`单个字符串。
 * `'text-font'` -指定应应用于此字符串的一个或多个字体系列。 如果指定，此值将覆盖`font`属性的`textOptions`单个字符串。

下面的伪代码定义文本字段格式表达式的结构。 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**示例**

下面的示例通过添加粗体的字体和纵向扩展的字体大小设置文本字段`title`功能的属性。 此示例还将添加`subtitle`属性的上一个换行符，使用扩展的功能向下字体大小。

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
            { 'font-scale': 0.75 }
        ]
    }
});
```

此层将呈现点功能，如下图中所示：
 
<center>

![点功能与带格式的文本字段的图像](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>数字格式表达式

`number-format`表达式只能与使用`textField`符号层的选项。 此表达式将格式化的字符串转换为提供的数。 此表达式将 JavaScript 的包装[Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函数，并支持以下选项组。

 * `locale` -指定此选项可将数字转换为字符串与指定的语言一致的方式。 传递[BCP 47 语言标记](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)到此选项。
 * `currency` -若要将数字转换成一个字符串，表示一种货币。 可能的值为[组成的 ISO 4217 货币代码](https://en.wikipedia.org/wiki/ISO_4217)，如"美元"表示美元、 欧元符号，"EUR"或"元"为中文 RMB。
 * `'min-fraction-digits'` -指定要包含在数字的字符串版本中的小数位数的最小数目。
 * `'max-fraction-digits'` -指定要包含在数字的字符串版本中的小数位数的最大数目。

下面的伪代码定义文本字段格式表达式的结构。 

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

下面的示例使用`number-format`若要修改的表达式如何`revenue`点功能的属性呈现在`textField`选项符号的层，使其显示的美元值。

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

此层将呈现点功能，如下图中所示：

<center>

![数字格式表达式示例](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>缩放表达式

一个`zoom`表达式用于在呈现时检索该映射的当前缩放级别和定义为`['zoom']`。 此表达式返回的映射的最小和最大缩放级别范围之间的数字。 使用此表达式可以为地图的缩放级别已更改时动态修改的样式。 `zoom`表达式可能仅可用于`interpolate`和`step`表达式。

**示例**

默认情况下，热度地图层中呈现的数据点的半径具有所有的缩放级别的固定的像素半径。 如放大地图组合在一起的数据聚合和热度地图层看起来不同。 一个`zoom`表达式可用于缩放的半径的每个缩放级别，使每个数据点包括映射的同一个物理区域。 这会使查找热度地图层，更静态和一致。 映射的每个缩放级别的整个像素由双倍的垂直和水平方向，为以前的缩放级别。 缩放半径，以便它与每个缩放级别加倍将创建所有的缩放级别看上去一致热度地图。 这可以通过实现`zoom`表达式使用`base 2 exponential interpolation`表达式，如下所示。 

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

[实时示例，请参阅](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>变量绑定表达式

变量绑定表达式的计算结果存储在变量中，以便它可以引用其他位置在表达式中多次而无需重新计算它。 这是一项实用优化涉及许多计算的表达式

| 表达式 | 返回类型 | 描述 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;let<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1： 字符串，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1： 任何，<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2： 字符串，<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2： 任何，<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 将一个或多个值存储为变量以供`var`中返回结果的子表达式的表达式。 |
| `['var', name: string]` | 任意 | 引用使用创建的变量`let`表达式。 |

**示例**

此示例使用一个表达式，计算相对于温度比，然后选择使用营业收入`case`要计算此值不同的布尔操作的表达式。 `let`表达式用于存储相对于温度比收入，以便它只需一次计算和`var`表达式引用此变量通常需要而无需重新计算它。

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

请参阅以下文章，了解实现表达式的更多代码示例：

> [!div class="nextstepaction"] 
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"] 
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [添加形状](map-add-shape.md)

> [!div class="nextstepaction"] 
> [添加热度地图层](map-add-heat-map-layer.md)

了解有关支持表达式的层选项的详细信息：

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
