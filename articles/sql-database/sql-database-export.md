---
title: "将 Azure SQL 数据库导出到 BACPAC 文件 | Microsoft Docs"
description: "使用 Azure 门户将 Azure SQL 数据库导出到 BACPAC 文件"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 7b96e8b144f329819596e3ff63e6febbbcc1ffb0
ms.lasthandoff: 03/18/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>将 Azure SQL 数据库或 SQL Server 数据库导出到 BACPAC 文件

本文说明如何将 Azure SQL 数据库或 SQL Server 数据库导出到 BACPAC 文件。 

> [!IMPORTANT]
> Azure SQL 数据库自动导出现在处于预览状态，将在 2017 年 3 月 1 日停用。 从 2016 年 12 月 1 日开始，你将无法再对任何 SQL 数据库配置自动导出。 所有现有自动导出作业将继续正常运行，直到 2017 年 3 月 1 日。 2016 年 12 月 1 日之后，你可以使用[长期备份保留](sql-database-long-term-retention.md)或 [Azure 自动化](../automation/automation-intro.md)，以根据所选计划定期使用 PowerShell 存档 SQL 数据库。 对于示例脚本，可以从 [Github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) 下载示例脚本。 
>

## <a name="overview"></a>概述

如果需要为存档或移动到其他平台而导出数据库，可将数据库架构和数据导出到 BACPAC 文件。 BACPAC 文件只是一个扩展名为 BACPAC 的 ZIP 文件。 之后可将 BACPAC 文件存储在 Azure blob 存储中或存储在本地位置的本地存储中，之后可以导入回 Azure SQL 数据库或 SQL Server 本地安装。 

* 可以使用 [Azure 门户](sql-database-export-portal.md)、[PowerShell](sql-database-export-powershell.md)、[SQLPackage](sql-database-export-sqlpackage.md) 或 [SQL Server Management Studio](sql-database-export-ssms.md) 导出 Azure SQL 数据库。
* 可以使用 [PowerShell](sql-database-export-powershell.md)、[SQLPackage](sql-database-export-sqlpackage.md) 或 [SQL Server Management Studio](sql-database-export-ssms.md) 导出 SQL Server 数据库。

> [!IMPORTANT]
> 如果将从 SQL Server 中导出作为迁移到 Azure SQL 数据库的准备，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。
> 

## <a name="considerations"></a>注意事项

* 为保证导出的事务处理方式一致，须确保导出期间未发生写入活动，或者正在从 Azure SQL 数据库的[事务处理方式一致性副本](sql-database-copy.md)中导出。
* 如果计划导出到 blob 存储，则 BACPAC 文件的最大大小为 200 GB。 若要存档更大的 BACPAC 文件，请导出到本地存储。
* 本文介绍的方法不支持将 BACPAC 文件导出到 Azure 高级存储。
* 如果从 Azure SQL 数据库的导出操作超过 20 个小时，则可能会取消操作。 为提高导出过程中的性能，你可以进行如下操作：
  * 暂时提高服务级别。
  * 在导出期间终止所有读取和写入活动。
  * 对所有大型表格上的非 null 值使用[聚集索引](https://msdn.microsoft.com/library/ms190457.aspx)。 如果不使用聚集索引，当时间超过 6-12 个小时时，导出可能会失败。 这是因为导出服务需要完成表格扫描，才能尝试导出整个表格。 确认表格是否针对导出进行优化的一个好方法是，运行 **DBCC SHOW_STATISTICS** 并确保 *RANGE_HI_KEY* 不是 null 并且值分布良好。 相关详细信息，请参阅 [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。

> [!NOTE]
> BACPAC 不能用于备份和还原操作。 Azure SQL 数据库会自动为每个用户数据库创建备份。 有关详细信息，请参阅[业务连续性概述](sql-database-business-continuity.md)和 [SQL 数据库备份](sql-database-automated-backups.md)。  
> 


## <a name="next-steps"></a>后续步骤

* 有关对于整个 SQL Server 数据库迁移进程的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。
* 有关在 Azure 内部复制数据库的概述，还可参阅[复制 Azure SQL 数据库](sql-database-copy.md)。
* 可以使用 [Azure 门户](sql-database-copy-portal.md)、[PowerShell](sql-database-copy-powershell.md) 或 [Transact-SQL](sql-database-copy-transact-sql.md) 在 Azure 内部复制 Azure SQL 数据库。 

