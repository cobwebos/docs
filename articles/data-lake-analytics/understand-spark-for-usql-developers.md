---
title: 了解 Azure Data Lake Analytics 的 SQL 开发人员 Apache Spark。
description: 本文介绍 Apache Spark 的概念，以帮助你在 SQL 开发人员之间的差异。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648423"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>了解针对 SQL 开发人员的 Apache Spark

Microsoft 支持多种 Analytics 服务，例如[Azure Databricks](../azure-databricks/what-is-azure-databricks.md)和[Azure HDInsight](../hdinsight/hdinsight-overview.md)以及 Azure Data Lake Analytics。 我们听说，开发人员在构建分析管道时，他们对开源解决方案具有清晰的首选项。 为了帮助 SQL 开发人员了解 Apache Spark，以及如何将您的 U 型脚本转换为 Apache Spark，我们已创建了本指南。

其中包括多个可以执行的步骤和几个备选方案。

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>将 SQL 转换为 Apache Spark 的步骤

1. 转换作业业务流程管道。

   如果使用[Azure 数据工厂](../data-factory/introduction.md)来协调 Azure Data Lake Analytics 脚本，则必须对其进行调整以协调新的 Spark 程序。
2. 了解如何通过 SQL 和 Spark 管理数据

   如果要将数据从[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)移动到[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)，则必须复制文件数据和保留的数据。 请注意，Azure Data Lake Analytics 仅支持 Azure Data Lake Storage Gen1。 请参阅[了解 Spark 数据格式](understand-spark-data-formats.md)
3. 将你的 U SQL 脚本转换为 Spark

   在转换您的 U SQL 脚本之前，必须选择一个分析服务。 可用的某些可用计算服务包括：
      - [Azure 数据工厂数据流](../data-factory/concepts-data-flow-overview.md)映射数据流是直观设计的数据转换，使数据工程师无需编写代码即可开发图形数据转换逻辑。 虽然不适合执行复杂的用户代码，但可以轻松地表示传统的 SQL 类似数据流转换
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)HDInsight 上的 Apache Hive 适用于提取、转换和加载（ETL）操作。 这意味着要将您的 U SQL 脚本转换为 Apache Hive。
      - Apache Spark 引擎，例如[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)或[Azure Databricks](../azure-databricks/what-is-azure-databricks.md)这意味着你要将 U SQL 脚本转换为 Spark。 有关详细信息，请参阅[了解 Spark 数据格式](understand-spark-data-formats.md)

> [!CAUTION]
> [Azure Databricks](../azure-databricks/what-is-azure-databricks.md)和[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)都是群集服务，而不是 Azure Data Lake Analytics 的无服务器作业。 你必须考虑如何设置群集，以获取适当的成本/性能比，以及如何管理其生存期以最大程度地降低成本。 对于以 .NET 编写的用户代码，这些服务具有不同的性能特征，因此，你必须以受支持的语言编写包装或重写代码。 有关详细信息，请参阅[了解 Spark 数据格式](understand-spark-data-formats.md)、[了解用于 SQL 开发人员的 Apache Spark 代码概念](understand-spark-code-concepts.md)、[适用于 Apache Spark 的 .net](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>后续步骤

- [了解适用于 SQL 开发人员的 Spark 数据格式](understand-spark-data-formats.md)
- [了解适用于 SQL 开发人员的 Spark 代码概念](understand-spark-code-concepts.md)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [适用于 Apache Spark 的 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [Azure HDInsight 中 Apache Spark 的内容](../hdinsight/spark/apache-spark-overview.md)
