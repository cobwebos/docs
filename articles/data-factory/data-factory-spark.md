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
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 8be5e1525a7c481de5cb02edd26da305af2d4798
ms.lasthandoff: 03/18/2017


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

## <a name="hdinsight-linked-service"></a>HDInsight 链接服务
在数据工厂管道中使用 Spark 活动之前，请创建 HDInsight（你自己的）链接服务。 以下 JSON 代码片段显示指向你自己的 Azure HDInsight Spark 群集的 HDInsight 链接服务的定义。   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> 目前，Spark 活动不支持将 Data Lake Store 用作为主存储的 Spark 群集，也不支持按需 HDInsight 链接服务。 

有关 HDInsight 链接服务和其他计算链接服务的详细信息，请参阅[数据工厂计算链接服务](data-factory-compute-linked-services.md)一文。 

## <a name="spark-activity-json"></a>Spark 活动 JSON
下面是 Spark 活动的示例 JSON 定义：    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
下表描述了 JSON 定义中使用的 JSON 属性： 

| 属性 | 说明 | 必选 |
| -------- | ----------- | -------- |
| name | 管道中活动的名称。 | 是 |
| description | 描述活动用途的文本。 | 否 |
| type | 此属性必须设置为 HDInsightSpark。 | 是 |
| linkedServiceName | 对运行 Spark 程序的 HDInsight 链接服务的引用。 | 是 |
| rootPath | 包含 Spark 文件的 Azure Blob 容器和文件夹。 文件名称需区分大小写。 | 是 |
| entryFilePath | Spark 代码/包的根文件夹的相对路径 | 是 |
| className | 应用程序的 Java/Spark main 类 | 否 | 
| arguments | Spark 程序的命令行参数列表。 | 否 | 
| proxyUser | 用于模拟执行 Spark 程序的用户帐户 | 否 | 
| sparkConfig | Spark 配置属性 | 否 | 
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


