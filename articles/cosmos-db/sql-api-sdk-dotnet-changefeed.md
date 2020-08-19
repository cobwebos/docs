---
title: Azure Cosmos DB .NET 更改源处理器 API、SDK 发行说明
description: 了解有关更改源处理器 API 和 SDK 的所有信息，包括发布日期、停用日期和 .NET 更改源处理器 SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 6d25eb2965e31211c0d30ec8d5e3e376176147c0
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590041"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET 更改源处理器 SDK：下载和发行说明

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [弹簧数据 v2](sql-api-sdk-java-spring-v2.md)
> * [弹簧数据 v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 连接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST] (/rest/api
> * [REST 资源提供程序] (/rest/api
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 文档**|[更改源处理器库 API 参考文档](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**入门**|[更改源处理器 .NET SDK 入门](change-feed.md)|
|**当前受支持的框架**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> 如果使用的是更改源处理器，请参阅 [.NET SDK](change-feed-processor.md) 的最新版本 3.x，其中包含内置于 SDK 中的更改源。 

## <a name="release-notes"></a>发行说明

### <a name="v2-builds"></a>v2 版本

### <a name="232"></a><a name="2.3.2"/>2.3.2
* 添加了与 [V3 SDK](sql-api-sdk-dotnet-standard.md) 的租赁存储兼容性，可实现热迁移路径。 应用程序可以迁移到 V3 SDK 并迁移回更改源处理器库，而不会丢失任何状态。

### <a name="231"></a><a name="2.3.1"/>2.3.1
* `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` `FeedProcessing.IChangeFeedObserver.CloseAsync` 如果找不到分区或者目标副本与读取会话不是最新的，则在将关闭原因发送到时更正了此情况。 在这些情况下 `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` ， `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` 现在使用了关闭原因。
* 添加了一个新的关闭原因 `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` ，当目标副本不是最新的读取会话时，将发送此关闭原因以关闭更改源观察程序。

### <a name="230"></a><a name="2.3.0"/>2.3.0
* 添加了新方法 `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` 和相应的公共接口 `ICheckpointPartitionProcessorFactory`。 这样可以实现 `IPartitionProcessor` 接口来使用内置检查点机制。 新工厂类似于现有 `IPartitionProcessorFactory`，只不过其 `Create` 方法也采用 `ILeaseCheckpointer` 参数。
* 只有 `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` 或 `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` 方法中的一个可用于同一个 `ChangeFeedProcessorBuilder` 实例。

### <a name="228"></a><a name="2.2.8"></a>2.2.8
* 稳定性和诊断改进：
  * 添加了对长时间检测读取更改源的支持。 所用时间比 `ChangeFeedProcessorOptions.ChangeFeedTimeout` 属性指定的值更长时，将执行以下步骤：
    * 将中止在有问题的分区上读取更改源的操作。
    * 更改源处理器实例将删除有问题的租约的所有权。 将在由相同或不同的更改源处理器实例完成的下次租约获取步骤期间选取已删除的租约。 这样便可以重新开始读取更改源。
    * 向运行状况监视器报告问题。 默认运行状况监视器将报告的所有问题发送到跟踪日志。
  * 添加了新的公共属性：`ChangeFeedProcessorOptions.ChangeFeedTimeout`。 此属性的默认值为 10 分钟。
  * 添加了新的公共枚举值：`Monitoring.MonitoredOperation.ReadChangeFeed`。 当 `HealthMonitoringRecord.Operation` 的值设置为 `Monitoring.MonitoredOperation.ReadChangeFeed` 时，表示运行状况问题与读取更改源相关。

### <a name="227"></a><a name="2.2.7"></a>2.2.7
* 获取所有租约所用时间超过租约过期间隔时（例如，由于网络问题），改进了方案的负载均衡策略：
  * 在此方案中，负载均衡算法过去常常将租约错误地视为过期，导致从活动所有者盗取租约。 这可能会导致对大量租约重新进行不必要的负载均衡。
  * 在此版本中已修复此问题，方法是避免在获取所有者未更改的过期租约发生冲突时进行重试，并将获取过期租约推迟到下次负载均衡迭代。

### <a name="226"></a><a name="2.2.6"></a>2.2.6
* 改进了对观察者异常的处理。
* 有关观察者错误的更丰富信息：
  * 当观察者由于观察者的 ProcessChangesAsync 抛出异常而关闭时，CloseAsync 现在将接收设置为 ChangeFeedObserverCloseReason.ObserverError 的 reason 参数。
  * 添加了跟踪以识别观察者的用户代码中的错误。

### <a name="225"></a><a name="2.2.5"></a>2.2.5
* 添加了对使用共享数据库吞吐量的拆分集合的处理支持。
  * 此版本修复了使用共享数据库吞吐量的拆分集合可能发生的问题，即在拆分导致分区重新平衡时仅创建一个子分区键范围，而不是两个。 发生这种情况时，更改源处理器可能会在删除旧分区键范围的租约时卡住，而无法创建新租约。 此版本中已修复了此问题。

### <a name="224"></a><a name="2.2.4"></a>2.2.4
* 添加了新属性 ChangeFeedProcessorOptions.StartContinuation 来支持从请求继续标记开始更改源。 只有当租约集合为空或者租约未设置 ContinuationToken 时才使用此属性。 对于租约集合中设置了 ContinuationToken 的租约，将使用 ContinuationToken 并忽略 ChangeFeedProcessorOptions.StartContinuation。

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* 增加了对使用自定义存储的支持，用以按分区持久保存继续标记。
  * 例如，自定义租约存储可以是以任何自定义方式分区的 Azure Cosmos DB 租约集合。
  * 自定义租约存储可以使用新的扩展点 ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) 和 ILeaseStoreManager 公共接口。
  * 将 ILeaseManager 接口重构到了多个角色接口中。
* 次要重大更改：删除了扩展点 ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager)，请改用 ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager)。

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* 此版本修复了在处理受监视集合中的拆分和使用已分区租约集合期间发生的一个问题。 在处理拆分分区的租约时，可能不会删除对应于该分区的租约。 此版本中已修复了此问题。

