---
title: Azure Cosmos DB SQL API：Java SDK v4 示例
description: 在 GitHub 上查找使用 Azure Cosmos DB SQL API 的常见任务的 Java 示例，包括 CRUD 操作。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: 6e52cf9add10191554fb1bb5393773f316ded539
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666853"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>Azure Cosmos DB SQL API：Java SDK v4 示例

> [!div class="op_single_selector"]
> * [.NET V2 SDK 示例](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 示例](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 示例](sql-api-java-sdk-samples.md)
> * [Node.js 示例](sql-api-nodejs-samples.md)
> * [Python 示例](sql-api-python-samples.md)
> * [Azure 代码示例库](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

> [!IMPORTANT]  
> 若要详细了解 Java SDK v4，请参阅 Azure Cosmos DB Java SDK v4 [发行说明](sql-api-sdk-java-v4.md)、[Maven 存储库](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、Azure Cosmos DB Java SDK v4 [性能提示](performance-tips-java-sdk-v4-sql.md)和 Azure Cosmos DB Java SDK v4 [故障排除指南](troubleshoot-java-sdk-v4-sql.md)获取详细信息。 如果你当前使用的是早于 v4 的版本，请参阅[迁移到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南，获取升级到 v4 的相关帮助。
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- 可以[激活 Visual Studio 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：Visual Studio 订阅每月提供可用来试用付费版 Azure 服务的信用额度。
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

[azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples) GitHub 存储库中包含可对 Azure Cosmos DB 资源执行 CRUD 操作和其他常见操作的最新示例应用程序。 本文将提供：

* 每个示例 Java项目文件中各项任务的链接。 
* 指向相关的 API 参考内容的链接。

**先决条件**

需要以下条件才能运行此示例应用程序：

* Java 开发工具包 8
* Azure Cosmos DB Java SDK v4

可以选择使用 Maven 获取最新的 Azure Cosmos DB Java SDK v4 二进制文件，供在项目中使用。 Maven 会自动添加任何必需的依赖项。 否则，可以直接下载 pom.xml 文件中列出的依赖项并将它们添加到生成路径。

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**运行示例应用程序**

克隆示例存储库：
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

可以使用 IDE（Eclipse、IntelliJ 或 VSCODE）或使用 Maven 从命令行运行示例。

必须设置这些环境变量

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account master key
```

才能使示例可以对你的帐户进行读/写访问。

若要运行示例，请指定其 Main 类 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

其中 sample.synchronicity.MainClass 可以是
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* storedprocedure.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* changefeed.SampleChangeFeedProcessor *（Changefeed 只有一个异步示例，没有同步示例。）* ...等等...

> [!NOTE]
> 每个示例都是独立的，自行对自身进行设置并在完成后自行进行清理。 这些示例会发出多个调用以创建 `CosmosContainer`。 每当执行此操作时，即会根据所创建的集合的性能层，对订阅收取使用 1 小时的费用。 
> 
> 

## <a name="database-examples"></a>数据库示例
[数据库 CRUD 示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java)文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 数据库，请参阅[使用数据库、容器和项](databases-containers-items.md)概念性文章。 

| 任务 | API 参考 |
| --- | --- |
| [创建数据库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient.createDatabaseIfNotExists |
| [按 ID 读取数据库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient.getDatabase |
| [读取所有数据库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [删除数据库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase.delete |

## <a name="collection-examples"></a>集合示例
[集合 CRUD 示例](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java)文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 集合，请参阅[使用数据库、容器和项](databases-containers-items.md)概念性文章。

| 任务 | API 参考 |
| --- | --- |
| [创建集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase.createContainerIfNotExists |
| [更改集合的已配置性能](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [按 ID 获取集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase.getContainer |
| [读取数据库中的所有集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [删除集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>自动缩放集合示例

若要在运行这些示例之前了解有关自动缩放的详细信息，请查看以下说明，了解如何在[帐户](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/)以及[数据库和容器](https://docs.microsoft.com/azure/cosmos-db/provision-throughput-autoscale)中启用自动缩放。

[自动缩放数据库 CRUD 示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java)文件演示如何执行以下任务。

| 任务 | API 参考 |
| --- | --- |
| [创建具有指定自动缩放最大吞吐量的数据库](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

[自动缩放集合 CRUD 示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java)文件演示如何执行以下任务。 

| 任务 | API 参考 |
| --- | --- |
| [创建具有指定自动缩放最大吞吐量的集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase.createContainerIfNotExists |
| [更改集合的已配置自动缩放最大吞吐量](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [读取集合的自动缩放吞吐量配置](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>分析存储集合示例

[分析存储集合 CRUD 示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java)文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 集合，请阅读有关 Azure Cosmos DB Synapse 和分析存储的内容。

| 任务 | API 参考 |
| --- | --- |
| [创建集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>文档示例
[文档 CRUD 示例](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java)文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos 文档，请参阅[使用数据库、容器和项](databases-containers-items.md)概念性文章。

| 任务 | API 参考 |
| --- | --- |
| [创建文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer.createItem |
| [按 ID 读取文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [查询文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [替换文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [更新插入文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [删除文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [使用条件 ETag 检查替换文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition.setType<br>AccessCondition.setCondition |
| [仅当文档已更改时读取文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>索引示例
[集合 CRUD 示例](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java)文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的索引，请参阅[索引策略](index-policy.md)、[索引类型](index-types.md)和[索引路径](index-paths.md)概念文章。 

| 任务 | API 参考 |
| --- | --- |
| 从索引中排除文档 | ExcludedIndex<br>IndexingPolicy |
| 使用延迟索引 | IndexingPolicy.IndexingMode |
| [在索引中包括指定的文档路径](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [从索引中排除指定的文档路径](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [创建组合索引](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| 对哈希索引路径强制执行范围扫描操作 | FeedOptions.EnableScanInQuery |
| 对字符串使用范围索引 | IndexingPolicy.IncludedPaths<br>RangeIndex |
| 执行索引转换 | - |
| [创建地理空间索引](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

有关索引的详细信息，请参阅 [Azure Cosmos DB 索引策略](index-policy.md)。

## <a name="query-examples"></a>查询示例
[查询示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java)文件演示如何使用 SQL 查询语法执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的 SQL 查询引用，请参阅 [Azure Cosmos DB 的 SQL 查询示例](how-to-sql-query.md)。 

| 任务 | API 参考 |
| --- | --- |
| [查询所有文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [使用 == 查询等式](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [使用 != 和 NOT 查询不等式](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [使用 >、<、>=、<= 等范围运算符进行查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [使用范围运算符对字符串进行查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [使用 ORDER BY 进行查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [使用 DISTINCT 查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [使用聚合函数进行查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [使用子文档](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [使用文档内联接进行查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [使用字符串、数学和数组运算符进行查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [通过使用 SqlQuerySpec 的参数化 SQL 进行查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [使用显式分页进行查询](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [并行查询已分区集合](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| 使用 ORDER BY 针对已分区集合进行查询 | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>更改源示例 
[更改源处理器示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java)文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的更改源，请参阅[读取 Azure Cosmos DB 更改源](read-change-feed.md)和[更改源处理器](change-feed-processor.md)。

| 任务 | API 参考 |
| --- | --- |
| [基本的更改源功能](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| 读取特定时间的更改源 | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [从头读取更改源](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>服务器端编程示例

[存储过程示例](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java)文件演示如何执行以下任务。 若要在运行以下示例之前了解 Azure Cosmos DB 中的服务器端编程，请参阅[存储过程、触发器和用户定义的函数](stored-procedures-triggers-udfs.md)。

| 任务 | API 参考 |
| --- | --- |
| [创建存储过程](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [执行存储过程](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.execute |
| [删除存储过程](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>用户管理示例
用户管理示例文件演示如何执行以下任务：

| 任务 | API 参考 |
| --- | --- |
| 创建用户 | - |
| 对集合或文档设置权限 | - |
| 获取用户权限列表 |- |