---
title: 什么是 Azure SQL 数据库服务？
description: 获取 SQL 数据库简介：Microsoft 在云中的关系数据库管理系统 (RDBMS) 的技术详细信息与功能。
keywords: SQL 简介, 什么是 SQL 数据库
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 0fd3778d50216e337b872f0a27cb30b04a0219f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617214"
---
# <a name="what-is-azure-sql-database"></a>什么是 Azure SQL 数据库？
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL 数据库是一个完全托管的平台即服务 (PaaS) 数据库引擎，可在无需用户参与的情况下处理大多数数据库管理功能，如升级、修补、备份和监控。 Azure SQL 数据库始终运行在最新稳定版 SQL Server 数据库引擎和具有 99.99% 可用性的已修补 OS 之上。 Azure SQL 数据库中内置的 PaaS 功能可让你专注于对业务至关重要的特定于域的数据库管理和优化活动。

使用 Azure SQL 数据库，可以为 Azure 中的应用程序和解决方案创建高度可用且高性能的数据存储层。 SQL 数据库可成为各种新式云应用程序的正确选择，因为它可让你处理关系数据和[非关系结构](../multi-model-features.md)（例如图形、JSON、空间和 XML）。

Azure SQL 数据库是基于 [Microsoft SQL Server 数据库引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)的最新稳定版本。 你可以使用高级查询处理功能集，例如[高性能内存中技术](../in-memory-oltp-overview.md)和[智能查询处理](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json)。 事实上，SQL Server 的最新功能会先发布到 SQL 数据库，然后再发布到 SQL Server 本身。 无需投入任何修补或升级开销，即可获得 SQL Server 的最新功能，这些功能已在数百万个数据库中进行测试。 

使用 SQL 数据库可在以下两个不同的购买模型中轻松定义和缩放性能：[基于 vCore 的购买模型](service-tiers-vcore.md)和[基于 DTU 的购买模型](service-tiers-dtu.md)。 SQL 数据库是完全托管型服务，提供内置的可用性、备份和其他常见维护操作。 Microsoft 可以处理 SQL 和操作系统代码的所有修补与更新。 你无需管理底层基础结构。

如果你不熟悉 Azure SQL 数据库，请观看我们深度讲解的 [Azure SQL 视频系列](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner)中的“Azure SQL 数据库概述”视频：
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

## <a name="deployment-models"></a>部署模型

Azure SQL 数据库提供下述适用于数据库的部署选项：

- [单一数据库](single-database-overview.md)表示完全托管的独立数据库。 对于需要单个可靠数据源的新式云应用程序和微服务，可以使用此选项。 单一数据库类似于 [SQL Server 数据库引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)中[包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json)。
- [弹性池](elastic-pool-overview.md)是[单一数据库](single-database-overview.md)的集合，其中包含一组共享的资源（例如 CPU 或内存）。 可以将单一数据库移入或移出弹性池。

> [!IMPORTANT]
> 若要了解 SQL 数据库与 SQL Server 的功能差异，以及不同 Azure SQL 数据库选项的区别，请参阅 [SQL 数据库功能](features-comparison.md)。

SQL 数据库通过多个资源类型、服务层级和计算大小提供可预测的性能。 它提供无需停机的动态可伸缩性、内置智能优化、全局可伸缩性和可用性，以及高级安全选项。 凭借这些功能，你可将注意力集中在如何快速进行应用开发和加快推向市场方面，而无需管理虚拟机和基础结构。 SQL 数据库目前在世界各地有 38 个数据中心，因此你可以在附近的数据中心运行数据库。

## <a name="scalable-performance-and-pools"></a>可缩放的性能和池

