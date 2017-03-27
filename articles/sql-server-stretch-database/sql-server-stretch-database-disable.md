---
title: "禁用 Stretch Database 和移回远程数据 | Microsoft 文档"
description: "了解如何为表禁用 Stretch Database 并选择性地移回远程数据。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 08291748-0dfd-4a7d-a6a4-a5618e9c248d
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a140e89058f6a246e80b71248b82d0f6767337cb


---
# <a name="disable-stretch-database-and-bring-back-remote-data"></a>禁用 Stretch Database 和移回远程数据
若要为某个表禁用 Stretch Database，请在 SQL Server Management Studio 中选择该表对应的“**延伸**”。 然后选择以下选项之一。

* **禁用 | 从 Azure 移回数据**。 将表的远程数据从 Azure 复制回到 SQL Server，然后为该表禁用 Stretch Database。 此操作会产生数据传输费用，并且不可取消。
* **禁用 | 将数据保留在 Azure 中**。 为表禁用 Stretch Database。  放弃表的远程数据，使其保留在 Azure 中。

也可以使用 Transact\-SQL 为表或数据库禁用 Stretch Database。

为表禁用 Stretch Database 之后，数据迁移将会停止，查询结果将不再包括来自远程表的结果。

如果你只是想要暂停数据迁移，请参阅[暂停和恢复 Stretch Database](sql-server-stretch-database-pause.md)。

> [!NOTE]
> 针对表或数据库禁用 Stretch Database 不会删除远程对象。 若要删除远程表或远程数据库，必须使用 Azure 管理门户。 远程对象在删除之前，会持续产生 Azure 费用。 有关详细信息，请参阅 [SQL Server Stretch Database 定价](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。
> 
> 

## <a name="disable-stretch-database-for-a-table"></a>为表禁用 Stretch Database
### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>使用 SQL Server Management Studio 为表禁用 Stretch Database
1. 在 SQL Server Management Studio 的对象资源管理器中，选择你要为其禁用 Stretch Database 的表。
2. \-右键单击并选择“**延伸**”，然后选择以下选项之一。
   
   * **禁用 | 从 Azure 移回数据**。 将表的远程数据从 Azure 复制回到 SQL Server，然后为该表禁用 Stretch Database。 此命令不可取消。
     
     > [!NOTE]
     > 将表的远程数据从 Azure 复制回 SQL Server 会产生数据传输费用。 有关详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)。
     > 
     > 
     
     将所有远程数据从 Azure 复制回到 SQL Server 之后，将为表禁用延伸。
   * **禁用 | 将数据保留在 Azure 中**。 为表禁用 Stretch Database。  放弃表的远程数据，使其保留在 Azure 中。
   
   > [!NOTE]
   > 针对表禁用 Stretch Database 不会删除远程数据或远程表。 若要删除远程表，必须使用 Azure 管理门户。 远程表在删除之前，会持续产生 Azure 费用。 有关详细信息，请参阅 [SQL Server Stretch Database 定价](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。
   > 
   > 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>使用 Transact\-SQL 为表禁用 Stretch Database
* 若要针对某个表禁用 Stretch 并将该表的远程数据从 Azure 复制回 SQL Server，请运行以下命令。 将所有远程数据从 Azure 复制回到 SQL Server 之后，将为表禁用延伸。
  
  此命令不可取消。
  
  ```tsql
  USE <Stretch-enabled database name>;
  GO
  ALTER TABLE <Stretch-enabled table name>  
     SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
  GO
  ```
  > [!NOTE]
  > 将表的远程数据从 Azure 复制回 SQL Server 会产生数据传输费用。 有关详细信息，请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)。
  > 
  > 
* 若要为某个表禁用延伸并放弃远程数据，请运行以下命令。
  
  ```tsql
  ALTER TABLE <table_name>
     SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
  ```

> [!NOTE]
> 针对表禁用 Stretch Database 不会删除远程数据或远程表。 若要删除远程表，必须使用 Azure 管理门户。 远程表在删除之前，会持续产生 Azure 费用。 有关详细信息，请参阅 [SQL Server Stretch Database 定价](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。
> 
> 

## <a name="disable-stretch-database-for-a-database"></a>为数据库禁用 Stretch Database
在为某个数据库禁用 Stretch Database 之前，必须对该数据库中已启用延伸的每个表禁用 Stretch Database。

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>使用 SQL Server Management Studio 为数据库禁用 Stretch Database
1. 在 SQL Server Management Studio 的对象资源管理器中，选择你要为其禁用 Stretch Database 的数据库。
2. \-右键单击，然后依次选择“**任务**”、“**延伸**”、“ **禁用**”。

> [!NOTE]
> 针对数据库禁用 Stretch Database 不会删除远程数据库。 若要删除远程数据库，必须使用 Azure 管理门户。 远程数据库在删除之前，会持续产生 Azure 费用。 有关详细信息，请参阅 [SQL Server Stretch Database 定价](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。
> 
> 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>使用 Transact\-SQL 为数据库禁用 Stretch Database
运行以下命令。

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

> [!NOTE]
> 针对数据库禁用 Stretch Database 不会删除远程数据库。 若要删除远程数据库，必须使用 Azure 管理门户。 远程数据库在删除之前，会持续产生 Azure 费用。 有关详细信息，请参阅 [SQL Server Stretch Database 定价](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)。
> 
> 

## <a name="see-also"></a>另请参阅
[ALTER DATABASE SET 选项 (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[暂停和恢复 Stretch Database](sql-server-stretch-database-pause.md)




<!--HONumber=Nov16_HO3-->


