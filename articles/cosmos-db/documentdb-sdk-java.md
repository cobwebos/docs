---
title: "Azure DocumentDB Java API、SDK 和资源 | Microsoft 文档"
description: "了解有关 Java API 和 SDK 的全部信息，包括发布日期、停用日期和 DocumentDB Java SDK 各版本之间所做的更改。"
services: cosmos-db
documentationcenter: java
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 7861cadf-2a05-471a-9925-0fec0599351b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/24/2017
ms.author: khdang
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 60c73f6ffb887337e6e52d20c42f316c355a8836
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="documentdb-java-sdk-release-notes-and-resources"></a>DocumentDB Java SDK：发行说明和资源
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK 下载**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>

<tr><td>**API 文档**</td><td>[Java API 参考文档](http://azure.github.io/azure-documentdb-java/)</td></tr>

<tr><td>**参与 SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>

<tr><td>**入门**</td><td>[Java SDK 入门](documentdb-java-get-started.md)</td></tr>

<tr><td>**Web 应用教程**</td><td>[使用 DocumentDB 开发 Web 应用程序](documentdb-java-application.md)</td></tr>

<tr><td>**当前受支持的运行时**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>发行说明

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* 添加了对每分钟请求单位 (RU/m) 功能的支持。
* 添加了对名为 ConsistentPrefix 的新一致性级别的支持。
* 修复了在会话模式下读取集合时的 bug。

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* 启用了对吞吐量低至 2,500 RU/秒并且缩放增量为 100 RU/秒的分区集合的支持。
* 修复了本机程序集中的 bug，该 bug 在某些查询中可能会导致 NullRef 异常。

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* 修复了查询引擎配置中可能会导致网关模式下查询异常的 Bug。
* 修复了会话容器中的一些 Bug，这些 Bug 可能会在创建集合后立即导致“找不到所有者资源”请求异常。

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* 添加了对聚合查询（COUNT、MIN、MAX、SUM、AVG）的支持。 请参阅[聚合支持](documentdb-sql-query.md#Aggregates)。
* 添加了对更改源的支持。
* 通过 RequestOptions.setPopulateQuotaInfo 添加了对集合配额信息的支持。
* 通过 RequestOptions.setScriptLoggingEnabled 添加了对存储过程脚本日志记录的支持。
* 修复了一个 bug，该 bug 导致 DirectHttps 模式的查询在遇到限制失败时可能会挂起。
* 修复了一个会话一致性模式的 bug。
* 修复了一个 bug，该 bug 可能会导致在请求率很高时，在 HttpContext 中出现 NullReferenceException。
* 改进了 DirectHttps 模式的性能。

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* 使用 ConnectionPolicy.setProxy() API 添加了基于简单客户端实例的代理支持。
* 添加了 DocumentClient.close() API 以正确关闭 DocumentClient 实例。
* 通过从本机程序集（而非网关）派生查询计划，提高直接连接模式下的查询性能。
* 设置 FAIL_ON_UNKNOWN_PROPERTIES = false，使用户无需在其 POJO 中定义 JsonIgnoreProperties。
* 重构了日志记录以使用 SLF4J。
* 修复了一致性读取器中的其他几个 Bug。

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* 修复了连接管理中的 bug，防止直接连接模式下的连接泄漏。
* 修复了 TOP 查询中可能会引发 NullReferenece 异常的 bug。
* 通过减少调用内部缓存的网络数提高了性能。
* 在 DocumentClientException 中添加了状态代码、ActivityID 和请求 URI，以更好地进行故障排除。

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* 修复了连接管理中的问题，实现了更好的稳定性。

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* 添加了对 BoundedStaleness 一致性级别的支持。
* 添加了对分区集合的 CRUD 操作的直接连接支持。
* 修复了使用 SQL 查询数据库的 bug。
* 修复了会话缓存中会话令牌设置可能不正确的 bug。

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* 添加了对跨分区并行查询的支持。
* 已对分区集合添加 TOP/ORDER BY 查询支持。
* 添加了对强一致性的支持。
* 添加了对使用直接连接时基于名称的请求的支持。
* 修复问题，以便使所有请求重试上的 ActivityId保持一致。
* 修复了重新创建具有相同名称的集合期间与会话缓存相关的 bug。
* 为地域隔离的空间查询指定集合索引策略的同时，已添加多边形和 LineString 数据类型。
* 解决 Java 文档中的 Java 1.8 的问题。

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* 修复了 PartitionKeyDefinitionMap 中的一个 bug，以便缓存单个分区集合，而不进行额外的提取分区键的请求。
* 修复了一个 bug，以便在提供不正确的分区键值时不重试。

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* 添加了对多区域数据库帐户的支持。
* 添加对自动重试限制请求的支持，并提供了选项用于自定义最大重试次数和最大重试等待时间。  请参阅 RetryOptions 和 ConnectionPolicy.getRetryOptions()。
* 已弃用基于 IPartitionResolver 的自定义分区代码。 请使用分区集合来提高存储和吞吐量。

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* 对限制添加了重试策略支持。  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* 对文档添加了生存时间 (TTL) 支持。

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* 实现了[分区集合](partition-data.md)和[用户定义的性能级别](performance-levels.md)。

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* 修复了 HashPartitionResolver 中的 Bug 以生成 little-endian 格式的哈希值，以便与其他 SDK 保持一致。

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* 添加哈希和范围分区冲突解决程序以协助跨多个分区对应用程序进行分片。

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* 实现 Upsert。 添加了新的 upsertXXX 方法以支持 Upsert 功能。
* 实现基于 ID 的路由。 无公共 API 更改，全部均为内部更改。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* 跳过了发布以使版本号与其他 SDK 符合

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 支持地理空间索引
* 验证所有资源的 ID 属性。 资源的 ID 不能包含 ？、/、#、\, 字符或以空格结尾。
* 将新标头“索引转换进度”添加到 ResourceResponse。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 实现 V2 索引策略

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议你始终尽早升级到最新 SDK 版本。

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都将被服务拒绝。

> [!WARNING]
> Azure DocumentDB SDK for Java 在 **1.0.0** 版之前的所有版本都将在 **2016 年 2 月 29 日**停用。
> 
> 

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.11.0](#1.11.0) |2017 年 5 月 10 日 |--- |
| [1.10.0](#1.10.0) |2017 年 3 月 11 日 |--- |
| [1.9.6](#1.9.6) |2017 年 2 月 21 日 |--- |
| [1.9.5](#1.9.5) |2017 年 1 月 31 日 |--- |
| [1.9.4](#1.9.4) |2016 年 11 月 24 日 |--- |
| [1.9.3](#1.9.3) |2016 年 10 月 30 日 |--- |
| [1.9.2](#1.9.2) |2016 年 10 月 28 日 |--- |
| [1.9.1](#1.9.1) |2016 年 10 月 26 日 |--- |
| [1.9.0](#1.9.0) |2016 年 10 月 3 日 |--- |
| [1.8.1](#1.8.1) |2016 年 6 月 30 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.1](#1.7.1) |2016 年 4 月 30 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 27 日 |--- |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1.5.1](#1.5.1) |2015 年 12 月 31 日 |--- |
| [1.5.0](#1.5.0) |2015 年 12 月 4 日 |--- |
| [1.4.0](#1.4.0) |2015 年 10 月 5 日 |--- |
| [1.3.0](#1.3.0) |2015 年 10 月 5 日 |--- |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |--- |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1.0.1](#1.0.1) |2015 年 5 月 12 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |--- |
| 0.9.5-prelease |2015 年 3 月 9 日 |2016 年 2 月 29 日 |
| 0.9.4-prelease |2015 年 2 月 17 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2015 年 1 月 13 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 12 月 10 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>常见问题
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。


