---
title: "Azure Cosmos DB 数据库问题 - 常见问题 | Microsoft Docs"
description: "获取与 Azure Cosmos DB 此全球分布式多模型数据库服务相关的常见问题答案。 解答有关容量、性能级别和缩放的数据库问题。"
keywords: "数据库问题, 常见问题, documentdb, azure, Microsoft azure"
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 687690fc3b99d52d7c822b7b7ff71c282541be6d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="frequently-asked-questions-about-azure-cosmos-db"></a>有关 Azure Cosmos DB 的常见问题
## <a name="database-questions-about-microsoft-azure-cosmos-db-fundamentals"></a>有关 Microsoft Azure Cosmos DB 基础功能的数据库问题
### <a name="what-is-microsoft-azure-cosmos-db"></a>什么是 Microsoft Azure Cosmos DB？
Microsoft Azure Cosmos DB 是全局复制的多模型数据库服务，可针对无架构数据提供丰富的查询，帮助提供可靠的可配置性能，并支持快速开发，这一切都通过一个托管平台来实现，而该平台有 Microsoft Azure 强大的功能与影响力作为后盾。 如果 Web、移动、游戏和 IoT 应用程序的关键要求是可预测的吞吐量、高可用性、低延迟和无架构数据模型，那么，Azure Cosmos DB 无疑是合适的解决方案。 Azure Cosmos DB 提供架构灵活性和丰富索引，并且对集成的 JavaScript 提供多文档事务性支持。  

Azure Cosmos DB 在 2010 年末开始解决 Microsoft 内部大型应用程序开发人员所面临的难题。 由于构建全球分布式应用程序不是 Microsoft 的独家问题，因此我们让该服务以 Azure DocumentDB 的形式供外部所有的 Azure 开发人员使用。 Azure Cosmos DB 是 DocumentDB 发展过程中的下一个重大飞跃，现在我们要让使它能为你所用。 随着 Azure Cosmos DB 的此次发布，DocumentDB 客户连同其数据将自动成为 Azure Cosmos DB 客户。 此转换是无缝进行的，这些客户现在有权访问 Azure Cosmos DB 提供的更广泛的新功能。 

