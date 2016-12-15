---
title: "Hadoop 流式处理活动"
description: "了解如何使用 Azure 数据工厂中的 Hadoop Streaming 活动在自己/按需的 HDInsight 群集上运行 Hadoop Streaming 程序。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: a4121f8857fa9eaeb1cf1bca70e29666f6a04f63
ms.openlocfilehash: 58c0e0cfd862bc421c3b4a9ad042283a22656460


---
# <a name="hadoop-streaming-activity"></a>Hadoop 流式处理活动
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop 流式处理](data-factory-hadoop-streaming-activity.md)
> * [机器学习](data-factory-azure-ml-batch-execution-activity.md) 
> * [存储过程](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET 自定义](data-factory-use-custom-activities.md)

可使用 HDInsightStreamingActivity 活动调用 Azure 数据工厂管道中的 Hadoop Streaming 作业。 以下 JSON 片段显示在管道 JSON 文件中使用 HDInsightStreamingActivity 的语法。 

数据工厂[管道](data-factory-create-pipelines.md)中的 HDInsight Streaming 活动将在[你自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或基于 Windows/Linux 的[按需](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 群集上执行 HDInsight Streaming 程序。 本文基于[数据转换活动](data-factory-data-transformation-activities.md)一文，它概述了数据转换和受支持的转换活动。

## <a name="json-sample"></a>JSON 示例
HDInsight 群集使用示例程序（wc.exe 和 cat.exe）和数据 (davinci.txt) 自动填充。 默认情况下，HDInsight 群集所用的容器名称就是群集本身的名称。 例如，如果群集名称为 myhdicluster，则关联的 blob 容器名称也是 myhdicluster。 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

请注意以下几点：

1. 将 **linkedServiceName** 设置为链接服务的名称，该服务指向运行流式处理 MapReduce 作业的 HDInsight 群集。
2. 将活动类型设置为 **HDInsightStreaming**。
3. 对于 **mapper** 属性，指定映射器可执行文件的名称。 在示例中，cat.exe 即是映射器可执行文件。
4. 对于 **reducer** 属性，指定减压器可执行文件的名称。 在示例中，wc.exe 即是减压器可执行文件。
5. 对于 **input** 类型属性，指定映射器的输入文件（包括位置）。 在此示例中：blob 容器为 "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample，文件夹为 example/data/Gutenberg，blob 为 davinci.txt。
6. 对于 **output** 类型属性，指定减压器的输出文件（包括位置）。 将 Hadoop Streaming 作业的输出写入到为该属性指定的位置。
7. 在“filePaths”部分，指定映射器和减压器可执行文件的路径。 在此示例中：blob 容器为 "adfsample/example/apps/wc.exe"adfsample，文件夹为 example/apps，可执行文件为 wc.exe。
8. 对于 **fileLinkedService** 属性，指定表示 Azure 存储（包含“filePaths”部分中指定的文件）的 Azure 存储链接服务。
9. 对于 **arguments** 属性，指定流式处理作业的参数。
10. **getDebugInfo** 属性是可选元素。 将其设置为“Failure”时，仅针对失败下载日志。 将其设置为“All”时，不考虑执行状态，始终下载日志。

> [!NOTE]
> 如以上示例所示，为 Hadoop Streaming 活动的 **outputs** 属性指定输出数据集。 该数据集仅是推动管道计划所需的一个虚拟数据集。 无需为活动的 **inputs** 属性指定任何输入数据集。  
> 
> 

## <a name="example"></a>示例
此演练中的管道在 Azure HDInsight 群集上运行字数统计流式处理 Map/Reduce 程序。 

### <a name="linked-services"></a>链接服务
#### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
首先，创建一个链接服务，将 Azure HDInsight 群集使用的 Azure 存储链接到 Azure 数据工厂。 如果要复制/粘贴以下代码，请记住将帐户名和帐户密钥替换为自己的 Azure 存储的名称和密钥。 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 链接服务
接下来，创建一个链接服务，将 Azure HDInsight 群集链接到 Azure 数据工厂。 如果要复制/粘贴下面的代码，请将 HDInsight 群集名称替换为自己的 HDInsight 群集的名称，并更改用户名和密码。 

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

### <a name="datasets"></a>数据集
#### <a name="output-dataset"></a>输出数据集
此示例中的管道不采用任何输入。 为 HDInsight Streaming 活动指定输出数据集。 该数据集仅是推动管道计划所需的一个虚拟数据集。 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>管道
此示例中的管道仅具有一个 **HDInsightStreaming** 类型的活动。 

HDInsight 群集使用示例程序（wc.exe 和 cat.exe）和数据 (davinci.txt) 自动填充。 默认情况下，HDInsight 群集所用的容器名称就是群集本身的名称。 例如，如果群集名称为 myhdicluster，则关联的 blob 容器名称也是 myhdicluster。  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>另请参阅
* [Hive 活动](data-factory-hive-activity.md)
* [Pig 活动](data-factory-pig-activity.md)
* [MapReduce 活动](data-factory-map-reduce.md)
* [调用 Spark 程序](data-factory-spark.md)
* [调用 R 脚本](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Nov16_HO3-->


