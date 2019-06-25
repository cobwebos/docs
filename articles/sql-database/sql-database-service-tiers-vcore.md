---
title: Azure SQL 数据库服务 - vCore | Microsoft 文档
description: 基于 vCore 的购买模型允许你独立缩放计算和存储资源、 匹配本地性能和优化价格。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693334"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>在 vCore 的服务层之间进行选择，并将从服务层的 DTU 迁移

虚拟核心 (vCore)-根据购买模型允许你独立缩放计算和存储资源、 匹配本地性能和优化价格。 它还允许您选择的新一代硬件：

- **Gen4**:最多 24 逻辑 Cpu 基于 Intel E5 2673 v3 (Haswell) 2.4-GHz 处理器，vCore = 1 PP （物理核心），每个核心，7 GB 的附加 SSD
- **Gen5**:高达 80 逻辑 Cpu 基于 Intel E5 2673 v4 (Broadwell) 2.3 GHz 处理器、 vCore = 1 LP （超线程），每个核心，快速 eNVM 5.1 GB

第 4 代为每个 vCore 提供的内存要大得多。 但是，第 5 代硬件允许以高得多的力度纵向扩展计算资源。

> [!NOTE]
> 有关基于 DTU 的服务层的信息，请参阅[服务层对于基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)。 有关基于 DTU 的服务层和基于 vCore 的购买模型之间的差异的信息，请参阅[购买模型的 Azure SQL 数据库](sql-database-purchase-models.md)。

## <a name="service-tier-characteristics"></a>服务层特征

基于 vCore 的购买模型提供了三个服务层： 常规用途、 超大规模和业务关键。 这些服务层根据一系列计算大小、 高可用性设计、 故障隔离方法、 类型和大小的存储和 I/O 范围进行区分。

必须单独配置所需的存储和备份保持期。 若要设置备份保持期，打开 Azure 门户，请转到服务器 （不是数据库），然后转到**管理备份** > **配置策略** >  **点在时间还原配置** > **过去 7-35 天**。

下表说明了三个层之间的差异：

||**常规用途**|**业务关键**|**超大规模**|
|---|---|---|---|
|最适用于|大多数业务工作负荷。 产品/服务面向预算的、 平衡，且可缩放计算和存储选项。|具有较高 I/O 要求业务应用程序。 通过使用多个独立的副本提供最高故障恢复能力。|使用高度可缩放存储和读取缩放要求的大多数业务工作负荷。|
|计算|**预配计算**：<br/>Gen4：1 到 24 个 Vcore<br/>Gen5：2 到 80 个 Vcore<br/>**无服务器计算**:<br/>Gen5：0.5 的 4 个 Vcore|**预配计算**：<br/>Gen4：1 到 24 个 Vcore<br/>Gen5：2 到 80 个 Vcore|**预配计算**：<br/>Gen4：1 到 24 个 Vcore<br/>Gen5：2 到 80 个 Vcore|
|内存|**预配计算**：<br/>Gen4：每个 vCore 7 GB<br/>Gen5：每个 vCore 5.1 GB<br/>**无服务器计算**:<br/>Gen5：每个 vCore 3 GB|**预配计算**：<br/>Gen4：每个 vCore 7 GB<br/>Gen5：每个 vCore 5.1 GB |**预配计算**：<br/>Gen4：每个 vCore 7 GB<br/>Gen5：每个 vCore 5.1 GB|
|存储|使用远程存储。<br/>**单一数据库预配计算**：<br/>5 GB – 4 TB<br/>**单一数据库无服务器计算**：<br/>5 GB - 1 TB<br/>**托管的实例**:32 GB - 8 TB |使用本地 SSD 存储。<br/>**单一数据库预配计算**：<br/>5 GB – 4 TB<br/>**托管的实例**:<br/>32 GB - 4 TB |根据需要存储的灵活速度自动增长。 支持最多 100 TB 的存储空间。 将本地 SSD 存储用于本地缓冲池缓存和本地数据存储。 使用 Azure 的远程存储作为最后一个长期数据存储区。 |
|输入/输出吞吐量 （近似）|**单个数据库**:每个 vCore 与 7000 最大 IOPS 的 500 IOPS。<br/>**托管的实例**:取决于[文件的大小](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)。|每个 vCore 提供 5000 IOPS，最大 200,000 IOPS|超大规模是使用缓存在多个级别的多层体系结构。 有效的 IOPs 将取决于工作负荷。|
|可用性|1 个副本，没有读取缩放副本|3 个副本，1 个[读取缩放副本](sql-database-read-scale-out.md)，<br/>区域冗余高可用性 (HA)|1 个读写副本，以及 0 到 4[读取缩放副本](sql-database-read-scale-out.md)|
|备份|[读取访问异地冗余存储 (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md)、 7 到 35 天 （默认情况下 7 天）|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)，7-35 天（默认为 7 天）|Azure 远程存储中的基于快照的备份。 还原使用这些快照进行快速恢复。 备份的瞬间完成，并且不会影响计算的 I/O 性能。 还原的速度快，不是大小的数据操作 （采用分钟而不是小时或天）。|
|内存中|不支持|支持|不支持|
|||

