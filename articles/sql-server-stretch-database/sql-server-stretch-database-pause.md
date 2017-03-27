---
title: "暂停和恢复数据迁移（Stretch Database）| Microsoft 文档"
description: "了解如何暂停或继续将数据迁移到 Azure。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>暂停和恢复数据迁移（Stretch Database）
若要暂停或继续将数据迁移到 Azure，请在 SQL Server Management Studio 中选择某个表对应的“**延伸**”，然后选择“**暂停**”以暂停数据迁移，或选择“**恢复**”以恢复数据迁移。 也可以使用 Transact\-SQL 来暂停或恢复数据迁移。

当你想要排查本地服务器上的问题或者最大程度地提供可用网络带宽时，可以暂停单个表的数据迁移。

## <a name="pause-data-migration"></a>暂停数据迁移
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>使用 SQL Server Management Studio 暂停数据迁移
1. 在 SQL Server Management Studio 的对象资源管理器中，选择你要暂停其数据迁移的、已启用延伸的表。
2. \-右键单击并选择“**延伸**”，然后选择“**暂停**”。

### <a name="use-transact-sql-to-pause-data-migration"></a>使用 Transact\-SQL 暂停数据迁移
运行以下命令。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>恢复数据迁移
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>使用 SQL Server Management Studio 恢复数据迁移
1. 在 SQL Server Management Studio 的对象资源管理器中，选择你要恢复其数据迁移的、已启用延伸的表。
2. \-右键单击并选择“**延伸**”，然后选择“**恢复**”。

### <a name="use-transact-sql-to-resume-data-migration"></a>使用 Transact\-SQL 恢复数据迁移
运行以下命令。

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>检查迁移处于活动状态还是已暂停
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>使用 SQL Server Management Studio 检查迁移处于活动状态还是已暂停
在 SQL Server Management Studio 中，打开 **Stretch Database 监视器**并检查**迁移状态**列的值。 有关详细信息，请参阅[数据迁移的监视和故障排除](sql-server-stretch-database-monitor.md)。

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>使用 ransact-SQL 检查迁移处于活动状态还是已暂停
查询目录视图 **sys.remote_data_archive_tables** 并检查 **is_migration_paused** 列的值。 有关详细信息，请参阅 [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx)。

## <a name="see-also"></a>另请参阅
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[数据迁移的监视和故障排除](sql-server-stretch-database-monitor.md)




<!--HONumber=Nov16_HO3-->


