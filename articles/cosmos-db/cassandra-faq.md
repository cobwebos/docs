---
title: 有关 Azure Cosmos DB Cassandra API 的常见问题
description: 获取有关 Azure Cosmos DB Cassandra API 的常见问题的解答。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 7d4618382c31f0b1f2efa42fe87a6efe4bc85319
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82608245"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>有关 Azure Cosmos DB 中的 Cassandra API 的常见问题

本文介绍了 Azure Cosmos DB 中 Apache Cassandra 与 Cassandra API 之间的功能差异。 它还提供有关 Azure Cosmos DB 中 Cassandra API 的常见问题的解答。

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Apache Cassandra 和 Cassandra API 之间的主要差异

- Apache Cassandra 建议对分区键大小的限制为 100 MB。 Azure Cosmos DB 的 Cassandra API 允许每个分区最多 20 GB。
- Apache Cassandra 允许禁用持久提交。 可以跳过写入提交日志，直接转到 memtables。 如果在将 memtables 刷新到磁盘上的 SSTables 之前节点出现故障，则可能导致数据丢失。 Azure Cosmos DB 始终执行持久提交，以帮助防止数据丢失。
- 如果工作负荷涉及多次替换或删除，Apache Cassandra 可能会看到性能下降。 原因是，读取工作负荷需要跳过来提取最新数据的逻辑删除。 当工作负荷有许多替换或删除时，Cassandra API 不会看到降低读取性能。
- 在高替换工作负载的情况下，需要运行压缩来合并磁盘上的 SSTables。 （需要进行合并，因为 Apache Cassandra 的写入只是 append。 多个更新存储为需要定期合并的单个 SSTable 条目。 这种情况还可能会导致压缩期间的读取性能降低。 Cassandra API 中不会对性能造成影响，因为 API 不实现压缩。
- 使用 Apache Cassandra 可以将复制系数设置为1。 但是，如果包含数据的唯一节点出现故障，则会导致可用性低。 这并不是 Azure Cosmos DB 的 Cassandra API 的问题，因为复制系数始终为4（仲裁为3）。
- 在 Apache Cassandra 中添加或删除节点需要手动干预，在新节点上的 CPU 使用率较高的情况下，在现有节点将部分标记范围移到新节点时。 当你取消现有节点时，这种情况是相同的。 但是，在服务或应用程序中不存在任何问题时，Cassandra API 会进行扩展。
- 不需要在群集中的每个节点上设置**num_tokens** ，就像在 Apache Cassandra 中一样。 Azure Cosmos DB 完全管理节点和标记范围。
- Cassandra API 完全托管。 不需要在 Apache Cassandra 中使用**nodetool**命令，例如 repair 和解除授权。

## <a name="other-frequently-asked-questions"></a>其他常见问题解答

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Cassandra API 支持哪个协议版本？

Azure Cosmos DB 的 Cassandra API 支持 CQL 版本1.x。 它的 CQL 兼容性基于公共[Apache Cassandra GitHub 存储库](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)。 如果你有关于支持其他协议的反馈，请通过[用户语音反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)或向发送电子邮件[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)来了解相关信息。

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>为什么要选择表的吞吐量？

Azure Cosmos DB 基于从其创建表的位置设置容器的默认吞吐量： Azure 门户或 CQL。

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 当引擎可以强制执行租户的操作时，可以确保这些保证。 设置吞吐量可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。
你可以[弹性更改吞吐量](manage-scale-cassandra.md)，以利用应用程序的季节性，并节省成本。

[Azure Cosmos DB 中的请求单位数](request-units.md)一文介绍了吞吐量概念。 表的吞吐量在底层物理分区上均匀分布。

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>通过 CQL 创建的表的吞吐量是多少？

Azure Cosmos DB 使用每秒请求单位数（RU/s）作为提供吞吐量的货币。 默认情况下，通过 CQL 创建的表具有 400 RU。 可以从 Azure 门户更改 RU。

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

Azure Cosmos DB 针对操作设置上限，在性能和延迟方面提供保障。 当引擎可以强制执行租户的操作时，可以确保这些保证。 设置吞吐量可确保在吞吐量和延迟方面获得保障，因为平台会保留此容量，并保证操作成功。

当你接管此容量时，你将收到以下错误消息，指出你的容量已用完：

**0x1001 超载：无法处理请求，因为 "请求速率很大"** 

