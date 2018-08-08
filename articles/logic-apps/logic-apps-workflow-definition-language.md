---
title: 工作流定义语言的架构引用 - Azure 逻辑应用 | Microsoft Docs
description: 使用工作流定义语言编写 Azure 逻辑应用的自定义工作流定义
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: reference
ms.date: 04/30/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 0ac191f1191357ecc7292d51b298567f7f4e4786
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391218"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Azure 逻辑应用中工作流定义语言的架构引用

使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)创建逻辑应用工作流时，工作流的基础定义将描述针对逻辑应用运行的实际逻辑。 此描述遵循工作流定义语言架构定义和验证的、使用 [JavaScript 对象表示法 (JSON)](https://www.json.org/) 的结构。 
  
## <a name="workflow-definition-structure"></a>工作流定义结构

工作流定义至少包含一个用于实例化逻辑应用的触发器，以及逻辑应用运行的一个或多个操作。 

下面是工作流定义的高级结构：  
  
```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```
  
| 元素 | 必选 | Description | 
|---------|----------|-------------| 
| 定义 | 是 | 工作流定义的起始元素 | 
| $schema | 仅当在外部引用工作流定义时才使用 | 描述工作流定义语言版本的 JSON 架构文件的位置。可在以下位置找到该文件： <p>`https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json`</p> |   
| contentVersion | 否 | 工作流定义的版本号，默认为“1.0.0.0”。 为了帮助在部署工作流时识别并确认正确的定义，请指定要使用的值。 | 
| parameters | 否 | 用于将数据传入工作流的一个或多个参数的定义 <p><p>参数数目上限：50 | 
| 触发器 | 否 | 用于实例化工作流的一个或多个触发器的定义 可以定义多个触发器，但只能使用工作流定义语言来定义，而不能通过逻辑应用设计器以可视方式进行定义。 <p><p>触发器数目上限：10 | 
| actions | 否 | 要在工作流运行时执行的一个或多个操作的定义 <p><p>操作数目上限：250 | 
| outputs | 否 | 从工作流运行返回的输出的定义 <p><p>输出数目上限：10 |  
|||| 

## <a name="parameters"></a>parameters

在 `parameters` 节中，定义逻辑应用在部署时用来接受输入的所有工作流参数。 部署时需要参数声明和参数值。 在其他工作流节中使用这些参数之前，请确保声明这些节中的所有参数。 

下面是参数定义的一般结构：  

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": { 
      "key": { 
        "name": "<key-value>"
      } 
    }
  }
},
```

| 元素 | 必选 | Type | Description |  
|---------|----------|------|-------------|  
| type | 是 | int、float、string、securestring、bool、array、JSON 对象、secureobject <p><p>**注意**：对于所有密码、密钥和机密，请使用 `securestring` 和 `secureobject` 类型，因为 `GET` 操作不会返回这些类型。 | 参数的类型 |
| defaultValue | 否 | 与 `type` 相同 | 在工作流实例化时未指定值的情况下使用的默认参数值 | 
| allowedValues | 否 | 与 `type` 相同 | 包含参数可接受的值的数组 |  
| metadata | 否 | JSON 对象 | 其他任何参数详细信息，例如，逻辑应用的名称或可读说明，或者 Visual Studio 或其他工具使用的设计时数据 |  
||||

## <a name="triggers-and-actions"></a>触发器和操作  

在工作流定义中，`triggers` 和 `actions` 节定义工作流执行期间发生的调用。 有关这些节的语法和详细信息，请参阅[工作流触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md)。
  
## <a name="outputs"></a>Outputs 

在 `outputs` 节中，定义工作流在完成运行时可以返回的数据。 例如，若要跟踪每次运行的特定状态或值，请指定工作流输出应返回该数据。 

> [!NOTE]
> 在响应来自服务 REST API 的传入请求时，请不要使用 `outputs`。 请改用 `Response` 操作类型。 有关详细信息，请参阅[工作流触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

下面是输出定义的一般结构： 

```json
"outputs": {
  "<key-name>": {  
    "type": "<key-type>",  
    "value": "<key-value>"  
  }
} 
```

| 元素 | 必选 | Type | Description | 
|---------|----------|------|-------------| 
| <*key-name*> | 是 | String | 输出返回值的密钥名称 |  
| type | 是 | int、float、string、securestring、bool、array、JSON 对象 | 输出返回值的类型 | 
| 值 | 是 | 与 `type` 相同 | 输出返回值 |  
||||| 

若要从工作流运行中获取输出，请在 Azure 门户中查看逻辑应用的运行历史记录和详细信息，或使用[工作流 REST API](https://docs.microsoft.com/rest/api/logic/workflows)。 也可将输出传递给 Power BI 等外部系统，以便可创建仪表板。 

<a name="expressions"></a>

## <a name="expressions"></a>表达式

使用 JSON 可以获取设计时存在的文本值，例如：

```json
"customerName": "Sophia Owen", 
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"], 
"rainbowColorsCount": 7 
```

还可以获取在运行时之前不存在的值。 若要表示这些值，可以使用运行时计算的表达式。 表达式是可以包含一个或多个[函数](#functions)、[运算符](#operators)、变量、显式值或常量的序列。 在工作流定义中，可以通过在表达式的前面加上 \@ 符号前缀在 JSON 字符串值中的任何位置使用表达式。 计算表示 JSON 值的表达式时，会通过删除 \@ 字符来提取表达式主体，并且始终生成另一个 JSON 值。 

例如，对于前面定义的 `customerName` 属性，可以通过在表达式中使用 [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 函数来获取属性值，并将该值分配给 `accountName` 属性：

```json
"customerName": "Sophia Owen", 
"accountName": "@parameters('customerName')"
```

字符串内插还允许在字符串中使用由 \@ 字符和大括号 ({}) 包装的多个表达式。 语法如下：

```json
@{ "<expression1>", "<expression2>" }
```

结果始终是一个字符串。因此，此功能类似于 `concat()` 函数，例如： 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

如果某个文本字符串以 \@ 字符开头，请加上 \@ 字符作为前缀，并加上另一个 \@ 字符作为转义符：\@\@

这些示例演示如何计算表达式：

| JSON 值 | 结果 |
|------------|--------| 
| "Sophia Owen" | 返回这些字符：'Sophia Owen' |
| "array[1]" | 返回这些字符：'array[1]' |
| "\@\@" | 以单字符字符串的形式返回这些字符：'\@' |   
| " \@" | 以双字符字符串的形式返回这些字符：' \@' |
|||

这些示例假设定义了 "myBirthMonth" 等于 "January"，"myAge" 等于数字 42：  
  
```json
"myBirthMonth": "January",
"myAge": 42
```

这些示例演示如何计算以下表达式：

| JSON 表达式 | 结果 |
|-----------------|--------| 
| "\@parameters('myBirthMonth')" | 返回此字符串："January" |  
| "\@{parameters('myBirthMonth')}" | 返回此字符串："January" |  
| "\@parameters('myAge')" | 返回此数字：42 |  
| "\@{parameters('myAge')}" | 以字符串形式返回此数字："42" |  
| "My age is \@{parameters('myAge')}" | 返回此字符串："My age is 42" |  
| "\@concat('My age is ', string(parameters('myAge')))" | 返回此字符串："My age is 42" |  
| "My age is \@\@{parameters('myAge')}" | 返回包含表达式的此字符串："My age is \@{parameters('myAge')}` | 
||| 

