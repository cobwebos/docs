---
title: Azure 宇宙数据库：SQL Node.js API、SDK &资源
description: 了解有关 SQL Node.js API 和 SDK 的全部信息，包括发布日期、停用日期和 Azure Cosmos DB Node.js SDK 各版本之间所做的更改。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 03f79535b3a62fbb4d0309ae86a142bd842cc308
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982882"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>用于 SQL API 的 Azure Cosmos DB Node.js SDK：发行说明和资源
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

|资源  |链接  |
|---------|---------|
|下载 SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API 文档  |  [JavaScript SDK 参考文档](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK 安装说明  |  [安装说明](https://github.com/Azure/azure-cosmos-js#installation)
|参与 SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| 示例 | [Node.js 代码示例](sql-api-nodejs-samples.md)
| 入门教程 | [JavaScript SDK 入门](sql-api-nodejs-get-started.md)
| Web 应用教程 | [使用 Azure Cosmos DB 创建 Node.js Web 应用程序](sql-api-nodejs-application.md)
| 当前受支持的平台 | [Node.js v12. x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK 版本 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK 版本 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK 版本 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK 版本 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/) - SDK 版本 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/) - SDK 版本 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) - SDK 版本 1.x.x

## <a name="release-notes"></a>发行说明

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* 将默认响应延续令牌限制InKB设置为 1kb。 默认情况下，我们将此限制为 1kb 以避免长标头（Node.js 具有全局标头大小限制）。 用户可以将此字段设置为允许较长的标头，这有助于后端优化查询执行。
* 删除禁用 SSL 验证。 此选项具有[#388](https://github.com/Azure/azure-cosmos-js/pull/388)中介绍的新备选方案

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* 允许初始标题显式设置分区密钥标头
* 使用包.json_文件防止发布无关文件
* 修复了旧版本的节点_v8上的路由映射排序错误
* 当用户提供部分重试选项时修复 Bug

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* 防止 Webpack 解析需要调用的模块

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* 修复了一个长期未解决的 Bug，其中聚合查询的 R 统始终报告为 0

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 v3 版本！ 🎉 许多新功能、错误修复和一些重大更改。 此版本的主要目标：

* 实现主要新功能
  * 二元查询
  * 限制/偏移查询
  * 用户可取消的请求
* 更新到最新的 Cosmos REST API 版本，其中所有容器都有无限比例
* 更轻松地从浏览器使用宇宙
* 更好地与新的 Azure JS SDK 指南保持一致

#### <a name="migration-guide-for-breaking-changes"></a>中断更改的迁移指南
##### <a name="improved-client-constructor-options"></a>改进的客户端构造函数选项

构造函数选项已简化：

* masterKey 已重命名密钥并移动到顶层
* 以前在选项.auth 下的属性已移动到顶层

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

##### <a name="simplified-queryiterator-api"></a>简化的查询迭代器 API
在 v2 中，从查询中迭代或检索结果的方法有很多种。 我们试图简化 v3 API 并删除类似或重复的 API：

* 删除迭代器.next（） 和迭代器.current（）。 使用 fetchNext（） 获取结果页。
* 删除迭代器.foreach（）。 改用异步迭代器。
* 迭代器.executeNext（） 重命名为迭代器.fetchNext（）
* 迭代器.toArray（） 重命名为迭代器.fetchAll（）
* 页面现在是正确的响应对象，而不是普通的 JS 对象
* const 容器 = 客户端.数据库（dbId.容器）容器 Id

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
Cosmos 服务现在支持所有容器的分区键，包括以前创建为固定容器的容器。 v3 SDK 更新到实现此更改的最新 API 版本，但它并未中断。 如果不为操作提供分区密钥，我们将默认为适用于所有现有容器和文档的系统密钥。

##### <a name="upsert-removed-for-stored-procedures"></a>为存储过程删除了 Upsert
以前允许对非分区集合进行 upsert，但随着 API 版本更新，所有集合都进行了分区，因此我们完全删除了它。

##### <a name="item-reads-will-not-throw-on-404"></a>项目读取不会扔在 404 上
const 容器 = 客户端.数据库（dbId.容器）容器 Id

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
如果 Cosmos 配置支持 SDK，则默认情况下，SDK 将写入多个区域。 这是以前选择加入的行为。

##### <a name="proper-error-objects"></a>正确的错误对象
失败的请求现在引发正确的错误或错误子类。 以前，他们抛出普通的JS对象。

#### <a name="new-features"></a>新增功能
##### <a name="user-cancelable-requests"></a>用户可取消的请求
内部获取的移动允许我们使用浏览器 AbortController API 来支持用户可取消的操作。 对于可能正在进行多个请求的操作（如跨分区查询），将取消该操作的所有请求。 现代浏览器用户已经具有 AbortController。 Node.js 用户将需要使用多填充库

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>将吞吐量设置为 db/容器创建操作的一部分
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
标头令牌生成被拆分为新库@azure/cosmos-sign。 直接调用 Cosmos REST API 的任何人都可以使用它使用我们在 内部@azure/cosmos调用的代码对标头进行签名。

##### <a name="uuid-for-generated-ids"></a>生成的 ID 的 UUID
v2 具有生成项目 ID 的自定义代码。 我们已经转到了众所周知和维护的社区图书馆。

##### <a name="connection-strings"></a>连接字符串
现在可以传递从 Azure 门户复制的连接字符串：

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>改进浏览器体验
虽然在浏览器中使用 v2 SDK 是可能的，但这不是理想的体验。 您需要对多个 node.js 内置库进行多边填充，并使用 Webpack 或 Parcel 等捆绑包。 v3 SDK 使开箱即用的体验对浏览器用户更好。

* 将请求内部替换为提取（#245）
* 删除缓冲区的使用（#330）
* 删除节点内置用法，以选择通用包/API （#328）
* 切换到节点中止控制器 （#294）

#### <a name="bug-fixes"></a>Bug 修复
* 修复产品/服务读取并带回产品/服务测试（#224）
* 修复启用终结点发现（#207）
* 修复分页结果上缺少的 R，#360）
* 展开 SQL 查询参数类型（#346）
* 将 ttl 添加到项目定义 （#341）
* 修复 CP 查询指标 （#311）
* 将活动 Id 添加到源响应（#293）
* 将_ts类型从字符串切换到数字（#252）（#295）
* 修复请求费用聚合（#289）
* 允许空白字符串分区键 （#277）
* 将字符串添加到冲突查询类型（#237）
* 向容器添加唯一的关键策略 （#234）

#### <a name="engineering-systems"></a>工程系统
并不总是最明显的变化，但它们有助于我们的团队更快地运送更好的代码。

* 对生产生成（#104）使用汇总
* 更新至 Typescript 3.5 （#327）
* 转换为 TS 项目引用。 提取测试文件夹 （#270）
* 启用无未使用的本地变量和未使用参数（#275）
* 用于 CI 生成的 Azure 管道 YAML（#298）

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* 没有代码更改。 修复了 2.1.4 包中包含一些额外文件的问题。

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* 修复重试策略中的区域故障转移
* 修复更改源具有更多结果属性
* 开发依赖项更新
* 添加 Policheck 排除.txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* 将_ts类型从字符串切换到数字
* 修复默认索引测试
* 回端口唯一的关键策略到 v2
* 演示和演示调试修补程序

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* 从 v3 分支提供修复
* 修复执行Next（） 类型签名中的 Bug
* Typo 修复

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* 构建重组。 允许在生成时拉取 SDK 版本。

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>新增功能
* 添加了更改源支持（#196）
* 添加了用于索引的多多边形数据类型（#191）
* 将"键"属性添加到构造函数作为主密钥 （#202） 的别名

#### <a name="fixes"></a>修复项
* 修复在迭代器上返回错误值的位置

#### <a name="engineering-improvements"></a>工程改进
* 添加类型脚本消耗的集成测试（#199）
* 直接从 GitHub （#194） 启用安装

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* 添加节点代理类型的接口。 Typescript 用户不再需要安装 @types/node 作为依赖项
* 现在将正确地采用首选位置
* 对参与开发人员文档的改进
* 各种拼写错误修复

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* 修复了 2.0.3 中引入的类型定义问题

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* 删除了 `big-integer` 依赖项
* 切换到 AsyncIterable 类型的引用指令。 Typescript 用户不再需要自定义其“lib”设置。
* 拼写错误修复

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* 修复了自述文件链接

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* 修复了重试接口实现

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* JavaScript SDK 版本 2.0.0 正式发布
* 添加了对多区域写入的支持。

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* RC1 版本 2.0.0 的 JavaScript SDK（公共预览版）。
* 新对象模型，使用顶级 CosmosClient 和方法拆分成相关的数据库、容器和项类。 
* 支持[承诺](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)。 
* 转换为 TypeScript 的 SDK。

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* npm 文档已修复。

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* 添加了对连接问题的默认重试的支持。
* 添加了对读取集合更改源的支持。
* 修复了间歇性导致“读取会话不可用”的会话一致性 bug。
* 添加了对查询指标的支持。
* 修改了 http 代理的最大连接数。

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* 将文档更新为了引用 Azure Cosmos DB 而非 Azure DocumentDB。
* 在 ConnectionPolicy 中增加了对 proxyUrl 设置的支持。

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* 微调了区分大小写的文件系统。

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* 添加了对会话一致性的支持。
* 此 SDK 版本需要最新版本的[Azure Cosmos DB 仿真器](https://aka.ms/cosmosdb-emulator)。

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* 防拆分跨分区查询。
* 添加对带有前导和尾随斜杠（和对应测试）的资源链接的支持。

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*    npm 文档已修复。

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* 修复了 executeStoredProcedure 中的一个 bug，其中涉及的文档具有特殊 Unicode 字符（LS、PS）。
* 修复了在分区键中处理含 Unicode 字符的文档时的 bug。
* 修复使用名称媒体创建集合的支持。 GitHub 问题 #114。
* 修复了权限授权令牌的支持。 GitHub 问题 #178。

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* 添加了对名为 ConsistentPrefix 的新[一致性级别](consistency-levels.md)的支持。
* 添加了对 UriFactory 的支持。
* 修复了 Unicode 支持 bug。 GitHub 问题 #171。

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* 添加了对聚合查询（COUNT、MIN、MAX、SUM、AVG）的支持。
* 现可控制跨分区查询的并行度。
* 添加了在针对 Azure Cosmos DB 仿真器运行时禁用 TLS 验证的选项。
* 将分区集合上的最小吞吐量从 10,100 RU/s 降低到 2500 RU/s。
* 修复了针对单分区集合的继续标记 bug。 GitHub 问题 #107。
* 修复了将 0 处理成单个参数时出现的 executeStoredProcedure bug。 GitHub 问题 #155。

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* 修复了 user-agent 标头，使之包括 SDK 版本。
* 细微代码清理。

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* 使用 SDK 定位仿真器（主机名+本地主机）时禁用 TLS 验证。
* 添加了在存储过程执行期间对启用脚本日志记录的支持。

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* 添加了对跨分区并行查询的支持。
* 已对分区集合添加 TOP/ORDER BY 查询支持。

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* 对限制添加了重试策略支持。 （已限制的请求收到请求速率过大的异常，错误代码 429。默认情况下，当遇到错误代码 429 时，Azure Cosmos DB 会为每个请求重试 9 次，从而在响应标头中遵守重试时间。 如果想要忽略重试之间由服务器返回的 retryAfter 时间，现在可以对 ConnectionPolicy 对象设置固定的重试间隔时间，并将其作为 RetryOptions 属性的一部分。 Azure Cosmos DB 现在对每个要中止的请求等待最多 30 秒（不考虑重试次数），并返回对错误代码 429 作出的响应。 还可以在 ConnectionPolicy 对象的 RetryOptions 属性中替代该时间。
* Cosmos DB 现在将 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 作为每个请求的响应标头返回，以表示限制重试计数和重试之间请求所等待的累计时间。
* 已添加 RetryOptions 类，从而公开了 ConnectionPolicy 类上可用于替代某些默认重试选项的 RetryOptions 属性。

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* 添加了对多区域数据库帐户的支持。

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* 在文档中添加了对生存时间 (TTL) 的支持。

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* 实现了[分区集合](partition-data.md)和[用户定义的性能级别](performance-levels.md)。

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* 修复了 RangePartitionResolver.resolveForRead Bug，其会由于结果的错误 concat，它不返回链接。

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* 修复了 hashPartitionResolver resolveForRead()：当没有提供分区键时，抛出异常，而不是返回所有已注册链接的列表。

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* 修复问题 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - 专用 HTTPS 代理：避免因 Azure Cosmos DB 用途而修改全局代理。 对所有 lib 的请求使用专用代理。

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* 修复了问题 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 正确处理媒体 ID 中的短划线。

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* 修复了问题 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 侦听器泄漏警告。

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* 修复了问题 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - 对区分大小写的系统，将文件夹 Hash 重命名为 hash。

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* 通过添加哈希和范围分区解析程序来实现分片支持。

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* 实现 Upsert。 DocumentClient 上的新 upsertXXX 方法。

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* 跳过以使版本号与其他 SDK 符合。

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* 将 Q 承诺包装拆分到新的存储库。
* 更新到 npm 注册表的程序包文件。

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* 实现基于 ID 的路由。
* 修复了问题 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - 当前属性与方法 current() 发生冲突。

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* 添加对地理空间索引的支持。
* 验证所有资源的 ID 属性。 资源的帐号不能包含""/"、*、 &#47;&#47;、字符或以空格结尾。
* 将新标头“索引转换进度”添加到 ResourceResponse。

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* 实施 V2 索引策略。

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* 问题 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 已在核心和承诺 SDK 中实现 eslint 和 grunt 配置。

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* 问题 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - 承诺包装器不包括错误的标头。

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* 通过添加 readConflicts、readConflictAsync 和 queryConflicts 实现了查询冲突的功能。
* 更新了 API 文档。
* 问题 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync 错误。

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK。

## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 前提前 12 个月**** 发出通知，以便顺利转换为更高版本/受支持版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

> [!WARNING]
> SQL API 的节点客户端 SDK 的所有版本**1.x**将于**2020 年 8 月 30**日停用。 这仅影响客户端节点 SDK，不会影响服务器端脚本（存储过程、触发器和 UDF）。
> 
>
<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019 年 6 月 26 日 |--- |
| [3.0.4](#3.0.4) |2019 年 7 月 22 日 |--- |
| [3.0.3](#3.0.3) |2019 年 7 月 17 日 |--- |
| [3.0.2](#3.0.2) |2019 年 7 月 9 日 |--- |
| [3.0.0](#3.0.0) |六月 28， 2019 |--- |
| [2.1.5](#2.1.5) |2019年3月20日 |--- |
| [2.1.4](#2.1.4) |2019年3月15日 |--- |
| [2.1.3](#2.1.3) |2019年3月8日 |--- |
| [2.1.2](#2.1.2) |2019 年 1 月 28 日 |--- |
| [2.1.1](#2.1.1) |2018 年 12 月 5 日 |--- |
| [2.1.0](#2.1.0) |2018 年 12 月 4 日 |--- |
| [2.0.5](#2.0.5) |2018 年 11 月 7 日 |--- |
| [2.0.4](#2.0.4) |2018年10月30日 |--- |
| [2.0.3](#2.0.3) |2018年10月30日 |--- |
| [2.0.2](#2.0.2) |2018 年 10 月 10 日 |--- |
| [2.0.1](#2.0.1) |2018 年 9 月 25日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 24 日 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018 年 8 月 2 号 |--- |
| [1.14.4](#1.14.4) |2018 年 5 月 3 日 |2020年8月30日 |
| [1.14.3](#1.14.3) |2018 年 5 月 3 日 |2020年8月30日 |
| [1.14.2](#1.14.2) |2017 年 12 月 21 日 |2020年8月30日 |
| [1.14.1](#1.14.1) |2017 年 11 月 10 日 |2020年8月30日 |
| [1.14.0](#1.14.0) |2017 年 11 月 9 日 |2020年8月30日 |
| [1.13.0](#1.13.0) |2017 年 10 月 11 日 |2020年8月30日 |
| [1.12.2](#1.12.2) |2017 年 8 月 10 日 |2020年8月30日 |
| [1.12.1](#1.12.1) |2017 年 8 月 10 日 |2020年8月30日 |
| [1.12.0](#1.12.0) |2017 年 5 月 10 日 |2020年8月30日 |
| [1.11.0](#1.11.0) |2017 年 3 月 16 日 |2020年8月30日 |
| [1.10.2](#1.10.2) |2017 年 1 月 27 日 |2020年8月30日 |
| [1.10.1](#1.10.1) |2016 年 12 月 22 日 |2020年8月30日 |
| [1.10.0](#1.10.0) |2016 年 10 月 3 日 |2020年8月30日 |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |2020年8月30日 |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |2020年8月30日 |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |2020年8月30日 |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |2020年8月30日 |
| [1.5.6](#1.5.6) |2016 年 3 月 8 日 |2020年8月30日 |
| [1.5.5](#1.5.5) |2016 年 2 月 2 日 |2020年8月30日 |
| [1.5.4](#1.5.4) |2016 年 2 月 1 日 |2020年8月30日 |
| [1.5.2](#1.5.2) |2016 年 1 月 26 日 |2020年8月30日 |
| [1.5.2](#1.5.2) |2016 年 1 月 22日 |2020年8月30日 |
| [1.5.1](#1.5.1) |2016 年 1 月 4 日 |2020年8月30日 |
| [1.5.0](#1.5.0) |2015 年 12 月 31 日 |2020年8月30日 |
| [1.4.0](#1.4.0) |2015 年 10 月 6 日 |2020年8月30日 |
| [1.3.0](#1.3.0) |2015 年 10 月 6 日 |2020年8月30日 |
| [1.2.2](#1.2.2) |2015 年 9 月 10日 |2020年8月30日 |
| [1.2.1](#1.2.1) |2015 年 8 月 15日 |2020年8月30日 |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |2020年8月30日 |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |2020年8月30日 |
| [1.0.3](#1.0.3) |2015 年 6 月 4 日 |2020年8月30日 |
| [1.0.2](#1.0.2) |2015 年 5 月 23 日 |2020年8月30日 |
| [1.0.1](#1.0.1) |2015年 5 月 15日 |2020年8月30日 |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |2020年8月30日 |

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。

