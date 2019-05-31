---
title: Azure Cosmos DB：.NET 更改源处理器 API、SDK 和资源
description: 了解有关更改源处理器 API 和 SDK 的全部信息，包括发布日期、停用日期和 .NET 更改源处理器 SDK 各版本之间所做的更改。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 2a4d636ccb03e36f7c495f3c10c90033d7c3c93c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417915"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET 更改源处理器 SDK：下载和发行说明

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [异步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 文档**|[更改源处理器库 API 参考文档](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**入门**|[更改源处理器 .NET SDK 入门](change-feed.md)|
|**当前受支持的框架**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>发行说明

### <a name="v2-builds"></a>v2 版本

### <a name="a-name227227"></a><a name="2.2.7"/>2.2.7
* 改进的负载平衡方案的策略，获取所有租约时花费的时间超过租约过期的时间间隔，例如，由于网络问题：
  * 在此方案中的负载平衡算法用于为已过期，错误地考虑租约活动所有者从导致窃取租约。 这可能会触发重新均衡的租用的很多不必要。
  * 通过获取已过期的租约的所有者未发生更改以及 posponing 获取过期租约到下一步的负载平衡迭代时避免冲突时重试情况下，在此版本中修复此问题。

### <a name="a-name226226"></a><a name="2.2.6"/>2.2.6
* 改进了对观察者异常的处理。
* 有关观察者错误的更丰富信息：
  * 当观察者由于观察者的 ProcessChangesAsync 抛出异常而关闭时，CloseAsync 现在将接收设置为 ChangeFeedObserverCloseReason.ObserverError 的 reason 参数。
  * 添加了跟踪以识别观察者的用户代码中的错误。

### <a name="a-name225225"></a><a name="2.2.5"/>2.2.5
* 添加了对使用共享数据库吞吐量的拆分集合的处理支持。
  * 此版本修复了使用共享数据库吞吐量的拆分集合可能发生的问题，即在拆分导致分区重新平衡时仅创建一个子分区键范围，而不是两个。 发生这种情况时，更改源处理器可能会在删除旧分区键范围的租约时卡住，而无法创建新租约。 此版本中已修复了此问题。

### <a name="a-name224224"></a><a name="2.2.4"/>2.2.4
* 添加了新属性 ChangeFeedProcessorOptions.StartContinuation 来支持从请求继续标记开始更改源。 只有当租约集合为空或者租约未设置 ContinuationToken 时才使用此属性。 对于租约集合中设置了 ContinuationToken 的租约，将使用 ContinuationToken 并忽略 ChangeFeedProcessorOptions.StartContinuation。

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* 增加了对使用自定义存储的支持，用以按分区持久保存继续标记。
  * 例如，自定义租约存储可以是以任何自定义方式分区的 Azure Cosmos DB 租约集合。
  * 自定义租约存储可以使用新的扩展点 ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) 和 ILeaseStoreManager 公共接口。
  * 将 ILeaseManager 接口重构到了多个角色接口中。
* 次要重大更改：删除了扩展点 ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager)，请改用 ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager)。

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* 此版本修复了在处理受监视集合中的拆分和使用已分区租约集合期间发生的一个问题。 在处理拆分分区的租约时，可能不会删除对应于该分区的租约。 此版本中已修复了此问题。

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* 多主机帐户和新的会话令牌格式的固定估计器计算。

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* 添加了对已分区租用集合的支持。 分区键必须定义为 /id。
* 次要重大更改：IChangeFeedDocumentClient 接口和 ChangeFeedDocumentClient 类的方法都已更改为包括 RequestOptions 和 CancellationToken 参数。 IChangeFeedDocumentClient 是一个高级的扩展点，通过它可以提供文档客户端自定义实现以与更改源处理器结合使用，例如修饰 DocumentClient 并截获对它的所有调用以进行额外跟踪和错误处理等等。利用此更新，实现 IChangeFeedDocumentClient 的代码将需要更改为在实现中包含新参数。
* 次要诊断改进。

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* 添加了新的 API，Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync()。 此 API 可用于获取每个分区估算的工作量。
* 支持 Microsoft.Azure.DocumentDB SDK 2.0。 需要 Microsoft.Azure.DocumentDB 2.0 或更高版本。

### <a name="a-name206206"></a><a name="2.0.6"/>2.0.6
* 添加了 ChangeFeedEventHost.HostName 公共属性以与 v1 兼容。

### <a name="a-name205205"></a><a name="2.0.5"/>2.0.5
* 修复了在分区拆分期间发生的争用条件。 争用条件可能导致获得租用后，在分区拆分期间立即失去该租用，从而引起争用。 此版本修复了争用条件问题。

### <a name="a-name204204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="a-name203-prerelease203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3 预发布
* 修复了以下问题：
  * 拆分分区时，可能会重复处理拆分前修改的文档。
  * 如果租用集合中没有租用，GetEstimatedRemainingWork API 返回的是 0。

* 以下异常已公开。 实现 IPartitionProcessor 的扩展可能会抛出这些异常。
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* 小的 API 更改：
  * 删除了标记为过时的 ChangeFeedProcessorOptions.IsAutoCheckpointEnabled。

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
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

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* 添加了更多日志记录。
* 修复了多次调用待处理工作评估时出现的 DocumentClient 泄漏。

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* 修复了待处理工作评估。

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* 稳定性改进。
  * 用于处理已取消的任务可能会导致的问题的修复某些分区上停止观察程序。
* 支持手动检查点。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 及更高版本。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 增加了对 .NET Standard 2.0 的支持。 程序包现在支持 `netstandard2.0` 和 `net451` Framework 名字对象。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 及更高版本。
* 兼容 [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 及更高版本。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 修复了更改源为空或无任何工作挂起时计算剩余工作估计值时的一个问题。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 及更高版本。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 添加了一个获取更改源中剩余待处理工作估计值的方法。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 及更高版本。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 及更低版本。

## <a name="release--retirement-dates"></a>发布和停用日期

Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。 

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

<br/>

| Version | 发布日期 | 停用日期 |
| --- | --- | --- |
| [2.2.7](#2.2.7) |2019 年 5 月 14 日， |--- |
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
