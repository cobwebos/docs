---
title: "Azure 流分析 JavaScript 用户定义的函数 | Microsoft 文档"
description: "带流分析和实时数据处理的 IoT 传感器标记和数据流"
keywords: "iot 解决方案, iot 入门, 工具"
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 822f0870396e1fa5fe2dfee3bae410aad3da1122
ms.openlocfilehash: 959fd48cb7d7f21385f6eea21f79ad26e8164bc0

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Azure 流分析 JavaScript 用户定义的函数
Azure 流分析支持以 JavaScript 编写的用户定义的函数 (UDF)。 利用 JavaScript 提供的丰富 String、RegExp、Math、Array 和 Date 方法，可以更轻松地创建包含流分析作业的复杂数据转换。

## <a name="overview"></a>概述
JavaScript UDF 支持仅用于计算的且不需要外部连接的无状态标量函数。 函数的返回值只能是标量（单个）值。 添加到作业后，该函数可在查询中的任何位置使用，如同内置标量函数一样。

下面是 JavaScript UDF 可派上用场的一些示例情景：
* 使用 Regexp_Replace() 和 Regexp_Extract() 等正则表达式函数分析和操作字符串
* 解码某个编码数据，例如，二进制到十六进制的转换
* 使用 JavaScript Math 函数进行数学计算
* 排序、联接、查找和填充等数组操作

下面是无法使用流分析中的 JavaScript UDF 实现的目的：
* 调用外部 REST 终结点，例如，执行反向 IP 查找，或者从外部源提取引用数据
* 针对输入/输出执行自定义事件格式序列化或反序列化
* 自定义聚合

尽管函数定义中并不禁止这样做，但应该避免使用 Date.GetDate() 或 Math.random() 等函数。 这些函数在每次被调用时**不会**返回相同的结果，并且 Azure 流分析服务不会保留函数调用和返回结果的日记。 因此，当你或流分析服务重新启动某个作业时，如果某个函数针对相同的事件返回不同的结果，将无法保证可重复性。

## <a name="adding-a-javascript-udf-from-azure-portal"></a>通过 Azure 门户添加 JavaScript UDF
若要在现有的流分析作业中创建一个简单的 JavaScript 用户定义的函数，请执行以下步骤。

1.  打开 Azure 门户。

2.  找到你的流分析作业，然后单击“作业拓扑”下面的函数链接。
 
3.  此时将显示现有函数的空列表，请单击“添加”图标添加新的 UDF。

4.  在“新建函数”边栏选项卡中，为“函数类型”选择“JavaScript”。编辑器中会出现默认的函数模板。
 
5.  输入 _hex2Int_ 作为 UDF 别名，然后按如下所示更改函数实现

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  单击“保存”按钮，该函数随即出现在函数列表中。 

7.  单击新函数 **hex2Int** 可以检查函数定义。 请注意，所有函数的别名前面都添加了“UDF”前缀。 需要在流分析查询中**结合该前缀**调用函数，在本例中为 **UDF.hex2Int**。
 
## <a name="calling-javascript-udf-in-a-query"></a>在查询中调用 JavaScript UDF

1. 单击“作业拓扑”下面的“查询”打开查询编辑器。 

2.  编辑查询，然后按如下所示调用刚刚添加的 UDF：

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  右键单击作业输入，上载示例数据文件 
 
4.  单击“测试”测试查询。


## <a name="supported-javascript-objects"></a>支持的 JavaScript 对象
Azure 流分析 JavaScript UDF 支持 JavaScript 语言的标准内置对象。 有关对象列表，请参阅有关[全局对象](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)的文档。

### <a name="stream-analytics-and-javascript-type-conversion"></a>流分析和 JavaScript 类型转换

流分析查询语言与 JavaScript 支持的类型有差别。 下表列出了两者之间的转换映射。


---
流分析 | JavaScript
--- | ---
bigint | 数字（请注意，JavaScript 只能精确呈现最大 2^53 的整数）
DateTime | 日期（请注意，JavaScript 仅支持毫秒） 
double | Number
nvarchar(MAX) | String
记录 | 对象
Array | Array
Null | Null


此外列出了 JavaScript 到 ASA 的转换。

---
JavaScript | 流分析
--- | ---
Number | 如果数字已舍入并介于 long.MinValue 和 long.MaxValue 之间，则为 Bigint；否则为 double
日期 | DateTime
String | nvarchar(MAX)
对象 | 记录
Array | Array
Null、Undefined | Null
其他任何类型，例如函数、错误，等等。 | 不支持 – 运行时错误

## <a name="troubleshooting"></a>故障排除
JavaScript 运行时错误被视为严重错误，可通过活动日志查看。 若要从 Azure 门户检索日志，请转到你的作业，然后单击“活动日志”。
 

## <a name="other-javascript-udf-usage-patterns"></a>其他 JavaScript UDF 使用模式

### <a name="writing-nested-json-to-output"></a>编写嵌套的 JSON 输出
如果后续处理步骤需要使用流分析作业输出作为输入并且要求采用 JSON 格式，则编写要输出的 JSON 字符串就是一项常见任务。 以下示例调用 JSON.stringify() 函数封装输入的所有名称/值对，并将其写入为输出中的单个字符串值。 

### <a name="javascript-udf-definition"></a>JavaScript UDF 定义：

```
function main(x) {
return JSON.stringify(x);
}
```

**示例查询：**
```
SELECT 
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--HONumber=Feb17_HO1-->


