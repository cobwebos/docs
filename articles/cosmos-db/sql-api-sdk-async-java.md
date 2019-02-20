---
title: Azure Cosmos DB：SQL Async Java API、SDK 和资源
description: 了解有关 SQL Async Java API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 2/8/2019
ms.author: moderakh
ms.openlocfilehash: 88fcfec903dac9d939d658e5d06a51ab8ff1aba9
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979167"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>适用于 SQL API 的 Azure Cosmos DB Async Java SDK：发行说明和资源
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
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK 与 SQL API Java SDK 的区别在于，前者通过支持 [Netty 库](https://netty.io/)提供异步操作。 先存在的 [SQL API Java SDK](sql-api-sdk-java.md) 不支持异步操作。 

| |  |
|---|---|
| **SDK 下载** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API 文档** |[Java API 参考文档](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**参与 SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**入门** | [Async Java SDK 入门](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**代码示例** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **性能提示**| [GitHub 自述文件](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| 受支持的最小运行时|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>发行说明

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* 直接公开上市。
* 添加了对 QueryMetrics 的支持。
* 将接受 java.util.Collection（顺序很重要）的 API 更改为接受 java.util.List。
  现在 ConnectionPolicy#getPreferredLocations()、JsonSerialization 和 PartitionKey(.) 接受列表。

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* 添加了对“直接”的支持。
* 将接受 java.util.Collection（顺序很重要）的 API 更改为接受 java.util.List。
  现在 ConnectionPolicy#getPreferredLocations()、JsonSerialization 和 PartitionKey(.) 接受列表。
* 修复了网关模式下文档查询的会话 Bug。
* 升级了依赖项（netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79)、RxJava 1.3.8）。

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* 修复了处理非常大的查询响应时遇到的问题。
* 修复了实例化客户端时的资源令牌处理 ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78))。
* 升级了易受攻击的依赖项 jackson-databind ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77))。

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* 修复了资源泄漏 bug。
* 添加了对 MultiPolygon 的支持
* 在 RequestOptions 中添加了对自定义标头的支持。

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* 修复了打包 bug。

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* 修复了写入重试路径中的 NPE bug。
* 修复了终结点管理中的 NPE bug。
* 升级了易受攻击的依赖项 ([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68))。
* 添加了对 Netty 网络日志记录的支持，以便进行故障排除。

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* 添加了对多区域写入的支持。

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* 添加了对代理的支持。
* 添加了对资源令牌授权的支持。
* 修复了处理大分区键时的 bug ([GitHub #63](https://github.com/Azure/azure-cosmosdb-java/issues/63))。
* 改进了文档。
* SDK 重构为更细粒度的模块。

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* 修复了非英语区域设置的 bug ([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51))。
* 在冲突资源中添加了帮助器方法。

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* 由于性能原因和授权，由 jackson 替换了 org.json 依赖项 ([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29))。
* 删除了弃用的 OfferV2 类。
* 向 Offer 类添加了访问器方法以获取吞吐量内容。
* Document/Resource 中返回 org.json 类型的任何方法均已更改为返回 jackson 对象类型。
* 扩展了 JsonSerializable 的类的 getObject(.) 方法已更改为返回 jackson ObjectNode 类型。
* getCollection(.) 方法已更改为返回 ObjectNode 的集合。
* 使用 org.json.JSONObject arg 删除了 JsonSerializable 子类的构造函数。
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) 现在使用两个空格进行缩进。
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* 添加了对唯一索引策略的支持。
* 在源选项中添加了对限制响应继续标记大小的支持。
* 在跨分区查询中添加了对分区拆分的支持。
* 修复了 Json 时间戳序列化中的一个 bug ([GitHub #32](https://github.com/Azure/azure-cosmosdb-java/issues/32))。
* 修复了 Json 枚举序列化中的一个 bug。
* 修复了管理 2MB 大小文档时的一个 bug ([GitHub #33](https://github.com/Azure/azure-cosmosdb-java/issues/33))。
* 由于 bug ([jackson-databind: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599))，依赖项 com.fasterxml.jackson.core:jackson-databind 升级到了 2.9.5
* 由于 bug ([rxjava-extras: GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30))，rxjava-extras 的依赖项升级到了 0.8.0.17。
* pom 文件中的元数据说明更新为与文档的其余部分内联。
* 语法改进 ([GitHub #41](https://github.com/Azure/azure-cosmosdb-java/issues/41))、([GitHub #40](https://github.com/Azure/azure-cosmosdb-java/issues/40))。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* 在查询中添加了反压力支持。
* 在查询中添加了对分区键范围 ID 的支持。
* 修复以允许在请求标头中使用更大的继续标记 (bugfix GitHub #24)。
* Netty 依赖项升级到 4.1.22.Final 以确保 JVM 在主线程完成后关闭。
* 修复以避免在读取主资源时传递会话令牌。
* 添加了更多示例。
* 添加了更多基准测试方案。
* 修复了 Java 头文件以生成正确的 java 文档。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 正式版 SDK，在网关模式下使用 [Netty 库](https://netty.io/)为非阻塞 IO 提供端到端支持。 

## <a name="release-and-retirement-dates"></a>发布日期和停用日期
Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性、功能和优化仅添加到最新的 SDK 中。 因此建议你始终尽早升级到最新的 SDK 版本。

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [2.4.0](#2.4.0) |2019 年 2 月 8 日|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |2019 年 2 月 4 日|--- |
| [2.3.1](#2.3.1) |2019 年 1 月 15 日|--- |
| [2.3.0](#2.3.0) |2018 年 11 月 29 日|--- |
| [2.2.2](#2.2.2) |2018 年 11 月 8 日|--- |
| [2.2.1](#2.2.1) |2018 年 11 月 2日|--- |
| [2.2.0](#2.2.0) |2018 年 9 月 22 日|--- |
| [2.1.0](#2.1.0) |2018 年 9 月 5 日|--- |
| [2.0.1](#2.0.1) |2018 年 8 月 16 日|--- |
| [2.0.0](#2.0.0) |2018 年 6 月 20 日|--- |
| [1.0.2](#1.0.2) |2018 年 5 月 18日|--- |
| [1.0.1](#1.0.1) |2018 年 4 月 20 日|--- |
| [1.0.0](#1.0.0) |2018 年 2 月 27 日|--- |

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

