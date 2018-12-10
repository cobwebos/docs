---
title: 使用图形 BulkExecutor .NET 库在 Azure Cosmos DB Gremlin API 中执行批量操作
description: 了解如何使用 BulkExecutor 库将图形数据大规模导入 Azure Cosmos DB Gremlin API 容器中。
services: cosmos-db
keywords: 图形, gremlin, 批量, bulkexecutor, 迁移, 数据, cosmosdb, cosmos, 数据库, 导入
author: luisbosquez
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: d5a25bd7cab68f77a37b14ba41bf3cc832c2125f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836706"
---
# <a name="using-the-graph-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>使用图形 BulkExecutor .NET 库在 Azure Cosmos DB Gremlin API 中执行批量操作

本教程说明了如何使用 Azure Cosmos DB 的 BulkExecutor .NET 库将图形对象导入 Azure Cosmos DB Gremlin API 容器并对其进行更新。 此过程使用 [BulkExecutor 库](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview)中的 Graph 类以编程方式创建顶点和边缘对象，然后按网络请求插入多个这样的对象。 可以通过 BulkExecutor 库来配置此行为，以便优化对数据库和本地内存资源的使用。

与向数据库发送 Gremlin 查询以便在数据库中逐个地对命令进行评估和执行相反，使用 BulkExecutor 库会要求在本地创建和验证对象。 创建对象后，即可通过库将图形对象按顺序发送到数据库服务。 使用此方法时，数据引入速度最多可以提高到 100 倍，因此此方法特别适合初始数据迁移或定期数据移动操作。 有关详细信息，请访问 [Azure Cosmos DB 图形 BulkExecutor 示例应用程序](https://aka.ms/graph-bulkexecutor-sample)的 GitHub 页。

## <a name="bulk-operations-with-graph-data"></a>对图形数据执行的批量操作

[BulkExecutor 库](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet)包含一个 `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` 命名空间，用于提供创建和导入图形对象所需的功能。 

以下过程概述了如何将数据迁移用于 Gremlin API 容器：
1. 从数据源检索记录。
2. 根据获得的记录构造 `GremlinVertex` 和 `GremlinEdge` 对象，然后将其添加到 `IEnumerable` 数据结构中。 应该在应用程序的此部分实施检测和添加关系的逻辑，以免出现数据源不是图形数据库的情况。
3. 使用[图形 BulkImportAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet)将图形对象插入集合中。

与使用 Gremlin 客户端相比，此机制会提高数据迁移效率。 之所以会体验到这种效率提高，是因为使用 Gremlin 插入数据时，需要应用程序一次发送一个查询，该查询在创建数据之前需经历验证、评估和执行这几个阶段。 BulkExecutor 库会在应用程序中处理验证，并且会针对每个网络请求一次发送多个图形对象。

### <a name="creating-vertices-and-edges"></a>创建顶点和边缘

`GraphBulkExecutor` 提供的 `BulkImportAsync` 方法要求使用一个包含 `GremlinVertex` 或 `GremlinEdge` 对象的 `IEnumerable` 列表，这两个对象都是在 `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element` 命名空间中定义的。 在示例中，我们已将边缘和顶点划分到两个 BulkExecutor 导入任务中。 请参阅以下示例：

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

有关 BulkExecutor 库的参数的详细信息，请参阅[“将数据批量导入 Azure Cosmos DB”主题](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#bulk-import-data-to-azure-cosmos-db)。

有效负载需实例化为 `GremlinVertex` 和 `GremlinEdge` 对象。 下面是创建这些对象的方法：

**顶点**：
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**边缘**：
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> BulkExecutor 实用程序不会在添加边缘之前自动检查是否存在顶点。 在运行 BulkImport 任务之前，需在应用程序中对此进行验证。

## <a name="sample-application"></a>示例应用程序

### <a name="prerequisites"></a>先决条件
* 包含 Azure 开发工作负荷的 Visual Studio 2017。 一开始可以使用免费的 [Visual Studio 2017 Community Edition](https://visualstudio.microsoft.com/downloads/)。
* Azure 订阅。 可以[在此创建一个免费的 Azure 帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db)。 也可创建一个 Cosmos DB 数据库帐户，在没有 Azure 订阅的情况下[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)。
* 包含**不受限集合**的 Azure Cosmos DB Gremlin API 数据库。 本指南介绍了如何开始使用 [.NET 中的 Azure Cosmos DB Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet)。
* Git。 有关详细信息，请查看[“Git 下载”页](https://git-scm.com/downloads)。

### <a name="clone-the-sample-application"></a>克隆示例应用程序
在本教程中，我们将通过在 GitHub 中托管的 [Azure Cosmos DB 图形 BulkExecutor 示例](https://aka.ms/graph-bulkexecutor-sample)来完成入门步骤。 此应用程序包含一个 .NET 解决方案，该解决方案随机生成顶点和边缘对象，然后执行批量插入，以便将内容插入指定的图形数据库帐户中。 若要获取应用程序，请运行下面的 `git clone` 命令：

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

此存储库包含的 GraphBulkExecutor 示例包含以下文件：

文件|说明
---|---
`App.config`|在这里指定特定于应用程序和数据库的参数。 在连接到目标数据库和集合之前，应先修改此文件。
`Program.cs`| 此文件包含在创建 `DocumentClient` 集合、处理清理和发送 BulkExecutor 请求之后的逻辑。
`Util.cs`| 此文件包含一个帮助程序类，该类包含在生成测试数据以及检查是否存在数据库和集合之后的逻辑。

在 `App.config` 文件中，以下项是可以提供的配置值：

设置|说明
---|---
`EndPointUrl`|这是 **.NET SDK 终结点**，位于 Azure Cosmos DB Gremlin API 数据库帐户的“概览”边栏选项卡中。 此项的格式为 `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|这是在 Azure Cosmos DB 帐户下列出的主密钥或辅助密钥。 详细了解如何[确保对 Azure Cosmos DB 数据的安全访问](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys)
`DatabaseName`、`CollectionName`|这些是**目标数据库和集合名称**。 当 `ShouldCleanupOnStart` 设置为 `true` 时，将会根据这些值和 `CollectionThroughput` 来删除它们并创建新的数据库和集合。 同样，如果 `ShouldCleanupOnFinish` 设置为 `true`，将会根据这些值在引入完成后立即删除数据库。 请注意，目标集合必须是**无限制集合**。
`CollectionThroughput`|如果 `ShouldCleanupOnStart` 选项设置为 `true`，则使用此项来创建新的集合。
`ShouldCleanupOnStart`|此项会在程序运行之前删除数据库帐户和集合，然后使用 `DatabaseName`、`CollectionName` 和 `CollectionThroughput` 值创建新的。
`ShouldCleanupOnFinish`|此项会在程序运行之后删除具有指定 `DatabaseName` 和 `CollectionName` 的数据库帐户和集合。
`NumberOfDocumentsToImport`|此项决定将要在示例中生成的测试顶点和边缘的数目。 此数字将应用于顶点和边缘。
`NumberOfBatches`|此项决定将要在示例中生成的测试顶点和边缘的数目。 此数字将应用于顶点和边缘。
`CollectionPartitionKey`|此项将用于创建测试顶点和边缘，其中的此属性会自动分配。 此项也会用于在 `ShouldCleanupOnStart` 选项设置为 `true` 的情况下重新创建数据库和集合。

### <a name="run-the-sample-application"></a>运行示例应用程序

1. 在 `App.config` 中添加特定的数据库配置参数。 此文件将用于创建 DocumentClient 实例。 如果数据库和容器尚未创建，则会自动创建它们。
2. 运行应用程序。 此时会调用 `BulkImportAsync` 两次，一次是为了导入顶点，另一次是为了导入边缘。 如果在插入这些对象时，有对象生成了错误，则会将这些对象添加到 `.\BadVertices.txt` 或 `.\BadEdges.txt`。
3. 通过查询图形数据库来评估结果。 如果 `ShouldCleanupOnFinish` 选项设置为 true，则会自动删除此数据库。

## <a name="next-steps"></a>后续步骤
* 若要了解 Nuget 包的详细信息以及 Bulk Executor .Net 库的发行说明，请参阅 [Bulk Executor SDK 详细信息](sql-api-sdk-bulk-executor-dot-net.md)。 
* 请查看[性能提示](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips)，以便进一步优化 BulkExecutor 的使用。
* 请查看 [BulkExecutor.Graph 参考文章](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet)，以便更详细地了解在此命名空间中定义的类和方法。
