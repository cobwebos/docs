---
title: Azure 流分析中的用户定义函数
description: 本文概述了 Azure 流分析中的用户定义函数。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: d167c603ada885a1a4917c66bab110e4ce38cab4
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598362"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure 流分析中的用户定义函数

Azure 流分析中类似 SQL 的查询语言使得在流数据上实现实时分析逻辑变得非常容易。 流分析允许在查询中调用自定义函数，因此提高了灵活性。 下面的代码示例是一个名为 `sampleFunction` 的 UDF，该 UDF 接受一个参数（作业收到的每个输入记录），并将结果作为 `sampleResult` 写入到输出中。

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
* C# 用户定义函数（使用 Visual Studio） 
* Azure 机器学习 

可以将这些函数用于如下场景：使用机器学习模型进行实时评分、处理字符串、执行复杂的数学计算、对数据进行编码和解码。 

## <a name="limitations"></a>限制

用户定义函数是无状态函数，其返回值只能是标量值。 不能从这些用户定义函数中调用外部 REST 终结点，因为这可能会影响作业的性能。 

Azure 流分析不会记录所有函数调用和返回结果。 为了保证可重复性（例如，从旧的时间戳重新运行作业会再次产生相同的结果），不要使用 `Date.GetData()` 或 `Math.random()` 之类的函数，因为这些函数不会每次调用都返回相同的结果。  

## <a name="resource-logs"></a>资源日志

所有运行时错误被视为严重错误，并通过活动和资源日志进行呈现。 建议让函数处理所有异常和错误，并将有效结果返回到查询。 这会阻止作业进入[“失败”状态](job-states.md)。  

## <a name="exception-handling"></a>异常处理

在 Azure 流分析中使用数据时，数据处理过程中的任何异常都被视为灾难性故障。 用户定义函数可能会引发异常并导致处理停止。 若要避免此问题，请使用 JavaScript 或 c # 中的 try-catch 块来捕获代码执行过程*中的异常*。 可以记录和处理捕获的异常，而不会导致系统故障。 建议你始终将自定义代码包装在*try-catch*块中，以避免向处理引擎引发意外的异常。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析中的 JavaScript 用户定义函数](stream-analytics-javascript-user-defined-functions.md)
* [Azure 流分析 JavaScript 用户定义聚合](stream-analytics-javascript-user-defined-aggregates.md)
* [为 Azure 流分析作业开发 .NET Standard 用户定义函数](stream-analytics-edge-csharp-udf-methods.md)
* [将 Azure 流分析与 Azure 机器学习集成](machine-learning-udf.md)
