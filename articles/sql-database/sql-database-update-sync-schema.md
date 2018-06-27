---
title: 在 Azure SQL 数据同步中自动复制架构更改 | Microsoft Docs
description: 了解如何在 Azure SQL 数据同步中自动复制架构更改。
services: sql-database
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: sql-database
author: allenwux
ms.author: Xiaochen.Wu
ms.reviewer: douglasl
manager: craigg
ms.custom: data-sync
ms.openlocfilehash: 6dbbd5c76953b23225951ffb655ac4f5b71fc1e0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231862"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>在 Azure SQL 数据同步中自动复制架构更改

SQL 数据同步可让用户在 Azure SQL 数据库与本地 SQL Server 之间单向或双向同步数据。 SQL 数据同步的当前限制之一是不支持架构更改的复制。 每次更改表架构时，都需要在所有终结点（包括中心和所有成员）上手动应用更改，然后更新同步架构。

本文介绍了将架构更改自动复制到所有 SQL 数据同步终结点的解决方法。
1. 此解决方法使用 DDL 触发器来跟踪架构更改。
2. 此触发器在跟踪表中插入架构更改命令。
3. 使用数据同步服务将此跟踪表同步到所有终结点。
4. 插入后，使用 DML 触发器在其他终结点上应用架构更改。

本文使用 ALTER TABLE 作为架构更改的示例，但此解决方案也适用于其他类型的架构更改。

