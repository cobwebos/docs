---
title: Cosmos DB 迁移选项
description: 此文档介绍用于将本地或云数据迁移到 Azure Cosmos DB 的各种选项
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 34698a215477abdd7d68c3dfe050657ecf049690
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984889"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>用于将本地或云数据迁移到 Azure Cosmos DB 的选项

可将各种数据源中的数据加载到 Azure Cosmos DB。 此外，由于 Azure Cosmos DB 支持多个 API，因此目标可以是任何现有的 API。 为了支持从各种源到不同 Azure Cosmos DB API 的迁移路径，有多种解决方案可为每个迁移路径提供专业化的处理。 本文档列出了可用的解决方案，并描述了其优势和限制。

## <a name="factors-affecting-the-choice-of-migration-tool"></a>影响迁移工具选择的因素

以下因素决定了迁移工具的选择：
* **联机迁移与脱机迁移**：许多迁移工具提供仅执行一次性迁移的路径。 这意味着，访问数据库的应用程序可能会出现一段时间的停机。 某些迁移解决方案提供实时迁移的方式，这需要在源与目标之间设置复制管道。

* **数据源**：现有数据可以位于各种数据源中，如 Oracle DB2、Datatax Cassanda、Azure SQL Server、PostgreSQL 等。数据也可以位于现有的 Azure Cosmos DB 帐户中，迁移的目的可能是更改数据模型或使用不同的分区键重新分区容器中的数据。

* **Azure Cosmos DB API**：对于 Azure Cosmos DB 中的 SQL API，Azure Cosmos DB 团队开发了多种工具，这些工具有助于处理不同的迁移方案。 所有其他 API 具有自身的，由社区开发和维护的专业工具集。 由于 Azure Cosmos DB 在网络协议级别支持这些 API，因此，在将数据迁移到 Azure Cosmos DB 时，这些工具也应按原样运行。 但是，它们可能需要对限制进行自定义的处理，因为此概念特定于 Azure Cosmos DB。

* **数据的大小**：大多数迁移工具对于较小的数据集非常有效。 如果数据集超过几百 GB，则迁移工具的选择将受到限制。 

* **预期的迁移持续时间**：可以将迁移配置为以缓慢、增量的速度进行，消耗更少的吞吐量，或者可以使用目标 Azure Cosmos DB 容器上预配的整个吞吐量，并在更短的时间内完成迁移。

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API
|**迁移类型**|**解决方案**|**注意事项**|
|---------|---------|---------|
|Offline|[数据迁移工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 易于设置且支持多个源 <br/>&bull; 不适合用于大型数据集|
|Offline|[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 易于设置且支持多个源 <br/>&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集 <br/>&bull; 缺少检查点 - 这意味着，如果在迁移过程中出现问题，需要重启整个迁移过程<br/>&bull; 缺少死信队列 - 这意味着，出现几个有错误的文件就可能会停止整个迁移过程。|
|Offline|[Azure 宇宙 DB 火花连接器](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集 <br/>&bull; 需要自定义的 Spark 设置 <br/>&bull; Spark 对架构不一致性比较敏感，这可能会在迁移过程中造成问题 |
|Offline|[包含 Cosmos DB 批量执行程序库的自定义工具](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull; 提供检查点和死信功能，可提高迁移复原能力 <br/>&bull; 适合用于极大型数据集 (10 TB+)  <br/>&bull; 需要对此工具进行自定义设置，使其作为应用服务运行 |
|联机|[Cosmos DB Functions + ChangeFeed API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull; 易于设置 <br/>&bull; 仅当源是 Azure Cosmos DB 容器时才适用 <br/>&bull; 不适合用于大型数据集 <br/>&bull; 不捕获源容器中的删除操作 |
|联机|[使用 ChangeFeed 的自定义迁移服务](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull; 提供进度跟踪 <br/>&bull; 仅当源是 Azure Cosmos DB 容器时才适用 <br/>&bull; 也适用于较大的数据集 <br/>&bull; 要求用户设置一个应用服务来托管更改源处理器 <br/>&bull; 不捕获源容器中的删除操作|
|联机|[斯特里伊姆](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;适用于各种来源，如 Oracle、DB2、SQL Server <br/>&bull;易于构建 ETL 管道，并提供用于监视的仪表板 <br/>&bull; 支持较大的数据集 <br/>&bull; 由于这是一个第三方工具，因此需要从市场购买并将其安装在用户环境中|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**迁移类型**|**解决方案**|**注意事项**|
|---------|---------|---------|
|Offline|[数据迁移工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 易于设置且支持多个源 <br/>&bull; 不适合用于大型数据集|
|Offline|[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 易于设置且支持多个源 <br/>&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集 <br/>&bull; 缺少检查点，这意味着，在迁移过程中出现任何问题都需要重启整个迁移过程<br/>&bull; 缺少死信队列，这意味着，出现几个有错误的文件就可能会停止整个迁移过程。 <br/>&bull; 需要编写自定义代码来增大某些数据源的读取吞吐量|
|Offline|[现有的 Mongo 工具（mongodump、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 易于设置和集成 <br/>&bull; 需要对限制进行自定义处理|
|联机|[Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集，负责复制实时更改 <br/>&bull; 仅适用于其他 MongoDB 源|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API
|**迁移类型**|**解决方案**|**注意事项**|
|---------|---------|---------|
|Offline|[cqlsh COPY 命令](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull; 易于设置 <br/>&bull; 不适合用于大型数据集 <br/>&bull; 仅当源是 Cassandra 表时才适用|
|Offline|[用 Spark 复制表](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull; 可以利用 Spark 功能来并行化转换和引入 <br/>&bull; 需要通过使用自定义重试策略的配置来处理限制|
|联机|[斯特里姆（来自甲骨文数据库/阿帕奇卡桑德拉）](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;适用于各种来源，如 Oracle、DB2、SQL Server <br/>&bull;易于构建 ETL 管道，并提供用于监视的仪表板 <br/>&bull; 支持较大的数据集 <br/>&bull; 由于这是一个第三方工具，因此需要从市场购买并将其安装在用户环境中|
|联机|[Blitzz（来自 Oracle DB/Apache Cassandra）](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull; 支持较大的数据集 <br/>&bull; 由于这是一个第三方工具，因此需要从市场购买并将其安装在用户环境中|

## <a name="other-apis"></a>其他 API
对于除 SQL API、Mongo API 和 Cassandra API 以外的 API，每个 API 的现有生态系统支持各种工具。 

**表 API** 
* [数据迁移工具](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [阿兹比贝](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**Gremlin API**
* [Graph 批量执行程序库](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>后续步骤

* 通过尝试在[.NET](bulk-executor-dot-net.md)和[Java](bulk-executor-java.md)中使用批量执行者库的示例应用程序，了解更多信息。 
* 批量执行器库集成到 Cosmos DB Spark 连接器中，有关详细信息，请参阅[Azure Cosmos DB Spark 连接器](spark-connector.md)一文。  
* 如需大规模迁移方面的更多帮助，请通过开具支持票证来联系 Azure Cosmos DB 产品团队：选择“常规建议”问题类型，“大规模迁移(TB+)”问题子类型。