### <a name="221"></a><a name="2.2.1"></a>2.2.1
* 多主机帐户和新的会话令牌格式的固定估计器计算。

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* 添加了对已分区租用集合的支持。 分区键必须定义为 /id。
* 次要重大更改：IChangeFeedDocumentClient 接口和 ChangeFeedDocumentClient 类的方法都已更改为包括 RequestOptions 和 CancellationToken 参数。 IChangeFeedDocumentClient 是一个高级的扩展点，通过它可以提供文档客户端自定义实现以与更改源处理器结合使用，例如修饰 DocumentClient 并截获对它的所有调用以进行额外跟踪和错误处理等等。利用此更新，实现 IChangeFeedDocumentClient 的代码将需要更改为在实现中包含新参数。
* 次要诊断改进。

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* 添加了新的 API，Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync()。 此 API 可用于获取每个分区估算的工作量。
* 支持 Microsoft.Azure.DocumentDB SDK 2.0。 需要 Microsoft.Azure.DocumentDB 2.0 或更高版本。

### <a name="206"></a><a name="2.0.6"></a>2.0.6
* 添加了 ChangeFeedEventHost.HostName 公共属性以与 v1 兼容。

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* 修复了在分区拆分期间发生的争用条件。 争用条件可能导致获得租用后，在分区拆分期间立即失去该租用，从而引起争用。 此版本修复了争用条件问题。

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"></a>2.0.3 预发布
* 修复了以下问题：
  * 拆分分区时，可能会重复处理拆分前修改的文档。
  * 如果租用集合中没有租用，GetEstimatedRemainingWork API 返回的是 0。

* 以下异常已公开。 实现 IPartitionProcessor 的扩展可能会抛出这些异常。
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"></a>2.0.2-prerelease
* 小的 API 更改：
  * 删除了标记为过时的 ChangeFeedProcessorOptions.IsAutoCheckpointEnabled。

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"></a>2.0.1-prerelease
* 稳定性改进：
  * 更好地处理租用存储初始化。 当租用存储为空时，只有一个处理器实例可以对其进行初始化，其他处理器实例将等待。
  * 更稳定/有效租用续订/发行版。 续订和释放一个分区的租用独立于续订其他租用。 在 v1 中，这是针对所有分区按顺序完成的。
