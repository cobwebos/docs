---
title: 在 Azure 数据工厂中使用 Hadoop Pig 活动转换数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂中的 Pig 活动在按需或自己的 HDInsight 群集上运行 Pig 脚本。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 914bc37552a80886df16ed69fba4e31b3f22ac22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399516"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Hadoop Pig 活动转换数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1](v1/data-factory-pig-activity.md)
> * [当前版本](transform-data-using-hadoop-pig.md)

数据工厂[管道](concepts-pipelines-activities.md)中的 HDInsight Pig 活动会在[自己的](compute-linked-services.md#azure-hdinsight-linked-service)或[按需](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 群集上执行 Pig 查询。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。

如果不熟悉 Azure 数据工厂，请在阅读本文之前，先通读 [Azure 数据工厂简介](introduction.md)，并学习[教程：转换数据](tutorial-transform-data-spark-powershell.md)。 

## <a name="syntax"></a>语法

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>语法详细信息

| 属性            | 说明                              | 需要 |
| ------------------- | ---------------------------------------- | -------- |
| 名称                | 活动名称                     | 是      |
| description         | 描述活动用途的文本 | 否       |
| type                | 对于 Hive 活动，活动类型是 HDinsightPig | 是      |
| linkedServiceName   | 引用在数据工厂中注册为链接服务的 HDInsight 群集。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| scriptLinkedService | 对 Azure 存储链接服务的引用，该服务用于存储要执行的 Pig 脚本。 如果未指定此链接服务，则使用 HDInsight 链接服务中定义的 Azure 存储链接服务。 | 否       |
| scriptPath          | 提供由 scriptLinkedService 引用的 Azure 存储中存储的脚本文件的路径。 文件名称需区分大小写。 | 否       |
| getDebugInfo        | 指定何时将日志文件复制到 HDInsight 群集使用的（或者）scriptLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：无。 | 否       |
| arguments           | 指定 Hadoop 作业的参数数组。 参数以命令行参数的形式传递到每个任务。 | 否       |
| 定义             | 在 Pig 脚本中指定参数作为键/值对，以供引用。 | 否       |

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据： 

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [机器学习“批处理执行”活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
