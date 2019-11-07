---
title: 'Azure SQL 数据库购买模型 '
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
ms.date: 04/26/2019
ms.openlocfilehash: 48537ec4c62410565d32350b1e3a11bbc7a2ca6e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687558"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>在 vCore 和 DTU 购买模型之间进行选择

使用 Azure SQL 数据库，可以轻松购买适合你的性能和成本要求的完全托管的平台即服务 (PaaS) 数据库引擎。 可以根据选择用于 Azure SQL 数据库的部署模型选择适合需求的购买模型：

- [基于虚拟核心 (vCore) 的购买模型](sql-database-service-tiers-vcore.md)（推荐）。 此购买模式在预配的计算层和无服务器计算层之间提供了选择。 使用预配的计算层级时，可以选择始终为工作负荷预配的确切计算资源。 使用无服务器计算层级时，可以指定对计算资源进行的自动缩放（在可配置的计算范围内）。 使用此计算层级时，还可以根据工作负荷活动自动暂停和恢复数据库。 预配计算层级中单位时间的 vCore 单位价格低于无服务器计算层级中的相应价格。
- [基于数据库事务单位 (DTU) 的购买模型](sql-database-service-tiers-dtu.md)。 此购买模型针对常见工作负荷提供均衡的捆绑计算和存储包。

不同的购买模型适用于不同的 Azure SQL 数据库部署模型：

