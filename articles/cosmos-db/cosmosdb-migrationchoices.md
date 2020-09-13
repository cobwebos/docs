---
title: Cosmos DB 迁移选项
description: 此文档介绍用于将本地或云数据迁移到 Azure Cosmos DB 的各种选项
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 4de6d4ba019af75b0f6179b2794ddb6c1e35e0c1
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030066"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>用于将本地或云数据迁移到 Azure Cosmos DB 的选项

可将各种数据源中的数据加载到 Azure Cosmos DB。 由于 Azure Cosmos DB 支持多个 Api，因此目标可以是任何现有的 Api。 以下是将数据迁移到 Azure Cosmos DB 的一些方案：

* 将数据从一个 Azure Cosmos 容器移动到同一数据库或不同数据库中的另一个容器。
* 将专用容器间的数据移动到共享数据库容器。
* 将数据从位于 region1 的 Azure Cosmos 帐户移到相同或不同区域中的另一个 Azure Cosmos 帐户。
* 将数据从 Azure blob 存储、JSON 文件、Oracle 数据库、Couchbase、DynamoDB 等源移动到 Azure Cosmos DB。

为了支持从各种源到不同 Azure Cosmos DB API 的迁移路径，有多种解决方案可为每个迁移路径提供专业化的处理。 本文档列出了可用的解决方案，并描述了其优势和限制。

## <a name="factors-affecting-the-choice-of-migration-tool"></a>影响迁移工具选择的因素

以下因素决定了迁移工具的选择：

* **联机与脱机迁移**：许多迁移工具提供的路径仅限一次性迁移。 这意味着，访问数据库的应用程序可能会出现一段时间的停机。 某些迁移解决方案提供实时迁移的方式，这需要在源与目标之间设置复制管道。

* **数据源**：现有数据可以位于各种数据源中，例如 Oracle DB2、Datastax Cassanda、Azure SQL Database、PostgreSQL 等。数据还可以位于现有的 Azure Cosmos DB 帐户中，迁移的意图可以是更改数据模型，或者使用不同的分区键对容器中的数据重新分区。

* **Azure Cosmos DB API**：对于 Azure Cosmos DB 中的 SQL API，Azure Cosmos DB 团队开发了各种工具来帮助实现不同的迁移方案。 所有其他 API 具有自身的，由社区开发和维护的专业工具集。 由于 Azure Cosmos DB 在网络协议级别支持这些 API，因此，在将数据迁移到 Azure Cosmos DB 时，这些工具也应按原样运行。 但是，它们可能需要对限制进行自定义的处理，因为此概念特定于 Azure Cosmos DB。

* **数据大小**：大多数迁移工具非常适合用于较小的数据集。 如果数据集超过几百 GB，则迁移工具的选择将受到限制。 

* **预期的迁移持续时间**：可将迁移配置为以递增步调缓慢进行，以减少消耗的吞吐量；或者，可将其配置为消耗针对目标 Azure Cosmos DB 容器预配的整个吞吐量，以便更快地完成迁移。

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

