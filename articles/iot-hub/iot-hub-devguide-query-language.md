---
title: "了解 Azure IoT 中心查询语言 |Microsoft 文档"
description: "开发人员指南-用于检索有关设备 twins 和作业信息从你的 IoT 中心的类似 SQL 的 IoT 中心查询语言的说明。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 851a9ed3-b69e-422e-8a5d-1d79f91ddf15
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/17
ms.author: elioda
ms.openlocfilehash: a7650104eda58923558892f6f0f6666d16dbce28
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="reference---iot-hub-query-language-for-device-twins-jobs-and-message-routing"></a>参考-IoT 中心设备 twins、 作业和邮件路由的查询语言

IoT 中心提供功能强大的类似 SQL 的语言，以检索信息有关[设备 twins] [ lnk-twins]和[作业][lnk-jobs]，和[邮件路由][lnk-devguide-messaging-routes]。 本文提供以下内容：

* IoT 中心查询语言 （） 的主要功能的简介和
* 语言详细的说明。

## <a name="get-started-with-device-twin-queries"></a>要开始使用设备双向查询
[设备 twins] [ lnk-twins]可以包含任意 JSON 对象作为标记和属性。 IoT 中心可让你对查询设备 twins 作为包含所有设备的双向信息的单个 JSON 文档。
例如，假设你的 IoT 中心设备 twins 具有以下结构：

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

IoT 中心将作为一个名为的文档集合公开设备 twins**设备**。
因此，以下查询检索设备 twins 的整个集：

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT Sdk] [ lnk-hub-sdks]支持分页的大型结果。

IoT 中心允许您检索设备 twins 使用任意条件进行筛选。 例如，

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

检索与设备 twins **location.region**标记设置为**美国**。
布尔运算符和算术比较支持例如

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

检索位于配置为发送遥测通常少于每隔一分钟美国的所有设备 twins。 为方便起见，它还可使用与数组常量**IN**和**NIN** （不在） 运算符。 例如，

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

检索所有设备 twins 报告 WiFi 或有线连接。 它通常是标识包含特定属性的所有设备 twins 所需的。 IoT 中心支持函数`is_defined()`为此目的。 例如，

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

检索定义的所有设备 twins`connectivity`报告属性。 请参阅[WHERE 子句][ lnk-query-where]筛选功能完整的参考部分。

此外支持分组和聚合。 例如，

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

返回每个遥测配置状态中的设备的计数。

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

前面的示例阐释了其中三个设备报告成功的配置，两个仍要将应用配置，并且其中一个报告错误的情况。

