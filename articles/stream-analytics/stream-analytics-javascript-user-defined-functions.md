---
title: Azure 流分析 JavaScript 用户定义的函数
description: 本文介绍了流分析中的 JavaScript 用户定义函数。
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80235390"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Azure 流分析中 JavaScript 用户定义的函数
 
Azure 流分析支持以 JavaScript 编写的用户定义的函数。 利用 JavaScript 提供的丰富 **String**、**RegExp**、**Math**、**Array** 和 **Date** 方法，可以更轻松地创建包含流分析作业的复杂数据转换。

## <a name="overview"></a>概述

JavaScript 用户定义的函数支持仅用于计算的且不需要外部连接的无状态标量函数。 函数的返回值只能是标量（单个）值。 将某个 JavaScript 用户定义的函数添加到作业后，可在查询中的任意位置使用该函数，如同内置标量函数一样。

下面是 JavaScript 用户定义的函数可派上用场的一些情景：
* 使用 **Regexp_Replace()** 和 **Regexp_Extract()** 等正则表达式函数分析和处理字符串
* 解码和编码数据，例如，二进制到十六进制的转换
* 使用 JavaScript **数学**函数进行数学计算
* 执行排序、联接、查找和填充等数组操作

使用流分析中的 JavaScript 用户定义的函数无法实现以下目的：
* 调用外部 REST 终结点，例如，执行反向 IP 查找，或者从外部源提取引用数据
* 针对输入/输出执行自定义事件格式序列化或反序列化
* 创建自定义聚合

尽管函数定义中并不禁止 **Date.GetDate()** 或 **Math.random()** 等函数，但应该避免使用这些函数。 这些函数在每次被调用时**不会**返回相同的结果，并且 Azure 流分析服务不会保留函数调用和返回结果的日记。 当你或流分析服务重新启动某个作业时，如果某个函数针对相同的事件返回不同的结果，将无法保证可重复性。

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>向作业中添加 JavaScript 用户定义的函数

> [!NOTE]
> 这些步骤适用于配置为在云中运行的流分析作业。 如果流分析作业配置为在 Azure IoT Edge 上运行，请改用 Visual Studio 并[使用 C# 编写用户定义的函数](stream-analytics-edge-csharp-udf.md)。

若要在流分析作业中创建 JavaScript 用户定义的函数，请在“作业拓扑”下选择“函数”。 然后，从“+ 添加”下拉菜单中选择“JavaScript UDF”。 

![添加 JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

然后，你必须提供以下属性并选择“保存”  。

|properties|说明|
|--------|-----------|
|函数别名|输入一个名称以在查询中调用函数。|
|输出类型|JavaScript 用户定义的函数将向流分析查询返回的类型。|
|函数定义|每次从查询中调用 UDF 时将执行的 JavaScript 函数的实现。|

## <a name="test-and-troubleshoot-javascript-udfs"></a>对 JavaScript UDF 进行测试和故障排除 

可在任何浏览器中测试和调试 JavaScript UDF 逻辑。 流分析门户目前不支持调试和测试这些用户定义函数的逻辑。 函数按预期方式运行后，可以将其添加到流分析作业（如上所述），然后直接从查询调用它。 还可以使用[适用于 Visual Studio 的流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)测试包含 JavaScript UDF 的查询逻辑。

JavaScript 运行时错误被视为严重错误，可通过活动日志查看。 要检索日志，请在 Azure 门户中转到作业，然后选择“活动日志”。 

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>在查询中调用 JavaScript 用户定义的函数

可以使用以 **udf** 为前缀的函数别名轻松在查询中调用 JavaScript 函数。 下面是 JavaScript UDF 的一个示例，它将十六进制值转换为在流分析查询中调用的整数。

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>支持的 JavaScript 对象

Azure 流分析 JavaScript 用户定义的函数支持标准的内置 JavaScript 对象。 有关这些对象的列表，请参阅 [Global Objects](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)（全局对象）。

### <a name="stream-analytics-and-javascript-type-conversion"></a>流分析和 JavaScript 类型转换

流分析查询语言与 JavaScript 支持的类型有差别。 下表列出了两者之间的转换映射：

流分析 | JavaScript
--- | ---
bigint | Number（JavaScript 只能精确呈现最大 2^53 的整数）
DateTime | Date（JavaScript 仅支持毫秒）
double | Number
nvarchar(MAX) | String
Record | Object
Array | Array
Null | Null

下面是 JavaScript 到流分析的转换：

JavaScript | 流分析
--- | ---
Number | 如果数字已舍入并介于 long.MinValue 和 long.MaxValue 之间，则为 Bigint；否则为 double
Date | DateTime
String | nvarchar(MAX)
Object | Record
Array | Array
Null、Undefined | Null
其他任何类型（例如函数或错误） | 不支持（导致运行时错误）

JavaScript 语言区分大小写，JavaScript 代码中对象字段的大小写必须与传入数据中字段的大小写匹配。 兼容性级别为 1.0 的作业会将 SQL SELECT 语句中的字段转换为小写。 在兼容性级别 1.1 及更高级别下，SELECT 语句中的字段将具有与 SQL 查询中指定的相同的大小写。

## <a name="other-javascript-user-defined-function-patterns"></a>JavaScript 用户定义的函数的其他模式

### <a name="write-nested-json-to-output"></a>编写嵌套的 JSON 输出

如果后续处理步骤需要使用流分析作业输出作为输入并且要求采用 JSON 格式，可以编写要输出的 JSON 字符串。 以下示例调用 **JSON.stringify()** 函数封装输入的所有名称/值对，然后将其写入为输出中的单个字符串值。

**JavaScript 用户定义的函数定义：**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**示例查询：**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="next-steps"></a>后续步骤

* [机器学习 UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
