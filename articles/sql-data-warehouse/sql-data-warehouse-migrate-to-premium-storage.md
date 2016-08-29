<properties
   pageTitle="将现有 Azure SQL 数据仓库迁移到高级存储 | Azure"
   description="将 SQL 数据仓库迁移到高级存储的说明"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.date="08/11/2016"
   wacn.date=""/>

# 迁移到高级存储的详细信息
SQL 数据仓库最近推出了[具有更好的性能可预测性的高级存储][]。现在我们已经准备好将目前在标准存储上的现有数据仓库迁移到高级存储。继续阅读，了解有关如何以及何时进行自动迁移的详细信息。如果您想控制何时发生停机时间，也请继续阅读以了解如何进行自行迁移。

如果您有多个数据仓库，请使用下面的[自动迁移计划][]来确定何时进行迁移。

## 确定存储类型
如果是在以下日期之前创建的数据仓库，则目前使用的是标准存储。针对将要自动迁移的标准存储上的每个数据仓库，在 [Azure 门户][]中的“数据仓库”边栏选项卡的顶部都有一个通知：“*即将进行的升级到高级存储需要停机。了解详细信息 ->*”。

| **区域** | **在此日期之前创建的数据仓库** |
| :------------------ | :-------------------------------- |
| 中国东部 | 高级存储尚不可用 |
| 中国北部 | 高级存储尚不可用 |

## 自动迁移的详细信息
默认情况下，我们将在下面的[自动迁移计划][]期间，在您所在地区当地时间的下午 6 点至早上 6 点期间，对您的数据库进行迁移。迁移期间，您的现有数据仓库将不可用。预计迁移每个数据仓库中的每 TB 的存储将花费 1 小时。我们还确保在自动迁移过程中的任何部分都不会向您收取费用。

> [AZURE.NOTE] 迁移期间你将不能使用你的现有数据仓库。迁移完成后，你的数据仓库将回到在线状态。

以下详细信息是 Microsoft 代表你完成迁移进行的步骤，不需要你的参与。在本示例中，假设您在标准存储上的现有数据仓库目前名为“MyDW”。

1.	Microsoft 将“MyDW”重命名为“MyDW\_DO\_NOT\_USE\_[Timestamp]”
2.	Microsoft 将暂停“MyDW\_DO\_NOT\_USE\_[Timestamp]”。 在此期间，进行了备份。如果在次过程中遇到任何问题，你将看到多次暂停/恢复。
3.	Microsoft 将在步骤 2 进行的备份中，在高级存储上创建一个名为“MyDW”的新数据仓库。还原完成之前，“MyDW”不会出现。
4.	还原完成后，“MyDW”将返回到相同的 DWU，并与迁移之前的已暂停或活动状态相同。
5.	迁移完成后，Microsoft 将删除“MyDW\_DO\_NOT\_USE\_[Timestamp]”
	
> [AZURE.NOTE] 这些设置不会作为迁移的一部分执行：
> 
>	-  需要重新启用在数据库级别执行审核 
>	-  需要重新添加**数据库**级别的防火墙规则。 
>	-  **服务器**级别的防火墙规则不受影响。

### 自动迁移计划
自动迁移会在下午 6 点 – 上午 6点（每个地区的本地时间）在以下中断计划期间发生。

| **区域** | **预计开始日期** | **预计结束日期** |
| :------------------ | :--------------------------- | :--------------------------- |
| 中国东部 | 尚未决定 | 尚未决定 |
| 中国北部 | 尚未决定 | 尚未决定 |

## 自行迁移到高级存储
如果您想控制何时发生停机时间，则可以使用以下步骤将标准存储上的现有数据仓库迁移到高级存储。如果选择自行迁移，则必须在该地区的自动迁移开始之前完成自行迁移，以避免自动迁移导致冲突的任何风险（请参阅[自动迁移计划][]）。

