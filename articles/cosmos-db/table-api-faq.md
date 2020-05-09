---
title: 有关 Azure Cosmos DB 中的表 API 的常见问题
description: 获取有关中的表 API 常见问题的答案 Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 981c6f145f0bf06fbe81234d473b9fbcd2235174
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614481"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>有关 Azure Cosmos DB 中的表 API 的常见问题

Azure Cosmos DB 表 API 在[Azure 门户](https://portal.azure.com)中首先必须注册 Azure 订阅。 注册以后，即可向 Azure 订阅添加 Azure Cosmos DB 表 API 帐户，然后向帐户添加表。 可以在 [Azure Cosmos DB 表 API 简介](table-introduction.md)中找到支持的语言和相关的快速入门。

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Azure Cosmos DB Vs Azure 表存储中的表 API

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>表 API 与 Azure 表存储的行为有哪些不同之处？

想要使用 Azure Cosmos DB 表 API 创建表的 Azure 表存储用户应注意以下这些行为差异：

* Azure Cosmos DB 表 API 使用保留容量模型来保障性能，但这意味着，一旦创建了表，就必须立即支付容量费用，即使容量未被使用。 使用 Azure 表存储时，只需为使用的容量付费。 这也说明了，表 API 在 99% 的时间里为何能够提供 10 毫秒的读取延迟和 15 毫秒的写入延迟 SLA，而 Azure 表存储提供 10 秒延迟 SLA。 因此，使用表 API 表（即使是不带任何请求的空表）时，要达到 Azure Cosmos DB 所提供的 SLA，必须支付费用来确保提供所需的容量来处理对这些表发出的所有请求。

* 表 API 返回的查询结果未按分区键/行键顺序排序，因为它们在 Azure 表存储中。

* 行键最多只能包含255字节。

* 批处理最多只能有 2 Mb。

* 当前不支持 CORS。

* Azure 表存储中的表名称不区分大小写，但它们处于 Azure Cosmos DB 表 API 中。

* Azure Cosmos DB 的某些编码信息内部格式，例如二进制字段，目前不如想像的那么有效。 因此，这会导致数据大小受到意外限制。 例如，目前无法使用整整有 1 MB 的表实体来存储二进制数据，因为编码会增大数据大小。

* 当前不支持实体属性名称 "ID"。

* TableQuery TakeCount 不限制为1000。

* 对于 REST API，有大量的终结点/查询选项不受 Azure Cosmos DB 表 API 的支持：

  | REST 方法 | REST 终结点/查询选项 | 文档 URL | 说明 |
  | ------------| ------------- | ---------- | ----------- |
  | GET、PUT | `/?restype=service@comp=properties`| [设置表服务属性](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties)和[获取表服务属性](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | 此终结点用于设置 CORS 规则、存储分析配置和日志记录设置。 CORS 目前不受支持，Azure Cosmos DB 与 Azure 存储表中以不同的方式处理分析和日志记录 |
  | OPTIONS | `/<table-resource-name>` | [预检 CORS 表请求](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | 这是 Azure Cosmos DB 目前不支持的 CORS 部分。 |
  | GET | `/?restype=service@comp=stats` | [获取表服务统计信息](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | 提供有关主节点与辅助节点之间的数据复制速度的信息。 由于复制是写入的一部分，因此在 Cosmos DB 中不需要此选项。 |
  | GET、PUT | `/mytable?comp=acl` | [获取表 ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) 和[设置表 ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | 获取和设置用于管理共享访问签名 (SAS) 的存储访问策略。 尽管支持 SAS，但其设置和管理方式不同。 |

* Azure Cosmos DB 表 API 仅支持 JSON 格式，而不支持 ATOM。

* 尽管 Azure Cosmos DB 支持共享访问签名 (SAS)，但它不支持某些策略，具体而言，是与管理操作相关的策略，例如创建新表的权限。

* 对于特定的 .NET SDK，Azure Cosmos DB 目前不支持某些类和方法。

  | 类 | 不支持的方法 |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties\* |
  |                  | \*ServiceStats\* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions* |
  | TableServiceContext | *（此类已弃用） |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>其他常见问题解答

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>是否需要新的 SDK 才能使用表 API？

不是，现有的存储 SDK 仍然适用。 但是，我们建议始终使用最新的 SDK，以获得最佳支持，并在许多场合下获得优异的性能。 请参阅 [Azure Cosmos DB 表 API 简介](table-introduction.md)中的可用语言列表。

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>连接到表 API 需要使用哪个连接字符串？

连接字符串为：

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

可以通过 Azure 门户中的“连接字符串”页获取连接字符串。

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>如何在表 API 的 .NET SDK 中替代请求选项的配置设置？

有些设置是通过 CreateCloudTableClient 方法处理的，还有一些设置是通过客户端应用程序中 appSettings 节的 app.config 处理的。 有关配置设置的信息，请参阅 [Azure Cosmos DB 功能](tutorial-develop-table-dotnet.md)。

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>使用现有 Azure 表存储 SDK 的客户是否需要进行任何更改？

无。 使用现有 Azure 表存储 SDK 的现有客户或新客户无需进行任何更改。

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>如何查看 Azure Cosmos DB 中存储的用于表 API 的表数据？

可以使用 Azure 门户浏览数据。 也可以使用表 API 代码或下一个问题答案中提到的工具。

### <a name="which-tools-work-with-the-table-api"></a>哪些工具适用于表 API？

可以使用 [Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)。

灵活地采用之前指定格式的连接字符串的工具可以支持新的表 API。 [Azure 存储客户端工具](../storage/common/storage-explorers.md)页中提供了表工具列表。

### <a name="is-the-concurrency-on-operations-controlled"></a>操作并发性是否受控制？

是的，通过使用 ETag 机制提供乐观并发。

### <a name="is-the-odata-query-model-supported-for-entities"></a>是否支持针对实体的 OData 查询模型？

是，表 API 支持 OData 查询和 LINQ 查询。

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>是否可以在同一应用程序中同时连接到 Azure 表存储和 Azure Cosmos DB 表 API？

是的，可以通过创建两个不同的 CloudTableClient 实例进行连接，每个实例都通过连接字符串指向各自的 URI。

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>如何将现有 Azure 表存储应用程序迁移到此服务？

支持使用 [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 和 [Azure Cosmos DB 数据迁移工具](import-data.md)。

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>如何为此服务扩展存储大小，比如，最初我有 *n* GB 的数据，但一段时间后我的数据会增长到 1 TB？

根据设计，可以通过横向缩放让 Azure Cosmos DB 提供无限的存储。 可以通过服务来监视并有效地增大存储。

### <a name="how-do-i-monitor-the-table-api-offering"></a>如何监视表 API 服务？

可以使用表 API 的“指标”  窗格来监视请求和存储使用情况。

### <a name="how-do-i-calculate-the-throughput-i-require"></a>如何计算所需的吞吐量？

可以使用容量估算器来计算操作所需的 TableThroughput。 有关详细信息，请参阅 [Estimate Request Units and Data Storage](https://www.documentdb.com/capacityplanner)（估算请求单位和数据存储）。 通常，可以将实体显示为 JSON 并且为操作提供所需数量。

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>是否可以在本地将表 API SDK 用于模拟器？

目前没有。

### <a name="can-my-existing-application-work-with-the-table-api"></a>现有的应用程序是否适用于表 API？

是的，支持相同的 API。

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>如果我不想使用表 API 功能，是否需要将现有 Azure 表存储应用程序迁移到该 SDK？

否，可以在没有任何干扰的情况下创建和使用现有 Azure 表存储资产。 但是，如果不使用表 API，则无法从自动索引、其他一致性选项或全局分发中受益。

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>如何在跨多个 Azure 区域的表 API 中添加数据复制？

可以使用 Azure Cosmos DB 门户的[全局复制设置](tutorial-global-distribution-sql-api.md#portal)添加适合应用程序的区域。 若要开发全球分布的应用程序，还应该将应用程序的 PreferredLocation 信息设置为本地区域，以实现较低的读取延迟。

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>如何在表 API 中更改帐户的主要写入区域？

可以使用 Azure Cosmos DB 的全局复制门户窗格添加区域，然后故障转移到所需区域。 有关说明，请参阅[使用多区域 Azure Cosmos DB 帐户进行开发](high-availability.md)。

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>如何配置首选的读取区域以降低分配数据时的延迟？

请使用 app.config 文件中的 PreferredLocation 键，方便从本地位置读取。 对于现有应用程序，如果设置 LocationMode，表 API 会引发错误。 请删除该代码，因为表 API 会从 app.config 文件中选取此信息。 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>如何理解表 API 中的一致性级别？

Azure Cosmos DB 在一致性、可用性和延迟之间提供合理的平衡。 Azure Cosmos DB 为表 API 开发人员提供五个一致性级别，因此可以在表级别选择合适的一致性模型，并在查询数据时发出相应的请求。 客户端可以在连接时指定一致性级别。 可以通过 CreateCloudTableClient 的 consistencyLevel 参数更改级别。

如果将“有限过时”一致性设置为默认值，表 API 可通过“读取自己写入的数据”提供低延迟的读取。 有关详细信息，请参阅[一致性级别](consistency-levels.md)。

Azure 表存储默认在区域中提供“强”一致性，在辅助位置提供“最终”一致性。

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB 表 API 是否比 Azure 表存储提供更多的一致性级别？

是的。若要了解如何利用 Azure Cosmos DB 的分布式特性，请参阅[一致性级别](consistency-levels.md)。 由于一致性级别有充分的保证，因此可以放心使用。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>启用全局分发后，完成数据的复制需要多久？

Azure Cosmos DB 会在本地区域持续提交数据，然后在几毫秒内将数据立即推送到其他区域。 这种形式的复制只取决于数据中心的往返时间 (RTT)。 若要详细了解 Azure Cosmos DB 的全局分发功能，请参阅 [Azure Cosmos DB：Azure 上的全球分布式数据库服务](distribute-data-globally.md)。

### <a name="can-the-read-request-consistency-level-be-changed"></a>是否可以更改读取请求一致性级别？

使用 Azure Cosmos DB 时，可以在容器级别（在表上）设置一致性级别。 使用 .NET SDK，可以通过在 app.config 文件中提供 TableConsistencyLevel 键值来更改级别。 可能的值包括：“强”、“有限过期”、“会话”、“一致前缀”和“最终”。 有关详细信息，请参阅 [Azure Cosmos DB 中可以优化的数据一致性级别](consistency-levels.md)。 关键是不能将请求的一致性级别设置为高于表的一致性级别。 例如，不能将表的一致性级别设置为“最终”，而将请求的一致性级别设置为“非常”。

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>在某个区域出现故障时，表 API 如何处理故障转移？

表 API 利用 Cosmos DB 的全球分布式平台。 若要确保应用程序能够容许数据中心停机，请在 Azure Cosmos DB 门户中为帐户额外启用至少一个区域。详见[使用多区域 Azure Cosmos DB 帐户进行开发](high-availability.md)。 可以使用门户设置区域的优先级。请参阅[使用多区域 Azure Cosmos DB 帐户进行开发](high-availability.md)。

可以视需要为帐户添加任意数目的区域，并通过提供故障转移优先级来控制可将该帐户故障转移到哪个位置。 若要使用数据库，还需要在那里提供一个应用程序。 这样，客户就不会遇到停机情况。 [最新的 .NET 客户端 SDK](table-sdk-dotnet.md) 可自动寻址，但其他 SDK 则不可以。 换句话说，它能够检测到有故障的区域，并自动故障转移到新区域。

### <a name="is-the-table-api-enabled-for-backups"></a>是否能够为表 API 启用备份？

可以，表 API 利用 Azure Cosmos DB 的平台进行备份。 可自动进行备份。 有关详细信息，请参阅[使用 Azure Cosmos DB 进行联机备份和还原](online-backup-and-restore.md)。

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>表 API 是否默认对实体的所有属性编制索引？

是的，默认情况下会为实体的所有属性编制索引。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](index-policy.md)。

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>这是否意味着，无需创建多个索引来满足查询要求？

是，Azure Cosmos DB 表 API 针对所有属性提供自动索引，无需任何架构定义。 这种自动化操作可以让开发人员将工作重点放在应用程序上，不必考虑索引的创建和管理。 有关详细信息，请参阅 [Azure Cosmos DB：索引策略](index-policy.md)。

### <a name="can-i-change-the-indexing-policy"></a>是否可以更改索引策略？

是的，可以通过提供索引定义来更改索引策略。 需要在 app.config 文件中使用字符串 json 格式

对于非 .NET SDK，只能在门户中设置索引策略，方法是：打开“数据资源管理器”，导航到想要更改的特定表，转到“缩放和设置”->“索引策略”，进行所需的更改，并单击“保存”。   

对于 .NET SDK，可以在 app.config 文件中提交更改：

```JSON
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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>平台形式的 Azure Cosmos DB 似乎有许多的功能，例如排序、聚合、分层等。 是否要向表 API 添加这些功能？

表 API 提供与 Azure 表存储相同的查询功能。 Azure Cosmos DB 还支持排序、聚合、地理空间查询、层次结构和各种内置函数。 有关详细信息，请参阅 [SQL 查询](how-to-sql-query.md)。

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>何时应更改表 API 的 TableThroughput？

应在符合以下某个条件时，更改 TableThroughput：

* 要对数据执行提取、转换和加载 (ETL) 操作，或者需在短时间内上传大量数据。
* 需要后端的容器或容器组提供更大的吞吐量。 例如，发现已用吞吐量超过预配吞吐量，且吞吐量已达到限制。 有关详细信息，请参阅[为 Azure Cosmos 容器设置吞吐量](set-throughput.md)。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>是否可以纵向扩展纵向缩减表 API 表的吞吐量？

是的，可以使用 Azure Cosmos DB 门户的缩放窗格来缩放吞吐量。 有关详细信息，请参阅[设置吞吐量](set-throughput.md)。

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>新预配的表是否设置了默认的 TableThroughput？

是，如果未通过 app.config 替代 TableThroughput，并且未使用 Azure Cosmos DB 中预创建的容器，服务则会创建吞吐量为 400 的表。

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>对于 Azure 表存储服务的现有客户，定价是否有任何变化？

无。 对于现有的 Azure 表存储客户，价格上没有任何更改。

### <a name="how-is-the-price-calculated-for-the-table-api"></a>表 API 的价格是如何计算的？

价格取决于分配的 TableThroughput。

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>如何在表 API 服务中处理对表设置的任何速率限制？

如果请求速率超出了为基础容器或容器组预配的吞吐量的容量，则会出现错误，SDK 会使用重试策略重试调用。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>为何需要选择吞吐量而不是 PartitionKey 和 RowKey 来利用 Azure Cosmos DB 的表 API 服务？

如果未在 app.config 文件中或通过门户提供吞吐量，Azure Cosmos DB 将为容器设置默认的吞吐量。

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保证。 如果引擎可以针对租户的操作实施调控，则可以提供这样的保证。 设置 TableThroughput 可确保吞吐量和延迟值得到保证，因为平台会保留相应的容量，保证操作成功。

考虑到应用程序在使用需求方面存在“季节性”，可以通过吞吐量规范来弹性更改吞吐量，在满足吞吐量需求的同时节省成本。

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure 表存储对我而言非常便宜，因为我只需支付数据的存储费用，并且我很少进行查询。 但是，即使我未执行任何事务或存储任何数据，Azure Cosmos DB 表 API 服务似乎也要收费。 是否能解释一下？

Azure Cosmos DB 设计为一个全球分布的、基于 SLA 的系统，在可用性、延迟和吞吐量方面提供保障。 在 Azure Cosmos DB 中保留吞吐量时，获得的保障与其他系统不同。 Azure Cosmos DB 还提供客户长期以来一直期盼的其他功能，例如辅助索引和全局分发。

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>在向 Azure 表存储引入数据时，我从未收到过“配额已满”通知（指示分区已满）。 但使用表 API 时会收到此消息。 是此产品有限制，迫使我更改现有的应用程序吗？

Azure Cosmos DB 是基于 SLA 的系统，可提供无限缩放，并在延迟、吞吐量、可用性和一致性方面提供保证。 请确保数据大小和索引可管理且可缩放，这样才能获得有保障的高级性能。 针对每个分区键的实体或项数实施 10 GB 限制是为了确保提供优异的查找和查询性能。 若要确保即使针对 Azure 存储，应用程序也能很好地进行缩放，建议*不要*创建热分区，即，将所有信息存储在一个分区内并查询它。

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>表 API 是否仍然需要 PartitionKey 和 RowKey？

是的。 由于表 API 的外围应用类似于 Azure 表存储 SDK，因此使用分区键可以高效地分发数据。 行键在该分区中是唯一的。 行键必须存在，且不能为 null（在标准 SDK 中可为 null）。 RowKey 的长度为 255 个字节，PartitionKey 的长度为 1 KB。

### <a name="what-are-the-error-messages-for-the-table-api"></a>表 API 的错误消息有哪些？

Azure 表存储和 Azure Cosmos DB 表 API 使用相同的 SDK，因此，大多数错误是相同的。

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>在表 API 中尝试一个接一个地创建许多表时，为何会受到限制？

Azure Cosmos DB 是基于 SLA 的系统，在可用性、延迟和吞吐量方面提供保障。 由于它是预配的系统，因此会保留资源来保证满足这些要求。 以极快的频率创建表会被系统检测到，因此会受到限制。 建议你查看表的创建频率，将其降到每分钟 5 个表以下。 请记住，表 API 是预配的系统。 只要预配它，就必须付费。

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>如何提供有关 SDK 或 Bug 的反馈？

可通过以下途径提供反馈：

* [用户之声](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow 适合编程问题。 请确保提问[切中主题](https://stackoverflow.com/help/on-topic)并[尽可能提供较多的详细信息，使问题清楚明了，便于回答](https://stackoverflow.com/help/how-to-ask)。

## <a name="next-steps"></a>后续步骤

* [使用 .NET SDK 和 Azure Cosmos DB 生成表 API 应用](create-table-dotnet.md)
* [生成 Java 应用以管理 Azure Cosmos DB 表 API 数据](create-table-java.md)