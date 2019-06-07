---
title: Azure SQL 数据库购买模型 | Microsoft Docs
description: 了解有关适用于 Azure SQL Database 的购买模型。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431366"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>VCore 与 DTU 的购买模型之间选择

Azure SQL 数据库允许你轻松地购买适合你性能和成本的需求的服务 (PaaS) 数据库引擎作为完全托管的平台。 根据你为 Azure SQL 数据库选择的部署模型，您可以选择适合您的购买模型：

- [虚拟核心 (vCore)-基于购买模型](sql-database-service-tiers-vcore.md)（推荐）。 此购买模型提供了预配的计算层和无服务器 （预览版） 计算层之间进行选择。 使用预配的计算层中，选择工作负荷的确切始终预配的计算资源量。 使用无服务器计算层中，您可配置的计算范围内指定计算资源自动的缩放。 凭借此计算层，还会自动可以暂停和恢复数据库根据工作负荷活动。 每个单位的时间的 vCore 单位价格低于预配的计算层中处于无服务器计算层。
- [数据库事务单位 (DTU)-基于购买模型](sql-database-service-tiers-dtu.md)。 此购买模型针对常见工作负荷提供均衡的捆绑计算和存储包。

不同的购买模型是可用于不同的 Azure SQL 数据库部署模型：

