---
title: Azure Cosmos DB：SQL Node.js API、SDK 和资源
description: 了解有关 SQL Node.js API 和 SDK 的全部信息，包括发布日期、停用日期和 Azure Cosmos DB Node.js SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 106f446c56377c1211d04e388608cd31e2aeef23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804208"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>适用于 SQL API 的 Azure Cosmos DB Node.js SDK：发行说明和资源
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 连接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 资源提供程序](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

|资源  |链接  |
|---------|---------|
|下载 SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API 文档  |  [JavaScript SDK 参考文档](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest&preserve-view=true)
|SDK 安装说明  |  [安装说明](https://github.com/Azure/azure-sdk-for-js)
|参与 SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| 示例 | [Node.js 代码示例](sql-api-nodejs-samples.md)
| 入门教程 | [JavaScript SDK 入门](sql-api-nodejs-get-started.md)
| Web 应用教程 | [使用 Azure Cosmos DB 创建 Node.js Web 应用程序](sql-api-nodejs-application.md)
| 当前受支持的平台 | [Node.js v12. x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK 版本 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK 版本 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK 版本 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK 版本 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/) - SDK 版本 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/) - SDK 版本 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) - SDK 版本 1.x.x

## <a name="release-notes"></a>发行说明

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* 将默认 ResponseContinuationTokenLimitInKB 设置为 1kb。 默认情况下，我们会将此限制为 1kb，以避免长标头（Node.js 具有全局标头大小限制）。 用户可以设置此字段以允许使用更长的标头，这有助于后端优化查询执行。
* 删除 disableSSLVerification。 此选项具有 [#388](https://github.com/Azure/azure-cosmos-js/pull/388) 中所述的新替代方法

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* 允许 initialHeaders 显式设置分区键标头
* 使用包 .json# 文件来阻止发布外来文件
* 修复了旧版本 node+v8 上的路由映射排序错误
* 修复了用户提供部分重试选项时遇到的 bug

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* 阻止 webpack 解析调用的模块时需要

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* 修复了在聚合查询中 RU 总是被报告为 0 的长期未解决的 bug

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 v3 版本！ 🎉 诸多新功能、bug 修复和一些重大变更。 此版本的主要目标：

* 实现主要新功能
  * DISTINCT 查询
  * LIMIT/OFFSET 查询
  * 用户可取消请求
* 更新到最新的 Cosmos REST API 版本，其中所有容器都可以无限缩放
* 更轻松地在浏览器中使用 Cosmos
* 更好地适应新的 Azure JS SDK 准则

#### <a name="migration-guide-for-breaking-changes"></a>重大变更的迁移指南
##### <a name="improved-client-constructor-options"></a>改进了客户端构造函数选项

简化了构造函数选项：

* masterKey 已重命名为 key 并移至顶层
* 之前在 options.auth 下的属性已移至顶层

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>简化了 QueryIterator API
在 v2 中，可以通过多种不同的方法来迭代或检索查询中的结果。 我们已尝试简化 v3 API 并删除类似或重复的 API：

* 删除 iterator.next() 和 iterator.current()。 使用 fetchNext() 获取结果页。
* 删除 iterator.forEach()。 改用异步迭代器。
* iterator.executeNext() 已重命名为 iterator.fetchNext()
* iterator.toArray() 已重命名为 iterator.fetchAll()
* 页面现在是正确的响应对象，而不是纯 JS 对象
* const 容器 = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>固定容器现已分区
Cosmos 服务现支持所有容器上的分区键，包括以前创建为固定容器的分区键。 v3 SDK 更新为实现此更改的最新 API 版本，但不会中断。 如果没有为操作提供分区键，则默认为适用于所有现有容器和文档的系统键。

##### <a name="upsert-removed-for-stored-procedures"></a>为存储过程删除了 upsert
以前允许将 upsert 用于非分区集合，但通过 API 版本更新，所有集合均已分区，因此我们将其完全删除。

##### <a name="item-reads-will-not-throw-on-404"></a>项读取不会引发 404 错误
const 容器 = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>默认多区域写入
如果 Cosmos 配置支持，SDK 现将默认写入多个区域。 这在以前是选择行为。

##### <a name="proper-error-objects"></a>相应的错误对象
失败请求现会引发相应的错误或错误子类。 以前，它们会引发纯 JS 对象。

#### <a name="new-features"></a>新增功能
##### <a name="user-cancelable-requests"></a>用户可取消请求
通过在内部进行提取，可以使用浏览器 AbortController API 来支持用户可取消操作。 对于可能正在进行多个请求的操作（如跨分区查询），该操作的所有请求都将被取消。 新式浏览器用户将拥有 AbortController。 Node.js 用户需要使用填充代码库

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>在 db/容器创建操作过程中设置吞吐量
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
标头令牌生成已拆分为新库 @azure/cosmos-sign。 任何直接调用 Cosmos REST API 的用户都可以使用它来签署标头，使用的代码与我们在 @azure/cosmos 中调用的代码相同。

##### <a name="uuid-for-generated-ids"></a>生成 ID 的 UUID
v2 具有生成项 ID 的自定义代码。 我们已切换到众所周知的、受维护的社区库 UUID。

##### <a name="connection-strings"></a>连接字符串
现在可以传递从 Azure 门户复制的连接字符串：

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>改进了浏览器体验
尽管可以在浏览器中使用 v2 SDK，但这并不是理想体验。 需要填充代码多个 node.js 内置库并使用 webpack 或包裹等捆绑程序。 v3 SDK 为浏览器用户提供了更好的全新体验。

* 将内部请求替换为 fetch (#245)
* 不使用缓冲区 (#330)
* 不使用节点 builtin，以支持通用包/API (#328)
* 切换到节点中止控制器 (#294)

#### <a name="bug-fixes"></a>Bug 修复
* 修复了产品/服务读取并返回产品/服务测试 (#224)
* 修复了 EnableEndpointDiscovery (#207)
* 修复了分页结果上缺少的 RU (#360)
* 展开 SQL 查询参数类型 (#346)
* 向 ItemDefinition 添加 ttl (#341)
* 修复了 CP 查询指标 (#311)
* 将 activityId 添加到 FeedResponse (#293)
* 将 _ts 类型从字符串转换为数字 (#252)(#295)
* 修复了请求费用聚合 (#289)
* 允许空字符串分区键 (#277)
* 将字符串添加到冲突查询类型 (#237)
* 将 uniqueKeyPolicy 添加到容器 (#234)

#### <a name="engineering-systems"></a>工程系统
虽然这些更改不总是那么明显，但可以帮助我们的团队更快地交付更出色的代码。

* 为生产版本使用汇总 (#104)
* 更新到 Typescript 3.5 (#327)
* 转换为 TS 项目引用。 提取测试文件夹 (#270)
* 启用 noUnusedLocals 和 noUnusedParameters (#275)
* 用于 CI 的 Azure Pipelines YAML 版本 (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* 无需更改代码。 修复了在 2.1.4 包中包含一些额外文件的问题。

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* 修复了重试策略内的区域故障转移
* 修复了 ChangeFeed hasMoreResults 属性
* 开发依赖项更新
* 添加了 PolicheckExclusions.txt

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* 将 _ts 类型从字符串转换为数字
* 修复了默认索引测试
* 将 uniqueKeyPolicy 向后移植到 v2
* 演示和演示调试修补程序

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* 向后移植 v3 分支中的产品/服务修补程序
* 修复了 executeNext() 类型签名中的 bug
* 拼写错误修复

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* 版本重构。 允许在生成时拉取 SDK 版本。

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>新功能
* 添加了 ChangeFeed 支持 (#196)
* 添加了用于索引的 MultiPolygon 数据类型 (#191)
* 向构造函数添加“key”属性作为 masterKey 的别名 (#202)

#### <a name="fixes"></a>修复项
* 修复了 next() 在迭代器上返回错误值的 bug

#### <a name="engineering-improvements"></a>工程改进
* 添加了对 typescript 使用的集成测试 (#199)
* 启用直接从 GitHub 安装 (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* 添加节点代理类型的接口。 Typescript 用户不再需要安装 @types/node 作为依赖项
* 现在将正确地采用首选位置
* 对参与开发人员文档的改进
* 各种拼写错误修复

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* 修复了 2.0.3 中引入的类型定义问题

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* 删除了 `big-integer` 依赖项
* 切换到 AsyncIterable 类型的引用指令。 Typescript 用户不再需要自定义其“lib”设置。
* 拼写错误修复

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* 修复了自述文件链接

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* 修复了重试接口实现

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* JavaScript SDK 版本 2.0.0 正式发布
* 添加了对多区域写入的支持。

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* RC1 版本 2.0.0 的 JavaScript SDK（公共预览版）。
* 新对象模型，使用顶级 CosmosClient 和方法拆分成相关的数据库、容器和项类。 
* 支持[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)。 
* 转换为 TypeScript 的 SDK。

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* npm 文档已修复。

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* 添加了对连接问题的默认重试的支持。
* 添加了对读取集合更改源的支持。
* 修复了间歇性导致“读取会话不可用”的会话一致性 bug。
* 添加了对查询指标的支持。
* 修改了 http 代理的最大连接数。

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* 将文档更新为了引用 Azure Cosmos DB 而非 Azure DocumentDB。
* 在 ConnectionPolicy 中增加了对 proxyUrl 设置的支持。

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* 微调了区分大小写的文件系统。

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* 添加了对会话一致性的支持。
* 此 SDK 版本需要最新版本的 [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)。

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* 防拆分跨分区查询。
* 添加对带有前导和尾随斜杠（和对应测试）的资源链接的支持。

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    npm 文档已修复。

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* 修复了 executeStoredProcedure 中的一个 bug，其中涉及的文档具有特殊 Unicode 字符（LS、PS）。
* 修复了在分区键中处理含 Unicode 字符的文档时的 bug。
* 修复使用名称媒体创建集合的支持。 GitHub 问题 #114。
* 修复了权限授权令牌的支持。 GitHub 问题 #178。

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* 添加了对称为“ConsistentPrefix”的新[一致性级别](consistency-levels.md)的支持。
* 添加了对 UriFactory 的支持。
* 修复了 Unicode 支持 bug。 GitHub 问题 #171。

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* 添加了对聚合查询（COUNT、MIN、MAX、SUM、AVG）的支持。
* 现可控制跨分区查询的并行度。
* Azure Cosmos DB 模拟器运行时，添加了禁用 TLS 验证的选项。
* 将分区集合上的最小吞吐量从 10,100 RU/s 降低到 2500 RU/s。
* 修复了针对单分区集合的继续标记 bug。 GitHub 问题 #107。
* 修复了将 0 处理成单个参数时出现的 executeStoredProcedure bug。 GitHub 问题 #155。

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* 修复了 user-agent 标头，使之包括 SDK 版本。
* 细微代码清理。

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* 使用 SDK 定位模拟器 (hostname=localhost) 时禁用 TLS 验证。
* 添加了在存储过程执行期间对启用脚本日志记录的支持。

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* 添加了对跨分区并行查询的支持。
* 添加了对分区集合的 TOP/ORDER BY 查询支持。

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* 添加了对限制请求的重试策略支持。 （限制请求收到请求速率太大的异常，错误代码 429。）默认情况下，出现错误代码 429 时，Azure Cosmos DB 将针对每个请求重试九次，具体取决于响应标头中的 retryAfter 时间。 如果想要忽略重试之间由服务器返回的 retryAfter 时间，现在可以对 ConnectionPolicy 对象设置固定的重试间隔时间，并将其作为 RetryOptions 属性的一部分。 Azure Cosmos DB 现在对每个要中止的请求等待最多 30 秒（不考虑重试次数），并返回对错误代码 429 作出的响应。 还可以在 ConnectionPolicy 对象的 RetryOptions 属性中替代该时间。
* Cosmos DB 现在将 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 作为每个请求的响应标头返回，以表示限制重试计数和重试之间请求所等待的累计时间。
* 已添加 RetryOptions 类，从而公开了 ConnectionPolicy 类上可用于替代某些默认重试选项的 RetryOptions 属性。

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* 添加了对多区域数据库帐户的支持。

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* 添加了对文档生存时间 (TTL) 功能的支持。

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* 实现了[分区集合](partition-data.md)和[用户定义的性能级别](performance-levels.md)。

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* 修复了 RangePartitionResolver.resolveForRead Bug，其会由于结果的错误 concat，它不返回链接。

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* 修复了 hashPartitionResolver resolveForRead()：当没有提供分区键时抛出异常，而不是返回所有已注册链接的列表。

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* 修复了问题 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - 专用 HTTPS 代理：避免因 Azure Cosmos DB 用途而修改全局代理。 对所有 lib 的请求均使用专用代理。

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* 修复了问题 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 正确处理媒体 ID 中的短划线。

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* 修复了问题 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 侦听器泄漏警告。

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* 修复了问题 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - 对区分大小写的系统，将文件夹 Hash 重命名为 hash。

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* 通过添加哈希和范围分区解析程序来实现分片支持。

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* 实现 Upsert。 DocumentClient 上的新 upsertXXX 方法。

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* 跳过以使版本号与其他 SDK 相符。

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* 将 Q 承诺包装拆分到新的存储库。
* 更新到 npm 注册表的程序包文件。

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* 实现基于 ID 的路由。
* 修复了问题 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - 当前属性与方法 current() 发生冲突。

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* 添加对地理空间索引的支持。
* 验证所有资源的 ID 属性。 资源的 ID 不能包含 ？、/、#、&#47;&#47;、字符或以空格结尾。
* 将新标头“索引转换进度”添加到 ResourceResponse。

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* 实现 V2 索引策略。

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* 问题 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 已在核心和承诺 SDK 中实现 eslint 和 grunt 配置。

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* 问题 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - 承诺包装器不包括错误的标头。

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* 通过添加 readConflicts、readConflictAsync 和 queryConflicts 实现了查询冲突的功能。
* 更新了 API 文档。
* 问题 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync 错误。

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK。

## <a name="release--retirement-dates"></a>发布和停用日期

Microsoft 至少会在停用 SDK 前提前 12 个月发出通知，以便顺利转换为更高版本/受支持版本。 新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019 年 7 月 26 日 |--- |
| [3.0.4](#3.0.4) |2019 年 7 月 22 日 |--- |
| [3.0.3](#3.0.3) |2019 年 7 月 17 日 |--- |
| [3.0.2](#3.0.2) |2019 年 7 月 9 日 |--- |
| [3.0.0](#3.0.0) |2019 年 6 月 28 日 |--- |
| [2.1.5](#2.1.5) |2019 年 3 月 20 日 |--- |
| [2.1.4](#2.1.4) |2019 年 3 月 15 日 |--- |
| [2.1.3](#2.1.3) |2019 年 3 月 8 日 |--- |
| [2.1.2](#2.1.2) |2019 年 1 月 28 日 |--- |
| [2.1.1](#2.1.1) |2018 年 12 月 5 日 |--- |
| [2.1.0](#2.1.0) |2018 年 12 月 4 日 |--- |
| [2.0.5](#2.0.5) |2018 年 11 月 7 日 |--- |
| [2.0.4](#2.0.4) |2018 年 10 月 30 日 |--- |
| [2.0.3](#2.0.3) |2018 年 10 月 30 日 |--- |
| [2.0.2](#2.0.2) |2018 年 10 月 10 日 |--- |
| [2.0.1](#2.0.1) |2018 年 9 月 25 日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 24 日 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018 年 8 月 2 号 |--- |
| [1.14.4](#1.14.4) |2018 年 5 月 3 日 |2020 年 8 月 30 日 |
| [1.14.3](#1.14.3) |2018 年 5 月 3 日 |2020 年 8 月 30 日 |
| [1.14.2](#1.14.2) |2017 年 12 月 21 日 |2020 年 8 月 30 日 |
| [1.14.1](#1.14.1) |2017 年 11 月 10 日 |2020 年 8 月 30 日 |
| [1.14.0](#1.14.0) |2017 年 11 月 9 日 |2020 年 8 月 30 日 |
| [1.13.0](#1.13.0) |2017 年 10 月 11 日 |2020 年 8 月 30 日 |
| [1.12.2](#1.12.2) |2017 年 8 月 10 日 |2020 年 8 月 30 日 |
| [1.12.1](#1.12.1) |2017 年 8 月 10 日 |2020 年 8 月 30 日 |
| [1.12.0](#1.12.0) |2017 年 5 月 10 日 |2020 年 8 月 30 日 |
| [1.11.0](#1.11.0) |2017 年 3 月 16 日 |2020 年 8 月 30 日 |
| [1.10.2](#1.10.2) |2017 年 1 月 27 日 |2020 年 8 月 30 日 |
| [1.10.1](#1.10.1) |2016 年 12 月 22 日 |2020 年 8 月 30 日 |
| [1.10.0](#1.10.0) |2016 年 10 月 3 日 |2020 年 8 月 30 日 |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |2020 年 8 月 30 日 |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |2020 年 8 月 30 日 |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |2020 年 8 月 30 日 |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |2020 年 8 月 30 日 |
| [1.5.6](#1.5.6) |2016 年 3 月 8 日 |2020 年 8 月 30 日 |
| [1.5.5](#1.5.5) |2016 年 2 月 2 日 |2020 年 8 月 30 日 |
| [1.5.4](#1.5.4) |2016 年 2 月 1 日 |2020 年 8 月 30 日 |
| [1.5.2](#1.5.2) |2016 年 1 月 26 日 |2020 年 8 月 30 日 |
| [1.5.2](#1.5.2) |2016 年 1 月 22日 |2020 年 8 月 30 日 |
| [1.5.1](#1.5.1) |2016 年 1 月 4 日 |2020 年 8 月 30 日 |
| [1.5.0](#1.5.0) |2015 年 12 月 31 日 |2020 年 8 月 30 日 |
| [1.4.0](#1.4.0) |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| [1.3.0](#1.3.0) |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| [1.2.2](#1.2.2) |2015 年 9 月 10日 |2020 年 8 月 30 日 |
| [1.2.1](#1.2.1) |2015 年 8 月 15日 |2020 年 8 月 30 日 |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |2020 年 8 月 30 日 |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |2020 年 8 月 30 日 |
| [1.0.3](#1.0.3) |2015 年 6 月 4 日 |2020 年 8 月 30 日 |
| [1.0.2](#1.0.2) |2015 年 5 月 23 日 |2020 年 8 月 30 日 |
| [1.0.1](#1.0.1) |2015年 5 月 15日 |2020 年 8 月 30 日 |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |2020 年 8 月 30 日 |

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

