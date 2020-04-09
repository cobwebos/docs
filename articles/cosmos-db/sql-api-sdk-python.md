---
title: Azure 宇宙数据库：SQL Python API、SDK &资源
description: 了解有关 SQL Python API 和 SDK 的全部信息，包括发布日期、停用日期和 Azure Cosmos DB Python SDK 各版本之间所做的更改。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: b81a3921ec11d589dadbdebd698ab9ad67d7649c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982899"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>用于 SQL API 的 Azure Cosmos DB Python SDK：发行说明和资源
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
> * [批量执行器 - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行器 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**下载 SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API 文档**|[Python API 参考文档](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK 安装说明**|[Python SDK 安装说明](https://github.com/Azure/azure-cosmos-python)|
|**参与 SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**入门**|[Python SDK 入门](sql-api-python-application.md)|
|**当前受支持的平台**|[Python 2.7](https://www.python.org/downloads/) 和 [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>发行说明

### <a name="302"></a><a name="3.0.2"/>3.0.2
* 添加了对 MultiPolygon 数据类型的支持
* 修复了会话读取重试策略中的 Bug
* 针对解码 base64 字符串时的不正确填充问题修复了 Bug

### <a name="301"></a><a name="3.0.1"/>3.0.1
* 修复了 LocationCache 中的 Bug
* 修复了终结点重试逻辑 Bug
* 修正了文档

### <a name="300"></a><a name="3.0.0"/>3.0.0
* 支持多区域写入。
* 命名空间已更改为 azure.cosmos。
* 集合和文档概念已重命名为 container 和 item，document_client 已重命名为 cosmos_client。 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* 添加了对代理的支持
* 添加了对读取更改源的支持
* 添加了对集合配额标头的支持
* 针对大型会话令牌问题修复了 Bug
* 修复了 ReadMedia API 的 Bug
* 修复了分区键范围缓存中的 Bug

### <a name="232"></a><a name="2.3.2"/>2.3.2
* 添加了对连接问题的默认重试的支持。

### <a name="231"></a><a name="2.3.1"/>2.3.1
* 将文档更新为了引用 Azure Cosmos DB 而非 Azure DocumentDB。

### <a name="230"></a><a name="2.3.0"/>2.3.0
* 此 SDK 版本需要最新版本的[Azure Cosmos DB 仿真器](https://aka.ms/cosmosdb-emulator)。

### <a name="221"></a><a name="2.2.1"/>2.2.1
* 聚合字典的 bug 修复。
* 用于在资源链接中裁剪斜杠的 bug 修复。
* 添加了对 Unicode 编码的测试。

### <a name="220"></a><a name="2.2.0"/>2.2.0
* 添加了对名为 ConsistentPrefix 的新一致性级别的支持。


### <a name="210"></a><a name="2.1.0"/>2.1.0
* 添加了对聚合查询（COUNT、MIN、MAX、SUM、AVG）的支持。
* 添加了一个选项，用于在针对 Cosmos DB 仿真器运行时禁用 TLS 验证。
* 删除了依赖请求模块精确是 2.10.0 的限制。
* 将分区集合上的最小吞吐量从 10,100 RU/s 降低到 2500 RU/s。
* 添加了在存储过程执行期间对启用脚本日志记录的支持。
* 此版本中的 REST API 版本已升级到“2017-01-19”。

### <a name="201"></a><a name="2.0.1"/>2.0.1
* 对文档注释进行编辑更改。

### <a name="200"></a><a name="2.0.0"/>2.0.0
* 添加了对 Python 3.5 的支持。
* 添加了对连接池使用请求模块的支持。
* 添加了对会话一致性的支持。
* 已对分区集合添加 TOP/ORDERBY 查询支持。

### <a name="190"></a><a name="1.9.0"/>1.9.0
* 对限制添加了重试策略支持。 （已限制的请求收到请求速率过大的异常，错误代码 429。默认情况下，当遇到错误代码 429 时，Azure Cosmos DB 会为每个请求重试 9 次，从而在响应标头中遵守重试时间。 如果想要忽略重试之间由服务器返回的 retryAfter 时间，现在可以对 ConnectionPolicy 对象设置固定的重试间隔时间，并将其作为 RetryOptions 属性的一部分。 Azure Cosmos DB 现在对每个要中止的请求等待最多 30 秒（不考虑重试次数），并返回对错误代码 429 作出的响应。 还可以在 ConnectionPolicy 对象的 RetryOptions 属性中替代该时间。
* Cosmos DB 现在将 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 作为每个请求的响应标头返回，以表示限制重试计数和重试之间请求所等待的累计时间。
* 已移除 document_client 类上公开的 RetryPolicy 类以及相应的属性 (retry_policy)，引入了在 ConnectionPolicy 对象上公开 RetryOptions 属性的 RetryOptions 类，该类可用于覆盖一些默认的重试选项。

### <a name="180"></a><a name="1.8.0"/>1.8.0
* 添加了对多区域数据库帐户的支持。

### <a name="170"></a><a name="1.7.0"/>1.7.0
* 在文档中添加了对生存时间 (TTL) 的支持。

### <a name="161"></a><a name="1.6.1"/>1.6.1
* 与服务器端分区相关的 bug 修复，以允许在分区键路径中使用特殊字符。

### <a name="160"></a><a name="1.6.0"/>1.6.0
* 实现了[分区集合](partition-data.md)和[用户定义的性能级别](performance-levels.md)。 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* 添加哈希和范围分区冲突解决程序以协助跨多个分区对应用程序进行分片。

### <a name="142"></a><a name="1.4.2"/>1.4.2
* 实现 Upsert。 添加了新的 UpsertXXX 方法以支持 Upsert 功能。
* 实现基于 ID 的路由。 无公共 API 更改，全部均为内部更改。

### <a name="120"></a><a name="1.2.0"/>1.2.0
* 支持地理空间索引。
* 验证所有资源的 ID 属性。 资源的 ID 不能包含 ？、/、#、\, 字符或以空格结尾。
* 将新标头“索引转换进度”添加到 ResourceResponse。

### <a name="110"></a><a name="1.1.0"/>1.1.0
* 实施 V2 索引策略。

### <a name="101"></a><a name="1.0.1"/>1.0.1
* 支持代理连接。

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK。

## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 前提前 12 个月**** 发出通知，以便顺利转换为更高版本/受支持版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新 SDK 版本。 

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

> [!WARNING]
> 在版本**1.0.0**之前，用于 SQL API 的所有版本的 Python SDK 均于**2016 年 2 月 29**日停用。 
> 
> 

> [!WARNING]
> 用于 SQL API 的 Python SDK 的所有版本 1.x 和 2.x 将于**2020 年 8 月 30**日停用。 
> 
> 

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [3.0.2](#3.0.2) |2018 年 11 月 15 日 |--- |
| [3.0.1](#3.0.1) |2018 年 10 月 4 日 |--- |
| [2.3.3](#2.3.3) |2018 年 9 月 8 日 |2020年8月30日 |
| [2.3.2](#2.3.2) |2018 年 5 月 8 日 |2020年8月30日 |
| [2.3.1](#2.3.1) |2017 年 12 月 21 日 |2020年8月30日 |
| [2.3.0](#2.3.0) |2017 年 11 月 10 日 |2020年8月30日 |
| [2.2.1](#2.2.1) |2017 年 9 月 29 日 |2020年8月30日 |
| [2.2.0](#2.2.0) |2017 年 5 月 10 日 |2020年8月30日 |
| [2.1.0](#2.1.0) |2017 年 5 月 1 日 |2020年8月30日 |
| [2.0.1](#2.0.1) |2016 年 10 月 30 日 |2020年8月30日 |
| [2.0.0](#2.0.0) |2016 年 9 月 29 日 |2020年8月30日 |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |2020年8月30日 |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |2020年8月30日 |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |2020年8月30日 |
| [1.6.1](#1.6.1) |2016 年 4 月 8 日 |2020年8月30日 |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |2020年8月30日 |
| [1.5.0](#1.5.0) |2016 年 1 月 3日 |2020年8月30日 |
| [1.4.2](#1.4.2) |2015 年 10 月 6 日 |2020年8月30日 |
| 1.4.1 |2015 年 10 月 6 日 |2020年8月30日 |
| [1.2.0](#1.2.0) |2015 年 8 月 6 日 |2020年8月30日 |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |2020年8月30日 |
| [1.0.1](#1.0.1) |2015 年 5 月 25 日 |2020年8月30日 |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |2020年8月30日 |
| 0.9.4-prelease |2015 年 1 月 14日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2014 年 12 月 9 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 11 月 25 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 9 月 23 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 8 月 21 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。 

