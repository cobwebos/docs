---
title: 在时间表中管理历史数据
description: 了解如何使用临时保留策略来控制历史数据。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: 3c2460c6f5e0905f45106148ecc3e8a949cf221f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820689"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>使用保留策略管理临时表中的历史数据

与普通的表相比，临时表数据库大小的增长幅度可能更大，尤其是长时间保留历史数据时。 因此，针对历史数据创建保留策略是规划和管理每个时态表的生命周期的一个重要方面。 Azure SQL 数据库中的临时表附带了易用的保留机制，可帮助完成此任务。

可在单个表级别配置时态历史记录保留，这使用户可以创建灵活的期限策略。 应用时态保留很简单：仅需要在创建表或更改架构期间设置一个参数。

定义保留策略后，Azure SQL 数据库开始定期检查是否有符合数据自动清理条件的历史数据行。 识别匹配行并将其从历史记录表中删除，这些操作以透明方式在系统计划和运行的后台任务中进行。 系统会依据表示 SYSTEM_TIME 结束期限的列检查历史记录表行的期限条件。 例如，如果保留期设置为 6 个月，则应清理的表行应符合以下条件：

```
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

在前面的示例中，我们假定**ValidTo**列对应于SYSTEM_TIME期的结束。

## <a name="how-to-configure-retention-policy"></a>如何配置保留策略

在为临时表配置保留策略之前，请首先检查是否*在数据库级别*启用了时态历史保留。

```
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

数据库标志 is_temporal_history_retention_enabled 默认设置为 ON，但用户可以使用 ALTER DATABASE 语句对其进行更改****。 在执行[时间点还原](sql-database-recovery-using-backups.md)操作后，它会自动设置为 OFF。 若要为数据库启用时态历史记录保留清理，请执行以下语句：

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> 即使 **is_temporal_history_retention_enabled** 设置为 OFF，也可以为时态表配置保留策略，但在这种情况下，不会针对陈旧的行触发自动清理。

创建表期间通过指定 HISTORY_RETENTION_PERIOD 参数的值对保留策略进行配置：

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Azure SQL 数据库允许使用不同的时间单位指定保留策略：DAYS、WEEKS、MONTHS 和 YEARS。 如果忽略了 HISTORY_RETENTION_PERIOD，则假定保留期为 INFINITE。 还可以显式使用 INFINITE 关键字。

在某些情况下，建议在创建表后配置保留策略或更改以前配置的值。 此时应使用 ALTER TABLE 语句：

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> 将 SYSTEM_VERSIONING 设置为 OFF *不会保存*保留期值。 在未显式指定 HISTORY_RETENTION_PERIOD 的情况下将 SYSTEM_VERSIONING 设置为 ON 会导致保留期为 INFINITE。