在逻辑应用设计器中以可视方式操作时，可以通过表达式生成器创建表达式，例如： 

![逻辑应用设计器 > 表达式生成器](./media/logic-apps-workflow-definition-language/expression-builder.png)

完成后，将为工作流定义中的相应属性显示表达式，例如，下面的 `searchQuery` 属性：

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
       "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>运算符

在[表达式](#expressions)和[函数](#functions)中，运算符执行特定的任务，例如，引用数组中的某个属性或值。 

| 运算符 | 任务 | 
|----------|------|
| ' | 若要使用字符串文本作为输入，或者在表达式和函数中使用字符串文本，请仅使用单引号包装该字符串，例如 `'<myString>'`。 不要使用双引号 ("")，否则与整个表达式两侧的 JSON 格式相冲突。 例如： <p>**正确**：length('Hello') </br>**错误**：length("Hello") <p>如果传递数组或数字，则不需要包装标点符号。 例如： <p>**正确**：length([1, 2, 3]) </br>**错误**：length("[1, 2, 3]") | 
| [] | 若要引用数组中特定位置（索引）处的某个值，请使用方括号。 例如，若要获取数组中的第二个项： <p>`myArray[1]` | 
| 。 | 若要引用对象中的属性，请使用点运算符。 例如，若要获取 `customer` JSON 对象的 `name` 属性： <p>`"@parameters('customer').name"` | 
| ? | 若要引用未发生运行时错误的对象中的 null 属性，请使用问号运算符。 例如，若要处理触发器中的 null 输出，可使用以下表达式： <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` | 
||| 

<a name="functions"></a>

## <a name="functions"></a>函数

某些表达式从运行时操作获取其值，而这些操作在逻辑应用开始执行时可能不存在。 若要引用或使用表达式中的这些值，可以使用[函数](../logic-apps/workflow-definition-language-functions-reference.md)。 可以使用数学函数进行计算。例如，[add()](../logic-apps/workflow-definition-language-functions-reference.md#add) 函数会从整数或浮点数返回总和。 有关每个函数的详细信息，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。
或者，继续学习函数及其一般用途。

下面是可以使用函数执行的几个示例任务： 

| 任务 | 函数语法 | 结果 | 
| ---- | --------------- | ------ | 
| 返回小写格式的字符串。 | toLower('<*text*>') <p>例如：toLower('Hello') | "hello" | 
| 返回全局唯一标识符 (GUID)。 | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" | 
|||| 

此示例演示如何从 `customerName` 参数获取值，并使用表达式中的 [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 函数将该值赋给 `accountName` 属性：

```json
"accountName": "@parameters('customerName')"
```

下面是表达式中的函数的其他一般使用方法：

| 任务 | 表达式中的函数语法 | 
| ---- | -------------------------------- | 
| 通过将某个项传递给函数，对该项执行操作。 | "\@<*functionName*>(<*item*>)" | 
| 1.使用嵌套的 `parameters()` 函数获取 *parameterName* 的值。 </br>2.通过将结果传递给 *functionName*，对该值执行操作。 | "\@<*functionName*>(parameters('<*parameterName*>'))" | 
| 1.从嵌套的内部函数 *functionName* 获取结果。 </br>2.将结果传递给外部函数 *functionName2*。 | "\@<*functionName2*>(<*functionName*>(<*item*>))" | 
| 1.从 *functionName* 获取结果。 </br>2.如果结果是包含属性 *propertyName* 的对象，则获取该属性的值。 | "\@<*functionName*>(<*item*>).<*propertyName*>" | 
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

有关每个函数的详细信息，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。
或者，根据函数的一般用途继续学习函数。

<a name="string-functions"></a>

### <a name="string-functions"></a>字符串函数

若要使用字符串，可以使用这些字符串函数以及某些[集合函数](#collection-functions)。 字符串函数仅适用于字符串。 

| 字符串函数 | 任务 | 
| --------------- | ---- | 
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | 组合两个或更多字符串，并返回组合后的字符串。 | 
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | 检查字符串是否以指定的子字符串结尾。 | 
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | 生成字符串形式的全局唯一标识符 (GUID)。 | 
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | 返回子字符串的起始位置。 | 
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | 返回子字符串的结束位置。 | 
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | 将子字符串替换为指定的字符串，并返回更新的字符串。 | 
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | 返回一个数组，该数组包含字符串中的所有字符，并使用特定的分隔符分隔每个字符。 | 
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | 检查字符串是否以特定的子字符串开头。 | 
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | 返回字符串中的字符，从指定的位置开始。 | 
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | 返回小写格式的字符串。 | 
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | 返回大写格式的字符串。 | 
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | 从字符串中删除前导和尾随空格，并返回更新后的字符串。 | 
||| 

<a name="collection-functions"></a>

### <a name="collection-functions"></a>集合函数

若要使用集合（通常是数组或字符串，有时是字典），可以使用这些集合函数。 

| 集合函数 | 任务 | 
| ------------------- | ---- | 
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | 检查集合是否包含某个特定项。 |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | 检查集合是否为空。 | 
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | 返回集合中的第一个项。 | 
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | 返回其中仅包含指定集合的共有项的一个集合。 | 
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | 返回一个字符串，其中包含某个数组中的所有项并以指定的分隔符分隔每个项。 | 
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | 返回集合中的最后一个项。 | 
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | 返回字符串或数组中的项数。 | 
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | 删除集合开头的项，并返回所有其他项。 | 
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | 返回集合开头的项。 | 
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | 返回一个集合，其中包含指定集合中的所有项。 | 
||| 

<a name="comparison-functions"></a>

### <a name="comparison-functions"></a>比较函数

若要使用条件、比较值和表达式结果或评估各种类型的逻辑，可以使用这些比较函数。 有关每个函数的完整参考，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 比较函数 | 任务 | 
| ------------------- | ---- | 
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

### <a name="conversion-functions"></a>转换函数

若要更改值的类型或格式，可以使用这些转换函数。 例如，可将值从布尔值更改为整数。 若要了解逻辑应用在转换期间如何处理内容类型，请参阅[处理内容类型](../logic-apps/logic-apps-content-type.md)。 有关每个函数的完整参考，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

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

<a name="math-functions"></a>

### <a name="math-functions"></a>数学函数

若要使用整数和浮点数，可以使用这些数学函数。 有关每个函数的完整参考，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

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

### <a name="date-and-time-functions"></a>日期和时间函数

若要使用日期和时间，可以使用这些日期和时间函数。
有关每个函数的完整参考，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 日期或时间函数 | 任务 | 
| --------------------- | ---- | 
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | 将天数加到时间戳。 | 
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | 将小时数加到时间戳。 | 
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | 将分钟数加到时间戳。 | 
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | 将秒数加到时间戳。 |  
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | 将时间单位数加到时间戳。 另请参阅 [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime)。 | 
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | 将时间戳从协调世界时 (UTC) 转换为目标时区。 | 
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | 将时间戳从源时区转换为目标时区。 | 
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | 将时间戳从源时区转换为协调世界时 (UTC)。 | 
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | 返回时间戳中月份组成部分的日期。 | 
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | 返回时间戳中周组成部分的星期日期。 | 
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | 返回时间戳中年组成部分的日期。 | 
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | 返回时间戳中的日期。 | 
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | 返回当前时间戳加上指定的时间单位。 另请参阅 [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime)。 | 
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | 返回当前时间戳减去指定的时间单位。 另请参阅 [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime)。 | 
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | 返回时间戳的日期起始位置。 | 
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | 返回时间戳的小时起始位置。 | 
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | 返回时间戳的月份起始位置。 | 
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | 从时间戳中减去时间单位数。 另请参阅 [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime)。 | 
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | 返回指定时间戳的 `ticks` 属性值。 | 
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | 返回字符串形式的当前时间戳。 | 
||| 

<a name="workflow-functions"></a>

### <a name="workflow-functions"></a>工作流函数

这些工作流函数有助于：

* 在运行时获取有关工作流实例的详细信息。 
* 使用用于实例化逻辑应用的输入。
* 引用触发器和操作的输出。

例如，可以引用一个操作的输出，并在后面的操作中使用该数据。 有关每个函数的完整参考，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 工作流函数 | 任务 | 
| ----------------- | ---- | 
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | 返回当前操作在运行时的输出，或者来自其他 JSON 名称和值对的值。 另请参阅 [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions)。 | 
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | 返回操作在运行时的 `body` 输出。 另请参阅 [body](../logic-apps/workflow-definition-language-functions-reference.md#body)。 | 
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | 返回操作在运行时的输出。 请参阅 [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions)。 | 
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | 返回操作在运行时的输出，或者来自其他 JSON 名称和值对的值。 另请参阅 [action](../logic-apps/workflow-definition-language-functions-reference.md#action)。  | 
| [body](#body) | 返回操作在运行时的 `body` 输出。 另请参阅 [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody)。 | 
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | 创建一个数组，其中包含与表单数据或表单编码操作输出中某个键名匹配的值。 | 
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | 返回与操作的表单数据或表单编码输出中的键名称匹配的单个值。 | 
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 位于针对数组的重复操作中时，返回在操作的当前迭代过程中数组中的当前项。 | 
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | 位于 for-each 或 do-until-loop 中时，返回指定循环中的当前项。| 
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | 返回调用某个触发器或操作的“回调 URL”。 | 
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | 返回具有多个部分的操作输出中某个特定部分的正文。 | 
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | 返回逻辑应用定义中描述的参数的值。 | 
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | 返回触发器在运行时的输出，或者来自其他 JSON 名称和值对的输出。 另请参阅 [triggerOutputs](#triggerOutputs) 和 [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)。 | 
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | 返回触发器在运行时的 `body` 输出。 请参阅 [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 | 
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | 返回与表单数据或表单编码触发器输出中某个键名匹配的单个值。 | 
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | 返回触发器多部分输出中特定部分的正文。 | 
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | 创建一个数组，该数组的值与表单数据或表单编码触发器输出中某个键名匹配。 | 
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | 返回触发器在运行时的输出，或者来自其他 JSON 名称和值对的值。 请参阅 [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger)。 | 
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | 返回指定变量的值。 | 
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | 返回有关运行时期间工作流本身的所有详细信息。 | 
||| 

<a name="uri-parsing-functions"></a>

### <a name="uri-parsing-functions"></a>URI 分析函数

若要使用统一资源标识符 (URI) 并获取这些 URI 的各个属性值，可以使用这些 URI 分析函数。 有关每个函数的完整参考，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

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

### <a name="json-and-xml-functions"></a>JSON 和 XML 函数

若要使用 JSON 对象和 XML 节点，可以使用这些操作函数。 有关每个函数的完整参考，请参阅[字母顺序参考文章](../logic-apps/workflow-definition-language-functions-reference.md)。

| 操作函数 | 任务 | 
| --------------------- | ---- | 
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | 将属性及其值或名称/值对添加到 JSON 对象，并返回更新的对象。 | 
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | 返回一个或多个参数中的第一个非 null 值。 | 
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | 从 JSON 对象中删除某个属性，并返回更新的对象。 | 
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | 设置 JSON 对象的属性值并返回更新的对象。 | 
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | 检查 XML 中是否存在与 XPath（XML 路径语言）表达式匹配的节点或值，并返回匹配的节点或值。 | 
||| 

## <a name="next-steps"></a>后续步骤

* 了解[工作流定义语言操作和触发器](../logic-apps/logic-apps-workflow-actions-triggers.md)
* 了解如何使用[工作流 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 以编程方式创建和管理逻辑应用
