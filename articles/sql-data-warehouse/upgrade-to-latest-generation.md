---
title: 升级到最新一代的 Azure SQL 数据仓库 | Microsoft Docs
description: 将 Azure SQL 数据仓库升级到最新一代 Azure 硬件和存储体系结构。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 58d65ef05ed872bb357070de9866253baea5dc70
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777801"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>通过升级 SQL 数据仓库优化性能
将 Azure SQL 数据仓库升级到最新一代 Azure 硬件和存储体系结构。

## <a name="why-upgrade"></a>为什么升级？
你现在可以在 Azure 门户中无缝升级到 SQL 数据仓库 Gen2。 如果数据仓库为 Gen1，建议升级。 升级后，可使用最新一代的 Azure 硬件和增强的存储体系结构。 可利用更快的性能、更高的可伸缩性和不受限制的列式存储。 

## <a name="applies-to"></a>适用于
此升级适用于 Gen1 数据仓库。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="before-you-begin"></a>开始之前
> [!NOTE]
> 如果现有的 Gen1 数据仓库不在 Gen2 所在的区域中，你可以通过 PowerShell 将[ Gen2 异地还原](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region)到支持的区域。
> 
>

1. 如果要升级的 Gen1 数据仓库已暂停，请[恢复数据仓库](pause-and-resume-compute-portal.md)。
2. 做好停机几分钟的准备。 



## <a name="start-the-upgrade"></a>开始升级

1. 在 Azure 门户中转到 Gen1 数据仓库，然后单击“升级到 Gen2”：![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. 默认情况下，请使用以下映射，根据 Gen1 的当前性能级别为数据仓库选择建议的性能级别：
    
| Gen1 | Gen2 |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. 在升级之前，请确保工作负荷已完成运行并处于静止状态。 在数据仓库作为 Gen2 数据仓库回到联机状态之前，会出现几分钟的停机时间。 **单击“升级”**。 在预览版期间，Gen2 性能层的价格减半：
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. 通过在 Azure 门户中检查状态来**监视升级**：

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   升级过程的第一个步骤将经历缩放操作（“升级 - 脱机”），其中，所有会话都将终止并且连接将会断开。 
   
   升级过程的第二个步骤是数据迁移（“升级 - 联机”）。 数据迁移是一个联机缓慢执行的后台进程，它利用本地 SSD 缓存，缓慢地将列式数据从旧的存储体系结构移动到新的存储体系结构。 在此期间，你的数据仓库将处于联机状态以便用于查询和加载。 所有数据都可供查询，无论它是否已迁移。 数据迁移以可变速率进行，具体取决于数据大小、性能级别和列存储段的数目。 

5. 通过使用 SQL 数据库浏览边栏选项卡，查找 Gen2 数据仓库。 

> [!NOTE]
> 当前有一个问题：SQL 数据仓库浏览边栏选项卡中不会出现 Gen2 数据仓库。 请使用 SQL 数据库浏览边栏选项卡查找新升级的 Gen2 数据仓库。 我们正致力于解决该问题。
> 

6. **可选建议：** 要加快数据迁移后台进程，建议通过以更大的 SLO 和资源类对所有列存储表运行 [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) 来立即强制数据移动。 与缓慢执行的后台进程相比，此过程是脱机的；不过，数据迁移要快得多，在完成后，可以通过高质量的行组充分利用全新增强的存储体系结构。 

以下查询生成加快数据迁移过程时所需的 Alter Index Rebuild 命令：

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>后续步骤
升级后的数据仓库已联机。 若要利用增强的体系结构，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。
 
