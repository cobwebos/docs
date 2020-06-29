---
title: Azure Cosmos DB：SQL Java API、SDK 和资源
description: 了解有关 SQL Java API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB SQL Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 53ae3217e77a9301cbe0d3d63ce0712a0b9b7f1e
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171610"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>适用于 SQL API 的 Azure Cosmos DB Java SDK：发行说明和资源
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

这是适用于 SQL API 的原始 Azure Cosmos DB 同步 Java SDK v2，支持同步操作。

> [!IMPORTANT]  
> 这不是最新的 Azure Cosmos DB Java SDK！ 请考虑将 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) 用于项目。 若要升级，请按照[迁移到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南和 [Reactor 与 RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 指南中的说明进行操作。 
>

| |  |
|---|---|
|**SDK 下载**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API 文档**|[Java API 参考文档](/java/api/com.microsoft.azure.documentdb)|
|**参与 SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**入门**|[Java SDK 入门](sql-api-java-get-started.md)|
|**Web 应用教程**|[使用 Azure Cosmos DB 进行 Web 应用程序开发](sql-api-java-application.md)|
|受支持的最小运行时|[Java 开发工具包 (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>发行说明

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* 修复了连接池超时问题。
* 修复了内部重试时的身份验证令牌刷新。

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* 更新了 databaseAccount 上正确的客户端副本策略标记，并从缓存中读取 databaseAccount 配置。

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* 如果用户提供 pkRangeId，则避免重试无效的分区键范围错误。

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* 优化了分区键范围缓存刷新。
* 修复了以下问题：SDK 不接受来自服务器的分区拆分提示，导致客户端路由缓存刷新不正确。

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* 优化了集合缓存刷新。

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* 添加了对从请求诊断字符串检索内部异常消息的支持。

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* 在 PartitionKeyDefinition 上引入版本 api。

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* 为直接模式添加了单独的超时支持。

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* 使用服务的 Null 错误消息并生成文档客户端异常。

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* 套接字连接改进，添加 SoKeepAlive 默认值 true。

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* 添加了请求诊断字符串支持。

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* 修复了适用于哈希 V2 的 PartitionKey 中的 bug。

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* 添加了对组合索引的支持。
* 修复了全局终结点管理器中的 bug 以强制刷新。
* 修复了在直接模式下使用前提条件更新插入的 bug。

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* 修复了网关地址缓存中的 bug。

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* 为直接模式添加了多区域写入支持。
* 添加了对处理从代理作为 ServiceUnavailable 异常引发的 IOExceptions 的支持。
* 修复了终结点发现重试策略中的 bug。
* 修复了 bug，以确保 BaseDatabaseAccountConfigurationProvider 不会引发空指针异常。
* 修复了 bug，以确保查询迭代器不会返回 Null 值。
* 修复了 bug，以确保允许较大的 PartitionKey

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* 为网关模式添加了多区域写入支持。

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* 修复了读取查询的分区键范围时的 bug。

### <a name="1163"></a><a name="1.16.3"/>1.16.3
* 修复了在 DirectHttps 模式下设置继续标记标头大小时的 bug。

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* 添加了流式故障转移支持。
* 添加了对自定义元数据的支持。
* 改进了会话处理逻辑。
* 修复了分区键范围缓存中的 bug。
* 修复了直接模式下的 NPE bug。

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* 添加了对唯一索引的支持。
* 在源选项中添加了对限制继续标记大小的支持。
* 修复了 Json 序列化（时间戳）中的一个 bug。
* 修复了 Json 序列化（枚举）中的一个 bug。
* com.fasterxml.jackson.core:jackson-databind 的依赖项升级到了 2.9.5。

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* 改进了直接模式的连接池。
* 改进了非 orderby 跨分区查询的预提取。
* 改进了 UUID 生成。
* 改进了会话一致性逻辑。
* 添加了对多多边形的支持。
* 添加了对集合的分区键范围统计信息的支持。
* 修复了多区域支持方面的一个 bug。

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* 改进了 Json 序列化性能。
* 此 SDK 版本需要最新版本的 [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)。

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* 对 Microsoft 友元库进行了内部更改。

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* 修复了读取单个分区键范围的问题。
* 修复了 ResourceID 分析中影响使用短名称的数据库的问题。
* 修复了分区键编码导致的问题。

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* 关键 bug 修复，用于在分区拆分期间请求处理。
* 解决了 Strong 和 BoundedStaleness 一致性级别存在的问题。

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* 添加了对名为 ConsistentPrefix 的新一致性级别的支持。
* 修复了在会话模式下读取集合时的 bug。

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* 启用了对吞吐量低至 2,500 RU/秒并且缩放增量为 100 RU/秒的分区集合的支持。
* 修复了本机程序集中的 bug，该 bug 在某些查询中可能会导致 NullRef 异常。

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* 修复了查询引擎配置中可能会导致网关模式下查询异常的 Bug。
* 修复了会话容器中的一些 Bug，这些 Bug 可能会在创建集合后立即导致“找不到所有者资源”请求异常。

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* 添加了对聚合查询（COUNT、MIN、MAX、SUM、AVG）的支持。 请参阅[聚合支持](sql-query-aggregates.md)。
* 添加了对更改源的支持。
* 通过 RequestOptions.setPopulateQuotaInfo 添加了对集合配额信息的支持。
* 通过 RequestOptions.setScriptLoggingEnabled 添加了对存储过程脚本日志记录的支持。
* 修复了以下 bug：DirectHttps 模式中的查询在遇到限制失败时可能会停止响应。
* 修复了一个会话一致性模式的 bug。
* 修复了一个 bug，该 bug 可能会导致在请求率很高时，在 HttpContext 中出现 NullReferenceException。
* 改进了 DirectHttps 模式的性能。

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* 使用 ConnectionPolicy.setProxy() API 添加了基于简单客户端实例的代理支持。
* 添加了 DocumentClient.close() API 以正确关闭 DocumentClient 实例。
* 通过从本机程序集（而非网关）派生查询计划，提高直接连接模式下的查询性能。
* 设置 FAIL_ON_UNKNOWN_PROPERTIES = false，使用户无需在其 POJO 中定义 JsonIgnoreProperties。
* 重构了日志记录以使用 SLF4J。
* 修复了一致性读取器中的其他几个 Bug。

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* 修复了连接管理中的 bug，防止直接连接模式下的连接泄漏。
* 修复了 TOP 查询中可能会引发 NullReference 异常的 bug。
* 通过减少调用内部缓存的网络数提高了性能。
* 在 DocumentClientException 中添加了状态代码、ActivityID 和请求 URI，以更好地进行故障排除。

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* 修复了连接管理中的问题，实现了更好的稳定性。

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* 添加了对 BoundedStaleness 一致性级别的支持。
* 添加了对分区集合的 CRUD 操作的直接连接支持。
* 修复了使用 SQL 查询数据库的 bug。
* 修复了会话缓存中会话令牌设置可能不正确的 bug。

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* 添加了对跨分区并行查询的支持。
* 已对分区集合添加 TOP/ORDER BY 查询支持。
* 添加了对强一致性的支持。
* 添加了对使用直接连接时基于名称的请求的支持。
* 修复问题，以便使所有请求重试上的 ActivityId保持一致。
* 修复了重新创建具有相同名称的集合期间与会话缓存相关的 bug。
* 为地域隔离的空间查询指定集合索引策略的同时，已添加多边形和 LineString 数据类型。
* 解决 Java 文档中的 Java 1.8 的问题。

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* 修复了 PartitionKeyDefinitionMap 中的一个 bug，以便缓存单个分区集合，而不进行额外的提取分区键的请求。
* 修复了一个 bug，以便在提供不正确的分区键值时不重试。

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* 添加了对多区域数据库帐户的支持。
* 添加对自动重试限制请求的支持，并提供了选项用于自定义最大重试次数和最大重试等待时间。  请参阅 RetryOptions 和 ConnectionPolicy.getRetryOptions()。
* 已弃用基于 IPartitionResolver 的自定义分区代码。 请使用分区集合来提高存储和吞吐量。

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* 添加了对速率限制的重试策略支持。  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* 对文档添加了生存时间 (TTL) 支持。

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* 实现了[分区集合](partition-data.md)和[用户定义的性能级别](performance-levels.md)。

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* 修复了 HashPartitionResolver 中的 Bug 以生成 little-endian 格式的哈希值，以便与其他 SDK 保持一致。

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* 添加哈希和范围分区冲突解决程序以协助跨多个分区对应用程序进行分片。

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* 实现 Upsert。 添加了新的 upsertXXX 方法以支持 Upsert 功能。
* 实现基于 ID 的路由。 无公共 API 更改，全部均为内部更改。

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* 跳过了发布以使版本号与其他 SDK 符合

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* 支持地理空间索引
* 验证所有资源的 ID 属性。 资源的 ID 不能包含 ？、/、#、\, 字符或以空格结尾。
* 将新标头“索引转换进度”添加到 ResourceResponse。

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* 实现 V2 索引策略

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>发布日期和停用日期
Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新 SDK 版本。

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

> [!WARNING]
> 适用于 Java 的 SQL SDK 的所有 1.x 版本将于 2020 年 5 月 30 日停用 。
> 
>

> [!WARNING]
> 1\.0.0 版之前的所有 Azure SQL SDK for Java 版本都已在 2016 年 2 月 29 日停用 。
> 
> 

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [2.4.7](#2.4.7) |2020 年 2 月 20 日 |--- |
| [2.4.6](#2.4.6) |2020 年 1 月 24 日 |--- |
| [2.4.5](#2.4.5) |2019 年 11 月 10 日 |--- |
| [2.4.4](#2.4.4) |2019 年 10 月 24 日 |--- |
| [2.4.2](#2.4.2) |2019 年 9 月 26 日 |--- |
| [2.4.1](#2.4.1) |2019 年 7 月 18 日 |--- |
| [2.4.0](#2.4.0) |2019 年 5 月 04 日 |--- |
| [2.3.0](#2.3.0) |2019 年 4 月 24 日 |--- |
| [2.2.3](#2.2.3) |2019 年 4 月 16 日 |--- |
| [2.2.2](#2.2.2) |2019 年 4 月 05 日 |--- |
| [2.2.0](#2.2.0) |2019 年 3 月 27 日 |--- |
| [2.1.3](#2.1.3) |2019 年 3 月 13 日 |--- |
| [2.1.2](#2.1.2) |2019 年 3 月 09 日 |--- |
| [2.1.1](#2.1.1) |2018 年 12 月 13 日 |--- |
| [2.1.0](#2.1.0) |2018 年 11 月 20 日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 21 日 |--- |
| [1.16.4](#1.16.4) |2018 年 9 月 10 日 |2020 年 5 月 30 日 |
| [1.16.3](#1.16.3) |2018 年 9 月 9 日 |2020 年 5 月 30 日 |
| [1.16.2](#1.16.2) |2018 年 6 月 29日 |2020 年 5 月 30 日 |
| [1.16.1](#1.16.1) |2018 年 5 月 16日 |2020 年 5 月 30 日 |
| [1.16.0](#1.16.0) |2018 年 3 月 15 日 |2020 年 5 月 30 日 |
| [1.15.0](#1.15.0) |2017 年 11 月 14 日 |2020 年 5 月 30 日 |
| [1.14.0](#1.14.0) |2017 年 10 月 28日 |2020 年 5 月 30 日 |
| [1.13.0](#1.13.0) |2017 年 8 月25 日 |2020 年 5 月 30 日 |
| [1.12.0](#1.12.0) |2017 年 7 月 11 日 |2020 年 5 月 30 日 |
| [1.11.0](#1.11.0) |2017 年 5 月 10 日 |2020 年 5 月 30 日 |
| [1.10.0](#1.10.0) |2017 年 3 月 11 日 |2020 年 5 月 30 日 |
| [1.9.6](#1.9.6) |2017 年 2 月 21 日 |2020 年 5 月 30 日 |
| [1.9.5](#1.9.5) |2017 年 1 月 31 日 |2020 年 5 月 30 日 |
| [1.9.4](#1.9.4) |2016 年 11 月 24 日 |2020 年 5 月 30 日 |
| [1.9.3](#1.9.3) |2016 年 10 月 30 日 |2020 年 5 月 30 日 |
| [1.9.2](#1.9.2) |2016 年 10 月 28 日 |2020 年 5 月 30 日 |
| [1.9.1](#1.9.1) |2016 年 10 月 26 日 |2020 年 5 月 30 日 |
| [1.9.0](#1.9.0) |2016 年 10 月 3 日 |2020 年 5 月 30 日 |
| [1.8.1](#1.8.1) |2016 年 6 月 30 日 |2020 年 5 月 30 日 |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |2020 年 5 月 30 日 |
| [1.7.1](#1.7.1) |2016 年 4 月 30 日 |2020 年 5 月 30 日 |
| [1.7.0](#1.7.0) |2016 年 4 月 27 日 |2020 年 5 月 30 日 |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |2020 年 5 月 30 日 |
| [1.5.1](#1.5.1) |2015 年 12 月 31 日 |2020 年 5 月 30 日 |
| [1.5.0](#1.5.0) |2015 年 12 月 4 日 |2020 年 5 月 30 日 |
| [1.4.0](#1.4.0) |2015 年 10 月 5 日 |2020 年 5 月 30 日 |
| [1.3.0](#1.3.0) |2015 年 10 月 5 日 |2020 年 5 月 30 日 |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |2020 年 5 月 30 日 |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |2020 年 5 月 30 日 |
| 1.0.1 |2015 年 5 月 12 日 |2020 年 5 月 30 日 |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |2020 年 5 月 30 日 |
| 0.9.5-prelease |2015 年 3 月 9 日 |2016 年 2 月 29 日 |
| 0.9.4-prelease |2015 年 2 月 17 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2015 年 1 月 13 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 12 月 10 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