若要查看保留策略的当前状态，请使用以下查询（此查询将数据库级别的时态保留启用标志与单个表的保留期相联接）：

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```


## <a name="how-sql-database-deletes-aged-rows"></a>SQL 数据库如何删除陈旧行

清理过程具体取决于历史记录表的索引布局。 请务必注意，仅具有聚集索引（B 树或列存储）的历史记录表可配置有限保留策略**。 对于具有有限保留期的所有时态表，系统会创建一个后台任务来执行陈旧数据清理。
行存储（B 树）聚集索引的清理逻辑以较小的区块（最大 10K）删除陈旧行，从而可以最大程度地减轻数据库日志和 IO 子系统的压力。 虽然清理逻辑使用要求的 B 树索引，但无法完全保证删除超过保留期的行的顺序。 因此，请不要对应用程序中的清理顺序有任何期待**。

群集列存储的清理任务一次删除整个[行组](https://msdn.microsoft.com/library/gg492088.aspx)（通常每个行包含 100 万行），这非常有效，尤其是在以高速度生成历史数据时。

![聚集列存储保留](./media/sql-database-temporal-tables-retention-policy/cciretention.png)

聚集列存储索引具有优秀的数据压缩和高效的保留清理能力，是工作负载快速生成大量历史数据时的最佳选择。 使用时态表进行更改跟踪和审核、趋势分析或 IoT 数据引入的密集型[事务处理工作负荷](https://msdn.microsoft.com/library/mt631669.aspx)往往使用该模式。

## <a name="index-considerations"></a>索引注意事项

针对具有行存储聚集索引的表的清理任务要求索引的开头为对应于 SYSTEM_TIME 期限结束时间的列。 若没有此类索引，则无法配置有限保留期：

*Msg 13765，16 级，<br></br>状态 1 设置有限保留期失败，系统版本化的时间表"时态阶段testdb.dbo.网站UserInfo"，因为历史表"时态阶段testdb.dbo.网站UserInfoHistory"不包含所需的群集索引。请考虑在历史记录表上创建群集列存储或 B 树索引，从与SYSTEM_TIME期结束的列匹配的列开始。*

请务必注意，Azure SQL 数据库创建的默认历史记录表已有聚集索引，这符合保留策略。 如果尝试在具有有限保留期的表中删除该索引，该操作会失败并出现以下错误：

*Msg 13766，16 级，<br></br>状态 1 不能删除群集索引"网站UserInfoHistory.IX_WebsiteUserInfoHistory"，因为它用于自动清理老化数据。如果需要删除此索引，请考虑在相应的系统版本化临时表上将HISTORY_RETENTION_PERIOD设置为 INFINITE。*

如果按升序插入历史行（按期限结束时间列排序），则清理聚集列存储索引的过程最为顺利。当历史记录表是由 SYSTEM_VERSIONIOING 机制以独占方式填充时，情况往往如此。 如果历史记录表中的行未按期限结束时间列排序（如果迁移了现有历史数据，可能会存在这种情况），则应在正确排序的 B 树行存储索引顶层重新创建聚集列存储索引，以获得最佳性能。

避免在具有有限保留期的历史记录表中重建聚集列存储索引，因为这可能会改变行组中由系统版本控制操作施加的固有顺序。 如果需要在历史记录表中重建聚集列存储索引，请在符合条件的 B 树索引顶层创建该索引，同时保留行组的顺序，以便能够执行常规数据清理。 如果要使用具有聚集列索引且数据顺序没有保证的现有历史记录表创建时态表，则应采用同样的方法：

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

为具有聚集列存储索引的历史记录表配置有限保留期时，无法在该表表创建附加的非聚集 B 树索引：

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

尝试执行上述语句会失败并出现以下错误：

*Msg 13772、16 级、<br></br>状态 1 无法在时间历史表"网站UserInfo历史"上创建非群集索引，因为它具有有限的保留期和定义的群集列存储索引。*

## <a name="querying-tables-with-retention-policy"></a>使用保留策略查询表

针对时态表执行的所有查询会自动筛选出与有限保留策略匹配的历史行，以免出现不可预测且不一致的结果，因为清理任务可能会*在任何时间点按任意顺序*删除陈旧行。

下图显示一个简单查询的查询计划：

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

该查询计划包含的附加筛选器已应用到历史记录表上“聚集索引扫描”运算符中的期限结束时间列 (ValidTo)（已突出显示）。 此示例假设已在 WebsiteUserInfo 表中设置了一个月 (1 MONTH) 的保留期。

![保留查询筛选器](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

但是，在直接查询历史记录表时，可以看到超过指定保留期的行，但不保证会看到可重复的查询结果。 下图显示了针对历史记录表执行的、未应用附加筛选器的查询的查询执行计划：

![在不使用保留筛选器的情况下查询历史记录](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

不要依赖于业务逻辑来读取超过保留期的历史记录表，否则可能会收到不一致或意外的结果。 建议配合 FOR SYSTEM_TIME 子句使用临时查询来分析时态表中的数据。

## <a name="point-in-time-restore-considerations"></a>时间点还原注意事项

通过[将现有数据库还原到特定时间点](sql-database-recovery-using-backups.md)创建新数据库时，会在数据库级别禁用临时保留。 （**is_temporal_history_retention_enabled** 标志设置为 OFF） 使用此功能可以在还原时检查所有历史行，无需担心在查询陈旧行之前它们是否已删除。 可以使用此功能*检查已超过配置的保留期的历史数据*。

假设为某个时态表指定了一个月的保留期。 如果数据库是在高级服务层中创建的，则可以使用保持过去最多 35 天前状态的数据库创建数据库副本。 这样，便可以通过直接查询历史记录表，有效分析保留时间最长为 65 天前的历史行。

如果想要激活临时保留清理，请在执行时间点还原后运行以下 Transact-SQL 语句：

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>后续步骤

要了解如何在应用程序中使用临时表，请查看[Azure SQL 数据库中的"开始使用临时表](sql-database-temporal-tables.md)"。

访问第 9 频道收听[客户实施临时表的真实成功案例](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions)，观看[临时表现场演示](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)。

有关临时表的详细信息，请查看 [MSDN 文档](https://msdn.microsoft.com/library/dn935015.aspx)。
