---
title: "集成 Data Lake Store 和其他 Azure 服务 | Microsoft Docs"
description: "了解 Data Lake Store 如何与其他 Azure 服务集成"
documentationcenter: 
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: de7aff6b31d937576da65498c5fcce2ae9abdbf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-data-lake-store-with-other-azure-services"></a>集成 Data Lake Store 和其他 Azure 服务
Azure Data Lake Store 可和其他 Azure 服务结合使用来提供更多的方案。 下面的文章列出了可和 Data Lake Store 集成的服务。

## <a name="use-data-lake-store-with-azure-hdinsight"></a>配合使用 Data Lake Store 和 HDInsight
可将使用 Data Lake Store 的 [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) 群集设置为兼容 HDFS 的存储。 此版本中，对于 Windows 和 Linux 上的 Hadoop 和 Storm 群集，可仅将 Data Lake Store 用作其他存储。 这类群集仍使用 Azure 存储 (WASB) 作为默认存储。 但是，对于 Windows 和 Linux 上的 HBase 群集，可使用 Data Lake Store 作为默认存储或其他存储，或者同时作为这两种存储。

有关如何设置具有 Data Lake Store 的 HDInsight 群集，请参阅：

* [使用 Azure 门户设置包含 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)
* [使用 Azure PowerShell 预配将 Data Lake Store 作为默认存储的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [使用 Azure PowerShell 预配将 Data Lake Store 作为附加存储的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>配合使用 Data Lake Store 和 Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) 允许处理云规模的的大数据。 它会动态设置资源，允许对储存在多种支持的数据源（其中之一便是 Data Lake Store）中的兆兆字节甚至百亿亿字节大小的数据进行分析。 Data Lake Analytics 经过特别优化，可用于 Azure Data Lake Store，从而为大数据工作负荷提供最高级别的性能、吞吐量和并行化。

有关如何配合使用 Data Lake Analytics 和 Data Lake Store 的说明，请参阅[使用 Data Lake Store 进行 Data Lake Analytics 的入门](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

## <a name="use-data-lake-store-with-azure-data-factory"></a>配合使用 Data Lake Store 和 Azure Data Factory
可使用 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/)从 Azure 表、Azure SQL 数据库、Azure SQL 数据仓库、Azure 存储 Blob 和本地数据库引入数据。 作为 Azure 生态系统的一类成员，Azure 数据工厂可用于协调从这些源到 Azure Data Lake Store 的数据引入。

有关如何配合使用 Azure 数据工厂和 Data Lake Store 的说明，请参阅[使用数据工厂向/从 Data Lake Store 移动数据](../data-factory/connector-azure-data-lake-store.md)。

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>从 Azure 存储 Blob 复制数据到 Data Lake Store
Azure Data Lake Store 提供命令行工具 AdlCopy，可从 Azure 存储 Blob 复制数据到 Data Lake Store 帐户。 有关详细信息，请参阅[从 Azure 存储 Blob 复制数据到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)。

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>在 Azure SQL 数据库和 Data Lake Store 之间复制数据
可使用 Apache Sqoop 在 Azure SQL 数据库和 Data Lake Store 之间导入和导出数据。 有关详细信息，请参阅[使用 Sqoop 在 Data Lake Store 和 Azure SQL 数据库之间复制数据](data-lake-store-data-transfer-sql-sqoop.md)。

## <a name="use-data-lake-store-with-stream-analytics"></a>配合使用 Data Lake Store 和流分析
可使用 Data Lake Store 作为一个输出来存储使用流分析处理的数据。 有关详细信息，请参阅使用 [Azure Stream Analytics 从 Azure 存储 Blob 流处理数据到 Data Lake Store](data-lake-store-stream-analytics.md)。

## <a name="use-data-lake-store-with-power-bi"></a>配合使用 Data Lake Store 和 Power BI
可使用 Power BI 从 Data Lake Store 帐户导入数据进行分析和可视化。 有关详细信息，请参阅[使用 Power BI分析 Data Lake Store 中的数据](data-lake-store-power-bi.md)。

## <a name="use-data-lake-store-with-data-catalog"></a>配合使用 Data Lake Store 和数据目录
可从 Data Lake Store 注册数据到 Azure 数据目录，以使数据在整个组织内可见。 有关详细信息，请参阅[在 Azure 数据目录中从 Data Lake Store 注册数据](data-lake-store-with-data-catalog.md)。

## <a name="use-data-lake-store-with-sql-server-integration-services-ssis"></a>结合使用 SQL Server Integration Services (SSIS) 和 Data Lake Store
可在 SSIS 中使用 Azure Data Lake Store 连接管理器将 SSIS 包与 Azure Data Lake Store 相连接。 有关详细信息，请参阅[在 SSIS 中使用 Data Lake Store](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)。

## <a name="use-data-lake-store-with-sql-data-warehouse"></a>在 SQL 数据仓库中使用 Data Lake Store
可使用 PolyBase 将数据从 Azure Data Lake Store 加载到 SQL 数据仓库中。 有关详细信息，请参阅[在 SQL 数据仓库中使用 Data Lake Store](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)。

## <a name="use-data-lake-store-with-azure-event-hubs"></a>配合使用 Data Lake Store 和 Azure 事件中心
可以使用 Azure Data Lake Store 存档和捕获 Azure 事件中心收到的数据。 有关详细信息，请参阅[配合使用 Data Lake Store 和 Azure 事件中心](data-lake-store-archive-eventhub-capture.md)。

## <a name="see-also"></a>另请参阅
* [Overview of Azure Data Lake Store](data-lake-store-overview.md)
* [通过门户实现 Data Lake Store 入门](data-lake-store-get-started-portal.md)
* [通过 PowerShell 实现 Data Lake Store 入门](data-lake-store-get-started-powershell.md)  