- [Azure SQL 数据库](sql-database-technical-overview.md)中的[单一数据库](sql-database-single-databases-manage.md)和[弹性池](sql-database-elastic-pool.md)部署选项提供[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- [托管的实例](sql-database-managed-instance.md)Azure SQL 数据库中的部署选项提供仅[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- [超大规模服务层](sql-database-service-tier-hyperscale.md)可供使用的单一数据库[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。

下面的表和图表进行比较和对比基于 vCore 的模型和基于 DTU 的购买模型：

|**购买模型**|**说明**|**最适用于**|
|---|---|---|
|基于 DTU 的模型|此模型基于计算、 存储和 I/O 资源的捆绑度量值。 在单一数据库的 dtu 数和弹性池的弹性数据库事务单位 (Edtu) 表示计算大小。 有关 Dtu 和 Edtu 的详细信息，请参阅[什么是 Dtu 和 Edtu？](sql-database-purchase-models.md#dtu-based-purchasing-model)。|最适合希望简单、 预配置资源选项的客户。|
|基于 vCore 的模型|此模型允许单独选择计算和存储资源。 基于 vCore 的购买模型还允许使用[适用于 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来节省成本。|最适合注重灵活性、控制度和透明度的客户。|
||||  

![定价模型比较](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>计算成本

### <a name="provisioned-compute-costs"></a>预配计算成本

在预配计算层级中，计算成本反映针对应用程序预配的总计算容量。

在“业务关键”服务层级中，我们会自动分配至少 3 个副本。 以反映计算资源的附加分配，在基于 vCore 的购买模型中的价格是大约 2.7 倍更高版本中业务关键型服务层比常规用途服务层。 同样，每 GB 业务关键服务层中的更高版本存储价格反映了的高 I/O 和 SSD 存储的低延迟。

备份存储成本是相同的业务关键型服务层和常规用途服务层，因为这两个层使用标准存储。

### <a name="serverless-compute-costs"></a>无服务器计算成本

无服务器计算层计算成本和计算能力的定义方式的说明，请参阅[SQL 数据库无服务器 （预览版）](sql-database-serverless.md)。

## <a name="storage-costs"></a>存储成本

不同存储类型的计费方式各不相同。 对于数据存储，我们根据你选择的最大数据库或池大小的预配存储进行收费。 除非减小或增大该最大值，则不会更改成本。 备份存储与实例的自动备份相关联，并动态分配。 增长备份保留期会使实例使用的备份存储空间。

默认情况下，7 天的自动备份的数据库复制到读取访问异地冗余存储 (RA-GRS) 标准 Blob 存储帐户。 此存储可供每周完整备份、 每日差异备份和事务日志备份，将其复制每 5 分钟。 事务日志的大小取决于数据库变化率。 最小存储量等于 100%数据库大小提供无需额外付费。 超出此部分的其他备份存储空间按 GB/月收费。

有关存储价格的详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/sql-database/single/)页。

## <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

虚拟核心 (vCore) 表示逻辑 CPU，并为你提供几代的硬件和硬件 （例如，核心、 内存和存储大小的数字） 的物理特性之间进行选择的选项。 基于 vCore 的购买模型提供了您的灵活性、 控制、 单项资源消耗的透明度和进行转换的简单方法的本地到云的工作负荷要求。 此模型，可选择根据工作负荷需求的计算、 内存和存储资源。

在基于 vCore 的购买模型中，您可以选择之间[常规用途](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)并[业务关键](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)服务的层[单一数据库](sql-database-single-database-scale.md)， [弹性池](sql-database-elastic-pool.md)，并[托管实例](sql-database-managed-instance.md)。 对于单一数据库，还可以选择[超大规模服务层级](sql-database-service-tier-hyperscale.md)。

基于 vCore 的购买模型允许单独选择计算和存储资源、 匹配本地性能和优化价格。 在基于 vCore 的购买模型中，您支付：

- 计算资源 （服务层 + Vcore 数和内存 + 硬件代的量）。
- 类型和数据和日志存储量。
- 备份存储 (RA-GRS)。

> [!IMPORTANT]
> 每个数据库或弹性池支付计算资源、 I/O 和数据和日志存储。 备份存储按每个数据库收费。 有关托管实例费用的详细信息，请参阅[托管实例](sql-database-managed-instance.md)。
> 区域限制：  有关受支持区域的当前列表，请参阅[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)。 若要在当前不支持的区域中创建的托管的实例[发送一个通过 Azure 门户的支持请求](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance)。

如果单一数据库或弹性池消耗的 300 多个 Dtu，则将转换为基于 vCore 的购买模型可能会降低你的成本。 可以将转换通过使用所选的 API 或通过使用 Azure 门户中，而无需停机。 但是，转换不是必需的并且无法自动完成。 如果基于 DTU 的购买模型可以满足性能和业务要求，应继续使用它。

若要从基于 DTU 的购买模型转换为基于 vCore 的购买模型，请使用以下规则的经验法则选择的计算实例大小：

- 在标准层中的每个 100 个 Dtu 要求在常规用途服务层中的至少 1 个 vCore。
- 高级层中每 125 个 Dtu 需要至少 1 个 vCore 业务关键服务层中。

## <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

数据库事务单位 (DTU) 表示 CPU、 内存、 读取和写入的混合度量值。 基于 DTU 的购买模型提供一组预配置的计算资源套件和随附的存储，以促成不同级别的应用程序性能。 如果希望使用的预配的捆绑和固定的付款的简单每个月，基于 DTU 的模型可能更适合你的需求。

在基于 DTU 的购买模型中，您可以选择基本、 标准和高级服务层之间的这两[单一数据库](sql-database-single-database-scale.md)并[弹性池](sql-database-elastic-pool.md)。 基于 DTU 的购买模型不适用于[托管实例](sql-database-managed-instance.md)。

### <a name="database-transaction-units-dtus"></a>数据库事务单位 (DTU)

为单个数据库中的特定计算中的大小[服务层](sql-database-single-database-scale.md)，Microsoft 保证一定级别的对该数据库的资源 （独立于 Azure 云中的任何其他数据库）。 这一保证提供可预测的性能级别。 为数据库分配的资源量的计算方式的 Dtu 数和计算、 存储和 I/O 资源的捆绑度量值。

在这些资源之间的比率最初由[联机事务处理 (OLTP) 基准工作负荷](sql-database-benchmark-overview.md)旨在作为典型的真实 OLTP 工作负荷。 当你的工作负荷超过任何这些资源的量时，吞吐量将受到限制，从而导致性能下降和超时值。

使用你的工作负载的资源不影响其他 SQL 数据库的 Azure 云可用的资源。 同样，使用其他工作负荷的资源不会影响可用于您的 SQL 数据库的资源。

![边界框](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu 将最为有用了解为 Azure SQL 数据库在不同的计算大小和服务层分配的相对资源。 例如：

- 使 Dtu 加倍通过增加数据库的计算大小等同于可用于该数据库的资源集增加一倍。
- 具有 1750 个 Dtu 的高级服务层 P11 数据库提供 350 倍更多的 DTU 计算能力比具有 5 个 Dtu 的基本服务层数据库。  

若要获得更深入地了解你的工作负荷的资源 (DTU) 消耗，请使用[查询性能见解](sql-database-query-performance.md)到：

- 按可以进行调整来提高性能的 CPU/持续时间/执行计数确定热门查询。 例如，O 密集型查询可能会受益于[内存中优化技术](sql-database-in-memory.md)以更好地利用可用内存在某一特定服务层和计算大小。
- 向下钻取到查询以查看其文本和资源使用情况的历史记录的详细信息。
- 访问显示所执行的操作的性能优化建议[SQL 数据库顾问](sql-database-advisor.md)。

### <a name="elastic-database-transaction-units-edtus"></a>弹性数据库事务单位 (Edtu)

对于始终可用的 SQL 数据库，而不是提供一组专用的资源 (Dtu)，可能不总是需要可以将放置到这些数据库[弹性池](sql-database-elastic-pool.md)。 在弹性池中的数据库位于单个 Azure SQL 数据库服务器和共享的资源池。

在弹性池中的共享的资源的弹性数据库事务单位 (Edtu) 衡量。 弹性池提供简单、 经济高效的解决方案，以管理具有很大不同和不可预测的使用情况模式的多个数据库的性能目标。 弹性池可保证所有的资源不能通过在池中的一个数据库使用同时确保池中的每个数据库始终具有最少量的必要的资源可用。

为池提供的 eDTU 的数量和价格是固定的。 弹性池中单个数据库可以自动缩放配置的边界内。 在较重负载下的数据库将消耗更多的 Edtu 来满足需求。 在负载较轻的数据库会占用较少的 Edtu。 无负载的数据库不消耗任何 eDTU。 为整个池而不是每个数据库预配资源，因为弹性池简化管理任务，为池提供可预测的预算。

可以与任何数据库的停机时间和池中的数据库不会影响现有池添加额外的 Edtu。 同样，如果不再需要额外的 Edtu，它们从现有池中删除在任何时间。 此外可以添加到数据库，或在任何时间减去从池中的数据库。 若要保留 Edtu 供其他数据库，限制在重负载下可以使用数据库的 Edtu 数。 如果数据库一致地 underuses 资源，将其移出池，并将其配置为单一数据库具有可预测的所需的资源量。

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>确定工作负荷所需的 DTU 数

如果你想要迁移现有的本地或 SQL Server 虚拟机工作负荷到 Azure SQL 数据库，请参考[DTU 计算器](https://dtucalculator.azurewebsites.net/)估计所需的 Dtu 数。 对于现有的 Azure SQL 数据库工作负荷，使用[查询性能见解](sql-database-query-performance.md)了解你的数据库资源使用量 (Dtu)，并获得有关优化工作负荷更深入的见解。 [Sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)动态管理视图 (DMV) 可让您可以查看过去一小时的资源消耗。 [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)目录视图显示过去 14 天，但准确性较低的五分钟平均值的资源消耗。

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>受益于资源弹性池的工作负荷

池很适合具有较低的资源利用率的平均值和相对不频繁使用率高峰的数据库。 SQL 数据库自动评估现有 SQL 数据库服务器上的数据库的历史资源使用量，并推荐适当的池配置在 Azure 门户中。 有关详细信息，请参阅[时应考虑 SQL 数据库弹性池？](sql-database-elastic-pool.md)。

## <a name="frequently-asked-questions-faqs"></a>常见问题 (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>我是否需要执行应用程序脱机，若要从基于 DTU 的服务层将转换为基于 vCore 的服务层？

不。 不需要使应用程序处于脱机状态。 新的服务层提供简单的在线转换方式，类似于现有标准数据库升级到高级服务层和周围的其他方法的过程。 可以使用 Azure 门户、 PowerShell、 Azure CLI、 T-SQL、 或 REST API 来启动此转换。 请参阅[管理单一数据库](sql-database-single-database-scale.md)和[管理弹性池](sql-database-elastic-pool.md)。

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>可以将转换数据库基于 vCore 的购买模型中的服务层从到基于 DTU 的购买模型中的服务层？

是的您可以轻松地将数据库转换任何受支持的性能目标通过使用 Azure 门户、 PowerShell、 Azure CLI、 T-SQL、 或 REST API。 请参阅[管理单一数据库](sql-database-single-database-scale.md)和[管理弹性池](sql-database-elastic-pool.md)。

## <a name="next-steps"></a>后续步骤

- 有关基于 vCore 的购买模型的详细信息，请参阅[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)。
- 有关基于 DTU 的购买模型的详细信息，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)。
