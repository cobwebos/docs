---
title: 在 Azure 数据工厂管道中执行 Azure 机器学习管道 |Microsoft Docs
description: 了解如何在 Azure 数据工厂管道中运行 Azure 机器学习管道。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: c05a0fde7019d9162a051a04696c2251301d70dc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490604"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>在 Azure 数据工厂中执行 Azure 机器学习管道

在 Azure 数据工厂管道中将 Azure 机器学习管道作为一个步骤运行。 机器学习执行管道活动可实现批预测方案，例如确定可能的贷款默认值、确定情绪并分析客户行为模式。

## <a name="syntax"></a>语法

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 需要
-------- | ----------- | -------------- | --------
name | 管道中活动的名称 | String | 是
type | Activity 的类型为 "AzureMLExecutePipeline" | String | 是
linkedServiceName | 链接服务到 Azure 机器学习 | 链接服务引用 | 是
mlPipelineId | 已发布的 Azure 机器学习管道的 ID | 字符串（或带有 resultType 字符串的表达式） | 是
experimentName | 运行历史记录试验机器学习管道运行的名称 | 字符串（或带有 resultType 字符串的表达式） | 否
mlPipelineParameters | 要传递到已发布 Azure 机器学习管道终结点的键、值对。 键必须与已发布机器学习管道中定义的管道参数的名称匹配 | 具有键值对的对象（或具有 resultType 对象的表达式） | 否
mlParentRunId | 父 Azure 机器学习管道运行 ID | 字符串（或带有 resultType 字符串的表达式） | 否
continueOnStepFailure | 如果步骤失败，是否在机器学习管道中继续执行其他步骤 | 布尔值 | 否

## <a name="next-steps"></a>后续步骤
请参阅以下文章了解如何以其他方式转换数据：

* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
