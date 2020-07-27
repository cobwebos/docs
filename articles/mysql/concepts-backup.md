---
title: 备份和还原 - Azure Database for MySQL
description: 了解如何自动备份和还原 Azure Database for MySQL 服务器。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: e28fc3c5779f2c31abbb48a7ced448cd8f92d1a2
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171849"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>在 Azure Database for MySQL 中进行备份和还原

Azure Database for MySQL 可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 备份可以用来将服务器还原到某个时间点。 备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。

## <a name="backups"></a>备份

Azure Database for MySQL 对数据文件和事务日志进行备份。 根据所支持的最大存储大小，我们可以进行完整备份和差异备份（4 TB 最大存储服务器）或快照备份（最多 16 TB 存储服务器）。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认的备份保留期为七天。 可以[选择将其配置](howto-restore-server-portal.md#set-backup-configuration)为长达 35 天。 所有备份都使用 AES 256 位加密进行加密。

这些备份文件不公开给用户，因此无法导出。 这些备份只能用于 Azure Database for MySQL 中的还原操作。 可以使用 [mysqldump](concepts-migrate-dump-restore.md) 复制数据库。

### <a name="backup-frequency"></a>备份频率

#### <a name="servers-with-up-to-4-tb-storage"></a>具有最多 4 TB 存储空间的服务器

对于支持最多 4 TB 存储空间的服务器，每周进行一次完整备份。 差异备份一天发生两次。 每五分钟执行一次事务日志备份。

#### <a name="servers-with-up-to-16-tb-storage"></a>具有高达 16 TB 存储空间的服务器
在[Azure 区域](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage)的子集中，所有新预配的服务器都可支持高达 16 TB 的存储。 这些大型存储服务器上的备份基于快照。 在创建服务器后立即计划第一次完整的快照备份。 此第一个完整快照备份将保留为服务器的基准备份。 后续的快照备份仅为差异备份。 

差异快照备份每天至少发生一次。 差异快照备份不按固定计划进行。 差异快照备份每24小时进行一次，除非事务日志（MySQL 中的 binlog）在上次差异备份后超过 50 GB。 一天内，最多允许6个差异快照。 

每五分钟执行一次事务日志备份。 

### <a name="backup-retention"></a>备份保留

根据服务器上的 "备份保留期" 设置保留备份。 你可以选择7到35天的保留期。 默认保持期为7天。 可以通过使用[Azure 门户](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal#set-backup-configuration)或[Azure CLI](https://docs.microsoft.com/azure/mysql/howto-restore-server-cli#set-backup-configuration)更新备份配置，在创建服务器或更高版本期间设置保留期。 

备份保留期控制可以往回检索多长时间的时间点还原，因为它基于可用备份。 备份保留期也可以从还原角度被视为恢复窗口。 在备份保留期内执行时间点还原所需的所有备份将保留在备份存储中。 例如，如果 "备份保留期" 设置为 "7 天"，则会将恢复时段视为 "最后7天"。 在这种情况下，将保留在过去7天内还原服务器所需的所有备份。 具有7天的备份保留时段：
- 具有 4 TB 存储的旧服务器将保留最多2个完整数据库备份、所有差异备份和自最早的完整数据库备份以来执行的事务日志备份。
-   具有大型存储空间（16 TB）的服务器将保留完整的数据库快照、所有差异快照和事务日志备份，过去8天。

### <a name="backup-redundancy-options"></a>备份冗余选项

使用 Azure Database for MySQL 时，可以灵活地在“常规用途”层和“内存优化”层中选择本地冗余或异地冗余备份存储。 当备份存储在异地冗余备份存储中时，这些备份不仅会存储在托管服务器所在的区域中，还会复制到[配对的数据中心](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 这样可以在发生灾难时提供更好的保护，并且可以将服务器还原到其他区域。 “基本”层仅提供本地冗余备份存储。

> [!IMPORTANT]
> 只能在服务器创建期间为备份配置本地冗余或异地冗余存储。 预配服务器以后，不能更改备份存储冗余选项。

### <a name="backup-storage-cost"></a>备份存储成本

Azure Database for MySQL 最高可以提供 100% 的已预配服务器存储作为备份存储，不收取任何额外费用。 使用的任何其他备份存储按每月 GB 的费率收费。 例如，如果你预配了一个具有 250 GB 存储空间的服务器，则可为服务器备份提供 250 GB 的额外存储，无需额外付费。 对于超过 250 GB 的备份，使用的存储按[定价模式](https://azure.microsoft.com/pricing/details/mysql/)收费。 

可以通过 Azure 门户使用 Azure Monitor 中的[备份存储使用](concepts-monitoring.md)的指标来监视服务器使用的备份存储空间。 "使用的备份存储" 指标表示根据为服务器设置的备份保留期保留的所有完整数据库备份、差异备份和日志备份占用的存储量。 备份的频率为服务管理，并在前面进行了说明。 服务器上的事务活动繁重会导致备份存储使用量增加，而不考虑总数据库大小。 对于异地冗余存储，备份存储使用情况是本地冗余存储的两倍。 

控制备份存储成本的主要方法是设置适当的备份保留期，并选择适当的备份冗余选项来满足所需的恢复目标。 可以选择 7 到 35 天的保留期。 常规用途和内存优化服务器可以选择使用异地冗余存储进行备份。

## <a name="restore"></a>还原

在 Azure Database for MySQL 中进行还原时，会根据原始服务器的备份创建新的服务器并还原服务器中包含的所有数据库。

可以使用两种类型的还原：

- **时间点还原**可通过任一备份冗余选项使用，并利用完整备份和事务日志备份的组合在原始服务器所在区域中创建一个新服务器。
- **异地还原**仅在你为服务器配置了异地冗余存储时可用，它允许你利用最近进行的备份将服务器还原到其他区域。

估计的恢复时间取决于若干因素，包括数据库大小、事务日志大小、网络带宽，以及在同一区域同时进行恢复的数据库总数。 恢复时间通常少于 12 小时。

> [!IMPORTANT]
> 已删除的服务器**无法**还原。 如果删除服务器，则属于该服务器的所有数据库也会被删除且不可恢复。 为了防止服务器资源在部署后遭意外删除或意外更改，管理员可以利用[管理锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

### <a name="point-in-time-restore"></a>时间点还原

可以还原到备份保留期中的任意时间点，不管备份冗余选项如何。 新服务器在原始服务器所在的 Azure 区域中创建。 它在创建时，使用原始服务器在定价层、计算的代、vCore 数、存储大小、备份保留期和备份冗余选项方面的配置。

多种情况下可以使用时间点还原。 例如，用户意外删除了数据、删除了重要的表或数据库，或者应用程序因为缺陷而意外地使用错误数据覆盖了正确数据。

可能需要等到下一个事务日志备份进行后，才能还原到上一个五分钟内的某个时间点。

### <a name="geo-restore"></a>异地还原

如果已将服务器配置为进行异地冗余备份，则可将服务器还原到另一 Azure 区域，只要服务在该区域可用即可。 支持多达 4 TB 存储的服务器可以还原到异地配对区域，也可以还原到支持高达 16 TB 存储的任何区域。 对于支持高达 16 TB 存储空间的服务器，还可以在支持 16 TB 服务器的任何区域还原异地备份。 查看 [Azure Database for MySQL 定价层](concepts-pricing-tiers.md)，以获取受支持区域的列表。

当服务器因其所在的区域发生事故而不可用时，异地还原是默认的恢复选项。 如果区域中出现的大规模事件导致数据库应用程序不可用，可以根据异地冗余备份将服务器还原到任何其他区域中的服务器。 异地还原利用服务器的最新备份。 提取备份后，会延迟一段时间才会将其复制到其他区域中。 此延迟可能长达一小时，因此发生灾难时，会有长达 1 小时的数据丢失风险。

在异地还原过程中，可以更改的服务器配置包括计算的代、vCore、备份保持期和备份冗余选项。 不支持在异地还原过程中更改定价层（“基本”、“常规用途”或“内存优化”）或存储大小。

### <a name="perform-post-restore-tasks"></a>执行还原后任务

从任一恢复机制还原后，都应执行以下任务，然后用户和应用程序才能重新运行：

- 如果需要使用新服务器来替换原始服务器，则请将客户端和客户端应用程序重定向到新服务器
- 对于要进行连接的用户，请确保设置适当的 VNet 规则。 不会从源服务器复制这些规则。
- 确保设置适当的登录名和数据库级权限
- 视情况配置警报

## <a name="next-steps"></a>后续步骤

- 若要详细了解业务连续性，请参阅 [业务连续性概述](concepts-business-continuity.md)。
- 若要使用 Azure 门户还原到某个时间点，请参阅 [使用 Azure 门户将服务器还原到某个时间点](howto-restore-server-portal.md)。
- 若要使用 Azure CLI 还原到某个时间点，请参阅 [使用 CLI 将服务器还原到某个时间点](howto-restore-server-cli.md)。
