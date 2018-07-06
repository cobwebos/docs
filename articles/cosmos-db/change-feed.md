---
title: 使用 Azure Cosmos DB 中的更改源支持 | Microsoft Docs
description: 使用 Azure Cosmos DB 的更改源支持跟踪文档中发生的更改，执行基于事件的处理（例如触发器），使缓存和分析系统保持最新状态。
keywords: 更改源
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 6b0aaa075b8b2881e269d79a67e75528d0d9a86a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129852"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的更改源支持

[Azure Cosmos DB](../cosmos-db/introduction.md) 是快速灵活的全球复制数据库服务，非常适合用于 IoT、游戏、零售和操作日志记录应用程序。 这些应用程序中的一种常见设计模式是使用数据更改来发起附加的操作。 这些附加操作可能是下列其中一项： 

* 插入或修改文档时触发通知或 API 调用。
* 对 IoT 进行流式处理，或执行分析。
* 通过与缓存、搜索引擎或数据仓库同步，或者将数据存档到冷存储，进行附加的数据移动。

使用 Azure Cosmos DB 中的**更改源支持**，可针对每种模式构建高效、可缩放的解决方案，如下图所示：

![使用 Azure Cosmos DB 更改源促成实时分析和事件驱动的计算方案](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> 更改源支持仅提供给 Azure Cosmos DB 中的所有数据模型和容器。 但是，更改源是使用 SQL 客户端读取的，会将项序列化为 JSON 格式。 由于采用 JSON 格式，MongoDB 客户端会遇到 BSON 格式的文档与 JSON 格式的更改源不匹配的情况。

在下面的视频中，Azure Cosmos DB 计划经理 Andrew Liu 演示了 Azure Cosmos DB 更改源的工作原理。

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>更改源的工作原理

Azure Cosmos DB 中的更改源支持的工作原理是侦听 Azure Cosmos DB 集合中发生的任何更改。 然后，它会按照所更改文档的修改顺序输出这些文档的排序列表。 这些更改将会持久保留且能以异步和增量方式进行处理。可将输出分配到一个或多个使用者供并行处理。 

如本文稍后所述，可通过三种不同的方式读取更改源：

*   [使用 Azure Functions](#azure-functions)
*   [使用 Azure Cosmos DB SDK](#sql-sdk)
*   [使用 Azure Cosmos DB 更改源处理器库](#change-feed-processor)

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
* 可以使用 ChangeFeedOptions.StartTime 提供初始起点，例如，为了查找给定时钟时间所对应的继续标记。 ContinuationToken（如果指定）将优先于 StartTime 和 StartFromBeginning 值。 ChangeFeedOptions.StartTime 的精度是 ~5 秒。 

## <a name="use-cases-and-scenarios"></a>用例和方案

使用更改源可对具有大量写入操作的大型数据集进行有效处理，这样就不需要查询整个数据集来识别发生了哪些更改。 

例如，使用更改源可以有效地执行以下任务：

* 使用 Azure Cosmos DB 中存储的数据更新缓存、搜索索引或数据仓库。
* 实现应用程序级别的数据分层和存档，即，将“热数据”存储在 Azure Cosmos DB 中，将“冷数据”搁置在 [Azure Blob 存储](../storage/common/storage-introduction.md)或 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 中。
* 在不造成任何停机的情况下迁移到使用不同分区方案的另一个 Azure Cosmos DB 帐户。
* 使用 Azure Cosmos DB [在 Azure 上实现 lambda 管道](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)。 Azure Cosmos DB 提供一种可缩放的数据库解决方案，该解决方案可处理引入和查询，实现 TCO 较低的 lambda 体系结构。 
* 接收和存储设备、传感器、基础架构和应用程序发出的事件数据，并使用 [Azure 流分析](../stream-analytics/stream-analytics-documentdb-output.md)、[Apache Storm](../hdinsight/storm/apache-storm-overview.md) 或 [Apache Spark](../hdinsight/spark/apache-spark-overview.md) 实时处理这些事件。 

下图显示了可以使用 Azure Cosmos DB 执行引入和查询的 lambda 管道如何使用更改源支持： 

![用于引入和查询的基于 Azure Cosmos DB 的 lambda 管道](./media/change-feed/lambda.png)

另外，在[无服务器的](http://azure.com/serverless) Web 应用和移动应用中，可以跟踪各种事件（例如，对客户配置文件、首选项或位置的更改），以触发特定的操作，例如，使用 [Azure Functions](#azure-functions) 向客户的设备发送推送通知。 例如，若要使用 Azure Cosmos DB 构建游戏，可使用更改源，根据已完成的游戏的分数实时更新排行榜。

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>使用 Azure Functions 

如果使用 Azure Functions，连接到 Azure Cosmos DB 更改数据源的最简单方法是将一个 Azure Cosmos DB 触发器添加到 Azure Functions 应用。 在 Azure Functions 应用中创建 Azure Cosmos DB 触发器时，请选择要连接到的 Azure Cosmos DB 集合，以及每当对该集合做出更改时要触发的函数。 

可在 Azure Functions 门户、Azure Cosmos DB 门户中或以编程方式创建触发器。 有关详细信息，请参阅 [Azure Cosmos DB：使用 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)。

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>使用 SDK

Azure Cosmos DB 的 [SQL SDK](sql-api-sdk-dotnet.md) 提供用于读取和管理更改源的所有强大功能。 但是，强大的功能也附带了诸多的责任。 如果想要管理检查点、处理文档序列号，并想要精细控制分区键，则使用 SDK 可能是适当的方法。

本部分逐步讲解如何使用 SQL SDK 来处理更改源。

1. 首先，读取 appconfig 中的以下资源。 [更新连接字符串](create-sql-api-dotnet.md#update-your-connection-string)中提供了有关检索终结点和授权密钥的说明。

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

> [!NOTE]
> 可以使用 `ChangeFeedOptions.PartitionKey`（而不是 `ChangeFeedOptions.PartitionKeyRangeId`）指定要为其获取更改源的单个分区键。 例如，`PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`。
> 
>

如果有多个读取者，可以使用 **ChangeFeedOptions** 将读取负载分配到不同的线程或不同的客户端。

只需编写这几行代码，即可开始读取更改源。 可从 [GitHub 存储库](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed)获取本文中使用的完整代码。

在上面步骤 4 所示的代码中，最后一行中的 **ResponseContinuation** 包含文档的最后一个逻辑序列号 (LSN)，下一次读取此序列号后面的新文档时，将要使用此序列号。 使用 **ChangeFeedOption** 的 **StartTime**，可以拓宽文档的检索覆盖面。 因此，如果 **ResponseContinuation** 为 null，但 **StartTime** 是过去的某个时间，则会获得从 **StartTime** 开始更改的所有文档。 但是，如果 **ResponseContinuation** 使用了某个值，则系统会获取从该 LSN 开始的所有文档。

因此，检查点数组只会保留每个分区的 LSN。 但如果不想要处理分区、检查点、LSN、开始时间等等，更简单的做法是使用更改源处理器库。

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>使用更改源处理器库 

借助 [Azure Cosmos DB 更改源处理器库](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed)，可以轻松地在多个使用者之间分配事件处理负载。 此库简化了跨分区的以及并行工作的多个线程中的更改的读取。

更改源处理器库的主要优势在于，无需管理每个分区和继续标记，也无需手动轮询每个集合。

更改源处理器库简化了跨分区的以及并行工作的多个线程中的更改的读取。  它使用租约机制自动管理跨分区的更改读取。 如下图中所示，如果启动两个使用更改源处理器库的客户端，它们会在彼此之间划分工作。 如果不断增加客户端，它们仍会在彼此之间划分工作。

![Azure Cosmos DB 更改源的分布式处理](./media/change-feed/change-feed-output.png)

左侧的客户端先启动并开始监视所有分区，然后第二个客户端启动，随后，第一个客户端让某些租约转移到第二个客户端。 可以看出，这是在不同的计算机和客户端之间分配工作的极佳方法。

请注意，如果有两个无服务器 Azure 函数正在监视同一个集合并使用相同的租约，则根据处理器库决定处理分区的方式，这两个函数可能会收到不同的文档。

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>了解更改源处理器库

实现更改源处理器库需要四个主要组件：监视集合、租用集合、处理器主机和使用者。 

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

* Microsoft.Azure.DocumentDB 最新版本。
* Newtonsoft.Json 最新版本

然后安装 [Microsoft.Azure.DocumentDB.ChangeFeedProcessor Nuget 包](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)并将其包含为引用。

若要实现更改源处理器库，必须执行以下操作：

1. 实现 **DocumentFeedObserver** 对象，用于实现 **IChangeFeedObserver**。
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. 实现 **DocumentFeedObserverFactory** 对象，用于实现 **IChangeFeedObserverFactory**。
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. 定义 CancellationTokenSource 和 ChangeFeedProcessorBuilder

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. 在定义相关对象后生成 ChangeFeedProcessorBuilder 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>是否可以使用 Java 读取更改源？

[Github 存储库](https://github.com/Azure/azure-documentdb-changefeedprocessor-java)中提供了用于读取更改源的 Java 库。 但是，基于 .NET 库的 Java 库版本目前很少。 不久后，这两个库将会同步。

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>是否可对响应中获取的内部簿记使用 _etag、_lsn 或 _ts？

_etag 属于内部格式，请不要依赖它（不要分析它），因为它随时可能更改。
_ts 是修改或创建时间戳。 可以使用 _ts 进行时间顺序比较。
_lsn 是仅为更改源添加的批 ID，它表示存储中的事务 ID。 许多文档可能具有相同的 _lsn。
另请注意，FeedResponse 中的 ETag 不同于文档中显示的 _etag。 _etag 是用于实现并发性的内部标识符，它告知文档的版本，而 ETag 用于将源定序。

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>读取更改源是否会提高成本？

需要支付消耗的 RU 费用，也就是说，将数据移入和移出 Azure Cosmos DB 集合始终会消耗 RU。 用户将根据租用集合支付消耗的 RU 费用。

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>多个 Azure Functions 是否可以读取一个集合的更改源？

是的。 多个 Azure Functions 可以读取同一集合的更改源。 但是，需要为 Azure Functions 定义不同的 leaseCollectionPrefix。

### <a name="should-the-lease-collection-be-partitioned"></a>是否应将租用集合分区？

不需要，租用集合可以固定。 不需要分区的租用集合，而且目前不支持。

### <a name="can-i-read-change-feed-from-spark"></a>是否可以从 Spark 读取更改源？

可以。 请参阅 [Azure Cosmos DB Spark 连接器](spark-connector.md)。 以[幻灯片](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s)演示了如何将更改源作为结构化流进行处理。

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>如果我使用 Azure Functions 处理更改源（例如，包含 10 个文档的批），在处理第 7 个文档时遇到错误， 在这种情况下，最后 3 个文档不会得到处理。如何在下一个源中从失败的文档 （即第 7 个文档）开始处理？

若要处理错误，建议的模式是使用 try-catch 块包装代码。 捕获错误，将该文档放入队列（死信），然后定义逻辑来处理已生成错误的文档。 如果批中的文档数有 200 个，并且只有一个文档失败，则使用此方法就无需丢弃整个批。

如果出错，则不应将检查点回退到开始位置，否则可以从更改源获取这些文档。 请记住，更改源保留文档的最后一个最终快照，因此，可能会丢失文档中的前一个快照。 更改源只保留文档的最后一个版本，在不同的版本之间，其他进程可能会更改文档。

在不断修复代码的过程中，你很快就会发现，死信队列中没有任何文档。
Azure Functions 由更改源系统自动调用，检查点等内容由 Azure 函数在内部维护。 若要回滚检查点并控制其各个方面，应考虑使用更改源处理器 SDK。


## <a name="next-steps"></a>后续步骤

有关将 Azure Cosmos DB 与 Azure Functions 配合使用的详细信息，请参阅 [Azure Cosmos DB：使用 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)。

有关使用更改源处理器库的详细信息，请参阅以下资源：

* [信息页](sql-api-sdk-dotnet-changefeed.md) 
* [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [演示上述步骤 1-6 的示例代码](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [GitHub 上的其他示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

有关通过 SDK 使用更改源的详细信息，请参阅以下资源：

* [SDK 信息页](sql-api-sdk-dotnet.md)
