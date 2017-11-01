---
title: "使用 Azure Cosmos DB 中的更改源支持 | Microsoft Docs"
description: "使用 Azure Cosmos DB 的更改源支持跟踪文档中发生的更改，执行基于事件的处理（例如触发器），使缓存和分析系统保持最新状态。"
keywords: "更改源"
services: cosmos-db
author: arramac
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 2d7798db-857f-431a-b10f-3ccbc7d93b50
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: article
ms.date: 10/10/2017
ms.author: arramac
ms.openlocfilehash: 0971959fb168d92096531d1c081666cf301608cf
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2017
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的更改源支持

[Azure Cosmos DB](../cosmos-db/introduction.md) 是快速灵活的全球复制数据库服务，非常适合用于 IoT、游戏、零售和操作日志记录应用程序。 这些应用程序中的一种常见设计模式是使用数据更改来发起附加的操作。 这些附加操作可能是下列其中一项： 

* 插入或修改文档时触发通知或 API 调用。
* 对 IoT 进行流式处理，或执行分析。
* 通过与缓存、搜索引擎或数据仓库同步，或者将数据存档到冷存储，进行附加的数据移动。

使用 Azure Cosmos DB 中的**更改源支持**，可针对每种模式构建高效、可缩放的解决方案，如下图所示：

