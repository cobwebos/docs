---
title: 使用顾问提高 Azure 应用的性能
description: 使用 Azure 顾问中的性能建议可提高业务关键型应用程序的速度和响应能力。
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 44252171a714acec0a9c0e83c9272b2f845560b3
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077807"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>使用 Azure 顾问提高 Azure 应用程序的性能

Azure 顾问中的性能建议有助于提高业务关键型应用程序的速度和响应能力。 可通过顾问从顾问仪表板的“性能”选项卡获取性能建议。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>缩短流量管理器配置文件上的 DNS 生存时间，以更快地故障转移到正常运行的终结点

可以使用 Azure 流量管理器配置文件中的[生存时间 (TTL) 设置](../traffic-manager/traffic-manager-performance-considerations.md)指定在给定终结点停止响应查询时切换终结点的速度。 如果降低 TTL 值，则客户端将更快地路由到正常运行的终结点。

Azure 顾问可识别配置了较长 TTL 的流量管理器配置文件。 它会建议将 TTL 配置为 20 秒或 60 秒，具体取决于配置文件是否配置为使用[快速故障转移](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)。

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>使用 SQL 数据库顾问（暂时禁用）提高数据库性能

Azure 顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 SQL 数据库顾问集成，为你提供用于提高数据库性能的建议。 SQL 数据库顾问通过分析使用情况历史记录来评估数据库的性能， 然后提供最适合运行数据库典型工作负荷的建议。

> [!NOTE]
> 需要先使用数据库大约一周时间，并在这一周内完成一些一致的活动，然后才能获取建议。 SQL 数据库顾问优化一致的查询模式比优化随机的突发活动更加轻松。

有关详细信息，请参阅 [SQL 数据库顾问](../azure-sql/database/database-advisor-implement-performance-recommendations.md)。

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>将存储客户端库升级到最新版本，以提高可靠性和性能

最新版存储客户端库 SDK 包含修补程序，可解决由客户报告的问题以及通过我们的 QA 过程主动识别的问题。 除了新功能，最新版本还提供可靠性和性能优化，可以改进你在使用 Azure 存储时的整体体验。 如果使用的是旧版本，则顾问会提供升级到最新版本的 SDK 所需的建议和步骤。 这些建议适用于受支持的语言：C++ 和 .NET。

## <a name="improve-app-service-performance-and-reliability"></a>提高应用服务性能和可靠性

Azure 顾问集成用于改善应用服务体验以及发现相关平台功能的建议。 以下为应用服务建议示例：
* 检测其中的应用运行时已耗尽内存或 CPU 资源的实例，并提出缓解措施。
* 检测在其中归置资源（如 Web 应用和数据库）可提高性能并降低成本的实例。

有关详细信息，请参阅 [Azure 应用服务最佳做法](../app-service/app-service-best-practices.md)。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用托管磁盘防止磁盘 I/O 限制

顾问可识别属于即将达到其可伸缩性目标的存储帐户的虚拟机。 这种情况使得这些 VM 容易遭受 I/O 限制。 顾问会建议它们使用托管磁盘来防止性能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>可以通过使用高级磁盘来提高虚拟机磁盘的性能和可靠性

顾问可识别标准磁盘在存储帐户上有大量事务的虚拟机。 它会建议升级到高级磁盘。 

Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 使用高级存储帐户的虚拟机磁盘在固态硬盘 (SSD) 上存储数据。 若要让应用程序实现最佳性能，建议将任何需要高 IOPS 的虚拟机磁盘迁移到高级存储。

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>删除 Azure Synapse Analytics 表上的数据倾斜以提高查询性能

