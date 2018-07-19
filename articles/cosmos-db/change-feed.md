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
ms.openlocfilehash: e53f1e62b9265d2eec2f49537cc05c865e1436f3
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902956"
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

就这么简单。 完成这几个步骤后，文档会开始显示在 **DocumentFeedObserver ProcessChangesAsync** 方法中。

上述代码用于说明目的，以显示不同类型的对象及其交互。 必须定义适当的变量并使用正确的值启动它们。 可从 [GitHub 存储库](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessorV2)获取本文中使用的完整代码。

> [!NOTE]
> 不应在代码或配置文件中包含主密钥，如上述代码所示。 请参阅[如何使用 Key Vault 检索密钥](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/)。


## <a name="faq"></a>常见问题解答

### <a name="what-are-the-different-ways-you-can-read-change-feed-and-when-to-use-each-method"></a>可通过哪些不同的方法读取更改源？何时使用哪种方法？

可通过三个选项读取更改源：

* **[使用 Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   使用此方法可对更改源进行低级控制。 可以管理检查点、访问特定的分区键，等等。如果有多个读取者，可以使用 [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) 将读取负载分配到不同的线程或不同的客户端。 。

* **[使用 Azure Cosmos DB 更改源处理器库](#change-feed-processor)**

   若要大量转移更改源的复杂性，可以使用更改源处理器库。 此库可以大幅消除复杂性，同时仍让你保持更改源的完全控制度。 此库遵循[观察程序模式](https://en.wikipedia.org/wiki/Observer_pattern)，处理函数由 SDK 调用。 

   如果有高吞吐量的更改源，可以实例化多个客户端来读取更改源。 由于使用的是“更改源处理器库”，它会自动在不同的客户端之间分配负载。 你不需要执行任何操作。 SDK 会解决所有的复杂性。 但是，若要使用自己的负载均衡器，可以针对自定义的分区策略实现 IParitionLoadBalancingStrategy。 实现 IPartitionProcessor - 用于对分区进行自定义处理更改。 使用 SDK 可以处理分区范围，但是，若要处理特定的分区键，则必须使用 SDK for SQL API。

* **[使用 Azure Functions](#azure-functions)** 
   
   Azure 函数是最后一个选项，也是最简单的选项。 我们建议使用此选项。 在 Azure Functions 应用中创建 Azure Cosmos DB 触发器时，请选择要连接到的 Azure Cosmos DB 集合，以及每当对该集合做出更改时要触发的函数。 观看使用 Azure 函数和更改源的[幻灯片](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s)

   可在 Azure Functions 门户、Azure Cosmos DB 门户中或以编程方式创建触发器。 Visual Studio 和 VS Code 很好地支持编写 Azure 函数。 可以在桌面上编写和调试代码，然后单击一下鼠标部署函数。 有关详细信息，请参阅 [Azure Cosmos DB：使用 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)一文。

### <a name="what-is-the-sort-order-of-documents-in-change-feed"></a>更改源中文档的排序顺序是什么？

更改源文档按其修改时间排序。 只能按分区保证这种排序顺序。

### <a name="for-a-multi-region-account-what-happens-to-the-change-feed-when-the-write-region-fails-over-does-the-change-feed-also-failover-would-the-change-feed-still-appear-contiguous-or-would-the-fail-over-cause-change-feed-to-reset"></a>对于多区域帐户，写入区域故障转移时更改源会发生什么情况？ 更改源是否也故障转移？ 更改源是否仍显示为连续状态，或者，故障转移是否导致更改源重置？

是的，每次执行手动故障转移操作时，更改源会正常工作，并且是连续的。

### <a name="how-long-change-feed-persist-the-changed-data-if-i-set-the-ttl-time-to-live-property-for-the-document-to--1"></a>如果将文档的 TTL（生存时间）属性设置为 -1，更改源会将更改的数据保留多久？

更改源会永久保留。 如果数据未被删除，它会保留在更改源中。

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region-as-change-feed-is-available-in-all-the-read-regions-by-default"></a>如何将 Azure Functions 配置为从特定的区域读取数据，因为更改源默认已在所有读取区域提供？

目前无法将 Azure Functions 配置为从特定的区域读取数据。 设置任何 Azure Cosmos DB 绑定和触发器的首选区域时，Azure Functions 存储库中会出现一个 GitHub 问题。

Azure Functions 使用默认连接策略。 可以在 Azure Functions 中配置连接模式，默认情况下，Azure Functions 从写入区域读取数据，因此，最好是将 Azure Functions 共置在同一区域。

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Azure Functions 中的默认批大小是什么？

每次调用 Azure Functions 时会读取 100 个文档。 但是，可在 function.json 文件中配置此数字。 下面是完整的[配置选项列表](../azure-functions/functions-run-local.md)。 如果在本地进行开发，请更新 [local.settings.json](../azure-functions/functions-run-local.md) 文件中的应用程序设置。

### <a name="i-am-monitoring-a-collection-and-reading-its-change-feed-however-i-see-i-am-not-getting-all-the-inserted-document-some-documents-are-missing-what-is-going-on-here"></a>我正在监视一个集合并读取其更改源，但发现无法获取所有插入的文档，某些文档已缺失。 这是怎么回事？

请确保没有其他函数正在读取具有相同租用集合的相同集合。 我也遇到过这种情况，后来我认识到，缺少的文档已由另一个 Azure 函数处理，该函数也使用了相同的租约。

因此，如果创建多个 Azure 函数来读取相同的更改源，则这些函数必须使用不同的租用集合，或使用“leasePrefix”配置来共享相同的集合。 但是，如果使用更改源处理器库，则可以启动函数的多个实例，SDK 会自动在不同的实例之间分割文档。

### <a name="my-document-is-updated-every-second-and-i-am-not-getting-all-the-changes-in-azure-functions-listening-to-change-feed"></a>我的文档每隔一秒更新一次，但无法在侦听更改源的 Azure Functions 中获取所有更改。

Azure Functions 每隔 5 秒轮询更改源一次，因此，在 5 秒间隔内发生的任何更改都会丢失。 Azure Cosmos DB 只存储 5 秒间隔的一个版本，因此，你会获取文档的第 5 次更改。 但是，若要使用 5 秒以下的频率，每隔一秒轮询更改源，则可以配置轮询时间“feedPollTime”，具体请参阅 [Azure Cosmos DB 绑定](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration)。 该时间以毫秒定义，默认值为 5000。 可以使用小于 1 秒的时间，但不建议使用，否则会消耗更多的 CPU。

### <a name="i-inserted-a-document-in-the-mongo-api-collection-but-when-i-get-the-document-in-change-feed-it-shows-a-different-id-value-what-is-wrong-here"></a>我在 Mongo API 集合中插入了一个文档，但在更改源中获取该文档时，显示了一个不同的 ID 值。 原因是什么？

你的集合是 Mongo API 集合。 请记住，更改源是使用 SQL 客户端读取的，会将项序列化为 JSON 格式。 由于采用 JSON 格式，MongoDB 客户端会遇到 BSON 格式的文档与 JSON 格式的更改源不匹配的情况。 显示的是 BSON 文档的 JSON 表示形式。 如果在 Mongo 帐户中使用二进制属性，它们会转换为 JSON。

### <a name="is-there-a-way-to-control-change-feed-for-updates-only-and-not-inserts"></a>是否可以通过某种方式来只控制更改源的更新，而不控制插入？

暂时不可以，但此功能已在规划中。 目前，可以在文档中添加更新软标记。

### <a name="is-there-a-way-to-get-deletes-in-change-feed"></a>是否可以通过某种方式来获取更改源的删除？

目前更改源不会记录日志删除操作。 更改源在不断改进，此功能已在规划中。 目前，可以在文档中添加删除软标记。 在文档中添加名为“deleted”的属性并将其设置为“true”，并在文档中设置 TTL，以便自动删除文档。

### <a name="can-i-read-change-feed-for-historic-documentsfor-example-documents-that-were-added-5-years-back-"></a>是否可以读取历史文档的更改源（例如，5 年前添加的文档）？

是的，如果未删除该文档，则可以读取不超过集合原始时间的更改源。

### <a name="can-i-read-change-feed-using-javascript"></a>是否可以使用 JavaScript 读取更改源？

是的，最近已添加对更改源的 Node.js SDK 初始支持。 可按以下示例中所示使用 JavaScript。在运行代码之前，请将 documentdb 模块更新到新版本：

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
