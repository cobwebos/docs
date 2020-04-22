---
title: 关于卡桑德拉的 Azure 宇宙 DB API 的常见问题。
description: 获取有关 Cassandra 的 Azure Cosmos DB API 的常见问题的解答。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727380"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>关于卡桑德拉的 Azure 宇宙 DB API 的常见问题

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>阿帕奇·卡桑德拉和卡桑德拉 API 之间有哪些主要区别？

- Apache Cassandra 建议对分区密钥的大小限制 100MB。 Cassandra API 允许每个分区最多 10GB。
- Apache Cassandra 允许您禁用持久提交 - 即跳过写入提交日志并直接转到 Memtable。 如果节点在将 Memtables 刷新到磁盘上的 SStables 之前出现故障，则可能导致数据丢失。 Cosmos DB 始终执行持久提交，因此您永远不会丢失数据。
- 如果工作负载涉及大量替换和/或删除，Apache Cassandra 的性能可能会降低。 原因是读取工作负载需要跳过以获取最新数据的墓碑。 当工作负载有很多替换和/或删除时，Cassandra API 的读取性能不会降低。
- 在高替换工作负载方案中，需要运行压缩以在磁盘上合并 SSTable（因为 Apache Cassandra 的写入仅追加，因此多个更新存储为需要定期合并的单个 SSTable 条目）。 这还可能导致压缩期间的读取性能降低。 这在 Cassandra API 中不会发生，因为它不实现压实。
- 使用 Apache Cassandra 可以设置复制因子 1。 但是，如果只有具有数据的节点出现故障，则会导致可用性低。 这不是 Azure Cosmos DB Cassandra API 的问题，因为复制因子始终为 4（仲裁为 3）。
- 在 Apache Cassandra 中添加/删除节点需要大量的手动干预，但还需要在新节点上使用高 CPU，而现有节点会将其令牌范围移动到新节点。 停用现有节点时，情况相同。 但是，在 Azure Cosmos DB Cassandra API 中，扩展将无缝地完成，在服务/应用程序中没有任何问题。
- 无需像 Apache Cassandra 那样在群集中的每个节点上设置num_tokens。 节点和令牌范围完全由 Cosmos DB 管理。
- Azure Cosmos DB Cassandra API 已完全管理，因此您不需要 Apache Cassandra 中使用的节点工具命令（如修复、停用等）。

## <a name="other-frequently-asked-questions"></a>其他常见问题解答

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Azure 宇宙 DB Cassandra API 支持的协议版本是什么？ 是否打算支持其他协议？

Azure 宇宙 DB Cassandra API 支持 CQL 版本 3.x。 它的CQL兼容性是基于公共[阿帕奇卡桑德拉GitHub存储库](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)。 如果有与支持其他协议相关的反馈，请通过 [UserVoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)告知我们或者向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>为何要求选择表的吞吐量？

Azure Cosmos DB 根据表的创建位置（门户或 CQL）设置容器的默认吞吐量。
Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 如果引擎可以针对租户的操作实施调控，则可以做到这一点。 设置吞吐量可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。
您可以[弹性地更改吞吐量](manage-scale-cassandra.md)，以受益于应用程序的季节性并节省成本。

[Azure Cosmos DB 中的请求单位数](request-units.md)一文介绍了吞吐量概念。 表的吞吐量平均分布到各个基础物理分区中。

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>通过 CQL 创建的表的默认吞吐量是多少 RU/s？ 如何更改该默认值？

Azure Cosmos DB 使用每秒请求单位数 (RU/s) 作为所提供的吞吐量的单位。 通过 CQL 创建的表具有 400 RU。 可以通过门户更改 RU。

CQL

