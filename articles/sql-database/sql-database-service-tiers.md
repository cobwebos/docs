---
title: Azure SQL 数据库购买模型 | Microsoft Docs
description: 了解 Azure SQL 数据库的购买模型。
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: f40fe3da6874d8656c9c0a0ddce9fed602cb25f9
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091909"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Azure SQL 数据库购买模型和资源 

[Azure SQL 数据库](sql-database-technical-overview.md)中的逻辑服务器为计算、存储和 IO 资源提供两种购买模型：基于 DTU 的购买模型和基于 vCore 的购买模型。 

> [!NOTE]
> Azure SQL 数据库中的[托管实例](sql-database-managed-instance.md)仅提供基于 vCore 的购买模型。

以下表格和图表对这两种购买模型做了对比。

> [!IMPORTANT]
> 若要了解基于 vCore 的购买模型，请参阅[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)

|**购买模型**|**说明**|**最适用于**|
|---|---|---|
|基于 DTU 的模型|此模型基于计算、存储和 IO 资源的捆绑度量。 单一数据库的性能级别以数据库事务单位 (DTU) 表示，弹性池则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的详细信息，请参阅[什么是 DTU 和 eDTU？](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)|最适合希望获得简单预配置资源选项的客户。| 
|基于 vCore 的模型|此模型允许单独缩放计算和存储资源。 此外，它还允许使用面向 SQL Server 的 Azure 混合权益来节省成本。|最适合注重灵活性、控制度和透明度的客户。|
||||  

![定价模型](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型 

虚拟核心表示逻辑 CPU，提供不同代的硬件供客户选择。 基于 vCore 的购买模型提供单项资源消耗的灵活性、控制度和透明度，并提供简单明了的方法将本地工作负荷要求转换到云。 此模型允许根据工作负荷需求来缩放计算、内存和存储资源。 在基于 vCore 的购买模型中，客户可为[单一数据库](sql-database-single-database-scale.md)和[弹性池](sql-database-elastic-pool.md)选择“常规用途”或“业务关键”服务层。 

使用基于 vCore 的购买模型，可以单独缩放计算和存储资源，匹配本地性能，以及优化价格。 如果数据库或弹性池消耗的 DTU 超过 300 个，则转换到 vCore 可以降低成本。 可以使用所选的 API 或 Azure 门户进行转换，无需停机。 但是，不一定非要转换。 如果 DTU 购买模型满足性能和业务需求，应继续使用它。 如果决定从 DTU 模型转换到 vCore 模型，应该根据以下经验法则选择性能级别：标准层中的每 100 个 DTU 至少需要常规用途层中的 1 个 vCore，高级层中的每 125 个 DTU 至少需要业务关键层中的 1 个 vCore。

在基于 vCore 的购买模型中，客户的费用包括：
- 计算（服务层 + vCores 数目 + 硬件代次）*
- 数据和日志存储的类型与数量 
- IO 数目**
- 备份存储 (RA-GRS)** 

\* 在初始公共预览版中，第 4 代逻辑 CPU 基于 Intel E5-2673 v3 (Haswell) 2.4-GHz 处理器。

\*\* 在预览期间，备份和 IO 免费 7 天。

> [!IMPORTANT]
> 计算、IO、数据和日志存储按数据库或弹性池收费。 备份存储按每个数据库收费。 有关托管实例费用的详细信息，请参阅 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)。
> **区域限制：** 基于 vCore 的购买模型在以下区域尚不可用：西欧、法国中部、英国南部、英国西部和澳大利亚东南部。

## <a name="dtu-based-purchasing-model"></a>基于 DTU 的购买模型

数据库吞吐量单位 (DTU) 表示 CPU、内存、读取和写入的混合度量。 基于 DTU 的购买模型提供一组预配置的计算资源套件和随附的存储，以促成不同级别的应用程序性能。 偏爱预配置套件简易性和每月定价付款的客户，可能会发现基于 DTU 的模型更适合解决其需求。 在基于 DTU 的购买模型中，客户可为[单一数据库](sql-database-single-database-scale.md)和[弹性池](sql-database-elastic-pool.md)选择“基本”、“标准”或“高级”服务层。 

