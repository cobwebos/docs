---
title: 利用 Advisor 提高 Azure 应用程序的性能
description: 使用 Azure 顾问中的性能建议，提高关键业务应用程序的速度和响应能力。
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 9625bb3b063234e9cadb20aacfcc5ca8a28b35cc
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405150"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>使用 Azure 顾问提高 Azure 应用程序的性能

Azure 顾问中的性能建议可以帮助提高关键业务应用程序的速度和响应能力。 可通过顾问从顾问仪表板的“性能”选项卡获取性能建议。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>减少流量管理器配置文件的 DNS 生存时间，以更快地故障转移到正常运行的终结点

你可以使用 Azure 流量管理器配置文件上的生存 [时间 (TTL) 设置](../traffic-manager/traffic-manager-performance-considerations.md) 来指定给定端点停止响应查询时切换端点的速度。 如果减少 TTL 值，客户端将路由到正常运行的终结点。

Azure 顾问标识配置了较长 TTL 的流量管理器配置文件。 建议将 TTL 配置为20秒或60秒，具体取决于配置文件是否配置为 [快速故障转移](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)。

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>使用暂时禁用 SQL 数据库顾问 (提高数据库性能) 

Azure 顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 SQL 数据库顾问集成，为你提供改进数据库性能的建议。SQL 数据库顾问通过分析使用历史记录来评估数据库的性能。 然后提供最适合运行数据库的典型工作负荷的建议。

> [!NOTE]
> 你的数据库需要在大约一周内使用，并且在该周内必须有一些一致的活动，然后才能获取建议。 SQL 数据库顾问优化一致的查询模式比优化随机的突发活动更加轻松。

有关详细信息，请参阅 [SQL 数据库顾问](../azure-sql/database/database-advisor-implement-performance-recommendations.md)。

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>将存储客户端库升级到最新版本，以获得更好的可靠性和性能

最新版本的存储客户端库 SDK 包含客户报告的问题的修补程序，并通过我们的 QA 流程主动识别这些问题。 最新版本还提供了可靠性和性能优化，以及可改善使用 Azure 存储的整体体验的新功能。 如果你使用的是旧版本，则顾问会提供升级到最新版本的 SDK 所需的建议和步骤。 建议用于支持的语言： c + + 和 .NET。

## <a name="improve-app-service-performance-and-reliability"></a>提高应用服务性能和可靠性

Azure 顾问集成了有关改善应用服务体验和发现相关平台功能的建议。 应用服务建议的示例包括：
* 使用缓解选项检测内存或 CPU 资源已由应用运行时耗尽的实例。
* 检测 web 应用和数据库等归置资源可以提高性能和降低成本的实例。

有关详细信息，请参阅 [Azure App Service 的最佳实践](../app-service/app-service-best-practices.md)。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用托管磁盘防止磁盘 i/o 限制

Advisor 会标识属于存储帐户的虚拟机，该帐户将达到其可伸缩性目标。 这种情况使得这些 VM 容易遭受 I/O 限制。 Advisor 将建议使用托管磁盘，以防止性能下降。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>可以通过使用高级磁盘来提高虚拟机磁盘的性能和可靠性

Advisor 会标识具有标准磁盘的虚拟机，这些磁盘在存储帐户中具有大量事务。 建议升级到高级磁盘。 

Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 使用高级存储帐户的虚拟机磁盘将数据存储在固态硬盘上， (Ssd) 。 为实现应用程序的最佳性能，建议将需要高 IOPS 的虚拟机磁盘迁移到高级存储。

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>消除 Azure Synapse 分析表的数据歪斜以提高查询性能

