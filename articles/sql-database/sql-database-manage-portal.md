<properties
	pageTitle="使用 Azure 门户管理 Azure SQL 数据库"
	description="了解如何使用 Azure 门户管理云中的关系数据库。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.date="05/20/2016"
	wacn.date="06/02/2016"/>


# 使用 Azure 门户管理 Azure SQL 数据库


> [AZURE.SELECTOR]
- [Azure 门户](/documentation/articles/sql-database-manage-portal)
- [SSMS](/documentation/articles/sql-database-manage-azure-ssms)
- [PowerShell](/documentation/articles/sql-database-command-line-tools)

在 [Azure 门户](https://portal.azure.cn)中，你可以创建、监视和管理 Azure SQL 数据库与逻辑服务器。本文重点介绍一些比较常见的任务。

![数据库概述](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\.数据库管理操作

![数据库管理操作](./media/sql-database-manage-portal/sqldatabase_actions.png)

Azure 门户提供了一系列的通用数据库操作，你可以在数据库边栏选项卡的顶部访问这些操作选项。你可以将数据库还原到以前的某个时间点，在 Visual Studio 中打开数据库，将数据库复制到新服务器，以及将该数据库导出到 Azure 存储帐户。

- [还原 SQL 数据库](/documentation/articles/sql-database-user-error-recovery)
- [在 Visual Studio 中打开 SQL 数据库](/documentation/articles/sql-database-connect-query)
- [导出 SQL 数据库](/documentation/articles/sql-database-export)

## 2\.数据库监视

![数据库监视](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

默认情况下，Azure SQL 数据库会根据数据库事务单位 (DTU)、数据库大小和连接运行状况提供监视图表。你可以自定义和扩展这些监视图表，以进一步绘制 CPU 百分比、数据 IO 百分比、死锁、日志 IO 百分比甚至防火墙阻止的请求百分比图表。

此外，可以设置警报规则以监视指定的指标，并在达到预设的阈值时通知指定的管理员和协同管理员。

## 3\.数据库安全和审核

![数据库安全](./media/sql-database-manage-portal/sqldatabase_security.png)

可将Azure SQL 数据库配置为跟踪所有数据库事件，并将这些事件写入 Azure 存储帐户中的审核日志。此功能可帮助你一直保持遵从法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差。

- [SQL 数据库审核](/documentation/articles/sql-database-auditing-get-started)

还可以将 Azure SQL 数据库配置为对非特权用户屏蔽敏感数据。

- [动态数据屏蔽](/documentation/articles/sql-database-dynamic-data-masking-get-started)


## 4\.异地复制

![异地复制](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

可将 Azure SQL 数据库配置为以异步方式将提交的事务复制到辅助数据库。在门户的异地复制部分，你可以选择需要安放辅助数据库的 Azure 区域。

- [异地复制](/documentation/articles/sql-database-geo-replication-overview)



## 其他资源

- [SQL 数据库](/documentation/articles/sql-database-technical-overview)

<!---HONumber=Mooncake_0711_2016-->