### <a name="c-example"></a>C# 示例
查询功能公开的[C# 服务 SDK] [ lnk-hub-sdks]中**RegistryManager**类。
下面是查询的一个简单的示例：

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

请注意如何**查询**页大小 （最多 1000 个），实例化对象，然后可通过调用检索多个页**GetNextAsTwinAsync**方法多次。
请注意，查询对象公开多个**下一步\***，取决于使用投影时，使用所需的查询，例如设备双向或作业对象或纯 JSON 反序列化选项。

### <a name="nodejs-example"></a>Node.js 示例
查询功能公开的[Azure IoT 服务 SDK for Node.js] [ lnk-hub-sdks]中**注册表**对象。
下面是查询的一个简单的示例：

```nodejs
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

请注意如何**查询**页大小 （最多 1000 个），实例化对象，然后可通过调用检索多个页**nextAsTwin**方法多次。
请注意，查询对象公开多个**下一步\***，取决于使用投影时，使用所需的查询，例如设备双向或作业对象或纯 JSON 反序列化选项。

### <a name="limitations"></a>限制
> [!IMPORTANT]
> 查询结果可以在设备 twins 有几分钟的延迟方面的最新值。 如果按 id 查询单个设备 twins，将始终优先使用检索设备双向 API 的说明进行操作，后者始终包含的最新值并具有更高版本节流限制。

目前，比较支持仅之间基元类型 （没有对象），例如`... WHERE properties.desired.config = properties.reported.config`这些属性具有基元值的情况下，才支持。

## <a name="get-started-with-jobs-queries"></a>要开始使用作业查询
[作业][ lnk-jobs]提供一种执行套设备上的操作。 每个设备双向包含其中它是一个名为集合中的一部分的作业的信息**作业**。
在逻辑上，

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

目前，此集合是可作为查询**devices.jobs** IoT 中心内查询语言。

> [!IMPORTANT]
> 目前，作业属性从不返回查询设备 twins （即包含设备的查询） 时。 它可直接通过使用查询进行访问`FROM devices.jobs`。
>
>

例如，若要获取影响单个设备的所有作业 （过去和计划），你可以使用以下查询：

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

请注意此查询如何提供特定于设备的状态 （和可能的直接方法响应） 的返回每个作业。
还有可能使用中的所有对象属性的任意布尔条件进行筛选**devices.jobs**集合。
例如，以下查询：

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

检索所有已完成设备双向更新作业设备**myDeviceId** 2016 年 9 月之后创建的。

还有可能要检索单个作业的每个设备结果。

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>限制
目前，查询上**devices.jobs**不支持：

* 投影，因此，仅`SELECT *`一点的。
* 除了作业属性 （请参阅上一节） 设备双向引用的条件。
* 执行聚合，例如计数、 平均值、 分组依据。

## <a name="get-started-with-device-to-cloud-message-routes-query-expressions"></a>要开始使用设备到云消息路由查询表达式

使用[设备到云路由][lnk-devguide-messaging-routes]，你可以配置 IoT 中心以设备到云将消息调度到不同的终结点基于针对各个消息计算的表达式。

路由[条件][ lnk-query-expressions]使用相同的 IoT 中心查询语言作为双向和作业的查询中的条件。 在消息标头和正文计算路由条件。 路由的查询表达式可能涉及到仅消息头，只有消息正文，和/或文件名消息标头消息正文。 IoT 中心假定的标头和消息正文的特定架构以便将消息，路由和以下各节描述了 IoT 中心以正确地将路由的要求：

### <a name="routing-on-message-headers"></a>在消息标头路由

IoT 中心假定邮件路由的消息头的以下 JSON 表示的形式：

```json
{
    "$messageId": "",
    "$enqueuedTime": "",
    "$to": "",
    "$expiryTimeUtc": "",
    "$correlationId": "",
    "$userId": "",
    "$ack": "",
    "$connectionDeviceId": "",
    "$connectionDeviceGenerationId": "",
    "$connectionAuthMethod": "",
    "$content-type": "",
    "$content-encoding": "",

    "userProperty1": "",
    "userProperty2": ""
}
```

消息的系统属性带有前缀`'$'`符号。
名称始终访问用户属性。 如果用户属性名恰好与系统的属性，同时发生 (如`$to`)，将使用检索用户属性`$to`表达式。
你始终可以访问系统属性使用方括号`{}`： 例如，你可以使用表达式`{$to}`若要访问系统属性`to`。 括在括号中的属性名称始终检索相应的系统属性。

请记住，属性名称区分大小写。

> [!NOTE]
> 所有消息属性都是字符串。 系统属性，如中所述[开发人员指南][lnk-devguide-messaging-format]，当前不能在查询中使用。
>

例如，如果你使用`messageType`属性，你可能想要将所有遥测都路由到一个终结点和到另一个终结点的所有警报。 你可以编写以下表达式将路由遥测数据：

```sql
messageType = 'telemetry'
```

和下列表达式以将警报消息路由：

```sql
messageType = 'alert'
```

此外支持布尔表达式和函数。 此功能使您能够区分严重性级别，例如：

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

请参阅[表达式和条件][ lnk-query-expressions]部分支持的运算符和函数的完整列表。

### <a name="routing-on-message-bodies"></a>消息正文上的路由

IoT 中心可以仅路由根据消息正文内容正确消息正文是否格式正确的 JSON 采用 utf-8、 utf-16 或 utf-32 编码。 必须设置为消息的内容类型`application/json`和要允许 IoT 中心基于正文内容将消息路由的消息标头中的受支持 UTF 编码之一的内容编码。 如果在任一标头未指定，IoT 中心将不尝试评估涉及对消息正文的任何查询表达式。 如果你的消息不是 JSON 消息，或者如果消息未指定的内容类型和内容编码，您仍可以使用消息路由基于消息标头将消息路由。

你可以使用`$body`在查询表达式将消息路由。 在查询表达式，可以使用简单的正文引用、 正文数组引用或正文中的多个引用。 查询表达式也可以组合使用的消息标头引用的正文引用。 例如，以下是所有的有效查询表达式：

```sql
$body.message.Weather.Location.State = 'WA'
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.message.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>IoT 中心查询的基础知识
IoT 中心的每个查询包括的 SELECT FROM 子句和可选的 WHERE 和 GROUP BY 子句。 每个查询的 JSON 文档，例如设备 twins 的集合上运行。 FROM 子句指明上循环的文档集合 (**设备**或**devices.jobs**)。 然后，将应用 WHERE 子句中的筛选器。 此步骤的结果分组为包含聚合，指定在 GROUP BY 子句中，并为每个组，行生成 SELECT 子句中指定。

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM 子句
**从 < from_specification >**子句可以假定只有两个值：**从设备**，若要查询设备 twins 或**从 devices.jobs**，若要查询作业每个设备详细信息。

## <a name="where-clause"></a>WHERE 子句
**其中 < filter_condition >**子句是可选的。 它指定 JSON 文档 FROM 集合中的一个或多个条件必须满足要的结果的一部分。 任何 JSON 文档的计算结果必须指定的条件为"true"，以包含在结果。

允许的条件部分所述[表达式和条件][lnk-query-expressions]。

## <a name="select-clause"></a>SELECT 子句
SELECT 子句 (**选择 < select_list >**) 是必需的并指定将从查询检索哪些值。 它指定要用于生成新的 JSON 对象的 JSON 值。
对于 FROM 集合筛选 （和 （可选） 分组） 一部分的每个元素，投影阶段生成一个新的 JSON 对象，使用 SELECT 子句中指定的值构造的。

下面是 SELECT 子句的语法：

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

其中**attribute_name**引用从集合中的 JSON 文档的任何属性。 SELECT 子句的一些示例可在[设备双向查询入门][ lnk-query-getstarted]部分。

目前，所选内容子句不同于**选择\***仅支持在设备 twins 上聚合查询中。

## <a name="group-by-clause"></a>GROUP BY 子句
**GROUP BY < group_specification >**子句是一个可选步骤，可以指定在 WHERE 子句中，和之前在选择所指定的筛选器之后执行。 它将若干文档属性的值。 这些组用于生成作为 SELECT 子句中指定的聚合的值。

是一个查询使用 GROUP BY 的示例：

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

分组依据的正式语法是：

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

其中**attribute_name**引用从集合中的 JSON 文档的任何属性。

目前，查询设备 twins 时，仅支持 GROUP BY 子句。

## <a name="expressions-and-conditions"></a>表达式和条件
在高级别，*表达式*:

* 计算结果为 （如布尔值、 数字、 字符串、 数组或对象），JSON 类型的实例和
* 由操作数据来自设备 JSON 文档和常量使用内置运算符和函数定义。

*条件*是计算结果为一个布尔值的表达式。 不同于布尔值任何常量**true**操作均被视为**false** (包括**null**，**未定义**，任何对象或数组的实例，任意字符串，并清楚地布尔值**false**)。

表达式的语法是：

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

其中：

| 符号 | 定义 |
| --- | --- |
| attribute_name | 中的 JSON 文档的任何属性**FROM**集合。 |
| binary_operator | 中列出的任何二元运算符[运算符](#operators)部分。 |
| function_name| 中列出的任何函数[函数](#functions)部分。 |
| decimal_literal |以十进制表示法表示的浮点数。 |
| hexadecimal_literal |一个由字符串"0x"跟十六进制数字的字符串表示的数字。 |
| 字符串文字 |字符串文字是由零个或多个 Unicode 字符序列或转义序列表示的 Unicode 字符串。 字符串文字括在单引号 (撇号:) 或双引号 (引号:")。 允许使用转义符： `\'`， `\"`， `\\`，`\uXXXX`由 4 位十六进制数字定义的 Unicode 字符。 |

### <a name="operators"></a>运算符
支持以下运算符：

| 系列 | 运算符 |
| --- | --- |
| 算术运算 |+,-,*,/,% |
| 逻辑 |并且，或不 |
| 比较 |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>函数
当查询 twins 和作业唯一受支持的函数是：

| 函数 | 描述 |
| -------- | ----------- |
| IS_DEFINED(property) | 返回一个布尔值，该值指示是否属性已被分配一个值 (包括`null`)。 |

在路由情况下，支持以下的数学函数：

| 函数 | 描述 |
| -------- | ----------- |
| ABS(x) | 返回指定数值表达式的绝对 （正） 值。 |
| EXP(x) | 返回指定数值表达式的指数值 (e ^ x)。 |
| POWER(x,y) | 返回指定表达式的值的指定次幂 (x ^ y)。|
| SQUARE(x) | 返回指定数字值的平方。 |
| CEILING(x) | 大于或等于指定数值表达式，则返回的最小的整数值。 |
| FLOOR(x) | 返回小于或等于指定数值表达式的最大整数。 |
| SIGN(x) | 返回的正 (+ 1)、 零 (0) 号或负号 (-1) 的指定数值表达式。|
| 传感器 | 返回指定数字值的平方。 |

在路由情况下，支持以下类型检查和强制转换函数：

| 函数 | 描述 |
| -------- | ----------- |
| AS_NUMBER | 将输入的字符串转换为数字。 `noop`如果输入是一个数字;`Undefined`如果字符串不表示一个数字。|
| IS_ARRAY | 返回一个布尔值，该值指示是否指定的表达式的类型一个数组。 |
| IS_BOOL | 返回一个布尔值，该值指示指定表达式的类型是否为一个布尔值。 |
| IS_DEFINED | 返回一个布尔值，该值指示是否属性已被分配一个值。 |
| IS_NULL | 返回一个布尔值，该值指示指定表达式的类型是否为 null。 |
| IS_NUMBER | 返回一个布尔值，该值指示是否指定的表达式的类型为一个数字。 |
| IS_OBJECT | 返回一个布尔值，该值指示指定表达式的类型是否为 JSON 对象。 |
| IS_PRIMITIVE | 返回一个布尔值，该值指示指定表达式的类型是否为基元 (字符串、 布尔型数字或`null`)。 |
| IS_STRING | 返回一个布尔值，该值指示是否指定的表达式的类型为 string。 |

在路由条件支持下列字符串函数：

| 函数 | 描述 |
| -------- | ----------- |
| CONCAT(x,...) | 返回一个字符串，它连接两个或多个字符串值的结果。 |
| LENGTH(x) | 返回指定的字符串表达式的字符数。|
| LOWER(x) | 返回将大写字符数据转换成小写后的字符串表达式。 |
| UPPER(x) | 返回将小写字符数据转换为大写后的字符串表达式。 |
| 子字符串 （字符串、 开始 [、 长度]） | 返回从指定的字符的从零开始位置开始的字符串表达式的一部分并继续到指定的长度或字符串的末尾。 |
| INDEX_OF (片段中的字符串） | 返回第二个中的第一个匹配项的起始位置字符串内第一个指定的字符串表达式，则为-1 的表达式，如果未找到该字符串。|
| STARTS_WITH （x，y） | 返回一个布尔值，该值指示是否的第一个字符串表达式开头第二个。 |
| ENDS_WITH （x，y） | 返回一个布尔值，该值指示是否的第一个字符串表达式的第二个结束。 |
| CONTAINS(x,y) | 返回一个布尔值，该值指示是否的第一个字符串表达式包含第二个。 |

## <a name="next-steps"></a>后续步骤
了解如何使用应用程序中执行查询[Azure IoT Sdk][lnk-hub-sdks]。

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