必须查看哪些操作（及其卷）导致了此问题。 你可以使用 Azure 门户上的指标来了解已使用的容量。 然后，需要确保容量在所有底层分区上几乎相等。 如果你看到某个分区占用了大部分吞吐量，则你的工作负荷会有偏差。

提供了一些指标，这些指标显示了在几个小时、几天、每七天、多个分区或聚合中如何使用吞吐量。 有关详细信息，请参阅[使用 Azure Cosmos DB 中的指标进行监视和调试](use-metrics.md)。

[Azure Cosmos DB 诊断日志记录](logging.md)一文中介绍了诊断日志。

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>主键是否映射到 Azure Cosmos DB 的分区键概念？

是的，分区键用于将实体置于正确的位置。 在 Azure Cosmos DB 中，它用于查找存储在物理分区上的正确逻辑分区。 [在 Azure Cosmos DB 中分区和缩放](partition-data.md)一文中很好地解释了分区概念。 此处的基本要点在于是逻辑分区不应超过 10 GB 的限制。

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>当收到分区已满的通知时，会发生什么情况？

Azure Cosmos DB 是基于服务级别协议（SLA）的系统。 它提供无限制的缩放，并保证延迟、吞吐量、可用性和一致性。 此无限存储基于横向扩展数据，使用分区作为关键概念。 [在 Azure Cosmos DB 中分区和缩放](partition-data.md)一文中很好地解释了分区概念。

应将每个逻辑分区的实体或项的数量限制为 10 GB。 为确保应用程序能够很好地进行缩放，建议*不要*创建热分区，即，将所有信息存储在一个分区内并查询它。 仅当数据歪斜时，此错误才会出现：也就是说，你有一个分区键的大量数据（超过 10&nbsp;GB）。 可以通过使用存储门户来查找数据的分布情况。 修复此错误的方法是重新创建表，并选择粒度较高的主分区（分区键），这样可以更好地分布数据。

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>是否可以将 Cassandra API 用作包含数百万或数十亿分区键的键值存储？

Azure Cosmos DB 可以通过对存储进行横向扩展来存储无限的数据。 此存储与吞吐量无关。 是的，你始终可以使用 Cassandra API 仅通过指定正确的主/分区键来存储和检索密钥和值。 这些单独的密钥会获取自己的逻辑分区，并留在不出现问题的物理分区上。

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>能否用 Cassandra API 创建多个表？

是的，可以创建具有 Cassandra API 的多个表。 这些表中的每一个都被视为用于吞吐量和存储的单元。

### <a name="can-i-create-more-than-one-table-in-succession"></a>能否连续创建多个表？

Azure Cosmos DB 是用于数据和控制平面活动的资源控制系统。 容器（如集合和表）是针对给定吞吐量容量预配的运行时实体。 快速连续创建这些容器不是预期的活动，可能会受到限制。 如果你的测试会立即删除或创建表，请尝试将其设置为空白。

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>我可以创建的最大表数是多少？

表的数量没有物理限制。 如果有大量的表（其中总稳定大小超过 10 TB 的数据）需要创建，而不是常见的数十个或几百个，则将电子邮件发送到[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)。

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>我可以创建的最大大量数是多少？

大量数没有物理限制，因为它们是元数据容器。 如果有大量大量，请将电子邮件发送到[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)。

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>从普通表开始后，是否可以引入大量数据？

是的。 假设分散分区，存储容量会自动管理，并随着你推送更多数据而增加。 因此，你可以放心地导入所需数量的数据，无需管理和预配节点等等。 但是，如果您要预测大量数据的即时增长，则直接[预配以实现预期的吞吐量](set-throughput.md)，而不是从较低的吞吐量开始，而是立即增加。

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>是否可以使用 YAML 文件设置来配置 API 行为？

Azure Cosmos DB 的 Cassandra API 为执行操作提供了协议级别的兼容性。 它消除了管理、监视和配置的复杂性。 作为开发人员/用户，你无需担心可用性、逻辑删除、键缓存、行缓存、布隆筛选器和其他许多设置。 Cassandra API 重点提供所需的读取和写入性能，而无需配置和管理开销。

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Cassandra API 是否支持节点添加、群集状态和节点状态命令？

