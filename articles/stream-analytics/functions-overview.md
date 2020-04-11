---
title: Azure 流分析中用户定义的函数
description: 本文概述了 Azure 流分析中用户定义的函数。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b29d66e8bb213fbbb162c3249f022e0783f9f62f
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115581"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Azure 流分析中用户定义的函数

Azure 流分析中类似 SQL 的查询语言便于在流数据上实现实时分析逻辑。 流分析通过查询中调用的自定义函数提供了额外的灵活性。 以下代码示例是一个 UDF，它接受`sampleFunction`一个参数，每个输入记录作业接收，结果写入输出为`sampleResult`。

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>函数类型

Azure 流分析支持以下四种功能类型： 

* JavaScript 用户定义的函数 
* JavaScript 用户定义的聚合 
* C# 用户定义的功能（使用可视化工作室） 
* Azure 机器学习 

您可以将这些函数用于使用机器学习模型、字符串操作、复杂数学计算、编码和解码数据等方案。 

## <a name="limitations"></a>限制

用户定义的函数是无状态的，返回值只能是标量值。 您不能从这些用户定义的函数向外部 REST 终结点调用，因为它可能会影响作业的性能。 

Azure 流分析不会记录所有函数调用和返回的结果。 为了保证可重复性（例如，从较旧的时间戳重新运行作业）将再次生成相同的结果，请不要使用 或`Date.GetData()``Math.random()`等函数，因为这些函数不会为每个调用返回相同的结果。  

## <a name="diagnostic-logs"></a>诊断日志

任何运行时错误都被视为致命错误，并通过活动和诊断日志显示。 建议函数处理所有异常和错误，并将有效结果返回给查询。 这将阻止您的作业进入["失败"状态](job-states.md)。  


## <a name="next-steps"></a>后续步骤

* [Azure 流分析中 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
* [Azure 流分析 JavaScript 用户定义的聚合](stream-analytics-javascript-user-defined-aggregates.md)
* [为 Azure 流分析作业开发 .NET 标准用户定义的函数](stream-analytics-edge-csharp-udf-methods.md)
* [将 Azure 流分析与 Azure 机器学习集成](machine-learning-udf.md)

