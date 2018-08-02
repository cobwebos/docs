---
title: Azure Cosmos DB 常见问题解答 | Microsoft Docs
description: 获取与 Azure Cosmos DB（一种全球分布式多模型数据库服务）相关的常见问题答案。 了解容量、性能级别和缩放。
keywords: 数据库问题, 常见问题, documentdb, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: sngun
ms.openlocfilehash: b792798c64ed0a2eb12a16d868d8a17302466fea
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215350"
---
# <a name="azure-cosmos-db-faq"></a>Azure Cosmos DB 常见问题解答
## <a name="azure-cosmos-db-fundamentals"></a>Azure Cosmos DB 基础知识
### <a name="what-is-azure-cosmos-db"></a>什么是 Azure Cosmos DB？
Azure Cosmos DB 是一种全局复制的多模型数据库服务，可针对无架构数据提供丰富的查询，帮助提供可靠的可配置性能，并支持快速开发。 这一切都通过一个托管平台来实现，而该平台有 Microsoft Azure 强大的功能与影响力作为后盾。 

如果 Web、移动、游戏和 IoT 应用程序的关键要求是可预测的吞吐量、高可用性、低延迟和无架构数据模型，那么，Azure Cosmos DB 无疑是合适的解决方案。 它提供架构灵活性和丰富索引，并且对集成的 JavaScript 提供多文档事务性支持。 