有关部署和使用此服务的更多数据库问题、解答及说明，请参阅 [Azure Cosmos DB 文档页面](https://azure.microsoft.com/documentation/services/documentdb/)。

### <a name="what-happened-to-documentdb"></a>DocumentDB 发生了什么情况？
DocumentDB API 是 Azure Cosmos DB 所支持的其中一个 API 和数据模型。 此外，Azure Cosmos DB 还支持图形 API（预览版）、表 API（预览版）和 MongoDB API。 

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>如何在 Azure 门户中登录到 DocumentDB 帐户？
只需在 Azure 门户左侧的菜单中单击 Azure Cosmos DB 图标即可。 如果之前已有 DocumentDB 帐户，现在则拥有 Azure Cosmos DB 帐户，并且不会更改计费。

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB 的典型用例有哪些？
对于侧重于以下要求的新 Web、移动、游戏和 IoT 应用程序而言，Azure Cosmos DB 是一个不错的选择：自动缩放、可预测的性能、毫秒响应时间的快速排序，以及查询无架构数据的能力。 Azure Cosmos DB 有助于快速开发，且支持应用程序数据模型的连续迭代。  管理用户生成的内容和数据的应用程序就是 [Azure Cosmos DB 的常见用例](documentdb-use-cases.md)。  

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB 如何提供可预测的性能？
[请求单位](documentdb-request-units.md)是 Azure Cosmos DB 中吞吐量的度量值。 1 个 RU 相当于获取 1KB 文档的吞吐量。 在 Azure Cosmos DB 中进行的每个操作（包括读取、写入、SQL 查询和存储过程执行）都具有一个确定性的 RU 值，该值基于完成该操作所需的吞吐量。 你无需考虑 CPU、IO 和内存，以及它们会怎样影响你的应用程序吞吐量，而是从单个 RU 度量值的角度进行考虑。

可以将每个 Azure Cosmos DB 容器保留为以每秒 RU 表示的预配吞吐量。 对于任何规模的应用程序，都可以将单个请求设为基准以测量其 RU 值，并预配容器以处理所有请求的请求单位总和。 也可以根据应用程序的发展需求，相应地增加或减少容器的吞吐量。 有关请求单位的详细信息以及确定容器需求方面的帮助，请阅读[估计吞吐量需求](documentdb-request-units.md#estimating-throughput-needs)并尝试使用[吞吐量计算器](https://www.documentdb.com/capacityplanner)。 此处的容器是指 DocumentDB API 的集合、图形 API 的图、MongoDB API 的集合以及表 API 的表。  


### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB 是否符合 HIPAA？
是，Azure Cosmos DB 符合 HIPAA。 HIPAA 针对可识别个人身份的健康信息的使用、泄露与保护制定了要求。 有关详细信息，请参阅 [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)（Microsoft 信任中心）。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的存储限制有哪些？
对于容器可以存储在 Azure Cosmos DB 中的数据总量并没有任何限制。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的吞吐量限制有哪些？
对于在 Azure Cosmos DB 中容器可以支持的吞吐量总量没有任何限制，关键是需要将工作负荷大致平均分配给量足够大的分区键。

### <a name="how-much-does-microsoft-azure-cosmos-db-cost"></a>Microsoft Azure Cosmos DB 的费用是多少？
请参考 [Azure Cosmos DB 定价详细信息](https://azure.microsoft.com/pricing/details/documentdb/)页面了解详细信息。 Azure Cosmos DB 使用费取决于预配的容器数目、容器的联机小时数，以及每个容器的已预配吞吐量。 此处的容器是指 DocumentDB API 的集合、图形 API 的图、MongoDB API 的集合以及表 API 的表。 

### <a name="is-there-a-free-account-available"></a>有免费的帐户吗？
如果不熟悉 Azure，可以注册 [Azure 免费帐户](https://azure.microsoft.com/free/)，这样可以得到 30 天试用期和 200 美元，让你试用所有 Azure 服务。 或者，如果有 Visual Studio 订阅，则有资格[免费获取每月 150 美元的 Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用于任何 Azure 服务。  

也可以使用 [Azure Cosmos DB 模拟器](documentdb-nosql-local-emulator.md)在本地免费开发和测试应用程序，而无需创建 Azure 订阅。 如果对应用程序在 Azure Cosmos DB 模拟器中的工作情况感到满意，则可以切换到在云中使用 Azure Cosmos DB 帐户。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>如何获取与 Azure Cosmos DB 相关的其他帮助？
如需任何帮助，请在 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) 或 [MSDN 论坛](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)上联系我们，或者通过向 [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) 发送邮件以安排与 Azure Cosmos DB 工程团队进行 1 对 1 交谈。 

## <a name="set-up-microsoft-azure-cosmos-db"></a>设置 Microsoft Azure Cosmos DB
### <a name="how-do-i-sign-up-for-microsoft-azure-cosmos-db"></a>如何注册 Microsoft Azure Cosmos DB？
Azure 门户中提供了 Microsoft Azure Cosmos DB。 首先，你必须注册 Microsoft Azure 订阅。 在注册了 Microsoft Azure 订阅后，便可将 DocumentDB API、图形 API（预览版）、表 API（预览版）或 MongoDB API 帐户添加到 Azure 订阅。

### <a name="what-is-a-master-key"></a>什么是主密钥？
主密钥是用于访问帐户的所有资源的安全令牌。 拥有此密钥的人对数据库帐户中的所有资源具有读取和写入访问权。 分发主密钥时要格外谨慎。 [Azure 门户][azure-portal]的“密钥”边栏选项卡中提供了主要主密钥和辅助主密钥。 有关密钥的详细信息，请参阅[查看、复制和重新生成访问密钥](documentdb-manage-account.md#keys)。

### <a name="what-are-the-regions-to-which-preferredlocations-be-set-to"></a>可以将 PreferredLocations 设置为哪些区域？ 
对于 PrefferredLocations，当前有效的区域包括“美国西部”、“美国中西部”、“美国西部 2”、“美国东部”、“美国东部 2”、“美国中部”、“美国中南部”、“美国中北部”、“西欧”、“北欧”、“东亚”、“东南亚”、“日本西部”、“日本东部”、“澳大利亚东南部”、“澳大利亚东部”、“印度中部”、“印度南部”、“印度西部”、“加拿大东部”、“加拿大中部”、“德国中部”、“德国东北部”、“中国北部”、“中国东部”、“韩国南部”、“韩国中部”、“英国西部”、“英国南部”、“巴西南部”、“USGov 亚利桑那州”、“USGov 德克萨斯州”。

### <a name="is-there-something-i-should-be-aware-of-when-distributing-data-across-the-world-via-azures-data-centers"></a>通过 Azure 数据中心在全球范围内分发数据时，是否有应了解的事项？ 
Cosmos DB 遍布于所有区域。 由于它属于核心服务，因此每个新的数据中心都有 Cosmos DB。 以上列表介绍了目前为止的所有区域。 在进行这些设置时，请务必记住，Cosmos DB 尊重 sovereign 云和政府云。 这意味着不允许在当地创建了帐户后向外复制，同样，无法通过外部帐户访问这些位置以启用复制。 



### <a name="do-you-plan-to-provide-more-price-options-in-the-future"></a>是否计划在将来会提供更多的价格选项？
是，目前的 Cosmos DB 提供吞吐量优化的模型。 我们计划在不久的将来提供更多最佳的定价模型。 

 
# <a name="documentdb-api"></a>DocumentDB API 
## <a name="database-questions-about-developing-against-documentdb-api"></a>针对 DocumentDB API 进行开发的相关数据库问题

### <a name="how-to-do-i-start-developing-against-documentdb-api"></a>如何开始针对 DocumentDB API 进行开发？
[Azure 门户][azure-portal]中提供了 Microsoft DocumentDB API。  首先，你必须注册 Microsoft Azure 订阅。  在注册了 Microsoft Azure 订阅后，便可将 DocumentDB API 容器添加到 Azure 订阅。 有关添加 Azure Cosmos DB 帐户的说明，请参阅[创建 Azure Cosmos DB 数据库帐户](documentdb-create-account.md)。 如果之前已有 DocumentDB 帐户，现在则拥有 Azure Cosmos DB 帐户。  

[SDK](documentdb-sdk-dotnet.md) 适用于 .NET、Python、Node.js、JavaScript 和 Java。  开发人员也可以使用 [RESTful HTTP API](https://msdn.microsoft.com/library/azure/dn781481.aspx)，从各种平台使用各种语言与 Azure Cosmos DB 资源进行交互。

### <a name="can-i-access-some-ready-made-samples-for-getting-headstart"></a>是否可以获取一些现有的示例以开始使用？
GitHub 上提供了 DocumentDB API [.NET](documentdb-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](documentdb-nodejs-samples.md) 和 [Python](documentdb-python-samples.md) SDK 的示例。


### <a name="does-documentdb-api--database-support-schema-free-data"></a>DocumentDB API 数据库是否支持无架构数据？
是，DocumentDB API 允许应用程序存储任意的 JSON 文档，而不需要架构定义或提示。 通过 Azure Cosmos DB SQL 查询界面便可立即查询数据。   

### <a name="does-documentdb-api-support-acid-transactions"></a>DocumentDB API 是否支持 ACID 事务？
是，DocumentDB API 支持以 JavaScript 存储过程和触发器形式表示的跨文档事务。 事务以每个集合内的单个分区为范围，且以 ACID 语义执行，也就是全有或全无，与其他并发执行的代码和用户请求隔离。  如果在服务器端执行 JavaScript 应用程序代码的过程中引发了异常，则会回滚整个事务。 有关事务的详细信息，请参阅[数据库程序事务](documentdb-programming.md#database-program-transactions)。

### <a name="what-is-a-collection"></a>什么是集合？
集合是文档和其关联的 JavaScript 应用程序逻辑的组。 集合是一个计费实体，其[成本](documentdb-performance-levels.md)由所使用的吞吐量和存储确定。 集合可以跨一个或多个分区/服务器，并且能伸缩以处理几乎无限制增长的存储或吞吐量。

集合也是 Azure Cosmos DB 的计费实体。 每个集合根据预配的吞吐量和使用的存储空间按小时计费。 有关详细信息，请参阅 [DocumentDB API 定价](https://azure.microsoft.com/pricing/details/documentdb/)。  

### <a name="how-do-i-create-a-database"></a>我如何创建数据库？
可以按照[创建 Azure Cosmos DB 集合和数据库](documentdb-create-collection.md)中所述使用 [Azure 门户]()，使用某个 [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md) 或通过 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 来创建数据库。  

### <a name="how-do-i-set-up-users-and-permissions"></a>我如何设置用户和权限？
可以使用某个 [DocumentDB API SDK](documentdb-sdk-dotnet.md) 或通过 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) 来创建用户和权限。   

### <a name="does-documentdb-api-support-sql"></a>DocumentDB API 是否支持 SQL？
SQL 查询语言是 SQL 所支持的查询功能的增强子集。 Azure Cosmos DB SQL 查询语言通过基于 JavaScript 的用户定义函数 (UDF)，提供丰富的分层和关系运算符以及扩展性。 JSON 语法允许将 JSON 文档模型化为以标签作为树节点的树状，它将由 Azure Cosmos DB 自动索引技术及 Azure Cosmos DB 的 SQL 查询方言使用。  有关如何使用 SQL 语法的详细信息，请参阅[查询 DocumentDB][query] 一文。

### <a name="does-documentdb-api--support-sql-aggregation-functions"></a>DocumentDB API 是否支持 SQL 聚合函数？
DocumentDB API 支持通过聚合函数 `COUNT`、`MIN`、`MAX`、`AVG` 和 `SUM` 使用 SQL 语法实现的任何规模的低延迟聚合。 有关详细信息，请参阅[聚合函数](documentdb-sql-query.md#Aggregates)。

### <a name="how-does-documentdb-api--provide-concurrency"></a>DocumentDB API 如何提供并发？
DocumentDB API 通过 HTTP 实体标记或 ETag 支持乐观并发控制 (OCC)。 每个 DocumentDB API 资源都有一个 ETag，并且会在每次更新文档时，在服务器上设置此 ETag。 ETag 标头和当前值会包含在所有响应消息中。 ETag 可与 If-Match 标头配合使用，让服务器能够决定是否应该更新资源。 If-Match 值是用作检查依据的 ETag 值。 如果 ETag 值与服务器的 ETag 值匹配，就会更新资源。 如果 ETag 不再是最新状态，则服务器会拒绝该操作，并提供“HTTP 412 前置条件失败”响应代码。 客户端接着必须重新提取资源，以获取该资源当前的 ETag 值。 此外，ETag 可以与 If-None-Match 标头配合使用，以确定是否需要重新提取资源。

若要在 .NET 中使用乐观并发，可使用 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 类。 相关 .NET 示例，请参阅 github 上 DocumentManagement 示例中的 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs)。

### <a name="how-do-i-perform-transactions-in-documentdb-api"></a>如何在 DocumentDB API 中执行事务？
DocumentDB API 通过 JavaScript 存储过程和触发器支持语言集成式事务。 如果是单一分区集合，则脚本内的所有数据库操作会在集合范围内的快照隔离下执行；如果集合已分区，则在集合中具有相同分区键值的文档下执行。 文档版本 (ETag) 的快照是在事务开始时获取的，且只有当脚本成功运行时才会提交。 如果 JavaScript 引发错误，则会回滚事务。 有关更多详细信息，请参阅 [DocumentDB API 服务器端编程](documentdb-programming.md)。

### <a name="how-can-i-bulk-insert-documents-into-documentdb-api"></a>如何将文档批量插入到 DocumentDB API 中？
可以通过三种方式将文档批量插入到 Azure Cosmos DB 中：

* 数据迁移工具，如[将数据导入 DocumentDB API](documentdb-import-data.md) 中所述。
* Azure 门户中的文档资源管理器，如[使用文档资源管理器批量添加文档](documentdb-view-json-document-explorer.md#bulk-add-documents)所述。
* 存储过程，如 [DocumentDB API 服务器端编程](documentdb-programming.md)中所述。

### <a name="does-documentdb-api-support-resource-link-caching"></a>DocumentDB API 是否支持资源链接缓存？
是，因为 Cosmos DB 是 RESTful 服务，而资源链接固定不变，所以可以缓存。 DocumentDB API 客户端可以指定“If-None-Match”标头来读取任何资源，例如文档或集合，且只有当服务器版本更改时才会更新其本地副本。

### <a name="is-a-local-instance-of-documentdb-api-available"></a>DocumentDB API 的本地实例是否可用？
是的。 [Azure Cosmos DB 模拟器](documentdb-nosql-local-emulator.md)提供对 DocumentDB API 服务的高保真模拟。 它支持与 Azure Cosmos DB 相同的功能，包括对创建和查询 JSON 文档，预配和扩展集合，以及执行存储过程和触发器的支持。 可以使用 Azure Cosmos DB 模拟器开发和测试应用程序，并通过对 Azure Cosmos DB 的连接终结点进行单一配置更改以将其部署到全局范围的 Azure。

## <a name="database-questions-about-developing-against-api-for-mongodb"></a>针对 MongoDB 的 API 进行开发的相关数据库问题
### <a name="what-is-azure-cosmos-dbs-api-for-mongodb"></a>什么是 Azure Cosmos DB 的适用于 MongoDB 的 API？
Microsoft Azure Cosmos DB 的适用于 MongoDB 的 API 是一个兼容层，使应用程序可以使用现有的社区支持的 Apache MongoDB API 和驱动程序轻松透明地与本机 Azure Cosmos DB 数据库引擎进行通信。 得益于 Azure Cosmos DB 的独特功能（包括自动索引、备份维护、得到资金支持的服务级别协议 (SLA) 等），开发人员现在可以使用现有的 MongoDB 工具链和技能构建利用 Azure Cosmos DB 的应用程序。

### <a name="how-to-do-i-connect-to-my-api-for-mongodb-database"></a>如何连接到 MongoDB 的 API 数据库？
连接到 Azure Cosmos DB 的适用于 MongoDB 的 API 最快捷方法是前往 [Azure 门户](https://portal.azure.com)。 导航到你的帐户。 在帐户的*左导航*中，单击“快速启动”。 “快速启动”是获取代码片段以连接到数据库的最佳方式。 

Azure Cosmos DB 强制实施严格的安全要求和标准。 Azure Cosmos DB 帐户需要通过 SSL 进行身份验证和安全通信，因此请确保使用 TLSv1.2。

有关更多详细信息，请参阅[连接到 MongoDB 的 API 数据库](documentdb-connect-mongodb-account.md)。

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>MongoDB 的 API 数据库是否有其他错误代码？
除了常见的 MongoDB 错误代码外，MongoDB 的 API 具有其自己的特定错误代码。


| 错误               | 代码  | 说明  | 解决方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用的请求单位总数已超过了集合的预配请求单位率，已达到限制。 | 请考虑从 Azure 门户缩放集合的吞吐量或重试。 |
| ExceededMemoryLimit | 16501 | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系技术支持。 <br><br>*Ex:  &nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="database-questions-about-developing-with-azure-cosmos-db-table-api-preview"></a>使用 Azure Cosmos DB：表 API（预览版）进行开发的相关数据库问题

### <a name="terms"></a>术语 
Azure Cosmos DB：表 API（预览版）是指 Azure Cosmos DB 为支持表所提供的高级服务，宣布于 Build 2017 会议上。 

标准表 SDK 是预先存在的 Azure 存储表 SDK。 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>如何使用新的表 API（预览版）服务？ 
[Azure 门户][azure-portal]中提供了 Microsoft 表 API。  首先，你必须注册 Microsoft Azure 订阅。  在注册了 Microsoft Azure 订阅后，便可将表 API 容器添加到 Azure 订阅。  
在预览期间，可以将 [SDK](../cosmos-db/table-sdk-dotnet.md) 用于 .NET，并且可以通过完成[表 API](../cosmos-db/create-table-dotnet.md) 快速入门以开始使用。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>是否需要新的 SDK 才能使用表 API（预览版）？ 
是 – 此处，[表 API](../cosmos-db/table-sdk-dotnet.md) 提供 Nuget 包形式的新的 SDK。 它称为与高级表 API（预览版）配合使用的用于 Windows Azure 存储 8.1.2 的 SDK。  

### <a name="how-do-i-provide-the-feedback-about-the-sdk-bugs"></a>如何提供有关 SDK 的反馈和 Bug？
请使用以下任一方法分享你的反馈：

* [UserVoice](https://feedback.azure.com/forums/263030-documentdb)
* [MSDN 论坛](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-documentdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>需要使用什么连接字符串以连接到表 API（预览版）？
连接字符串为 `DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com`。 可以从 Azure 门户的“密钥”页面获取连接字符串。 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-apipreview"></a>如何在新的表 API（预览版）中替代请求选项的配置设置？
[Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)中介绍了这些设置。 可以在客户端应用程序中将设置添加到 appsettings 部分中的 app.config 以进行更改。
<appSettings>
    <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
    <add key="TableThroughput" value="<PositiveIntegerValue"/>
    <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
    <add key="TableUseGatewayMode" value="True|False"/>
    <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
</appSettings>


### <a name="is-there-any-change-to-existing-customers-that-use-the-existing-standard-table-sdk"></a>对使用现有标准表 SDK 的现有客户而言，是否有任何更改？
无。 对使用现有标准表 SDK 的现有客户或新客户而言，没有任何更改。 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>如何查看存储在 Azure Cosmos DB 中以用于表 API（预览版）的表数据？ 
可以使用 Azure 门户浏览此数据。 也可以使用表 API（预览版）代码或以下所述的工具。 

### <a name="which-tools-will-work-with-table-api-preview"></a>哪些工具适用于表 API（预览版）？ 
旧版本的 Azure 资源管理器 (0.8.9)。

灵活地采用之前指定格式的连接字符串的工具可以支持新的表 API（预览版）。 [Azure 存储客户端工具](../storage/storage-explorers.md)页上提供了表工具的列表。 

### <a name="does-powershellcli-work-with-the-new-table-api-preview-"></a>PowerShell/CLI 是否适用于新的表 API（预览版）？
我们计划为表 API（预览版）添加对 PowerShell/CLI 的支持。 

### <a name="is-the-concurrency-on-operations-controlled"></a>是否能控制并发操作？
是，乐观并发是通过在标准表 API 中按预期方式使用 ETag 机制所提供的。 

### <a name="is-odata-query-model-supported-for-entities"></a>实体是否支持 OData 查询模型？ 
是，表 API（预览版）支持 OData 查询和 Linq 查询。 

### <a name="can-i-connect-to-standard-azure-table-and-the-new-premium-table-api-preview-at-side-by-side-in-same-application-"></a>是否可以连接到同一应用程序中并排的标准 Azure 表和新的高级表 API（预览版）？ 
是，可以创建 CloudTableClient 的两个不同实例（通过连接字符串指向各自的 URI）来实现这点。

### <a name="how-do-i-migrate-existing-table-storage-application-to-this-new-offering"></a>如何将现有表存储应用程序迁移到此新服务中？
如果想在现有表存储数据的基础上使用新的表 API 服务，请联系 askdocdb@microsoft.com。 

### <a name="what-is-the-roadmap-for-this-service-when-will-other-functionality-of-standard-table-api-be-offered"></a>此服务的路线图是什么？何时将提供标准表 API 的其他功能？
我们计划添加对 SAS 令牌、ServiceContext、统计信息、加密、分析和其他功能的支持，因为我们将朝着通用版本的方向发展。  请在 [UserVoice](https://feedback.azure.com/forums/263030-documentdb) 上为我们提供反馈。 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-say-i-start-with-n-gbs-of-data-and-my-data-will-grow-to-1-tb-overtime"></a>此服务的存储大小扩展性如何，假设一开始数据为 N GB，随着时间的推移，数据是否会增长到 1 TB？  
Cosmos DB 旨在通过使用水平扩展提供无限的存储空间。 我们的服务将监视存储空间并有效地进行增加。 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>如何监视表 API（预览版）服务？
可以使用表 API（预览版）指标窗格来监视请求和存储使用情况。 

### <a name="how-do-i-calculate-throughput-i-require"></a>如何计算所需的吞吐量？
可以使用容量估算器计算操作所需的 TableThroughput，如 [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner)（估算请求单位和数据存储空间）中所述。 通常，可以将实体表示为 JSON 并且为操作提供所需数量。 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>是否可以在本地将新的表 API（预览版）SDK 用于模拟器？
是，如果使用的是新的 SDK，则可以在本地模拟器上使用表 API（预览版）。 请从[此处](documentdb-nosql-local-emulator.md)下载新的模拟器。 app.config 中的 StorageConnectionString 值需要为“DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081”。 

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>现有的应用程序是否适用于表 API（预览版）？ 
在使用 .NET API 进行的创建、更新、删除和查询操作中，新的表 API（预览版）的外围应用与现有 Azure 标准表 SDK 相兼容。 需确保拥有行键，这是因为表 API（预览版）同时需要分区键和行键。我们也计划添加其他的 SDK 支持，因为我们将让服务朝通用版本的方向发展。

### <a name="do-i-need-to-migrate-existing-azure-table-based-application-to-new-sdk-if-i-do-not-want-to-use-table-api-preview-features"></a>如果不想使用表 API（预览版）功能，是否需要将基于 Azure 表的现有应用程序迁移到新的 SDK？
否，现有客户可以在没有任何干扰的情况下创建和使用现有的标准表资产。 但是，如果不使用新的表 API（预览版），则无法从自动索引、其他的并发选项或全球分布中受益。 

### <a name="how-do-i-add-replication-for-the-data-in-premium-table-api-preview-across-multiple-regions-of-azure"></a>如何在多个 Azure 区域的高级表 API（预览版）中添加数据的复制？
可以使用 Cosmos DB 门户的[全局复制设置](documentdb-portal-global-replication.md)添加适合应用程序的区域。 若要开发全球分布的应用程序，还应该将设置有 PreferredLocation 信息的应用程序添加到本地区域以实现较低的读取延迟。 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in--premium-table-apipreview"></a>如何在高级表 API（预览版）中更改帐户的主要写入区域？
可以使用 Cosmos DB 的全局复制门户窗格添加区域，然后故障转移到所需区域。 有关说明，请参阅[使用多区域 Azure Cosmos DB 帐户进行开发](documentdb-regional-failovers.md)。 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>在分发数据时，如何配置首选读取区域以实现较低的延迟？ 
需要利用 app.config 中的 PreferredLocation 键以帮助从本地位置进行读取。 由于如果设置了 LocationMode，现有的应用程序表 API（预览版）将引发错误，因此，请删除该代码，因为高级表 API（预览版）将从 app.config 文件中选取此信息。  [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)中介绍了这些设置


### <a name="how-do-i-think-of-consistency-in-table-api-preview"></a>对表 API（预览版）中的一致性有何看法？ 
Cosmos DB 在一致性、可用性和延迟之间进行十分合理的权衡。 自此版本起，它提供 5 个一致性级别以供选择。 表 API（预览版）开发人员可以使用这些一致性级别。 这允许开发人员在查询数据时在表级选择正确的一致性模式和单个请求。  当客户端连接时，它可以指定一致性级别：可以通过 TableConsistencyLevel 键值的 app.config 设置更改此级别。 凭借默认的会话一致性，表 API（预览版）通过读取你自己写入的数据实现较低的延迟。 有关详细信息，请参阅[一致性级别](documentdb-consistency-levels.md)。 默认情况下，目前标准表在区域内提供非常一致性，在辅助位置中提供最终一致性。  

### <a name="does-this-mean-compared-to-eventual--strong-consistency-that-is-possible-with-standard-table---we-now-have-more-choices"></a>这是否意味着与标准表可能适用的最终和非常一致性相比，我们现在有更多的选择？
是，[一致性级别](documentdb-consistency-levels.md)一文中介绍了这些选择，用于帮助应用程序开发人员利用 Cosmos DB 的分布式性质。 由于同时提供了一致性的保障，因此现在可以放心地使用这些选择。 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)中介绍了这些设置

### <a name="when-global-distribution-is-enabled--how-long-does-it-take-to-replicate-the-data"></a>启用全局分发后，完成数据的复制需要多久？
我们持续不断地提交本地区域中的数据，然后立刻在数毫秒内将数据推送到其他区域，此复制操作仅依赖于数据中心的 RTT。 请在 [Azure Cosmos DB：Azure 上的全球分布式数据库服务](documentdb-distribute-data-globally.md)中阅读 Cosmos DB 全球分布的相关功能。

### <a name="can-the-read-request-consistency-be-changed"></a>是否可以更改读取请求一致性？
Cosmos DB 允许在容器级别（即表中）设置一致性。 SDK 还允许通过在 app.config 文件中提供 TableConsistencyLevel 键的值来更改级别。 这些是可以使用的值 - Strong|Bounded Staleness|Session|ConsistentPrefix|Eventual。 [一致性级别](documentdb-consistency-levels.md)一文中对此进行了介绍。 此处的关键是要记住，请求一致性级别不能超过为表设置的一致性级别。 例如，如果为表设置的是最终一致性，而为请求设置的是非常一致性，则这将不起作用。 

### <a name="how-does-premium-table-api-preview-account-take-care-of-failover-in-case-a-region-goes-down"></a>在某个区域出现故障时，高级表 API（预览版）帐户将如何进行故障转移？ 
高级表 API（预览版）利用 Cosmos DB 的全球分布式平台。 若要确保应用程序可以容忍数据中心故障时间，则需要在 Cosmos DB 门户中至少再为帐户启用一个区域（[使用多区域 Azure Cosmos DB 帐户进行开发](documentdb-regional-failovers.md)）。 可以通过使用门户设置区域的优先级（[使用多区域 Azure Cosmos DB 帐户进行开发](documentdb-regional-failovers.md)）。 可以为帐户添加多个区域，并通过提供优先级控制要将帐户故障转移到的区域。 当然，不言而喻，也需要提供应用程序以利用数据库。 如此一来，客户将不会看到故障时间。 客户端 SDK 是自动进行操作的，它可以检测到当前出现故障的区域，并自动故障转移到新的区域。

### <a name="is-premium-table-api-preview-enabled-for-backups"></a>是否能够对高级表 API（预览版）进行备份？
是，高级表 API（预览版）利用 Cosmos DB 的全球分布式平台进行备份。 备份将自动进行。 此处介绍了 Cosmos DB 备份：[Azure Cosmos DB 的联机备份和还原](C:\Users\govindk\azure-docs-pr\articles\documentdb\documentdb-online-backup-and-restore.md)

 
### <a name="does-the-table-api-preview-index-all-attributes-of-entities-by-default"></a>表 API（预览版）是否默认对实体的所有属性进行索引？
是，默认情况下，会对实体的所有属性进行索引。 [Azure Cosmos DB：索引策略](documentdb-indexing-policies.md)一文中介绍了索引详细信息。 

### <a name="does-this-mean-i-do-not-have-to-create-different-indexes-to-satisfy-the-queries"></a>这是否意味着无需创建不同的索引即可满足查询要求？ 
是，Cosmos DB 提供对所有属性进行的自动索引，而无需任何架构定义。 这使开发人员能够将重心放在应用程序上，而不是担心索引的创建和管理。 [Azure Cosmos DB：索引策略](documentdb-indexing-policies.md)一文中介绍了索引详细信息。

### <a name="can-the-indexing-policy-be-changed"></a>是否可以更改索引策略？
是，可以提供索引定义来更改索引。 [Azure Cosmos DB 功能](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities)一文中介绍了这些设置的含义。 需要正确地进行编码并转义这些设置。  

app.config 文件中 JSON 格式的字符串。
{ "indexingMode": "consistent", "automatic": true, "includedPaths": [ { "path": "/somepath", "indexes": [ { "kind": "Range", "dataType": "Number", "precision": -1 }, { "kind": "Range", "dataType": "String", "precision": -1 } ] } ], "excludedPaths": [ { "path": "/anotherpath" } ] }

### <a name="cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-like-sorting-aggregates-hierarchy-and-other-functionality---is-this-planned-to-be-added-to-the-table-api-"></a>作为一个平台，Cosmos DB 似乎拥有排序、聚合、层次结构以及其他功能等许多功能，是否会将此计划添加到表 API 中？ 
在预览版中，对于表 API，Azure Cosmos DB 支持与 Azure 表存储相同的查询功能。 Azure Cosmos DB 还支持排序、聚合、地理空间查询、层次结构和各种内置函数。 在将来的服务更新中，将在表 API 中提供更多的功能。 有关这些功能的概述，请参阅 [Azure Cosmos DB 查询](../documentdb/documentdb-sql-query.md)。
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>何时应更改表 API（预览版）的 TableThroughput？
在对数据进行 ETL 操作或想在短时间内上传大量数据时，应更改 TableThroughput。 

或

当看到使用的吞吐量在指标上超出预配的吞吐量并且受到限制时，则需要来自后端容器中更多的吞吐量。 [设置吞吐量](documentdb-set-throughput.md)一文中对此进行了介绍。

### <a name="can-i-scale-up-or-down-the-throughput-of-my-table-api-preview-table"></a>是否可以增加或减少表 API（预览版）表的吞吐量？ 
是，可以使用 Cosmos DB 门户的缩放窗格执行相同的操作。 [设置吞吐量](documentdb-set-throughput.md)主题中对此进行了介绍。

### <a name="can-premium-table-api-preview-leverage-ru-per-minute-offering"></a>高级表 API（预览版）是否可以利用每分钟 RU 服务？ 
是，高级表（预览版）利用 Cosmos DB 的功能提供 SLA 以满足性能、延迟、可用性和一致性需求。 这可确保它能够利用每分钟 RU 服务，如[请求单位](documentdb-request-units.md)中所述。 此功能使客户不必预配最大使用量，并消除工作负荷中的峰值。

### <a name="is-there-a-default-tablethroughput-which-is-set-for-newly-provisioned-tables"></a>是否为新预配的表设置了默认 TableThroughput？
是，如果未通过 app.config 替代 TableThroughput 以及未在 Cosmos DB 中使用预创建的容器，服务则会创建吞吐量为 400 的表。
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-standard-table-api"></a>对于标准表 API 的现有客户，定价是否有任何更改？
无。 对于现有的标准表 API 客户，价格上没有任何更改。 

### <a name="how-is-the-price-calculated-for-the-table-apipreview"></a>表 API（预览版）的价格是如何计算的？ 
它取决于分配的 TableThroughput。 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>如何在表 API（预览版）服务中处理表的限制？ 
如果请求速率超出了为基础容器预配的吞吐量容量，则将收到错误，并且 SDK 将使用重试策略重新尝试进行调用。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-leverage-premium-table-preview-offering-of-cosmos-db"></a>除了 PartitionKey 和 RowKey 外，为何需要选择吞吐量以利用 Cosmos DB 的高级表（预览版）服务？
如果未在 app.config 中提供一个吞吐量，Cosmos DB 将为容器设置一个默认的吞吐量。 

凭借操作上限，Cosmos DB 可以提供性能和延迟上的保障。 在引擎强制对租户的操作进行调控时，可能会出现此情况。 设置 TableThroughput 基本上能确保获得吞吐量和延迟方面的保障，因为平台现在将保留此容量，并保证操作成功。  
吞吐量的规范使你能够对它进行弹性更改，以利用应用程序的季节性，满足吞吐量需求并节省成本。

### <a name="azure-storage-sdk-has-been-very-cheap-for-me-as-i-only-pay-to-store-the-data-and-i-rarely-query-cosmos-dbs-new-offering-seems-to-be-charging-me-even-though-i-have-not-performed-single-transaction-or-stored-anything-whats-going-on-here"></a>对我来说，Azure 存储 SDK 非常便宜，因为我仅需要支付存储数据的费用，并且我很少进行查询。 而即使在我没有执行单个事务或存储任何数据的情况下，Cosmos DB 的新服务似乎也会产生费用。 这是怎么回事？

Cosmos DB 设计为全球分布、基于 SLA 的系统，提供了可用性、延迟和吞吐量方面的保障。 在 Cosmos DB 中保留吞吐量时，对它的保障与其他系统不同。 Cosmos DB 还提供客户要求已久的其他功能，例如辅助索引、全局分发等。在预览期间，我们提供吞吐量优化的模型，并且从长远来看，我们计划提供存储优化的模型，以满足我们客户的需求。  

### <a name="i-never-get-quota-full-indicating-a-partition-is-full-when-i-keep-ingesting-data-into-azure-table-storage-with-the-table-apipreview--i-can-get-this-error-is-this-offering-limiting-me-and-forcing-me-to-change-my-present-application"></a>在持续将数据引入到 Azure 表存储中时，从未收到“配额”已满（意味着分区已满）的消息。 使用表 API（预览版）时，则会收到此错误。 此服务是否限制我并强迫我更改现有应用程序？

Cosmos DB 是基于 SLA 的系统，它提供无限制的扩展，并且能提供延迟、吞吐量、可用性和一致性方面的保障。 若要确保获得保障的高级性能，需要确保数据大小和索引是可管理和扩展的。 我们为每分区键的实体/项的数量提供了 10 GB 限制，以确保能提供强大的查找和查询性能。 若要确保即使针对 Azure 存储，应用程序也能很好地进行扩展，则请勿通过对一个分区的所有信息进行排序并查询它来创建热分区。  

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>新的表 API（预览版）是否仍然需要 PartitionKey 和 RowKey？ 
是的。 由于表 API（预览版）的外围应用类似于 Azure 表存储 SDK，因此分区键提供了分发数据的好办法。 行键在分区中是唯一的。 是，因为在标准 SDK 中，需要存在行键且它不能为 Null。 截至到生成预览，RowKey 的长度是 255 个字节，PartitionKey 的长度是 100 个字节（即将增加到 1 KB）。 

### <a name="what-will-be-the-error-messages-of-table-api-preview-"></a>表 API（预览版）的错误消息有哪些？
由于此预览版与标准表兼容，因为大部分的错误将映射到标准表中的错误。 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another--in-tables-api-preview"></a>为何在表 API（预览版）中尝试一个接一个地创建许多表时会受到限制？
Cosmos DB 是基于 SLA 的系统，提供了可用性、延迟和吞吐量方面的保障。 由于它是预配的系统，因为它会保留资源以保障满足这些需求。 由于系统检测到了以快速方式创建表的速率，因此进行了限制。 我们建议查看表的创建速率，并将此速率降低为每分钟 5 个。 请记住，表 API（预览版）是预配的系统，因此目前当你对它进行预配时，需要付费。  

# <a name="graph-apipreview"></a>图形 API（预览版）
## <a name="database-questions-about-developing-against-graph-apipreview"></a>针对图形 API（预览版）进行开发的相关数据库问题
### <a name="how-can-i-leverage-graph-api-preview-with-cosmos-db"></a>如何将图形 API（预览版）用于 Cosmos DB？
可以从扩展库利用图形 API（预览版）的基本功能。 它称为 Microsoft Azure 图形，并且在 nuget 中提供。 

### <a name="it-looks-like-you-support-gremlin-as-traversal-language-do-you-plan-to-add-some-more-forms-of-query-"></a>看上去似乎你们支持使用 gremlin 作为遍历语言，那么你们是否打算添加更多的查询形式？
是，在将来我们计划为查询添加其他机制。 

### <a name="how-can-i-use-the-new-graph-apipreview-offering"></a>如何使用新的图形 API（预览版）服务？ 
通过完成[图形 API](../cosmos-db/create-graph-dotnet.md) 快速入门以开始使用。




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

