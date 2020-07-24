---
title: 备份和还原–超大规模（Citus）-Azure Database for PostgreSQL
description: 防止数据意外损坏或删除
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 22bc3d6efca24a88b28217b2e06ac79d33f16b2e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030073"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-超大规模（Citus）中的备份和还原

Azure Database for PostgreSQL –超大规模（Citus）自动创建每个节点的备份，并将其存储在本地冗余存储中。 备份可用于将超大规模（Citus）群集还原到指定时间。 备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。

## <a name="backups"></a>备份

至少一天，Azure Database for PostgreSQL 会对数据文件和数据库事务日志进行快照备份。 利用备份，你可以将服务器还原到保留期内的任何时间点。 （对于所有群集，保持期目前为35天。）使用 AES 256 位加密对所有备份进行加密。

在支持可用性区域的 Azure 区域中，备份快照存储在三个可用性区域中。 只要至少有一个可用性区域处于联机状态，超大规模（Citus）群集就是可恢复的。

无法导出备份文件。 它们只能在 Azure Database for PostgreSQL 中用于还原操作。

### <a name="backup-storage-cost"></a>备份存储成本

有关当前备份存储的定价，请参阅 Azure Database for PostgreSQL-超大规模（Citus）[定价页](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)。

## <a name="restore"></a>还原

在 Azure Database for PostgreSQL 中，还原超大规模（Citus）群集将从原始节点的备份创建新群集。 

> [!IMPORTANT]
>只能在同一订阅和资源组中以及使用不同的群集名称还原超大规模群集。


> [!IMPORTANT]
> 无法还原已删除的超大规模（Citus）群集。 如果删除群集，则属于该群集的所有节点都将被删除且无法恢复。 若要保护群集资源、部署后、意外删除或意外更改，管理员可以利用[管理锁](/azure/azure-resource-manager/management/lock-resources)。

### <a name="point-in-time-restore-pitr"></a>时间点还原 (PITR)

你可以将群集还原到过去35天内的任何时间点。
多种情况下可以使用时间点还原。 例如，当用户意外地删除数据、删除重要的表或数据库时，或者如果应用程序意外地使用错误数据覆盖了正确的数据。

还原过程会在与原始群集相同的 Azure 区域、订阅和资源组中创建新群集。 群集具有原始配置：相同数量的节点、Vcore 数、存储大小、用户角色、PostgreSQL 版本和 Citus 扩展的版本。

不会从原始服务器组中保留防火墙设置和 PostgreSQL 服务器参数，它们会重置为默认值。 防火墙将阻止所有连接。 还原后需要手动调整这些设置。

> [!IMPORTANT]
> 需要打开支持请求来执行超大规模（Citus）群集的时点还原。

### <a name="post-restore-tasks"></a>还原后任务

从任一恢复机制还原后，你应执行以下操作，使你的用户和应用程序恢复正常运行：

* 如果需要使用新服务器来替换原始服务器，则请将客户端和客户端应用程序重定向到新服务器
* 对于要进行连接的用户，请确保设置适当的服务器级防火墙规则和 VNet 规则。 这些规则不会从原始服务器组复制。
* 根据需要调整 PostgreSQL 服务器参数。 参数不会从原始服务器组复制。
* 确保设置适当的登录名和数据库级权限
* 视情况配置警报

## <a name="next-steps"></a>后续步骤

* 了解 [Azure 可用性区域](/azure/availability-zones/az-overview)。
* 针对超大规模（Citus）服务器组设置 [建议的警报](/azure/postgresql/howto-hyperscale-alert-on-metric#suggested-alerts)。