Cassandra API 简化了容量规划，并响应了吞吐量和存储的弹性需求。 在 Azure Cosmos DB 中，可以预配所需的吞吐量。 然后，你可以在一天中增加和减少任意数量的时间，而无需担心添加、删除或管理节点。 不需要使用工具进行节点和群集管理。

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>密钥空间创建的各种配置设置（如简单/网络）会发生什么情况呢？

Azure Cosmos DB 为可用性和低延迟原因提供现成的全局分发。 不需要设置副本或其他项。 写入始终是在你编写的任何区域中提交的持久仲裁，同时提供性能保证。

### <a name="what-happens-with-various-settings-for-table-metadata"></a>表元数据的各种设置会发生什么情况？

Azure Cosmos DB 提供读取、写入和吞吐量的性能保证。 因此，您无需担心触及任何配置设置并意外操作。 这些设置包括布隆筛选器、缓存、读取修复机会、gc_grace 和压缩 memtable_flush_period。

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Cassandra 表是否支持生存时间？

是的，支持 TTL。

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>如何监视基础结构以及吞吐量？

Azure Cosmos DB 是一个平台服务，可帮助你提高工作效率，而无需担心如何管理和监视基础结构。 例如，你不需要通过各种工具来监视节点状态、副本状态、gc 和 OS 参数。 只需处理门户指标中可用的吞吐量即可查看是否受到限制，然后增加或减少该吞吐量。 你可以：

- 监视[sla](monitor-accounts.md)
- 使用[度量值](use-metrics.md)
- 使用[诊断日志](logging.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>哪些客户端 Sdk 可以使用 Cassandra API？

使用 CQLv3 的 Apache Cassandra SDK 的客户端驱动程序用于客户端程序。 如果你有其他使用的驱动程序，或者如果遇到问题，请将邮件发送[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)到。

### <a name="are-composite-partition-keys-supported"></a>是否支持复合分区键？

是的，可以使用常规语法来创建复合分区键。

### <a name="can-i-use-sstableloader-for-data-loading"></a>是否可以使用 sstableloader 来加载数据？

不支持，不支持 sstableloader。

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>是否可以将本地 Apache Cassandra 群集与 Cassandra API 配对？

目前，Azure Cosmos DB 为云环境提供优化的体验，且不会产生操作开销。 如果需要配对，请将邮件发送[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)到，并提供方案说明。 我们正在致力于提供一个产品/服务，帮助将本地或云 Cassandra 群集与 Azure Cosmos DB 的 Cassandra API 配对。

### <a name="does-the-cassandra-api-provide-full-backups"></a>Cassandra API 是否提供完整备份？

Azure Cosmos DB 提供了在所有 Api 中以四个小时为间隔创建的两个免费完整备份。 因此，您无需设置备份计划。 

如果要修改保留期和频率，请向发送电子[askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)邮件或提出支持案例。 [Azure Cosmos DB 的自动联机备份和还原](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)一文中提供了有关备份功能的信息。

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>当某个区域出现故障时，Cassandra API 帐户如何处理故障转移？

Cassandra API 从 Azure Cosmos DB 的全局分布式平台借用。 若要确保应用程序可以容忍数据中心停机，请在 Azure 门户中至少为帐户启用一个区域。 有关详细信息，请参阅[Azure Cosmos DB 的高可用性](high-availability.md)。

可以视需要为帐户添加任意数目的区域，并通过提供故障转移优先级来控制可将该帐户故障转移到哪个位置。 若要使用数据库，还需要在那里提供一个应用程序。 这样，客户就不会遇到停机情况。

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>默认情况下，Cassandra API 是否为实体的所有属性编制索引？

否。 Cassandra API 支持[辅助索引](cassandra-secondary-index.md)，其行为方式类似于 Apache Cassandra。 默认情况下，API 不会索引每个属性。  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>是否可以在本地将新的 Cassandra API SDK 用于模拟器？

是的，支持此操作。 你可以在将[Azure Cosmos 模拟器用于本地开发和测试](local-emulator.md#cassandra-api)文章中找到有关如何启用此操作的详细信息。


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>如何将数据从 Apache Cassandra 群集迁移到 Azure Cosmos DB？

可在 Azure Cosmos DB 教程中的将[数据迁移到 Cassandra API 帐户](cassandra-import-data.md)中了解迁移选项。


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>在哪里可以 Cassandra API 功能提供反馈？

请通过 [UserVoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db)提供反馈。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>后续步骤

- [弹性扩展 Azure Cosmos DB Cassandra API 帐户](manage-scale-cassandra.md)入门。
