---
title: Azure SQL 数据库常见问题解答 | Microsoft Docs
description: 客户就云数据库、Azure SQL 数据库、Microsoft 的关系数据库管理系统 (RDBMS) 和云中“数据库即服务”经常提出的问题的解答。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 9bb79a2054a0e51ce435f51a52f964062427cea4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647990"
---
# <a name="sql-database-faq"></a>SQL 数据库常见问题

## <a name="what-is-the-current-version-of-sql-database"></a>SQL 数据库的当前版本是多少？
SQL 数据库的当前版本是 V12。 V11 版本已停用。

## <a name="what-is-the-sla-for-sql-database"></a>什么是 SQL 数据库的 SLA？
我们保证至少在 99.99% 的时间内，不管使用哪个服务层，都能在 Microsoft Azure SQL 数据库与我们的 Internet 网关之间保持连接。 有关详细信息，请参阅 [SLA](http://azure.microsoft.com/support/legal/sla/)。

## <a name="whatis-the-new-vcore-based-purchasing-model-preview-for-azure-sql-database"></a>Azure SQL 数据库的基于 vCore 的新购买模型（预览版）是什么？

新购买模型是对现有基于 DTU 的模型的补充。 基于 vCore 的购买模型旨在让客户以灵活、可控、透明且直接的方式将本地工作负载要求转换到云。 它还允许客户根据其工作负荷需求缩放计算和存储资源。 此外，借助[面向 SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，使用 vCore 模型的单一数据库和弹性池选项最大能够实现 30% 的节省。 有关详细信息，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型（预览版）](sql-database-service-tiers-vcore.md)。 

## <a name="what-is-a-vcore"></a>什么是 vCore？ 
虚拟核心表示逻辑 CPU，提供不同代的硬件供客户选择。 第 4 代逻辑 CPU 基于 Intel E5-2673 v3 (Haswell) 2.4 GHz 处理器，第 5 代逻辑 CPU 基于 Intel E5-2673 v4 (Broadwell) 2.3 GHz 处理器。

## <a name="is-moving-to-the-vcore-based-model-required"></a>是否必须转移到基于 vCore 的模型？
不是。弹性池和单一数据库的基于 vCore 的部署选项简介体现了我们对客户选择和灵活性的承诺。 如果客户想要继续使用基于 DTU 的模型，他们不需要对此公告采取任何措施，且其体验和计费方式将保持不变。 

在许多情况下，应用程序可以受益于预配置的资源套件的简单性。 因此，我们继续向客户提供并支持这些基于 DTU 的选项。 如果你正在使用这些选项且其符合业务要求，请继续使用。

基于 DTU 和 vCore 的模型将继续共存。 我们应客户要求推出了基于 vCore 的模型，这些客户希望对数据库资源获得更大的透明度，并可分别缩放计算和存储资源。 使用基于 vCore 的模型，拥有活动软件保障的客户还可通过面向 SQL Server 的 Azure 混合权益节约成本。

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model-preview"></a>如何在基于 DTU 的购买模型与基于 vCore 的购买模型（预览版）之间做出选择？ 
数据传输单元 (DTU) 基于 CPU、内存、读取和写入的混合衡量。 基于 DTU 的性能级别代表驱动不同级别应用程序性能的预配资源捆绑。 对于不想要考虑底层资源，而希望使用简单的预配捆绑而每月支付固定费用的客户而言，基于 DTU 的模型可能更适合满足需求。 但是，对于需要更深入了解底层资源，或对其进行独立缩放以实现最优性能的客户而言，基于 vCore 的模型是最佳选择。  此外，如果客户为 SQL Server 激活了软件保障 (SA)，可通过[面向 SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)利用现有投资并最多节省 30% 的费用。  两种购买模型的选项都能提供完全托管的服务的权益，例如自动化备份、软件更新和补丁。 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>面向 SQL Server 的 Azure 混合权益是什么？ 
[面向 SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)可帮助你最大限度地提高当前许可证投资的价值，并加快向云迁移的速度。 Azure 混合权益 SQL Server 是基于 Azure 的混合权益，可通过它使用具有软件保障的 SQL Server 许可证，从而支付更少的 SQL 数据库费用（“基准费率”）。 面向 SQL Server 的 Azure 混合权益可用于 SQL 数据库单一数据库和弹性池基于 vCore 的购买模型（预览版）的公共预览版。 即使 SKU 是活动的也可以应用此权益，但请注意，从你在 Azure 门户中进行选择时起，就会应用基准费率。 不会补发信用额度。

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>面向 SQL Server 的 Azure 混合权益是否具有双倍使用权利？
我们为客户提供 180 天的许可证双倍使用权利，以确保无缝运行迁移。 在 180 天期限过后，SQL Server 许可证只能在云中的 SQL 数据库内使用，并且在本地和云中都没有双倍使用权利。


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>面向 SQL Server 的 Azure 混合权益与许可证移动性有何区别？
目前，我们为 SQL Server 客户提供软件保障许可证移动性权益，以便将其许可证重新分配到第三方共享服务器。 可对 Azure IaaS 和 AWS EC2 使用此权益。
与许可证移动性相比，面向 SQL Server 的 Azure 混合权益的区别主要体现在两个方面：
- 提供经济权益，以便将高度虚拟化的工作负荷转移到 Azure。 对于高度虚拟化的应用程序，如果 SQL EE 客户在本地拥有一个核心，则他们可以在 Azure 的常规用途 SKU 中获得 4 个核心。 许可证移动性不允许使用任何特殊成本权益将虚拟化工作负荷转移到云中。
- 它适用于 Azure 上与本地 SQL Server – SQL 数据库托管实例高度兼容的 PaaS 目标。

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>面向 SQL Server 的 Azure 混合权益的特殊权利有哪些？
SQL 数据库客户将获得与面向 SQL Server 的 Azure 混合权益相关的以下权利：

|许可证足迹|面向 SQL Server 的 Azure 混合权益可带来哪些好处？|
|---|---|
|具有 SA 的 SQL Server Enterprise Edition 核心客户|<li>可以根据“常规用途”或“业务关键”SKU 支付基准费率</li><br><li>1 个本地核心 =“常规用途”SKU 中的 4 个核心</li><br><li>1 个本地核心 =“业务关键”SKU 中的 1 个核心</li>|
|具有 SA 的 SQL Server Standard Edition 核心客户|<li>只能根据“常规用途”SKU 支付基准费率</li><br><li>1 个本地核心 =“常规用途”SKU 中的 1 个核心</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>基于 vCore 的模型是否适用于 SQL 数据库托管实例？
[托管实例](sql-database-managed-instance.md)只能使用基于 vCore 的模型。 有关详细信息，另请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/managed/)。 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>计算和存储成本是否取决于所选的服务层？
计算成本反映针对应用程序预配的总计算容量。 在“业务关键”服务层中，我们会自动分配至少 3 个 Always ON 副本。 为了反映计算资源的附加分配，在“业务关键”服务层中，vCore 价格大约高出 2.7 倍。 出于相同的原因，“业务关键”层中更高的每 GB 存储价格反映了 SSD 存储的高 IO 和低延迟。 同时，备份存储成本并无不同，因为在这两种情况下，我们都使用某类标准存储。

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>如何支付存储费用 - 是根据预先的配置，还是数据库使用的功能？
不同存储类型的计费方式各不相同。 对于数据存储，你要根据所选的最大数据库或池大小支付预配存储的费用。 除非减小或增大该最大值，否则费用不会变化。 备份存储与实例的自动备份相关。 延长备份保留期会使实例使用的备份存储空间增大。 如果备份存储空间未超过 100% 的总预配服务器存储空间，则无需额外付费。 超出此部分的其他备份存储空间按 GB/月收费。 例如，如果数据库存储空间大小为 100 GB，则可以获得 100 GB 的备份，无需额外付费。 但如果备份为 110 GB，则需额外支付 10 GB 存储空间的费用。

