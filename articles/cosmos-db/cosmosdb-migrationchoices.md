---
title: Cosmos DB 迁移选项
description: 此文档介绍将本地或云数据迁移到 Azure Cosmos DB 的各种选项
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 9111193bb441487b9e3c49bc9ee1a296d49f8a31
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882389"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>用于将本地或云数据迁移到 Azure Cosmos DB 的选项

您可以将数据从各种数据源加载到 Azure Cosmos DB。 此外，由于 Azure Cosmos DB 支持多个 Api，因此目标可以是任何现有的 Api。 为了支持不同源的迁移路径到不同 Azure Cosmos DB Api，有多个解决方案可为每个迁移路径提供专用处理。 本文档列出了可用的解决方案，并介绍了其优点和限制。

## <a name="factors-affecting-the-choice-of-migration-tool"></a>影响迁移工具选择的因素

以下因素决定了迁移工具的选择：
* **联机迁移与离线迁移**：许多迁移工具都提供仅执行一次性迁移的路径。 这意味着访问数据库的应用程序可能会遇到一段停机时间。 某些迁移解决方案提供一种在源和目标之间设置了复制管道的实时迁移。

* **数据源**：现有数据可以位于各种数据源中，如 Oracle DB2、Datastax Cassanda、Azure SQL Server、PostgreSQL 等。数据还可以在现有 Azure Cosmos DB 帐户中，迁移意向可以是更改数据模型或对容器中具有不同分区键的数据进行重新分区。

* **AZURE COSMOS DB api**：对于 Azure Cosmos DB 中的 SQL api，由 Azure Cosmos DB 团队开发的各种工具，这些工具可帮助实现不同的迁移方案。 所有其他 Api 都具有自己开发和维护的专用工具集。 由于 Azure Cosmos DB 在网络协议级别支持这些 Api，因此，在将数据迁移到 Azure Cosmos DB 时，这些工具应按原样工作。 但是，它们可能需要对限制进行自定义处理，因为此概念特定于 Azure Cosmos DB。

* **数据大小**：大多数迁移工具适用于较小的数据集。 如果数据集超过几百 gb，则迁移工具的选择将受到限制。 

* **预期迁移持续时间**：迁移可以配置为在速度较慢的速度较慢的情况下进行，也可以消耗在目标 Azure Cosmos DB 容器上预配的整个吞吐量，并在更少的时间内完成迁移阶段.

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**迁移类型**|**解决方案**|**注意事项**|
|---------|---------|---------|
|脱机|[数据迁移工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 易于设置和支持多个源 <br/>&bull; 不适用于大型数据集|
|脱机|[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 易于设置和支持多个源 <br/>&bull; 使用 Azure Cosmos DB 批量执行器库 <br/>适用于大型数据集的 &bull; <br/>&bull; 缺少检查点，这意味着，如果在迁移过程中出现问题，则需要重新启动整个迁移过程<br/>缺少死信队列的 &bull;，这意味着几个错误文件可以停止整个迁移过程。|
|脱机|[Azure Cosmos DB Spark 连接器](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; 使用 Azure Cosmos DB 批量执行器库 <br/>适用于大型数据集的 &bull; <br/>&bull; 需要自定义 Spark 设置 <br/>&bull; Spark 对架构不一致性敏感，这可能是迁移过程中的问题 |
|脱机|[具有 Cosmos DB 批量执行器库的自定义工具](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull; 提供检查点功能、可提高迁移复原能力的死信功能 <br/>适用于超大型数据集的 &bull; （10 TB +）  <br/>&bull; 需要此工具的自定义安装，作为应用服务运行 |
|联机|[Cosmos DB 函数 + ChangeFeed API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; 易于设置 <br/>仅当源为 Azure Cosmos DB 容器时，&bull; 才有效 <br/>&bull; 不适用于大型数据集 <br/>&bull; 不捕获源容器的删除 |
|联机|[使用 ChangeFeed 的自定义迁移服务](https://aka.ms/CosmosDBMigrationSample)|&bull; 提供进度跟踪 <br/>仅当源为 Azure Cosmos DB 容器时，&bull; 才有效 <br/>&bull; 也适用于较大的数据集 <br/>&bull; 要求用户设置应用服务以托管更改源处理器 <br/>&bull; 不捕获源容器的删除|
|联机|[Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull; 适用于 Oracle、DB2、SQL Server 等众多源 <br/>&bull; 轻松构建 ETL 管道并提供监视仪表板 <br/>&bull; 支持更大的数据集 <br/>&bull; 因为这是第三方工具，所以它需要从 marketplace 购买并安装在用户的环境中|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**迁移类型**|**解决方案**|**注意事项**|
|---------|---------|---------|
|脱机|[数据迁移工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 易于设置和支持多个源 <br/>&bull; 不适用于大型数据集|
|脱机|[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 易于设置和支持多个源 <br/>&bull; 使用 Azure Cosmos DB 批量执行器库 <br/>适用于大型数据集的 &bull; <br/>&bull; 缺少检查点，这意味着在迁移过程中出现的任何问题都需要重新启动整个迁移过程<br/>&bull; 缺少死信队列，则意味着几个错误的文件可能会停止整个迁移过程 <br/>&bull; 需要自定义代码以增加某些数据源的读取吞吐量|
|脱机|[现有的 Mongo 工具（mongodump、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 易于设置和集成 <br/>&bull; 需要对限制进行自定义处理|
|联机|[Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; 使用 Azure Cosmos DB 批量执行器库 <br/>&bull; 适用于大型数据集并负责复制实时更改 <br/>&bull; 仅适用于其他 MongoDB 源|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**迁移类型**|**解决方案**|**注意事项**|
|---------|---------|---------|
|脱机|[cqlsh 复制命令](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; 易于设置 <br/>&bull; 不适用于大型数据集 <br/>仅当源是 Cassandra 表时，&bull; 才有效|
|脱机|[用 Spark 复制表](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull; 可以利用 Spark 功能实现并行转换和引入 <br/>&bull; 需要使用自定义重试策略来处理限制|
|联机|[Striim （来自 Oracle DB/Apache Cassandra）](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull; 适用于 Oracle、DB2、SQL Server 等众多源 <br/>&bull; 轻松构建 ETL 管道并提供监视仪表板 <br/>&bull; 支持更大的数据集 <br/>&bull; 因为这是第三方工具，所以它需要从 marketplace 购买并安装在用户的环境中|
|联机|[Blitzz （来自 Oracle DB/Apache Cassandra）](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; 支持更大的数据集 <br/>&bull; 因为这是第三方工具，所以它需要从 marketplace 购买并安装在用户的环境中|

## <a name="other-apis"></a>其他 Api
对于除 SQL API、Mongo API 和 Cassandra API 以外的 Api，每个 API 的现有生态系统都支持各种工具。 

**表 API** 
* [数据迁移工具](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [图形大容量执行程序库](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>后续步骤

* 通过尝试使用[.net](bulk-executor-dot-net.md)和[Java](bulk-executor-java.md)中的批量执行程序库的示例应用程序，了解详细信息。 
* 大容量执行程序库已集成到 Cosmos DB Spark 连接器，若要了解详细信息，请参阅[Azure Cosmos DB Spark 连接器](spark-connector.md)一文。  
* 若要获得大规模迁移的更多帮助，请在 "常规建议" 问题类型和 "大型（TB +）迁移" 问题子类型下打开支持票证，联系 Azure Cosmos DB 产品团队。
