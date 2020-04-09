---
title: 支持 Azure 数据存储第 2 代的 Azure 服务 |微软文档
description: 了解哪些 Azure 服务与 Azure 数据存储第 2 代集成
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878317"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>支持 Azure Data Lake Storage Gen2 的 Azure 服务

可以使用 Azure 服务来引入数据、执行分析和创建可视化表示形式。 本文提供了受支持的 Azure 服务的列表，公开其支持级别，并为您提供指向有助于将这些服务与 Azure 数据湖存储 Gen2 一起使用的文章的链接。

## <a name="supported-azure-services"></a>支持的 Azure 服务

此表列出了可用于 Azure 数据湖存储 Gen2 的 Azure 服务。 随着支持的继续扩展，这些表中显示的项将随时间而变化。

> [!NOTE]
> 支持级别仅指数据湖存储第 2 代如何支持服务。

|Azure 服务 |支持级别 |相关文章 |
|---------------|-------------------|---|
|Azure 数据工厂|正式发布|[使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|正式发布|[与 Azure Databricks 配合使用](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [快速入门：使用 Azure 数据块分析 Azure 数据湖存储第 2 代中的数据](data-lake-storage-quickstart-create-databricks-account.md) <br>[教程：使用 Azure 数据块提取、转换和加载数据](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[教程：使用 Spark 使用 Azure 数据块访问数据存储第 2 代数据](data-lake-storage-use-databricks-spark.md)|
|Azure 事件中心|正式发布|[通过 Azure Blob 存储或 Azure Data Lake Storage 中的 Azure 事件中心来捕获事件](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure 事件网格|正式发布|[教程：实现数据湖捕获模式以更新数据块增量表](data-lake-storage-events.md)|
|Azure 逻辑应用|正式发布|[概述 - 什么是 Azure 逻辑应用？](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure 机器学习|正式发布|[访问 Azure 存储服务中的数据](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure 流分析|正式发布|[快速入门：使用 Azure 门户创建流分析作业](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [进入 Azure 数据湖 第 2 代](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|正式发布|[使用 Azure 数据框将数据从本地 HDFS 存储迁移到 Azure 存储](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |正式发布|[将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[将 HDFS CLI 与 Data Lake Storage Gen2 配合使用](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[教程：在 Azure HDInsight 上使用 Apache Hive 提取、转换和加载数据](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT 中心 |正式发布|[使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|正式发布|[使用 Power BI 分析 Data Lake Storage Gen2 中的数据](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL 数据仓库|正式发布|[与 Azure SQL 数据仓库配合使用](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|正式发布|[Azure 存储连接管理器](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure 认知搜索|预览|[索引和搜索 Azure 数据存储第 2 代文档（预览版）](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure 数据资源管理器|预览|[使用 Azure 数据资源管理器在 Azure 数据湖中查询数据](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure 内容分发网络|尚不支持|[索引和搜索 Azure 数据存储第 2 代文档（预览版）](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>请参阅

- [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)
- [Azure 数据湖存储第 2 代中提供的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)
- [支持 Azure 数据存储第 2 代的开源平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 的多协议访问](data-lake-storage-multi-protocol-access.md)