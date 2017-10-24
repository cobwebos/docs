---
title: "Azure SQL 数据库常见问题解答 | Microsoft Docs"
description: "客户就云数据库、Azure SQL 数据库、Microsoft 的关系数据库管理系统 (RDBMS) 和云中“数据库即服务”经常提出的问题的解答。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 1da12abc-0646-43ba-b564-e3b049a6487f
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 02/07/2017
ms.author: sashan;carlrab
ms.openlocfilehash: 6ed02ead07c50b9a49e8868756b6f957d7b49b99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sql-database-faq"></a>SQL 数据库常见问题

## <a name="what-is-the-current-version-of-sql-database"></a>SQL 数据库的当前版本是多少？
SQL 数据库的当前版本是 V12。 V11 版本已停用。

## <a name="what-is-the-sla-for-sql-database"></a>什么是 SQL 数据库的 SLA？
我们保证至少在 99.99% 的时间内客户能够在其单一或弹性“基本”、“标准”或“高级”版 Microsoft Azure SQL 数据库与我们的 Internet 网关之间保持连接。 有关详细信息，请参阅 [SLA](http://azure.microsoft.com/support/legal/sla/)。

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>如何重置服务器管理员的密码？
在 [Azure 门户](https://portal.azure.com)中，单击“SQL Server”，从列表中选择服务器，并单击“重置密码”。

## <a name="how-do-i-manage-databases-and-logins"></a>如何管理数据库和登录名？
请参阅[管理数据库和登录名](sql-database-manage-logins.md)。

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>如何确保只允许经过授权的 IP 地址访问服务器？
请参阅[如何：在 SQL 数据库上配置防火墙设置](sql-database-configure-firewall-settings.md)。

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>SQL 数据库的使用情况如何体现在我的帐单上？
SQL 数据库以可预测的每小时费率收费，同时基于服务层 + 单一数据库的性能级别或每一弹性池的 eDTU。 实际使用量是每小时按比例计算的，因此帐单可能会显示一小时的分数。 例如，如果某个数据库在一个月内存在了 12 小时，则帐单会显示 0.5 天的使用量。 此外，在帐单中还会列出每个池的服务层 + 性能级别和 eDTU 数，以便更容易查看一个月内每个用户所用的数据库天数。

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>如果单一数据库活动的时间少于一小时，或使用更高服务层的时间少于一小时，会发生什么情况？
需要支付使用最高服务层数据库存在的时数 + 在该小时适用的性能级别，无论使用方式或数据库的活动状态是否少于一小时。 例如，如果创建了单一数据库，五分钟后删除了它，则将按该数据库存在一小时收费。 

示例

* 如果创建了一个基本数据库并立即将其升级为标准版 S1，则第一小时将按标准版 S1 费率收费。
* 如果从晚上 10:00 开始将数据库从“基本”升级到“高级”， 并且在第二天凌晨 1:35 完成升级， 那么会从凌晨 1:00 开始按高级版费率收费。 
* 如果在上午 11:00 将数据库从“高级”降级到“基本”级别， 并且在下午 2:15 完成降级，则会针对数据库以高级版费率收取到下午 3:00，之后以基本版费率收费。

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>弹性池的使用情况如何体现在我的帐单上，另外，更改每个池的 eDTU 会发生什么情况？
在帐单上，弹性池收费显示为弹性 DTU (eDTU)，并在[定价页](https://azure.microsoft.com/pricing/details/sql-database/)上以每一池 eDTU 递增显示。 弹性池没有按照数据库收取的费用。 对于池存在的每个小时，需要支付最高的 eDTU，无论使用量是多少，也不管池处于活动状态的时间少于一小时。 

示例

* 如果在上午 11:18 以 200 eDTU 创建标准弹性池，同时将五个数据库添加到池，则从上午 11:00 开始以 200 eDTU 收取整小时的费用。 到当天剩余的时间。
* 在第 2 天上午 5:05，数据库 1 开始使用 50 eDTU 并稳定持有一天。 数据库 2-5 在 0 和 80 eDTU 之间波动。 在当天，添加全天使用不同 eDTU 的其他五个数据库。 第 2 天将全天以 200 eDTU 计费。 
* 在第 3 天的上午 5:00， 添加了另外 15 个数据库。 数据库使用量全天增加，到下午 8:05 确定将池的 eDTU 从 200 增加为 400。 以 200 eDTU 收费生效到下午 8:00，并在其余 4 小时增加到 400 eDTU。 

## <a name="elastic-pool-billing-and-pricing-information"></a>弹性池计费和定价信息
弹性池按以下特征计费：

* 弹性池一创建即计费，即使池中没有数据库。
* 弹性池按小时计费。 该计量频率与单一数据库性能级别的计量频率相同。
* 如果将弹性池的大小调整为新的 eDTU 量，则在调整操作完成之前，不会按新的 eDTU 量计费。 这种计费所遵循的模式与更改单一数据库的性能级别所遵循的模式相同。
* 弹性池的价格取决于池的 eDTU 数。 弹性池的价格与池内弹性数据库的数目和使用率无关。
* 价格的计算公式为：（池 eDTU 的数量）x（每 eDTU 的单位价格）。

弹性池的 eDTU 单价高于同一服务层中单一数据库的 DTU 单价。 有关详细信息，请参阅 [SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/)。 

若要了解 eDTU 和服务层，请参阅 [SQL 数据库选项和性能](sql-database-service-tiers.md)。

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>如何在我的帐单上体现弹性池中活动异地复制的使用？
与单一数据库不同的是，对弹性数据库使用[活动异地复制](sql-database-geo-replication-overview.md)对计费没有直接的影响。  只需支付对每个池（主池和辅助池）预配的 eDTU 费用

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>使用审核功能将对我的帐单产生什么影响？
审核功能是 SQL 数据库服务的内置功能，无需另行付费，基本、标准、高级和高级 RS 数据库均提供此功能。 但是，为了存储审核日志，审核功能将使用 Azure 存储帐户，而 Azure 存储中的表和队列费率根据审核日志的大小来应用。

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>如何找到单一数据库和弹性池的正确服务层和性能级别？
有几个工具可供使用。 

* 对于本地数据库，请使用 [DTU 选型顾问](http://dtucalculator.azurewebsites.net/)，它会建议所需的数据库和 DTU，并为弹性池评估多个数据库。
* 如果单一数据库可因池受益，当 Azure 的智能引擎发现了担保的历史使用模式时，将建议使用弹性池。 请参阅[使用 Azure 门户监视和管理弹性池](sql-database-elastic-pool-manage-portal.md)。 有关如何自行进行数学计算的详细信息，请参阅[弹性池的价格和性能注意事项](sql-database-elastic-pool.md)
* 若要确定是否需要向上或向下调整单一数据库，请参阅[单一数据库的性能指南](sql-database-performance-guidance.md)。

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>可以按何种频率更改单一数据库的服务层或性能级别？
可以（在基本、标准、高级和高级 RS 之间）更改服务层或服务层内的性能级别（例如 S1 到 S2），次数随意。 对于早期版本的数据库，可以在 24 小时内更改服务层或性能级别总共四次。

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>可以按何种频率调整每个池的 eDTU？
次数随意。

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>更改单一数据库的服务层或性能级别，或将数据库移入和移出弹性池需要多长时间？
更改数据库的服务层和移入和移出池需要在平台上以后台操作的形式复制数据库。 更改服务层可能需要几分钟至几小时的时间，具体取决于数据库的大小。 在这两种情况下，数据库在移动期间保持联机和可用。 有关更改单一数据库的详细信息，请参阅[更改数据库的服务层](sql-database-service-tiers.md)。 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>何时应该使用单一数据库或弹性数据库？
一般而言，弹性池针对典型的[软件即服务 (SaaS) 应用程序模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)而设计，该模式中每个客户或租户有一个数据库。 购买单独的数据库并超量设置以满足每个数据库的可变和峰值需求通常不够经济高效。 使用池可以管理池的整体性能，数据库会自动扩展和收缩。 如果 Azure 的智能引擎发现了担保的使用模式，将为数据库建议池。 有关详细信息，请参阅[弹性池指南](sql-database-elastic-pool.md)。

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>具有高达备份存储的最大已设置数据库存储两倍的容量是什么意思？
备份存储是与用于[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)和[异地还原](sql-database-recovery-using-backups.md#geo-restore)的自动数据库备份关联的存储。 Microsoft Azure SQL 数据库提供了高达备份存储的最大已设置数据库存储两倍的容量，不需要支付额外的成本。 例如，如果有一个标准数据库实例并且设置的数据库大小为 250 GB，则会向你提供 500 GB 的备份存储并且不额外收费。 如果数据库超过提供的备份存储，则可以选择与 Azure 支持联系来缩短保留期，或针对按标准读取访问地域冗余存储 (RA-GRS) 费率计费的额外备份存储支付费用。 有关 RA-GRS 计费的更多信息，请参阅“存储定价详细信息”。

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>我正在从 Web/企业版迁移到新服务层，我需要了解哪些信息？
Azure SQL Web 和企业数据库现已停用。 基本、标准、高级、高级 RS 和弹性层将取代即将停用的 Web 和企业数据库。 

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
* 在 [Azure 门户](https://portal.azure.com)中，单击“SQL 数据库”，从列表中选择数据库，并单击“复制”。 有关详细信息，请参阅[复制 Azure SQL 数据库](sql-database-copy.md)。

## <a name="to-move-a-database-between-subscriptions"></a>在订阅之间移动数据库
* 在 [Azure 门户](https://portal.azure.com)中，单击“SQL Server”，并从列表中选择托管数据库的服务器。 单击“**移动**”，并选择要移动的资源以及要移动到其中的订阅。
