---
title: 使用 Azure 顾问提高 Azure 应用程序的性能
description: 使用顾问优化 Azure 部署的性能。
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: ff9b8fb9494c887397947f009b22cdc89d8f70b5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787934"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>使用 Azure 顾问提高 Azure 应用程序的性能

Azure 顾问性能建议有助于提高关键业务应用程序的速度和响应能力。 可通过顾问从顾问仪表板的“性能”  选项卡获取性能建议。

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>减少流量管理器配置文件上的 DNS 生存时间，可更快地故障转移到正常运行的终结点

使用流量管理器配置文件上的[生存时间 (TTL) 设置](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations)可以指定在给定终结点停止响应查询时切换终结点的速度。 减少 TTL 值意味着客户端将更快地路由到正常运行的终结点。

Azure 顾问将识别配置了较长 TTL 的流量管理器配置文件，并建议将 TTL 配置为 20 秒或 60 秒，具体取决于配置文件是否配置为使用[快速故障转移](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)。

## <a name="improve-database-performance-with-sql-db-advisor"></a>通过 SQL DB 顾问提高数据库性能

顾问针对所有 Azure 资源提供一个一致且统一的建议视图。 它与 SQL 数据库顾问集成，提供建议以改进 SQL Azure 数据库性能。SQL 数据库顾问通过分析使用情况历史记录来评估 SQL Azure 数据库的性能。 提供的建议最适合运行数据库典型工作负荷。

> [!NOTE]
> 若要获取建议，数据库必须具有一周左右的使用量，且该周内必须有一些一致的活动。 SQL 数据库顾问优化一致的查询模式比优化随机的突发活动更加轻松。

有关 SQL 数据库顾问的详细信息，请参阅[SQL 数据库顾问](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)。

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>将存储客户端库升级到最新版本，以提高可靠性和性能

最新版存储客户端库/SDK 包含问题修复，这些问题有客户报告的，也有我们通过 QA 过程主动标识的。 最新版除了新功能，还有可靠性和性能优化，可以改进你在使用 Azure 存储时的总体体验。 如果你使用的是旧版本，则顾问会为你提供升级到最新版本的 SDK 的建议和步骤。 建议用于支持的语言-c + + 和 .Net。

## <a name="improve-app-service-performance-and-reliability"></a>提高应用服务性能和可靠性

Azure 顾问集成最佳做法建议，可改善应用服务体验，并发现相关平台的功能。 以下为应用服务建议示例：
* 检测其中应用运行时已耗尽内存或 CPU 资源的实例，并提出缓解措施。
* 检测在其中并置资源（如 Web 应用和数据库）的实例，以改善性能并降低成本。

有关应用服务建议的详细信息，请参阅 [Best Practices for Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)（Azure 应用服务的最佳实践）。

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>使用托管磁盘防止磁盘 I/O 限制

顾问将识别属于即将达到其可伸缩性目标的存储帐户的虚拟机。 这种情况使得这些 VM 容易遭受 I/O 限制。 顾问会建议它们使用托管磁盘以防止性能降低。

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>可以通过使用高级磁盘来提高虚拟机磁盘的性能和可靠性

顾问将识别其中的标准磁盘在存储帐户上具有大量事务的虚拟机，并建议升级到高级磁盘。 

Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 在固态硬盘 (SSD) 上使用高级存储帐户存储数据的虚拟机磁盘。 为使应用程序实现最佳性能，建议将任何需要高 IOPS 的虚拟机磁盘迁移到高级存储。

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>删除 SQL 数据仓库表的数据倾斜以提高查询性能

运行工作负荷时，数据倾斜可能会造成不必要的数据移动或资源瓶颈。 顾问将检测大于 15% 的分布数据倾斜，并建议重新分布数据和重新访问表分布键选择。 若要了解有关识别和删除倾斜的详细信息，请参阅[倾斜疑难解答](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)。

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>创建或更新 SQL 数据仓库表的过期表统计信息以提高查询性能

