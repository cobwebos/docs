---
title: 使用 Hadoop MapReduce 活动转换数据
description: 了解如何通过从 Azure 数据工厂在 Azure HDInsight 群集上运行 Hadoop MapReduce 程序来处理数据。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/16/2018
ms.openlocfilehash: 5d38e3126442bcf34c96cead2b2ea59507b50b8c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912863"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Hadoop MapReduce 活动转换数据

> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](v1/data-factory-map-reduce.md)
> * [当前版本](transform-data-using-hadoop-map-reduce.md)

数据工厂[管道](concepts-pipelines-activities.md)中的 HDInsight MapReduce 活动会在[自己的](compute-linked-services.md#azure-hdinsight-linked-service)或[按需](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 群集上调用 MapReduce 程序。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。

如果不熟悉 Azure 数据工厂，请在阅读本文之前，先通读 [Azure 数据工厂简介](introduction.md)，并学习教程：[教程：转换数据](tutorial-transform-data-spark-powershell.md)。

请参阅 [Pig](transform-data-using-hadoop-pig.md) 和 [Hive](transform-data-using-hadoop-hive.md)，深入了解如何使用 HDInsight Pig 和 Hive 活动通过管道在 HDInsight 群集上运行 Pig/Hive 脚本。

## <a name="syntax"></a>语法

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>语法详细信息

| properties          | 描述                              | 需要 |
| ----------------- | ---------------------------------------- | -------- |
| name              | 活动名称                     | 是      |
| description       | 描述活动用途的文本 | No       |
| type              | 对于 MapReduce 活动，活动类型是 HDinsightMapReduce | 是      |
| linkedServiceName | 引用在数据工厂中注册为链接服务的 HDInsight 群集。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| className         | 要执行的类的名称         | 是      |
| jarLinkedService  | 对 Azure 存储链接服务的引用，该服务用于存储 Jar 文件。 如果未指定此链接服务，则使用 HDInsight 链接服务中定义的 Azure 存储链接服务。 | No       |
| jarFilePath       | 提供由 jarLinkedService 引用的 Azure 存储中存储的 Jar 文件的路径。 文件名称需区分大小写。 | 是      |
| jarlibs           | 作业引用的 Jar 库文件路径的字符串数组，该作业存储在 jarLinkedService 中定义的 Azure 存储中。 文件名称需区分大小写。 | No       |
| getDebugInfo      | 指定何时将日志文件复制到 HDInsight 群集使用的（或者）jarLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：None。 | No       |
| arguments         | 指定 Hadoop 作业的参数数组。 参数以命令行参数的形式传递到每个任务。 | No       |
| 定义           | 在 Hive 脚本中指定参数作为键/值对，以供引用。 | No       |



## <a name="example"></a>示例
可使用 HDInsight MapReduce 活动在 HDInsight 群集中运行任何 MapReduce jar 文件。 在管道的以下示例 JSON 定义中，配置了HDInsight 活动，以便运行 Mahout JAR 文件。

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
可以在**参数**部分为 MapReduce 程序指定任意参数。 运行时，可在 MapReduce 框架中看到几个额外的参数（例如：mapreduce.job.tags）。 要区分自己的参数和 MapReduce 参数，请考虑将选项和值同时作为参数使用，如下例所示（-s、--input、--output 等选项后紧跟有相应的值）。

## <a name="next-steps"></a>后续步骤
请参阅以下文章了解如何以其他方式转换数据：

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [机器学习“批处理执行”活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
