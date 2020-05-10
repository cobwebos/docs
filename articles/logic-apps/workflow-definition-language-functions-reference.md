---
title: 表达式中函数的参考指南
description: Azure 逻辑应用和 Power Automate 的表达式中的函数参考指南
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 56bf1898eb00d74fe92934ca8cd7d9d2848c2f1f
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005896"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>有关在 Azure 逻辑应用和 Power Automate 的表达式中使用函数的参考指南

对于 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 [Power Automate](https://docs.microsoft.com/flow/getting-started) 中的工作流定义，某些[表达式](../logic-apps/logic-apps-workflow-definition-language.md#expressions)获取的值来自运行时操作，而这些运行时操作在工作流开始运行时可能尚不存在。 若要在这些表达式中引用或处理这些值，可以使用[工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)提供的函数  。

> [!NOTE]
> 本参考页适用于 Azure 逻辑应用和 Power Automate，但显示在 Azure 逻辑应用文档中。 虽然此页面专门引用逻辑应用，但这些函数适用于流和逻辑应用。 有关 Power Automate 中函数和表达式的详细信息，请参阅[在条件中使用表达式](https://docs.microsoft.com/flow/use-expressions-in-conditions)。

例如，当您想要从整数或浮点值求和时，可以使用数学函数（例如[add （）](../logic-apps/workflow-definition-language-functions-reference.md#add)函数）来计算值。 下面是可以使用函数执行的其他示例任务：

| 任务 | 函数语法 | 结果 |
| ---- | --------------- | ------ |
| 返回小写格式的字符串。 | toLower('<*text*>') <p>例如：toLower('Hello') | "hello" |
| 返回全局唯一标识符 (GUID)。 | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

若要[基于函数的常规用途](#ordered-by-purpose)查找函数，请查看以下各表。 若要了解每个函数的详细信息，请参阅[按字母顺序排序的列表](#alphabetical-list)。

## <a name="functions-in-expressions"></a>表达式中的函数

为了展示如何在表达式中使用函数，此示例展示了如何从 `customerName` 参数获取值，并在表达式中使用 [parameters()](#parameters) 函数将该值赋给 `accountName` 属性：

```json
"accountName": "@parameters('customerName')"
```

下面是表达式中的函数的其他一般使用方法：

| 任务 | 表达式中的函数语法 |
| ---- | -------------------------------- |
| 通过将某个项传递给函数，对该项执行操作。 | "\@<*functionName*>(<*item*>)" |
| 1. 使用嵌套*parameterName* `parameters()`函数获取 parameterName 的值。 </br>2. 通过将该值传递给*functionName*来执行与结果有关的操作。 | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. 从嵌套内部函数*functionName*获取结果。 </br>2. 将结果传递给外部函数*functionName2*。 | "\@<*functionName2*>(<*functionName*>(<*item*>))" |
| 1. 从*functionName*获取结果。 </br>2. 假设结果是具有属性*propertyName*的对象，则获取该属性的值。 | "\@<*functionName*>(<*item*>).<*propertyName*>" |
|||

例如，`concat()` 函数可以采用两个或更多个字符串值作为参数。 此函数将这些字符串组合成一个字符串。 可以传入字符串文字（例如 "Sophia" 和 "Owen"），以便获取组合的字符串 "SophiaOwen"：

```json
"customerName": "@concat('Sophia', 'Owen')"
```

或者，可以从参数获取字符串值。 此示例在每个 `concat()` 参数以及 `firstName` 和 `lastName` 参数中使用 `parameters()` 函数。 然后，将生成的字符串传递给 `concat()` 函数，以便获取组合的字符串，例如 "SophiaOwen"：

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

无论使用哪种方式，这两个示例都会将结果分配给 `customerName` 属性。

下面是有关表达式中的函数的一些其他说明：

* 函数参数从左到右进行计算。

* 在参数定义的语法中，参数后显示的问号 (?) 表示参数是可选的。 有关示例，请参阅 [getFutureTime()](#getFutureTime)。

以下部分根据函数的常规用途来组织函数，或者可以按[字母顺序](#alphabetical-list)浏览这些函数。

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>字符串函数

若要使用字符串，可以使用这些字符串函数以及某些[集合函数](#collection-functions)。 字符串函数仅适用于字符串。

| 字符串函数 | 任务 |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | 组合两个或更多字符串，并返回组合后的字符串。 |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | 检查字符串是否以指定的子字符串结尾。 |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | 根据指定的格式将数字返回为字符串 |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | 生成字符串形式的全局唯一标识符 (GUID)。 |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | 返回子字符串的起始位置。 |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | 返回最后一次出现的子字符串的起始位置。 |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | 将子字符串替换为指定的字符串，并返回更新的字符串。 |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | 根据原始字符串中指定的分隔符字符，从较大字符串中返回一个包含子字符串（以逗号分隔）的数组。 |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | 检查字符串是否以特定的子字符串开头。 |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | 返回字符串中的字符，从指定的位置开始。 |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | 返回小写格式的字符串。 |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | 返回大写格式的字符串。 |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | 从字符串中删除前导和尾随空格，并返回更新后的字符串。 |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>集合函数

若要使用集合（通常是数组或字符串，有时是字典），可以使用这些集合函数。

| 集合函数 | 任务 |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | 检查集合是否包含某个特定项。 |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | 检查集合是否为空。 |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | 返回集合中的第一个项。 |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | 返回其中仅包含指定集合的共有项的一个集合。** |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 位于针对数组的重复操作中时，返回在操作的当前迭代过程中数组中的当前项。 |
| [联接](../logic-apps/workflow-definition-language-functions-reference.md#join) | 返回一个字符串，其中包含某个数组中的所有项并以指定的分隔符分隔每个项。** |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | 返回集合中的最后一个项。 |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | 返回字符串或数组中的项数。 |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | 删除集合开头的项，并返回所有其他项。** |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | 返回集合开头的项。 |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | 返回一个集合，其中包含指定集合中的所有项。** |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>逻辑比较函数

若要使用条件、比较值和表达式结果或评估各种类型的逻辑，可以使用这些逻辑比较函数。 有关每个函数的完整参考，请参阅[按字母顺序排序的列表](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)。

| 逻辑比较函数 | 任务 |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | 检查所有表达式是否为 true。 |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | 检查两个值是否相等。 |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | 检查第一个值是否大于第二个值。 |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | 检查第一个值是否大于或等于第二个值。 |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | 检查表达式为 true 还是 false。 根据结果返回指定的值。 |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | 检查第一个值是否小于第二个值。 |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | 检查第一个值是否小于或等于第二个值。 |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | 检查表达式是否为 false。 |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | 检查是否至少一个表达式为 true。 |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>转换函数

若要更改值的类型或格式，可以使用这些转换函数。 例如，可将值从布尔值更改为整数。 若要详细了解逻辑应用在转换期间如何处理内容类型，请参阅[处理内容类型](../logic-apps/logic-apps-content-type.md)。 有关每个函数的完整参考，请参阅[按字母顺序排序的列表](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)。

> [!NOTE]
> Azure 逻辑应用会自动转换某些数据类型之间的值，这意味着你无需手动执行这些转换。 但是，如果这样做，可能会遇到意外的显示行为，这不会影响实际的转换，而只会显示它们的显示方式。 有关详细信息，请参阅[隐式数据类型转换](#implicit-data-conversions)。

| 转换函数 | 任务 |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | 从单个指定的输入返回数组。 对于多个输入，请参阅 [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray)。 |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | 返回字符串的 base64 编码版本。 |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | 返回 base64 编码字符串的二进制版本。 |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | 返回 base64 编码字符串的字符串版本。 |
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | 返回输入值的二进制版本。 |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | 返回输入值的布尔值版本。 |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | 从多个输入返回数组。 |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | 返回输入值的数据 URI。 |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | 返回数据 URI 的二进制版本。 |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | 返回数据 URI 的字符串版本。 |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | 返回 base64 编码字符串的字符串版本。 |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | 返回数据 URI 的二进制版本。 |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | 返回一个字符串，并将其中的转义字符替换为解码后的版本。 |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | 返回一个字符串，并将其中的 URL 不安全字符替换为转义字符。 |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | 返回输入值的浮点数。 |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | 返回字符串的整数版本。 |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | 返回字符串或 XML 的 JavaScript 对象表示法 (JSON) 类型的值或对象。 |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | 返回输入值的字符串版本。 |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | 通过将 URL 不安全字符替换为转义字符来返回输入值的 URI 编码版本。 |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | 返回 URI 编码字符串的二进制版本。 |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | 返回 URI 编码字符串的字符串版本。 |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | 返回字符串的 XML 版本。 |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>隐式数据类型转换

Azure 逻辑应用自动或隐式地在某些数据类型之间进行转换，因此你不必手动转换这些类型。 例如，如果使用非字符串值，其中字符串应为输入，则逻辑应用会自动将非字符串值转换为字符串。

例如，假设触发器返回一个数值作为输出：

`triggerBody()?['123']`

如果使用此数字输出，其中字符串输入应为，如 URL，则逻辑应用会使用大括号（`{}`）表示法自动将该值转换为字符串：

`@{triggerBody()?['123']}`

### <a name="base64-encoding-and-decoding"></a>Base64 编码和解码

逻辑应用自动或隐式执行 base64 编码或解码，因此您无需使用相应的表达式手动执行这些操作：

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> 如果你手动将这些表达式添加到逻辑应用，例如通过使用表达式编辑器，则从逻辑应用设计器中导航并返回到设计器，设计器仅显示参数值。 仅当不编辑参数值时，才会将表达式保留在代码视图中。 否则，逻辑应用会从代码视图中删除表达式，仅保留参数值。 此行为不会影响编码或解码，而只会显示表达式是否显示。

<a name="math-functions"></a>

## <a name="math-functions"></a>数学函数

若要使用整数和浮点数，可以使用这些数学函数。
有关每个函数的完整参考，请参阅[按字母顺序排序的列表](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)。

| 数学函数 | 任务 |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | 返回两个数字相加的结果。 |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | 返回两个数字相除的结果。 |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | 返回一组数字或数组中的最大值。 |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | 返回一组数字或数组中的最小值。 |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | 返回将两个数字相除后的余数。 |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | 返回将两个数字相乘得到的乘积。 |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | 返回指定范围内的随机整数。 |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | 返回以指定整数开头的一个整数数组。 |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | 返回第一个数字减去第二个数字得到的结果。 |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>日期和时间函数

若要使用日期和时间，可以使用这些日期和时间函数。
有关每个函数的完整参考，请参阅[按字母顺序排序的列表](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)。

| 日期或时间函数 | 任务 |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | 将天数加到时间戳。 |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | 将小时数加到时间戳。 |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | 将分钟数加到时间戳。 |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | 将秒数加到时间戳。 |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | 将一定数目的时间单位加到时间戳。 另请参阅 [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime)。 |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | 将时间戳从协调世界时 (UTC) 转换为目标时区。 |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | 将时间戳从源时区转换为目标时区。 |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | 将时间戳从源时区转换为协调世界时 (UTC)。 |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | 返回时间戳中月份组成部分的日期。 |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | 返回时间戳中周组成部分的星期日期。 |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | 返回时间戳中年组成部分的日期。 |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | 返回时间戳中的日期。 |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | 返回当前时间戳加上指定的时间单位。 另请参阅 [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime)。 |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | 返回当前时间戳减去指定的时间单位。 另请参阅 [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime)。 |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | 返回时间戳中的天的开始时间。 |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | 返回时间戳中的小时的开始时间。 |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | 返回时间戳中的月份的开始时间。 |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | 从时间戳中减去一定数目的时间单位。 另请参阅 [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime)。 |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | 返回指定时间戳的 `ticks` 属性值。 |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | 返回字符串形式的当前时间戳。 |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>工作流函数

这些工作流函数有助于：

* 在运行时获取有关工作流实例的详细信息。
* 使用用于实例化逻辑应用或流的输入。
* 引用触发器和操作的输出。

例如，可以引用一个操作的输出，并在后面的操作中使用该数据。
有关每个函数的完整参考，请参阅[按字母顺序排序的列表](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)。

| 工作流函数 | 任务 |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | 返回当前操作在运行时的输出，或者来自其他 JSON 名称和值对的值。 另请参阅 [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions)。 |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | 返回操作在运行时的 `body` 输出。 另请参阅 [body](../logic-apps/workflow-definition-language-functions-reference.md#body)。 |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | 返回操作在运行时的输出。 请参阅[输出](../logic-apps/workflow-definition-language-functions-reference.md#outputs)和[操作](../logic-apps/workflow-definition-language-functions-reference.md#actions)。 |
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | 返回操作在运行时的输出，或者来自其他 JSON 名称和值对的值。 另请参阅 [action](../logic-apps/workflow-definition-language-functions-reference.md#action)。  |
| [body](#body) | 返回操作在运行时的 `body` 输出。 另请参阅 [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody)。 |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | 创建一个数组，其中包含与表单数据或表单编码操作输出中某个键名匹配的值。**** |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | 返回与操作的表单数据或表单编码输出中的键名称匹配的单个值。**** |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 位于针对数组的重复操作中时，返回在操作的当前迭代过程中数组中的当前项。 |
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | 位于 Foreach 或 Until 循环中时，返回指定循环中的当前项。|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | 位于 Until 循环中时，返回当前迭代的索引值。 可以在嵌套式的 Until 循环中使用该函数。 |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | 返回调用某个触发器或操作的“回调 URL”。 |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | 返回具有多个部分的操作输出中某个特定部分的正文。 |
| [输出](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | 返回操作在运行时的输出。 |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | 返回工作流定义中描述的参数的值。 |
| [输出](../logic-apps/workflow-definition-language-functions-reference.md#result) | 返回指定的范围受限操作（例如 `For_each`、`Until` 和 `Scope`）中所有操作的输入和输出。 |
| [界限](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | 返回触发器在运行时的输出，或者来自其他 JSON 名称和值对的输出。 另请参阅 [triggerOutputs](#triggerOutputs) 和 [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)。 |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | 返回触发器在运行时的 `body` 输出。 请参阅[触发器](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | 返回与表单数据或表单编码触发器输出中某个键名匹配的单个值。**** |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | 返回触发器多部分输出中特定部分的正文。 |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | 创建一个数组，该数组的值与表单数据或表单编码触发器输出中某个键名匹配。**** |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | 返回触发器在运行时的输出，或者来自其他 JSON 名称和值对的值。 请参阅[触发器](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 |
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | 返回指定变量的值。 |
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | 返回运行期间有关工作流本身的所有详细信息。 |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI 分析函数

若要使用统一资源标识符 (URI) 并获取这些 URI 的各个属性值，可以使用这些 URI 分析函数。
有关每个函数的完整参考，请参阅[按字母顺序排序的列表](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)。

| URI 分析函数 | 任务 |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | 返回统一资源标识符 (URI) 的 `host` 值。 |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | 返回统一资源标识符 (URI) 的 `path` 值。 |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | 返回统一资源标识符 (URI) 的 `path` 和 `query` 值。 |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | 返回统一资源标识符 (URI) 的 `port` 值。 |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | 返回统一资源标识符 (URI) 的 `query` 值。 |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | 返回统一资源标识符 (URI) 的 `scheme` 值。 |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>操作函数：JSON 和 XML

若要使用 JSON 对象和 XML 节点，可以使用这些操作函数。
有关每个函数的完整参考，请参阅[按字母顺序排序的列表](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)。

| 操作函数 | 任务 |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | 将属性及其值或名称/值对添加到 JSON 对象，并返回更新的对象。 |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | 返回一个或多个参数中的第一个非 null 值。 |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | 从 JSON 对象中删除某个属性，并返回更新的对象。 |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | 设置 JSON 对象的属性值并返回更新的对象。 |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | 检查 XML 中是否存在与 XPath（XML 路径语言）表达式匹配的节点或值，并返回匹配的节点或值。 |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>所有函数 - 字母列表

此部分按字母顺序列出所有可用函数。

<a name="action"></a>

### <a name="action"></a>action

返回“当前”** 操作在运行时的输出，或者来自其他 JSON 名称和值对的值，可以将其分配给表达式。
默认情况下，此函数引用整个操作对象，但是也可以指定你需要其值的属性。
另请参阅[操作（）](../logic-apps/workflow-definition-language-functions-reference.md#actions)。

只能在以下位置使用 `action()` 函数：

* Webhook 操作的 `unsubscribe` 属性，用以访问原始 `subscribe` 请求的结果
* 操作的 `trackedProperties` 属性
* 操作的 `do-until` 循环条件

```
action()
action().outputs.body.<property>
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*知识产权*> | 否 | String | 需要获取其值的操作对象属性的名称：**name**、**startTime**、**endTime**、**inputs**、**outputs**、**status**、**code**、**trackingId** 和 **clientTrackingId**。 在 Azure 门户中，可以通过查看特定运行历史记录的详细信息来查找这些属性。 有关详细信息，请参阅 [REST API - 工作流运行操作](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get)。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| <*操作-输出*> | String | 当前操作或属性的输出 |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

返回操作在运行时的 `body` 输出。
`actions('<actionName>').outputs.body` 的速记。
请参阅 [body()](#body) 和 [actions()](#actions)。

```
actionBody('<actionName>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 是 | String | 所需的操作 `body` 输出的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| <*操作-正文-输出*> | String | 指定操作的 `body` 输出 |
||||

*示例*

此示例获取 Twitter 操作 `Get user` 的 `body` 输出：

```
actionBody('Get_user')
```

并返回以下结果：

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

返回操作在运行时的输出。  和是的`actions('<actionName>').outputs`简写形式。 请参阅[操作（）](#actions)。 `actionOutputs()`函数在逻辑应用`outputs()`设计器中解析为，因此请考虑使用[输出（）](#outputs)，而不是`actionOutputs()`。 虽然这两个函数的工作方式相同，但首选 `outputs()`。

```
actionOutputs('<actionName>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 是 | String | 所需的操作输出的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| <*输出*> | String | 指定操作的输出 |
||||

*示例*

此示例获取 Twitter 操作 `Get user` 的输出：

```
actionOutputs('Get_user')
```

并返回以下结果：

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>actions

返回操作在运行时的输出，或者来自其他 JSON 名称和值对的值，可以将其分配给表达式。 默认情况下，此函数引用整个操作对象，但是也可以指定你需要其值的属性。
有关速记版本，请参阅 [actionBody()](#actionBody)、[actionOutputs()](#actionOutputs) 和 [body()](#body)。
有关当前操作，请参阅 [action()](#action)。

> [!NOTE]
> 以前，当指定基于另一操作的输出运行的操作时，可以使用 `actions()` 函数或 `conditions` 元素。 但是，若要显式声明操作之间的依赖关系，现在必须使用依赖的操作的 `runAfter` 属性。
> 若要详细了解 `runAfter` 属性，请参阅[使用 runAfter 属性捕获和处理故障](../logic-apps/logic-apps-workflow-definition-language.md)。

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 是 | String | 需要获取其输出的操作对象的名称  |
| <*知识产权*> | 否 | String | 需要获取其值的操作对象属性的名称：**name**、**startTime**、**endTime**、**inputs**、**outputs**、**status**、**code**、**trackingId** 和 **clientTrackingId**。 在 Azure 门户中，可以通过查看特定运行历史记录的详细信息来查找这些属性。 有关详细信息，请参阅 [REST API - 工作流运行操作](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get)。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| <*操作-输出*> | String | 指定操作或属性的输出 |
||||

*示例*

此示例获取 Twitter 操作 `Get user` 在运行时的 `status` 属性值：

```
actions('Get_user').outputs.body.status
```

并返回以下结果：`"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

返回两个数字相加的结果。

```
add(<summand_1>, <summand_2>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | 是 | 整数、浮点数或混合类型 | 要相加的数字 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| <*result-sum*> | 整数或浮点数 | 指定数字相加的结果。 |
||||

*示例*

此示例将指定的数字相加：

```
add(1, 1.5)
```

并返回以下结果：`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

将天数加到时间戳。

```
addDays('<timestamp>', <days>, '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*天数*> | 是 | Integer | 要加上的正负天数 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 时间戳加上指定的天数  |
||||

*示例 1*

此示例将 10 天加到指定的时间戳：

```
addDays('2018-03-15T13:00:00Z', 10)
```

并返回以下结果：`"2018-03-25T00:00:0000000Z"`

*示例 2*

此示例从指定的时间戳减去 5 天：

```
addDays('2018-03-15T00:00:00Z', -5)
```

并返回以下结果：`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

将小时数加到时间戳。

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*小时*> | 是 | Integer | 要加上的正负小时数 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 时间戳加上指定的小时数  |
||||

*示例 1*

此示例将 10 小时加到指定的时间戳：

```
addHours('2018-03-15T00:00:00Z', 10)
```

并返回以下结果：`"2018-03-15T10:00:0000000Z"`

*示例 2*

此示例从指定的时间戳减去 5 小时：

```
addHours('2018-03-15T15:00:00Z', -5)
```

并返回以下结果：`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

将分钟数加到时间戳。

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*）*> | 是 | Integer | 要加上的正负分钟数 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 时间戳加上指定的分钟数 |
||||

*示例 1*

此示例将 10 分钟加到指定的时间戳：

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

并返回以下结果：`"2018-03-15T00:20:00.0000000Z"`

*示例 2*

此示例从指定的时间戳减去 5 分钟：

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

并返回以下结果：`"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

将属性及其值或名称/值对添加到 JSON 对象，并返回更新的对象。 如果在运行时已存在该属性，此函数会失败并引发错误。

```
addProperty(<object>, '<property>', <value>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*对象*> | 是 | Object | 要将属性添加到的 JSON 对象 |
| <*知识产权*> | 是 | String | 要添加的属性的名称 |
| <*负值*> | 是 | 任意 | 属性的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-对象*> | Object | 具有指定属性的更新后 JSON 对象 |
||||

若要向现有属性添加子属性，请使用以下语法：

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*对象*> | 是 | Object | 要将属性添加到的 JSON 对象 |
| <*父属性*> | 是 | String | 要在其中添加子属性的父属性的名称 |
| <*子属性*> | 是 | String | 要添加的子属性的名称 |
| <*负值*> | 是 | 任意 | 要为指定属性设置的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-对象*> | Object | 设置了其属性的更新后 JSON 对象 |
||||

*示例 1*

此示例将 `middleName` 属性加到 JSON 对象，可通过 [JSON()](#json) 函数将其从字符串转换为 JSON。 该对象已经包含 `firstName` 和 `surName` 属性。 该函数将指定的值分配给新属性，并返回更新后的对象：

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

下面是当前的 JSON 对象：

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

下面是已更新的 JSON 对象：

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*示例 2*

此示例将 `middleName` 子属性加到 JSON 对象中的现有 `customerName` 属性，可通过 [JSON()](#json) 函数将其从字符串转换为 JSON。 该函数将指定的值分配给新属性，并返回更新后的对象：

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

下面是当前的 JSON 对象：

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

下面是已更新的 JSON 对象：

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

将秒数加到时间戳。

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*计算*> | 是 | Integer | 要加上的正负秒数 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 时间戳加上指定的秒数  |
||||

*示例 1*

此示例将 10 秒加到指定的时间戳：

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

并返回以下结果：`"2018-03-15T00:00:10.0000000Z"`

*示例 2*

此示例从指定的时间戳减去 5 秒：

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

并返回以下结果：`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

将一定数目的时间单位加到时间戳。
另请参阅[getFutureTime （）](#getFutureTime)。

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*间隔*> | 是 | Integer | 要添加的指定时间单位数 |
| <*timeUnit*> | 是 | String | 要与 *interval* 一起使用的时间单位：“Second”、“Minute”、“Hour”、“Day”、“Week”、“Month”、“Year” |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 时间戳加上指定的时间单位数  |
||||

*示例 1*

此示例将 1 天加到指定的时间戳：

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

并返回以下结果：`"2018-01-02T00:00:00.0000000Z"`

*示例 2*

此示例将 1 天加到指定的时间戳：

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

并使用可选的“D”格式返回结果：`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>and

检查所有表达式是否为 true。
当所有表达式均为 true 时返回 true，当至少一个表达式为 false 时返回 false。

```
and(<expression1>, <expression2>, ...)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | 是 | 布尔 | 要检查的表达式 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| true 或 false | 布尔 | 当所有表达式均为 true 时返回 true。 当至少一个表达式为 false 时返回 false。 |
||||

*示例 1*

这些示例检查指定的布尔值是否全为 true：

```
and(true, true)
and(false, true)
and(false, false)
```

并返回以下结果：

* 第一个示例：两个表达式均为 true，因此返回 `true`。
* 第二个示例：一个表达式为 false，因此返回 `false`。
* 第三个示例：两个表达式均为 false，因此返回 `false`。

*示例 2*

这些示例检查指定的表达式是否均为 true：

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

并返回以下结果：

* 第一个示例：两个表达式均为 true，因此返回 `true`。
* 第二个示例：一个表达式为 false，因此返回 `false`。
* 第三个示例：两个表达式均为 false，因此返回 `false`。

<a name="array"></a>

### <a name="array"></a>array

从单个指定的输入返回数组。
有关多个输入，请参阅[createArray （）](#createArray)。

```
array('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 用于创建数组的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*值*>] | Array | 一个包含单一指定输入的数组 |
||||

*示例*

此示例基于“hello”字符串创建数组：

```
array('hello')
```

并返回以下结果：`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

返回字符串的 base64 编码版本。

> [!NOTE]
> Azure 逻辑应用自动执行 base64 编码和解码，这意味着你无需手动执行这些转换。 但是，如果这样做，可能会遇到意外的显示行为，这不会影响实际的转换，而只会显示它们的显示方式。 有关详细信息，请参阅[隐式数据类型转换](#implicit-data-conversions)。

```
base64('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 输入字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*base64 字符串*> | String | 返回输入字符串的 base64 编码版本 |
||||

*示例*

此示例将“hello”字符串转换为 base64 编码的字符串：

```
base64('hello')
```

并返回以下结果：`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

返回 base64 编码字符串的二进制版本。

> [!NOTE]
> Azure 逻辑应用自动执行 base64 编码和解码，这意味着你无需手动执行这些转换。 但是，如果这样做，可能会遇到意外的显示行为，这不会影响实际的转换，而只会显示它们的显示方式。 有关详细信息，请参阅[隐式数据类型转换](#implicit-data-conversions)。

```
base64ToBinary('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换的 base64 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*二进制-base64 字符串*> | String | base64 编码字符串的二进制版本 |
||||

*示例*

此示例将 base64 编码的“aGVsbG8=”字符串转换为二进制字符串：

```
base64ToBinary('aGVsbG8=')
```

并返回以下结果：

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

返回 base64 编码字符串的字符串版本，有效地对 base64 字符串进行解码。 使用此函数，而不是已弃用的[decodeBase64 （）](#decodeBase64)。

> [!NOTE]
> Azure 逻辑应用自动执行 base64 编码和解码，这意味着你无需手动执行这些转换。 但是，如果这样做，可能会遇到意外的显示行为，这不会影响实际的转换，而只会显示它们的显示方式。 有关详细信息，请参阅[隐式数据类型转换](#implicit-data-conversions)。

```
base64ToString('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要解码的 base64 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已解码-base64-字符串*> | String | base64 编码字符串的字符串版本 |
||||

*示例*

此示例将 base64 编码的“aGVsbG8=”字符串转换为单纯的字符串：

```
base64ToString('aGVsbG8=')
```

并返回以下结果：`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

返回字符串的二进制版本。

```
binary('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*二进制-输入-值*> | String | 指定字符串的二进制版本 |
||||

*示例*

此示例将“hello”字符串转换为二进制字符串：

```
binary('hello')
```

并返回以下结果：

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

返回操作在运行时的 `body` 输出。
`actions('<actionName>').outputs.body` 的速记。
请参阅 [actionBody()](#actionBody) 和 [actions()](#actions)。

```
body('<actionName>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 是 | String | 所需的操作 `body` 输出的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| <*操作-正文-输出*> | String | 指定操作的 `body` 输出 |
||||

*示例*

此示例获取 `Get user` Twitter 操作的 `body` 输出：

```
body('Get_user')
```

并返回以下结果：

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

返回值的布尔版本。

```
bool(<value>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | 任意 | 要转换的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 指定的值的布尔版本 |
||||

*示例*

这些示例将指定的值转换为布尔值：

```
bool(1)
bool(0)
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

返回一个或多个参数中的第一个非 null 值。
空字符串、空数组和空对象不为 null。

```
coalesce(<object_1>, <object_2>, ...)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*object_1*>，<*object_2*> .。。 | 是 | 任意，可以混用各种类型 | 要检查是否为 null 的一个或多个项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*第一-非 null-项*> | 任意 | 第一个不为 null 的项或值。 如果所有参数均为 null，则此函数返回 null。 |
||||

*示例*

这些示例返回指定值中的第一个非 null 值，当所有值均为 null 时返回 null：

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`"hello"`
* 第三个示例：`null`

<a name="concat"></a>

### <a name="concat"></a>concat

组合两个或更多字符串，并返回组合后的字符串。

```
concat('<text1>', '<text2>', ...)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | 是 | String | 至少两个要组合的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*text1text2...*> | String | 基于组合后的输入字符串创建的字符串 |
||||

*示例*

此示例将字符串“Hello”和“World”组合到一起：

```
concat('Hello', 'World')
```

并返回以下结果：`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

检查集合是否包含某个特定项。
当找到该项目时返回 true，找不到该项目时返回 false。
此函数区分大小写。

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

具体而言，此函数对以下集合类型起作用：

* 字符串**，在其中查找子字符串**
* 数组**，在其中查找值**
* 字典**，在其中查找键**

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*集合*> | 是 | 字符串、数组或字典 | 要检查的集合 |
| <*负值*> | 是 | 分别为字符串、数组或字典 | 要查找的项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当找到该项时返回 true。 找不到时返回 false。 |
||||

*示例 1*

此示例检查字符串“hello world”中是否有子字符串“world”并返回 true：

```
contains('hello world', 'world')
```

*示例 2*

此示例检查字符串“hello world”中是否有子字符串“universe”并返回 false：

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

将时间戳从协调世界时 (UTC) 转换为目标时区。

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*destinationTimeZone*> | 是 | String | 目标时区的名称。 有关时区名称，请参阅 [Microsoft 时区索引值](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)，但你可能需要删除时区名称中的任何标点。 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已转换-时间戳*> | String | 已转换为目标时区的时间戳 |
||||

*示例 1*

此示例将时间戳转换为指定的时区：

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

并返回以下结果：`"2018-01-01T00:00:00.0000000"`

*示例 2*

此示例将时间戳转换为指定的时区和格式：

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

并返回以下结果：`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

将时间戳从源时区转换为目标时区。

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*sourceTimeZone*> | 是 | String | 源时区的名称。 有关时区名称，请参阅 [Microsoft 时区索引值](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)，但你可能需要删除时区名称中的任何标点。 |
| <*destinationTimeZone*> | 是 | String | 目标时区的名称。 有关时区名称，请参阅 [Microsoft 时区索引值](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)，但你可能需要删除时区名称中的任何标点。 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已转换-时间戳*> | String | 已转换为目标时区的时间戳 |
||||

*示例 1*

此示例将源时区转换为目标时区：

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

并返回以下结果：`"2018-01-01T00:00:00.0000000"`

*示例 2*

此示例将时区转换为指定的时区和格式：

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

并返回以下结果：`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

将时间戳从源时区转换为协调世界时 (UTC)。

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*sourceTimeZone*> | 是 | String | 源时区的名称。 有关时区名称，请参阅 [Microsoft 时区索引值](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values)，但你可能需要删除时区名称中的任何标点。 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已转换-时间戳*> | String | 已转换为 UTC 的时间戳 |
||||

*示例 1*

此示例将时间戳转换为 UTC：

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

并返回以下结果：`"2018-01-01T08:00:00.0000000Z"`

*示例 2*

此示例将时间戳转换为 UTC：

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

并返回以下结果：`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

从多个输入返回数组。
对于单输入数组，请参阅 [array()](#array)。

```
createArray('<object1>', '<object2>', ...)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | 是 | 任意，但不能混用 | 至少两个用于创建数组的项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*object1*>，<*b j*>，...] | Array | 基于所有输入项创建的数组 |
||||

*示例*

此示例基于以下输入创建数组：

```
createArray('h', 'e', 'l', 'l', 'o')
```

并返回以下结果：`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

返回字符串的数据统一资源标识符 (URI)。

```
dataUri('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*数据 uri*> | String | 输入字符串的数据 URI |
||||

*示例*

此示例创建“hello”字符串的数据 URI：

```
dataUri('hello')
```

并返回以下结果：`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

返回数据统一资源标识符 (URI) 的二进制版本。
请使用此函数而非 [decodeDataUri()](#decodeDataUri)。
虽然这两个函数的工作方式相同，但首选 `dataUriBinary()`。

```
dataUriToBinary('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换的数据 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*二进制-数据 uri*> | String | 数据 URI 的二进制版本 |
||||

*示例*

此示例创建以下数据 URI 的二进制版本：

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

并返回以下结果：

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

返回数据统一资源标识符 (URI) 的字符串版本。

```
dataUriToString('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换的数据 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*数据字符串-uri*> | String | 数据 URI 的字符串版本 |
||||

*示例*

此示例创建以下数据 URI 的字符串：

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

并返回以下结果：`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

基于时间戳返回月中的某天。

```
dayOfMonth('<timestamp>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*每月的某一日*> | Integer | 基于指定的时间戳返回月中的某天 |
||||

*示例*

此示例基于以下时间戳返回月中的某天：

```
dayOfMonth('2018-03-15T13:27:36Z')
```

并返回以下结果：`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

基于时间戳返回周几。

```
dayOfWeek('<timestamp>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*一周的某一日*> | Integer | 指定的时间戳中的周几，其中周日为 0，周一为 1，依此类推 |
||||

*示例*

此示例基于以下时间戳返回周几：

```
dayOfWeek('2018-03-15T13:27:36Z')
```

并返回以下结果：`4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

基于时间戳返回年中的某天。

```
dayOfYear('<timestamp>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*每年的某一日*> | Integer | 基于指定的时间戳返回年中的某天 |
||||

*示例*

此示例基于以下时间戳返回年中的某天：

```
dayOfYear('2018-03-15T13:27:36Z')
```

并返回以下结果：`74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 （不推荐使用）

此函数已弃用，请改用[base64ToString （）](#base64ToString) 。

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

返回数据统一资源标识符 (URI) 的二进制版本。 请考虑使用 [dataUriToBinary()](#dataUriToBinary) 而非 `decodeDataUri()`。 虽然这两个函数的工作方式相同，但首选 `dataUriToBinary()`。

> [!NOTE]
> Azure 逻辑应用自动执行 base64 编码和解码，这意味着你无需手动执行这些转换。 但是，如果这样做，可能会遇到意外的显示行为，这不会影响实际的转换，而只会显示它们的显示方式。 有关详细信息，请参阅[隐式数据类型转换](#implicit-data-conversions)。

```
decodeDataUri('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要解码的数据 URI 字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*二进制-数据 uri*> | String | 数据 URI 字符串的二进制版本 |
||||

*示例*

此示例返回以下数据 URI 的二进制版本：

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

并返回以下结果：

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

返回一个字符串，并将其中的转义字符替换为解码后的版本。

```
decodeUriComponent('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 包含要解码的转义字符的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已解码-uri*> | String | 包含解码后的转义字符的更新后字符串 |
||||

*示例*

此示例将以下字符串中的转义字符替换为解码后的版本：

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

并返回以下结果：`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

返回将两个数字相除后的整数结果。
若要获取余数结果，请参阅 [mod()](#mod)。

```
div(<dividend>, <divisor>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*被除数*> | 是 | 整数或浮点数 | 要用作 *divisor* 的被除数的数字 |
| <*除数*> | 是 | 整数或浮点数 | *除数*相除的数字，但不能为0 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*商-结果*> | Integer | 将第一个数字除以第二个数字后得到的整数结果 |
||||

*示例*

两个示例都将第一个数字除以第二个数字：

```
div(10, 5)
div(11, 5)
```

并返回以下结果：`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

通过将 URL 不安全字符替换为转义字符来返回字符串的统一资源标识符 (URI) 编码版本。 请考虑使用 [uriComponent()](#uriComponent) 而非 `encodeUriComponent()`。 虽然这两个函数的工作方式相同，但首选 `uriComponent()`。

> [!NOTE]
> Azure 逻辑应用自动执行 base64 编码和解码，这意味着你无需手动执行这些转换。 但是，如果这样做，可能会遇到意外的显示行为，这不会影响实际的转换，而只会显示它们的显示方式。 有关详细信息，请参阅[隐式数据类型转换](#implicit-data-conversions)。

```
encodeUriComponent('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换为 URI 编码格式的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*编码-uri*> | String | 带有转义字符的 URI 编码字符串 |
||||

*示例*

此示例创建以下字符串的 URI 编码版本：

```
encodeUriComponent('https://contoso.com')
```

并返回以下结果：`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

检查集合是否为空。
当集合为空时返回 true，不为空时返回 false。

```
empty('<collection>')
empty([<collection>])
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*集合*> | 是 | 字符串、数组或对象 | 要检查的集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当集合为空时返回 true。 不为空时返回 false。 |
||||

*示例*

这些示例检查指定的集合是否为空：

```
empty('')
empty('abc')
```

并返回以下结果：

* 第一个示例：传递一个空字符串，所以此函数返回 `true`。
* 第二个示例：传递字符串“abc”，所以此函数返回 `false`。

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

检查字符串是否以特定的子字符串结尾。
当找到该子字符串时返回 true，找不到时返回 false。
此函数不区分大小写。

```
endsWith('<text>', '<searchText>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 要检查的字符串 |
| <*searchText*> | 是 | String | 要查找的结尾子字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false  | 布尔 | 当找到结尾子字符串时返回 true。 找不到时返回 false。 |
||||

*示例 1*

此示例检查“hello world”字符串是否以“world”字符串结尾：

```
endsWith('hello world', 'world')
```

并返回以下结果：`true`

*示例 2*

此示例检查“hello world”字符串是否以“universe”字符串结尾：

```
endsWith('hello world', 'universe')
```

并返回以下结果：`false`

<a name="equals"></a>

### <a name="equals"></a>equals

检查两个值、表达式或对象是否相等。
当两者相等时返回 true，两者不相等时返回 false。

```
equals('<object1>', '<object2>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | 是 | 各种各样 | 要比较的值、表达式或对象 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当两者相等时返回 true。 不相等时返回 false。 |
||||

*示例*

这些示例检查指定的输入是否相等。

```
equals(true, 1)
equals('abc', 'abcd')
```

并返回以下结果：

* 第一个示例：两个值相等，所以此函数返回 `true`。
* 第二个示例：两个值不相等，所以此函数返回 `false`。

<a name="first"></a>

### <a name="first"></a>first

返回字符串或数组中的第一项。

```
first('<collection>')
first([<collection>])
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*集合*> | 是 | 字符串或数组 | 要在其中查找第一项的集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*第一个集合-项*> | 任意 | 集合中的第一项 |
||||

*示例*

这些示例查找以下集合中的第一项：

```
first('hello')
first(createArray(0, 1, 2))
```

并返回以下结果：

* 第一个示例：`"h"`
* 第二个示例：`0`

<a name="float"></a>

### <a name="float"></a>float

将浮点数的字符串版本转换为实际的浮点数。
仅当将自定义参数传递给应用（例如，逻辑应用或流）时，才使用此函数。

```
float('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 包含要转换的有效浮点数的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*float-值*> | Float | 指定的字符串的浮点数 |
||||

*示例*

此示例创建以下浮点数的字符串版本：

```
float('10.333')
```

并返回以下结果：`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

以指定的格式返回时间戳。

```
formatDateTime('<timestamp>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*重新格式化-时间戳*> | String | 采用指定格式的更新后时间戳 |
||||

*示例*

此示例将时间戳转换为指定的格式：

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

并返回以下结果：`"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

返回与操作的“表单数据”或“表单编码”输出中的键名称匹配的值组成的数组。****

```
formDataMultiValues('<actionName>', '<key>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 是 | String | 其输出中具有所需键值的操作 |
| <*按键*> | 是 | String | 需要获取其值的键的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | 包含与指定键匹配的所有值的数组 |
||||

*示例*

此示例基于指定操作的表单数据或表单编码输出中的“Subject”键的值创建数组。

```
formDataMultiValues('Send_an_email', 'Subject')
```

并在数组中返回主题文本，例如：`["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

返回一个与操作的*窗体数据*或*表单编码*的输出中的键名匹配的单个值。
如果此函数找到多个匹配项，此函数将引发错误。

```
formDataValue('<actionName>', '<key>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 是 | String | 其输出中具有所需键值的操作 |
| <*按键*> | 是 | String | 需要获取其值的键的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*键-值*> | String | 指定的键中的值  |
||||

*示例*

此示例基于指定操作的表单数据或表单编码输出中的“Subject”键的值创建字符串。

```
formDataValue('Send_an_email', 'Subject')
```

并以字符串形式返回主题文本，例如：`"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

将数字作为基于指定格式的字符串返回。

```text
formatNumber(<number>, <format>, <locale>?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*多种*> | 是 | Integer 或 Double | 要设置格式的值。 |
| <*形式*> | 是 | String | 一个复合格式字符串，指定要使用的格式。 有关支持的数字格式字符串，请参阅支持的[标准数字格式字符串](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings) `number.ToString(<format>, <locale>)`。 |
| <*本地*> | 否 | String | 要用于的支持的区域设置`number.ToString(<format>, <locale>)`。 如果未指定，默认值为 `en-us`。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*格式化数字*> | String | 指定的数字作为指定的格式的字符串。 可以将此返回值强制转换为`int`或`float`。 |
||||

*示例 1*

假设要设置数字`1234567890`的格式。 此示例将该数字的格式设置为字符串 "1234567890.00"。

```
formatNumber(1234567890, '{0:0,0.00}', 'en-us')
```

* 示例 2 "

假设要设置数字`1234567890`的格式。 此示例将数字的格式设置为字符串 "1.234.567.890，00"。

```
formatNumber(1234567890, '{0:0,0.00}', 'is-is')
```

*示例 3*

假设要设置数字`17.35`的格式。 此示例将数字的格式设置为字符串 "$17.35"。

```
formatNumber(17.36, 'C2')
```

*示例4*

假设要设置数字`17.35`的格式。 此示例将数字的格式设置为字符串 "17，35 kr"。

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

返回当前时间戳加上指定的时间单位。

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*间隔*> | 是 | Integer | 要添加的指定时间单位数 |
| <*timeUnit*> | 是 | String | 要与 *interval* 一起使用的时间单位：“Second”、“Minute”、“Hour”、“Day”、“Week”、“Month”、“Year” |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 当前时间戳加上指定的时间单位数 |
||||

*示例 1*

假设当前时间戳是“2018-03-01T00:00:00.0000000Z”。
此示例将 5 天加到该时间戳：

```
getFutureTime(5, 'Day')
```

并返回以下结果：`"2018-03-06T00:00:00.0000000Z"`

*示例 2*

假设当前时间戳是“2018-03-01T00:00:00.0000000Z”。
此示例加上 5 天，并将结果转换为“D”格式：

```
getFutureTime(5, 'Day', 'D')
```

并返回以下结果：`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

返回当前时间戳减去指定的时间单位。

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*间隔*> | 是 | Integer | 要减去的指定时间单位数 |
| <*timeUnit*> | 是 | String | 要与 *interval* 一起使用的时间单位：“Second”、“Minute”、“Hour”、“Day”、“Week”、“Month”、“Year” |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 当前时间戳减去指定的时间单位数 |
||||

*示例 1*

假设当前时间戳是“2018-02-01T00:00:00.0000000Z”。
此示例从该时间戳减去 5 天：

```
getPastTime(5, 'Day')
```

并返回以下结果：`"2018-01-27T00:00:00.0000000Z"`

*示例 2*

假设当前时间戳是“2018-02-01T00:00:00.0000000Z”。
此示例减去 5 天，并将结果转换为“D”格式：

```
getPastTime(5, 'Day', 'D')
```

并返回以下结果：`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

检查第一个值是否大于第二个值。
如果第一个值大，则返回 true；如果第一个值小，则返回 false。

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | 整数、浮点数或字符串 | 要检查是否大于第二个值的第一个值。 |
| <*compareTo*> | 是 | 分别为整数、浮点数或字符串 | 比较值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当第一个值大于第二个值时返回 true。 当第一个值等于或小于第二个值时返回 false。 |
||||

*示例*

这些示例检查第一个值是否大于第二个值：

```
greater(10, 5)
greater('apple', 'banana')
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

检查第一个值是否大于或等于第二个值。
当第一个值大于或等于第二个值时返回 true，当第一个值小于第二个值时返回 false。

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | 整数、浮点数或字符串 | 要检查是否大于或等于第二个值的第一个值 |
| <*compareTo*> | 是 | 分别为整数、浮点数或字符串 | 比较值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当第一个值大于或等于第二个值时返回 true。 当第一个值小于第二个值时返回 false。 |
||||

*示例*

这些示例检查第一个值是否大于或等于第二个值：

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="guid"></a>

### <a name="guid"></a>GUID

生成一个字符串形式的全局唯一标识符 (GUID)，例如“c2ecc88d-88c8-4096-912c-d6f2e2b138ce”：

```
guid()
```

此外，还可以为 GUID 指定与默认格式“D”（由连字符分隔的 32 位数字）不同的格式。

```
guid('<format>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*形式*> | 否 | String | 表示返回的 GUID 的单一[格式说明符](https://msdn.microsoft.com/library/97af8hh4)。 默认情况下，格式为“D”，但可以使用“N”、“D”、“B”、“P”或“X”。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*GUID-值*> | String | 随机生成的 GUID |
||||

*示例*

此示例生成相同的 GUID，但它是 32 位的，以连字符分隔并括在括号中：

```
guid('P')
```

并返回以下结果：`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

检查表达式为 true 还是 false。 根据结果返回指定的值。 按从左至右的顺序计算参数。

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*表达式*> | 是 | 布尔 | 要检查的表达式 |
| <*valueIfTrue*> | 是 | 任意 | 当表达式为 true 时要返回的值 |
| <*valueIfFalse*> | 是 | 任意 | 当表达式为 false 时要返回的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*指定的-返回值*> | 任意 | 根据表达式为 true 或 false 返回的指定值 |
||||

*示例*

此示例返回 `"yes"`，因为指定的表达式返回 true。
否则，此示例返回 `"no"`：

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

返回子字符串的起始位置或索引值。
此函数不区分大小写，并且索引从数字 0 开始。

```
indexOf('<text>', '<searchText>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 包含要查找的子字符串的字符串 |
| <*searchText*> | 是 | String | 要查找的子字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*索引-值*>| Integer | 指定的子字符串的起始位置或索引值。 <p>如果未找到该字符串，则返回数字 -1。 |
||||

*示例*

此示例查找“hello world”字符串中的“world”子字符串的起始索引值：

```
indexOf('hello world', 'world')
```

并返回以下结果：`6`

<a name="int"></a>

### <a name="int"></a>int

返回字符串的整数版本。

```
int('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*整数-结果*> | Integer | 指定的字符串的整数版本 |
||||

*示例*

此示例创建字符串“10”的整数版本：

```
int('10')
```

并返回以下结果：`10`

<a name="item"></a>

### <a name="item"></a>item

当在针对数组的重复操作中使用时，返回在操作的当前迭代过程中数组中的当前项。
还可以获取该项的属性中的值。

```
item()
```

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*当前数组-项*> | 任意 | 返回在操作的当前迭代中数组中的当前项 |
||||

*示例*

此示例在 for-each 循环的当前迭代中从“Send_an_email”操作的当前消息中获取 `body` 元素：

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

返回 for-each 循环中每个周期的当前项。
请在 for-each 循环中使用此函数。

```
items('<loopName>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | 是 | String | for-each 循环的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*内容*> | 任意 | 指定的 for-each 循环中当前周期中的项 |
||||

*示例*

此示例获取指定 for-each 循环的当前项：

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

位于 Until 循环中时，返回当前迭代的索引值。 可以在嵌套式的 Until 循环中使用该函数。 

```
iterationIndexes('<loopName>')
```

| 参数 | 必须 | 类型 | 说明 | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | 是 | String | Until 循环的名称 | 
||||| 

| 返回值 | 类型 | 说明 | 
| ------------ | ---- | ----------- | 
| <*编入*> | Integer | 指定的 Until 循环中当前迭代的索引值 | 
|||| 

*示例* 

此示例创建一个计数器变量，在 Until 循环中每迭代一次该变量就会递增 1，直至计数器值达到 5。 此示例还创建一个变量，用于跟踪每次迭代的当前索引。 在 Until 循环中，在每次迭代时，此示例会递增计数器，然后将计数器值指定给当前索引值，然后再递增计数器。 在循环中，此示例使用`iterationIndexes`函数引用当前迭代索引：

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

返回字符串或 XML 的 JavaScript 对象表示法 (JSON) 类型的值或对象。

```
json('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | 字符串或 XML | 要转换的字符串或 XML |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*JSON-结果*> | JSON 本机类型或对象 | 指定的字符串或 XML 的 JSON 本机类型的值或对象。 如果字符串为 null，则此函数返回一个空对象。 |
||||

*示例 1*

此示例将以下字符串转换为 JSON 值：

```
json('[1, 2, 3]')
```

并返回以下结果：`[1, 2, 3]`

*示例 2*

此示例将以下字符串转换为 JSON：

```
json('{"fullName": "Sophia Owen"}')
```

并返回以下结果：

```
{
  "fullName": "Sophia Owen"
}
```

*示例 3*

此示例将以下 XML 转换为 JSON：

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

并返回以下结果：

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>交集

返回其中仅包含指定集合的共有项的一个集合。**
某个项必须出现在传递给此函数的所有集合中才会出现在结果中。
如果一个或多个项具有相同的名称，则具有该名称的最后一项将出现在结果中。

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | 是 | 数组或对象，但不能为两者 | 仅需从中获取共有项的各个集合** |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*常用项*> | 分别为数组或对象 | 其中仅包含指定集合的共有项的一个集合 |
||||

*示例*

此示例查找以下数组中的共有项：

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

并返回“仅”** 包含这些项的数组：`[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

返回一个字符串，它包含某个数组中的所有项并且以分隔符分隔每个字符。**

```
join([<collection>], '<delimiter>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*集合*> | 是 | Array | 包含要联接的项的数组 |
| <*后面*> | 是 | String | 出现在结果字符串中的每个字符之间的分隔符 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*分隔符><*char2*char2><*分隔符*> | String | 基于指定数组中的所有项创建的结果字符串 |
||||

*示例*

此示例基于以下数组中的所有项创建字符串，并且以指定的字符作为分隔符：

```
join(createArray('a', 'b', 'c'), '.')
```

并返回以下结果：`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

返回集合中的最后一个项。

```
last('<collection>')
last([<collection>])
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*集合*> | 是 | 字符串或数组 | 要在其中查找最后一项的集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*最后收集-项*> | 分别为字符串或数组 | 集合中的最后一项 |
||||

*示例*

这些示例查找以下集合中的最后一项：

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

并返回以下结果：

* 第一个示例：`"d"`
* 第二个示例：`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

返回最后一次出现的子字符串的起始位置或索引值。
此函数不区分大小写，并且索引从数字 0 开始。

```
lastIndexOf('<text>', '<searchText>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 包含要查找的子字符串的字符串 |
| <*searchText*> | 是 | String | 要查找的子字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*结尾-索引-值*> | Integer | 最后一次出现的指定子字符串的起始位置或索引值。 <p>如果未找到该字符串，则返回数字 -1。 |
||||

*示例*

此示例查找“hello world”字符串中最后一次出现的“world”子字符串的起始索引值：

```
lastIndexOf('hello world', 'world')
```

并返回以下结果：`6`

<a name="length"></a>

### <a name="length"></a>length

返回集合中的项数。

```
length('<collection>')
length([<collection>])
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*集合*> | 是 | 字符串或数组 | 包含要计数的项的集合 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*长度或计数*> | Integer | 集合中的项数 |
||||

*示例*

这些示例对以下集合中的项数进行计数：

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

并返回以下结果：`4`

<a name="less"></a>

### <a name="less"></a>less

检查第一个值是否小于第二个值。
如果第一个值小，则返回 true；如果第一个值大，则返回 false。

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | 整数、浮点数或字符串 | 要检查是否小于第二个值的第一个值 |
| <*compareTo*> | 是 | 分别为整数、浮点数或字符串 | 比较项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当第一个值小于第二个值时返回 true。 当第一个值等于或大于第二个值时返回 false。 |
||||

*示例*

这些示例检查第一个值是否小于第二个值。

```
less(5, 10)
less('banana', 'apple')
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

检查第一个值是否小于或等于第二个值。
当第一个值小于或等于第二个值时返回 true，当第一个值大时返回 false。

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | 整数、浮点数或字符串 | 要检查是否小于或等于第二个值的第一个值 |
| <*compareTo*> | 是 | 分别为整数、浮点数或字符串 | 比较项 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false  | 布尔 | 当第一个值小于或等于第二个值时返回 true。 当第一个值大于第二个值时返回 false。 |
||||

*示例*

这些示例检查第一个值是否小于或等于第二个值。

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

并返回以下结果：

* 第一个示例：`true`
* 第二个示例：`false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

返回调用某个触发器或操作的“回调 URL”。
此函数仅对适用于 **HttpWebhook** 和 **ApiConnectionWebhook** 连接器类型的触发器和操作起作用，对 **Manual**、**Recurrence**、**HTTP** 和 **APIConnection** 类型不起作用。

```
listCallbackUrl()
```

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*回拨-URL*> | String | 触发器或操作的回调 URL |
||||

*示例*

此示例显示了此函数可能会返回的示例回调 URL：

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

返回列表中或包括两端数字在内的数组中的最大值。

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | 是 | 整数、浮点数或两者 | 需要从中获取最大值的数字集 |
| [<*数字 1*>，<*数字 2*>，...] | 是 | 数组 - 整数、浮点数或两者 | 需要从中获取最大值的数字数组 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*最大值*> | 整数或浮点数 | 指定的数组或数字集中的最大值 |
||||

*示例*

这些示例获取数字集和数组中的最大值：

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

并返回以下结果：`3`

<a name="min"></a>

### <a name="min"></a>min

返回一组数字或数组中的最小值。

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | 是 | 整数、浮点数或两者 | 需要从中获取最小值的数字集 |
| [<*数字 1*>，<*数字 2*>，...] | 是 | 数组 - 整数、浮点数或两者 | 需要从中获取最小值的数字数组 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*最小值*> | 整数或浮点数 | 指定的数字集或指定的数组中的最小值 |
||||

*示例*

这些示例获取数字集和数组中的最小值：

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

并返回以下结果：`1`

<a name="mod"></a>

### <a name="mod"></a>mod

返回将两个数字相除后的余数。
若要获取整数结果，请参阅 [div()](#div)。

```
mod(<dividend>, <divisor>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*被除数*> | 是 | 整数或浮点数 | 要用作 *divisor* 的被除数的数字 |
| <*除数*> | 是 | 整数或浮点数 | 用作 *dividend* 的除数的数字，但不能为 0。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*取模-结果*> | 整数或浮点数 | 将第一个数字除以第二个数字后得到的余数 |
||||

*示例*

此示例将第一个数字除以第二个数字：

```
mod(3, 2)
```

并返回以下结果：`1`

<a name="mul"></a>

### <a name="mul"></a>mul

返回将两个数字相乘得到的乘积。

```
mul(<multiplicand1>, <multiplicand2>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | 是 | 整数或浮点数 | 要与 *multiplicand2* 相乘的数字 |
| <*multiplicand2*> | 是 | 整数或浮点数 | 要与 *multiplicand1* 相乘的数字 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*产品-结果*> | 整数或浮点数 | 将第一个数字乘以第二个数字后得到的乘积 |
||||

*示例*

这些示例将第一个数字乘以第二个数字：

```
mul(1, 2)
mul(1.5, 2)
```

并返回以下结果：

* 第一个示例：`2`
* 第二个示例：`3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

返回具有多个部分的操作输出中某个特定部分的正文。

```
multipartBody('<actionName>', <index>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 是 | String | 具有多部分输出的操作的名称 |
| <*编入*> | 是 | Integer | 所需部分的索引值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*大量*> | String | 指定部分的正文 |
||||

<a name="not"></a>

### <a name="not"></a>not

检查表达式是否为 false。
当表达式为 false 时返回 true，当表达式为 true 时返回 false。

```json
not(<expression>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*表达式*> | 是 | 布尔 | 要检查的表达式 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当表达式为 false 时返回 true。 当表达式为 true 时返回 false。 |
||||

*示例 1*

这些示例检查指定的表达式是否为 false：

```json
not(false)
not(true)
```

并返回以下结果：

* 第一个示例：表达式为 false，所以此函数返回 `true`。
* 第二个示例：表达式为 true，所以此函数返回 `false`。

*示例 2*

这些示例检查指定的表达式是否为 false：

```json
not(equals(1, 2))
not(equals(1, 1))
```

并返回以下结果：

* 第一个示例：表达式为 false，所以此函数返回 `true`。
* 第二个示例：表达式为 true，所以此函数返回 `false`。

<a name="or"></a>

### <a name="or"></a>或

检查是否至少一个表达式为 true。
当至少一个表达式为 true 时返回 true，当所有表达式均为 false 时返回 false。

```
or(<expression1>, <expression2>, ...)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | 是 | 布尔 | 要检查的表达式 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false | 布尔 | 当至少一个表达式为 true 时返回 true。 当所有表达式均为 false 时返回 false。 |
||||

*示例 1*

这些示例检查是否至少一个表达式为 true：

```json
or(true, false)
or(false, false)
```

并返回以下结果：

* 第一个示例：至少一个表达式为 true，所以此函数返回 `true`。
* 第二个示例：两个表达式均为 false，所以此函数返回 `false`。

*示例 2*

这些示例检查是否至少一个表达式为 true：

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

并返回以下结果：

* 第一个示例：至少一个表达式为 true，所以此函数返回 `true`。
* 第二个示例：两个表达式均为 false，所以此函数返回 `false`。

<a name="outputs"></a>

### <a name="outputs"></a>outputs

返回操作在运行时的输出。 使用此函数，而不是在逻辑应用设计器中解析为 `outputs()` 的 `actionOutputs()`。 虽然这两个函数的工作方式相同，但首选 `outputs()`。

```
outputs('<actionName>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 是 | String | 所需的操作输出的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | -----| ----------- |
| <*输出*> | String | 指定操作的输出 |
||||

*示例*

此示例获取 Twitter 操作 `Get user` 的输出：

```
outputs('Get_user')
```

并返回以下结果：

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>参数

返回工作流定义中描述的参数的值。

```
parameters('<parameterName>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | 是 | String | 需要获取其值的参数的名称。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*参数-值*> | 任意 | 指定的参数的值 |
||||

*示例*

假设具有以下 JSON 值：

```json
{
  "fullName": "Sophia Owen"
}
```

此示例获取指定参数的值：

```
parameters('fullName')
```

并返回以下结果：`"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

返回指定范围（仅包括起端在内）中的一个随机整数。

```
rand(<minValue>, <maxValue>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | 是 | Integer | 范围中的最小整数 |
| <*Timespan.maxvalue*> | 是 | Integer | 此函数可以返回的范围中的最大整数之后的整数 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*随机结果*> | Integer | 从指定范围中返回的随机整数 |
||||

*示例*

此示例从指定范围内获取一个随机整数，不包括最大值：

```
rand(1, 5)
```

并返回下列数字之一作为结果：`1`、`2`、`3` 或 `4`

<a name="range"></a>

### <a name="range"></a>range

返回以指定整数开头的一个整数数组。

```
range(<startIndex>, <count>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | 是 | Integer | 作为数组开头的第一项的整数值 |
| <*计*> | 是 | Integer | 数组中的整数个数 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | 从指定索引开始的整数组成的数组 |
||||

*示例*

此示例创建一个整数数组，从指定索引开始，并包含指定数目的整数：

```
range(1, 4)
```

并返回以下结果：`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

将子字符串替换为指定字符串，并返回结果字符串。 此函数区分大小写。

```
replace('<text>', '<oldText>', '<newText>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 包含要替换的子字符串的字符串 |
| <*oldText*> | 是 | String | 要替换的子字符串 |
| <*newText*> | 是 | String | 替换字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-文本*> | String | 替换子字符串后得到的更新后字符串 <p>如果未找到子字符串，则返回原始字符串。 |
||||

*示例*

此示例查找“the old string”中的“old”子字符串并将“old”替换为“new”：

```
replace('the old string', 'old', 'new')
```

并返回以下结果：`"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

从对象中删除属性，并返回更新后的对象。 如果尝试删除的属性不存在，此函数会返回原始对象。

```
removeProperty(<object>, '<property>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*对象*> | 是 | Object | 要从中删除属性的 JSON 对象 |
| <*知识产权*> | 是 | String | 要删除的属性的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-对象*> | Object | 不具有指定属性的更新后 JSON 对象 |
||||

若要从现有属性中删除子属性，请使用以下语法：

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*对象*> | 是 | Object | 要删除其属性的 JSON 对象 |
| <*父属性*> | 是 | String | 要删除其子属性的父属性的名称 |
| <*子属性*> | 是 | String | 要删除的子属性的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-对象*> | Object | 删除了其子属性的已更新 JSON 对象 |
||||

*示例 1*

此示例从 JSON 对象（此对象通过 [JSON()](#json) 函数从字符串转换为 JSON）中删除 `middleName` 属性，并返回更新后的对象：

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

下面是当前的 JSON 对象：

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

下面是已更新的 JSON 对象：

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*示例 2*

此示例从 JSON 对象（此对象通过 [JSON()](#json) 函数从字符串转换为 JSON）中的 `customerName` 父属性中删除 `middleName` 子属性，并返回更新后的对象：

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

下面是当前的 JSON 对象：

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

下面是已更新的 JSON 对象：

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

返回指定的范围受限操作（例如 `For_each`、`Until` 或 `Scope` 操作）中所有操作的输入和输出。 此函数用于从失败的操作中返回结果，方便你诊断和处理异常。 有关详细信息，请参阅[获取失败的上下文和结果](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)。

```
result('<scopedActionName>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | 是 | String | 将会从其返回所有内部操作的输入和输出的范围受限操作的名称。 |
||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*array 对象*> | 数组对象 | 一个数组，其中包含显示在指定的范围受限操作中的每个操作的输入和输出数组 |
||||

*示例*

此示例返回 HTTP 操作的每个迭代的输入和输出，该操作位于 `For_each` 循环中，在 `Compose` 操作中使用 `result()` 函数：

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

下面是示例返回的数组可能的外观，其中的外部 `outputs` 对象包含 `For_each` 操作中每个操作迭代的输入和输出。

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

设置 JSON 对象的属性值并返回更新的对象。 如果尝试设置的属性不存在，则会为对象添加该属性。 若要添加新属性，请使用 [addProperty()](#addProperty) 函数。

```
setProperty(<object>, '<property>', <value>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*对象*> | 是 | Object | 要设置其属性的 JSON 对象 |
| <*知识产权*> | 是 | String | 要设置的现有属性或新属性的名称 |
| <*负值*> | 是 | 任意 | 要为指定属性设置的值 |
|||||

若要在子对象中设置子属性，请改用嵌套式 `setProperty()` 调用。 否则，函数仅返回子对象作为输出。

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*对象*> | 是 | Object | 要设置其属性的 JSON 对象 |
| <*父属性*> | 是 | String | 要设置其子属性的父属性的名称 |
| <*子属性*> | 是 | String | 要设置的子属性的名称 |
| <*负值*> | 是 | 任意 | 要为指定属性设置的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-对象*> | Object | 设置了其属性的更新后 JSON 对象 |
||||

*示例 1*

此示例在 JSON 对象中设置 `surName` 属性，可通过 [JSON()](#json) 函数将其从字符串转换为 JSON。 该函数将指定的值分配给属性，并返回更新后的对象：

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

下面是当前的 JSON 对象：

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

下面是已更新的 JSON 对象：

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*示例 2*

此示例为 JSON 对象中的 `customerName` 父属性设置 `surName` 子属性，可通过 [JSON()](#json) 函数将其从字符串转换为 JSON。 该函数将指定的值分配给属性，并返回更新后的对象：

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

下面是当前的 JSON 对象：

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

下面是已更新的 JSON 对象：

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>skip

删除集合开头的项，并返回所有其他项。**

```
skip([<collection>], <count>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*集合*> | 是 | Array | 要删除其项的集合 |
| <*计*> | 是 | Integer | 要从开头删除的项数（一个正整数） |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | 删除指定项后得到的更新后的集合 |
||||

*示例*

此示例从指定数组的开头删除一个项（数字 0）：

```
skip(createArray(0, 1, 2, 3), 1)
```

并返回包含剩余项的以下数组：`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

根据原始字符串中指定的分隔符字符，返回一个包含子字符串（以逗号分隔）的数组。

```
split('<text>', '<delimiter>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 根据原始字符串中指定的分隔符将分隔成子字符串的字符串 |
| <*后面*> | 是 | String | 原始字符串中用作分隔符的字符 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | 一个数组，其中包含从原始字符串返回以逗号分隔的子字符串 |
||||

*示例*

此示例根据作为分隔符的指定字符，从指定字符串创建具有子字符串的数组：

```
split('a_b_c', '_')
```

并将此数组作为以下结果返回：`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

返回时间戳中的天的开始时间。

```
startOfDay('<timestamp>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 指定的时间戳，但从该天的零小时标记开始 |
||||

*示例*

此示例查找以下时间戳中的天的开始时间：

```
startOfDay('2018-03-15T13:30:30Z')
```

并返回以下结果：`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

返回时间戳中的小时的开始时间。

```
startOfHour('<timestamp>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 指定的时间戳，但从该小时的零分钟标记开始 |
||||

*示例*

此示例查找以下时间戳中的小时的开始时间：

```
startOfHour('2018-03-15T13:30:30Z')
```

并返回以下结果：`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

返回时间戳中的月份的开始时间。

```
startOfMonth('<timestamp>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 指定的时间戳，但从该月第一天的零小时标记开始 |
||||

*示例*

此示例查找以下时间戳中月份的开始时间：

```
startOfMonth('2018-03-15T13:30:30Z')
```

并返回以下结果：`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

检查字符串是否以特定的子字符串开头。
当找到该子字符串时返回 true，找不到时返回 false。
此函数不区分大小写。

```
startsWith('<text>', '<searchText>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 要检查的字符串 |
| <*searchText*> | 是 | String | 要查找的起始字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| true 或 false  | 布尔 | 当找到起始子字符串时返回 true。 找不到时返回 false。 |
||||

*示例 1*

此示例检查“hello world”字符串是否以“hello”子字符串开头：

```
startsWith('hello world', 'hello')
```

并返回以下结果：`true`

*示例 2*

此示例检查“hello world”字符串是否以“greetings”子字符串开头：

```
startsWith('hello world', 'greetings')
```

并返回以下结果：`false`

<a name="string"></a>

### <a name="string"></a>string

返回值的字符串版本。

```
string(<value>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | 任意 | 要转换的值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*字符串-值*> | String | 指定的值的字符串版本 |
||||

*示例 1*

此示例创建以下数字的字符串版本：

```
string(10)
```

并返回以下结果：`"10"`

*示例 2*

此示例为指定的 JSON 对象创建字符串并使用反斜杠字符 (\\) 作为双引号 (") 的转义字符。

```
string( { "name": "Sophie Owen" } )
```

并返回以下结果：`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

返回第一个数字减去第二个数字得到的结果。

```
sub(<minuend>, <subtrahend>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*被减数*> | 是 | 整数或浮点数 | 要从中减去 *subtrahend* 的数字 |
| <*减数*> | 是 | 整数或浮点数 | 要从 *minuend* 中减去的数字 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*输出*> | 整数或浮点数 | 第一个数字减去第二个数字得到的结果 |
||||

*示例*

此示例从第一个数字中减去第二个数字：

```
sub(10.3, .3)
```

并返回以下结果：`10`

<a name="substring"></a>

### <a name="substring"></a>substring

返回字符串中的字符，从指定的位置或索引开始。
索引值从数字 0 开始。

```
substring('<text>', <startIndex>, <length>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 所需字符所在的字符串 |
| <*startIndex*> | 是 | Integer | 一个等于或大于 0 的正数，需将其用作起始位置或索引值 |
| <*长短*> | 是 | Integer | 希望子字符串中具有的字符数（正数） |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*子字符串-结果*> | String | 包含指定字符数的子字符串，从源字符串中的指定索引位置开始 |
||||

*示例*

此示例基于指定的字符串从索引值 6 开始创建一个五字符的子字符串：

```
substring('hello world', 6, 5)
```

并返回以下结果：`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

从时间戳中减去一定数目的时间单位。
另请参阅 [getPastTime](#getPastTime)。

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 包含时间戳的字符串 |
| <*间隔*> | 是 | Integer | 要减去的指定时间单位数 |
| <*timeUnit*> | 是 | String | 要与 *interval* 一起使用的时间单位：“Second”、“Minute”、“Hour”、“Day”、“Week”、“Month”、“Year” |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已更新-时间戳*> | String | 时间戳减去指定的时间单位数 |
||||

*示例 1*

此示例从以下时间戳中减去 1 天：

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

并返回以下结果：`"2018-01-01T00:00:00:0000000Z"`

*示例 2*

此示例从以下时间戳中减去 1 天：

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

并使用可选的“D”格式返回以下结果：`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

返回集合开头的项。

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*集合*> | 是 | 字符串或数组 | 所需项所在的集合 |
| <*计*> | 是 | Integer | 从开头算起所需的项数（一个正整数） |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*subset*> or [<*subset*>] | 分别为字符串或数组 | 一个字符串或数组，其中包含来自原始集合开头的指定数目的项 |
||||

*示例*

这些示例从以下集合的开头获取指定的项数：

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

并返回以下结果：

* 第一个示例：`"abc"`
* 第二个示例：`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

返回指定时间戳的 `ticks` 属性值。
一个“时钟周期”** 是 100 纳秒时间间隔。

```
ticks('<timestamp>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*标志*> | 是 | String | 时间戳的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*刻度-数字*> | Integer | 从指定的时间戳算起已过去的时钟周期数 |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

返回小写格式的字符串。 如果字符串中的某个字符没有对应的小写版本，则该字符在返回的字符串中保持原样。

```
toLower('<text>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 要以小写格式返回的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*小写文本*> | String | 采用小写格式的原始字符串 |
||||

*示例*

此示例将以下字符串转换为小写：

```
toLower('Hello World')
```

并返回以下结果：`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

返回大写格式的字符串。 如果字符串中的某个字符没有对应的大写版本，则该字符在返回的字符串中保持原样。

```
toUpper('<text>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 要以大写格式返回的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*大写文本*> | String | 采用大写格式的原始字符串 |
||||

*示例*

此示例将以下字符串转换为大写：

```
toUpper('Hello World')
```

并返回以下结果：`"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>触发器

返回触发器在运行时的输出，或者来自其他 JSON 名称和值对的值，可以将其分配给表达式。

* 在触发器的输入中，此函数返回来自上次执行的输出。

* 在触发器的条件中，此函数返回来自当前执行的输出。

默认情况下，此函数引用整个触发器对象，但是也可以指定你需要其值的属性。
此外，此函数还提供了速记版本，请参阅 [triggerOutputs()](#triggerOutputs) 和 [triggerBody()](#triggerBody)。

```
trigger()
```

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*触发器-输出*> | String | 触发器在运行时的输出 |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

返回触发器在运行时的 `body` 输出。
`trigger().outputs.body` 的速记。
请参阅 [trigger()](#trigger)。

```
triggerBody()
```

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*触发器-正文-输出*> | String | 触发器的 `body` 输出 |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

返回与触发器的“表单数据”或“表单编码”输出中的键名称匹配的值组成的数组。****

```
triggerFormDataMultiValues('<key>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*按键*> | 是 | String | 需要获取其值的键的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | 包含与指定键匹配的所有值的数组 |
||||

*示例*

此示例基于 RSS 触发器的表单数据或表单编码输出中的“feedUrl”键值创建数组：

```
triggerFormDataMultiValues('feedUrl')
```

并将以下数组返回为示例结果：`["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

返回一个字符串，其中包含与触发器的“表单数据”或“表单编码”输出中的键名称匹配的单个值。****
如果此函数找到多个匹配项，此函数将引发错误。

```
triggerFormDataValue('<key>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*按键*> | 是 | String | 需要获取其值的键的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*键-值*> | String | 指定的键中的值 |
||||

*示例*

此示例基于 RSS 触发器的表单数据或表单编码输出中的“feedUrl”键值创建字符串：

```
triggerFormDataValue('feedUrl')
```

并将以下字符串返回为示例结果：`"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

返回具有多个部分的触发器输出中某个特定部分的正文。

```
triggerMultipartBody(<index>)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*编入*> | 是 | Integer | 所需部分的索引值 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*大量*> | String | 触发器多部分输出中指定部分的正文 |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

返回触发器在运行时的输出，或者来自其他 JSON 名称和值对的值。
`trigger().outputs` 的速记。
请参阅 [trigger()](#trigger)。

```
triggerOutputs()
```

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*触发器-输出*> | String | 触发器在运行时的输出  |
||||

<a name="trim"></a>

### <a name="trim"></a>trim

从字符串中删除前导和尾随空格，并返回更新后的字符串。

```
trim('<text>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*全文*> | 是 | String | 包含要删除的前导和尾随空格的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | 原始字符串的更新后版本，其中不含前导或尾随空格 |
||||

*示例*

此示例从字符串“Hello World”中删除前导和尾随空格：

```
trim(' Hello World  ')
```

并返回以下结果：`"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

返回一个集合，其中包含指定集合中的所有项。**
某个项只要出现在传递给此函数的任一集合中便会出现在结果中。 如果一个或多个项具有相同的名称，则具有该名称的最后一项将出现在结果中。

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | 是 | 数组或对象，但不能为两者 | 需要从中获取所有项的各个集合** |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | 分别为数组或对象 | 一个集合，其中包含指定集合中的所有项 - 无重复项 |
||||

*示例*

此示例获取以下集合中的“所有”** 项：

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

并返回以下结果：`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

通过将 URL 不安全字符替换为转义字符来返回字符串的统一资源标识符 (URI) 编码版本。
请使用此函数而非 [encodeUriComponent()](#encodeUriComponent)。
虽然这两个函数的工作方式相同，但首选 `uriComponent()`。

```
uriComponent('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换为 URI 编码格式的字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*编码-uri*> | String | 带有转义字符的 URI 编码字符串 |
||||

*示例*

此示例创建以下字符串的 URI 编码版本：

```
uriComponent('https://contoso.com')
```

并返回以下结果：`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

返回统一资源标识符 (URI) 组件的二进制版本。

```
uriComponentToBinary('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换的 URI 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*二进制编码-uri*> | String | URI 编码字符串的二进制版本。 二进制内容是 base64 编码的，并且由 `$content` 表示。 |
||||

*示例*

此示例创建以下 URI 编码字符串的二进制版本：

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

并返回以下结果：

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

返回统一资源标识符 (URI) 编码字符串的字符串版本，有效地对 URI 编码字符串进行解码。

```
uriComponentToString('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要解码的 URI 编码字符串 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*已解码-uri*> | String | URI 编码字符串的解码后的版本 |
||||

*示例*

此示例创建以下 URI 编码字符串的解码后字符串版本：

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

并返回以下结果：`"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

返回统一资源标识符 (URI) 的 `host` 值。

```
uriHost('<uri>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*oma-uri*> | 是 | String | 需要获取其 `host` 值的 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*主机-值*> | String | 指定的 URI 的 `host` 值 |
||||

*示例*

此示例查找以下 URI 的 `host` 值：

```
uriHost('https://www.localhost.com:8080')
```

并返回以下结果：`"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

返回统一资源标识符 (URI) 的 `path` 值。

```
uriPath('<uri>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*oma-uri*> | 是 | String | 需要获取其 `path` 值的 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*路径-值*> | String | 指定的 URI 的 `path` 值。 如果 `path` 不含值，则返回“/”字符。 |
||||

*示例*

此示例查找以下 URI 的 `path` 值：

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

并返回以下结果：`"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

返回统一资源标识符 (URI) 的 `path` 和 `query` 值。

```
uriPathAndQuery('<uri>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*oma-uri*> | 是 | String | 需要获取其 `path` 和 `query` 值的 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*路径查询-值*> | String | 指定的 URI 的 `path` 和 `query` 值。 如果 `path` 未指定值，则返回“/”字符。 |
||||

*示例*

此示例查找以下 URI 的 `path` 和 `query` 值：

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

并返回以下结果：`"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

返回统一资源标识符 (URI) 的 `port` 值。

```
uriPort('<uri>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*oma-uri*> | 是 | String | 需要获取其 `port` 值的 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*端口-值*> | Integer | 指定的 URI 的 `port` 值。 如果 `port` 未指定值，则返回协议的默认端口。 |
||||

*示例*

此示例查找以下 URI 的 `port` 值：

```
uriPort('http://www.localhost:8080')
```

并返回以下结果：`8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

返回统一资源标识符 (URI) 的 `query` 值。

```
uriQuery('<uri>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*oma-uri*> | 是 | String | 需要获取其 `query` 值的 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*查询-值*> | String | 指定的 URI 的 `query` 值 |
||||

*示例*

此示例查找以下 URI 的 `query` 值：

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

并返回以下结果：`"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

返回统一资源标识符 (URI) 的 `scheme` 值。

```
uriScheme('<uri>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*oma-uri*> | 是 | String | 需要获取其 `scheme` 值的 URI |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*方案-值*> | String | 指定的 URI 的 `scheme` 值 |
||||

*示例*

此示例查找以下 URI 的 `scheme` 值：

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

并返回以下结果：`"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

返回当前时间戳。

```
utcNow('<format>')
```

另外，也可以使用 <*format*> 参数指定不同的格式。


| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*形式*> | 否 | String | [单一格式的说明符](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自定义格式的模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 时间戳的默认格式为[“o”](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddT:mm:ss:fffffffK)，这符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 标准并保留了时区信息。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*当前时间戳*> | String | 当前日期和时间 |
||||

*示例 1*

假设今天的当前时间为 2018 年 4 月 15 日下午 1:00:00。
此示例获取当前时间戳：

```
utcNow()
```

并返回以下结果：`"2018-04-15T13:00:00.0000000Z"`

*示例 2*

假设今天的当前时间为 2018 年 4 月 15 日下午 1:00:00。
此示例使用可选的“D”格式获取当前时间戳：

```
utcNow('D')
```

并返回以下结果：`"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variables

返回指定变量的值。

```
variables('<variableName>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | 是 | String | 需要获取其值的变量的名称 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*变量-值*> | 任意 | 指定的变量的值 |
||||

*示例*

假设“numItems”变量的当前值为 20。
此示例获取以下变量的整数值：

```
variables('numItems')
```

并返回以下结果：`20`

<a name="workflow"></a>

### <a name="workflow"></a>工作流

返回运行期间有关工作流本身的所有详细信息。

```
workflow().<property>
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*知识产权*> | 否 | String | 需要获取其值的工作流属性的名称 <p>工作流对象应具有以下属性：**name**、**type**、**id**、**location** 和 **run**。 **run** 属性值也是一个对象，它具有以下属性：**name**、**type** 和 **id**。 |
|||||

*示例*

此示例返回工作流的当前运行的名称：

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

返回包含 JSON 对象的字符串的 XML 版本。

```
xml('<value>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*负值*> | 是 | String | 要转换的包含 JSON 对象的字符串 <p>该 JSON 对象必须只有一个根属性，该属性不能是数组。 <br>请使用反斜杠字符 (\\) 作为双引号 (") 的转义字符。 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*xml-版本*> | Object | 指定的字符串或 JSON 对象的编码 XML |
||||

*示例 1*

此示例创建包含 JSON 对象的以下字符串的 XML 版本：

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

并返回以下结果 XML：

```xml
<name>Sophia Owen</name>
```

*示例 2*

假设具有以下 JSON 对象：

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

此示例创建包含以下 JSON 对象的字符串的 XML：

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

并返回以下结果 XML：

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

检查 XML 中是否存在与 XPath（XML 路径语言）表达式匹配的节点或值，并返回匹配的节点或值。 XPath 表达式或单纯的“XPath”有助于在 XML 文档结构中导航，以便可以在 XML 内容中选择节点或计算值。

```
xpath('<xml>', '<xpath>')
```

| 参数 | 必须 | 类型 | 说明 |
| --------- | -------- | ---- | ----------- |
| <*xml*> | 是 | 任意 | 要在其中搜索与 XPath 表达式值匹配的节点或值的 XML 字符串 |
| <*xpath*> | 是 | 任意 | 用来查找匹配的 XML 节点或值的 XPath 表达式 |
|||||

| 返回值 | 类型 | 说明 |
| ------------ | ---- | ----------- |
| <*xml-节点*> | XML | 一个 XML 节点，当只有单个节点与指定的 XPath 表达式匹配时 |
| <*负值*> | 任意 | 来自一个 XML 节点的值，当只有单个值与指定的 XPath 表达式匹配时 |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-或- </br>[<*value1*>, <*value2*>, ...] | Array | 一个数组，其中包含与指定的 XPath 表达式匹配的 XML 节点或值 |
||||

*示例 1*

此示例查找与指定参数中的 `<name></name>` 节点匹配的节点，并返回包含这些节点值的数组：

`xpath(xml(parameters('items')), '/produce/item/name')`

下面是参数：

* “items”字符串，其中包含以下 XML：

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  此示例使用 [parameters()](#parameters) 函数从“items”参数中获取 XML 字符串，但还必须使用 [xml()](#xml) 函数将该字符串转换为 XML 格式。

* 以下 XPath 表达式，它是作为字符串传递的：

  `"/produce/item/name"`

下面是结果数组，其中包含与 `<name></name` 匹配的节点：

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*示例 2*

在示例 1 的基础上，此示例查找与 `<count></count>` 节点匹配的节点并通过 `sum()` 函数添加这些节点值：

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

并返回以下结果：`30`

*示例 3*

对于此示例，两个表达式都查找与指定参数（其中通过命名空间包括了 XML）中的 `<location></location>` 节点匹配的节点。 

> [!NOTE]
>
> 如果正在代码视图中操作，请使用反斜杠字符（\\）对双引号（"）进行转义。 
> 例如，如果将表达式序列化为 JSON 字符串，则需要使用转义符。 
> 但是，如果使用的是逻辑应用设计器或表达式编辑器，则无需转义双引号，因为反斜杠字符会自动添加到基础定义，例如：
> 
> * 代码视图：`xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * 表达式编辑器：`xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> 以下示例适用于在表达式编辑器中输入的表达式。

* *表达式1*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *表达式2*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

下面是参数：

* 以下 XML，其中包括 XML 文档命名空间 `xmlns="http://contoso.com"`：

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* 以下任一 XPath 表达式：

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

下面是与 `<location></location>` 节点匹配的结果节点：

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*示例4*

在示例 3 的基础上，此示例在 `<location></location>` 节点中查找值：

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

并返回以下结果：`"Paris"`

## <a name="next-steps"></a>后续步骤

了解[工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)