顾问标识不包含最新[表统计信息](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)的表并建议创建或更新表统计信息。 SQL 数据仓库查询优化器使用最新统计信息来估计基数或查询结果中的行数，这样一来，查询优化器能够创建高质量的查询计划，以提高性能。

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>通过纵向扩展来优化 SQL 数据仓库上的缓存利用率，从而提升查询性能

Azure 顾问会检测 SQL 数据仓库是否具有高缓存使用百分比和低命中百分比。 这种情况表示存在高缓存逐出，可能会影响 SQL 数据仓库的性能。 顾问建议你对 SQL 数据仓库进行纵向扩展以确保为工作负荷分配足够的缓存容量。

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>将 SQL 数据仓库表转换为复制表以提高查询性能

顾问会识别非复制但是会受益于转换的表，并建议转换这些表。 建议是基于 SQL 数据仓库表的复制表大小、列数、表分布类型以及分区数提出的。 还可能会根据上下文在建议中提供额外的启发。 若要详细了解此建议是如何确定的，请参阅 [SQL 数据仓库建议](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)。 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>将存储帐户迁移到 Azure 资源管理器来获取所有最新 Azure 功能。

将存储帐户部署模型迁移到 Azure 资源管理器（资源管理器）可利用模板部署和更多的安全选项，并可升级到 GPv2 帐户来利用 Azure 存储的最新功能。 顾问将识别使用经典部署模型的任何独立存储帐户，并建议迁移到资源管理器部署模型。

> [!NOTE]
> Azure Monitor 中的经典警报已于 2019 年 8月停用。 建议升级经典存储帐户以使用资源管理器在新平台中保留警报功能。 有关详细信息，请参阅[经典警报停用](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)。

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>设计存储帐户以避免达到最大订阅限制

对于每个订阅，Azure 区域最多支持250个存储帐户。 达到此限制后，将无法再创建该区域/订阅组合中的任何其他存储帐户。 Advisor 将检查你的订阅和表面建议，以便为接近达到最大限制的任何内容设计更少的存储帐户。

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-adress-high-p2s-use"></a>考虑将 VNet 网关 SKU 的大小增加到 P2S，使用

每个网关 SKU 只能支持指定的并发 P2S 连接计数。 如果连接计数接近网关限制，则其他连接尝试可能会失败。 增加网关的大小，可以支持更多的并发 P2S 用户。顾问为此提供建议和步骤。

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-address-high-cpu"></a>考虑增加 VNet 网关 SKU 的大小以处理高 CPU

在高流量负载下，VPN 网关可能会因为 CPU 太高而丢弃数据包。 你应考虑升级 VPN 网关 SKU，因为你的 VPN 一直在上运行。增加 VPN 网关的大小可确保不会因 CPU 太高而丢弃连接。 Advisor provdes 建议，以主动解决此问题。 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>在加载时提高批大小，以最大限度地提高负载吞吐量、数据压缩和查询性能

顾问可以通过在加载到数据库时增加批大小来检测是否可以提高负载性能和吞吐量。 可以考虑使用 COPY 语句。 如果无法使用 COPY 语句，请考虑在使用加载实用程序（例如 SQLBulkCopy API 或 BCP）时增加批大小，好的经验法则是10到1M 行之间的批大小。 这会增加负载吞吐量、数据压缩和查询性能。

## <a name="co-locate-the-storage-account-within-the-same-region-to-minimize-latency-when-loading"></a>归置同一区域中的存储帐户，以便在加载时最大程度地减少延迟

Advisor 可以检测到你正在从不同于 SQL 池的区域加载。 你应考虑从与 SQL 池位于同一区域中的存储帐户加载，以最大程度地减少加载数据时的延迟。 这将有助于最大程度地减少延迟并提高负载性能。

## <a name="unsupported-kubernetes-version-is-detected"></a>检测到不支持的 Kubernetes 版本

