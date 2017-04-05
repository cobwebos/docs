---
title: "从 Azure 数据工厂调用 Spark 程序"
description: "了解如何使用 MapReduce 活动从 Azure 数据工厂调用 Spark 程序。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 3c7d109ec7fd92859cad4ded7422105e8094485c
ms.lasthandoff: 03/30/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>从数据工厂调用 Spark 程序
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

## <a name="introduction"></a>介绍
数据工厂[管道](data-factory-create-pipelines.md)中的 HDInsight Spark 活动在[你自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)的 HDInsight 群集上执行 Spark 程序。 本文基于[数据转换活动](data-factory-data-transformation-activities.md)一文，它概述了数据转换和受支持的转换活动。

> [!IMPORTANT]
> 如果你不熟悉 Azure 数据工厂，我们建议在阅读本文之前，先完成[构建第一个管道](data-factory-build-your-first-pipeline.md)教程。 有关数据工厂服务的概述，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)。 

## <a name="apache-spark-cluster-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Spark 群集
首先，遵循教程[在 Azure HDInsight 中创建 Apache Spark 群集](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md)中的说明，在 Azure HDInsight 中创建 Apache Spark 群集。 

## <a name="create-data-factory"></a>创建数据工厂 
下面是使用 Spark 活动创建数据工厂管道的典型步骤。  

1. 创建数据工厂。
2. 创建一个链接服务，用于将 Azure HDInsight 中的 Apache Spark 群集链接到数据工厂。
3. 目前，即使不生成任何输出，也必须为活动指定输出数据集。 若要创建 Azure Blob 数据集，请执行以下步骤：
    1. 创建一个链接服务，用于将 Azure 存储帐户链接到数据工厂。
    2. 创建引用 Azure 存储链接服务的数据集。  
3. 使用 Spark 活动创建引用步骤 2 中创建的 Apache HDInsight 链接服务的管道。 该活动配置为使用上一步骤中创建的数据集作为输出数据集。 输出数据集驱动计划（每小时、每日等）。 因此，即使该活动实际上不生成任何输出，也必须指定输出数据集。

有关创建数据工厂的详细分步说明，请参阅教程[构建第一个管道](data-factory-build-your-first-pipeline.md)。 本教程使用 HDInsight Hadoop 群集使用 Hive 活动，但步骤是类似的 Spark 活动使用 HDInsight Spark 群集。   

以下各节提供有关要在数据工厂中使用 Apache Spark 群集和 Spark 活动的数据工厂实体的信息。   

## <a name="hdinsight-linked-service"></a>HDInsight 链接服务
在数据工厂管道中使用 Spark 活动之前，请创建 HDInsight（你自己的）链接服务。 以下 JSON 代码片段显示指向 Azure HDInsight Spark 群集的 HDInsight 链接服务的定义。   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<nameofyoursparkcluster>.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> 目前，Spark 活动不支持将 Azure Data Lake Store 用作主存储的 Spark 群集，也不支持按需 HDInsight 链接服务。 

有关 HDInsight 链接服务和其他计算链接服务的详细信息，请参阅[数据工厂计算链接服务](data-factory-compute-linked-services.md)一文。 

## <a name="spark-activity-json"></a>Spark 活动 JSON
下面是包含 Spark 活动的管道的示例 JSON 定义：    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

