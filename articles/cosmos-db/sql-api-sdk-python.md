---
title: Azure Cosmos DB SQL Python API、SDK 和资源
description: 了解有关 SQL Python API 和 SDK 的全部信息，包括发布日期、停用日期和 Azure Cosmos DB Python SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.custom: tracking-python
ms.openlocfilehash: d68fc7b01e6204cb9ad00ea60e413c73856d758e
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542440"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>适用于 SQL API 的 Azure Cosmos DB Python SDK：发行说明和资源

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 资源提供程序](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**下载 SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API 文档**|[Python API 参考文档](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK 安装说明**|[Python SDK 安装说明](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**入门**|[Python SDK 入门](create-sql-api-python.md)|
|**当前受支持的平台**|[Python 2.7](https://www.python.org/downloads/) 和 [Python 3.5.3+](https://www.python.org/downloads/)|

## <a name="release-history"></a>版本历史记录

### <a name="400"></a>4.0.0

* 稳定版。
* 将 HttpLoggingPolicy 添加到管道，以传入请求和响应标头的自定义记录器。

### <a name="400b6"></a>4.0.0b6

* 为媒体 API 修复了 synchronized_request 中的 bug。
* 由于不支持媒体请求，因此从 ConnectionPolicy 中删除了 MediaReadMode 和 MediaRequestTimeout。

### <a name="400b5"></a>4.0.0b5

* azure.cosmos.errors 模块已弃用，并替换为 azure.cosmos.exception
* 访问条件参数（`access_condition`、`if_match`、`if_none_match`）已弃用，现在使用单独的 `match_condition` 和 `etag` 参数。
* 修复了路由映射提供程序中的 bug。
* 添加了查询 Distinct、Offset 和 Limit 支持。
* 默认文档查询执行上下文现在用于

  * ChangeFeed 查询
  * 单个分区查询（选项中显示了 partitionkey、partitionKeyRangeId）
  * 非文档查询

* 多个分区上出现聚合错误，“启用跨分区查询”设置为 true，但无“value”关键字
* 命中其他方案的查询计划终结点以获取查询计划
* 添加了对 Cosmos 实体对象的 `__repr__` 支持。
* 已更新的文档。

### <a name="400b4"></a>4.0.0b4

* 为所有操作添加了对 `timeout` 关键字参数的支持，用于指定绝对超时时间（以秒为单位），操作必须在该时间内完成。 如果超过超时值，将引发 `azure.cosmos.errors.CosmosClientTimeoutError`。

* 添加了新的 `ConnectionRetryPolicy`，用于在 HTTP 连接错误期间管理重试行为。

* 添加了新的构造函数和每个操作的配置关键字参数：

  * `retry_total` - 最大重试次数。
  * `retry_backoff_max` - 最大重试等待时间（以秒为单位）。
  * `retry_fixed_interval` - 固定的重试间隔（以毫秒为单位）。
  * `retry_read` - 最大套接字读取重试次数。
  * `retry_connect` - 最大连接错误重试次数。
  * `retry_status` - 出现错误状态代码时的最大重试次数。
  * `retry_on_status_codes` - 在出现时要执行重试操作的特定状态代码的列表。
  * `retry_backoff_factor` - 计算重试尝试之间的间隔时间时要纳入的因素。

### <a name="400b3"></a>4.0.0b3

* 分别将 `create_database_if_not_exists()` 和 `create_container_if_not_exists` 功能添加到了 CosmosClient 和数据库。

### <a name="400b2"></a>4.0.0b2

* 版本 4.0.0b2 是我们以构建适合 Python 语言最佳做法的客户端库为目标的第二个迭代产物。

**重大更改**

* 客户端连接已调整为使用 `azure.core.pipeline` 中定义的 HTTP 管道。

* 交互式对象现已重命名为代理。 这包括：

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* `CosmosClient` 的构造函数已更新：

  * `auth` 参数已重命名为 `credential`，现在将直接采用一个身份验证类型。 这意味着可以传入主键值、资源令牌字典或权限列表。 但仍支持旧字典格式。

  * `connection_policy` 参数已变为仅关键字的参数，并仍受支持，且现在可以传入策略的每个单独属性作为显式关键字参数：

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* 已向 `CosmosClient` 添加新构造函数，以便可以实现从 Azure 门户中检索到的连接字符串来创建。

* 某些 `read_all` 操作已重命名为 `list` 操作：

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* 所有采用 `request_options` 或 `feed_options` 参数的操作都已转变为使用仅关键字的参数。 此外，在仍然支持这些选项字典的同时，也支持将字典中每个单独的选项作为显式关键字参数。

* 错误层次结构现在继承自 `azure.core.AzureError` ：

  * `HTTPFailure` 已重名为 `CosmosHttpResponseError`
  * `JSONParseFailure` 已删除，并替换为 `azure.core.DecodeError`
  * 为特定响应代码添加了其他错误：
    * 状态 404 `CosmosResourceNotFoundError`
    * 状态 409 `CosmosResourceExistsError`
    * 状态 412 `CosmosAccessConditionFailedError`

* `CosmosClient` 现在可以在上下文管理器中运行，以便处理关闭客户端连接的操作。

* 可迭代的响应（例如，查询响应和列表响应）现在是 `azure.core.paging.ItemPaged` 类型。 方法 `fetch_next_block` 已替换为辅助迭代器，该迭代器由 `by_page` 方法访问。

### <a name="400b1"></a>4.0.0b1

版本 4.0.0b1 是我们以创建适合 Python 语言最佳做法的用户友好型客户端库为目标的第一个预览版本。 有关此内容的详细信息以及其他 Azure SDK 库的预览版本，请访问 https://aka.ms/azure-sdk-preview1-python 。

中断性变更：新 API 设计

* 操作现在作用于特定的客户端：

  * `CosmosClient`：此客户端处理帐户级操作。 这包括管理服务属性和列出帐户中的数据库。
  * `Database`：此客户端处理数据库级操作。 这包括创建和删除容器、用户和存储过程。 可以在 cosmosClient` 实例中按名称对其进行访问。
  * `Container`：此客户端处理特定容器的操作。 这包括查询和插入项以及管理属性。
  * `User`：此客户端处理特定用户的操作。 这包括添加和删除权限和管理用户属性。

    可以使用 `get_<child>_client` 方法向下导航客户端层次结构来访问这些客户端。 有关新 API 的完整详细信息，请参阅[参考文档](https://aka.ms/azsdk-python-cosmos-ref)。

* 按名称而不是按 ID 来访问客户端。 无需串联字符串来创建链接。

* 不再需要从各个模块导入类型和方法。 可在 `azure.cosmos` 包中直接使用公共 API 外围应用。

* 单个请求属性可以作为关键字参数提供，而不用构造单独的 `RequestOptions` 实例。

### <a name="302"></a>3.0.2

* 添加了对 MultiPolygon 数据类型的支持
* 修复了会话读取重试策略中的 Bug
* 针对解码 base 64 字符串时的不正确填充问题修复了 Bug

### <a name="301"></a>3.0.1

* 修复了 LocationCache 中的 Bug
* 修复了终结点重试逻辑 Bug
* 修正了文档

### <a name="300"></a>3.0.0

* 添加了多区域写入支持
* 命名变更
  * DocumentClient 到 CosmosClient
  * 集合到容器
  * 文档到项目
  * 包名称已更新为“azure-cosmos”
  * 命名空间已更新为“azure.cosmos”

### <a name="233"></a>2.3.3

* 添加了对代理的支持
* 添加了对读取更改源的支持
* 添加了对集合配额标头的支持
* 针对大型会话令牌问题修复了 Bug
* 修复了 ReadMedia API 的 Bug
* 修复了分区键范围缓存中的 Bug

### <a name="232"></a>2.3.2

* 添加了对连接问题的默认重试的支持。

### <a name="231"></a>2.3.1

* 将文档更新为了引用 Azure Cosmos DB 而非 Azure DocumentDB。

### <a name="230"></a>2.3.0

* 此 SDK 版本需要最新版本的 Azure Cosmos DB 模拟器（可从 https://aka.ms/cosmosdb-emulator 下载）。

### <a name="221"></a>2.2.1

* 聚合字典 bug 修复
* 用于在资源链接中裁剪斜杠的 bug 修复
* Unicode 编码测试

### <a name="220"></a>2.2.0

* 添加了对每分钟请求单位数 (RU/m) 功能的支持。
* 添加了对称为“ConsistentPrefix”的新一致性级别的支持。

### <a name="210"></a>2.1.0

* 添加了对聚合查询（COUNT、MIN、MAX、SUM、AVG）的支持。
* 添加了在对 DocumentDB 模拟器运行时禁用 SSL 验证的选项。
* 删除了依赖请求模块精确是 2.10.0 的限制。
* 将分区集合上的最小吞吐量从 10,100 RU/s 降低到 2500 RU/s。
* 添加了在存储过程执行期间对启用脚本日志记录的支持。
* 此版本中的 REST API 版本已升级到“2017-01-19”。

### <a name="201"></a>2.0.1

* 对文档注释进行编辑性更改。

### <a name="200"></a>2.0.0

* 添加了对 Python 3.5 的支持。
* 添加了对连接池使用请求模块的支持。
* 添加了会话一致性支持。
* 添加了对分区集合的 TOP/ORDERBY 查询支持。

### <a name="190"></a>1.9.0

* 添加了对限制请求的重试策略支持。 （限制请求收到请求速率太大的异常，错误代码 429。）默认情况下，遇到错误代码 429 时，DocumentDB 会针对每个请求重试九次，具体取决于响应标头中的 retryAfter 时间。
  如果想要忽略重试之间由服务器返回的 retryAfter 时间，现在可以对 ConnectionPolicy 对象设置固定的重试间隔时间，并将其作为 RetryOptions 属性的一部分。
  DocumentDB 现在对每个要中止的请求等待最多 30 秒（不考虑重试计数），并返回错误代码为 429 的响应。
  还可以在 ConnectionPolicy 对象的 RetryOptions 属性中替代该时间。

* DocumentDB 现在将 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 作为每个请求的响应标头返回，以表示限制重试计数和重试之间请求所等待的累计时间。

* 删除了 document_client 类上公开的 RetryPolicy 类以及相应的属性 (retry_policy)，改为引入了在 ConnectionPolicy 对象上公开 RetryOptions 属性的 RetryOptions 类，该类可用于覆盖一些默认的重试选项。

### <a name="180"></a>1.8.0

* 添加了对异地复制数据库帐户的支持。
* 测试修补程序以将全局主机和 masterKey 移动到各个测试类中。

### <a name="170"></a>1.7.0

* 添加了对文档生存时间 (TTL) 功能的支持。

### <a name="161"></a>1.6.1

* 与服务器端分区相关的 bug 修复，以允许在分区键路径中使用特殊字符。

### <a name="160"></a>1.6.0

* 添加了对服务器端分区集合功能的支持。

### <a name="150"></a>1.5.0

* 将客户端分片框架添加到 SDK。 实现了 HashPartionResolver 和 RangePartitionResolver 类。

### <a name="142"></a>1.4.2

* 实现 Upsert。 添加了新的 UpsertXXX 方法以支持 Upsert 功能。
* 实现基于 ID 的路由。 无公共 API 更改，全部均为内部更改。

### <a name="130"></a>1.3.0

* 跳过了发布以使版本号与其他 SDK 符合

### <a name="120"></a>1.2.0

* 支持地理空间索引。
* 验证所有资源的 ID 属性。 资源的 ID 不能包含 `?, /, #, \\` 字符或以空格结尾。
* 将新标头“索引转换进度”添加到 ResourceResponse。

### <a name="110"></a>1.1.0

* 实现 V2 索引策略

### <a name="101"></a>1.0.1

* 支持代理连接

## <a name="release--retirement-dates"></a>发布和停用日期

Microsoft 至少会在停用 SDK 前提前 12 个月发出通知，以便顺利转换为更高版本/受支持版本。 新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。

> [!WARNING]
> 2022年8月31日之后，Azure Cosmos DB 将不再进行 bug 修复、添加新功能，并为适用于 SQL API 的 Azure Cosmos DB Python SDK 的版本1.x 或2.x 提供支持。 如果你不愿意升级，则从版本1.x 到版本1.x 发送的请求将继续由 Azure Cosmos DB 服务提供服务。

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [4.0.0](#400) |2020 年 5 月 20 日 |--- |
| [3.0.2](#302) |2018 年 11 月 15 日 |--- |
| [3.0.1](#301) |2018 年 10 月 4 日 |--- |
| [2.3.3](#233) |2018 年 9 月 8 日 |2020 年 8 月 30 日 |
| [2.3.2](#232) |2018 年 5 月 8 日 |2020 年 8 月 30 日 |
| [2.3.1](#231) |2017 年 12 月 21 日 |2020 年 8 月 30 日 |
| [2.3.0](#230) |2017 年 11 月 10 日 |2020 年 8 月 30 日 |
| [2.2.1](#221) |2017 年 9 月 29 日 |2020 年 8 月 30 日 |
| [2.2.0](#220) |2017 年 5 月 10 日 |2020 年 8 月 30 日 |
| [2.1.0](#210) |2017 年 5 月 1 日 |2020 年 8 月 30 日 |
| [2.0.1](#201) |2016 年 10 月 30 日 |2020 年 8 月 30 日 |
| [2.0.0](#200) |2016 年 9 月 29 日 |2020 年 8 月 30 日 |
| [1.9.0](#190) |2016 年 7 月 7 日 |2020 年 8 月 30 日 |
| [1.8.0](#180) |2016 年 6 月 14 日 |2020 年 8 月 30 日 |
| [1.7.0](#170) |2016 年 4 月 26 日 |2020 年 8 月 30 日 |
| [1.6.1](#161) |2016 年 4 月 8 日 |2020 年 8 月 30 日 |
| [1.6.0](#160) |2016 年 3 月 29 日 |2020 年 8 月 30 日 |
| [1.5.0](#150) |2016 年 1 月 3日 |2020 年 8 月 30 日 |
| [1.4.2](#142) |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| 1.4.1 |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| [1.2.0](#120) |2015 年 8 月 6 日 |2020 年 8 月 30 日 |
| [1.1.0](#110) |2015 年 7 月 9 日 |2020 年 8 月 30 日 |
| [1.0.1](#101) |2015 年 5 月 25 日 |2020 年 8 月 30 日 |
| 1.0.0 |2015 年 4 月 7 日 |2020 年 8 月 30 日 |
| 0.9.4-prelease |2015 年 1 月 14日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2014 年 12 月 9 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 11 月 25 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 9 月 23 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 8 月 21 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>常见问题解答

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤

若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。 