![使用 Azure Cosmos DB 更改源促成实时分析和事件驱动的计算方案](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> 更改源支持仅提供给 Azure Cosmos DB 中的所有数据模型和容器。 但是，更改源是使用 DocumentDB 客户端读取的，会将项序列化为 JSON 格式。 由于采用 JSON 格式，MongoDB 客户端会遇到 BSON 格式的文档与 JSON 格式的更改源不匹配的情况。 

## <a name="how-does-change-feed-work"></a>更改源的工作原理

Azure Cosmos DB 中的更改源支持的工作原理是侦听 Azure Cosmos DB 集合中发生的任何更改。 然后，它会按照所更改文档的修改顺序输出这些文档的排序列表。 这些更改将会持久保留且能以异步和增量方式进行处理。可将输出分配到一个或多个使用者供并行处理。 

如本文稍后所述，可通过三种不同的方式读取更改源：

1.  [使用 Azure Functions](#azure-functions)
2.  [使用 Azure Cosmos DB SDK](#rest-apis)
3.  [使用 Azure Cosmos DB 更改源处理器库](#change-feed-processor)

更改源适用于文档集合中的每个分区键范围，因此，可以分配到一个或多个使用者供并行处理，如下图所示。

![Azure Cosmos DB 更改源的分布式处理](./media/change-feed/changefeedvisual.png)

其他详细信息：
* 默认已为所有帐户启用更改源。
* 可使用写入区域或任何[读取区域](distribute-data-globally.md)中的[预配吞吐量](request-units.md)从更改源中读取数据，就像执行其他任何 Azure Cosmos DB 操作一样。
* 更改源包括针对集合中的文档所做的插入和更新操作。 可以通过在文档中的删除位置设置“软删除”标志来捕获删除操作。 或者，可以通过 [TTL 功能](time-to-live.md)为文档设置有限的过期期限（例如 24 小时），并使用该属性的值捕获删除操作。 使用此解决方案时，处理更改的时间间隔必须比 TTL 过期期限要短。
* 在更改源中，对文档的每个更改都将显示一次，且客户端管理其检查点逻辑。 更改源处理器库提供自动检查点和“至少一次”语义。
* 更改日志中仅包含最近对给定文档所做的更改， 而不包含中途的更改。
* 更改源按照每个分区键值中的修改顺序排序。 无法保证各分区键值中的顺序一致。
* 更改可从任意时间点同步，也就是说，发生更改的数据没有固定的保留期。
* 更改以分区键范围区块提供。 多个使用者/服务器可以使用此功能并行处理大型集合中发生的更改。
* 应用程序可针对同一集合同时请求多个更改源。

## <a name="use-cases-and-scenarios"></a>用例和方案

使用更改源可对具有大量写入操作的大型数据集进行有效处理，这样就不需要查询整个数据集来识别发生了哪些更改。 

例如，使用更改源可以有效地执行以下任务：

* 使用 Azure Cosmos DB 中存储的数据更新缓存、搜索索引或数据仓库。
* 实现应用程序级别的数据分层和存档，即，将“热数据”存储在 Azure Cosmos DB 中，将“冷数据”搁置在 [Azure Blob 存储](../storage/common/storage-introduction.md)或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 中。
* 使用 [Apache Hadoop](run-hadoop-with-hdinsight.md) 实现数据批量分析。
* 在不造成任何停机的情况下迁移到使用不同分区方案的另一个 Azure Cosmos DB 帐户。
* 使用 Azure Cosmos DB [在 Azure 上实现 lambda 管道](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)。 Azure Cosmos DB 提供一种可缩放的数据库解决方案，该解决方案可处理引入和查询，实现 TCO 较低的 lambda 体系结构。 
* 接收和存储设备、传感器、基础架构和应用程序发出的事件数据，并使用 [Azure 流分析](../stream-analytics/stream-analytics-documentdb-output.md)、[Apache Storm](../hdinsight/hdinsight-storm-overview.md) 或 [Apache Spark](../hdinsight/hdinsight-apache-spark-overview.md) 实时处理这些事件。 

下图显示了可以使用 Azure Cosmos DB 执行引入和查询的 lambda 管道如何使用更改源支持： 

![用于引入和查询的基于 Azure Cosmos DB 的 lambda 管道](./media/change-feed/lambda.png)

另外，在[无服务器的](http://azure.com/serverless) Web 应用和移动应用中，可以跟踪各种事件（例如，对客户配置文件、首选项或位置的更改），以触发特定的操作，例如，使用 [Azure Functions](#azure-functions) 向客户的设备发送推送通知。 例如，若要使用 Azure Cosmos DB 构建游戏，可使用更改源，根据已完成的游戏的分数实时更新排行榜。

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>使用 Azure Functions 

如果使用 Azure Functions，连接到 Azure Cosmos DB 更改数据源的最简单方法是将一个 Azure Cosmos DB 触发器添加到 Azure Functions 应用。 在 Azure Functions 应用中创建 Azure Cosmos DB 触发器时，请选择要连接到的 Azure Cosmos DB 集合，以及每当对该集合做出更改时要触发的函数。 

可在 Azure Functions 门户、Azure Cosmos DB 门户中或以编程方式创建触发器。 有关详细信息，请参阅 [Azure Cosmos DB：使用 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)。

<a id="rest-apis"></a>
## <a name="using-the-sdk"></a>使用 SDK

Azure Cosmos DB 的 [DocumentDB SDK](documentdb-sdk-dotnet.md) 提供用于读取和管理更改源的所有强大功能。 但是，强大的功能也附带了诸多的责任。 如果想要管理检查点、处理文档序列号，并想要精细控制分区键，则使用 SDK 可能是适当的方法。

本部分逐步讲解如何使用 DocumentDB SDK 来处理更改源。

1. 首先，读取 appconfig 中的以下资源。 [更新连接字符串](create-documentdb-dotnet.md#update-your-connection-string)中提供了有关检索终结点和授权密钥的说明。

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. 按如下所示创建客户端：

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. 获取分区键范围：

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. 针对每个分区键范围调用 ExecuteNextAsync：

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

如果有多个读取者，可以使用 **ChangeFeedOptions** 将读取负载分配到不同的线程或不同的客户端。

只需编写这几行代码，即可开始读取更改源。 可从 [azure-cosmos-db-DocumentFeed GitHub 存储库](https://github.com/rsarosh/azure-cosmos-db-DocumentFeed)获取本文中使用的完整代码。

在上面步骤 4 所示的代码中，最后一行中的 **ResponseContinuation** 包含文档的最后一个逻辑序列号 (LSN)，下一次读取此序列号后面的新文档时，将要使用此序列号。 使用 **ChangeFeedOption** 的 **StartTime**，可以拓宽文档的检索覆盖面。 因此，如果 **ResponseContinuation** 为 null，但 **StartTime** 是过去的某个时间，则会获得从 **StartTime** 开始更改的所有文档。 但是，如果 **ResponseContinuation** 使用了某个值，则系统会获取从该 LSN 开始的所有文档。

因此，检查点数组只会保留每个分区的 LSN。 但如果不想要处理分区、检查点、LSN、开始时间等等，更简单的做法是使用更改源处理器库。

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>使用更改源处理器库 

借助 [Azure Cosmos DB 更改源处理器库](https://docs.microsoft.com/azure/cosmos-db/documentdb-sdk-dotnet-changefeed)，可以轻松地在多个使用者之间分配事件处理负载。 此库简化了跨分区的以及并行工作的多个线程中的更改的读取。

更改源处理器库的主要优势在于，无需管理每个分区和继续标记，也无需手动轮询每个集合。

更改源处理器库简化了跨分区的以及并行工作的多个线程中的更改的读取。  它使用租约机制自动管理跨分区的更改读取。 如下图中所示，如果启动两个使用更改源处理器库的客户端，它们会在彼此之间划分工作。 如果不断增加客户端，它们仍会在彼此之间划分工作。

![Azure Cosmos DB 更改源的分布式处理](./media/change-feed/change-feed-output.png)

左侧的客户端先启动并开始监视所有分区，然后第二个客户端启动，随后，第一个客户端让某些租约转移到第二个客户端。 可以看出，这是在不同的计算机和客户端之间分配工作的极佳方法。

请注意，如果有两个无服务器 Azure 函数正在监视同一个集合并使用相同的租约，则根据处理器库决定处理分区的方式，这两个函数可能会收到不同的文档。

### <a name="understanding-the-change-feed-processor-library"></a>了解更改源处理器库

实现更改源处理器需要四个主要组件：监视集合、租用集合、处理器主机和使用者。 

> [!WARNING]
> 创建集合会影响定价，因为要保留应用程序的吞吐量才能与 Azure Cosmos DB 进行通信。 有关详细信息，请访问[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

监视集合：监视集合是生成更改源的数据。 对监视集合的任何插入和更改都会反映在集合的更改源中。 

租用集合：租用集合协调处理跨多个辅助角色的更改源。 单独集合用于存储租用，一个分区一个租用。 最好将此租用集合存储在不同的帐户（写入区域更靠近更改源处理器运行位置）。 租用对象有以下属性： 
* 所有者：指定拥有租用的主机
* 继续：为特殊分区指定更改源中的位置（继续标记）
* 时间戳：租用最近更新时间；时间戳可用于检查租用是否到期 

处理器主机：每个主机根据具有活动租用的主机其他实例数目，确定要处理的分区数目。 
1.  主机启动时，将获取租用，以在所有主机中均衡工作负荷。 主机定期续订租用，使租用保持活动状态。 
2.  主机为每次读取检查其租用的最近继续标记。 为确保并发安全，主机会检查 ETag 的每次租用更新。 此外还支持其他检查点策略。  
3.  关闭后，主机会释放所有租用，但保留继续信息，以便稍后从存储检查点继续读取。 

此时，主机数量不能大于分区（租用）数量。

使用者：使用者或辅助角色是执行每个主机启动的更改源处理的线程。 每个处理器主机可以有多个使用者。 每个使用者从分配给其的分区上读取更改源，并就更改和过期的租用通知主机。

若要进一步了解更改源处理器的四个元素是如何协同工作的，请看下图中的一个示例。 监视集合存储文档，并将“city”用作分区键。 可以看到蓝色分区包含“A - E”中的“city”字段的文档。 有两个主机，每个主机有两个从四个并行分区读取的使用者。 箭头显示的是从更改源中特定位置读取的使用者。 在第一个分区中，深蓝色表示更改源上未读取的更改，而浅蓝色表示更改源上已读取的更改。 主机使用租用集合来存储“继续”值，跟踪每个使用者的当前读取位置。 

![使用 Azure Cosmos DB 更改源处理器主机](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>使用更改源处理器库

安装更改源处理器 NuGet 包之前，请先安装： 

* Microsoft.Azure.DocumentDB 1.13.1 或更高版本 
* Newtonsoft.Json，9.0.1 或更高版本

然后安装 [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget 包](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)并将其包含为引用。

若要实现更改源处理器库，必须执行以下操作：

1. 实现 **DocumentFeedObserver** 对象，用于实现 **IChangeFeedObserver**。

2. 实现 **DocumentFeedObserverFactory** 对象，用于实现 **IChangeFeedObserverFactory**。

3. 在 **DocumentFeedObserverFacory** 的 **CreateObserver** 方法中，实例化在步骤 1 中创建的 **ChangeFeedObserver** 并将其返回。

    ```
    public IChangeFeedObserver CreateObserver()
    {
              DocumentFeedObserver newObserver = new DocumentFeedObserver(this.client, this.collectionInfo);
              return newObserver;
    }
    ```

4. 实例化 **DocumentObserverFactory**。

5. 实例化 **ChangeFeedEventHost**：

    ```csharp
    ChangeFeedEventHost host = new ChangeFeedEventHost(
                     hostName,
                     documentCollectionLocation,
                     leaseCollectionLocation,
                     feedOptions,
                     feedHostOptions);
    ```

6. 将 **DocumentFeedObserverFactory** 注册到主机。

步骤 4 到 6 的代码为： 

```
ChangeFeedOptions feedOptions = new ChangeFeedOptions();
feedOptions.StartFromBeginning = true;

ChangeFeedHostOptions feedHostOptions = new ChangeFeedHostOptions();
 
// Customizing lease renewal interval to 15 seconds.
// Can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay
feedHostOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);
 
using (DocumentClient destClient = new DocumentClient(destCollInfo.Uri, destCollInfo.MasterKey))
{
        DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory(destClient, destCollInfo);
        ChangeFeedEventHost host = new ChangeFeedEventHost(hostName, documentCollectionLocation, leaseCollectionLocation, feedOptions, feedHostOptions);
        await host.RegisterObserverFactoryAsync(docObserverFactory);
        await host.UnregisterObserversAsync();
}
```

就这么简单。 完成这几个步骤后，文档会开始传入 **DocumentFeedObserver ProcessChangesAsync** 方法。

## <a name="next-steps"></a>后续步骤

有关将 Azure Cosmos DB 与 Azure Functions 配合使用的详细信息，请参阅 [Azure Cosmos DB：使用 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)。

有关使用更改源处理器库的详细信息，请参阅以下资源：

* [信息页](documentdb-sdk-dotnet-changefeed.md) 
* [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [演示上述步骤 1-6 的示例代码](https://github.com/rsarosh/Cosmos-ChangeFeedProcessor)
* [GitHub 上的其他示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

有关通过 SDK 使用更改源的详细信息，请参阅以下资源：

* [SDK 信息页](documentdb-sdk-dotnet.md)
