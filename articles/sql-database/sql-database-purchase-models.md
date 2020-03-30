---
title: 购买模型
description: 了解适用于 Azure SQL 数据库的购买模型。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255985"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>在 vCore 和 DTU 购买模型之间进行选择

使用 Azure SQL 数据库，可以轻松购买适合你的性能和成本要求的完全托管的平台即服务 (PaaS) 数据库引擎。 可以根据选择用于 Azure SQL 数据库的部署模型选择适合需求的购买模型：

- [基于虚拟核心 (vCore) 的购买模型](sql-database-service-tiers-vcore.md)（推荐）。 此购买模型允许在预配的计算层级和无服务器计算层级之间进行选择。 使用预配的计算层级时，可以选择始终为工作负荷预配的确切计算资源。 使用无服务器计算层级时，可以指定对计算资源进行的自动缩放（在可配置的计算范围内）。 使用此计算层级时，还可以根据工作负荷活动自动暂停和恢复数据库。 预配计算层级中单位时间的 vCore 单位价格低于无服务器计算层级中的相应价格。
- [基于数据库事务单位 (DTU) 的购买模型](sql-database-service-tiers-dtu.md)。 此购买模型针对常见工作负荷提供均衡的捆绑计算和存储包。

不同的购买模型适用于不同的 Azure SQL 数据库部署模型：