注意以下几点： 
- type 属性设置为 HDInsightSpark。 
- rootPath 设置为 adfspark\\pyFiles，其中，adfspark 是 Azure Blob 容器，pyFiles 是该容器中的 fine 文件夹。 在此示例中，Azure Blob 存储是与 Spark 群集关联的存储。 可将文件上载到不同的 Azure 存储。 如果这样做，请创建 Azure 存储链接服务，将该存储帐户链接到数据工厂。 然后，指定链接服务的名称作为 sparkJobLinkedService 属性的值。 请参阅 [Spark 活动属性](#spark-activity-properties)，了解有关 Spark 活动支持的此属性及其他属性的详细信息。  
- entryFilePath 设置为 test.py，即 python 文件。 
- 通过使用参数属性，Spark 程序参数的值进行传递。 此示例中有两个参数：arg1、arg2。 
- getDebugInfo 属性设置为 Always，表示始终生成日志文件（成功或失败）。 
- sparkConfig 节指定一个 python 环境设置：worker.memory。 
- outputs 节包含一个输出数据集。 必须指定一个输出数据集，即使 spark 程序不会生成任何输出。 输出数据集驱动管道的计划（每小时、每日等）。     

type 属性（在 typeProperties 节中）中的这篇文章在后面进行介绍 [Spark 活动属性](#spark-activity-properties)部分。 

如前所述，必须指定活动的输出数据集，如这是什么驱动器管道（每小时、每日等）的计划。 此示例使用 Azure Blob 数据集。 若要创建 Azure Blob 数据集，首先需要创建 Azure 存储链接服务。 

下面是 Azure 存储链接服务和 Azure Blob 数据集的示例定义： 

**Azure 存储链接服务：**
```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>"
        }
    }
}
```
 

**Azure Blob 数据集：** 
```json
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "sparkoutput.txt",
            "folderPath": "spark/output/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

此数据集主要是虚拟数据集。 数据工厂使用的频率和间隔设置，并在管道的开始和结束时间内每日运行管道。 在示例管道定义中，开始和结束时间是仅一天远，因此管道只运行一次。 

## <a name="spark-activity-properties"></a>Spark 活动属性

下表描述了 JSON 定义中使用的 JSON 属性： 

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| name | 管道中活动的名称。 | 是 |
| description | 描述活动用途的文本。 | 否 |
| type | 此属性必须设置为 HDInsightSpark。 | 是 |
| linkedServiceName | 运行 Spark 程序的 HDInsight 链接服务的名称。 | 是 |
| rootPath | 包含 Spark 文件的 Azure Blob 容器和文件夹。 文件名称需区分大小写。 | 是 |
| entryFilePath | Spark 代码/包的根文件夹的相对路径 | 是 |
| className | 应用程序的 Java/Spark main 类 | 否 | 
| arguments | Spark 程序的命令行参数列表。 | 否 | 
| proxyUser | 用于模拟执行 Spark 程序的用户帐户 | 否 | 
| sparkConfig | Spark 配置属性。 | 否 | 
| getDebugInfo | 指定何时将 Spark 日志文件复制到 HDInsight 群集使用的（或者）sparkJobLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：None。 | 否 | 
| sparkJobLinkedService | 用于保存 Spark 作业文件、依赖项和日志的 Azure 存储链接服务。  如果未指定此属性的值，将使用与 HDInsight 群集关联的存储。 | 否 |

## <a name="folder-structure"></a>文件夹结构
与 Pig 和 Hive 活动不同，Spark 活动不支持内联脚本。 与 Pig/Hive 作业相比，Spark 作业的可扩展性更高。 对于 Spark 作业，可以提供多个依赖项，例如 jar 包（放在 java CLASSPATH 中）、python 文件（放在 PYTHONPATH 中）和其他任何文件。

在 HDInsight 链接服务引用的 Azure Blob 存储中创建以下文件夹结构。 然后，将依赖文件上载到 **entryFilePath** 表示的根文件夹中的相应子文件夹。 例如，将 python 文件上载到根文件夹的 pyFiles 子文件夹，将 jar 文件上载到根文件夹的 jars 子文件夹。 在运行时，数据工厂服务需要 Azure Blob 存储中的以下文件夹结构：     

| 路径 | 说明 | 必选 | 类型 |
| ---- | ----------- | -------- | ---- | 
| 。    | Spark 作业在存储链接服务中的根路径    | 是 | 文件夹 |
| &lt;用户定义&gt; | 指向 Spark 作业入口文件的路径 | 是 | 文件 | 
| ./jars | 此文件夹下的所有文件将上载并放置在群集的 java 类路径中 | 否 | 文件夹 |
| ./pyFiles | 此文件夹下的所有文件将上载并放置在群集的 PYTHONPATH 中 | 否 | 文件夹 |
| ./files | 此文件夹下的所有文件将上载并放置在执行器工作目录中 | 否 | 文件夹 |
| ./archives | 此文件夹下的所有文件未经压缩 | 否 | 文件夹 |
| ./logs | 来自 Spark 群集的日志所存储到的文件夹。| 否 | 文件夹 |

以下示例显示了一个在 HDInsight 链接服务引用的 Azure Blob 存储中包含两个 Spark 作业文件的存储。 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> 有关使用转换活动创建管道的完整演练，请参阅[创建传输数据的管道](data-factory-build-your-first-pipeline-using-editor.md)一文。 



## <a name="see-also"></a>另请参阅
* [Hive 活动](data-factory-hive-activity.md)
* [Pig 活动](data-factory-pig-activity.md)
* [MapReduce 活动](data-factory-map-reduce.md)
* [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
* [调用 R 脚本](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


