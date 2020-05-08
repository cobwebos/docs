---
title: 有关 Azure Cosmos DB 中不同 API 的常见问题解答
description: 获取与 Azure Cosmos DB（一种全球分布式多模型数据库服务）相关的常见问题答案。 了解容量、性能级别和缩放。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 976ff4d3be194bf33d2c01f06c66782caf8dad50
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608041"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>有关 Azure Cosmos DB 中不同 API 的常见问题

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB 的典型用例有哪些？

对于侧重于以下要求的新 Web、移动、游戏和 IoT 应用程序而言，Azure Cosmos DB 是一个不错的选择：自动缩放、可预测的性能、毫秒响应时间的快速排序，以及查询无架构数据的能力。 Azure Cosmos DB 有助于快速开发，且支持应用程序数据模型的连续迭代。 用于管理用户生成的内容和数据的应用程序就是 [Azure Cosmos DB 的常见用例](use-cases.md)。

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB 如何提供可预测的性能？

[请求单位](request-units.md) (RU) 是 Azure Cosmos DB 中吞吐量的衡量单位。 1RU 吞吐量相当于通过 GET 操作获取 1 KB 文档的吞吐量。 在 Azure Cosmos DB 中进行的每个操作（包括读、写、SQL 查询和执行存储过程）都具有一个确定的 RU 值，该值基于完成该操作所需的吞吐量。 无需考虑 CPU、IO 和内存，以及它们会怎样影响应用程序吞吐量，只需从单个 RU 度量值的角度进行考虑即可。

