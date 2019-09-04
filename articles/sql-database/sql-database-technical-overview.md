---
title: 什么是 Azure SQL 数据库服务？ | Microsoft Docs
description: 获取 SQL 数据库简介：Microsoft 在云中的关系数据库管理系统 (RDBMS) 的技术详细信息与功能。
keywords: SQL 简介, 什么是 SQL 数据库
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 8c931521e77123844817dff0e9209f8f95f7ad59
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279899"
---
# <a name="what-is-azure-sql-database-service"></a>什么是 Azure SQL 数据库服务

Azure SQL 数据库是一种通用的关系数据库托管服务，可用于为 Microsoft Azure 云中的应用程序和解决方案创建高度可用且高性能的数据存储层。 SQL 数据库可成为各种新式云应用程序的正确选择，因为它可让你使用强大的功能来处理关系数据和[非关系结构](sql-database-multi-model-features.md)（例如图形、JSON、空间和 XML）。 它基于[Microsoft SQL Server 数据库引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)的最新稳定版本，并使你能够使用一组丰富的高级查询处理功能，如[高性能内存中技术](sql-database-in-memory.md)和[智能查询处理](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json).
利用 Microsoft 的云优先策略，首先将 SQL Server 的最新功能发布到 SQL 数据库，然后再发布到 SQL Server 本身。 此方法可为你提供 SQL Server 的最新功能，这些新功能已经过数百万个数据库的测试且无任何修补或升级开销。 使用 SQL 数据库可在两个不同的购买模型中轻松定义和缩放性能：[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)和[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)。 SQL 数据库是完全托管型服务，提供内置的可用性、备份和其他常见维护操作。 Microsoft 无缝地处理 SQL 和 OS 代码的所有修补和更新，并对底层基础结构的所有管理进行了抽象。

> [!NOTE]
> 有关 Azure SQL 数据库术语词汇表，请参阅 [SQL 数据库术语词汇表](sql-database-glossary-terms.md)

Azure SQL 数据库提供下述适用于 Azure SQL 数据库的部署选项：

![部署选项](./media/sql-database-technical-overview/deployment-options.png)

- [单一数据库](sql-database-single-database.md)代表完全托管的独立数据库，它是需要单一可靠数据源的新式云应用程序和微服务的理想选择。 单个数据库类似于[Microsoft SQL Server 数据库引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)中[包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json)。
- [托管实例](sql-database-managed-instance.md)是 [Microsoft SQL Server 数据库引擎](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)的完全托管型实例，其中包含一组可结合使用的数据库。 若要轻松将本地 SQL Server 数据库迁移到 Azure 云，或者对于需要利用 SQL Server 数据库引擎提供的强大的数据库功能的应用程序，托管实例都是完美的选择。
- [弹性池](sql-database-elastic-pool.md)是[单一数据库](sql-database-single-database.md)的集合，其中包含一组共享的资源（例如 CPU 或内存）。 可以将单一数据库移入或移出弹性池。

> [!IMPORTANT]
> 若要了解 SQL 数据库与 SQL Server 的功能差异，以及不同 Azure SQL 数据库部署选项的区别，请参阅 [SQL 功能](sql-database-features.md)。

SQL 数据库通过多个资源类型、服务层级和计算大小提供可预测的、动态可缩放的性能，主要表现在：无停机时间、内置智能优化、全局可伸缩性和可用性，以及高级安全选项 - 一切几乎不用人工管理。 借助这些功能，用户可将注意力集中在如何快速进行应用开发、加快推向市场，而不需要投入宝贵的时间和资源来管理虚拟机与基础结构。 SQL 数据库服务目前在世界各地有 38 个数据中心，且有更多数据中心定期上线，因此你可以在附近的数据中心运行数据库。

## <a name="scalable-performance-and-pools"></a>可缩放的性能和池

可以使用各种风格的 SQL 数据库来定义要分配的资源量。 
- 使用单一数据库，每个数据库相互隔离且可移植，每个数据库都有自己有保证的计算、内存和存储资源量。 分配给数据库的资源量专门用于该数据库，而不会与 Azure 云中的其他数据库共享。 你还可以动态[扩展和缩减单一数据库资源](sql-database-single-database-scale.md)。 单一数据库提供不同的计算、内存和存储资源（1 到 80 个 vCore、32 GB 到 4 TB 存储等等）来满足不同的需求。借助单一数据库的[“超大规模”服务层级](sql-database-service-tier-hyperscale.md)，可以通过快速备份和还原功能扩展到 100TB。
- 使用弹性池可以分配池中所有数据库共享的资源。 可以新建数据库，或者将现有单一数据库移到资源池中，以最大限度地使用资源和节省资金，并且还能动态[扩展和缩减弹性池资源](sql-database-elastic-pool-scale.md)。
- 使用托管实例，每个托管实例都与其他实例隔离开来，并包含有保证的资源。 在托管实例中，实例数据库共用资源集，并且能够纵向和横向动态[扩展托管实例资源](sql-database-managed-instance-resource-limits.md)。

