---
title: "将现有 Azure 数据仓库迁移到高级存储 | Microsoft 文档"
description: "有关将现有数据仓库迁移到高级存储的说明"
services: sql-data-warehouse
documentationcenter: NA
author: happynicolle
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/29/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: e66f808da8d301e0adc393ba0ae67ab8618ce814
ms.openlocfilehash: e73e52665dd22e33054745907613c269b6d57915


---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>将数据仓库迁移到高级存储
Azure SQL 数据仓库最近推出了[具有更好的性能可预测性的高级存储][premium storage for greater performance predictability]。 现在可以将标准存储上现有的数据仓库迁移到高级存储。 可以利用自动迁移，如果想要控制迁移时间（这涉及某种停机时间），也可自己执行迁移操作。

如果有多个数据仓库，请使用[自动迁移计划][automatic migration schedule]来确定何时进行迁移。

## <a name="determine-storage-type"></a>确定存储类型
如果是在以下日期之前创建的数据仓库，则目前使用的是标准存储。

| **区域** | **在此日期之前创建的数据仓库** |
|:--- |:--- |
| 澳大利亚东部 |高级存储尚不可用 |
| 中国东部 |2016 年 11 月 1 日 |
| 中国北部 |2016 年 11 月 1 日 |
| 德国中部 |2016 年 11 月 1 日 |
| 德国东北部 |2016 年 11 月 1 日 |
| 印度西部 |高级存储尚不可用 |
| 日本西部 |高级存储尚不可用 |
| 美国中北部 |2016 年 11 月 10 日 |

## <a name="automatic-migration-details"></a>自动迁移的详细信息
默认情况下，我们将在[自动迁移计划][automatic migration schedule]期间，在用户所在地区当地时间的下午 6:00 至早上 6:00 期间，对用户的数据库进行迁移。 迁移期间，用户的现有数据仓库将不可用。 此迁移对于每个数据仓库中的每 TB 的存储将需要大约 1 小时。 自动迁移过程中的任何部分都不会向用户收取费用。

> [!NOTE]
> 迁移完成后，用户的数据仓库将恢复联机状态并可用。
>
>

Microsoft 执行以下步骤来完成迁移（这些步骤不需要用户参与）。 在本示例中，假设用户在标准存储上的现有数据仓库目前名为“MyDW”。

1. Microsoft 会将“MyDW”重命名为“MyDW_DO_NOT_USE_[Timestamp]”。
2. Microsoft 将暂停“MyDW_DO_NOT_USE_[Timestamp]”。 在此期间，进行了备份。 如果在此过程中遇到任何问题，用户将看到多次暂停和恢复。
3. Microsoft 将根据在步骤 2 中创建的备份在高级存储上创建一个名为“MyDW”的新数据仓库。 还原完成之前，“MyDW”不会出现。
4. 还原完成后，“MyDW”将返回到迁移之前的相同数据仓库单位和状态（“暂停”或“活动”）。
5. 迁移完成后，Microsoft 将删除“MyDW_DO_NOT_USE_[Timestamp]”。

> [!NOTE]
> 以下设置不会在迁移过程中留存：
>
> * 需要重新启用数据库级别的审核。
> * 需要重新添加数据库级别的防火墙规则。 服务器级别的防火墙规则将不受影响。
>
>

### <a name="automatic-migration-schedule"></a>自动迁移计划
在以下服务中断计划期间，将在下午 6:00 到上午 6:00（每个区域的本地时间）之间执行自动迁移。

| **区域** | **预计开始日期** | **预计结束日期** |
|:--- |:--- |:--- |
| 澳大利亚东部 |尚未决定 |尚未决定 |
| 中国东部 |2017 年 1 月 9 日 |2017 年 1 月 13 日 |
| 中国北部 |2017 年 1 月 9 日 |2017 年 1 月 13 日 |
| 德国中部 |2017 年 1 月 9 日 |2017 年 1 月 13 日 |
| 德国东北部 |2017 年 1 月 9 日 |2017 年 1 月 13 日 |
| 印度西部 |尚未决定 |尚未决定 |
| 日本西部 |尚未决定 |尚未决定 |
| 美国中北部 |2017 年 1 月 9 日 |2017 年 1 月 13 日 |

## <a name="self-migration-to-premium-storage"></a>自行迁移到高级存储
如果要控制何时发生停机时间，则可以使用以下步骤将标准存储上的现有数据仓库迁移到高级存储。 如果选择此选项，则必须在该区域的自动迁移开始之前，完成自行迁移。 这可确保避免导致冲突的自动迁移的任何风险（请参阅[自动迁移计划][automatic migration schedule]）。

### <a name="self-migration-instructions"></a>自行迁移说明
若要自行迁移数据仓库，请使用备份和还原功能。 在迁移过程的还原部分中，对于每个数据仓库中每 TB 的存储，预计将花费大约 1 小时。 如果想在迁移完成后保留相同的名称，请按照[迁移期间重命名的步骤][steps to rename during migration]进行操作。

1. [暂停][Pause]数据仓库。 这将执行自动备份。
2. 从最新的快照进行[还原][Restore]。
3. 删除标准存储上的现有数据仓库。 **如果此步骤失败，你将需要为两个数据仓库支付费用。**

> [!NOTE]
> 以下设置不会在迁移过程中留存：
>
> * 需要重新启用数据库级别的审核。
> * 需要重新添加数据库级别的防火墙规则。 服务器级别的防火墙规则将不受影响。
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>在迁移期间重命名数据仓库（可选）
同一逻辑服务器上的两个数据库不能具有相同的名称。 SQL 数据仓库现在支持对数据仓库进行重命名。

在本示例中，假设用户在标准存储上的现有数据仓库目前名为“MyDW”。

1. 使用以下 ALTER DATABASE 命令重命名“MyDW”。 （在此示例中，我们将其重命名为“MyDW_BeforeMigration”。）此命令将停止所有现有的事务，并且必须在 master 数据库中执行才能成功。
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [暂停][Pause]“MyDW_BeforeMigration”。 这将执行自动备份。
3. 从最新的快照进行[还原][Restore]，并使用它以前的名称（例如，“MyDW”）创建一个新数据库。
4. 删除“MyDW_BeforeMigration”。 **如果此步骤失败，你将需要为两个数据仓库支付费用。**


## <a name="next-steps"></a>后续步骤
通过对高级存储的更改，我们还增加了数据仓库基础结构中的数据库 blob 文件的数量。 若要充分利用此更改的性能优势，请使用以下脚本重新生成聚集列存储索引。 该脚本的工作原理是强制将一些现有数据分发到其他 blob。 如果不采取任何操作，当将更多数据加载到表时，这些数据将随着时间的推移自然地重新分发。

**先决条件：**

- 数据仓库应以 1,000 或更高的数据仓库单位运行（请参阅[缩放计算能力][scale compute power]）。
- 执行脚本的用户应具有 [mediumrc 角色][mediumrc role]或更高级的角色。 若要将用户添加到此角色中，请执行以下语句：    ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create table to control index rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute index rebuilds. If script fails, the below can be re-run to restart where last left off.
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Clean up table created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

如果有任何关于数据仓库的问题，请[创建支持票证][create a support ticket]，并引用“迁移到高级存储”作为可能的原因。

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com



<!--HONumber=Feb17_HO3-->


