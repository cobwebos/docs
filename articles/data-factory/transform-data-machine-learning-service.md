---
title: 执行 Azure 机器学习管道
description: 了解如何在 Azure 数据工厂管道中运行 Azure 机器学习管道。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: f033651eb7e52ba60cce9b74941a4ef0eb376d2b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418994"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>在 Azure 数据工厂中执行 Azure 机器学习管道

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 Azure 数据工厂管道中运行 Azure 机器学习管道作为步骤。 机器学习执行管道活动支持批处理预测方案，例如识别可能的贷款默认值、确定情绪和分析客户行为模式。

以下视频包含 6 分钟的介绍和演示此功能。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

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

properties | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
name | 管道中活动的名称 | 字符串 | 是
type | 活动类型为"AzureML 执行管道" | 字符串 | 是
linkedServiceName | 链接到 Azure 机器学习的服务 | 链接服务引用 | 是
毫升里线Id | 已发布的 Azure 机器学习管道的 ID | 字符串（或带有 resultType 字符串的表达式） | 是
实验名称 | 运行机器学习管道运行的历史实验名称 | 字符串（或带有 resultType 字符串的表达式） | 否
mlPipeline 参数 | 键，要传递给已发布的 Azure 机器学习管道终结点的值对。 键必须与已发布的机器学习管道中定义的管道参数的名称匹配 | 具有键值对的对象（或具有结果类型对象的表达式） | 否
毫升家长鲁尼 | 父 Azure 机器学习管道运行 ID | 字符串（或带有 resultType 字符串的表达式） | 否
继续步进失败 | 如果步骤失败，是否继续执行机器学习管道中的其他步骤 | boolean | 否

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据：

* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [猪活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流活动](transform-data-using-hadoop-streaming.md)
* [火花活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