运行工作负荷时，数据歪斜可能导致不必要的数据移动或资源瓶颈。 顾问检测到超过15% 的分布数据偏差。 建议你重新分发数据并重新访问表分布键选择。 若要了解有关识别和删除倾斜的详细信息，请参阅[倾斜疑难解答](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>在 Azure Synapse Analytics 表中创建或更新过期的表统计信息，以提高查询性能

顾问标识不具有最新 [表统计信息](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) 的表，并建议创建或更新统计信息。 Azure Synapse Analytics 中的查询优化器使用最新的统计信息来估计查询结果中的基数或行数。 通过这些估计，查询优化器可以创建一个查询计划，以提供最快的性能。

## <a name="improve-mysql-connection-management"></a>改进 MySQL 连接管理

顾问分析可以指示连接到 MySQL 服务器的应用程序可能没有有效地管理连接。 这种情况可能会导致不必要的资源消耗和应用程序的总体延迟。 为了改进连接管理，建议你减少短期连接数并消除不必要的空闲连接。 可以通过配置服务器端连接池程序（例如 ProxySQL）来进行这些改进。


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>增加以优化 Azure Synapse 分析表的缓存利用率以提高查询性能

Azure 顾问将检测 Azure Synapse 分析表是否具有高缓存使用百分比和低命中率百分比。 这种情况表示高速缓存逐出，这可能会影响 Azure Synapse Analytics 实例的性能。 顾问建议增加 Azure Synapse Analytics 实例，以确保为工作负荷分配足够的缓存容量。

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>将 Azure Synapse 分析表转换为复制的表以提高查询性能

顾问标识不是复制表，但会受益于转换的表。 建议转换这些表。 建议基于：
- 复制的表的大小。 
- 列数。 
- 表分布类型。 
- Azure Synapse Analytics 表中的分区数。 

建议为上下文提供其他试探法。 若要详细了解如何确定此建议，请参阅 [Azure Synapse Analytics 建议](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>将存储帐户迁移到 Azure 资源管理器以获取最新的 Azure 功能

将存储帐户部署模型迁移到 Azure 资源管理器以利用：
- 模板部署。
- 其他安全选项。
- 升级到 GPv2 帐户的功能，因此可以使用最新的 Azure 存储功能。 

顾问会标识任何使用经典部署模型的独立存储帐户，并建议迁移到资源管理器部署模型。

> [!NOTE]
> Azure Monitor 中的经典警报已于8月2019停用。 建议升级经典存储帐户以使用资源管理器在新平台中保留警报功能。 有关详细信息，请参阅 [经典警报停](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)用。

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>设计存储帐户以避免达到最大订阅限制

Azure 区域每个订阅最多支持250个存储帐户。 达到此限制后，将无法在该区域/订阅组合中创建存储帐户。 顾问会检查你的订阅，并提供建议，以便为接近达到最大限制的任何订阅/区域设计更少的存储帐户。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>请考虑增加 VPN 网关 SKU 的大小，以满足高 P2S 使用

每个 Azure VPN 网关 SKU 只能支持指定数量的并发 P2S 连接。 如果连接数接近网关限制，则可能会导致其他连接尝试失败。 如果增加网关的大小，将能够支持更多并发 P2S 用户。 顾问提供增加网关大小的建议和说明。

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>考虑增加 VPN 网关 SKU 的大小以处理高 CPU

在高流量负载下，VPN 网关可能会因为 CPU 太高而丢弃数据包。 请考虑升级 VPN 网关 SKU。 增加 VPN 网关的大小可确保不会由于高 CPU 而断开连接。 顾问提供主动解决此问题的建议。 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>加载时增加批大小，以最大限度提高负载吞吐量、数据压缩和查询性能

顾问会检测是否可以通过在加载到数据库时增加批大小来提高负载性能和吞吐量。 可以考虑使用 COPY 语句。 如果无法使用 COPY 语句，请考虑在使用加载实用程序（例如 SQLBulkCopy API 或 BCP）时增加批大小。 一个很好的通用规则是使用介于100000和1000000行之间的批大小。 增加批大小会提高负载吞吐量、数据压缩和查询性能。

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>归置同一区域中的存储帐户，以便在加载时最大程度地减少延迟

Advisor 检测是否从 SQL 池以外的区域加载。 考虑从与 SQL 池位于同一区域中的存储帐户进行加载，以便在加载数据时最大程度地减少延迟。 此更改有助于最大程度地减少延迟并提高负载性能。

## <a name="use-a-supported-kubernetes-version"></a>使用受支持的 Kubernetes 版本

顾问检测到不受支持的 Kubernetes 版本。

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>优化 Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure Database for MariaDB 服务器的性能

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>解决具有 CPU 瓶颈的 Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure Database for MariaDB 服务器的 CPU 压力
较长时间内 CPU 的高利用率会导致工作负荷的查询性能缓慢。 增加 CPU 大小有助于优化数据库查询的运行时并提高整体性能。 顾问可识别 CPU 使用率较高的服务器，这些服务器可能正在运行 CPU 约束的工作负荷，并建议扩展计算。

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>减少对 Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure Database for MariaDB 服务器的内存限制，或移动到内存优化的 SKU
低缓存命中率可能导致查询性能降低并增加 IOPS。 这种情况可能是由于查询计划错误或内存密集型工作负荷导致的。 修复查询计划或增加 Azure Database for PostgreSQL、Azure Database for MySQL 或 Azure Database for MariaDB 服务器的 [内存](../postgresql/concepts-pricing-tiers.md) 将有助于优化数据库工作负荷的执行。 Azure Advisor 识别受此高缓冲池改动影响的服务器。 建议您执行以下操作之一： 
- 修复查询计划
- 移动到具有更多内存的 SKU 
- 增加存储大小以获取更多的 IOPS。

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用 Azure Database for MySQL 或 Azure Database for PostgreSQL 读取副本横向扩展读取密集型工作负荷
顾问使用基于工作负荷的试探法，如在过去7天内服务器上写入到服务器上的读取比率，以识别读取密集型工作负荷。 具有较高读/写比率的 Azure Database for PostgreSQL 或 Azure Database for MySQL 资源可能导致 CPU 或内存争用，并导致查询性能下降。 添加 [副本](../postgresql/howto-read-replicas-portal.md) 将有助于扩展对副本服务器的读取，并防止主服务器上出现 CPU 或内存限制。 Advisor 识别具有读取密集型工作负载的服务器，并建议你添加 [读取副本](../postgresql/concepts-read-replicas.md)   以卸载某些读取工作负荷。


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>将 Azure Database for MySQL、Azure Database for PostgreSQL 或 Azure Database for MariaDB 服务器缩放到更高的 SKU，以防止连接约束
到数据库服务器的每个新连接都占用内存。 如果由于内存 [上限](../postgresql/concepts-limits.md) 而导致到服务器的连接失败，则数据库服务器的性能会下降。 Azure Advisor 标识运行的服务器，其中的连接失败。 建议升级服务器的连接限制，通过执行以下操作之一，为服务器提供更多内存：
- 横向扩展计算。 
- 使用内存优化 Sku，每个核心具有更多计算。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>将缓存缩放为不同大小或 SKU 以提高缓存和应用程序性能

缓存实例在高内存压力、高服务器负载或高网络带宽下运行时性能最佳。 在这些情况下，它们可能会导致无法响应、丢失数据或变得不可用。 在这些情况中，顾问会标识缓存实例。 建议您执行以下操作之一：
- 应用最佳做法以减少内存压力、服务器负载或网络带宽。
- 缩放到具有更大容量的不同大小或 SKU。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>将包含流量的区域添加到 Azure Cosmos DB 帐户

顾问检测到来自当前未配置区域的流量的 Azure Cosmos DB 帐户。 建议添加该区域。 这样做会缩短来自该区域的请求的延迟时间，并在发生区域中断时确保可用性。 [详细了解 Azure Cosmos DB 的全局数据分布。](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>使用自定义的包含或排除的路径配置 Azure Cosmos DB 索引策略

顾问识别使用默认索引策略 Azure Cosmos DB 容器，但可以从自定义索引策略中获益。 此决定基于工作负荷模式。 默认索引策略将为所有属性编制索引。 具有查询筛选器中使用的显式包含或排除路径的自定义索引策略可以减少 ru 和用于索引的存储。 [详细了解如何修改索引策略。](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>将 Azure Cosmos DB 查询页面大小 (MaxItemCount) 设置为-1 

Azure 顾问识别使用查询页面大小100的 Azure Cosmos DB 容器。 建议使用-1 的页大小，以实现更快的扫描。 [了解有关 MaxItemCount 的详细信息。](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>请考虑在 HBase 群集中使用加速写入功能，以提高群集性能
Azure 顾问会在过去7天内分析系统日志，并确定群集是否遇到以下情况：
1. 高 WAL 同步时间延迟 
2. 高写入请求计数（1 小时内至少 3 个，超过 1000 个平均写入请求/秒/节点）

这些情况指示你的群集遇到了高写入延迟。 这可能是因为在群集上执行了大量工作负荷。若要提高群集的性能，你可能需要考虑使用 Azure HDInsight HBase 提供的加速写入功能。 适用于 HDInsight Apache HBase 群集的加速写入功能可将高级 SSD 托管磁盘附加到每个 RegionServer（工作器节点），而不是使用云存储。 因此，请提供应用程序的低写入延迟和更好的复原能力。 若要[了解](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)有关此功能的详细信息，请参阅

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>查看 Azure 数据资源管理器表缓存- (策略) ，以获得更好的性能 (预览版) 
此建议显示 Azure 数据资源管理器表，这些表具有大量查询，可追溯到配置的缓存周期（策略）之外（你将看到按访问缓存外数据的查询百分比排序的前 10 个表）。 提高群集性能的建议操作：将此表上的查询限制为所需的最小时间范围（在定义的策略内）。 或者，如果需要整个时间范围内的数据，请将缓存周期增加为建议值。

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>通过优化 MySQL 临时表的大小来提高性能
顾问分析表明，由于临时表参数设置较低，MySQL 服务器可能会导致不必要的 i/o 开销。 这可能会导致不必要的基于磁盘的事务并降低性能。 建议增大“tmp_table_size”和“max_heap_table_size”参数值，以减少基于磁盘的事务数。 [了解详细信息](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>在服务器组中分布数据，以在节点间分布工作负载
顾问会确定尚未分发数据但在协调器上保留的服务器组。 根据此，顾问建议对于完全超大规模 (Citus) 权益在服务器组的辅助角色节点上分布数据。 这将通过使用服务器组中每个节点的资源来提高查询性能。 [了解详细信息](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>通过更近于 Windows 虚拟桌面部署位置的 Vm 部署来改善用户体验和连接
我们已使用 Windows 虚拟桌面 (WVD) 确定 VM 所在的区域与用户连接所在的区域不同或相距较远。 这可能会导致连接响应时间延长，并会影响 WVD 上的整体用户体验。 为主机池创建 VM 时，应尝试使用靠近用户的区域。 距离更近可确保 WVD 服务的持续满意度，并提供更好的整体体验质量。 [在此处了解有关连接延迟的详细信息](https://docs.microsoft.com/azure/virtual-desktop/connection-latency)。

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>升级到沉浸式阅读器 SDK 的最新版本
我们已使用沉浸式阅读器 SDK 的已过时版本识别了此订阅下的资源。 使用沉浸式阅读器 SDK 的最新版本，你可以获取更新的安全性、性能及扩展的一组功能，可用于自定义和增强集成体验。
了解有关 [沉浸式读者 SDK](https://aka.ms/ImmersiveReaderAzureAdvisorSDKLearnMore)的详细信息。


## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何访问顾问中的性能建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板上，选择 " **性能** " 选项卡。

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
