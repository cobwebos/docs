---
title: 了解 Azure IoT 中心查询语言 | Microsoft Docs
description: 开发人员指南 - 介绍了类似 SQL 的 IoT 中心查询语言，该语言用于在 IoT 中心检索设备/模块孪生和作业的相关信息。
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: rezas
ms.openlocfilehash: 0cb3bc63f9c3d63c68edba0b4162970e18fca76d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312695"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>用于设备和模块孪生、作业和消息路由的 IoT 中心查询语言

IoT 中心提供类似于 SQL 的强大语言，用于检索有关[设备孪生](iot-hub-devguide-device-twins.md)、[作业](iot-hub-devguide-jobs.md)和[消息路由](iot-hub-devguide-messages-d2c.md)的信息。 本文内容：

* IoT 中心查询语言的主要功能简介，以及
* 语言的详细说明。 有关用于消息路由的查询语言的详细信息，请参阅[消息路由中的查询](../iot-hub/iot-hub-devguide-routing-query-syntax.md)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>设备和模块孪生查询

[设备孪生](iot-hub-devguide-device-twins.md)和模块孪生可以包含标记和属性形式的任意 JSON 对象。 通过 IoT 中心，可将设备孪生和模块孪生作为包含所有孪生信息的单个 JSON 文档进行查询。

