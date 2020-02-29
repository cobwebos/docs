---
title: 支持 Azure Data Lake Storage Gen2 的 Azure 服务 |Microsoft Docs
description: 了解与 Azure Data Lake Storage Gen2 集成哪些 Azure 服务
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 71df31562971af8bcdb38be76894c1d0abeccc3f
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925574"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>支持 Azure Data Lake Storage Gen2 的 Azure 服务

你可以使用 Azure 服务引入数据、执行分析和创建可视化表示形式。 本文提供了受支持的 Azure 服务列表，并公开了其支持级别，并提供了指向文章的链接，这些文章可帮助你将这些服务与 Azure Data Lake Storage Gen2 结合使用。

## <a name="supportedazureservices"></a>支持的 Azure 服务

此表列出了可与 Azure Data Lake Storage Gen2 一起使用的 Azure 服务。 当支持持续扩展时，这些表中出现的项将随时间而改变。

> [!NOTE]
> 支持级别仅指 Data Lake Storage 第2代支持服务的方式。

|Azure 服务 |支持级别 |相关文章 |
|---------------|-------------------|---|
|Azure 数据工厂|正式发布|[使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|正式发布|[与 Azure Databricks 一起使用](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [快速入门：使用 Azure Databricks 分析 Azure Data Lake Storage Gen2 中的数据](data-lake-storage-quickstart-create-databricks-account.md) <br>[教程：使用 Azure Databricks 提取、转换和加载数据](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[教程：使用 Spark 通过 Azure Databricks 访问 Data Lake Storage Gen2 数据](data-lake-storage-use-databricks-spark.md)|
|Azure 事件中心捕获| 正式发布|[通过 azure Blob 存储中的 Azure 事件中心或 Azure Data Lake Storage 来捕获事件](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure 逻辑应用|正式发布|[概述-什么是 Azure 逻辑应用？](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure 机器学习|正式发布|[访问 Azure 存储服务中的数据](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure 流分析|正式发布|[快速入门：使用 Azure 门户创建流分析作业](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [出口到 Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box| 正式发布|[使用 Azure Data Box 将数据从本地 HDFS 存储迁移到 Azure 存储](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | 正式发布|[将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[在 Data Lake Storage Gen2 中使用 HDFS CLI](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[教程：在 Azure HDInsight 上使用 Apache Hive 提取、转换和加载数据](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT 中心 | 正式发布|[使用 IoT 中心消息路由将设备到云消息发送到不同的终结点](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI| 正式发布|[使用 Power BI 分析 Data Lake Storage Gen2 中的数据](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL 数据仓库|正式发布|[与 Azure SQL 数据仓库配合使用](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|正式发布|[Azure 存储连接管理器](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure 认知搜索| 预览|[索引和搜索 Azure Data Lake Storage Gen2 文档（预览）](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure 内容分发网络|尚不支持|[索引和搜索 Azure Data Lake Storage Gen2 文档（预览）](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)
- [支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 的多协议访问](data-lake-storage-multi-protocol-access.md)