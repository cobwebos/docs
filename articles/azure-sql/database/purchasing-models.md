---
title: 购买模型
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 了解适用于 Azure SQL 数据库和 Azure SQL 托管实例的购买模型。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 05/28/2020
ms.openlocfilehash: a11894eb94b73d8d31ca7135be2ba9c05eca5e04
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86075869"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>选择 vCore 和 DTU 购买模型-Azure SQL 数据库和 SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

使用 azure SQL 数据库和 Azure SQL 托管实例，你可以轻松购买完全托管的平台即服务（PaaS）数据库引擎，以满足你的性能和成本需求。 可以根据选择用于 Azure SQL 数据库的部署模型选择适合需求的购买模型：

- [基于虚拟核心 (vCore) 的购买模型](service-tiers-vcore.md)（推荐）。 此购买模型允许在预配的计算层级和无服务器计算层级之间进行选择。 使用预配的计算层级时，可以选择始终为工作负荷预配的确切计算资源。 使用无服务器计算层级时，可以指定对计算资源进行的自动缩放（在可配置的计算范围内）。 使用此计算层级时，还可以根据工作负荷活动自动暂停和恢复数据库。 预配计算层级中单位时间的 vCore 单位价格低于无服务器计算层级中的相应价格。
- [基于数据库事务单位 (DTU) 的购买模型](service-tiers-dtu.md)。 此购买模型针对常见工作负荷提供均衡的捆绑计算和存储包。

有两种购买模型：

- [基于 vCore 的购买模型](service-tiers-vcore.md)适用于[azure Sql 数据库](sql-database-paas-overview.md)和[azure sql 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)。 [超大规模服务层级](service-tier-hyperscale.md)适用于使用[基于 vCore 的购买模型](service-tiers-vcore.md)的单一数据库。
- [基于 DTU 的购买模式](service-tiers-dtu.md)适用于[Azure SQL 数据库](single-database-manage.md)。

以下表格和图表对基于 vCore 和基于 DTU 的购买模型做了对比。

|**购买模型**|**说明**|**最适用于**|
|---|---|---|
|基于 DTU|此模型基于计算、存储和 I/O 资源的捆绑度量。 单一数据库的计算大小以 DTU 表示，弹性池则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU 和 eDTU？](purchasing-models.md#dtu-based-purchasing-model)。|需要简单的预配置资源选项的客户|
|基于 vCore|此模型允许单独选择计算和存储资源。 使用基于 vCore 的购买模型，还可以使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)SQL Server 来节约成本。|值灵活性、控制和透明度的客户|
||||  

![定价模型比较](./media/purchasing-models/pricing-model.png)

想要优化并保存云支出？

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>计算成本

### <a name="provisioned-compute-costs"></a>预配计算成本

在预配计算层级中，计算成本反映针对应用程序预配的总计算容量。

在业务关键服务层，我们将自动分配至少三个副本。 为了反映这种额外的计算资源分配，在业务关键服务层中，基于 vCore 的购买模型中的价格大约比常规用途服务层高2.7 倍。 同理，“业务关键”服务层级中更高的每 GB 存储价格反映了 SSD 存储的更高 IO 限制和更低延迟。

“业务关键”服务层级和“常规用途”服务层级的备份存储成本是相同的，因为两者都使用标准存储进行备份。

### <a name="serverless-compute-costs"></a>无服务器计算成本

有关如何定义计算容量和计算无服务器计算层开销的说明，请参阅[SQL 数据库无服务器层](serverless-tier-overview.md)。

## <a name="storage-costs"></a>存储费用

不同存储类型的计费方式各不相同。 对于数据存储，你要根据所选的最大数据库或池大小支付预配存储的费用。 除非减小或增大该最大值，否则费用不会变化。 备份存储与实例的自动备份相关联，并动态分配。 延长备份保留期会使实例使用的备份存储增大。

