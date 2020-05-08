---
title: 将 Data Lake Storage Gen1 与其他 Azure 服务集成
description: 了解如何将 Azure Data Lake Storage Gen1 与其他 Azure 服务集成。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: ae27aba9fb6dc1421d224dab0c36373753acd5a2
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690533"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>将 Azure Data Lake Storage Gen1 和其他 Azure 服务集成
Azure Data Lake Storage Gen1 可和其他 Azure 服务结合使用来实现更多的使用方案。 下面的文章列出了可和 Data Lake Storage Gen1 集成的服务。

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>将 Data Lake Storage Gen1 与 Azure HDInsight 配合使用
可将 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 群集预配为使用 Data Lake Storage Gen1 作为兼容 HDFS 的存储。 此版本中，对于 Windows 和 Linux 上的 Hadoop 和 Storm 群集，仅可将 Data Lake Storage Gen1 用作额外存储。 这类群集仍使用 Azure 存储 (WASB) 作为默认存储。 但是，对于 Windows 和 Linux 上的 HBase 群集，可使用 Data Lake Storage Gen1 作为默认存储或额外存储，或者同时作为这两种存储。

有关如何设置具有 Data Lake Storage Gen1 的 HDInsight 群集，请参阅：

* [使用 Azure 门户预配包含 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)
* [使用 Azure PowerShell 预配将 Data Lake Storage Gen1 作为默认存储的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [使用 Azure PowerShell 预配将 Data Lake Storage Gen1 作为额外存储的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>配合使用 Data Lake Storage Gen1 和 Azure Data Lake Analytic
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) 允许处理云规模的的大数据。 它会动态预配资源，允许对储存在多种支持的数据源（其中之一便是 Data Lake Storage Gen1）中的兆兆字节甚至百亿亿字节大小的数据进行分析。 Data Lake Analytics 经过特别优化，可用于 Data Lake Storage Gen1，从而为大数据工作负荷提供最高级别的性能、吞吐量和并行化。

有关如何配合使用 Data Lake Analytics 和 Data Lake Storage Gen1 的说明，请参阅[使用 Data Lake Storage Gen1 进行 Data Lake Analytics 的入门](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>配合使用 Data Lake Storage Gen1 和 Azure Data Factory
可使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)从 Azure 表、Azure SQL 数据库、Azure SQL 数据仓库、Azure 存储 Blob 和本地数据库引入数据。 作为 Azure 生态系统的一类成员，Azure 数据工厂可用于协调从这些源到 Data Lake Storage Gen1 的数据引入。

有关如何配合使用 Azure 数据工厂和 Data Lake Storage Gen1 的说明，请参阅[使用数据工厂向/从 Data Lake Storage Gen1 移动数据](../data-factory/connector-azure-data-lake-store.md)。

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>将数据从 Azure 存储 Blob 复制到 Data Lake Storage Gen1
Azure Data Lake Storage Gen1 提供命令行工具 AdlCopy，可从 Azure 存储 Blob 复制数据到 Data Lake Storage Gen1 帐户。 有关详细信息，请参阅[从 Azure 存储 Blob 复制数据到 Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)。

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>在 Azure SQL 数据库和 Data Lake Storage Gen1 之间复制数据
可使用 Apache Sqoop 在 Azure SQL 数据库和 Data Lake Storage Gen1 之间导入和导出数据。 有关详细信息，请参阅[使用 Sqoop 在 Data Lake Storage Gen1 和 Azure SQL 数据库之间复制数据](data-lake-store-data-transfer-sql-sqoop.md)。

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>配合使用 Data Lake Storage Gen1 和流分析
可使用 Data Lake Storage Gen1 作为一个输出来存储使用 Azure 流分析处理的数据。 有关详细信息，请参阅[使用 Azure 流分析将 Azure 存储 Blob 中的数据流式传输到 Data Lake Storage Gen1](data-lake-store-stream-analytics.md)。

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>配合使用 Data Lake Storage Gen1 和 Power BI
可使用 Power BI 从 Data Lake Storage Gen1 帐户导入数据进行分析和可视化。 有关详细信息，请参阅[使用 Power BI分析 Data Lake Storage Gen1 中的数据](data-lake-store-power-bi.md)。

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>配合使用 Data Lake Storage Gen1 和 数据目录
可将数据从 Data Lake Storage Gen1 注册到 Azure 数据目录，以使数据在整个组织内可见。 有关详细信息，请参阅[将数据从 Data Lake Storage Gen1 注册到 Azure 数据目录](data-lake-store-with-data-catalog.md)。

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>配合使用 Data Lake Storage Gen1 和 SQL Server Integration Services (SSIS)
可在 SSIS 中使用 Data Lake Storage Gen1 连接管理器将 SSIS 包与 Azure Data Lake Storage Gen1 相连接。 有关详细信息，请参阅[配合使用 SSIS 和 Data Lake Storage Gen1](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)。

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>配合使用 Data Lake Storage Gen1 和 SQL 数据仓库
可使用 PolyBase 将数据从 Data Lake Storage Gen1 加载到 SQL 数据仓库中。 有关详细信息，请参阅[配合使用 Data Lake Storage Gen1 和 SQL 数据仓库](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)。

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>配合使用 Data Lake Storage Gen1 和 Azure 事件中心
可以使用 Azure Data Lake Storage Gen1 存档和捕获 Azure 事件中心收到的数据。 有关详细信息，请参阅[配合使用 Data Lake Storage Gen1 和 Azure 事件中心](data-lake-store-archive-eventhub-capture.md)。

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Storage Gen1 概述](data-lake-store-overview.md)
* [通过门户实现 Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)
* [使用 PowerShell 开始使用 Data Lake Storage Gen1](data-lake-store-get-started-powershell.md)  

