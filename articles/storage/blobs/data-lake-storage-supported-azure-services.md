---
title: 支持 Azure Data Lake Storage Gen2 的 Azure 服务 | Microsoft Docs
description: 了解哪些 Azure 服务与 Azure Data Lake Storage Gen2 集成
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: c3a5f3a984c95af400c9e0c1543e3c1883290668
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442950"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>支持 Azure Data Lake Storage Gen2 的 Azure 服务

可以使用 Azure 服务来引入数据、执行分析和创建可视化表示形式。 本文提供了受支持的 Azure 服务的列表，公开了它们的支持级别，并提供了一些文章的链接，这些文章可帮助你将这些服务与 Azure Data Lake Storage Gen2 配合使用。

## <a name="supported-azure-services"></a>支持的 Azure 服务

下表列出了可与 Azure Data Lake Storage Gen2 配合使用的 Azure 服务。 随着支持的不断扩展，这些表中出现的项将随着时间的推移而变化。

> [!NOTE]
> 支持级别仅指 Data Lake Storage Gen2 支持该服务的方式。

|Azure 服务 |支持级别 |Azure AD |共享密钥| 相关文章 |
|---------------|-------------------|---|---|---|
|Azure 数据工厂|正式发布|是|是|[使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|正式发布|是|是|[与 Azure Databricks 配合使用](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [快速入门：使用 Azure Databricks 分析 Azure Data Lake Storage Gen2 中的数据](data-lake-storage-quickstart-create-databricks-account.md) <br>[教程：使用 Azure Databricks 提取、转换和加载数据](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[教程：使用 Spark 通过 Azure Databricks 访问 Data Lake Storage Gen2 数据](data-lake-storage-use-databricks-spark.md)|
|Azure 事件中心|正式发布|否|是|[通过 Azure Blob 存储或 Azure Data Lake Storage 中的 Azure 事件中心来捕获事件](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure 事件网格|正式发布|是|是|[教程：实现 data lake 捕获模式以更新 Databricks 增量表](data-lake-storage-events.md)|
|Azure 逻辑应用|正式发布|否|是|[概述 - 什么是 Azure 逻辑应用？](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure 机器学习|正式发布|是|是|[访问 Azure 存储服务中的数据](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure 流分析|正式发布|是|是|[快速入门：使用 Azure 门户创建流分析作业](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Azure Data Lake Gen2 流出量](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|正式发布|否|是|[使用 Azure Data Box 将数据从本地 HDFS 存储迁移到 Azure 存储](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |正式发布|是|是|[将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[将 HDFS CLI 与 Data Lake Storage Gen2 配合使用](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[教程：使用 Apache Hive on Azure HDInsight 提取、转换和加载数据](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT 中心 |正式发布|否|是|[使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|正式发布|是|是|[使用 Power BI 分析 Data Lake Storage Gen2 中的数据](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics（以前称为 SQL 数据仓库）|正式发布|是|是|[用于 Azure Synapse Analytics（以前称为 SQL 数据仓库）](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server 集成服务 (SSIS)|正式发布|是|是|[Azure 存储连接管理器](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure 数据资源管理器|正式发布|是|是|[使用 Azure 数据资源管理器查询 Azure Data Lake 中的数据](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure 认知搜索|预览|是|是|[为 Azure Data Lake Storage Gen2 文档编制索引及对其进行搜索（预览）](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure 内容分发网络|尚不支持|不适用|不适用|[为 Azure Data Lake Storage Gen2 文档编制索引及对其进行搜索（预览）](https://docs.microsoft.com/azure/cdn/cdn-overview)|

## <a name="see-also"></a>另请参阅

- [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage Gen2 中可用的 Blob 存储功能](data-lake-storage-supported-blob-storage-features.md)
- [支持 Azure Data Lake Storage Gen2 的开源平台](data-lake-storage-supported-open-source-platforms.md)
- [Azure Data Lake Storage 的多协议访问](data-lake-storage-multi-protocol-access.md)