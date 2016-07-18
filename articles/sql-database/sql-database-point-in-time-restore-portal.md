<properties
	pageTitle="将 Azure SQL 数据库还原到之前的时间点（Azure 门户）| Azure"
	description="将 Azure SQL 数据库还原到之前的时间点。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.date="06/17/2016"
	wacn.date="05/23/2016"/>


# 使用 Azure 门户将 Azure SQL 数据库还原到之前的时间点


> [AZURE.SELECTOR]
- [概述](/documentation/articles/sql-database-point-in-time-restore)
- [Azure 门户](/documentation/articles/sql-database-point-in-time-restore-portal)
- [PowerShell](/documentation/articles/sql-database-point-in-time-restore-powershell)

本文将向你说明如何使用 Azure 门户将数据库从 [SQL 数据库自动备份](/documentation/articles/sql-database-automated-backups)还原到以前的时间点。

## 选择要还原到之前时间点的数据库

要在 Azure 门户中还原数据库，请执行以下操作：

1.	打开 [Azure 门户](https://portal.azure.cn)。
2.  在屏幕左侧选择“浏览”>“SQL 数据库”。
3.  导航到你要还原的数据库并选择它。
4.  在数据库边栏选项卡的顶部，选择“还原”。

    ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  指定数据库名称和时间点，然后单击“确定”：

    ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## 后续步骤

- 如需使用 PowerShell 恢复到某个时间点的详细步骤，请参阅[使用 PowerShell 进行的时间点还原](/documentation/articles/sql-database-point-in-time-restore-powershell)。
- 有关如何使用 REST API 恢复到某个时间点的信息，请参阅 [Point-In-Time Restore using the REST API（使用 REST API 进行的时间点还原）](https://msdn.microsoft.com/zh-cn/library/azure/mt163685.aspx)。
- 有关时间点还原的概述，请参阅[时间点还原](/documentation/articles/sql-database-point-in-time-restore)。
- 有关如何从用户或应用程序错误进行恢复的完整讨论，请参阅[用户错误恢复](/documentation/articles/sql-database-user-error-recovery)。

## 其他资源

- [业务连续性方案](/documentation/articles/sql-database-business-continuity-scenarios)

<!---HONumber=Mooncake_0711_2016-->