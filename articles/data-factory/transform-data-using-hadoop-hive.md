---
title: "在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂中的 Hive 活动在按需/自己的 HDInsight 群集上运行 Hive 查询。"
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: shengc
ms.openlocfilehash: 579df714910020e1e16e410a051c8b3773369dea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-hive-activity.md)
> * [版本 2 - 预览版](transform-data-using-hadoop-hive.md)

数据工厂[管道](concepts-pipelines-activities.md)中的 HDInsight Hive 活动会在[自己的](compute-linked-services.md#azure-hdinsight-linked-service)或[按需](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 群集上执行 Hive 查询。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（正式版 (GA)），请参阅 [V1 中的 Hive 活动](v1/data-factory-hive-activity.md)。

如果不熟悉 Azure 数据工厂，请在阅读本文之前，先通读 [Azure 数据工厂简介](introduction.md)，并学习[教程：转换数据](tutorial-transform-data-spark-powershell.md)。 

## <a name="syntax"></a>语法

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
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
| 属性            | 说明                              | 必选 |
| ------------------- | ---------------------------------------- | -------- |
| name                | 活动名称                     | 是      |
| description         | 描述活动用途的文本 | 否       |
| type                | 对于 Hive 活动，活动类型是 HDinsightHive | 是      |
| linkedServiceName   | 引用在数据工厂中注册为链接服务的 HDInsight 群集。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| scriptLinkedService | 对 Azure 存储链接服务的引用，该服务用于存储要执行的 Hive 脚本。 如果未指定此链接服务，则使用 HDInsight 链接服务中定义的 Azure 存储链接服务。 | 否       |
| scriptPath          | 提供由 scriptLinkedService 引用的 Azure 存储中存储的脚本文件的路径。 文件名称需区分大小写。 | 是      |
| getDebugInfo        | 指定何时将日志文件复制到 HDInsight 群集使用的（或者）scriptLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：None。 | 否       |
| arguments           | 指定 Hadoop 作业的参数数组。 参数以命令行参数的形式传递到每个任务。 | 否       |
| 定义             | 在 Hive 脚本中指定参数作为键/值对，以供引用。 | 否       |

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据： 

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [机器学习“批处理执行”活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)

