---
title: "在 Azure 数据工厂中使用 Spark 活动转换数据 | Microsoft Docs"
description: "了解如何通过使用 Spark 活动从 Azure 数据工厂管道运行 Spark 程序来转换数据。"
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
ms.date: 08/10/2017
ms.author: shengc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ecef890d26aa097d9a5db63555ae59e16e80c9e1
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Spark 活动转换数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-spark.md)
> * [版本 2 - 预览版](transform-data-using-spark.md)


Spark 活动是 Azure 数据工厂支持的[数据转换活动](transform-data.md)之一。 此活动在 Azure HDInsight 中 Apache Spark 群集上运行指定的 Spark 程序。    

> [!NOTE]
> 本文适用于目前处于预览状态的版本 2 的数据工厂。 如果使用数据工厂服务版本 1（正式版 (GA)），请参阅 [V1 中的 Spark 活动](v1/data-factory-spark.md)。

> [!IMPORTANT]
> Spark 活动不支持那些使用 Azure Data Lake Store 作为主存储的 HDInsight Spark 群集。

## <a name="spark-activity-properties"></a>Spark 活动属性
下面是 Spark 活动的示例 JSON 定义：    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

下表描述了 JSON 定义中使用的 JSON 属性：

| 属性              | 说明                              | 必选 |
| --------------------- | ---------------------------------------- | -------- |
| name                  | 管道中活动的名称。    | 是      |
| description           | 描述活动用途的文本。  | 否       |
| type                  | 对于 Spark 活动，活动类型是 HDInsightSpark。 | 是      |
| linkedServiceName     | 运行 Spark 程序的 HDInsight Spark 链接服务的名称。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。 | 是      |
| SparkJobLinkedService | 用于保存 Spark 作业文件、依赖项和日志的 Azure 存储链接服务。  如果未指定此属性的值，将使用与 HDInsight 群集关联的存储。 | 否       |
| rootPath              | 包含 Spark 文件的 Azure Blob 容器和文件夹。 文件名称需区分大小写。 有关此文件夹结构的详细信息，请参阅“文件夹结构”部分（下一部分）。 | 是      |
| entryFilePath         | Spark 代码/包的根文件夹的相对路径 | 是      |
| className             | 应用程序的 Java/Spark main 类      | 否       |
| arguments             | Spark 程序的命令行参数列表。 | 否       |
| proxyUser             | 用于模拟执行 Spark 程序的用户帐户 | 否       |
| sparkConfig           | 指定在以下主题中列出的 Spark 配置属性的值：[Spark 配置 - 应用程序属性](https://spark.apache.org/docs/latest/configuration.html#available-properties)。 | 否       |
| getDebugInfo          | 指定何时将 Spark 日志文件复制到 HDInsight 群集使用的（或者）sparkJobLinkedService 指定的 Azure 存储。 允许的值：None、Always 或 Failure。 默认值：None。 | 否       |

## <a name="folder-structure"></a>文件夹结构
与 Pig/Hive 作业相比，Spark 作业的可扩展性更高。 对于 Spark 作业，可以提供多个依赖项，例如 jar 包（放在 java CLASSPATH 中）、python 文件（放在 PYTHONPATH 中）和其他任何文件。

在 HDInsight 链接服务引用的 Azure Blob 存储中创建以下文件夹结构。 然后，将依赖文件上传到 **entryFilePath** 表示的根文件夹中的相应子文件夹。 例如，将 python 文件上传到根文件夹的 pyFiles 子文件夹，将 jar 文件上传到根文件夹的 jars 子文件夹。 在运行时，数据工厂服务需要 Azure Blob 存储中的以下文件夹结构：     

| 路径                  | 说明                              | 必选 | 类型   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`（根）            | Spark 作业在存储链接服务中的根路径 | 是      | 文件夹 |
| &lt;用户定义&gt; | 指向 Spark 作业入口文件的路径 | 是      | 文件   |
| ./jars                | 此文件夹下的所有文件将上传并放置在群集的 java 类路径中 | 否       | 文件夹 |
| ./pyFiles             | 此文件夹下的所有文件将上传并放置在群集的 PYTHONPATH 中 | 否       | 文件夹 |
| ./files               | 此文件夹下的所有文件将上传并放置在执行器工作目录中 | 否       | 文件夹 |
| ./archives            | 此文件夹下的所有文件未经压缩 | 否       | 文件夹 |
| ./logs                | 包含 Spark 群集的日志的文件夹。 | 否       | 文件夹 |

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
## <a name="next-steps"></a>后续步骤
请参阅以下文章了解如何以其他方式转换数据： 

* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [机器学习批处理执行活动](transform-data-using-machine-learning.md)
* [存储过程活动](transform-data-using-stored-procedure.md)

