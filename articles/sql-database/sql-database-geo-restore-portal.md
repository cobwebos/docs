<properties
	pageTitle="从异地冗余备份还原 Azure SQL 数据库（Azure 门户）。| Azure"
	description="从异地冗余备份异地还原 Azure SQL 数据库（Azure 门户）。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.date="06/17/2016"
	wacn.date="" />


# 使用 Azure 门户从异地冗余备份异地还原 Azure SQL 数据库


> [AZURE.SELECTOR]
- [概述](/documentation/articles/sql-database-geo-restore)
- [Azure 门户](/documentation/articles/sql-database-geo-restore-portal)
- [PowerShell](/documentation/articles/sql-database-geo-restore-powershell)

本文演示了如何使用 Azure 门户通过异地还原将数据库还原到新服务器中。

## 选择要还原的数据库

要在 Azure 门户中还原数据库，请执行以下操作：

1.	打开 [Azure 门户](https://portal.azure.cn)。
2.  在屏幕左侧选择“新建”>“数据和存储”>“SQL 数据库”。
3.  选择“备份”作为源，然后选择要从中进行恢复的异地冗余备份。

    ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  指定数据库名称、要将数据库还原到其中的服务器，然后单击“创建”：

## 后续步骤

- 有关如何使用 Azure 门户从异地冗余备份还原 Azure SQL 数据库的详细步骤，请参阅[使用 Azure 门户进行异地还原](/documentation/articles/sql-database-geo-restore-portal)
- 有关从异地冗余备份还原 Azure SQL 数据库的详细信息，请参阅[使用 PowerShell 进行异地还原](/documentation/articles/sql-database-geo-restore)
- 有关如何在中断后进行恢复的完整讨论，请参阅[在中断后恢复](/documentation/articles/sql-database-disaster-recovery)

## 其他资源

- [业务连续性方案](/documentation/articles/sql-database-business-continuity-scenarios)


<!---HONumber=Mooncake_0711_2016-->