### 自行迁移说明
如果你想控制自己的停机时间，则可以使用备份/还原自行迁移你的数据仓库。预计迁移的还原部分每个数据仓库中的每 TB 的存储将花费 1 小时。如果想在迁移完成后保持相同的名称，请按照[迁移期间重命名的步骤][]进行操作。

1.	[暂停][]将进行自动备份的数据仓库
2.	从最新的快照进行[还原][]
3.	删除标准存储上的现有数据仓库。**如果此步骤操作失败，你将需要为两个数据仓库支付费用**

> [AZURE.NOTE] 这些设置不会作为迁移的一部分执行：
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

#### 可选：迁移过程中重命名的步骤 
同一逻辑服务器上的两个数据库不能具有相同的名称。SQL 数据仓库现在支持重命名数据仓库。

在本示例中，假设您在标准存储上的现有数据仓库目前名为“MyDW”。

1.	使用类似“MyDW\_BeforeMigration”的内容后跟的 ALTER DATABASE 命令重命名“MyDW”。 此命令终止所有现有事务，而且必须在 master 数据库执行才能成功。
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.	[暂停][]将自动备份的“MyDW\_BeforeMigration”
3.	从最新的快照进行[还原][]，并使用曾经拥有的名称（即“MyDW”）创建一个新数据库。
4.	删除“MyDW\_BeforeMigration”。**如果此步骤操作失败，你将需要为两个数据仓库支付费用**

> [AZURE.NOTE] 这些设置不会作为迁移的一部分执行：
> 
>	-  Auditing at the Database level needs to be re-enabled
>	-  Firewall rules at the **Database** level need to be readded.  Firewall rules at the **Server** level are not be impacted.

## 后续步骤
通过对高级存储的更改，我们还增加了数据仓库基础结构中的数据库 blob 文件的数量。如果您遇到任何性能问题，我们建议使用以下脚本重新生成聚集列存储索引。下面脚本的工作原理是强制将一些现有数据分发到其他 blob。如果不采取任何操作，随着您将更多数据加载到数据仓库表中，数据将自然地重新分发。

**先决条件：**

1.	数据仓库应以 1,000 DWU 或更高 DWU 运行（请参阅[缩放计算能力][]）
2.	执行脚本的用户应为 [mediumrc 角色][]或更高版本
	1.	若要将用户添加到此角色中，请执行下列语句：
		1.	````EXEC sp_addrolemember 'xlargerc', 'MyUser'````  


````sql
-------------------------------------------------------------------------------
-- 步骤 1：创建表来控制索引重新生成
-- 在 mediumrc 或更高版本中以用户身份运行
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
-- 步骤 2：执行索引重新生成。如果脚本失败，可以重新运行以下语句以从最后中断的地方重新启动
-- 在 mediumrc 或更高版本中以用户身份运行
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
-- 步骤 3：清理步骤 1 中创建的表
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

如果有任何关于数据仓库的问题，请[创建支持票证][]，并参阅“迁移到高级存储”寻找可能的原因。

<!--Image references-->

<!--Article references-->
[自动迁移计划]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[创建支持票证]: /documentation/articles/sql-data-warehouse-get-started-create-support-ticket
[Azure paired region]: /documentation/articles/best-practices-availability-paired-regions
[main documentation site]: /documentation/articles/services/sql-data-warehouse
[暂停]: /documentation/articles/sql-data-warehouse-manage-compute-portal/#pause-compute
[还原]: /documentation/articles/sql-data-warehouse-manage-database-restore-portal
[迁移期间重命名的步骤]: #optional-steps-to-rename-during-migration
[缩放计算能力]: /documentation/articles/sql-data-warehouse-manage-compute-portal/#scale-compute-power
[mediumrc 角色]: /documentation/articles/sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[具有更好的性能可预测性的高级存储]: https://azure.microsoft.com/zh-cn/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure 门户]: https://portal.azure.cn

<!---HONumber=Mooncake_0822_2016-->