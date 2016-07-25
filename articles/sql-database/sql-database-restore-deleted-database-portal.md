<properties
	pageTitle="还原已删除的 Azure SQL 数据库（Azure 门户）| Azure"
	description="还原已删除的 Azure SQL 数据库（Azure 门户）。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.date="06/09/2016"
	wacn.date="05/23/2016" />


# 使用 Azure 门户还原已删除的 Azure SQL 数据库

> [AZURE.SELECTOR]

- [概述](/documentation/articles/sql-database-restore-deleted-database/)
- [Azure 门户](/documentation/articles/sql-database-restore-deleted-database-portal/)
- [PowerShell](/documentation/articles/sql-database-restore-deleted-database-powershell/)



## 选择要还原的数据库 

要在 Azure 门户中还原数据库，请执行以下操作：

1.	打开 [Azure 门户](https://portal.azure.cn)。
2.  在屏幕左侧选择“浏览”>“SQL 服务器”。
3.  导航到具有你要还原的已删除数据库的服务器并选择该服务器
4.  向下滚动到服务器边栏选项卡的“操作”部分并选择“已删除的数据库”：
	![还原 Azure SQL 数据库](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  选择要还原的已删除数据库。
6.  指定数据库名称，然后单击“确定”：

    ![还原 Azure SQL 数据库](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## 后续步骤


- 有关如何使用 PowerShell 还原已删除的数据库的详细步骤，请参阅 [使用 PowerShell 还原已删除的数据库](/documentation/articles/sql-database-restore-deleted-database-powershell/)。
- 有关如何还原已删除的数据库的信息，请参阅 [Restore a deleted database using the REST API（使用 REST API 还原已删除的数据库）](https://msdn.microsoft.com/zh-cn/library/azure/mt163685.aspx)。
- 有关还原已删除的数据库的详细信息，请参阅[还原已删除的数据库](/documentation/articles/sql-database-restore-deleted-database/)。
- 有关 Azure SQL 数据库自动备份的详细信息，请参阅 [SQL 数据库自动备份](/documentation/articles/sql-database-automated-backups/)。

## 其他资源

- [时间点还原](/documentation/articles/sql-database-point-in-time-restore/)
- [业务连续性概述](/documentation/articles/sql-database-business-continuity/)
- [异地还原](/documentation/articles/sql-database-geo-restore/)
- [活动异地复制](/documentation/articles/sql-database-geo-replication-overview/)
- [设计用于云灾难恢复的应用程序](/documentation/articles/sql-database-designing-cloud-solutions-for-disaster-recovery/)




<!---HONumber=Mooncake_0718_2016-->