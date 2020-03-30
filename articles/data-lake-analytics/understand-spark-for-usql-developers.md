---
title: 了解 Azure 数据湖分析 U-SQL 开发人员的 Apache Spark。
description: 本文介绍了 Apache Spark 概念，以帮助您在 U-SQL 开发人员之间存在差异。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648423"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>了解适用于 U-SQL 开发人员的 Apache Spark

Microsoft 支持多个分析服务，如[Azure 数据块](../azure-databricks/what-is-azure-databricks.md)和[Azure HDInsight](../hdinsight/hdinsight-overview.md)以及 Azure 数据湖分析。 我们从开发人员那里得知，他们在构建分析管道时明显倾向于开源解决方案。 为了帮助 U-SQL 开发人员了解 Apache Spark 以及如何将 U-SQL 脚本转换为 Apache Spark，我们创建了本指南。

它包括您可以采取的一些步骤，以及几个备选方案。

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>将 U-SQL 转换为 Apache Spark 的步骤

1. 转换作业业务流程管道。

   如果使用[Azure 数据工厂](../data-factory/introduction.md)来协调 Azure 数据湖分析脚本，则必须调整它们以协调新的 Spark 程序。
2. 了解 U-SQL 和 Spark 管理数据之间的区别

   如果要将数据从[Azure 数据存储第 1 代](../data-lake-store/data-lake-store-overview.md)移动到 Azure[数据湖存储 Gen2，](../storage/blobs/data-lake-storage-introduction.md)必须同时复制文件数据和目录维护的数据。 请注意，Azure 数据湖分析仅支持 Azure 数据湖存储第 1 代。 请参阅[了解 Spark 数据格式](understand-spark-data-formats.md)
3. 将 U-SQL 脚本转换为 Spark

   在转换 U-SQL 脚本之前，您必须选择分析服务。 一些可用的计算服务包括：
      - [Azure 数据工厂数据流](../data-factory/concepts-data-flow-overview.md)映射数据流是可视化设计的数据转换，允许数据工程师无需编写代码即可开发图形数据转换逻辑。 虽然它们不适合执行复杂的用户代码，但它们可以很容易地表示传统的类似 SQL 的数据流转换
      - [Azure HDInsight 蜂巢](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)HDInsight 上的 Apache 蜂巢适用于提取、转换和加载 （ETL） 操作。 这意味着您将 U-SQL 脚本转换为 Apache Hive。
      - Apache Spark 引擎（如[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)或[Azure 数据块](../azure-databricks/what-is-azure-databricks.md)）这意味着您将将 U-SQL 脚本转换为 Spark。 有关详细信息，请参阅了解[Spark 数据格式](understand-spark-data-formats.md)

> [!CAUTION]
> [Azure 数据块](../azure-databricks/what-is-azure-databricks.md)和[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)都是群集服务，而不是无服务器作业，如 Azure 数据湖分析。 您必须考虑如何预配群集以获得适当的成本/性能比率，以及如何管理其生命周期以最小化成本。 这些服务具有不同的性能特征，用户代码以 .NET 编写，因此您必须编写包装器或用受支持的语言重写代码。 有关详细信息，请参阅了解[Spark 数据格式](understand-spark-data-formats.md)、[了解 U-SQL 开发人员的 Apache Spark 代码概念](understand-spark-code-concepts.md) [.net 表示阿帕奇 Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>后续步骤

- [了解 U-SQL 开发人员的 Spark 数据格式](understand-spark-data-formats.md)
- [了解 U-SQL 开发人员的 Spark 代码概念](understand-spark-code-concepts.md)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [Azure HDInsight 中的 Apache Spark 是什么](../hdinsight/spark/apache-spark-overview.md)