### <a name="what-are-database-transaction-units-dtus"></a>数据库事务单位 (DTU) 的定义是？
对于[服务层](sql-database-single-database-scale.md)内特定性能级别的单个 Azure SQL 数据库，Microsoft 保证该数据库（独立于 Azure 云中的任何其他数据库）可获得一定级别的资源，从而提供可预测的性能级别。 此资源量是以若干数据库事务单位或 DTU 计算的，是计算资源、存储资源和 IO 资源的捆绑度量值。 这些资源之间的比例最初由 [OLTP 基准工作负荷](sql-database-benchmark-overview.md)确定，该工作负荷是一种典型的真实 OLTP 工作负荷。 工作负荷超过任何以上资源量时，吞吐量将受到限制，从而导致性能下降和超时。 工作负荷使用的资源不会影响 Azure 云中其他 SQL 数据库可用的资源，而其他工作负荷使用的资源也不会影响用户自己的 SQL 数据库可用的资源。

![边界框](./media/sql-database-what-is-a-dtu/bounding-box.png)

要了解 Azure SQL 数据库在不同性能级别和服务层之间的资源相对数量，DTU 将最为有用。 例如，提高数据库的性能级别可使 DTU 加倍，这等同于使该数据库可用的资源集增加一倍。 例如，具有 1750 个 DTU 的高级 P11 数据库提供的 DTU 计算能力是具有 5 个 DTU 的基本数据库的 350 倍。  

若要更深入了解工作负荷的资源 (DTU) 消耗，请使用 [Azure SQL 数据库 Query Performance Insight](sql-database-query-performance.md)：

- 按 CPU/持续时间/执行计数确定热门查询，可以对这些查询进行调整来提高性能。 例如，IO 密集型查询可能会受益于使用[内存中优化技术](sql-database-in-memory.md)，以便在某个特定服务层和性能级别更好地利用可用内存。
- 深入了解查询详情，查看其文本和资源使用历史记录。
- 访问性能优化建议可显示 [SQL 数据库顾问](sql-database-advisor.md)执行的操作。

可以随时更改 [DTU 服务层](sql-database-service-tiers-dtu.md)，将应用程序故障时间降至最低（通常在平均 4 秒以下）。 许多业务和应用只要能够创建数据库并按需调高或调低性能即可，尤其是当使用模式相对容易预测时。 但如果有无法预测的使用模式，则管理成本和业务模式就会变得相当困难。 对于这种情况，可以使用一个具有一定数量 eDTU 的弹性池，这些 eDTU 在池中的多个数据库之间共享。