可以在小型单一数据库中构建第一个应用，每个月只需在“常规用途”服务层级中花费少量资金。然后可以根据解决方案的需要，随时手动或以编程方式将服务层级更改为“业务关键”服务层级。 可在不给应用或客户造成停机的情况下调整性能。 动态可伸缩性可让数据库以透明方式响应快速变化的资源要求，使用户只需为用到的资源付费。

动态可伸缩性不同于自动缩放。 自动缩放是指服务根据条件自动缩放，而动态可伸缩性允许在无停机时间的情况下进行手动缩放。 单个数据库支持手动动态可伸缩性，但不支持自动缩放。 若要获得更多*自动*体验，请考虑使用弹性池，它允许数据库根据各个数据库需求共享池中的资源。 但是，有一些脚本可帮助自动执行单个数据库的可伸缩性。 有关示例，请参阅[使用 PowerShell 监视和缩放单个数据库](scripts/sql-database-monitor-and-scale-database-powershell.md)。

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>购买模型、服务层级、计算大小和存储量

SQL 数据库提供以下两种购买模型：
- [基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)允许选择 vCore 数、内存容量，以及存储的容量和速度。 基于 vCore 的购买模型还允许使用[适用于 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来节省成本。 有关 Azure 混合权益的详细信息，请参阅[常见问题解答](#sql-database-frequently-asked-questions-faq)。
- [基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)：混合提供三个服务层级中的计算、内存和 IO 资源，以支持轻型到重型数据库工作负荷。 每个层级中的不同计算大小提供这些资源的不同组合，你可以向其添加更多的存储资源。

### <a name="elastic-pools-to-maximize-resource-utilization"></a>弹性池可以最大化资源利用率

许多业务和应用程序只要能够创建单一数据库并按需调高或调低性能即可，尤其是当使用模式相对容易预测时。 但如果有无法预测的使用模式，则管理成本和业务模式就会变得相当困难。 [弹性池](sql-database-elastic-pool.md)旨在解决此问题。 概念很简单。 可以向池而不是单个数据库分配性能资源，并且仅需为池的总体性能资源付费，而无需为单一数据库的性能付费。

   ![弹性池](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

使用弹性池时，不需要在资源需求波动时担心如何上下调节数据库性能。 共用数据库可根据需要使用弹性池的性能资源。 共用数据库会使用该池，但不会超出其限制，因此即使单个数据库的使用情况仍不可预测，成本也仍是可预测的。 此外，可以 [向池添加和删除数据库](sql-database-elastic-pool-manage-portal.md)，将应用从少量数据库扩展到数千个，而一切费用不会超出由你控制的预算范围。 还可以控制池中数据库可用的资源量上限与下限，确保池中不会有任何数据库使用所有的池资源，每个共用数据库的可用资源量都有最低保障。 若要深入了解如何通过弹性池设计 SaaS 应用程序的模式，请参阅[具有 SQL 数据库的多租户 SaaS 应用程序的设计模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。

脚本可以帮助监视和缩放弹性池。 有关示例，请参阅[使用 PowerShell 在 Azure SQL 数据库中监视和缩放 SQL 弹性池](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> 托管实例不支持弹性池。 相反，托管实例是共用托管实例资源的实例数据库的集合。

### <a name="blend-single-databases-with-pooled-databases"></a>混合使用单一数据库与共用数据库

可将单一数据库与弹性池混合使用，快速轻松地更改单一数据库和弹性池的服务层级来适应自己的情况。 凭借 Azure 的功能和作用范围，可将其他 Azure 服务与 SQL 数据库搭配使用以满足独特的应用设计需求，提高成本和资源效益，发掘新的商机。

## <a name="extensive-monitoring-and-alerting-capabilities"></a>集中监视和警报功能

Azure SQL 数据库提供一组高级监视和故障排除功能，可帮助你深入了解工作负荷的特征。 这些功能和工具可划分为：
 - 最新版本的 SQL Server 数据库引擎提供的内置监视功能，可让你找到实时性能见解。 
 - Azure 平台提供的 PaaS 监视功能，可让你轻松监视大量的数据库实例，并提供故障排除建议来帮助你解决性能问题。

你应利用的最重要的内置数据库引擎监视功能是[查询存储](sql-database-operate-query-store.md)组件，该功能可实时记录查询性能，并使你能够确定潜在的性能问题和顶级资源使用者. 自动优化和建议提供有关性能退化以及存在缺失或重复索引的查询的建议。 使用 Azure SQL 数据库中的自动优化，可以手动应用脚本来解决问题，或者让 Azure SQL 数据库应用修复措施，测试并验证该修复措施是否起了作用，并根据结果保留或还原更改。 除了查询存储和自动优化功能外，还可以使用标准的 [DMV 和 XEvent](sql-database-monitoring-with-dmvs.md) 来监视工作负荷性能。

Azure 平台提供[内置的性能监视](sql-database-performance.md)和[警报](sql-database-insights-alerts-portal.md)工具，将它们与性能评分相结合，可以轻松监视数千个数据库的状态。 使用这些工具，可以根据当前的或预计的性能需求，快速评估纵向扩展或缩减的影响。 此外，SQL 数据库可[发出指标和诊断日志](sql-database-metrics-diag-logging.md)，以便更轻松地监视。 可配置 SQL 数据库，将资源使用情况、辅助角色和会话以及连接性存储到以下 Azure 资源之一：

- **Azure 存储**：用于较小的价格存档大量遥测数据。
- **Azure 事件中心**：用于将 SQL 数据库遥测与自定义监视解决方案或热管道集成。
- **Azure Monitor 日志**：用于具有报告、警报和缓解功能的内置监视解决方案。

    ![体系结构](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>可用性功能

在传统的 SQL Server 环境中，通常需要在本地设置（至少）2 台计算机（使用 AlwaysOn 可用性组或故障转移群集实例等功能进行设置），其中包含确切数目的数据副本（以同步方式维护，以便在单个计算机/组件出现故障时提供保护。 这样可以提供高可用性，但在发生损毁数据中心的自然灾难时无法提供保护。

灾难恢复假设灾难性事件在局部地区发生，而远地部署了另一个包含数据副本的计算机/计算机组。  在 SQL Server 中，可以使用以异步模式运行的 Always On 可用性组来获取此功能。  轻型问题的速度通常意味着，用户不希望等待复制在提交事务之前发生，因此在执行未计划的故障转移时可能会丢失数据。

高级和业务关键服务层级中的数据库的某些功能已经与可用性组的同步功能[非常类似](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)。 较低服务层级中的数据库使用[不同但等效的机制](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)通过存储提供冗余。 有相应的逻辑可以防范单个计算机发生故障。  使用活动异地复制功能可以在灾难损毁整个区域时提供保护。

Azure 可用性区域是一种高可用性问题的重头戏。  它尝试防止单个区域中的单个数据中心构建中断。  因此，它希望防止网络丢失或网络丢失。 在 SQL Azure 中，这将通过将不同的副本放置在不同的可用性区域中（与大楼不同）并以其他方式工作。

事实上，Azure 业界领先的 99.99% 可用性服务级别协议[（SLA）](https://azure.microsoft.com/support/legal/sla/)由 Microsoft 管理的数据中心的全球网络提供支持，可帮助保持应用运行24/7。 Azure 平台完全管理每个数据库，并保证不会丢失数据并实现高百分比数据可用性。 Azure 会自动处理修补、备份、复制、故障检测；基础的潜在硬件、软件或网络故障；部署 bug 修复、故障转移、数据库升级和其他维护任务。 标准可用性是通过将计算层与存储层相隔离来实现的。 高级可用性的实现方式是将计算和存储层集成到单个节点以提高性能，然后藉此实施类似于 Always On 可用性组的技术。 有关 Azure SQL 数据库的高可用性功能的完整讨论，请参阅 [SQL 数据库可用性](sql-database-high-availability.md)。 此外，SQL 数据库还提供内置[业务连续性和全局可伸缩性](sql-database-business-continuity.md)功能，包括：

- **[自动备份](sql-database-automated-backups.md)** ：

  SQL 数据库自动执行 Azure SQL 数据库的完整备份、差异备份和事务日志备份，以便能够还原到任何时间点。 对于单一数据库和共用数据库，可以将 SQL 数据库配置为，将完整数据库备份存储到 Azure 存储，以供长期备份保留。 对于托管实例，还可以执行仅复制备份，以供长期备份保留。

- **[时间点还原](sql-database-recovery-using-backups.md)** ：

  所有 SQL 数据库部署选项都支持恢复到任何 Azure SQL 数据库的自动备份保留期内的任何时间点。
- **[活动异地复制](sql-database-active-geo-replication.md)** ：

  使用单一数据库和共用数据库，可以在同一或全球分布式 Azure 数据中心内配置至多 4 个可读辅助数据库。  例如，如果有一个 SaaS 应用程序，其中的目录数据库包含大量并发只读事务，请使用活动异地复制来启用全局读取缩放并删除主数据库上因读取工作负荷产生的瓶颈。 对于托管实例，请使用自动故障转移组。
- **[自动故障转移组](sql-database-auto-failover-group.md)** ：

  通过所有 SQL 数据库部署选项，可使用故障转移组实现全球范围内的高可用性和负载均衡，包括大量数据库、弹性池和托管实例的透明异地复制和故障转移。 通过故障转移组，可以最小管理开销创建全球分布式 SaaS 应用程序，将所有复杂监视、路由和故障转移业务流程都留给 SQL 数据库。
- **[区域冗余数据库](sql-database-high-availability.md)** ：

  通过 SQL 数据库，可以跨多个可用性区域预配高级或业务关键数据库或弹性池。 由于这些数据库和弹性池有多个用于确保高可用性的冗余副本，因此将这些副本置于多个可用性区域可以提高复原能力，包括自动从数据中心规模的故障中恢复而不丢失数据的能力。

## <a name="built-in-intelligence"></a>内置智能

使用 SQL 数据库，可获得内置智能，帮助你大幅降低运行和管理数据库的成本，并将应用程序的性能和安全性最大化。 通过全天候运行数以百万计的客户工作负荷，SQL 数据库收集和处理大量遥测数据，同时完全尊重幕后客户的隐私。 各种算法正在不间断计算遥测数据，以便服务可以了解并适应应用程序。 根据此分析，该服务会提出针对特定工作负荷的性能改进建议。

### <a name="automatic-performance-monitoring-and-tuning"></a>自动性能监视和优化

SQL 数据库提供针对需要监视的查询的详细见解。 SQL 数据库了解数据库模式，使你能够根据工作负荷调整数据库架构。 SQL 数据库[提供性能优化建议](sql-database-advisor.md)，可在其中查看并应用优化操作。

然而，持续监视数据库是一种很繁琐的任务，尤其是在处理多个数据库时。 [Intelligent Insights](sql-database-intelligent-insights.md) 通过大规模地自动监视 SQL 数据库性能并就性能下降问题发出通知来为你完成此作业，它确定问题的根本原因并在可能的情况下提供性能改进建议。

可能无法高效管理大量数据库，即使使用 SQL 数据库和 Azure 门户提供的所有可用工具和报表也一样。 可考虑使用[自动优化](sql-database-automatic-tuning.md)将某些监视和优化操作委派给 SQL 数据库，而不是手动监视和优化数据库。 SQL 数据库自动应用建议、测试，并验证每个优化操作，确保性能持续提升。 这样一来，SQL 数据库将以受控且安全的方式自动适应工作负荷。 自动优化是指在每次优化操作前后仔细监视和比较数据库性能，如果性能未得到提升，则还原优化操作。

目前，在 SQL 数据库上运行 [SaaS 多租户应用](sql-database-design-patterns-multi-tenancy-saas-applications.md)的许多合作伙伴均依赖于自动性能优化，确保其应用程序始终具有稳定且可预测的性能。 对他们而言，此功能可大大降低午夜出现性能事故的风险。 此外，由于部分客户群也使用 SQL Server，因此他们使用 SQL 数据库提供的相同索引建议来帮助 SQL Sever 客户。

[SQL 数据库可从两个方面进行自动优化](sql-database-automatic-tuning.md)：

- **自动索引管理**：标识应在数据库中添加的索引以及应删除的索引。
- **自动计划更正**：标识有问题的计划并修复 SQL 计划性能问题（即将推出，已在 SQL Server 2017 中提供）。

### <a name="adaptive-query-processing"></a>自适应查询处理

我们还将向 SQL 数据库添加[自适应查询处理](/sql/relational-databases/performance/intelligent-query-processing)系列功能，包括交错执行多语句表值函数，批处理模式内存授予反馈和批处理模式自适应联接。 每个自适应查询处理功能均应用类似的“学习和适应”技巧，帮助进一步解决与历史上棘手的查询优化问题相关的性能问题。

## <a name="advanced-security-and-compliance"></a>高级安全性和符合性

SQL 数据库提供一系列[内置安全性和符合性功能](sql-database-security-overview.md)，帮助应用程序满足各种安全性和符合性要求。

> [!IMPORTANT]
> Azure SQL 数据库（所有部署选项）已通过了许多合规性标准的认证。 有关详细信息，请参阅[Microsoft Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，你可以在其中找到最新的 SQL 数据库符合性认证列表。

### <a name="advance-threat-protection"></a>高级威胁防护

高级数据安全是高级 SQL 安全功能的统一包。 它包括发现敏感数据并分类、管理数据库漏洞以及检测可能对数据库造成威胁的异常活动的功能。 它提供用于启用和管理这些功能的一个转到位置。

- [数据发现和分类](sql-database-data-discovery-and-classification.md)：

  此功能（目前为预览版）提供内置于 Azure SQL 数据库的功能，可用于发现、分类、标记和保护数据库中的敏感数据。 它可用于直观查看数据库分类状态，以及跟踪对数据库内和其边界外的敏感数据的访问。
- [漏洞评估](sql-vulnerability-assessment.md)：

  此服务可以发现、跟踪和帮助补救潜在的数据库漏洞。 它可直观查看安全状态，包括解决安全问题的可操作步骤，并可加强数据库的防御工事。
- [威胁检测](sql-database-threat-detection.md)：

  此功能可检测异常活动，这些活动表示异常和可能有害的数据库访问或使用尝试。 它不断监视数据库的可疑活动，并针对潜在漏洞、SQL 注入攻击和异常数据库访问模式提供即时的安全警报。 威胁检测警报提供可疑活动的详细信息，并建议如何调查和缓解威胁。

### <a name="auditing-for-compliance-and-security"></a>符合性和安全性审核

[审核](sql-database-auditing.md)可跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 审核可帮助你一直保持符合法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。

### <a name="data-encryption"></a>数据加密

SQL 数据库可以保护数据。对于动态数据，它使用[传输层安全性](https://support.microsoft.com/kb/3135244)提供加密；对于静态数据，它使用[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)提供加密；对于使用中的数据，它使用 [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) 提供加密。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 集成和多重身份验证

通过 SQL 数据库，可使用 [Azure Active Directory 集成](sql-database-aad-authentication.md)集中管理数据库用户和其他 Microsoft 服务的身份。 此功能简化了权限管理，增强了安全性。 Azure Active Directory 支持[多重身份验证](sql-database-ssms-mfa-authentication.md) (MFA)，以便在支持单一登录过程的同时提高数据和应用程序安全性。

### <a name="compliance-certification"></a>符合性认证

SQL 数据库参与定期审核，并已针对多个符合性标准进行验证。 有关详细信息，请参阅[Microsoft Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，你可以在其中找到最新的 SQL 数据库符合性认证列表。

## <a name="easy-to-use-tools"></a>易于使用的工具

SQL 数据库让应用程序的构建和维护更加轻松、高效。 SQL 数据库让你能够专注于自己的专长：生成优秀的应用。 可使用已有工具和技能在 SQL 数据库进行管理和开发。

- **[Azure 门户](https://portal.azure.com/)** ：

  用于管理所有 Azure 服务的基于 Web 的应用程序
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** ：

  用于管理任何 SQL 基础结构（从 SQL Server 到 SQL 数据库，不一而足）的免费可下载客户端应用程序。
- **[Visual Studio 中的 SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** ：

  用于开发 SQL Server 关系数据库、Azure SQL 数据库、Integration Services 包、Analysis Services 数据模型和 Reporting Services 报表的免费可下载客户端应用程序。
- **[Visual Studio Code](https://code.visualstudio.com/docs)** ：

  一种可免费下载的开源代码编辑器，适用于 Windows、macOS 和 Linux，并支持各种扩展，其中包括 [mssql 扩展](https://aka.ms/mssql-marketplace)（用于查询 Microsoft SQL Server、Azure SQL 数据库和 SQL 数据仓库）。

SQL 数据库支持在 MacOS、Linux 和 Windows 上使用 Python、Java、Node.js、PHP、 Ruby 和 .NET 生成应用程序。 SQL 数据库和 SQL Server 支持相同的[连接库](sql-database-libraries.md)。

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions-faq"></a>SQL 数据库常见问题解答 (FAQ)

### <a name="what-is-the-current-version-of-sql-database"></a>SQL 数据库的最新版本是多少

SQL 数据库的当前版本是 V12。 V11 版本已停用。

### <a name="can-i-control-when-patching-downtime-occurs"></a>我可以控制修补故障的时间吗

否。 如果在应用中[使用重试逻辑](sql-database-develop-overview.md#resiliency)，则修补通常不会产生明显影响。 有关如何为 Azure SQL 数据库上的计划内维护事件做好准备的详细信息，请参阅[规划 Azure SQL 数据库中的 Azure 维护事件](sql-database-planned-maintenance.md)。

### <a name="azure-hybrid-benefit-questions"></a>Azure 混合权益问题

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>面向 SQL Server 的 Azure 混合权益是否具有双倍使用权利

我们为客户提供 180 天的许可证双倍使用权利，以确保无缝运行迁移。 在 180 天期限过后，SQL Server 许可证只能在云中的 SQL 数据库内使用，并且在本地和云中都没有双倍使用权利。

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>面向 SQL Server 的 Azure 混合权益与许可证移动性有何区别

目前，我们为 SQL Server 客户提供软件保障许可证移动性权益，以便将其许可证重新分配到第三方共享服务器。 可对 Azure IaaS 和 AWS EC2 使用此权益。
与许可证移动性相比，面向 SQL Server 的 Azure 混合权益的区别主要体现在两个方面：

- 提供经济权益，以便将高度虚拟化的工作负荷转移到 Azure。 对于高度虚拟化的应用程序，如果 SQL EE 客户在本地拥有一个核心，则他们可以在 Azure 的常规用途 SKU 中获得 4 个核心。 许可证移动性不允许使用任何特殊成本权益将虚拟化工作负荷转移到云中。
- 它适用于 Azure（SQL 数据库托管实例）上与本地 SQL Server 高度兼容的 PaaS 目标

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>面向 SQL Server 的 Azure 混合权益的特殊权利有哪些

SQL 数据库客户将获得与面向 SQL Server 的 Azure 混合权益相关的以下权利：

|许可证足迹|面向 SQL Server 的 Azure 混合权益可带来哪些好处？|
|---|---|
|具有 SA 的 SQL Server Enterprise Edition 核心客户|<li>可以根据“常规用途”或“业务关键”SKU 支付基准费率</li><br><li>1 个本地核心 =“常规用途”SKU 中的 4 个核心</li><br><li>1 个本地核心 =“业务关键”SKU 中的 1 个核心</li>|
|具有 SA 的 SQL Server Standard Edition 核心客户|<li>只能根据“常规用途”SKU 支付基准费率</li><br><li>1 个本地核心 =“常规用途”SKU 中的 1 个核心</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>与 SQL Server 工程团队合作

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server)：询问数据库管理问题
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server)：询问开发问题
- [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?category=sqlserver)：询问技术问题
- [反馈](https://aka.ms/sqlfeedback)：报告 Bug 和请求功能
- [Reddit](https://www.reddit.com/r/SQLServer/)：讨论 SQL Server

## <a name="next-steps"></a>后续步骤

- 有关单一数据库和弹性池的成本比较与计算器，请参阅[定价页](https://azure.microsoft.com/pricing/details/sql-database/)。
- 请参阅这些快速入门，以便尽快入门：

  - [在 Azure 门户中创建 SQL 数据库](sql-database-single-database-get-started.md)  
  - [使用 Azure CLI 创建 SQL 数据库](sql-database-get-started-cli.md)
  - [使用 PowerShell 创建 SQL 数据库](sql-database-get-started-powershell.md)

- 如需一组 Azure CLI 和 PowerShell 的示例，请参阅：
  - [适用于 SQL 数据库的 Azure CLI 示例](sql-database-cli-samples.md)
  - [适用于 SQL 数据库的 Azure PowerShell 示例](sql-database-powershell-samples.md)

 - 有关公布的新功能的信息，请参阅 
   - **[适用于 SQL 数据库的 Azure 路线图](https://azure.microsoft.com/roadmap/?category=databases)** -用于找出新增功能和即将推出的功能的位置。
  - **[AZURE Sql 数据库博客](https://azure.microsoft.com/blog/topics/database)** -SQL Server 产品团队成员通过博客了解有关 SQL 数据库的新闻和功能。