运行工作负荷时，数据倾斜可能会造成不必要的数据移动或资源瓶颈。 顾问可检测大于 15% 的分布数据倾斜。 建议重新分布数据并重新考虑表分布键选项。 若要了解有关识别和删除倾斜的详细信息，请参阅[倾斜疑难解答](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>在 Azure Synapse Analytics 表中创建或更新过期表统计信息，以提高查询性能

顾问可识别不包含最新[表统计信息](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md)的表，并建议创建或更新统计信息。 Azure Synapse Analytics 中的查询优化器使用最新统计信息来估算查询结果的基数或行数。 借助这些估算值，查询优化器可创建查询计划以提供最快的性能。

## <a name="improve-mysql-connection-management"></a>改进 MySQL 连接管理

顾问分析可以指示连接到 MySQL 服务器的应用程序可能无法高效管理连接。 这种情况可能导致不必要的资源消耗和应用程序的总体延迟过高。 为了改进连接管理，建议你减少短期连接数并消除不必要的空闲连接。 可以通过配置服务器端连接池（如 ProxySQL）来进行这些改进。


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>通过纵向扩展来优化 Azure Synapse Analytics 表上的缓存利用率，从而提高查询性能

Azure 顾问可检测 Azure Synapse Analytics 表是否具有较高的缓存利用率和较低的命中率。 这种情况表示存在高缓存逐出，这可能影响 Azure Synapse Analytics 实例的性能。 顾问建议纵向扩展 Azure Synapse Analytics 实例，以确保为工作负荷分配足够的缓存容量。

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>将 Azure Synapse Analytics 表转换为复制表，以提高查询性能

顾问可识别不是复制表，但将从转换中受益的表。 它会建议转换这些表。 建议基于：
- 复制表的大小。 
- 列数。 
- 表分布类型。 
- Azure Synapse Analytics 表中的分区数。 

还可能会根据上下文在建议中提供额外的启发。 若要详细了解此建议是如何确定的，请参阅 [Azure Synapse Analytics 建议](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>将存储帐户迁移到 Azure 资源管理器来获取最新 Azure 功能

将存储帐户部署模型迁移到 Azure 资源管理器，以利用：
- 模板部署。
- 其他安全选项。
- 升级到 GPv2 帐户的能力，因此可以使用最新的 Azure 存储功能。 

顾问可识别使用经典部署模型的任何独立存储帐户，并建议迁移到资源管理器部署模型。

> [!NOTE]
> Azure Monitor 中的经典警报已于 2019 年 8 月停用。 建议升级经典存储帐户以使用资源管理器在新平台中保留警报功能。 有关详细信息，请参阅 [经典警报停](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)用。

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>设计存储帐户，防止达到最大订阅数限制

一个 Azure 区域最多支持每个订阅拥有 250 个存储帐户。 达到此限制后，将无法在该区域/订阅组合中创建存储帐户。 顾问会检查你的订阅并提供建议，以便为任何即将达到最大限制的订阅/区域设计更少的存储帐户。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>考虑增加 VPN 网关 SKU 的大小以解决高 P2S 利用率问题

每个 Azure VPN 网关 SKU 只能支持指定数量的并发 P2S 连接。 如果连接计数接近网关限制，则其他连接尝试可能会失败。 如果增加网关的大小，你将能够支持更多并发 P2S 用户。 顾问会提供有关增加网关大小的建议和说明。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>考虑增加 VPN 网关 SKU 的大小以解决高 CPU 利用率问题

在高流量负载下，VPN 网关可能会由于高 CPU 利用率而丢弃数据包。 考虑升级 VPN 网关 SKU。 增加 VPN 网关的大小将确保连接不会因高 CPU 利用率而断开。 顾问会提供用于主动解决此问题的建议。 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>加载时增加批大小，以最大限度提高负载吞吐量、数据压缩和查询性能

顾问可检测在向数据库中进行加载时，是否可以通过增加批大小来提高负载性能和吞吐量。 可以考虑使用 COPY 语句。 如果无法使用 COPY 语句，则在使用 SQLBulkCopy API 或 BCP 等加载实用工具时，考虑增加批大小。 一条很好的通用规则是，使用在 10 万到 100 万行之间的批大小。 增加批大小将提高负载吞吐量、数据压缩和查询性能。

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>将存储帐户归置到同一区域，以在加载时最大程度地减少延迟

顾问可检测是否从不同于 SQL 池的区域进行加载。 考虑从与 SQL 池位于同一区域内的存储帐户进行加载，以最大程度地减少加载数据时的延迟。 此更改将有助于最大程度地减少延迟并提高负载性能。

## <a name="use-a-supported-kubernetes-version"></a>使用受支持的 Kubernetes 版本

顾问可检测不受支持的 Kubernetes 版本。

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>优化 Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure Database for MariaDB 服务器的性能

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>解决具有 CPU 瓶颈的 Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure Database for MariaDB 服务器的 CPU 压力问题
CPU 利用率长时间处于较高状态可能导致工作负荷的查询性能降低。 增加 CPU 大小将有助于优化数据库查询的运行时并提高整体性能。 顾问可识别 CPU 利用率较高并可能运行 CPU 受约束工作负荷的服务器，并建议缩放计算。

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>减少 Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure Database for MariaDB 服务器上的内存约束或迁移到内存优化 SKU
低缓存命中率可能导致查询性能降低和 IOPS 增加。 这种情况可能是由错误的查询计划或内存密集型工作负荷导致的。 修复查询计划或 [增加](../postgresql/concepts-pricing-tiers.md) Azure Database for PostgreSQL、Azure Database for MySQL 或 Azure Database for MariaDB 服务器的内存将有助于优化数据库工作负荷的执行。 Azure 顾问可识别受此高缓冲池变动影响的服务器。 建议执行以下操作之一： 
- 修复查询计划
- 迁移到具有更多内存的 SKU 
- 增加存储大小以获得更多 IOPS。

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用 Azure Database for MySQL 或 Azure Database for PostgreSQL 只读副本横向扩展读取密集型工作负荷的读取
顾问使用基于工作负荷的启发（如过去 7 天内服务器上的读写比）来识别读取密集型工作负荷。 具有较高读/写比的 Azure Database for PostgreSQL 或 Azure Database for MySQL 资源可能导致 CPU 或内存争用并导致查询性能降低。 添加 [副本](../postgresql/howto-read-replicas-portal.md)有助于将读取横向扩展到副本服务器，并防止主服务器上的 CPU 或内存限制。 顾问可识别具有读取密集型工作负荷的服务器，并建议添加 [只读副本](../postgresql/concepts-read-replicas.md) 以卸载某些读取工作负荷。


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>将 Azure Database for MySQL、Azure Database for PostgreSQL 或 Azure Database for MariaDB 服务器缩放为更高的 SKU，以防止连接约束
到数据库服务器的每个新连接都会占用内存。 如果由于内存 [上限](../postgresql/concepts-limits.md)而导致与服务器的连接失败，则数据库服务器的性能会下降。 Azure 顾问可识别运行时连接失败很多次的服务器。 建议通过执行以下操作之一来升级服务器的连接限制，以向服务器提供更多内存：
- 纵向扩展计算资源。 
- 使用内存优化 SKU，其中每个核心具有更多的计算资源。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>将缓存缩放到其他大小或 SKU，以提高缓存和应用程序性能

如果运行时不存在高内存压力、高服务器负载或高网络带宽，则缓存实例性能最佳。 这些情况可能导致它们变得无响应、出现数据丢失或变得不可用。 顾问可识别处于这些情况下的缓存实例。 建议执行以下操作之一：
- 应用最佳做法，以降低内存压力、服务器负载或网络带宽。
- 缩放到具有更大容量的其他大小或 SKU。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>将包含流量的区域添加到 Azure Cosmos DB 帐户

顾问可检测包含来自当前未配置区域的流量的 Azure Cosmos DB 帐户。 建议添加该区域。 这样做可以减少来自该区域的请求的延迟，并在发生区域中断的情况下确保可用性。 [了解有关如何使用 Azure Cosmos DB 全局分布数据的详细信息。](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>使用自定义的已包括或已排除路径配置 Azure Cosmos DB 索引编制策略

顾问可识别使用默认索引编制策略，但可从自定义索引编制策略受益的 Azure Cosmos DB 容器。 此判断基于工作负荷模式。 默认索引编制策略为所有属性编制索引。 在查询筛选器中使用显式已包括或已排除路径的自定义索引策略可减少进行索引编制时使用的 RU 和存储。 [了解有关如何修改索引编制策略的详细信息。](/azure/cosmos-db/index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>将 Azure Cosmos DB 查询页大小 (MaxItemCount) 设置为 -1 

Azure 顾问可识别使用的查询页大小为 100 的 Azure Cosmos DB 容器。 建议使用的页大小为 -1，以提高扫描速度。 [详细了解 MaxItemCount。](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>考虑在 HBase 群集中使用加速写入功能来提高群集性能
Azure 顾问会分析过去 7 天内的系统日志，并识别群集是否遇到以下情况：
1. 高 WAL 同步时间延迟 
2. 高写入请求计数（1 小时内至少 3 个，超过 1000 个平均写入请求/秒/节点）

这些情况指示你的群集遇到了高写入延迟。 这可能是由于群集上执行繁重工作负荷所致。若要提高群集性能，你可能需要考虑使用 Azure HDInsight HBase 提供的加速写入功能。 适用于 HDInsight Apache HBase 群集的加速写入功能可将高级 SSD 托管磁盘附加到每个 RegionServer（工作器节点），而不是使用云存储。 因此，请提供应用程序的低写入延迟和更好的复原能力。 若要详细了解此功能，请[了解详细信息](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>查看 Azure 数据资源管理器表缓存周期（策略）以获得更好的性能（预览版）
此建议显示 Azure 数据资源管理器表，这些表具有大量查询，可追溯到配置的缓存周期（策略）之外（你将看到按访问缓存外数据的查询百分比排序的前 10 个表）。 提高群集性能的建议操作：将此表上的查询限制为所需的最小时间范围（在定义的策略内）。 或者，如果需要整个时间范围内的数据，请将缓存周期增加为建议值。

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>通过优化 MySQL 临时表的大小来提高性能
顾问分析指示，由于临时表参数设置较低，MySQL 服务器可能会产生不必要的 I/O 开销。 这可能会导致不必要的基于磁盘的事务并降低性能。 建议增大“tmp_table_size”和“max_heap_table_size”参数值，以减少基于磁盘的事务数。 [了解详细信息](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>在服务器组中分布数据，以在节点间分布工作负载
顾问可识别数据未进行分布，而是仍保留在协调器中的服务器组。 基于这一点，顾问建议在服务器组中的工作器节点上分布数据，以获得完整的超大规模 (Citus) 权益。 这将通过利用服务器组中每个节点的资源来提高查询性能。 [了解详细信息](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>通过更近于 Windows 虚拟桌面部署位置的 Vm 部署来改善用户体验和连接
我们已使用 Windows 虚拟桌面 (WVD) 确定 VM 所在的区域与用户连接所在的区域不同或相距较远。 这可能会导致连接响应时间延长，并会影响 WVD 上的整体用户体验。 为主机池创建 VM 时，应尝试使用靠近用户的区域。 距离更近可确保 WVD 服务的持续满意度，并提供更好的整体体验质量。 [在此处了解有关连接延迟的详细信息](../virtual-desktop/connection-latency.md)。

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>升级到沉浸式阅读器 SDK 的最新版本
我们已使用沉浸式阅读器 SDK 的已过时版本识别了此订阅下的资源。 使用沉浸式阅读器 SDK 的最新版本，你可以获取更新的安全性、性能及扩展的一组功能，可用于自定义和增强集成体验。
了解有关 [沉浸式读者 SDK](../cognitive-services/immersive-reader/index.yml)的详细信息。


## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何访问顾问中的性能建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，选择“性能”选项卡。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：

* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问分数](azure-advisor-score.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问可靠性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](/rest/api/advisor/)