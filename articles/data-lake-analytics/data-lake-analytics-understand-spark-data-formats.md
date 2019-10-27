---
title: 了解 Apache Spark Azure Data Lake Analytics 的 SQL 开发人员的数据格式。
description: 本文介绍 Apache Spark 的概念，以帮助 U_SQL 开发人员了解 U SQL 和 Spark 数据格式之间的差异。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 05a56288e3fa56f4f147182725073dc39e498a95
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72966367"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>了解 U SQL 和 Spark 数据格式之间的差异

如果要使用[Azure Databricks](../azure-databricks/what-is-azure-databricks.md)或[Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)，则建议将数据从[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)迁移到[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)。

除了移动文件外，还需要将数据存储在可由 Spark 访问的 U SQL 表中。

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>移动 Azure Data Lake Storage Gen1 文件中存储的数据

文件中存储的数据可以通过多种方式移动：

- 编写[Azure 数据工厂](../data-factory/introduction.md)管道，将[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)帐户中的数据复制到[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)帐户。
- 编写一个 Spark 作业，该作业从[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)帐户读取数据并将其写入[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)帐户。 根据用例，如果不需要保留原始文件格式，则可以使用不同的格式（例如 Parquet）来编写。

建议你查看文章将[大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>移动在 U SQL 表中存储的数据

Spark 不理解 U-SQL 表。 如果将数据存储在 U SQL 表中，您将运行一个可提取表数据并将其保存为 Spark 可识别的格式的 U SQL 作业。 最合适的格式是在 Hive 元存储的文件夹布局之后创建一组 Parquet 文件。

可以通过内置 Parquet 输出器在 U SQL 中实现输出，并使用包含文件集的动态输出分区来创建分区文件夹。 [处理比以往更多的文件，使用 Parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics)提供了有关如何创建此类 Spark 可耗用数据的示例。

完成此转换后，按 "[移动存储在 Azure Data Lake Storage Gen1 文件中的数据](#move-data-stored-in-azure-data-lake-storage-gen1-files)" 一章中所述复制数据。

## <a name="caveats"></a>注意事项

- 数据语义复制文件时，副本将在字节级别进行。  因此， [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)帐户中应显示相同的数据。  但请注意，Spark 可能会以不同的方式解释某些字符。  例如，对于 CSV 文件中的行分隔符，它可能会使用不同的默认值。
    此外，如果您要复制类型化的数据（从表中），则 Parquet 和 Spark 对于某些类型化值（例如 float）可能具有不同的精度和小数位数，并且可能会以不同的方式处理 null 值。 例如，U SQL 具有 null 值的C#语义，而 Spark 对于 null 值具有三值逻辑。

- 数据组织（分区） U SQL 表提供了两种级别的分区。 外部级别（`PARTITIONED BY`）是按值进行的，并且主要映射到使用文件夹层次结构的 Hive/Spark 分区方案。 你将需要确保将空值映射到正确的文件夹。 U 中的内部级别（`DISTRIBUTED BY`）提供4个分发方案：轮循机制、范围、哈希和直接哈希。
    Hive/Spark 表仅支持使用与 U SQL 不同的哈希函数的值分区或哈希分区。 输出 U 型表数据时，您可能只能映射到 Spark 的值分区，并可能需要根据您的最终 Spark 查询进一步优化数据布局。

## <a name="next-steps"></a>后续步骤

- [了解适用于 SQL 开发人员的 Spark 代码概念](data-lake-analytics-understand-spark-code-concepts.md)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [适用于 Apache Spark 的 .NET](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight 中 Apache Spark 的内容](../hdinsight/spark/apache-spark-overview.md)