- [Azure SQL 数据库](sql-database-single-databases-manage.md)中的[单一数据库](sql-database-elastic-pool.md)和[弹性池](sql-database-technical-overview.md)部署选项提供[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- Azure SQL 数据库中的[托管实例](sql-database-managed-instance.md)部署选项仅提供[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- [超大规模服务层级](sql-database-service-tier-hyperscale.md)仅适用于使用[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)的单一数据库。

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

在“业务关键”服务层级中，我们会自动分配至少 3 个副本。 为了反映计算资源的此额外分配，在基于 vCore 的购买模型中，“业务关键”服务层级的价格比“常规用途”服务层级的价格高约 2.7 倍。 同理，“业务关键”服务层级中更高的每 GB 存储价格反映了 SSD 存储的高 I/O 和低延迟。

业务关键服务层和常规用途服务层的备份存储成本是相同的，因为两者都使用标准存储。

### <a name="serverless-compute-costs"></a>无服务器计算成本

有关如何定义计算容量和计算无服务器计算层开销的说明，请参阅[SQL 数据库无服务器](sql-database-serverless.md)。

## <a name="storage-costs"></a>存储成本

不同存储类型的计费方式各不相同。 对于数据存储，你要根据所选的最大数据库或池大小支付预配存储的费用。 除非减小或增大该最大值，否则费用不会变化。 备份存储与实例的自动备份相关联，并动态分配。 延长备份保留期会使实例使用的备份存储增大。

默认情况下，数据库的 7 天自动备份会复制到读取访问异地冗余存储 (RA-GRS) 标准 Blob 存储帐户。 存储由每周完整备份、每日差异备份和 5 分钟复制一次的事务日志备份使用。 事务日志的大小取决于数据库的变化率。 提供与 100% 数据库大小相等的最小存储量，不收取额外费用。 超出此部分的其他备份存储空间按 GB/月收费。

有关存储价格的详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/sql-database/single/)页。

## <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

虚拟核心 (vCore) 表示通过一个选项提供的逻辑 CPU，你可以在硬件的层代和硬件的物理特性（例如，核心数、内存、存储大小）之间进行选择。 基于 vCore 的购买模型提供使用单项资源的灵活性、控制度和透明度，并提供简单明了的方法将本地工作负荷要求转换到云。 此模型允许根据工作负荷需求来选择计算、内存和存储资源。

在基于 vCore 的购买模型中，可以为[单一数据库](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)、[弹性池](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)和[托管实例](sql-database-single-database-scale.md)选择[常规用途](sql-database-elastic-pool.md)或[业务关键](sql-database-managed-instance.md)服务层级。 对于单一数据库，还可以选择[超大规模服务层级](sql-database-service-tier-hyperscale.md)。

使用基于 vCore 的购买模型，可以单独选择计算和存储资源、匹配本地性能，以及优化价格。 在基于 vCore 的购买模型中，费用包括：

- 计算资源（服务层级 + vCore 数目和内存量 + 硬件层代）。
- 数据和日志存储的类型与数量。
- 备份存储 (RA-GRS)。

> [!IMPORTANT]
> 计算资源、I/O 以及数据和日志存储按数据库或弹性池收费。 备份存储按每个数据库收费。 有关托管实例费用的详细信息，请参阅[托管实例](sql-database-managed-instance.md)。
> **区域限制：** 有关支持的区域的最新列表，请参阅[可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)）。 若要在当前不受支持的区域中创建托管实例，请[通过 Azure 门户发送支持请求](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance)。

如果单一数据库或弹性池消耗的 DTU 超过 300 个，则转换为基于 vCore 的购买模型可以降低成本。 可以使用所选的 API 或 Azure 门户进行转换，无需停机。 但是，转换不是必需的并且不会自动完成。 如果基于 DTU 的购买模型可以满足性能和业务要求，应继续使用它。

若要从基于 DTU 的购买模型转换为基于 vCore 的购买模型，请使用以下经验法则来选择计算大小：

- 标准层中的每 100 个 DTU 至少需要常规用途服务层中的 1 个 vCore。
- 高级层中的每 125 个 DTU 至少需要业务关键服务层中的 1 个 vCore。

## <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

数据库事务单位 (DTU) 表示 CPU、内存、读取和写入的混合度量。 基于 DTU 的购买模型提供一组预配置的计算资源套件和随附的存储，以促成不同级别的应用程序性能。 如果你偏爱预配置套件简易性和每月定价付款，基于 DTU 的模型可能更符合你的需求。

在基于 DTU 的购买模型中，可为[单一数据库](sql-database-single-database-scale.md)和[弹性池](sql-database-elastic-pool.md)选择“基本”、“标准”或“高级”服务层级。 基于 DTU 的购买模型不适用于[托管实例](sql-database-managed-instance.md)。

### <a name="database-transaction-units-dtus"></a>数据库事务单位 (DTU)

对于[服务层](sql-database-single-database-scale.md)内特定计算大小的单一数据库，Microsoft 保证该数据库（独立于 Azure 云中的任何其他数据库）的特定资源级别。 这可以保证提供可预测的性能级别。 为数据库分配的资源量是以若干 DTU 计算的，是计算资源、存储资源和 I/O 资源的捆绑度量值。

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

若要将现有的本地或 SQL Server 虚拟机工作负荷迁移到 Azure SQL 数据库，可以使用 [DTU 计算器](https://dtucalculator.azurewebsites.net/)来估算所需的 DTU 数目。 对于现有的 Azure SQL 数据库工作负荷，可以使用[查询性能见解](sql-database-query-performance.md)来了解数据库资源使用量 (DTU)，更深入地了解如何优化工作负荷。 使用 [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 动态管理视图 (DMV) 可以查看过去一小时的资源消耗。 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 目录视图显示过去 14 天的资源消耗，不过，五分钟平均值的准确性较低。

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>受益于资源弹性池的工作负荷

池很适合用于低平均资源利用率和相对不频繁的使用高峰的数据库。 SQL数据库自动评估现有 SQL 数据库服务器中数据库的历史资源使用率，并在 Azure 门户中推荐适当的池配置。 有关详细信息，请参阅[何时应当考虑使用 SQL 数据库弹性池？](sql-database-elastic-pool.md)。

## <a name="frequently-asked-questions-faqs"></a>常见问题 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>是否需要将应用程序脱机，才能将基于 DTU 的服务层级转换为基于 vCore 的服务层级？

不能。 不需要使应用程序脱机。 新服务层级提供简单的在线转换方式，与现有“标准”和“高级”服务层级间的升级和降级过程类似。 可以使用 Azure 门户、PowerShell、Azure CLI、T-SQL 或 REST API 启动此转换。 请参阅[管理单一数据库](sql-database-single-database-scale.md)和[管理弹性池](sql-database-elastic-pool.md)。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>是否可以将数据库从使用基于 vCore 的购买模型中的服务层级转换为使用基于 DTU 的购买模型中的服务层级？

是的，可以使用 Azure 门户、PowerShell、Azure CLI、T-SQL 或 REST API 轻松将数据库转换为支持的任何性能目标。 请参阅[管理单一数据库](sql-database-single-database-scale.md)和[管理弹性池](sql-database-elastic-pool.md)。

## <a name="next-steps"></a>后续步骤

- 有关基于 vCore 的购买模型的详细信息，请参阅[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- 有关基于 DTU 的购买模型的详细信息，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)。
