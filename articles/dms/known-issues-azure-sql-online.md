---
title: 有关联机迁移到 Azure SQL 数据库时存在的已知问题/迁移限制的文章 | Microsoft Docs
description: 了解在联机迁移到 Azure SQL 数据库时存在的已知问题/迁移限制。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/09/2019
ms.openlocfilehash: a822e540db87c36358f1a0e34d75e05ed866868d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491384"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>联机迁移到 Azure SQL 数据库时存在的已知问题/迁移限制

下面描述了从 SQL Server 联机迁移到 Azure SQL 数据库时存在的已知问题和限制。

> [!IMPORTANT]
> 使用的 SQL Server 到 Azure SQL 数据库的联机迁移，不支持 SQL_variant 数据类型的迁移。

### <a name="migration-of-temporal-tables-not-supported"></a>不支持迁移时态表

**症状**

如果源数据库包含一个或多个时态表，在执行“完整数据加载”操作期间数据库迁移将会失败，并可能出现以下消息：

{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode:SQL_ERROR SqlState:42000 NativeError:13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line:1 Column: -1 "]" }
 
 ![时态表错误示例](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**解决方法**

1. 使用以下查询在源架构中查找时态表。
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. 在用于指定要迁移的表的“配置迁移设置”边栏选项卡中排除这些表。

3. 重新运行迁移活动。

**资源**

有关详细信息，请参阅[时态表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)一文。
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>迁移的表中包含一个或多个 hierarchyid 数据类型的列

**症状**

在执行“完整数据加载”操作期间，可能会出现一个 SQL 异常，指出“ntext 与 hierarchyid 不兼容”：
     
![hierarchyid 错误示例](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**解决方法**

1. 使用以下查询来查找包含 hierarchyid 数据类型的列的用户表。

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

2. 在用于指定要迁移的表的“配置迁移设置”边栏选项卡中排除这些表。

3. 重新运行迁移活动。

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>在执行“完整数据加载”或“增量数据同步”期间发生迁移失败，并且与架构中的活动触发器发生各种完整性冲突

**解决方法**

1. 使用以下查询在源数据库中查找当前处于活动状态的触发器：

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. 使用 [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) 一文中提供的步骤在源数据库中禁用这些触发器。

3. 重新运行迁移活动。

### <a name="support-for-lob-data-types"></a>对 LOB 数据类型的支持

**症状**

如果大型对象 (LOB) 列的长度超过 32 KB，目标上的数据可能会截断。 可使用以下查询检查 LOB 列的长度： 

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**解决方法**

如果必须大于 32 KB 的 LOB 列，请联系工程团队[让 Azure 数据库迁移](mailto:AskAzureDatabaseMigrations@service.microsoft.com)。

### <a name="issues-with-timestamp-columns"></a>时间戳列的问题

**症状**

DMS 不会迁移源时间戳值；DMS 在目标表中生成新的时间戳值。

**解决方法**

如果需要 DMS 迁移源表中存储的确切时间戳值，请联系的工程团队[让 Azure 数据库迁移](mailto:AskAzureDatabaseMigrations@service.microsoft.com)。

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>数据迁移错误未提供数据库的详细的状态边栏选项卡上的其他详细信息。

**症状**

跨数据库的详细信息状态视图中的迁移失败时，选择**数据的迁移错误**顶部功能区上的链接可能无法提供特定于迁移失败的其他详细信息。

![发生数据迁移错误时不提供详细信息的示例](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**解决方法**

若要查看特定失败的详细信息，请执行以下步骤。

1. 关闭“数据库详细状态”边栏选项卡以显示“迁移活动”屏幕。

     ![迁移活动屏幕](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. 选择“查看错误详细信息”以查看可帮助排查迁移错误的具体错误消息。