```shell
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

### <a name="what-happens-when-throughput-is-used-up"></a>耗尽吞吐量时会发生什么情况？

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 如果引擎可以针对租户的操作实施调控，则可以做到这一点。 这可以通过设置吞吐量来实现，并可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。
当超出此容量时，会收到超载错误消息，指出已耗尽容量。
0x1001 超载：无法处理此请求，因为“请求速率太大”。 此时，必须查明是哪些操作及其数据量导致了此问题。 可以通过门户中的指标了解超出了预配容量的已消耗容量。 然后，你需要确保容量差不多是在所有基础分区中平均消耗的。 如果你看到大多数吞吐量是由一个分区消耗的，则说明存在工作负载倾斜。

相关指标显示了吞吐量在若干小时内、若干天内以及每七天内在各个分区中的使用情况或总体使用情况。 有关详细信息，请参阅[使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)。

[Azure Cosmos DB 诊断日志记录](logging.md)一文中介绍了诊断日志。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>主键是否映射到 Azure Cosmos DB 的分区键概念？

是，分区键用来将实体放置在正确位置。 在 Azure Cosmos DB 中，它用来查找存储在物理分区中的正确逻辑分区。 [在 Azure Cosmos DB 中分区和缩放](partition-data.md)一文中很好地解释了分区概念。 此处，必须记住的一点是，逻辑分区目前不应当超出 10-GB 限制。

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>当收到指示分区已满的“配额已满”通知时，会发生什么情况？

Azure Cosmos DB 是基于 SLA 的系统，可提供无限缩放，并在延迟、吞吐量、可用性和一致性方面提供保障。 此无限制的存储是通过使用分区作为键概念的数据水平横向扩展实现的。 [在 Azure Cosmos DB 中分区和缩放](partition-data.md)一文中很好地解释了分区概念。

应当遵循每个逻辑分区的实体数或项数不超过 10 GB 的限制。 为确保应用程序能够很好地进行缩放，建议*不要*创建热分区，即，将所有信息存储在一个分区内并查询它。 只有当存在数据倾斜时，也就是说，当一个分区键有大量数据（超过 10&nbsp;GB）时，才会发生此错误。 可以使用存储门户查明数据的分布。 若要修复此错误，请重新创建表并选择一个细粒度的主键（分区键），这可以实现更好的数据分布。

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>是否可以将 Cassandra API 作为键值存储用于数百万或数十亿单独的分区键？

Azure Cosmos DB 可以通过对存储进行横向扩展来存储无限的数据。 这与吞吐量无关。 你始终可以仅使用 Cassandra API 通过指定正确的主键/分区键来存储和检索键/值。 这些单独的键获取其自己的逻辑分区并存在于物理分区之上，且不会出现问题。

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>是否可以使用 Azure Cosmos DB 的 Apache Cassandra API 创建多个表？

是的，可以使用 Apache Cassandra API 创建多个表。 这些表中的每一个都被视为用于吞吐量和存储的单元。

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>是否可以连续创建多个表？

Azure Cosmos DB 是资源调控的系统，适用于数据和控制平面活动。 与集合和表一样，容器是针对给定的吞吐量容量预配的运行时实体。 快速连续创建这些容器是非预期的活动，会被限制。 如果存在立即删除/创建表的测试，请尽量将它们隔开。

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>最多可以创建几个表？

表数没有物理限制，[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)如果您有大量表（总稳定大小超过 10 TB 的数据）需要从通常的 10s 或 100s 创建，请发送电子邮件。

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>最多可以创建多少个键空间？

由于密钥空间是元数据容器，因此对键空间的数量没有物理限制，如果您由于某种原因拥有大量键[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)空间，请发送电子邮件。

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>从普通的表启动后，是否可以引入大量数据？

是的，假设统一分布的分区，存储容量会自动管理，并在您推送更多数据时增加。 因此，您可以放心地导入所需数量的数据，不需要管理和预配节点等等。 但是，如果您预计数据会立即增长，则直接[预配预期的吞吐量](set-throughput.md)而不是立即开始降低和增加吞吐量更有意义。

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>是否可以提供 yaml 文件设置来配置 Azure Cosmos DB 的 Apache Casssandra API 行为？

Azure Cosmos DB 的 Apache Cassandra API 是一项平台服务。 它提供协议级的兼容性来执行操作。 它消除了管理、监视和配置的复杂性。 开发人员/用户无需担心可用性、逻辑删除、键缓存、行缓存、布隆筛选器和其他许多设置。 Azure Cosmos DB 的 Apache Cassandra API 注重于提供所需的读取和写入性能，不会产生配置和管理开销。

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Azure Cosmos DB 的 Apache Cassandra API 是否支持节点添加/群集状态/节点状态命令？

Apache Cassandra API 是一项平台服务，使用它可以轻松做出容量规划，以及应对吞吐量和存储方面的弹性需求。 借助 Azure Cosmos DB，可以预配所需的吞吐量。 然后，可以在一整天中多次上调和下调吞吐量，而无需担心如何添加/删除或管理节点。 这意味着也不需要使用节点、群集管理工具。

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>有关创建键空间的各项配置设置（例如简单/网络）会是怎样的？

出于可用性和低延迟的原因，Azure Cosmos DB 提供现成的全局分发。 不需要设置副本或其他内容。 所有写入均在你进行写入的区域中经过仲裁永久认可，同时提供性能保障。

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>布隆筛选器、缓存、读取修复更改、gc_grace 和 compression memtable_flush_period 等表元数据的各项设置有什么变化？

Azure Cosmos DB 为读/写操作提供性能和吞吐量，无需更改任何配置设置，也不会意外处理这些设置。

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Cassandra 表是否支持生存时间 (TTL)？

是的，支持 TTL。

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>以前是否可以使用各种工具监视节点状态、副本状态、gc 和 OS 参数？ 现在需要监视什么？

Azure Cosmos DB 是一个平台服务，可帮助你提高工作效率，而无需担心如何管理和监视基础结构。 只需在门户指标中关注可用的吞吐量，以查明你是否受到限制并增大或减小该吞吐量。
监视 [SLA](monitor-accounts.md)。
使用[指标](use-metrics.md) 使用[诊断日志](logging.md)。

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>哪些客户端 SDK 适用于 Azure Cosmos DB 的 Apache Cassandra API？

Apache Cassandra SDK 的使用 CQLv3 的客户端驱动程序用于客户端程序。 如果您有其他驱动程序，您使用或如果您面临问题，请发送邮件到[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)。

### <a name="is-composite-partition-key-supported"></a>是否支持复合分区键？

是的，可以使用正则语法创建复合分区键。

### <a name="can-i-use-sstableloader-for-data-loading"></a>我可以使用稳定加载器进行数据加载吗？

否，不支持稳定加载器。

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>本地 Apache Cassandra 群集是否可与 Azure Cosmos DB 的 Cassandra API 配对？

目前，Azure Cosmos DB 针对云环境提供了优化的体验，且不产生操作开销。 如果需要配对，请发送邮件，[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)并说明您的方案。 我们正致力于提供一项服务，以便帮助用户将本地的 Cassandra 群集或不同的云 Cassandra 群集与 Cosomos DB 的 Cassandra API 配对。

### <a name="does-cassandra-api-provide-full-backups"></a>Cassandra API 是否提供完整备份？

Azure Cosmos DB 的所有 API 目前都提供间隔四小时的两个免费完整备份。 这可确保你不需要设置备份计划和其他配置。
如果要修改保留率和频率，请发送电子邮件或[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)引发支持案例。 [Azure Cosmos DB 的自动联机备份和还原](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)一文中提供了有关备份功能的信息。

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>当某个区域出现故障时，Cassandra API 帐户如何处理故障转移？

Azure Cosmos DB Cassandra API 借助 Azure Cosmos DB 的全球分布式平台。 若要确保应用程序能够容许数据中心停机，需在 Azure Cosmos DB 门户中至少再为帐户启用一个区域（[使用多区域 Azure Cosmos DB 帐户进行开发](high-availability.md)）。 可以通过使用门户设置区域的优先级（[使用多区域 Azure Cosmos DB 帐户进行开发](high-availability.md)）。

可为帐户添加任意数目的区域，并通过提供故障转移优先级控制可将帐户故障转移到的区域。 若要使用数据库，还需要在那里提供一个应用程序。 这样，客户就不会遇到停机情况。

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API 是否默认对实体的所有属性编制索引？

不是。 Cassandra API 支持[二级索引](cassandra-secondary-index.md)，其活动方式与 Apache Cassandra 非常相似。 默认情况下，它不索引每个属性。  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>是否可以在本地将新的 Cassandra API SDK 用于模拟器？

可以，支持该操作。 您可以[在此处](local-emulator.md#cassandra-api)找到有关如何启用此功能的详细信息


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>如何将数据从其 Apache Cassandra 群集迁移到 Cosmos DB？

您可以在此处阅读有关迁移选项[的信息](cassandra-import-data.md)。


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>常规 Cassandra API 的功能 x 目前不工作，可以在哪里提供反馈？

请通过 [UserVoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)提供反馈。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>后续步骤

- 开始弹性[扩展 Azure 宇宙 DB Cassandra API 帐户](manage-scale-cassandra.md)。
