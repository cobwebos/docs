---
title: 将数据迁移到 Azure Cosmos DB Cassandra API 帐户
description: 了解如何使用 CQL Copy 命令和 Spark 将数据从 Apache Cassandra 复制到 Azure Cosmos DB Cassandra API。
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: f73a201a25bb2f975e8a261a6c21aa7b066c3a7c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247844"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>将数据迁移到 Azure Cosmos DB Cassandra API 帐户

本教程介绍如何将 Apache Cassandra 数据迁移到 Azure Cosmos DB Cassandra API。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 规划迁移
> * 迁移的先决条件
> * 使用 cqlsh COPY 命令迁移数据
> * 使用 Spark 迁移数据 

## <a name="plan-for-migration"></a>规划迁移

在将数据迁移到 Azure Cosmos DB Cassandra API 之前，应估计工作负载的吞吐量需求。 通常，建议先从 CRUD 操作所需的平均吞吐量开始，然后再包括提取转换加载 (ETL) 或高峰操作所需的额外吞吐量。 需要以下详细信息来规划迁移： 

* 现有数据大小或估计数据大小：定义最小数据库大小和吞吐量要求。 如果正在估计新应用程序的数据大小，则可以假定数据均匀分布在行中并通过乘以数据大小来估计值。 

* 所需的吞吐量：近似读取（查询/获取）和写入（更新/删除/插入）吞吐率。 需要使用此值来计算所需的请求单位以及处于稳定状态的数据大小。  

* 获取架构：通过 cqlsh 连接到现有的 Cassandra 群集并从 Cassandra 中导出架构： 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

确定现有工作负载的需求后，应根据收集到的吞吐量需求创建一个 Azure Cosmos DB 帐户、数据库和容器。  

* 确定操作的 RU 费用：可以使用所选的 Azure Cosmos DB Cassandra API SDK 来确定 RU。 此示例演示获取 RU 费用的 .NET 版本。

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* 分配所需的吞吐量：随着吞吐量需求的增长，Azure Cosmos DB 可以自动扩展存储和吞吐量。 可以使用 [Azure Cosmos DB 请求单位计算器](https://www.documentdb.com/capacityplanner)来估计吞吐量需求。 

## <a name="prerequisites-for-migration"></a>迁移的先决条件

* 在 Azure Cosmos DB Cassandra API 帐户中创建表：在开始迁移数据之前，从 Azure 门户或从 cqlsh 预创建所有表。 如果要迁移到具有数据库级别吞吐量的 Azure Cosmos DB 帐户，请确保在创建 Azure Cosmos DB 容器时提供分区键。

* 增加吞吐量：数据迁移的持续时间取决于为 Azure Cosmos DB 中的表预配的吞吐量。 在迁移期间增加吞吐量。 提高吞吐量后，可避免受到速率限制，并缩短迁移时间。 完成迁移后，减少吞吐量以节约成本。 有关增加吞吐量的详细信息，请参阅[设置 Azure Cosmos DB 容器的吞吐量](set-throughput.md)。 此外，还建议在源数据库所在的同一区域中拥有 Azure Cosmos DB 帐户。 

* 启用 SSL：Azure Cosmos DB 具有严格的安全要求和标准。 请确保在与帐户进行交互时启用 SSL。 当你将 CQL 与 SSH 配合使用时，可以选择提供 SSL 信息。

## <a name="options-to-migrate-data"></a>迁移数据的选项

你可以使用以下选项将数据从现有的 Cassandra 工作负载移到 Azure Cosmos DB：

* [使用 cqlsh COPY 命令](#using-cqlsh-copy-command)  
* [使用 Spark](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>使用 cqlsh COPY 命令迁移数据

[CQL COPY 命令](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)用于将本地数据复制到 Azure Cosmos DB Cassandra API 帐户。 使用以下步骤复制数据：

1. 获取 Cassandra API 帐户的连接字符串信息：

   * 登录到 [Azure 门户](https://portal.azure.com)，导航到 Azure Cosmos DB 帐户。

   * 打开“连接字符串”窗格，其中包含从 cqlsh 连接到 Cassandra API 帐户所需的所有信息。

2. 使用门户中的连接信息登录到 cqlsh。

3. 使用 CQL COPY 命令将本地数据复制到 Cassandra API 帐户。

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>使用 Spark 迁移数据 

使用以下步骤通过 Spark 将数据迁移到 Azure Cosmos DB Cassandra API：

- 预配 [Azure Databricks](cassandra-spark-databricks.md) 或 [HDInsight 群集](cassandra-spark-hdinsight.md) 

- 使用[表复制操作](cassandra-spark-table-copy-ops.md)将数据移到目标 Cassandra API 终结点 

如果有数据驻留在 Azure 虚拟机或任何其他云的现有群集中，则建议使用 Spark 作业迁移数据。 这需要将 Spark 设置为一次性的中介，或者定期引入。 你可以使用本地与 Azure 之间的快速路由连接来加快此迁移。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何将数据迁移到 Azure Cosmos DB Cassandra API 帐户。 现在可以转到“概念”部分详细了解 Azure Cosmos DB。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB 中的可优化数据一致性级别](../cosmos-db/consistency-levels.md)


