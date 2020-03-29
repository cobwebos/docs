---
title: 转换数据
description: 了解如何在 Azure 数据工厂中利用 Hadoop、机器学习或 Azure Data Lake Analytics 转换或处理数据。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 1f920f2672c19455a8e8ac979e8d6d1eb14d4c35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924298"
---
# <a name="transform-data-in-azure-data-factory"></a>在 Azure 数据工厂中转换数据

> [!div class="op_single_selector"]
> * [映射数据流](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [Mapreduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [机器学习](transform-data-using-machine-learning.md) 
> * [存储过程](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET 自定义](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>概述
本文介绍了 Azure 数据工厂中的数据转换活动，可利用这些活动将原始数据转换和处理为大规模预测和见解。 转换活动在计算环境中执行，如 Azure 数据块或 Azure HDInsight。 其提供了相关文章链接，内附各转换活动的详细信息。

数据工厂支持以下数据转换活动，这些活动可单独添加到[管道](concepts-pipelines-activities.md)，还可与其他活动关联在一起。

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>使用数据流在 Azure 数据工厂进行本机转换

### <a name="mapping-data-flows"></a>映射数据流

映射数据流是在 Azure 数据工厂中直观地设计的数据转换。 数据流允许数据工程师无需编写代码即可开发图形数据转换逻辑。 生成的数据流在 Azure 数据工厂管道中执行，这些活动使用横向扩展的 Spark 群集。 数据流活动可以通过现有的数据工厂调度、控制、流和监视功能进行操作。 有关详细信息，请参阅[映射数据流](concepts-data-flow-overview.md)。

### <a name="wrangling-data-flows"></a>旋转数据流

通过 Azure 数据工厂中旋转数据流，您可以迭代云规模进行无代码数据准备。 旋转数据流与[在线电源查询](https://docs.microsoft.com/power-query/)集成，并通过火花执行使 Power Query M 函数可用于云级数据争鸣。 有关详细信息，请参阅[争论数据流](wrangling-data-flow-overview.md)。

## <a name="external-transformations"></a>外部转换

或者，您可以自行编写转换和管理外部计算环境。

### <a name="hdinsight-hive-activity"></a>HDInsight Hive 活动
数据工厂管道中的 HDInsight Hive 活动会在自己的或基于 Windows/Linux 的按需 HDInsight 群集上执行 Hive 查询。 有关此活动的详细信息，请参阅 [Hive 活动](transform-data-using-hadoop-hive.md)一文。 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig 活动
数据工厂管道中的 HDInsight Pig 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 Pig 查询。 有关此活动的详细信息，请参阅 [Pig 活动](transform-data-using-hadoop-pig.md)一文。 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 活动
数据工厂管道中的 HDInsight MapReduce 活动会在自己或基于 Windows/Linux 的按需 HDInsight 群集上执行 MapReduce 程序。 有关此活动的详细信息，请参阅 [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)一文。

### <a name="hdinsight-streaming-activity"></a>HDInsight Streaming 活动
数据工厂管道中的 HDInsight 流式处理活动会在自己的或基于 Windows/Linux 的按需 HDInsight 群集上执行 Hadoop 流式处理程序。 有关此活动的详细信息，请参阅 [HDInsight Streaming 活动](transform-data-using-hadoop-streaming.md)。

### <a name="hdinsight-spark-activity"></a>HDInsight Spark 活动
数据工厂管道中的 HDInsight Spark 活动在自己的 HDInsight 群集上执行 Spark 程序。 有关详细信息，请参阅[从 Azure 数据工厂调用 Spark 程序](transform-data-using-spark.md)。 

### <a name="machine-learning-activities"></a>机器学习活动
借助 Azure 数据工厂，可轻松创建相关管道，利用已发布的 Azure 机器学习 Web 服务进行预测分析。 使用 Azure 数据工厂管道中的[批处理执行活动](transform-data-using-machine-learning.md)，可以调用机器学习 Web 服务对批处理中的数据进行预测。

随着时间推移，需要使用新的输入数据集重新定型机器学习评分实验中的预测模型。 完成重新定型后，可使用重新定型的机器学习模型更新评分 Web 服务。 您可以使用["更新资源"活动](update-machine-learning-models.md)使用新训练的模型更新 Web 服务。  

有关这些机器学习活动的详细信息，请参阅[使用机器学习活动](transform-data-using-machine-learning.md)。 

### <a name="stored-procedure-activity"></a>存储过程活动
可在数据工厂管道中使用 SQL Server 存储过程活动调用以下数据存储中的存储过程：Azure SQL 数据库、Azure SQL 数据仓库、企业中的 SQL Server 数据库或 Azure VM。 有关详细信息，请参阅[存储过程活动](transform-data-using-stored-procedure.md)一文。  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活动
Data Lake Analytics U-SQL 活动在 Azure Data Lake Analytics 群集上运行 U-SQL 脚本。 有关详细信息，请参阅 [Data Analytics U-SQL 活动](transform-data-using-data-lake-analytics.md)一文。 

### <a name="databricks-notebook-activity"></a>Databricks Notebook 活动

数据工厂管道中的 Azure 数据砖块笔记本活动在 Azure 数据砖块工作区中运行数据砖块笔记本。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。 请参阅[通过运行 Databricks Notebook 转换数据](transform-data-databricks-notebook.md)。

### <a name="databricks-jar-activity"></a>Databricks Jar 活动

数据工厂管道中的 Azure Databricks Jar 活动运行 Azure Databricks 群集中的 Spark Jar。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。 请参阅[通过运行 Azure Databricks 中的 Jar 活动转换数据](transform-data-databricks-jar.md)。

### <a name="databricks-python-activity"></a>Databricks Python 活动

数据工厂管道中的 Azure Databricks Python 活动运行 Azure Databricks 群集中的 Python 文件。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。 请参阅[通过运行 Azure Databricks 中的 Python 活动转换数据](transform-data-databricks-python.md)。

### <a name="custom-activity"></a>自定义活动
如果需要采用数据工厂不支持的方式转换数据，可以使用自己的数据处理逻辑创建自定义活动，并在管道中使用该活动。 可以使用 Azure Batch 服务或 Azure HDInsight 群集配置要运行的自定义 .NET 活动。 有关详细信息，请参阅[使用自定义活动](transform-data-using-dotnet-custom-activity.md)文章。 

可以创建一项自定义活动，在安装了 R 的 HDInsight 群集上运行 R 脚本。 请参阅 [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)（使用 Azure 数据工厂运行 R 脚本）。 

### <a name="compute-environments"></a>计算环境
为计算环境创建链接服务，并在定义转换活动时使用该服务。 数据工厂支持两类计算环境。 

- **按需**：此情况下，计算环境由数据工厂完全托管。 作业提交到进程数据前，数据工厂服务会自动创建计算环境，作业完成后则自动将其删除。 针对作业执行、群集管理和启动操作，可以配置和控制按需计算环境的粒度设置。 
- **自带**：此情况下，可将自己的计算环境（例如 HDInsight 群集）注册为数据工厂中的链接服务。 计算环境由用户进行管理，数据工厂服务使用它执行活动。 

有关数据工厂支持的计算服务列表，请参阅 [计算链接服务](compute-linked-services.md)文章。 

## <a name="next-steps"></a>后续步骤
请参阅以下使用转换活动的示例教程：[教程：使用 Spark 转换数据](tutorial-transform-data-spark-powershell.md)
