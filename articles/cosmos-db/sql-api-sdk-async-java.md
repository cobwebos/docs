---
title: Azure Cosmos DB：SQL Async Java API、SDK 和资源 | Microsoft Docs
description: 了解有关 SQL Async Java API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
services: cosmos-db
documentationcenter: java
author: SnehaGunda
manager: kfile
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/20/2018
ms.author: sngun
ms.openlocfilehash: 25a84c42430c76d296e12d3f83040fa18febdcb1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>用于 SQL API 的 Azure Cosmos DB Async Java SDK：发行说明和资源
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

SQL API Async Java SDK 与 SQL API Java SDK 的区别在于，前者通过支持 [Netty 库](http://netty.io/)提供异步操作。 先存在的 [SQL API Java SDK](sql-api-sdk-java.md) 不支持异步操作。 

<table>

<tr><td>**SDK 下载**</td><td>[Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**API 文档**</td><td>[Java API 参考文档](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**参与 SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**入门**</td><td>[Async Java SDK 入门](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**代码示例**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**性能提示**</td><td>[Github 自述文件](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>受支持的最小运行时</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>发行说明

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* 在查询中添加了反压力支持。
* 在查询中添加了对分区键范围 ID 的支持。
* 修复以允许在请求标头中使用更大的继续标记 (bugfix github #24)。
* Netty 依赖项升级到 4.1.22.Final 以确保 JVM 在主线程完成后关闭。
* 修复以避免在读取主资源时传递会话令牌。
* 添加了更多示例。
* 添加了更多基准测试方案。
* 修复了 Java 头文件以生成正确的 java 文档。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 正式版 SDK，在网关模式下使用 [Netty 库](http://netty.io/)为非阻塞 IO 提供端到端支持。 

## <a name="release-and-retirement-dates"></a>发布日期和停用日期
Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性、功能和优化仅添加到最新的 SDK 中。 因此建议你始终尽早升级到最新的 SDK 版本。

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.0.1](#1.0.1) |2018 年 4 月 20 日|--- |
| [1.0.0](#1.0.0) |2018 年 2 月 27 日|--- |

## <a name="faq"></a>常见问题
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