|迁移类型|解决方案|受支持的源|支持的目标|注意事项|
|---------|---------|---------|---------|---------|
|Offline|[数据迁移工具](import-data.md)| &bull;JSON/CSV 文件<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;表存储<br/>&bull;AWS DynamoDB<br/>&bull;Azure Blob 存储|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB 表 API<br/>&bull;JSON 文件 |&bull; 易于设置和支持多个源。 <br/>&bull; 不适合用于大型数据集。|
|Offline|[Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV 文件<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;用于 MongoDB 的 Azure Cosmos DB API<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;表存储<br/>&bull;Azure Blob 存储 <br/> <br/>有关其他受支持的源，请参阅 [Azure 数据工厂](../data-factory/connector-overview.md) 一文。|&bull;Azure Cosmos DB SQL API<br/>&bull;用于 MongoDB 的 Azure Cosmos DB API<br/>&bull;JSON 文件 <br/><br/> 有关其他支持的目标，请参阅 [Azure 数据工厂](../data-factory/connector-overview.md) 一文。 |&bull; 易于设置和支持多个源。<br/>&bull; 使用 Azure Cosmos DB 大容量执行程序库。 <br/>&bull; 适用于大型数据集。 <br/>&bull; 缺少检查点-这意味着，如果在迁移过程中出现问题，则需要重新启动整个迁移过程。<br/>&bull; 缺少死信队列 - 这意味着，出现几个有错误的文件就可能会停止整个迁移过程。|
|Offline|[Azure Cosmos DB Spark 连接器](spark-connector.md)|Azure Cosmos DB SQL API。 <br/><br/>可以将其他源用于 Spark 生态系统的其他连接器。| Azure Cosmos DB SQL API。 <br/><br/>可以将其他目标用于 Spark 生态系统的其他连接器。| &bull; 使用 Azure Cosmos DB 大容量执行程序库。 <br/>&bull; 适用于大型数据集。 <br/>&bull; 需要自定义 Spark 设置。 <br/>&bull; Spark 对架构不一致非常敏感，迁移过程中可能会出现问题。 |
|Offline|[包含 Cosmos DB 批量执行程序库的自定义工具](migrate-cosmosdb-data.md)| 源依赖于自定义代码 | Azure Cosmos DB SQL API| &bull; 提供检查点功能，这些功能可提高迁移复原能力。 <br/>&bull; 适用于超大型数据集 (10 TB +) 。  <br/>&bull; 需要此工具的自定义安装，以应用服务的形式运行。 |
|联机|[Cosmos DB Functions + ChangeFeed API](change-feed-functions.md)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; 易于设置。 <br/>&bull; 仅当源是 Azure Cosmos DB 容器时才起作用。 <br/>&bull; 不适合用于大型数据集。 <br/>&bull; 不捕获源容器的删除操作。 |
|联机|[使用 ChangeFeed 的自定义迁移服务](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| Azure Cosmos DB SQL API | Azure Cosmos DB SQL API| &bull; 提供进度跟踪。 <br/>&bull; 仅当源是 Azure Cosmos DB 容器时才起作用。 <br/>&bull; 同样适用于较大的数据集。<br/>&bull; 要求用户设置应用服务以托管更改源处理器。 <br/>&bull; 不捕获源容器的删除操作。|
|联机|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;联手 <br/>&bull;Apache Cassandra<br/><br/> 有关其他受支持的源，请参阅 [Striim 网站](https://www.striim.com/sources-and-targets/) 。 |&bull;Azure Cosmos DB SQL API <br/>&bull; Azure Cosmos DB Cassandra API<br/><br/> 有关其他受支持的目标，请参阅 [Striim 网站](https://www.striim.com/sources-and-targets/) 。 | &bull; 适用于各种源，如 Oracle、DB2、SQL Server。<br/>&bull; 轻松构建 ETL 管道并提供监视仪表板。 <br/>&bull; 支持更大的数据集。 <br/>&bull; 由于这是第三方工具，因此需要从 marketplace 购买并安装在用户的环境中。|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API

|迁移类型|解决方案|受支持的源|支持的目标|注意事项|
|---------|---------|---------|---------|---------|
|联机|[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|用于 MongoDB 的 Azure Cosmos DB API |&bull; 使用 Azure Cosmos DB 大容量执行程序库。 <br/>&bull; 适用于大型数据集并负责复制实时更改。 <br/>&bull; 仅适用于其他 MongoDB 源。|
|Offline|[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| 用于 MongoDB 的 Azure Cosmos DB API| &bull; 使用 Azure Cosmos DB 大容量执行程序库。 <br/>&bull; 适用于大型数据集并负责复制实时更改。 <br/>&bull; 仅适用于其他 MongoDB 源。|
|Offline|[Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)| &bull;JSON/CSV 文件<br/>&bull;Azure Cosmos DB SQL API<br/>&bull;用于 MongoDB 的 Azure Cosmos DB API <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;表存储<br/>&bull;Azure Blob 存储 <br/><br/> 有关其他受支持的源，请参阅 [Azure 数据工厂](../data-factory/connector-overview.md) 一文。 | &bull;Azure Cosmos DB SQL API<br/>&bull;用于 MongoDB 的 Azure Cosmos DB API <br/>&bull; JSON 文件 <br/><br/> 有关其他支持的目标，请参阅 [Azure 数据工厂](../data-factory/connector-overview.md) 一文。| &bull; 易于设置和支持多个源。 <br/>&bull; 使用 Azure Cosmos DB 大容量执行程序库。 <br/>&bull; 适用于大型数据集。 <br/>&bull; 缺少检查点，这意味着在迁移过程中出现的任何问题都需要重新启动整个迁移过程。<br/>&bull; 缺少死信队列意味着几个错误文件可能会停止整个迁移过程。 <br/>&bull; 需要自定义代码以增加某些数据源的读取吞吐量。|
|Offline|[现有的 Mongo 工具（mongodump、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | 用于 MongoDB 的 Azure Cosmos DB API| &bull; 易于设置和集成。 <br/>&bull; 需要对限制进行自定义处理。|

## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API

|迁移类型|解决方案|受支持的源|支持的目标|注意事项|
|---------|---------|---------|---------|---------|
|Offline|[cqlsh COPY 命令](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|CSV 文件 | Azure Cosmos DB Cassandra API| &bull; 易于设置。 <br/>&bull; 不适合用于大型数据集。 <br/>&bull; 仅当源是 Cassandra 表时才起作用。|
|Offline|[用 Spark 复制表](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;Azure Cosmos DB Cassandra API| Azure Cosmos DB Cassandra API | &bull; 可以利用 Spark 功能并行转换和引入。 <br/>&bull; 需要具有自定义重试策略的配置来处理限制。|
|联机|[Oracle DB/Apache Cassandra 中的 Striim () ](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;联手<br/>&bull;Apache Cassandra<br/><br/> 有关其他受支持的源，请参阅 [Striim 网站](https://www.striim.com/sources-and-targets/) 。|&bull;Azure Cosmos DB SQL API<br/>&bull;Azure Cosmos DB Cassandra API <br/><br/> 有关其他受支持的目标，请参阅 [Striim 网站](https://www.striim.com/sources-and-targets/) 。| &bull; 适用于各种源，如 Oracle、DB2、SQL Server。 <br/>&bull; 轻松构建 ETL 管道并提供监视仪表板。 <br/>&bull; 支持更大的数据集。 <br/>&bull; 由于这是第三方工具，因此需要从 marketplace 购买并安装在用户的环境中。|
|联机|[Blitzz（来自 Oracle DB/Apache Cassandra）](oracle-migrate-cosmos-db-blitzz.md)|&bull;联手<br/>&bull;Apache Cassandra<br/><br/>有关其他受支持的源，请参阅 [Blitzz 网站](https://www.blitzz.io/) 。 |Azure Cosmos DB Cassandra API。 <br/><br/>有关其他受支持的目标，请参阅 [Blitzz 网站](https://www.blitzz.io/) 。 | &bull; 支持更大的数据集。 <br/>&bull; 由于这是第三方工具，因此需要从 marketplace 购买并安装在用户的环境中。|

## <a name="other-apis"></a>其他 API

对于除 SQL API、Mongo API 和 Cassandra API 以外的 API，每个 API 的现有生态系统支持各种工具。 

**表 API** 

* [数据迁移工具](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [Graph 批量执行程序库](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>后续步骤

* 若要进行详细了解，请试用那些在 [.NET](bulk-executor-dot-net.md) 和 [Java](bulk-executor-java.md) 中使用批量执行程序库的示例应用程序。 
* 批量执行程序库已集成到 Cosmos DB Spark 连接器中。若要进行详细的了解，请参阅 [Azure Cosmos DB Spark 连接器](spark-connector.md)一文。  
* 如需大规模迁移方面的更多帮助，请通过开具支持票证来联系 Azure Cosmos DB 产品团队：选择“常规建议”问题类型，“大规模迁移(TB+)”问题子类型。
