---
title: "数据转换：处理和转换数据 |Microsoft Docs"
description: "了解如何在 Azure 数据工厂中利用 Hadoop、机器学习或 Azure Data Lake Analytics 转换或处理数据。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 39786731-1e4b-40a4-81b7-d06e127427aa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8f6c428d6b7d5b9cab823c2a2408b895bb2c05a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-in-azure-data-factory"></a>在 Azure 数据工厂中转换数据
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop 流式处理](data-factory-hadoop-streaming-activity.md)
> * [机器学习](data-factory-azure-ml-batch-execution-activity.md) 
> * [存储过程](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET 自定义](data-factory-use-custom-activities.md)

## <a name="overview"></a>概述
本文介绍了 Azure 数据工厂中的数据转换活动，可利用这些活动将原始数据转换和处理为预测和见解。 转换活动在计算环境（例如 Azure HDInsight 群集或 Azure Batch）中执行。 其提供了相关文章链接，内附各转换活动的详细信息。

数据工厂支持以下数据转换活动，这些活动可单独添加到[管道](data-factory-create-pipelines.md)，还可与其他活动关联在一起。

> [!NOTE]
> 有关分步说明演练，请参阅[使用 Hive 转换创建管道](data-factory-build-your-first-pipeline.md)。  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive 活动
数据工厂管道中的 HDInsight Hive 活动会在自己的或基于 Windows/Linux 的按需 HDInsight 群集上执行 Hive 查询。 有关此活动的详细信息，请参阅 [Hive 活动](data-factory-hive-activity.md)文章。 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig 活动
数据工厂管道中的 HDInsight Pig 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 Pig 查询。 有关此活动的详细信息，请参阅 [Pig 活动](data-factory-pig-activity.md)文章。 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 活动
数据工厂管道中的 HDInsight MapReduce 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 MapReduce 程序。 有关此活动的详细信息，请参阅 [MapReduce 活动](data-factory-map-reduce.md)文章。

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming 活动
数据工厂管道中的 HDInsight Streaming 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 HDInsight Streaming 程序。 有关此活动的详细信息，请参阅 [HDInsight Streaming 活动](data-factory-hadoop-streaming-activity.md)。

## <a name="hdinsight-spark-activity"></a>HDInsight Spark 活动
数据工厂管道中的 HDInsight Spark 活动在自己的 HDInsight 群集上执行 Spark 程序。 有关详细信息，请参阅[从 Azure 数据工厂调用 Spark 程序](data-factory-spark.md)。 

## <a name="machine-learning-activities"></a>机器学习活动
借助 Azure 数据工厂，可轻松创建相关管道，利用已发布的 Azure 机器学习 Web 服务进行预测分析。 借助 Azure 数据工厂管道中的[批处理执行活动](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity)，可调用 Azure 机器学习 Web 服务预测批处理中的数据。

随着时间推移，需要使用新的输入数据集重新定型机器学习评分实验中的预测模型。 完成重新定型后，可使用重新定型的机器学习模型更新评分 Web 服务。 通过[更新资源活动](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity)，可利用新的定型模型更新 Web 服务。  

有关这些机器学习活动的详细信息，请参阅[使用机器学习活动](data-factory-azure-ml-batch-execution-activity.md)。 

## <a name="stored-procedure-activity"></a>存储过程活动
可在数据工厂管道中使用 SQL Server 存储过程活动调用以下数据存储中的存储过程：Azure SQL 数据库、Azure SQL 数据仓库、企业中的 SQL Server 数据库或 Azure VM。 有关详细信息，请参阅[存储过程活动](data-factory-stored-proc-activity.md)文章。  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活动
Data Lake Analytics U-SQL 活动在 Azure Data Lake Analytics 群集上运行 U-SQL 脚本。 有关详细信息，请参阅 [Data Analytics U-SQL 活动](data-factory-usql-activity.md)文章。 

## <a name="net-custom-activity"></a>.NET 自定义活动
如果需要采用数据工厂不支持的方式转换数据，可以使用自己的数据处理逻辑创建自定义活动，并在管道中使用该活动。 可以使用 Azure Batch 服务或 Azure HDInsight 群集配置要运行的自定义 .NET 活动。 有关详细信息，请参阅[使用自定义活动](data-factory-use-custom-activities.md)文章。 

可以创建一项自定义活动，在安装了 R 的 HDInsight 群集上运行 R 脚本。 请参阅 [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)（使用 Azure 数据工厂运行 R 脚本）。 

## <a name="compute-environments"></a>计算环境
为计算环境创建链接服务，并在定义转换活动时使用该服务。 数据工厂支持两类计算环境。 

1. **按需**：此情况下，计算环境由数据工厂完全托管。 作业提交到进程数据前，数据工厂服务会自动创建计算环境，作业完成后则自动将其删除。 针对作业执行、群集管理和启动操作，可以配置和控制按需计算环境的粒度设置。 
2. **自带**：此情况下，可将自己的计算环境（例如 HDInsight 群集）注册为数据工厂中的链接服务。 计算环境由用户进行管理，数据工厂服务使用它执行活动。 

有关数据工厂支持的计算服务列表，请参阅 [计算链接服务](data-factory-compute-linked-services.md)文章。 

## <a name="summary"></a>摘要
Azure 数据工厂支持以下数据转换活动和活动的计算环境。 这些活动可单独添加到管道，也可与其他活动关联在一起。

| 数据转换活动 | 计算环境 |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop 流式处理](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [机器学习活动：批处理执行和更新资源](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [存储过程](data-factory-stored-proc-activity.md) |Azure SQL、Azure SQL 数据仓库或 SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] 或 Azure Batch |

