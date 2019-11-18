---
title: 在 Azure Database for PostgreSQL - 单一服务器中进行备份和还原
description: 了解如何自动备份和还原 Azure Database for PostgreSQL 服务器 - 单一服务器。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 695c2da9313f768b3d176176ed677c63b5ad858e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143730"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>在 Azure Database for PostgreSQL - 单一服务器中进行备份和还原

Azure Database for PostgreSQL 可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 备份可以用来将服务器还原到某个时间点。 备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。

## <a name="backups"></a>备份

Azure Database for PostgreSQL 获取数据文件和事务日志的备份。 根据所支持的最大存储大小，我们可以进行完整备份和差异备份（4 TB 最大存储服务器）或快照备份（最多 16 TB 的存储服务器）。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认的备份保留期为七天。 可以选择将其配置为长达 35 天。 所有备份都使用 AES 256 位加密进行加密。

### <a name="backup-frequency"></a>备份频率

通常情况下，完整备份每周进行一次，对于最大支持存储空间为 4 TB 的服务器，每日执行一次差异备份。 对于支持高达 16 TB 存储空间的服务器，每一天至少发生一次快照备份。 在这两种情况下，事务日志备份每五分钟执行一次。 创建服务器后，立即计划完整备份的第一个快照。 在较大的还原服务器上，初始完整备份的时间可能更长。 新服务器可以还原到的最早时间点是完成初始完整备份的时间。 随着快照 instantanious，可将最多 16 TB 存储空间的服务器恢复到创建时间。

### <a name="backup-redundancy-options"></a>备份冗余选项

使用 Azure Database for PostgreSQL 时，可以灵活地在“常规用途”层和“内存优化”层中选择本地冗余或异地冗余备份存储。 当备份存储在异地冗余备份存储中时，这些备份不仅会存储在托管服务器所在的区域中，还会复制到[配对的数据中心](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)。 这样可以在发生灾难时提供更好的保护，并且可以将服务器还原到其他区域。 “基本”层仅提供本地冗余备份存储。

> [!IMPORTANT]
> 只能在服务器创建期间为备份配置本地冗余或异地冗余存储。 预配服务器以后，不能更改备份存储冗余选项。

### <a name="backup-storage-cost"></a>备份存储成本

Azure Database for PostgreSQL 最高可以提供 100% 的已预配服务器存储作为备份存储，不收取任何额外费用。 通常情况下，这适合将备份保留七天。 超出的备份存储使用量按每月每 GB 标准收费。

例如，如果为服务器预配了 250 GB，则有 250 GB 不额外收费的备份存储。 存储超出 250 GB 就会收费。

## <a name="restore"></a>还原

在 Azure Database for PostgreSQL 中进行还原时，会根据原始服务器的备份创建新的服务器。

可以使用两种类型的还原：

- **时间点还原**：可以与任一备份冗余选项配合使用，所创建的新服务器与原始服务器位于同一区域。
- **异地还原**：只能在已将服务器配置为进行异地冗余存储的情况下使用，用于将服务器还原到另一区域。

估计的恢复时间取决于若干因素，包括数据库大小、事务日志大小、网络带宽，以及在同一区域同时进行恢复的数据库总数。 恢复时间通常少于 12 小时。

> [!IMPORTANT]
> 删除的服务器无法还原。 如果删除服务器，则属于该服务器的所有数据库也会被删除且不可恢复。 为了防止服务器资源在部署后遭意外删除或意外更改，管理员可以利用[管理锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)。

### <a name="point-in-time-restore"></a>时间点还原

可以还原到备份保留期中的任意时间点，不管备份冗余选项如何。 新服务器在原始服务器所在的 Azure 区域中创建。 它在创建时，使用原始服务器在定价层、计算的代、vCore 数、存储大小、备份保留期和备份冗余选项方面的配置。

多种情况下可以使用时间点还原。 例如，用户意外删除了数据、删除了重要的表或数据库，或者应用程序因为缺陷而意外地使用错误数据覆盖了正确数据。

可能需要等到下一个事务日志备份进行后，才能还原到上一个五分钟内的某个时间点。

### <a name="geo-restore"></a>异地还原

如果已将服务器配置为进行异地冗余备份，则可将服务器还原到另一 Azure 区域，只要服务在该区域可用即可。 对于支持高达 16 TB 存储空间的服务器，只能在支持 16 TB 服务器的区域中还原异地备份。 查看[Azure Database for MySQL 的定价层](concepts-pricing-tiers.md)以获取受支持区域的列表。

当服务器因其所在的区域发生事故而不可用时，异地还原是默认的恢复选项。 如果区域中出现的大规模事件导致数据库应用程序不可用，可以根据异地冗余备份将服务器还原到任何其他区域中的服务器。 提取备份后，会延迟一段时间才会将其复制到其他区域中。 此延迟可能长达一小时，因此发生灾难时，会有长达 1 小时的数据丢失风险。

在异地还原过程中，可以更改的服务器配置包括计算的代、vCore、备份保持期和备份冗余选项。 不支持更改定价层（“基本”、“常规用途”或“内存优化”）或存储大小。

### <a name="perform-post-restore-tasks"></a>执行还原后任务

从任一恢复机制还原后，都应执行以下任务，然后用户和应用程序才能重新运行：

- 如果需要使用新服务器来替换原始服务器，则请将客户端和客户端应用程序重定向到新服务器
- 对于要进行连接的用户，请确保设置适当的服务器级防火墙规则
- 确保设置适当的登录名和数据库级权限
- 视情况配置警报

## <a name="next-steps"></a>后续步骤

- 了解如何使用  [Azure 门户](howto-restore-server-portal.md)进行还原。
- 了解如何使用  [Azure CLI](howto-restore-server-cli.md) 进行还原。
- 若要详细了解业务连续性，请参阅 [业务连续性概述](concepts-business-continuity.md)。