* 新 v2 API：
  * 处理器的灵活构造生成器模式：ChangeFeedProcessorBuilder 类。
    * 可以采用参数的任何组合。
    * 可以采用用于监视的 DocumentClient 实例和/或租用集合（v1 中不可用）。
  * IChangeFeedObserver.ProcessChangesAsync 现在采用 CancellationToken。
  * IRemainingWorkEstimator - 剩余工作估计器可以与处理器分开使用。
  * 新的扩展点：
    * IPartitionLoadBalancingStrategy - 用于对处理器实例之间的分区进行自定义负载均衡。
    * ILease、ILeaseManager - 用于自定义租用管理。
    * IPartitionProcessor - 用于对分区进行自定义处理更改。
* 日志记录 - 使用 [LibLog](https://github.com/damianh/LibLog) 库。
* 与 v1 API 100% 向后兼容。
* 新建代码库。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1 及更高版本。

### <a name="v1-builds"></a>v1 版本

### <a name="133"></a><a name="1.3.3"></a>1.3.3
* 添加了更多日志记录。
* 修复了多次调用待处理工作评估时出现的 DocumentClient 泄漏。

### <a name="132"></a><a name="1.3.2"></a>1.3.2
* 修复了待处理工作评估。

### <a name="131"></a><a name="1.3.1"></a>1.3.1
* 稳定性改进。
  * 修复了处理取消的任务问题，该问题可能导致某些分区上的观察者停止。
* 支持手动检查点。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 及更高版本。

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* 增加了对 .NET Standard 2.0 的支持。 程序包现在支持 `netstandard2.0` 和 `net451` Framework 名字对象。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 及更高版本。
* 兼容 [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 及更高版本。

### <a name="111"></a><a name="1.1.1"></a>1.1.1
* 修复了更改源为空或无任何工作挂起时计算剩余工作估计值时的一个问题。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 及更高版本。

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* 添加了一个获取更改源中剩余待处理工作估计值的方法。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 及更高版本。

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 及更低版本。

## <a name="release--retirement-dates"></a>发布和停用日期

Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。 新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。

> [!WARNING]
> 在 2022 年 8 月 31 日之后，Azure Cosmos DB 将不再进行 bug 修复，不再添加新功能，也不再支持 1.x 版的 Azure Cosmos DB .NET 或 .NET Core SDK for SQL API。 如果你不想升级，则从 1.x 版 SDK 发送的请求将继续由 Azure Cosmos DB 服务处理。

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [2.3.2](#2.3.2) |2020 年 8 月 11 日 |--- |
| [2.3.1](#2.3.1) |2020 年 7 月 30 日 |--- |
| [2.3.0](#2.3.0) |2020 年 4 月 2 日 |--- |
| [2.2.8](#2.2.8) |2019 年 10 月 28 日 |--- |
| [2.2.7](#2.2.7) |2019 年 5 月 14 日 |--- |
| [2.2.6](#2.2.6) |2019 年 1 月 29 日 |--- |
| [2.2.5](#2.2.5) |2018 年 12 月 13 日 |--- |
| [2.2.4](#2.2.4) |2018 年 11 月 29 日 |--- |
| [2.2.3](#2.2.3) |2018 年 11 月 19 日 |--- |
| [2.2.2](#2.2.2) |2018 年 10 月 31 日 |--- |
| [2.2.1](#2.2.1) |2018 年 10 月 24 日 |--- |
| [1.3.3](#1.3.3) |2018 年 5 月 8 日 |--- |
| [1.3.2](#1.3.2) |2018 年 4 月 18 日 |--- |
| [1.3.1](#1.3.1) |2018 年 3 月 13 日 |--- |
| [1.2.0](#1.2.0) |2017 年 10 月 31 日 |--- |
| [1.1.1](#1.1.1) |2017 年 8 月 29 日 |--- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |

## <a name="faq"></a>常见问题解答

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅

若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。
