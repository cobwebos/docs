<properties
   pageTitle="云业务连续性 - 还原已删除的数据库 - SQL 数据库 | Azure"
   description="了解有关时间点还原的信息，它让你能够将 Azure SQL 数据库回滚到之前的时间点（最多 35 天）。"
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.date="06/09/2016"
   wacn.date=""/>

# 概述：还原已删除的 Azure SQL 数据库

> [AZURE.SELECTOR]
- [业务连续性概述](/documentation/articles/sql-database-business-continuity)
- [时间点还原](/documentation/articles/sql-database-point-in-time-restore)
- [还原已删除的数据库](/documentation/articles/sql-database-restore-deleted-database)
- [异地还原](/documentation/articles/sql-database-geo-restore)
- [活动异地复制](/documentation/articles/sql-database-geo-replication-overview)
- [业务连续性方案](/documentation/articles/sql-database-business-continuity-scenarios)


你可以根据自己的[服务层](/documentation/articles/sql-database-service-tiers)，在 [SQL 数据库自动备份](/documentation/articles/sql-database-automated-backups)的保留期内还原已删除的数据库。为此，可以使用 [Azure 门户](/documentation/articles/sql-database-restore-deleted-database-portal)、[PowerShell](/documentation/articles/sql-database-restore-deleted-database-powershell) 或 [REST API](https://msdn.microsoft.com/zh-cn/library/azure/mt163685.aspx)。

> [AZURE.SELECTOR]
- [Azure 门户](/documentation/articles/sql-database-restore-deleted-database-portal)
- [PowerShell](/documentation/articles/sql-database-restore-deleted-database-powershell)

## 还原最近删除的数据库

可以使用相同或不同的数据库名称，将已删除的数据库还原到包含原始数据库的逻辑服务器。对于已删除的数据库，还原点固定为数据库的删除时间。

> [AZURE.IMPORTANT] 如果删除 Azure SQL 数据库服务器实例，其所有数据库也会一并删除，并且无法恢复。

## 还原时间

还原数据库所需的时间取决于许多因素，其中包括数据库的大小、事务日志数、选择的时间点和需重播以重构所选点时状态的活动量。对于非常大和/或活动的数据库，还原可能要花费几个小时。还原数据库始终会在原始数据库的同一服务器上创建新的数据库，因此必须为还原的数据库指定新的名称。大部分数据库还原操作可在 12 小时内完成。

## 摘要

自动备份可以保护数据库，防范数据库被意外删除。所有 SQL 数据库均适用此零成本、零管理的解决方案。

## 后续步骤

- 有关如何使用 Azure 门户还原已删除的数据库的详细步骤，请参阅 [Restore a deleted database using the Azure Portal（使用 Azure 门户还原已删除的数据库）](/documentation/articles/sql-database-restore-deleted-database-portal)。
- 有关如何使用 PowerShell 还原已删除的数据库的详细步骤，请参阅 [Restore a deleted database using PowerShell（使用 PowerShell 还原已删除的数据库）](/documentation/articles/sql-database-restore-deleted-database-powershell)。
- 有关如何还原已删除的数据库的信息，请参阅 [Restore a deleted database using the REST API（使用 REST API 还原已删除的数据库）](https://msdn.microsoft.com/zh-cn/library/azure/mt163685.aspx)。
- 有关 Azure SQL 数据库自动备份的详细信息，请参阅 [SQL Database automated backups（SQL 数据库自动备份）](/documentation/articles/sql-database-automated-backups)。

## 其他资源

- [时间点还原](/documentation/articles/sql-database-point-in-time-restore)
- [业务连续性概述](/documentation/articles/sql-database-business-continuity)
- [异地还原](/documentation/articles/sql-database-geo-restore)
- [活动异地复制](/documentation/articles/sql-database-geo-replication-overview)
- [设计用于云灾难恢复的应用程序](/documentation/articles/sql-database-designing-cloud-solutions-for-disaster-recovery)

<!---HONumber=Mooncake_0704_2016-->