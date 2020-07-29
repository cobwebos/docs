---
title: 有关用于 Azure Cosmos DB 的 Cassandra API 的常见问题解答
description: 获取有关 Azure Cosmos DB 的 Cassandra API 的常见问题的解答
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 04708a307cd0eedfbe0510324930eb2327adf06e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449730"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>有关 Azure Cosmos DB 中 Cassandra API 的常见问题解答。

本文介绍了 Azure Cosmos DB 中 Apache Cassandra 与 Cassandra API 之间的功能差异。 还提供了有关 Azure Cosmos DB 中 Cassandra API 的常见问题解答。

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Apache Cassandra 和 Cassandra API 之间的主要差异

- Apache Cassandra 建议分区键的大小限制为 100 MB。 用于 Azure Cosmos DB 的 Cassandra API 允许每个分区最多 20 GB。
- Apache Cassandra 允许禁用持久提交。 可以跳过写入提交日志，直接转到 memtable。 如果在将 memtable 刷新到磁盘上的 SSTables 之前节点出现故障，则可能导致数据丢失。 Azure Cosmos DB 始终执行持久提交，以帮助防止数据丢失。
- 如果工作负荷涉及多次替换或删除，Apache Cassandra 可能会降低性能。 原因是“读取工作负荷”需要跳过逻辑删除来提取最新数据。 当工作负荷有许多替换操作或删除操作时，Cassandra API 不会降低读取性能。
- 在高替换工作负载的情况下，需要运行压缩来合并磁盘上的 SSTables。 （需要进行合并的原因是 Apache Cassandra 的写入仅为追加。 多个更新都存储为需要进行定期合并的单个 SSTable 条目。 这种情况还可能会导致压缩期间读取性能降低。 Cassandra API 中不会对性能造成影响，因为 API 不实现压缩。
- 使用 Apache Cassandra 可以将复制因子设置为 1。 但是，如果包含数据的唯一节点出现故障，则会导致低可用性。 这并不是用于 Azure Cosmos DB 的 Cassandra API 所导致的问题，因为复制因子始终为 4（仲裁为 3）。
- 在 Apache Cassandra 中添加或删除节点需要手动干预，且由于现有节点将部分标记范围转移到新节点，新节点上的 CPU 使用率较高。 停止使用某个现有节点时，会发生同样的情况。 但是，Cassandra API 横向扩展时，服务或应用程序中不会产生任何问题。
- 不需要像在 Apache Cassandra 中那样在群集中每个节点上设置 num_tokens。 Azure Cosmos DB 完全托管节点和标记范围。
- Cassandra API 是完全托管的。 不需要 Apache Cassandra 中使用的 nodetool 命令，例如修复和解除授权。

## <a name="other-frequently-asked-questions"></a>其他常见问题解答

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Cassandra API 支持哪个协议版本？

用于 Azure Cosmos DB 的 Cassandra API 支持 CQL 版本 3.x。 它的 CQL 兼容性基于公共 [Apache Cassandra GitHub 存储库](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)。 如果有与支持其他协议相关的反馈，请通过 [UserVoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)告知我们或者向 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 发送电子邮件。

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>为何要求选择表的吞吐量？

Azure Cosmos DB 根据以下表的创建位置设置容器的默认吞吐量：Azure 门户或 CQL。

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 如果引擎可以针对租户的操作实施调控，则可以提供这些保证。 设置吞吐量可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。
可以[灵活更改吞吐量](manage-scale-cassandra.md)以便从应用程序的季节性因素中受益并节省成本。

[Azure Cosmos DB 中的请求单位数](request-units.md)一文介绍了吞吐量概念。 表的吞吐量平均分布到各个基础物理分区中。

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>通过 CQL 创建的表的吞吐量是多少？

Azure Cosmos DB 使用每秒请求单位数 (RU/s) 作为所提供的吞吐量的单位。 通过 CQL 创建的表的默认吞吐量为 400 RU。 可以通过 Azure 门户更改 RU。

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

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 如果引擎可以针对租户的操作实施调控，则可以提供这些保证。 设置吞吐量可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。

当超出此容量时，会收到以下错误消息，指出已耗尽容量：

0x1001 超载：无法处理此请求，因为“请求速率太大” 

