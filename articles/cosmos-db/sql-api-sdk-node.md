---
title: "Azure Cosmos DB：SQL Node.js API、SDK 和资源 | Microsoft Docs"
description: "了解有关 SQL Node.js API 和 SDK 的全部信息，包括发布日期、停用日期和 Azure Cosmos DB Node.js SDK 各版本之间所做的更改。"
services: cosmos-db
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 1/4/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa9b11e754ab86f0e4c88923d4b895aa5dd0e271
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>用于 SQL API 的 Azure Cosmos DB Node.js SDK：发行说明和资源
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

<table>

<tr><td>**下载 SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**API 文档**</td><td>[Node.js API 参考文档](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**SDK 安装说明**</td><td>[安装说明](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**参与 SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**示例**</td><td>[Node.js 代码示例](sql-api-nodejs-samples.md)</td></tr>

<tr><td>**入门教程**</td><td>[Node.js SDK 入门](sql-api-nodejs-get-started.md)</td></tr>

<tr><td>**Web 应用教程**</td><td>[使用 Azure Cosmos DB 创建 Node.js Web 应用程序](sql-api-nodejs-application.md)</td></tr>

<tr><td>**当前受支持的平台**</td><td> 
[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/)<br/> 
[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> 
[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> 
[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 
</td></tr>
</table></br>

## <a name="release-notes"></a>发行说明

### <a name="1.14.2"/>1.14.2</a>
* 将文档更新为了引用 Azure Cosmos DB 而非 Azure DocumentDB。
* 在 ConnectionPolicy 中增加了对 proxyUrl 设置的支持。

### <a name="1.14.1"/>1.14.1</a>
* 微调了区分大小写的文件系统。

### <a name="1.14.0"/>1.14.0</a>
* 添加了对会话一致性的支持。
* 此 SDK 版本需要最新版本的 Azure Cosmos DB 模拟器（可从 https://aka.ms/cosmosdb-emulator 下载）。

### <a name="1.13.0"/>1.13.0</a>
* 防拆分跨分区查询。
* 添加对带有前导和尾随斜杠（和对应测试）的资源链接的支持。

### <a name="1.12.2"/>1.12.2</a>
*   npm 文档已修复。

### <a name="1.12.1"/>1.12.1</a>
* 修复了 executeStoredProcedure 中的一个 bug，其中涉及的文档具有特殊 Unicode 字符（LS、PS）。
* 修复了在分区键中处理含 Unicode 字符的文档时的 bug。
* 修复使用名称媒体创建集合的支持。 Github 问题 #114。
* 修复了权限授权令牌的支持。 Github 问题 #178。

### <a name="1.12.0"/>1.12.0</a>
* 添加了对名为 ConsistentPrefix 的新[一致性级别](consistency-levels.md)的支持。
* 添加了对 UriFactory 的支持。
* 修复了 Unicode 支持 bug。 GitHub 问题 #171。

### <a name="1.11.0"/>1.11.0</a>
* 添加了对聚合查询（COUNT、MIN、MAX、SUM、AVG）的支持。
* 现可控制跨分区查询的并行度。
* Azure Cosmos DB 模拟器运行时，添加了禁用 SSL 验证的选项。
* 将分区集合上的最小吞吐量从 10,100 RU/s 降低到 2500 RU/s。
* 修复了针对单分区集合的继续标记 bug。 Github 问题 #107。
* 修复了将 0 处理成单个参数时出现的 executeStoredProcedure bug。 Github 问题 #155。

### <a name="1.10.2"/>1.10.2</a>
* 修复了 user-agent 标头，使之包括 SDK 版本。
* 细微代码清理。

### <a name="1.10.1"/>1.10.1</a>
* 使用 SDK 定位模拟器 (hostname=localhost) 时禁用 SSL 验证。
* 添加了在存储过程执行期间对启用脚本日志记录的支持。

### <a name="1.10.0"/>1.10.0</a>
* 添加了对跨分区并行查询的支持。
* 已对分区集合添加 TOP/ORDER BY 查询支持。

### <a name="1.9.0"/>1.9.0</a>
* 对限制添加了重试策略支持。 （限制请求收到请求速率太大的异常，错误代码 429。）默认情况下，出现错误代码 429 时，Azure Cosmos DB 将针对每个请求重试九次，具体取决于响应标头中的 retryAfter 时间。 如果想要忽略重试之间由服务器返回的 retryAfter 时间，现在可以对 ConnectionPolicy 对象设置固定的重试间隔时间，并将其作为 RetryOptions 属性的一部分。 Azure Cosmos DB 现在对每个要中止的请求等待最多 30 秒（不考虑重试次数），并返回对错误代码 429 作出的响应。 还可以在 ConnectionPolicy 对象的 RetryOptions 属性中替代该时间。
* Cosmos DB 现在将 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 作为每个请求的响应标头返回，以表示限制重试计数和重试之间请求所等待的累计时间。
* 已添加 RetryOptions 类，从而公开了 ConnectionPolicy 类上可用于替代某些默认重试选项的 RetryOptions 属性。

### <a name="1.8.0"/>1.8.0</a>
* 添加了对多区域数据库帐户的支持。

### <a name="1.7.0"/>1.7.0</a>
* 在文档中添加了对生存时间 (TTL) 的支持。

### <a name="1.6.0"/>1.6.0</a>
* 实现了[分区集合](partition-data.md)和[用户定义的性能级别](performance-levels.md)。

### <a name="1.5.6"/>1.5.6</a>
* 修复了 RangePartitionResolver.resolveForRead Bug，其会由于结果的错误 concat，它不返回链接。

### <a name="1.5.5"/>1.5.5</a>
* 修复了 hashParitionResolver resolveForRead()：这时没有提供的分区键抛出异常，而不是返回所有已注册链接的列表。

### <a name="1.5.4"/>1.5.4</a>
* 修复问题 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - 专用 HTTPS 代理：避免因 Azure Cosmos DB 用途而修改全局代理。 对所有 lib 的请求均使用专用代理。

### <a name="1.5.3"/>1.5.3</a>
* 修复了问题 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 正确处理媒体 ID 中的短划线。

### <a name="1.5.2"/>1.5.2</a>
* 修复了问题 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 侦听器泄漏警告。

### <a name="1.5.1"/>1.5.1</a>
* 修复了问题 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - 对区分大小写的系统，将文件夹 Hash 重命名为 hash。

### <a name="1.5.0"/>1.5.0</a>
* 通过添加哈希和范围分区解析程序来实现分片支持。

### <a name="1.4.0"/>1.4.0</a>
* 实现 Upsert。 DocumentClient 上的新 upsertXXX 方法。

### <a name="1.3.0"/>1.3.0</a>
* 跳过以使版本号与其他 SDK 符合。

### <a name="1.2.2"/>1.2.2</a>
* 将 Q 承诺包装拆分到新的存储库。
* 更新到 npm 注册表的程序包文件。

### <a name="1.2.1"/>1.2.1</a>
* 实现基于 ID 的路由。
* 修复了问题 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - 当前属性与方法 current() 发生冲突。

### <a name="1.2.0"/>1.2.0</a>
* 添加对地理空间索引的支持。
* 验证所有资源的 ID 属性。 资源的 ID 不能包含 ？、/、#、&#47;&#47;、字符或以空格结尾。
* 将新标头“索引转换进度”添加到 ResourceResponse。

### <a name="1.1.0"/>1.1.0</a>
* 实施 V2 索引策略。

### <a name="1.0.3"/>1.0.3</a>
* 问题 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 已在核心和承诺 SDK 中实现 eslint 和 grunt 配置。

### <a name="1.0.2"/>1.0.2</a>
* 问题 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - 承诺包装器不包括错误的标头。

### <a name="1.0.1"/>1.0.1</a>
* 通过添加 readConflicts、readConflictAsync 和 queryConflicts 实现了查询冲突的功能。
* 更新了 API 文档。
* 问题 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync 错误。

### <a name="1.0.0"/>1.0.0</a>
* GA SDK。

## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 前提前 12 个月发出通知，以便顺利转换为更高版本/受支持版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.14.2](#1.14.2) |2017 年 12 月 21 日 |--- |
| [1.14.1](#1.14.1) |2017 年 11 月 10 日 |--- |
| [1.14.0](#1.14.0) |2017 年 11 月 9 日 |--- |
| [1.13.0](#1.13.0) |2017 年 10 月 11 日 |--- |
| [1.12.2](#1.12.2) |2017 年 8 月 10 日 |--- |
| [1.12.1](#1.12.1) |2017 年 8 月 10 日 |--- |
| [1.12.0](#1.12.0) |2017 年 5 月 10 日 |--- |
| [1.11.0](#1.11.0) |2017 年 3 月 16 日 |--- |
| [1.10.2](#1.10.2) |2017 年 1 月 27 日 |--- |
| [1.10.1](#1.10.1) |2016 年 12 月 22 日 |--- |
| [1.10.0](#1.10.0) |2016 年 10 月 3 日 |--- |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |--- |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1.5.6](#1.5.6) |2016 年 3 月 8 日 |--- |
| [1.5.5](#1.5.5) |2016 年 2 月 2 日 |--- |
| [1.5.4](#1.5.4) |2016 年 2 月 1 日 |--- |
| [1.5.2](#1.5.2) |2016 年 1 月 26 日 |--- |
| [1.5.2](#1.5.2) |2016 年 1 月 22日 |--- |
| [1.5.1](#1.5.1) |2016 年 1 月 4 日 |--- |
| [1.5.0](#1.5.0) |2015 年 12 月 31 日 |--- |
| [1.4.0](#1.4.0) |2015 年 10 月 6 日 |--- |
| [1.3.0](#1.3.0) |2015 年 10 月 6 日 |--- |
| [1.2.2](#1.2.2) |2015 年 9 月 10日 |--- |
| [1.2.1](#1.2.1) |2015 年 8 月 15日 |--- |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |--- |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1.0.3](#1.0.3) |2015 年 6 月 4 日 |--- |
| [1.0.2](#1.0.2) |2015 年 5 月 23 日 |--- |
| [1.0.1](#1.0.1) |2015年 5 月 15日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |--- |

## <a name="faq"></a>常见问题
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