![SQL 数据库简介：按层和级别统计的单一数据库 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>弹性数据库事务单位 (eDTU) 的定义是？
可将数据库放在 SQL 数据库服务器上的[弹性池](sql-database-elastic-pool.md)中，该服务器在这些数据库之间共享资源池，而不必为 SQL 数据库提供一组始终可用的专用资源 (DTU)，因为有时可能并不需要。 弹性池中的共享资源用弹性数据库事务单位或 eDTU 度量。 弹性池提供一种简单的低成本高效益的解决方案，用于管理使用模式变化很大且不可预测的多个数据库的性能目标。 弹性池可保证不出现一个数据库使用池中所有资源的情况，同时确保池中的每个数据库始终可以使用最少量的必需资源。 

![SQL 数据库简介：按层和级别统计的 eDTU](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

为池提供的 eDTU 的数量和价格是固定的。 在弹性池中，单个数据库能够在配置的边界范围内灵活自动缩放。 负载较重的数据库消耗较多的 eDTU 来满足需求。 负载较轻的数据库消耗较少的 eDTU。 无负载的数据库不消耗任何 eDTU。 为整个池而不是每个数据库预配资源可以简化管理任务，使池的预算可预测。

可以对现有池增加额外的 eDTU，这不会造成数据库关闭，也不会影响池中的数据库。 同样，随时可以从现有池中删除不再需要的额外 eDTU。 可以在池中添加或删除数据库，或者限制重负载数据库的 eDTU 用量，以便为其他数据库预留 eDTU。 如果可以预见到某个数据库的资源利用率不高，可将其移出池，并使用所需的可预测资源量将它配置为单一数据库。

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>如何确定工作负荷所需的 DTU 数？
如果打算将现有的本地或 SQL Server 虚拟机工作负荷迁移到 Azure SQL 数据库，可以使用 [DTU 计算器](http://dtucalculator.azurewebsites.net/) 来估计所需的 DTU 数。 对于现有的 Azure SQL 数据库工作负荷，可以使用 [SQL 数据库 Query Performance Insight](sql-database-query-performance.md) 来了解数据库资源使用量 (DTU)，更深入地了解如何优化工作负荷。 也可以使用 [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV 查看最近一小时的资源消耗。 目录视图 [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) 也可显示最近 14 天的资源消耗，不过，五分钟平均值的准确性较低。

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>如何知道是否可以从资源的弹性池受益？
池适合具有特定使用模式的大量数据库。 对于给定的数据库，此模式的特征是低平均使用量与相对不频繁的使用高峰。 SQL数据库自动评估现有 SQL 数据库服务器中数据库的历史资源使用率，并在 Azure 门户中推荐适当的池配置。 有关详细信息，请参阅[何时使用弹性池？](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>达到最大 DTU 时会发生什么情况？
系统会校准并控制性能级别，以便在选定服务层/性能级别所允许的最大范围内提供所需的资源来运行数据库工作负荷。 如果工作负荷达到了 CPU/数据 IO/日志 IO 限制中的其中一个限制，你将继续接收资源直到达到允许的最大级别，但是，你可能会发现查询延迟不断增加。 这些限制不会导致任何错误，而只会减慢工作负荷，直到严重变慢，以致于查询开始超时。如果达到了并发用户会话/请求（工作线程）的最大允许数目，你将看到明确的错误。 有关 CPU、内存、数据 IO 或事务日志 IO 以外的资源限制的信息，请参阅 [Azure SQL 数据库资源限制]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached)。

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>将基准检验结果与实际数据库性能进行关联
请务必了解，所有基准检验只具有代表性和指示性。 使用基准检验应用程序完成的事务率不会与使用其他应用程序可能完成的事务率相同。 基准检验包含不同事务类型的集合，这些事务针对包含一系列表和数据类型的架构运行。 虽然基准检验执行普遍适用于所有 OLTP 工作负荷的相同基本操作，但它并不代表任何特定类别的数据库或应用程序。 基准检验的目标是为在上调或下调性能级别时可预期的数据库相对性能提供合理的指导。 实际上，数据库具有不同的大小和复杂度，会遇到工作负荷的不同组合，并且以不同方式进行响应。 例如，IO 密集型应用程序可能会更快地达到 IO 阈值，或者 CPU 密集型应用程序可能会更快地达到 CPU 限制。 不能保证任何特定数据库在不断增加的负载下会以与基准检验相同的方式扩展。

基准检验及其方法会在下面更详细地说明。

### <a name="benchmark-summary"></a>基准检验摘要
ASDB 将度量联机事务处理 (OLTP) 工作负荷中最常发生的基本数据库操作组合的性能。 尽管在设计基准检验时考虑到了云计算，但已将数据库架构、数据填充和事务设计为广泛代表 OLTP 工作负荷中最常用的基本元素。

### <a name="schema"></a>架构
架构设计为具有足够的多样性和复杂度以支持各种操作。 基准检验针对包含六个表的数据库运行。 这些表分为三个类别：固定大小、缩放和增长。 有两个固定大小表、三个缩放性表和一个增长性表。 固定大小表的行数不变。 缩放性表具有一个与数据库性能成正比的基数，但在基准检验期间不会更改。 增长性表的大小调整在初始加载时与缩放性表类似，但随后在运行基准检验的过程中随着插入和删除行基数会更改。

架构包含数据类型（包括整数、数字、字符和日期/时间）的组合。 架构包含主键和辅助键，但不包含任何外键（即，表之间没有任何引用完整性约束）。

数据生成程序会生成初始数据库的数据。 使用不同策略生成整数和数字数据。 在某些情况下，值在某一范围内随机分布。 在其他情况下，会对一组值进行随机排列以确保维持特定分布。 文本字段从加权单词列表中生成以产生具有真实感的数据。

数据库根据“缩放比例”调整大小。 缩放比例（简称为 SF）确定缩放性表和增长性表的基数。 如下面的“用户和步调”部分中所述，数据库大小、用户数和最大性能全都相互成比例缩放。

### <a name="transactions"></a>事务
工作负荷由九种事务类型组成，如下表中所示。 每种事务旨在强调数据库引擎和系统硬件中的特定一组系统特征，与其他事务形成高反差。 此方法可更方便地评估不同组件对总体性能的影响。 例如，事务“Read Heavy”将从磁盘生成大量的读取操作。

| 事务类型 | Description |
| --- | --- |
| Read Lite |SELECT；在内存中；只读 |
| Read Medium |SELECT；大多数在内存中；只读 |
| Read Heavy |SELECT；大多数不在内存中；只读 |
| Update Lite |UPDATE；在内存中；读写 |
| Update Heavy |UPDATE；大多数在内存中；读写 |
| Insert Lite |INSERT；在内存中；读写 |
| Insert Heavy |INSERT；大多数不在内存中；读写 |
| 删除 |DELETE；在内存中和不在内存中的组合；读写 |
| CPU Heavy |SELECT；在内存中；相对较高的 CPU 负载；只读 |

### <a name="workload-mix"></a>工作负荷组合
从具有以下整体组合的加权分布中随机选择事务。 整体组合的读/写比率大约为 2:1。

| 事务类型 | 组合百分比 |
| --- | --- |
| Read Lite |35 |
| Read Medium |20 |
| Read Heavy |5 |
| Update Lite |20 |
| Update Heavy |3 |
| Insert Lite |3 |
| Insert Heavy |2 |
| 删除 |2 |
| CPU Heavy |10 |

### <a name="users-and-pacing"></a>用户和步调
基准检验工作负荷是由一个工具驱动的，该工具通过一组连接提交事务以模拟大量并发用户的行为。 虽然所有连接和事务都是由计算机生成的，但为简单起见我们将这些连接称为“用户”。 虽然每个用户都独立于所有其他用户进行操作，但所有用户都执行相同的步骤循环，如下所示：

1. 建立数据库连接。
2. 重复执行，直到收到退出通知：
   * 随机选择事务（从加权分布中）。
   * 执行所选的事务并测量响应时间。
   * 等待步调延迟。
3. 关闭数据库连接。
4. 退出。

随机选择了步调延迟（在步骤 2c 中），但却使用了平均值为 1.0 秒的分布。 因此，平均而言，每个用户每秒最多可以生成一个事务。

### <a name="scaling-rules"></a>缩放规则
用户数由数据库大小（以缩放比例单位数表示）确定。 每个五个缩放比例单位有一个用户。 由于步调延迟，一个用户平均每秒最多可以生成一个事务。

例如，缩放比例为 500 (SF = 500) 的数据库将具有 100 个用户，并且可以实现的最大速率为 100 TPS。 若要驱动更高的 TPS 速率，需要更多的用户和更大的数据库。

下表显示了为每个服务层和性能级别实际保留的用户数。

| 服务层（性能级别） | 用户 | 数据库大小 |
| --- | --- | --- |
| 基本 |5 |720 MB |
| 标准 (S0) |10 |1 GB |
| 标准 (S1) |20 |2.1 GB |
| 标准 (S2) |50 |7.1 GB |
| 高级 (P1) |100 |14 GB |
| 高级 (P2) |200 |28 GB |
| 高级 (P6) |800 |114 GB |

### <a name="measurement-duration"></a>度量持续时间
有效地运行基准检验需要稳定状态度量持续时间至少为 1 小时。

### <a name="metrics"></a>度量值
基准检验中的关键指标是吞吐量和响应时间。

* 吞吐量是基准检验中至关重要的性能度量值。 吞吐量以每时间单位的事务数的形式报告，计算所有事务类型。
* 响应时间是性能可预测性的度量值。 响应时间约束因服务等级而异，服务等级越高，响应时间要求越更严格，如下所示。

| 服务等级 | 吞吐量度量值 | 响应时间要求 |
| --- | --- | --- |
| 高级 |每秒事务数 |0.5 秒时达到 95% |
| 标准 |每分钟事务数 |1.0 秒时达到 90% |
| 基本 |每小时事务数 |2.0 秒时达到 80% |

## <a name="next-steps"></a>后续步骤

- 若要了解基于 vCore 的购买模型，请参阅[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)
- 有关基于 DTU 的购买模型，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)。