必须查明是哪些操作（及其数据量）导致了此问题。 可以通过 Azure 门户上的指标了解超出了预配容量的已消耗容量。 然后，你需要确保容量差不多是在所有基础分区中平均消耗的。 如果你看到一个分区在消耗大多数吞吐量，则说明存在工作负载倾斜。

相关指标显示了吞吐量在若干小时内、若干天内以及每七天内在各个分区中的使用情况或总体使用情况。 有关详细信息，请参阅[使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)。

[Azure Cosmos DB 诊断日志记录](logging.md)一文中介绍了诊断日志。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>主键是否映射到 Azure Cosmos DB 的分区键概念？

是，分区键用来将实体放置在正确位置。 在 Azure Cosmos DB 中，它用来查找存储在物理分区中的正确逻辑分区。 [在 Azure Cosmos DB 中分区和缩放](partition-data.md)一文中很好地解释了分区概念。 此处，必须记住的一点是，逻辑分区不应当超出 10-GB 限制。

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>当收到分区已满通知时，会发生什么情况？

Azure Cosmos DB 是基于服务级别协议 (SLA) 的系统。 可提供无限缩放，并在延迟、吞吐量、可用性和一致性方面提供保障。 此无限制的存储是通过使用分区作为键概念的数据水平横向扩展实现的。 [在 Azure Cosmos DB 中分区和缩放](partition-data.md)一文中很好地解释了分区概念。

应当遵循每个逻辑分区的实体数或项数不超过 10-GB 的限制。 为确保应用程序能够很好地进行缩放，建议*不要*创建热分区，即，将所有信息存储在一个分区内并查询它。 只有存在数据倾斜时，也就是说，当一个分区键有大量数据（超过 10&nbsp;GB）时，才会发生此错误。 可以使用存储门户查明数据的分布。 修复此错误的方法是：重新创建表并选择一个细粒度的主键（分区键），这可以实现更好的数据分布。

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>是否可以将 Cassandra API 用作具有数百万或数十亿分区键的键值存储？

Azure Cosmos DB 可以通过对存储进行横向扩展来存储无限的数据。 此存储与吞吐量无关。 是的，你始终可以使用 Cassandra API 通过指定正确的主键/分区键来存储和检索键和值。 这些单独的键获取其自己的逻辑分区并存在于物理分区之上，且不会出现问题。

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>能否用 Cassandra API 创建多个表？

是的，可以使用 Cassandra API 创建多个表。 这些表中的每一个都被视为用于吞吐量和存储的单元。

### <a name="can-i-create-more-than-one-table-in-succession"></a>是否可以连续创建多个表？

Azure Cosmos DB 是资源调控的系统，适用于数据和控制平面活动。 与集合和表一样，容器是针对给定吞吐量容量预配的运行时实体。 快速连续创建这些容器是非预期的活动，可能会被限制。 如果存在立即删除或创建表的测试，请尽量将它们隔开。

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>最多可以创建几个表？

表的数量没有实际限制。 如果需要创建大量表（总大小超过 10 TB 的数据），而不是常见的数十个或几百个，请发送电子邮件到 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)。

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>最多可以创建几个键空间？

键空间的数量没有实际限制，因为它们是元数据容器。 如果有大量键空间，请发送电子邮件到 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)。

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>从普通的表启动后，是否可以引入大量数据？

是的。 假设均匀分布分区，存储容量会自动管理，并在推入更多的数据时增加。 因此，可放心地导入所需数量的数据，不需要管理和预配节点等等。 但是，如果预期数据随即会有大量增长，比较有意义的做法是直接[预配预期的吞吐量](set-throughput.md)，而不是一开始预配较低的吞吐量，然后随即增加。

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>是否可以使用 YAML 文件设置来配置 API 行为？

用于 Azure Cosmos DB 的 Cassandra API 提供协议级的兼容性来执行操作。 它消除了管理、监视和配置的复杂性。 开发人员/用户无需担心可用性、逻辑删除、键缓存、行缓存、布隆筛选器和其他许多设置。 Cassandra API 注重于提供所需的读取和写入性能，不会产生配置和管理开销。

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Cassandra API 是否支持节点添加、群集状态和节点状态命令？

Cassandra API 可简化容量规划，并应对吞吐量和存储方面的弹性需求。 借助 Azure Cosmos DB，你可以预配所需的吞吐量。 然后，可以在一整天中多次上调和下调吞吐量，而无需担心如何添加、删除或管理节点。 节点和群集管理不需要使用工具。

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>在处理有关创建键空间的各项配置设置（例如简单/网络）时，会发生什么情况？

