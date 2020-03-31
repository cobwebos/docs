---
title: 了解 Azure 数据湖分析 U-SQL 开发人员的 Apache Spark 数据格式。
description: 本文介绍了 Apache Spark 概念，以帮助U_SQL开发人员了解 U-SQL 和 Spark 数据格式之间的差异。
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648436"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>了解 U-SQL 和 Spark 数据格式之间的差异

如果要使用[Azure 数据块](../azure-databricks/what-is-azure-databricks.md)或[Azure HDInsight Spark，](../hdinsight/spark/apache-spark-overview.md)我们建议您将数据从[Azure 数据存储第 1 代](../data-lake-store/data-lake-store-overview.md)迁移到[Azure 数据湖存储 Gen2](../storage/blobs/data-lake-storage-introduction.md)。

除了移动文件之外，您还需要使存储在 U-SQL 表中的数据可供 Spark 访问。

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>移动存储在 Azure 数据存储第 1 代文件中的数据

存储在文件中的数据可以通过多种方式移动：

- 编写[Azure 数据工厂](../data-factory/introduction.md)管道将数据从[Azure 数据存储库存储 Gen1](../data-lake-store/data-lake-store-overview.md)帐户复制到[Azure 数据湖存储 Gen2](../storage/blobs/data-lake-storage-introduction.md)帐户。
- 编写 Spark 作业，从 Azure[数据存储库存储 Gen1](../data-lake-store/data-lake-store-overview.md)帐户中读取数据并将其写入[Azure 数据湖存储 Gen2](../storage/blobs/data-lake-storage-introduction.md)帐户。 根据用例，如果您不需要保留原始文件格式，则可能需要以其他格式（如 Parquet）编写它。

我们建议您查看文章[，将大数据分析解决方案从 Azure 数据存储第 1 代升级到 Azure 数据湖存储 Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>移动存储在 U-SQL 表中的数据

Spark 无法理解 U-SQL 表。 如果数据存储在 U-SQL 表中，则运行 U-SQL 作业，该作业提取表数据并将其保存为 Spark 理解的格式。 最合适的格式是在 Hive 元存储的文件夹布局之后创建一组 Parquet 文件。

输出可以通过内置 Parquet 输出器在 U-SQL 中实现，并使用带文件集的动态输出分区来创建分区文件夹。 [处理的文件比以往任何时候都多，使用 Parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics)提供了如何创建此类 Spark 易耗数据的示例。

在此转换后，您可以复制存储在[Azure 数据湖存储 Gen1 文件中](#move-data-stored-in-azure-data-lake-storage-gen1-files)的章节中的所述数据。

## <a name="caveats"></a>注意事项

- 数据语义 复制文件时，复制将在字节级别发生。 因此[，Azure 数据湖存储 Gen2](../storage/blobs/data-lake-storage-introduction.md)帐户中应显示相同的数据。 但请注意，Spark 可能会以不同的方式解释某些字符。 例如，它可能对 CSV 文件中的行分隔符使用不同的默认值。
    此外，如果要复制类型化数据（来自表），则 Parquet 和 Spark 对某些类型化值（例如 float）的精度和比例可能不同，并且可能会以不同的方式对待空值。 例如，U-SQL 具有 null 值的 C# 语义，而 Spark 具有 null 值的三值逻辑。

- 数据组织（分区）U-SQL 表提供两个级别分区。 外部级别 （`PARTITIONED BY`） 按值和映射主要到使用文件夹层次结构的 Hive/Spark 分区方案。 您需要确保 null 值映射到正确的文件夹。 U-SQL`DISTRIBUTED BY`中的内部级别 （ ） 提供 4 种分布方案：循环、范围、哈希和直接哈希。
    Hive/Spark 表仅支持使用与 U-SQL 不同的哈希函数进行值分区或哈希分区。 输出 U-SQL 表数据时，您可能只能映射到 Spark 的值分区，并且可能需要根据最终的 Spark 查询进行进一步调整数据布局。

## <a name="next-steps"></a>后续步骤

- [了解 U-SQL 开发人员的 Spark 代码概念](understand-spark-code-concepts.md)
- [将大数据分析解决方案从 Azure Data Lake Storage Gen1 升级到 Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [在 Azure 数据工厂中使用 Spark 活动转换数据](../data-factory/transform-data-using-spark.md)
- [在 Azure 数据工厂中使用 Hadoop Hive 活动转换数据](../data-factory/transform-data-using-hadoop-hive.md)
- [Azure HDInsight 中的 Apache Spark 是什么](../hdinsight/spark/apache-spark-overview.md)