> [!IMPORTANT]
> 我们建议仔细阅读本文，尤其是[故障排除](#troubleshooting)和[其他注意事项](#other)部分，然后在同步环境中开始实现自动架构更改复制。 我们还建议阅读[使用 SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)。某些数据库操作可能导致本文所述的解决方法不起作用。 可能需要有 SQL Server 和 Transact-SQL 领域的其他知识才能排查这些问题。

![自动复制架构更改](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>设置自动架构更改复制

### <a name="create-a-table-to-track-schema-changes"></a>创建用于跟踪架构更改的表

创建一个表，用于跟踪同步组中所有数据库的架构更改：

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

此表包含一个用于跟踪架构更改顺序的标识列。 可根据需要添加更多字段来记录详细信息。

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>创建用于跟踪架构更改历史记录的表

在所有终结点上，创建一个表用于跟踪最近应用的架构更改命令的 ID。

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>在发生架构更改的数据库中创建一个 ALTER TABLE DDL 触发器

为 ALTER TABLE 操作创建 DDL 触发器。 只需在发生架构更改的数据库中创建此触发器。 为避免冲突，只允许同步组中的一个数据库发生架构更改。

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

此触发器在架构更改跟踪表中为每个 ALTER TABLE 命令插入一条记录。 此示例会添加一个筛选器，以避免复制架构 **DataSync** 下发生的架构更改，因为这些更改很有可能是数据同步服务做出的。 如果只想复制特定类型的架构更改，请添加更多的筛选器。

还可以添加更多触发器来复制其他类型的架构更改。 例如，创建 CREATE_PROCEDURE、ALTER_PROCEDURE 和 DROP_PROCEDURE 触发器，将更改复制到存储过程。

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>在其他终结点上创建用于在插入期间应用架构更改的触发器

此触发器在同步到其他终结点后，将执行架构更改命令。 需要在所有端点上创建此触发器，但发生架构更改的终结点（即，在上一步骤中创建 DDL 触发器 `AlterTableDDLTrigger` 的数据库）除外。

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

插入后，此触发器将会运行，并检查接下来是否应运行当前命令。 代码逻辑会确保不会跳过任何架构更改语句，并且即使插入操作失序，也会应用所有更改。

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>将架构更改跟踪表同步到所有终结点

可以使用现有同步组或新同步组将架构更改跟踪表同步到所有终结点。 确保可将跟踪表中的更改同步到所有终结点，尤其是使用单向同步时。

不要同步架构更改历史记录表，因为该表维护不同终结点上的不同状态。

### <a name="apply-the-schema-changes-in-a-sync-group"></a>在同步组中应用架构更改

只会复制创建了 DDL 触发器的数据库中发生的架构更改。 不会复制其他数据库中发生的架构更改。

将架构更改复制到所有终结点后，还需要执行额外的步骤才能更新同步架构，以启动或停止新列同步。

#### <a name="add-new-columns"></a>添加新列

1.  进行架构更改。

2.  在完成创建触发器的步骤之前，请避免做出涉及到新列的任何数据更改。

3.  等到架构更改已应用到所有终结点。

4.  刷新数据库架构，并将新列添加到同步架构。

5.  在下次执行同步操作期间，新列中的数据会同步。

#### <a name="remove-columns"></a>删除列

1.  从同步架构中删除列。 数据同步会停止同步这些列中的数据。

2.  进行架构更改。

3.  刷新数据库架构。

#### <a name="update-data-types"></a>更新数据类型

1.  进行架构更改。

2.  等到架构更改已应用到所有终结点。

3.  刷新数据库架构。

4.  如果新旧数据类型不完全兼容 - 例如，从 `int` 更改为 `bigint` - 在完成创建触发器的步骤之前，同步可能会失败。 重试后，同步会成功。

#### <a name="rename-columns-or-tables"></a>重命名列或表

重命名列或表会使数据同步停止工作。 创建新表或列、回填数据，然后删除旧表或列，而不要重命名。

#### <a name="other-types-of-schema-changes"></a>其他类型的架构更改

对于其他类型的架构更改 - 例如，创建存储过程或删除索引 - 不需要更新同步架构。

## <a name="troubleshoot"></a>排查自动架构更改复制问题

在某些情况下，本文所述的复制逻辑会停止工作 - 例如，如果在 Azure SQL 数据库不支持的本地数据库中进行架构更改。 在这种情况下，同步架构更改跟踪表会失败。 需要手动解决此问题：

1.  禁用 DDL 触发器，并在问题得到解决之前避免任何进一步的架构更改。

2.  在发生问题的终结点数据库中，在无法进行架构更改的终结点上禁用 AFTER INSERT 触发器。 此操作可以同步架构更改命令。

3.  触发同步，以同步架构更改跟踪表。

4.  在发生问题的终结点数据库中，查询架构更改历史记录表，以获取上次应用的架构更改命令的 ID。

5.  查询架构更改跟踪表，以列出 ID 大于上一步骤中检索到的 ID 的所有命令。

    a.  忽略无法在终结点数据库中执行的命令。 需要处理架构不一致情况。 如果不一致性影响了应用程序，请还原原始架构更改。

    b.  手动应用这些命令。

6.  更新架构更改历史记录表，并将上次应用的 ID 设置为正确值。

7.  仔细检查架构是否是最新的。

8.  重新启用在第二个步骤中禁用的 AFTER INSERT 触发器。

9.  重新启用在第一个步骤中禁用的 DDL 触发器。

如果想要清理架构更改跟踪表中的记录，请使用 DELETE 而不是 TRUNCATE。 切勿使用 DBCC CHECKIDENT 在架构更改跟踪表中重新植入标识列。 如果需要重新植入，可以创建新的架构更改跟踪表，并更新 DDL 触发器中的表名称。

## <a name="other"></a>其他注意事项

-   配置中心和成员数据库的数据库用户需有足够的权限才能执行架构更改命令。

-   可在 DDL 触发器中添加更多筛选器，以便只复制所选表或操作中的架构更改。

-   只能在创建 DDL 触发器的数据库中进行架构更改。

-   如果在本地 SQL Server 数据库中进行更改，请确保 Azure SQL 数据库支持架构更改。

-   如果在其中进行架构更改的数据库不是创建了 DDL 触发器的数据库，则不会复制更改。 若要避免此问题，可以创建 DDL 触发器来阻止其他终结点上的更改。

-   如果需要更改架构更改跟踪表的架构，请在进行更改之前禁用 DDL 触发器，然后手动将更改应用到所有终结点。 无法在同一个表上更新 AFTER INSERT 触发器中的架构。

-   不要使用 DBCC CHECKIDENT 重新植入标识列。

-   不要使用 TRUNCATE 清理架构更改跟踪表中的数据。
