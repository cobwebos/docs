---
title: Azure Cosmos DB：.NET 更改源处理器 API、SDK 和资源 | Microsoft Docs
description: 了解有关更改源处理器 API 和 SDK 的全部信息，包括发布日期、停用日期和 .NET 更改源处理器 SDK 各版本之间所做的更改。
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/21/2018
ms.author: maquaran
ms.openlocfilehash: a2770b9349dac8caa8e0611d77522ab56ca1bf07
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798858"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET 更改源处理器 SDK：下载和发行说明
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [异步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 文档**|[更改源处理器库 API 参考文档](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**入门**|[更改源处理器 .NET SDK 入门](change-feed.md)|
|**当前受支持的框架**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>发行说明

### <a name="stable-builds"></a>稳定版本

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* 添加了更多日志记录。
* 修复了多次调用待处理工作评估时出现的 DocumentClient 泄漏。

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* 修复了待处理工作评估。

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* 稳定性改进。
  * 修复了处理取消的任务问题，该问题可能导致某些分区上的观察者停止。
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

### <a name="pre-release-builds"></a>预发布版本

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
    * IParitionLoadBalancingStrategy - 用于对处理器实例之间的分区进行自定义负载均衡。
    * ILease、ILeaseManager - 用于自定义租用管理。
    * IPartitionProcessor - 用于对分区进行自定义处理更改。
* 日志记录 - 使用 [LibLog](https://github.com/damianh/LibLog) 库。
* 与 v1 API 100% 向后兼容。
* 新建代码库。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1 及更高版本。

## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。 

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.3.3](#1.3.3) |2018 年 5 月 8 日 |--- |
| [1.3.2](#1.3.2) |2018 年 4 月 18 日 |--- |
| [1.3.1](#1.3.1) |2018 年 3 月 13 日 |--- |
| [1.2.0](#1.2.0) |2017 年 10 月 31 日 |--- |
| [1.1.1](#1.1.1) |2017 年 8 月 29 日 |--- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |


## <a name="faq"></a>常见问题
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。 

