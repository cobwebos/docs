---
title: 从 Azure Databricks 访问 Azure Cosmos DB Cassandra API
description: 本文介绍如何从 Azure Databricks 使用 Azure Cosmos DB Cassandra API。
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: 3f1bdb63253506aee211f3733df2a339824de7a0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994643"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>从 Azure Databricks 访问 Azure Cosmos DB Cassandra API 数据

本文详细介绍了如何在 [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) 上通过 Spark 使用 Azure Cosmos DB Cassandra API。

## <a name="prerequisites"></a>先决条件

* [预配 Azure Cosmos DB Cassandra API 帐户](create-cassandra-dotnet.md#create-a-database-account)

* [回顾连接到 Azure Cosmos DB Cassandra API 的基础知识](cassandra-spark-generic.md)

* [预配 Azure Databricks 群集](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [查看使用 Cassandra API 的代码示例](cassandra-spark-generic.md#next-steps)

* [使用 cqlsh 进行验证（如需要）](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Datastax Cassandra 连接器的 Cassandra API 实例配置：**

  Datastax Cassandra 连接器要求将 Cassandra 连接的详细信息作为 spark 上下文的一部分进行初始化。 当启动 Databricks 笔记本时，已初始化 spark 上下文，不建议停止和重新初始化。 一种解决方案是在群集 spark 配置中添加群集级别的 Cassandra API 实例配置。 这是每个群集的一次性活动。 将以下代码添加到 Spark 配置，作为空格分隔的键值对：
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>添加必需的依赖项

* Datastax Cassandra Spark 连接器：- 要与 Spark 中的 Azure Cosmos DB Cassandra API 集成，Datastax Cassandra 连接器应附加到 Azure Databricks 群集。 若要附加群集：

  * 查看 Databricks 运行时版本，即 Spark 版本。 然后找到与 Datastax Cassandra Spark 连接器兼容的 [maven 坐标](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector)，并将其附加到群集。 请参阅[“上传 Maven 包或 Spark 包”](https://docs.databricks.com/user-guide/libraries.html)一文，将连接器库附加到群集。 例如，“Databricks Runtime 版本 4.3”、“Spark 2.3.1”和“Scala 2.11”的 maven 坐标为 `spark-cassandra-connector_2.11-2.3.1`

* Azure Cosmos DB Cassandra API 的特定库：- 需要自定义连接工厂才能将重试策略从 Datastax Spark 连接器配置到 Azure Cosmos DB Cassandra API。 添加 `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`[maven 坐标](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar)将库附加到群集。

## <a name="sample-notebooks"></a>示例笔记本

可在 Github 存储库下载一系列 Azure Databricks [示例笔记本](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala)。 这些示例包括如何从 Spark 连接到 Azure Cosmos DB Cassandra API 并对数据执行不同的 CRUD 操作。 此外可以[将所有笔记本导入](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc)到 Databricks 群集工作区并运行。 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>从 Spark Scala 程序访问 Azure Cosmos DB Cassandra API

在 Azure Databricks 上作为自动化流程运行的 Spark 程序将通过使用 [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) 提交给群集，并安排在 Azure Databricks 作业中运行。

以下链接可以帮助你开始生成 Spark Scala 程序，以便与 Azure Cosmos DB Cassandra API 进行交互。
* [如何从 Spark Scala 程序连接到 Azure Cosmos DB Cassandra API](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [如何在 Azure Databricks 上以自动化作业的形式运行 Spark Scala 程序](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [使用 Cassandra API 的代码示例完整列表](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>后续步骤

开始使用 Java 应用程序[创建 Cassandra API 帐户、数据库和表](create-cassandra-api-account-java.md)。
