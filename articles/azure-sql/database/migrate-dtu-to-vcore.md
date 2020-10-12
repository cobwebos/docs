---
title: 从 DTU 迁移到 vCore
description: 将 Azure SQL 数据库中的数据库从 DTU 模型迁移到 vCore 模型。 迁移到 vCore 类似于在标准层和高级层之间进行升级或降级。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 05/28/2020
ms.openlocfilehash: b8c7671e655594456621e4489cb06191d820b134
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333148"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>将 Azure SQL 数据库从基于 DTU 的模型迁移到基于 vCore 的模型
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍如何将 Azure SQL 数据库中的数据库从[基于 DTU 的购买模型](service-tiers-dtu.md)迁移到[基于 vCore 的购买模型](service-tiers-vcore.md)。 

## <a name="migrate-a-database"></a>迁移数据库

将数据库从基于 DTU 的购买模型迁移到基于 vCore 的购买模型类似于在“基本”、“标准”和“高级”服务层级中的服务目标之间进行缩放，并且在迁移过程结束时，[持续时间](single-database-scale.md#latency)相似且[停机时间极短](scale-resources.md)。 迁移到基于 vCore 的购买模型的数据库可以在任何时间以相同方式迁移回基于 DTU 的购买模型，但迁移到[超大规模](service-tier-hyperscale.md)服务层级的数据库除外。 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>选择 vCore 服务层级和服务目标

对于大多数从 DTU 到 vCore 的迁移方案，“基本”和“标准”服务层级中的数据库和弹性池将映射到[常规用途](service-tier-general-purpose.md)服务层级。 “高级”服务层级中的数据库和弹性池将映射到[业务关键](service-tier-business-critical.md)服务层级。 根据应用程序方案和要求，[超大规模](service-tier-hyperscale.md)服务层级通常可用作所有 DTU 服务层级中单一数据库的迁移目标。

要为 vCore 模型中已迁移的数据库选择服务目标或计算大小，可以使用一种简单但近似的经验法则：“基本”或“标准”层级中的每 100 个 DTU 需要至少 1 个 vCore，“高级”层级中的每 125 个 DTU 需要至少 1 个 vCore 。 

> [!TIP]
> 此规则是一个近似规则，因为它不考虑用于 DTU 数据库或弹性池的硬件代系。 

在 DTU 模型中，任何可用的[硬件代系](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model)都可用于数据库或弹性池。 此外，只能通过选择更高或更低的 DTU 或 eDTU 值，间接控制 vCore（逻辑 CPU）数目。 

使用 vCore 模型时，客户必须明确选择硬件代系和 vCore（逻辑 CPU）数目。 DTU 模型不提供这些选项，但通过动态管理视图公开了硬件代系以及用于每个数据库和弹性池的逻辑 CPU 数目。 这样就可以更精确地确定匹配的 vCore 服务目标。 

以下方法使用此信息来确定具有相似资源分配的 vCore 服务目标，以在迁移到 vCore 模型后获得相似的性能级别。

### <a name="dtu-to-vcore-mapping"></a>DTU 到 vCore 的映射

在要迁移的 DTU 数据库的上下文中执行以下 T-SQL 查询时，系统将返回 vCore 模型的每个硬件代系中匹配的 vCore 数目（可能是小数）。 通过将此数字舍入为与 vCore 模型内每个硬件代系中[数据库](resource-limits-vcore-single-databases.md)和[弹性池](resource-limits-vcore-elastic-pools.md)可用的 vCore 数目最接近的数，客户可以选择与其 DTU 数据库或弹性池最匹配的 vCore 服务目标。 

[示例](#dtu-to-vcore-migration-examples)部分中介绍了使用此方法的示例迁移方案。

请在要迁移的数据库的上下文中执行该查询，而不是在 `master` 数据库中执行。 迁移弹性池时，请在池中任何数据库的上下文中执行该查询。

```SQL
WITH dtu_vcore_map AS
(
SELECT TOP (1) rg.slo_name,
               CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
                    WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
                    WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
               END AS dtu_hardware_gen,
               s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
               CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>其他因素

除了 vCore（逻辑 CPU）数目和硬件代系之外，还有一些其他因素也可能影响 vCore 服务目标的选择：

- 映射 T-SQL 查询根据 CPU 容量来匹配 DTU 和 vCore 服务目标，因此对于 CPU 密集型工作负载，结果更准确。
- 硬件代系和 vCore 数目相同时，vCore 数据库的 IOPS 和事务日志吞吐量资源限制通常高于 DTU 数据库。 对于 IO 密集型工作负载，在 vCore 模型中使用较少数量的 vCore 就有可能达到相同的性能级别。 DTU 和 vCore 数据库的资源限制（以绝对值表示）在 [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 视图中公开。 在要迁移的 DTU 数据库和使用近似匹配服务目标的 vCore 数据库之间比较这些值有助于更精确地选择 vCore 服务目标。
- 映射查询还返回要迁移的 DTU 数据库或弹性池以及 vCore 模型中每个硬件代系的每个内核的内存量。 对于需要大量内存数据缓存来实现足够性能的工作负载或需要大量内存授予来进行查询处理的工作负载，必须确保在迁移到 vCore 之后具有相似或更高的总内存。 对于此类工作负载，可能有必要增加 vCore 的数量以获得足够的总内存，具体取决于实际性能。
- 选择 vCore 服务目标时，应考虑 DTU 数据库的[历史资源使用率](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)。 若 DTU 数据库的 CPU 资源一直未得到充分利用，则其需要的 vCore 数目可能比映射查询所返回的数目少。 相反，对于因持续的 CPU 高利用率而导致工作负载性能不足的 DTU 数据库，其需要的 vCore 数目可能比映射查询所返回的数量多。
- 如果要迁移具有间歇性或不可预测的使用模式的数据库，请考虑使用[无服务器](serverless-tier-overview.md)计算层。  请注意，无服务器中并发辅助角色（请求）的最大数目是所配置最大 vCore 的预配计算限制的 75%。  此外，无服务器中可用的最大内存为配置的最大 vCore 数目乘以 3 GB。例如，当配置的最大 vCore 数目为 40 时，最大内存为 120 GB。   
- 在 vCore 模型中，支持的最大数据库大小可能因硬件代系而异。 对于大型数据库，请检查 vCore 模型中支持的[单一数据库](resource-limits-vcore-single-databases.md)和[弹性池](resource-limits-vcore-elastic-pools.md)最大大小。
- 对于弹性池，[DTU](resource-limits-dtu-elastic-pools.md) 和 [vCore](resource-limits-vcore-elastic-pools.md) 模型中每个池支持的数据库最大数目有所不同。 迁移包含多个数据库的弹性池时，应该考虑这一点。
- 某些硬件代系可能并非在每个区域都可用。 请在[硬件代系](service-tiers-vcore.md#hardware-generations)下检查可用性。

> [!IMPORTANT]
> 上述从 DTU 到 vCore 的大小调整指南旨在帮助初步估计目标数据库服务目标。
>
> 目标数据库的最佳配置取决于工作负荷。 因此，要在迁移后达到最佳性价比，可能需要利用 vCore 模型的灵活性来调整 vCore 数目、[硬件代系](service-tiers-vcore.md#hardware-generations)、[服务层级](service-tiers-vcore.md#service-tiers)和[计算层](service-tiers-vcore.md#compute-tiers)，以及优化其他数据库配置参数，例如[最大并行度](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)。
> 

### <a name="dtu-to-vcore-migration-examples"></a>DTU 到 vCore 的迁移示例

> [!NOTE]
> 以下示例中的值仅用于说明目的。 所述场景中返回的实际值可能不同。
> 

**迁移标准 S9 数据库**

映射查询返回以下结果（为简洁起见，某些列未显示）：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24.00|Gen5|5.40|16.800|7|24.000|5.05|

我们看到 DTU 数据库具有 24 个逻辑 CPU (vCore)，每个 vCore 的内存为 5.4 GB，并使用 Gen5 硬件。 与之直接匹配是 Gen5 硬件上的常规用途 24 vCore 数据库，即“GP_Gen5_24”vCore 服务目标。

**迁移标准 S0 数据库**

映射查询返回以下结果（为简洁起见，某些列未显示）：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0.25|Gen4|0.42|0.250|7|0.425|5.05|

我们看到 DTU 数据库具有相当于 0.25 个逻辑 CPU (vCore)，每个 vCore 的内存为 0.42 GB，并使用 Gen4 硬件。 Gen4 和 Gen5 硬件代系中的最小 vCore 服务目标为“GP_Gen4_1”和“GP_Gen5_2”，其提供的计算资源超出了标准 S0 数据库的需求，因此无法进行直接匹配 。 由于 Gen4 硬件即将[停用](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)，因此首选“GP_Gen5_2”。 此外，如果工作负载很适合[无服务器](serverless-tier-overview.md)计算层，那么“GP_S_Gen5_1”更匹配。

**迁移高级 P15 数据库**

映射查询返回以下结果（为简洁起见，某些列未显示）：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42.00|Gen5|4.86|29.400|7|42.000|5.05|

我们看到 DTU 数据库具有 42 个逻辑 CPU (vCore)，每个 vCore 的内存为 4.86 GB，并使用 Gen5 硬件。 尽管没有 vCore 服务目标具有 42 个内核，但“BC_Gen5_40”服务目标的 CPU 和内存容量都非常接近，因此是一个非常有效的匹配项。

**迁移基本 200 eDTU 弹性池**

映射查询返回以下结果（为简洁起见，某些列未显示）：

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4.00|Gen5|5.40|2.800|7|4.000|5.05|

我们看到 DTU 弹性池具有 4 个逻辑 CPU (vCore)，每个 vCore 的内存为 5.4 GB，并使用 Gen5 硬件。 vCore 模型中的直接匹配项是“GP_Gen5_4”弹性池。 但是，此服务目标的每个池支持最多 200 个数据库，而基本 200 eDTU 弹性池支持最多 500 个数据库。 如果要迁移的弹性池具有超过 200 个数据库，则匹配的 vCore 服务目标必须为“GP_Gen5_6”，它支持最多 500 个数据库。

## <a name="migrate-geo-replicated-databases"></a>迁移异地复制的数据库

从基于 DTU 的模型迁移到基于 vCore 的购买模型类似于在标准和高级服务层级中的数据库之间升级或降级异地复制关系。 在迁移过程中无需停止异地复制，但必须遵循以下顺序规则：

- 升级时，必须先升级辅助数据库，再升级主数据库。
- 降级时，必须反转顺序：先降级主数据库，再降级辅助数据库。

在两个弹性池之间使用异地复制时，建议将一个池指定为主池，另一个池指定为辅助池。 在这种情况下，迁移弹性池时应遵循相同的顺序指导。 但是，如果弹性池同时包含主数据库和辅助数据库，请将利用率较高的池视为主池，并相应地遵循顺序规则。  

下表提供具体迁移场景的指导：

|当前服务层级|目标服务层级|迁移类型|用户操作|
|---|---|---|---|
|标准|常规用途|横向|可按任意顺序迁移，但需确保 vCore 大小适当，如上文所述|
|高级|业务关键|横向|可按任意顺序迁移，但需确保 vCore 大小适当，如上文所述|
|标准|业务关键|升级|必须先迁移辅助数据库|
|业务关键|标准|降级|必须先迁移主数据库|
|高级|常规用途|降级|必须先迁移主数据库|
|常规用途|高级|升级|必须先迁移辅助数据库|
|业务关键|常规用途|降级|必须先迁移主数据库|
|常规用途|业务关键|升级|必须先迁移辅助数据库|
||||

## <a name="migrate-failover-groups"></a>迁移故障转移组

迁移包含多个数据库的故障转移组需要单独迁移主数据库和辅助数据库。 在此过程中，请遵循相同的注意事项和顺序规则。 将数据库转换到基于 vCore 的购买模型后，故障转移组将保持有效并使用相同的策略设置。

### <a name="create-a-geo-replication-secondary-database"></a>创建异地复制辅助数据库

只能使用与主数据库所用的相同服务层级来创建异地复制辅助数据库。 对于日志生成速率较高的数据库，我们建议使用与主数据库相同的计算大小创建异地辅助数据库。

如果在弹性池中为单个主数据库创建异地辅助数据库，请确保对该池使用的 `maxVCore` 设置与主数据库计算大小相匹配。 如果为另一个弹性池中的主数据库创建异地辅助数据库，我们建议对该池使用相同的 `maxVCore` 设置。

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>使用数据库副本从 DTU 迁移到 vCore

可将采用基于 DTU 的计算大小的任何数据库复制到采用基于 vCore 的计算大小的数据库，且无需遵守上述限制或特殊的顺序，前提是目标计算大小支持源数据库的最大数据库大小。 数据库复制会在复制操作启动时创建数据快照，且不会在源数据库与目标数据库之间同步数据。

## <a name="next-steps"></a>后续步骤

- 有关适用于单一数据库的特定计算大小和存储大小选项，请参阅[适用于单一数据库的 SQL 数据库基于 vCore 的资源限制](resource-limits-vcore-single-databases.md)。
- 有关适用于弹性池的特定计算大小和存储大小选项，请参阅[适用于弹性池的 SQL 数据库基于 vCore 的资源限制](resource-limits-vcore-elastic-pools.md)。
