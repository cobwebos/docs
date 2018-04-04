---
title: 从 Azure Databricks 连接到不同数据源 | Microsoft Docs
description: 了解如何从 Azure Databricks 连接到不同数据源。
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: nitinme
ms.openlocfilehash: 865313a7c6eabd847529b88ff5fff0b7db438fa5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>从 Azure Databricks 连接到数据源

本文提供了指向 Azure 中可以连接到 Azure Databricks 的所有不同数据源的链接。 请按照这些链接中的示例操作，将 Azure 数据源（例如，Azure Blob 存储、Azure 事件中心等）中的数据提取到 Azure Databricks 群集中，并对这些数据运行分析作业。 

## <a name="prerequisites"></a>先决条件

* 必须具备 Azure Databricks 工作区和 Spark 群集。 请按照 [Azure Databricks 入门](quickstart-create-databricks-workspace-portal.md)中的说明操作。

## <a name="data-sources-for-azure-databricks"></a>Azure Databricks 的数据源

以下列表提供了 Azure 中可用于 Azure Databricks 的数据源。 有关可用于 Azure Databricks 的数据源的完整列表，请参阅 [Azure Databricks 的数据源](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)。

- [Azure SQL 数据库](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    此链接提供了用于使用 JDBC 连接到 SQL 数据库的数据帧 API，并介绍了如何控制通过 JDBC 接口进行的读取操作的并行度。 本主题提供了使用 Scala API 的详细示例，并在末尾提供了 Python 和 Spark SQL 的简略示例。
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)

    此链接举例说明了如何使用 Azure Active Directory 服务主体向 Data Lake Store 进行身份验证。 它还说明了如何从 Azure Databricks 访问 Data Lake Store 中的数据。

- [Azure Blob 存储](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    此链接举例说明了如何使用给定容器的访问密钥或 SAS 从 Azure Databricks 直接访问 Azure Blob 存储。 此链接还提供了信息说明如何使用 RDD API 从 Azure Databricks 访问 Azure Blob 存储。

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    此链接说明了如何通过 Azure Databricks 使用 [Azure Cosmos DB Spark 连接器](https://github.com/Azure/azure-cosmosdb-spark)访问 Azure Cosmos DB 中的数据。

- [Azure 事件中心](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    此链接说明了如何通过 Azure Databricks 使用 [Azure 事件中心 Spark 连接器](https://github.com/Azure/azure-event-hubs-spark)访问 Azure 事件中心内的数据。

- [Azure SQL 数据仓库](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    此链接说明了如何通过 Azure Databricks 使用 Azure SQL 数据仓库连接器进行连接。
    

## <a name="next-steps"></a>后续步骤

若要了解可以从中将数据导入到 Azure Databricks 中的源，请参阅 [Azure Databricks 的数据源](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)。


