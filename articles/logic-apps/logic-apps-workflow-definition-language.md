---
title: 工作流定义语言-Azure 逻辑应用的架构参考
description: 在 Azure 逻辑应用中的工作流定义语言架构的参考指南
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: 3b0ad33ea6348f24079b3c88f972437244c0bc93
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596757"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Azure 逻辑应用中工作流定义语言的架构引用

当您创建了逻辑应用中的[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)，逻辑应用具有描述在逻辑应用中运行的实际逻辑的基础工作流定义。 该工作流定义使用[JSON](https://www.json.org/)并遵循由工作流定义语言架构验证的结构。 本参考提供有关此结构，该架构如何定义工作流定义中的属性的概述。

## <a name="workflow-definition-structure"></a>工作流定义结构

工作流定义始终包括用于实例化逻辑应用，加上后触发器触发时运行的一个或多个操作的触发器。

下面是工作流定义的高级结构：

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| 特性 | 需要 | 描述 |
|-----------|----------|-------------|
| `definition` | 是 | 工作流定义的起始元素 |
| `$schema` | 仅当在外部引用工作流定义时才使用 | 描述工作流定义语言版本的 JSON 架构文件的位置。可在以下位置找到该文件： <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | 否 | 一个或多个要在工作流运行时执行的操作的定义。 有关详细信息，请参阅[触发器和操作](#triggers-actions)。 <p><p>操作数量上限：250 |
| `contentVersion` | 否 | 工作流定义的版本号，默认为“1.0.0.0”。 为了帮助在部署工作流时识别并确认正确的定义，请指定要使用的值。 |
| `outputs` | 否 | 运行工作流中返回的输出定义。 有关详细信息，请参阅[输出](#outputs)。 <p><p>输出数量上限：10 |
| `parameters` | 否 | 将数据传递到工作流中的一个或多个参数的定义。 有关详细信息，请参阅[参数](#parameters)。 <p><p>参数数量上限：50 |
| `staticResults` | 否 | 操作返回的模拟输出作为这些操作在中启用静态结果时的一个或多个静态结果的定义。 在每个操作定义中，`runtimeConfiguration.staticResult.name`属性引用在相应的定义`staticResults`。 有关详细信息，请参阅[静态结果](#static-results)。 |
| `triggers` | 否 | 用于实例化工作流的一个或多个触发器的定义 可以定义多个触发器，但只能使用工作流定义语言来定义，而不能通过逻辑应用设计器以可视方式进行定义。 有关详细信息，请参阅[触发器和操作](#triggers-actions)。 <p><p>触发器数量上限：10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>触发器和操作

在工作流定义中，`triggers` 和 `actions` 节定义工作流执行期间发生的调用。 有关这些节的语法和详细信息，请参阅[工作流触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md)。

<a name="outputs"></a>

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

| 特性 | 需要 | Type | 描述 |
|-----------|----------|------|-------------|
| <*key-name*> | 是 | String | 输出返回值的密钥名称 |
| <*key-type*> | 是 | int、float、string、securestring、bool、array、JSON 对象 | 输出返回值的类型 |
| <*key-value*> | 是 | 与相同 <*密钥类型*> | 输出返回值 |
|||||

若要运行工作流中获取输出，请查看逻辑应用的运行历史记录和在 Azure 门户中的详细信息，或使用[工作流 REST API](https://docs.microsoft.com/rest/api/logic/workflows)。 也可将输出传递给 Power BI 等外部系统，以便可创建仪表板。

<a name="parameters"></a>

## <a name="parameters"></a>parameters

在`parameters`部分中，定义你的工作流定义使用在部署时用于接受输入的所有工作流参数。 部署时需要参数声明和参数值。 在其他工作流节中使用这些参数之前，请确保声明这些节中的所有参数。 

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

| 特性 | 需要 | Type | 描述 |
|-----------|----------|------|-------------|
| <*parameter-type*> | 是 | int、float、string、securestring、bool、array、JSON 对象、secureobject <p><p>**注意**：对于所有密码、密钥和机密，请使用 `securestring` 和 `secureobject` 类型，因为 `GET` 操作不会返回这些类型。 有关保护参数的详细信息，请参阅[保护逻辑应用](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | 参数的类型 |
| <*default-parameter-values*> | 是 | 与 `type` 相同 | 在工作流实例化时未指定值的情况下使用的默认参数值 |
| <*array-with-permitted-parameter-values*> | 否 | Array | 包含参数可接受的值的数组 |
| `metadata` | 否 | JSON 对象 | 任何其他参数的详细信息，例如，名称或逻辑应用或流中或使用 Visual Studio 或其他工具的设计时数据的可读说明 |
||||

<a name="static-results"></a>

## <a name="static-results"></a>静态结果

在中`staticResults`属性，定义操作的模拟`outputs`和`status`操作返回操作的静态结果设置开启时。 在操作的定义中，`runtimeConfiguration.staticResult.name`属性引用内部静态结果定义的名称`staticResults`。 了解如何[测试具有模拟数据的逻辑应用通过设置静态结果](../logic-apps/test-logic-apps-mock-data-static-results.md)。

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| 特性 | 需要 | Type | 描述 |
|-----------|----------|------|-------------|
| <*static-result-definition-name*> | 是 | String | 操作定义可以通过引用静态结果定义的名称`runtimeConfiguration.staticResult`对象。 有关详细信息，请参阅[运行时配置设置](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options)。 <p>可以使用任何所需的唯一名称。 默认情况下，此唯一名称后追加一个数字，其会相应增加。 |
| <*output-attributes-and-values-returned*> | 是 | 多种多样 | 这些属性的要求因不同的条件。 例如，当`status`是`Succeeded`，则`outputs`属性包括属性和所返回的操作为模拟输出值。 如果`status`是`Failed`，则`outputs`属性包括`errors`特性，它是一个包含一个或多个错误的数组`message`具有错误的信息的对象。 |
| <*header-values*> | 否 | JSON | 操作返回的任何标头值 |
| <*status-code-returned*> | 是 | String | 返回由该操作的状态代码 |
| <*action-status*> | 是 | String | 操作的状态，例如，`Succeeded`或 `Failed` |
|||||

例如，在此 HTTP 操作定义中，`runtimeConfiguration.staticResult.name`属性引用`HTTP0`内`staticResults`其中定义该操作的模拟输出的属性。 `runtimeConfiguration.staticResult.staticResultOptions`属性指定静态结果设置为`Enabled`上 HTTP 操作。

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

HTTP 操作返回中的输出`HTTP0`内部定义`staticResults`。 在此示例中，状态代码，用于模拟的输出是`OK`。 对于标头值，模拟的输出是`"Content-Type": "application/JSON"`。 有关操作的状态，模拟的输出是`Succeeded`。

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

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
| "Sophia Owen" | 返回这些字符：“Sophia Owen” |
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
| "\@parameters('myBirthMonth')" | 返回此字符串：“January” |
| "\@{parameters('myBirthMonth')}" | 返回此字符串：“January” |
| "\@parameters('myAge')" | 返回此数字：42 |
| "\@{parameters('myAge')}" | 以字符串形式返回此数字：“42” |
| "My age is \@{parameters('myAge')}" | 返回此字符串：“My age is 42” |
| "\@concat('My age is ', string(parameters('myAge')))" | 返回此字符串：“My age is 42” |
| "My age is \@\@{parameters('myAge')}" | 返回此字符串，其中包括表达式：“My age is \@{parameters('myAge')}` |
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
| . | 若要引用对象中的属性，请使用点运算符。 例如，若要获取 `customer` JSON 对象的 `name` 属性： <p>`"@parameters('customer').name"` |
| ? | 若要引用未发生运行时错误的对象中的 null 属性，请使用问号运算符。 例如，若要处理触发器中的 null 输出，可使用以下表达式： <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>函数

某些表达式可能尚不存在工作流定义开始运行时的运行时操作中获取它们的值。 若要在表达式中引用或处理这些值，可以使用工作流定义语言提供的[*函数*](../logic-apps/workflow-definition-language-functions-reference.md)。

## <a name="next-steps"></a>后续步骤

* 了解[工作流定义语言操作和触发器](../logic-apps/logic-apps-workflow-actions-triggers.md)
* 了解如何使用[工作流 REST API](https://docs.microsoft.com/rest/api/logic/workflows) 以编程方式创建和管理逻辑应用