顾问可以检测是否检测到不受支持的 Kubernetes 版本。 建议确保使用受支持的版本运行 Kubernetes 群集。

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>优化 Azure MySQL、Azure PostgreSQL 和 Azure MariaDB 服务器的性能 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>解决具有 CPU 瓶颈的 Azure MySQL、Azure PostgreSQL 和 Azure MariaDB 服务器的 CPU 压力
在较长时间内 CPU 利用率非常高会导致工作负荷的查询性能缓慢。 增加 CPU 大小有助于优化数据库查询的运行时并提高整体性能。 Azure Advisor 将识别 CPU 使用率较高的服务器，这些服务器可能正在运行 CPU 约束的工作负荷，并建议缩放计算。

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>减少对 Azure MySQL、Azure PostgreSQL 和 Azure MariaDB 服务器的内存限制，或转到内存优化 SKU
低缓存命中率可能导致查询性能降低并增加 IOPS。 这可能是由不正确的查询计划或运行大量内存的工作负荷导致的。 修复查询计划或增加 Azure Database for PostgreSQL 数据库服务器、Azure MySQL 数据库服务器或 Azure MariaDB 服务器的 [内存](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers)，有助于优化数据库工作负荷的执行。 Azure 顾问会识别受此较高缓冲池改动影响的服务器，并建议修复查询计划，将其移到具有更多内存的更高 SKU，或增加存储大小以获得更多的 IOPS。

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>使用 Azure MySQL 或 Azure PostgreSQL 读取副本对读取密集型工作负荷进行横向扩展
Azure 顾问利用基于工作负荷的试探法，例如，在过去7天内服务器上写入操作的比率，用于识别读取密集型工作负荷。 如果你的 Azure database for PostgreSQL 资源或 Azure database for MySQL 资源的读取/写入比率非常高，则可能导致 CPU 和/或内存争用导致查询性能下降。 添加 [副本](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal)将有助于扩大对副本服务器的读取，从而防止主服务器上的 CPU 和/或内存限制。 Advisor 将识别具有大量读取密集型工作负载的服务器，并建议添加 [读取副本](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) 以卸载某些读取工作负荷。


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>将 Azure MySQL、Azure PostgreSQL 或 Azure MariaDB 服务器缩放到更高的 SKU，以防止连接约束
与数据库服务器之间的每个新连接都占用一些内存。 如果由于内存 [上限](https://docs.microsoft.com/azure/postgresql/concepts-limits)而导致到服务器的连接失败，则数据库服务器的性能会下降。 Azure Advisor 将识别运行的服务器，其中包含许多连接故障，并建议升级服务器的连接限制，通过扩展计算或使用内存优化 Sku （每个内核具有更多计算）来为服务器提供更多内存。

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>将缓存缩放为不同大小或 SKU 以提高缓存和应用程序性能

当未在高内存压力、高服务器负载或高网络带宽下运行时，缓存实例的性能最佳，这可能会导致这些实例停止响应、丢失数据或变得不可用。 在这些情况下，顾问将标识缓存实例，并建议应用最佳做法以减少内存压力、服务器负载或网络带宽，或扩展到不同大小或具有更大容量的 SKU。

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>将包含流量的区域添加到 Azure Cosmos DB 帐户

Advisor 将检测到 Azure Cosmos DB 帐户的流量来自当前未配置的区域，并建议添加该区域。 这会缩短来自该区域的请求的延迟时间，并在发生区域中断时确保可用性。 [详细了解 Azure Cosmos DB 的全局数据分布](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>为客户包括或排除的路径配置 Azure Cosmos DB 索引策略

Azure Advisor 将识别使用默认索引策略 Cosmos DB 容器，但可以根据工作负荷模式从自定义索引策略中获益。 默认索引策略将为所有属性编制索引，但使用自定义索引策略时，查询筛选器中使用的显式包含或排除路径可以减少 ru 和用于索引的存储。 [了解有关修改索引策略的详细信息](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>将 Azure Cosmos DB 查询页大小 (MaxItemCount) 配置为 -1 

Azure 顾问将识别使用查询页面大小100的 Azure Cosmos DB 容器，并建议使用页面大小-1 以提高扫描速度。 [了解有关最大项计数的详细信息](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>如何访问顾问中的性能建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，单击“性能”**** 选项卡。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：

* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问操作优秀建议](advisor-operational-excellence-recommendations.md)