> [!NOTE]
> 在配合 Azure 免费帐户中的基本服务层，可以获取免费的 Azure SQL 数据库。 有关详细信息，请参阅[使用 Azure 免费帐户创建托管的云数据库](https://azure.microsoft.com/free/services/sql-database/)。

- 有关 vCore 的资源限制的详细信息，请参阅[在单个数据库的 vCore 资源限制](sql-database-vcore-resource-limits-single-databases.md)并[vCore 的托管实例中的资源限制](sql-database-managed-instance.md#vcore-based-purchasing-model)。
- 有关常规用途层和业务关键服务层的详细信息，请参阅[常规用途和业务关键服务层](sql-database-service-tiers-general-purpose-business-critical.md)。
- 有关基于 vCore 的购买模型中的超大规模服务层的详细信息，请参阅[超大规模服务层](sql-database-service-tier-hyperscale.md)。  

## <a name="azure-hybrid-benefit"></a>Azure 混合权益

在预配的计算层的基于 vCore 的购买模型中，可以通过使用交换现有许可证的 SQL 数据库上提供了折扣费率[面向 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。 此 Azure 权益，可通过使用具有软件保障的本地 SQL Server 许可证在 Azure SQL 数据库上节省多达 30%。

![定价](./media/sql-database-service-tiers/pricing.png)

使用 Azure 混合权益，您可以选择只需支付底层 Azure 基础结构用于 SQL 数据库引擎本身 （基础计算定价），使用你现有的 SQL Server 许可证或支付底层基础结构和 SQL Server许可证 （许可证的定价）。

可以选择，也可以更改您的许可模型，通过使用 Azure 门户或通过使用以下 Api 之一：

- 若要设置或使用 PowerShell 更新许可证类型：

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- 若要设置或通过使用 Azure CLI 更新许可证类型：

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- 若要设置或通过使用 REST API 更新许可证类型：

  - [数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [数据库 - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [托管实例 - 创建或更新](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [托管实例 - 更新](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>将基于 DTU 的模型迁移到基于 vCore 的模型

### <a name="migrate-a-database"></a>迁移数据库

将数据库从基于 DTU 的购买模型迁移到基于 vCore 的购买模型是类似于升级或降级之间基于 DTU 的购买模型中的标准和高级服务层。

### <a name="migrate-databases-with-geo-replication-links"></a>迁移具有异地复制链接的数据库

从基于 DTU 的模型迁移到基于 vCore 的购买模型是类似于升级或降级标准和高级服务层中的数据库之间的异地复制关系。 在迁移期间，无需停止异地复制，但必须遵循这些排序规则：

- 升级时，必须先升级辅助数据库，再升级主数据库。
- 降级时，必须反转顺序：先降级主数据库，再降级辅助数据库。

在两个弹性池之间使用异地复制，我们建议将一个池指定为主要和辅助数据库作为其他。 在这种情况下，迁移弹性池时应使用相同的序列化指南。 但是，如果您有包含主要和辅助数据库的弹性池，且使用率较高的池视为主并相应地遵循顺序规则。  

下表提供了对特定迁移方案的指导：

|当前服务层级|目标服务层级|迁移类型|用户操作|
|---|---|---|---|
|标准|常规用途|横向|可按任意顺序迁移，但需确保 vCore 大小适当*|
|高级|业务关键|横向|可按任意顺序迁移，但需确保 vCore 大小适当*|
|标准|业务关键|升级|必须先迁移辅助数据库|
|业务关键|标准|降级|必须先迁移主数据库|
|高级|常规用途|降级|必须先迁移主数据库|
|常规用途|高级|升级|必须先迁移辅助数据库|
|业务关键|常规用途|降级|必须先迁移主数据库|
|常规用途|业务关键|升级|必须先迁移辅助数据库|
||||

\* 在标准层中的每个 100 个 Dtu 需要至少 1 个 vCore，而在高级层中的每 125 个 Dtu 需要至少 1 个 vCore。

### <a name="migrate-failover-groups"></a>迁移故障转移组

迁移包含多个数据库的故障转移组需要单独迁移主数据库和辅助数据库。 在此过程中，请遵循相同的注意事项和顺序规则。 将数据库转换到基于 vCore 的购买模型后，故障转移组将在日后失效使用相同的策略设置。

### <a name="create-a-geo-replication-secondary-database"></a>创建异地复制辅助数据库

只能通过使用相同的服务层，因为用于主数据库，可以创建异地复制辅助数据库 （异地辅助）。 对于具有较高的日志生成速率的数据库，我们建议使用相同的计算大小的主数据库创建异地辅助数据库。

如果要在弹性池中为单个主数据库创建异地辅助数据库，请确保`maxVCore`设置池与主数据库的计算大小匹配。 如果要在另一个弹性池中创建主异地辅助数据库，我们建议池具有相同`maxVCore`设置。

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>使用数据库复制来将基于 DTU 的数据库转换到基于 vCore 的数据库

可将采用基于 DTU 的计算大小的任何数据库复制到采用基于 vCore 的计算大小的数据库，且无需遵守上述限制或特殊的顺序，前提是目标计算大小支持源数据库的最大数据库大小。 数据库副本创建的复制操作在启动时会数据快照，并不会同步在源和目标之间的数据。

## <a name="next-steps"></a>后续步骤

- 对于特定的计算大小和存储大小选项可用于单一数据库，请参阅[单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 对于特定的计算大小和存储大小选项适用于弹性池，请参阅[弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)。