例如，假定 IoT 中心设备孪生具有以下结构（模块孪生将与之类似，只是具有附加的 moduleId）：

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",    
    "connectionState": "Disconnected",    
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",    
    "x509Thumbprint": {    
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
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

### <a name="device-twin-queries"></a>设备孪生查询

IoT 中心将设备孪生公开为名为**设备**的文档集合。 例如，以下查询将检索设备孪生的整个集：

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 支持将大型结果分页。

IoT 中心允许使用任意条件检索设备孪生筛选结果。 例如，若要接收 **location.region** 标记设置为 **US** 的设备孪生，请使用以下查询：

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

还支持布尔运算符和算术比较。 例如，若要接收位于美国且配置为每隔不到一分钟就发送遥测数据的设备孪生，请使用以下查询：

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

方便起见，还可将数组常量与 **IN** 和 **NIN**（不包含）运算符结合使用。 例如，若要检索报告了 WiFi 或有线连接的设备孪生，请使用以下查询：

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

通常需要它才能识别包含特定属性的所有设备孪生。 为此，IoT 中心支持函数 `is_defined()`。 例如，若要检索定义了 `connectivity` 属性的设备孪生，请使用以下查询：

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

有关筛选功能的完整参考，请参阅 [WHERE 子句](iot-hub-devguide-query-language.md#where-clause)部分。

此外还支持分组与聚合。 例如，若要查明每个遥测配置中的设备计数，请使用以下查询：

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

此分组查询将返回类似于以下示例的结果：

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

在此示例中，三个设备报告了成功配置，两个仍在应用配置，一个报告了错误。

投影查询允许开发人员仅返回他们所关注的属性。 例如，若要检索所有已断开连接设备的上次活动时间，请使用以下查询：

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>模块孪生查询

对模块孪生进行查询类似于对设备孪生进行查询，但使用你可以查询 device.modules 的一个不同集合/命名空间，即不“从设备”查询：

```sql
SELECT * FROM devices.modules
```

不允许在 devices 与 devices.modules 集合之间进行联接。 如果要跨设备查询模块孪生，则需要基于标记来执行此操作。 以下查询将返回所有设备中具有扫描状态的所有模块孪生：

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

以下查询将仅返回指定的设备子集上具有扫描状态的所有模块孪生：

```sql
SELECT * FROM devices.modules 
  WHERE properties.reported.status = 'scanning' 
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C# 示例

查询功能由 [C# 服务 SDK](iot-hub-devguide-sdks.md) 在 RegistryManager 类中公开。

下面是一个简单的查询示例：

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

“查询”对象实例化为页面大小（最大为 100）。 然后通过多次调用 GetNextAsTwinAsync 方法来检索多个页面。

查询对象公开多个“下一步”值，具体取决于该查询所需的反序列化选项。 例如，设备孪生或作业对象，或使用投影时的普通 JSON。

### <a name="nodejs-example"></a>Node.js 示例

查询功能由[适用于 Node.js 的 Azure IoT 服务 SDK](iot-hub-devguide-sdks.md) 在 Registry 对象中公开。

下面是一个简单的查询示例：

```javascript
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

“查询”对象实例化为页面大小（最大为 100）。 然后通过多次调用 nextAsTwin 方法来检索多个页面。

查询对象公开多个“下一步”值，具体取决于该查询所需的反序列化选项。 例如，设备孪生或作业对象，或使用投影时的普通 JSON。

### <a name="limitations"></a>限制

> [!IMPORTANT]
> 关于设备孪生中的最新值，查询结果可能有几分钟的延迟。 如果通过 ID 查询单个设备孪生，请使用检索设备双向 API。 此 API 始终包含最新值，并具有更高的节流限制。

目前，仅支持在基元类型（无对象）之间进行比较，例如，仅在这些属性具有基元值时才支持 `... WHERE properties.desired.config = properties.reported.config`。

## <a name="get-started-with-jobs-queries"></a>作业查询入门

使用[作业](iot-hub-devguide-jobs.md)可对一组设备执行操作。 每个设备孪生包含名为**作业**的集合中该设备参与的作业的信息。

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

目前，可以使用 IoT 中心查询语言以 **devices.jobs** 形式查询此集合。

> [!IMPORTANT]
> 目前，查询设备孪生时不会返回作业属性。 即包含“FROM 设备”的查询。 该作业属性仅可通过使用 `FROM devices.jobs` 的查询直接访问。
>
>

例如，若要获取影响单个设备的所有作业（过去和计划的作业），可以使用以下查询：

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

请注意此查询如何提供每个返回的作业的设备特定状态（可能还会提供直接方法响应）。

还可针对 **devices.jobs** 集合中的所有对象属性，使用任意布尔条件进行筛选。

若要为特定设备检索在 2016 年 9 月之后创建的所有已完成的设备孪生更新作业，请使用以下查询：

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

还可以检索单个作业在每个设备上的结果。

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>限制

目前，对 **devices.jobs** 的查询不支持：

* 投影，因此仅可使用 `SELECT *`。
* 引用设备孪生和作业属性的条件（参见上一节）。
* 执行聚合，例如 count、avg、group by。

## <a name="basics-of-an-iot-hub-query"></a>IoT 中心查询基础知识

每个 IoT 中心查询都包括 SELECT 和 FROM 子句，以及可选的 WHERE 和 GROUP BY 子句。 每个查询针对 JSON 文档的集合（例如，设备孪生）运行。 FROM 子句指示要迭代的文档集合（**devices** 或 **devices.jobs**）。 然后，应用 WHERE 子句中的筛选器。 使用聚合时，将按 GROUP BY 子句中的指定对此步骤的结果分组。 对于每组，将按照 SELECT 子句中的指定生成一行。

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM 子句

FROM <from_specification> 子句只能假定两个值：用于查询设备孪生的“FROM devices”，或用于根据设备详情查询作业“FROM devices.jobs”。


## <a name="where-clause"></a>WHERE 子句
**WHERE <filter_condition>** 子句是可选的。 它指定要将 FROM 集合中的 JSON 文档内含在结果中时需满足的一项或多项条件。 任何 JSON 文档必须将指定的条件求值为“true”才能包含在结果中。

[表达式和条件](iot-hub-devguide-query-language.md#expressions-and-conditions)部分介绍了允许的条件。

## <a name="select-clause"></a>SELECT 子句

**SELECT <select_list>** 是必需的，用于指定要通过查询检索的值。 它指定用于生成新 JSON 对象的 JSON 值。
对于 FROM 集合中已筛选子集（且可选择性分组）的每个元素，投影阶段将生成一个新 JSON 对象。 使用 SELECT 子句中指定的值构造此对象。

SELECT 子句的语法如下：

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

**Attribute_name** 引用 FROM 集合中 JSON 文档的任一属性。 在“设备孪生查询入门”部分可以找到 SELECT 子句的一些示例。

目前，仅支持在针对设备孪生执行的聚合查询中使用除 **SELECT*** 以外的选择子句。

## <a name="group-by-clause"></a>GROUP BY 子句
**GROUP BY <group_specification>** 子句是可选步骤，可在 WHERE 子句中指定的筛选器后、在 SELECT 中指定的投影前执行该子句。 它根据属性值来分组文档。 这些组用于生成 SELECT 子句中指定的聚合值。

下面是使用 GROUP BY 的查询示例：

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

GROUP BY 的正式语法为：

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** 引用 FROM 集合中 JSON 文档的任一属性。

目前，仅在查询设备孪生时才支持使用 GROUP BY 子句。

> [!IMPORTANT]
> 术语 `group` 目前被视为查询中的特殊关键字。 如果你使用 `group` 作为属性名，请考虑使用双括号将其括起来以避免错误，例如 `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`。
>
>

## <a name="expressions-and-conditions"></a>表达式和条件
从较高层面讲，*表达式*可以：

* 求值结果为 JSON 类型的实例（例如布尔值、数字、字符串、数组或对象）。
* 由设备 JSON 文档中的操作数据以及使用内置运算符和函数的常量定义。

条件是求值为布尔值的表达式。 将任何不同于布尔值“true”的常数视为“false”。 此规则包括“Null”、“undefined”、任何对象或数组实例、任何字符串和布尔值“false”。

表达式的语法为：

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

若要了解表达式语法中的每个符号表示什么，请参阅下表：

| 符号 | 定义 |
| --- | --- |
| attribute_name | **FROM** 集合中 JSON 文档的任一属性。 |
| binary_operator | [运算符](#operators)部分列出的任意二进制运算符。 |
| function_name| [函数](#functions)部分列出的任一函数。 |
| decimal_literal |以十进制表示法表示的浮点数。 |
| hexadecimal_literal |以字符串“0x”后接十六进制数字符串表示的数字。 |
| string_literal |字符串文本是以零个或多个 Unicode 字符序列或转义符序列表示的 Unicode 字符串。 字符串文本括在单引号或双引号中。 允许的转义符：`\'`、`\"`、`\\`、`\uXXXX`（由 4 个十六进制数字定义的 Unicode 字符）。 |

### <a name="operators"></a>运算符
支持以下运算符：

| 系列 | 运算符 |
| --- | --- |
| 算术 |+, -, *, /, % |
| 逻辑 |AND、OR、NOT |
| 比较 |=、!=、<、>、<=、>=、<> |

### <a name="functions"></a>函数
查询克隆和作业时唯一受支持的函数是：

| 函数 | 说明 |
| -------- | ----------- |
| IS_DEFINED(property) | 返回一个布尔值，指示是否已向属性分配值（包括 `null`）。 |

在路由情况下，支持以下数学函数：

| 函数 | 说明 |
| -------- | ----------- |
| ABS(x) | 返回指定数值表达式的绝对（正）值。 |
| EXP(x) | 返回指定数值表达式 (e^x) 的指数值。 |
| POWER(x,y) | 返回指定表达式相对指定幂 (x^y) 的值。|
| SQUARE(x) | 返回指定数字值的平方。 |
| CEILING(x) | 返回大于或等于指定数值表达式的最小整数值。 |
| FLOOR(x) | 返回小于或等于指定数值表达式的最大整数。 |
| SIGN(x) | 返回指定数值表达式的正数 (+1)、零 (0) 或负数 (-1)。|
| SQRT(x) | 返回指定数值的平方根。 |

在路由情况下，支持以下检查和强制转换类型的函数：

| 函数 | 说明 |
| -------- | ----------- |
| AS_NUMBER | 将输入字符串转换为数字。 如果输入数字，则为 `noop`；如果字符串不表示数字，则为 `Undefined`。|
| IS_ARRAY | 返回一个布尔值，指示指定表达式类型是否为数组。 |
| IS_BOOL | 返回一个布尔值，指示指定表达式的类型是否为布尔表达式。 |
| IS_DEFINED | 返回一个布尔，它指示属性是否已经分配了值。 |
| IS_NULL | 返回一个布尔值，指示指定表达式的类型是否为 null。 |
| IS_NUMBER | 返回一个布尔值，指示指定表达式的类型是否为数字。 |
| IS_OBJECT | 返回一个布尔值，指示指定表达式的类型是否为 JSON 对象。 |
| IS_PRIMITIVE | 返回一个布尔值，指示指定表达式的类型是否为基元（字符串、布尔值、数字或 `null`）。 |
| IS_STRING | 返回一个布尔值，指示指定表达式的类型是否为字符串。 |

在路由情况下，支持以下字符串函数：

| 函数 | 说明 |
| -------- | ----------- |
| CONCAT(x, y, …) | 返回一个字符串，该字符串是连接两个或多个字符串值的结果。 |
| LENGTH(x) | 返回指定字符串表达式的字符数。|
| LOWER(x) | 返回在将大写字符数据转换为小写后的字符串表达式。 |
| UPPER(x) | 返回在将小写字符数据转换为大写后的字符串表达式。 |
| SUBSTRING(string, start [, length]) | 返回字符串表达式的部分内容，该内容起于指定字符从零开始的位置，继续到指定长度或字符串结尾。 |
| INDEX_OF(string, fragment) | 返回第一个指定的字符串表达式中第一次出现第二个字符串表达式的起始位置，如果未找到字符串，则返回 -1。|
| STARTS_WITH(x, y) | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式开头。 |
| ENDS_WITH(x, y) | 返回一个布尔值，指示第一个字符串表达式是否以第二个字符串表达式结尾。 |
| CONTAINS(x,y) | 返回一个布尔值，该值指示第一个字符串表达式是否包含第二个字符串表达式。 |

## <a name="next-steps"></a>后续步骤

了解如何使用 [Azure IoT SDK](iot-hub-devguide-sdks.md) 在应用中执行查询。