可以定义分配的资源量。 
- 使用单一数据库，每个数据库相互隔离且可移植。 每个数据库都有自身有保证的计算、内存和存储资源量。 分配给数据库的资源量专门用于该数据库，而不会与 Azure 中的其他数据库共享。 可以动态[扩展和缩减单一数据库资源](single-database-scale.md)。 单一数据库提供不同的计算、内存和存储资源来满足不同的需求。 例如，你可以获得 1 到 80 个 vCore，或 32 GB 到 4 TB 的存储。 使用快速备份和还原功能，单一数据库的[超大规模服务层级](service-tier-hyperscale.md)可以扩展到 100 TB。
- 使用弹性池可以分配池中所有数据库共享的资源。 可以新建数据库，或者将现有单一数据库移到资源池中，以最大限度地使用资源和节省资金。 此选项还可让你动态[扩展和缩减弹性池资源](elastic-pool-scale.md)。

可以在小型单一数据库中构建第一个应用，每个月只需在常规用途服务层级中花费少量资金。 然后可以根据解决方案的需要，随时手动或以编程方式将服务层级更改为业务关键服务层级。 可在不给应用或客户造成停机的情况下调整性能。 动态可伸缩性使得数据库能够以透明方式对不断变化的资源需求做出响应。 只需在需要资源时为需要的资源付费。

动态可伸缩性不同于自动缩放。  自动缩放是指服务根据条件自动缩放，而动态可伸缩性允许在无停机时间的情况下进行手动缩放。 单一数据库选项支持手动动态可伸缩性，但不支持自动缩放。 若要获得更多自动体验，请考虑使用弹性池，它允许数据库根据各个数据库需求共享池中的资源。 另一个选项是使用脚本来帮助自动执行单一数据库的可伸缩性。 有关示例，请参阅[使用 PowerShell 监视和缩放单个数据库](scripts/monitor-and-scale-database-powershell.md)。

### <a name="purchasing-models"></a>购买模型