- [Azure SQL 数据库](sql-database-technical-overview.md)中的[单一数据库](sql-database-single-databases-manage.md)和[弹性池](sql-database-elastic-pool.md)部署选项提供[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- Azure SQL 数据库中的[托管实例](sql-database-managed-instance.md)部署选项仅提供[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- [超大规模服务层级](sql-database-service-tier-hyperscale.md)适用于使用[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)的单一数据库。

以下表格和图表对基于 vCore 和基于 DTU 的购买模型做了对比。

|**购买模型**|**说明**|**最适用于**|
|---|---|---|
|基于 DTU 的模型|此模型基于计算、存储和 I/O 资源的捆绑度量。 单一数据库的计算大小以 DTU 表示，弹性池则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU 和 eDTU？](sql-database-purchase-models.md#dtu-based-purchasing-model)。|最适合希望获得简单预配置资源选项的客户。|
|基于 vCore 的模型|此模型允许单独选择计算和存储资源。 基于 vCore 的购买模型还允许使用[适用于 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来节省成本。|最适合注重灵活性、控制度和透明度的客户。|
||||  

![定价模型比较](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>计算成本

### <a name="provisioned-compute-costs"></a>预配计算成本

在预配计算层级中，计算成本反映针对应用程序预配的总计算容量。

在“业务关键”服务层级中，我们会自动分配至少 3 个副本。 为了反映计算资源的此额外分配，在基于 vCore 的购买模型中，“业务关键”服务层级的价格比“常规用途”服务层级的价格高约 2.7 倍。 同理，“业务关键”服务层级中更高的每 GB 存储价格反映了 SSD 存储的更高 IO 限制和更低延迟。

“业务关键”服务层级和“常规用途”服务层级的备份存储成本是相同的，因为两者都使用标准存储进行备份。

### <a name="serverless-compute-costs"></a>无服务器计算成本

对于无服务器计算层级，请参阅 [SQL 数据库无服务器](sql-database-serverless.md)，其中介绍了如何定义计算容量以及如何计算成本。

## <a name="storage-costs"></a>存储成本

不同存储类型的计费方式各不相同。 对于数据存储，你要根据所选的最大数据库或池大小支付预配存储的费用。 除非减小或增大该最大值，否则费用不会变化。 备份存储与实例的自动备份相关联，并动态分配。 增加备份保留期会增加实例使用的备份存储。

默认情况下，数据库的 7 天自动备份会复制到读取访问异地冗余存储 (RA-GRS) 标准 Blob 存储帐户。 存储由每周完整备份、每日差异备份和 5 分钟复制一次的事务日志备份使用。 事务日志的大小取决于数据库的变化率。 提供与 100% 数据库大小相等的最小存储量，不收取额外费用。 超出此部分的其他备份存储空间按 GB/月收费。

有关存储价格的详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/sql-database/single/)页。

## <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

虚拟核心 (vCore) 表示通过一个选项提供的逻辑 CPU，你可以在硬件的层代和硬件的物理特性（例如，核心数、内存、存储大小）之间进行选择。 基于 vCore 的购买模型提供使用单项资源的灵活性、控制度和透明度，并提供简单明了的方法将本地工作负荷要求转换到云。 此模型允许根据工作负荷需求来选择计算、内存和存储资源。

在基于 vCore 的购买模型中，可以为[单一数据库](sql-database-single-database-scale.md)、[弹性池](sql-database-elastic-pool.md)和[托管实例](sql-database-managed-instance.md)选择[常规用途](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)或[业务关键](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)服务层级。 对于单一数据库，还可以选择[超大规模服务层级](sql-database-service-tier-hyperscale.md)。

使用基于 vCore 的购买模型，可以单独选择计算和存储资源、匹配本地性能，以及优化价格。 在基于 vCore 的购买模型中，费用包括：

- 计算资源（服务层级 + vCore 数目和内存量 + 硬件层代）。
- 数据和日志存储的类型与数量。
- 备份存储 (RA-GRS)。

> [!IMPORTANT]
> 计算资源、I/O 以及数据和日志存储按数据库或弹性池收费。 备份存储按每个数据库收费。 有关托管实例费用的详细信息，请参阅[托管实例](sql-database-managed-instance.md)。
> **区域限制：** 有关当前受支持区域的列表，请参阅[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 若要在当前不受支持的区域中创建托管实例，请[通过 Azure 门户发送支持请求](quota-increase-request.md)。

如果单一数据库或弹性池消耗的 DTU 超过 300 个，则转换为基于 vCore 的购买模型可以降低成本。 可以使用所选的 API 或 Azure 门户进行转换，无需停机。 但是，转换不是必需的并且不会自动完成。 如果基于 DTU 的购买模型可以满足性能和业务要求，应继续使用它。

若要从基于 DTU 的购买模型转换为基于 vCore 的购买模型，请使用以下经验法则来选择计算大小：

- 标准层级中的每 100 个 DTU 至少需要“常规用途”服务层级中的 1 个 vCore。
- 高级层级中的每 125 个 DTU 至少需要“业务关键”服务层级中的 1 个 vCore。

> [!NOTE]
> DTU vCore 大小调整指南是近似值的，并且提供有助于对目标数据库服务目标进行初始估计。 目标数据库的最佳配置取决于工作负载。 
> 
> 实现最佳性价比可能需要利用 vCore 模型的灵活性来调整 vCore 的数量、[硬件生成](sql-database-service-tiers-vcore.md#hardware-generations)、[服务和](sql-database-service-tiers-vcore.md#service-tiers)[计算](sql-database-service-tiers-vcore.md#compute-tiers)层，以及调整其他数据库配置参数，如[最大并行性程度](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)。

## <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

数据库事务单位 (DTU) 表示 CPU、内存、读取和写入的混合度量。 基于 DTU 的购买模型提供一组预配置的计算资源套件和随附的存储，以促成不同级别的应用程序性能。 如果你偏爱预配置套件简易性和每月定价付款，基于 DTU 的模型可能更符合你的需求。

在基于 DTU 的购买模型中，可为[单一数据库](sql-database-single-database-scale.md)和[弹性池](sql-database-elastic-pool.md)选择“基本”、“标准”或“高级”服务层级。 基于 DTU 的购买模型不适用于[托管实例](sql-database-managed-instance.md)。

### <a name="database-transaction-units-dtus"></a>数据库事务单位 (DTU)

对于[服务层](sql-database-single-database-scale.md)中具有特定计算大小的单个数据库，Microsoft 保证该数据库的一定级别的资源（独立于 Azure 云中的任何其他数据库）。 这可以保证提供可预测的性能级别。 为数据库分配的资源量是以若干 DTU 计算的，是计算资源、存储资源和 I/O 资源的捆绑度量值。

这些资源之间的比率最初由[联机事务处理 (OLTP) 基准工作负荷](sql-database-benchmark-overview.md)决定，后者旨在作为典型的真实 OLTP 工作负荷。 工作负荷超过任何以上资源量时，吞吐量将受到限制，从而导致性能下降和超时。

工作负荷使用的资源不会影响 Azure 云中其他 SQL 数据库可用的资源。 同样，其他工作负荷使用的资源也不会影响你的 SQL 数据库可用的资源。

![边界框](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU 最好地解释了在不同计算大小和服务层级为 Azure SQL 数据库分配的相对资源量。 例如：

- 提高数据库的计算大小可使 DTU 加倍，这等同于使该数据库可用的资源集增加一倍。
- 具有 1750 个 DTU 的高级服务层级 P11 数据库提供的 DTU 计算能力是具有 5 个 DTU 的基本服务层级数据库的 350 倍。  

若要更深入地了解工作负荷的资源 (DTU) 消耗，请使用[查询性能见解](sql-database-query-performance.md)：

- 按 CPU/持续时间/执行计数确定热门查询，可以对这些查询进行调整来提高性能。 例如，I/O 密集型查询可能会受益于[内存中优化技术](sql-database-in-memory.md)，以便通过特定的服务层级和计算大小更好地利用可用内存。
- 深入了解查询详情，查看其文本和资源用量历史记录。
- 访问性能优化建议，这些建议可显示 [SQL 数据库顾问](sql-database-advisor.md)执行的操作。

### <a name="elastic-database-transaction-units-edtus"></a>弹性数据库事务单位 (eDTU)

对于始终保持可用的 SQL 数据库，可将其放入[弹性池](sql-database-elastic-pool.md)，而不必提供一组专用资源 (DTU)，因为有时可能并不需要这些资源。 弹性池中的所有数据库位于单个 Azure SQL 数据库服务器上，它们共享一个资源池。

弹性池中的共享资源用弹性数据库事务单位 (eDTU) 度量。 弹性池是一种简单的低成本高效益的解决方案，用于管理使用模式变化很大且不可预测的多个数据库的性能目标。 弹性池可保证不出现一个数据库使用池中所有资源的情况，同时确保池中的每个数据库始终可以使用最少量的必需资源。

为池提供的 eDTU 的数量和价格是固定的。 在弹性池中，单个数据库可在配置的边界内自动缩放。 负载较重的数据库消耗较多的 eDTU 来满足需求。 负载较轻的数据库消耗较少的 eDTU。 无负载的数据库不消耗任何 eDTU。 由于资源是为整个池预配的，而不是为每个数据库预配的，因此弹性池可以简化管理任务，使池的预算可预测。

可以向现有池添加额外的 eDTU，不会造成数据库关闭，对池中的数据库也不会有影响。 同样，可以随时从现有池中删除不再需要的额外 eDTU。 还可以随时在池中添加或去除数据库。 若要为其他数据库预留 eDTU，可以限制重负载数据库可用的 eDTU 数目。 如果某个数据库的资源用量一直不高，可将其移出池，并使用所需的可预测资源量将它配置为单一数据库。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>确定工作负荷所需的 DTU 数

若要将现有的本地或 SQL Server 虚拟机工作负荷迁移到 Azure SQL 数据库，可以使用 [DTU 计算器](https://dtucalculator.azurewebsites.net/)来估算所需的 DTU 数目。 对于现有的 Azure SQL 数据库工作负荷，可以使用[查询性能见解](sql-database-query-performance.md)来了解数据库资源使用量 (DTU)，更深入地了解如何优化工作负荷。 使用 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) 动态管理视图 (DMV) 可以查看过去一小时的资源消耗。 [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 目录视图显示过去 14 天的资源消耗，不过，五分钟平均值的准确性较低。

### <a name="determine-dtu-utilization"></a>确定 DTU 利用率

若要相对于数据库或弹性池的 DTU/eDTU 限制确定 DTU/eDTU 利用率平均百分比，请使用以下公式：

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

可从 [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)、[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) 和 [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV 获取此公式的输入值。 换言之，若要相对于数据库或弹性池的 DTU/eDTU 限制确定 DTU/eDTU 利用率百分比，请从 `avg_cpu_percent`、`avg_data_io_percent` 和 `avg_log_write_percent` 中拾取给定时间点的最大百分比值。

> [!NOTE]
> 数据库的 DTU 限制由 CPU、读取次数、写入次数和数据库的可用内存决定。 但是，由于 SQL Server 数据库引擎通常会使用所有可用内存来使其数据缓存提高性能，不管当前数据库负载如何，`avg_memory_usage_percent` 值通常都接近 100%。 因此，尽管内存确实会间接影响 DTU 限制，但 DTU 利用率公式中并不使用内存值。
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>受益于资源弹性池的工作负荷

池很适合用于低平均资源利用率和相对不频繁的使用高峰的数据库。 有关详细信息，请参阅[何时应当考虑使用 SQL 数据库弹性池？](sql-database-elastic-pool.md)。

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>基于 DTU 的采购模型中的硬件代

在基于 DTU 的采购模型中，客户无法选择用于其数据库的硬件生成。 虽然给定数据库通常在特定硬件生成上长时间（通常持续数月），但某些事件可能会导致数据库移动到另一个硬件生成。

例如，如果数据库被向上扩展或缩减为不同的服务目标，或者数据中心中的当前基础结构接近其容量限制，或者当前使用的硬件正在退役，由于其寿命的结束。

如果将数据库移动到不同的硬件，工作负载性能可能会更改。 DTU 模型可确保[DTU 基准](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark)工作负载的吞吐量和响应时间将基本相同，因为数据库将移动到不同的硬件生成，只要其服务目标（DTU 的数量）保持不变。 

但是，在 Azure SQL 数据库中运行的各种客户工作负载中，对同一服务目标使用不同的硬件的影响可能更为明显。 不同的工作负载将受益于不同的硬件配置和功能。 因此，对于 DTU 基准以外的工作负载，如果数据库从一个硬件生成移动到另一个硬件生成，则可以看到性能差异。

例如，由于在 Gen5 中使用加速网络，对网络延迟敏感的应用程序在 Gen5 硬件上可以看到更好的性能，但使用密集读取 IO 的应用程序在 Gen4 硬件上可以看到更好的性能，因为 Gen4 硬件与 Gen5 的性能更高第 4 代上每个内核比的内存更高。

具有对硬件更改敏感的工作负载的客户，或者希望控制数据库硬件生成选择的客户，可以使用[vCore](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore)模型在数据库创建和缩放期间选择首选硬件生成。 在 vCore 模型中，记录每个硬件生成上每个服务目标的资源限制，包括[单个数据库](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)和[弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)。 有关 vCore 模型中硬件生成的详细信息，请参阅[硬件生成](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations)。

## <a name="frequently-asked-questions-faqs"></a>常见问题 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>是否需要将应用程序脱机，才能将基于 DTU 的服务层级转换为基于 vCore 的服务层级？

不是。 不需要使应用程序脱机。 新服务层级提供简单的在线转换方式，与现有“标准”和“高级”服务层级间的升级和降级过程类似。 可以使用 Azure 门户、PowerShell、Azure CLI、T-SQL 或 REST API 启动此转换。 请参阅[管理单个数据库](sql-database-single-database-scale.md)[和管理弹性池](sql-database-elastic-pool.md)。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>是否可以将数据库从使用基于 vCore 的购买模型中的服务层级转换为使用基于 DTU 的购买模型中的服务层级？

是的，可以使用 Azure 门户、PowerShell、Azure CLI、T-SQL 或 REST API 轻松将数据库转换为支持的任何性能目标。 请参阅[管理单个数据库](sql-database-single-database-scale.md)[和管理弹性池](sql-database-elastic-pool.md)。

## <a name="next-steps"></a>后续步骤

- 有关基于 vCore 的购买模型的详细信息，请参阅[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- 有关基于 DTU 的购买模型的详细信息，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)。