默认情况下，将数据库的7天自动备份复制到读取访问异地冗余存储（GRS）标准 Blob 存储帐户。 此存储由每周完整备份、每日差异备份和事务日志备份使用，每五分钟复制一次。 事务日志的大小取决于数据库的变化率。 提供与 100% 数据库大小相等的最小存储量，不收取额外费用。 超出此部分的其他备份存储空间按 GB/月收费。

有关存储价格的详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/sql-database/single/)页。

## <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

虚拟核心 (vCore) 表示通过一个选项提供的逻辑 CPU，你可以在硬件的层代和硬件的物理特性（例如，核心数、内存、存储大小）之间进行选择。 基于 vCore 的购买模型提供使用单项资源的灵活性、控制度和透明度，并提供简单明了的方法将本地工作负荷要求转换到云。 此模型允许根据工作负荷需求选择计算、内存和存储资源。

在基于 vCore 的购买模型中，可以在 SQL 数据库和 SQL 托管实例的[常规用途](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability)和[业务关键](high-availability-sla.md#premium-and-business-critical-service-tier-availability)服务层之间进行选择。  对于单一数据库，还可以选择[超大规模服务层级](service-tier-hyperscale.md)。

使用基于 vCore 的购买模型，可以单独选择计算和存储资源、匹配本地性能，以及优化价格。 在基于 vCore 的购买模型中，费用包括：

- 计算资源（服务层级 + vCore 数目和内存量 + 硬件层代）。
- 数据和日志存储的类型与数量。
- 备份存储 (RA-GRS)。

> [!IMPORTANT]
> 计算资源、I/O 以及数据和日志存储按数据库或弹性池收费。 备份存储按每个数据库收费。 有关 SQL 托管实例收费的详细信息，请参阅[sql 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)。
> 区域限制：  有关受支持区域的当前列表，请参阅[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 若要在当前不受支持的区域中创建托管实例，请[通过 Azure 门户发送支持请求](quota-increase-request.md)。

如果您的数据库消耗300多个 Dtu，转换为基于 vCore 的购买模型可能会降低您的成本。 可以使用所选的 API 或 Azure 门户进行转换，无需停机。 但是，转换不是必需的并且不会自动完成。 如果基于 DTU 的购买模型可以满足性能和业务要求，应继续使用它。

若要从基于 DTU 的购买模型转换为基于 vCore 的购买模型，请参阅[从 Dtu 迁移到 vCore](migrate-dtu-to-vcore.md)。

## <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

数据库事务单位 (DTU) 表示 CPU、内存、读取和写入的混合度量。 基于 DTU 的购买模型提供一组预配置的计算资源套件和随附的存储，以促成不同级别的应用程序性能。 如果你偏爱预配置套件简易性和每月定价付款，基于 DTU 的模型可能更符合你的需求。

在基于 DTU 的购买模型中，可以在 Azure SQL 数据库的 "基本"、"标准" 和 "高级" 服务层之间进行选择。 基于 DTU 的购买模型不适用于 Azure SQL 托管实例。

### <a name="database-transaction-units-dtus"></a>数据库事务单位 (DTU)

对于[服务层级](single-database-scale.md)内特定计算大小的单一数据库，Azure 保证该数据库（独立于 Azure 云中的任何其他数据库）可获得一定级别的资源。 这可以保证提供可预测的性能级别。 为数据库分配的资源量是以若干 DTU 计算的，是计算资源、存储资源和 I/O 资源的捆绑度量值。

这些资源之间的比率最初由[联机事务处理 (OLTP) 基准工作负荷](service-tiers-dtu.md)决定，后者旨在作为典型的真实 OLTP 工作负荷。 工作负荷超过任何以上资源量时，吞吐量将受到限制，从而导致性能下降和超时。

工作负荷使用的资源不会影响可用于 Azure 云中其他数据库的资源。 同样，其他工作负荷使用的资源不会影响可用于数据库的资源。

![边界框](./media/purchasing-models/bounding-box.png)

Dtu 对于了解为不同计算大小和服务层上的数据库分配的相对资源最有用。 例如：

- 提高数据库的计算大小可使 DTU 加倍，这等同于使该数据库可用的资源集增加一倍。
- 与具有5个 Dtu 的基本服务层数据库相比，具有 1750 Dtu 的高级服务层 P11 数据库提供的 DTU 计算能力更多350倍。  

若要更深入地了解工作负荷的资源 (DTU) 消耗，请使用[查询性能见解](query-performance-insight-use.md)：

- 按 CPU/持续时间/执行计数确定热门查询，可以对这些查询进行调整来提高性能。 例如，I/O 密集型查询可能会受益于[内存中优化技术](../in-memory-oltp-overview.md)，以便通过特定的服务层级和计算大小更好地利用可用内存。
- 深入了解查询详情，查看其文本和资源用量历史记录。
- 访问性能优化建议，这些建议可显示 [SQL 数据库顾问](database-advisor-implement-performance-recommendations.md)执行的操作。

### <a name="elastic-database-transaction-units-edtus"></a>弹性数据库事务单位 (eDTU)

对于始终可用的数据库，您可以将这些数据库放入[弹性池中](elastic-pool-overview.md)，而不是提供一组不一定需要的专用资源（dtu）。 弹性池中的数据库位于单个服务器上，并共享资源池。

弹性池中的共享资源用弹性数据库事务单位 (eDTU) 度量。 弹性池是一种简单的低成本高效益的解决方案，用于管理使用模式变化很大且不可预测的多个数据库的性能目标。 弹性池可保证不出现一个数据库使用池中所有资源的情况，同时确保池中的每个数据库始终可以使用最少量的必需资源。

为池提供的 eDTU 的数量和价格是固定的。 在弹性池中，单个数据库可在配置的边界内自动缩放。 负载较重的数据库消耗较多的 eDTU 来满足需求。 负载较轻的数据库消耗较少的 eDTU。 无负载的数据库不消耗任何 eDTU。 由于资源是为整个池预配的，而不是为每个数据库预配的，因此弹性池可以简化管理任务，使池的预算可预测。

可以向现有池添加额外的 eDTU，不会造成数据库关闭，对池中的数据库也不会有影响。 同样，可以随时从现有池中删除不再需要的额外 eDTU。 还可以随时在池中添加或去除数据库。 若要为其他数据库预留 eDTU，可以限制重负载数据库可用的 eDTU 数目。 如果某个数据库的资源用量一直不高，可将其移出池，并使用所需的可预测资源量将它配置为单一数据库。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>确定工作负荷所需的 DTU 数

如果要将现有的本地或 SQL Server 虚拟机工作负荷迁移到 SQL 数据库，请使用[dtu 计算器](https://dtucalculator.azurewebsites.net/)估计所需的 dtu 数。 对于现有的 SQL 数据库工作负荷，请使用[查询性能见解](query-performance-insight-use.md)来了解数据库资源消耗（dtu），并获得更深入的信息来优化工作负荷。 使用 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 动态管理视图 (DMV) 可以查看过去一小时的资源消耗。 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 目录视图显示过去 14 天的资源消耗，不过，五分钟平均值的准确性较低。

### <a name="determine-dtu-utilization"></a>确定 DTU 利用率

若要相对于数据库或弹性池的 DTU/eDTU 限制确定 DTU/eDTU 利用率平均百分比，请使用以下公式：

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

可从 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 和 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV 获取此公式的输入值。 换言之，若要相对于数据库或弹性池的 DTU/eDTU 限制确定 DTU/eDTU 利用率百分比，请从 `avg_cpu_percent`、`avg_data_io_percent` 和 `avg_log_write_percent` 中拾取给定时间点的最大百分比值。

> [!NOTE]
> 数据库的 DTU 限制由 CPU、读取次数、写入次数和数据库的可用内存决定。 但是，因为 SQL 数据库引擎通常会使用所有可用内存来提高性能，所以， `avg_memory_usage_percent` 无论当前数据库负载如何，该值通常都将接近100%。 因此，尽管内存确实会间接影响 DTU 限制，但 DTU 利用率公式中并不使用内存值。

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>受益于资源弹性池的工作负荷

池非常适合于资源利用率较低且利用率相对较少的数据库。 有关详细信息，请参阅[何时应当考虑使用 SQL 数据库弹性池？](elastic-pool-overview.md)。

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>基于 DTU 的购买模型中的硬件代系

在基于 DTU 的购买模型中，客户不能选择用于其数据库的硬件代系。 虽然通常情况下，某个给定的数据库会长时间（通常为多个月）驻留在特定的硬件代系上，但在出现特定事件的情况下，可能必须将数据库转移到另一个硬件代系。

例如，如果某个数据库已向上或向下扩展到不同的服务目标，或者数据中心的当前基础结构正在接近其容量限制，或者当前使用的硬件正在停止运行，则可以将该数据库移到不同的硬件生成中。

如果将数据库移到另一硬件上，工作负载性能可能会变化。 DTU 模型保证 [DTU 基准](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark)工作负载的吞吐量和响应时间在数据库移到另一硬件代系时保持大体相同，前提是其服务目标（DTU 数）保持不变。

但是，在 Azure SQL 数据库中运行的各种客户工作负载中，对相同服务目标使用不同硬件的影响可能更明显。 不同的工作负载将受益于不同的硬件配置和功能。 因此，对于 DTU 基准以外的工作负荷，如果数据库从一个硬件生成移到另一个，则可能会看到性能差异。

例如，对网络延迟敏感的应用程序可能会在 Gen5 硬件与 Gen4 上看到更好的性能，因为使用 Gen5 中的加速网络，而使用大量读取 IO 的应用程序可以在 Gen4 硬件和 Gen5 上查看更好的性能，因为 Gen4 上的每个核心比率更高。

如果客户的工作负荷对硬件更改或想要控制其数据库的硬件生成选择的客户，则可以使用[vCore](service-tiers-vcore.md)模型在数据库创建和缩放期间选择其首选的硬件生成。 在 vCore 模型中，会记录[单一数据库](resource-limits-vcore-single-databases.md)和[弹性池](resource-limits-vcore-elastic-pools.md)的每个硬件代系上每个服务目标的资源限制。 有关 vCore 模型中的硬件生成的详细信息，请参阅[硬件代](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations)。

## <a name="frequently-asked-questions-faqs"></a>常见问题 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>是否需要将应用程序脱机，才能将基于 DTU 的服务层级转换为基于 vCore 的服务层级？

否。 不需要使应用程序脱机。 新服务层级提供简单的在线转换方式，与现有“标准”和“高级”服务层级间的升级和降级过程类似。 可以使用 Azure 门户、PowerShell、Azure CLI、T-SQL 或 REST API 启动此转换。 请参阅[管理单一数据库](single-database-scale.md)和[管理弹性池](elastic-pool-overview.md)。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>是否可以将数据库从使用基于 vCore 的购买模型中的服务层级转换为使用基于 DTU 的购买模型中的服务层级？

是的，可以使用 Azure 门户、PowerShell、Azure CLI、T-SQL 或 REST API 轻松将数据库转换为支持的任何性能目标。 请参阅[管理单一数据库](single-database-scale.md)和[管理弹性池](elastic-pool-overview.md)。

## <a name="next-steps"></a>后续步骤

- 有关基于 vCore 的购买模型的详细信息，请参阅[基于 vCore 的购买模型](service-tiers-vcore.md)。
- 有关基于 DTU 的购买模型的详细信息，请参阅[基于 DTU 的购买模型](service-tiers-dtu.md)。