SQL 数据库提供以下购买模型：
- [基于 vCore 的购买模型](service-tiers-vcore.md)允许选择 vCore 数、内存容量，以及存储的容量和速度。 基于 vCore 的购买模型还允许使用[适用于 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来节省成本。 有关 Azure 混合权益的详细信息，请参阅本文稍后的“常见问题解答”部分。
- [基于 DTU 的购买模型](service-tiers-dtu.md)在以下三个服务层级中提供包括计算、内存和 I/O 资源在内的各种内容，以支持轻型到重型数据库工作负荷。 每个层级中的不同计算大小提供这些资源的不同组合，你可以向其添加更多的存储资源。
- [无服务器模型](serverless-tier-overview.md)可根据工作负荷需求自动缩放计算，并按每秒使用的计算量计费。 此外，当仅对存储计费时，无服务器计算层将在非活动期间自动暂停数据库；当活动返回时，它将自动恢复数据库。

### <a name="service-tiers"></a>服务层

Azure SQL 数据库提供了三个专为不同类型的应用程序设计的服务层级：
- [常规用途/标准](service-tier-general-purpose.md)服务层级设计用于常见工作负荷。 它提供以预算导向的、均衡的计算和存储选项。
- [业务关键型/高级](service-tier-business-critical.md)服务层级专为事务率较高、I/O 延迟最低的 OLTP 应用程序而设计。 它使用多个独立副本，提供最高级别的故障恢复能力。
- [超大规模](service-tier-hyperscale.md)服务层级专为超大型 OLTP 数据库设计，能够自动缩放存储和流畅地缩放计算。    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>弹性池可以最大化资源利用率

许多业务和应用程序只要能够创建单一数据库并按需调高或调低性能即可，尤其是当使用模式相对容易预测时。 无法预测的使用模式可能会使成本管理和业务模式变得困难。 [弹性池](elastic-pool-overview.md)旨在解决此问题。 可以向池而不是单个数据库分配性能资源。 只需为池的总体性能资源付费，而无需为单一数据库性能付费。

   ![显示基本、标准和高级版中的弹性池的图形](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

使用弹性池时，不需要在资源需求波动时担心如何上下调节数据库性能。 共用数据库可根据需要使用弹性池的性能资源。 共用数据库会使用该池，但不会超出其限制，因此即使单个数据库的使用情况仍不可预测，成本也仍是可预测的。

可以[向池添加和删除数据库](elastic-pool-overview.md)，将应用从少量数据库扩展到数千个，而一切费用不会超出所控制的预算范围。 还可以控制池中数据库可用的资源量上限与下限，确保池中不会有任何数据库使用所有的池资源，每个共用数据库的可用资源量都有最低保障。 若要详细了解使用弹性池的软件即服务 (SaaS) 应用程序的设计模式，请参阅[使用 SQL 数据库的多租户 SaaS 应用程序的设计模式](saas-tenancy-app-design-patterns.md)。

脚本可以帮助监视和缩放弹性池。 有关示例，请参阅[在 Azure SQL 数据库中使用 PowerShell 监视和缩放弹性池](scripts/monitor-and-scale-pool-powershell.md)。


### <a name="blend-single-databases-with-pooled-databases"></a>将单一数据库与共用数据库混合使用

可将单一数据库与弹性池混合使用，并更改单一数据库和弹性池的服务层级来适应自己的情况。 还可将其他 Azure 服务与 SQL 数据库搭配使用以满足独特的应用设计需求，提高成本和资源效益，发掘新的商机。

## <a name="extensive-monitoring-and-alerting-capabilities"></a>集中监视和警报功能

Azure SQL 数据库提供高级监视和故障排除功能，可帮助你更深入了解工作负荷的特征。 这些功能和工具包括：
 - SQL Server 数据库引擎的最新版本提供内置监视功能。 它们可让你找到实时性能见解。 
 - Azure 提供的 PaaS 监视功能，可让你监视大量的数据库实例并对其进行故障排除。

[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store) - 内置的 SQL Server 监视功能，它可以实时记录查询性能，并使你能够识别潜在的性能问题和排名靠前的资源使用者。 自动优化和建议提供有关性能退化以及存在缺失或重复索引的查询的建议。 使用 Azure SQL 数据库中的自动优化可以手动应用脚本来解决问题，或者让 Azure SQL 数据库应用修复措施。 SQL 数据库还可以测试并验证该修复措施是否起了作用，并根据结果保留或还原更改。 除了查询存储和自动优化功能外，还可以使用标准的 [DMV 和 XEvent](monitoring-with-dmvs.md) 来监视工作负荷性能。

Azure 提供[内置的性能监视](performance-guidance.md)和[警报](alerts-insights-configure-portal.md)工具，将它们与性能评分相结合，可以监视数千个数据库的状态。 使用这些工具，可以根据当前的或预计的性能需求，快速评估纵向扩展或纵向缩减产生的影响。 此外，SQL 数据库可[发出指标和资源日志](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)，以方便进行监视。 可配置 SQL 数据库，将资源使用情况、辅助角色和会话以及连接性存储到以下 Azure 资源之一：

- **Azure 存储**：以较低的价格存档大量的遥测数据。
- **Azure 事件中心**：用于将 SQL 数据库遥测与自定义监视解决方案或热门管道集成。
- **Azure Monitor 日志**：用于具有报告、警报和缓解功能的内置监视解决方案。

![Azure 监视体系结构示意图](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>可用性功能

Azure SQL 数据库使你的企业可以在中断期间继续运营。 在传统的 SQL Server 环境中，通常需要在本地设置至少两台计算机。 这些计算机包含确切数目的、以同步方式维护的数据副本，以便在单个计算机或组件出现故障时提供保护。 此环境提供高可用性，但在发生损毁数据中心的自然灾难时无法提供保护。

灾难恢复假设灾难性事件在局部地区发生，而远地部署了另一个包含数据副本的计算机或计算机组。 在 SQL Server 中，可以使用以异步模式运行的 Always On 可用性组来获取此功能。 人们通常不想等到远端复制完成后再提交事务，因此，在执行计划外故障转移时可能会丢失数据。

高级和业务关键服务层级中的数据库已[执行与可用性组同步类似的操作](high-availability-sla.md#premium-and-business-critical-service-tier-availability)。 较低服务层级中的数据库使用[不同但等效的机制](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability)通过存储提供冗余。 内置逻辑可帮助防范单个计算机发生故障。 使用活动异地复制功能可以在灾难损毁整个区域时提供保护。

在单个区域中的单个数据中心建筑物发生服务中断时，Azure 可用性区域会尝试提供保护。 它有助于防止建筑物中发生断电或网络中断。 在 SQL 数据库中，可将不同的副本放在不同的可用性区域（其实是不同的建筑物）中。

事实上，Azure 的服务级别协议 [(SLA)](https://azure.microsoft.com/support/legal/sla/)（由 Microsoft 所管理的数据中心的全球网络提供支持）有助于保持应用全天候运行。 Azure 平台完全管理每个数据库，并保证不会丢失数据并实现高百分比数据可用性。 Azure 会自动处理修补、备份、复制、故障检测；基础的潜在硬件、软件或网络故障；部署 bug 修复、故障转移、数据库升级和其他维护任务。 标准可用性是通过将计算层与存储层相隔离来实现的。 高级可用性的实现方式是将计算和存储层集成到单个节点以提高性能，然后实施类似于 Always On 可用性组的技术。 有关 Azure SQL 数据库的高可用性功能的完整讨论，请参阅 [SQL 数据库可用性](high-availability-sla.md)。 

此外，SQL 数据库还提供内置[业务连续性和全局可伸缩性](business-continuity-high-availability-disaster-recover-hadr-overview.md)功能。 其中包括：

- [自动备份](automated-backups-overview.md)：

  SQL 数据库自动执行数据库的完整备份、差异备份和事务日志备份，以便能够还原到任何时间点。 对于单一数据库和共用数据库，可以将 SQL 数据库配置为，将完整数据库备份存储到 Azure 存储，以供长期备份保留。 对于托管实例，还可以执行仅复制备份，以供长期备份保留。

- [时间点还原](recovery-using-backups.md)：

  所有 SQL 数据库部署选项都支持恢复到任何数据库的自动备份保留期内的任何时间点。
- [活动异地复制](active-geo-replication-overview.md)：

  使用单一数据库和共用数据库选项，可以在同一或全球分布式 Azure 数据中心内配置至多 4 个可读辅助数据库。 例如，如果有一个 SaaS 应用程序，其中的目录数据库包含大量并发只读事务，请使用活动异地复制来启用全局读取缩放。 这可以消除主数据库上因读取工作负荷产生的瓶颈。 对于托管实例，请使用自动故障转移组。
- [自动故障转移组](auto-failover-group-overview.md)：

  所有 SQL 数据库部署选项允许使用故障转移组实现全局规模的高可用性和负载均衡。 这包括大量数据库、弹性池和托管实例的透明异地复制与故障转移。 使用故障转移组可以最小的管理开销创建全局分布式 SaaS 应用程序。 这会将所有复杂的监视、路由和故障转移业务流程都留给 SQL 数据库来处理。
- [区域冗余的数据库](high-availability-sla.md)：

  可以通过 SQL 数据库跨多个可用性区域预配高级数据库或业务关键型数据库或者弹性池。 由于这些数据库和弹性池具有多个可实现高可用性的冗余副本，将这些副本置于多个可用性区域能够提高复原能力。 这包括能够在发生数据中心规模的故障后自动予以恢复，且不会丢失数据。

## <a name="built-in-intelligence"></a>内置智能

使用 SQL 数据库，可获得内置智能，帮助大幅降低运行和管理数据库的成本，并将应用程序的性能和安全性最大化。 通过全天候运行数以百万计的客户工作负荷，SQL 数据库收集和处理大量遥测数据，同时完全尊重客户的隐私。 各种算法正在不间断计算遥测数据，以便服务可以了解并适应应用程序。

### <a name="automatic-performance-monitoring-and-tuning"></a>自动性能监视和优化

SQL 数据库提供针对需要监视的查询的详细见解。 SQL 数据库会学习用户的数据库模式，使用户能够根据工作负荷调整数据库架构。 SQL 数据库[提供性能优化建议](database-advisor-implement-performance-recommendations.md)，可在其中查看并应用优化操作。

但是，持续监视数据库是一项艰巨且乏味的任务，尤其是在处理多个数据库时。 [Intelligent Insights](intelligent-insights-overview.md) 通过大规模地自动监视 SQL 数据库性能来为你完成此作业。 它会在出现性能下降问题时发出通知，识别每个问题的根本原因，并在可能的情况下提供性能改进建议。

可能无法高效管理大量数据库，即使使用 SQL 数据库和 Azure 提供的所有可用工具和报告也一样。 可考虑使用[自动优化](automatic-tuning-overview.md)将某些监视和优化操作委派给 SQL 数据库，而不是手动监视和优化数据库。 SQL 数据库自动应用建议、测试，并验证每个优化操作，确保性能持续提升。 这样一来，SQL 数据库将以受控且安全的方式自动适应工作负荷。 自动优化是指在每次优化操作前后仔细监视和比较数据库性能。 如果性能未得到提升，则还原优化操作。

在 SQL 数据库基础上运行 [SaaS 多租户应用](saas-tenancy-app-design-patterns.md)的许多合作伙伴均依赖于自动性能优化，确保其应用程序始终具有稳定且可预测的性能。 对他们而言，此功能可大大降低午夜出现性能事故的风险。 此外，由于部分客户群也使用 SQL Server，因此他们会使用 SQL 数据库提供的相同索引建议来帮助 SQL Sever 客户。

[SQL 数据库中](automatic-tuning-overview.md)有两个自动优化方面：

- **自动索引管理**：标识应在数据库中添加的索引以及应删除的索引。
- **自动计划更正**：识别有问题的计划并修复 SQL 计划性能问题。

### <a name="adaptive-query-processing"></a>自适应查询处理

可以使用[自适应查询处理](/sql/relational-databases/performance/intelligent-query-processing)，包括交错执行多语句表值函数，批处理模式内存授予反馈和批处理模式自适应联接。 每个自适应查询处理功能均应用类似的“学习和适应”技巧，帮助进一步解决与历史上棘手的查询优化问题相关的性能问题。

## <a name="advanced-security-and-compliance"></a>高级安全性和符合性

SQL 数据库提供一系列[内置安全性和符合性功能](../../active-directory/identity-protection/security-overview.md)，帮助应用程序满足各种安全性和符合性要求。

> [!IMPORTANT]
> Microsoft 已根据许多合规性标准认证了 Azure SQL 数据库（所有部署选项）。 有关详细信息，请参阅 [Microsoft Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，从中找出 SQL 数据库法规认证的最新列表。

### <a name="advance-threat-protection"></a>高级威胁防护

Azure Defender for SQL 是高级 SQL 安全功能的统一包。 它包括管理数据库漏洞以及检测可能对数据库造成威胁的异常活动的功能。 它提供用于启用和管理这些功能的一个位置。

- [漏洞评估](sql-vulnerability-assessment.md)：

  此服务可以发现、跟踪和帮助补救潜在的数据库漏洞。 它可直观查看安全状态，包括解决安全问题的可操作步骤，并可加强数据库的防御工事。
- [威胁检测](threat-detection-configure.md)：

  此功能可检测异常活动，这些活动表示异常和可能有害的数据库访问或使用尝试。 它不断监视数据库的可疑活动，并针对潜在漏洞、SQL 注入攻击和异常数据库访问模式提供即时的安全警报。 威胁检测警报提供可疑活动的详细信息，并建议如何调查和缓解威胁。

### <a name="auditing-for-compliance-and-security"></a>符合性和安全性审核

[审核](../../azure-sql/database/auditing-overview.md)可跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 审核可帮助你遵守法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

### <a name="data-encryption"></a>数据加密

SQL 数据库可提供加密功能来帮助保护数据。 对于动态数据，它使用[传输层安全性](https://support.microsoft.com/kb/3135244)。 对于静态数据，它使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。 对于使用中的数据，它使用 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)。

### <a name="data-discovery-and-classification"></a>数据发现和分类

[数据发现和分类](data-discovery-and-classification-overview.md)提供内置于 Azure SQL 数据库的功能，可用于对数据库中的敏感数据进行发现、分类、标记和保护。 它提供数据库分类状态的见解，以及跟踪对数据库内和其边界外的敏感数据的访问。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 集成和多重身份验证

通过 SQL 数据库，可使用 [Azure Active Directory 集成](authentication-aad-overview.md)集中管理数据库用户和其他 Microsoft 服务的身份。 此功能简化了权限管理，增强了安全性。 Azure Active Directory 支持[多重身份验证](authentication-mfa-ssms-overview.md)，以便在支持单一登录过程的同时提高数据和应用程序安全性。

## <a name="easy-to-use-tools"></a>易于使用的工具

SQL 数据库让应用程序的构建和维护更加轻松、高效。 SQL 数据库让你能够专注于自己的专长：生成优秀的应用。 可使用已有工具和技能在 SQL 数据库进行管理和开发。

|工具|说明|
|:---|:---|
|[Azure 门户](https://portal.azure.com/)|用于管理所有 Azure 服务的基于 Web 的应用程序。|
|[Azure Data Studio](/sql/azure-data-studio/)|一种跨平台数据库工具，在 Windows、macOS 和 Linux 上运行。|
|[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)|用于管理任何 SQL 基础结构（从 SQL Server 到 SQL 数据库，不一而足）的免费可下载客户端应用程序。|
|[Visual Studio 中的 SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)|用于开发 SQL Server 关系数据库、Azure SQL 数据库中的数据库、Integration Services 包、Analysis Services 数据模型和 Reporting Services 报表的免费可下载客户端应用程序。|
|[Visual Studio Code](https://code.visualstudio.com/docs)|一个可免费下载的开源代码编辑器，适用于 Windows、macOS 和 Linux。 它支持各种扩展，其中包括 [mssql 扩展](https://aka.ms/mssql-marketplace)（用于查询 Microsoft SQL Server、Azure SQL 数据库和 Azure Synapse Analytics（以前称为“SQL 数据仓库”））。|

SQL 数据库支持在 macOS、Linux 和 Windows 上使用 Python、Java、Node.js、PHP、 Ruby 和 .NET 生成应用程序。 SQL 数据库和 SQL Server 支持相同的[连接库](connect-query-content-reference-guide.md#libraries)。

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL 数据库常见问题解答

### <a name="can-i-control-when-patching-downtime-occurs"></a>我可以控制修补故障的时间吗？

否。 如果在应用中[使用重试逻辑](develop-overview.md#resiliency)，则修补通常不会产生明显影响。 有关详细信息，请参阅[规划 Azure SQL 数据库中的 Azure 维护事件](planned-maintenance.md)。



## <a name="engage-with-the-sql-server-engineering-team"></a>与 SQL Server 工程团队合作

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server)：询问数据库管理问题。
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server)：询问开发问题。
- [Microsoft 问答页](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)：询问技术问题。
- [反馈](https://aka.ms/sqlfeedback)：报告 Bug 和请求功能。
- [Reddit](https://www.reddit.com/r/SQLServer/)：讨论 SQL Server。

## <a name="next-steps"></a>后续步骤

- 有关单一数据库和弹性池的成本比较与计算器，请参阅[定价页](https://azure.microsoft.com/pricing/details/sql-database/)。
- 参阅以下快速入门：

  - [在 Azure 门户中创建数据库](single-database-create-quickstart.md)  
  - [使用 Azure CLI 创建数据库](az-cli-script-samples-content-guide.md)
  - [使用 PowerShell 创建数据库](powershell-script-content-guide.md)

- 如需一组 Azure CLI 和 PowerShell 的示例，请参阅：
  - [适用于 SQL 数据库的 Azure CLI 示例](az-cli-script-samples-content-guide.md)
  - [适用于 SQL 数据库的 Azure PowerShell 示例](powershell-script-content-guide.md)

- 有关已公布的新功能的信息，请参阅 [Azure SQL 数据库路线图](https://azure.microsoft.com/roadmap/?category=databases)。
- 请参阅 [Azure SQL 数据库博客](https://azure.microsoft.com/blog/topics/database)，SQL Server 产品团队成员会在其中发布有关 SQL 数据库新闻和功能的博客。