可以按照每秒吞吐量的 RU 数来配置每个 Azure Cosmos 容器的预配吞吐量。 对于任何规模的应用程序，都可以通过将单个请求设为基准来测量其 RU 值，并通过预配容器来处理所有请求的请求单位总和。 也可以随着应用程序的发展需求，扩展或缩减容器的吞吐量。 如需请求单位的详细信息以及帮助确定容器需求，请尝试使用[吞吐量计算器](https://www.documentdb.com/capacityplanner)。

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB 如何支持各种数据模型（例如键/值、纵栏表、文档和图形）？

键/值（表）、纵栏表、文档和图形数据模型都是原本就支持的，因为 Azure Cosmos DB 是基于 ARS（原子、记录和序列）设计构建的。 原子、记录和序列可以轻松映射和投影到各种数据模型。 现在提供了适用于一个模型子集的 API（SQL、MongoDB、表和 Gremlin），并且将来会提供特定于其他数据模型的其他 API。

Azure Cosmos DB 有一个不受架构影响的索引编制引擎，能够自动为它引入的所有数据编制索引，不会要求开发者提供任何架构或辅助索引。 该引擎依赖于一组逻辑索引布局（倒置、纵栏表、树形），这些布局将存储布局从索引和查询处理子系统中分离出来。 Cosmos DB 还能够以可扩展方式支持一组连网协议和 API 并将它们高效地转换为核心数据模型 (1) 和逻辑索引布局 (2)，这使得它具有原本就支持多个数据模型的独特功能。

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>是否可以使用多个 API 来访问我的数据？

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 其中多模型意味着 Azure Cosmos DB 支持多个 API 和多个数据模型，不同的 API 将不同的数据格式用于存储和线路协议。 例如，SQL 使用 JSON，MongoDB 使用 BSON，表使用 EDM，Cassandra 使用 CQL，Gremlin 使用 JSON 格式。 因此，建议使用相同的 API 对给定帐户中的数据进行所有访问。

除了可互操作的 Gremlin 和 SQL API，每个 API 都独立运行。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB 是否符合 HIPAA？

是的，Azure Cosmos DB 符合 HIPAA。 HIPAA 针对可识别个人身份的健康信息的使用、泄露与保护制定了要求。 有关详细信息，请参阅[Microsoft 信任中心](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的存储限制是什么？

对于容器可以存储在 Azure Cosmos DB 中的数据总量并没有任何限制。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的吞吐量限制是什么？

对于 Azure Cosmos DB 中容器可以支持的吞吐量总量并没有任何限制。 关键是要让工作负荷大致均匀地分布到足够多的分区键中。

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>直接连接和网关连接模式是否加密？

是的，两种模式始终完全加密。

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB 的费用如何？

有关详细信息，请参阅 [Azure Cosmos DB 定价详细信息](https://azure.microsoft.com/pricing/details/cosmos-db/)页。 Azure Cosmos DB 使用费取决于预配的容器数、容器的联机小时数，以及每个容器的预配吞吐量。

### <a name="is-a-free-account-available"></a>有免费的帐户吗？

是的，可免费注册一个限时的帐户，且无需任何承诺费。 若要注册，请访问[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 或参阅 [Azure Cosmos DB 试用常见问题解答](#try-cosmos-db)了解详细信息。

如果你不熟悉 Azure，可以注册 [Azure 免费帐户](https://azure.microsoft.com/free/)，这样可以得到 30 天试用期和信用额度，让你试用所有 Azure 服务。 如果你有 Visual Studio 订阅，则还有资格[免费获取 Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用于任何 Azure 服务。

也可以使用 [Azure Cosmos DB 模拟器](local-emulator.md)在本地免费开发和测试应用程序，无需创建 Azure 订阅。 如果对应用程序在 Azure Cosmos DB 模拟器中的工作情况感到满意，则可以切换到在云中使用 Azure Cosmos DB 帐户。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>如何获取 Azure Cosmos DB 的更多帮助？

若要询问技术问题，可以在下述两个问答论坛之一发帖：

* [MSDN 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow 适合编程问题。 请确保提问[切中主题](https://stackoverflow.com/help/on-topic)并[尽可能提供较多的详细信息，使问题清楚明了，便于回答](https://stackoverflow.com/help/how-to-ask)。

若要请求新功能，请在 [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db) 上提交新的请求。

若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>试用 Azure Cosmos DB 订阅

现在无需订阅和承诺，即可限时免费体验 Azure Cosmos DB。 若要注册试用 Azure Cosmos DB 订阅，请参阅[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)并使用任何个人 MICROSOFT 帐户（MSA）。 此订阅独立于 [Azure 免费试用版](https://azure.microsoft.com/free/)，可在使用 Azure 免费试用版或 Azure 付费订阅的同时进行使用。

“试用 Azure Cosmos DB”订阅显示在 Azure 门户中与用户 ID 关联的其他订阅旁。

以下条件适用于“试用 Azure Cosmos DB”订阅：

* 可以将帐户访问权限授予个人 Microsoft 帐户（MSA）。 避免使用 Active Directory （AAD）帐户或属于公司 AAD 租户的帐户，它们可能会阻止访问权限。
* 针对 SQL、Gremlin API 和表帐户，每个订阅[预配了一个吞吐量容器](./set-throughput.md#set-throughput-on-a-container)。
* 对于 MongoDB 帐户，每个订阅最多可[预配](./set-throughput.md#set-throughput-on-a-container)三个吞吐量。
* 为每个订阅[预配](./set-throughput.md#set-throughput-on-a-database)了一个吞吐量。 已预配吞吐量的数据库可以包含任意数量的容器。
* 10-GB 存储容量。
* 全球复制面向后列 [Azure 区域](https://azure.microsoft.com/regions/)提供：美国中部、北欧和东南亚
* 在容器级别预配时，最大吞吐量为 5 K RU/s。
* 在数据库级别预配时，最大吞吐量为 20 K RU/s。
* 订阅将在30天后过期，最多可以扩展到31天。
* 不能为“试用 Azure Cosmos DB”帐户创建 Azure 支持票据；但会为拥有现有支持计划的订阅者提供支持。

## <a name="set-up-azure-cosmos-db"></a>设置 Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>如何注册 Azure Cosmos DB？

可以在 Azure 门户中注册 Azure Cosmos DB。 首先，注册 Azure 订阅。 注册后，可将 Azure Cosmos DB 帐户添加到 Azure 订阅。

### <a name="what-is-a-master-key"></a>什么是主密钥？

主密钥是用于访问帐户的所有资源的安全令牌。 拥有此密钥的人对数据库帐户中的所有资源具有读取和写入访问权。 分发主密钥时需谨慎。 [Azure 门户][azure-portal]的“密钥”边栏选项卡中提供主要主密钥和辅助主密钥。  有关密钥的详细信息，请参阅[查看、复制和重新生成访问密钥](manage-with-cli.md#list-account-keys)。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>可以将 PreferredLocations 设置为哪些区域？

可以将 PreferredLocations 值设置为提供 Cosmos DB 的任何 Azure 区域。 有关可用区域的列表，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>通过 Azure 数据中心在全球范围内分发数据时是否需要注意什么？

如 [Azure 区域](https://azure.microsoft.com/regions/)页面中指定的那样，Azure Cosmos DB 遍布于所有 Azure 区域。 由于它属于核心服务，因此每个新的数据中心都有 Azure Cosmos DB。

设置区域时，请记住，Azure Cosmos DB 遵从主权和政府云的要求。 也就是说，如果在[主权区域](https://azure.microsoft.com/global-infrastructure/)中创建帐户，则不能从该[主权区域](https://azure.microsoft.com/global-infrastructure/)中复制。 同样，也无法通过外部帐户启用到其他主权位置的复制。

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>是否可以从容器级吞吐量预配切换到数据库级吞吐量预配？ 或者反之亦然？

容器级和数据库级吞吐量预配是不同的产品，在这两者之间切换需要将数据从源迁移到目标。 这意味着你需要创建新数据库或新容器，然后使用[批量执行程序库](bulk-executor-overview.md)或 [Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)迁移数据。

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB 是否支持时序分析？

是的，Azure CosmosDB 支持时序分析，下面是[时序模式](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns)的示例。 此示例显示如何使用更改源来构建时序数据的聚合视图。 可以使用 Spark 流式处理或其他流数据处理器来扩展此方法。

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>什么是 Azure Cosmos DB 服务配额和吞吐量限制

有关详细信息，请参阅 Azure Cosmos DB [服务配额](concepts-limits.md)和[每个容器和数据库的吞吐量限制](set-throughput.md#comparison-of-models)文章。

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>有关 SQL API 的常见问题

### <a name="how-do-i-start-developing-against-the-sql-api"></a>如何开始针对 SQL API 进行开发？

首先必须注册 Azure 订阅。 注册 Azure 订阅后，即可将 SQL API 容器添加到 Azure 订阅。 有关添加 Azure Cosmos DB 帐户的说明，请参阅[创建 Azure Cosmos 数据库帐户](create-sql-api-dotnet.md#create-account)。

[SDK](sql-api-sdk-dotnet.md) 适用于 .NET、Python、Node.js、JavaScript 和 Java。 开发人员也可以利用 [RESTful HTTP API](/rest/api/cosmos-db/)，从各种平台使用各种语言与 Azure Cosmos DB 资源进行交互。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>是否可以访问一些现成的示例来帮助自己入门？

GitHub 上提供了 SQL API [.NET](sql-api-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](sql-api-nodejs-samples.md) 和 [Python](sql-api-python-samples.md) SDK 的示例。

### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API 数据库是否支持无架构数据？

是的，SQL API 允许应用程序存储任意 JSON 文档，不需要架构定义或提示。 通过 Azure Cosmos DB SQL 查询接口可立即查询数据。

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API 是否支持 ACID 事务？

是的，SQL API 支持以 JavaScript 存储过程和触发器形式表示的跨文档事务。 事务以每个容器内的单个分区为范围，且以 ACID 语义执行，也就是“全有或全无”，与其他并发执行的代码和用户请求隔离。 如果在服务器端执行 JavaScript 应用程序代码的过程中引发了异常，则会回滚整个事务。 

### <a name="what-is-a-container"></a>什么是容器？

容器是文档及其关联的 JavaScript 应用程序逻辑的组。 容器是一个计费实体，其[成本](performance-levels.md)由吞吐量和已用存储确定。 容器可以跨一个或多个分区或服务器，并且能缩放以处理几乎无限制增长的存储或吞吐量。

* 对于 SQL API，容器映射到容器。
* 对于 Cosmos DB 的 API for MongoDB 帐户，容器映射到集合。
* 对于 Cassandra 和表 API 帐户，容器映射到表。
* 对于 Gremlin API 帐户，容器映射到图表。

容器也是 Azure Cosmos DB 的计费实体。 每个容器根据预配的吞吐量和已用的存储空间按小时计费。 有关详细信息，请参阅 [Azure Cosmos DB 定价](https://azure.microsoft.com/pricing/details/cosmos-db/)。

### <a name="how-do-i-create-a-database"></a>我如何创建数据库？

可以使用 [Azure 门户](https://portal.azure.com)（详见[添加容器](create-sql-api-java.md#add-a-container)中所述）、某个 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 或 [REST API](/rest/api/cosmos-db/) 来创建数据库。

### <a name="how-do-i-set-up-users-and-permissions"></a>我如何设置用户和权限？

可以使用某个 [Cosmos DB API SDK](sql-api-sdk-dotnet.md) 或 [REST API](/rest/api/cosmos-db/) 来创建用户和权限。

### <a name="does-the-sql-api-support-sql"></a>SQL API 是否支持 SQL？

SQL API 支持的 SQL 查询语言是 SQL Server 支持的查询功能增强子集。 Azure Cosmos DB SQL 查询语言通过基于 JavaScript 的用户定义函数 (UDF)，提供丰富的分层和关系运算符以及可扩展性。 JSON 语法允许将 JSON 文档模型化为带标签节点的树状，由 Azure Cosmos DB 的自动索引技术及 Azure Cosmos DB 的 SQL 查询方言使用。 若要了解如何使用 SQL 语法，请参阅 [SQL 查询][query]一文。

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL API 是否支持 SQL 聚合函数？

SQL API 支持通过聚合函数 `COUNT`、`MIN`、`MAX`、`AVG` 和 `SUM` 通过 SQL 语法实现的任何规模的低延迟聚合。 有关详细信息，请参阅[聚合函数](sql-query-aggregates.md)。

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API 如何提供并发性？

SQL API 通过 HTTP 实体标记或 ETag 支持乐观并发控制 (OCC)。 每个 SQL API 资源都有一个 ETag。每次更新文档时，都会在服务器上设置此 ETag。 ETag 标头和当前值包含在所有响应消息中。 ETag 可与 If-Match 标头配合使用，让服务器决定是否应更新资源。 If-Match 值是用作检查依据的 ETag 值。 如果 ETag 值与服务器的 ETag 值匹配，就会更新资源。 如果 ETag 不再是最新状态，则服务器会拒绝该操作，并提供“HTTP 412 不满足前提条件”响应代码。 客户端接着必须重新提取资源，以获取该资源当前的 ETag 值。 此外，ETag 可以与 If-None-Match 标头配合使用，以确定是否需要重新提取资源。

若要在 .NET 中使用乐观并发，可以使用 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 类。 如需 .NET 示例，请参阅 GitHub 上 DocumentManagement 示例中的 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs)。

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>如何在 SQL API 中执行事务？

SQL API 通过 JavaScript 存储过程和触发器支持语言集成式事务。 脚本中的所有数据库操作都是在进行快照隔离的情况下执行的。 如果是单分区容器，则在容器范围内执行。 如果容器已分区，则执行范围为该容器中具有相同分区键值的文档。 文档版本 (ETag) 的快照是在事务开始时获取的，且只有当脚本成功运行时才会提交。 如果 JavaScript 引发错误，则会回滚事务。 有关详细信息，请参阅 [Azure Cosmos DB 的服务器端 JavaScript 编程](stored-procedures-triggers-udfs.md)。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>如何将文档批量插入到 Document DB 中？

可以通过以下方法之一，将文档批量插入到 Azure Cosmos DB 中：

* 批量执行程序工具，如[使用批量执行程序 .NET 库](bulk-executor-dot-net.md)和[使用批量执行程序 Java 库](bulk-executor-java.md)中所述
* 数据集成工具，如 [Azure Cosmos DB 的数据迁移工具](import-data.md)中所述。
* 存储过程，如 [Azure Cosmos DB 的服务器端 JavaScript 编程](stored-procedures-triggers-udfs.md)中所述。

### <a name="does-the-sql-api-support-resource-link-caching"></a>SQL API 是否支持资源链接缓存？

是的，因为 Azure Cosmos DB 是 RESTful 服务，而资源链接固定不变，所以可以缓存。 SQL API 客户端可以通过指定“If-None-Match”标头来读取任何资源（例如文档或容器），然后在服务器版本更改后更新本地副本。

### <a name="is-a-local-instance-of-sql-api-available"></a>SQL API 的本地实例是否可用？

是的。 [Azure Cosmos DB 模拟器](local-emulator.md)提供对 Cosmos DB 服务的高保真模拟。 它支持和 Azure Cosmos DB 相同的功能，包括支持创建和查询 JSON 文档、预配集合和调整集合的规模，以及执行存储过程和触发器。 可以使用 Azure Cosmos DB 模拟器开发和测试应用程序，然后对 Azure Cosmos DB 的连接终结点进行单一配置更改，可以将这些应用程序部署到全局范围的 Azure。

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>当从门户中的数据资源管理器查看时，为何会对文档中的长浮点值进行舍入？

这是 JavaScript 的限制。 JavaScript 根据 IEEE 754 中的规定使用双精度浮点格式的数字，并且只能安全地保存 -(2<sup>53</sup> - 1) 和 2<sup>53</sup>-1（即 9007199254740991）之间的数字。

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>在对象层次结构中的何处启用权限？

可以在容器级别以及其下的级别（例如文档级别、附件级别）使用 ResourceTokens 来创建权限。 这意味着，目前不允许在数据库或帐户级别创建权限。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>后续步骤

若要了解有关其他 Api 中的常见问题的信息，请参阅：

* 有关[Azure Cosmos DB 的适用于 MongoDB 的 API 的](mongodb-api-faq.md)常见问题
* [Azure Cosmos DB 中的 GREMLIN API](gremlin-api-faq.md)常见问题
* 有关[Azure Cosmos DB 中 Cassandra API 的](cassandra-faq.md)常见问题
* 有关[Azure Cosmos DB 中表 API 的](table-api-faq.md)常见问题
