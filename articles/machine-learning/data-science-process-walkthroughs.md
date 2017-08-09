---
title: "团队数据科学过程演练 | Microsoft Docs"
description: "演练展示如何将云和本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: c0f0ff599909234b2b43f2b8512d77f9053b8a09
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---
# <a name="team-data-science-process-walkthroughs"></a>团队数据科学过程演练
本文逐条列举的每个端对端演练展示了特定方案的 Team Data Science Process 步骤。 具体展示了如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 按所使用的平台对演练进行分组： 

- Spark 与 PySpark 和 Scala
- HDInsight (Hadoop)
- Azure Data Lake 
- SQL Server
- SQL 数据仓库 


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>使用 PySpark 和 Scala 的 HDInsight Spark

- [在 Azure HDInsight 上使用 Spark](machine-learning-data-science-spark-overview.md)演练在方案中使用 Team Data Science Process，以便使用 [Azure HDInsight Spark 群集](https://azure.microsoft.com/services/hdinsight/)存储、浏览并突出纽约市公用计程车车程和费用数据集中的工程师数据。

- [在 Azure 上配合使用 Scala 和 Spark](machine-learning-data-science-process-scala-walkthrough.md) 演练介绍了如何通过 Azure HDInsight Spark 群集上的 Spark 机器学习库 (MLlib) 和 SparkML 包，对受监督的机器学习任务使用 Scala。 它将指导你完成构成[数据科学过程](http://aka.ms/datascienceprocess)的任务：数据引入和浏览、可视化、特征工程、建模和模型使用。 生成的模型包括逻辑和线性回归、随机林和梯度提升树。


## <a name="hdinsight-hadoop"></a>HDInsight Hadoop 

- [使用 HDInsight Hadoop 群集](machine-learning-data-science-process-hive-walkthrough.md)演练使用了 [Azure HDInsight Hadoop 群集](https://azure.microsoft.com/services/hdinsight/)从纽约市公用计程车车程和费用数据集存储、浏览工程师数据并描绘其特征

- [在 1TB 数据集中使用 Azure HDInsight Hadoop 群集](machine-learning-data-science-process-hive-criteo-walkthrough.md)演练展示的方案使用 [Azure HDInsight Hadoop 群集](https://azure.microsoft.com/services/hdinsight/)存储、浏览并突出工程师数据，并从公用 [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) 数据集下载示例数据。


## <a name="azure-data-lake"></a>Azure Data Lake

- [将 Azure Data Lake 用于数据科学](machine-learning-data-science-process-data-lake-walkthrough.md)介绍如何使用 Azure Data Lake 对纽约市计程车数据集进行数据浏览和二进制分类任务，以预测客户是否会支付小费。 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server 和 SQL 数据仓库 

- [使用 SQL 数据仓库](machine-learning-data-science-process-sqldw-walkthrough.md)演练展示如何使用纽约市公用计程车车程和费用数据集的 SQL 数据仓库 (SQL DW) 生成和部署机器学习分类以及回归模型。

- [使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md) 演练展示如何使用 SQL Server 和纽约市公用计程车车程和费用数据集生成和部署机器学习分类以及回归模型。

- [使用 SQL Server R Services](https://msdn.microsoft.com/library/mt612857.aspx) 演练向数据科学家提供了 R 代码、SQL Server 数据和自定义 SQL 函数，来生成和部署 R 模型到 SQL Server。

- [将 T-SQL 用于 SQL Server R Services](https://msdn.microsoft.com/library/mt683480.aspx) 演练向 SQL 程序员提供了通过 SQL Server R 使用 Transact-SQL 构建高级分析解决方案以使用 R 解决方案的体验。

- [将 T-SQL 用于 SQL Server Python Services](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) 演练向 SQL 程序员介绍了如何在 SQL Server 中生成机器学习解决方案。 具体展示了如何将 Python 代码添加到存储过程，从而将 Python 合并到应用程序中。

## <a name="whats-next"></a>后续步骤
获取主题概述，了解包含 Azure 中数据科学过程的任务，请参阅[数据科学过程](http://aka.ms/datascienceprocess)。 