有关部署和使用此服务的更多数据库问题、解答及说明，请参阅 [Azure Cosmos DB 文档页面](https://docs.microsoft.com/azure/cosmos-db/)。

### <a name="what-happened-to-the-documentdb-api"></a>DocumentDB API 发生了什么情况？

Azure Cosmos DB DocumentDB API 或 SQL (DocumentDB) API 现在称为 Azure Cosmos DB SQL API。 无需进行任何更改即可继续运行使用 DocumentDB API 构建的应用。 功能保持相同。

如果之前已有 DocumentDB API 帐户，现在则拥有 SQL API 帐户，并且不会更改计费。 

### <a name="what-happened-to-azure-documentdb-as-a-service"></a>作为一项服务，Azure DocumentDB 发生了什么情况？

Azure DocumentDB 服务现在是 Azure Cosmos DB 服务的一部分，以 SQL API 的形式显示出来。 在不对 Azure Cosmos DB SQL API 进行任何更改的情况下，将运行针对 Azure DocumentDB 生成的应用程序。 此外，Azure Cosmos DB 还支持图形 API、表 API、MongoDB API 和 Cassandra API（预览版）。

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB 的典型用例有哪些？
对于侧重于以下要求的新 Web、移动、游戏和 IoT 应用程序而言，Azure Cosmos DB 是一个不错的选择：自动缩放、可预测的性能、毫秒响应时间的快速排序，以及查询无架构数据的能力。 Azure Cosmos DB 有助于快速开发，且支持应用程序数据模型的连续迭代。 管理用户生成的内容和数据的应用程序就是 [Azure Cosmos DB 的常见用例](use-cases.md)。 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB 如何提供可预测的性能？
[请求单位](request-units.md) (RU) 是 Azure Cosmos DB 中吞吐量的衡量单位。 1 RU 吞吐量相当于获取 1 KB 文档的吞吐量。 在 Azure Cosmos DB 中进行的每个操作（包括读取、写入、SQL 查询和存储过程执行）都具有一个确定性的 RU 值，该值基于完成该操作所需的吞吐量。 无需考虑 CPU、IO 和内存，以及它们会怎样影响应用程序吞吐量，而是从单个 RU 度量值的角度进行考虑。

可以为每个 Azure Cosmos DB 容器保留以每秒 RU 表示的预配吞吐量。 对于任何规模的应用程序，都可以将单个请求设为基准以测量其 RU 值，并预配容器以处理所有请求的请求单位总和。 也可以根据应用程序的发展需求，相应地增加或减少容器的吞吐量。 如需请求单位的详细信息以及帮助确定容器需求，请参阅[估计吞吐量需求](request-units.md#estimating-throughput-needs)并尝试使用[吞吐量计算器](https://www.documentdb.com/capacityplanner)。 此处的容器一词是指 SQL API 的集合、图形 API 的图、MongoDB API 的集合以及表 API 的表。 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB 如何支持各种数据模型（例如键/值、纵栏表、文档和图形）？

键/值（表）、纵栏表、文档和图形数据模型都是原本就支持的，因为 Azure Cosmos DB 是基于 ARS（原子、记录和序列）设计构建的。 原子、记录和序列可以轻松映射和投影到各种数据模型。 现在提供了适用于一个模型子集的 API（SQL、MongoDB、表和图形 API），并且将来会提供特定于其他数据模型的其他 API。

Azure Cosmos DB 有一个不受架构影响的索引编制引擎，能够自动为它引入的所有数据编制索引，不会要求开发者提供任何架构或辅助索引。 该引擎依赖于一组逻辑索引布局（倒置、纵栏表、树形），这些布局将存储布局从索引和查询处理子系统中分离出来。 Cosmos DB 还能够以可扩展方式支持一组连网协议和 API 并将它们高效地转换为核心数据模型 (1) 和逻辑索引布局 (2)，这使得它具有原本就支持多个数据模型的独特功能。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB 是否符合 HIPAA？
是，Azure Cosmos DB 符合 HIPAA。 HIPAA 针对可识别个人身份的健康信息的使用、泄露与保护制定了要求。 有关详细信息，请参阅 [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)（Microsoft 信任中心）。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的存储限制有哪些？
对于容器可以存储在 Azure Cosmos DB 中的数据总量并没有任何限制。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的吞吐量限制有哪些？
对于 Azure Cosmos DB 中容器可以支持的吞吐量总量并没有任何限制。 关键是要将工作负荷大致均匀地分布于数量足够多的分区键上。

### <a name="are-direct-and-gateway-connectivity-modes-encrypted-"></a>直接连接和网关连接模式是否加密？ 
是的，两种模式始终完全加密。 

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB 的费用是多少？
有关详细信息，请参阅 [Azure Cosmos DB 定价详细信息](https://azure.microsoft.com/pricing/details/cosmos-db/)页面。 Azure Cosmos DB 使用费取决于预配的容器数目、容器的联机小时数，以及每个容器的预配吞吐量。 此处的容器一词是指 SQL API 的集合、图形 API 的图、MongoDB API 的集合以及表 API 的表。 

### <a name="is-a-free-account-available"></a>有免费的帐户吗？
是的，可免费注册一个限时的帐户，且无需任何承诺费。 若要注册，请访问[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 或参阅 [Azure Cosmos DB 试用常见问题解答](#try-cosmos-db)了解详细信息。

如果不熟悉 Azure，可以注册 [Azure 免费帐户](https://azure.microsoft.com/free/)，这样可以得到 30 天试用期和信用额度，让你试用所有 Azure 服务。 如果你有 Visual Studio 订阅，则还有资格[免费获取 Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用于任何 Azure 服务。 

也可以使用 [Azure Cosmos DB 模拟器](local-emulator.md)在本地免费开发和测试应用程序，而无需创建 Azure 订阅。 如果对应用程序在 Azure Cosmos DB 模拟器中的工作情况感到满意，则可以切换到在云中使用 Azure Cosmos DB 帐户。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>如何获取与 Azure Cosmos DB 相关的其他帮助？

若要询问技术问题，可以在下述两个问答论坛之一发帖：
* [MSDN 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow 适合编程问题。 请确保提问[切中主题](https://stackoverflow.com/help/on-topic)并[尽可能提供较多的详细信息，使问题清楚明了，便于回答](https://stackoverflow.com/help/how-to-ask)。 

若要请求新功能，请在 [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) 上提交新的请求。

若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

其他问题可以通过 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) 提交给支持团队；不过，该邮箱不是技术支持邮箱。 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>试用 Azure Cosmos DB 订阅

现在无需订阅和承诺，即可限时免费体验 Azure Cosmos DB。 若要注册试用 Azure Cosmos DB 订阅，请转到[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)。 此订阅独立于 [Azure 免费试用版](https://azure.microsoft.com/free/)，可在使用 Azure 免费试用版或 Azure 付费订阅的同时进行使用。 

“试用 Azure Cosmos DB”订阅显示在 Azure 门户中与用户 ID 关联的其他订阅旁。 

以下条件适用于“试用 Azure Cosmos DB”订阅：

* 对于 SQL、Gremlin（图形 API）和表帐户，每个订阅一个容器。
* 对于 MongoDB 帐户，每个订阅最多 3 个集合。
* 10 GB 存储容量。
* 全球复制面向后列 [Azure 区域](https://azure.microsoft.com/regions/)提供：美国中部、北欧和东南亚
* 最大吞吐量为 5K RU/s。
* 订阅在 24 小时后到期，有效期最多延长 48 小时。
* 不能为“试用 Azure Cosmos DB”帐户创建 Azure 支持票据；但会为拥有现有支持计划的订阅者提供支持。 

## <a name="set-up-azure-cosmos-db"></a>设置 Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>如何注册 Azure Cosmos DB？
Azure 门户中提供了 Azure Cosmos DB。 首先注册 Azure 订阅。 注册成功后，便可将 SQL API、图形 API、表 API、MongoDB API 或 Cassandra API 帐户添加到 Azure 订阅。

### <a name="what-is-a-master-key"></a>什么是主密钥？
主密钥是用于访问帐户的所有资源的安全令牌。 拥有此密钥的人对数据库帐户中的所有资源具有读取和写入访问权。 分发主密钥时要格外小心。 [Azure 门户][azure-portal]的“密钥”边栏选项卡中提供了主要主密钥和辅助主密钥。 有关密钥的详细信息，请参阅[查看、复制和重新生成访问密钥](manage-account.md#keys)。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>可以将 PreferredLocations 设置为哪些区域？ 
可以将 PreferredLocations 值设置为推出了 Cosmos DB 的任意 Azure 区域。 有关可用区域的列表，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>通过 Azure 数据中心在全球范围内分发数据时是否需要注意什么？ 
如 [Azure 区域](https://azure.microsoft.com/regions/)页面中指定的那样，Azure Cosmos DB 遍布于所有 Azure 区域。 由于它属于核心服务，因此每个新的数据中心都有 Azure Cosmos DB。 

设置区域时，请记住，Azure Cosmos DB 遵从 主权和政府云的要求。 也就是说，如果帐户是在某个[主权区域](https://azure.microsoft.com/global-infrastructure/)中创建的，则不能在该[主权区域](https://azure.microsoft.com/global-infrastructure/)外进行复制。 同样，也无法通过外部帐户启用到其他主权位置的复制。 

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>是否可以从容器级吞吐量预配切换到数据库级吞吐量预配？ 或者反之亦然？

容器级和数据库级吞吐量预配是不同的产品，在这两者之间切换需要将数据从源迁移到目标。 这意味着你需要创建新数据库或新集合，然后使用[批量执行程序库](bulk-executor-overview.md)或 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)迁移数据。

### <a name="how-do-i-create-fixed-collection-with-partition-key"></a>如何使用分区键创建固定集合

目前，可以使用 .Net SDK 的 [CreatePartitionedCollection](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L118) 方法或使用 [Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb/collection?view=azure-cli-latest#az-cosmosdb-collection-create) 创建带有分区键吞吐量的集合。 目前不支持使用 Azure 门户创建固定集合。  

## <a name="develop-against-the-sql-api"></a>针对 SQL API 进行开发

### <a name="how-do-i-start-developing-against-the-sql-api"></a>如何开始针对 SQL API 进行开发？
首先必须注册 Azure 订阅。 注册 Azure 订阅后，可将 SQL API 容器添加到 Azure 订阅。 有关添加 Azure Cosmos DB 帐户的说明，请参阅[创建 Azure Cosmos DB 数据库帐户](create-sql-api-dotnet.md#create-account)。 

[SDK](sql-api-sdk-dotnet.md) 适用于 .NET、Python、Node.js、JavaScript 和 Java。 开发人员也可以使用 [RESTful HTTP API](/rest/api/cosmos-db/)，从各种平台使用各种语言与 Azure Cosmos DB 资源进行交互。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>是否可以访问一些现成的示例来帮助自己入门？
GitHub 上提供了 SQL API [.NET](sql-api-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](sql-api-nodejs-samples.md) 和 [Python](sql-api-python-samples.md) SDK 的示例。


### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API 数据库是否支持无架构数据？
是，SQL API 允许应用程序存储任意的 JSON 文档，而不需要架构定义或提示。 通过 Azure Cosmos DB SQL 查询界面便可立即查询数据。  

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API 是否支持 ACID 事务？
是，SQL API 支持以 JavaScript 存储过程和触发器形式表示的跨文档事务。 事务以每个容器内的单个分区为范围，且以 ACID 语义执行，也就是“全有或全无”，与其他并发执行的代码和用户请求隔离。 如果在服务器端执行 JavaScript 应用程序代码的过程中引发了异常，则会回滚整个事务。 有关事务的详细信息，请参阅[数据库程序事务](programming.md#database-program-transactions)。

### <a name="what-is-a-container"></a>什么是容器？
容器是文档及其关联的 JavaScript 应用程序逻辑的组。 容器是一个计费实体，其[成本](performance-levels.md)由吞吐量和已用存储确定。 容器可以跨一个或多个分区或服务器，并且能缩放以处理几乎无限制增长的存储或吞吐量。 

* 对于 SQL 和 MongoDB API 帐户，容器映射到集合。 
* 对于 Cassandra 和表 API 帐户，容器映射到表。 
* 对于 Gremlin API 帐户，容器映射到图表。 

容器也是 Azure Cosmos DB 的计费实体。 每个容器根据预配的吞吐量和已用的存储空间按小时计费。 有关详细信息，请参阅 [Azure Cosmos DB 定价](https://azure.microsoft.com/pricing/details/cosmos-db/)。 

### <a name="how-do-i-create-a-database"></a>我如何创建数据库？
可以按照[添加集合](create-sql-api-dotnet.md#create-collection)中所述使用 [Azure 门户](https://portal.azure.com)，使用某个 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 或使用 [REST API](/rest/api/cosmos-db/) 来创建数据库。 

### <a name="how-do-i-set-up-users-and-permissions"></a>我如何设置用户和权限？
可以使用某个 [Cosmos DB API SDK](sql-api-sdk-dotnet.md) 或 [REST API](/rest/api/cosmos-db/) 来创建用户和权限。  

### <a name="does-the-sql-api-support-sql"></a>SQL API 是否支持 SQL？
由 SQL API 帐户支持的 SQL 查询语言是 SQL Server 所支持的查询功能的增强子集。 Azure Cosmos DB SQL 查询语言通过基于 JavaScript 的用户定义函数 (UDF)，提供丰富的分层和关系运算符以及扩展性。 JSON 语法允许将 JSON 文档模型化为具有标签化节点的树状，由 Azure Cosmos DB 自动索引技术及 Azure Cosmos DB 的 SQL 查询方言使用。 有关使用 SQL 语法的信息，请参阅 [SQL 查询][query]一文。

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL API 是否支持 SQL 聚合函数？
SQL API 支持使用 SQL 语法通过聚合函数 `COUNT`、`MIN`、`MAX`、`AVG` 和 `SUM` 在任何规模中实现的低延迟聚合。 有关详细信息，请参阅[聚合函数](sql-api-sql-query.md#Aggregates)。

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API 如何提供并发？
SQL API 通过 HTTP 实体标记或 ETag 支持乐观并发控制 (OCC)。 每个 SQL API 资源都有一个 ETag，并且会在每次更新文档时在服务器上设置此 ETag。 ETag 标头和当前值会包含在所有响应消息中。 ETag 可与 If-Match 标头配合使用，让服务器能够决定是否应该更新资源。 If-Match 值是用作检查依据的 ETag 值。 如果 ETag 值与服务器的 ETag 值匹配，就会更新资源。 如果 ETag 不再是最新状态，则服务器会拒绝该操作，并提供“HTTP 412 前置条件失败”响应代码。 客户端接着必须重新提取资源，以获取该资源当前的 ETag 值。 此外，ETag 可以与 If-None-Match 标头配合使用，以确定是否需要重新提取资源。

若要在 .NET 中使用乐观并发，可使用 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 类。 相关 .NET 示例请参阅 GitHub 上 DocumentManagement 示例中的 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs)。

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>如何在 SQL API 中执行事务？
SQL API 通过 JavaScript 存储过程和触发器支持语言集成式事务。 脚本内的所有数据库操作均在快照隔离下执行。 如果是单分区集合，则在集合范围内执行。 如果集合已分区，则在集合内具有相同分区键值的文档下执行。 文档版本 (ETag) 的快照是在事务开始时获取的，且只有当脚本成功运行时才会提交。 如果 JavaScript 引发错误，则会回滚事务。 有关详细信息，请参阅 [Azure Cosmos DB 的服务器端 JavaScript 编程](programming.md)。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>如何将文档批量插入到 Document DB 中？
可以通过以下方法之一，将文档批量插入到 Azure Cosmos DB 中：

* 批量执行程序工具，如[使用批量执行程序 .NET 库](bulk-executor-dot-net.md)和[使用批量执行程序 Java 库](bulk-executor-java.md)中所述
* 数据集成工具，如 [Azure Cosmos DB 的数据迁移工具](import-data.md)中所述。
* 存储过程，如 [Azure Cosmos DB 的服务器端 JavaScript 编程](programming.md)中所述。

### <a name="i-have-setup-my-container-to-use-lazy-indexing-i-see-that-my-queries-do-not-return-expected-results"></a>我将自己的容器设置为使用延迟索引编制，结果发现查询没有返回预期的结果。 
如索引编制部分所述，延迟索引编制可能导致此行为。 应该始终对所有应用程序使用一致的索引编制。 


### <a name="does-the-sql-api-support-resource-link-caching"></a>SQL API 是否支持资源链接缓存？
是，因为 Azure Cosmos DB 是 RESTful 服务，而资源链接固定不变，所以可以缓存。 SQL API 客户端可以指定“If-None-Match”标头来读取任何资源，例如文档或集合，然后在服务器版本更改后更新其本地副本。

### <a name="is-a-local-instance-of-sql-api-available"></a>SQL API 的本地实例是否可用？
是的。 [Azure Cosmos DB 模拟器](local-emulator.md)提供对 Cosmos DB 服务的高保真模拟。 它支持与 Azure Cosmos DB 相同的功能，包括对创建和查询 JSON 文档、预配和缩放集合，以及执行存储过程和触发器的支持。 可以使用 Azure Cosmos DB 模拟器开发和测试应用程序，然后对 Azure Cosmos DB 的连接终结点进行单一配置更改，可以将这些应用程序部署到全局范围的 Azure。

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>当从门户中的数据资源管理器查看时，为何会对文档中的长浮点值进行舍入？ 
这是 JavaScript 的限制。 JavaScript 根据 IEEE 754 中的规定使用双精度浮点格式的数字，并且只能安全地呈现 -(253 - 1) 和 253-1（即 9007199254740991）之间的数字。

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>在对象层次结构中的何处启用权限？

可以在容器级别以及其下的级别（例如文档级别、附件级别）使用 ResourceTokens 来创建权限。 这意味着，目前不允许在数据库或帐户级别创建权限。


## <a name="develop-against-the-api-for-mongodb"></a>针对 API for MongoDB 进行开发
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>什么是 Azure Cosmos DB API for MongoDB？
Azure Cosmos DB API for MongoDB 是一个兼容层，使应用程序可以使用社区支持的现有 Apache MongoDB API 和驱动程序轻松透明地与本机 Azure Cosmos DB 数据库引擎进行通信。 现在，开发人员可以使用现有 MongoDB 工具链和技能构建利用 Azure Cosmos DB 的应用程序。 开发人员将受益于 Azure Cosmos DB 的独有功能，包括自动索引、备份维护、得到资金支持的服务级别协议 (SLA)，等等。

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>如何连接到 API for MongoDB 数据库？
连接到 Azure Cosmos DB API for MongoDB 的最快捷方法是前往 [Azure 门户](https://portal.azure.com)。 转到你的帐户，然后在左侧导航菜单上单击“快速启动”。 “快速启动”是获取代码片段以连接到数据库的最佳方式。 

Azure Cosmos DB 强制实施严格的安全要求和标准。 Azure Cosmos DB 帐户需要通过 SSL 进行身份验证和安全通信，因此请确保使用 TLSv1.2。

有关详细信息，请参阅[连接到 API for MongoDB 数据库](connect-mongodb-account.md)。

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>MongoDB 的 API 数据库是否有其他错误代码？
除了常见的 MongoDB 错误代码外，MongoDB API 还具有自己的特定错误代码：


| 错误               | 代码  | Description  | 解决方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用的请求单位总数超过了集合的预配请求单位率，已达到限制。 | 考虑从 Azure 门户中对分配给一个容器或一组容器的吞吐量进行缩放，或者重试。 |
| ExceededMemoryLimit | 16501 | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系支持人员。 <br><br>示例：*&nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api"></a>使用表 API 进行开发

### <a name="how-can-i-use-the-table-api-offering"></a>如何使用表 API 产品/服务？ 
[Azure 门户][azure-portal]中提供了 Azure Cosmos DB 表 API。 首先必须注册 Azure 订阅。 注册成功后，可将 Azure Cosmos DB 表 API 帐户添加到 Azure 订阅，然后向帐户添加表。 

可以在 [Azure Cosmos DB 表 API 简介](table-introduction.md)中找到支持的语言和相关的快速入门。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>是否需要新的 SDK 才能使用表 API？ 
不是，现有的存储 SDK 仍然适用。 但是，我们建议始终使用最新的 SDK，以获得最佳支持，并在许多场合下获得优异的性能。 请参阅 [Azure Cosmos DB 表 API 简介](table-introduction.md)中的可用语言列表。

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>表 API 与 Azure 表存储的行为有哪些不同之处？
想要使用 Azure Cosmos DB 表 API 创建表的 Azure 表存储用户应注意以下这些行为差异：

* Azure Cosmos DB 表 API 使用保留容量模型来保障性能，但这意味着，一旦创建了表，就必须立即支付容量费用，即使容量未被使用。 使用 Azure 表存储时，只需为实际使用的容量付费。 这也说明了，表 API 在 99% 的时间里为何能够提供 10 毫秒的读取延迟和 15 毫秒的写入延迟 SLA，而 Azure 表存储提供 10 秒延迟 SLA。 因此，使用表 API 表（即使是不带任何请求的空表）时，要达到 Azure Cosmos DB 所提供的 SLA，必须支付费用来确保提供所需的容量来处理对这些表发出的所有请求。
* 表 API 返回的查询结果未按分区键/行键顺序排序，因为它们在 Azure 表存储中。
* 行键最多只能包含 255 个字节
* 批最多只能包含 2 MB
* 目前不支持 CORS
* Azure 表存储中的表名不区分大小写，但出现在 Azure Cosmos DB 表 API 中
* Azure Cosmos DB 的某些编码信息内部格式，例如二进制字段，目前不如想像的那么有效。 因此，这会导致数据大小受到意外限制。 例如，目前无法使用整整有 1 MB 的表实体来存储二进制数据，因为编码会增大数据大小。
* 当前不支持实体属性名称“Id”
* TableQuery TakeCount 不限为 1000 以内

对于 REST API，有大量的终结点/查询选项不受 Azure Cosmos DB 表 API 的支持：
| REST 方法 | REST 终结点/查询选项 | 文档 URL | 说明 |
| ------------| ------------- | ---------- | ----------- |
| GET、PUT | /?restype=service@comp=properties| [设置表服务属性](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties)和[获取表服务属性](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | 此终结点用于设置 CORS 规则、存储分析配置和日志记录设置。 CORS 目前不受支持，Azure Cosmos DB 与 Azure 存储表中以不同的方式处理分析和日志记录 |
| OPTIONS | /<table-resource-name> | [预检 CORS 表请求](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | 这是 Azure Cosmos DB 目前不支持的 CORS 部分。 |
| GET | /?restype=service@comp=stats | [获取表服务统计信息](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | 提供有关主节点与辅助节点之间的数据复制速度的信息。 由于复制是写入的一部分，因此在 Cosmos DB 中不需要此选项。 |
| GET、PUT | /mytable?comp=acl | [获取表 ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) 和[设置表 ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | 获取和设置用于管理共享访问签名 (SAS) 的存储访问策略。 尽管支持 SAS，但其设置和管理方式不同。 |

此外，Azure Cosmos DB 表 API 仅支持 JSON 格式，而不支持 ATOM。

尽管 Azure Cosmos DB 支持共享访问签名 (SAS)，但它不支持某些策略，具体而言，是与管理操作相关的策略，例如创建新表的权限。

对于特定的 .NET SDK，Azure Cosmos DB 目前不支持某些类和方法。

| 类 | 不支持的方法 |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | *（实际上此类已弃用） |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

如果其中的任何差异会给项目造成问题，请通过 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) 联系并告诉我们。

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>如何提供有关 SDK 的反馈或 Bug？
可以通过以下任一方式分享反馈：

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow 适合编程问题。 请确保提问[切中主题](https://stackoverflow.com/help/on-topic)并[尽可能提供较多的详细信息，使问题清楚明了，便于回答](https://stackoverflow.com/help/how-to-ask)。

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>连接到表 API 需要使用哪个连接字符串？
连接字符串为：
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```
可以通过 Azure 门户中的“连接字符串”页获取连接字符串。 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>如何在表 API 的 .NET SDK 中替代请求选项的配置设置？
有关配置设置的信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。 有些设置是通过 CreateCloudTableClient 方法处理的，还有一些设置是通过客户端应用程序中 appSettings 节的 app.config 处理的。

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>使用现有 Azure 表存储 SDK 的客户是否需要进行任何更改？
无。 使用现有 Azure 表存储 SDK 的现有客户或新客户无需进行任何更改。 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>如何查看 Azure Cosmos DB 中存储的用于表 API 的表数据？ 
可以使用 Azure 门户浏览此数据。 也可以使用表 API 代码或下一个问题答案中提到的工具。 

### <a name="which-tools-work-with-the-table-api"></a>哪些工具适用于表 API？ 
可以使用 [Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)。

灵活地采用之前指定格式的连接字符串的工具可以支持新的表 API。 [Azure 存储客户端工具](../storage/common/storage-explorers.md)页上提供了表工具的列表。 

### <a name="is-the-concurrency-on-operations-controlled"></a>是否能控制并发操作？
是，乐观并发是通过使用 ETag 机制提供的。 

### <a name="is-the-odata-query-model-supported-for-entities"></a>实体是否支持 OData 查询模型？ 
是，表 API 支持 OData 查询和 LINQ 查询。 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>是否可以在同一应用程序中同时连接到 Azure 表存储和 Azure Cosmos DB 表 API？ 
是，可以创建 CloudTableClient 的两个不同实例并使其通过连接字符串指向其自身的 URI 来实现此目的。

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>如何将现有 Azure 表存储应用程序迁移到此服务？
支持使用 [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 和 [Azure Cosmos DB 数据迁移工具](import-data.md)。

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>如何为此服务扩展存储大小，比如，最初我有 *n* GB 的数据，但一段时间后我的数据会增长到 1 TB？ 
Azure Cosmos DB 旨在通过横向缩放提供无限的存储。 该服务可以监视并有效地增加存储。 

### <a name="how-do-i-monitor-the-table-api-offering"></a>如何监视表 API 服务？
可以使用表 API 的“指标”窗格来监视请求和存储使用情况。 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>如何计算所需的吞吐量？
可以使用容量估算器计算操作所需的 TableThroughput。 有关详细信息，请参阅[估算请求单位和数据存储](https://www.documentdb.com/capacityplanner)。 通常，可以将实体表示为 JSON 并且为操作提供所需数量。 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>是否可以在本地将表 API SDK 用于模拟器？
现在不行。

### <a name="can-my-existing-application-work-with-the-table-api"></a>现有的应用程序是否适用于表 API？ 
是的，支持相同的 API。

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>如果我不想使用表 API 功能，是否需要将现有 Azure 表存储应用程序迁移到该 SDK？
否，可以在没有任何干扰的情况下创建和使用现有 Azure 表存储资产。 但是，如果不使用表 API，则无法从自动索引、其他一致性选项或全局分发中受益。 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>如何在跨多个 Azure 区域的表 API 中添加数据复制？
可以使用 Azure Cosmos DB 门户的[全局复制设置](tutorial-global-distribution-sql-api.md#portal)添加适合应用程序的区域。 若要开发全球分布的应用程序，还应该将应用程序的 PreferredLocation 信息设置为本地区域，以实现较低的读取延迟。 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>如何在表 API 中更改帐户的主要写入区域？
可以使用 Azure Cosmos DB 的全局复制门户窗格添加区域，然后故障转移到所需区域。 有关说明，请参阅[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)。 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>在分发数据时，如何配置首选读取区域以实现较低的延迟？ 
可使用 app.config 文件中的 PreferredLocation 键，帮助从本地位置进行读取。 对于现有应用程序，如果设置 LocationMode，表 API 会引发错误。 请删除该代码，因为表 API 会从 app.config 文件中选取此信息。 有关详细信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>如何理解表 API 中的一致性级别？ 
Azure Cosmos DB 在一致性、可用性和延迟之间提供合理的平衡。 Azure Cosmos DB 为表 API 开发人员提供五个一致性级别，因此可以在表级别选择合适的一致性模型，并在查询数据时发出相应的请求。 客户端建立连接后，可以指定一致性级别。 可以通过 CreateCloudTableClient 的 consistencyLevel 参数更改级别。 

如果将“有限过时”一致性设置为默认值，表 API 可通过“读取自己写入的数据”提供低延迟的读取。 有关详细信息，请参阅[一致性级别](consistency-levels.md)。 

默认情况下，Azure 表存储在区域内提供非常一致性，在辅助位置中提供最终一致性。 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB 表 API 是否比 Azure 表存储提供更多的一致性级别？
是，有关如何受益于 Azure Cosmos DB 的分布式特性的信息，请参阅[一致性级别](consistency-levels.md)。 由于为一致性级别提供了保障，因此可以放心使用它们。 有关详细信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>启用全局分发后，完成数据的复制需要多久？
Azure Cosmos DB 会在本地区域持续提交数据，然后在几毫秒内将数据立即推送到其他区域。 此复制仅依赖于数据中心的往返时间 (RTT)。 若要详细了解 Azure Cosmos DB 的全局分发功能，请参阅 [Azure Cosmos DB：Azure 上的全球分布式数据库服务](distribute-data-globally.md)。

### <a name="can-the-read-request-consistency-level-be-changed"></a>是否可以更改读取请求一致性级别？
使用 Azure Cosmos DB 时，可以在容器级别（在表上）设置一致性级别。 使用 .NET SDK，可以通过在 app.config 文件中提供 TableConsistencyLevel 键值来更改级别。 可能的值包括：非常、有限过期、会话、一致前缀和最终。 有关详细信息，请参阅 [Azure Cosmos DB 中的可优化数据一致性级别](consistency-levels.md)。 关键是不能将请求的一致性级别设置为高于表的一致性级别。 例如，不能将表的一致性级别设置为“最终”，而将请求的一致性级别设置为“非常”。 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>在某个区域出现故障时，表 API 如何处理故障转移？ 
表 API 利用 Cosmos DB 的全球分布式平台。 若要确保应用程序能够容许数据中心停机，需在 Azure Cosmos DB 门户中至少再为帐户启用一个区域（[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)）。 可以通过使用门户设置区域的优先级（[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)）。 

可为帐户添加任意数目的区域，并通过提供故障转移优先级控制可将帐户故障转移到的区域。 当然，还需要在帐户中提供一个应用程序，以利用数据库。 这样，客户就不会遇到停机情况。 [最新的 .NET 客户端 SDK](table-sdk-dotnet.md)可自动寻址，但其他 SDK 则不可以。 也就是说，它能够检测到有故障的区域，并自动故障转移到新区域。

### <a name="is-the-table-api-enabled-for-backups"></a>是否能够为表 API 启用备份？
可以，表 API 利用 Azure Cosmos DB 的平台进行备份。 可自动创建备份。 有关详细信息，请参阅[使用 Azure Cosmos DB 联机备份和还原](online-backup-and-restore.md)。

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>表 API 是否默认对实体的所有属性编制索引？
是，默认情况下会对实体的所有属性进行索引。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](indexing-policies.md)。 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>这是否意味着无需创建多个索引即可满足查询要求？ 
是，Azure Cosmos DB 表 API 针对所有属性提供自动索引，无需任何架构定义。 此自动化功能使开发人员能够将重心放在应用程序上，而不必担心索引的创建和管理。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](indexing-policies.md)。

### <a name="can-i-change-the-indexing-policy"></a>是否可以更改索引策略？
是，可以提供索引定义来更改索引策略。 有关详细信息，请参阅 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)。 需要适当地将这些设置编码并转义。 

对于非 .NET SDK，只能在门户中设置索引策略，方法是：打开“数据资源管理器”，导航到想要更改的特定表，转到“缩放和设置”->“索引策略”，进行所需的更改，并单击“保存”。

对于 .NET SDK，可以在 app.config 文件中提交更改：
```
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        } 
      ]
    }
  ],
  "excludedPaths": 
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>平台形式的 Azure Cosmos DB 似乎拥有许多功能，例如排序、聚合、分层和其他功能。 是否会将这些功能添加到表 API 中？ 
表 API 提供与 Azure 表存储相同的查询功能。 Azure Cosmos DB 还支持排序、聚合、地理空间查询、层次结构和各种内置函数。 在将来的服务更新中，我们会在表 API 中提供更多的功能。 有关详细信息，请参阅 [SQL 查询](sql-api-sql-query.md)。
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>何时应更改表 API 的 TableThroughput？
符合以下任一情况时，都应更改 TableThroughput：
* 要执行数据提取、转换和加载 (ETL) 操作，或者想在短时间内上传大量数据。 
* 需要后端的容器或容器组提供更大的吞吐量。 例如，发现已用吞吐量超过预配吞吐量，且吞吐量已达到限制。 有关详细信息，请参阅[为 Azure Cosmos DB 容器设置吞吐量](set-throughput.md)。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>是否可以提高或降低表 API 表的吞吐量？ 
是，可以使用 Azure Cosmos DB 门户的缩放窗格来缩放吞吐量。 有关详细信息，请参阅[设置吞吐量](set-throughput.md)。

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>是否为新预配的表设置了默认 TableThroughput？
是，如果未通过 app.config 替代 TableThroughput，并且未使用 Azure Cosmos DB 中预创建的容器，服务则会创建吞吐量为 400 的表。
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>对于 Azure 表存储服务的现有客户，定价是否有任何变化？
无。 对于现有的 Azure 表存储客户，价格上没有任何更改。 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>表 API 的价格是如何计算的？ 
价格取决于分配的 TableThroughput。 

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>如何在表 API 服务中处理对表设置的任何速率限制？ 
如果请求速率超出了为基础容器或容器组预配的吞吐量的容量，则会出现错误，SDK 会使用重试策略重试调用。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>为何需要选择吞吐量而不是 PartitionKey 和 RowKey 来利用 Azure Cosmos DB 的表 API 服务？
如果未在 app.config 文件中或通过门户提供吞吐量，Azure Cosmos DB 将为容器设置默认的吞吐量。 

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 如果引擎可以针对租户的操作实施调控，则可以做到这一点。 设置 TableThroughput 可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。 

通过使用吞吐量规范，可以弹性更改吞吐量，以利用应用程序的季节性，满足吞吐量需求并节省成本。

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure 表存储对我而言非常便宜，因为我只需支付数据的存储费用，并且我很少进行查询。 但是，即使我未执行任何事务或存储任何数据，Azure Cosmos DB 表 API 服务似乎也要收费。 这是怎么回事？

Azure Cosmos DB 设计为一个全球分布的、基于 SLA 的系统，在可用性、延迟和吞吐量方面提供保障。 在 Azure Cosmos DB 中保留吞吐量时，获得的保障与其他系统不同。 Azure Cosmos DB 还提供客户长期以来一直期盼的其他功能，例如辅助索引和全局分发。  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>在向 Azure 表存储引入数据时，我从未收到过“配额已满”通知（指示分区已满）。 但使用表 API 时会收到此消息。 是此产品有限制，迫使我更改现有的应用程序吗？

Azure Cosmos DB 是基于 SLA 的系统，可提供无限缩放，并在延迟、吞吐量、可用性和一致性方面提供保障。 为了确保获得有保障的高级性能，需确保数据大小和索引可管理且可缩放。 我们为每个分区键的实体或项数实施 10 GB 限制，以确保能提供强大的查找和查询性能。 若要确保即使针对 Azure 存储，应用程序也能很好地进行缩放，建议*不要*创建热分区，即，将所有信息存储在一个分区内并查询它。 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>表 API 是否仍然需要 PartitionKey 和 RowKey？ 
是的。 由于表 API 的外围应用类似于 Azure 表存储 SDK，因此使用分区键可以高效地分发数据。 行键在该分区中是唯一的。 需要存在行键且它不能为 null（在标准 SDK 中可为 null）。 RowKey 的长度为 255 个字节，PartitionKey 的长度为 1 KB。 

### <a name="what-are-the-error-messages-for-the-table-api"></a>表 API 的错误消息有哪些？
Azure 表存储和 Azure Cosmos DB 表 API 使用相同的 SDK，因此，大多数错误是相同的。

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>在表 API 中尝试一个接一个地创建许多表时，为何会受到限制？
Azure Cosmos DB 是基于 SLA 的系统，在可用性、延迟和吞吐量方面提供保障。 由于它是预配的系统，因此会保留资源来保证满足这些要求。 以较快的速率创建表会被系统检测到并受到限制。 建议查看表创建速率，将其降至每分钟不超过 5 个。 请记住，表 API 是预配的系统。 只要预配它，就必须付费。 

## <a name="develop-against-the-graph-api"></a>针对图形 API 进行开发
### <a name="how-can-i-apply-the-functionality-of-graph-api-to-azure-cosmos-db"></a>如何向 Azure Cosmos DB 应用图形 API 的功能？
可以使用扩展库来应用图形 API 的功能。 此库称为 Microsoft Azure Graphs，在 NuGet 中提供。 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>你们似乎支持 Gremlin 图形遍历语言。 是否计划添加更多查询形式？
是，我们计划将来添加其他查询机制。 

### <a name="how-can-i-use-the-new-graph-api-offering"></a>如何使用新的图形 API 服务？ 
若要开始使用，请先阅读[图形 API](../cosmos-db/create-graph-dotnet.md) 快速入门文章。

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>使用 Apache Cassandra API（预览版）进行开发

### <a name="what-is-the-protocol-version-supported-in-the-private-preview-is-there-a-plan-to-support-other-protocols"></a>个人预览版支持哪个协议版本？ 是否打算支持其他协议？
Azure Cosmos DB 的 Apache Cassandra API 目前支持 CQL 版本 4。 如果有与支持其他协议相关的反馈，请通过 [UserVoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)告知我们或者向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。 

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>为何要求选择表的吞吐量？
Azure Cosmos DB 根据表的创建位置（门户或 CQL）设置容器的默认吞吐量。 Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 如果引擎可以针对租户的操作实施调控，则可以做到这一点。 设置吞吐量可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。 可以弹性更改吞吐量以便使季节性应用受益并节省成本。

[Azure Cosmos DB 中的请求单位数](request-units.md)一文介绍了吞吐量概念。 表的吞吐量平均分布到各个基础物理分区中。  

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>通过 CQL 创建的表的默认吞吐量是多少 RU/s？ 如何更改该默认值？
Azure Cosmos DB 使用每秒请求单位数 (RU/s) 作为所提供的吞吐量的单位。 通过 CQL 创建的表具有 400 RU。 可以通过门户更改 RU。 

CQL
```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET
```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload); 
``` 

### <a name="what-happens-when-throughput-is-exceeded"></a>超出吞吐量时，会发生什么情况？ 
Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 如果引擎可以针对租户的操作实施调控，则可以做到这一点。 这可以通过设置吞吐量来实现，并可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。 当超出此容量时，会收到超载错误消息，指出已超出了容量。 0x1001 超载：无法处理此请求，因为“请求速率太大”。 此时，必须查明是哪些操作及其数据量导致了此问题。 可以通过门户中的指标了解超出了预配容量的已消耗容量。 然后，你需要确保容量差不多是在所有基础分区中平均消耗的。 如果你看到大多数吞吐量是由一个分区消耗的，则说明存在工作负载倾斜。 

相关指标显示了吞吐量在若干小时内、若干天内以及每七天内在各个分区中的使用情况或总体使用情况。 有关详细信息，请参阅[使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)。

[Azure Cosmos DB 诊断日志记录](logging.md)一文中介绍了诊断日志。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>主键是否映射到 Azure Cosmos DB 的分区键概念？
是，分区键用来将实体放置在正确位置。 在 Azure Cosmos DB 中，它用来查找存储在物理分区中的正确逻辑分区。 [在 Azure Cosmos DB 中分区和缩放](partition-data.md)一文中很好地解释了分区概念。 此处，必须记住的一点是，逻辑分区目前不应当超出 10 GB 限制。 

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>当收到指示分区已满的“配额已满”通知时，会发生什么情况？
Azure Cosmos DB 是基于 SLA 的系统，可提供无限缩放，并在延迟、吞吐量、可用性和一致性方面提供保障。 它的 Cassandra API 也允许无限制的数据存储。 此无限制的存储是通过使用分区作为键概念的数据水平横向扩展实现的。 [在 Azure Cosmos DB 中分区和缩放](partition-data.md)一文中很好地解释了分区概念。

应当遵循每个逻辑分区的实体数或项数不超过 10 GB 的限制。 为确保应用程序能够很好地进行缩放，建议*不要*创建热分区，即，将所有信息存储在一个分区内并查询它。 只有当存在数据倾斜时，也就是说，当一个分区键有大量数据（即超过 10 GB）时，才会发生此错误。 可以使用存储门户查明数据的分布。 若要修复此错误，请重新创建表并选择一个细粒度的主键（分区键），这可以实现更好的数据分布。

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>是否可以将 Cassandra API 作为键值存储用于数百万或数十亿单独的分区键？
Azure Cosmos DB 可以通过对存储进行横向扩展来存储无限的数据。 这与吞吐量无关。 你始终可以仅使用 Cassandra API 通过指定正确的主键/分区键来存储和检索键/值。 这些单独的键获取其自己的逻辑分区并存在于物理分区之上，且不会出现问题。 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>是否可以使用 Azure Cosmos DB 的 Apache Cassandra API 创建多个表？
是，可以使用 Apache Cassandra API 创建多个表。 这些表中的每一个都被视为用于吞吐量和存储的单元。 

### <a name="is-it-possible-to-create-multiple-tables-in-succession"></a>是否可以连续创建多个表？
Azure Cosmos DB 是资源调控的系统，适用于数据和控制平面活动。 与集合和表一样，容器是针对给定的吞吐量容量预配的运行时实体。 快速连续创建这些容器是非预期的活动，会被限制。 如果存在立即删除/创建表的测试，请尽量将它们隔开。

### <a name="what-is-maximum-number-of-tables-which-can-be-created"></a>最多可以创建几个表？
表数目没有物理限制，如果需要创建数量很多（远远超过平常的数十个或数百个）的表（其中，稳定的总大小超过 10 TB 数据），请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。 

### <a name="what-is-the-maximum--of-keyspace-which-we-can-create"></a>最多可以创建多少个键空间？ 
键空间数目没有物理限制，因为它们是元数据容器，如果由于某种原因，你有大量的键空间，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。 

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>从普通的表启动后，是否可以引入大量数据？ 
存储容量是自动管理的，推入更多的数据时，存储容量将会增大。 因此，您可以放心地导入所需数量的数据，不需要管理和预配节点等等。 

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>是否可以提供 yaml 文件设置来配置 Azure Cosmos DB 的 Apache Casssandra API 行为？
Azure Cosmos DB 的 Apache Cassandra API 是一项平台服务。 它提供协议级的兼容性来执行操作。 它消除了管理、监视和配置的复杂性。 开发人员/用户无需担心可用性、逻辑删除、键缓存、行缓存、布隆筛选器和其他许多设置。 Azure Cosmos DB 的 Apache Cassandra API 注重于提供所需的读取和写入性能，不会产生配置和管理开销。

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Azure Cosmos DB 的 Apache Cassandra API 是否支持节点添加/群集状态/节点状态命令？
Apache Cassandra API 是一项平台服务，使用它可以轻松做出容量规划，以及应对吞吐量和存储方面的弹性需求。 借助 Azure Cosmos DB，可以预配所需的吞吐量。 然后，可以在一整天中多次上调和下调吞吐量，而无需担心如何添加/删除或管理节点。 这意味着也不需要使用节点、群集管理工具。 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>有关创建键空间的各项配置设置（例如简单/网络）会是怎样的？
出于可用性和低延迟的原因，Azure Cosmos DB 提供现成的全局分发。 不需要设置副本等等。所有写入均在你进行写入的区域中经过仲裁永久认可，同时提供性能保障。  

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-etc"></a>布隆筛选器、缓存、读取修复更改、gc_grace 和 compression memtable_flush_period 等表元数据的各项设置有什么变化？
Azure Cosmos DB 为读/写操作提供性能和吞吐量，无需更改任何配置设置，也不会意外处理这些设置。  

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Cassandra 表是否支持生存时间 (TTL)？ 
是的，支持 TTL。 

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>以前是否可以使用各种工具监视节点状态、副本状态、gc 和 OS 参数？ 现在需要监视什么？
Azure Cosmos DB 是一个平台服务，可帮助你提高工作效率，而无需担心如何管理和监视基础结构。 你只需要在门户指标中关注可用的吞吐量，以查明你是否受到限制并增大或减小该吞吐量。 监视 [SLA](monitor-accounts.md)。
使用[指标](use-metrics.md) 使用[诊断日志](logging.md)。

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>哪些客户端 SDK 适用于 Azure Cosmos DB 的 Apache Cassandra API？
在个人预览版中，Apache Cassandra SDK 的使用 CQLv3 的客户端驱动程序用于客户端程序。 如果你使用其他驱动程序或者遇到问题，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送邮件。 

### <a name="is-composite-partition-key-supported"></a>是否支持复合分区键？
是的，可以使用正则语法创建复合分区键。 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>是否可以使用 sstable 加载程序加载数据？
否，预览版不支持 sstable 加载程序。 

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>本地 Cassandra 群集是否可与 Azure Cosmos DB 的 Apache Cassandra API 配对？
目前，Azure Cosmos DB 针对云环境提供了优化的体验，且不产生操作开销。 如果需要配对，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送邮件并在其中提供你的方案的说明。

### <a name="does-cassandra-api-provide-full-backups"></a>Cassandra API 是否提供完整备份？ 
Azure Cosmos DB 的所有 API 目前都提供间隔四小时的两个免费完整备份。 这可确保你不需要设置备份计划等等。如果希望修改保留期和频率，请向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送邮件或提交支持案例。 [Azure Cosmos DB 的自动联机备份和还原](online-backup-and-restore.md)一文中提供了有关备份功能的信息。 

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>当某个区域出现故障时，Cassandra API 帐户如何处理故障转移？ 
Azure Cosmos DB Cassandra API 借助 Azure Cosmos DB 的全球分布式平台。 若要确保应用程序能够容许数据中心停机，需在 Azure Cosmos DB 门户中至少再为帐户启用一个区域（[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)）。 可以通过使用门户设置区域的优先级（[使用多区域 Azure Cosmos DB 帐户进行开发](regional-failover.md)）。 

可为帐户添加任意数目的区域，并通过提供故障转移优先级控制可将帐户故障转移到的区域。 若要使用数据库，还需要在那里提供一个应用程序。 这样，客户就不会遇到停机情况。 

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API 是否默认对实体的所有属性编制索引？
是，默认情况下 Azure Cosmos DB 会对实体的所有属性进行索引。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](indexing-policies.md)。 这样始终可以获得有保障的性能，以及一致的索引和持久的仲裁提交写入。 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>这是否意味着无需创建多个索引即可满足查询要求？ 
是，Azure Cosmos DB 针对所有属性提供自动索引，无需任何架构定义。 此自动化功能使开发人员能够将重心放在应用程序上，而不必担心索引的创建和管理。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](indexing-policies.md)。

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>是否可以在本地将新的 Cassandra API SDK 用于模拟器？
我们计划在将来支持此功能。 

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-changefeed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>平台形式的 Azure Cosmos DB 似乎拥有许多功能，例如，更改源和其他功能。 这些功能是否将添加到 Cassandra API 中？ 
Apache Cassandra API 提供了与 Apache Cassandra 相同的 CQL 功能。 我们确实打算研究在将来支持各种功能的可行性。

### <a name="feature-x-of-regular-cassandra-api-is-not-working-as-today-where-can-the-feedback-be-provided"></a>常规 Cassandra API 的功能 x 目前不工作，可以在哪里提供反馈？
请通过 [uservoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)提供反馈。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
