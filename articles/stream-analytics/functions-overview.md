---
title: Azure 流分析中的用户定义函数
description: 本文概述了 Azure 流分析中的用户定义函数。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 45e766c624ee96f7faa06fb07d00349e620a4c0a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133489"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure 流分析中的用户定义函数

Azure 流分析中的类似 SQL 的查询语言使实现流式处理数据的实时分析逻辑变得更加容易。 流分析通过在查询中调用的自定义函数提供额外的灵活性。 下面的代码示例是一个名`sampleFunction`为的 UDF，它接受一个参数，作业接收的每个输入记录，并将结果以形式`sampleResult`写入输出。

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>函数类型

Azure 流分析支持以下四种函数类型： 

* JavaScript 用户定义的函数 
* JavaScript 用户定义的聚合 
* C # 用户定义函数（使用 Visual Studio） 
* Azure 机器学习 

您可以使用这些函数来实现以下方案：使用机器学习模型、字符串操作、复杂的数学计算、编码和解码数据等。 

## <a name="limitations"></a>限制

用户定义函数是无状态的，并且返回值只能是标量值。 不能从这些用户定义的函数中调用外部 REST 终结点，因为这可能会影响作业的性能。 

Azure 流分析不会记录所有函数调用和返回结果。 为了保证可重复性，例如，从较旧的时间戳重新运行作业将再次生成相同的结果-请勿使用`Date.GetData()`或`Math.random()`等函数，因为这些函数不会为每个调用返回相同的结果。  

## <a name="resource-logs"></a>资源日志

所有运行时错误被视为严重错误，并通过活动和资源日志进行呈现。 建议函数处理所有异常和错误，并将有效结果返回到查询。 这会阻止你的作业进入[失败状态](job-states.md)。  


## <a name="next-steps"></a>后续步骤

* [Azure 流分析中 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
* [Azure 流分析 JavaScript 用户定义的聚合](stream-analytics-javascript-user-defined-aggregates.md)
* [为 Azure 流分析作业开发 .NET Standard 用户定义的函数](stream-analytics-edge-csharp-udf-methods.md)
* [将 Azure 流分析与 Azure 机器学习集成](machine-learning-udf.md)