Azure Cosmos DB 为可用性和低延迟原因提供现成的全局分发。 不需要设置副本或其他内容。 写入内容均在你进行写入的区域中经过仲裁永久认可，同时提供性能保障。

### <a name="what-happens-with-various-settings-for-table-metadata"></a>表元数据的各种设置会产生什么效果？

Azure Cosmos DB 为读取、写入和吞吐量提供性能保障。 因此，无需担心改动配置设置或不小心实施了手动操控会出现问题。 这些设置包括布隆筛选器、缓存、读取修复机会、gc_grace 和压缩 memtable_flush_period。

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Cassandra 表是否支持生存时间？

是的，支持 TTL。

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>如何监视基础结构以及吞吐量？

Azure Cosmos DB 是一个平台服务，可帮助你提高工作效率，而无需担心如何管理和监视基础结构。 例如，你无需使用各种工具监视节点状态、副本状态、gc 和 OS 参数。 只需在门户指标中关注可用的吞吐量，以查明你是否受到限制，然后增大或减小该吞吐量。 方法：

- 监视 [SLA](monitor-accounts.md)
- 使用[指标](use-metrics.md)
- 使用[诊断日志](logging.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>哪些客户端 SDK 可以使用 Cassandra API？

Apache Cassandra SDK 的使用 CQLv3 的客户端驱动程序用于客户端程序。 如果你有其他使用的驱动程序，或者如果遇到问题，请将邮件发送到 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 。

### <a name="are-composite-partition-keys-supported"></a>是否支持复合分区键？

是的，可以使用正则语法创建复合分区键。

### <a name="can-i-use-sstableloader-for-data-loading"></a>是否可以使用 sstableloader 加载数据？

不可以，不支持 sstableloader。

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>是否可以将本地 Apache Cassandra 群集与 Cassandra API 配对？

目前，Azure Cosmos DB 针对云环境提供了优化的体验，且不产生操作开销。 如果需要配对，请将邮件发送到， [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 并提供方案说明。 我们正致力于提供一项产品/服务，以便帮助用户将本地或云 Cassandra 群集与用于 Azure Cosmos DB 的 Cassandra API 配对。

### <a name="does-the-cassandra-api-provide-full-backups"></a>Cassandra API 是否提供完整备份？

Azure Cosmos DB 的所有 API 都提供间隔四小时的两个免费完整备份。 因此，无需设置备份计划。 

如果想要修改保留期和频率，请发送电子邮件到 [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) 或提交支持案例。 [Azure Cosmos DB 的自动联机备份和还原](online-backup-and-restore.md)一文中提供了有关备份功能的信息。

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>当某个区域出现故障时，Cassandra API 帐户如何处理故障转移？

Cassandra API 从 Azure Cosmos DB 的全局分布式平台借用。 为了确保应用程序能够容许数据中心停机，需在 Azure 门户中至少再为帐户启用一个区域。 有关详细信息，请参阅[使用 Azure Cosmos DB 实现高可用性](high-availability.md)。

可以视需要为帐户添加任意数目的区域，并通过提供故障转移优先级来控制可将该帐户故障转移到哪个位置。 若要使用数据库，还需要在那里提供一个应用程序。 这样，客户就不会遇到停机情况。

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Cassandra API 是否默认对实体的所有属性编制索引？

否。 Cassandra API 支持[辅助索引](cassandra-secondary-index.md)，后者与 Apache Cassandra 的行为类似。 API 默认不会对每个属性编制索引。  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>是否可以在本地将新的 Cassandra API SDK 用于模拟器？

是，系统支持该操作。 可以在[使用 Azure Cosmos 模拟器进行本地开发和测试](local-emulator.md#cassandra-api) 文章中找到有关如何启用此操作的详细信息。


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>如何将数据从 Apache Cassandra 群集迁移到 Azure Cosmos DB？

可以通过[将数据迁移到 Azure Cosmos DB 中的 Cassandra API 帐户](cassandra-import-data.md)教程，了解迁移选项。


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>在哪里可以提供有关 Cassandra API 功能的反馈？

请通过 [UserVoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)提供反馈。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>后续步骤

- 开始使用[弹性缩放 Azure Cosmos DB Cassandra API 帐户](manage-scale-cassandra.md)。
