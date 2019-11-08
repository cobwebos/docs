---
title: 将 Azure Data Lake Storage 与 Azure 服务集成 | Microsoft Docs
description: 了解哪些 Azure 服务与 Azure Data Lake Storage Gen2 集成。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: c7223274417ef4c911c32acbcde1511682d6d9e0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796068"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>将 Azure Data Lake Storage 与 Azure 服务集成

可以使用 Azure 服务来引入数据、执行分析和创建可视化表示形式。 本文提供了支持的 Azure 服务的列表，并提供了指向帮助你将这些服务与 Azure Data Lake Storage Gen2 一起使用的文章的链接。

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>支持 Azure Data Lake Storage Gen2 的 Azure 服务

下表列出了支持 Azure Data Lake Storage Gen2 的 Azure 服务。

| Azure 服务 |  相关文章 |
|---------------|-------------------|
|Azure 数据工厂 | [使用 Azure 数据工厂将数据加载到 Azure Data Lake Storage Gen2 中](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [与 Azure Databricks 一起使用](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [快速入门：使用 Azure Databricks 分析 Azure Data Lake Storage Gen2 中的数据](data-lake-storage-quickstart-create-databricks-account.md) <br>[教程：使用 Azure Databricks 提取、转换和加载数据](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[教程：使用 Spark 通过 Azure Databricks 访问 Data Lake Storage Gen2 数据](data-lake-storage-use-databricks-spark.md) |
|Azure 事件中心捕获| [通过 Azure Blob 存储或 Azure Data Lake Storage 中的 Azure 事件中心来捕获事件](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure 逻辑应用 | [概述 - 什么是 Azure 逻辑应用？](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure 机器学习|[访问 Azure 存储服务中的数据](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data)|
|Azure 认知搜索 | [索引和搜索 Azure Data Lake Storage Gen2 文档（预览）](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure 流分析| [快速入门：使用 Azure 门户创建流分析作业](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [出口到 Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [使用 Azure Data Box 将数据从本地 HDFS 存储迁移到 Azure 存储](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[将 HDFS CLI 与 Data Lake Storage Gen2 配合使用](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[教程：在 Azure HDInsight 上使用 Apache Hive 提取、转换和加载数据](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|IoT 中心 | [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [使用 Power BI 分析 Data Lake Storage Gen2 中的数据](data-lake-storage-use-power-bi.md) |
|SQL 数据仓库 | [与 Azure SQL 数据仓库配合使用](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Azure 存储连接管理器](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>后续步骤

- 详细了解可用于处理数据湖中数据的工具。 请参阅[使用 Azure Data Lake Storage Gen2 满足大数据需求](data-lake-storage-data-scenarios.md)。

- 详细了解 Data Lake Storage Gen2 以及如何开始使用它。 请参阅 [Azure Data Lake Storage Gen2 简介](data-lake-storage-introduction.md)
