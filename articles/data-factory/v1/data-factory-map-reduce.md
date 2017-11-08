---
title: "从 Azure 数据工厂调用 MapReduce 程序"
description: "了解如何通过从 Azure 数据工厂在 Azure HDInsight 群集上运行 MapReduce 程序来处理数据。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: e5fd49c6b269b5f247440c2bc91680fc77fc296c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>从数据工厂调用 MapReduce 程序
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 活动](data-factory-hive-activity.md) 
> * [Pig 活动](data-factory-pig-activity.md)
> * [MapReduce 活动](data-factory-map-reduce.md)
> * [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
> * [Spark 活动](data-factory-spark.md)
> * [机器学习批处理执行活动](data-factory-azure-ml-batch-execution-activity.md)
> * [机器学习更新资源活动](data-factory-azure-ml-update-resource-activity.md)
> * [存储过程活动](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活动](data-factory-usql-activity.md)
> * [.NET 自定义活动](data-factory-use-custom-activities.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[在数据工厂版本 2 中使用 MapReduce 活动转换数据](../transform-data-using-hadoop-map-reduce.md)。


数据工厂[管道](data-factory-create-pipelines.md)中的 HDInsight MapReduce 活动会在[自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或基于 Windows/Linux 的[按需](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 群集上执行 MapReduce 程序。 本文基于[数据转换活动](data-factory-data-transformation-activities.md)一文，它概述了数据转换和受支持的转换活动。

> [!NOTE] 
> 如果是刚开始接触 Azure 数据工厂，请仔细阅读 [Azure 数据工厂简介](data-factory-introduction.md)，并学习[教程：生成首个数据管道](data-factory-build-your-first-pipeline.md)，然后再阅读本文。  

## <a name="introduction"></a>介绍
Azure 数据工厂中的管道通过使用链接计算服务来处理链接存储服务中的数据。 它包含一系列活动，其中每个活动执行特定的处理操作。 本文介绍如何使用 HDInsight MapReduce 活动。

请参阅 [Pig](data-factory-pig-activity.md) 和 [Hive](data-factory-hive-activity.md)，深入了解如何通过使用 HDInsight Pig 和 Hive 活动在管道中基于 Windows/Linux 的 HDInsight 群集上运行 Pig/Hive 脚本。 

## <a name="json-for-hdinsight-mapreduce-activity"></a>HDInsight MapReduce 活动的 JSON
在 HDInsight 活动的 JSON 定义中： 

1. 将**活动**的**类型**设置为 **HDInsight**。
2. 为 **className** 属性指定类名。
3. 为 **jarFilePath** 属性指定 JAR 文件的路径（包含文件名）。
4. 为 **jarLinkedService** 属性指定指向 Azure Blob 存储（包含 JAR 文件）的链接服务。   
5. 在**参数**部分为 MapReduce 程序指定任意参数。 运行时，可在 MapReduce 框架中看到几个额外的参数（例如：mapreduce.job.tags）。 要区分自己的参数和 MapReduce 参数，请考虑将选项和值同时作为参数使用，如下例所示（-s、--input、--output 等选项后紧跟有相应的值）。

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
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
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
可使用 HDInsight MapReduce 活动在 HDInsight 群集中运行任何 MapReduce jar 文件。 在管道的以下示例 JSON 定义中，配置了HDInsight 活动，以便运行 Mahout JAR 文件。

## <a name="sample-on-github"></a>GitHub 上的示例
可从 [GitHub 上的数据工厂示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample)中下载使用 HDInsight MapReduce 活动的示例。  

## <a name="running-the-word-count-program"></a>运行字数统计程序
此示例中的管道在 Azure HDInsight 群集上运行字数统计 Map/Reduce 程序。   

### <a name="linked-services"></a>链接服务
首先，创建一个链接服务，将 Azure HDInsight 群集使用的 Azure 存储链接到 Azure 数据工厂。 如果要复制/粘贴下面的代码，请不要忘记将“帐户名”和“帐户密钥”替换为自己的 Azure 存储 的名称和密钥。 

#### <a name="azure-storage-linked-service"></a>Azure 存储链接服务

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 链接服务
接下来，创建一个链接服务，将 Azure HDInsight 群集链接到 Azure 数据工厂。 如果要复制/粘贴下面的代码，请将 **HDInsight 群集名称**替换为自己的 HDInsight 群集的名称，并更改用户名和密码值。   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>数据集
#### <a name="output-dataset"></a>输出数据集
此示例中的管道不采用任何输入。 为 HDInsight MapReduce 活动指定一个输出数据集。 该数据集仅是推动管道计划所需的一个虚拟数据集。  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>管道
此示例中的管道仅具有一个 HDInsightMapReduce 类型的活动。 JSON 中的一些重要属性有： 

| 属性 | 说明 |
|:--- |:--- |
| type |类型必须设置为 **HDInsightMapReduce**。 |
| className |类名为：**wordcount** |
| jarFilePath |包含该类的 jar 文件的路径。 如果要复制/粘贴下面的代码，请不要忘记更改群集的名称。 |
| jarLinkedService |包含 jar 文件的 Azure 存储链接服务。 此链接服务指与 HDInsight 群集关联的存储。 |
| arguments |字数统计程序具有两个参数（一个输入和一个输出）。 输入文件是 davinci.txt 文件。 |
| frequency/interval |这些属性的值与输出数据集匹配。 |
| linkedServiceName |指先前创建的 HDInsight 链接服务。 |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>运行 Spark 程序
可以通过 MapReduce 活动在 HDInsight Spark 群集上运行 Spark 程序。 有关详细信息，请参阅[从 Azure 数据工厂调用 Spark 程序](data-factory-spark.md)。  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## <a name="see-also"></a>另请参阅
* [Hive 活动](data-factory-hive-activity.md)
* [Pig 活动](data-factory-pig-activity.md)
* [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
* [调用 Spark 程序](data-factory-spark.md)
* [调用 R 脚本](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