对于单一数据库的备份存储，将会根据分配给数据库备份的存储减去数据库大小按比例计费。 对于弹性池的备份存储，将会根据分配给池中所有数据库的数据库备份的存储，减去弹性池最大数据大小按比例计费。 增加数据库大小或弹性池或者增加事务率都需要购买更多存储，因此会增加备份存储的费用。  增加最大数据大小时，会从计费备份存储大小中减去此新数量。

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>将现有数据库转换到新服务层时如何选择适当的 SKU？ 
对于使用基于 DTU 的模型的现有 SQL 数据库应用程序，“常规用途”服务层相当于“标准”层。 “业务关键”服务层相当于“高级”层。 对于这两种情况，应该为应用程序在基于 DTU 的模型中使用的每 100 个 DTU 至少分配 1 个 vCore。

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>基于 vCore 的新服务层是否提供与所有现有服务级别目标 (SLO) 兼容的性能级别？
对于使用 100 个或更多 DTU 的所有弹性池和数据库，基于 vCore 的新服务层提供类似的性能选项。  我们将不断添加更多的 SLO，以适应继续段时间，以适应 DTU 数目不足 100 的工作负荷。

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>基于 DTU 的现有服务层与基于 vCore 的新服务层之间是否有任何数据库功能差别？ 
新服务层支持当前基于 DTU 的产品/服务提供的功能超集。 附加功能包括一组额外的动态管理视图 (DMV) 和更多的资源配置选项。 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>如果我的数据库受 CPU 限制且不使用大量存储，是否无需支付额外的存储费用即可增加计算资源？
是的，可以单独选择应用程序所需的计算级别，同时保留存储不变。 存储最低可设置为 32 GB。 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>基于 vCore 的新层是否支持目前的 35 天时间点还原 (PITR)？ 
可为 PITR 配置 7 到 35 天的备份保留期。 如果备份存储超过存储量（等于最大数据大小），则会根据实际存储消耗量计费。 在预览版中，PITR 保留期默认设置为 7 天。 在许多情况下，最大数据大小足以存储 7 天的备份。

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>你们为何允许选择硬件代次进行计算？
我们的目标是实现最大的灵活性，以便可以选择与应用程序需求最相符的性能配置。 上表显示了第 4 代与第 5 代的差别。 具体而言，第 4 代为每个 vCore 提供的内存要大得多。 但是，第 5 代硬件允许以高得多的力度纵向扩展计算资源。 我们希望这些差异透明化，使应用程序达到最高的性价比。

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>是否需要将应用程序脱机，才能将基于 DTU 的数据库转换为基于 vCore 的服务层？ 
新服务层提供简单的在线转换方式，与现有标准和高级服务层间的升级和降级过程类似。 可以使用门户、ARM、PowerShell、Azure CLI 或 T-SQL 启动此转换。 请参阅[管理单一数据库](sql-database-single-database-resources.md)和[管理弹性池](sql-database-elastic-pool.md)。

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>是否可将数据库从基于 vCore 的服务层转换为基于 DTU 的服务层？ 
是的，可以使用门户，或者使用门户、ARM、PowerShell、Azure CLI 或 T-SQL 以编程方式，轻松将数据库转换为支持的任何性能目标。 请参阅[管理单一数据库](sql-database-single-database-resources.md)和[管理弹性池](sql-database-elastic-pool.md)。

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>是否可以在“常规用途”和“业务关键”服务层之间升级或降级？ 
可以，但存在一些限制。 目标 SKU 必须符合针对现有部署配置的最大数据库或弹性池大小。 如果使用[面向 SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，则“业务关键”SKU 仅适用于拥有 Enterprise Edition 许可证的客户。 只有已使用面向 SQL Server 的 Azure 混合权益从本地迁移到“常规用途”服务层，且拥有 Enterprise Edition 许可证的客户可以升级到“业务关键”服务层。 有关详细信息，请参阅[面向 SQL Server 的 Azure 混合使用权益的特殊权利有哪些](../virtual-machines/windows/hybrid-use-benefit-licensing.md)？

这种转换不会造成停机；可以使用门户、ARM、PowerShell、Azure CLI 或 T-SQL 启动此转换。 请参阅[管理单一数据库](sql-database-single-database-resources.md)和[管理弹性池](sql-database-elastic-pool.md)。

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>我正在使用非正式版高级 RS 数据库 - 是否可将其升级到新层，从而获得类似的性价比优势？
由于 vCore 模型允许单独控制预配的计算和存储量，因此，你可以更有效地控制最终成本，使该模型成为高级 RS 数据库的有吸引力目标。 此外，使用基于 vCore 的模型时，[面向 SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)提供大幅的折扣。 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>可以按何种频率调整每个池的资源？
次数随意。 请参阅[管理弹性池](sql-database-elastic-pool.md)。

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>更改单一数据库的服务层或性能级别，或将数据库移入和移出弹性池需要多长时间？
更改数据库的服务层和移入和移出池需要在平台上以后台操作的形式复制数据库。 更改服务层可能需要几分钟至几小时的时间，具体取决于数据库的大小。 在这两种情况下，数据库在移动期间保持联机和可用。 有关更改单一数据库的详细信息，请参阅[更改数据库的服务层](sql-database-service-tiers-dtu.md)。 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>何时应该使用单一数据库或弹性数据库？
一般而言，弹性池针对典型的[软件即服务 (SaaS) 应用程序模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)而设计，该模式中每个客户或租户有一个数据库。 购买单独的数据库并超量设置以满足每个数据库的可变和峰值需求通常不够经济高效。 使用池可以管理池的整体性能，数据库会自动纵向扩展和缩减。 如果 Azure 的智能引擎发现了担保的使用模式，将为数据库建议池。 有关详细信息，请参阅[弹性池指南](sql-database-elastic-pool.md)。

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>使用基于 DTU 的购买模型的 SQL 数据库使用情况如何体现在我的帐单上？
SQL 数据库根据[购买模型](sql-database-service-tiers-dtu.md)按可预测的小时费率计费。 实际使用量是每小时按比例计算的，因此帐单可能会显示一小时的分数。 例如，如果某个数据库在一个月内存在了 12 小时，则帐单会显示 0.5 天的使用量。 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>如果单一数据库活动的时间少于一小时，或使用更高服务层的时间少于一小时，会发生什么情况？
需要支付使用最高服务层数据库存在的时数 + 在该小时适用的性能级别，无论使用方式或数据库的活动状态是否少于一小时。 例如，如果创建了单一数据库，五分钟后删除了它，则将按该数据库存在一小时收费。 

示例:

* 如果创建了一个基本数据库并立即将其升级为标准版 S1，则第一小时将按标准版 S1 费率收费。
* 如果从晚上 10:00 开始将数据库从“基本”升级到“高级”， 并且在第二天凌晨 1:35 完成升级， 那么会从凌晨 1:00 开始按高级版费率收费。 
* 如果在上午 11:00 将数据库从“高级”降级到“基本”级别， 并且在下午 2:15 完成降级，则会针对数据库以高级版费率收取到下午 3:00，之后以基本版费率收费。

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>使用基于 DTU 的购买模型的弹性池使用情况如何体现在我的帐单上？
在帐单上，弹性池费用显示为弹性 DTU (eDTU) 或 vCore 数加上存储量，并在[定价页](https://azure.microsoft.com/pricing/details/sql-database/)上递增显示。 弹性池没有按照数据库收取的费用。 对于池存在的每个小时，需要支付最高的 eDTU 或 vCore 费用，无论使用量是多少，也不管池处于活动状态的时间是否小于一小时。 

基于 DTU 的购买模型示例：

* 如果在上午 11:18 以 200 eDTU 创建标准弹性池，同时将五个数据库添加到池，则从上午 11:00 开始以 200 eDTU 收取整小时的费用。 到当天剩余的时间。
* 在第 2 天上午 5:05，数据库 1 开始使用 50 eDTU 并稳定持有一天。 数据库 2-5 在 0 和 80 eDTU 之间波动。 在当天，添加全天使用不同 eDTU 的其他五个数据库。 第 2 天将全天以 200 eDTU 计费。 
* 在第 3 天的上午 5:00， 添加了另外 15 个数据库。 数据库使用量全天增加，到下午 8:05 确定将池的 eDTU 从 200 增加为 400。 以 200 eDTU 收费生效到下午 8:00，并在其余 4 小时增加到 400 eDTU。 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>如何根据基于 DTU 的购买模型计收弹性池费用？
弹性池按以下特征计费：

* 弹性池一创建即计费，即使池中没有数据库。
* 弹性池按小时计费。 该计量频率与单一数据库性能级别的计量频率相同。
* 如果调整弹性池的大小，则在调整操作完成之前，不会按新的资源量计费。 这种计费所遵循的模式与更改单一数据库的性能级别所遵循的模式相同。
* 弹性池的价格取决于池的资源。 弹性池的价格与池内弹性数据库的数目和使用率无关。

有关详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)、[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型（预览版）](sql-database-service-tiers-vcore.md)。

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>基于 vCore 的使用情况如何体现在我的帐单上？ 
在基于 vCore 的模型中，服务根据中 vCore 预配的计算、预配的存储（GB/月）及使用的备份存储，按可预测的小时费率计费。 如果备份存储超过数据库总大小（即，数据库大小的 100%），则会产生额外的费用。 vCore 小时数、配置的数据库存储、消耗的 IO 和备份存储清楚地在帐单中逐项列出，以方便你查看所用资源的详细信息。 包含最大数据库大小的至多 100% 的备份存储，超过后按每月使用的 GB 数计费。

例如：
- 如果 SQL 数据库在一个月内存在 12 小时，帐单将显示使用 vCore 12 小时的费用。 如果 SQL 数据库预配了 100 GB 的附加存储，则帐单会以 GB/月为单位显示按小时分摊的存储使用情况及一个月内使用的 IO 数。
- 如果 SQL 数据库运行时间少于 1 小时，则根据该小时内所用的最高服务层和预配存储和 IO 来计算数据库每小时运行的费用，而不考虑使用情况或数据库运行时间是否少于 1 小时。
- 如果创建托管实例并在 5 分钟后删除，则按 1 数据库小时收费。
- 如果在“常规用途”层中创建一个具有 8 个 vCore 的托管实例，并立即将其升级为 16 个 vCore，则第一个小时按 16 个 vCore 收费。

> [!NOTE]
> 在 2018 年 6 月 30 日之前的有限期内，将不收取备份费用和 IO 费用。

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>如何在我的帐单上体现弹性池中活动异地复制的使用？
与单一数据库不同的是，对弹性数据库使用[活动异地复制](sql-database-geo-replication-overview.md)对计费没有直接的影响。  只需支付对每个池（主池和辅助池）预配的资源费用

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>使用审核功能将对我的帐单产生什么影响？
审核功能内置在 SQL 数据库服务中，不另行收费，适用于所有服务层。 但是，为了存储审核日志，审核功能将使用 Azure 存储帐户，而 Azure 存储中的表和队列费率根据审核日志的大小来应用。

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>如何重置服务器管理员的密码？
在 [Azure 门户](https://portal.azure.com)中，单击“SQL Server”，从列表中选择服务器，并单击“重置密码”。

## <a name="how-do-i-manage-databases-and-logins"></a>如何管理数据库和登录名？
请参阅[管理数据库和登录名](sql-database-manage-logins.md)。

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>如何确保只允许经过授权的 IP 地址访问服务器？
请参阅[如何：在 SQL 数据库上配置防火墙设置](sql-database-configure-firewall-settings.md)。

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>在相同的 Azure 地理位置内的两个区域之间进行异地复制数据库时，哪些是预期的复制延迟？
目前支持 5 秒的 RPO，并且只要地域辅助数据库承载于 Azure 建议的配对区域并且属于相同的服务层，则复制延迟就会少于该时间。

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>在主数据库所在的同一区域中创建地域辅助数据库时，哪些是预期的复制滞后？
根据经验数据，在使用 Azure 建议的配对区域时，内部区域和区域之间的复制延迟没有太多差别。 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>如果两个区域之间存在网络故障，那么在设置了异地复制的情况下，重试逻辑是如何运作的？
如果存在断开连接，我们会每 10 秒钟重试一次以重新建立连接。

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>为了保证复制主数据库上的关键更改，我该做什么？
地域辅助数据库是非同步副本，我们不尝试将其与主数据库保持完全同步。 不过，我们提供一种强制同步的方法，确保复制关键更改（例如密码更新）。 强制同步会阻止调用线程，直到所有提交的事务得到复制，因此会影响性能。 有关详细信息，请参阅 [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx)。 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>哪些工具可用于监视主数据库与地域辅助数据库之间的复制延迟？
我们通过 DMV 显示主数据库与地域辅助数据库之间的实时复制延迟。 有关详细信息，请参阅 [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx)。

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>将数据库移到同一订阅中的不同服务器
在 [Azure 门户](https://portal.azure.com)中，单击“SQL 数据库”，从列表中选择数据库，并单击“复制”。 有关详细信息，请参阅[复制 Azure SQL 数据库](sql-database-copy.md)。

## <a name="to-move-a-database-between-subscriptions"></a>在订阅之间移动数据库
在 [Azure 门户](https://portal.azure.com)中，单击“SQL Server”，并从列表中选择托管数据库的服务器。 单击“**移动**”，并选择要移动的资源以及要移动到其中的订阅。
